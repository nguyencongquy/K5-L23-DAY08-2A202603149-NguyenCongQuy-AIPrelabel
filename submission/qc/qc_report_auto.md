# Báo cáo QC tự động (nháp)

> **Gợi ý** do script tính; bạn xác nhận. Script chỉ đếm theo phiếu kiểm bằng mắt người.

## Mẫu
- 24 ảnh (3 ca khó + 21 ngẫu nhiên), 490 đối tượng.
- taxonomy v1.1

## Số đo
| Chỉ số | Giá trị | Mẫu số |
|---|---|---|
| Lỗi **theo đối tượng** | 0/490 = 0.0% | 490 đối tượng trong mẫu |
| **Ảnh có lỗi** (dùng cho ngưỡng) | 0/24 = 0.0% | 24 ảnh trong mẫu |
| Máy: accepted / edited / deleted | 439/508 = 86.4% / 13/508 = 2.6% / 56/508 = 11.0% | 508 box máy |
| Máy: added | 23/475 = 4.8% | 475 đối tượng sau duyệt |
| Bắt lỗi bẫy | 5/12 = 41.7% | 12 bẫy |

## Top 3 loại lỗi
(không ghi nhận lỗi nào)

## Giới hạn thống kê
- 0 ảnh lỗi trong 24 ảnh mẫu: cận trên 95% (Wilson) của tỉ lệ ảnh lỗi thật là **14%**. Ngưỡng 2% không thể được **chứng minh** bằng mẫu nhỏ như vậy: hãy nói rõ giới hạn này trong báo cáo.

## Gợi ý quyết định
**HOLD**

- guideline chưa rõ (1 ca) / dữ liệu có vấn đề (0 ảnh) -> giữ lại làm rõ

Cây quyết định: Hold (guideline chưa rõ / dữ liệu hỏng) -> Rework (> ngưỡng, lỗi lặp, bắt bẫy / nhất quán thấp) -> Release.
