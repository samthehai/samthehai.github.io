---
title: "Thuật toán cơ bản: Quicksort trong practice hay là Introsort"
date: "2021-01-25"
---

# 1. Giới thiệu

Tuần này ta sẽ tìm hiểu về thuật toán Quicksort, một thuật toán sắp xếp nổi tiếng là nhanh.
Ta sẽ thử tìm hiểu xem Quicksort nhanh đến cỡ nào, và nó có phải là thuật toán sắp xếp nhanh nhất hiện tại hay không?

Tất nhiên trước hết ta sẽ đi qua tìm hiểu thuật toán bên dưới của Quicksort. Giả sử ta có một danh sách các phần tử, thuật toán Quicksort sẽ bao gồm các bước dưới đây:

- Chọn một phần tử p, còn được gọi là pivot, từ danh sách
- Chia danh sách sao cho:
  - Tất các các phần tử nhỏ hơn p sẽ nằm ở phần đầu của danh sách
  - Tất cả các phần tử lớn hơn p sẽ nằm ở phần cuối của danh sách
  - Còn lại các phần tử bằng p sẽ nằm ở giữa
- Lặp lại đệ quy các bước ở trên cho các danh sách ở phần đầu và phần cuối.

```Go
//  Quicksort sắp xếp các phần tử của danh sách theo thứ tự tăng dần.
func Quicksort(v []int) {
    if len(v) < 2 {
        return
    }
    p := Pivot(v)
    low, high := Partition(v, p)
    Quicksort(v[:low])
    Quicksort(v[high:])
}
```

Vậy thời gian thực thi của Quicksort sẽ phụ thuộc vào các yếu tố nào?
Trước hết ta có thể thấy bởi vì phần Partition hầu như chỉ là các phép toán swap phần tử qua lại nên ta có thể đạt được linear time. Còn lại thời gian xây dựng các cây đệ quy cho các phần tử trong mỗi lần gọi hàm ở các danh sách con ước chừng là O(n. h), h ở đây chính là chiều cao của cây đệ quy. Và chiều cao này lại phụ thuộc rất lớn vào việc chọn Pivot ở đầu thuật toán. Nếu chúng ta may mắn thì có thể chọn được pivot ở đúng vị trí chia đôi danh sách, trường hợp này là lý tưởng nhất, khi đó thời gian thực thi sẽ là O(n log n).
Trường hợp xấu nhất đó là khi danh sách đã được sắp xếp đúng thứ tự và pivot nằm ở đầu danh sách, khi đó thời gian thực thi sẽ bằng O(n2) tương đương với brute force. Vì vậy điều quan trọng đối với thuật toán Quicksort chính là ở việc chọn pivot.

Sau đây ta sẽ đi vào tìm hiểu Go đang implement thuật toán Quicksort như thế nào.

# 2. Implement Quicksort trong thư viện built-in của Go

Không phải chờ đợi thêm nữa ta đọc ngay vào hàm chính của package sort: hàm Sort

## 2.1. Hàm Sort

```Go
// Sort sorts data.
// It makes one call to data.Len to determine n, and O(n*log(n)) calls to
// data.Less and data.Swap. The sort is not guaranteed to be stable.
func Sort(data Interface) {
	n := data.Len()
	quickSort(data, 0, n, maxDepth(n))
}
```

Ta có thể thấy ở trên hàm Sort gọi vào hàm con quickSort implement thuật toán Quicksort (có một số cải tiến), và không thể bỏ qua câu mô tả cuối cùng:

> The sort is not guaranteed to be stable

việc sắp xếp thì không được đảm bảo sẽ ổn định. ??!!?? Vậy thì tại sao lại không đảm bảo ổn định?

Trước hết ta phải hiểu khái niệm `stable sort` là gì.

## 2.1.1. Stable Sort là gì?

Trên Wiki có định nghĩa như sau:

> Stable sorting algorithms maintain the relative order of records with equal keys (i.e. values). That is, a sorting algorithm is stable if whenever there are two records R and S with the same key and with R appearing before S in the original list, R will appear before S in the sorted list. See here for a more complete description.

Ví dụ: Sắp xếp chuỗi này: C(1) C(2) A B

- Kết quả của thuật toán `stable sort`: A B C(1) C(2)
- Kết quả của thuật toán không `stable sort`: A B C(1) C(2) hoặc A B C(2) C(1)

* Chú thích: để dễ hiểu ta đánh dấu hai ký tự C giống nhau bằng cách thêm (1), (2) vào ngay sau.

stable sort là ám chỉ đến các thuật toán sắp xếp vẫn giữ được thứ tự như trước khi sắp xếp đối với các item mà cùng key (cùng giá trị) ta dễ dàng hiểu nguyên nhân ở trên khi biết rằng Quicksort không phải thuật toán stable sort.

Danh sách các thuật toán stable sort
https://en.wikipedia.org/wiki/Category:Stable_sorts

## 2.2. Interface interface

Ta có thể thấy hàm Sort nhận vào parameter data có kiểu dữ liệu là `Interface`, đây chính là các interface mà user phải implement để cho hàm Sort biết các để sắp xếp, swap, và đếm số lượng các phần tử trong array

```Go
// A type, typically a collection, that satisfies sort.Interface can be
// sorted by the routines in this package. The methods require that the
// elements of the collection be enumerated by an integer index.
type Interface interface {
	// Len is the number of elements in the collection.
	Len() int
	// Less reports whether the element with
	// index i should sort before the element with index j.
	Less(i, j int) bool
	// Swap swaps the elements with indexes i and j.
	Swap(i, j int)
}
```

Ngoài `Interface` interface, trước khi qua tìm hiểu về phần hiện thực của thuật toán Quicksort, ta để ý còn có hàm `maxDepth`

## 2.3. Tính maxDepth

```Go
// maxDepth returns a threshold at which quicksort should switch
// to heapsort. It returns 2*ceil(lg(n+1)).
func maxDepth(n int) int {
	var depth int
	for i := n; i > 0; i >>= 1 {
		depth++
	}
	return depth * 2
}
```

MaxDepth sẽ có giá trị là 2\*ceil(lg(n+1))

Đây chính là một phần cải tiến để cải thiện performance của thuật toán so với thuật toán gốc. Khi số lượng phần tử cần sắp xếp đủ nhỏ sẽ không sử dụng quicksort nữa mà chuyển sang sử dụng `heapsort`, `insertion sort` là những thuật toán thích hợp hơn quicksort khi số lượng phần tử nhỏ:

- `heapsort`: có thời gian thực thi trong trường hợp worse case là O(n\*logn) tốt hơn quicksort
- `insertion sort`: hiệu quả nhất trong những thuật toán có độ phức tạp n^2

Note: Phần cải tiến switch từ quicksort sang merge sort trên còn có tên gọi khác là [`introsort` hay `introspective sort`](https://en.wikipedia.org/wiki/Introsort)

## 2.4. Hàm quickSort

```Go
func quickSort(data Interface, a, b, maxDepth int) {
	for b-a > 12 { // Use ShellSort for slices <= 12 elements
		if maxDepth == 0 {
			heapSort(data, a, b)
			return
		}
		maxDepth--
		mlo, mhi := doPivot(data, a, b)
		// Avoiding recursion on the larger subproblem guarantees
		// a stack depth of at most lg(b-a).
		if mlo-a < b-mhi {
			quickSort(data, a, mlo, maxDepth)
			a = mhi // i.e., quickSort(data, mhi, b)
		} else {
			quickSort(data, mhi, b, maxDepth)
			b = mlo // i.e., quickSort(data, a, mlo)
		}
	}
	if b-a > 1 {
		// Do ShellSort pass with gap 6
		// It could be written in this simplified form cause b-a <= 12
		for i := a + 6; i < b; i++ {
			if data.Less(i, i-6) {
				data.Swap(i, i-6)
			}
		}
		insertionSort(data, a, b)
	}
}

```

### 2.4.1 Dùng Shellsort khi kích thước slices từ nhỏ hơn 12 phần tử

```Go
  for b-a > 12 { // Use ShellSort for slices <= 12 elements
    // simplified
    // ....
  }
	if b-a > 1 {
		// Do ShellSort pass with gap 6
		// It could be written in this simplified form cause b-a <= 12
		for i := a + 6; i < b; i++ {
			if data.Less(i, i-6) {
				data.Swap(i, i-6)
			}
		}
		insertionSort(data, a, b)
	}
```

Thực hiện Shellsort một lần với gap bằng 6, sau đó thực hiện `insertion sort`. Ta sẽ thắc mắc tại sao lại có con số 12 và 6 ở đây? Shellsort có hiệu quả với số phần tử nhỏ, không tốn bộ nhớ và tốc độ thật sự cải tiến hơn so với insertion sort, có worstcase là O(n*(logn)^2) khi gap được chọn có cấu trúc 2^j*3^k

### 2.4.2 Dùng Introsort (Quicksort) khi kích thước slices lớn hơn 12 phần tử

Quicksort có nhược điểm là worsecase là O(n^2) và chiếm bộ nhớ O(n) vì vậy khi vượt quá một mức độ sâu đệ quy thì sẽ hiệu quả hơn nếu sử dụng `Heapsort` bởi vì sẽ luôn đảm bảo được worsecase là O(n\*logn) và chỉ chiếm stack O(1).

Vậy tại sao ta không sử dụng `Heapsort` cho tất cả các trường họp? Bởi vì Quicksort chỉ swap khi cần thiết còn và worsecase O(n^2) có thể tránh được nếu ta chọn pivot thích hợp vòn việc tốn thời gian để swap phần tử trong `Heapsort` là không thể tránh được.

```Go
		if maxDepth == 0 {
			heapSort(data, a, b)
			return
		}
		maxDepth--
		mlo, mhi := doPivot(data, a, b)
		// Avoiding recursion on the larger subproblem guarantees
		// a stack depth of at most lg(b-a).
		if mlo-a < b-mhi {
			quickSort(data, a, mlo, maxDepth)
			a = mhi // i.e., quickSort(data, mhi, b)
		} else {
			quickSort(data, mhi, b, maxDepth)
			b = mlo // i.e., quickSort(data, a, mlo)
		}
```

Ta có một số nhận xét nhỏ sau:

- Nếu cây đệ quy vượt quá maxDepth thì ta sẽ sử dụng heapSort để đảm bảo được worsecase là O(n\*logn)
- Quicksort hoạt động chậm và có thể gây tràn stack khi cây đệ quy quá dài vì vậy để khắc phục bớt hậu quả ta sẽ so sánh điều kiện mlo-a < b-mhi để đảm bảo chỉ thực hiện đệ quy với phân đoạn ngắn và lặp lại toàn bộ quá trình partition với phần dài hơn.

Cuối cùng ta sẽ chuyển qua hàm `doPivot` phụ trách việc chọn pivot và partition.

2.4.3 Chọn Pivot và thực hiện việc partition

# Nguồn tham khảo

1. https://golang.org/src/sort/sort.go

2. https://yourbasic.org/golang/quicksort-optimizations/

3. The Algorithm Design Manual – Skiena, Steven S.

4. Giải Thuật Và Lập Trình - Lê Minh Hoàng

5. https://www.cpp.edu/~ftang/courses/CS241/notes/sorting.htm

6. https://www.ics.uci.edu/~goodrich/teach/cs260P/notes/Shellsort.pdf

7. https://www.geeksforgeeks.org/introsort-or-introspective-sort/
