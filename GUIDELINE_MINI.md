# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe ô tô con, xe du lịch cá nhân (thường từ 4-7 chỗ). | Xe sedan, hatchback, SUV. Gầm thấp, mui xe bo viền, không có thùng chở hàng. |
| `truck` | Xe tải chở hàng trọng tải từ nhẹ đến nặng. | Phía sau buồng lái có thùng chở hàng rõ ràng (thùng kín, thùng bạt hoặc thùng hở), bánh xe thường lớn. Bao gồm cả xe bán tải (pickup). |
| `bus` | Xe buýt nội đô hoặc xe khách đường dài chở nhiều người. | Kích thước dài, dáng hình hộp chữ nhật, có nhiều cửa sổ kính sát nhau dọc theo thân xe. |
| `van` | Xe tải nhỏ (minivan), xe chở khách nhỏ (khoảng 16 chỗ) hoặc xe chở hàng cỡ nhỏ. | To và vuông vức hơn `car` nhưng nhỏ và không dài như `bus`. Thường có cửa lùa bên hông, cốp sau phẳng đứng. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Kéo các cạnh của bounding box (trên, dưới, trái, phải) chạm sát mép ngoài cùng của phần thân xe nhìn thấy được. Khung không bao lấy phần bóng râm (shadow) in trên mặt đường, ăng-ten quá nhỏ hoặc luồng khí thải.
- Vật thể bị che khuất: Vẽ bounding box bao lấy đúng phần diện tích xe còn nhìn thấy được (không đoán định và vẽ lấn sang phần vật cản). Nếu xe bị che khuất quá 50% hoặc phần bị che lấp mất các đặc trưng phân biệt loại xe, bỏ qua không gán nhãn.
- Vật thể bị cắt bởi rìa ảnh: Bounding box dừng lại đúng ở sát mép viền của bức ảnh. Nếu phần xe nằm trong ảnh còn quá ít (không đủ nhận diện là xe gì), bỏ qua không gán.
- Vật thể rất nhỏ/mơ hồ: Các xe ở khoảng cách quá xa (gần đường chân trời), bị mờ nhòe do chuyển động hoặc kích thước tổng thể quá nhỏ (ví dụ: chiều ngang/dọc < 15 pixel), không gán nhãn để tránh tạo nhiễu (noise) cho mô hình (đánh dấu là background).

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_015.jpg` | Xe bán tải dễ nhầm với xe `van`. | Gán `truck` | Do phần mũi xe và thân xe tách riêng biệt |
| `drive_042.jpg` | Xe bán tải (pickup truck) có khoang chở người phía trước và thùng hở phía sau. | Gán `truck` | Tuy kích thước chỉ bằng SUV cỡ lớn nhưng có cấu trúc thùng chở hàng tách biệt, chức năng khớp với nhóm xe tải chuyên dụng theo định nghĩa của lớp truck. |
| `drive_030.jpg` | Nhiều phương tiện nằm xe bị mời, bị che khuất khoảng 60-70%. | Bỏ qua (không gán nhãn) | Diện tích bị che khuất vượt mức cho phép (>50%), hình ảnh còn lại không đủ đặc trưng để máy học nhận diện chính xác, rủi ro làm nhiễu tập train. |
