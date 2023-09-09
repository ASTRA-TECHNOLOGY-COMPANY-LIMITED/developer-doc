| col1 | col2 | col3 |
| ---- | ---- | ---- |
|      |      |      |
|      |      |      |

# Overview of the get started guide

- Xây dựng và chạy image với với container
- Chia sẻ hình ảnh bằng Docker Hub
- Triển khai các ứng dụng Docker bằng nhiều container với database
- Chạy ứng dụng bằng Docker Compose

## [What is a container?](https://docs.docker.com/get-started/#what-is-a-container)

### Là một sandboxed process chạy trên máy chủ và tách biệt với tất cả các quy trình chạy trên máy chủ đó tận dụng kernel namespaces và cgroups, tính năng có trong linux từ lâu , tóm lại

- Có thể chạy image. Có thể create, start, stop, move or delete container bằng Docker API or CLI
- Có thể chạy trên máy cục bộ, máy ảo hoặc được triển khai trên cloud
- Có tính di động(và có thể chạy trên mọi hệ điều hành)
- Các container cách ly nhau và chạy phần mềm, binaries, config, etc riêng
- Nếu biết chroot thì hay xem nó như phiên bản mở rộng của chroot

## [What is an image?](https://docs.docker.com/get-started/#what-is-an-image)

### Một container đang chạy sử dụng 1 filesystem cô lập, filesystem biệt lập này được cung cấp bởi một image và image đó phải chứa mọi thứ cần thiết để chạy ứng dụng (dependencies, config,scripts, ...vv) . Image chứa các config cho vùng chứa chẳng hạn như biến môi trường, lệnh mặc định command để chạy và các siêu dữ liệu khác

# Example

1. Cài đặt Docker Desktop
2. Cài đặt Git client
3. Cài đặt Visual Studio Code
4. Clone ứng dụng mẫu ở "https://github.com/docker/getting-started-app.git"
5. Tạo file Docker bằng lệnh "touch Dockerfile"
6. Tạo câu lệnh mẫu trong Docker
7. Chạy câu lệnh "docker build -t getting-started" để build image
8. Chạy câu lệnh "docker run -dp 127.0.0.1:3000:3000 getting-started" để start
   - -d (--detach) chạy ở chế độ nền
   - -p (--publish) tạo ánh xạ cổng giữa host và container (localhost:3000)
9. Mở web với port "[http://localhost:3000](http://localhost:3000/)" để xem
10. Xem nhanh các containers bằng Docker Desktop hoặc bật temiral lên chạy lệnh "`docker ps`"

# Update the application

1. Vào source code sửa 1 dòng code bất kì
2. Chạy lệnh "docker build -t getting-started "
3. Chạy lệnh "docker run -dp 127.0.0.1:3000:3000 getting-started"
4. Kiểm tra kết quả sẽ báo lỗi do container cũ đang chạy trên port 3000

## [Remove the old container](https://docs.docker.com/get-started/03_updating_app/#remove-the-old-container)

1. Để xóa 1 container trước tiên hãy dừng nó lại sau đó xóa nó bằng 2 cách sau
2. Với CLI
   - docker ps
   - docker stop `<the-container-id>`
   - docker rm `<the-container-id>`
3. Với Docker Desktop
   - Mở container view
   - Stop rồi Delete Container cần xóa

# Share the application

1. Sign in Docker Hub
2. Tạo **Repository**

## [Push the image](https://docs.docker.com/get-started/04_sharing_app/#push-the-image)

1. Trong CLI chạy lệnh push thấy trên docker hub (sẽ lỗi và thức hiện các bước tiếp theo)
2. Đăng nhập docker hub bằng command "docker login -u YOUR-USER-NAME"
3. Sử dụng lệnh docker tag để đặt tên mới cho image "docker tag getting-started YOUR-USER-NAME/getting-started"
4. Chạy lại câu lệnh push "docker push YOUR-USER-NAME/getting-started"

## [Run the image on a new instance](https://docs.docker.com/get-started/04_sharing_app/#run-the-image-on-a-new-instance)

    Image đã được tạo và đẩy lên docker hub giờ sẽ cho chạy ứng dụng trên một instance mới bằng cách sử dụng Play with Docker

    Note : Vì Play with Docker sử dụng nền tảng amd64 nên nếu sử dụng ARM based Mac with Apple Silicon thì phải build lại theo câu lệnh "docker build --platform linux/amd64 -t YOUR-USER-NAME/getting-started ." sau đó đẩy lại lên docker hub

1. Truy cập vào Play with Docker và đăng nhập thông qua tài khoản docker hub
2. Bắt đầu và **ADD NEW INSTANCE**
3. Ở terminal khởi động ứng dụng vừa được đẩy "docker run -dp 0.0.0.0:3000:3000 YOUR-USER-NAME/getting-started"
4. Nhấn vào open port 3000 và kiểm tra kết quả
