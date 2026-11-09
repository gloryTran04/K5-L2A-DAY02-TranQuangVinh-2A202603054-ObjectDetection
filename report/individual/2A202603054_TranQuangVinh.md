# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Trần Quang Vinh
- MSSV: 2A202603054
- Email: tqvinh20804@gmail.com
- Lớp: K5-L2A
- Link repo bài làm: https://github.com/gloryTran04/K5-L2A-DAY02-TranQuangVinh-2A202603054-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1kN6uCJOwfAbofP36fpPTf6uYUMiVEzQh?usp=sharing

## 2. Schema và mini guideline

Chi tiết ở [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema cố định theo `data.yaml`: `car=0`, `truck=1`, `bus=2`, `van=3`.

Cách áp dụng schema:

- `car`: xe con, SUV, taxi, bán tải; `van`: thân hộp cao, đầu ngắn, cabin và khoang sau liền một khối; `truck`: cabin và thùng/thiết bị tách thành hai khối; `bus`: thân dài, nhiều ô cửa sổ, có bảng tuyến.
- Xe đang đỗ vẫn gán theo loại xe (GUIDE, bảng "Các quyết định cần nhất quán"). Xe bị che/cắt khung vẫn gán nếu nhận ra lớp, box chỉ bao phần nhìn thấy, cạnh box dừng ở rìa ảnh.
- Không gán mô tô, xe đạp, xe ba bánh, người, hình xe trên biển quảng cáo.

Các ca mơ hồ đã quyết định:

| Ảnh | Tình huống | Quyết định |
| --- | --- | --- |
| drive_009 | Xe xanh lá: cabin + thùng kín hình hộp | `truck` (cabin và thùng tách khối) |
| drive_042 | Xe tải chất đống bao hàng cao | `truck`, box bao cả phần hàng |
| drive_019 (và 014, 034, 038, 046) | Xe cứu hộ có cần cẩu | `truck` ở cả năm ảnh |
| drive_024 (và 011, 034) | Xe ba bánh có mái/thùng | Không gán (ngoài schema) |

Phần cần giảng viên làm rõ (chưa gán, chờ trả lời):

- drive_006: xe xanh ngọc có giá nóc ở mép phải — `car` hay `van`?
- drive_014 và drive_038: xe đỏ thân hộp có chữ trắng ở góc trên phải — `van` hay `bus`? (gán cùng một lớp cho cả hai ảnh)

## 3. Annotation và self-QC

- Số file nhãn train: 50 (`dataset/labels/train/drive_001.txt` … `drive_050.txt`), tổng 1153 box: car 963, bus 106, van 61, truck 23.
- Kết quả checker và lỗi đã xử lý: `python3 tools/check_yolo_labels.py` → "ĐẠT cấu trúc: 50/50 file nhãn hợp lệ".
- Danh sách ít nhất 10 ảnh đã rà bằng mắt (phóng to từng vùng, có lưới tọa độ, đối chiếu box với xe):

| Ảnh | Đã kiểm | Phát hiện và cách sửa |
| --- | --- | --- |
| drive_003 | Đủ xe, box sát ở cụm xe đông bên trái | Lượt đầu nhiều box lệch vị trí; vẽ lại toàn bộ ảnh và kiểm lại bằng ảnh phóng to |
| drive_004 | Xe nhỏ ở mép trên | Xóa 2 box không phải xe ở góc trên |
| drive_007 | Lớp xe hộp trắng giữa ảnh | Đổi `truck` → `van` (thân hộp liền khối) |
| drive_008 | Xe ben, van, bus nối toa | Thu sát lại box xe ben; lớp đúng theo guideline |
| drive_017 | Ảnh tối, xe ở xa | Xóa 4 box đặt trên vùng không phải xe |
| drive_023 | Vật thể tối ở góc trên trái | Xóa 1 box (không phải xe) |
| drive_028 | Hàng xe đỗ hai bên đường | Xóa 1 box ở vùng giữa hai xe đỗ (chỉ có vật màu đỏ, có thể là người, không phải xe)  |
| drive_044 | Đường phía trên tường chắn | Xóa 4 box ở đường phía trên tường chắn; thêm 1 xe nhỏ còn sót. Kiểm lại sau: 3/4 box đã xóa (1 xe buýt trắng, 2 xe con) vẫn nhận ra được lớp nên lẽ ra phải gán (có thể là FN); box còn lại (khối xanh lá ở mép trên) không chắc là xe |
| drive_045 | Hàng xe đỗ bên trái | Sửa 3 box bị lệch sang xe bên cạnh; thêm 2 xe bị sót |
| drive_050 | Cụm xe đông bên trái và phải | Sửa khoảng 6 box lệch, xóa 3 box trùng, thêm 2 xe bị sót, đổi 1 box `van` → `car` (xe SUV) |

- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: lỗi chủ yếu là box đặt lệch sang xe bên cạnh ở các cụm xe đông và box trùng; đã sửa như bảng trên. Xe ở rất xa mà không xác định được lớp thì không gán.
- Commit khóa nhãn ban đầu: `f271c38` ("Lock initial train labels and annotation evaluation").

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 469 | — |
| FP | 684 | — |
| FN | 184 | — |
| Precision | 0.4068 | — |
| Recall | 0.7182 | — |
| F1 | 0.5194 | — |
| mean_iou_of_matches | 0.8486 | — |
| passed | false (ngưỡng F1 mặc định 0.80) | — |

Kết quả ở `outputs/annotation_evaluation.json`. Mean IoU 0.85 cho thấy box đã sát; F1 thấp chủ yếu do FP (684) và nhầm lớp. Khi kiểm từng ảnh, em thấy hai điểm khác giữa gold và tài liệu bài:

1. **Thứ tự ID lớp khác `data.yaml`.** Ghép box của em với box gold cùng vị trí (IoU ≥ 0.5, bỏ qua lớp), lệch có quy luật: em `bus(2)` ↔ gold `3` (53 box), em `van(3)` ↔ gold `1` (34 box), em `truck(1)` ↔ gold `2` (19 box). Ví dụ drive_008: xe ben đỏ (GUIDELINE_MINI lấy làm ví dụ `truck`) có lớp `2` trong gold, xe buýt nối toa lớp `3`, ba xe van trắng lớp `1`. Gold như đang dùng `car=0, van=1, truck=2, bus=3`, trong khi `data.yaml` là `car=0, truck=1, bus=2, van=3`.
2. **Gold không gán xe đang đỗ.** Ví dụ drive_028: gold 3 box, không có hàng xe đỗ hai bên đường; drive_049: gold 6 box, không có hàng xe đỗ bên trái. GUIDE ghi "Xe đang đỗ: vẫn gán lớp theo loại xe", nên em giữ các box này. Đây là suy luận từ một số ảnh, chưa chắc đây là nguyên nhân duy nhất (gold cũng có thể bỏ xe nhỏ ở xa).

Không rework: em giữ nhãn theo `data.yaml` và GUIDE, không sửa nhãn chỉ để khớp gold, không sửa gold. Đã khai báo với lab coach và đã chấp nhận F1 trên 0.5 là đạt.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab, GPU T4, Ultralytics 8.4.208, torch-2.14.1+cu130
- Các tham số thay đổi so với notebook nếu có và lý do: không thay đổi; `yolo26n.pt`, `epochs=50`, `imgsz=640`, `batch=-1` như notebook.
- Đường dẫn best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (không commit file `.pt`).
- Lưu ý: theo [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv), `best.pt` là checkpoint **epoch 1** (mAP50 0.2724, mAP50–95 0.2018 trên tập val = 10 ảnh eval). Các epoch sau đều thấp hơn, epoch 50 còn mAP50 0.2309, mAP50–95 0.1592. Tức là train thêm trên nhãn của em không làm điểm trên eval tăng. Một nguyên nhân có thể là nhãn eval dùng thứ tự lớp khác nhãn train (xem câu 3 bên dưới), nên model càng học theo nhãn train thì càng lệch với nhãn eval; ngoài ra tập train chỉ có 50 ảnh.

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4105 |
| Recall | 0.2303 |
| mAP50 | 0.2723 |
| mAP50–95 | 0.2017 |

Nguồn: [model_eval_metrics.json](assets/model_eval_metrics.json) (best checkpoint, chạy `val` trên 10 ảnh eval).

- Ảnh dự đoán trên 10 ảnh eval: [assets/eval_predictions/visuals/](assets/eval_predictions/visuals/) (drive_051 … drive_060).
- Log train theo epoch: [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv), [results.png](assets/yolo26_runs/vehicle_annotation/results.png).
- Đánh giá best checkpoint: [confusion_matrix.png](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), [BoxPR_curve.png](assets/yolo26_runs/vehicle_eval/BoxPR_curve.png).

Trả lời bốn câu hỏi cuối notebook:

Số liệu theo lớp lấy từ [confusion_matrix.png](assets/yolo26_runs/vehicle_eval/confusion_matrix.png) (cột = nhãn eval, hàng = dự đoán):

| Lớp trong nhãn eval | Số box | Dự đoán đúng lớp | Bị dự đoán thành | Bỏ sót | Recall ước tính |
| --- | ---: | ---: | --- | ---: | ---: |
| car (0) | 94 | 84 | bus 2, van 3 | 5 | ~0.89 |
| truck (1) | 5 | 1 | car 4 | 0 | ~0.20 |
| bus (2) | 2 | 0 | car 1 | 1 | 0 |
| van (3) | 12 | 0 | **bus 11** | 1 | 0 |

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?**
   `bus` và `van` đều có recall 0, `truck` ~0.20, trong khi `car` ~0.89. Riêng `van`: 11/12 box bị dự đoán thành `bus`. Ảnh minh họa là [drive_056](assets/eval_predictions/visuals/drive_056.jpg): chiếc xe buýt trắng ở phía trên giữa ảnh được model dự đoán `bus 0.52`, nhưng trong `dataset/labels/eval/drive_056.txt` box này có lớp `3` (= `van` theo `data.yaml`), nên bị tính là sai lớp.

2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**
   Khi đó model tìm đúng vật thể nhưng box chưa sát ở ngưỡng IoU cao, nên cần kiểm: cạnh box có ôm sát thân xe hay lấn nền/bóng xe; quy ước box có nhất quán không (xe bị che chỉ bao phần nhìn thấy, xe bị cắt khung dừng ở rìa ảnh, có tính hàng chất trên thùng không); và xe nhỏ ở xa, nơi lệch vài pixel đã làm IoU giảm mạnh. Ở lần chạy này mAP50 (0.272) và mAP50–95 (0.202) đều thấp, nên vấn đề chính là phát hiện/phân lớp chứ không phải độ sát box; mean IoU nhãn train so với gold là 0.85 (mục 4).

3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**
   - `van` → `bus` (11 box): nhiều khả năng do lệch thứ tự ID lớp chứ không phải model nhầm. Nhãn train của em theo `data.yaml` (`bus=2`, `van=3`), nhưng nhãn eval dùng cùng thứ tự với gold train (mục 4): ở drive_056, xe buýt trắng có lớp `3`, xe thân hộp nhỏ cạnh nó có lớp `1`. Model dự đoán `bus` cho xe buýt, đúng với hình ảnh và với nhãn train, nhưng bị chấm sai lớp.
   - `truck` → `car` (4/5 box): nếu nhãn eval cũng lệch thứ tự thì lớp `1` trong eval có thể là xe van, dễ bị dự đoán thành `car` vì ranh giới car/van ở GUIDELINE_MINI (thân hộp cao, đầu ngắn) khó thấy ở xe nhỏ.
   - `car` ↔ `van`: ở drive_056, hai xe SUV (giữa và dưới ảnh) bị dự đoán `van 0.95` và `van 0.89`. SUV cao gần giống minivan; guideline phân biệt bằng capo nhô và thân thấp, nhưng tập train chỉ có 61 box van so với 963 car nên model khó học ranh giới này.
   - Ngoài ra, nhãn train của em có gán xe đang đỗ (theo GUIDE), còn gold không gán; nếu nhãn eval cũng không gán xe đỗ thì các dự đoán đúng trên xe đỗ sẽ bị tính là FP, làm precision thấp (0.41).

4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?**
   Nếu train trên ảnh eval thì model đã "thấy đáp án", số đo trên eval sẽ cao giả và không phản ánh khả năng trên ảnh mới. Trong notebook, 10 ảnh eval được khai báo là `val` trong `data.yaml`: sau mỗi epoch Ultralytics đo mAP trên tập này và dùng nó để chọn `best.pt`, rồi cuối cùng chạy `val` và `predict` trên cùng 10 ảnh. Vì đã dùng để chọn checkpoint, tập eval là tập validation chứ không phải test set độc lập, nên kết quả trên nó có thể hơi lạc quan.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Self-QC | Checker báo thiếu 50 file | Chép file export YOLO từ CVAT vào `dataset/labels/train/` | Đạt 50/50 |
| Gold QC | F1 0.52 < 0.80 | So box với gold theo vị trí, tìm quy luật lệch lớp; đối chiếu GUIDE | Phát hiện lệch thứ tự lớp và khác quy định xe đỗ; báo giảng viên |
| Push lên GitHub | Lỗi 403 (máy đăng nhập sai tài khoản GitHub) | Đăng nhập lại đúng tài khoản sở hữu repo | Push thành công commit `f271c38` |
| Chạy Colab | Không cấp được runtime (cả GPU và CPU), Colab báo sự cố hệ thống | Đổi runtime sang CPU, chờ Colab khắc phục | Chạy lại sau, Colab cấp GPU; notebook chạy hết 50 epoch |

## 7. Tự đánh giá theo rubric

| Tiêu chí | Tình trạng |
| --- | --- |
| Hoàn thành và định dạng YOLO | Có 50 file, checker đạt |
| Đầy đủ vật thể | Gán cả xe đỗ, xe che/cắt khung; chưa gán 3 xe mơ hồ đang chờ giảng viên |
| Độ bám sát box | Mean IoU ~ 0.85 trên các box khớp |
| Phân lớp nhất quán | Theo `data.yaml`; lệch lớp so với gold do khác thứ tự ID (mục 4) |
| Mini guideline | Có định nghĩa 4 lớp, quy tắc box, ≥ 3 ca mơ hồ |
| Gold-set QC | Có JSON; F1 ~ 0.52 chưa đạt 0.80, đã giải thích nguyên nhân, và đã được chấp nhận |
| Pipeline YOLO | Colab chạy xong, giữ đúng split 50 train/10 eval, có metrics, ảnh dự đoán và trả lời 4 câu hỏi; mAP thấp, đã phân tích nguyên nhân (ít dữ liệu, mất cân bằng lớp, lệch thứ tự ID lớp trong nhãn eval) |

## 8. Công cụ AI và tài liệu tham khảo
- Phân tích gold QC: Claude viết script so box với gold, tìm ra lệch thứ tự lớp và xe đỗ.
- Tài liệu: GUIDE.md, GUIDELINE_MINI.md, RUBRIC.md, SUBMISSION.md trong repo; tài liệu Ultralytics YOLO.
