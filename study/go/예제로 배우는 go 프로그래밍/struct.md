## Struct

### 정의

- Struct 는 custom data type 을 정의할 때 사용한다. struct 에는 필드만 속할 수 있고, 메소드는 갖지 않는다는 점이 java 의 클래스와는 다르다.
    - 하지만 Method 를 사용하여 struct 와 함께 사용할 수 있다.
    - 이렇게 Go 언어는 전통적인 OOP 방식으로 클래스, 객체, 상속 개념을 제공하지는 않지만, 고유의 방식으로 OOP 를 제공한다.
    - [유튜브 링크](https://www.youtube.com/watch?v=1ZjvhGfpwJ8&list=FLJSJJ1PsImE7v2dZLoQOMSA&index=6&t=4s)

### 선언

- 다음과 같이 type 문으로 선언하여 각각의 필드를 정의한다.

```
type person struct {
    name string
    age  int
}
```

### 객체 생성

- 5 가지 방법 존재

- 첫 번째 방법
```
// 객체 생성 후에 필드값 일일히 설정
p := person{}
p.name = "Lee"
p.age = 10
```

- 두 번째 방법
```
// 미리 선언한 변수에 객체 생성 + 초기값 할당
var p1 person
p1 = person{"Bob", 20}
```

- 세 번째 방법
```
// 변수 생성과 동시에 객체 생성 + 초기값 할당
p2 := person{name: "Sean", age: 50} // struct 에 정의된 필드의 이름 순서에 상관없이 필드값을 key-value 로 지정할 수 있으며, 일부 필드가 생략된 경우는 각 type 에 맞는 zero value 로 할당된다.
```

- 네 번째 방법
```
// Go 내장함수 new() : 모든 필드를 초기화하고 해당 struct 의 포인터를 return 함
p := new(person)
p.name = "Lee" // p 가 포인터라도 .으로 필드에 접근 가능*
```

- go 에서 struct 는 mutuable 개체이다.
    - 필드값이 변화할 경우 새로운 개체를 만들지 않고 해당 개체 메모리에 있는 값을 직접 변경
    - 하지만 struct 개체를 다른 함수의 parameter 로 넘기면 pass by value 에 따라 개체의 복사본이 전달된다.
        - 그러나, struct 의 포인터를 넘기면 pass by reference 로 사용할 수 있다.

- 다섯 번째 방법

```
// 생성자를 미리 만들어놓고 사용

package main
 
type dict struct {
    data map[int]string
}
 
//생성자 함수 정의
func newDict() *dict {
    d := dict{}
    d.data = map[int]string{}
    return &d //포인터 전달
}
 
func main() {
    dic := newDict() // 생성자 호출
    dic.data[1] = "A"
}
```