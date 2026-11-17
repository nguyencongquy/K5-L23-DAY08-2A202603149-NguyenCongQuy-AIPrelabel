# Báo cáo QC tự động (nháp)

> **Gợi ý** do script tính; bạn xác nhận. Script chỉ đếm theo phiếu kiểm bằng mắt người.

## Mẫu
- 24 ảnh (3 ca khó + 21 ngẫu nhiên), 488 đối tượng.
- taxonomy v1.0

## Số đo
| Chỉ số | Giá trị | Mẫu số |
|---|---|---|
| Lỗi **theo đối tượng** | 8/488 = 1.6% | 488 đối tượng trong mẫu |
| **Ảnh có lỗi** (dùng cho ngưỡng) | 8/24 = 33.3% | 24 ảnh trong mẫu |
| Máy: accepted / edited / deleted | 439/508 = 86.4% / 13/508 = 2.6% / 56/508 = 11.0% | 508 box máy |
| Máy: added | 23/475 = 4.8% | 475 đối tượng sau duyệt |
| Bắt lỗi bẫy | 5/12 = 41.7% | 12 bẫy |

## Top 3 loại lỗi
| Loại lỗi | Số lỗi | Ảnh ví dụ |
|---|---|---|
| sai_vi_tri_kich_thuoc | 4 | img_008.jpg, img_021.jpg, img_025.jpg |
| bo_sot | 2 | img_017.jpg, img_023.jpg |
| sai_lop | 2 | img_018.jpg, img_022.jpg |

**Lỗi hệ thống**: 'sai_vi_tri_kich_thuoc' lặp lại -> sửa quy tắc / recipe, không chỉ sửa từng ảnh.

## Giới hạn thống kê
- 8 ảnh lỗi trong 24 ảnh mẫu: cận trên 95% (Wilson) của tỉ lệ ảnh lỗi thật là **53%**. Ngưỡng 2% không thể được **chứng minh** bằng mẫu nhỏ như vậy: hãy nói rõ giới hạn này trong báo cáo.

## Gợi ý quyết định
**HOLD**

- guideline chưa rõ (1 ca) / dữ liệu có vấn đề (0 ảnh) -> giữ lại làm rõ

Cây quyết định: Hold (guideline chưa rõ / dữ liệu hỏng) -> Rework (> ngưỡng, lỗi lặp, bắt bẫy / nhất quán thấp) -> Release.
