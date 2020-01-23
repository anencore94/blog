# ginkgo 튜토리얼

> 본 가이드는 onsi에서 제공하는 ginkgo [공식 문서](http://onsi.github.io/ginkgo/)를 바탕으로 ginkgo 를 처음 사용하는 사람을 위해 작성되었습니다. <br>
> 위의 문서의 앞부분을 따라하며 개인 공부 겸 작성하였으며, 주관적 판단으로 크게 중요하지 않은 내용은 생략하였습니다.

## 테스트 환경

- go1.13.5 linux/amd64
- Ubuntu 18.04.3 LTS (Desktop)

## 목차

- 1. Ginkgo 설치
- 2. 시작해보기 : 첫 테스트 작성

--------------------------

## 1. Ginkgo 설치

> Ginkgo 는 Go v1.6 이상에서 테스트되었으며 적정 버전의 Go 설치 및 세팅은 되어있다고 가정합니다.

### 설치 과정은 간단합니다

- 다음의 명령어를 실행하면 됩니다
  - 다음 명령어는 ginkgo, gomega pkg 를 다운받고, $GOPATH/bin 아래 ginkgo 실행파일을 다운받습니다.

```{sh}
$ go get github.com/onsi/ginkgo/ginkgo
$ go get github.com/onsi/gomega/...
```

- 혹시 gomega 전체 패키지가 아닌 일부 패키지만 다운받고 싶다면 `go get -t` 를 사용하시기 바랍니다.
  - gomega 는 주로 ginkgo 와 함께 사용되는 matcher/assertion 기능을 제공하는 library 입니다.

--------------------------

## 2. 시작해보기 : 첫 테스트 작성

> Ginkgo 는 Go 에서 기본적으로 제공하는 `testing infrastructure` 를 사용합니다.

### Bootstrapping a Suite

> 적절한 한국어 단어를 찾지 못하였습니다. 대략 Ginkgo 에서 샘플 형태로 제공하는 테스트 세트를 불러오는 것을 의미합니다.

```{sh}
$ mkdir -p ~/go/src/books # 테스트를 위해 $GOPATH/src/books 디렉토리를 생성합니다.
$ cd ~/go/src/books
$ ginkgo bootstrap
```

 - 다음과 같이 출력되어야 하며, 다른 stdout, stderr 가 확인된다면 Go 세팅 등을 확인바랍니다.
```{sh}
Generating ginkgo test suite bootstrap for books in:
	books_suite_test.go
```

- 생성된 books_suite_test.go 를 확인해보면 다음과 같습니다.
```{sh}
package books_test

import (
    . "github.com/onsi/ginkgo"
    . "github.com/onsi/gomega"
    "testing"
)

func TestBooks(t *testing.T) {
    RegisterFailHandler(Fail)
    RunSpecs(t, "Books Suite")
}
```

- 각각의 라인을 살펴보겠습니다.
  - `package book_test`
    - TODO go package 공부 후 작성
  - `. "github.com/onsi/ginkgo"`
    - package 를 import 할 때, alias namespace 를 등록하지 않고 dot import 로 설정하면 top-level namespace 로써 사용할 수 있습니다.
    - 간단히 말해서, ginkgo 패키지의 변수, 메소드 등을 namespace 를 지정하지 않고 바로바로 사용할 수 있습니다.
      - 위의 예의 `Fail`, `RunSpecs` 함수가 ginkgo 패키지의 함수라는 것을 보면 이를 확인할 수 있습니다.
  - `func TestBooks`
    - 해당 함수는 go 에서 제공하는 표준 "testing" 패키지에서 인식하는 테스트 메소드입니다.
      - `go test` 또는 `ginkgo` 명령어는 현재 폴더의 모든 `*_test.go` 파일들을 테스트 코드로 인식하고, 일괄적으로 실행합니다.
      - 이 때, `*_test.go` 파일 내의 `TestXxx`라는 형태의 메소드를 테스트 메소드로 인식합니다.
      - 테스트 메소드는 다음 형식을 지켜야 합니다.
        - `Xxx` 는 임의로 지정할 수 있으나 첫 글자는 반드시 대문자여야 하며
        - input parameter 로 `testing.T` 포인터를 받으며 return 은 하지 않습니다.
  - `RegisterFailHandler(Fail)`
    - ginkgo test 는 ginkgo 의 `Fail` 함수를 콜하여 테스트의 실패를 알립니다.
    - ginkgo 와 gomega 를 연결하기 위해 자주 사용되는 방식으로, gomega 의 `RegisterFailHandler` 함수를 사용해 이 `Fail` 함수를 넘깁니다.
  - `RunSpecs`
    - ginkgo 에게 test suite 를 시작하라고 알립니다.

- 위와 같이 세팅된 상황에서 `ginkgo` 를 실행하면 다음과 같은 화면을 볼 수 있습니다.

```{sh}
$ ginkgo #or go test

=== RUN TestBootstrap

Running Suite: Books Suite
==========================
Random Seed: 1378936983

Will run 0 of 0 specs


Ran 0 of 0 Specs in 0.000 seconds
SUCCESS! -- 0 Passed | 0 Failed | 0 Pending | 0 Skipped

--- PASS: TestBootstrap (0.00 seconds)
PASS
ok      books   0.019s
```

### Suite 에 스펙 추가하기

- 위에서 실행한 테스트 코드는 빈 테스트 코드였습니다.
- 여기서는 새로운 파일을 만든 후, 실제 테스트를 해보도록 하겠습니다.

```{sh}
$ ginkgo generate book
```

- 위 명령을 실행하면 다음과 같은 book_test.go 파일이 생성됩니다.

```{sh}
package books_test

import (
	. "github.com/onsi/ginkgo"
	. "github.com/onsi/gomega"

	. "books" // 해당 파일이 위치한 디렉토리
)

var _ = Describe("Book", func() {

})

```

- 다시 한 줄 한 줄 살펴보겠습니다.
  - `import ()`
    - 앞서 언급했던 dot import 를 사용하여 3 개의 패키지를 import 한 모습입니다.
  - `var _ = Describe .....`
    - ginkgo 패키지의 Describe 함수를 사용하여 describe container 를 추가하였습니다.
    - `var _ = ... ` 는 `func init() {}` 로 감싸지 않고 top-level 에서 Describe 를 evaluate 하도록 하는 일종의 트릭입니다.
      - Describe 함수의 반환값(bool 타입)을 _ 에 바로 저장하게 됩니다.
      - TODO 더 조사 필요

- 이제 `Describe(text string, body func()) bool {}` 함수의 func() 에 원하는 스펙을 적어보겠습니다.

```
var _ = Describe("Book", func() {
    var (
        longBook  Book
        shortBook Book
    )

    BeforeEach(func() {
        longBook = Book{
            Title:  "Les Miserables",
            Author: "Victor Hugo",
            Pages:  1488,
        }

        shortBook = Book{
            Title:  "Fox In Socks",
            Author: "Dr. Seuss",
            Pages:  24,
        }
    })

    Describe("Categorizing book length", func() {
        Context("With more than 300 pages", func() {
            It("should be a novel", func() {
                Expect(longBook.CategoryByLength()).To(Equal("NOVEL"))
            })
        })

        Context("With fewer than 300 pages", func() {
            It("should be a short story", func() {
                Expect(shortBook.CategoryByLength()).To(Equal("SHORT STORY"))
            })
        })
    })
})
```

- 위의 auto-gen 파일은 `type Book` 과 `func CategoryByLength` 를 포함하고 있지 않기 때문에 컴파일 에러가 발생합니다.
  - 따라서 본인은 같은 폴더에 다음과 같은 `book.go` 를 직접 생성해야 했습니다.
  - TODO 버그인지, 생략된 내용인지, 제가 무언가를 잘못 입력하여 자동 생성이 되지 않았는지 확인 필요

    ```{sh}
    package books

    type Book struct {
        Title  string `json:"title"`
        Author string `json:"author"`
        Pages  int    `json:"pages"`
    }

    func (b *Book) CategoryByLength() string {
        if b.Pages > 24 {
            return "NOVEL"
        }
        return "SHORT STORY"
    }

    ```

- 이제 다시 위의 `book_test.go` 파일을 한 줄 한 줄 살펴보겠습니다.
  - Describe block 에는 spec 을 적습니다. Describe block 은 임의의 수의 BeforeEach, AfterEach, JustBeforeEach, It, Measurement block 을 포함할 수 있습니다.
  - BeforeEach block 에서는 각각의 spec 이전에 실행할 상태를 세팅합니다.
  - It block 에서는 single spec 만 기술 가능합니다.
  - 각 block 에서 공유할 변수는 longBook, shortBook 처럼 최상위 block 에 기술합니다.
  - Gomega 의 Expect 함수를 사용하여 단언문을 사용합니다.
    - 단언문은 Expect 혹은 Omega 를 사용할 수 있으며, 형태만 다를 뿐 하는 일은 동일합니다.

- 이제 이 test 코드를 다시 돌려보면 2 개의 spec(테스트 케이스)를 실행했으며 모두 성공했음을 확인할 수 있습니다.

```{sh}
=== RUN   TestBooks
Running Suite: Books Suite
==========================
Random Seed: 1579761378
Will run 2 of 2 specs

••
Ran 2 of 2 Specs in 0.000 seconds
SUCCESS! -- 2 Passed | 0 Failed | 0 Pending | 0 Skipped
--- PASS: TestBooks (0.00s)
PASS
```

- 확실하게 하기 위하여, 의도적으로 Fail case 를 추가하고, 어떤 output 이 출력되는지 확인해보겠습니다.
  - 다음과 같은 spec 을 `Describe("Categorizing book length", func() {}` 내부에 추가하였습니다.

    ```{sh}
            Context("Intentional Failure case ?", func() {
                It("should fail", func() {
                    Expect(shortBook.CategoryByLength()).To(Equal("EXPECTED CATEGORY"))
                })
            })
    ```

- 출력은 다음과 같습니다.
  - 3 개 spec 실행하였으며, 1 개 spec 이 Failed 되었고, expected vs to equal 를 비교해주는 것을 확인할 수 있습니다.

```{sh}
=== RUN   TestBooks
Running Suite: Books Suite
==========================
Random Seed: 1579761560
Will run 3 of 3 specs

••
------------------------------
• Failure [0.000 seconds]
Book
/home/kjy/go/src/books/book_test.go:10
  Categorizing book length
  /home/kjy/go/src/books/book_test.go:30
    Intentional Failure case ?
    /home/kjy/go/src/books/book_test.go:43
      should fail [It]
      /home/kjy/go/src/books/book_test.go:44

      Expected
          <string>: SHORT STORY
      to equal
          <string>: EXPECTED CATEGORY

      /home/kjy/go/src/books/book_test.go:45
------------------------------


Summarizing 1 Failure:

[Fail] Book Categorizing book length Intentional Failure case ? [It] should fail 
/home/kjy/go/src/books/book_test.go:45

Ran 3 of 3 Specs in 0.000 seconds
FAIL! -- 2 Passed | 1 Failed | 0 Pending | 0 Skipped
--- FAIL: TestBooks (0.00s)
FAIL

```

--------------------------

## 3. 이후

- 기본적인 내용은 익혔으니 이젠 각자 야생학습을 통해 여러 테스트 코드 및 spec 을 작성해보러 떠납시다.