# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: **Lê Danh Trung**   Nhóm: **2A202602317**   Ngày: **16/09/2026**

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

<!-- Lấy số từ reports/visibility_report.md hoặc outputs/visibility_report.json sau Chặng 4.
Số ảnh phải là 20; số skeleton là tổng số người trong 20 ảnh. Thời gian trung bình = tổng
thời gian gán / 20. -->

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 320 / 138 / 35 |
| Thời gian trung bình mỗi ảnh | Không ghi nhận trong quá trình gán |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear`: 19/29, tương đương 66%
2. `right_ear`: 13/29, tương đương 45%
3. `left_wrist`: 11/29, tương đương 38%

Ba khớp này có tỷ lệ bị che cao, nhưng “hay bị che” không hoàn toàn đồng nghĩa với “khó xác định vị trí giải phẫu”. Trong các ảnh đã gán, tai thường không nhìn thấy trực tiếp do góc quay của đầu hoặc bị phần đầu che, còn cổ tay có thể bị thân người, vật đang cầm hoặc người khác che khuất. Khi phần cơ thể chứa khớp vẫn còn trong khung, tôi ước lượng vị trí từ các phần cơ thể liền kề và đặt `v=1`, thay vì loại bỏ keypoint bằng `v=0`.

## 2. Chấm với gold

<!-- Lấy hai cột từ outputs/eval_vs_gold.json: một lần ngay khi protected release mở và một
lần sau rework. Đếm số phần tử trong từng danh sách lỗi, không tự làm tròn. -->

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.962 | 0.962 |
| OKS@0.50 | 1.000 | 1.000 |
| OKS@0.75 | 1.000 | 1.000 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 | 0 |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

<!-- Mỗi dòng phải có: tên ảnh + người thứ mấy + keypoint + thao tác sửa. Không viết “đã sửa
lại một số lỗi”. -->

- `train_13.jpg`, người thứ 3 trong nhãn của tôi, keypoint `right_wrist`: công cụ phát hiện lệch nhẹ 16 px, tương đương 1,3 lần bán kính dung sai; tôi đã kiểm tra finding nhưng giữ nguyên vì đây là lỗi nhẹ, ít hại và không có skeleton nào bị yêu cầu rework.
- `train_13.jpg`, người thứ 3 trong nhãn của tôi, các keypoint có finding `gold_khong_gan_nhan`: không sửa, vì gold đặt `v=0` nên các khớp này bị loại khỏi OKS và GUIDE yêu cầu bỏ qua loại finding này.
- Toàn bộ 20 ảnh, các keypoint thuộc 48 finding `co_khac_gold`: không sửa, vì vị trí vẫn đúng, khác biệt chỉ ở cờ visibility và không bị trừ OKS.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?

<!-- Nếu không có lỗi, ghi rõ “Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh.” -->

Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh. `check_pose_labels.py` từng cảnh báo nghi ngờ tại `train_02.jpg`, người thứ nhất, ở cặp vai và cặp hông, nhưng kết quả `eval_vs_gold.json` không có finding `dao_trai_phai`; vì vậy cảnh báo hình học này không được tính là một lỗi đảo trái/phải đã được gold xác nhận.

## 3. Kiểm chéo

Bạn cùng nhóm: ______

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| | | | | |
| | | | | |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

<!-- Viết một rule kiểm chứng được: điều kiện nhìn thấy/căn cứ vị trí → chọn v=1 hoặc v=0.
Không chỉ ghi “cẩn thận hơn khi gán”. -->

-

## 4. Model

<!-- Chép số từ outputs/eval_model.json sau Chặng 6. “Chênh” = sau fine-tune trừ baseline;
đây là quan sát trên tập test, không phải chất lượng sản phẩm. -->

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể. Một con số thấp không tự chứng minh nhãn sai;
> kiểm lại bằng bằng chứng thị giác và kết quả gold.

1. `pose_mAP50-95` tăng từ 0.6853 lên 0.6908, tức tăng 0.0055. Vì chỉ số không giảm, trường hợp giả định “nếu nó giảm” không xảy ra trong lần chạy này. Mức tăng nhỏ cho thấy fine-tune trên 20 ảnh giúp model thích nghi nhẹ với các tư thế và trường hợp che khuất trong tập train, nhưng 20 ảnh chưa đủ để kết luận model được cải thiện tổng quát. Đồng thời, `box_mAP50-95` giảm 0.0078, từ 0.8119 xuống 0.8041, cho thấy sự thích nghi nhỏ về pose đi kèm suy giảm nhẹ về định vị hộp trên tập test.

2. Sau fine-tune, `box_mAP50-95` là 0.8041 và `pose_mAP50-95` là 0.6908, chênh 0.1133. Model tìm vùng chứa người dễ hơn định vị chính xác các khớp, vì bounding box chỉ cần bao quanh cơ thể, còn pose phải xác định đồng thời 17 keypoint, phân biệt trái/phải và xử lý các khớp bị che hoặc ra ngoài khung.

3. Trong các ảnh dự đoán đã kiểm tra trực quan, tôi chưa có đủ bằng chứng đối chiếu với nhãn test để gọi chắc chắn một lỗi theo bốn loại của slide 43. Ảnh dự đoán có hai người mà tôi đã kiểm tra cho thấy model phát hiện đủ hai người và không có dấu hiệu rõ của lỗi đảo trái/phải, nhầm người hoặc trượt hẳn. Vì vậy, tôi không tự gán tên một lỗi khi chưa có ảnh prediction chồng với nhãn chuẩn để đối chiếu.

4. Ảnh có OKS thấp nhất giữa nhãn của tôi và model là `train_13`, với OKS 0.595. Chỉ số này cho thấy model và nhãn bất đồng mạnh nhất tại ảnh đó, nhưng không tự chứng minh bên nào sai. Khi so nhãn của tôi với gold, skeleton thấp nhất cũng thuộc `train_13.jpg`: gold person 1 ghép với người thứ 3 trong nhãn của tôi, đạt OKS 0.9098 và chỉ có một finding `lech_nhe` tại `right_wrist`, lệch 16 px; không có lỗi đảo trái/phải, nhầm người hoặc trượt hẳn. Vì bảng model không chỉ rõ giá trị 0.595 thuộc người nào trong ba người của ảnh, tôi chỉ kết luận rằng gold ủng hộ chất lượng tổng thể của nhãn trong ảnh này, không khẳng định hai OKS trên thuộc cùng một skeleton.

5. Có. Ảnh tôi gán có OKS thấp nhất khi so với gold là `train_13.jpg`, với OKS thấp nhất 0.9098; `train_13` cũng là ảnh model bất đồng mạnh nhất với nhãn của tôi, với OKS 0.595. Ảnh này có ba người ở các độ sâu khác nhau và có sự che khuất giữa các cơ thể, nên việc ghép đúng người và định vị các keypoint bị che khó hơn các ảnh đơn giản. Tuy nhiên, bằng chứng gold chỉ xác nhận một lỗi nhẹ tại `right_wrist`, vì vậy không thể quy toàn bộ mức bất đồng của model cho lỗi nhãn.

## 5. Một rule evidence bạn đã dùng

Chọn một keypoint trong ảnh core mà bạn phải quyết định giữa `v=1` và `v=0`. Nêu ảnh, người,
khớp, bằng chứng nhìn thấy và lý do chọn trạng thái đó trong 3-5 câu.

<!-- Cấu trúc gợi ý: (1) train_XX + người thứ mấy + keypoint; (2) căn cứ thị giác như phần cơ
thể liền kề, trang phục hoặc vật che; (3) vì sao khớp còn trong khung (v=1) hay đã ra khỏi
khung (v=0). -->

Tại `train_13.jpg`, người thứ 3 trong nhãn của tôi, keypoint `left_wrist` được đặt `v=1`. Kết quả nhãn cho thấy cổ tay trái có tọa độ trong ảnh thay vì `(0, 0)`, còn ảnh trực quan cho thấy vùng tay bị che một phần trong cảnh có nhiều người. Vị trí cổ tay vẫn có thể được ước lượng từ vai, khuỷu tay và hướng của cẳng tay, đồng thời phần tay vẫn nằm trong khung ảnh. Vì vậy, tôi giữ keypoint ở vị trí giải phẫu ước lượng và chọn `v=1`, không chọn `v=0`.
