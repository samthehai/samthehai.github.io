---
title: "Thuật toán cơ bản: Dynamic Programming"
date: "2021-02-28"
description: "Tìm hiểu về Dynamic Programming hay còn gọi là Quy hoạch động trong Tiếng Việt"
tags: [Programming, Algorithm, Dynamic Programming]
---

---

# 1. Giới thiệu

Khi học thuật toán chắc chắn các bạn đã một lần nghe tới Dynamic programming hay còn gọi là quy hoạch động.
(nếu mà chưa nghe thì giờ cũng đã nghe rồi) Đây là phương pháp dùng để tối ưu hoá thời gian tính toán các bài toán có phần tính toán lặp đi lặp lại bằng cách cache lại các bài toán nhỏ đã được tính toán trước đó để dùng lại sau này.

Nghe có vẻ cũng đơn giản chỉ là lưu cache lại thôi, tuy nhiên đây là phần khó hiểu và master trong thuật toán mà lập trình viên nào học thuật toán cũng phải đau đầu vật lộn với nó.

Vậy thì có bạn sẽ hỏi tôi làm lập trình viên mấy chục năm trời có thấy cần biết và sử dụng nó đâu?
Câu trả lời là có được sử dụng nhiều trong các lĩnh vực cần giải quyết các bài toán tối ưu.
Có thể do ngày nay cấu hình máy tính bình thường đã mạnh lên rất nhiều nên là nhiều khi ta viết code không cần phải tối ưu đến nhỏ hơn O(n^2) chương trình vẫn chạy rất là nhanh. Tuy nhiên khi ta quan tâm đến performance của hệ thống và muốn tối ưu nó thì có thể quy hoạch là vấn đề ta cần quan tâm (cái này còn tuỳ thuộc vào lĩnh vực của hệ thống của mình nữa)

Một ví dụ về ứng dụng của dynamic programming là trong việc implement thuật toán propagation đình đám của [Neuro Network](http://blog.ezyang.com/2011/05/neural-networks/)

Tuy nhiên, ta có thể thấy mục đích của dynamic programming là cải thiện thuật toán naive có sẵn (bruceforce đệ quy, ...), vì vậy điều kiện đầu tiên để master được nó là bạn phải nghĩ ra được cách giải vét cạn của nó trước cái đã. Vì vậy sau đây ta sẽ đi trước vào các phương pháp để tìm ra các naive solution trước đã.

# 2. Phương pháp đệ quy và cách giải "naive" cho các bài toán đối tượng của dynamic programing.

Ta sẽ bắt đầu phần này bằng một ví dụ.

## 2.1 Ví dụ

### 2.1.1 Cách giải "naive" đệ quy của bài toán tìm phần tử thứ n của dãy Fibonacci Numbers

Về dãy Fibonacci có thể tham khảo ở [đây](https://vi.wikipedia.org/wiki/D%C3%A3y_Fibonacci)

Chuỗi Fibonacci sẽ có dạng như này:

```
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, ...
```

Nói nôm na là số thứ n bằng tổng của hai số thứ n - 1 và n - 2, hai số đầu tiên là 0 và 1.

Sau một hồi mò tìm thì ta cho ra được một đáp án naive này.

```Go
func fib(n int) int {
  if n <= 1 {
    return n
  }

  return fib(n - 1) + fib(n - 2)
}
```

Minh hoạ các hàm sẽ được gọi khi tính fib(5)

```
                          fib(5)
                     /                \
               fib(4)                fib(3)
             /        \              /       \
         fib(3)      fib(2)         fib(2)   fib(1)
        /    \       /    \        /      \
  fib(2)   fib(1)  fib(1) fib(0) fib(1) fib(0)
  /     \
fib(1) fib(0)
```

### 2.1.2 Cách giải "naive" đệ quy của bài toán tìm giai thừa của một số (factorial)

Về giai thừa có thể tham khảo ở [đây](https://vi.wikipedia.org/wiki/Giai_th%E1%BB%ABa)

Không dài dòng nữa ta đưa ngay ra thuật toán đệ quy đơn giản luôn

```Go
func fac(n int) int {
  if n < 1 {
    return 1
  }

  return n * fac(n - 1)
}
```

```
                      fac(5)
                     /
               fac(4)
             /
         fac(3)
        /
  fac(2)
  /
fac(1)
```

## 2.2 Rút ra bài học :)

### 2.2.1.

Qua hai bài toán trên ta thấy cách giải đơn giản đệ quy thật sự khi tìm ra được lời giải rồi thì thấy rất là đơn giản :)

Với cách giải bài toán thứ nhất ta thấy có nhiều hàm được tính đi tính lại nhiều lần như là fib(3), fib(2), fib(1), fib(0)

Còn đối với bài toán thứ hai ta không thấy việc lặp lại tính toán ở đây.

### 2.2.2

Giả sử vào thời chưa có máy tính thì những lúc như thế này ta sẽ dùng nay giấy bút để note lại những thứ đã tính, nếu ko có giấy, bút thì dùng cây quẹt ra đất cũng được.

Đối với một coder ta chắc chắn sẽ sử dụng một biến gì đó để lưu lại rồi.
Và việc dùng biến (hay còn gọi là cache) để lưu lại kết quả đã tính toán người ta đặt cho nó một cái tên huyền bí là Dynamic Programming dịch ra tiếng Việt là Quy Hoạch Động :)

### 2.2.3

Dễ dàng nhận ra là ta có thể dùng quy hoạch động để giải quyết bài toán số 1, còn bài toán số 2 thì không thể vì nó không có sự lặp đi lặp lại tính toán ở đây.

### 2.2.4 Nguồn tham khảo về các naive solution khác.

- https://algorithm-visualizer.org/

  - simple-recursive
  - brute-force

# 3. Dynamic Programming

## 3.1. Tabulation vs Memoization (hay còn gọi là Bottom-up vs Top-Down)

Như ở trên mô tả Dynamic Programming đơn giản chỉ là lưu lại các giá trị của các bài toán con nhỏ hơn để dùng sau. Tuy nhiên vậy thôi mà cũng có hai cách để lưu :)

### 3.1.1. Tabulation (Bottom-Up)

Lưu tất cả các bài toán con của bài toán cần giải quyết bằng cách tính  và tạo một bảng giá trị từ các subproblem nhỏ nhất trước (Sau khi tạo xong rồi thì ta chỉ việc tra bảng và trả về giá trị cần tìm, lúc này là ưu việt nhất là sử dụng array, vì nó cho access trực tiếp phần tử và các máy tính hiện tại đều có hỗ trợ cache cho kiểu dữ liệu array)


n    | 1  | 2  | 3  | 4  | 5  |
--   | -- | -- | -- | -- |--  |
f(n) | 0  | 1  | 1  | 2  |  3 |

# Nguồn tham khảo
