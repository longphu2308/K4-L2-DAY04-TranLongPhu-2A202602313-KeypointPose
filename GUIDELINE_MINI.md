# Mini guideline - nhóm: cá nhân  |  người gán: Trần Long Phú  |  ngày: 16/09/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Đặt tại vị trí giải phẫu ước lượng giữa thân và đùi; nếu còn trong ảnh nhưng bị vải che thì `v=1`. | Hông thường không có bề mặt nhìn thấy, nhưng vẫn phải có điểm để giữ cấu trúc cơ thể. ![Ví dụ hông](outputs/vis_train/train_03.jpg) |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Còn thấy hoặc suy ra được vị trí tai trong khung thì đặt điểm và dùng `v=1`; chỉ dùng `v=0` khi tai ra ngoài ảnh. | `left_ear` có 66% `v=1`, cho thấy đây là tình huống bị che phổ biến. ![Ví dụ tai](outputs/vis_train/train_01.jpg) |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp chân nằm ngoài mép ảnh dùng `v=0` và không đặt chấm; các khớp còn trong ảnh vẫn gán bình thường. | Không biến Outside thành Occluded chỉ vì không nhìn thấy phần chân. ![Ví dụ mép ảnh](outputs/vis_train/train_04.jpg) |
| Cổ tay nằm sau tay lái / sau thân mình | Nếu cổ tay còn trong khung nhưng bị vật che thì đặt vị trí ước lượng và dùng `v=1`. | Khớp bị che khác khớp đã ra ngoài ảnh. ![Ví dụ cổ tay](outputs/vis_train/train_10.jpg) |
| Hai người chồng lên nhau | Hoàn thành từng người riêng; dùng vai, hông và hướng nối xương để giữ đúng người, không lấy điểm của người phía trước. | `train_03` có nhiều người chồng lấn nên phải kiểm tra đường nối trước khi tinh chỉnh pixel. ![Ví dụ chồng lấn](outputs/vis_train/train_03.jpg) |
| Người nhỏ đến mức nào thì không gán nữa | Không bỏ người nếu vẫn nhận ra đủ một cơ thể; bộ dữ liệu này vẫn gán đủ 17 điểm, điểm khó dùng thì gắn cờ. | Không tự đặt ngưỡng kích thước ngoài quy định của bài. ![Ví dụ người nhỏ](outputs/vis_train/train_13.jpg) |

Với mỗi luật, chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu, không phải
một câu văn chung chung.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_01`, người thứ `1`, khớp `left_ankle`

- Mơ hồ ở chỗ nào: Phần chân dưới nằm sát/ngoài mép ảnh nên không thấy rõ mắt cá.
- Bạn quyết thế nào: Chọn `v=0`, không đặt chấm.
- Vì sao: Khớp đã ra ngoài vùng ảnh; đây là Outside, không phải khớp còn trong ảnh bị che.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Model sẽ học một vị trí mắt cá giả ở ngoài khung.

### Ca 2 - ảnh `train_03`, người thứ `1`, khớp `left_wrist`

- Mơ hồ ở chỗ nào: Nhiều người và vật thể chồng lên nhau, cổ tay bị thân người/đồ vật che.
- Bạn quyết thế nào: Đặt điểm theo trục cẳng tay và chọn `v=1` nếu điểm còn trong ảnh.
- Vì sao: Cẳng tay vẫn cho căn cứ hình học và khớp không ra khỏi mép ảnh.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Model sẽ coi cổ tay bị che là không tồn tại và học pose bị cụt.

### Ca 3 - ảnh `train_04`, người thứ `2`, khớp `right_hip`

- Mơ hồ ở chỗ nào: Hông nằm dưới quần áo và người thứ hai chồng lên người thứ nhất.
- Bạn quyết thế nào: Ước lượng tại giao điểm thân và đùi của đúng người thứ hai, dùng `v=1`.
- Vì sao: Hông còn nằm trong khung; việc không thấy bề mặt khớp không có nghĩa là Outside.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Model sẽ học hông lệch sang người bên cạnh hoặc mất một nửa cấu trúc chân.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: Chưa xác định (chưa có visibility report của bạn cùng nhóm).
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Chưa đủ dữ liệu để phân biệt.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: Khớp còn trong khung nhưng bị che luôn giữ
	điểm ước lượng và dùng `v=1`; chỉ dùng `v=0` khi vị trí khớp nằm ngoài mép ảnh.
