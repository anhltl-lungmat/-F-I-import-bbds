# Handoff: Quản lý đối soát – tab Đối soát dịch vụ & Import dữ liệu

## Tổng quan
Màn **Quản lý đối soát** (menu ĐỐI SOÁT - QUYẾT TOÁN) của portal *Quản Trị Bán Lẻ* có 2 tab:
1. **ĐỐI SOÁT DỊCH VỤ** – danh sách kỳ đối soát; thêm cột **Ghi chú** và popup **Chỉnh sửa số tiền quyết toán** có ô Ghi chú.
2. **IMPORT DỮ LIỆU** (mới) – import file giao dịch theo đối tác, kiểm tra lỗi, xem lịch sử import.

Đối tác: **Finviet** (`FINVIET`), **Imedia** (`IMEDIA`).

## Về file thiết kế
`prototype/index.html` là **bản mẫu HTML tham chiếu** (giao diện + hành vi), không phải code production. Hãy dựng lại trong codebase hiện có của portal (framework, component, API sẵn có). Mở trực tiếp `prototype/index.html` trên trình duyệt để bấm thử (cần `support.js` cùng thư mục, có mạng để tải font/icon).

Dữ liệu trong prototype là giả lập (hard-code). Logic kiểm tra file thật phải làm ở backend.

## Độ trung thực
**High-fidelity** – bám theo giao diện portal hiện tại (sidebar tối, dải tab xanh, panel lọc xám, nút vuông chữ in hoa, Font Awesome 4.7). Dùng lại component sẵn có của portal; giá trị bên dưới để đối chiếu.

---

## Tab 1 – ĐỐI SOÁT DỊCH VỤ

### Bộ lọc (giữ nguyên hiện tại)
Mã đối soát · Mã đối tác (Finviet / Imedia) · Trạng thái · Từ ngày · Đến ngày · nút EXPORT (xanh lá) · TÌM KIẾM (xanh dương).

### Bảng – thứ tự cột
STT · File · Mã đối soát · Mã đối tác · Tổng tiền · Tổng phí · Tổng hoàn · Tổng phí hoàn · Tổng tiền thanh toán · Luồng tiền · Thời gian bắt đầu · Thời gian kết thúc · Trạng thái · Thời gian duyệt · Người duyệt · Ngày tạo · Ngày cập nhật · **Hành động** · **Ghi chú** (mới)

- Cột tiền căn phải, định dạng `vi-VN` (1.254.000).
- Bảng cuộn ngang; **không ghim** cột Hành động / Ghi chú.
- **Hành động**: 3 nút icon 34×30px – Chỉnh sửa (`#2a7fc9`, `fa-pencil-square-o`), Duyệt (`#4c9a1f`, `fa-check`), Từ chối (`#c9161d`, `fa-times`).
- **Ghi chú**: rộng cố định 220px, xuống dòng (`white-space: pre-wrap; word-break: break-word`). Chưa có ghi chú → hiển thị `—` màu `#aaa`.

### Popup "Chỉnh sửa số tiền quyết toán"
Mở khi bấm nút Chỉnh sửa. Rộng 640px, nền overlay `rgba(0,0,0,.5)`, tiêu đề căn giữa 20px.
| Trường | Kiểu | Ghi chú |
|---|---|---|
| Tổng tiền | input | prefill giá trị dòng |
| Tổng tiền hoàn | input | prefill |
| Số tiền quyết toán | readonly (nền `#eee`) | tự tính = Tổng tiền − Tổng phí − Tổng hoàn + Tổng phí hoàn |
| **Ghi chú** (mới) | textarea 3 dòng, `maxlength=500`, đếm ký tự `n/500` | prefill ghi chú hiện có |

Nút: **Đóng** (xám `#eee`) · **LƯU** (`#45a9ec`).
Bấm LƯU → cập nhật Tổng tiền, Tổng hoàn, Tổng tiền thanh toán và **Ghi chú** (trim) của dòng đó; đóng popup. Bấm overlay hoặc Đóng → huỷ.

---

## Tab 2 – IMPORT DỮ LIỆU

### Phần 1: Import dữ liệu
- **Mã đối tác \*** (bắt buộc): select `Chọn đối tác` / Finviet / Imedia.
- Nút **IMPORT** (`#45a9ec`, icon `fa-upload`) và **TẢI FILE MẪU** (`#5cb82f`, icon `fa-download`).
  - Chưa chọn đối tác → 2 nút mờ (opacity .5, `cursor:not-allowed`), dòng gợi ý "Chọn mã đối tác để import hoặc tải file mẫu".
  - Đã chọn → gợi ý "Import và file mẫu theo đối tác {Tên} · .xlsx, .xls, .csv · tối đa 10 MB".
- **TẢI FILE MẪU**: tải file mẫu **theo đối tác** đã chọn (`file_mau_import_{finviet|imedia}.xlsx`).
- **IMPORT**: mở hộp chọn file (`accept=".xlsx,.xls,.csv"`). Chọn file xong là gửi import ngay (không có bước kiểm tra riêng).
  - Sai định dạng → "Chỉ nhận file .xlsx, .xls, .csv" (đỏ, dưới nút).
  - > 10 MB → "File vượt quá 10 MB".

**Kết quả import**
- **Không lỗi** → banner xanh (`#dff0d8`, viền `#c3e2b5`, chữ `#3c763d`, có nút ×): "Import thành công {N} bản ghi từ file {tên file}." và thêm 1 dòng vào Lịch sử import.
- **Có lỗi** → **không import bản ghi nào**, mở popup **"Import không thành công"** (rộng 860px):
  - Hộp đỏ (`#f2dede` / `#a94442`): "File **{tên}** có **{x}** / {tổng} bản ghi lỗi. Vui lòng sửa các dòng dưới đây và import lại. Số dòng tính theo file Excel (dòng 1 là tiêu đề)."
  - Bảng chỉ 2 cột: **Dòng** (số dòng trong file Excel = thứ tự bản ghi + 1) · **Mô tả lỗi** (chữ `#c9302c`, nhiều lỗi nối bằng `; `). Không hiển thị dữ liệu bản ghi. Cao tối đa 360px, header sticky.
  - Nút: **Đóng** · **XUẤT FILE LỖI** (xanh lá) – xuất Excel các dòng lỗi kèm cột Kết quả & Mô tả lỗi.

**Quy tắc kiểm tra (đề xuất – cần khớp với file mẫu thật)**
| Trường | Quy tắc | Thông báo |
|---|---|---|
| Mã đối tác | bắt buộc | Thiếu mã đối tác |
| Mã đối tác | phải = đối tác đã chọn | Mã đối tác khác với đối tác đã chọn |
| Ngày giao dịch | `dd-mm-yyyy`, ngày hợp lệ | Sai định dạng ngày (dd-mm-yyyy) |
| Số tiền | > 0 | Số tiền phải lớn hơn 0 |
| Trạng thái | ∈ {Thành công, Hoàn tiền, Thất bại} | Trạng thái không hợp lệ |
| Mã giao dịch | không trùng trong file | Trùng mã giao dịch với dòng {n} |

### Phần 2: Lịch sử import
- Bộ lọc: **Mã đối tác** (`Tất cả đối tác` / Finviet / Imedia) · **Từ ngày** · **Đến ngày** (`dd-mm-yyyy`, có icon lịch) · nút **TÌM KIẾM**. Lọc chỉ áp dụng khi bấm TÌM KIẾM; lọc theo ngày của *Thời gian import*.
- Dòng đếm: "Hiển thị **{n}** bản ghi." (in nghiêng 13px).
- Bảng: **STT · Thời gian · Tên file · Mã đối tác · Kỳ đối soát · Người import**. Sắp xếp mới nhất trước.
  - Thời gian: `dd-mm-yyyy HH:mm`. Kỳ đối soát: ngày giao dịch sớm nhất → muộn nhất trong file.
  - Rỗng → 1 dòng "Không có bản ghi phù hợp với điều kiện tìm kiếm." (xám, nghiêng, căn giữa).

---

## State (gợi ý)
- Tab: `tab: 'list' | 'import'`
- Đối soát: `rows[]`, `editing: { id, total, refund, note } | null`
- Import: `partner`, `submitted` (hiện lỗi "Vui lòng chọn đối tác" + viền đỏ select), `fileErr`, `successMsg`, `errorResult: { fileName, total, errors: [{ line, message }] } | null`
- Lịch sử: `filter draft { partner, from, to }`, `appliedFilter`, `history[]`

## API gợi ý
- `GET /reconciliations?…` – danh sách đối soát
- `PUT /reconciliations/{id}` – `{ totalAmount, totalRefund, note }`
- `GET /imports/template?partner=FINVIET` – file mẫu theo đối tác
- `POST /imports` (multipart: `partner`, `file`) → `200 { imported: N }` hoặc `422 { total, errors: [{ line, message }] }`
- `GET /imports/{id}/errors.xlsx` – file lỗi (cho nút XUẤT FILE LỖI)
- `GET /imports?partner=&from=&to=` – lịch sử

## Design tokens
| Vai trò | Giá trị |
|---|---|
| Font | Roboto 400/500/700, 14px body; tiêu đề khối 22px weight 300 |
| Nút chính / tab bar | `#45a9ec` / `#42a5f0` |
| Nút xanh lá (Export, Tải file mẫu) | `#5cb82f` |
| Link / text nhấn | `#2a8bd8` (hover `#1a6fb3`) |
| Đỏ bắt buộc / lỗi | `#d9230f`, chữ lỗi bảng `#c9302c` |
| Panel lọc | nền `#f6f6f6`, viền `#ddd` |
| Header bảng | nền `#f5f5f5`, chữ 12px, viền ô `#ddd`; dòng chẵn `#f9f9f9` |
| Input / select | cao 32px (popup 36px), viền `#ccc`, radius 0 |
| Nút | cao 31px (popup 34px), padding 0 12px, chữ in hoa 500, radius 0 |
| Sidebar | `#505b66`, mục active `#3d4650`, tiêu đề nhóm `#3fa9f5` |
| Modal | overlay `rgba(0,0,0,.5)`, shadow `0 5px 15px rgba(0,0,0,.5)` |

## Assets
- Font Awesome 4.7 (CDN) – icon: `fa-upload`, `fa-download`, `fa-history`, `fa-calendar`, `fa-pencil-square-o`, `fa-check`, `fa-times`, `fa-check-circle`, `fa-exclamation-triangle`, `fa-file-excel-o`, `fa-table`.
- Google Fonts Roboto.
- Không có ảnh.

## Files
- `prototype/index.html` – prototype đầy đủ (template + logic ở cuối file trong `<script data-dc-script>`).
- `prototype/support.js` – runtime để mở prototype trên trình duyệt.

> Lưu ý: logic trong prototype còn một số biến thừa từ các vòng chỉnh trước (kéo thả file, ô tổng hợp, nút import dòng hợp lệ, Luồng tiền ở tab import). Không có trên giao diện, bỏ qua khi triển khai.
