---
title: "Thuật toán cơ bản: phân tích độ phức tạp giải thuật"
date: "2020-10-24"
---

# 1. Giới thiệu

Như ta đã biết ưu điểm của linked list so với array là ta có thể thêm và xoá node một cách linh hoạt và ít tốn kém hơn.
Tuy nhiên độ phức tạp khi tìm kiếm và insert dữ liệu vào linked list trong trường hợp worst case là O(n). Vậy có cách nào cải thiện được vấn đề này ở linked list không?
Câu trả lời là ta có thể đạt được kết quả tốt hơn ở một linked list đã sắp xếp nêú sử dụng skiplist.

## 1.1 Skiplist là gì?

Là một cấu trúc dữ liệu xác xuất (probabilistic data structure) được xây dựng dựa trên ordered linked list (linked list đã được sắp xếp), sử dụng xác xuất để xây dựng những lớp con dựa trên linked list ban đầu. Mỗi layer thêm vào sẽ chứa số lượng node ít hơn, và không có node nào mới.

Có thể tưởng tượng nó giống như là hệ thống tàu điện gồm có tàu bình thường và tàu nhanh.

![skip list](https://upload.wikimedia.org/wikipedia/commons/thumb/2/2c/Skip_list_add_element-en.gif/800px-Skip_list_add_element-en.gif)

> Ảnh trích từ [wikipedia](https://en.wikipedia.org/wiki/Skip_list)

