---
title: PoC ONAP
date: 2026-03-18
---

## Mục tiêu: Triển khai PoC vFWNG bằng ONAP

Để Deploy một phiên bản demo của hệ thống ONAP, mình cần phải có một lộ trình rõ ràng để Handle số lượng Microservices khổng lồ của dự án này.

- Giai đoạn 1: Chuẩn bị tài nguyên và Môi trường
- Giai đoạn 2: Thiết lập hạ tầng Kubernetes & Docker
- Giai đoạn 3: Tinh chỉnh và Cấu hình OOM
- Giai đoạn 4: Deploy và Theo dõi
- Giai đoạn 5: Cấu hình Mạng & Truy cập Portal

[Kiến Trúc ONAP](https://halibut205.github.io/Halibut_ONAP_Solution_Research/ki%E1%BA%BFn-tr%C3%BAc-onap/ki%E1%BA%BFn-tr%C3%BAc-onap/)

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

ONAP được xây dựng theo kiến trúc Microservices, đa phần các service cốt lõi của nó lại được viết bằng Java ([Spring Boot](https://spring.io/projects/spring-boot)) ([SO](https://github.com/onap/so), [A&AI](https://github.com/onap/aai-resources), [Policy Framework](https://github.com/onap/policy-api), [CCSDK / SDNC](https://github.com/onap/ccsdk-cds), [DCAE](https://github.com/onap/dcaegen2-collectors-ves), …).

→ Đặc sản của Java là cơ chế cấp phát bộ nhớ JVM (Java Virtual Machine). Mỗi một pod khi khởi động đều sẽ chiếm trước một [vùng Heap](https://tapit.vn/ram-memory-cau-truc-chuc-nang-phan-vung-heap-stack/#:~:text=4%2E%20Ki%E1%BA%BFn%20tr%C3%BAc%20chung%20c%E1%BB%A7a%20RAM) cố định, chưa cần biết nó có làm việc hay không.

Vậy có phải mỗi dịch vụ của ONAP sẽ chạy trên một [pod](https://vietnix.vn/kubernetes-pod/?utm_source=ggads&utm_medium=pmax&utm_campaign={CampaignName}&p=&gad_source=1&gad_campaignid=23234186547&gclid=Cj0KCQjwj47OBhCmARIsAF5wUEFp4849a2zZ0Ds1N8SD0dvlSH8i__ZEpE0tc89VBwMu-fvGpuCJ4o8aAkf_EALw_wcB) không?

-> Về cơ bản, mỗi Microservice (như `so-bpmn-infra`, `sdnc-ansible-server`, `aai-resources`) sẽ chạy trong ít nhất trong một Pod riêng biệt. Tuy nhiên, **một component lớn bao gồm nhiều pod** như SDC sẽ là một cụm gồm: `sdc-be` (Backend), `sdc-fe` (Frontend), `sdc-cassandra`, `sdc-onboarding`.

Với tổng cộng khoảng **36GB RAM** giữa hai máy, mình **không thể** deploy bản Full ONAP mà bắt buộc phải sử dụng chế độ ["Small/Starter" của OOM](https://github.com/onap/oom/blob/master/kubernetes/onap/resources/environments/minimal-onap.yaml) và chỉ chọn lọc các component thiết yếu cho vFWNG:

- **Phải có:** SDC, A&AI, SO, SDNC, Portal, Policy, Robot (để test).
- **Có thể lược bỏ:** DCAE (nếu không cần closed-loop ngay), CLAMP, Multi-VIM.


## Thiết lập hạ tầng Kubernetes & Docker

