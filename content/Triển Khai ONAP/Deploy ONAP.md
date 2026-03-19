---
title: PoC ONAP
date: 2026-03-18
---

### Mục tiêu: Triển khai PoC vFWNG bằng ONAP

Để Deploy một phiên bản demo của hệ thống ONAP, mình cần phải có một lộ trình rõ ràng để Handle số lượng Microservices khổng lồ của dự án này.

- Giai đoạn 1: Chuẩn bị tài nguyên và Môi trường
- Giai đoạn 2: Thiết lập hạ tầng Kubernetes & Docker
- Giai đoạn 3: Tinh chỉnh và Cấu hình OOM
- Giai đoạn 4: Deploy và Theo dõi
- Giai đoạn 5: Cấu hình Mạng & Truy cập Portal

[[Kiến Trúc ONAP]]

## Chuẩn bị tài nguyên và môi trường

Hiện tại mình đang có 2 thiết bị

- là laptop của mình:

```bash
               total        used        free      shared  buff/cache   available
Mem:            15Gi       5.7Gi       4.0Gi       1.4Gi       6.9Gi       9.7Gi
Swap:          4.0Gi          0B       4.0Gi
```

- và server Ubuntu:

```bash
               total        used        free      shared  buff/cache   available
Mem:            19Gi       643Mi        18Gi       1.0Mi       728Mi        18Gi
Swap:          2.0Gi          0B       2.0Gi
```

Phân bố kiến trúc trên 2 thiết bị

→ Máy Laptop: Môi trường Design Time

ONAP được xây dựng theo kiến trúc Microservices, đa phần các service cốt lõi của nó lại được viết bằng Java ([Spring Boot](https://spring.io/projects/spring-boot)) ([SO](https://github.com/onap/so), [A&AI](https://github.com/onap/aai-resources), [Policy Framework](https://github.com/onap/policy-api), [CCSDK / SDNC](https://github.com/onap/ccsdk-cds), [DCAE](https://github.com/onap/dcaegen2-collectors-ves), …). File `application.properties` của các repo là nơi chứa các cấu hình Spring Boot.

→ Đặc sản của Java là cơ chế cấp phát bộ nhớ JVM (Java Virtual Machine). Mỗi một pod khi khởi động đều sẽ chiếm trước một [vùng Heap](https://tapit.vn/ram-memory-cau-truc-chuc-nang-phan-vung-heap-stack/#:~:text=4%2E%20Ki%E1%BA%BFn%20tr%C3%BAc%20chung%20c%E1%BB%A7a%20RAM) cố định, chưa cần biết nó có làm việc hay không.