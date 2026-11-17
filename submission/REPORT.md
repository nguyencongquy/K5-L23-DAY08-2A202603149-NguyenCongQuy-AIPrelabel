# Báo cáo Day 08 — 2A202603149 — Nguyễn Công Quý

Bộ thực nghiệm gồm 36 ảnh: 4 reference, 8 pilot và 24 batch. Đã chạy model, duyệt lô, thử mask, calibration và hai lượt QC. **Quyết định bàn giao: HOLD.** Đủ file kỹ thuật không đồng nghĩa nhãn đạt chất lượng hoặc đáp ứng yêu cầu Gold độc lập. Bản này dùng quy trình hỗ trợ; người nộp chưa xác nhận kết luận.

## 1. Model và recipe

| Lớp | Model | Ngưỡng | Căn cứ |
|---|---|---:|---|
| person | YOLO26m | 0.30 | r01/r02/r05; recall tối đa 67% trên reference, không đạt 90%; giữ mặc định và cần thêm lượt tìm sót. |
| bicycle | YOLO26m | 0.30 | 6 vật reference; recall tối đa 50%, không ép một ngưỡng mới. |
| motorbike | YOLO26m | 0.35 | Sweep đạt khoảng 91% precision và recall trên 11 vật reference. |
| car | YOLO26m | 0.30 | Recall tối đa 88%, chưa đạt mục tiêu 90%. |
| bus / truck | YOLO26m | 0.30 | Reference có 1 bus, 0 truck: không đủ ≥5 vật để chọn ngưỡng riêng. |
| three_wheeler | YOLOE-26s-seg | 0.15 | r03/r06; prompt `tuk-tuk`, `cyclo`, `cargo tricycle`; sweep recall 100%, precision 78% trên 7 vật. |

Lệnh gộp thực tế:

```bash
python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "motorbike=0.35,three_wheeler=0.15" --conf 0.3 --iou 0.6 --split batch,gold --out work/prelabel/merged_review/pre.coco.json
```

Sáu lượt thật trong `model_log.csv`, kèm bản `run.json` ở `evidence/model_runs/`. r01/r02 chạy cùng 8 ảnh pilot: tăng conf 0.25 lên 0.50 làm số box giảm **215 xuống 139**. Riêng img_007 giảm 26 xuống 11, có cả người/xe xa thật bị mất; ít box hơn không tự là tốt hơn. r03 giữ tuk-tuk img_002 với score 0.93 và xích lô img_032 với score 0.92, nhưng tạo gợi ý ba bánh sai ở mép xe lớn img_011 (0.28). r04 tạo 16 mask/2 ảnh từ box YOLO26. r05/r06 chạy 36 ảnh ở conf 0.05, lần lượt 1669 và 125 box; đây là nguồn sweep, chưa phải nhãn cuối.

**SAM 2.1 b** được chọn vì trọng số đã sẵn cục bộ, chạy được trên Apple MPS, không cần quyền model gated. Chỉ đo phiên bản này, không suy nó tốt hơn SAM 1 hoặc SAM 3. SAM tinh biên theo prompt; không sửa class car thành three_wheeler. Ví dụ img_004 bị detector gọi tuk-tuk là car thì mask vẫn mang lỗi ngữ nghĩa đó. Cần đổi nguồn detector/prompt và review, không chỉ hạ confidence.

Lớp có ít vật không được kết luận mạnh. Chọn và chấm ngưỡng trên cùng 4 ảnh gây lạc quan; reference có hỗ trợ còn làm kết luận yếu thêm. Những ngưỡng trên là recipe thực nghiệm, không phải ngưỡng đã xác nhận triển khai.

## 2. Error profile của pre-label

Nguồn: `qc/review_stats.json` và `.md`, đo trên **bản duyệt đầu được khóa trước khi mở đáp án**, không phải bản rework.

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---:|---:|---|
| accepted | 439 | 86.4% | 508 box máy |
| edited | 13 | 2.6% | 508 box máy |
| deleted | 56 | 11.0% | 508 box máy |
| added | 23 | 4.8% | 475 đối tượng sau duyệt |

Script loại **27 đối tượng quanh vùng bẫy** khỏi error profile để lỗi nhân tạo không bị tính thành lỗi tự nhiên của model. Vì vậy không dùng tổng 522 box import làm mẫu số 508; không dùng 491 annotation vòng đầu làm mẫu số 475. Trong deleted/added có **7 ca đổi lớp**, được tính xóa + thêm; số bỏ sót thật ước tính 23 − 7 = **16**, không gọi cả 23 là bỏ sót.

Theo lớp, three_wheeler có 12 accepted, 9 edited, 22 deleted, 2 added: nhiều box mái/thùng trùng hoặc nhận nhầm vật như đòn gánh. img_014 có ba box ba bánh trên đồ mang của người đi bộ. img_009 là xe hai bánh chở chuối, không phải ba bánh. Motorbike có 13 added, cho thấy bỏ sót còn đáng chú ý.

So reference: **TP 47, FP 23, FN 16**, precision **47/70 = 67.1%**, recall **47/63 = 74.6%**. 63 là số vật reference sau loại 2 ignore_region, không phải 65 annotation. Không gọi đây là đo trên Gold gán tay độc lập. Khoảng tin cậy rộng và sai khác reference có thể làm thay đổi kết quả.

## 3. Thiên kiến tự động hóa và ảnh bẫy

Lượt đầu sửa **5/12 = 41.7%**, dưới yêu cầu ≥10/12: missing **1/3**, wrong_class **1/3**, loose **0/3**, extra **3/3**. Kết quả và hash bản khóa nằm ở `evidence/round1/` và `evidence/review_freeze.json`. `traps_key.json` chỉ được đọc sau khi tạo `batch_reviewed.json`.

Các ca lọt: person mép ảnh img_017/img_023; wrong_class img_022/img_018; khung motorbike lỏng ở img_025/img_029/img_021. Lỗi extra rõ trên tán cây/tường dễ thấy, còn khung lệch ít pixel và vật nhỏ bị bỏ sót khi nhãn có sẵn trông hợp lý. Accepted 86.4% nhưng bắt bẫy 41.7% cho thấy không được suy chất lượng review từ accepted.

Đã sửa lại các ca này sau khi biết đáp án, lưu ở `labels/qc_fixed.json`, kèm `evidence/qc_rework_changes.json`. **Không thay bản duyệt đầu hoặc đổi 5/12 thành điểm của lượt sửa đã biết đáp án.** Review do quy trình hỗ trợ thực hiện, nên số đo này không đại diện trực tiếp cho thiên kiến của người học.

## 4. Máy có đáng dùng không

`time_log.csv` ghi thời gian thực của quy trình: tạo reference **1.6250 phút/4 ảnh**, sửa batch lượt đầu **8.8057 phút/24 ảnh** sau quét ảnh ban đầu. Các tên step theo schema; đây **không phải** thời gian người học gán tay/duyệt CVAT. Batch time chưa gồm rework, QC và báo cáo; timer có trong `evidence/environment.json`. Không tính phần trăm tăng tốc của con người từ hai dòng này.

Bằng chứng hiện có chỉ ủng hộ việc dùng detector làm gợi ý cho vật phổ biến. Chưa đủ chứng minh recipe tiết kiệm công người: precision 67.1%, recall 74.6% trên reference có hỗ trợ; review mù còn lọt 7/12 bẫy. Three_wheeler cần nguồn open-vocabulary và rà riêng. Kết luận này là bản phân tích để người nộp kiểm, không phải trải nghiệm gán tay được xác nhận.

Ba quy tắc vận hành đề xuất:

1. Quét ảnh không nhãn trước, chia bốn góc, ghi checklist **24/24 ảnh**; sau đó kiểm người ngồi trên xe và mép ảnh riêng. Không chỉ đi theo box sẵn có.
2. Kiểm đủ bốn cạnh của **mọi box**, kể cả conf ≥0.9; dừng ở từng motorbike nhỏ và vùng chồng nhau. Bẫy loose phải được đo trên một lượt mù mới, không trên lượt đã biết đáp án.
3. Sau mỗi 10 ảnh cần nghỉ và lưu checkpoint; khi gán tay thật phải bấm giờ riêng cho gán mới, review và nghỉ. Phần thực nghiệm hỗ trợ hiện tại không dùng để xác nhận quy tắc nghỉ của con người đã được thực hiện.

## 5. Mask SAM cho ba vật

Nguồn: `masks/PROMPTS.csv`, ảnh kết quả trong `masks/`; box prompt không tính là click.

| Ảnh | Phiên bản | Click + / − | Quan sát và lỗi | File |
|---|---|---|---|---|
| img_013 | SAM 2.1 b | 2 / 2 | Chọn được mái/thân tuk-tuk, còn dính phần áo người lái và nền gần bánh; 287169 pixel, 1 polygon. | tuktuk_img013.jpg |
| img_003 | SAM 2.1 b | 2 / 2 | Có thùng/mái/bánh nhưng còn dính trẻ nhỏ phía trước dù đã click âm; 88974 pixel, 2 polygon. | cargo_img003.jpg |
| img_009 | SAM 2.1 b | 2 / 2 | Bỏ phần lớn chuối nhô phải, còn túi hàng và thiếu phần tay lái vì click âm sát gương; 28921 pixel, 2 polygon. | motorbike_img009.jpg |

Cả ba là **kết quả thử SAM**, chưa phải mask chuẩn đã qua người kiểm. Theo v1.0 mục 4.2, phải tách người/hàng rời, bỏ bóng và lỗ lớn; mask biên đẹp không chứng minh đúng phạm vi. Muốn dùng làm ground truth phải sửa phần dính và kiểm ở ảnh gốc, không tự gọi output này đạt sai số 3 px. Không thử Interactor SAM trong CVAT; không có kết luận so sánh offline/Interactor.

## 6. Calibration

Có **6 quyết định CAL-01–CAL-06**, gồm `loi_may`, `cau_hoi_mo`, `bay_lot`, `can_review`. Bản bổ sung `guidelines/guideline_v1.1_calibrated.md` chỉ ghi thay đổi, giữ nguyên tám lớp và `taxonomy.rules`.

- CAL-01 phân biệt lỗi máy với lỗ hổng guideline: mái/thùng của cùng tuk-tuk vốn đã thuộc một xe; không thêm lớp để chữa detector.
- CAL-02 làm rõ thùng/giỏ cố định và hàng rời; img_031 cho thấy vẽ mỗi bánh trước là thiếu.
- CAL-03 giới hạn ignore_region cho cụm thực sự không tách được, không bỏ cả bãi xe gần vào ignore.
- CAL-04 kiểm mask người/lỗ khung thay vì nhận SAM nguyên trạng.
- CAL-05 tách lượt mù và rework đã biết đáp án.
- CAL-06 giữ những ca chưa chắc phạm vi lớp để người nộp/coach kiểm; chưa đóng ca chỉ để đạt QC.

Còn **9 box can_review trên 7 ảnh**: img_003, img_008, img_009, img_022, img_023, img_031, img_034. Câu hỏi xe đạp trợ lực chưa có ví dụ được xác nhận; không bịa một ảnh xe điện để điền bảng. Đây là một nhóm vấn đề cần làm rõ, dùng `--guideline-gap 1`.

## 7. QC và quyết định

Đã mở rộng kiểm từ mẫu ban đầu sang **24/24 ảnh batch** bằng `qc_sample.py --n 24 --seed 42 --force`: 3 ảnh được ưu tiên ca khó, 21 ảnh còn lại; thực tế là kiểm toàn lô, không còn suy mẫu ngẫu nhiên đại diện lô này. Người nộp chưa kiểm độc lập; trường reviewer ghi `assisted-review`.

| Lượt | Lỗi theo đối tượng | Ảnh có lỗi | Ý nghĩa |
|---|---|---|---|
| QC đầu | 8/488 = 1.6% | 8/24 = 33.3% | 4 lỗi vị trí/kích thước, 2 sai lớp, 2 bỏ sót; lưu nguyên phiếu/report ở evidence/qc_round1. |
| Sau sửa | 0/490 = 0% lỗi rõ mới được ghi nhận | 0/24 = 0% | Không chứng nhận nhãn hoàn hảo; các ca can_review vẫn chưa chốt. |

Hai mẫu số 488/490 loại ignore_region; release candidate có 493 annotation = 490 vật + 3 vùng ignore. Không gọi 0/24 là bằng chứng tỉ lệ lỗi thực dưới 2%: cận trên Wilson 95% do script in là **14%**, và điều đó còn chưa xét sai lệch reference/reviewer hay vật cả máy lẫn lượt review cùng bỏ sót. Với toàn lô này, con số 0 chỉ là số lỗi được ghi nhận theo lượt kiểm hiện có.

`check_submission.py` kiểm đủ file/cột/số dòng; `validate_labels.py` kiểm schema, lớp, box trong ảnh và phạm vi. Chúng không xác nhận độ ôm sát 2 px, phần nhìn thấy 20%, chất lượng mask, việc người học đã tự gán hay Gold độc lập.

**Quyết định: HOLD**, ngày 2026-11-17. Theo cây Hold → Rework → Release, dừng tại Hold vì nhóm ca CAL-06 chưa được xác nhận. Ngay cả khi đóng nhóm đó, kết quả mù 5/12 vẫn yêu cầu một lượt review mù mới trước khi suy Release; sửa sau biết đáp án không thay thế được. Gold có hỗ trợ cũng chưa đáp ứng Part 1 không AI. Không triển khai release candidate như nhãn đã được phê duyệt.

Bản sau sửa là sản phẩm để kiểm tiếp: nhãn CVAT local job #20 khớp 493 annotation; Gold job #17 khớp 65 annotation. Không thao tác job #19 của bài HumanPose trong quá trình này. Bài nâng cao là tùy chọn, chưa thực hiện và không nhận điểm bonus.

## 8. Ghi chú cá nhân và khả năng tái lập

- **Phương pháp hỗ trợ:** trợ lý xử lý annotation, script và tổng hợp báo cáo. Đã đối chiếu ảnh gốc, giữ bằng chứng lượt đầu, chạy validator và đọc lại dữ liệu CVAT. Không có xác nhận người học/coach hoặc review độc lập; kết luận là bản dự thảo chờ người nộp kiểm.
- **Reference không độc lập:** 4 ảnh/65 annotation do quy trình hỗ trợ tạo. Không xem detector pre-label trước khi khóa bản này, nhưng vẫn không đạt Gold không AI theo RULES.md. Tên `gold_manual.json` chỉ để tương thích script, không phải tuyên bố gán tay. Cần người nộp báo đúng phương pháp khi nộp và chấp nhận giới hạn điểm phần Gold.
- Máy: macOS Apple Silicon, MPS, Python 3.11.15, Ultralytics 8.4.215, torch 2.15.0, CVAT Docker 2.74.1. Version và hash trọng số ở `evidence/environment.json`. YOLOE tự cài CLIP và tải text encoder vào models; không commit model hay môi trường.
- **Không dùng cache** `data/cache` thay cho các lượt chạy. Đã chạy thật cả sáu lượt, log tham số/latency và nhận xét cụ thể. Không gửi ảnh riêng tư hoặc ảnh ngoài bộ dữ liệu lên repo; giữ ATTRIBUTION.
- **Lỗi script đã sửa:** checker tiêu đề chỉ nhận MSHV toàn số, báo sai với `2A202603149`; mở rộng sang chữ/số. Đã kiểm mã số thuần, mã chữ/số, tiêu đề thiếu tên và placeholder. Không thay các ngưỡng QC, không sửa output đo bằng tay.
- `time_log` là thời gian thực hỗ trợ, không phải đo tốc độ người. Không tự điền thời gian gán tay/hiệu quả học viên chưa đo. Hash pre-label/đáp án được khóa trước review; lưu tách kết quả trước/sau khi mở đáp án.
- Lần sau cần tạo Gold do người học tự gán trước khi dùng bất kỳ hỗ trợ xem ảnh; một lượt review mù độc lập và đối chiếu box ở 100% giúp tránh việc thấy output có vẻ hợp lý rồi bỏ qua mép ảnh.

Kiến thức rút ra từ thực nghiệm: YOLO26 tìm nhanh các lớp cố định nhưng không tự có lớp ba bánh; YOLOE bổ sung bằng prompt nhưng vẫn tạo nhãn sai và khung lỏng; SAM tìm biên theo prompt chứ không quyết định ngữ nghĩa. CVAT quản lý sửa nhãn; checker/QC hỗ trợ đo, không thay việc kiểm đúng vật và đúng phạm vi. Bản ghi nhớ khái niệm nằm ở [STUDY_NOTES](../STUDY_NOTES.md).
