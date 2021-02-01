---
title: "Cấu trúc dữ liệu trong Go: Interfaces"
date: "2020-12-27"
---

# 1. Giới thiệu

Interfaces của Go - là static, được kiểm tra lúc compile, dynamic khi cần thiết- theo tôi là phần thú vị nhất của Go theo góc nhìn thiết kế. Nếu có thể chọn một feature để export sang ngôn ngữ khác tôi sẽ chọn interface.

Bài viết này là quan điểm của tôi khi hiện thực giá trị interface trong các trình biên dịc "gc": 6g, 8g, và 5g. Tại Airs, Ian Lance Taylor có viết hai bài [post](https://www.airs.com/blog/archives/277) về việc hiện thực giá trị interface trong gccgo. Việc hiện thực có nhiều điểm giống hơn là khác nhau: sự khác biệt lớn nhất là bài viết này có hình ảnh :D.

Trước khi đi vào việc hiện thực hãy xem những gì nó hỗ trợ.

# 2. Cách dùng

Interface của Go cho phép bạn sử dụng [Duck Typing](http://en.wikipedia.org/wiki/Duck_typing) giống như cách mà bạn sử dụng nó ở những ngôn ngữ động như Python nhưng vẫn cho phép trình biên dịch bắt những lỗi hiển nhiên như truyền vào giá trin int vào nơi mà đáng nhẽ phải là một object với hàm Read, hoặc là gọi hàm Read với số lượng tham số không đúng. Để sử dụng Interface trước hết ta sẽ định nghĩa kiểu interface (như là ReadCloser)

```Go
type ReadCloser interface {
  Read(b []byte) (n int, err os.Error)
  Close()
}
```

và sau đó định nghĩa function mới của bạn nhận vào một ReadCloser. Ví dụ, hàm này lặp gọi hàm Reaad để lấy tất cẩ dữ liệu được request và sau đó gọi hàm Close:

```Go
func ReadAndClose(r ReadCloser, buf []byte) (n int, err os.Error) {
  for len(buf) > 0 && err == nil {
    var nr int
    nr, err = r.Read(buf)
    n += nr
    buf = buf[nr:]
  }
  r.Close()
  return
}
```

Phần code gọi `ReadAndClose` có thể truyền vào một giá trị của bất cứ kiểu nào mà nó có các method `Read` và `Close`. Và, không giống như Python, nếu bạn truyền vào một giá trị sai kiểu, trình biên dịch sẽ báo lỗi lúc biên dịch, chứ không phải lúc chạy.

Interfaces không chỉ giới hạn ở việc kiểm tra kiểu dữ liệu tĩnh. Bạn có thể kiểm tra động việc một giá trị interface có những method khác. Ví dụ:

```Go
type Stringer interface {
  String() string
}

func ToString(any interface{}) string {
  if v, ok := any.(Stringer); ok {
    return v.String()
  }
  switch v := any.(type) {
    case int:
      return strconv.Itoa(v)
    case float:
      return strconv.Ftoa(v, 'g', -1)
  }

  return "???"
}
```

Còn nữa ...

# Tham khảo

Bài này được dịch từ bài viết của Russ Cox https://research.swtch.com/interfaces
