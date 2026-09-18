# Kế Hoạch Thực Hành AWS Cloud Lab

## Thông Tin Tài Khoản

| Thông số | Chi tiết |
|---|---|
| Account ID | 379620500929 |
| Region | us-east-1 |
| User | user-watytstzgqob |
| VPC mặc định | vpc-0f391abcff57b7ee6 (172.31.0.0/16) |
| Tổ chức | o-ddkcxdbdz1 (có SCP giới hạn) |

## Giới Hạn Của Tài Khoản (SCP)

- **Loại máy ảo được phép:** t2.nano, t2.micro, t2.small, t3.nano, t3.micro, t3.small, t4g.nano, t4g.micro, t4g.small, m3.medium
- **IAM:** Không xem được policies (bị SCP chặn)
- **RAM tối đa:** 3.75 GB (m3.medium)
- **vCPU tối đa:** 2 (dòng t3/t4g)

---

## Lab 1: Máy Chủ Web Cơ Bản

**Mục tiêu:** Dựng máy chủ EC2 cài Nginx, hiểu cách hoạt động của Security Group, SSH và Public IP.

**Các bước thực hiện:**
1. Tạo key pair để truy cập SSH
2. Tạo Security Group mở cổng 22 (SSH), 80 (HTTP), 443 (HTTPS)
3. Khởi tạo máy ảo EC2 loại t3.small chạy Ubuntu 24.04
4. SSH vào máy ảo và cài đặt Nginx
5. Tạo một trang HTML đơn giản
6. Truy cập trang web qua địa chỉ Public IP

**Kỹ năng đạt được:** EC2, Security Group, SSH, quản trị Linux cơ bản

---

## Lab 2: Triển Khai WordPress Bằng Docker

**Mục tiêu:** Cài đặt WordPress sử dụng Docker Compose trên máy ảo EC2.

**Các bước thực hiện:**
1. Khởi tạo máy ảo EC2 loại t3.small chạy Ubuntu 24.04
2. Cài đặt Docker và Docker Compose
3. Viết file docker-compose.yml gồm WordPress và MySQL
4. Chạy Docker Compose và truy cập WordPress
5. (Nâng cao) Cấu hình chứng chỉ SSL với Certbot

**Kỹ năng đạt được:** Docker, Docker Compose, mạng giữa các container

---

## Lab 3: Thiết Kế VPC Tùy Chỉnh - Public/Private Subnets

**Mục tiêu:** Xây dựng VPC từ đầu với kiến trúc 2 tầng gồm mạng công khai và mạng riêng tư.

**Các bước thực hiện:**
1. Tạo VPC mới với dải địa chỉ 10.0.0.0/16
2. Tạo 2 Public Subnets (10.0.1.0/24 và 10.0.2.0/24)
3. Tạo 2 Private Subnets (10.0.10.0/24 và 10.0.20.0/24)
4. Tạo Internet Gateway và gắn vào VPC
5. Tạo NAT Gateway trong Public Subnet
6. Cấu hình Route Tables: public đi qua IGW, private đi qua NAT
7. Đặt web server ở Public Subnet, database ở Private Subnet
8. Kiểm tra kết nối giữa 2 tầng mạng

**Kỹ năng đạt được:** VPC, Subnets, Route Tables, Internet Gateway, NAT Gateway

---

## Lab 4: Cân Bằng Tải Và Tự Động Mở Rộng

**Mục tiêu:** Tạo Application Load Balancer phân phối lưu lượng đến 2 máy chủ EC2.

**Các bước thực hiện:**
1. Sử dụng VPC đã tạo ở Lab 3
2. Khởi tạo 2 máy ảo EC2 loại t2.micro ở 2 vùng khả dụng (AZ) khác nhau
3. Cài web server trên cả 2 máy (nội dung khác nhau để phân biệt)
4. Tạo Target Group chứa 2 máy ảo
5. Tạo Application Load Balancer
6. Truy cập qua địa chỉ DNS của ALB, kiểm tra phân phối tải
7. Tắt 1 máy ảo, kiểm tra khả năng chuyển đổi dự phòng (failover)
8. (Nâng cao) Tạo Auto Scaling Group để tự động tăng/giảm số máy

**Kỹ năng đạt được:** ALB, Target Group, Auto Scaling, tính sẵn sàng cao

---

## Lab 5: Website Tĩnh Trên S3 Và CloudFront

**Mục tiêu:** Lưu trữ website tĩnh trên S3 và phân phối nội dung qua CloudFront CDN.

**Các bước thực hiện:**
1. Tạo S3 bucket
2. Tải lên các file HTML, CSS, JS
3. Bật tính năng Static Website Hosting trên S3
4. Cấu hình Bucket Policy cho phép đọc công khai
5. Tạo CloudFront Distribution trỏ về S3
6. Truy cập website qua đường dẫn CloudFront
7. (Nâng cao) Gắn tên miền riêng với Route 53

**Kỹ năng đạt được:** S3, CloudFront, Bucket Policy, mạng phân phối nội dung (CDN)

---

## Lab 6: Cơ Sở Dữ Liệu RDS

**Mục tiêu:** Tạo cơ sở dữ liệu RDS (MySQL hoặc PostgreSQL) và kết nối từ EC2.

**Các bước thực hiện:**
1. Sử dụng VPC đã tạo ở Lab 3
2. Tạo DB Subnet Group từ các Private Subnets
3. Tạo Security Group cho RDS, chỉ mở cổng 3306 (MySQL) hoặc 5432 (PostgreSQL) từ Private Subnet
4. Khởi tạo RDS phiên bản Free Tier (db.t3.micro)
5. Khởi tạo EC2 trong cùng VPC
6. SSH vào EC2, kết nối đến RDS bằng mysql hoặc psql client
7. Tạo database, bảng, thêm dữ liệu mẫu
8. (Nâng cao) Tạo Read Replica để phân tải đọc

**Kỹ năng đạt được:** RDS, DB Subnet Group, kết nối cơ sở dữ liệu

---

## Lab 7: Ứng Dụng Serverless Với Lambda + API Gateway

**Mục tiêu:** Xây dựng REST API không cần máy chủ (serverless).

**Các bước thực hiện:**
1. Viết Lambda function bằng Python xử lý các thao tác CRUD
2. Tạo bảng DynamoDB làm nơi lưu trữ dữ liệu
3. Tạo REST API trên API Gateway
4. Tạo các resource và method: GET, POST, PUT, DELETE
5. Kết nối luồng: API Gateway -> Lambda -> DynamoDB
6. Kiểm thử API bằng lệnh curl
7. (Nâng cao) Thêm xác thực bằng API Key

**Kỹ năng đạt được:** Lambda, API Gateway, DynamoDB, kiến trúc Serverless

---

## Lab 8: Quy Trình CI/CD Tự Động

**Mục tiêu:** Tạo pipeline tự động triển khai mã nguồn lên EC2 mỗi khi có thay đổi.

**Các bước thực hiện:**
1. Tạo kho mã nguồn trên CodeCommit (hoặc dùng GitHub)
2. Tạo S3 bucket lưu trữ artifacts (sản phẩm build)
3. Tạo CodeBuild project để biên dịch và kiểm thử
4. Tạo CodeDeploy application để triển khai
5. Cài đặt CodeDeploy agent trên máy ảo EC2
6. Tạo CodePipeline kết nối tất cả các bước lại
7. Đẩy mã nguồn lên -> hệ thống tự động triển khai
8. Kiểm thử khả năng quay lại phiên bản trước (rollback)

**Kỹ năng đạt được:** CodePipeline, CodeBuild, CodeDeploy, quy trình CI/CD

---

## Lab 9: Giám Sát Và Ghi Log

**Mục tiêu:** Thiết lập hệ thống giám sát toàn diện cho hạ tầng đám mây.

**Các bước thực hiện:**
1. Cài đặt CloudWatch Agent trên EC2
2. Tạo các chỉ số tùy chỉnh: RAM, dung lượng ổ đĩa
3. Tạo CloudWatch Dashboard hiển thị tổng quan hệ thống
4. Tạo SNS topic và đăng ký nhận thông báo qua email
5. Tạo CloudWatch Alarms: cảnh báo khi CPU > 80%, ổ đĩa > 90%
6. Bật VPC Flow Logs ghi lại lưu lượng mạng vào CloudWatch Logs
7. Tạo bộ lọc chỉ số (metric filters) và cảnh báo từ log
8. (Nâng cao) Tạo CloudTrail trail ghi lại mọi hành động trên tài khoản

**Kỹ năng đạt được:** CloudWatch, SNS, VPC Flow Logs, CloudTrail

---

## Lab 10: Hạ Tầng Dưới Dạng Mã (Terraform)

**Mục tiêu:** Tái tạo toàn bộ Lab 3 và Lab 4 bằng Terraform, quản lý hạ tầng qua mã nguồn.

**Các bước thực hiện:**
1. Cài đặt Terraform (đã có sẵn trên Codespace)
2. Viết main.tf: định nghĩa VPC, Subnets, Internet Gateway, NAT Gateway
3. Viết ec2.tf: định nghĩa máy ảo EC2 và Security Groups
4. Viết alb.tf: định nghĩa Load Balancer và Target Group
5. Viết variables.tf (biến đầu vào) và outputs.tf (kết quả đầu ra)
6. Chạy lần lượt: terraform init -> terraform plan -> terraform apply
7. Xác nhận hạ tầng được tạo đúng như mong đợi
8. Chạy terraform destroy để dọn dẹp toàn bộ tài nguyên

**Kỹ năng đạt được:** Terraform, quản lý hạ tầng bằng mã, quản lý state

---

## Thứ Tự Thực Hiện Khuyến Nghị

```
Lab 1 (Máy chủ web cơ bản)
  │
  ▼
Lab 2 (WordPress + Docker)
  │
  ▼
Lab 3 (VPC tùy chỉnh) ──────► Lab 6 (Cơ sở dữ liệu RDS)
  │                                │
  ▼                                ▼
Lab 4 (Cân bằng tải) ──────► Lab 9 (Giám sát)
  │
  ▼
Lab 5 (S3 + CloudFront)
  │
  ▼
Lab 7 (Serverless)
  │
  ▼
Lab 8 (CI/CD)
  │
  ▼
Lab 10 (Terraform - tổng hợp tất cả)
```

---

## Lưu Ý Chung

- **Dọn dẹp sau mỗi lab:** Luôn xoá toàn bộ tài nguyên sau khi hoàn thành để tránh phát sinh chi phí
- **Ưu tiên Free Tier:** Sử dụng các loại tài nguyên miễn phí như t2.micro, t3.micro, db.t3.micro
- **Giới hạn SCP:** Chỉ được dùng các loại máy ảo trong danh sách cho phép, không tạo được loại lớn hơn
- **Bảo mật key pair:** Lưu giữ cẩn thận, không commit vào git
- **Kiểm tra trước và sau:** Chạy `aws ec2 describe-instances` trước và sau mỗi lab để đảm bảo tài khoản sạch
