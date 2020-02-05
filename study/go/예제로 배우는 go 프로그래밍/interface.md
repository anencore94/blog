## Interface

### 정의

- struct 가 필드의 집합체라면 interface 는 메서드들의 집합체이다.
    - 인터페이스는 해당 type 이 반드시 구현해야 하는 메서드들을 정의한다.
    - custom type (struct) 가 interface 를 구현하기 위해서는 단순히 그 인터페이스가 갖는 모든 메서드들을 구현하면 된다.
        - 완전히 동일한 세트의 method 들을 갖고 이름만 다른 interface 가 두 개 (A, B) 있을 경우, 해당 method 세트를 구현한 타입은 자동으로 두 개의 interface A, B 를 모두 구현한 것이 된다.
- 하나의 type 은 여러 interface 를 구현할 수 있다.

```
// Shape 이라는 이름의 interface 가 정의됨
type Shape interface {
    area() float64
    perimeter() float64
}
```

- 다음과 같이 type 을 정의하고 interface 의 메소드들을 구현해주기만하면 해당 interface 를 구현하게 된다.

```
//Rect 정의
type Rect struct {
    width, height float64
}

//Circle 정의
type Circle struct {
    radius float64
}

//Rect 타입에 대한 Shape 인터페이스 구현
func (r Rect) area() float64 {
    return r.width * r.height
}
func (r Rect) perimeter() float64 {
     return 2 * (r.width + r.height)
}

//Circle 타입에 대한 Shape 인터페이스 구현
func (c Circle) area() float64 {
    return math.Pi * c.radius * c.radius
}
func (c Circle) perimeter() float64 {
    return 2 * math.Pi * c.radius
}
```

- 인터페이스의 사용
    - 당연히 특정 함수(showArea)에서 입력 parameter 로 interface 를 받으면, 해당 interface 를 구현한 모든 type 을 입력받을 수 있다.
```
func main() {
    r := Rect{10., 20.}
    c := Circle{10}

    showArea(r, c)
}

func showArea(shapes ...Shape) {
    for _, s := range shapes {
        a := s.area() //인터페이스 메서드 호출
        println(int64(a))
    }
}
```

### 빈 인터페이스

- empty interface 는 interface type 으로도 불리우며, 다른 언어에서 일컫는 Dynamic Type 이다.(java 의 object type)
    - 즉, 어떠한 type 도 담을 수 있는 type 이다.
    - go 의 모든 type 은 적어도 0 개의 method 를 구현하기 때문

```
package main

import "fmt"

func printIt(v interface{}) {  // printIt 은 모든 type 을 입력 parameter 로 받을 수 있는 함수
    fmt.Println(v)
}

func main() {
    var x interface{} // 변수를 empty interface 로 선언
    x = 1
    x = "Tom"

    printIt(x) // Tom 출력
}
```

