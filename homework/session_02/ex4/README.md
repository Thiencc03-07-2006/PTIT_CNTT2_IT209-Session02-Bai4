# Exercise 4: UFW & Cloud Firewall Integration

## 1. Thông tin Server

* **Hostname:** `ptit-devops`
* **OS:** Ubuntu 24.04.2 LTS
* **Public IP:** `221.121.3.169`
* **Web Server:** Nginx
* **Cloud Provider:** WiServicesPlatform

---

## 2. Cấu hình UFW

UFW (Uncomplicated Firewall) được cấu hình để bảo vệ máy chủ và chỉ cho phép các kết nối cần thiết.

### Chính sách mặc định

* Incoming: `deny`
* Outgoing: `allow`

### Các port được cho phép

* TCP `22`: SSH
* TCP `80`: HTTP

### Kiểm tra trạng thái UFW

Command:

```bash
sudo ufw status verbose
```

Kết quả:

```text
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
80/tcp (v6)                ALLOW IN    Anywhere (v6)
```

### Screenshot UFW

![alt text](<ufw status verbose.png>)

---

## 3. Cloud Firewall

Cloud Firewall được cấu hình tại WiServicesPlatform để kiểm soát traffic trước khi đến máy chủ.

### Inbound Rules

| Protocol | Port | Action | Source      | Purpose |
| -------- | ---: | ------ | ----------- | ------- |
| TCP      |   22 | ACCEPT | `0.0.0.0/0` | SSH     |
| TCP      |   80 | ACCEPT | `0.0.0.0/0` | HTTP    |

### Screenshot Cloud Firewall

![alt text](<Cloud Firewall.png>)

**Lưu ý:** Bài thực hành yêu cầu DigitalOcean Cloud Firewall, tuy nhiên server thực tế của bài này được triển khai trên WiServicesPlatform. Vì vậy Cloud Firewall được cấu hình tương đương trên WiServicesPlatform.

---

## 4. Kiểm tra kết nối HTTP

Từ máy Windows, kiểm tra port `80`:

```powershell
Test-NetConnection 221.121.3.169 -Port 80
```

Kết quả:

```text
ComputerName     : 221.121.3.169
RemoteAddress    : 221.121.3.169
RemotePort       : 80
TcpTestSucceeded : True
```

Kết luận: **HTTP port 80 được phép truy cập thành công.**

Website:

```text
http://221.121.3.169
```

Hiển thị:

```text
Welcome to PTIT DevOps Course - Session 02
```

---

## 5. Kiểm tra port không được mở

Kiểm tra port `8080`, port không được khai báo trong firewall:

```powershell
Test-NetConnection 221.121.3.169 -Port 8080
```

Kết quả:

```text
ComputerName     : 221.121.3.169
RemoteAddress    : 221.121.3.169
RemotePort       : 8080
TcpTestSucceeded : False
```

Kết luận: **Port 8080 bị chặn**, đúng với chính sách mặc định `deny` của UFW.

---

## 6. Kiểm tra SSH

SSH được cho phép thông qua port `22`.

Command:

```powershell
ssh root@221.121.3.169
```

SSH connection thành công.

---

## 7. Kết luận

Firewall của server đã được cấu hình theo nguyên tắc chỉ cho phép các dịch vụ cần thiết:

* `22/tcp` → SSH
* `80/tcp` → HTTP
* Các port khác → bị chặn theo chính sách `deny` mặc định.

UFW đang ở trạng thái `active` và được cấu hình tự động khởi động cùng hệ thống.

Cloud Firewall cũng cho phép các kết nối SSH và HTTP cần thiết.