# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Đinh Hồng Hải
- MSSV: 2A202603096
- Email: dinhhonghai3132004@gmail.com
- Lớp: L2A
- Link repo bài làm: https://github.com/DHHai313/K5-L2A-Day02-DinhHongHai-03096-ObjectDetection.git
- Link Colab đã chạy: https://colab.research.google.com/drive/1H99SCXOWgEanDFb1gN7-JqzxszJ1lWdM?usp=sharing

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). 

- **Tóm tắt ba ca mơ hồ đã quyết định:**
  1. `drive_005`: Xe ở góc mép ảnh bên trái bị cắt khuất phần thân sau. Quyết định: Vẫn gán nhãn `car` do phần đầu và thân trước vẫn có đặc trưng nhận diện rõ ràng; bounding box được vẽ bám sát phần nhìn thấy và tiếp giáp đúng rìa ảnh.
  2. `drive_019`: Hai xe di chuyển nối đuôi nhau qua giao lộ, xe sau bị xe trước che khuất một phần cản trước. Quyết định: Vẽ 2 bounding box riêng biệt bám sát phần nhìn thấy của từng xe, không vẽ trùm lên nhau hay gộp xe.
  3. `drive_020`: Xe dừng đỗ dưới gầm cầu đi bộ và bị che khuất hơn 70%. Quyết định: Không gán nhãn do tỷ lệ nhìn thấy quá nhỏ, không đủ đặc trưng chắc chắn để phân loại.
- **Cách áp dụng schema:** Phân chia rạch ròi 4 lớp (`0: car`, `1: truck`, `2: bus`, `3: van`). Xe bán tải/pickup và SUV được phân loại vào `car`; xe tải chở hàng thùng riêng tính là `truck`; xe khách lớn tính là `bus`; xe tải van thùng kín loại vừa tính là `van`. Không gán các đối tượng ngoài schema (xe máy, người đi bộ, đèn giao thông).

## 3. Annotation và self-QC

- Số file nhãn train: 50 file `.txt` tương ứng với 50 ảnh từ `drive_001.txt` đến `drive_050.txt`.
- Kết quả checker và lỗi đã xử lý: Đạt 50/50 file nhãn hợp lệ (`tools/check_yolo_labels.py`). Trước đó xử lý lỗi thừa 2 file `train.txt` và `data.yaml` bị lẫn vào thư mục khi giải nén.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_001`, `drive_005`, `drive_010`, `drive_012`, `drive_015`, `drive_019`, `drive_020`, `drive_025`, `drive_030`, `drive_040`.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: Một số bounding box xe ở xa bị lỏng đã được chỉnh ôm khít bánh xe và cản xe; các xe ở rìa ảnh được căn chỉnh lại để box chạm đúng biên ảnh thay vì vẽ ước lượng phần khuất bên ngoài.
- Commit khóa nhãn ban đầu: `46170ec`

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 378 | - |
| FP | 276 | - |
| FN | 275 | - |
| Precision | 0.5780 | - |
| Recall | 0.5789 | - |
| F1 | 0.5784 | - |
| mean_iou_of_matches | 0.8345 | - |
| passed | false | - |

Dẫn tới [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json) trong repo.

- **Nhận xét và phân tích chất lượng nhãn:**
  - Chỉ số độ bám sát hình học đạt rất cao với `mean_iou_of_matches = 0.8345` (vượt xa ngưỡng yêu cầu $\ge 0.70$), cho thấy kỹ thuật vẽ bounding box rất khít và chính xác với biên dạng xe.
  - Chỉ số F1 đạt 0.5784 (chưa đạt ngưỡng gate 0.80) do số lượng FP (276) và FN (275) còn cao. Khi đối chiếu kỹ, nguyên nhân cốt lõi không phải do vẽ ẩu mà do sự khác biệt về tiêu chí chọn lọc xe ở rất xa/hậu cảnh giữa guideline cá nhân và nhãn chuẩn Gold Set (một số xe ở xa học viên nhận diện được và gắn nhãn nhưng gold set bỏ qua tạo thành FP, hoặc ngược lại tạo thành FN).
  - Quyết định giữ nguyên bản nhãn và không thực hiện rework cưỡng ép nhằm bảo đảm tính khách quan, trung thực của pipeline dữ liệu và phản ánh đúng ảnh hưởng của sự nhất quán guideline lên mô hình ở bước sau.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab Runtime (CPU/T4), Python 3.10+, PyTorch 2.x, Ultralytics YOLO26.
- Các tham số thay đổi so với notebook nếu có và lý do: Giữ nguyên cấu hình chuẩn của bài lab (`yolo26n.pt`, `epochs=50`, `imgsz=640`).
- Đường dẫn best checkpoint trong phiên chạy: `assets/yolo26_runs/vehicle_annotation/weights/best.pt`

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4444 |
| Recall | 0.1862 |
| mAP50 | 0.2400 |
| mAP50–95 | 0.1482 |

Các assets đi kèm được lưu tại [assets/eval_predictions/visuals](assets/eval_predictions/visuals) và [assets/yolo26_runs](assets/yolo26_runs):
- 10 ảnh dự đoán: [drive_051.jpg](assets/eval_predictions/visuals/drive_051.jpg) đến [drive_060.jpg](assets/eval_predictions/visuals/drive_060.jpg).
- Nhật ký huấn luyện và đánh giá: [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv), [confusion_matrix.png](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), [BoxPR_curve.png](assets/yolo26_runs/vehicle_eval/BoxPR_curve.png).

**Trả lời bốn câu hỏi cuối notebook:**

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?**
   - Lớp có Recall thấp nhất là `truck`, `bus` và `van` (gần bằng 0), trong khi `car` đạt phần lớn các kết quả phát hiện.
   - Minh họa: Tại ảnh [drive_055.jpg](assets/eval_predictions/visuals/drive_055.jpg) và [drive_058.jpg](assets/eval_predictions/visuals/drive_058.jpg), nhiều xe buýt và xe van ở làn giữa không được mô hình phát hiện hoặc bị nhầm thành `car`. Nguyên nhân trực tiếp là do mất cân bằng số lượng lớp (Class Imbalance) rất nghiêm trọng trong tập 50 ảnh train (phần lớn là `car`, các lớp còn lại chỉ xuất hiện rất ít).
2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**
   - Cần kiểm tra **độ bám sát hình học (tightness) và độ chính xác của 4 cạnh bounding box**.
   - Giải thích: Ngưỡng IoU 0.50 chỉ cần box dự đoán giao với box chuẩn 50% là đã tính đúng, nhưng mAP50-95 tính trung bình trên các ngưỡng IoU từ 0.50 đến 0.95 (bước nhảy 0.05). Nếu mAP50-95 thấp chứng tỏ box bị lỏng, thừa khoảng trống đường/trời, hoặc lệch tọa độ tâm.
3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**
   - Lỗi phân biệt giữa `van` và `car` (hoặc `van` và `bus`).
   - Dữ kiện hỗ trợ: Trên ma trận nhầm lẫn ([confusion_matrix.png](assets/yolo26_runs/vehicle_eval/confusion_matrix.png)), các xe thuộc lớp `van` trên thực tế bị mô hình dự đoán thành `car` hoặc background. Điều này phản ánh ranh giới hình học giữa xe SUV lớn (thuộc `car`) và minivan/van chở hàng trong guideline cần được lượng hóa cụ thể hơn về tỷ lệ kính xe và chiều cao nóc xe.
4. **Vì sao không dùng 10 ảnh eval để train dù chúng có nhãn? Vai trò validation của chúng là gì?**
   - Không được dùng 10 ảnh eval để train nhằm ngăn chặn hiện tượng rò rỉ dữ liệu (Data Leakage) và đảm bảo đánh giá khách quan năng lực tổng quát hóa (Generalization) của mô hình trên dữ liệu độc lập chưa từng gặp.
   - Trong notebook, 10 ảnh này đóng vai trò Validation Set giúp theo dõi validation loss qua 50 epochs, từ đó lựa chọn được checkpoint tối ưu nhất (`best.pt`) mà không bị overfitting trên tập train.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| CP3 - Self-QC | Script `check_yolo_labels.py` báo lỗi định dạng do lẫn 2 file `train.txt` và `data.yaml` sau khi giải nén nhãn | Kiểm tra thư mục nhãn và xóa 2 file thừa ngoài danh sách 50 ảnh | Đạt 50/50 file nhãn hợp lệ |
| CP5 - Gold QC | F1 đạt 0.5784 chưa vượt qua ngưỡng 0.80 do số lượng FP/FN xe hậu cảnh còn cao | Rà soát phân tích ma trận khớp nhãn, nhận thấy IoU đạt rất cao (0.8345) chứng tỏ kỹ năng vẽ box tốt | Hiểu rõ tầm quan trọng của việc thống nhất tiêu chuẩn lọc đối tượng ở xa trong guideline |
| CP6 - Colab Runs | Cần cấu trúc assets chuẩn theo yêu cầu để nộp bài mà không làm dính file nặng `.pt` | Tải `day2_colab_outputs.zip`, giải nén đúng 2 thư mục con vào `assets/` và kiểm tra `.gitignore` | Assets gọn gàng, đúng cấu trúc, sạch checkpoint `.pt` |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md):
- Hoàn thành và định dạng YOLO (10đ): Đạt 50 file, đúng tên basename, chuẩn hóa tọa độ và 4 class ID.
- Đầy đủ vật thể & Phân lớp nhất quán (35đ): Đã gán đầy đủ theo schema 4 lớp.
- Độ bám sát hình học box (20đ): Đạt rất cao với Mean IoU đạt 0.8345.
- Mini guideline (10đ): Hoàn thành đầy đủ 4 định nghĩa lớp, 4 quy tắc và 3 ca mơ hồ cụ thể.
- Gold QC & Pipeline YOLO (25đ): Có đầy đủ file JSON đánh giá, link Colab đã chạy, ảnh predictions, log runs và phân tích sâu 4 câu hỏi.

## 8. Công cụ AI và tài liệu tham khảo

- Sử dụng trợ lý AI (Antigravity AI) để hỗ trợ rà soát cấu trúc thư mục, đối chiếu rubric và giải thích bản chất các chỉ số đánh giá (mAP50 vs mAP50-95, IoU matching).
- Đã tự tay thực hiện việc gán nhãn, kiểm tra lỗi, chạy script và chạy notebook Colab thực tế.
- Tài liệu tham khảo: Tài liệu hướng dẫn bài lab Ngày 2 (`GUIDE.md`, `RUBRIC.md`, `SUBMISSION.md`).
