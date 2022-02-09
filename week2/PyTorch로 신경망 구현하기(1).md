# PyTorch로 신경망 구현하기(1)

인공지능 공부를 한지 1년 정도 되어가지만 이론 공부만 했어서 부끄럽게도 pytorch나 tensorflow와 같은 주요 머신러닝 라이브러리를 다룰 줄 몰라서 이번에 pytorch tutorial을 참고하여 신경망을 구현하는 것을 공부하였다. 

 파이토치의 `torch.nn` 을 사용하면 쉽게 인공 신경망(neural network)을 만들어낼 수 있다. 물론 `torch.nn` 을 사용하지 않고도 만들어낼 수 있는데, `torch.nn` 의 함수를 더 잘 이해하기 위해서 이를 사용하지 않고 구현한 것을 먼저 보고, 파이토치의 함수들을 사용하여 코드를 더 간결하고 효율적으로 만드는 순서로 글을 쓸 예정이다.

이번 글에서는 `torch.nn` 을 사용하지 않고 구현한 것을 볼 것이다. 

# MNIST 데이터 준비

신경망을 학습하기 위해서는 신경망도 필요하지만, 학습시킬 데이터도 있어야 한다. 파이토치 튜토리얼에서는 MNIST 데이터를 사용하여 신경망을 학습했다. 

```python
from pathlib import Path
import requests
import pickle
import gzip

DATA_PATH = Path("data")
PATH = DATA_PATH / "mnist"

PATH.mkdir(parents=True, exist_ok=True)

URL = "https://github.com/pytorch/tutorials/raw/master/_static/"
FILENAME = "mnist.pkl.gz"

if not (PATH / FILENAME).exists():
    content = requests.get(URL + FILENAME).content
    (PATH / FILENAME).open("wb").write(content)

with gzip.open((PATH / FILENAME).as_posix(), "rb") as f:
        ((x_train, y_train), (x_valid, y_valid), _) = pickle.load(f, encoding="latin-1")
```

이 데이터셋은 numpy 배열 포맷이고, 데이터를 직렬화하기 위한 python 전용 포맷 pickle을 이용하여 저장되어 있다. 각 이미지는 28 x 28 형태 이고, 784 (=28x28) 크기를 가진 하나의 행으로 저장되어 있다.

그런데 PyTorch는 numpy 배열 보다는 `torch.tensor` 를 사용하므로, 해당 데이터를 tensor로 변환해줘야 한다. 

```python
import torch

# x_train, y_train, x_valid, y_valid를 모두 torch.tensor 타입으로 바꿈
x_train, y_train, x_valid, y_valid = map(
    torch.tensor, (x_train, y_train, x_valid, y_valid)
)

n, c = x_train.shape
print(x_train, y_train)
print(x_train.shape)
print(y_train.min(), y_train.max())
```

이제 훈련 데이터는 준비가 되었다. x_train과 y_train을 사용하여 신경망을 학습할 것이다. 

# torch.nn없이 밑바닥부터 신경망 만들기

`torch.nn`대신 파이토치의 텐서(tensor) 연산을 사용해서 모델을 만들 것이다. 

먼저 텐서는 배열(array)이나 행렬(matrix)과 매우 유사한 특수한 자료구조로, 파이토치에서는 텐서를 사용하여 모델의 입력(input)과 출력(output)과 모델의 매개변수를 부호화(encode)한다. 텐서는 numpy의 ndarray 비슷하기 때문에 numpy에 익숙한 사람이라면 텐서도 금방 사용할 수 있을 것이다. 

전체 코드는 다음과 같다. 

```python
import math

weights = torch.randn(784, 10) / math.sqrt(784)
weights.requires_grad_()
bias = torch.zeros(10, requires_grad=True)

def log_softmax(x):
    return x - x.exp().sum(-1).log().unsqueeze(-1)

def model(xb):
    return log_softmax(xb @ weights + bias)

def nll(input, target):
    return -input[range(target.shape[0]), target].mean() 

def accuracy(out, yb):
    preds = torch.argmax(out, dim=1)
    return (preds == yb).float().mean()

lr = 0.5 
epochs = 2 
bs = 64 

loss_func = nll

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
            weights -= weights.grad * lr
            bias -= bias.grad * lr
            weights.grad.zero_()
            bias.grad.zero_()

print(loss_func(model(xb), yb), accuracy(model(xb), yb))
```

코드의 각 부분을 천천히 보자.

먼저모델의 가중치(weights)와 편향(bias)을 생성하기 위해 텐서를 생성한다. weights 값은 랜덤 값으로, bias 값은 0으로 초기화해주었다. 그리고 파이토치에서는 기울기(gradient)값을 알아서 계산해주는 편리한 기능을 제공하는데, 텐서에 행해지는 모든 연산을 기록하게 하고, 이를 이용해서 자동으로 역전파(역방향 전파;back-propagation) 동안에 기울기를 계산할 수 있게 해준다. 

```python
import math

# weights, bias를 초기화하기 위한 텐서 생성
weights = torch.randn(784, 10) / math.sqrt(784)
weights.requires_grad_()
bias = torch.zeros(10, requires_grad=True)
```

`requires_grad_()` 는 weights 텐서에 대해 연산을 기록하여 자동으로 기울기 값을 계산할 수 있게 한다. `bias = torch.zeros(10, requires_grad=True)` 처럼 텐서를 생성할 때 설정할 수도 있고, `weights.requires_grad_()` 처럼 함수를 사용하여 나중에 설정할 수도 있다.

신경망을 생성하기 위해 활성화 함수(activation function)가 필요하므로 이 예시에서는 log_softmax를 구현하여 사용할 것이다. 파이토치에는 사전구현된 많은 활성화 함수와 손실함수(loss function)가 제공되지만 내장 python함수들을 사용하여 충분히 구현할 수 있으므로 직접 구현해보겠다. 

```python
# 활성화 함수로 los_softmax를 사용
def log_softmax(x):
    # .unsqueeze(n): n의 위치에 1인 차원을 생성하는 함수(-1: 뒤에서 첫번째)
    return x - x.exp().sum(-1).log().unsqueeze(-1)

def nll(input, target):
	'''
	input(64, 10)에서 (해당 data에서의 특정 class에 대한 prediction 값)*(-1)을 return
	ex) 첫번째 data(input[0])의 정답이 idx=5(class 6)라고 하면 (input[0][5]의 값)*(-1)을 return하게 됨
	(prediction값은 큰 값일 수록 해당 class로 분류될 확률 높은(좋은) 반면 loss는 클수록 안 좋기 때문에 -1을 곱함) 
	'''
    return -input[range(target.shape[0]), target].mean()

def model(xb):
		# @ : 행렬 곱셉(matrix multiplication) 연산
    return log_softmax(xb @ weights + bias)

loss_func = nll

def accuracy(out, yb):
    preds = torch.argmax(out, dim=1)
    # pred, yb 모두 tensor로 pred==yb 연산 결과 True, False로 구성된 배열이 나올 것
    return (preds == yb).float().mean()
```

먼저 한번의 forward 연산에 대해서만 구현해보겠다. 미니배치의 크기를 64로 지정했기 때문에 한번의 forward 연산을 수행하면 64개의 데이터에 대해서만 예측 값을 계산하게 된다. 손실함수를 계산하여 모델의 정확도를 계산한다. weights를 무작위의 값으로 설정했기 때문에 낮은 정확도를 가질 것이다. 

```python
bs = 64 # 배치 크기

# 첫번째 batch의 input값들에 대해서만 forward 수행
xb = x_train[0:bs] # x로부터 미니배치 추출
preds = model(xb)  # 예측
print(preds[0], preds.shape)

yb = y_train[0:bs]
print(loss_func(preds, yb))

print(accuracy(preds, yb))
```

훈련(train)과정은 위와 같은 forward 연산과 backward 연산을 반복하여 수행된다. 

forward 연산을 통해 훈련 데이터(training set)에 대한 y값을 예측하고 예측한 값이 얼마나 정확한지 계산한다. 그리고나서 backward를 통해 weights과 bias을 어떻게 수정해야 모델의 정확도가 높아질지 계산하고 이를 반영하여 weights과 bias를 업데이트한다. 여기까지가(forward-backward-update) 한 epoch이고 사람이 원하는 epoch만큼 모델을 훈련시킨다. 여기서는 epoch을 2로 설정하겠다. 

학습률은 weights과 bias 값을 얼마나 수정할지 결정하는 값이다. 학습률을 크게하면 weights과 bias가 한번에 많이 업데이트(수정)되고, 작게하면 조금씩 업데이트된다. 학습률이 작을수록 느리게 학습될 것이고, 클수록 빠르게 학습되지만 너무 크면 정확도가 오히려 (많이) 떨어지게 된다. 

```python
lr = 0.5  # 학습률(learning rate)
epochs = 2  # 훈련에 사용할 에폭(epoch) 수

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
            weights -= weights.grad * lr
            bias -= bias.grad * lr
            weights.grad.zero_()
            bias.grad.zero_()
```

위에서 weights과 bias를 업데이트하기 위해서 기울기(gradient)를 계산할 때, 둘은 항상 자동으로 기울기를 계산하기 때문에 계산값이 기록(누계)되어 다음 연산에도 영향을 미치게 된다. 이를 막기 위해서 업데이트하는 부분에서 weights과 bias의 미분값이 기록되지 않게끔하고 기울기 값을 0으로 초기화하여 다음 계산에 영향을 주지 않도록 한다. 자동 미분은 backward 연산을 위해 필요한 것이기 때문에 backward 연산 후에 자동으로 미분하지 않게끔한다. 

이렇게 해서 제일 간단한 신경망을 생성하고 훈련하였다. 손실(loss)값과 정확도를 이전과 비교하여 얼마나 성능이 좋아졌는지 확인해보자.

```python
print(loss_func(model(xb), yb), accuracy(model(xb), yb))
```
