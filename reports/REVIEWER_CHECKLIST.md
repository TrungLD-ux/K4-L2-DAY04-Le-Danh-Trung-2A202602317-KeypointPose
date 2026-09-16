# Reviewer checklist - điền khi kiểm bài người khác

Người gán: **NGUYỄN LÊ THẾ ANH**   Người kiểm: **Lê Danh Trung**   Ngày: **16/09/2026**

Chạy trước khi soi bằng mắt:

```bash
python tools/check_pose_labels.py --images dataset/images/train --labels ban_cung_nhom/dataset/labels/train
python tools/visualize_pose.py --images dataset/images/train --labels ban_cung_nhom/dataset/labels/train --out outputs/vis_partner
python tools/visibility_report.py --labels dataset/labels/train --compare ban_cung_nhom/dataset/labels/train --markdown reports/visibility_compare.md
```

| | Mục kiểm | Đạt? | Ghi chú / ảnh nào |
| --- | --- | --- | --- |
| 1 | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu | ☑ | `check_pose_labels.py` đã đọc đủ 20/20 file nhãn và 29 skeleton. |
| 2 | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông | ☐ | Cần kiểm tra trực quan `train_13.jpg`, người thứ 2; công cụ cảnh báo cặp `left_shoulder/right_shoulder` có dấu hiệu đảo trái/phải so với hai mắt. |
| 3 | Không có xương nào kéo dài sang một cơ thể khác | ☐ | Chưa xác nhận bằng ảnh visualize của bài đối chiếu. |
| 4 | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0` | ☐ | Công cụ đưa ra 9 cảnh báo liên quan đến việc dùng `v=0` tại những skeleton được nhận định là nằm gọn trong ảnh; cần kiểm tra trực quan trước khi kết luận. |
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ☐ | Bài đối chiếu có 71 keypoint `v=0`; cần xem lại `train_02`, `train_04`, `train_06`, `train_09`, `train_10`, `train_11`, `train_13` và `train_14`. |
| 6 | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý) | ☐ | Chưa xác nhận bằng ảnh visualize của bài đối chiếu. |
| 7 | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ☐ | Chưa kiểm tra trực tiếp file export COCO Keypoints của bài đối chiếu. |
| 8 | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]` | ☑ | `check_pose_labels.py` đã đọc thành công 20/20 file nhãn, 29 skeleton và kết luận `ĐẠT định dạng`. |
| 9 | Visibility report đã nộp, và hai bảng đã được đặt cạnh nhau | ☑ | Đã tạo `reports/visibility_compare.md`; hai bộ nhãn đều có 29 skeleton. |
| 10 | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md` | ☐ | Chưa kiểm tra trực tiếp `GUIDELINE_MINI.md` của bài đối chiếu. |
| 11 | `check_pose_labels.py` chạy 0 lỗi | ☑ | Không có lỗi chặn định dạng; công cụ kết luận `ĐẠT định dạng`, nhưng có 10 cảnh báo cần xem lại. |

## Lỗi tìm được

Chép sang `reports/review_partner.md`. Mỗi dòng một lỗi, đủ bốn cột - người sửa phải
mở đúng chỗ đó được mà không cần hỏi lại.

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| `train_13.jpg` | 2 | `left_shoulder / right_shoulder` | Công cụ cảnh báo cặp vai có dấu hiệu đảo trái/phải so với hai mắt; chưa xác nhận bằng mắt. | Mở ảnh visualize và xác định trái/phải theo cơ thể. Chỉ đổi cặp vai nếu hình ảnh xác nhận hai keypoint đã bị đảo. |
| `train_02.jpg` | 1 | Các keypoint đang có `v=0` | Có 4 keypoint `v=0` trong khi công cụ nhận định toàn bộ cơ thể nằm gọn trong ảnh. | Kiểm tra từng keypoint. Nếu khớp bị che nhưng còn trong khung thì đặt điểm ước lượng và chuyển sang `v=1`. |
| `train_06.jpg` | 1 | Các keypoint đang có `v=0` | Có 7 keypoint `v=0` trong khi công cụ nhận định toàn bộ cơ thể nằm gọn trong ảnh. | Chỉ giữ `v=0` nếu phần cơ thể chứa keypoint thực sự ra ngoài mép ảnh; nếu còn trong khung nhưng bị che thì dùng `v=1`. |

## Hai câu kết luận

- Lỗi lặp đi lặp lại nhiều nhất của bài này: Công cụ nhiều lần cảnh báo việc sử dụng `v=0` tại những skeleton được nhận định là nằm gọn trong ảnh; có 9 cảnh báo thuộc nhóm này.
- Nó là lỗi **thao tác** hay lỗi **guideline chưa rõ**? Kết quả kiểm tra cho thấy đây trước hết có thể là lỗi guideline chưa rõ khi phân biệt `Outside` (`v=0`) với `Occluded` (`v=1`); cần kiểm tra trực quan từng trường hợp trước khi kết luận là lỗi thao tác.
`