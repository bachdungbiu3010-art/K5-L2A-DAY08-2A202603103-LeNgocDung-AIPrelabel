# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 521; đối tượng sau duyệt: 561; ngưỡng accepted: IoU >= 0.9
- Đã **loại 24 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 479 | 479/521 = 91.9% | 521 box máy |
| edited (sửa) | 18 | 18/521 = 3.5% | 521 box máy |
| deleted (xoá) | 24 | 24/521 = 4.6% | 521 box máy |
| added (vẽ thêm) | 64 | 64/561 = 11.4% | 561 đối tượng sau duyệt |

Trong số deleted + added, **6 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 58.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 261 | 4 | 3 | 16 |
| bicycle | 6 | 0 | 2 | 3 |
| motorbike | 127 | 8 | 4 | 17 |
| car | 51 | 1 | 2 | 18 |
| bus | 3 | 0 | 0 | 1 |
| truck | 5 | 0 | 1 | 1 |
| three_wheeler | 26 | 5 | 12 | 8 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 73 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 53 | 21 | 20 | 71.6% (KTC 95%: 60–81%) | 72.6% (KTC 95%: 61–82%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 8/12 = 66.7% (KTC 95%: 39–86%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - loose: 3/3
  - missing: 2/3
  - wrong_class: 3/3
  - extra: 0/3
  - lọt: img_004.jpg (missing), img_028.jpg (extra), img_005.jpg (extra), img_017.jpg (extra)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
