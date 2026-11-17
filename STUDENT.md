# Thông tin học viên

- Họ và tên: Nguyễn Công Quý
- MSHV: 2A202603149
- Lớp: L2
- Tài khoản GitHub: nguyencongquy
- Repo bài nộp: https://github.com/nguyencongquy/K5-L23-DAY08-2A202603149-NguyenCongQuy-AIPrelabel
- Commit chốt nội dung: xem commit sản phẩm được ghi khi hoàn tất handoff; bản hiện hành trên main.
- Máy: macOS Apple Silicon, Apple MPS, Python 3.11.15
- CVAT Docker: 2.74.1

## Kết quả tóm tắt

- Máy: accepted 439/508 = 86.4%; edited 13/508 = 2.6%; deleted 56/508 = 11.0%; added 23/475 = 4.8%.
- So reference có hỗ trợ: precision 47/70 = 67.1%; recall 47/63 = 74.6%. Không phải Gold gán tay độc lập.
- Bắt bẫy mù: 5/12 = 41.7%; giữ nguyên sau rework đã biết đáp án.
- Thời gian hỗ trợ: reference 1.6250 phút/4 ảnh; batch 8.8057 phút/24 ảnh. Không có baseline gán tay/duyệt của người học để tính tiết kiệm.
- QC đầu: 8/488 đối tượng = 1.6%, 8/24 ảnh = 33.3%. Sau sửa: 0/490 và 0/24 lỗi rõ mới được ghi nhận; cận trên Wilson 95% 14%, review chưa độc lập.
- Nhãn sau sửa: 24 ảnh, 493 annotation = 490 vật + 3 ignore_region; còn 9 box can_review trên 7 ảnh.
- **Quyết định: HOLD.** Còn nhóm ca phạm vi lớp chưa chốt, điểm bắt bẫy mù dưới ngưỡng và không có Gold gán tay độc lập. Người nộp cần kiểm và xác nhận trước khi nộp.
