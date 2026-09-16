# Mini guideline - nhóm: G04  |  người gán: Lê Danh Trung  |  ngày: 16/09/2026

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
| Hông bị trang phục che nhưng phần thân và đùi vẫn còn trong ảnh | Chọn `v = 1` và đặt điểm ở vị trí giải phẫu ước lượng | Khớp hông còn trong khung; vị trí được ước lượng từ trục thân, hai vai, hướng của đùi và đường viền trang phục. |
| Tai bị tóc, mũ hoặc góc quay của đầu che | Chọn `v = 1` nếu vị trí giải phẫu của tai vẫn còn trong khung | Tai không nhìn thấy trực tiếp nhưng vị trí có thể được ước lượng từ mắt, đường viền đầu và hướng quay của khuôn mặt. |
| Đầu gối hoặc mắt cá chân đã ra ngoài mép ảnh | Chọn `v = 0` và đánh dấu `Outside` cho keypoint tương ứng | Phần cơ thể chứa keypoint không còn trong ảnh nên không có bằng chứng thị giác để đặt tọa độ trong khung. |
| Cổ tay bị vật đang cầm, thân người hoặc bộ phận khác che | Chọn `v = 1` và đặt điểm ở vị trí ước lượng | Cổ tay còn trong khung; vị trí được suy ra từ khuỷu tay, hướng cẳng tay và phần bàn tay còn quan sát được. |
| Hai người chồng lên nhau | Hoàn thành riêng từng skeleton; keypoint bị người khác che nhưng còn trong ảnh chọn `v = 1` | Làm riêng từng người giúp tránh nối keypoint sang cơ thể bên cạnh và vẫn giữ thông tin của khớp bị che. |
| Người xuất hiện nhỏ trong ảnh | Vẫn gán đủ 17 điểm nếu người đó thuộc phạm vi người cần gán của task | Không tự bỏ skeleton chỉ vì kích thước nhỏ; trạng thái từng keypoint vẫn được quyết định theo bằng chứng trong ảnh. |
| Tâm keypoint của tai bị tóc, mũ, góc quay đầu hoặc vật cản che nhưng vị trí tai vẫn trong khung | Chọn `v = 1` và đặt điểm giải phẫu ước lượng | Keypoint không nhìn thấy trực tiếp nhưng vẫn tồn tại trong ảnh; chỉ chọn `v=2` khi tâm keypoint nhìn thấy rõ và chọn `v=0` khi tai thực sự ra ngoài mép ảnh. |

Ảnh mẫu minh họa các luật trên:

![Ví dụ người chồng lên nhau và keypoint bị che](assets/guide/train_13_overlap.png)

![Ví dụ keypoint ở chân ra ngoài mép ảnh](assets/guide/train_04_outside.png)

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_13.jpg`, người thứ `2`, khớp `left_ankle / right_ankle`

- Mơ hồ ở chỗ nào: Khi chọn skeleton người thứ 2 trong CVAT, phần chân dưới không cung cấp đủ bằng chứng thị giác để đặt hai mắt cá chân trong khung ảnh.
- Bạn quyết thế nào: Đánh dấu `Outside` cho `left_ankle` và `right_ankle`, tương ứng `v = 0`; không tự đặt tọa độ ước lượng trong ảnh.
- Vì sao: File nhãn xuất ra ghi nhận cả hai keypoint ở trạng thái `v = 0` với tọa độ `(0, 0)`, phù hợp với quyết định rằng hai mắt cá chân đã ra ngoài mép ảnh.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu đặt điểm ước lượng cho keypoint nằm ngoài khung, model có thể học tọa độ không có bằng chứng thị giác và tạo dự đoán không ổn định gần mép ảnh.

Ảnh mẫu:

![train_13, người thứ 2, hai mắt cá chân Outside](assets/guide/train_13_person_2_ankles.png)

### Ca 2 - ảnh `train_02.jpg`, người thứ `1`, khớp `right_hip`

- Mơ hồ ở chỗ nào: `right_hip` không nhìn thấy trực tiếp, nhưng phần thân và phần chân tương ứng vẫn còn trong ảnh.
- Bạn quyết thế nào: Đặt `right_hip` tại vị trí giải phẫu ước lượng và chọn `v = 1` (`Occluded`).
- Vì sao: Keypoint vẫn nằm trong khung; vị trí được suy ra từ trục thân, vai và hướng của đùi. File nhãn xuất ra ghi nhận `right_hip` có tọa độ trong ảnh và `v = 1`.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu chọn `v = 0`, model sẽ mất tín hiệu học đối với một khớp vẫn tồn tại trong ảnh nhưng không nhìn thấy trực tiếp.

Ảnh mẫu:

![train_02, người thứ 1, right_hip Occluded](assets/guide/train_02_person_1_right_hip.png)

### Ca 3 - ảnh `train_04.jpg`, người thứ `1`, khớp `left_wrist / right_wrist`

- Mơ hồ ở chỗ nào: Hai cổ tay của cùng một skeleton có trạng thái khác nhau; cần quyết định riêng từng keypoint thay vì áp dụng một cờ cho cả hai.
- Bạn quyết thế nào: Đặt `left_wrist` tại vị trí ước lượng và chọn `v = 1`; đánh dấu `Outside` cho `right_wrist`, tương ứng `v = 0`.
- Vì sao: File nhãn xuất ra ghi nhận `left_wrist` có tọa độ trong ảnh với `v = 1`, còn `right_wrist` có tọa độ `(0, 0)` với `v = 0`.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu dùng cùng một trạng thái cho cả hai cổ tay mà không xét bằng chứng riêng, model có thể mất một keypoint còn trong ảnh hoặc học tọa độ giả cho keypoint đã ra ngoài ảnh.

Ảnh mẫu:

![train_04, người thứ 1, trạng thái hai cổ tay](assets/guide/train_04_person_1_wrists.png)

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `left_ear` (bạn `66%` / đối chiếu `45%`), lệch `21` điểm phần trăm.
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Bảng so sánh cho thấy hai bên chưa áp dụng hoàn toàn giống nhau tiêu chí phân biệt `v=1` và `v=2` đối với `left_ear`. Chỉ riêng tỷ lệ thống kê chưa đủ để kết luận một bên gán sai; cần đối chiếu hình ảnh tại các trường hợp tai bị tóc, góc quay đầu hoặc vật cản che.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: Nếu tâm keypoint của tai không nhìn thấy trực tiếp do tóc, mũ, góc quay đầu hoặc vật cản nhưng vị trí tai vẫn còn trong khung ảnh, đặt điểm tại vị trí giải phẫu ước lượng và chọn `v=1`. Chỉ chọn `v=2` khi tâm keypoint nhìn thấy trực tiếp; chỉ chọn `v=0` khi vị trí tai thực sự ra ngoài mép ảnh.
