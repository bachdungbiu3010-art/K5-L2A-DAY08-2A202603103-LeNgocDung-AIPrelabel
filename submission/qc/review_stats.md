# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 521; đối tượng sau duyệt: 548; ngưỡng accepted: IoU >= 0.9
- Đã **loại 22 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 465 | 465/521 = 89.3% | 521 box máy |
| edited (sửa) | 19 | 19/521 = 3.6% | 521 box máy |
| deleted (xoá) | 37 | 37/521 = 7.1% | 521 box máy |
| added (vẽ thêm) | 64 | 64/548 = 11.7% | 548 đối tượng sau duyệt |

Trong số deleted + added, **6 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 58.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 259 | 4 | 5 | 16 |
| bicycle | 5 | 0 | 3 | 3 |
| motorbike | 121 | 7 | 11 | 16 |
| car | 48 | 3 | 3 | 19 |
| bus | 3 | 0 | 0 | 1 |
| truck | 5 | 0 | 1 | 1 |
| three_wheeler | 24 | 5 | 14 | 8 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 73 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 53 | 21 | 20 | 71.6% (KTC 95%: 60–81%) | 72.6% (KTC 95%: 61–82%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 12/12 = 100.0% (KTC 95%: 76–100%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - loose: 3/3
  - missing: 3/3
  - wrong_class: 3/3
  - extra: 3/3

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
