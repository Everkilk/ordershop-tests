## Tổng hợp các Scenario đã test thủ công

- Các testcase sẽ được liệt kê trong file **OrderShop_TestCase.xlsx**
- Các tài liệu liên quan tới **test api** với postman sẽ nằm trong folder **postman** và **docs** 

```

Module 1: Quản lí sản phẩm
Scenario ID | Mô tả
SC-001 | Xác minh trang Products tải thành công
SC-002 | Xác minh danh sách/lưới sản phẩm hiển thị các sản phẩm (hoặc hiển thị thông báo trạng thái trống nếu không có dữ liệu)
SC-003 | Xác minh mỗi thẻ sản phẩm hiển thị đầy đủ thông tin cơ bản (ví dụ: tên, giá, hình ảnh/ảnh thay thế)
SC-004 | Xác minh giao diện danh sách sản phẩm vẫn hiển thị đúng sau khi refresh trang (F5)
SC-005 | Xác minh giao diện danh sách sản phẩm vẫn hiển thị đúng khi dùng nút Back/Forward của trình duyệt
SC-006 | Xác minh tên/mô tả sản phẩm quá dài không làm vỡ bố cục (tự xuống dòng/ellipsis)
SC-007 | Xác minh định dạng hiển thị giá nhất quán (tiền tệ/số thập phân) giữa các sản phẩm
SC-008 | Xác minh xử lý khi ảnh sản phẩm bị lỗi/thiếu (hiển thị ảnh placeholder, không hiện icon ảnh hỏng)
SC-009 | Xác minh trang xử lý tình huống tải chậm ổn định (có loading, không bị treo)
SC-010 | Xác minh xử lý lỗi khi mất mạng (hiển thị lỗi/cho phép thử lại, không bị crash)
SC-011 | Xác minh người dùng có thể mở trang chi tiết sản phẩm từ danh sách
SC-012 | Xác minh trang chi tiết sản phẩm hiển thị đúng thông tin của sản phẩm đã chọn
SC-013 | Xác minh tên/giá trên trang chi tiết khớp với thông tin trên danh sách
SC-014 | Xác minh trang chi tiết xử lý product ID không hợp lệ/không tồn tại (hiển thị Not Found / trang lỗi an toàn)
SC-015 | Xác minh trang chi tiết sản phẩm tải đúng sau khi refresh
SC-016 | Xác minh khi quay lại từ trang chi tiết thì trở về danh sách sản phẩm thành công

Module 2: Tìm kiếm / Lọc / Sắp xếp 
Scenario ID | Mô tả
SC-017 | Xác minh tìm kiếm theo từ khóa trả về các sản phẩm phù hợp
SC-018 | Xác minh tìm kiếm không phân biệt hoa/thường (nếu áp dụng)
SC-019 | Xác minh tìm kiếm không có kết quả hiển thị “No results”/trạng thái trống đúng cách
SC-020 | Xác minh xóa nội dung tìm kiếm sẽ khôi phục lại danh sách đầy đủ
SC-021 | Xác minh áp dụng bộ lọc theo danh mục/trạng thái hiển thị đúng sản phẩm
SC-022 | Xác minh có thể áp dụng nhiều bộ lọc cùng lúc (nếu hỗ trợ)
SC-023 | Xác minh xóa/bỏ bộ lọc sẽ khôi phục lại danh sách ban đầu
SC-024 | Xác minh sắp xếp (ví dụ: theo giá/tên) thay đổi thứ tự sản phẩm đúng
SC-025 | Xác minh chuyển đổi tùy chọn sắp xếp cập nhật kết quả đúng, không bị trùng/thiếu sản phẩm

Module 3: Điều hướng
Scenario ID | Mô tả
SC-026 | Xác minh các liên kết điều hướng trên header/menu (nếu có) hoạt động đúng
SC-027 | Xác minh bấm logo/home sẽ quay về trang Products (nếu có)
SC-028 | Xác minh UI responsive khi thu nhỏ màn hình (kiểm tra hiển thị mobile)
SC-029 | Xác minh các thao tác chính có thể dùng bàn phím (Tab/Enter) để truy cập

Module 4: Kiểm tra dữ liệu 
Scenario ID | Mô tả
SC-030 | Xác minh tạo sản phẩm với dữ liệu hợp lệ thành công
SC-031 | Xác minh tạo sản phẩm khi thiếu các trường bắt buộc sẽ hiển thị thông báo validate
SC-032 | Xác minh tạo sản phẩm với định dạng giá không hợp lệ (chữ/ký tự đặc biệt) bị từ chối
SC-033 | Xác minh tạo sản phẩm với giá ở ngưỡng biên (0, âm, rất lớn) được xử lý đúng
SC-034 | Xác minh chỉnh sửa sản phẩm với dữ liệu cập nhật hợp lệ sẽ lưu thành công
SC-035 | Xác minh xóa sản phẩm yêu cầu xác nhận (nếu có) và thao tác xóa hoạt động đúng
SC-036 | Xác minh hiển thị thông báo lỗi khi server/API trả lỗi trong quá trình tạo/sửa/xóa
