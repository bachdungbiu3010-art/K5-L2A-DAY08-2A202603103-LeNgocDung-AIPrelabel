# Báo cáo Day 08 — 2A202603103 - Lê Ngọc Dũng

Bài cá nhân. Kết quả dưới đây phân biệt lượt review ban đầu, các lượt sửa có hướng dẫn, QC trước sửa và bộ nhãn cuối. Quyết định của tôi: **Rework**. Chưa nộp LMS.

## 1. Model và recipe (Part 2–3)

Tôi thử YOLO26m ở hai ngưỡng, YOLOE tìm xe ba bánh và YOLO26 kết hợp SAM. Nhật ký: [model_log.csv](model_log.csv).

| Run | Cấu hình thử | Kết quả thực chạy |
|---|---|---|
| r01 | YOLO26m, pilot, conf=0.25 | 215 box/8 ảnh; 1.30 giây/ảnh |
| r02 | YOLO26m, pilot, conf=0.50 | 139 box/8 ảnh; 0.33 giây/ảnh |
| r03 | YOLOE-s, chỉ three_wheeler, conf=0.25 | 8 box/8 ảnh; 0.19 giây/ảnh |
| r04 | YOLO26m + SAM 2.1, conf=0.30 | 16 box có mask/2 ảnh; 1.65 giây/ảnh |

Trên img_011, tôi nhận xét lượt 0.50 nhận đủ car và bỏ các người nhỏ so với lượt 0.25; kích thước dưới 20 pixel trong nhận xét này chưa được đo riêng trên ảnh gốc. YOLOE chỉ tìm three_wheeler nên không gán person/car/motorbike không được tính là bỏ sót các lớp đó. Lượt r04 chỉ yêu cầu person, motorbike, car; SAM không tự sửa tên lớp sai của detector.

Sau đó, tôi chạy cả 36 ảnh ở conf=0.05 để có nguồn chọn ngưỡng. Theo [threshold_sweep.md](qc/threshold_sweep.md):

| Lớp | Nguồn model | Ngưỡng dùng | Căn cứ trên gold |
|---|---|---|---|
| person | YOLO26m | 0.30 mặc định | 36 vật; recall cao nhất trong các ngưỡng thử là 81%, chưa đạt mục tiêu 90% |
| bicycle | YOLO26m | 0.30 mặc định | 5 vật; recall cao nhất 60%, chưa đạt 90% |
| motorbike | YOLO26m | 0.30 mặc định | 16 vật; recall cao nhất 88%, chưa đạt 90% |
| car | YOLO26m | 0.30 mặc định | 8 vật; recall cao nhất 88%, chưa đạt 90% |
| bus / truck | YOLO26m | 0.30 mặc định | Chỉ 1/0 vật gold; không đủ mẫu để chọn ngưỡng riêng |
| three_wheeler | YOLOE-s; tuk-tuk, cyclo, cargo tricycle | 0.15 | 7 vật gold; recall 7/7=100%, precision 7/9=77.8% |

Lệnh gộp đã chạy:

```text
python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "three_wheeler=0.15" --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json
```

Tôi dùng SAM 2.1 (`sam2.1_b.pt`) theo bản khuyến nghị của bài, chạy trên Colab T4; chưa thử SAM 1/3 nên không kết luận bản nào tốt nhất. Lỗi xe ba bánh bị gọi car ở img_013 cần kiểm tra và sửa lớp, không thể chỉ đổi ngưỡng để tự đổi tên lớp. Ví dụ điểm cao vẫn sai: bộ QC trước sửa ở img_003 có hai khung three_wheeler #19 (0.90), #20 (0.80) chồng lên cùng xe; một khung phải bị loại.

Ngưỡng được chọn và đánh giá trên cùng 4 ảnh gold nên số đo lạc quan. Đây là ước lượng trên mẫu nhỏ, không bảo đảm kết quả trên toàn bộ ảnh.

## 2. AI error profile

Nguồn: [review_stats.md](qc/review_stats.md), đo trên `batch_reviewed.json` sau sửa có hướng dẫn, **trước sửa QC**. Script đã loại 22 đối tượng quanh bẫy khỏi bảng chất lượng máy để lỗi nhân tạo không bị tính là chất lượng model.

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted | 465 | 89.3% | 521 box máy trong phép đo |
| edited | 19 | 3.6% | 521 box máy trong phép đo |
| deleted | 37 | 7.1% | 521 box máy trong phép đo |
| added | 64 | 11.7% | 548 đối tượng sau duyệt trong phép đo |

Trong 64 đối tượng added, có 6 trường hợp đổi lớp; script ước tính 58 vật bị máy sót thực sự. Accepted cao không có nghĩa là máy không bỏ sót.

Theo trải nghiệm của tôi, **xe ba bánh là lớp dễ bị gán sai nhất**: dễ nhầm với xe đạp và có trường hợp một xe bị AI tách thành hai khung cho hai bộ phận. Ví dụ img_003 và xe xích lô ở img_035. Đây là nhận xét từ quan sát, không phải xếp hạng khách quan trên một test set độc lập.

So với 73 đối tượng gold: TP=53, FP=21, FN=20; precision=53/74=71.6%, recall=53/73=72.6%. Đây là chất lượng pre-label máy, không phải độ chính xác của nhãn cuối. Gold không được đưa vào Task review.

Để giữ đúng lịch sử, lượt review đầu còn ở [review_stats_round1.md](qc/review_stats_round1.md): accepted 479/521, edited 18/521, deleted 24/521, added 64/561. Các mẫu số trong error profile không phải tổng số annotation của bộ release cuối.

## 3. Thiên kiến tự động hóa và ảnh bẫy

Lượt đầu chưa biết đáp án, tôi bắt được **8/12=66.7%**: loose 3/3, missing 2/3, wrong_class 3/3, extra 0/3. Lọt missing ở img_004 và extra ở img_005, img_017, img_028.

Tôi nhận thấy nguyên nhân là **kiểm tra chưa kỹ và chủ quan tin vào AI**. Tôi không xác định được một bước checklist cụ thể đã bỏ qua, nên không ghi là đã bỏ bước nào khi chưa chắc.

Sau khi được thông báo vị trí lỗi và sửa, script đạt **12/12=100%**. Đây là kết quả sửa có hướng dẫn, không thay thế số đo ban đầu. Lượt đầu accepted 479/521=91.9% nhưng extra chỉ bắt 0/3 cho thấy giữ nhiều khung chưa đồng nghĩa với kiểm tra kỹ. Chi tiết các lượt sửa: [rework_notes.txt](qc/rework_notes.txt).

## 4. Máy có đáng dùng không?

**Theo trải nghiệm của tôi, AI đáng dùng để gán nhãn trước vì giúp nhanh hơn, nhưng phải kiểm tra kỹ.** Theo [time_log.csv](time_log.csv), gold mất 32 phút/4 ảnh=8 phút/ảnh; review lượt đầu mất 36 phút/24 ảnh=1.5 phút/ảnh. Thời gian review là 18:55–19:36, trừ 5 phút nghỉ.

Tôi chưa ghi thời gian thực làm các lượt sửa bổ sung và QC. Hai nhóm ảnh khác nhau và tôi đã quen quy tắc sau khi gán gold, nên không coi chênh lệch 8 so với 1.5 phút/ảnh là mức tiết kiệm của toàn bộ quy trình.

Ba hành động kiểm tra rút từ quyết định của tôi, dùng để giảm việc tin máy quá mức:

1. Ghi lỗi cụ thể theo ảnh, vật và model thay vì chấp nhận chỉ vì conf cao (CAL-03).
2. Nếu mask dính người, điều chỉnh điểm gợi ý/khung rồi chạy lại; giữ các lần thử để so sánh (CAL-01).
3. Đối chiếu ảnh gốc và kết quả, dùng mask đen trắng khi màu overlay gây nhầm; chỉ chấp nhận sau khi kiểm tra, không lấy việc chạy xong làm bằng chứng đúng (CAL-01, ví dụ img_002).

Lần sau tôi sẽ định hình trước tiêu chuẩn gán nhãn và những lỗi hay mắc rồi mới đánh giá sản phẩm AI.

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | Model | Số click ở bản giữ lại | Người lái/hàng hóa | Lỗi quan sát | File |
|---|---|---|---|---|---|
| img_013.jpg | SAM 2.1, sam2.1_b.pt | 3 dương + 4 âm=7 | Vẫn gồm một phần người lái; chưa đánh giá hàng hóa riêng | Lần 3 tốt hơn lần 2 nhưng còn dính đầu/chân và thủng một số phần xe | [tuktuk_img013_v2.jpg](masks/tuktuk_img013_v2.jpg) |
| img_002.jpg | SAM 2.1, sam2.1_b.pt | 3 dương + 4 âm=7 | Người lái được loại một phần, chưa sạch hoàn toàn; chưa đánh giá hàng hóa riêng | Ban đầu nghi lẫn đường; mask đen trắng không cho thấy mảng lan rõ xuống đường. Màu đường đỏ sẵn gây nhầm với overlay | [tuktuk_img002_v2.jpg](masks/tuktuk_img002_v2.jpg) |
| img_004.jpg | SAM 2.1, sam2.1_b.pt | 3 dương + 2 âm=5 | Không thấy gồm người đứng bên phải; chưa đánh giá hàng hóa riêng | Bao phần lớn xe nhưng còn khuyết một mảng nhỏ trên yên xe | [tuktuk_img004.jpg](masks/tuktuk_img004.jpg) |

Có tổng 6 ảnh JPG nhưng chỉ **3 vật khác nhau**; 3 ảnh còn lại là các bản thử trước. Chi tiết: [mask_observations.txt](qc/mask_observations.txt). Diện tích mask không phải độ chính xác của mask.

Đối chiếu mục 4.2 của guideline: các lỗi dính người và thủng phần xe cho thấy cần kiểm tra đường biên phần nhìn thấy, không chỉ diện tích hoặc hình dáng chung. CAL-01 bổ sung bước ghi lỗi/chạy lại/kiểm tra; CAL-02 chốt phạm vi mask của ca xe chở trái cây. Chưa tạo mask img_003 để kiểm chứng quy tắc trái cây. Không thử SAM Interactor trong CVAT; các mask trên chạy offline bằng Colab.

## 6. Calibration (Part 6)

Đã ghi 3 quyết định do tôi đưa ra trong [calibration_decisions.csv](qc/calibration_decisions.csv) và chuyển vào [guideline v1.1](guidelines/guideline_v1.1_calibrated.md):

| Mã | Nguồn | Ảnh | Quyết định |
|---|---|---|---|
| CAL-01 | loi_may | img_013 | Ghi rõ lỗi mask dính người; điều chỉnh điểm gợi ý/khung, chạy lại và kiểm tra |
| CAL-02 | cau_hoi_mo | img_003 | Mask xe chở trái cây gồm thân xe, thùng cố định và trái cây nhô ra; không thay quy tắc box |
| CAL-03 | loi_may | img_013 | Tìm nguyên nhân nhận sai lớp, gán lại nhãn và ghi lỗi; không chấp nhận chỉ dựa vào conf |

CAL-02 giải quyết câu hỏi còn mở về phạm vi hàng hóa trong mask; CAL-01/CAL-03 xử lý lỗi quan sát của máy, không phải đổi định nghĩa mọi lớp để hợp thức hóa nhãn. Tôi chưa kết luận nguyên nhân kỹ thuật gốc hoặc xếp hạng quyết định quan trọng nhất. Các câu hỏi mở khác của v1.0 chưa được chốt. Bộ taxonomy dùng cho script vẫn v1.0, có guideline bổ sung v1.1; mục `rules` không thay đổi.

## 7. QC và quyết định (Part 6)

Mẫu QC gồm 5/24 ảnh batch: 0 ảnh can_review và 5 ảnh ngẫu nhiên, seed 42; 109 đối tượng trước sửa. Tôi vừa duyệt vừa kiểm tra nên chưa có QC độc lập bởi người khác. Mẫu và phiếu: [qc_sample.csv](qc/qc_sample.csv), [qc_review.csv](qc/qc_review.csv).

Theo [qc_report_auto.md](qc/qc_report_auto.md), QC trước sửa có **16/109=14.7% theo đối tượng**, và **5/5=100% theo ảnh**. Có 12 khung thừa/bị loại (img_003, 006, 031, 035), 2 người bỏ sót (img_014), 1 sai lớp (img_003: three_wheeler thành bicycle) và 1 sai khung (img_003). Nhóm bị loại bao gồm cả ca tôi thấy chưa đủ căn cứ gán lớp; không khẳng định đã xác định được lớp thật của mọi vật đó.

| Ảnh QC | Trước sửa | Bản cuối | Xử lý đã ghi nhận |
|---|---:|---:|---|
| img_003 | 26 | 21 | Loại khung dư/thiếu căn cứ; sửa bicycle; chỉnh khung xe bao mái |
| img_006 | 27 | 26 | Xóa khung car thừa |
| img_014 | 19 | 21 | Thêm 2 person; hai người từng bị nghi trùng được tôi xác nhận là người khác nhau |
| img_031 | 4 | 3 | Xóa khung person thừa |
| img_035 | 33 | 27 | Xóa các person thừa; gộp hình học xe xích lô vào một khung, bỏ khung mui dư |
| Tổng | 109 | 98 | Không thay phiếu QC trước sửa bằng số liệu sau sửa |

Bản [qc_fixed.json](labels/qc_fixed.json) có 5 ảnh/98 đối tượng. Script thay 5 ảnh này vào batch 24 ảnh/559 đối tượng, cho bộ [release cuối](release/final_det.coco.json) gồm **24 ảnh/548 đối tượng**: 559−109+98=548.

Lượt tái kiểm có Codex hỗ trợ đã đối chiếu các sửa đổi ở mái xe img_003, khung xích lô img_035 và xác nhận của tôi về người ở img_014. Chưa thấy lỗi rõ ràng mới trong lượt rà tổng thể 5 ảnh, nhưng đây không phải phép đo tỷ lệ lỗi sau sửa, không phải QC độc lập của người thứ hai và không chứng minh toàn bộ 24 ảnh sạch lỗi. Chi tiết trạng thái cuối: [recheck_ai.md](qc/recheck_ai.md).

Script QC gợi ý REWORK trên số liệu trước sửa vì 5/5 ảnh có lỗi và nhóm khung bị loại chiếm 12/16 lỗi. Cận trên 95% Wilson của tỷ lệ ảnh lỗi trước sửa là 100%; mẫu 5 ảnh không thể chứng minh tỷ lệ lỗi toàn bộ bộ ảnh <=2%. Script kiểm cấu trúc và đếm theo phiếu, không tự xác nhận lớp thực, độ ôm sát hoặc vật mà cả người lẫn máy cùng bỏ sót.

**Quyết định của tôi: Rework**, ngày 2026-11-18. Các lỗi đã xác định đã được sửa; tôi giữ quyết định này vì chưa tái kiểm đầy đủ các ảnh ngoài mẫu và chưa có căn cứ xác nhận Release. Việc tiếp theo là rà tiếp các ảnh ngoài mẫu, ghi thời gian bổ sung và kết quả kiểm tra riêng.

## 8. Ghi chú cá nhân

- Tôi dùng ChatGPT/Codex để hướng dẫn thao tác, kiểm tra file, chạy script, đối chiếu thay đổi và diễn đạt các nhận xét tôi cung cấp. Tôi trực tiếp gán gold, review, quan sát mask, đưa quyết định calibration và xác nhận lỗi QC. Lượt xem lại 5 ảnh có AI hỗ trợ được ghi riêng, không nhận là QC độc lập bằng người.
- Gold không bị xem nhãn máy hoặc nhờ AI nhận vật trước khi tôi gán và xuất xong 4 ảnh, theo xác nhận của tôi.
- Máy Windows, Intel UHD Graphics; model chạy Colab Tesla T4. CVAT Docker v2.82.0. Lỗi 500 khi tạo Project do server v2.81.0 không khớp cơ sở dữ liệu đã cập nhật bằng v2.82.0 được xử lý bằng sao lưu rồi đưa server về v2.82.0.
- Dùng kết quả model chạy thật trên Colab, không dùng `data/cache/`. Giữ thời gian/lượt chạy thật; không bổ sung thời gian sửa khi chưa ghi nhận.
- Không sửa thuật toán hoặc sửa tay kết quả script. Từng có khác biệt khung tham chiếu bẫy với khung tôi vẽ ở img_004; giữ số đo thật và ghi lịch sử trong `qc/rework_notes.txt`.
- Lần sau: định hình tiêu chuẩn gán nhãn cùng các lỗi thường gặp trước khi đánh giá AI, kiểm tra kỹ hơn và tránh chủ quan tin vào nhãn máy.
- Trạng thái nộp: chưa xác minh đã nộp LMS.
