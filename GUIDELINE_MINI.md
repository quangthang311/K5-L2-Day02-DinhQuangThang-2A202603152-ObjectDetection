# Mini guideline — bài làm cá nhân

> Đọc cùng [GUIDE.md](GUIDE.md). Dùng schema và quy tắc chung dưới đây làm chuẩn; trước khi gán nhãn, hãy tự kiểm tra ảnh và ghi lại ít nhất ba ca mơ hồ thực tế ở cuối file. Không điền tình huống hoặc kết quả chưa quan sát.

## 1. Schema lớp

Giữ nguyên ID lớp khi xuất nhãn YOLO.

| ID | Lớp | Gán nhãn khi | Phân biệt / không gán nhãn khi |
| ---: | --- | --- | --- |
| 0 | `car` | Xe con, sedan, SUV, taxi hoặc xe bán tải | Không dùng cho xe buýt hoặc xe tải; xe bán tải thuộc `car` theo schema bài này |
| 1 | `truck` | Xe tải chở hàng hoặc xe đầu kéo | Không dùng cho xe bán tải/cabin nhỏ thuộc `car` |
| 2 | `bus` | Xe buýt hoặc minibus chở khách | Phân biệt với `van`; nếu không thể xác định chắc, ghi ca mơ hồ và hỏi giảng viên |
| 3 | `van` | Xe van/minivan chở hàng hoặc chở người | Không dùng cho xe buýt lớn (`bus`) hoặc xe con thông thường (`car`) |

Phân lớp theo loại xe nhìn thấy được, không theo trạng thái đang chạy hay đỗ. Không tự suy đoán mục đích sử dụng nếu ảnh không đủ căn cứ.

## 2. Quy tắc gán nhãn và vẽ box

- Mỗi xe thuộc bốn lớp là một đối tượng và có một box riêng; không gộp các xe.
- Vẽ box sát phần thân xe **nhìn thấy được**, không lấy thêm mặt đường, trời hoặc xe bên cạnh.
- Xe bị che một phần vẫn được gán nhãn nếu phần nhìn thấy đủ để nhận diện lớp; vẽ box theo phần nhìn thấy.
- Xe bị cắt bởi rìa ảnh vẫn được gán nhãn nếu nhận diện được; box chạm rìa ảnh và không đoán phần nằm ngoài ảnh.
- Không gán người đi bộ, xe đạp, mô tô/xe máy, biển báo, đèn giao thông, hình xe trên quảng cáo hoặc ảnh phản chiếu không phải xe thật trong cảnh.
- Nếu chỉ còn mảnh rất nhỏ hoặc không thể xác định chắc lớp, không đoán. Ghi ảnh và lý do ở mục 3, rồi hỏi giảng viên/labcoach.

## 3. Nhật ký quyết định cho ít nhất ba ca mơ hồ

Chọn các ca bạn thực sự gặp trong ảnh. Ghi tên ảnh/case ID chính xác, dấu hiệu quan sát được, quyết định bạn áp dụng và quy tắc/schema làm căn cứ. Nếu chưa phân loại chắc chắn, ghi `chờ giảng viên` thay vì tự tạo quy tắc mới.

| Ảnh/case ID | Dấu hiệu quan sát được và điểm mơ hồ | Quyết định đã áp dụng | Căn cứ theo schema/quy tắc |
| --- | --- | --- | --- |
| `drive_002` | Xe buýt lớn ở giữa ảnh bị các xe khác che khuất một phần; các thân xe chồng lấp trong dòng giao thông đông. | Gán xe buýt là `bus`; vẽ một box theo phần thân xe buýt nhìn thấy. Gán các xe khác thành box riêng nếu nhận diện được. | Một xe là một box; xe bị che vẫn được gán nhãn khi phần nhìn thấy đủ để nhận diện lớp. Không gộp nhiều xe. |
| `drive_004` | Một số xe ở xa trong vùng tối bị nhỏ, mờ và lẫn với ánh đèn; không đủ đặc trưng để phân biệt chắc các lớp. | Không đoán lớp và không gán các đối tượng không thể xác định chắc; ghi nhận case để hỏi giảng viên. | Chỉ gán các lớp trong schema khi có đủ căn cứ nhận diện; vật thể rất nhỏ/mơ hồ thì không tự đoán. |
| `drive_006` | Xe màu xanh nhạt ở góc dưới bên phải có dáng cao, thân dài kiểu van/minivan nên dễ lẫn với xe ô tô con. | Gán `van`, không gán `car`; vẽ box sát phần thân xe nhìn thấy. | Schema xếp van/minivan chở hàng hoặc chở người vào `van`; không suy đoán mục đích sử dụng nếu ảnh không cho biết. |

> Khi gặp tình huống mới làm thay đổi cách hiểu schema, hỏi giảng viên/labcoach trước khi áp dụng hàng loạt. Không thay đổi ID lớp hoặc tự thêm lớp.
