# Tái kiểm sau sửa — có AI hỗ trợ

Ngày: 2026-11-18. Người/công cụ kiểm tra: Codex (AI), không phải lượt kiểm tra độc lập của một người gán nhãn khác. Phạm vi: 5 ảnh QC đã sửa, không phải toàn bộ 24 ảnh. Không thay phiếu QC ban đầu.

## Nguồn

- `release/final_det.coco.json`: 24 ảnh, 549 đối tượng. Các ảnh được xem: 003, 006, 014, 031, 035 (99 đối tượng).
- Đối chiếu ảnh gốc và ảnh vẽ lại từ đúng bộ release. ID ở đây thuộc bộ release, có thể khác ID CVAT hoặc phiếu QC ban đầu.
- Không thay đổi nhãn trong lượt tái kiểm này.

## Kiểm tra tự động

- Bộ release qua `validate_labels.py --expect batch`: đủ 24 ảnh, không có lỗi hoặc cảnh báo.
- Đã xác nhận bản sửa QC có hai person bổ sung ở img_014; việc này không chứng minh đã tìm đủ mọi người.

## Kiểm tra bằng hình ảnh

| Ảnh | Kết quả lượt xem |
|---|---|
| img_003 | Các khung bộ phận/thừa cũ đã bị loại, vật mép phải đã đổi bicycle. Cần kiểm tra khung xe chở trái cây #20 có thiếu mái bạt gắn liền không. |
| img_006 | Khung car cũ đã bị loại. Chưa phát hiện lỗi mới đủ chắc chắn trong lượt xem tổng thể; cảnh tối/che khuất vẫn cần người học đối chiếu, không coi là chứng nhận sạch lỗi. |
| img_014 | Hai person bổ sung có mặt. Nghi khung person #159 trùng phần đầu của #149; cần phóng to và xác nhận trước khi xóa. |
| img_031 | Khung person bị loại trước đó không còn. Chưa phát hiện lỗi mới đủ chắc chắn trong lượt xem tổng thể; không coi là chứng nhận sạch lỗi. |
| img_035 | Phát hiện xe xích lô giữa đường có hai khung three_wheeler #531 và #532 chỉ bao từng phần trên/dưới của cùng một xe. Cần một khung cho toàn bộ phần xe nhìn thấy. |

Chi tiết: `recheck_ai_findings.csv`. Các nghi vấn chưa xác nhận không được tự tính thành lỗi chắc chắn. Không tính tỷ lệ lỗi mới hoặc báo 0 lỗi sau sửa từ lượt xem này.

## Trạng thái

Có ít nhất một lỗi cần sửa và hai điểm cần đối chiếu. Chưa có căn cứ nâng quyết định của học viên từ Rework lên Release. Giữ nguyên số liệu QC trước sửa 16/109 và 5/5; ghi riêng tái kiểm có AI hỗ trợ.

## Cập nhật bản QC_Review ngày 2026-11-18, 12:20

- File mới thực sự có sửa, dù vẫn 99 annotations: img_003 mở khung xe lên vùng mái; img_035 mở một khung xích lô lên vùng mui.
- img_035 vẫn còn hai khung three_wheeler cho cùng xe: khung nhỏ bbox [745.55,428.01,68.98,34.25] và khung lớn [747.85,428.1,64.18,108.4]. Cần bỏ khung nhỏ nếu khung lớn đã bao đủ xe.
- img_014 không thay đổi hình học hoặc lớp so với bản QC trước. Nghi vấn trùng người chưa được xác nhận là đã giải quyết; không tự xóa.
- Nhãn các ảnh 006 và 031 không thay đổi. Bộ sửa mới vẫn 5 ảnh/99 annotations; Rework được giữ nguyên.
