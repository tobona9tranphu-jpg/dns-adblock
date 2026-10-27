# 🛡️ DNS Ad Blocker - Chặn Quảng Cáo

Chặn quảng cáo, tracker và mã độc trên iPhone/iPad/Mac bằng DNS-over-HTTPS.

> **An toàn tuyệt đối**: Không Root CA, không SSL Interception, không MITM.

---

## 📦 Bao gồm

| File | Mô tả |
|---|---|
| `dns-adblock.mobileconfig` | Profile DNS-over-HTTPS cho iOS/macOS |
| `index.html` | Trang web tải và hướng dẫn cài đặt |
| `README.md` | File này |

## 🚀 Cách hoạt động

```
iPhone ──DNS query──▶ AdGuard DNS Server
                           │
                     ┌─────┴─────┐
                     │ Blocklist │
                     │ 1M+ domain│
                     └─────┬─────┘
                           │
              ┌────────────┼────────────┐
              ▼                         ▼
        Domain quảng cáo          Domain bình thường
        → Trả 0.0.0.0             → Trả IP thật
        (quảng cáo biến mất)      (truy cập bình thường)
```

Profile cấu hình thiết bị sử dụng **AdGuard Public DNS** (`dns.adguard-dns.com`) — dịch vụ DNS miễn phí, chặn quảng cáo ở tầng DNS.

## 📋 Cài đặt trên iPhone

### Cách 1: Từ GitHub Pages (khuyên dùng)

1. Mở **Safari** trên iPhone
2. Truy cập URL website GitHub Pages của bạn
3. Nhấn **"⬇️ Tải Profile DNS"**
4. Safari hỏi cho phép → nhấn **Cho phép**
5. Mở **Cài đặt** → **Đã tải về Profile** → **Cài đặt**
6. Nhập mật khẩu → Xong!

### Cách 2: Cài trực tiếp từ file

1. Gửi file `dns-adblock.mobileconfig` qua AirDrop hoặc iMessage
2. Mở file → Cài đặt → nhấn **Cài đặt**

## 🌐 Deploy lên GitHub Pages

### Bước 1: Tạo Repository

1. Vào [github.com/new](https://github.com/new)
2. Repository name: `dns-adblock`
3. Chọn **Public**
4. Nhấn **Create repository**

### Bước 2: Upload files

1. Nhấn **"uploading an existing file"**
2. Kéo thả 3 file: `index.html`, `dns-adblock.mobileconfig`, `README.md`
3. Nhấn **Commit changes**

### Bước 3: Bật GitHub Pages

1. Vào **Settings** → **Pages**
2. Source: **Deploy from a branch**
3. Branch: `main`, Folder: `/ (root)`
4. Nhấn **Save**
5. Đợi 1-2 phút → URL sẽ là: `https://YOUR_USERNAME.github.io/dns-adblock/`

## 🔧 Tùy chỉnh DNS Server

Mặc định profile sử dụng **AdGuard Public DNS**. Bạn có thể đổi sang:

| DNS Provider | DoH URL | Chặn quảng cáo |
|---|---|---|
| **AdGuard DNS** (mặc định) | `https://dns.adguard-dns.com/dns-query` | ✅ Có |
| Cloudflare (nhanh nhất) | `https://cloudflare-dns.com/dns-query` | ❌ Không |
| Cloudflare Family | `https://family.cloudflare-dns.com/dns-query` | ✅ Malware + Adult |
| Google DNS | `https://dns.google/dns-query` | ❌ Không |
| Quad9 | `https://dns.quad9.net/dns-query` | ✅ Malware |
| NextDNS | `https://dns.nextdns.io/YOUR_ID` | ✅ Tùy chỉnh |

Để đổi: mở file `.mobileconfig` bằng text editor → thay URL trong `<string>` sau `ServerURL`.

## 🏠 Tự dựng DNS Server riêng (nâng cao)

Nếu muốn tự kiểm soát hoàn toàn, bạn có thể dựng **AdGuard Home** trên VPS:

### Cài đặt nhanh (Ubuntu/Debian)

```bash
# Cài AdGuard Home
curl -s -S -L https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh -s -- -v

# Mở web admin: http://YOUR_IP:3000
# Setup SSL + DoH trong Settings → Encryption
```

### Dùng Docker

```bash
docker run -d \
  --name adguardhome \
  --restart always \
  -p 53:53/udp -p 53:53/tcp \
  -p 80:80/tcp -p 443:443/tcp \
  -p 3000:3000/tcp \
  -v /opt/adguardhome/work:/opt/adguardhome/work \
  -v /opt/adguardhome/conf:/opt/adguardhome/conf \
  adguard/adguardhome
```

### Sau khi cài xong

1. Cấu hình SSL với Let's Encrypt
2. Bật DNS-over-HTTPS trong Encryption Settings
3. Cập nhật `ServerURL` trong `.mobileconfig` thành URL DoH của bạn
4. Thêm blocklist: ABPVN, AdGuard DNS Filter, OISD, v.v.

## 🔐 Bảo mật

| Tiêu chí | Profile này | Profile "VIP/Gold" |
|---|---|---|
| Root CA | ❌ Không có | ⚠️ Có — cho phép đọc HTTPS |
| SSL Interception | ❌ Không | ⚠️ Có — giải mã traffic |
| Đọc mật khẩu | ❌ Không thể | ⚠️ Có thể |
| Gỡ bỏ được | ✅ Bất cứ lúc nào | ✅ Thường được |
| DNS Provider | AdGuard (uy tín) | Server bên thứ 3 |

## ❓ FAQ

**Q: Có ảnh hưởng tốc độ mạng không?**
A: Không đáng kể. AdGuard DNS có server ở châu Á (Singapore, Nhật), tốc độ rất nhanh.

**Q: Có chặn được quảng cáo YouTube không?**
A: Chặn được một phần. YouTube phục vụ quảng cáo từ cùng domain với video, nên DNS blocking không chặn được 100%.

**Q: Profile có an toàn không?**
A: Có. Bạn có thể mở file `.mobileconfig` bằng bất kỳ text editor nào để xem nội dung. Không có Root CA, không có mã độc.

**Q: Cài profile có bị mất dữ liệu không?**
A: Không. Profile chỉ thay đổi cấu hình DNS, không ảnh hưởng dữ liệu hay ứng dụng.

**Q: Gỡ profile thế nào?**
A: Cài đặt → Cài đặt chung → VPN & Quản lý thiết bị → chọn profile → Xóa Profile.

---

**License**: MIT — Tự do sử dụng và chia sẻ.
