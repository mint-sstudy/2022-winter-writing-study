# Ch05 람다로 프로그래밍
## 📌 학습목표
```
• 람다 식과 멤버 참조
• 함수형 스타일로 컬렉션 다루기
• 시퀀스: 지연 컬렉션 연산
• 자바 함수형 인터페이스를 코틀린에서 사용
• 수신 객체 지정 람다 사용
```
<br>

> 람다 식(lambda expression) 또는 람다는 다른 함수에 넘길 수 있는 작은 코드 조각을 뜻한다.
> 람다를 사용하면 공통 코드 구조를 <br>라이브러리 함수로 뽑아낼 수 있다.
<br>
<br>

## 5.1 람다 식과 멤버 참조
### 5.1.1 람다 소개: 코드 블록을 함수 인자로 넘기기
- 함수형 프로그래밍에서는 함수를 값처럼 다룬다. 또한, 클래스를 선언하고 그 클래스의 인스턴스를 함수에 넘기는 것이 아니라 함수 자체를<br>
  다른 함수에 전달한다. <br>
→ 이때, `람다 식`을 사용하면 함수를 선언할 필요가 없고 코드 블록을 직접 함수의 인자로 전달할 수 있기 때문에 코드가 더욱 간결해진다.
<br>

**Ex)** 버튼 클릭 시 특정 이벤트 발생
```java
button.setOnClickListener(new OnClickListener() {
  @Override
  public void onClick(View view) {
    /* 클릭 시 수행할 동작 */
  }
});
```
→ 무명 내부 클래스 선언하느라 코드가 번잡하다.
<br>
<br>

```kotlin
button.setOnClickListener { /* 클릭 시 수행할 동작 */ }
```
→ `람다`를 메소드가 하나뿐인 무명 객체 대신 사용할 수 있다.
<br>
<br>
<br>

 ### 5.1.2 람다와 컬렉션
 Ex) 사람의 이름과 나이를 저장하는 Person 클래스를 사용하고, 사람들로 이루어진 리스트에서 연장자를 찾는 예제
 ```kotlin
 fun findTheOldest(people: List<Person>) {
  var maxAge = 0
  var theOldest: Person ?= null
  for (person in people) {
    if (person.age > maxAge) {
      masAge = person.age
      theOldest = person
    }
  }
  println(theOldest)
}
```
```
>>> val people = listOf(Person("Alice", 29), Person("Bob", 31))
>>> findTheOldest(people)
Person(name=Bob, age=31)
```
→ 람다를 사용하지 않는다면, 위와 같이 루프를 사용해 직접 검색을 구현할 것이다.
<br>
<br>

- 코틀린에서는 더 좋은 방법이 있다. **라이브러리 함수**를 사용하자!
```
>>> val people = listOf(Person("Alice", 29), Person("Bob", 31))
>>> println(people.maxBy { it.age }) //age 프로퍼티를 비교해 값이 가장 큰 원소 찾기
Person(name=Bob, age=31)
```
→ 모든 컬렉션에 대해 `maxBy 함수`를 호출할 수 있는데, `maxBy`는 가장 큰 원소를 찾기 위해 비교에 사용할 값을 돌려주는 함수를 인자로 받는다. <br>
{ it.age }는 바로 비교에 사용할 값을 돌려주는 함수이며, 컬렉션의 원소를 인자로 받아 (it이 그 인자를 지칭) 비교에 사용할 값을 리턴한다. <br>
위의 예제에서 { it.age }가 반환하는 값은 Person 객체의 age 필드에 저장된 나이 정보다.
<br>
<br>

- 단지 함수나 프로퍼티를 반환하는 역할을 하는 **람다**는 멤버 참조로 대치할 수 있다.
```kotlin
people.maxBy(Person::age)
```
→ 이 코드는 바로 위의 코드와 같은 일을 한다.
<br>
<br>
<br>

### 5.1.3 람다 식의 문법
```kotlin
{ x: Int, y: Int -> x + y }
```
- `람다`는 값처럼 전달할 수 있으며, 람다를 따로 선언해서 변수에 저장할 수도 있다. 하지만 함수에 인자로 넘기면서 바로 정의하는 경우가 대부분이다.
- 람다식은 항상 중괄호로 둘러싸여 있으며, 인자 목록 주변에는 괄호가 없다. 화살표가 인자 목록과 람다 본문을 구분해준다.
<br>

- 람다 식을 변수에 저장할 수도 있다. 람다가 저장된 변수를 다른 일반 함수와 마찬가지로 다룰 수 있다. <br>
예를 들어, 변수 이름 뒤에 괄호를 쓰고 그 안에 인자를 넣어 람다를 호출할 수 있다.
```
>>> val sum = { x: Int, y: Int -> x + y }
>>> println(sum(1, 2)) //변수에 저장된 람다를 호출
3
```
<br>
<br>

```
>>> run { println(42) }
42
```
→ 코드의 일부분을 블록으로 둘러싸 실행할 필요가 있다면 `run`을 사용한다. `run`은 인자로 받은 람다를 실행해주는 라이브러리 함수이다.
<br>
<br>

- 위의 예제 중 `people.maxBy { it.age }`에서 코드를 줄여 쓴 부분을 제거하고 정식으로 람다를 작성하면 다음과 같다. <br>
> people.maxBy({ p: Person -> p.age }) 

→ 여기서 문맥 상 컴파일러가 유추할 수 있는 인자 타입은 굳이 적어줄 필요 없고, 인자가 단 하나뿐인 경우 굳이 인자에 이름을 붙이지 않아도 된다. <br>
 또한, 함수 호출 시 맨 뒤에 있는 인자가 람다식이면 그 람다를 괄호 밖으로 뺴낼 수 있다. <br>
 <br>
 
> people.maxBy() { p: Person -> p.age }

→ 람다가 어떤 함수의 유일한 인자이고 괄호 뒤에 람다를 썼다면 호출 시 빈 괄호를 없애도 된다. <br>
<br>

> people.maxBy { p: Person -> p.age }
<br>
<br>
<br>

### 5.1.4 현재 영역에 있는 변수에 접근
- 람다를 함수 안에서 정의하면, 함수의 파라미터뿐 아니라 람다 정의의 앞에 선언된 로컬 변수까지 람다에서 모두 사용할 수 있다. <br>
**Ex)** 함수 파라미터를 람다 안에서 사용하는 예제
```kotlin
fun printMessagesWithPrefix(messages: Collection<String>, prefix: String) {
  messages.forEach { //각 원소에 대해 수행할 작업을 람다로 처리
    println("$prefix $it") //람다 안에서 함수의 'prefix' 파라미터 사용
  }
}
```
```
>>> val errors = listOf("403 Forbidden", "404 Not Found")
>>> printMessagesWithPrefix(errors, "Error:")
Error: 403 Forbidden
Error: 404 Not Found
```
<br>

- 코틀린에서는 자바와 달리 람다에서 람다 밖 함수에 있는 파이널이 아닌 변수에 접근할 수 있고, 그 변수를 변경할 수도 있다. <br>
**Ex)** 람다 안에서 바깥 함수의 로컬 변수 변경하는 예제
```kotlin
fun printProblemCounts(responses: Collection<String>) {
  var clientErrors = 0
  var serverErrors = 0
  responses.forEach {
    if (it.startsWith("4")) {
      clientErrors++
    } else if (it.startsWith("5")) {
      serverErrors++
    }
  }
  println("$clientErrors client errors, $serverErrors server errors")
}
```
```
>>> val responses = listOf("200 OK", "418 I'm a teapot",
...                         "500 Internal Server Error")
>>> printProblemCounts(responses)
1 client errors, 1 server errors
```
<br>

- **주의)** 람다를 이벤트 핸들러나 다른 비동기적으로 실행되는 코드로 활용하는 경우, 함수 호출이 끝난 후 로컬 변수가 변경될 수 있다. <br>
**Ex)**
```kotlin
fun tryToCountButtonClicks(button: Button): Int {
  var clicks = 0
  button.onClick { clicks++ }
  return clicks
}
```
→ 항상 0을 return한다. onClick 핸들러는 tryToCountButtonClicks가 clicks를 반환한 다음에 호출되기 때문에, clicks 값의 변화를 알 수 없다. <br>
제대로 구현하려면 클릭 횟수를 세는 카운터 변수를 클래스의 프로퍼티나 전역 프로퍼티 위치로 빼야 한다.
<br>
<br>
<br>

### 5.1.5 멤버 참조
> 람다를 사용해 코드 블록을 다른 함수에게 인자로 넘기는 방법 말고, 넘기려는 코드가 이미 함수로 선언된 경우 그 함수를 직접 넘길 수는 없을까?

- 코틀린에서는 `이중 콜론`(::)을 사용해 함수를 값으로 바꿀 수 있다. 
```kotlin
val getAge = Person::age
```
<br>

- **::** 를 사용하는 식을 멤버 참조(member reference)라고 하며, 멤버 참조는 프로퍼티나 메소드를 하나만 호출하는 함수 값을 만들어 준다.
- 멤버 참조는 그 멤버를 호출하는 람다와 같은 타입이므로, 다음의 예처럼 둘을 자유롭게 바꿔 쓸 수 있다.
```kotlin
people.maxBy(Person::age)
people.maxBy { p -> p.age }
people.maxBy { it.age }
```
<br>

- 최상위에 선언된 함수나 프로퍼티를 참조할 수도 있다.
```kotlin
fun salute() = println("Salute!")
>>> run(::salute) //최상위 함수 참조
Salute!
```
→ 클래스 이름을 생략하고 `::`로 바로 참조를 시작한다.
<br>
<br>

- 생성자 참조(constructor reference)를 사용하면 클래스 생성 작업을 연기하거나 저장해둘 수 있다. `::` 뒤에 클래스 이름을 넣으면 생성자 참조를 만들 수 있다.
```kotlin
data class Person(val name: String, val age: Int)
>>> val createPerson = ::Person //Person의 인스턴스를 만드는 동작을 값으로 저장
>>> val p = createPerson("Alice", 29)
>>> println(p)
Person(name=Alice, age=29)
```
