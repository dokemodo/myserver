# Hysteria 2 - Fake YouTube Traffic via QUIC + TLS (Docker Deployment)

## 🌐 Mục tiêu
Triển khai server VPN dùng Hysteria 2, ngụy trang lưu lượng giống như đang xem video YouTube thông qua giao thức QUIC + TLS, tương thích với Shadowrocket trên iOS.

---

## ✅ Yêu cầu trước khi bắt đầu

- VPS chạy Ubuntu/Debian
- Tên miền trỏ về IP của VPS (ví dụ: vpn.example.com)
- Đã mở port `443/UDP`
- Docker + Docker Compose
- Chứng chỉ TLS từ Let's Encrypt

---

## 🛠 Cài đặt và chạy

### 1. Lấy chứng chỉ TLS (nếu chưa có)

```bash
sudo apt install certbot -y
sudo certbot certonly --standalone -d vpn.example.com
```

### 2. Giải nén gói cấu hình

```bash
unzip hysteria2-youtube-style.zip
cd hysteria2-docker
```

### 3. Khởi chạy Hysteria 2

```bash
docker compose up -d
```

### 4. Mở cổng tường lửa nếu cần

```bash
sudo ufw allow 443/udp
```

---

## 🔐 File cấu hình `config/config.yaml`

- TLS sử dụng chứng chỉ thật
- ALPN: `h3`, `h2`, `http/1.1` — giống traffic từ app YouTube
- SNI: `rr3---sn-i3belnez.googlevideo.com` — subdomain thật của YouTube
- Obfuscation: `salamander`

---

## 📱 Cấu hình Shadowrocket (iOS)

| Trường         | Giá trị                                      |
|----------------|-----------------------------------------------|
| Type           | Hysteria2                                    |
| Server         | vpn.example.com                              |
| Port           | 443                                          |
| Password       | your-strong-password                         |
| TLS            | ✅ ON                                        |
| SNI            | rr3---sn-i3belnez.googlevideo.com           |
| ALPN           | h3,h2,http/1.1                               |
| Obfs           | ✅ ON                                        |
| Obfs Type      | salamander                                   |
| Obfs Password  | obfs-secret                                  |

---

## 🧪 Kiểm tra bằng Wireshark

- Gói gửi đi qua UDP port 443
- TLS handshake hiển thị SNI là `*.googlevideo.com`
- ALPN là `h3` hoặc `h2`
- Traffic trông giống như đang xem YouTube qua app

---

## 📌 Ghi chú nâng cao

- Có thể thay đổi `sni` thành subdomain Google CDN khác nếu cần
- Nên thiết lập cron để tự động gia hạn chứng chỉ TLS
- Kết hợp với `fail2ban` để bảo vệ server

---

## ✨ Tác giả

Được tạo bởi ChatGPT - OpenAI, hỗ trợ bởi người dùng yêu cầu ẩn danh traffic cao.
