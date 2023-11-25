# Docker Tutorial for Beginners - VN

[Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)

## 1. Docker là gì?

### 1.1. Khái niệm

> **Docker** là nền tảng (platform) cho việc xây dựng (building), chạy (running), vận chuyển (shipping) các ứng dụng. Nếu như ứng dụng chạy bình thường trên máy của ta, thì nó cũng sẽ chạy bình thường trên máy khác.
> 

Trong thực tế, đôi khi ứng dụng chạy tốt trên máy tính của ta, nhưng không tốt trên máy tính của người khác. Một vài nguyên nhân của việc này là:

- Thiếu một vài file
- Khác biệt về phiên bản phần mềm
- Khác biệt về cấu hình (configuration: env variable,…)

Với Docker, ta sẽ gói ứng dụng của ta với bất kỳ ứng dụng nào khác vào một package và chạy nó ở bất cứ đâu.

### 1.2. Lợi ích

- Cho phép nhiều ứng dụng sử dụng các phiên bản phần mềm khác nhau chạy song song với nhau trên cùng một máy tính.
    
    Giả sử có nhân viên mới tham gia dự án, họ không cần dành thời gian để set up máy. Họ chỉ cần gọi Docker để chạy ứng dụng. 
    
    Docker sẽ tự động tải và chạy những dependencies bên trong một môi trường độc lập (isolated environment) gọi là **Container**. 
    
    ![Hai ứng dụng chạy trên hai phiên bản Node khác nhau](Docker%20Tutorial%20for%20Beginners%20-%20VN%2020ee6c25fd02428c8a1a71c0a9b6a6ee/Untitled.png)
    
    Hai ứng dụng chạy trên hai phiên bản Node khác nhau
    
- Lợi ích thứ hai của Docker là cho phép ta loại bỏ ứng dụng và mọi dependencies của nó một cách dễ dàng.
    
    Nếu không có Docker, khi làm việc trên nhiều Project, máy tính sẽ có nhiều thư viện và công cụ được sử dụng bởi nhiều ứng dụng khác nhau. Sau đó, ta không thể loại bỏ các dependencies vì có thể ứng dụng khác vẫn đang sử dụng nó. 
    
    Với Docker, ta không cần lo về chuyện này vì mỗi ứng dụng được chạy bởi các dependencies bên trong một Container. Ta có thể xóa đi một ứng dụng và các dependencies của nó một cách an toàn.
    

---

## 2. Máy áo vs Container

> ******************Container****************** là một môi trường độc lập để chạy ứng dụng.
> 

> **Virtual Machine** là một trừu tượng hóa của một máy tính vật lý, tạo ra một môi trường ảo có hệ điều hành, ứng dụng như máy tính thực tế.
> 
> 
> Nhưng VM không phải phần cứng vật lý. 
> 

### 2.1. Virtual Machine

- Nếu ta cần chạy hai máy ảo Window và Linux trên Macbook . Để có thể tạo ra máy ảo và chạy chúng, ta cần sử dụng một công cụ là ********************Hypervisor********************.
    
    > Hypervisor là phần mềm ta dùng để tạo và quản lý máy ảo.
    > 
    
    Một vài Hypervisor phổ biến đó là Virtual Box (cross-platform), VMware (cross-playform), Hyper-v (Window only).
    

![Untitled](Docker%20Tutorial%20for%20Beginners%20-%20VN%2020ee6c25fd02428c8a1a71c0a9b6a6ee/Untitled%201.png)

- Lợi ích của việc sử dụng máy ảo đó là cho phép ta chạy các ứng dụng ở cách môi trường độc lập với nhau bên trong các máy ảo.
    
    ![Untitled](Docker%20Tutorial%20for%20Beginners%20-%20VN%2020ee6c25fd02428c8a1a71c0a9b6a6ee/Untitled%202.png)
    
    Chẳng hạn như ta có hai máy ảo, chạy hai App khác nhau, mỗi App sẽ sử dụng các dependencies khác nhau.
    
    Tất cả chúng đều được chạy trên cùng một máy tính nhưng ở những môi trường khác nhau.
    
- Vấn đề của việc sử dụng máy ảo chạy nhiều ứng dụng
    - Mỗi máy ảo sẽ cần một hệ điều hành hoàn chỉnh (MacOS, Window, Linux,…)
    - Vì vậy, sẽ tốn thời gian để khởi động máy ảo vì cần khởi động cả hệ điều hành (giống như khởi động máy tính)
    - Máy ảo sẽ chiếm tài nguyên của máy tính. Mỗi máy ảo sẽ chiếm một phần phần cứng của máy tính thật như CPU, RAM, ổ cứng. Vì vậy không thể chạy nhiều máy ảo.

### 2.2. Containers

- Container cho ta một môi trường độc lập tương tự với máy ảo nên ta có thể chạy nhiều ứng dụng một cách độc lập
- Nhưng chúng nhẹ hơn (more lightweight) vì không cần hệ điều hành nữa
- Sử dụng chung hệ điều hành của máy tính
- Khởi động nhanh hơn vì không cần khởi động hệ điều hành nữa
- Không cần tài nguyên phần cứng vì vậy ta hoàn toàn có thể chạy hàng trăm container song song

---

## 3. Kiến trúc của Docker

### 3.1. Kiến trúc của Docker

Docker sử dụng kiến trúc Client-Server:

- Client sẽ gọi tới Docker Engine (Server) bằng Restful API.
- Docker Engine sẽ sits on the background và xây dựng, chạy Container.
- Về mặt kỹ thuật, Container chỉ là một Process đặc biệt, sử dụng hệ điều hành chung với máy tính, cụ thể hơn, mọi Container sẽ sử dụng chung Kernel với máy tính.

### 3.2. Kernel

> **Kernel** là phần lõi của một hệ điều hành, nó như động cơ của ô tô, quản lý mọi phần mềm và phần cứng.
> 

![Untitled](Docker%20Tutorial%20for%20Beginners%20-%20VN%2020ee6c25fd02428c8a1a71c0a9b6a6ee/Untitled%203.png)

Mọi hệ điều hành đều có Kernel riêng của nó và môi Kernel sẽ có API khác nhau. Vì vậy, ta không thể chạy Window Application trên Linux vì ứng dụng đó cần phải giao tiếp với Kernel. Vì vậy, Linux Container chỉ có thể được chạy trên hệ điều hành Linux.

Tuy nhiên, với hệ điều hành Window, ta có thể chạy cả Linux Container và Window Container vì kể từ Window 10 trở đi, Kernel của Linux đã được shipped cùng với Window Kernel.

MacOS có Kernel riêng của nó, khác hoàn toàn với Kernel của Window và Linux. Kernel này không có native support cho continuous applications. Vì vậy, Docker trên Mac sử dụng một lightweight linux machine để có thể chạy Linux Container.

## 4. Cài đặt Docker

Với MacOS và Window, ta có Docker Desktop, bao gồm Docker Engine là hàng loạt các công cụ hỗ trợ khác. Tuy nhiên, Linux không có Docker Desktop mà chỉ có Docker Engine. 

Lưu ý rằng với Window, ta cần enable Hyper-V and Containers Windows features. Sau đó, ta sẽ gặp lỗi “WSL 2 installation is incomplete”, ta cần nâng cấp Linux Kernel nằm bên trong Window bằng cách bấm vào link ở thông báo lỗi.

![Untitled](Docker%20Tutorial%20for%20Beginners%20-%20VN%2020ee6c25fd02428c8a1a71c0a9b6a6ee/Untitled%204.png)

## 5. Development Workflow - Quy trình hoạt động

Ta chọn một ứng dụng và ******************dockerize****************** nó (******************dockerize****************** là việc ta làm thay đổi một phần nhỏ của ứng dụng để nó có thể chạy bằng Docker). Ta thường ******************dockerize****************** bằng cách thêm một `Dockerfile` vào trong ứng dụng đó.

`Dockerfile` là một file plain text bao gồm những hướng dẫn mà Docker sử dụng để gói ứng dụng vào Image.

Image chứa mọi thứ mà ứng dụng cần để chạy, thường bao gồm:

- Một hệ điều hành cut-down (A cut-down OS)
- Một runtime environment (như là Node)
- Các file của ứng dụng
- Thư viện từ bên thứ ba
- Environment variables.

Khi ta đã có một Image, Docker sẽ khởi động một Container sử dụng Image đó. Container chỉ một là process đặc biệt vì nó có file system của riêng nó mà được Image cung cấp. Vậy ứng dụng của ta sẽ được load vào bên trong Container, đây chính là cách ta chạy ứng dụng một cách locally trên máy tính của ta.

Vì vậy, thay vì ta chạy trực tiếp một ứng dụng bằng một process cụ thể, ta có thể gọi Docker và bảo nó chạy ứng dụng nó bên trong một container (với môi trường độc lập)

![Untitled](Docker%20Tutorial%20for%20Beginners%20-%20VN%2020ee6c25fd02428c8a1a71c0a9b6a6ee/Untitled%205.png)

1. Khi phía lập trình viên đã có Image rồi, họ sẽ push Image đó tới một Docker Registry, cụ thể là Docker Hub. (Docker Hub với Docker giống như Github với Git là nơi lưu trữ Image mà mọi người có thể sử dụng). 
2. Khi Image đã có trên Docker Hub, ta có thể pull nó về tới máy khác. Máy khác đó sẽ chứa Image giống hệt Image ở máy của Dev (cùng phiên bản ứng dụng, cùng dependencies). 

Vì vậy, ta có thể chạy ứng dụng tương tự với máy ở phía lập trình viên, cụ thể là bảo Docker sử dụng Image để chạy một Container cho ứng dụng này.

## Thực hành

### Tạo chương trình

Trong terminal, ta sẽ tạo một folder `hello-docker` và mở nó trong Visual Studio Code.

```
mkdir hello-docker
cd hello-docker
code .
```

Tạo ra một file mới `app.js`

```jsx
console.log("Hello Docker!");
```

<aside>
💡 Nếu như không có Docker, để deploy chương trình này, ta cần:

- Khởi động hệ điều hành
- Cài đặt Node
- Copy app files
- Chạy `node app.js`

Với Docker, ta sẽ viết các bước trên bên trong `Dockerfile` và để Docker gói ứng dụng của chúng ta lại.

</aside>

### Tạo Dockerfile

```
FROM --platform=linux/amd64 node:alpine
COPY . /app
WORKDIR /app
CMD node file.js
```

- Trong một số trường hợp, ta không cần `--platform=linux/amd64`.

Quay lại Visual Studio Code, tạo một file `Dockerfile` (không có extension) và viết hướng dẫn cho Docker để đóng gói ứng dụng của ta. 

- `FROM`: Ta bắt đầu với việc lựa chọn Base Image bằng cú pháp `FROM` (Base Image này chứa hàng loạt các file và ta sẽ thêm các additional file vào Base Image đó, việc này tương tự với kế thừa trong OOP).
    - Vậy ta sẽ sử dụng Base Image nào? Ta có thể dùng `linux` Image và cài đặt Node bên trên nó, hoặc ta có thể dùng `node` Image (đã được built on top of `linux`) (Ta có thể xem tên của các Image trên Docker Hub).
    - Lưu ý rằng, trên Docker Hub có thể có nhiều `node` Image vì có nhiều phiên bản (distribution) của Linux, vì vậy ta cần thêm thông tin sau dấu `:` để lựa chọn distribution cho Linux. Trong trường hợp này chúng ta sử dụng `node:alpine`.
- `COPY`: Tiếp theo, ta cần copy ứng dụng/chương trình của ta bằng cú pháp `COPY`, ta sẽ copy mọi file ở thư mục hiện tại, và đưa vào thư mục `/app`, và đưa vào Image. Vậy Image đó sẽ có một file system và trong file system đó, ta sẽ tạo ra một thư mục `/app` với các file mà ta đã copy vào.
- `CMD`: Cuối cùng, ta sẽ dùng cú pháp `CMD`, để chạy command giúp ta khởi động ứng dụng. Ở đây ta khởi động ứng dụng bằng command `node /app/app.js`.
- `WORKDIR`: Nếu như ta thêm cú pháp `WORKDIR /app` phía trước `CMD`, thì ta chỉ cần khởi động bằng cú pháp `node app.js`

### Đóng gói ứng dụng và Push lên Docker Hub

Bây giờ, ta sẽ bảo Docker đóng gói ứng dụng của chúng ta bằng cách vào Terminal và nhập:

```jsx
docker build -t hello-docker .
```

- `-t hello-docker`: Thêm tag để identify Image của ta
- `.`: Nơi chứa `Dockerfile`

Bên trong thư mục của ta, không có file Image nào cả vì Image không phải một file và sẽ không lưu trữ trong thư mục. Cách Docker lưu trữ Image rất phức tạp nên ta đừng suy nghĩ nhiều về nó.

Để xem các Image nằm trên máy tính của ta, ta chạy trong Terminal: 

```
docker images
```

![Untitled](Docker%20Tutorial%20for%20Beginners%20-%20VN%2020ee6c25fd02428c8a1a71c0a9b6a6ee/Untitled%206.png)

- REPOSITORY: Tên của Image
- TAG: lastest (mặc định của Docker), ta thường dùng tag để kiểm soát phiên bản của Image để mỗi Image có thể chứa các phiên bản khác nhau của ứng dụng của ta

Để chạy Image đó, ta chạy câu lệnh này:

```jsx
docker run hello-docker
```

Để publish lên Docker, ta làm như sau (Chưa có)

1. **Login to Docker Hub:**
    - Use the **`docker login`** command to log in to your Docker Hub account:
        
        ```bash
        docker login -u username -p password docker.io
        ```
        
    - Enter your Docker Hub username and password when prompted.
2. **Tag your Image:**
    - After successfully logging in, you need to tag your local image with the repository information on Docker Hub. Use the **`docker tag`** command:Replace **`yourusername`**, **`imagename`**, and **`tag`** with the values you used when building the image.
        
        ```bash
        docker tag hello-docker user/hello-docker
        ```
        
3. **Push your Image:**
    - Finally, use the **`docker push`** command to upload your image to Docker Hub:This command will push the specified image to your Docker Hub repository.
        
        ```bash
        docker push user/hello-docker
        ```
        

### Cách để tạo máy ảo và test docker

Truy cập: [https://labs.play-with-docker.com/](https://labs.play-with-docker.com/?_gl=1*ass8a*_ga*NjQwOTAwOTYzLjE3MDA2MTYxOTc.*_ga_XJWPQMJYHQ*MTcwMDY0NTYwMi40LjEuMTcwMDY0NTYyMy4zOS4wLjA.)

Đăng nhập và tạo instance mới. Máy ảo này sẽ chỉ có Linux và Docker. Ta sẽ pull Image:

```jsx
docker pull codewithmosh/hello-docker
```

Kiểm tra lại Image bằng câu lệnh:

```jsx
docker images
```

Chạy chương trình:

```jsx
docker run codewithmosh/hello-docker
```