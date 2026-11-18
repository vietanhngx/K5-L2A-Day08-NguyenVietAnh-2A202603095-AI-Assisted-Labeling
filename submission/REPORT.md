# Báo cáo Day 08 — 2A202603095 - Nguyễn Việt Anh

> Một file duy nhất cho cả bài (đã gộp recipe, QC và ghi chú cá nhân). Mọi con số kèm **mẫu số** và nguồn (file nào). Viết bằng lời của bạn.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao (dẫn `run_id` trong `model_log.csv`) |
|---|---|---|---|
| person / bicycle / motorbike / car / bus / truck | YOLO26m | motorbike 0.25; các lớp còn lại 0.30 | r01/r02; motorbike recall/precision 92%; person/car chưa đạt recall 90%; bicycle/bus/truck dưới 5 vật gold, giữ mặc định. Nguồn: qc/threshold_sweep.md |
| three_wheeler | YOLOE-26s; tuk-tuk, cyclo, cargo tricycle | 0.20 | r03; trên 6 vật gold: recall 100%, precision 75% |

- Lệnh `merge_prelabels.py` đã dùng (dán nguyên): python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "motorbike=0.25,three_wheeler=0.2" --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json
- **Phiên bản SAM bạn chọn** (SAM 1 / 2.1 / 3) và lý do: SAM 2.1 (sam2.1_b.pt), chạy CPU với trọng số đã có; đã thử trên các ca khó. Chưa đo so sánh định lượng với SAM 1/3
- Lỗi nào **không sửa được bằng đổi ngưỡng** (ví dụ tuk-tuk bị gọi `car`)? Sửa thế nào? YOLO26 không có lớp three_wheeler, gọi tuk-tuk thành car; bổ sung YOLOE và sửa lớp khi review. SAM chỉ tạo mask, không sửa lớp detector
- Box **điểm cao mà sai** hoặc **điểm thấp mà đúng** (ảnh nào): img_002.jpg: bán tải bên phải được YOLO26 gọi truck với conf 0.73, nhưng guideline xếp bán tải vào car
- Giới hạn: chọn ngưỡng trên chính gold (4 ảnh) nên số đo máy trên gold là lạc quan: Chỉ có 4 ảnh; dùng cùng dữ liệu để chọn và chấm ngưỡng, không phải tập kiểm định độc lập

## 2. AI error profile (từ `qc/review_stats.md`)

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted | 469 | 88.7% | 529 box máy |
| edited | 9 | 1.7% | 529 box máy |
| deleted | 46 | 8.7% | 529 box máy |
| added | 50 | 9.5% | 528 đối tượng sau duyệt, đã loại vùng bẫy; file đầy đủ có 548 annotation |

- Lớp nào máy tệ nhất? Vì sao (dẫn ảnh)? three_wheeler cần chú ý: 18 deleted, 6 edited, 10 accepted; img_002.jpg YOLOE còn gọi nhầm vùng bán tải. Không xếp hạng lớp chỉ từ số tuyệt đối vì mẫu số khác nhau
- So với gold: precision / recall của **máy** precision 45/61 = 73.8%; recall 45/53 = 84.9% (mẫu số: 53 đối tượng được tính sau xử lý ignore trên 4 ảnh, khác tổng 61 annotation gold)

## 3. Thiên kiến tự động hóa và ảnh bẫy

- Bắt bẫy: lượt đầu 6/12 = 50%; sau sửa 12/12 = 100%; loại nào lọt: lượt đầu: missing ở img_024/img_025; extra ở img_009/img_013; loose ở img_008/img_033; vì sao (bạn đã bỏ bước nào của checklist)? Lượt đầu còn lọt vật thiếu, box thừa và box lệch. Sau khi đối chiếu vị trí bẫy và sửa đạt 12/12; đây không phải phép đo độc lập.
- Accepted cao mà bắt bẫy thấp (hoặc ngược lại) nói gì về cách bạn duyệt? Accepted cao không chứng minh duyệt tốt; lượt đầu vẫn lọt 6/12 bẫy. Phải quét ảnh trước khi xem nhãn

## 4. Máy có đáng dùng không?

- Thời gian (từ `time_log.csv`): gán tay 20/4 = 5 phút/ảnh vs duyệt 40/24 = 1.67 phút/ảnh, thời gian người làm cung cấp; chưa bổ sung thời gian các lượt QC sau đó. Thiên lệch (bạn gán gold trước nên đã quen cảnh): Hai tập khác nhau và người làm đã quen cảnh sau gold; không quy toàn bộ mức giảm trung bình 66.7% cho AI
- Kết luận: Có ích để giảm công vẽ, nhưng cần review và QC, dựa trên recall máy trên gold 45/53 = 84.9%; thời gian review trung bình thấp hơn nhưng chưa phải thời gian hoàn thiện cuối cùng
- **Ba quy tắc giảm thiên kiến của bạn** (mỗi quy tắc kiểm tra được bằng số hoặc việc làm):
  1. Ẩn box và quét toàn ảnh để tìm vật sót trước khi hiện nhãn
  2. Rà box thừa/trùng, sai lớp và biên; không dùng conf cao thay cho nhìn ảnh
  3. Ca mơ hồ tick can_review và ghi quyết định; chỉ mở đáp án bẫy sau export lượt đầu

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM (phiên bản) | Số click | Mask gồm người lái / hàng hoá? | Lỗi quan sát | File trong `masks/` |
|---|---|---|---|---|---|
| img_013.jpg (tuk-tuk); img_024.jpg (van bên trái); img_034.jpg (xe tải) | SAM 2.1 (sam2.1_b.pt), tất cả chạy CPU | tuk-tuk: 2 dương + 1 âm; van và xe tải: mỗi ca 2 dương + 2 âm | tuk-tuk còn lẫn người và chút xe máy bên cạnh; chưa ghi nhận hàng hoá lộ rõ ở các ca còn lại | van và xe tải bám thân khá tốt, bánh/gầm tối cần rà; mask không phải nhãn chuẩn đã sửa tay. | tuktuk_img013.jpg; passenger_van_img024.jpg; truck_img034.jpg |

- Đối chiếu guideline mục 4.2: quy tắc nào giúp, quy tắc nào thiếu? Mỗi vật có mask riêng; dùng click âm để loại người/xe bên cạnh, không đoán phần bị che. Prompt tuk-tuk: dương (400,480)/(600,110), âm (720,300); van: dương (342,284)/(362,345), âm (280,252)/(416,357); tải: dương (573,363)/(554,422), âm (610,437)/(512,438)
- *(Tuỳ chọn)* Interactor SAM trong CVAT: làm được / không (lỗi gì, đã thử gì); khác gì offline: Chỉ có minh chứng chạy offline; chưa ghi nhận thử Interactor trong CVAT

## 6. Calibration (Part 6)

- Số quyết định: 4 (`calibration_decisions.csv`); quan trọng nhất (dẫn `CAL-xx`): CAL-01 sửa lớp tuk-tuk; CAL-02 loại box trùng; CAL-03 đề xuất van, chưa áp dụng; CAL-04 phân biệt xe đạp điện và xe máy điện
- Quy tắc mới đã vào `guideline_v1.1_calibrated.md`: Xe đạp có bàn đạp và trợ lực điện là bicycle; xe máy điện là motorbike; mỗi vật một box. Van chỉ là đề xuất, chưa thêm lớp. Bộ nhãn hiện tại giữ minivan là car, minibus là bus
- Nguyên nhân gốc: Máy thiếu lớp mới; guideline còn mơ hồ về một số xe; ảnh bị che hoặc thiếu đặc điểm dễ gây nhầm lớp

## 7. QC và quyết định (Part 6)

- Mẫu: 6 ảnh (1 ảnh `can_review` + 5 ngẫu nhiên, seed 42); người kiểm: bạn (**hạn chế**: bạn vừa là người duyệt).
- Lỗi theo **đối tượng**: 0/115 = 0.0% · theo **ảnh**: 0/6 = 0.0% (ngưỡng Release ≤ 2%)
- Top lỗi (trong: bỏ sót, thừa, sai lớp, sai vị trí/kích thước, sai thuộc tính, sai mask) + một ảnh ví dụ mỗi loại: Lượt đầu QC ghi box trùng img_003 và một xe sai lớp img_025, đã sửa. Kết luận sai về xe đạp điện release #363 được rút lại theo CAL-04, không phải đổi nhãn để đạt. Lượt hiện tại chưa ghi nhận lỗi xác định; kết quả được ghi trong qc/qc_review.csv
- **Giới hạn mẫu nhỏ** (cận trên 95% trong `qc_report_auto.md`): Cận trên Wilson 95% khoảng 39% khi 0/6 ảnh có lỗi; không thể chứng minh ngưỡng 2% bằng mẫu nhỏ. QC ban đầu 2/6 ảnh, 2/116 đối tượng lỗi; vòng trung gian có kết luận nhầm xe đạp điện. Nguồn: qc/qc_report_auto.md
- Script kiểm được / không kiểm được (ví dụ ôm sát, cùng bỏ sót với máy, theo checklist thật): Kiểm schema, ảnh, lớp, hình học và số đo so nhãn; không chứng minh box sát, lớp đúng thực tế hay vật người/máy cùng bỏ sót. Việc tự kiểm cùng bộ nhãn không phải kiểm định độc lập

**Quyết định: Release**, ngày 2026-11-18. Lý do (đi theo Hold → Rework → Release): QC không ghi nhận lỗi trong 6 ảnh và 115 đối tượng; đã sửa đủ 12/12 bẫy. Mẫu nhỏ, không khẳng định toàn bộ dữ liệu không còn lỗi. Đề xuất van chưa áp dụng
Nếu Rework / Hold, việc cần làm tiếp: Không áp dụng; đã chốt Release

## 8. Ghi chú cá nhân

- **Dùng AI để làm bài** (công cụ nào, việc gì, kiểm tra thế nào; "không dùng" nếu không): ChatGPT/Codex: giải thích hướng dẫn, hỗ trợ lệnh và kiểm tra file. Đối chiếu kết quả với ảnh, guideline và script kiểm tra
- Gold có bị nhiễm không (bạn có nhìn pre-label hay hỏi AI trước khi gán xong)? Có hỏi về quy tắc và gửi crop xe sát mép trong khi gán gold, nên không khẳng định hoàn toàn không có hỗ trợ AI; chưa có bằng chứng xem pre-label gold trước khi gán xong
- Máy / GPU / CVAT dùng; vướng mắc cài đặt: Windows, RTX 4050 6 GB, PyTorch CPU; tải bản CUDA hủy theo yêu cầu người làm. CVAT v2.81.0, chạy local bằng Docker
- Dùng cache `data/cache/` thay vì tự chạy? Có log tự chạy YOLO26/YOLOE/SAM; chưa có xác nhận dùng thêm cache
- Lỗi / bug script đã gặp (nếu có): Thiếu CLIP, FutureWarning torch.jit.load nhưng chạy hoàn tất; từng dán COCO vào Labels Raw rồi sửa bằng labels_generated.json
- Một điều bạn làm khác lần sau: Đề xuất: quét ảnh trước khi xem nhãn, chốt ca xe điện/van sớm; không đổi lớp dựa vào crop thiếu đặc điểm
