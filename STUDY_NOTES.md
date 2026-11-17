# Day08 — Ghi nhớ công cụ và quy trình

| Công cụ | Đầu vào | Đầu ra | Dùng trong lab | Giới hạn cần kiểm |
|---|---|---|---|---|
| YOLO26 | Ảnh và ngưỡng | Box, lớp trong danh mục cố định, confidence | Pre-label người/xe phổ biến | Có thể gọi tuk-tuk là car/truck; đổi ngưỡng không tạo lớp mới |
| YOLOE | Ảnh, cụm từ/prompt và ngưỡng | Box và có thể mask theo từ mô tả | Tìm three_wheeler bằng tuk-tuk/cyclo/cargo tricycle | Prompt không bảo đảm đúng; box lỏng/thừa/sót vẫn phải review |
| SAM | Ảnh cùng điểm dương/âm hoặc box | Mask phần vật được chỉ định | Tách biên ba vật khó; box detector → mask | Không tự xác nhận đúng class; có thể dính người, hàng, nền hoặc bóng |
| CVAT | Ảnh và nhãn đã nhập | Nhãn đã chỉnh, export COCO | Quét ảnh khi ẩn nhãn rồi sửa | Accept không có nghĩa đã kiểm bỏ sót |

SAM 1/2.1/3 là lựa chọn phiên bản; không phải ba model đều bắt buộc. Bài chính bắt buộc YOLO26, YOLOE và một đường SAM, ít nhất bốn lần chạy có nhận xét thật.

## Đọc số đo

- Precision: trong box máy dự đoán có bao nhiêu đúng. Recall: trong vật chuẩn có bao nhiêu được máy tìm thấy. Luôn ghi mẫu số.
- Accepted/edited/deleted chia theo box máy; added chia theo đối tượng sau review. Accepted cao không chứng minh recall cao.
- Bắt bẫy: bẫy sửa đúng/12; mục tiêu ít nhất10/12. Không mở đáp án trước export bản review.
- Conf dùng để lọc/xếp thứ tự, không thay việc nhìn ảnh; điểm giữa các model không so trực tiếp.
- Gold chỉ bốn ảnh; chọn và đánh giá ngưỡng trên cùng gold tạo kết quả lạc quan. Lớp dưới năm vật không chọn ngưỡng riêng.
- QC mẫu nhỏ có0 lỗi vẫn không chứng minh tỷ lệ lỗi thật dưới2%; phải nêu giới hạn thống kê.

## Thứ tự phải giữ

Gold gán tay trước khi thấy nhãn máy → thử model trên pilot → chọn ngưỡng → pre-label và bẫy → review → SAM → calibration/QC/báo cáo. Ghi thời gian thật tại lúc thực hiện, giữ output đo do script sinh, không sửa rules để qua checker.

Nguồn: GUIDE.md, RULES.md, RUBRIC.md, docs/MODELS.md, docs/CALIBRATION_QC.md của repo đề. Đây là bản ghi nhớ yêu cầu, chưa phải kết luận từ kết quả thực nghiệm cá nhân.
