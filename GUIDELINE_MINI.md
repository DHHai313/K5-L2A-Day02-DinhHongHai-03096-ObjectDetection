# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp     | Định nghĩa của bạn                                                                             | Ví dụ/điểm phân biệt                                                                |
| ------- | ---------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| `car`   | Xe con chở người, gầm thấp, tối đa 5 chỗ: sedan, hatchback, SUV, crossover                     | Có cốp/khoang hành lý liền thân; SUV cỡ lớn vẫn là car                              |
| `truck` | Xe tải/bán tải có thùng hàng hoặc khoang chở hàng riêng, gồm pickup, tải nhỏ, tải lớn, đầu kéo | Cabin tách biệt với thùng hàng; pickup vẫn là truck                                 |
| `bus`   | Xe khách/buýt chở nhiều người, thân dài, nhiều cửa sổ, ≥ 16 chỗ                                | Dài hơn hẳn xe khác, cửa sổ hai bên thành dãy; minibus lớn tính là bus              |
| `van`   | Xe thùng liền khối,xe van chở hàng nhỏ                                                         | Thân liền khối, cao hơn car, không có thùng rời như truck; xe 16 chỗ trở lên là bus |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: hình chữ nhật nhỏ nhất bao trọn phần nhìn thấy của xe (thân, bánh, cản), không chừa viền thừa, không cắt mất phần xe; không tính bóng đổ, gương chiếu hậu thò ra có thể bỏ qua.
- Vật thể bị che khuất: vẽ box quanh phần nhìn thấy được, không đoán phần bị khuất; nếu che quá ~70% hoặc không xác định được loại thì không gán nhãn.
- Vật thể bị cắt bởi rìa ảnh: vẫn gán nhãn nếu còn thấy đủ ~30% trở lên và nhận ra được loại; box dừng đúng tại rìa ảnh.
- Vật thể rất nhỏ/mơ hồ: bỏ qua nếu nhỏ hoặc không phân biệt được loại xe; nếu phân vân giữa hai lớp thì chọn lớp theo hình dáng tổng thể và ghi vào bảng ca mơ hồ.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh         | Tình huống                                                                                      | Quyết định cuối                 | Lý do theo schema                                                                                                                 |
| ----------- | ----------------------------------------------------------------------------------------------- | ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `drive_005` | Xe ở mép ảnh bị che khuất phần đuôi xe                                                          | Vẫn quyết định gán nhãn car     | Dù bị cắt bởi mép ảnh, phần còn lại vẫn có đầy đủ đặc trưng nhận diện rõ ràng là đuôi xe con. Box được vẽ chạm khít cạnh mép ảnh. |
| `drive_019` | Hai xe đi nối đuôi nhau qua giao lộ, xe phía sau bị đuôi xe phía trước che khuất phần thân sau. | Vẽ 2 box riêng biệt cho từng xe | Mỗi xe là một đối tượng độc lập, vẽ box quanh phần nhìn thấy được, không đoán phần bị khuất                                       |
| `drive_020` | Xe đỗ trên vỉa hè bị cầu đi bộ che khuất 80%                                                    | Không gán nhãn                  | che quá ~70% hoặc không xác định được loại thì không gán nhãn.                                                                    |
