# Kế Hoạch Thực Hành Azure Cloud Lab

## Thông Tin Tài Khoản

| Thông số | Chi tiết |
|---|---|
| Subscription | cloudlabs05 |
| Subscription ID | 0c5e73e6-7614-49fa-bdce-2df003038229 |
| Tenant ID | 9a8cd433-6113-49e5-aa7f-42788a01a246 |
| Resource Group | user-lsmbpzjzuohi |
| Region | East US |

## Giới Hạn Của Tài Khoản (Azure Policy)

- **Loại VM được phép:** Basic_A0, Basic_A1, Basic_A2, Basic_A3, Basic_A4, Standard_A0, Standard_A1, Standard_A2, Standard_A1_v2, Standard_DS1_v2, Standard_DS2_v2
- **RAM tối đa:** 7 GB (Standard_DS2_v2)
- **vCPU tối đa:** 8 (Basic_A4) hoặc 2 vCPU + SSD (Standard_DS2_v2)
- **VM khuyến nghị:** Standard_DS1_v2 (1 vCPU, 3.5 GB, SSD) hoặc Standard_DS2_v2 (2 vCPU, 7 GB, SSD)

---

## Lab 1: Máy Chủ Web Cơ Bản

**Mục tiêu:** Tạo VM Linux, cài Nginx, hiểu NSG (Network Security Group) và Public IP.

**Các bước thực hiện:**
1. Tạo VM Ubuntu 24.04 (Standard_DS1_v2) với SSH key
2. Mở port 80 (HTTP) và 443 (HTTPS) trên NSG
3. SSH vào VM, cài đặt Nginx
4. Tạo trang HTML đơn giản
5. Truy cập trang web qua Public IP
6. (Nâng cao) Cấu hình custom domain với Azure DNS

**Kỹ năng đạt được:** Virtual Machine, NSG, SSH, Public IP, quản trị Linux cơ bản

**Lệnh tham khảo:**
```bash
az vm create --resource-group user-lsmbpzjzuohi --name web-vm --image Ubuntu2404 --size Standard_DS1_v2 --admin-username azureuser --generate-ssh-keys
az vm open-port --resource-group user-lsmbpzjzuohi --name web-vm --port 80
```

---

## Lab 2: WordPress Với Docker Compose

**Mục tiêu:** Triển khai WordPress + MySQL bằng Docker Compose trên VM.

**Các bước thực hiện:**
1. Tạo VM Ubuntu 24.04 (Standard_DS2_v2 - cần RAM cho MySQL)
2. Cài Docker và Docker Compose
3. Viết docker-compose.yml với WordPress + MySQL
4. Chạy Docker Compose, truy cập WordPress
5. Cấu hình WordPress cơ bản (theme, plugin)
6. (Nâng cao) Thêm Nginx reverse proxy + SSL với Let's Encrypt

**Kỹ năng đạt được:** Docker, Docker Compose, container networking

---

## Lab 3: Virtual Network Tùy Chỉnh (Hub-Spoke)

**Mục tiêu:** Xây dựng VNet từ đầu với kiến trúc public/private subnets.

**Các bước thực hiện:**
1. Tạo VNet mới với dải địa chỉ 10.0.0.0/16
2. Tạo Public Subnet (10.0.1.0/24) và Private Subnet (10.0.2.0/24)
3. Tạo NSG riêng cho từng subnet
4. Đặt web server ở Public Subnet với Public IP
5. Đặt database server ở Private Subnet (không Public IP)
6. Cấu hình NAT Gateway cho Private Subnet truy cập internet
7. Kiểm tra kết nối giữa 2 subnet
8. (Nâng cao) Tạo thêm VNet thứ 2 và VNet Peering

**Kỹ năng đạt được:** VNet, Subnets, NSG, NAT Gateway, VNet Peering

**Lệnh tham khảo:**
```bash
az network vnet create --resource-group user-lsmbpzjzuohi --name lab-vnet --address-prefix 10.0.0.0/16 --subnet-name public-subnet --subnet-prefix 10.0.1.0/24
az network vnet subnet create --resource-group user-lsmbpzjzuohi --vnet-name lab-vnet --name private-subnet --address-prefix 10.0.2.0/24
```

---

## Lab 4: Load Balancer + Availability Set

**Mục tiêu:** Tạo Azure Load Balancer phân phối lưu lượng đến nhiều VM.

**Các bước thực hiện:**
1. Sử dụng VNet từ Lab 3
2. Tạo Availability Set
3. Tạo 2 VM (Standard_DS1_v2) trong Availability Set ở Public Subnet
4. Cài web server trên cả 2 VM (nội dung khác nhau)
5. Tạo Azure Load Balancer với health probe
6. Tạo backend pool chứa 2 VM
7. Tạo load balancing rule cho port 80
8. Truy cập qua IP của Load Balancer, kiểm tra round-robin
9. Tắt 1 VM, kiểm tra failover

**Kỹ năng đạt được:** Load Balancer, Availability Set, health probe, high availability

---

## Lab 5: Storage Account + Static Website + CDN

**Mục tiêu:** Lưu trữ website tĩnh trên Azure Blob Storage, phân phối qua CDN.

**Các bước thực hiện:**
1. Tạo Storage Account (StorageV2)
2. Bật tính năng Static Website trên Blob Storage
3. Tải lên các file HTML, CSS, JS vào container `$web`
4. Truy cập website qua endpoint của Storage Account
5. Tạo Azure CDN profile và endpoint trỏ về Storage
6. Truy cập website qua CDN endpoint
7. (Nâng cao) Gắn custom domain và HTTPS

**Kỹ năng đạt được:** Blob Storage, Static Website, Azure CDN

**Lệnh tham khảo:**
```bash
az storage account create --name labstorage01 --resource-group user-lsmbpzjzuohi --location eastus --sku Standard_LRS --kind StorageV2
az storage blob service-properties update --account-name labstorage01 --static-website --index-document index.html
```

---

## Lab 6: Azure Database for MySQL

**Mục tiêu:** Tạo managed database và kết nối từ VM.

**Các bước thực hiện:**
1. Sử dụng VNet từ Lab 3
2. Tạo Azure Database for MySQL Flexible Server (Burstable B1ms)
3. Cấu hình VNet integration hoặc firewall rules
4. Tạo VM trong cùng VNet
5. SSH vào VM, kết nối database bằng mysql client
6. Tạo database, bảng, thêm dữ liệu mẫu
7. (Nâng cao) Cấu hình read replica

**Kỹ năng đạt được:** Azure Database for MySQL, VNet integration, managed database

---

## Lab 7: Azure Functions + API Management

**Mục tiêu:** Xây dựng REST API serverless.

**Các bước thực hiện:**
1. Tạo Function App (runtime Python hoặc Node.js)
2. Viết HTTP trigger function xử lý CRUD
3. Tạo Azure Cosmos DB (hoặc Table Storage) làm backend
4. Kết nối Function với database qua binding
5. Test API bằng curl
6. (Nâng cao) Tạo API Management để quản lý API, thêm API key

**Kỹ năng đạt được:** Azure Functions, Cosmos DB, API Management, serverless

**Lệnh tham khảo:**
```bash
az functionapp create --resource-group user-lsmbpzjzuohi --consumption-plan-location eastus --runtime python --functions-version 4 --name lab-func-app --storage-account labstorage01
```

---

## Lab 8: CI/CD Với Azure DevOps + App Service

**Mục tiêu:** Tạo pipeline tự động triển khai web app.

**Các bước thực hiện:**
1. Tạo Azure App Service Plan (Free tier F1)
2. Tạo Web App trên App Service
3. Kết nối GitHub repository
4. Cấu hình Deployment Center với GitHub Actions
5. Push code → tự động deploy
6. Kiểm tra deployment slots (staging/production)
7. Swap slots để deploy không downtime
8. (Nâng cao) Thêm Application Insights để monitoring

**Kỹ năng đạt được:** App Service, GitHub Actions, deployment slots, CI/CD

---

## Lab 9: Monitoring + Alerts

**Mục tiêu:** Thiết lập hệ thống giám sát toàn diện.

**Các bước thực hiện:**
1. Tạo VM và cài Azure Monitor Agent
2. Cấu hình Data Collection Rules thu thập metrics + logs
3. Tạo Azure Monitor Dashboard
4. Tạo Action Group (gửi email khi có alert)
5. Tạo Alert Rules: CPU > 80%, disk > 90%
6. Bật NSG Flow Logs ghi lại traffic
7. Tạo Log Analytics Workspace, viết KQL query
8. (Nâng cao) Bật Azure Activity Log để audit mọi thao tác

**Kỹ năng đạt được:** Azure Monitor, Log Analytics, KQL, Alert Rules, NSG Flow Logs

---

## Lab 10: Infrastructure as Code (Terraform)

**Mục tiêu:** Tái tạo Lab 3 + Lab 4 bằng Terraform trên Azure.

**Các bước thực hiện:**
1. Cài Terraform, cấu hình Azure Provider
2. Viết main.tf: VNet, Subnets, NSG, NAT Gateway
3. Viết vm.tf: Virtual Machines, Availability Set
4. Viết lb.tf: Load Balancer, backend pool, health probe
5. Viết variables.tf và outputs.tf
6. Chạy terraform init → plan → apply
7. Xác nhận hạ tầng tạo đúng
8. Chạy terraform destroy để dọn dẹp

**Kỹ năng đạt được:** Terraform với Azure, azurerm provider, quản lý state

**Lệnh tham khảo:**
```hcl
provider "azurerm" {
  features {}
  subscription_id = "0c5e73e6-7614-49fa-bdce-2df003038229"
}

resource "azurerm_virtual_network" "lab" {
  name                = "lab-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "eastus"
  resource_group_name = "user-lsmbpzjzuohi"
}
```

---

## Thứ Tự Thực Hiện Khuyến Nghị

```
Lab 1 (VM + Nginx)
  │
  ▼
Lab 2 (WordPress + Docker)
  │
  ▼
Lab 3 (VNet tùy chỉnh) ──────► Lab 6 (Azure Database)
  │                                │
  ▼                                ▼
Lab 4 (Load Balancer) ────────► Lab 9 (Monitoring)
  │
  ▼
Lab 5 (Storage + CDN)
  │
  ▼
Lab 7 (Serverless)
  │
  ▼
Lab 8 (CI/CD)
  │
  ▼
Lab 10 (Terraform - tổng hợp)
```

---

## So Sánh AWS vs Azure

| Khái niệm | AWS | Azure |
|---|---|---|
| Máy ảo | EC2 | Virtual Machine |
| Mạng ảo | VPC | Virtual Network (VNet) |
| Firewall | Security Group | Network Security Group (NSG) |
| Cân bằng tải | ALB/NLB | Azure Load Balancer |
| Lưu trữ đối tượng | S3 | Blob Storage |
| CDN | CloudFront | Azure CDN |
| Database managed | RDS | Azure Database for MySQL/PostgreSQL |
| Serverless | Lambda | Azure Functions |
| API Gateway | API Gateway | API Management |
| CI/CD | CodePipeline | Azure DevOps / GitHub Actions |
| Monitoring | CloudWatch | Azure Monitor |
| IaC | CloudFormation | ARM Templates / Bicep |
| DNS | Route 53 | Azure DNS |

---

## Lưu Ý Chung

- **Dọn dẹp sau mỗi lab:** Xoá toàn bộ resources để tránh phát sinh chi phí
- **VM sizes cho phép:** Chỉ dùng sizes trong danh sách policy (xem bảng ở đầu file)
- **VM khuyến nghị:** Standard_DS1_v2 (nhẹ) hoặc Standard_DS2_v2 (nặng)
- **Bảo mật SSH key:** Không commit vào git
- **Kiểm tra trước/sau:** Chạy `az resource list --output table` để đảm bảo tài khoản sạch
- **Resource Group:** Luôn dùng `user-lsmbpzjzuohi`, không tạo thêm group mới
