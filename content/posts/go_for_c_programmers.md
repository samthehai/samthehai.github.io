---
title: "Golang dành cho dân lập trình C"
date: "2020-03-15"
---

# 1. Giới thiệu về Go

## 1.1. Động lực

| Hiện tại đang có                  |                               Muốn                                |                                                                      Golang |
| --------------------------------- | :---------------------------------------------------------------: | --------------------------------------------------------------------------: |
| Code khó hiểu và không an toàn    |                 Dễ đọc, code an toàn và hiệu quả                  |                                                    Nhỏ gọn mà biểu diễn tốt |
| Build code siêu chậm              |                Một hệ thống có thể mở rộng (scale)                | Kiểu dữ liệu tĩnh (statically) và có hỗ trợ thu dọn rác (garbage collected) |
| Không có hỗ trợ lập trình đồng bộ |                   Hỗ trợ lập trình đồng bộ tốt                    |                     Đối tượng nhưng không định hướng kiểu (type - oriented) |
| Tools cũ kĩ                       | Những Tools mà có thể hoạt động tại những hệ thống lớn như Google |                                                Hỗ trợ lập trình đồng bộ tốt |
|                                   |                                                                   |                                                          Hiện thực hiệu quả |
|                                   |                                                                   |                                                    Thư viện chuẩn phong phú |
|                                   |                                                                   |                                                             Biên dịch nhanh |
|                                   |                                                                   |                                                   Tools có thể mở rộng được |

## 1.2. Hello World

```Go
package main

import "fmt"

func main() {
  fmt.Println("Hello, 世界!")
}
```

## 1.3. Syntax

> Syntax is not important... - unless you are a programmer.

Rob Pike.

> The readability of programs is immeasurably more important than their writeability.

Hints on Programming Language Design

C. A. R. Hoare 1973

### 1.3.1. Quá dài dòng

```C
scoped_ptr<logStats::LogStats>
    logStats(logStats::LogStats::NewLogStats(FLAGS_logStats,logStats::LogStats::kFIFO));
```

### 1.3.2. Quá dày đặc

```Haskell
(n: Int) => (2 to n) |> (r => r.foldLeft(r.toSet)((ps, x) =>
    if (ps(x)) ps -- (x * x to n by x) else ps))
```

### 1.3.3. Chuẩn

```Go
t := time.Now()
switch {
case t.Hour() < 12:
    return "morning"
case t.Hour() < 18:
    return "afternoon"
default:
    return "evening"
}
```

## 1.4. Đọc code Go

### 1.4.1. Packages

Một chương trình Go sẽ bao gồm các packages.
Một packages sẽ bao gồm 1 hoặc nhiều files mã nguồn (go files).
Một file mã nguồn sẽ bắt đầu với lệnh package như khai báo dưới này:

```Go
package main

import "fmt"

func main() {
    fmt.Println("Hello, 世界!")
}
```

Theo quy ước thì tất cả các file thuộc về một package sẽ nằm chung một thư mục.

### 1.4.2. Khai báo, sử dụng Pascal-style (từ trái sang phải)

Pattern: \<keyword\> \<tên\> \[kiểu dữ liệu\]\[khởi tạo\]

```Go
import "fmt"

const digits = "0123456789abcdef"

type Point struct {
    x, y int
    tag  string
}

var s [32]byte

var msgs = []string{"Hello, 世界", "Ciao, Mondo"}

func itoa(x, base int) string
```

### 1.4.3. Why?

```Go
p, q *Point

func adder(delta int) func(x int) int
```

### 1.4.4. Constants (Hằng số)

Trong Go, constants thì chính xác về mặt toán học
Không cần thiết phải có hậu tố phía sau (như là -42LL, 7UL,...)

```Go
const (
    MaxUInt = 1<<64 - 1
    Pi      = 3.14159265358979323846264338327950288419716939937510582097494459
    Pi2     = Pi * Pi
    Delta   = 2.0
)
```

Chỉ khi được sử dụng constant mới được cắt ngắn theo kích thước.

```Go
    var x uint64 = MaxUInt
    var pi2 float32 = Pi2
    var delta int = Delta
```

Lợi thế lớn là ở tính dễ đọc và dễ sử dụng

### 1.4.5. Types (Kiểu)

- Phần quen thuộc:
  - Kiểu cơ bản, arrays, structs, pointers, functions.
- Tuy nhiên:
  - string là kiểu cơ bản
  - Không có tự động chuyển đổi kiểu cơ bản trong dòng lệnh.
  - Không có tính toán pointer (con trỏ), pointer và array là khác nhau.
  - Một kiểu function sẽ đại diện cho 1 function; context và tất cả
- Mới:
  - Slices thay vì array pointer + độ dài khác nhau: []int
  - Maps bởi vì mọi người đều cần nó: map[string]int
  - Interfaces được dùng cho việc đa hình (polymorphism): interface {}
  - Channels để giao tiếp giữa các goroutines: chan int

### 1.4.6. Biến

- Phần quen thuộc:

```Go
var i int
var p, q *Point
var threshold float64 = 0.75
```

- Mới: Kiểu có thể được diễn giải từ cách khởi tạo

```Go
var i = 42       // type của i là int
var z = 1 + 2.3i // type của z là complex128
```

- Shortcut (bên trong functions):

```Go
i := 42 // type của i là int
```

- Thao tác lấy địa chỉ của bất kì biến nào là an toàn:

```Go
return &i
```

### 1.4.7. Functions

Function có thể trả về nhiều giá trị:

```Go
func atoi(s string) (i int, err error)
```

Closures

```Go
func addler(delta int) func(x int) int {
  f := func (x int) int {
    return x + delta;
  }
  return f
}
```

```Go
var inc = addler(1)
fmt.Println(inc(0))
fmt.Println(addler(-1)(10))
```

### 1.4.8. Statments (Câu lệnh)

```Go
t := x;
switch {
case x == 0:
  return "0"
case x < 0:
  t = -x
}
var s [32]byte
i := len(s)
for t != 0 {
  i--
  s[i] = digits[t%base]
  t /= base
}
if x < 0 {
  i--
  s[i] = '-'
}
return string(s[i:])
```

### 1.4.9. Statement của Go dành cho người lập trình C

- Làm rõ ràng hơn:
  - Không có dấu chấm phẩy
  - Gán nhiều giá trị
  - ++ và -- cũng là câu lệnh
  - Không có dấu mở đóng ngoặc ở điều kiện, dấu mở đóng ngoặc nhọn là bắt buộc
  - Break được ngầm hiểu trong switch, làm rõ ràng việc fallthrough
- Mới:
  - for range
  - type switch
  - go, select
  - defer

### 1.4.10. Assignment (Gán giá trị)

Có thể gán nhiều giá trị đồng thời

```Go
a, b  = x, y
```

Bằng với

```Go
t1 := x
t2 := y
a = t1
b = t2
```

Ví dụ

```Go
a, b = b, a       // swap a and b
i, err = atoi(s)  // assign results of atoi
i, _ = atoi(991)  // discard 2nd value
```

### 1.4.11. Switch statements

Switch statement có thể có nhiều case và break được hiểu ngầm

```Go
switch {
case 1, 2, 3, 4, 5:
  tag = "workday"
case 0, 6:
  tag = "weekend"
default:
  tag = "invalid"
}
```

Giá trị trong case thì không bắt buộc phải là constanst

```Go
switch {
case day < 0 || day > 6:
  tag = "invalid"
case day == 0 || day == 6:
  tag = "weekend"
default:
  tag = "workday"
}
```

### 1.4.12. Vòng lặp for

```Go
for i := 0; i < len(primes); i++ {
  fmt.Println(i, primes[i])
}
```

Mệnh đề range cho phép duyệt qua array và slice dễ dàng

```Go
for i, x := range primes {
  fmt.Println(i, x)
}
```

Những giá trị không sử dụng được bỏ qua bằng cách gán vào định danh (identifier) \_

```Go
var sum int
for _, x := range primes {
  sum += x
}
```

## 1.5. Dependencies (Phụ thuộc)

### 1.5.1. Dependence trong Go

Một khai báo import được dùng để mô tả một dependency trong một package khác:

```Go
import "net/rpc"
```

Ở đây importing package phụ thuộc vào Go package "rpc"

Đường dẫn import ("net/rpc") xác định duy nhất một package; nhiều packages có thể có cùng tên, nhưng chúng lại có thể nằm ở những vị trí khác nhau (thư mục)

Quy định thì tên của package sẽ là phần tử cuối cùng của đường dẫn import (ở đây là "rpc")

Chức năng export của package rpc thì sẵn có ở trong package qualifier (rpc):

```Go
rpc.Call
```

Một khai báo import trong Go có vai trò giống như include trong C.

### 1.5.2. Dạo quanh một vòng với cách đặt tên trong Go

Cách mà tên hoạt động thì có ảnh hưởng quan trọng đến tính dễ đọc.

Scope thì quyết định cách thức hoạt động của tên.

Go có một cấu trúc scope tương đối đơn giản:

- universe
- package
- file (chỉ được dùng cho việc import)
- function
- block

### 1.5.3. Tính cục bộ của tên (TODO)

Tên viết hoa sẽ được exported: Name vs name

Package qualifier luôn đại diện cho tên được import

Component đầu tiên của mọi tên luôn được khai báo trong package hiện tại

Đây là 1 trong những quyết định đúng đắn và khó khăn nhất trong Go

### 1.5.4. Mở rộng cục bộ (Locality scales)

Không có bất ngờ khi import

- Việc export thêm 1 tên sẽ không phá vỡ package của tôi.

Tên không bị rò rỉ vượt ra khỏi package.
Trong C, C++, Java tên y có thể gắn với bất cứ thứ gì.
Trong Go, y (và cả Y) thì phải luôn được định nghĩa trong package.
Trong Go, x.Y thì rõ ràng hơn: tìm x trong local, Y thì thuộc về x và chỉ có duy nhất một Y như vậy.

Có hiệu quả tức thì cho tính dễ đọc.

### 1.5.5. Trở về với việc import

Importing một package có nghĩa là đọc một API được export từ package đó.
Thông tin export này là tự chứa (self-contain). Ví dụ:

- A imports B
- B imports C
- Những export từ B chứa tham khảo đến C

Những dữ liệu export của B chứa tất cả những thông tin cần thiết về C. Một compiler thì không cần thiết phải đọc dữ liệu export bởi C.

Việc này có ảnh hưởng rất lớn đến thời gian build!

### 1.5.6. Dependence trong C

File .h thì không tự chứa.
Như một kết quả, một compiler cần phải lặp lại việc đọc những file header cốt lõi.
Sử dụng ifdef vẫn yêu cầu preprocessor phải đọc rất nhiều code.
Hiển nhiên là việc này tốn rất nhiều thời gian...
Đối với một hệ thống lớn như Google việc bùng nổ depedence là cấp số trở nên không thể tính toán được.
Một bản build C++ của Google có thể đọc cùng một file header mười ngàn lần!!!

## 1.6. Tools (Công cụ)

### 1.6.1. Giới thiệu

Hai compiler: gc, gccgo
Hỗ trợ nhiều platform khác nhau: x86 (32/64bit), ARM (32bit), Linux, BSD, OS X, ...
Tự dộng format source code: gofmt
Tự động extract document: godoc
Tự động lấy API: gofix

Tất cả (và còn hơn nữa)! dược tích hợp vào go command.

### 1.6.2. Build một chương trình Go

Một chương trình Go có thể dược biên dịch và linked mà không cần thêm bất kì thông tin gì như makefiles,...
Theo quy định thì tất cả các file thuộc về một package sẽ nằm cùng trong thư mục.
Tất cả các package phụ thuộc sẽ được tìm thấy bằng cách truy theo dường dẫn import ở package ngoài cùng (main packagee).
Một tool đơn được tích hợp có thể đảm nhận việc build cho từng file hoặc cả hệ thống.

### 1.6.3. Câu lệnh trong Go

Cách sử dụng:

```bash
go command [arguments]
```

Các lệnh có thể:

```bash
build       compile packages and dependencies
clean       remove object files
doc         run godoc on package sources
fix         run go tool fix on packages
fmt         run gofmt on package sources
get         download and install packages and dependencies
install     compile and install packages and dependencies
list        list packages
run         compile and run Go program
test        test packages
vet         run go tool vet on packages
```

golang.org/cmd/go/

# 2. Object-oriented programming

## 2.1. What is object-oriented programming?

> Object-oriented programming (OOP) is a programming paradigm using objects – usually instances of a class – consisting of data fields and methods together with their interactions – to design applications and computer programs. Programming techniques may include features such as data abstraction, encapsulation, messaging, modularity, polymorphism, and inheritance. Many modern programming languages now support forms of OOP, at least as an option.

(Wikipedia)

## 2.2. OOP chỉ yêu cầu thêm rất ít hỗ trợ từ phía ngôn ngữ lập trình

Chùng ta chỉ cần:

- Khái niệm về _Object_
- Một cơ chế dể tương tác với chúng (_Methods_)
- Và hỗ trợ việc đa hình (_Interfaces_)

Claim: Data abstraction, encapsulation, và modularity là những cơ chế độc lập với OOP và một ngôn ngữ hiện đại (OOP hoặc không) cũng đều nên hỗ trợ chúng một cách độc lập.

## 2.3. Lập trình hướng đối tượng trong Go

Có phương thức (methods) không có class

Có interface không có phân chia cấp bậc (hierarchies)

Sử dụng lại code mà không cần thừa kế (inheritance)

Cụ thể:

- Bất cứ value nào cũng có thể là object
- Bất cứ type nào cũng có thể có vai trò là class
- Methods có thể đính kèm vào bất cứ type nào
- Interface hiện thực tính đa hình (polymorphism).

## 2.4. Methods

```Go
package main

import "fmt"

type Point struct{ x, y int }

func PointToString(p Point) string {
  return fmt.Sprintf("Point{%d, %d}", p.x, p.y)
}

func (p Point) String() string {
  return fmt.Sprintf("Point{%d, %d}", p.x, p.y)
}

func main() {
  p := Point{3, 5}
  fmt.Println(PointToString(p)) // static dispatch
  fmt.Println(p.String())       // static dispatch
  fmt.Println(p)
}
```

Output:

```bash
Point{3, 5}
Point{3, 5}
Point{3, 5}
```

## 2.5. Methods có thể được đính kèm vào bất cứ type nào

```Go
package main

import "fmt"

type Celsius float32
type Fahrenheit float32

func (t Celsius) String() string           { return fmt.Sprintf("%g°C", t) }
func (t Fahrenheit) String() string        { return fmt.Sprintf("%g°F", t) }
func (t Celsius) ToFahrenheit() Fahrenheit { return Fahrenheit(t*9/5 + 32) }

func main() {
  var t Celsius = 21
  fmt.Println(t.String())
  fmt.Println(t)
  fmt.Println(t.ToFahrenheit())
}
```

## 2.6. Interfaces

```Go
type Stringer interface {
     String() string
}

type Reader interface {
     Read(p []byte) (n int, err error)
}

type Writer interface {
     Write(p []byte) (n int, err error)
}

type Empty interface{}
```

Một interface định nghĩa một tập hợp các method
Một type hiện thực tất cả các method của một interface được gọi là hiện thực interface.
Tất cả các type hiện thực interface interface{}

## 2.7. Dispatch động

```Go
type Stringer interface {
  String() string
}

var v Stringer
var corner = Point{1, 1}
var boiling = Celsius(100)

v = corner
fmt.Println(v.String()) // dynamic dispatch
fmt.Println(v)

v = boiling.ToFahrenheit()
fmt.Println(v.String()) // dynamic dispatch
fmt.Println(v)
```

Một giá trị (ở đây là corner, boiling) của một type (Point, Celcius) mà hiện thực một interface (Stringer) có thể được assign vào một biến (v) của type đó.

## 2.8. Composition và chaining

Thông thường interface có quy mô nhỏ (Tầm 1 -3 method).

Việc sử dụng xen kẽ các interface chính trong thư viện chuẩn giúp dễ dàng xâu chuỗi (chain) các API lại với nhau.

```Go
package io func Copy(dst Writer, src Reader) (int64, error)
```

Function io.Copy copy bằng cách đọc từ bất cứ Reader nào và ghi vào bất cứ Writer nào.

Interface thường được giới thiệu là tạm thời, và giống như trong thực tế.

Không có hệ thống phân cấp rõ ràng và do dó cũng không cần design bất cứ hệ thống phân cấp nào.

## 2.9. Cat

```Go
package main

import (
	"flag"
	"io"
	"os"
)

func main() {
	flag.Parse()
	for _, arg := range flag.Args() {
		f, err := os.Open(arg)
		if err != nil {
			panic(err)
		}
		defer f.Close()
		_, err = io.Copy(os.Stdout, f)
		if err != nil {
			panic(err)
		}
	}
}
```

## 2.10. Interface trong thực hành

- Method ở bất cứ type nào và những interface tạm tạo thành 1 kiểu lập trình hướng đối tượng nhỏ gọn.

- Go interface tạo nên một trừu tượng hậu facto.

- Không có phân cấp kiểu rõ ràng

- Plug and play bằng cách type-safe

# 3. Đồng bộ (concurrency)

## 3.1. Đồng bộ là gì?

Đồng bộ là việc đóng gói những thực thi tính toán độc lập.

Đồng bộ là cách để cấu trúc phần mềm, như là một cách đặc biệt để viết clean code tương tác tốt với thế giới thực.

Đồng bộ thì không phải việc tính toán song song

## 3.2. Đồng bộ không phải là song song

Đồng bộ không phải là song song, mặc dù nó kích hoạt tính song song.

Nếu bạn chỉ có một processor, chương trình của bạn vẫn có thể đồng bộ nhưng nó không thể song song được.

Nói một cách khác, một chương trình đồng bộ tốt có thể chạy một cách hiệu quả trên multiprocessor. Đặc tính này thì quan trọng ...

Để thêm thông tin chi tiết, hãy tham khảo link dưới này.

  http://golang.org/s/concurrency-is-not-parallelism

## 3.3. Một model để xây dựng phần mềm

Dễ hiểu.

Dễ dùng.

Dễ nêu lý do.

Bạn không cần phải là một chuyên gia!

(Là dễ dàng hơn nhiều việc đối mặt với các loại xử lý song song (threads, semaphores, locks, barriers,...))

Có một lịch sử dài đằng sau tính năng đồng bộ của Go, kể từ thời Hoare's CSP năm 1978 và thậm chí là những câu lệnh đảm bảo của Dijkstra (1975).

## 3.4. Ví dụ đơn giản

### 3.4.1. Một function đơn giản

Chúng ta cần một ví dụ để chỉ ra những đặc tính thú vị nổi bật của việc đồng bộ.

Để tránh xao nhãng, chúng ta sẽ tạo ra một ví dụ đơn giản

```Go
func main() {
  f("Hello, World", 500*time.Millisecond)
}
```

```Go
func f(msg string, delay time.Duration) {
  for i := 0; ; i++ {
    fmt.Println(msg, i)
    time.Sleep(delay)
  }
}
```

### 3.4.2 Bỏ qua nó

Dòng lệnh go thực thi function như bình thường, nhưng không bắt caller phải đợi.

Nó chạy một goroutine.

Chức năng tương tự như là & ở cuối một lệnh shell.

```Go
func main() {
  go f("three", 300*time.Millisecond)
  go f("six", 600*time.Millisecond)
  go f("nine", 900*time.Millisecond)
}
```

### 3.4.3. Bỏ qua nó bớt đi một ít

Khi hàm main return, chương trình kết thúc và function f kết thúc theo nó.

Chúng ta có thể gặp nhau một ít, và nhân tiện chứng mình rằng cả main và goroutine được tạo ra đều đang chạy

```Go
func main() {
  go f("three", 300*time.Millisecond)
  go f("six", 600*time.Millisecond)
  go f("nine", 900*time.Millisecond)
  time.Sleep(3*time.Second)
  fmt.Println("Done.")
}
```

### 3.4.5. Goroutines

Goroutine là gì? Nó là một function thực thi độc lập, được khởi phát bởi một lệnh go.

Nó có call stack riêng, sẽ thu giảm theo nhu cầu.

Cheap. Trong thực tế sẽ có hàng ngàn, thậm chí hàng trăm ngàn goroutine.

Không phải là thread.

Có thể có chỉ một thread trong một chương trình với hàng ngàn goroutine.

Thay vì như vậy, goroutine được phân luồng động thành các thread khi cần thiết để giữ tất cả goroutine hoạt động.

Bạn có thể nghĩ goroutine gần giống như là một thread rất nhẹ (very cheap)

### 3.4.6. Channels

Một channel trong Go cung cấp một kết nối giữa hai goroutine, cho phép chung giao tiếp với nhau.

```Go
// Khai báo và khởi tạo
var c chan int c = make(chan int)
// hoặc
c := make(chan int)
```

```Go
// Gửi đến một channel
c <- 1
```

```Go
// Nhận từ một channel
// Dấu mũi tên ám chỉ hướng của dòng dữ liệu.
value = <-c
```

### 3.4.7. Sử dụng channel

Một channel sẽ kết nối hàm main và f goroutine để chúng có thể giao tiếp với nhau.

```Go
func main() {
  c := make(chan string)
  go f("three", 300*time.Millisecond, c)
  for i := 0; i < 10; i++ {
    fmt.Println("Received", <-c) // Lệnh Receive chỉ là một giá trị
  }
  fmt.Println("Done.")
}
```

```Go
func f(msg string, delay time.Duration, c chan string) {
  for i := 0; ; i++ {
    c <- fmt.Sprintf("%s %d", msg, i) // Bất cứ giá trị thích hợp nào có thể được gửi.
    time.Sleep(delay)
  }
}
```

### 3.4.8. Đồng bộ

Khi function main thực thi <-c, nó sẽ chờ một giá trị để được gửi.

Tương tự, khi function f thực thi c<-value, nó sẽ đợi một bên nhận sẵn sàng.

Một bên gửi và bên nhận phải đều sẵn sàng thì mới có thể giao tiếp được. Ngược lại chúng ta phải đợi cho đến khi chúng sẵn sàng.

Vì vậy các channel đều phải giao tiếp và đồng bộ.

Channel có thể không buffer hoặc có buffer.

### 3.4.9. Sử dụng channel giữa nhiều goroutine

```Go
func main() {
  c := make(chan string)
  go f("three", 300*time.Millisecond, c)
  go f("six", 600*time.Millisecond, c)
  go f("nine", 900*time.Millisecond, c)

  for i := 0; i < 10; i++ {
    fmt.Println("Received", <-c)
  }

  fmt.Println("Done.")
}
```

Một channel đơn có thể được dùng để giao tiếp giữa nhiều (không chỉ là hai) goroutine; nhiều goroutine có thể giao tiếp thông qua một hoặc nhiều channel.

Điều này cho phép tạo ra sự đa dạng trong các cấu trúc đồng bộ (concurrency pattern).

### 3.4.10. Các yếu tố của một working-stealing scheduler

```Go
func worker(in chan int, out chan []int) {
  for {
    order := <-in // Nhận một chỉ thị công việc.
    result := factor(order)
  }
}
```

Các worker sử dụng hai channel để giao tiếp:
- in channel đợi các tính toán về chỉ thị công việc
- out channel giao tiếp kết quả.
- Bởi vì cân bằng tải, một worker (một cách rất chậm) sẽ tính toán danh sách các ước chung của một chỉ thị cho sẵn.

### 3.4.11. Một matching giữa producer và consumer

```Go
func producer(out chan int) {
  for order := 0; ; order++ {
    out <- order // Tạo ra một chỉ thị công việc.
  }
}

func consumer(in chan []int, n int) {
  for i := 0; i < n; i++ {
    result := <-in // Nhận một kết quả
    fmt.Println("Consumed", result)
  }
}
```

Bên producer tạo ra và cung cấp không giới hạn chỉ thị công việc và gửi chúng ra ngoài.

Bên consumer nhận n kết quả từ in channel và sau đó kết thúc.

### 3.4.12. Gộp tất cả chung với nhau

```Go
func main() {
  start := time.Now()
  in := make(chan int)    // Channel mà thứ tự công việc sẽ được nhận.
  out := make(chan []int) // Channel mà kết quả được trả về.
  go producer(in)
  go worker(in, out)      // Khởi phát một worker.
  consumer(out, 100)
  fmt.Println(time.Since(start))
}
```

Chúng ta sử dụng một worker đảm nhận tất cả công việc liên quan đến cân bằng tải (work load).

Bởi vì chỉ có duy nhất một worker, chúng ta có thể thấy kết quả trả ngược về đúng thứ tự.

Đoạn này chạy khá là chậm...

### 3.4.13. Sử dụng 10 worker

```Go
in := make(chan int)
out := make(chan []int)
go producer(in) // Khởi phát 10 workers.

for i := 0; i < 10; i++ {
  go worker(in, out)
}

consumer(out, 100)
```

Một worker sẵn sàng sẽ đọc các chỉ thị tiếp theo từ channel in và bắt đầu làm việc từ đó.

Một worker sẵn sàng sẽ tiến hành với các chỉ thị tiếp theo và cứ thế tiếp diễn.

Bởi vì chúng ta có nhiều worker và vì vậy những chỉ thị khác nhau sẽ tốn thời gian khác nhau, chúng ta thấy kết quả trả về không đúng thứ tự nữa.

Trong một hệ thống nhiều core, nhiều worker có thể chạy hoàn toàn song song với nhau.

Đoạn mã này thì chạy nhanh hơn nhiều...

### 3.4.14. Cách tiếp cận của Go

Không giao tiếp bằng cách chia sẻ bộ nhớ, mà là chia sẻ bộ nhớ bằng  cách giao tiếp.

## Links tham khảo

- Trang chủ Go

  golang.org

- Go Tour (học Go qua browser của bạn)

  tour.golang.org

- Tài liệu về package:

  golang.org/pkg

- Các bài viết phong phú khác:

  golang.org/doc

