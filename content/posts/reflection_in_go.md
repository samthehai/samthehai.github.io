---
title: "Reflection trong Go"
date: "2020-12-27"
---

# 1. Giới thiệu

Go cung cấp một cơ chế dể cập nhật biến, và kiểm tra giá trị của nó lúc runtime, để gọi hàm,... mà không cần biết về kiểu dữ liệu của nó lúc biên dịch. Cơ chế này gọi là `reflection`. Ta có thể xem định nghĩa về `reflection` trên [Wikipedia](https://en.wikipedia.org/wiki/Reflective_programming)

> In computer science, reflection programming is the ability of a process to examine, introspect, and modify its own structure and behavior.[1]

Trong computer science, `reflection programming` là khả năng của một process để thực thi, kiểm tra và thay đổi cấu trúc và hành vi của chính nó.

# 2. Tại sao lại cần Reflection?

Đôi khi chúng ta cần viết một hàm có khả năng hỗ trợ nhiều kiểu dữ liệu không phải là những interface thông thường, hoặc là chưa tồn tại ở thời điểm mình viết function, được diễn đạt ở dạng chưa biết trước, ... Ví dụ như là phần logic của hàm `fmt.Fprintf`, mà ta có thể dùng để in bất cứ kiểu dữ liệu gì mình muốn. Để minh hoạ ta thử xây dựng hàm `fmt.Sprint`:

```Go
func Sprint(x interface{}) string {
  type stringer interface {
    String() string
  }

  switch x := x.(type) {
    case stringer:
      return x.String()
    case string:
      return x
    case int:
      return strconv.Itoa(x)
    // ...tương tự cho các trường hợp int16, uint32,...
    case bool:
      if x {
          return "true"
      }
      return "false"
    default:
      // array, chan, func, map, pointer, slice, struct
      return "???"
  }
}
```

Nhưng còn đối với các trường hợp khác như `[]float64, map[string][]string`,... ta có thể cứ tiếp tục thêm vào case nhưng những kiểu dữ liệu này là vô tận. Và cả những kiểu dữ liệu tự định nghĩa nữa. Trong trường hợp này điều chúng ta cần chính là reflection.

# 3. Type và Interface

Bởi vì reflection được xây dựng dựa trên hệ thống kiểu dữ liệu (type) nên ta sẽ bắt đầu bằng việc nhắc lại hệ thống kiểu dữ liệu trong Go

Go là `statically typed` có nghĩa là kiểu dữ liệu tĩnh cố định không giống như Python hay Java là `dynamically typed`, mỗi biến sẽ có một kiểu dữ liệu tĩnh cố định và được xác định lúc biên dịch như là int, float32, \*Mytype, ...Nếu chúng ta khai báo

```Go
type MyInt int

var i int
var j MyInt
```

thì i sẽ có kiểu int, j có kiểu là MyInt. các biến i, j có kiểu dữ liệu khác nhau mặc dù ở ẩn giấu bên trong chúng là cùng một kiểu, chúng không được assign cho lẫn nhau mà không sử dụng hàm convertion.

Một loại kiểu dữ liệu quan trọng là kiểu interface, sẽ đại diện cho một tập hợp các method. Một biến interface có thể chứa bất cứ giá trị concrete (không phải interface) nào mà thoả mãn interface đó (bằng cách implement tất cả các methods mà interface đó định nghĩa). Ta có thể tham khảo ví dụ mọi người hay sử dụng trong [io.package](https://golang.org/pkg/io/)

```Go
// Reader là interface wrap hàm Read
type Reader interface {
  Read(p []byte) (n int, err error)
}
// Writer là interface wrap hàm Write
type Writer interface {
  Write(p []byte) (n int, err error)
}
```

Bất cứ kiểu nào mà implement hàm `Read` (hoặc `Write`) được gọi là implement `io.Reader` (hoặc `io.Writer`) và có thể chứa bất cứ giá trị nào mà có kiểu dữ liệu có chứa `Read` method

```Go
var r io.Reader
r = os.Stdin
r = bufio.NewReader(r)
r = new(bytes.Buffer)
// and so on
```

Điều quan trọng cần phải ghi nhớ là dù cho r có chứa bất cứ giá trị nào thì kiểu dữ liệu của nó luôn là `io.Reader` bởi vì Go là `statically typed` và kiểu dữ liệu tĩnh của r là `io.Reader`.

Một ví dụ rất quan trọng khác của interface là kiểu interface rỗng: `interface {}`
Nó đại diện một tập hợp rỗng các methods, và vì vậy nó sẽ được thoả mãn bởi bất cứ kiểu dữ liệu nào.

# 4. Biểu diễn của một interface

Về chi tiết bạn có thể tham khảo bài viết của Russ Cox ở [đây](https://research.swtch.com/2009/12/go-data-structures-interfaces.html).
Tôi sẽ tóm tắt lại nội dung của nó ở đây

Một biến của kiểu interface sẽ chứa một cặp: giá trị concrete được gán cho biến đó, và phần mô tả kiểu dữ liệu của nó.

```Go
var r io.Reader
tty, err := os.OpenFile("/dev/tty", os.O_RDWR, 0)
if err != nil {
    return nil, err
}
r = tty
```

r chứa một cặp (value, type), (tty, *os.File). Chú ý ở đây `*os.File`hiện thực các methods khác ngoài hàm Read; mặc dù giá trị của interface chỉ cung cấp access vào duy nhất hàm`Read` nhưng giá trị bên trong của nó chứa đầy đủ tất cả các thông tin về kiểu dữ liệu của giá trị đó. Chính vì vậy mà ta có thể implement như dưới này:

```Go
var w io.Writer
w = r.(io.Writer)
```

Đây là một kiểu `type assertion`, nó kiểm tra kiểu dữ liệu bên trong của r có implement io.Writer hay không, và vì vậy mà ta có thể gán nó cho w. Sau khi assign, w sẽ chứa cặp giá trị giống với cặp giá trị của r (tty, \*os.File). Kiểu static sẽ xác định những method nào sẽ được gọi từ biến interface, mặc dù giá trị concrete bên trong của nó có thể có nhiều methods khác nữa.

Tiếp tục chúng ta có thể làm như dưới này:

```Go
var empty interface{}
empty = w
```

Và biến empty của chúng ta một lần nữa lại chứa cùng cặp giá trị (tty, \*os.File)

Điều cần nhớ ở đây là cặp giá trị bên trong một interface luôn luôn là (value, concrete type) không thể có cặp giá trị (value, interface type). Interface không chứa giá trị của interface.

# 5. Các nguyên tắc của reflection

## 5.1. Reflection đi từ giá trị của interface đến reflection object

Về cơ bản, reflection chỉ là cơ chế để kiểm tra cặp kiểu và giá trị chứa bên trong biến interface. Để bắt đầu có hai điều ta cần phải biết về [package reflect](https://golang.org/pkg/reflect/): [Type](https://golang.org/pkg/reflect/#Type) và [Value](https://golang.org/pkg/reflect/#Value). Những kiểu này cung cấp access đến nội dung của một biến interface, và hai function `reflect.TypeOf` và `reflect.ValueOf` truy xuất `reflect.Type` và `reflect.Value` từ biến interface. (Thực ra từ `reflect.Value` ta có thể dễ dàng lấy được thông tin về `reflect.Type` nhưng hãy tách biệt hai khái niệm này ra khỏi nhau từ bây giờ).

Bắt đầu với `TypeOf`:

```Go
package main

import (
    "fmt"
    "reflect"
)

func main() {
    var x float64 = 3.4
    fmt.Println("type:", reflect.TypeOf(x))
}
```

Chương trình này sẽ in ra `type: float64`

Bạn sẽ thắc mắc interface ở đây là cái nào, bởi vì chương trình này giống như là kiểu truyền một biến x float64, không phải là một giá trị interface, vào hàm `reflect.TypeOf`. Tuy nhiên theo mô tả ở đây [godoc report](https://golang.org/pkg/reflect/#TypeOf) kí hiệu (signature) của `reflect.TypeOf` bao gồm cả interface rỗng.

```Go
// TypeOf returns the reflection Type of the value in the interface{}.
func TypeOf(i interface{}) Type
```

Khi chúng ta gọi `reflect.TypeOf(x)`, x đầu tiên được chứa vào một interface rỗng, sau đó interface rỗng này sẽ được truyền vào `reflect.TypeOf` và bung ra lại thông tin kiểu dữ liệu và tất nhiên là cả thông tin về value.

```Go
var x float64 = 3.4
fmt.Println("value:", reflect.ValueOf(x).String())
```

sẽ in ra `value: <float64 Value>`

Ở đây ta phải gọi hàm String bởi vì `fmt.Println` sẽ tự động truy xuất vào bên trong reflect.Value để hiển thị giá trị trị concrete bên trong của nó. hàm String thì không làm như vậy

Cả `reflect.Value` và `reflect.Type` có nhiều method, chúng ta hãy cũng nhau kiểm tra chúng. Một ví dụ quan trọng là `Value` có một method tên là `Type` trả về type của reflect.Value. Cả hai `Value` và `Type` đều có method `Kind` trả về một hằng số mô tả loại dữ liệu gì được chứa Uint, Float64, Slice,... Và các method của `Value` như `Int` và `Float` cho phép ta lấy giá trị được lưu bên trong:

```Go
var x float64 = 3.4
v := reflect.ValueOf(x)
fmt.Println("type:", v.Type())
fmt.Println("kind is float64:", v.Kind() == reflect.Float64)
fmt.Println("value:", v.Float())
```

sẽ in ra

```bash
type: float64
kind is float64: true
value: 3.4
```

Còn có những hàm như `SetInt` và `SetFloat` nhưng để sử dụng được chúng ta phải hiểu rõ cách gán giá trị sẽ được mô tả ở nguyên tắc thứ 3.

Trong thư viện reflection còn có những đặc tính khác cần được mô tả. Để cho đơn giản method setter và getter của `Value` sẽ thao tác trên kiểu dữ liệu lớn nhất mà có thể chứa giá trị. ví dụ như là `int64` cho tất cả giá trị integer, điều này có nghĩa là method `Int` của `Value` sẽ trả về kiểu `int64` và method `SetInt` sẽ nhận kiểu `int64` làm thông số đầu vào.

```Go
var x uint8 = 'x'
v := reflect.ValueOf(x)
fmt.Println("type:", v.Type())                            // uint8.
fmt.Println("kind is uint8: ", v.Kind() == reflect.Uint8) // true.
x = uint8(v.Uint())                                       // v.Uint returns a uint64.
```

Đặc tính thứ hai là `Kind` sẽ mô tả kiểu dữ liệu ẩn bên trong chứ không phải là kiểu static, nếu một reflection object chứa một giá trị của kiểu dữ liệu tự định nghĩa MyType, như sau

```Go
type MyInt int
var x MyInt = 7
v := reflect.ValueOf(x)
```

Method `Kind` của v vẫn sẽ là reflect.Int, mặc dù kiểu dữ liệu static của nó là MyInt, điều này thì khác với method `Type`

## 5.2. Reflection đi từ reflection object đến interface value.

Giống như hiện tượng vật lý reflection, reflection trong Go cũng có hiện tượng phản ngược của nó.
Cho một `reflect.Value` ta có thể hồi phục một interface value sử dụng `Interface` method; method này sẽ đóng gói thông tin type và value vào lại một interface đại diện và trả về kết quả.

```Go
// Interface returns v's value as an interface{}.
func (v Value) Interface() interface{}
```

Ta cũng có thể dùng như sau

```Go
y := v.Interface().(float64) // y will have type float64
fmt.Println(y)
```

để in giá trị float64 đại diện bởi reflection object v.

Bởi vì `fmt.Println`, `fmt.Printf`,... đều nhận vào tham số là giá trị interface rỗng và sau đó chúng sẽ được tháo ra bởi package `fmt` như cách chúng ta làm ở ví dụ trên, vì vậy ta có thể làm tốt hơn bằng cách truyền kết quả của method `Interface` vào hàm `fmt.Println`.

```Go
fmt.Println(v.Interface())
```

Thậm chí ta có thể sử dụng format floating-point nếu muốn:

```Go
fmt.Printf("value is %7.1e\n", v.Interface())
```

sẽ trả ra kết quả `3.4e+00`

## 5.3. Để chỉnh sửa một reflection object, value cần phải settable.

Nguyên tắc thứ ba hơi khó hiểu và dễ gây nhầm lẫn, sẽ dễ hiểu hơn nếu ta bắt đầu từ nguyên lý đầu tiên.

Sau đây là một vài đoạn code không working nhưng mà đáng để xem xét

```Go
var x float64 = 3.4
v := reflect.ValueOf(x)
v.SetFloat(7.1) // Error: will panic.
```

Nếu bạn thực thi đoạn code này, sẽ có panic với message

```
panic: reflect.Value.SetFloat using unaddressable value
```

Vấn đề thì không phải là không xác định được địa chỉ của 7.1, mà là v thì không `settable`. `settable` là đặc tính của reflection Value, nhưng không phải tất cả reflection Values đều có nó.

Ta có thể sử dụng `CanSet` method của `Value` để kiểm tra xem đặc tính `settable` của một `Value`

```Go
var x float64 = 3.4
v := reflect.ValueOf(x)
fmt.Println("settability of v:", v.CanSet())
```

sẽ ra output

```bash
settability of v: false
```

Khi chúng ta gọi Method `Set` cho những Value không có đặc tính `settable` sẽ gây nên lỗi, vậy thì đặc tính này là gì? Nó phản ánh reflection object có khả năng thay đổi giá trị thật được dùng để tạo ra reflection object ban đầu hay không, nó xác định reflection object có lưu giữ item gốc hay không. Khi ta gọi

```Go
var x float64 = 3.4
v := reflect.ValueOf(x)
```

ta truyền một bản copy của x vào method `ValueOf` nên là interface value được tạo ra không nắm giữ giá trị gốc x mà chỉ là bản sao của nó vì vậy mà khi gọi method `Set` sẽ bị panic.

Sau đây ta sẽ thử truyền địa chỉ của x vào method `ValueOf`

```Go
var x float64 = 3.4
p := reflect.ValueOf(&x) // Note: take the address of x.
fmt.Println("type of p:", p.Type())
fmt.Println("settability of p:", p.CanSet())
```

output vẫn sẽ là false

```
type of p: *float64
settability of p: false
```

Nhưng thật ra ta sẽ không muốn thay đổi gía trị của p mà giá trị của phần tử được trỏ đến bởi p, vì vậy ta sẽ gọi `Elem` method để lấy giá trị được trỏ đến bởi p

```Go
v := p.Elem()
fmt.Println("settability of v:", v.CanSet())
```

và v giờ thì có đặc tính `setable`

```
settability of v: true
```

Cuối cùng ta cũng đã có thể sử dụng method `Set` để thay đổi giá trị của x

```Go
v.SetFloat(7.1)
fmt.Println(v.Interface())
fmt.Println(x)
```

Và output như mong đợi

```
7.1
7.1
```

# 6. Structs

Một cách phổ biến sử dụng reflection là áp dụng nó để thay đổi các trường của một struct, chỉ cần có địa chỉ của struct ta có thể thay đổi các trường bên trong nó.

```Go
type T struct {
  A int
  B string
}

t := T{23, "skido"}
s := reflect.ValueOf(&t).Elem()
typeOfT := s.Type()
for i := 0; i < s.NumField(); i++ {
  f := s.Field(i)
  fmt.Printf("%d: %s %s = %v\n", i,
    typeOfT.Field(i).Name, f.Type(), f.Interface())
}
```

Output sẽ là

```
0: A int = 23
1: B string = skidoo
```

Có một điểm cần lưu ý ở đây nữa là chỉ những field được export mới có thể là `setable`.
Bởi vì s chứa một `setable` reflection object, chúng ta có thể thay đổi giá trị của các field bên trong nó

```Go
s.Field(0).SetInt(77)
s.Field(1).SetString("Sunset Strip")
fmt.Println("t is now", t)
```

Và output sẽ là

```
t is now {77 Sunset Strip}
```

# Reference

https://blog.golang.org/laws-of-reflection

[The Go Programming Language](https://www.gopl.io/), Chapter 12. Reflection

https://en.wikipedia.org/wiki/Reflective_programming

https://yourbasic.org/golang/generics/

https://dev.to/designpuddle/coding-concepts---reflection-4d2c
