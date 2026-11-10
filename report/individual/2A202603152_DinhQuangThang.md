# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Đinh Quang Thắng
- MSSV: 2A202603152
- Email trường: 26ai.thangdq4@vinuni.edu.vn
- Email cá nhân: dqthang311@gmail.com
- Lớp: L2A
- Link repo bài làm: https://github.com/quangthang311/K5-L2-Day02-DinhQuangThang-2A202603152-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1voma8Fl2pDgLwt9uv0omqCCwnmWKjuJK?usp=sharing

## 2. Schema và mini guideline

Tham khảo [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema giữ nguyên ID: `car` (0), `truck` (1), `bus` (2), `van` (3). Gán một box riêng cho mỗi xe, ôm sát phần thân nhìn thấy; xe bị che/cắt vẫn gán nếu đủ căn cứ nhận diện. Không đoán lớp khi ảnh quá mờ hoặc vật thể quá nhỏ.

Ba case đã ghi trong mini guideline:

- `drive_002`: xe buýt bị che một phần; gán `bus` theo phần nhìn thấy và vẽ box riêng cho từng xe.
- `drive_004`: xe xa, nhỏ và mờ trong vùng tối; không đoán lớp, ghi nhận để hỏi giảng viên.
- `drive_006`: xe dáng cao, thân dài giống van/minivan; gán `van`, không gán `car`.

Các trường hợp xe bus/van khó phân biệt cần hỏi giảng viên/labcoach.

## 3. Annotation và self-QC

- Số file nhãn train: 50.
- Kết quả checker: **ĐẠT**, 50/50 file nhãn hợp lệ. Checker xác nhận cấu trúc, class ID, tọa độ YOLO và box trong biên; không xác nhận box có đúng vật thể/lớp hay không.
- Danh sách ảnh train đã rà bằng mắt: `drive_001`, `drive_002`, `drive_003`, `drive_004`, `drive_005`.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: Gold QC có FP/FN nhưng các tổng số không đủ để quy nguyên nhân cụ thể cho từng lỗi.
- Commit khóa nhãn ban đầu: Đã tiến hành commit lên Github.

## 4. Gold QC và rework

Kết quả hiện có trong [annotation_evaluation.json](../../outputs/annotation_evaluation.json).

| Chỉ số | Gía trị hiện có |
|---|---:|
| TP | 416 |
| FP | 264 |
| FN | 237 |
| Precision | 0.6118 |
| Recall | 0.6371 |
| F1 | 0.6242 |
| mean_iou_of_matches | 0.8127 |
| passed | false |

F1 chưa đạt ngưỡng 0.80; mean IoU đạt ngưỡng 0.70. Vì F1 chưa đạt nên gold QC tổng thể **chưa đạt** và cần kiểm tra/rework nhãn train trước khi coi annotation đủ điều kiện train.

Outputs vẫn có artifacts của một lần train YOLO, dù kết quả gold QC hiện có chưa đạt; vì vậy kết quả train dưới đây cần được xem là lần chạy trên nhãn chưa qua cổng QC,

## 5. Kết quả YOLO

- Runtime: GPU/CPU được ghi là **CPU** trong [args.yaml](../../report/individual/assets/yolo26_runs/vehicle_annotation/args.yaml). Runtime cụ thể và phiên bản thư viện chưa được lưu trong outputs.
- Các tham số: Cấu hình lưu được là `yolo26n.pt`, 50 epochs, `imgsz=640`, `batch=-1`, seed `20260909`, CPU. Các giá trị này khớp với cấu hình notebook; không thấy tham số thay đổi được ghi lại.
- Đường dẫn checkpoint: [best.pt](../../report/individual/assets/yolo26_runs/vehicle_annotation/weights/best.pt).
- Ảnh dự đoán eval: [thư mục eval_predictions](../../report/individual/assets/eval_predictions/visuals/).
- Biểu đồ và ma trận đánh giá: [thư mục vehicle_eval](../../report/individual/assets/yolo26_runs/vehicle_eval/).

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4605 |
| Recall | 0.2490 |
| mAP50 | 0.2780 |
| mAP50–95 | 0.1801 |

Các chỉ số lấy từ [model_eval_metrics.json](../../outputs/model_eval_metrics.json). Ví dụ trực quan: ở [drive_051.jpg](../../report/individual/assets/eval_predictions/visuals/drive_051.jpg), model không phát hiện đúng lớp chiếc bus được gán nhãn trong ảnh; phần lớn prediction hiện ra là `car`.

### Phân tích bốn câu hỏi

1. **`bus` và `van` là hai lớp thấp nhất (cùng mức thấp nhất).** Trong confusion matrix chuẩn hóa, không có giá trị đúng trên đường chéo của `bus` và `van` ở ngưỡng ma trận; biểu đồ recall theo confidence cũng cho thấy hai lớp kém hơn `car`. Ví dụ `drive_051`: ảnh eval có một nhãn `bus`, nhưng prediction không khớp đúng lớp. Tập eval nhỏ nên đây là kết quả trên split này, không phải kết luận tổng quát.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, model có thể đã tìm đúng vật thể ở ngưỡng IoU 0.50 nhưng box chưa đủ khít/chính xác ở các ngưỡng IoU cao hơn. Cần rà cạnh box, độ rộng/chiều cao, tâm box và phần nền hoặc xe khác bị lấy vào. Với kết quả hiện tại, cả mAP50 (0.2780) và mAP50–95 (0.1801) đều thấp, không phải trường hợp mAP50 cao.
3. Có dấu hiệu không nhất quán ở lớp `bus`/`van`: [drive_052.txt](../../dataset/labels/eval/drive_052.txt) gán class ID `3` (`van`) cho chiếc xe lớn chở khách trong [ảnh drive_052](../../dataset/images/eval/drive_052.jpg). Theo guideline, xe bus/minibus chở khách thuộc `bus` (ID `2`), còn `van` dành cho van/minivan. Cần xác nhận nhãn này với giảng viên; nhãn không nhất quán có thể góp phần làm model nhầm bus và van. Confusion matrix cũng cho thấy model nhầm giữa hai lớp trên eval, nhưng riêng điều đó không chứng minh nguyên nhân là guideline.
4. Không dùng 10 ảnh eval để train vì model sẽ học chính dữ liệu dùng để đánh giá, gây rò rỉ và làm kết quả không còn khách quan. Notebook dùng eval làm validation trong quá trình train/chọn `best.pt`; do đó eval không phải test set độc lập, nhưng vẫn phải giữ tách khỏi dữ liệu train.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Gold QC nhãn train | F1 0.6242 thấp hơn ngưỡng 0.80 | Đọc kết quả TP/FP/FN và mean IoU | Cần kiểm tra nhãn theo từng ảnh.|
| Đánh giá model eval | Recall 0.2490; nhầm lẫn rõ ở `bus`/`van` | Đối chiếu confusion matrix, ảnh prediction và nhãn eval | Có dấu hiệu nhãn `drive_052` cần được xác nhận theo guideline|

## 7. Tự đánh giá theo rubric

- **Định dạng YOLO:** Đạt bước kiểm tra cấu trúc, đủ 50/50 file nhãn train hợp lệ.
- **Đầy đủ vật thể, hình học box, phân lớp:** Chưa thể tự xác nhận chỉ bằng checker; cần rà ảnh và xử lý các lỗi gold QC.
- **Mini guideline:** Có schema và ba case quyết định được ghi trong `GUIDELINE_MINI.md`.
- **Gold-set QC:** Chưa đạt F1 yêu cầu (`0.6242 < 0.80`); mean IoU đạt (`0.8127 >= 0.70`).
- **Pipeline YOLO và phân tích:** Có metrics, confusion matrix và ảnh dự đoán trên 10 ảnh eval. Kết quả model thấp; cần ghi nhận việc eval cũng được dùng làm validation và không coi đây là test set độc lập.

## 8. Công cụ AI và tài liệu tham khảo

- Đã dùng Copilot SDK trong VS Code để hỗ trợ phần phân tích trong báo cáo.
- Tài liệu trong repo: [GUIDE.md](../../GUIDE.md), [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md), [day2_annotation_to_yolo.ipynb](../../notebooks/day2_annotation_to_yolo.ipynb), [SUBMISSION.md](../../SUBMISSION.md), [CHECKPOINTS.md](../../CHECKPOINTS.md).
