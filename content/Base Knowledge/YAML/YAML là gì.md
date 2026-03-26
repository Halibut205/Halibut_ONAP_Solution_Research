---
title: YAML
date: 2026-02-05
---
**YAML** (Ain't Markup Language) là một ngôn ngữ **tuần tự hóa dữ liệu** được thiết kế để con người có thể viết và đọc hiểu một cách dễ dàng.

# Data Serialization

**Tuần tự hóa dữ liệu (Data Serialization)** là quá trình chuyển đổi một **đối tượng (object)** trong bộ nhớ máy tính thành một định dạng lưu trữ hoặc định dạng truyền tải sao cho nó có thể phục hồi lại sau này.

## Object

Ta thường thấy trong các ngôn ngữ lập trình:

`int` → lưu trữ số nguyên.
`float` → lưu trữ số thực.

Trong Python hay trong nhiều ngôn ngữ lập trình khác, đây chính là một ==kiểu dữ liệu== đã được định nghĩa sẵn trong hệ thống.

Khi đó, ta có thể tạo ra các ==biến== với kiểu dữ liệu này. Nó sẽ được sử dụng để lưu trữ giá trị nào đó, ví dụ `int x = 2`, `int y = 3` hay `float z = 1.2`.

các biến x, y, z này có thể được gọi là một `object`, đơn giản là một thứ mà ta có thể tương tác vào. bằng các phương thức. Ví dụ, `a=x+y` .

Cho đoạn code Python như sau:

```python
class Student:
    def __init__(self, name, id):
        self.name = name
        self.id = id
```

Trong trường hợp ta muốn **tự khai báo** một ==kiểu dữ liệu== của riêng mình, đây chính là cách ta khai báo nó trong Python và nhiều loại ngôn ngữ lập trình khác.

Sau đó, ta có thể tạo ra một `object` là `sv1` từ kiểu dữ liệu trên:

```python
sv1 = Student("Nguyễn Văn A", "SV12345")
```

## Serialization


![YAML diagram](/images/yaml/image.png)

Để hiểu rõ quá trình tuần tự hóa diễn ra như thế nào, chúng ta cần nhìn vào sự khác biệt giữa cách dữ liệu tồn tại trong bộ nhớ (RAM) và cách nó tồn tại trên ổ cứng hoặc đường truyền mạng.

Trong bộ nhớ máy tính, một `object` không phải là một khối dữ liệu liền mạch. Nó thường là một ==đồ thị phức tạp nơi các pointers tham chiếu đến các vùng nhớ khác==. Trong khi đó, mạng máy tính hay ổ cứng chỉ có thể đọc và ghi dữ liệu theo một **chiều tuyến tính, một chuỗi các byte liên tục**.

Quá trình tuần tự hóa chính là việc biến đồ thị tham chiếu không gian 3 chiều phức tạp kia thành một đường thẳng 1 chiều. 

Quá trình bắt đầu khi bạn chỉ định một `object` cụ thể cần tuần tự hóa. Trình tuần tự hóa sẽ lấy `object` này làm điểm xuất phát. Trình tuần tự hóa sẽ quét qua toàn bộ cấu trúc của `object` gốc. Nếu `object` này chứa các thuộc tính là những `object` con khác (ví dụ: `object` Customer chứa `object` Address), nó sẽ tiếp tục đi sâu vào các `object` con đó cho tới cuối cùng.

Khi đi qua từng nút trong đồ thị, trình tuần tự hóa sẽ đọc các giá trị nguyên thủy (như số nguyên, chuỗi văn bản, boolean,...). Sau đó, nó ánh xạ các kiểu dữ liệu trong bộ nhớ sang ==*định dạng đích*==.

## Định dạng đích





