# Thông tin học viên

- Họ và tên: Lê Ngọc Dũng
- MSHV: 2A202603103
- Lớp (L2 / L3 / khác): L2A
- Tài khoản GitHub: bachdungbiu3010-art
- Repo bài nộp (URL): https://github.com/bachdungbiu3010-art/K5-L2A-DAY08-2A202603103-LeNgocDung-AIPrelabel
- Commit chốt bài (hash): 385e0e5870ca1d0486b4871cd228af94bde5e7dd (mốc hoàn thiện báo cáo và dữ liệu; commit sau chỉ ghi lại mốc này)
- Máy: Windows; Intel UHD Graphics (không GPU NVIDIA CUDA); model chạy Colab Tesla T4
- CVAT (Docker): v2.82.0

## Kết quả tóm tắt

- Máy: accepted / edited / deleted: 465 / 19 / 37 (mẫu số: 521 box máy, lượt sửa trước QC); added: 64 (mẫu số: 548 đối tượng trong phép đo)
- So với gold: precision / recall của **máy** 71.6% (53/74) / 72.6% (53/73)
- Bắt bẫy: lượt đầu 8/12 = 66.7%; sau sửa có hướng dẫn 12/12 = 100%
- Thời gian: gán tay 8 phút/ảnh; duyệt có pre-label 1.5 phút/ảnh ở lượt đầu; chưa gồm các lượt sửa
- Lỗi QC: 100% theo ảnh (5/5, trước sửa), 14.7% theo đối tượng (16/109, trước sửa)
- **Quyết định:** Rework — lý do một dòng: QC trước sửa có lỗi ở 5/5 ảnh mẫu; các điểm đã nêu trong 5 ảnh đã được sửa/đối chiếu, nhưng chưa tái kiểm đầy đủ các ảnh ngoài mẫu nên chưa xác nhận Release

- Bộ nhãn cuối: 24 ảnh / 548 đối tượng; 5 ảnh sửa QC có 98 đối tượng.
- AI có ích theo trải nghiệm cá nhân: giúp nhanh hơn nhưng phải kiểm tra kỹ. Lượt đầu còn lỗi vì tôi kiểm tra chưa kỹ và chủ quan tin AI.
- Tái kiểm: Codex hỗ trợ xem lại 5 ảnh; không thay kết quả QC ban đầu và không coi là kiểm tra độc lập toàn bộ 24 ảnh.
- Trạng thái LMS: chưa xác minh đã nộp.
