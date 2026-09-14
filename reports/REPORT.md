# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Ngô Đức Mạnh<br>
**MSSV:** 2A202602118<br>
**Hình thức:** Cá nhân<br>
**Mã cặp:** Solo

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh: drive_008, drive_022, drive_033, drive_038 
- Số vật thể thực tế: 122
- Mã SHA-256 của gói YOLO của bạn: 04b318439328b51f448518b09a0edfd1df8a4a89c0450846ac50e64ceb90978e
- Mã SHA-256 của gói CVAT gốc của bạn: 07d9b9180dd5fd740d0cc1340824e87bbe87f42ad32b1c1feaa83eb053966465
- Nguồn đối chiếu: Bộ tham chiếu do người hướng dẫn thực hành cấp
- Mã SHA-256 của gói đối chiếu: c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: 15:03

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu: 

Bài làm vẫn độc lập vì tôi đã hoàn tất toàn bộ quá trình tự gán nhãn trên CVAT, tự kiểm tra chất lượng và xuất các gói dữ liệu của riêng mình trước khi quyết định tải lên bộ nhãn tham chiếu của Lab Coach để đối chiếu.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| `drive_033` / Vật thể 1 | `car` | Xe sedan 4 chỗ, nằm trọn vẹn trong khung hình và không bị vật cản che khuất. | Phân loại là `car` (ô tô con). Các thuộc tính đi kèm: `visibility: clear` và `boundary: inside`. |
| `drive_038` / Vật thể 5 | `truck` | Xe có thùng chở hàng phía sau rất rõ ràng, nhưng phần gầm xe đang bị một chiếc xe con khác che lấp một phần. | Phân loại là `truck` (xe tải) do có thùng hàng. Các thuộc tính đi kèm: `visibility: occluded` và `boundary: inside`. |
| `drive_022` / Vật thể 2 | `bus` | Xe có thân dài, nhiều hàng cửa sổ kính liên tiếp, nhưng phần đuôi xe bị rìa phải của bức ảnh cắt ngang. | Phân loại là `bus` (xe buýt). Các thuộc tính đi kèm: `visibility: clear` và `boundary: truncated`. |
| `drive_008` / Vật thể 4 | `van` | Xe có dạng hộp nhỏ, kín, nhưng hình ảnh khá mờ, khó phân biệt chắc chắn với xe tải nhỏ có thùng kín. | Phân loại tạm là `van` (xe van). Các thuộc tính đi kèm: `visibility: unclear` và `review_state: needs_review` để quay lại kiểm tra. |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Ở `drive_038` / Vật thể 5, lớp là `truck` vì vật thể có thùng chở hàng. Thuộc tính `visibility: occluded` chỉ mô tả mức độ bị che khuất, còn `boundary: inside` chỉ mô tả vật thể vẫn nằm trong phạm vi ảnh; thay đổi các thuộc tính này không làm vật thể đổi từ `truck` sang lớp khác.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Gán nhãn một chiếc SUV cỡ lớn là `truck` (xe tải). | Lớp | Phóng to ảnh quan sát kỹ thấy xe không có thùng chở hàng chuyên dụng, dùng để chở người. | Đổi nhãn thành `car` (Quy tắc: SUV thuộc lớp ô tô con). |
| Vẽ hộp giới hạn bao gồm cả phần bóng râm của chiếc xe in trên mặt đường. | Hình học / Phạm vi | Rà soát lại các hộp, thấy phần viền dưới của hộp dư ra nhiều so với gầm xe. | Chỉnh lại cạnh dưới của hộp cho sát vào phần thân xe và bánh xe thực tế nhìn thấy (Quy tắc: Hộp phải vẽ sát vật thể). |
| Một chiếc xe bị rìa phải bức ảnh cắt ngang nhưng thuộc tính `boundary` đang để là `inside`. | Thuộc tính | Lọc (filter) và kiểm tra lại toàn bộ các vật thể nằm sát mép ảnh. | Chỉnh lại thuộc tính thành `boundary: truncated` (Quy tắc: Xe chạm mép và bị cắt thì đánh dấu `truncated`). |

- **Số hộp `needs_review` trước và sau khi kiểm:** Trước khi kiểm: 4 hộp. Sau khi kiểm (đã tự giải quyết hoặc tham khảo ý kiến): 0 hộp.
- **Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ:** Có một chiếc xe ở rất xa trong ảnh `drive_008`, bị lấp sau nhiều xe khác và độ phân giải mờ, không thể phân biệt là xe bán tải (`car`) hay xe tải nhỏ (`truck`). Tôi đã gắn thẻ `needs_review` trên CVAT, sau đó đối chiếu quy tắc "Không đoán" và quyết định xóa bỏ hộp giới hạn này vì không đủ căn cứ thị giác.

## 4. Một dòng nhãn YOLO

- **Dòng `class x_center y_center width height`:** `0 0.265828 0.568578 0.131062 0.087375`
- **Tên lớp và tọa độ điểm ảnh `xyxy`:** Lớp `0` (`car`) | Tọa độ pixel: `[128.2, 335.9, 212.1, 391.8]`
- **Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?** Định dạng YOLO chỉ kiểm tra tính hợp lệ về mặt cú pháp (đủ 5 cột, các giá trị tọa độ là số thực nằm trong khoảng `[0, 1]`). Hệ thống không thể tự xác minh xem tọa độ đó có thực sự vẽ sát vật thể trên ảnh hay không, hoặc nhãn phân lớp (ví dụ: `0` là `car`) có đúng với bản chất thực tế của xe trong ảnh hay không.

## 5. Huấn luyện và dự đoán thử

- **Ba mã ảnh huấn luyện:** `drive_022`, `drive_033`, `drive_038`
- **Mã ảnh thẩm định:** `drive_008`
- **Mô tả một dự đoán trong `detect_result.jpg`:** Mô hình dự đoán đúng một chiếc ô tô con ở góc trái thành lớp `car` với độ tin cậy 0.75, nhưng bỏ sót một chiếc xe tải ở phía xa.
- **Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào?** Gợi ý cần kiểm tra lại quy tắc về "Vật thể quá nhỏ hoặc mờ" để xem chiếc xe tải ở xa kia có thực sự đủ điều kiện gán nhãn hay không, hoặc do mô hình học chưa đủ tốt.
- **Minh chứng nào có thể bác bỏ nhận định của bạn?** Nếu đối chiếu với bộ quy tắc, chiếc xe tải đó bị che khuất quá 80% và không đủ căn cứ thị giác để gán nhãn, thì việc mô hình bỏ sót là đúng với tinh thần "Không đoán", lỗi nằm ở nhận định chủ quan của tôi.
- **Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?** Tập dữ liệu quá nhỏ (chỉ 3 ảnh huấn luyện, 1 ảnh thẩm định) không có tính đại diện cho các điều kiện thực tế (thời tiết, ánh sáng, góc cam đa dạng). Kết quả này rất dễ bị "học vẹt" (overfitting) và chỉ có giá trị chẩn đoán lỗi gán nhãn, không thể dùng để đo lường độ chính xác thực tế của mô hình (mAP).

## 6. Đối chiếu nhãn

- **Số hộp ghép được:** 48
- **IoU trung bình và trung vị:** Trung bình: 0.768935 | Trung vị: 0.805165
- **Mức đồng thuận lớp:** 0.729167 (khoảng 72.9%)
- **Số hộp phía bạn không ghép được:** 74
- **Số hộp phía đối chiếu không ghép được:** 2
- **Một điểm khác biệt cụ thể:** Hộp của tôi (màu đỏ) bao trọn cả bóng đổ của chiếc xe, trong khi hộp đối chiếu (màu xanh) chỉ vẽ sát gầm xe.
- **Quy tắc hoặc hành động sửa phát sinh:** Quay lại CVAT chỉnh sửa các hộp bị dư phần bóng đổ để bảo đảm quy tắc "Vẽ sát phần vật thể nhìn thấy".
- **Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?** Như cảnh báo trong kết quả, bộ nhãn đối chiếu chỉ để hỗ trợ phản hồi, không phải kết luận chất lượng sản xuất. Mức đồng thuận cao chỉ đo lường khả năng trùng khớp (tái lập) quy tắc giữa hai bộ nhãn. Vẫn có khả năng cả bạn và bộ nhãn đối chiếu đều cùng áp dụng sai một quy tắc (ví dụ: cùng đoán nhãn cho những xe ở quá xa, hoặc cùng phân loại sai bản chất xe).

## 7. Kiểm tra kho GitHub cá nhân

- [X] Có phiếu quy tắc với ba tình huống mơ hồ.
- [X] Có kết quả kiểm hai gói xuất.
- [X] Có thông tin lần huấn luyện và ảnh dự đoán.
- [X] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [X] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [X] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng:

- Ảnh phủ hộp đối chiếu "comparison_overlay.png" kết hợp cùng bảng dữ liệu "comparison_summary.json" và "comparison_iou.csv". Các minh chứng này phản ánh trung thực quá trình gán nhãn độc lập của tôi (IoU trung bình đạt 0.768), đồng thời chỉ ra chính xác các vị trí tôi và bộ nhãn tham chiếu áp dụng quy tắc khác nhau để từ đó rút kinh nghiệm.

Câu hỏi cho lab coach:

- Về quy tắc gắn nhãn thì đối với những chiếc xe ở rất xa gần đường chân trời hoặc bị che khuất hơn 80% nhưng vẫn lờ mờ nhận diện được loại xe, ranh giới giữa việc "tuân thủ quy tắc không đoán để xóa nhãn" và "cố gắng gán nhãn để cung cấp dữ liệu khó cho mô hình" nên được xác định như thế nào trong các dự án thực tế.

- Về huẩn luyện mô hình thì bước dự đoán thử, mô hình bỏ sót khá nhiều xe nhỏ ở xa. Điều này thuần túy là do tập dữ liệu huấn luyện quá nhỏ (chỉ 3 ảnh), hay do kích thước đầu vào (imgsz=640) đã làm mất đi các đặc trưng độ phân giải của vật thể nhỏ?
