# Tái kiểm sau sửa — trạng thái cuối

Ngày 2026-11-18. Phạm vi: 5 ảnh QC đã sửa; Codex hỗ trợ đối chiếu ảnh và dữ liệu. Đây không phải QC độc lập bởi một người gán nhãn khác, không thay phiếu QC ban đầu và không đại diện toàn bộ 24 ảnh.

## 1. Nguồn và số lượng

- Bộ cuối: `release/final_det.coco.json`, 24 ảnh/548 đối tượng.
- Bản sửa: `labels/qc_fixed.json`, 5 ảnh/98 đối tượng.
- Trước sửa: `qc_sample.csv`, 5 ảnh/109 đối tượng; `qc_review.csv` ghi 16 lỗi trước sửa.
- Các ID trong ảnh vẽ từ release, phiếu QC ban đầu và CVAT có thể khác nhau. Đối chiếu bằng ảnh, lớp và tọa độ thay vì chỉ số ID.

## 2. Các điểm đã đối chiếu

| Ảnh | Trạng thái cuối |
|---|---|
| img_003 | Khung bộ phận/thừa đã bị loại, xe mép phải đổi bicycle theo xác nhận học viên. Khung xe chở trái cây mới [243.1,109.8,501.0,514.2] đã lấy phần mái bên trái từng bị cắt. |
| img_006 | Khung car thừa trước đó đã bị loại; chưa thấy lỗi rõ ràng mới trong lượt xem tổng thể. |
| img_014 | Có thêm 2 person. Học viên xác nhận hai khung từng nghi trùng là hai người khác nhau, giữ cả hai. |
| img_031 | Khung person thừa trước đó đã bị loại; chưa thấy lỗi rõ ràng mới trong lượt xem tổng thể. |
| img_035 | Đã mở một khung bao mui đến bánh xe xích lô và loại khung nhỏ chỉ bao mui; chỉ còn một three_wheeler cho xe giữa đường. |

## 3. Giới hạn và trạng thái

Các điểm cụ thể đã nêu trong lượt tái kiểm đã được sửa hoặc xác nhận. Chưa thấy lỗi rõ ràng mới trong lượt rà tổng thể này, nhưng không coi là tỷ lệ lỗi 0%, không khẳng định mọi biên khung/lớp đều đúng và chưa rà toàn bộ 24 ảnh theo cách này.

Giữ số QC trước sửa 16/109=14.7%, 5/5 ảnh có lỗi và bẫy ban đầu 8/12. Bẫy sau sửa có hướng dẫn là 12/12. Quyết định của học viên vẫn **Rework**, chưa xác nhận Release. Lịch sử chi tiết nằm trong các commit của repo và `qc_observation_notes.txt`; không trình bày trạng thái cũ như lỗi còn tồn tại ở bản cuối.
