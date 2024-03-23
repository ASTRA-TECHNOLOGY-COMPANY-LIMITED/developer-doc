# Nestjs

## I. Giới thiệu
    - Nestjs là gì? 
       - NestJS là một framework Node.JS cho phép xây dựng ứng dụng phía server. Nest mở rộng các framework Node.js như Express hay Fastify để bổ sung thêm nhiều module hay thư viện hỗ trợ việc xử lý tác vụ. Đây là một framework mã nguồn mở, sử dụng TypeScript và rất linh hoạt để xây dựng các hệ thống backend
    - Tại sao lại dùng Nestjs
        - Cho phép develop nhanh và hiệu quả hơn. Khả năng mở rộng tốt, dễ bảo trì ứng dụng.
        - Là framework Node.js phát triển mạnh trong 3 năm trở lại đây. Cộng đồng hỗ trợ lớn, tích cực.
        - Kết hợp phát triển front-end và mid-tier, một đặc điểm vượt trội so với hầu hết các ngôn ngữ khác.
        - Sử dụng TypeScript, cho phép thích ứng nhanh chóng với các thay đổi khi JavaScript đang ngày càng phát triển mạnh mẽ.
        - Được xây dựng chuyên dùng cho các ứng dụng doanh nghiệp có quy mô lớn.
        - Cung cấp kiến trúc ứng dụng độc lập, cho phép các developer tạo ra những ứng dụng dễ test, dễ mở rộng và dễ bảo trì.
        - Cho phép xây dựng ứng dụng Rest API, MVC, microservices, GraphQL, Web Socket hay CRON job.
## II. Cách cài đặt
    - Yêu cầu nodejs >=16
    - Chọn folder bạn muốn lưu và bật terminal gõ câu lệnh sau
    - $ npm i -g @nestjs/cli
      $ nest new project-name
    - sau khi chạy 2 lệnh trên ta được folder nestjs với cấu trúc sau
![Alt text](image.png)

    -Ta chỉ cần để ý đến thư mục src với ba file được tạo sẵn:
        . main.ts: File để khởi tạo các đối tượng chạy ứng dụng, chẳng hạn ta có thể dùng lệnh
        . NestFactory.create() để tạo instance cho Nest.
        . app.module.ts: Là module gốc của ứng dụng, có trách nhiệm đóng gói mọi thứ có trong project.
        . app.controller.ts: Chứa các router để xử lý các request và trả về response cho client.
        . app.services.ts: Chứa các hàm xử lý logic cho service, chẳng hạn như ứng dụng có service kết nối đến DB hoặc xử lý file,…
        . app.controller.spec.ts: File dùng để viết unit test cho các controller.
## III.Thành phần
![Alt text](overview.png)
    - Nestjs gồm 3 thành phần chính: controller, provider và module.
###     1. Controllers
![Alt text](controller.png)

        Controllers chịu trách nhiệm nhận các request từ clide side và trả lại thông qua routing. Và dưới đây là 1 ví đụ đơn giản

        `import { Controller, Get } from '@nestjs/common';

            @Controller('cats')
            export class CatsController {
            @Post('/create')
            create(@Body: body: any): string {
                return 'This action returns all cats';
            }
            @Get()
            findAll(): string {
                return 'This action returns all cats';
            }
            @Get(:id)
            findAll(): string {
                return 'This action returns all cats';
            }
            }`
###     Route:

            - NestJs controller hỗ trợ tốt cho các kiểu route thông thường như ví dụ trên và ở đây chúng ta cũng có thể dùng Route wildcards với cú pháp sau: `@Get('ab*cd')` thì nó sẽ mactch với "abcd", "abccd", "ab_cd".Dấu gạch nối ( -) và dấu chấm (.) được hiểu theo nghĩa đen bằng các đường dẫn dựa trên chuỗi.
###     Header

            - Chúng ta có thể tự custom respose header bằng cách sử dụng @Header() hoặc dùng res.header()
            `@Post()
             @Header('Cache-Control', 'none')
             create() {
                return 'This action adds a new cat';
             }`

###     2.Providers

            Trong NestJS, "providers" là một trong những khái niệm cơ bản và quan trọng nhất, chúng đóng một vai trò trung tâm trong cách thức hoạt động của ứng dụng. Providers có thể là bất kỳ thứ gì có thể được "inject" (tiêm) vào các phần khác của ứng dụng, như là các lớp dịch vụ, giá trị hằng số, hoặc thậm chí là một hàm đơn giản. Khái niệm này giúp NestJS hỗ trợ tính năng Dependency Injection (DI), một kỹ thuật thiết kế phần mềm cho phép việc tạo ra các lớp phụ thuộc một cách linh hoạt và dễ dàng quản lý.
![Alt text](provider.png)
            Dưới đây là một số điểm chính về providers trong NestJS:

            1. Khái niệm cơ bản:
                - Dependency Injection: Providers cho phép NestJS áp dụng mô hình Dependency Injection, giúp giảm sự phụ thuộc trực tiếp giữa các lớp phần mềm. Thay vào đó, các phụ thuộc (dependencies) được "inject" vào lớp cần chúng thông qua constructor hoặc thông qua các setter/getter methods.
                - Reusable: Providers thường được tạo ra như là các singleton, điều này có nghĩa là bạn có thể tái sử dụng chúng trong nhiều phần khác nhau của ứng dụng mà không cần phải tạo ra nhiều thực thể.

            2. Cách sử dụng:
                - Trong một ứng dụng NestJS, bạn có thể định nghĩa providers trong một module thông qua thuộc tính providers trong decorator @Module(). Sau đó, bạn có thể yêu cầu NestJS inject những providers này vào các controllers, các providers khác, hoặc modules thông qua constructor của chúng.

            3. Loại Providers:
                - Dịch vụ (Services): Lớp dịch vụ thực hiện một số logic kinh doanh cụ thể và có thể được inject vào các controllers hoặc các services khác.
                - Repositories: Các lớp thao tác dữ liệu, thường được sử dụng để tương tác với cơ sở dữ liệu.
                - Factory Providers: Cung cấp một hàm factory để tạo ra các providers.
                - Value Providers: Cung cấp một giá trị cụ thể làm provider, có thể hữu ích cho việc cấu hình ứng dụng hoặc cung cấp mock data cho testing.
                - Class Providers: Sử dụng một lớp cụ thể như một provider, cho phép bạn tùy chỉnh việc triển khai.

            4. Lợi ích:
                - Loose Coupling: Giảm sự phụ thuộc giữa các phần của ứng dụng, làm cho code dễ bảo trì và mở rộng hơn.
                - Testability: Việc sử dụng DI và providers giúp việc mock và test các phần của ứng dụng trở nên dễ dàng hơn.
                - Flexibility: Dễ dàng thay đổi giữa các triển khai của cùng một interface mà không cần sửa đổi code sử dụng interface đó.

###     3. Modules

        - Trong NestJS, module là một lớp được chú thích bằng decorator @Module(). Decorator @Module() cung cấp metadata mà NestJS sử dụng để tổ chức cấu trúc ứng dụng. Mỗi ứng dụng có ít nhất một module gốc, và module gốc này là điểm xuất phát mà NestJS sử dụng để xây dựng đồ thị ứng dụng - cấu trúc dữ liệu nội bộ mà NestJS dùng để giải quyết các mối quan hệ và phụ thuộc giữa module và provider.
![Alt text](modules.png)

        Module trong NestJS thường bao gồm:
            - Providers: Các dịch vụ sẽ được khởi tạo bởi injector của NestJS và có thể được chia sẻ ít nhất trong module này.
            - Controllers: Tập hợp các controller được định nghĩa trong module này và cần được khởi tạo.
            - Imports: Danh sách các module đã nhập khẩu mà xuất các providers cần thiết trong module này.
            - Exports: Tập con các providers được cung cấp bởi module này và nên có sẵn trong các module khác nhập khẩu module này
###     4. Middleware

            - Trong NestJs, middleware là một chức năng được gọi trước khi route handle xử lí yêu cầu. MiddleWare có quyền truy cập đối tượng request và respose cũng như hàm next()
![Alt text](middleWare.png)
            
            - Middle ware có thể thực hiện:
                . thực hiện bất kì code nào
                . thay đổi request và respose obj
                . kết thúc chu kì request-respose
                . gọi hàm middleware tiếp theo với next(). Nếu middleware hiện tại không được next() để sang bước tiếp theo thì request sẽ bị treo

            - MiddleWare trong nestjs có thể được triển khai dưới dạng hàm hoặc 1 lớp với decorator @Injectable






