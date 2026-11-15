# Hướng dẫn build lại LearnHarness (bản Mac Intel) và cài vào Applications

Làm lần lượt từ trên xuống. Mỗi khung xám là **một lệnh**: copy cả khung, dán vào Terminal, bấm **Enter**, đợi chạy xong rồi mới làm bước tiếp theo.

> Dữ liệu học tập và các key bạn đã nhập **không bị mất** khi build lại hay cài lại app. Chúng được lưu riêng, không nằm trong app.

---

## Chuẩn bị (chỉ làm 1 lần trên mỗi máy)

Mở **Terminal** (nhấn `Cmd + Space`, gõ `Terminal`, bấm Enter) và kiểm tra 2 thứ sau.

**1. Node.js phiên bản 22 trở lên**

```sh
node --version
```

Kết quả phải là `v22...` hoặc số lớn hơn. Nếu thấy số nhỏ hơn (ví dụ `v20.10.0`) hoặc báo `command not found`, tải Node.js bản LTS tại https://nodejs.org, cài xong **mở lại Terminal** rồi kiểm tra lại.

**2. Xcode 26 trở lên**

```sh
ls -d /Applications/Xcode*.app
```

Phải có một dòng Xcode bản 26 trở lên, ví dụ `/Applications/Xcode 26.0.app` hoặc `/Applications/Xcode.app`. Ghi nhớ đúng tên này, Bước 4 sẽ dùng. Nếu chưa có, cài **Xcode** từ App Store (khá nặng, cần chờ lâu), mở Xcode một lần cho nó cài thêm thành phần, rồi kiểm tra lại.

---

## Bước 1. Mở Terminal và vào thư mục project

Mở **Terminal** (nhấn `Cmd + Space`, gõ `Terminal`, bấm Enter), rồi chạy:

```sh
cd ~/labs/learnharness
```

> Nếu bạn để project ở thư mục khác, hãy thay `~/labs/learnharness` bằng đường dẫn tới thư mục đó.

## Bước 2. (Chỉ khi vừa cập nhật code) Cài thư viện

Bỏ qua bước này nếu bạn không thay đổi hay tải code mới.

```sh
npm install
```

## Bước 3. Thoát LearnHarness

Nếu app đang mở, bấm vào app rồi nhấn `Cmd + Q`.

## Bước 4. Build app

Đầu tiên, chỉ cho máy biết Xcode 26 nằm ở đâu:

```sh
export DEVELOPER_DIR="/Applications/Xcode 26.0.app/Contents/Developer"
```

> Nếu ở phần Chuẩn bị Xcode của bạn có tên khác (ví dụ `/Applications/Xcode.app`), thay `Xcode 26.0.app` trong lệnh trên bằng đúng tên đó. Giữ nguyên dấu ngoặc kép.

Sau đó, **trong cùng cửa sổ Terminal**, chạy lệnh build:

```sh
rm -rf dist .next/standalone && npm run build && node scripts/build-server.mjs && npx electron-builder --mac --x64 --config electron-builder.intel.yml
```

- Bước này mất **vài phút**, chữ chạy liên tục là bình thường.
- Chạy xong, kiểm tra kết quả bằng lệnh:

```sh
ls dist/mac
```

Nếu thấy dòng `LearnHarness.app` là build thành công. Nếu báo lỗi, xem mục **Gặp lỗi** ở cuối.

## Bước 5. Thay app cũ trong Applications bằng app mới

```sh
rm -rf /Applications/LearnHarness.app && ditto dist/mac/LearnHarness.app /Applications/LearnHarness.app
```

(Lệnh này chỉ xoá **bản app cũ**, không xoá dữ liệu của bạn.)

## Bước 6. Ký app để macOS cho dùng micro

```sh
codesign --force --deep --sign - /Applications/LearnHarness.app
```

## Bước 7. Đặt lại quyền micro

```sh
tccutil reset Microphone com.learnharness.LearnHarness
```

Thấy chữ `Successfully reset` là được.

## Bước 8. Mở app

```sh
open /Applications/LearnHarness.app
```

Lần đầu bấm **Dictate**, macOS sẽ hỏi quyền dùng micro. Chọn **Allow**.

Xong! Từ giờ bạn có thể mở LearnHarness từ **Launchpad** hoặc thư mục **Applications** như mọi app khác.

---

## Gặp lỗi?

| Hiện tượng | Cách xử lý |
| --- | --- |
| `command not found: npm` | Máy chưa cài Node.js. Tải tại https://nodejs.org (bản LTS), cài xong mở lại Terminal và làm lại từ Bước 1. |
| Bước 4 báo `Unknown file extension ".ts"` | Node.js quá cũ. Làm lại mục **Chuẩn bị → 1**, rồi mở lại Terminal và làm lại từ Bước 1. |
| Bước 4 báo `Failed to check actool version` | Chưa chỉ đúng Xcode 26. Kiểm tra lại mục **Chuẩn bị → 2**, chạy lại lệnh `export DEVELOPER_DIR=...` với đúng tên Xcode, rồi chạy lại lệnh build **trong cùng cửa sổ Terminal**. |
| Bước 4 báo lỗi đỏ khác và dừng | Chạy Bước 2 (`npm install`) rồi chạy lại Bước 4. Vẫn lỗi thì chụp màn hình đoạn chữ đỏ cuối cùng gửi người hỗ trợ. |
| Mở app bị macOS chặn ("cannot be opened") | Vào **System Settings → Privacy & Security**, kéo xuống, bấm **Open Anyway**. |
| Dictate không ra chữ | Kiểm tra **System Settings → Privacy & Security → Microphone**: LearnHarness phải được bật. Nếu không thấy app trong danh sách, làm lại Bước 6 → 8. |
| Dictate ra chữ sai / không ra chữ khi nói tiếng Việt | Trong app: **Settings → Providers → Dictation → Language**, chọn **Vietnamese**. |
