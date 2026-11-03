# Bộ nhớ dự án: Tổng hợp báo cáo dân số – Trạm Y tế xã Lệ Thuỷ

Tài liệu ghi nhớ cho người phát triển (và trợ lý AI) khi tiếp tục làm trang. Đọc tệp này trước khi sửa mã.
Cập nhật lần cuối: 03/11/2026.

## 1. Mục đích và người dùng

- Trang web giúp **cán bộ tổng hợp dân số** của Trạm Y tế xã Lệ Thuỷ (UBND xã Lệ Thuỷ) nhập số liệu hằng tháng, tổng hợp và xuất báo cáo:
  - Biểu 01-DSX (tháng), 02-DSX (quý), 03-DSX (năm) theo Thông tư 01/2022/TT-BYT;
  - báo cáo lời bằng Word đúng thể thức **Nghị định 30/2020/NĐ-CP**;
  - sản phẩm tuyên truyền (banner, tài liệu tập huấn, bản trình bày).
- Tài khoản dùng chung qua Firebase: các email được phép nằm trong `firestore.rules`. Người trình bày tài liệu: Đỗ Yến Ngọc (người lập biểu).
- Giao diện, nội dung, thông báo: **tiếng Việt**.

## 2. Hai bản của trang

| Bản | Nơi chạy | Lưu dữ liệu | Claude (đọc ảnh, soạn nội dung) |
|---|---|---|---|
| GitHub Pages | https://baocaodanso.github.io (repo `Baocaodanso/baocaodanso.github.io`) | Firebase Firestore dự án `baocaodanso-21327` khi có `firebase-config.js`; nếu không có thì IndexedDB trên máy | Cần khóa API Anthropic (nhập trong trang; có thể dùng chung qua `cfg/apikey` trong Firestore) |
| Artifact claude.ai | Trang riêng tư của chủ tài khoản (artifact `M4JqADrroqkkrHtny73rbm`) | Cơ sở dữ liệu của artifact (`window.claude.use("db")`) | Có sẵn (`sample`) |

- Hai bản dùng **cùng mã ứng dụng**. Bản GitHub thêm một lớp *shim* ở đầu `index.html` để giả lập `window.claude.use(...)` (db, downloads, sample, user) và đăng nhập Firebase.
- Mỗi lần sửa phải sửa **cả hai** tệp nguồn (xem mục 8) rồi đưa lên cả hai nơi.

## 3. Cấu trúc tệp trong repo

- `index.html` – toàn bộ ứng dụng (HTML + CSS + JS, một tệp). Thư viện tải khi cần qua CDN: SheetJS 0.18.5, mammoth 1.6.0, docx 7.8.2, ExcelJS 4.4.0, PptxGenJS 3.12.0, Firebase compat 10.12.2.
- `firebase-config.js` – cấu hình web Firebase (công khai được; quyền do rules quyết định).
- `firestore.rules` – chỉ các email trong danh sách được đọc/ghi. Dán vào Firebase Console → Firestore → Rules.
- `du-lieu/2026.json` – **chỉ số liệu tổng hợp** năm 2026 (không có dữ liệu cá nhân), tự nạp khi kho trống.
- `docs/memory.md` – tệp này.

**Không bao giờ** đưa lên repo công khai: phiếu CTV (họ tên, CCCD), tệp sao lưu, khóa API.

## 4. Điều hướng (2 chức năng)

- **Tổng quan** (`home`).
- **BÁO CÁO**
  - **Phiếu thu thập** (`phieu`): chỉ để **nhập liệu**.
    - Thẻ *Phiếu tháng theo thôn* (`S.phMode="thon"`, mặc định): bảng 17 chỉ tiêu × 19 thôn, cột “Tổng trên biểu” để đối chiếu với phiếu giấy, khung *Rà soát*, nhập từ Excel/ảnh, tải phiếu trống, **Lưu phiếu**.
    - Thẻ *Phiếu CTV chi tiết (P0/CTV)* (`S.phMode="ctv"`): danh sách từng trường hợp.
  - **Số liệu báo cáo** (nhóm `hub`):
    - *Báo cáo tháng* (`baocaothang`): tổng hợp từ phiếu, chỉ xem; xuất Excel theo địa bàn/thôn; tạo/mở kỳ báo cáo Biểu 01 để xuất Word NĐ30.
    - *Kỳ báo cáo* (`reports`/`report`): kỳ tùy chỉnh, điền biểu, xuất Excel theo mẫu, báo cáo lời Word.
    - *Theo địa bàn* (`diaban`): 6 địa bàn, mốc thời gian tùy chỉnh.
    - *Đã gửi cấp trên* (`solieu`): Biểu 01 hằng tháng/lũy kế đã nhập từ tệp, **Đối chiếu**, sửa chỗ lệch.
  - **Biểu mẫu** (`templates`), **Thông tin cơ quan** (`settings`).
- **TUYÊN TRUYỀN** (`tuyentruyen`): banner PNG, tài liệu tập huấn .docx, bản trình bày .pptx; soạn bằng Claude hoặc “theo mẫu có sẵn” (thư viện 8 chủ đề, hoặc dựng theo cấu trúc văn bản tải lên).

## 5. Địa bàn và thôn (đã được người dùng xác nhận)

| Địa bàn | Thôn |
|---|---|
| An Thuỷ | Lộc Thượng, Lộc Hạ, Lộc An, Thạch Tân, Phú Thọ |
| Lộc Thuỷ | Tuy Lộc, Án Xá |
| Phong Thuỷ | Đại Phong, Thượng Phong |
| Kiến Giang | Thượng Giang, Phong Giang, Xuân Giang |
| Liên Thuỷ | Uẩn Áo, Quy Hậu, Đông Thành, Xuân Hồi |
| Xuân Thuỷ | Xuân Thuỷ, Xuân Thượng, Xuân Hạ |

- Thứ tự cột theo mẫu biểu Lệ Thuỷ (`BT_ORDER`): An Thuỷ → Lộc Thuỷ → Phong Thuỷ → Kiến Giang → Liên Thuỷ → Xuân Thuỷ.
- Danh sách lưu ở `cfg/settings.diaBan`. So khớp tên dùng `stripPre(noDia(...))` (bỏ dấu, bỏ “thôn/xóm…”).
- Lưu ý: An Thuỷ và Lộc Thuỷ đã bị hoán đổi ở bản đầu và được sửa ngày 27/10/2026 (`fixDiaBan`, cờ `settings.diaBanFix`).

## 6. Mô hình dữ liệu (Firestore / db artifact)

Collections: `templates`, `reports`, `phieu`, `solieu`, `tuyentruyen`, `cfg` (`cfg/settings`, `cfg/apikey`).

**`solieu/b01t-YYYY-MM`** – Biểu 01 tháng (tài liệu trung tâm):
- `toanXa{key}` – số toàn xã; **luôn = cộng `perDb`** (trừ chỉ tiêu tồn).
- `perDb{địa bàn:{key}}` – số theo địa bàn; khi lưu phiếu = cộng các thôn của địa bàn.
- `perThon{thôn:{key, _db}}` – số theo thôn từ phiếu (gồm cả khóa `x_*`).
- `tongGiay{địa bàn|_tx:{key}}` – số tổng ghi trên phiếu giấy (để đối chiếu).
- `them{nhãn:{thôn:v}}` – dòng ngoài mẫu đọc từ ảnh.
- `suaTay[]` – lịch sử sửa `{at, who, why, changes:[{k, db, old, new}]}`; `nguon[]`, `banCu[]` (bản trước khi thay/đồng bộ).

**`solieu/b01lk-YYYY-01-MM`** – lũy kế đã báo cáo T01–Tmm (chỉ `toanXa`). **`solieu/ct-YYYY`** – chỉ tiêu giao theo thôn.

**Khóa chỉ tiêu**
- `B01_KEYS`: `sinh_tong, sinh_nam, sinh_nu, sinh_con2, sinh_con3, sinh_me20, mangthai_dang, vong_moi, vong_thay, vong_thoi, tsnam, tsnu, cay_moi, cay_thay, cay_thoi`.
- Thêm của mẫu tháng Lệ Thuỷ (`MAU_THANG`): `x_mt_hienco` (mang thai hiện còn), `x_sangloc`, `x_tiem`, `x_bcs`, `x_tutt`, `x_chet`, `x_kethon` (cặp), `x_tuvan_kh`, `x_nct`, `x_tt`, `x_di`, `x_den`.
- `BT_XMAP` đưa vào kỳ báo cáo: `x_chet→chet`, `x_den→den`, `x_di→di`, `x_nct→nct`, `x_tuvan_kh→tuvan_kh` (hàng biểu 02/03 không có `auto` được ghép theo tên bằng `autoByName`).

## 7. Quy tắc nghiệp vụ đã chốt (đừng đổi khi chưa hỏi người dùng)

1. **Liên kết số liệu**: sửa thôn → cộng vào địa bàn → Toàn xã (`slSyncDoc`) → lũy kế nhận **phần chênh lệch** (`slPropagate`), không ghi đè; kỳ báo cáo đã “Dùng số đã báo cáo” được thay các ô còn giữ số cũ.
2. **Tổng số trẻ sinh = nam + nữ**, tự tính (đã kiểm 72 nhóm số liệu, luôn đúng).
3. **`mangthai_dang` là chỉ tiêu tồn (`STOCK_KEYS`)**: KHÔNG cộng dồn, KHÔNG đồng bộ lũy kế theo tháng, KHÔNG đối chiếu lũy kế–tháng. (Lỗi 27/10 đã ghi đè lũy kế T7–T9 = 36/35/40; đã khôi phục 114/116/134 bằng `fixStockSync`, cờ `settings.stockFix`.)
4. Theo mẫu mới: dòng “Số phụ nữ đang mang thai tháng mới” ghép vào `mangthai_dang`; “Số phụ nữ mang thai hiện còn” (số thai hẹn còn) lưu riêng `x_mt_hienco`.
5. **Không tự liên kết** “số người kết hôn” (biểu thôn ghi *cặp*, biểu 02/03 ghi *người*).
6. Đồng bộ toàn bộ (`slSyncFull`, cờ `settings.syncFull`) đã chạy: lũy kế = cộng dồn hằng tháng (trừ chỉ tiêu tồn); số cũ giữ trong `banCu`.
7. Nhập từ ảnh/dữ liệu một lần dùng `DATA_PATCHES` + cờ `settings.patches` (chạy một lần khi mở trang).
8. Thể thức NĐ30: Times New Roman, A4, lề 20/20/30/15 mm; quốc hiệu 13pt in đậm, tiêu ngữ 14pt có gạch dưới; ngày < 10 và tháng 1–2 thêm số 0; `lineRule: "auto"`.

## 8. Quy trình làm việc của người phát triển

- Nguồn đang sửa (trong thư mục làm việc của phiên): `tong-hop-dan-so.html` (bản artifact) và `gh/index.html` (bản GitHub = shim + cùng mã). Mỗi thay đổi áp **cùng một bản vá** cho cả hai (script `patch_*.py` với `assert s.count(a)==1`).
- Kiểm thử: Playwright + máy chủ http cục bộ phục vụ `site/` (bản sao `gh/index.html` + `du-lieu/2026.json`); giả lập Firebase (`fakefb.js`), Claude (route `api.anthropic.com`), ExcelJS (stub) khi không tải được CDN.
- Phát hành:
  - Artifact: Artifact publish `tong-hop-dan-so.html` với `url` của artifact (luôn truyền `url` để không tạo trang trùng).
  - GitHub: `git fetch && git reset --hard origin/main`, chép `gh/index.html` → `index.html`, commit (tác giả `bunbun170516-ai`), push lên `main`. Pages cập nhật sau 1–2 phút.
- Sao lưu trước mỗi thay đổi lớn: `backup_vNN.html`, `gh/index_v_*.html`.

## 9. Việc còn treo / cần người dùng xác nhận

- **Tháng 08/2026, An Thuỷ**: phiếu 5 thôn (dò từ ảnh) cho tổng sinh 5, vòng 17; số tổng đang lưu (MIS) là 8 và 1. Cột “Xuân” của MIS lại trùng ảnh. Chờ người dùng bấm *Sửa phiếu → Lưu phiếu* nếu số ảnh đúng.
- Ô BCS Lộc An tháng 8 khó đọc (28 hoặc 29; cột tổng giấy 136).
- Tháng 10 có 5 thôn trùng số tháng 8 (BCS Thạch Tân 23 vs 25) – có thể nhập nhầm tháng.
- Xuất Excel (ExcelJS) mới kiểm bố cục bằng stub; cần người dùng mở thử tệp thật.
- Đọc ảnh viết tay bằng Claude thật chưa được kiểm trên bản GitHub (cần khóa API).
- Có một artifact trùng tên “Tổng hợp Báo cáo Dân số” tạo nhầm (không có dữ liệu) – chờ người dùng đồng ý xóa.
