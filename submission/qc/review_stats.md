# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 529; đối tượng sau duyệt: 528; ngưỡng accepted: IoU >= 0.9
- Đã **loại 20 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 469 | 469/529 = 88.7% | 529 box máy |
| edited (sửa) | 9 | 9/529 = 1.7% | 529 box máy |
| deleted (xoá) | 46 | 46/529 = 8.7% | 529 box máy |
| added (vẽ thêm) | 50 | 50/528 = 9.5% | 528 đối tượng sau duyệt |

Trong số deleted + added, **15 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 35.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 265 | 0 | 7 | 5 |
| bicycle | 5 | 0 | 3 | 5 |
| motorbike | 138 | 3 | 9 | 12 |
| car | 47 | 0 | 5 | 18 |
| bus | 2 | 0 | 1 | 2 |
| truck | 2 | 0 | 3 | 4 |
| three_wheeler | 10 | 6 | 18 | 4 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 53 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 45 | 16 | 8 | 73.8% (KTC 95%: 62–83%) | 84.9% (KTC 95%: 73–92%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 12/12 = 100.0% (KTC 95%: 76–100%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - wrong_class: 3/3
  - missing: 3/3
  - extra: 3/3
  - loose: 3/3

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
