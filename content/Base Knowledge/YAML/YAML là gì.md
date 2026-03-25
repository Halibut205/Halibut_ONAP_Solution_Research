---
title: YAML
date: 2026-02-05
---
**YAML** (Ain't Markup Language) là một ngôn ngữ **tuần tự hóa dữ liệu** được thiết kế để con người có thể viết và đọc hiểu một cách dễ dàng.

**Tuần tự hóa dữ liệu (Data Serialization)** là quá trình chuyển đổi một **đối tượng (object)** trong bộ nhớ máy tính thành một định dạng lưu trữ hoặc định dạng truyền tải sao cho nó có thể phục hồi lại sau này.

Bình thường ta thường thấy:

Số nguyên `int` -> lưu trữ số nguyên.
Số thực `float` -> lưu trữ số thực.

Trong Python hay trong nhiều ngôn ngữ lập trình khác, đây chính là một ==*kiểu dữ liệu*== đã được định nghĩa sẵn trong hệ thống.

Khi đó, ta có thể tạo ra các ==*biến*== với kiểu dữ liệu này. Nó sẽ được sử dụng để lưu trữ giá trị nào đó, ví dụ `int x = 2`, `int y = 3` hay `float z = 1.2`.

các biến x, y, z này được gọi là một `object`, đơn giản là một thứ mà ta có thể tương tác vào. bằng các phương thức. Ví dụ, `a=x+y` .

Cho đoạn code Python như sau:

```
class Student: 
	def __init__(self, name, id):
		self.name = name
		self.id = id
```

Trong trường hợp ta muốn **tự khai báo** một ==*kiểu dữ liệu*== của riêng mình, đây chính là cách ta khai báo nó trong Python và nhiều loại ngôn ngữ lập trình khác.


