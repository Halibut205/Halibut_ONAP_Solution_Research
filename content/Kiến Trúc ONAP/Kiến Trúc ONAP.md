---
title: Architecture
date: 2026-03-18
draft: "false"
---
ONAP là một bộ sưu tập các giải pháp tự động hóa mạng. Trong đó, những giải pháp này sẽ bao gồm công việcđiều phối, quản lý và tự động hóa.

Thử thách mà ONAP đặt ra đó là giúp đỡ các nhà vận hành mạng:

- Quản lý quy mô và chi phí của những thay đổi thủ công khi cần triển khai các dịch vụ mới.
- Tận dụng SDN và NFV để đẩy nhanh tốc độ cung cấp dịch vụ.
- Có một hệ thống giám sát để đảm bảo các cam kết chất lượng dịch vụ (SLA).

## Kiến trúc tổng quan

Kiến trúc của ONAP dựa trên một mô hình lý thuyết tiêu chuẩn, đó là mô hình NFV của ETSI (Viện Tiêu Chuẩn Viễn Thông Châu Âu). Trong đó, lớp MANO trong mô hình có mối quan hệ cực kỳ mật thiết.

nói một cách dễ hiểu ETSI NFV là bản vẽ kiến trúc tiêu chuẩn lý thuyết, còn ONAP là một phần mềm mã nguồn mở thực tế đã hiện thực hóa bản vẽ đó (và thậm chí còn làm được nhiều hơn thế).

![image.png](../../image.png)

Kiến trúc của ONAP có thể được chia làm 3 phần:

- Phần thiết kế (Design Time)
- Phần triển khai (Run Time)
- Phần quản lý (OOM)

![image 1.png](../../image%201.png)

## Quá trình tinh gọn

ONAP ban đầu được xây dựng theo kiến trúc Monolithic, tức là toàn bộ phần mềm được phát triển, đóng gói và triển khai theo một khối thống nhất; giao diện, logic nghiệp vụ và cơ sở dữ liệu kết hợp chặt chẽ với nhau.

Giờ đây, ONAP được triển khai theo cấu trúc Micro Services bao gồm các chức năng tự động hóa và bảo mật trong hệ sinh thái Linux Network Foundations (LNF)

Quá trình tinh gọn này hướng tới mục đích:

- Tạo ra các giao diện trừu tượng dựa trên tiêu chuẩn nhất định, sử dụng declarative APIs, tức là thay vì phải ra lệnh từng bước một, ta chỉ cần sử dụng giao diện đơn giản để định nghĩa trạng thái mong muốn (Intent-based). Hệ thống sẽ tự động tính toán để đạt được trạng thái đó.
- Các thành phần này có thể hoạt động không phải phụ thuộc cứng nhắc vào nhau mà có thể tự vận hành độc lập và xử lý nhiệm vụ riêng biệt.
- Phù hợp hơn với quy trình CI/CD

## Kiến trúc chi tiết

![image 2.png](../../image%202.png)

### 1. SDC (Service Design and Creation)

ONAP cung cấp hẳn một hệ thống đồ sộ là **SDC (Service Design and Creation)** để các kỹ sư thiết kế, đóng gói và thử nghiệm các dịch vụ mạng trước khi thực sự triển khai vào môi trường Production

Thành phần của SDC bao gồm

- Service/xNF Design
- xNF Onboarding
- Workflow Designer
- Catalog

#### Service/xNF Design

Service/xNF Design là quá trình mô hình hóa, định nghĩa kiến trúc và tạo ra các bản thiết kế cho các dịch vụ mạng trước khi được đưa vào triển khai thực tế.

Để hiểu được, trước tiên cần làm rõ thuật ngữ xNF (x Network Function):

- PNF (Physical Network Function): Chức năng mạng vật lý truyền thống, nằm trên các thiết bị chuyên dụng.
- VNF (Virtual Network Function): Chức năng ảo hóa, chạy trên các máy ảo, với thiết bị là các máy tính có kiến trúc tập lệnh 64 bit.
- CNF (Containerized/Cloud-na+tive Network Function) chạy trên các Container

![image 3.png](../../image%203.png)

Tiếp thoe là thuật ngữ Service Design:

Các xNF đứng một mình không tạo ra giá trị cho người dùng, ta cần phải tập hợp nhiều xNF lại với nhau, cấu hình mạng (network, subnet, IP, …) và thiết lập policies để tạo ra một hệ thống hoàn chỉnh.

⇒ Service/xNF Design nhấn mạnh vào việc tạo ra một bẩn thiết kế hệ thống hoàn chỉnh với các chức năng mạng được liên kết với nhau qua việc cấu hình mạng

#### xNF Onboarding

Đây là quá trình “nhập kho” và “kiểm đinh” khi một chức năng mạng mới từ vendor được đưa vào hệ thống. 

Trước khi được đưa vào, người quản trị cần phải kiểm tra chức năng mạng trên có ảnh hưởng tới hệ thống thực tế không.

Đầu vào sẽ là một gói **VPS (Vendor Software Product)**
- Gói này sẽ chứa toàn bộ những thứ liên quan tới chức năng mạng đó
	- Với CNF, gói này sẽ thường chứa Helm Charts, một trình quản lý gói, các tệp cấu hình YAML K8s, các đường dẫn tới các Docker Images
	- Với VNF, Gói này chứa các **HEAT templates** (để tạo máy ảo trên OpenStack) và các file image hệ điều hành (như qcow2).
- Thông tin đi kèm bao gồm yêu cầu tài nguyên (Cần bao nhiêu CPU, RAM, các file script cấu hình, và License)

⇒ Khi Upload gói này nên SDC, Quá trình Onboard sẽ chạy qua bốn bước kiểm định

1. Validation
2. Translation 
3. Enrichment
4. Certification

Sau khi Onboard thành công, xNF đó sẽ xuất hiện trong **SDC Catalog** dưới dạng một tài nguyên (Resource).

Từ lúc này, người làm Service Design có thể dễ dàng mở giao diện kéo-thả, nối tài nguyên đó vào các thành phần khác của bản thiết kế.

#### Workflow Designer

Chức năng của thành phần này là cho phép người dùng thiết kế các luồng công việc (workflow), lưu lại và đính kèm chúng vào một dịch vụ SDC dưới dạng một **"artifact"** (tác phẩm/thành phẩm). 

Nó cũng quản lý định nghĩa của các hoạt động nhỏ lẻ để tái sử dụng trong các workflow khác.

Vậy **workflow** là gì? Nó là một chuỗi các bước được sắp xếp theo một trình tự logic nào đó để hệ thống tự động thực hiện một hành động từ đầu tới cuối. 

Hình dung mình cần triển khai một Firewall ảo. Bạn không thể vứt cục file cấu hình đó vào hệ thống rồi hi vọng nó tự chạy. Bạn cần một workflow như sau:
1. Nhận yêu cầu triển khai (Khách hàng yêu cầu tên dịch vụ, dải IP, ...).
2. Cấp phát tài nguyên, gọi API xuống tầng ảo hóa để tạo máy ảo hoặc container cho chức năng firewall này.
3. Chạy quy trình Rollback nếu tạo máy ảo không thành công.
4. Gán địa chỉ IP khách hàng yêu cầu, kết nối firewall vào mạng.
5. Đẩy các rule mặc định vào firewall.
6. Trả về kết quả triển khai thành công.

Toàn bộ các bước trên được đóng gói lại thành một file workflow, syntax dựa trên XML, thường có đuôi file là `.bpmn` (Business Process Model and Notation), hoặc file `.yaml`(Yet Another Model Language).

Có thể thấy hầu hết các "hoạt động" trong một workflow có phần lặp đi lặp lại (cấp phát IP, tạo VM,...). **Workflow Design** sẽ cung cấp cho người dùng một một giao diện Web, nơi các "hoạt động" này được biểu diễn dưới dạng các khối kéo thả. Ta có thể nối các khối ấy lại với nhau bằng các mũi tên để tạo thành dòng chảy logic.

#### Catalog

Các chức năng mạng sau khi vượt qua quá trình Onboard - Validation sẽ được đưa vào trưng bày trong Catalog. nó chứa 3 thành phần chính:

- **Tài nguyên**: chính là các chức năng mạng (VNF, CNF, PNF) đã được onboard thành công từ các vendor. Ví dụ: Một cục Firewall ảo của Fortinet, một con Router ảo của Cisco, một hàm 5G Core chạy trên K8s.
- **Dịch vụ**: Các dịch vụ mạng hoàn chỉnh do chính tay mình ghép nối từ các tài nguyên ở trên. Ví dụ: Một dịch vụ mạng 5G cho doanh nghiệp với 1 Firewall + 1 Load Balancer + 1 Web Server. Thiết kế này sẽ được lưu lại và Catalog.
- **Artifacts**: Như đã giới thiệu trong phần Workflow Design, đây là các khối lưu trữ workflow.
- **Chính sách**: các chính sách (Policy) về bảo mật hay tự động mở rộng hệ thống.

### 2. UUI (Usecase User Interface)

![image 3.png](../../image%204.png)