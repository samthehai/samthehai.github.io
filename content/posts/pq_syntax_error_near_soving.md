---
title: "Giải quyết lỗi pq: syntax error at or near"
date: "2021-05-20"
description: "pq: syntax error at or near solving"
tags: [Programming, Go, Postgresql]
---

---

# 1. Giới thiệu

Khi đang làm việc chuyển từ MySQL vọc thử Postgres thường hay mắc phải cái lỗi này

- Câu SQL

```bash
	stmt, err := r.db.PrepareContext(ctx, `INSERT INTO users (name, firebase_id, provider) VALUES (?,?,?)`)
```

- Lỗi

```bash
pq: syntax error at or near ","
```

Thử đi thử lại bằng tay vẫn thấy đúng, ức chế đạp bàn đập máy ... hư máy khỏi làm luôn vẫn không ra.
Thật ra vấn đề chính là chỗ rất căn bản.

Postgres không sử dụng `?,?,?` mà là `$1,$2,$3`.

Câu SQL đúng phải là
```bash
	stmt, err := r.db.PrepareContext(ctx, `INSERT INTO users (name, firebase_id, provider) VALUES ($1,$2,$3)`)
```

:cry:

# Nguồn tham khảo

1. https://golang.org/src/sort/sort.go
