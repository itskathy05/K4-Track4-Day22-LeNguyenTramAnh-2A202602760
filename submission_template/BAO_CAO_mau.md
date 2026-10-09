# Báo cáo lab: chọn tracker cho 5 video

**Nhóm:** LeNguyenTramAnh  
**Thành viên:** Lê Nguyễn Trâm Anh

Detector cố định: `yolo26n.pt`, ảnh 640 px, lớp người; cấu hình Re-ID `osnet_x0_25_msmt17.pt` theo notebook. Tracker và ngưỡng được lựa chọn riêng cho từng video.

## 1. Cấu hình đã chọn

| Video | Tracker | conf | iou | Quan sát / lý do lựa chọn | Cấu hình đã thử nhưng loại |
|---|---|---:|---:|---|---|
| video_1 (quảng trường, tĩnh, ban ngày) | StrongSORT | 0.3 | 0.5 | Chấm trên toàn bộ video bằng TrackEval; HOTA cao nhất trong 6 cấu hình đã so sánh. | ByteTrack, conf 0.15 / 0.3 / 0.5, IoU 0.5; ByteTrack conf 0.3, IoU 0.4 / 0.7. |
| video_2 (phố đêm, tĩnh, rất đông) | StrongSORT | 0.3 | 0.5 | Re-ID có thể giúp nối lại danh tính khi người bị che khuất trong cảnh đông và tối. | ByteTrack đã chạy thử trên 150 frame; chưa có so sánh trực quan toàn video. |
| video_3 (camera di động, ảnh nhỏ) | DeepOCSORT | 0.3 | 0.5 | Kết hợp thông tin chuyển động và ngoại hình, phù hợp để thử trong cảnh camera di động, độ phân giải thấp. | OC-SORT đã chạy thử trên 150 frame; chưa có so sánh trực quan toàn video. |
| video_4 (trong nhà, camera di chuyển) | StrongSORT | 0.3 | 0.5 | Dùng Re-ID để hỗ trợ duy trì ID khi camera tiến và góc nhìn thay đổi. | ByteTrack đã chạy thử trên 150 frame; chưa có so sánh trực quan toàn video. |
| video_5 (trên xe bus, giao lộ đông) | OC-SORT | 0.3 | 0.5 | Tracker dựa trên chuyển động, làm cấu hình khởi đầu gọn cho video rung mạnh. | BoT-SORT đã chạy thử trên 150 frame; chưa có so sánh trực quan toàn video. |

## 2. Số liệu video_1

Kết quả TrackEval trên toàn bộ 600 frame với cấu hình nộp StrongSORT (`conf=0.3`, `iou=0.5`):

| Cấu hình | HOTA | MOTA | IDF1 |
|---|---:|---:|---:|
| StrongSORT, conf 0.3, IoU 0.5 | 28.658 | 19.698 | 29.854 |
| ByteTrack, conf 0.15, IoU 0.5 | 27.314 | 18.314 | 26.987 |
| ByteTrack, conf 0.3, IoU 0.4 | 26.914 | 17.292 | 25.715 |
| ByteTrack, conf 0.3, IoU 0.5 | 26.912 | 17.292 | 25.713 |
| ByteTrack, conf 0.3, IoU 0.7 | 26.017 | 17.222 | 24.846 |
| ByteTrack, conf 0.5, IoU 0.5 | 25.262 | 15.866 | 23.438 |

Trong nhóm cấu hình đã thử, StrongSORT đạt HOTA, MOTA và IDF1 cao nhất. Kết quả được chấm bằng TrackEval sau khi tạo cấu hình đánh giá MOT17/train từ `seqinfo.ini`, vì gói dữ liệu gốc không cung cấp `eval_config.json`.

`video_2` đến `video_5` không có ground truth, nên không có điểm định lượng để báo cáo. Các cấu hình cho bốn video này là lựa chọn dựa trên đặc điểm cảnh và thử nghiệm 150 frame; cần xem preview toàn video để xác nhận chất lượng ID trước khi kết luận bằng mắt.

## 3. Phân tích

Trên video_1, StrongSORT đạt HOTA 28.658 và IDF1 29.854, nhỉnh hơn các cấu hình ByteTrack đã thử trên toàn bộ 600 frame. Kết quả này ủng hộ lựa chọn StrongSORT cho cảnh đông người, nơi duy trì danh tính quan trọng. Video_2 là cảnh phố tối và đông; Re-ID có thể giúp nối lại track sau che khuất, nhưng lựa chọn này chưa được xác nhận bằng so sánh toàn video. Video_3 có camera di động và ảnh nhỏ nên DeepOCSORT là lựa chọn cần kiểm tra kỹ vì camera chuyển động có thể làm sai giả định chuyển động đơn giản. Video_4 cũng cần xem các đoạn phản chiếu để phát hiện ID giả; video_5 cần kiểm tra các lần rung mạnh và giao cắt đông người vì tracker dựa trên chuyển động có thể đổi ID.

## 4. Nếu có thêm thời gian

Xem preview toàn bộ video_2 đến video_5 và so sánh tối thiểu hai tracker trên từng video; ghi lại các lần mất ID, đổi ID và hộp giả. Với video_1, thử thêm ngưỡng quanh cấu hình StrongSORT tốt nhất và kiểm tra các frame gây ID switch.
