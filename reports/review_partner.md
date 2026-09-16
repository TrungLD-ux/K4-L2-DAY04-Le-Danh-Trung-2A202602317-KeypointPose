# Kết quả kiểm chéo nhãn Keypoint & Pose

Người gán: **NGUYỄN LÊ THẾ ANH**  
Người kiểm: **Lê Danh Trung**  
Ngày: **16/09/2026**

## Kết quả kiểm tra

`check_pose_labels.py` đã đọc đủ 20/20 file nhãn và 29 skeleton. Bộ nhãn đối chiếu đạt định dạng YOLO Pose, với tổng số cờ visibility:

- `v=2`: 314
- `v=1`: 108
- `v=0`: 71

Công cụ đưa ra 10 cảnh báo không chặn nộp, gồm 9 cảnh báo liên quan đến việc sử dụng `v=0` tại những skeleton được nhận định là nằm gọn trong ảnh và 1 cảnh báo nghi ngờ đảo trái/phải.

## Lỗi và cảnh báo tìm được

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| `train_13.jpg` | 2 | `left_shoulder / right_shoulder` | Công cụ cảnh báo cặp vai có dấu hiệu đảo trái/phải so với hai mắt. Khu vực này có các skeleton chồng lấn nên chưa thể xác nhận chắc chắn chỉ bằng ảnh visualize. | Mở đúng skeleton người thứ 2 trong CVAT, xác định trái/phải theo cơ thể và chỉ đổi cặp vai nếu vị trí từng keypoint xác nhận đã bị đảo. |
| `train_06.jpg` | 1 | Các keypoint đang có `v=0` | Có 7 keypoint `v=0`; ảnh trực quan cho thấy phần thân dưới và các chi thể bị phương tiện che đáng kể. | Kiểm tra từng keypoint với mép ảnh; dùng `v=1` nếu còn trong ảnh nhưng bị che, chỉ giữ `v=0` nếu keypoint thực sự ngoài ảnh. |
| `train_14.jpg` | 2 | Các keypoint đang có `v=0` | Có 4 keypoint `v=0`; ảnh trực quan cho thấy phần chân vẫn xuất hiện trong khung nhưng bị che một phần. | Mở từng keypoint trong CVAT; chuyển sang `v=1` và đặt điểm ước lượng nếu khớp còn trong khung, chỉ giữ `v=0` nếu khớp thực sự ngoài mép ảnh. |

## So sánh visibility

| Khớp | Bài của tôi | Bài đối chiếu | Lệch |
| --- | ---: | ---: | ---: |
| `left_ear` | 66% | 45% | 21 điểm phần trăm |
| `right_knee` | 28% | 14% | 14 điểm phần trăm |
| `nose` | 21% | 10% | 10 điểm phần trăm |
| `left_wrist` | 38% | 28% | 10 điểm phần trăm |
| `left_knee` | 24% | 14% | 10 điểm phần trăm |

## Kết luận

Lỗi hoặc cảnh báo lặp lại nhiều nhất là việc dùng `v=0` tại những skeleton được công cụ nhận định là nằm gọn trong ảnh. Kiểm tra trực quan cho thấy dữ liệu có cả trường hợp bộ phận thực sự bị cắt bởi mép ảnh và trường hợp bộ phận bị vật thể che trong khung.

Khác biệt chủ yếu xuất phát từ cách áp dụng guideline khi phân biệt `Outside` (`v=0`) với `Occluded` (`v=1`). Từng keypoint phải được đối chiếu với mép ảnh và phần cơ thể liền kề trước khi kết luận là lỗi thao tác.
`
