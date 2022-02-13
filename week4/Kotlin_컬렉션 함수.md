## 5.2 컬렉션 함수형 API
> 컬렉션을 다루는 코틀린 표준 라이브러리 몇가지를 배워보자! filter와 map, 그리고 이 둘을 뒷받침하는 개념에 대해서
<br>

### 5.2.1 필수적인 함수: filter와 map
- **filter 함수**는 컬렉션을 iteration하면서 주어진 람다에 각 원소를 넘겨 true를 반환하는 원소만 모은다.
```kotlin
data class Person(val name: String, val age: Int)
```
```
>>> val list = listOf(1, 2, 3, 4)
>>> println(list.filter { it % 2 == 0 }) //짝수만 filter
[2, 4]
```
→ 주어진 조건(술어)를 만족하는 원소만 걸러 새로운 컬렉션을 만든다. 
<br>

- filter 함수는 컬렉션에서 원치 않는 원소를 제거할 때 쓰지만 원소를 변환할 수는 없어서 변환하려면 **map 함수**를 사용해야 한다. <br>
→ map 함수는 주어진 람다를 컬렉션의 각 원소에 적용한 결과를 모아 새 컬렉션을 만든다.
```
>>> val list = listOf(1, 2, 3, 4)
>>> println(list.map { it * it }) //제곱값으로 변경
[1, 4, 9, 16]
```
<br>

```
>>> val people = listOf(Person("Alice", 29), Person("Bob", 31))
>>> println(people.map { it.name }) //people이 아닌 people의 name list 출력
[Alice, Bob]
```
<br>

→ 멤버 참조를 사용하면 더욱 간결하게 작성이 가능하다.
```kotlin
people.map(Person::name)
```
<br>

- 연쇄적으로 호출해 30살 이상인 사람의 이름을 출력해보자!
```
>>> people.filter { it.age > 30 }.map(Person::name)
```
<br>

- 가장 나이 많은 사람의 이름을 출력해보자! <br>
→ list에 있는 사람들의 나이의 최댓값을 구하고 나이가 구한 최댓값과 같은 모든 사람의 이름을 return
```kotlin
people.filter { it.age == people.maxBy(Person::age)!!.age }
```
→ list에서 최댓값을 구하는 연산을 사람 수 만큼 반복한다는 단점이 존재한다. 
<br>
<br>

```kotlin
val maxAge = people.maxBy(Person::age)!!.age
people.filter { it.age == maxAge }
```
→ 위의 코드를 개선해 최댓값을 한 번만 계산하도록 수정
<br>
<br>

- filter와 변환 함수를 **map**에 적용할 수도 있다.
```
>>> val numbers = mapOf(0 to "zero", 1 to "one")
>>> println(numbers.mapValues { it.value.toUpperCase() })
{0=ZERO, 1=ONE}
```
→ `map`은 key와 value를 처리하는 함수가 따로 존재한다. <br>
  `filterKeys`, `mapKeys`: key를 걸러내거나 변환 <br>
  `filterValues`, `mapValues`: value를 걸러내거나 변환
<br>
<br>
<br>
<br>

### 5.2.2 all, any, count, find: 컬렉션에 술어 적용
```
all, any: 컬렉션의 모든 원소가 어떤 조건을 만족하는지 판단하는 연산
count: 조건을 만족하는 원소의 개수를 반환하는 연산
find: 조건을 만족하는 첫 번쨰 원소를 반환하는 연산
```
<br>
<br>

**Ex)** 어떤 사람의 나이가 27살 이하인지 판단하는 함수인 canBeInClub27
```kotlin
val canBeInClub27 = { p: Person -> p.age <= 27 }
```
<br>

1) all
```
>>> val people = listOf(Person("Alice", 27), Person("Bob", 31))
>>> println(people.all(canBeInClub27))
false
```
→ people의 모든 원소가 canBeInClub27 술어를 만족하는지 판단하기 위해 `all` 사용
<br>
<br>

2) any
```
>>> println(people.any(canBeInClub27)
true
```
→ people의 원소 중 canBeInClub27을 만족하는 원소가 하나라도 있는지 판단하기 위해 `any` 사용
<br>
<br>
<br>

> 드 모르간의 법칙에 의해, 어떤 조건에 대해 !all을 수행한 결과와 그 조건의 부정에 대해 any를 수행한 결과는 같다. <br>
> 마찬가지로, 어떤 조건에 대해 any를 수행한 결과와 그 조건의 부정에 대해 !all을 수행한 결과 또한 같다. 

```
>>> val list = listOf(1, 2, 3)
>>> println(!list.all { it == 3 }) //list의 모든 원소가 3인 것은 아니다. (= 적어도 하나의 원소는 3이 아니다.)
true
>>> println(list.any{ it != 3 }) // any를 사용하면 술어를 부정해야 한다 (!=)
true
```
<br>
<br>

3) count
```
>>> val people = listOf(Person("Alice", 27), Person("Bob", 31))
>> println(people.count(canBeInClub27))
1
```
→ people의 원소 중 canBeInClub27을 만족하는 원소의 개수를 구하기 위해 `count` 사용
<br>
<br>

4) find
```
>>> val people = listOf(Person("Alice", 27), Person("Bob", 31))
>>> println(people.find(canBeInClub27))
Person(name=Alice, age=27)
```
→ canBeInClub27을 만족하는 원소를 하나 찾기 위해 `find` 사용 <br>
→ `find`는 조건을 만족하는 원소가 하나라도 있으면 그 원소를 반환하고, 만족하는 원소가 없을 경우 null을 반환한다. <br>
   따라서 `find`는 `firstOrNull`과 같다.
<br>
<br>
<br>

### 5.2.3 groupBy: 리스트를 여러 그룹으로 이뤄진 맵으로 변경
> 컬렉션의 모든 원소를 어떤 특성에 따라 여러 그룹으로 분류하고 싶을 때 사용하는 groupBy
<br>

- **groupBy**: 특성을 파라미터로 전달하면 자동으로 컬렉션을 구분해주는 함수
```
>>> val people = listOf(Person("Alice", 31),
  ...Person("Bob", 29), Person("Carol", 31))
>>> println(people.groupBy { it.age })
{29=[Person(name=Bob, age=29)],
31=[Person(name=Alice, age=31), Person(name=Carol, age=31)]}
```
→ groupBy 함수 연산의 결과는 컬렉션의 원소를 구분하는 **특성** (= age)이 **key**이고, key 값에 따른 **각 그룹**(= Person 객체의 모임)이 **value**인<br>
`map`이다. <br>
→ 출력 결과에서 각 그룹은 list이고, groupBy의 결과 타입은 Map<Int, List<Person>>이다.
<br>
<br>
<br>

### 5.2.4 flatMap과 flatten: 중첩된 컬렉션 안의 원소 처리
**Ex)** 
```kotlin
class Book(val title: String, val authors: List<String>)
```
```kotlin
books.flatMap { it.authors }.toSet() //books 컬렉션에 있는 책의 모든 저자들의 집합
```
<br>
<br>
  
- **flatMap**: 인자로 주어진 람다를 컬렉션의 모든 객체에 적용(맵핑)하고, 람다를 적용해 얻은 여러 list를 한 list로 모은다(flatten).
```
>>> val strings = listOf("abc", "def")
>>> println(strings.flatMap { it.toList() })
[a, b, c, d, e, f]
```
→ abc, def에 toList를 적용하면 문자열을 구성하는 모든 문자로 이루어진 list가 만들어지므로, a, b, c로 이루어진 list와 d, e, f로 이루어진 list가<br> 만들어진다. <br>
→ 다음으로, flatMap 함수는 list에 들어있던 모든 원소로 구성된 **단일** list를 반환한다.
<br>
<br>
<br>
  
```
>>> val books = listOf(Book("Thursday Next", listOf("Jasper Fforde")),
...                    Book("Mort", listOf("Terry Pratchett")),
...                    Book("Good Omens", listOf("Terry Pratchett",
...                                              "Neil Gaiman")))
>>> println(books.flatMap { it.authors }.toSet())
[Jasper Fforde, Terry Pratchett, Neil Gaiman]
```
→ `books.authors` 프로퍼티는 작가를 모아둔 컬렉션이고, `flatMap` 함수는 모든 책의 작가를 flat(문자열만으로 이루어진)한 하나의 list로 모은다. <br>
  `toSet`은 flatMap의 결과로 만들어진 list에서 중복을 없애고 집합으로 만들기 때문에, 최종 출력 부분에서는 Terry Pratchett 작가가 한 번만 포함된다.
<br>
<br>
  
- 모든 중첩된 list의 원소를 한 list로 모을 때는 `flatMap`을 사용하고, 반환할 내용이 없다면 list의 list(중첩된 list)를 펼치기만 하면 되므로,<br>
  이런 경우 `flatten` 함수를 사용한다.
