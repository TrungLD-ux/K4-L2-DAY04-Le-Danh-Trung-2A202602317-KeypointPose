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
| 2 | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông | ☐ | Đã kiểm tra trực quan toàn bộ ảnh được công cụ cảnh báo. Không thấy cắt chéo bất thường rõ ràng ở `train_02`, `train_04`, `train_06`, `train_09`, `train_10`, `train_11` và `train_14`; riêng `train_13`, người thứ 2, vẫn có cảnh báo hình học về cặp vai trong khu vực có các skeleton chồng lấn nên chưa thể đánh dấu đạt. |
| 3 | Không có xương nào kéo dài sang một cơ thể khác | ☑ | Đã kiểm tra trực quan các ảnh cảnh báo `train_02`, `train_04`, `train_06`, `train_09`, `train_10`, `train_11`, `train_13` và `train_14`; không phát hiện đường xương nối sang cơ thể khác. |
| 4 | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0` | ☐ | `train_02`, `train_06`, `train_09`, `train_10`, `train_11` và `train_14` có các bộ phận bị phương tiện, vật thể hoặc cơ thể khác che trong khung. Cần mở từng keypoint đang có `v=0` trong CVAT để phân biệt chính xác `Occluded` với `Outside`. |
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ☐ | Bài đối chiếu có 71 keypoint `v=0`. `train_04` có các phần chân thực sự bị cắt ở mép ảnh, nhưng những ảnh khác cho thấy một số bộ phận có thể chỉ bị vật thể che trong khung. Do đó, chưa thể xác nhận toàn bộ keypoint `v=0` đều đúng. |
| 6 | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý) | ☑ | Không phát hiện điểm `v=2` nằm ở vị trí vô lý trong toàn bộ các ảnh được công cụ cảnh báo và đã kiểm tra trực quan. |
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
| `train_13.jpg` | 2 | `left_shoulder / right_shoulder` | Công cụ cảnh báo cặp vai có dấu hiệu đảo trái/phải so với hai mắt. Khu vực này có các skeleton chồng lấn nên chưa thể xác nhận chắc chắn chỉ bằng ảnh visualize. | Mở đúng skeleton người thứ 2 trong CVAT, xác định trái/phải theo cơ thể và chỉ đổi cặp vai nếu vị trí từng keypoint xác nhận đã bị đảo. |
| `train_06.jpg` | 1 | Các keypoint đang có `v=0` | Có 7 keypoint `v=0`; ảnh trực quan cho thấy phần thân dưới và các chi thể bị phương tiện che đáng kể. | Kiểm tra từng keypoint với mép ảnh; dùng `v=1` nếu còn trong ảnh nhưng bị che và chỉ giữ `v=0` nếu keypoint thực sự ngoài ảnh. |
| `train_14.jpg` | 2 | Các keypoint đang có `v=0` | Có 4 keypoint `v=0`; ảnh trực quan cho thấy phần chân vẫn xuất hiện trong khung nhưng bị che một phần bởi tư thế và đối tượng ở tiền cảnh. | Mở từng keypoint trong CVAT; chuyển sang `v=1` và đặt điểm ước lượng nếu khớp còn trong khung, chỉ giữ `v=0` nếu khớp thực sự ngoài mép ảnh. |

## Hai câu kết luận

- Lỗi lặp đi lặp lại nhiều nhất của bài này: Công cụ đưa ra 9 cảnh báo về việc sử dụng `v=0` tại những skeleton được nhận định là nằm gọn trong ảnh. Kiểm tra trực quan cho thấy dữ liệu có cả trường hợp bộ phận thực sự bị cắt bởi mép ảnh và trường hợp bộ phận bị vật thể che trong khung.
- Nó là lỗi **thao tác** hay lỗi **guideline chưa rõ**? Khác biệt chủ yếu xuất phát từ việc guideline phân biệt `Outside` (`v=0`) với `Occluded` (`v=1`) chưa được áp dụng nhất quán. Từng keypoint cần được đối chiếu với mép ảnh và phần cơ thể liền kề trước khi xác định trường hợp cụ thể là lỗi thao tác.
