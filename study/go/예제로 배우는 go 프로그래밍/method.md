## Method

### 정의

- 일반적인 OOP 의 클래스가 필드와 메서드를 함께 갖는 것과 달리 Go 에서는 struct 는 필드만 가지고, 메서드는 분리되어 정의된다.
- Go 의 메서드는 특별한 형태의 func 함수이다.
    - `func {$메서드 리시버} {$반환 type}` 꼴로 정의한다.
    - `{$메서드 리시버}` 는 `{$변수명} {$type명}` 으로 정의한다.
    - 메서드는 선언만 되고나면 struct 객체로부터 직접 호출할 수 있다.


```
//Rect - struct 정의
type Rect struct {
    width, height int
}
 
//Rect의 area() **메소드**
func (r Rect) area() int {
    return r.width * r.height   
}
 
func main() {
    rect := Rect{10, 20}
    area := rect.area() //메서드 호출
    println(area)
}
```

- 메서드는 struct 에만 사용할 수 있는 것은 아니고 기본형에도 사용할 수 있다.
    ```
    package main

    import (
        "fmt"
        "math"
    )

    type MyFloat float64

    func (f MyFloat) Abs() float64 {
        if f < 0 {
            return float64(-f)
        }
        return float64(f)
    }

    func main() {
        f := MyFloat(-math.Sqrt2)
        fmt.Println(f.Abs())
    }
    ```

### value receiver vs pointer receiver

- 말 그대로, value 만 전달하는 receiver 와 pointer 를 전달하는 receiver 가 있다.
    - 메서드 내에서 struct 의 필드값이 변경될 경우, value receiver 를 사용했을 때는 호출자 struct 의 데이터는 변경되지 않으나, pointer 를 전달했을 경우 호출자 strcut 의 데이터도 변경된다.
    ```
    // value Receiver
    func (r Rect) area2() int {
        r.width++
        return r.width * r.height
    }

    // 포인터 Receiver
    func (r *Rect) area3() int {
        r.width++
        return r.width * r.height
    }

    func main() {
        rect := Rect{10, 20}

        areaValue := rect.area2() // 메서드 호출
        println(rect.width, areaValue) // 10, 220 출력
        // area2 내부적으로만 width 를 +1 시켜서 사용하였으나 호출자인 rect 에는 적용 안 됨

        areaPointer := rect.area3() //메서드 호출
        println(rect.width, areaPointer) // 11, 220 출력
        // 호출자의 width 값이 변경됨
    }
    ```
