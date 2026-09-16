# Kiểm chéo nhãn Keypoint & Pose

- Người gán: **NGUYỄN LÊ THẾ ANH**
- Người kiểm: **Lê Danh Trung**
- Ngày: **16/09/2026**
- Dữ liệu đối chiếu: **20 ảnh, 29 skeleton**

## 1. Kết quả kiểm tra bằng công cụ

Lệnh kiểm tra đã đọc đủ `20/20` file nhãn và `29` skeleton. Bộ nhãn đối chiếu đạt yêu cầu định dạng.

Thống kê visibility của bài đối chiếu:

- `v=2`: 314
- `v=1`: 108
- `v=0`: 71

Công cụ đưa ra 10 cảnh báo cần xem lại nhưng không chặn nộp:

- `train_02.txt`, người 1: có 4 khớp `v=0` trong khi người nằm gọn trong ảnh.
- `train_04.txt`, người 1: có 4 khớp `v=0` trong khi người nằm gọn trong ảnh.
- `train_04.txt`, người 2: có 7 khớp `v=0` trong khi người nằm gọn trong ảnh.
- `train_06.txt`, người 1: có 7 khớp `v=0` trong khi người nằm gọn trong ảnh.
- `train_09.txt`, người 1: có 7 khớp `v=0` trong khi người nằm gọn trong ảnh.
- `train_10.txt`, người 1: có 4 khớp `v=0` trong khi người nằm gọn trong ảnh.
- `train_11.txt`, người 1: có 5 khớp `v=0` trong khi người nằm gọn trong ảnh.
- `train_13.txt`, người 1: có 4 khớp `v=0` trong khi người nằm gọn trong ảnh.
- `train_13.txt`, người 2: cặp vai có dấu hiệu đảo trái/phải so với hai mắt.
- `train_14.txt`, người 2: có 4 khớp `v=0` trong khi người nằm gọn trong ảnh.

Các cảnh báo trên là cảnh báo tự động và cần kiểm tra trực quan trước khi kết luận là lỗi thật.

## 2. Kết quả so sánh visibility

| Khớp | `%v=1` của tôi | `%v=1` của bài đối chiếu | Lệch |
| --- | ---: | ---: | ---: |
| `left_ear` | 66% | 45% | 21 điểm phần trăm |
| `right_knee` | 28% | 14% | 14 điểm phần trăm |
| `nose` | 21% | 10% | 10 điểm phần trăm |
| `left_wrist` | 38% | 28% | 10 điểm phần trăm |
| `left_knee` | 24% | 14% | 10 điểm phần trăm |

## 3. Nhận xét

`left_ear` có chênh lệch `%v=1` lớn nhất. Hai bên có thể đang áp dụng khác nhau tiêu chí phân biệt tai bị che với tai nhìn thấy trực tiếp. Bảng thống kê chưa đủ để kết luận bên nào gán sai.

`right_knee` có chênh lệch lớn thứ hai. Cần kiểm tra riêng các trường hợp đầu gối không nhìn thấy trực tiếp nhưng phần đùi và cẳng chân vẫn nằm trong khung, nhằm phân biệt chính xác `v=1` với `v=0`.

Bài đối chiếu có 71 keypoint `v=0`, cao hơn 35 keypoint `v=0` trong bài của tôi. Các cảnh báo tự động cho thấy một số trường hợp có thể đã dùng `Outside` tại vị trí đáng lẽ cần xem xét `Occluded`; tuy nhiên phải kiểm tra ảnh trực quan trước khi xác nhận lỗi.

## 4. Rule đề xuất sau kiểm chéo

Nếu tâm keypoint của tai không nhìn thấy trực tiếp do tóc, mũ, góc quay đầu hoặc vật cản nhưng vị trí tai vẫn còn trong khung ảnh, đặt điểm tại vị trí giải phẫu ước lượng và chọn `v=1`. Chỉ chọn `v=2` khi tâm keypoint nhìn thấy trực tiếp; chỉ chọn `v=0` khi vị trí tai thực sự ra ngoài mép ảnh.

## 5. Kết luận

Hai bộ nhãn đều có đủ 20 file và 29 skeleton, đồng thời đều đạt định dạng YOLO Pose. Khác biệt chính nằm ở cách sử dụng cờ visibility, đặc biệt tại `left_ear`, `right_knee` và tổng số keypoint `v=0`. Không tự động sửa nhãn chỉ dựa trên bảng chênh lệch; các cảnh báo cần được xác nhận bằng ảnh trước khi thay đổi.