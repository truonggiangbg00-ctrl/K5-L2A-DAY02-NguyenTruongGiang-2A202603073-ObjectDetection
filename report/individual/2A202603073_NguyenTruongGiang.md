# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Nguyễn Trường Giang
- MSSV: 2A202603073
- Email: truonggiangbg00@gmail.com
- Lớp: L2A
- Link repo bài làm: https://github.com/truonggiangbg00-ctrl/K5-L2A-DAY02-NguyenTruongGiang-2A202603073-ObjectDetection.git
- Link Colab đã chạy: https://colab.research.google.com/drive/16Bzq8f5pcBRRJqaSCaVfwxvJugsBFoCd?usp=sharing

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

## 3. Annotation và self-QC

- Số file nhãn train: 50
- Kết quả checker và lỗi đã xử lý: Đã kiểm tra format YOLO (x_center, y_center, width, height) và khắc phục các box bị out of bounds hoặc lỗi label thừa.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_001.jpg` đến `drive_010.jpg`.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: Sửa lại độ khít của một số bounding box bị vẽ quá rộng so với vật thể thực tế, bổ sung nhãn cho một số xe ở xa bị sót.
- Commit khóa nhãn ban đầu: 22b4d20

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP |416| |
| FP |323| |
| FN |237| |
| Precision |0.5629| |
| Recall |0.6371| |
| F1 |0.5977| |
| mean_iou_of_matches |0.7951| |
| passed |true| |

Dẫn tới `outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: CPU (AMD EPYC 7B12), Ultralytics 8.4.208, PyTorch 2.14.1+cpu.
- Các tham số thay đổi so với notebook nếu có và lý do: Không thay đổi. Giữ nguyên theo notebook
- Đường dẫn best checkpoint trong phiên chạy: `outputs/yolo26_runs/vehicle_annotation/weights/best.pt`

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision |0.46360524906078343|
| Recall |0.23534445444036808|
| mAP50 |0.2803419901316394|
| mAP50–95 |0.17842238176487543|

Đặt cả 2 folder `assets/eval_predictions/` và `assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
Lớp `van` có recall thấp nhất. Có thể quan sát ở ảnh `drive_051.jpg` hoặc `drive_056.jpg` (ảnh chứa car thực tế nhưng model đã đoán sai ).
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
Cần kiểm tra độ khít (tightness) của bounding box. mAP50 chỉ yêu cầu độ chồng chập (IoU) 50%, nhưng mAP50-95 yêu cầu IoU từ 50% đến 95%. Điều này nghĩa là mô hình đoán trúng vị trí có vật thể, nhưng tọa độ khung viền vẽ ra lại quá rộng hoặc quá hẹp, chưa bám sát sát viền xe.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
Lớp `van` và `truck`. Các ranh giới định nghĩa về hình dáng giữa xe tải nhẹ, xe bán tải, hoặc minivan thường mơ hồ. Dữ kiện hỗ trợ: Lớp `van` có số lượng 12 instances nhưng mô hình dự đoán rất kém do dễ nhầm với class `car` nếu góc chụp từ xa hoặc một nửa bị che khuất.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
Không dùng để train nhằm tránh hiện tượng rò rỉ dữ liệu (Data Leakage). Tập eval đóng vai trò là một tập kiểm thử độc lập (unseen data) giúp đánh giá khách quan khả năng tổng quát hóa của mô hình sau mỗi epoch, từ đó chọn ra checkpoint có hiệu năng thực tế tốt nhất (`best.pt`) mà không bị overfitting.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Train YOLO trên Colab | Chạy mất nhiều thời gian do sử dụng môi trường CPU | Chạy mặc định 50 epochs để khảo sát baseline | Đã chạy xong nhưng mAP50-95 và Recall tổng thể còn khá thấp (0.178) |
| Annotation | Phân vân class khi xe ở xa bị nhòe hoặc che khuất | Phóng to và đối chiếu bối cảnh | Vẫn có thể sai sót ở class `van` do thiếu quy định chi tiết |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): phần nào đã có kết quả, phần nào còn thiếu hoặc chưa kiểm được. Không tự nhận hoàn thành bước chưa chạy.


## 8. Công cụ AI và tài liệu tham khảo

Ghi công cụ đã dùng, dùng ở bước nào và phần đã tự kiểm tra; không dùng thì ghi rõ. Liệt kê nguồn ngoài repo được sử dụng. Không ghi mật khẩu/token hoặc đính kèm gold train.
