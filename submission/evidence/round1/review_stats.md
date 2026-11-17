# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 508; đối tượng sau duyệt: 475; ngưỡng accepted: IoU >= 0.9
- Đã **loại 27 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 439 | 439/508 = 86.4% | 508 box máy |
| edited (sửa) | 13 | 13/508 = 2.6% | 508 box máy |
| deleted (xoá) | 56 | 56/508 = 11.0% | 508 box máy |
| added (vẽ thêm) | 23 | 23/475 = 4.8% | 475 đối tượng sau duyệt |

Trong số deleted + added, **7 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 16.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 254 | 3 | 11 | 1 |
| bicycle | 5 | 0 | 3 | 1 |
| motorbike | 116 | 1 | 8 | 13 |
| car | 48 | 0 | 7 | 3 |
| bus | 3 | 0 | 0 | 3 |
| truck | 1 | 0 | 5 | 0 |
| three_wheeler | 12 | 9 | 22 | 2 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 63 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 47 | 23 | 16 | 67.1% (KTC 95%: 56–77%) | 74.6% (KTC 95%: 63–84%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 5/12 = 41.7% (KTC 95%: 19–68%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - missing: 1/3
  - wrong_class: 1/3
  - loose: 0/3
  - extra: 3/3
  - lọt: img_022.jpg (wrong_class), img_018.jpg (wrong_class), img_025.jpg (loose), img_017.jpg (missing), img_023.jpg (missing), img_029.jpg (loose), img_021.jpg (loose)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
