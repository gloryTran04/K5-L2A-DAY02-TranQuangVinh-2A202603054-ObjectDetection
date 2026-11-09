# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con: sedan, hatchback, SUV, taxi, xe bán tải; không gồm xe thân hộp cao (van), xe buýt, xe tải, mô tô. | Khác van ở chỗ có nắp capo nhô rõ phía trước kính lái và thân thấp; bán tải có thùng hở ngắn vẫn là car. Ví dụ: drive_006, xe đỏ và xe trắng ở nửa dưới ảnh. |
| `truck` | Xe tải chở hàng, xe ben, xe bồn, xe đầu kéo và xe tải gắn thiết bị chuyên dụng; không gồm xe bán tải (thuộc car). | Khác van ở chỗ cabin và phần chở hàng tách thành hai khối riêng, thấy rõ khe hoặc bậc giữa cabin và thùng. Ví dụ: drive_008, xe ben đỏ chở đất phía trên giữa ảnh; drive_037, xe bồn xanh dương ở nửa trái phía trên. |
| `bus` | Xe buýt, xe buýt nối toa, minibus chở khách; không gồm van chở người (thuộc van). | Khác van ở chỗ thân dài gấp khoảng 2–3 lần xe con, có nhiều ô cửa sổ liên tiếp dọc thân và bảng số tuyến trên đầu xe. Ví dụ: drive_009, xe buýt trắng đỏ lớn ở nửa phải; drive_022, xe buýt nối toa vàng xanh. |
| `van` | Xe van, minivan chở hàng hoặc chở người; không gồm xe buýt lớn (bus) và xe con thông thường (car). | Khác car ở chỗ thân hình hộp cao, đầu xe ngắn gần như không có capo; khác truck ở chỗ cabin và khoang sau liền thành một khối kín. Ví dụ: drive_008, xe van trắng ở giữa ảnh, ngay bên trái xe buýt nối toa; drive_024, các xe van màu be ở nửa phải. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: bốn cạnh box chạm điểm ngoài cùng nhìn thấy được của thân xe (kể cả hàng hóa để cố định trên xe); không lấy thêm bóng xe, vệt phản chiếu trên mặt đường ướt, quầng sáng đèn pha ban đêm, mặt đường, trời hay xe bên cạnh; phóng to khi vẽ để không còn khoảng hở rõ giữa cạnh box và thân xe.
- Vật thể bị che khuất: vẫn vẽ box nếu phần nhìn thấy đủ nhận ra lớp; box chỉ bao phần nhìn thấy, không đoán phần bị xe khác, cây hay biển báo che; hai xe chồng lên nhau vẽ hai box riêng; vật cản mảnh như cột đèn chắn ngang làm xe tách thành hai phần thì vẫn vẽ một box bao cả xe.
- Vật thể bị cắt bởi rìa ảnh: vẫn gán nếu phần nằm trong ảnh đủ nhận ra lớp; cạnh box nằm đúng trên rìa ảnh, không đoán phần nằm ngoài ảnh.
- Vật thể rất nhỏ/mơ hồ: gán nếu khi phóng to vẫn nhận ra là xe bốn bánh và xác định được lớp; nếu không xác định chắc lớp thì không tự đoán, ghi tên ảnh vào bảng dưới và hỏi giảng viên. Không gán mô tô, xe đạp, xe ba bánh, người đi bộ, phản chiếu hoặc hình xe trên bảng quảng cáo.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_006` | Xe nhỏ màu xanh ngọc có giá nóc ở mép phải, giữa ảnh: thân cao như hình hộp nhưng chỉ dài bằng xe con, đầu xe ngắn. | Không tự chốt giữa car và van: ghi case ID, hỏi giảng viên trước khi khóa nhãn và gán theo câu trả lời. | Thân cao, đầu ngắn giống van nhưng kích thước bằng xe con; GUIDE quy định van/minivan mơ hồ với car thì không tự chọn mà ghi case ID và hỏi. |
| `drive_014` | Xe đỏ thân hộp có chữ trắng ở góc trên phải, bị rìa phải cắt một phần; dài hơn van thường nhưng ngắn hơn xe buýt (cùng loại xe ở góc trên phải drive_038). | Không tự chốt giữa van và bus: ghi case ID, hỏi giảng viên trước khi khóa nhãn; gán cùng một lớp cho cả hai ảnh; box dừng đúng ở rìa ảnh. | Schema xếp minibus chở khách vào bus nhưng van chở người vào van; GUIDE quy định ca mơ hồ giữa van và bus thì hỏi, không tự chọn. |
| `drive_009` | Xe màu xanh lá phía trên bên trái, ngay bên trái xe buýt trắng đỏ lớn: cabin phía trước, phía sau là thùng hàng kín hình hộp. | Gán truck | Cabin và thùng hàng tách thành hai khối riêng, đúng định nghĩa truck; van phải là một khối liền. |
| `drive_024` | Xe ba bánh có mái che ở giữa nửa dưới ảnh, trên làn gần hàng rào phân cách; xe ba bánh chở hàng còn có ở drive_011 (thùng vàng) và drive_034 (thùng xanh). | Không gán ở mọi ảnh. | Xe ba bánh không thuộc bốn lớp của schema; GUIDE quy định không gán vật thể ngoài schema. |
| `drive_042` | Xe tải chở đống bao hàng chất cao ở nửa phải ảnh. | Gán truck; box bao cả phần hàng chất trên thùng. | Hàng hóa chất cố định và di chuyển cùng xe nên là phần nhìn thấy của xe; box không lấy bóng hay mặt đường. |
| `drive_019` | Xe trắng có cần cẩu và thiết bị kéo ở giữa nửa dưới ảnh (xe cứu hộ); cùng xe này xuất hiện ở drive_014, drive_034, drive_038, drive_046. | Gán truck ở cả năm ảnh. | Có cabin riêng, phần sau là sàn và thiết bị chuyên dụng; không phải xe con và không phải van thân kín. |
