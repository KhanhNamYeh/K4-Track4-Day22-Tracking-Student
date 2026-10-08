# Báo cáo lab: chọn tracker cho 5 video

**Nhóm:** 2 thành viên **Thành viên:** Nguyễn Đức Anh-2A202602625, Nguyễn Đặng Nam Khánh-2A202602741

Detector cố định: `yolo26n.pt`, ảnh 640 px, Re-ID `osnet_x0_25_msmt17`. Không đổi các mục này trong bài nộp chính.

## 1. Cấu hình đã chọn

Mỗi video: tracker bạn nộp, `conf`, `iou`, điều bạn **nhìn thấy** trên video, và một cấu hình đã thử rồi loại.

| Video | Tracker | conf | iou | Quan sát khi xem video | Đã thử nhưng loại |
|---|---|---|---|---|---|
| video_1 (quảng trường, tĩnh, ban ngày) | botsort | 0.3 | 0.7 | Frame 120–270: người áo tím ở tiền cảnh giữ ID 22 khi đi cùng nhóm; nhiều người nhỏ phía xa chưa có hộp. Chất lượng phát hiện chưa đủ tốt dù một số track gần camera ổn định. | botsort, conf=0.5, iou=0.5: chỉ có 3.349 dòng kết quả so với 4.943 dòng của bản chọn trên cùng 600 frame; không ưu tiên cấu hình này vì bản chọn vẫn bỏ sót nhiều người. |
| video_2 (phố đêm, tĩnh, rất đông) | ocsort | 0.3 | 0.4 | Frame 180–280: người áo tối ở nửa trái giữ ID 18, cặp áo sáng gần cột đèn có hộp riêng; nhiều người ở cụm đông phía trên vẫn thiếu hộp. Cột đèn và người đi sát nhau gây che khuất. | ocsort, conf=0.5, iou=0.5: 2.364 dòng trên 300 frame thử, so với 3.622 dòng của cấu hình chọn trên cùng đoạn; không ưu tiên tăng conf khi còn thiếu hộp ở vùng đông. |
| video_3 (camera di động, ảnh nhỏ) | ocsort | 0.3 | 0.4 | Frame 1–200: người áo sọc đỏ/trắng giữ ID 1 khi camera tiến gần; người nhỏ phía bên kia đường chỉ được phát hiện rải rác. Các đoạn sau có người sát camera bị cắt bởi mép ảnh. | ocsort, conf=0.5, iou=0.5: 1.292 dòng trên 300 frame thử, so với 1.677 dòng của cấu hình chọn; mức conf cao chưa phù hợp ưu tiên giữ phát hiện ở ảnh nhỏ. |
| video_4 (trong nhà, camera di chuyển) | deepocsort | 0.3 | 0.5 | Frame 260–300: người áo trắng đi cùng hướng giữ ID 6; frame 320 ID 6 chuyển sang người áo tối đi ngược chiều, đến frame 340 người áo trắng mang ID 39. Re-ID vẫn không ngăn được gán nhầm khi hai người che nhau. | deepocsort, conf=0.15, iou=0.5: 53 ID khác nhau trên 300 frame thử, so với 26 ID ở cấu hình chọn; chưa ưu tiên cấu hình sinh thêm nhiều track khi cảnh có kính và che khuất. |
| video_5 (trên xe bus, giao lộ đông) | botsort | 0.15 | 0.5 | Frame 310–410: có hộp trên nhóm chờ qua đường ở góc phải, nhưng chưa phủ hết nhóm đông trước cửa hàng; kích thước và vị trí người thay đổi nhanh khi xe tiến và rẽ. Người xa trong bóng râm vẫn dễ thiếu hộp. | botsort, conf=0.5, iou=0.5: 784 dòng trên 300 frame thử, so với 1.479 dòng của cấu hình chọn; không ưu tiên ngưỡng cao trong cảnh có nhiều người nhỏ. |

Căn cứ cấu hình: `video_1.txt` khớp toàn bộ file sweep tương ứng; `video_2.txt`–`video_5.txt` khớp từng dòng ở đoạn 300 frame đầu với cấu hình ghi trong bảng. Log sweep xác nhận tracker, conf và iou. Không có log riêng của lượt chạy đầy đủ để xác nhận độc lập tham số sau đoạn này.

Đã mở và trích khung hình từ năm file `runs/nop_bai/video_N_preview.mp4`, xem mẫu đầu, giữa, cuối và các đoạn chi tiết nêu trên. Số frame preview lần lượt là 600, 1.050, 837, 900, 750; mỗi file kết quả có dòng ở frame cuối tương ứng. Chưa có thư mục ảnh gốc để đối chiếu độc lập độ dài bản nộp với dữ liệu đầu vào. Thời gian trong preview được tính theo 20 FPS của file xuất, không dùng để suy ra FPS dữ liệu gốc.

Các cấu hình ở cột cuối đều có file kết quả và log trong `runs/sweep/`. Số dòng là số hộp qua các frame; số ID khác nhau không phải số lần đổi ID. Đây chỉ là căn cứ sàng lọc, không chứng minh cấu hình nào chính xác hơn: hộp tăng có thể là phát hiện đúng hoặc hộp giả, ID tăng có thể do người mới xuất hiện hoặc track bị đứt. Không có preview của từng lượt sweep hay bảng metric so sánh các lượt trong artifact hiện có, nên chưa kết luận bản chọn tối ưu hoặc giữ ID tốt hơn mọi cấu hình đã thử.

## 2. Số liệu video_1

Số liệu lấy từ `runs/nop_bai/video_1_metrics.txt` (kết quả chấm đã có, chưa chạy chấm lại trong lần rà soát này). Giữ nguyên thang số TrackEval in ra:

| Video | HOTA | MOTA | IDF1 | DetA | AssA |
|---|---:|---:|---:|---:|---:|
| video_1 | 30.004 | 19.278 | 29.749 | 18.426 | 49.123 |

| TP | FN | FP | IDSW | Frag | CLR_Re | CLR_Pr |
|---:|---:|---:|---:|---:|---:|---:|
| 4.178 | 14.403 | 563 | 33 | 105 | 22.485 | 88.125 |

Có 18.581 đối tượng-frame trong phép chấm CLEAR. Kiểm tra MOTA: `1 - (14403 + 563 + 33) / 18581 ≈ 0.19278`, tương ứng 19.278 trên bảng. Recall 22.485 thấp hơn nhiều so với precision 88.125: bỏ sót là vấn đề chính, bên cạnh 33 lần đổi ID và 105 lần phân mảnh track.

`video_2` đến `video_5` không có nhãn trong gói lab. Không điền số cho các video đó.

## 3. Phân tích

**Video_1:** BoT-SORT giữ ID 22 của người áo tím qua các frame 120–270, cho thấy một track gần camera khá ổn định khi nhóm người đi sát nhau. Camera tĩnh và chuyển động tương đối đều thuận lợi cho liên kết theo vị trí; ngoại hình có thể bổ sung thông tin khi các hộp gần nhau, nhưng đoạn xem này chưa tách được tác dụng riêng của Re-ID. NMS iou=0.7 cho phép giữ các hộp chồng lấn nhiều hơn ngưỡng 0.5, phù hợp để thử khi người đi gần nhau, đồng thời cần kiểm tra hộp trùng. DetA 18.426 thấp hơn AssA 49.123, cùng số FN lớn, cho thấy ưu tiên tiếp theo là giảm bỏ sót thay vì chỉ tập trung đổi tracker. IDF1 29.749 cũng không cho phép kết luận danh tính đã được giữ tốt trên toàn video.

**Video_2:** OC-SORT duy trì ID 18 của người áo tối trong đoạn frame 180–280, trong khi cụm người xa ở phía trên ảnh vẫn thiếu nhiều hộp. Camera tĩnh tạo điều kiện cho mô hình chuyển động, nhưng cột đèn, ánh sáng chói và người đi sát nhau vẫn gây mất quan sát. Conf=0.3 là mức giữ lại trong bản nộp; bản thử conf=0.5 sinh ít hộp hơn rõ rệt nên chưa phù hợp ưu tiên giảm bỏ sót. Iou=0.4 cần được kiểm tra thêm ở các cặp người chồng lấn vì NMS mạnh có thể loại cả hộp đúng. Chưa có bằng chứng đủ để khẳng định OC-SORT tốt hơn các tracker có Re-ID trong toàn cảnh đêm này.

**Video_3:** Người áo sọc đỏ/trắng giữ ID 1 từ frame 1 đến 200 dù camera tiến gần và hộp tăng kích thước. Điều đó cho thấy OC-SORT xử lý được đoạn chuyển động này, nhưng không chứng minh ổn định khi người bị che hoàn toàn hoặc ra khỏi ảnh. Ảnh 640×480 và người nhỏ phía xa làm thông tin ngoại hình ít rõ; đây là lý do hợp lý để giữ một cấu hình dựa vào chuyển động, song cần so sánh trực tiếp với Re-ID mới xác nhận được lợi thế. Conf=0.3 tránh mức cắt quá cao của lượt thử 0.5, trong khi vẫn còn bỏ sót các mục tiêu nhỏ. Khi người sát camera bị cắt bởi mép ảnh, cần phân biệt rời khung hình với lỗi mất track.

**Video_4:** DeepOCSORT có ngoại hình để hỗ trợ liên kết trong cảnh camera tiến tới và nhiều người đi ngược chiều. Tuy nhiên, frame 300–340 cho thấy ID 6 từ người áo trắng chuyển sang người áo tối, rồi người áo trắng nhận ID 39; đây là bằng chứng gán nhầm và đứt danh tính. Kính, nền sáng và che khuất là các yếu tố cần kiểm tra ở cảnh này, nhưng chưa thể quy riêng lỗi trên cho phản chiếu. Cấu hình conf=0.3, iou=0.5 là lựa chọn hiện có, chưa đủ cơ sở gọi là tốt nhất; lượt conf=0.15 sinh nhiều ID hơn chỉ là dấu hiệu cần xem lại. Nên xem liên tiếp quanh frame 300–340 và đối chiếu cùng đoạn của OC-SORT trước khi kết luận Re-ID giúp nhiều đến đâu.

**Video_5:** BoT-SORT với conf=0.15 tạo được hộp trên nhiều người ở góc phải giao lộ trong đoạn frame 310–410, nhưng vẫn thiếu hộp trong cụm đông trước cửa hàng. Camera trên xe làm nền và người dịch chuyển nhanh, đồng thời thay đổi kích thước mục tiêu khi tiến và rẽ. Ngoại hình là nguồn thông tin bổ sung hợp lý khi vị trí thay đổi mạnh, nhưng preview hiện tại chưa chứng minh BoT-SORT giữ ID tốt hơn OC-SORT trên cùng đoạn. Ngưỡng conf thấp được giữ để ưu tiên người nhỏ và vùng tối; cần xem thêm hộp giả và hộp nhấp nháy để đánh giá đánh đổi. Không coi một ID biến mất khi xe đi qua và người rời ảnh là lỗi đổi ID.

## 4. Nếu có thêm thời gian

Giữ nguyên detector, kích thước ảnh và mô hình Re-ID; xuất preview cùng đoạn cho các cấu hình sweep, xem kỹ frame 300–340 của video_4 và các cụm người xa của video_2/video_5. Quét conf mịn hơn quanh mức đang chọn, mỗi lượt chỉ đổi một tham số, rồi chấm lại chỉ video_1 và so sánh HOTA / MOTA / IDF1 cùng FN / FP / IDSW trước khi thay bản nộp.
