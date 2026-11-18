# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603103 - Lê Ngọc Dũng

Chỉ bổ sung phần thay đổi so với v1.0. Các quyết định do học viên đưa ra; không thay đổi mục `rules` trong taxonomy.

## 1. Câu hỏi mở đã chốt

| Câu hỏi | Quyết định | Ảnh ví dụ | case_id |
|---|---|---|---|
| Mục 6.3: mask xe chở trái cây có gồm hàng nhô ra không? | Với ca xe chở trái cây, mask gồm thân xe, thùng gắn cố định và trái cây chở trên xe kể cả phần nhô ra. Chưa áp dụng cho mọi loại hàng. | img_003.jpg | CAL-02 |

Bổ sung này áp dụng cho mask ở mục 4.2, không thay quy tắc box mục 4.1.5: không tính phần hàng nhô ra quá khoảng 20% chiều dài xe. Chưa tạo mask img_003 để xác minh việc áp dụng. Những câu hỏi mở khác chưa được chốt.

## 2. Ca khó mới

| Ảnh | Tình huống | Quyết định của học viên | Quy tắc bổ sung | case_id |
|---|---|---|---|---|
| img_013.jpg | SAM 2.1 còn tô vào đầu/chân người lái | Ghi lỗi, điều chỉnh điểm gợi ý/khung, chạy lại và kiểm tra | Bổ sung mục 4.2: đối chiếu ảnh gốc hoặc mask nhị phân trước khi chấp nhận; chạy xong chưa chứng minh mask đúng. | CAL-01 |
| img_013.jpg | YOLO26 gán tuk-tuk thành car | Tìm nguyên nhân, gán lại nhãn và ghi chi tiết lỗi | Đối chiếu label contract; sửa thành three_wheeler khi đúng thực tế, ghi ảnh/model/lỗi; không chấp nhận lớp chỉ vì conf cao. | CAL-03 |

## 3. Lịch sử thay đổi

| Phiên bản | Bổ sung | Căn cứ |
|---|---|---|
| v1.1 | Kiểm tra và thử lại mask xe dính người | CAL-01 |
| v1.1 | Phạm vi mask xe chở trái cây, gồm phần trái cây nhô ra | CAL-02 |
| v1.1 | Quy trình kiểm tra/sửa sai lớp xe ba bánh | CAL-03 |
