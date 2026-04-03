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

## Serialization và Desrialization


![YAML diagram](/images/yaml/image.png)

Để hiểu rõ **Serialization** (tuần tự hóa) diễn ra như thế nào, chúng ta cần nhìn vào sự khác biệt giữa cách dữ liệu tồn tại trong bộ nhớ (RAM) và cách nó tồn tại trên ổ cứng hoặc đường truyền mạng.

Trong bộ nhớ máy tính, một `object` không phải là một khối dữ liệu liền mạch. Nó thường là một ==đồ thị phức tạp nơi các pointers tham chiếu đến các vùng nhớ khác nhau trên RAM==. Trong khi đó, mạng máy tính hay ổ cứng chỉ có thể đọc và ghi dữ liệu theo một **chiều tuyến tính, một chuỗi các byte liên tục**.

Quá trình Serialization chính là việc biến đồ thị tham chiếu không gian 3 chiều phức tạp kia thành một đường thẳng 1 chiều. 

Quá trình bắt đầu khi bạn chỉ định một `object` cụ thể cần tuần tự hóa. Trình tuần tự hóa sẽ lấy `object` này làm điểm xuất phát. Trình tuần tự hóa sẽ quét qua toàn bộ cấu trúc của `object` gốc. Nếu `object` này chứa các thuộc tính là những `object` con khác (ví dụ: `object` Customer chứa `object` Address), nó sẽ tiếp tục đi sâu vào các `object` con đó cho tới cuối cùng.

Khi đi qua từng nút trong đồ thị, trình tuần tự hóa sẽ đọc các giá trị nguyên thủy (như số nguyên, chuỗi văn bản, boolean,...). Sau đó, nó ánh xạ các kiểu dữ liệu trong bộ nhớ sang ==*định dạng ngôn ngữ mô hình*==

**Desrialization** là quá trình lấy dữ liệu đã được định dạng ở mức tĩnh và chuyển đổi nó ngược lại thành một  sống trong bộ nhớ RAM để chương trình có thể thao tác và sử dụng.

## Ngôn ngữ mô hình (Model Language)

Định dạng đích chính là kết quả đầu ra của quá trình tuần tự hóa. Sau khi trình tuần tự hóa duyệt qua toàn bộ cấu trúc đồ thị của `object` và lấy được các giá trị cần thiết, nó sẽ cần một bộ quy tắc để "đóng gói" các dữ liệu này thành một chuỗi tuyến tính. Bộ quy tắc đó chính là định dạng đích.

| Tên định dạng | Ứng dụng phổ biến                                                                                        |
| ------------- | -------------------------------------------------------------------------------------------------------- |
| JSON          | Giao tiếp Web API.                                                                                       |
| YAML          | Cấu hình hạ tầng & triển khai, Các kịch bản tự động hóa, File thiết lập.                                 |
| XML           | Các hệ thống doanh nghiệp truyền thống, Giao diện ứng dụng điện thoại.                                   |
| Protobuf      | Giao tiếp nội bộ giữa các Microservices, Xử lý luồng dữ liệu lớn, Ứng dụng yêu cầu băng thông mạng thấp. |
| BSON          | Định dạng lưu trữ và truy vấn cốt lõi của cơ sở dữ liệu **MongoDB**.                                     |

Mục tiêu cốt lõi của định dạng đích là đảm bảo tính **toàn vẹn** và **khả năng Deserialization**. Nghĩa là, khi chuỗi dữ liệu này được gửi sang một máy tính khác (hoặc lưu xuống ổ cứng và đọc lại sau), hệ thống đó phải biết cách giải mã và dựng lại chính xác object ban đầu trong bộ nhớ (RAM).

Các định dạng đích thường được chia thành hai nhóm chính, tùy thuộc vào mục đích sử dụng:

**Định dạng nhị phân**

Nhóm này ưu tiên hiệu năng và tốc độ cho máy tính. Dữ liệu được mã hóa thành các chuỗi byte (0 và 1) vô cùng nhỏ gọn.

- **Đặc điểm:** Tốc độ đọc/ghi cực kỳ nhanh, băng thông truyền tải qua mạng rất nhỏ. Tuy nhiên, con người không thể đọc trực tiếp bằng mắt thường mà phải dùng các công cụ chuyên dụng để giải mã.
    
- **Các định dạng tiêu biểu:** ==Protocol Buffers== (Protobuf của Google), ==BSON== (dùng trong MongoDB), ==MessagePack==. Thường được dùng trong giao tiếp giữa các microservices hoặc trong các hệ thống đòi hỏi độ trễ cực thấp (online gaming, hệ thống tài chính).

**Định dạng văn bản**

Nhóm này ưu tiên sự thân thiện với con người. Dữ liệu được lưu dưới dạng các ký tự văn bản thông thường, giúp lập trình viên có thể dễ dàng mở ra đọc, chỉnh sửa hoặc tìm lỗi.

- **JSON (JavaScript Object Notation):** Rất phổ biến trong phát triển Web và các RESTful API. Nó sử dụng dấu ngoặc nhọn `{}` và ngoặc vuông `[]` để cấu trúc dữ liệu.
    
- **XML (eXtensible Markup Language):** Sử dụng các thẻ (tags) đóng mở tương tự như HTML (ví dụ `<name>Nguyễn Văn A</name>`). Cấu trúc chặt chẽ nhưng khá cồng kềnh.
    
- **YAML (YAML Ain't Markup Language):** Đây chính là ngôn ngữ mà chúng ta đề cập ở đầu bài! Cực kỳ thân thiện với con người, không dùng dấu ngoặc hay thẻ rườm rà, mà dùng **khoảng trắng (thụt lề)** để cấu trúc dữ liệu. Rất được ưa chuộng trong các file cấu hình (configuration files) như Docker, Kubernetes, CI/CD pipelines.

**Ví dụ:** Nếu ta tuần tự hóa object `sv1` từ đoạn code Python của bạn sang định dạng **YAML**, kết quả đích trông sẽ đơn giản và trực quan như thế này

```yaml
Student: 
	name: Nguyễn Văn A 
	id: SV12345
```

# Ngôn ngữ mô hình YAML

## YAML là gì?

YAML là một ngôn ngữ tuần tự hóa dữ liệu.

Một file tài liệu YAML sẽ biểu diễn cấu trúc dữ liệu nguyên bản của máy tính dưới dạng văn bản mà con người có thể đọc được.

Dữ liệu trong file YAML được biểu diễn dưới dạng các NODE, có 3 dữ liệu cơ bản cấu thành các NODE này, đó là:
- **Scalar (Giá trị vô hướng):** Biểu diễn các kiểu dữ liệu ở cấp nguyên tử (Atomic), tức là các dữ liệu ở cấp nhỏ nhất, không thể chứa bên trong các kiểu dữ liệu khác (ví dụ như Strings, Numbers, Booleans và NULL).
- **Sequence (Trình tự/ Danh sách):** Một danh sách các NODE
- **Mapping (Ánh xạ):** 
	- Một tập hợp ánh xạ từ các nút tới các nút. Một cặp Key-Value. Còn được gọi là Hashes, Hash Maps, Dictionaries, hoặc Objects.
	- Không giống như nhiều ngôn ngữ lập trình, một khóa có thể không chỉ là một chuỗi đơn thuần. Nó có thể là một sequence hoặc chính là một mapping.

Thêm nữa, YAML cũng cho phép tuần tự hóa các loại dữ liệu khác và các Class dưới dạng:
- **Alias và Anchor:** Để tuần tự hóa các tham chiếu / con trỏ

## Cách viết một File YAML

Thử viết một hóa đơn thanh toán bao gồm ==số hóa đơn==, ==tên khách hàng==, ==địa chỉ==, ==các mặt hàng đã mua==:

### Mapping 
- Kiểu dữ liệu cấp cao nhất và phổ biến nhất là mappings, tức là các cặp giá trị Key/Value.
- Key và Value sẽ được phân tách bằng dấu `:`.
- Mỗi cặp Key/Value sẽ nằm trên một dòng riêng.

```YAML
invoice number: 0326583225
name: Pham Minh Duc
address: Ha Noi
```

Ta cũng có thể viết như sau:
```YAML
---
invoice number: 0326583225
name: Pham Minh Duc
address: Ha Noi
```
Dấu `---` biểu thị rõ ràng sự bắt đầu của một tài liệu YAML -> Nó đánh dấu nội dung theo sau là YAML, nhưng nó là tùy chọn.

Ta thường sử dụng nó khi ta có nhiều Tài liệu YAML được viết trong cùng một file.

### Nested Mappings

Bây giờ thử thay thế chuỗi địa chỉ (address) bằng một mapping khác.
-> Trong trường hợp đó, sau dấu hai chấm là một dấu ngắt dòng. Các giá trị mapping không phải là `scalar` phải luôn bắt đầu trên một dòng mới.

==Mức thụt lề thông thường là hai khoảng trắng. **Không được sử dụng Tab để thụt lề.**==

