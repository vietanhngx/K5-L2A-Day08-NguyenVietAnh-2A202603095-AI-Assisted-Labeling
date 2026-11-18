# Thông tin học viên

- Họ và tên: Nguyễn Việt Anh
- MSHV: 2A202603095
- Lớp (L2 / L3 / khác): L2A
- Tài khoản GitHub: vietanhngx
- Repo bài nộp (URL): https://github.com/vietanhngx/K5-L2A-Day08-NguyenVietAnh-2A202603095-AI-Assisted-Labeling
- Commit chốt bài (hash): 36a8e48b774aaa06dc37f40afa7c0d0d9d2ea638
- Máy: Windows, RTX 4050 6 GB; bài chạy bằng PyTorch CPU.
- CVAT (Docker): v2.81.0, chạy local bằng Docker.

## Kết quả tóm tắt (điền cuối bài; số lấy từ `submission/qc/*`, kèm mẫu số)

- Máy: accepted / edited / deleted: 469/529 = 88.7% / 9/529 = 1.7% / 46/529 = 8.7%; added: 50/528 = 9.5% đối tượng sau duyệt (đã loại vùng bẫy)
- So với gold: precision / recall của **máy** 45/61 = 73.8% / 45/53 = 84.9%
- Bắt bẫy: lượt đầu 6/12 = 50%; sửa 12/12 = 100%
- Thời gian: gán tay 5 phút/ảnh; duyệt có pre-label 40/24 = 1.67 phút/ảnh
- Lỗi QC: 0% theo ảnh (0/6), 0% theo đối tượng (0/115)
- **Quyết định:** Release — lý do một dòng: QC 0/6 ảnh, 0/115 đối tượng có lỗi được ghi nhận; đã sửa đủ 12/12 bẫy, có giới hạn mẫu nhỏ
