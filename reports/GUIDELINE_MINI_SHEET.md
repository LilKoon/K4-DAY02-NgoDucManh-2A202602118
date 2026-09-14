# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Ngô Đức Mạnh <br>
**MSSV:** 2A202602118<br>
**Hình thức:** Cá nhân<br>
**Mã cặp:** Solo

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: `drive_022` / Vật thể #3
- Dấu hiệu nhìn thấy: Xe có dạng hình hộp, thân dài hơn ô tô con nhưng kích thước tổng thể nhỏ, không có nhiều hàng cửa sổ lớn liên tiếp như xe khách/xe buýt tiêu chuẩn.
- Quy tắc áp dụng: `van` là xe thân hộp nhỏ, kín; trong khi `bus` yêu cầu thân xe khách dài, nhiều cửa sổ hoặc hàng ghế rõ ràng.
- Quyết định: Gán lớp `van`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đánh dấu `review_state: needs_review`. Nếu sau khi xem xét kĩ vẫn không thể phân biệt chắc chắn do xe ở quá xa, tôi sẽ xóa hộp giới hạn để tuân thủ quy tắc "Không đoán".

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_038` / Vật thể #12
- Dấu hiệu nhìn thấy: Xe có phần đầu gầm cao giống SUV, phía sau có nắp thùng thấp che kín (dạng xe bán tải - pickup).
- Quy tắc áp dụng: Quy tắc định nghĩa rõ xe bán tải dùng như xe con (không có thùng/ben chở hàng chuyên dụng tách biệt rõ ràng) thì gán vào lớp `car`. Lớp `truck` chỉ dành cho xe có thùng chở hàng, ben hoặc sàn hàng công vụ rõ ràng.
- Quyết định: Gán lớp `car`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Nếu xe bị che khuất nửa sau và không rõ là SUV hay xe tải nhỏ, tôi sẽ gắn thẻ `needs_review`. Nếu không có đủ căn cứ thị giác để xác định phần đuôi, tôi sẽ xóa nhãn.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_008` / Vật thể #25
- Dấu hiệu nhìn thấy khi phóng 100%: Phần đuôi của một chiếc ô tô con nằm sát lề phải và bị rìa bức ảnh cắt mất. Đồng thời, phần đầu của xe này đang bị một chiếc xe tải đi cùng chiều che lấp một nửa. Tuy nhiên, vẫn nhìn rõ mui xe và bánh xe.
- Giá trị `visibility`: `occluded` (do bị chiếc xe tải phía trước che khuất một phần).
- Giá trị `boundary`: `truncated` (do mép phải bức ảnh cắt ngang thân xe).
- Trạng thái `review_state`: `confident`
- Lý do: Dù xe bị cắt ngang (`truncated`) và bị che lấp (`occluded`), nhưng phần diện tích còn lại trong ảnh (kính chắn gió, mui xe, dáng sedan) vẫn cung cấp đủ bằng chứng thị giác rõ ràng để phân loại tự tin đây là một chiếc ô tô con (`car`) mà không cần phải đoán.

## 6. Xác nhận tự kiểm tra

- [X] Đã rà đủ bốn ảnh.
- [X] Đã kiểm vật thể thiếu và trùng.
- [X] Đã kiểm lớp và hình học từng hộp.
- [X] Mỗi hộp có đủ ba thuộc tính.
- [X] Đã xử lý mọi hộp `needs_review`.
- [X] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [X] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [X] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [X] Số vật thể thực tế: 122
