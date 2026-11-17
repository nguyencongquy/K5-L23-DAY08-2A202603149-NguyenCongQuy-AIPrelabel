# Label contract Phố châu Á bổ sung v1.1 — 2A202603149 — Nguyễn Công Quý

Bản bổ sung cho v1.0. Giữ nguyên tám lớp và toàn bộ `rules` trong taxonomy. Đây là đề xuất calibration từ kết quả thực nghiệm có hỗ trợ; người nộp chưa xác nhận. Không coi là biểu quyết nhóm hay xác nhận của coach.

## 1. Câu hỏi mở và quyết định

| Câu hỏi v1.0 | Quyết định bổ sung | Ca và ảnh đối chiếu |
|---|---|---|
| 1 — xe điện | Chưa có ảnh xe điện được xác nhận trong các ca đã rà. Không tự suy điện/đạp từ kích thước. Chỉ chọn motorbike khi có dấu hiệu động cơ; không đủ căn cứ giữ `can_review`. Quy tắc này chưa thay thế xác nhận của coach cho xe đạp trợ lực. | CAL-06; img_009.jpg minh họa xe có động cơ thông thường, không được gọi là bằng chứng xe điện. |
| 2 — xe đỗ sát | C3: từng xe nhìn rõ, cao ≥20 px phải có box. Chỉ ignore phần không tách cá thể hoặc cụm ≥5 vật quá nhỏ; ghi vùng và lý do. | CAL-03; img_026.jpg, img_018.jpg, img_024.jpg. |
| 3 — hàng nhô | C2: khung, mái, thùng và giỏ gắn cố định thuộc xe. Hàng rời nhô >20% chiều dài xe bị loại; box vẫn phải đủ các bánh/tay lái nhìn thấy, không chỉ bánh trước. Không dùng hàng rời để gọi xe hai bánh thành ba bánh. | CAL-02; img_009.jpg, img_031.jpg, img_008.jpg. |
| 4 — người trong xe | Giữ ngưỡng phần nhìn thấy ≥20% của v1.0. Box theo phần nhìn thấy, riêng với xe; không suy chân sau mui. Gần ngưỡng và chưa chắc quy tắc thì giữ `can_review`, ghi ca cần xác nhận. | CAL-04; img_013.jpg; chưa tuyên bố đã đo chính xác phần trăm che bằng mắt. |
| 5 — kính | C4: phần kính nhìn thấy thuộc xe. Khi tách cả người và xe, pixel người nhìn thấy qua cửa hoặc kính phải được tách khỏi mask xe; không tô chung người vào mask xe. Lỗ lớn >5% bỏ ra; không cố vẽ các nan nhỏ hơn phạm vi v1.0. | CAL-04; img_013.jpg là xe hở, minh họa tách người; chưa có kiểm chứng riêng mask kính kín. |
| 6 — box chồng | C1: chỉ gộp box cùng vật. Người và xe được chồng box theo v1.0. Loại box trùng mái/thùng, không gộp hai xe chạm nhau. | CAL-01; img_003.jpg, img_010.jpg. |
| 7 — confidence | C5: không có ngưỡng conf tự động cho phép bỏ review. 100% ảnh phải được quét trước khi xem box; rà bốn góc và biên từng vật. Confidence chỉ dùng ưu tiên kiểm. | CAL-05; img_021.jpg, img_029.jpg; lượt đầu vẫn lọt bẫy lỏng. |

## 2. Ca khó mới và lỗi thực thi

- **C1 / CAL-01:** mái và khung của cùng tuk-tuk/xích lô nằm trong một box `three_wheeler`. Đây chủ yếu là sửa lỗi máy theo phạm vi lớp đã có, không phải thêm lớp. [img_003](../../data/images/img_003.jpg), [img_010](../../data/images/img_010.jpg), [img_013](../../data/images/img_013.jpg).
- **C2 / CAL-02:** kiểm cả bánh sau và khung khi xe chở hàng. [img_031](../../data/images/img_031.jpg) cho thấy box chỉ bánh trước là thiếu; [img_009](../../data/images/img_009.jpg) là motorbike chở chuối.
- **C3 / CAL-03:** không dùng ignore để che vật rõ nhưng khó vẽ. [img_026](../../data/images/img_026.jpg) có xe gần nhìn được riêng; [img_024](../../data/images/img_024.jpg) có cụm vệt chuyển động không tách được.
- **C4 / CAL-04:** mask SAM là kết quả thử cần review, không phải chân lý. [Mask img_003](../masks/cargo_img003.jpg) còn dính trẻ nhỏ; [mask img_013](../masks/tuktuk_img013.jpg) còn dính một phần áo người lái; [mask img_009](../masks/motorbike_img009.jpg) còn túi hàng và mất một phần tay lái.
- **C5 / CAL-05:** lượt sửa sau khi đã biết đáp án không được thay số đo mù ban đầu. Giữ `batch_reviewed.json`, ghi sửa ở `qc_fixed.json`; ảnh [img_017](../../data/images/img_017.jpg), [img_023](../../data/images/img_023.jpg).
- **C6 / CAL-06:** ca class xa ở img_003/img_022/img_034 và cách áp dụng một số ca mở còn cần người nộp/coach kiểm. Chưa đủ điều kiện Release. `can_review` không được đóng chỉ để làm đẹp số liệu.

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Nguồn |
|---|---|---|
| v1.0 | Bản giảng viên; không sửa. | guideline/guideline_v1.0_label_contract.md |
| v1.1 | Bổ sung C1–C6, tách lỗi thực thi và câu hỏi guideline, ghi ca chưa chốt. Giữ nguyên lớp và ngưỡng QC. | CAL-01–CAL-06; thực nghiệm và review hiện tại. |
