# Báo cáo Day 08 — 2A202603103 - Lê Ngọc Dũng

> Một file duy nhất cho cả bài (đã gộp recipe, QC và ghi chú cá nhân). Mọi con số kèm **mẫu số** và nguồn (file nào). Viết bằng lời của bạn.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Nguồn và cách dùng |
|---|---|---|---|
| person / bicycle / motorbike / car / bus / truck | YOLO26m | 0.30 | r01/r02 trong model_log.csv là các lượt thử; threshold_sweep.md không gợi ý ngưỡng riêng đạt mục tiêu cho các lớp này. bus/truck có quá ít vật gold. |
| three_wheeler | YOLOE-s; prompt tuk-tuk, cyclo, cargo tricycle | 0.15 | r03 là lượt thử; threshold_sweep.md gợi ý 0.15 trên 7 vật gold, recall 100%, precision 78%. |

Lệnh gộp đã chạy trên máy:

```text
python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "three_wheeler=0.15" --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json
```

- SAM dùng SAM 2.1 (`sam2.1_b.pt`) theo lựa chọn khuyến nghị của tài liệu. Chạy trên GPU T4, các lần thử và hạn chế được ghi tại mục 5; chưa thử SAM 1/3 để so sánh.
- Theo quan sát của học viên, img_013 có xe ba bánh bị gọi car. CAL-03 yêu cầu tìm nguyên nhân, gán lại nhãn và ghi chi tiết lỗi; chỉ thay ngưỡng không tự đổi tên lớp.
- Ví dụ điểm cao vẫn cần kiểm tra: ở ảnh QC img_003, hai khung three_wheeler #19 (0.90), #20 (0.80) chồng lên cùng xe; khung #20 đã bị loại trong lượt sửa QC. Điểm cao không chứng minh hai khung là hai vật khác nhau.
- Ngưỡng được chọn và đánh giá trên cùng 4 ảnh gold nên số đo lạc quan; mẫu nhỏ không đại diện toàn bộ bộ ảnh.

## 2. AI error profile

Nguồn: `qc/review_stats.md` sau lượt sửa có hướng dẫn, trước sửa QC. Script loại vùng bẫy khỏi bảng chất lượng máy; mẫu số khác tổng annotations.

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted | 465 | 89.3% | 521 box máy |
| edited | 19 | 3.6% | 521 box máy |
| deleted | 37 | 7.1% | 521 box máy |
| added | 64 | 11.7% | 548 đối tượng sau duyệt trong phép đo |

- So với 73 đối tượng gold: TP=53, FP=21, FN=20; precision=53/74=71.6%, recall=53/73=72.6%. Đây là chất lượng pre-label máy, không phải độ chính xác của bộ nhãn đã sửa.
- Lượt review ban đầu: accepted 479/521, edited 18/521, deleted 24/521, added 64/561; giữ riêng trong `qc/review_stats_round1.*`.
- Lớp máy tệ nhất theo đánh giá của học viên: Học viên chưa kết luận lớp tệ nhất. Các quan sát đã ghi gồm nhầm xe ba bánh thành car và các khung three_wheeler bị loại ở img_003; không tự suy một thứ hạng từ vài ví dụ..

## 3. Thiên kiến tự động hóa và ảnh bẫy

- Lượt đầu chưa biết đáp án: 8/12=66.7%; loose 3/3, missing 2/3, wrong_class 3/3, extra 0/3. Lọt ở img_004 (missing), img_005/img_017/img_028 (extra).
- Sau khi được chỉ các ảnh còn lỗi và sửa: script đạt 12/12=100%. Đây là sửa có hướng dẫn, không thay thế kết quả review ban đầu 8/12.
- Các khung thiếu/thừa đã được người học sửa và kiểm tra qua file xuất; trường hợp img_004 từng có chênh lệch khung với tham chiếu được ghi ở `qc/rework_notes.txt`.
- Vì sao lượt đầu lọt lỗi và accepted cao nói gì về cách duyệt: Học viên muốn định hình trước tiêu chuẩn gán nhãn và những lỗi thường mắc rồi mới đánh giá sản phẩm AI. Chưa xác định cụ thể bước checklist nào đã bỏ qua trong lượt đầu..

## 4. Máy có đáng dùng không?

- Từ `time_log.csv`: gold 32 phút/4 ảnh = 8 phút/ảnh; review lượt đầu 36 phút/24 ảnh = 1.5 phút/ảnh (18:55–19:36 trừ 5 phút nghỉ).
- Chưa ghi thời gian thực làm các lượt sửa bổ sung/QC; không coi 1.5 phút/ảnh là tổng thời gian để tạo nhãn cuối. Hai nhóm ảnh khác nhau, người học đã quen quy tắc sau gold, nên không thể quy toàn bộ chênh lệch thời gian cho AI.
- Kết luận của học viên: Cần định hình trước việc gán nhãn của bài và các lỗi thường mắc, sau đó mới đánh giá sản phẩm AI. Học viên chưa đưa kết luận có/không cho mọi lớp; chênh lệch thời gian lượt đầu chưa đủ để kết luận toàn bộ lợi ích vì chưa tính thời gian sửa..
- Ba hành động kiểm tra rút từ quyết định học viên đã đưa ra: ghi chi tiết lỗi theo ảnh/model (CAL-03); điều chỉnh prompt rồi chạy lại khi mask sai (CAL-01); đối chiếu kết quả thực tế trước khi chấp nhận (CAL-01/CAL-03). Không lấy conf hoặc việc chạy xong làm bằng chứng đúng.

## 5. Mask SAM cho 3 vật (Part 5)

Các nhận xét dưới đây tổng hợp từ quan sát và phản hồi của học viên khi chạy trên Colab T4.

| Ảnh | SAM (phiên bản) | Số click | Mask gồm người lái / hàng hoá? | Lỗi quan sát | File trong `masks/` |
|---|---|---|---|---|---|
| img_013.jpg | SAM 2.1, sam2.1_b.pt | Bản v2: 3 dương + 4 âm = 7 | Vẫn gồm một phần người lái; chưa đánh giá hàng hóa riêng | Lần 3 tách tốt hơn lần 2 nhưng còn dính đầu và chân; một số phần xe bị thủng | tuktuk_img013_v2.jpg |
| img_002.jpg | SAM 2.1, sam2.1_b.pt | Bản v2: 3 dương + 4 âm = 7 | Người lái được loại một phần, chưa sạch hoàn toàn; chưa đánh giá hàng hóa riêng | Ban đầu nghi lẫn mặt đường; xem mask đen trắng không thấy mảng tràn rõ xuống đường. Màu đường đỏ có sẵn gây nhầm với overlay | tuktuk_img002_v2.jpg |
| img_004.jpg | SAM 2.1, sam2.1_b.pt | 3 dương + 2 âm = 5 | Không thấy gồm người đứng bên phải; chưa đánh giá hàng hóa riêng | Học viên nhận xét xe được bôi ổn nhưng vẫn khuyết một mảng nhỏ trên yên xe | tuktuk_img004.jpg |

- Giữ thêm ba ảnh thử trước trong `masks/` để đối chiếu; tổng 6 ảnh JPG nhưng chỉ có **3 vật khác nhau**.
- Lịch sử điểm click và các lần thử: `qc/mask_observations.txt`. Diện tích mask không phải độ chính xác của mask.
- Đối chiếu guideline mục 4.2: CAL-01 bổ sung việc ghi lỗi mask dính người, điều chỉnh prompt và kiểm tra lại; CAL-02 chốt phạm vi mask xe chở trái cây có gồm hàng nhô ra. Chưa mở rộng quyết định trái cây cho mọi loại hàng..
- Interactor SAM trong CVAT: chưa có bằng chứng đã thử; các kết quả trên chạy offline bằng script trên Colab.

## 6. Calibration (Part 6)

- Đã ghi 3 quyết định do học viên đưa ra trong `qc/calibration_decisions.csv`:
  - CAL-01 (`loi_may`, img_013): ghi rõ mask dính người, điều chỉnh điểm gợi ý/khung cho SAM, chạy lại và kiểm tra.
  - CAL-02 (`cau_hoi_mo`, img_003): mask xe chở trái cây gồm thân xe, thùng cố định và cả trái cây nhô ra; không thay quy tắc box.
  - CAL-03 (`loi_may`, img_013): tìm nguyên nhân xe ba bánh bị gọi car, gán lại nhãn và ghi chi tiết lỗi; điểm cao không thay thế kiểm tra thực tế.
- Các bổ sung đã ghi trong `guidelines/guideline_v1.1_calibrated.md`; những câu hỏi mở khác của v1.0 chưa được chốt.
- Học viên chưa xếp hạng các quyết định hoặc kết luận nguyên nhân gốc; các quan sát và hành động đã ghi ở từng CAL, không coi giả thuyết về model là nguyên nhân đã chứng minh.

## 7. QC và quyết định (Part 6)

- Mẫu trước sửa: 5/24 ảnh batch, 0 ảnh can_review + 5 ảnh ngẫu nhiên, seed 42; 109 đối tượng. Người kiểm: Lê Ngọc Dũng, cũng là người duyệt nên chưa có kiểm tra độc lập.
- Theo `qc/qc_review.csv` và `qc/qc_report_auto.md`: lỗi theo đối tượng 16/109=14.7%; ảnh có lỗi 5/5=100.0%. Đây là QC trước sửa.
- Các loại lỗi: 12 khung thừa/bị loại (ví dụ img_003, img_006, img_031, img_035), 2 người bỏ sót (img_014), 1 sai lớp (img_003: three_wheeler đổi bicycle), 1 sai vị trí/kích thước (img_003). Nhóm khung bị loại gồm cả trường hợp học viên thấy chưa đủ căn cứ gán; không khẳng định đã biết lớp thật của mọi vật đó.
- Bản sửa `labels/qc_fixed.json` có 5 ảnh, 99 đối tượng; đã bổ sung 2 person ở img_014. `release/final_det.coco.json` đã thay 5 ảnh này vào bộ 24 ảnh, tổng 549 đối tượng.
- Chưa có một lượt tái-QC đầy đủ sau sửa. Không đổi phiếu trước sửa thành không lỗi và không coi kiểm tra JSON là chứng minh chất lượng nhãn đã đạt.
- Mẫu chỉ có 5 ảnh; Wilson cận trên 95% của tỷ lệ ảnh lỗi trước sửa là 100%. Không thể dùng mẫu này chứng minh tỷ lệ lỗi toàn bộ bộ ảnh <=2%.
- Script kiểm tra cấu trúc, ảnh/lớp, kích thước box và tính số liệu từ phiếu. Script không tự xác minh lớp thực, độ ôm sát, bỏ sót cùng máy, hoặc người học đã thực hiện đủ checklist.
- Gợi ý tự động: REWORK do 5/5 ảnh có lỗi và nhóm khung bị loại chiếm 12/16 lỗi. Đây là gợi ý trên lượt QC trước sửa, không phải kết luận tái-QC sau sửa.

**Quyết định của học viên: Rework — tiếp tục kiểm tra sau sửa. QC ban đầu phát hiện lỗi ở 5/5 ảnh mẫu; đã sửa các lỗi xác định được nhưng chưa tái-QC đầy đủ, nên chưa xác nhận Release**, ngày 2026-11-18.

## 8. Ghi chú cá nhân

- Dùng ChatGPT/Codex để hướng dẫn thao tác, kiểm tra file, chạy các script của bài, đối chiếu thay đổi và tổng hợp các nhận xét học viên cung cấp. Học viên trực tiếp gán gold, review, quan sát mask và đưa các quyết định calibration; lỗi QC được ghi theo phản hồi của học viên. Các lần dùng SAM/YOLO là nội dung bắt buộc của bài.
- Gold có bị nhiễm trước khi gán xong không: Không. Học viên xác nhận chưa xem nhãn máy hoặc nhờ AI xác định vật trên gold trước khi gán và xuất xong 4 ảnh..
- Máy Windows, GPU tích hợp Intel UHD Graphics; model chạy trên Colab Tesla T4. CVAT Docker v2.82.0. Đã khắc phục lỗi 500 khi tạo Project do server v2.81.0 không khớp cơ sở dữ liệu đã cập nhật bằng v2.82.0; có sao lưu trước khi đưa server về v2.82.0.
- Các lượt model dùng kết quả chạy thật trên Colab, không lấy từ `data/cache/`; nguồn `work/runs.csv`, `model_log.csv` và các ZIP đầu ra.
- Không sửa thuật toán các script đo. Ghi nhận giới hạn của phép so khung tham chiếu ở img_004 trong `qc/rework_notes.txt`; giữ số đo script thực tế và ghi riêng kết quả lượt đầu/lượt sửa.
- Một điều làm khác lần sau: Định hình trước tiêu chuẩn gán nhãn của bài và những lỗi thường mắc, sau đó mới đánh giá sản phẩm AI..

Việc tiếp theo theo quyết định Rework: tái kiểm bộ nhãn sau sửa và kiểm tra các ảnh ngoài mẫu; ghi riêng thời gian bổ sung và kết quả tái-QC, không thay số liệu QC ban đầu bằng kết quả sau sửa. Chưa nộp LMS.
