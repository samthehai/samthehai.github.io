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

Một ví dụ về ứng dụng của Quy hoạch động là trong việc implement thuật toán propagation đình đám của [Neuro Network](http://blog.ezyang.com/2011/05/neural-networks/)

# Nguồn tham khảo

1. https://golang.org/src/sort/sort.go
