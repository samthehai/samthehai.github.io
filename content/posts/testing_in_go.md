---
title: "Giới thiệu về Testing trong Go"
date: "2020-03-18"
---

# Giới thiệu

Testing là một quá trình quan trọng trong mọi ngành sản xuất từ việc trồng trọt:lựa chọn giống, phương pháp sản xuất đến công việc sản xuất các thiết bị, hệ thống cực lớn trong các ngành công ngiệp. Chẳng thế mà người đảm nhận vị trí Quality assurancce (QA) luôn có một quyền lực rất lớn trong các công ty sản xuất công ngiệp. Và ngành công nghiệp sản xuất phầm mềm hiển nhiên là cũng không ngoại lệ. Tuy nhiên việc testing theo cách nhìn nhận chủ quan của mình vẫn chưa được xem trọng đúng mực. Trước khi Go ra đời (có lẽ là) chưa có một ngôn ngữ lập trình nào tích hợp các tool testing vào!

Wait, What??? Vâng, đúng vậy Go có bao gồm một chương trình đặc biệt `go test` và thư viện `testing` giúp cho việc viết unittest dễ dàng hơn.
Chúng ta hãy cùng tìm hiểu về cách viết unittest trong Go ngay sau đây qua một vi dụ được trich dẫn từ
<https://gobyexample.com/testing>

```Go
package main

import (
  "fmt"
  "testing"
)

func IntMin(a, b int) int {
  if a < b {
    return a
  } else {
    return b
  }
}

func TestIntMinBasic(t *testing.T) {
  ans := IntMin(2, -2)
  if ans != -2 {
    t.Errorf("IntMin(2, -2) = %d; want -2", ans)
  }
}

func TestIntMinTableDriven(t *testing.T) {
  var tests = []struct {
    a,b int
    want int
  }{
    {0, 1, 0},
    {1, 0, 0},
    {2, -2, -2},
    {0, -1, -1},
    {-1, 0, -1},
  }

  for _, tt := range tests {
    testname := fmt.Sprintf("%d, %d", tt.a, tt.b)
    t.Run(testname, func(t *testing.T) {
      ans := IntMin(tt.a, tt.b)
      if ans != tt.want {
        t.Errorf("got %d, want %d", ans, tt.want)
      }
    })
  }
}
```

Nhấn nút play để chạy thử (có thể chạy ở local bằng lệnh go test) trên <https://play.golang.org/p/jCRgtgRwie3> sẽ ra được kết quẩ như dưới này

```Go
=== RUN   TestIntMinBasic
--- PASS: TestIntMinBasic (0.00s)
=== RUN   TestIntMinTableDriven
=== RUN   TestIntMinTableDriven/0,1
=== RUN   TestIntMinTableDriven/1,0
=== RUN   TestIntMinTableDriven/2,-2
=== RUN   TestIntMinTableDriven/0,-1
=== RUN   TestIntMinTableDriven/-1,0
--- PASS: TestIntMinTableDriven (0.00s)
    --- PASS: TestIntMinTableDriven/0,1 (0.00s)
    --- PASS: TestIntMinTableDriven/1,0 (0.00s)
    --- PASS: TestIntMinTableDriven/2,-2 (0.00s)
    --- PASS: TestIntMinTableDriven/0,-1 (0.00s)
    --- PASS: TestIntMinTableDriven/-1,0 (0.00s)
PASS

All tests passed.
```

Sau đây chúng ta sẽ thử code (hoặc copy paste tuỳ ý) lại phần test trên và chạy ở local

* Tạo file source Go với tên kiểu như intutils.go chứa nội dung như ở dưới

```Go
package main

import (
  "fmt"
  "testing"
)

func IntMin(a, b int) int {
  if a < b {
    return a
  } else {
    return b
  }
}
```

* Sau đó tạo code test cho phần code ở trên và lưu ở file intutils_test.go

```Go
func TestIntMinBasic(t *testing.T) {
  ans := IntMin(2, -2)
  if ans != -2 {
    t.Errorf("IntMin(2, -2) = %d; want -2", ans)
  }
}

func TestIntMinTableDriven(t *testing.T) {
  var tests = []struct {
    a,b int
    want int
  }{
    {0, 1, 0},
    {1, 0, 0},
    {2, -2, -2},
    {0, -1, -1},
    {-1, 0, -1},
  }

  for _, tt := range tests {
    testname := fmt.Sprintf("%d, %d", tt.a, tt.b)
    t.Run(testname, func(t *testing.T) {
      ans := IntMin(tt.a, tt.b)
      if ans != tt.want {
        t.Errorf("got %d, want %d", ans, tt.want)
      }
    })
  }
}
```

Một số lưu ý:

* Một test sẽ được tạo bằng cách viết một function với tên bắt đầu bằng chữ Test.
* t.Error* sẽ báo lỗi khi test fail nhưng sẽ tiếp tục thực hiện việc test tiếp, trong khi đó t.Fail* sẽ báo test fail và dừng hay lập tức khi test bị fail.
* t.Log* có thể được sử dụng để cung cấp thông tin debug
* Việc test thường sẽ được lặp lại với các bộ input khác nhau nên idiom là sử dụng table-driven style: các input và expected tương ứng sẽ được liệt kê ra một bảng ( thường sủ dụng slice) và sử dụng một vòng lặp đơn để duyệt qua tất cả và thực hiện việc test.
* t.Run cho phép chạy từng test con, từng entry một và có thể chạy và liệt kê kết quả của từng test con bằng cách dùng lệnh `go test -v`


```Go
$ go test -v
== RUN   TestIntMinBasic
--- PASS: TestIntMinBasic (0.00s)
=== RUN   TestIntMinTableDriven
=== RUN   TestIntMinTableDriven/0,1
=== RUN   TestIntMinTableDriven/1,0
=== RUN   TestIntMinTableDriven/2,-2
=== RUN   TestIntMinTableDriven/0,-1
=== RUN   TestIntMinTableDriven/-1,0
--- PASS: TestIntMinTableDriven (0.00s)
    --- PASS: TestIntMinTableDriven/0,1 (0.00s)
    --- PASS: TestIntMinTableDriven/1,0 (0.00s)
    --- PASS: TestIntMinTableDriven/2,-2 (0.00s)
    --- PASS: TestIntMinTableDriven/0,-1 (0.00s)
    --- PASS: TestIntMinTableDriven/-1,0 (0.00s)
PASS
ok      _source/testing_in_go        0.064s
```

# Thư viện testing

<!-- TODO -->

# Công cụ go test

<!-- TODO -->

# Thư viện khác

## go-sqlmock: sql driver mock dành cho Golang

<https://github.com/DATA-DOG/go-sqlmock>
sqlmock là thư viện mock hiện thực sql/driver. Chỉ có 1 mục đích duy nhất là để mô phỏng hành vì của bất cứ sql driver nào trong test mà không cần một kết nối thật đến database. Đảm bảo duy trì đúng TDD workflow.

* Thư viện này đã được hoàn thiện và ổn định.
* Hỗ trọ đồng bộ và nhiều kết nối.
* Hỗ trợ mocking các feature liên quan đến Context ở go1.8 và đặt tên sql parameter.
* Không yêu cầu phải chỉnh sửa source code.
* Driver cho phép mocking bứt cứ hành vi của phương thức nào của sql driver.
* Expectation mặc định yêu cầu chặt chẽ về thứ tự matching.
* Không có third-party deoendency.

### Cài đặt

```bash
go get github.com/DATA-DOG/go-sqlmock
```

### Cách viết test sử dụng go-sqlmock

Sau đây chúng ta sẽ đi qua một ví dụ về cách sử dụng thư viện giả sử code cần test đang sử dụng [go-mysql-driver](https://github.com/go-sql-driver/mysql)

#### Code đối tượng để test

```Go
package main

import (
  "database/sql"
  _ "github.com/go-sql-driver/mysql"
)

func recordStats(db *sql.DB, userID, productID int64) (err error) {
  tx, err := db.Begin()
  if err != nil {
    return err
  }

  defer func() {
    switch err {
    case nil:
      err = tx.Commit()
    default:
      tx.Rollback()
    }
  }()

  if _, err = tx.Exec("UPDATE products SET views = views + 1"); err != nil {
    return err
  }

  if _, err = tx.Exec("INSERT INTO product_viewers (user_id, product_id) VALUES (?, ?)", userID, productID); err != nil {
    return err
  }

  return nil
}

func main() {
  // @NOTE: the real connection is not required for tests
  db, err := sql.Open("mysql", "root@/blog")
  if err != nil {
    panic(err)
  }
  defer db.Close()
  if err = recordStats(db, 1 /*some user id*/, 5 /*some product id*/); err != nil {
    panic(err)
  }
}
```

#### Code test sử dụng sqlmock

```Go
package main

import (
  "fmt"
  "testing"

  "github.com/DATA-DOG/go-sqlmock"
)

// a successful case
func TestShouldUpdateStats(t *testing.T) {
  // create mock db connection, mock data entity
  db, mock, err := sqlmock.New()
  if err != nil {
    t.Fatalf("an error '%s' was not expected when opening stub database connection", err)
  }
  defer db.Close()

  // creat mock data for test
  mock.ExpectBegin()
  mock.ExpectExec("UPDATE products").
    WillReturnResult(
      sqlmock.NewResult(1, 1)
    )
  mock.ExpectExec("INSERT INTO product_viewers").
    WithArgs(2, 3).
    WillReturnResult(
      sqlmock.NewResult(1, 1)
    )
  mock.ExpectCommit()

  // test table
  var tests = []struct {
    userID, productID int64
  }{
    {2, 3},
  }

  // execute test
  for _, tt := range tests {
    testname := fmt.Sprintf("%d, %d", tt.userID, tt.productID)
    t.Run(testname, func(t *testing.T) {
      err := recordStats(db, tt.userID, tt.productID)
      if err != nil {
        t.Errorf("error was not expected while updating stats: %s", err)
      }

      // we make sure that all expectations were met
      if err := mock.ExpectationsWereMet(); err != nil {
        t.Errorf("there were unfulfilled expectations: %s", err)
      }
    })
  }
}

// a failing test case
func TestShouldRollbackStatUpdatesOnFailure(t *testing.T) {
  // create mock db connection, mock data entity
  db, mock, err := sqlmock.New()
  if err != nil {
    t.Fatalf("an error '%s' was not expected when opening a stub database connection", err)
  }

  defer db.Close()

  // creat mock data for test
  mock.ExpectBegin()
  mock.ExpectExec("UPDATE products").
    WillReturnResult(
      sqlmock.NewResult(1, 1)
    )
  mock.ExpectExec("INSERT INTO product_viewers").
    WithArgs(2, 3).
    WillReturnError(fmt.Errorf("some error"))
  mock.ExpectRollback()

  // test table
  var tests = []struct {
    userID, productID int64
  }{
    {2, 3},
  }

  // execute test
  for _, tt := range tests {
    testname := fmt.Sprintf("%d, %d", tt.userID, tt.productID)
    t.Run(testname, func(t *testing.T) {
      err := recordStats(db, tt.userID, tt.productID)
      if err == nil {
        t.Errorf("was expecting an error, but there was none")
      }

      // we make sure that all expectations were met
      if err := mock.ExpectationsWereMet(); err != nil {
        t.Errorf("there were unfulfilled expectations: %s", err)
      }
    })
  }
}
```

Ở trên là ví dụ test cho code sử dụng go-mysql-driver, chúng ta có thể sử dụng go-sqlmock để test code sử dụng các thư viện khác, ORM như GORM,... đều OK.

# Nguồn tham khảo

[1] <https://golang.org/pkg/testing/>

[2] <https://github.com/DATA-DOG/go-sqlmock>

[3] <https://blog.alexellis.io/golang-writing-unit-tests/>
