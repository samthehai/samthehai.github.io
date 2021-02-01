---
title: "Thuật toán cơ bản: phân tích độ phức tạp giải thuật"
date: "2020-10-24"
---

# 1. Giới thiệu

Đối với một người làm về khoa học máy tính, thuật toán chắc chắn phải là một trong những khái niệm đầu tiên phải học.

Vậy thì thuật toán là gi?

Một thuật toán là một quá trình để thực hiện một task, thuật toán là ý tưởng bên trong của bất kì chương trình máy tính nghiêm chỉnh nào.

Ta có thể nói một thuật toán thì sẽ phải giải quyết một vấn đề nào đó và để giải quyết một vấn đề có thể có nhiều thuật toán khác nhau.

Ví dụ:

Vấn đề sắp xếp:

```text
- Input: Nhận vào một chuỗi các khoá x1, x2, x3, ...
- Output: Sắp xếp lại thứ tự của chuỗi đầu vào sao cho theo thứ tự x1' <= x2' <= x3' ...
```

Để giải quyết vấn đề sắp xếp ta có các thuật toán sắp xếp: merge sort, quick sort, insertion sort, selection sort, ...

# 2. Phân tích thuật toán

Có nhiều thuật toán để giải quyết một vấn đề.

Vì vậy sẽ nảy sinh vấn đề làm sao để lựa chọn thuật toán nào cho vấn đề nào.

Để giải quyết vấn đề đó chúng ta sẽ phải phân tích thuật toán.

Có hai công cụ quan trọng để đánh giá thuật toán: mô hình RAM, Big-O

## 2.1. Mô hình RAM

1. Mỗi thao tác đơn giản (+, -, *, /, if, call) tốn đúng một bước

2. Vòng lặp và hàm thì không phải là thao tác đơn giản và sẽ tốn nhiều hơn 1 bước để thực hiện

3. Mỗi thao tác truy xuất bộ nhớ sẽ tốn đúng một bước

Hiển nhiên chúng ta có thể thấy các điều kiện ở trên chỉ có tính tương đối và không đúng với thực tiễn.

Nhưng khi áp dụng mô hình RAM nó lại khiến cho việc phân tích thuật toán trở nên đơn giản và hiệu quả.

### 2.1.1 Độ phức tạp theo thời gian

Khi áp dụng mô hình trên người ta thường sẽ để ý đến các trường hợp:

1. Trường hợp tốt nhất (best-case) là số lượng bước ít nhất cần để thực hiện thuật toán

2. Trường hợp tệ nhất (worst-case) là số lượng bước nhiều nhất cần để thực hiện thuật toán

3. Trường hợp trung bình (average-case) là số lượng bước trung bình để thực hiện thuật toán

Trường hợp tệ nhất đóng vai trò quan trọng trong thực tiễn và thường ta chỉ quan tâm đến nó mà bỏ qua hai trường hợp còn lại.

### 2.1.2 Cách xác định độ phức tạp theo thời gian

Độ phức tạp theo thời gian dùng để ước lượng thời gian cần thiết để thực thi giải thuật và được tính toán dựa vào số bước thực hiện các thao tác cơ bản.

Ví dụ: Hãy xác định thời gian thiện hiện thuật toán dưới đây

```Go
// Tính toán tìm phần tử lớn nhất trong mảng
Algorithm max(a):
    max ← a[0]
    for i = 1 to len(a)-1
        if a[i] > max
            max ← a[i]
    return max
```

Câu trả lời chính xác tuỳ thuộc vào nhiều yếu tố như là giá trị đầu vào, ngôn ngữ lập trình và môi trường thực thi, trình biên dịch, hệ điều hành và phần cứng, ...

Vì vậy chúng ta thường chỉ muốn ước lượng thời gian thực thi thuật toán tương đối chỉ dựa vào thuật toán và giá trị đầu vào.
Điều này có thể được thực hiện bằng cách chọn một thao tác cơ bản mà thuật toán lặp lại nhiều lần và tính toán độ phức tạp thời gian như là số thao tác cơ bản cần để thực hiện thuật toán với input array có độ dài n

Đối với thuật toán ở trên ta có thể chọn thao tác so sánh a[i] > max làm thao tác cơ bản.

1. Bởi vì phép toán so sánh được sử dụng xuyên suốt thuật toán nên là điều này sẽ cho phép phản ánh đúng đắn thời gian chạy của thuật toán

2. Hơn nữa thời gian thực hiện của phép so sánh là hằng số và không phụ thuộc vào kích thước của array.

#### 2.1.2.1. Độ phức tạp thời gian tệ nhất (worst-case)

Xem xét thuật toán dưới này:

```Go
// Kiểm tra xem array a có chứa x.
Algorithm contains(a, x):
    for i = 0 to len(a)-1
        if x == a[i]
            return true
    return false
```

Phép toán so sánh x == a[i] có thể được dùng làm phép toán cơ bản trong trường hợp này.
Tuy nhiên như chúng ta thấy trong trường hợp này số lượng phép toán so sánh được thực hiện không chỉ phụ thuộc vào số lượng phần tử array n mà còn phụ thộc vào giá trị của x và giá trị của các phần tử trong a.

1. Nếu không tìm thấy x thì số phép toán so sánh là n

2. Nếu x == a[0] thì chỉ cần một phép so sánh .

Vì lý do này mà thông thường ta sẽ chọn xác định trường hợp thời gian tệ nhất của thuật toán:

1. Cho T1(n), T2(n), ... là thời gian thực thi của tất cả các giá trị có thể có của input kích thước n

2. Độ phức tạp thời gian trong trường hợp tệ nhất sẽ là W(n) = max(T1(n), T2(n), ...)

Vì vậy độ phức thời gian trong trường hợp tệ nhất sẽ là n

#### 2.1.2.2 Độ phức tạp thời gian trung bình (average-case)

Độ phức tạp thời gian trung bình thì ít phổ biến hơn, có thể được định nghĩa là số bước trung bình cần thiết để thực hiện thuật toán.

1. Cho T1(n), T2(n), ... là thời gian thực thi của tất cả các giá tri có thể có của input kích thước n, và P1(n), P2(n), ...là xác xuất của những input này.

2. Độ phức tạp thời gian trung bình (average-case) được định nghĩa là P1(n)T1(n) + P2(n)T2(n) + ...

Độ phức tạp thời gian trung bình khó để tính toán và ý nghĩa của nó là chủ đề gây tranh cãi.

## 2.2. Big-O

Như ở trên ta có nói thời gian thực thi của một thuật toán phụ thuộc vào nhiều yếu tố và khó để xác định chính xác nó.
Vì vậy khi nói đến việc tính toán thời gian thực thi của thuật toán đó chính là thời gian ước lượng, việc này thì thực ra không gây ra vấn đề gì. Bởi vì trong khoa học máy tính thứ được quan tâm nhiều hơn đối với một thuật toán là thời gian thực thi của nó sẽ tăng nhanh như thế nào khi số lượng phần tử đầu vào thay đổi.

Từ đó khái niệm Big-O được ra đời.

Và sau đây là một định nghĩa toán học của Big-O

> Cho T(n) và f(n) là hai hàm số dương. Ta viết T(n) = O(f(n)) có nghĩa rằng c . f(n) là cận trên của T(n). Vì vậy tồn tại một hằng số c sao cho T(n) luôn luôn <= c . f(n) với một số n đủ lớn (n >= n0)

Hay nói một cách dễ hiểu hơn:

> T(n) = O(f(n)) có nghĩa là T(n) không tăng nhanh hơn f(n).

Lưu ý: Ngoài Big-O, còn có hai khái niệm khác Big Omega(Ω) và Big Thelta(Θ):

1. Big Omega f(n) = Ω(g(n)) có nghĩa là c . g(n) là cận dưới của f(n), có tồn tại một số n >= n0 sao cho f(n) luôn nhỏ hơn c . g(n)

2. Big Thelta f(n) = Θ(g(n)) có nghĩa là c1 . g(n) là cận dưới và c2 . g(n) là cận trên của f(n), có tồn tại một số n >= n0 sao cho c1.g(n) <= f(n) <= c2.g(n)

### 2.2.1. Giới thiệu về một số hàm số tăng cơ bản

#### 2.2.1.1 Hàm số hằng

Đây là trường hợp đơn giản và lý tưởng nhất cho mọi thuật toán T(n) = O(1).

Theo như định nghĩa có nghĩa là sẽ tồn tại một giá trị n đủ lớn n >= n0 sao cho T(n) luôn luôn nhỏ hơn một giá trị cố định không phụ thuộc vào n.

### 2.2.1.2. Hàm số tuyến tính

Đây là trường hợp thuật toán có độ phức tạp theo thời gian T(n) = O(n).

Áp dụng định nghĩa của Big-O, điều này có nghĩa là tồn tại một số n đủ lớn n0 sao cho thời gian thực thi của thuật toán luôn nhỏ hơn n

### 2.2.1.3 Hàm số mũ

Đây là trường hợp thuật toán có độ phức tạp theo thời gian T(n) = O(n2).

Một lần nữa áp dụng định nghĩa, điều này có nghĩa là tồn tại một số n đủ lớn sao cho thời gian thực thi của thuật toán luôn nhỏ hơn n2.

### 2.2.1.4 Ví dụ về thay đổi thời gian thực thi theo đầu vào

![chart](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcQ6MsN%2FbtqubeWgXC4%2FXqPDbr2YJLA03Gx5g9VIJk%2Fimg.jpg)


# Nguồn tham khảo

1. https://yourbasic.org/

2. The Algorithm Design Manual – Skiena, Steven S.
