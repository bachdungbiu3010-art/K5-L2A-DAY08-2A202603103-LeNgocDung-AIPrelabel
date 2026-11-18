# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603103 - Lê Ngọc Dũng

Chỉ ghi phần bổ sung v1.0. Các quyết định dưới đây do học viên đưa ra, được hỗ trợ diễn đạt.

## 1. Câu hỏi mở đã chốt

| Câu hỏi | Quyết định của học viên | Ảnh ví dụ | case_id |
|---|---|---|---|
| Mục 6.3: mask xe chở trái cây có gồm hàng nhô ra không? | Mask bao thân xe, thùng gắn cố định và cả trái cây chở trên xe nhô ra. Quyết định áp dụng cho ca xe chở trái cây; chưa mở rộng cho mọi loại hàng. | img_003.jpg | CAL-02 |

Bổ sung mục 4.2 cho phạm vi mask của ca này. Quy tắc box tại mục 4.1.5 vẫn giữ nguyên: không tính phần hàng nhô ra quá khoảng 20% chiều dài xe. Đây là quyết định cho guideline; chưa tạo mask SAM cho img_003 và chưa xác minh việc áp dụng trên mask này. Các câu hỏi mở khác chưa chốt.

## 2. Ca khó mới

| Ảnh | Tình huống | Quyết định của học viên | Quy tắc bổ sung | case_id |
|---|---|---|---|---|
| img_013.jpg | SAM 2.1 còn tô vào đầu và chân người lái | Ghi chú lỗi, điều chỉnh điểm gợi ý/khung, chạy lại và kiểm tra | Bổ sung mục 4.2: mask xe dính người phải được ghi nhận; điều chỉnh prompt và chạy lại, đối chiếu ảnh gốc hoặc mask nhị phân trước khi chấp nhận. Chạy lại chưa chứng minh kết quả đã đúng. | CAL-01 |

| img_013.jpg | YOLO26 gán xe tuk-tuk thành car | Tìm hiểu nguyên nhân, gán lại nhãn và ghi chi tiết lỗi | Bổ sung quy trình review: đối chiếu xe với label contract, kiểm tra nguyên nhân trước khi kết luận; đổi về three_wheeler khi đúng thực tế và ghi ảnh/model/lỗi. Không chấp nhận lớp chỉ do conf cao. | CAL-03 |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do |
|---|---|---|
| v1.1 | Bổ sung xử lý mask xe lẫn người | CAL-01 |

- CAL-02: bổ sung phạm vi mask cho xe chở trái cây theo quyết định của học viên.

- CAL-03: bổ sung xử lý sai lớp xe ba bánh theo quyết định của học viên.
