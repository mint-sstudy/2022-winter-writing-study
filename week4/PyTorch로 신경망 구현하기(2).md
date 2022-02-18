# PyTorch로 신경망 구현하기(2)

# torch.nn.functional 사용하기

이제 우리는 코드를 리팩토링(refactoring) 하겠다, 그럼으로써 이전과 동일하지만, PyTorch의 `nn` 클래스의 장점을 활용하여 더 간결하고 유연하게 만들 것이다.

지금부터 매 단계에서, 코드를 더 짧고, 이해하기 쉽고, 유연하게 만들것이다. 

코드를 짧게 만들기 가장 쉬운 첫번째의 단계는 직접 작성한 활성화, 손실 함수를 `torch.nn.functional` 의 함수로 대체하는 것이다. (관례에 따라, 일반적으로 `F` 네임스페이스(namespace)를 통해 임포트(import)한다).

이 모듈에는 `torch.nn` 라이브러리의 모든 함수가 포함되어 있다. (라이브러리의 다른 부분에는 클래스가 포함되어 있다.) 다양한 손실 및 활성화 함수 뿐만 아니라, 풀링(pooling) 함수와 같이 신경망을 만드는데 편리한 몇 가지 함수도 여기에서 찾을 수 있다.

(컨볼루션(convolution) 연산, 선형(linear) 레이어, 등을 수행하는 함수도 있지만, 일반적으로 라이브러리의 다른 함수들을 사용하여 더 잘 처리 할 수 있다.)

만약 여러분들이 음의 로그 우도 손실과 로그 소프트맥스 (log softmax) 활성화 함수를 사용하는 경우, Pytorch는 이 둘을 결합하는 단일 함수인 `F.cross_entropy` 를 제공한다. 따라서 모델에서 활성화 함수를 제거할 수도 있다.

```python
import torch.nn.functional as F

# cross_entropy = log_softmax + log_likelihood_loss 이기 때문에
# cross_entropy 한번으로 activation func적용 + loss 함수 계산을 한번에 할 수 있다.
loss_func = F.cross_entropy

def model(xb):
    # cross_entropy에 activation func(softmax 함수)이 포함되어있어서 x*w+b만 계산해줌
    return xb @ weights + bias
```

더이상 ``model`` 함수에서 ``log_softmax`` 를 호출하지 않고 있다. 손실과 정확도과 이전과 동일한지 확인해보자:

```python
print(loss_func(model(xb), yb), accuracy(model(xb), yb))
```

# nn.Module 을 이용하여 리팩토링 하기

다음으로, 더 명확하고 간결한 훈련 루프를 위해 ``nn.Module`` 및 ``nn.Parameter`` 를 사용한다.

먼저, ``nn.Module``의 (자체가 클래스이고 상태를 추척할 수 있는)하위 클래스(subclass)를 만든다. 이 경우에는, 포워드(forward) 단계에 대한 가중치, 절편, 리고 메소드(method) 등을 유지하는 클래스를 만들고자 한다. ``nn.Module`` 은 우리가 사용할 몇 가지 속성(attribute)과 메소드를 (``.parameters()`` 와

``.zero_grad()`` 같은) 가지고 있다. `nn.Module` (대문자 M) 은 PyTorch 의 특정 개념이고, 우리는 이 클래스를 많이 사용할 것이다.

```python
from torch import nn

class Mnist_Logistic(nn.Module):
    def __init__(self):
        super().__init__()
        self.weights = nn.Parameter(torch.randn(784, 10) / math.sqrt(784))
        self.bias = nn.Parameter(torch.zeros(10))

    def forward(self, xb):
        return xb @ self.weights + self.bias
```

함수를 사용하는 대신에 이제는 오브젝트(object) 를 사용하기 때문에, 먼저 모델을 인스턴스화(instantiate) 해야 한다:

```python
model = Mnist_Logistic()
```

이제 우리는 이전과 동일한 방식으로 손실을 계산할 수 있다.

여기서 ``nn.Module`` 오브젝트들은 마치 함수처럼 사용된다 (즉, 이들은 **호출가능** 하다),

그러나 배후에서 Pytorch 는 우리의 ``forward`` 메소드를 자동으로 호출한다.

```python
# Mnist_Logistic() 클래스의 forward 함수를 자동으로 호출해주기 때문에 따로 forward함수를 적용하는 코드를 작성하지 않는다.
print(loss_func(model(xb), yb))
```

이전에는 훈련 루프를 위해 이름 별로 각 매개변수(parameter)의 값을 업데이트하고 다음과 같이

각 매개 변수에 대한 기울기들을 개별적으로 수동으로 0으로 제거했었다:

```
with torch.no_grad():

  weights -= weights.grad * lr

  bias -= bias.grad * lr

  weights.grad.zero_()

  bias.grad.zero_()
```

이제 우리는 model.parameters() 및 model.zero_grad() (모두 ``nn.Module`` 에 대해 PyTorch에 의해 정의됨)를 활용하여 이러한 단계를 더 간결하게 만들고, 특히 더 복잡한 모델에 대해서 일부 매개변수를 잊어 버리는 오류를 덜 발생시킬 수 있다:

```
with torch.no_grad():

  for p in model.parameters(): p -= p.grad * lr

    model.zero_grad()
```

이제 이것을 나중에 다시 실행할 수 있도록 ``fit`` 함수로 작은 훈련 루프를 감쌀 것이다.

```python
def fit():
    for epoch in range(epochs):
        for i in range((n - 1) // bs + 1):
            start_i = i * bs
            end_i = start_i + bs
            xb = x_train[start_i:end_i]
            yb = y_train[start_i:end_i]
            pred = model(xb)
            loss = loss_func(pred, yb)

            loss.backward()
            with torch.no_grad():
                for p in model.parameters():
                    p -= p.grad * lr
                model.zero_grad()

fit()
```

# nn.Linear 를 이용하여 리팩토링 하기

계속해서 코드를 리팩토링 한다. ``self.weights`` 및 ``self.bias`` 를 수동으로 정의 및 초기화하고, ``xb  @ self.weights + self.bias`` 를 계산하는 대신에,

위의 모든 것을 해줄 Pytorch 클래스인 `nn.Linear `를 선형

레이어로 사용한다. Pytorch 에는 다양한 유형의 코드를 크게 단순화 할 수 있는 미리 정의된 레이어가 있고 이는 또한 종종 기존 코드보다 속도를 빠르게 한다.

```python
class Mnist_Logistic(nn.Module):
    def __init__(self):
        super().__init__()
        # weights랑 bias parameter는 어디감? 필요없나.??
        # superclass에 weights랑 bias parameter는 디폴트로 선언되어있고
        # 따로 초기화(혹은 대입)할 필요 없어서 weights, bias를 초기화하는 코드가 없을뿐인듯
        self.lin = nn.Linear(784, 10)

    def forward(self, xb):
        # wb*weights+bias -> self.lin(xb)
        return self.lin(xb)
```

이전과 같은 방식으로 모델을 인스턴스화하고 손실을 계산한다:

```python
model = Mnist_Logistic()
print(loss_func(model(xb), yb))
```

# optim 을 이용하여 리팩토링 하기

Pytorch에는 다양한 최적화(optimization) 알고리즘을 가진 패키지인 ``torch.optim`` 도 있다. 각 매개변수를 수동으로 업데이트 하는 대신, 옵티마이저(optimizer)의 ``step`` 메소드를 사용하여 업데이트를 진행할 수 있다.

이렇게 하면 이전에 수동으로 코딩한 최적화 단계를 대체할 수 있다:

```
with torch.no_grad():

  for p in model.parameters(): p -= p.grad * lr

  model.zero_grad()
```

위의 코드를 밑의 코드로

```
opt.step()

opt.zero_grad()
```

optimizer가 weight, bias를 업데이트하는 함수를 의미하니까 해당 코드가 opt.step()코드로 대체된다. (``optim.zero_grad()`` 는 기울기를 0으로 재설정 해준다. 다음 미니 배치에 대한 기울기를 계산하기 전에 호출해야 한다.)

```python
from torch import optim
```

나중에 다시 사용할 수 있도록 모델과 옵티마이져를 만드는 작은 함수를 정의한다.

```python
def get_model():
    model = Mnist_Logistic()
    return model, optim.SGD(model.parameters(), lr=lr)

model, opt = get_model()
print(loss_func(model(xb), yb))

for epoch in range(epochs):
    for i in range((n - 1) // bs + 1):
        start_i = i * bs
        end_i = start_i + bs
        xb = x_train[start_i:end_i]
        yb = y_train[start_i:end_i]
        pred = model(xb)
        loss = loss_func(pred, yb)

        loss.backward()
        opt.step()
        opt.zero_grad()

print(loss_func(model(xb), yb))
```
