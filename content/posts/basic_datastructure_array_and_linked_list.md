---
title: "Cấu trúc dữ liệu cơ bản: Array và Linked List"
date: "2020-12-27"
---

# 1. Giới thiệu

## 1.1. Contiguous vs Linked Data Structures

Cấu trúc dữ liệu có thể được phân loại thành hai phần: contiguous (liên tục) hoặc linked (liên kết) tuỳ thuộc vào việc chúng đang dựa vào array (mảng) hay là pointer (con trỏ):

- `Contiguously-allocated structures` bao gồm nhiều block memory liên tiếp nhau, gồm có: arrays, matrices, heaps, và hash tables.
- `Linked data structures` bao gồm những mảnh memory khác nhau được kết nối với nhau bởi pointer, gồm có: lists, trees, và graph adjacency lists

## 1.2. Array

Array là một cấu trúc dữ liệu cơ bản trong nhóm `contiguously-allocated` có số lượng phần tử cố định và các phần tử có thể được truy xuất một cách hiệu quả bằng index hoặc địa chỉ tương ứng.

### 1.2.1 Ưu điểm

- `Truy xuất đến từng phần tử bằng index với độ phức tạp là hằng số` - Bởi vì index của từng phần tử được map trực tiếp với địa chỉ memory nên chúng ta có thể access trực tiếp đến phần tử đó nếu ta biết index cuả chúng.
- `Tiết kiệm bộ nhớ` - Bởi vì array chỉ chứa dữ liệu đơn thuần, không phải tốn bộ nhớ để lưu trữ thông tin về linked hay là nhứng thông tin về format. Và hơn nữa vì là kích thước cố định nên ta cũng không cần lưu thông tin đánh dấu vị trí kết thúc.
- `Memory locality` - Là một idiom trong thực hành khi duyệt qua các phần tử trong cấu trúc dữ liệu. Đặc tính chứa các khối dữ liệu liên tiếp nhau của array giúp chúng khai thác tối tốc độ nhanh của cache trong mô hình máy tính hiện đại. https://www.cs.cornell.edu/courses/cs3110/2012sp/lectures/lec25-locality/lec25.html

### 1.2.2 Nhược điểm

- Không thể thay đổi kích thước - khi chương trình đang thực thi ta không thể thay đổi kích thước của array điều này dẫn tới việc ta phải cấp phát trước một số lượng bộ nhớ lớn trước khi chạy chương trình gây ra lãng phí. Ta có thể khắc phục nhược điểm này bằng cách sử dụng dynamic array: bắt đầu với kích thước array bằng 1, sẽ double kích thước của nó khi hết bộ nhớ từ m -> 2m và copy nội dung của array cũ sang phần dưới của array mới và trả lại phần bộ nhớ cũ đã sử dụng cho hệ thống => tuy nhiên điều này sẽ dẫn tới việc ta không thể đảm bảo việc truy xuất mỗi phần tử là hằng số trong trường hợp xấu nhất (khi mà array đạt giới hạn cần phải double). Tuy nhiên ta có thể áp dụng phương pháp [Amortized_analysis](https://en.wikipedia.org/wiki/Amortized_analysis) và xem như insertion trong dynamic array là một hằng số O(1).

### 1.2.3 Performance

| Thao tác                       | Array | Dynamic Aray |
| ------------------------------ | :---: | :----------: |
| Truy xuất (Access)             | O(1)  |     O(1)     |
| Tìm kiếm (Search)              | O(n)  |     O(n)     |
| Chèn (Insertion)               |  N/A  |     O(n)     |
| Thêm vào cuối (Appending)      |  N/A  |     O(1)     |
| Xoá (Deletioon)                |  N/A  |     O(n)     |
| Lãng phí bộ nhớ (Wasted space) |   0   |     O(n)     |

## 1.3 Linked List

### 1.3.1 Khái niệm và cơ chế

List là một `linked structure` đơn giản nhất, gồm tập hợp nhiều node liên kết với nhau bởi các con trỏ.
Một node trong `linked list` đơn giản nhất sẽ có cấu trúc như dưới này:

```Go
type Node struct {
  value interface{}
  next *Node
}
```

Trong đó `value` là giá trị data mà node đó nắm giữ, `next` là con trỏ đến node kế tiếp trong linked list.

`Head` là node đầu tiên và `tail` là node cuối cùng trong list.

Dưới đây là các thể loại linked list

- `Singly linked list` là cấu trúc đơn giản nhất của linked list, mỗi node sẽ có value và con trỏ chỉ đến node kế tiếp.
  Các thao tác có thể thực hiện gồm: `insert`, `delete`, `traversal`

![Singly-linked-list](https://en.wikipedia.org/wiki/File:Singly-linked-list.svg)

- `Doubly linked list` tương tự như single linked list, những mỗi node có thêm con trở chỉ đến node trước đó.

![Doubly-linked-list](https://en.wikipedia.org/wiki/File:Doubly-linked-list.svg)

### 1.3.2 Ưu điểm

- Dễ dàng insert và remove item mà không cần phải cấp phát hoặc tổ chức lại cấu trúc bởi vì đặc điểm của linked list là các block memory không liên tục nhau, trong khi đó thao tác insert hoặc remove item ở array khá là tốn kém.

### 1.3.3 Nhược điểm

- Tốn bộ nhớ để lưu các thông tin khác ngoài dữ liệu
- Không thể random access các item, khi muốn access item ta phải duyệt qua toàn bộ linked list.
- Không tận dụng được `cache locality`, [CPU cache](https://en.wikipedia.org/wiki/CPU_cache).

### 1.3.4 Performance

| Thao tác                       | Singly Linked List | Double Linked List |
| ------------------------------ | :----------------: | :----------------: |
| Truy xuất (Access)             |        O(n)        |        O(1)        |
| Tìm kiếm (Search)              |        O(n)        |        O(n)        |
| Thêm vào đầu (Insertion)       |        O(1)        |        O(1)        |
| Thêm vào cuối (Appending)      |        O(1)        |        O(1)        |
| Xoá ở đầu (Deletion)           |        O(1)        |        O(1)        |
| Xoá ở cuối (Deletion)          |        O(1)        |        O(1)        |
| Xoá ở giữa (Deletion)          |        O(n)        |        O(n)        |
| Lãng phí bộ nhớ (Wasted space) |        0(n)        |       O(n^2)       |

# 2. Implement

## 2.1. Dynamic Array

Một Dynamic Array có thể được implement dựa vào static array, và ta có thể thay đổi kích thước của dynamic array (tăng hoặc giảm), tuỳ vào việc hiện thực ở từng ngôn ngữ mà grow factor và shrink factor có thể khác nhau.

Các method cơ bản của Dynamic Array: `Add`, `Remove`, `RemoveAt`, `InsertAt`, ...

Một cách implement của Dynamic Array có thể được tham khảo ở đây
https://github.com/samthehai/algorithms/blob/master/datastructures/dynamicarray/dynamic_array.go

## 2.2. List

Một cách implement của Double Linked List có thể được tham khảo tại đây
https://github.com/samthehai/algorithms/blob/master/datastructures/list/double_linkedlist.go

Sau đây sẽ là phần phân tích source code [List](https://golang.org/src/container/list/list.go) của Go source code

còn tiếp ...

# 3. Tài liệu tham khảo

[Wikipedia's Dynamic Array](https://en.wikipedia.org/wiki/Dynamic_array)

[Memory and Locality](https://www.cs.cornell.edu/courses/cs3110/2012sp/lectures/lec25-locality/lec25.html)

[William Fiset Youtube](https://www.youtube.com/channel/UCD8yeTczadqdARzQUp29PJw)

[The Algorithm Design Manual](https://www.algorist.com/)
