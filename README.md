# Hệ thống quản lý Trung tâm Thăng Long – Cơ sở liên kết Bình Tân

Web app quản lý trung tâm luyện thi, chạy hoàn toàn trên **GitHub Pages**, không cần máy chủ riêng.

**Địa chỉ trung tâm:** 542B Tỉnh lộ 10, Phường Bình Trị Đông B, Quận Bình Tân, TP.HCM

## Tính năng

| Phân hệ | Chức năng |
|---|---|
| **Giám đốc** | Tổng quan buổi học trong ngày, doanh thu, công nợ |
| | Cấp tài khoản và phân quyền |
| | Quản lý giáo viên và học sinh |
| | Cấu hình lớp: sĩ số, phòng, học phí |
| | Thời khoá biểu **kéo thả**, tự chặn trùng phòng hoặc trùng giáo viên, tự báo lịch cho giáo viên |
| | Học phí và công nợ: biên lai in được, số tiền bằng chữ |
| | Chi phí và lương giáo viên tính theo buổi điểm danh |
| | Báo cáo thu – chi theo tuần/tháng/năm, có biểu đồ và xuất CSV |
| | Phản hồi, cài đặt hệ thống, sao lưu và khôi phục |
| **Thu ngân** | Thu học phí, biên lai, công nợ, tra cứu học sinh |
| **Giáo viên** | Lịch dạy và thông báo |
| | Điểm danh 3 trạng thái, báo cáo buổi học |
| | Tải tài liệu (tệp hoặc link Drive) |
| | Tạo bài trắc nghiệm trực tuyến có công thức LaTeX |
| | Trả lời câu hỏi học sinh, xem phản hồi |
| **Học sinh & phụ huynh** | Lịch học, kho tài liệu, làm bài trực tuyến có chấm ngay |
| | **Hỏi đáp thông minh:** gõ công thức Toán–Lí–Hoá, bảng tuần hoàn, chụp ảnh đề; hỏi AI hoặc gửi giáo viên |
| | Xem học phí, gửi đánh giá |

Công nghệ: HTML + Tailwind CSS (CDN), JavaScript thuần, MathJax (LaTeX và `\ce{}` hoá học), Chart.js, Firebase Firestore (tuỳ chọn).

## Tài khoản dùng thử

| Vai trò | Tên đăng nhập | Mật khẩu |
|---|---|---|
| Giám đốc | `admin` | `admin123` |
| Thu ngân | `thungan` | `123456` |
| Giáo viên | `gv.hung`, `gv.mai`, `gv.tuan`, `gv.ha`, `gv.son`, `gv.bao` | `123456` |
| Học sinh | `hs01` … `hs30` | `123456` |

---

## 1. Đưa lên GitHub Pages (khoảng 5 phút)

1. Đăng nhập GitHub → **New repository**.
   - Đặt tên, ví dụ `thanglong-binhtan`.
   - Chọn **Public**. Gói miễn phí chỉ bật Pages cho repo công khai.
2. Trong repo, chọn **Add file → Upload files**.
   - Kéo **toàn bộ nội dung** thư mục này vào: `index.html`, `css/`, `js/`, `.nojekyll`…
   - Bấm **Commit changes**.
3. Vào **Settings → Pages**.
   - Mục *Source* chọn **Deploy from a branch**.
   - *Branch* chọn **main**, thư mục **/(root)**, rồi bấm **Save**.
4. Chờ 1–2 phút. Trang chạy tại `https://<tên-tài-khoản>.github.io/thanglong-binhtan/`.

Lúc này app đã chạy ở **chế độ thử nghiệm**: dữ liệu lưu trong trình duyệt của từng máy. Góc trên hiện nhãn "Lưu trên máy này".

## 2. Bật Firebase để mọi thiết bị dùng chung dữ liệu (miễn phí)

1. Vào https://console.firebase.google.com → **Add project**. Có thể tắt Google Analytics.
2. Tạo cơ sở dữ liệu:
   - Chọn **Build → Firestore Database → Create database**.
   - Chọn vị trí `asia-southeast1` (Singapore), chế độ **Production**.
3. Bật đăng nhập ẩn danh: **Build → Authentication → Get started → Sign-in method → Anonymous → Enable**.
4. Dán luật bảo mật:
   - Vào **Firestore → Rules**.
   - Dán nội dung file [`firestore.rules`](firestore.rules), rồi bấm **Publish**.
5. Lấy cấu hình web app:
   - Vào **Project settings (⚙) → Your apps → biểu tượng `</>`**, đặt tên app.
   - Sao chép đối tượng `firebaseConfig`.
6. Mở `js/config.js` trên GitHub (biểu tượng bút chì).
   - Thay `firebase: null` bằng cấu hình vừa chép, rồi **Commit**.
7. Thêm tên miền được phép:
   - Vào **Authentication → Settings → Authorized domains → Add domain**.
   - Nhập `<tên-tài-khoản>.github.io`.

Mở lại trang. Nhãn phải chuyển thành **"Đã đồng bộ"**.

- Lần đầu, hệ thống tự nạp dữ liệu mẫu lên Firestore.
- Mọi thay đổi đồng bộ tức thì sang các máy đang mở.
- Khi hai người sửa cùng lúc, thay đổi được **gộp tự động theo từng bản ghi**. Chỉ khi hai người sửa đúng cùng một bản ghi thì người sau mới được nhắc làm lại.

**Tệp tài liệu:**

- Tệp dưới 680 KB được lưu thẳng trong Firestore, không cần Firebase Storage nên không phải nâng gói trả phí.
- Tệp lớn hơn: tải lên Google Drive, chọn "Bất kỳ ai có đường liên kết", rồi dán link khi đăng tài liệu.

## 3. Cấu hình trợ lý AI

Có hai cách.

**Cách nhanh (để thử):**

- Đăng nhập `admin` → **Cài đặt hệ thống → Trợ lý AI**.
- Chọn nhà cung cấp và dán khoá API. Khoá Gemini lấy miễn phí tại https://aistudio.google.com/apikey.
- Bấm **Hỏi thử trợ lý AI** để kiểm tra.

> ⚠ **Nhược điểm của cách nhanh:** khoá được gửi xuống trình duyệt người dùng, nên người có kiến thức kỹ thuật đọc được.
> Nếu dùng cách này, vào Google Cloud Console → *APIs & Services → Credentials* để giới hạn khoá theo **HTTP referrer** `https://<tên-tài-khoản>.github.io/*`.

**Cách an toàn (khuyến nghị): máy chủ trung gian Cloudflare Worker, miễn phí**

1. Đăng ký https://dash.cloudflare.com.
2. Tạo Worker và dán mã:
   - Vào **Workers & Pages → Create → Create Worker**, rồi **Deploy**.
   - Bấm **Edit code**, dán toàn bộ file [`cloudflare-worker/ai-proxy.js`](cloudflare-worker/ai-proxy.js), rồi **Deploy**.
3. Khai báo biến môi trường trong **Settings → Variables and Secrets**:
   - `GEMINI_API_KEY`: loại **Secret**, dán khoá Gemini.
   - `ALLOWED_ORIGINS`: `https://<tên-tài-khoản>.github.io`.
   - Tuỳ chọn: `PROVIDER=anthropic` kèm `ANTHROPIC_API_KEY` nếu muốn dùng Claude.
4. Chép địa chỉ Worker (dạng `https://ten-worker.ten-ban.workers.dev`).
   - Dán vào `aiProxyUrl` trong `js/config.js`, rồi Commit.

## 4. Chạy thử trên máy tính

Ứng dụng tải MathJax, Chart.js và Firebase qua mạng, nên cần có Internet. Nên chạy qua một máy chủ tĩnh thay vì mở trực tiếp file:

```bash
npx serve .          # hoặc: python -m http.server 8000
```

Dùng Live Server của VS Code cũng được.

## 5. Cấu trúc mã nguồn

```
index.html                  Khung trang, nạp thư viện
css/style.css               Giao diện (màu Royal Blue #1E3A8A, điểm nhấn vàng cam #F59E0B/#D97706)
js/config.js                CẤU HÌNH: Firebase, máy chủ trung gian AI
js/app.js                   Toàn bộ logic: dữ liệu mẫu, phân quyền, các phân hệ, đồng bộ Firestore, AI
firestore.rules             Luật bảo mật Firestore
cloudflare-worker/          Máy chủ trung gian AI (tuỳ chọn)
.nojekyll                   Để GitHub Pages phục vụ nguyên trạng các tệp
```

**Dữ liệu trên Firestore:**

- Mỗi nhóm dữ liệu (học sinh, học phí, điểm danh…) được lưu thành tài liệu trong collection `thanglong_data`.
  - Nhóm lớn tự chia thành nhiều mảnh.
  - Có thêm tài liệu `_meta` ghi số phiên bản.
- Tệp đính kèm nằm trong `thanglong_files`.

## 6. Giới hạn của bản hiện tại

Đây là bản **thử nghiệm và trình diễn**. Trước khi vận hành chính thức với dữ liệu thật:

- **Đăng nhập** đang kiểm tra trên trình duyệt. Mọi người đều tải cùng một bộ dữ liệu, kể cả mật khẩu tài khoản.
  - Bản chính thức cần Firebase Authentication theo từng người (email hoặc số điện thoại).
  - Cần thêm Firestore Rules phân quyền theo vai trò, để học sinh chỉ đọc được dữ liệu của mình.
- **Không nhập** số CCCD, số tài khoản ngân hàng hay thông tin nhạy cảm vào bản này.
- Hạn mức miễn phí Firestore (gói Spark): 50.000 lượt đọc và 20.000 lượt ghi mỗi ngày, đủ cho trung tâm vài chục người dùng.
- Nên **tải bản sao lưu** hằng tuần (Cài đặt hệ thống → Tải bản sao lưu).
