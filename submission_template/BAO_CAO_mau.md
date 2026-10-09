# Báo cáo lab: lựa chọn tracker cho năm video

**Nhóm:** LeNguyenTramAnh  
**Thành viên:** Lê Nguyễn Trâm Anh

## Thiết lập chung

Các video được xử lý bằng detector cố định `yolo26n.pt`, kích thước ảnh 640 px và lớp `person`. Các tham số điều chỉnh trong bài là tracker, ngưỡng confidence (`conf`) và IoU NMS (`iou`). Với tracker dùng ngoại hình, cấu hình Re-ID là `osnet_x0_25_msmt17.pt`.

## 1. Cấu hình được chọn

| Video | Tracker | conf | iou | Cơ sở lựa chọn | Cấu hình đối chiếu |
|---|---|---:|---:|---|---|
| video_1 — quảng trường, camera tĩnh, ban ngày | StrongSORT | 0.3 | 0.5 | Đạt HOTA, MOTA và IDF1 cao nhất trong sáu cấu hình được chấm trên toàn bộ video. | ByteTrack: conf 0.15 / 0.3 / 0.5 với IoU 0.5; conf 0.3 với IoU 0.4 / 0.7. |
| video_2 — phố đêm, camera tĩnh trên cao, mật độ rất đông | StrongSORT | 0.3 | 0.5 | Re-ID bổ sung thông tin ngoại hình, phù hợp với cảnh đông và các tình huống người đi bộ che khuất nhau. | ByteTrack, conf 0.3, IoU 0.5. |
| video_3 — camera di động, ảnh độ phân giải thấp | DeepOCSORT | 0.3 | 0.5 | Kết hợp tín hiệu chuyển động và ngoại hình để theo dõi mục tiêu khi vị trí trong ảnh thay đổi nhanh. | OC-SORT, conf 0.3, IoU 0.5. |
| video_4 — trong nhà, camera tiến về phía trước, có phản chiếu kính | StrongSORT | 0.3 | 0.5 | Re-ID hỗ trợ phân biệt và duy trì danh tính khi góc nhìn thay đổi trong chuyển động của camera. | ByteTrack, conf 0.3, IoU 0.5. |
| video_5 — góc quay từ xe bus tại giao lộ đông, hình ảnh rung | OC-SORT | 0.3 | 0.5 | Tracker dựa trên chuyển động với cấu hình gọn, thích hợp làm lựa chọn cho chuỗi hình rung và nhiều chuyển động. | BoT-SORT, conf 0.3, IoU 0.5. |

## 2. Kết quả định lượng video_1

So sánh TrackEval trên toàn bộ 600 frame. StrongSORT với `conf=0.3`, `iou=0.5` được chọn làm cấu hình nộp.

| Cấu hình | HOTA | MOTA | IDF1 |
|---|---:|---:|---:|
| StrongSORT, conf 0.3, IoU 0.5 | **28.658** | **19.698** | **29.854** |
| ByteTrack, conf 0.15, IoU 0.5 | 27.314 | 18.314 | 26.987 |
| ByteTrack, conf 0.3, IoU 0.4 | 26.914 | 17.292 | 25.715 |
| ByteTrack, conf 0.3, IoU 0.5 | 26.912 | 17.292 | 25.713 |
| ByteTrack, conf 0.3, IoU 0.7 | 26.017 | 17.222 | 24.846 |
| ByteTrack, conf 0.5, IoU 0.5 | 25.262 | 15.866 | 23.438 |

Trong sáu cấu hình thử nghiệm toàn chuỗi, StrongSORT đứng đầu cả ba chỉ số được báo cáo. HOTA tổng hợp chất lượng phát hiện và liên kết track; MOTA phản ánh lỗi phát hiện và đổi ID; IDF1 đánh giá độ nhất quán danh tính qua các frame. Theo yêu cầu của lab, bảng điểm định lượng được thực hiện cho video_1; video_2–video_5 được lựa chọn tracker theo đặc điểm cảnh và kết quả xem preview.

## 3. Phân tích lựa chọn

Trên video_1, StrongSORT cho HOTA 28.658, MOTA 19.698 và IDF1 29.854, cao nhất trong các cấu hình được so sánh trên toàn chuỗi. Kết quả này cho thấy cấu hình Re-ID có lợi trong bài toán vừa phát hiện người vừa duy trì danh tính. Với video_2 đông và tối, StrongSORT được ưu tiên vì thông tin ngoại hình bổ trợ cho liên kết chuyển động khi có che khuất. Với video_3, DeepOCSORT được chọn để kết hợp ngoại hình với chuyển động trong cảnh camera di động; video_4 cũng ưu tiên Re-ID do camera tiến và có phản chiếu. Video_5 dùng OC-SORT làm cấu hình dựa trên chuyển động cho góc quay rung từ xe bus.

## 4. Hướng thử nghiệm tiếp theo

Có thể mở rộng so sánh bằng cách quét mịn `conf` và `iou`, đồng thời tập trung vào các đoạn có che khuất, giao cắt, phản chiếu và rung để lựa chọn tracker phù hợp hơn cho từng kiểu cảnh.
