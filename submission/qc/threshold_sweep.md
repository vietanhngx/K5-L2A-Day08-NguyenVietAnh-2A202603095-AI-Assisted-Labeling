# Quét ngưỡng theo lớp

Mục tiêu recall: 90%. Ảnh dùng để quét: 4.

| Lớp | Ngưỡng gợi ý | Lý do |
|---|---|---|
| person | (mặc định) | không ngưỡng nào <= 0.5 đạt recall 90% (tối đa 86%, 21 vật): dùng ngưỡng mặc định, nói rõ trong báo cáo |
| bicycle | (mặc định) | chỉ 4 vật trong ảnh gold (< 5): mẫu quá ít, KHÔNG chọn ngưỡng theo lớp này, dùng ngưỡng mặc định |
| motorbike | 0.25 | recall 92% (>= 90%), precision 92% (12 vật gold) |
| car | (mặc định) | không ngưỡng nào <= 0.5 đạt recall 90% (tối đa 89%, 9 vật): dùng ngưỡng mặc định, nói rõ trong báo cáo |
| bus | (mặc định) | chỉ 1 vật trong ảnh gold (< 5): mẫu quá ít, KHÔNG chọn ngưỡng theo lớp này, dùng ngưỡng mặc định |
| truck | (mặc định) | chỉ 0 vật trong ảnh gold (< 5): mẫu quá ít, KHÔNG chọn ngưỡng theo lớp này, dùng ngưỡng mặc định |
| three_wheeler | 0.2 | recall 100% (>= 90%), precision 75% (6 vật gold) |

Dán vào lệnh merge_prelabels (lớp không có trong chuỗi dùng `--conf` mặc định 0.3): `--thr "motorbike=0.25,three_wheeler=0.2"`

Lưu ý: bạn chọn ngưỡng trên chính nhãn gold, nên precision / recall của máy trên gold ở ngưỡng đã chọn là con số **lạc quan** (chọn và chấm trên cùng dữ liệu); chỉ có vài ảnh -> ngưỡng là ước lượng thô; lớp có ít vật (xem n_gold) thì kết luận yếu.
