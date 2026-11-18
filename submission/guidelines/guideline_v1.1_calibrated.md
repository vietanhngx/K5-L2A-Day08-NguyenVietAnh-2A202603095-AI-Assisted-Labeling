# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603095 - Nguyễn Việt Anh

> **Chỉ viết phần thay đổi so với v1.0** (không copy cả file). Chốt các câu hỏi mở ở mục 6 của v1.0 (hoặc nói rõ câu nào chưa chốt, vì sao)
> và thêm quy tắc mới rút ra từ lỗi của máy / lỗi của bạn. Mỗi ca khó **có ảnh ví dụ** (`img_0xx`) và khớp một dòng trong `qc/calibration_decisions.csv`.

## 1. Câu hỏi mở đã chốt (từ mục 6 của v1.0)

| # | Câu hỏi | Quyết định | Ảnh ví dụ | `case_id` |
|---|---|---|---|---|
| 1 và 6 | Xe đạp điện: bicycle/motorbike? Box chồng nhau giữ hay gộp? | CAL-04: xe đạp điện có bàn đạp và trợ lực điện dùng bicycle; xe điện/xe máy điện chạy bằng động cơ điện (không thuộc loại xe đạp trợ lực vừa nêu) dùng motorbike. img_025 xe sát mép phải (release #363) được xác định là xe đạp điện trong calibration, giữ bicycle và can_review=true; crop chưa đủ thấy bàn đạp, không tự suy đoán. CAL-02: một vật một box, bỏ box trùng cùng vật; khác vật vẫn giữ riêng dù chồng nhau | img_025.jpg; img_003.jpg | CAL-04; CAL-02 |

## 2. Ca khó mới (từ lỗi của máy, bẫy lọt, box `can_review`)

| Ảnh | Tình huống | Máy làm gì (model nào) | Quyết định của bạn | Quy tắc mới (vào mục nào của v1.0) |
|---|---|---|---|---|
| img_013.jpg; img_024.jpg; img_034.jpg | Tuk-tuk sai lớp; khó phân biệt van/minibus | YOLO26 gọi tuk-tuk là car; SAM giữ nhãn đó | Tuk-tuk: three_wheeler; xe 7 chỗ: car; xe tải: truck. Đề xuất thêm lớp van trong tương lai, chưa áp dụng | Hiện giữ bộ nhãn gốc: minivan thuộc car, minibus thuộc bus. Chỉ ghi đề xuất van; không đổi taxonomy/CVAT |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do (ca nào, `case_id`) |
|---|---|---|
| v1.0 | Bản giảng viên | — |
| v1.1 | Chốt xe điện và box trùng; đề xuất lớp van, chưa triển khai | CAL-01–CAL-04; img_003/013/024/025/034 |
