# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Trần Long Phú  Nhóm: ______   Ngày: 16/09/2026

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 317 / 141 / 35 |
| Thời gian trung bình mỗi ảnh | Chưa ghi nhận |

Ba khớp có `%v=1` cao nhất là `left_ear` (66%), `right_ear` (48%), và nhóm đồng hạng
`left_eye`, `left_wrist`, `left_hip` (38%). Tai và cổ tay thường bị tóc, mũ hoặc vật thể
che nên tỷ lệ này phản ánh việc bị che. Hông khó hơn ở chỗ vị trí giải phẫu phải ước lượng
qua quần áo; vì vậy `%v=1` không tự chứng minh hông là khớp khó nhất.

## 2. Chấm với gold

Chỉ có lần chạy sau cùng được lưu trong `outputs/eval_vs_gold.json`; không có bản JSON
trước rework nên không thể điền trung thực cột trước rework.

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | Chưa lưu | 0.9436 |
| OKS@0.50 | Chưa lưu | 1.0 |
| OKS@0.75 | Chưa lưu | 1.0 |
| Lỗi `dao_trai_phai` | Chưa lưu | 0 |
| Lỗi `nham_nguoi` | Chưa lưu | 0 |
| Lỗi `xoa_khop_bi_che` | Chưa lưu | 0 |

Không có nhật ký rework theo ảnh/người/khớp trong repository. Kết quả cuối ghi nhận 29/29
người được ghép; các phát hiện còn lại là 67 cờ khác gold, 66 khớp gold không gán và 9
trường hợp lệch nhẹ.

Không có lỗi đảo trái/phải được ghi nhận trong kết quả đánh giá cuối.

## 3. Kiểm chéo

Bạn cùng nhóm: ______

Chưa có bảng visibility của bạn cùng nhóm nên chưa thể tính khớp lệch `%v=1` một cách có
căn cứ.

Luật nhóm áp dụng: khớp còn trong khung nhưng bị che phải giữ điểm ước lượng và gán `v=1`;
chỉ gán `v=0` khi khớp thực sự nằm ngoài mép ảnh. Hông được đặt theo vị trí giải phẫu ước
lượng giữa thân và chân, không theo mép quần áo.

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` tăng `0.0055`, từ `0.6853` lên `0.6908`; vì vậy không có hiện tượng
giảm cần giải thích. Hai mươi ảnh bổ sung giúp model thích nghi nhẹ với bố cục, người
và kiểu che khuất của bộ bài; box mAP50-95 lại giảm `0.0078`, cho thấy cải thiện pose
không đồng nghĩa với cải thiện định vị box.

2. Ở mô hình sau fine-tune, `box_mAP50-95 - pose_mAP50-95 = 0.8041 - 0.6908 = 0.1133`.
Model tìm người dễ hơn tìm đúng vị trí các khớp, vì box chỉ cần bao đúng người còn pose
phải đặt đúng 17 điểm dưới các mức OKS nghiêm ngặt hơn.

3. Notebook không lưu output per-image hoặc ảnh dự đoán test trong repository, nên chưa
đủ bằng chứng để chọn một ảnh model đoán sai và phân loại thành lệch nhẹ, đảo trái/phải,
nhầm người hay trượt hẳn. Không suy ra loại lỗi chỉ từ các mAP tổng.

4. Notebook cũng không lưu bảng OKS giữa model và nhãn của tôi theo từng ảnh. Do đó chưa
thể kết luận ảnh nào thấp nhất hoặc ai đúng; cần lưu output của cell so sánh và ảnh
visualize test trước khi kết luận.

5. OKS thấp nhất giữa nhãn của tôi và gold là `train_03.jpg`, người thứ nhất, với `0.8789`.
Không có OKS model-vs-nhãn theo ảnh để kiểm tra nó có trùng ảnh model tệ nhất hay không.
Từ dữ liệu hiện có chỉ có thể nói `train_03` là ảnh nhãn khó hơn, vì có nhiều người chồng
lấn; chưa thể kết luận nguyên nhân từ phía model.

## 5. Một rule evidence tôi đã dùng

Trong `train_01.jpg`, người thứ nhất có `left_knee`, `right_knee`, `left_ankle` và
`right_ankle` ở trạng thái `v=0`. Phần chân dưới của người này không còn nằm trong vùng
ảnh/không có đủ phần chân để đặt vị trí khớp đáng tin cậy, trong khi các khớp phía trên
vẫn được gán. Vì vậy các khớp này là Outside (`v=0`), không phải Occluded (`v=1`); nếu
khớp còn trong khung nhưng bị vật thể che thì phải đặt điểm ước lượng và dùng `v=1`.
