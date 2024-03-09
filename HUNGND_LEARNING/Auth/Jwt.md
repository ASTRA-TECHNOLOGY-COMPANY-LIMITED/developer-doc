## Token là gì ?

1. **Token** là một chuỗi ký tự được tạo ra để đại diện cho một đối tượng hoặc một quyền truy cập nào đó
2. Ví dụ như access token, refresh token, jwt...
3. Token thường được sử dụng trong các hệ thống xác thực và ủy quyền để kiểm soát quyền truy cập của người dùng đối với tài nguyên hoặc dịch vụ.

## JWT

1. JSON Web Token (JWT) là một chuẩn mở ([RFC 7519](https://tools.ietf.org/html/rfc7519 "RFC 7519")) giúp truyền tải thông tin dưới dạng JSON.
2. Chuỗi JWT thường có cấu trúc gồm ba phần, mỗi phần được phân tách bởi dấu chấm (.): **Header** , **Payload** và **Signature** .
   - **Header** : Phần này chứa thông tin về loại token (thường là "JWT") và thuật toán mã hóa được sử dụng để tạo chữ ký (ví dụ: HMAC SHA256 hoặc RSA). Header sau đó được mã hóa dưới dạng chuỗi Base64Url.
   - **Payload** : Phần này chứa các thông tin mà người dùng định nghĩa. Payload cũng được mã hóa dưới dạng chuỗi Base64Url.
   - **Signature** : Phần này được tạo bằng cách dùng thuật toán HMACSHA256 (cái này có thể thay đổi) với nội dung là Base64 encoded Header + Base64 encoded Payload kết hợp một "secret key" (khóa bí mật).

## Access Token

1. Access Token là một chuỗi với **bất kỳ định dạng nào** , nhưng định dạng phổ biến nhất của access token là JWT.
2. Vấn đề của Access Token
   - Ở server, chúng ta muốn chủ động đăng xuất một người dùng thì không được, vì không có cách nào xóa access token ở thiết bị client được.
   - Client bị hack dẫn đến làm lộ access token, hacker lấy được access token và có thể truy cập vào tài nguyên được bảo vệ. Dù cho server biết điều đấy nhưng không thể từ chối access token bị hack đó được
   - Chúng ta có thể thiết lập thời gian hiệu lực của access token ngắn, ví dụ là 5 phút, thì nếu access token bị lộ thì hacker cũng có ít thời gian để xâm nhập vào tài nguyên của chúng ta hơn nhưng không hay vì login logout sau 5 phút
   - => Sử dụng thêm Refresh Token.

## Refresh Token

1. Refresh Token là một chuỗi token khác, được tạo ra cùng lúc với Access Token. Refresh Token có thời gian hiệu lực lâu hơn Access Token, ví dụ như 1 tuần
2. Flow xác thực với access token và refresh token sẽ được cập nhật như sau:
   - Client gửi request vào tài nguyên được bảo vệ trên server. Nếu client chưa được xác thực, server trả về lỗi 401 Authorization. Client gửi username và password của họ cho server.
   - Server xác minh thông tin xác thực được cung cấp so với cơ sở dữ liệu user. Nếu thông tin xác thực khớp, server tạo ra **2 JWT khác nhau** là Access Token và Refresh Token chứa payload là `user_id` (hoặc trường nào đó định danh người dùng). Access Token có thời gian ngắn (cỡ 5 phút). Refresh Token có thời gian dài hơn (cỡ 1 tuần). Refresh Token sẽ được lưu vào cơ sở dữ liệu, còn Access Token thì không.
   - Server trả về access token và refresh token cho client.
   - Client lưu trữ access token và refresh token ở bộ nhớ thiết bị (cookie, local storage,...)
   - Đối với các yêu cầu tiếp theo, client gửi kèm access token trong header của request.
   - Server verify access token bằng secret key để kiểm tra access token có hợp lệ không.
   - Nếu hợp lệ, server cấp quyền truy cập vào tài nguyên được yêu cầu.
   - Khi access token hết hạn, client gửi refresh token lên server để lấy access token mới.
   - Server kiểm tra refresh token có hợp lệ không, có tồn tại trong cơ sở dữ liệu hay không. Nếu ok, server sẽ **xóa refresh token cũ** và **tạo ra refresh token mới với expire date như cũ**
   - Server trả về access token mới và refresh token mới cho client.
