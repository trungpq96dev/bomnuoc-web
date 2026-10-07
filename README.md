# Trang điều khiển bơm nước

Trang web theo dõi và điều khiển hệ thống bơm nước chạy trên ESP32, qua MQTT.
Mở được từ bất kỳ đâu có mạng — máy cơ quan, điện thoại 4G.

Chỉ có một file `index.html`, không cần máy chủ, không cần build.

## Trang này không chứa mật khẩu

Địa chỉ broker, tên đăng nhập và mật khẩu **do bạn gõ vào lúc dùng**, trình duyệt
nhớ lại trong máy (localStorage). Không có gì nhạy cảm nằm trong mã nguồn, nên
để repo công khai là an toàn.

> ⚠️ Repo chứa **firmware** thì ngược lại: `config.h` có mật khẩu WiFi, MQTT và
> OTA. Repo đó phải để **private**. Đó là lý do trang web tách ra đây.

## Bật GitHub Pages

1. Đẩy repo này lên GitHub, để **Public**
2. **Settings → Pages**
3. *Source*: **Deploy from a branch**, branch `main`, thư mục `/ (root)`
4. **Save**, chờ khoảng một phút

Địa chỉ sẽ là:

```
https://TEN-GITHUB-CUA-BAN.github.io/bomnuoc-web/
```

Mở trên iPhone rồi chọn **Chia sẻ → Thêm vào MH chính** là có icon như một ứng dụng.

## Dùng lần đầu

| Ô | Điền gì |
|---|---|
| Địa chỉ cụm | Tên host broker, ví dụ `xxxxxxxx.ala.asia-southeast1.emqxsl.com` |
| Cổng WebSocket | **8084** với EMQX, **8884** với HiveMQ |
| Tên đăng nhập / Mật khẩu | Tài khoản tạo trong phần Authentication của broker |

🔴 **Broker có hai cổng khác nhau.** ESP32 nối bằng **8883** (MQTT over TLS), còn
trình duyệt nối bằng **8084** (WebSocket over TLS). Điền nhầm là không nối được.

Nên tạo **tài khoản riêng cho trang web**, khác với tài khoản của ESP32. Lộ cái
nào thì chỉ cần đổi cái đó, khỏi phải nạp lại firmware.

## Đọc thông báo kết nối

Dòng chữ ngay dưới tiêu đề cho biết đang tắc ở đâu:

| Hiện gì | Nghĩa là |
|---|---|
| `lỗi: không nối được` | Sai host, sai cổng, sai tài khoản, hoặc mạng chặn |
| `nối được broker nhưng chưa thấy bo` | Trang ổn — **vấn đề ở ESP32**: chưa nạp firmware, chưa bật nguồn, hoặc sai tài khoản MQTT phía bo |
| `bo đang offline` | Broker báo bo mất kết nối (mất điện hoặc mất WiFi) |
| `đang theo dõi trực tiếp` | Xong, mọi thứ thông suốt |

Mạng công ty hay chặn các cổng lạ như 8084. Ở nhà chạy được mà cơ quan không thì
đó là tường lửa, không phải lỗi trang — mở bằng 4G trên điện thoại là được.

## Các topic MQTT

| Topic | Chiều | Nội dung |
|---|---|---|
| `bomnuoc/state` | bo → trang | JSON trạng thái, có cờ retained |
| `bomnuoc/usage` | bo → trang | Lượng nước 12 tháng gần nhất, retained |
| `bomnuoc/status` | bo → trang | `online` / `offline`, dùng cơ chế last will |
| `bomnuoc/cmd` | trang → bo | `tank=auto`, `booster=off`, `valve=open`, `clear=1`… |

## Thư viện

Dùng [MQTT.js](https://github.com/mqttjs/MQTT.js) tải từ unpkg, đã ghim phiên bản
major (`mqtt@5`) để một ngày nào đó họ đổi API thì trang không chết.
