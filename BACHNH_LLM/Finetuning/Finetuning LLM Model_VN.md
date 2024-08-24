# Finetuning LLM Model

## 1. Transfer learning

**Transfer learning (chuyển giao tri thức)** là việc model tận dụng tri thức của một pre-trained model khác nhằm gia tăng hiệu quả cho model.

Như ta đã biết, trong CNN:

- Convolutional Layer có tác dụng lấy ra những đặc trưng của ảnh. Qua từng lớp, Model sẽ học được các chi tiết khác nhau. (Điểm → Đường thẳng → Mũi, tai,… → Khuôn mặt)
- Sau đó đi vào Fully Connected Layer để giảm chiều dữ liệu,…

![image.png](images/image.png)

Vậy ta có thể sử dụng phần Convolutional Network của pre-trained model (những Conv Layer và Pool Layer) cho bài toán của chúng ta. Quá trình này được gọi là **Transfer learning**.

![Untitled](images/Untitled.png)

Qua biểu đồ trên, có thể nhìn thấy 3 ưu điểm của transfer learning:

- Performance khởi đầu tốt hơn
- Tốc độ gia tăng performance tốt hơn
- Đường tiệm cận của độ chính xác tối ưu cao hơn (higher asymptote).

Có 2 loại transfer learning:

- **Feature Extractor**: ConvNet của pre-trained model có output là các đặc điểm ảnh (tai như nào, mũi ra sao,…). Giờ các đặc điểm đó sẽ trở thành input cho bài toán phân loại ảnh linear classifier (linear [SVM](https://machinelearningcoban.com/2017/04/09/smv/), softmax classifier,..)  (linear regression hay logistic regression)
    - Hiểu đơn giản là lấy ConvNet nối với Output layer, trong đó Activation Function ở output layer có thể là softmax, logistic,…
- **Fine tuning**: Sau khi lấy ra các đặc điểm của ảnh bằng việc sử dụng ConvNet của pre-trained model, thì ta sẽ coi đây là input của 1 CNN mới bằng cách thêm các ConvNet và Fully Connected layer. Lý do là ConvNet của VGGFace 2 model có thể lấy ra được các thuộc tính của mặt người nói chung nhưng người Việt Nam có nhưng đặc tính khác nên cần thêm 1 số Convnet mới để học thêm các thuộc tính của người Việt Nam.
    - Hiểu đơn giản là lấy ConvNet nối vào một ConvNet khác của riêng ta để train cho những đặc điểm riêng biệt.

### 1.1. Feature Extractor

Ta chỉ giữ lại phần ConvNet trong CNN và bỏ đi FCs. Sau đó dùng output của ConvNet còn lại để làm input cho **mô hình Logistic Regression với nhiều output**.

![Bên trái là mô hình VGG16, bên phải là mô hình VGG16 chỉ bao gồm ConvNet (bỏ Fully conencted layer)](images/Untitled%201.png)

Bên trái là mô hình VGG16, bên phải là mô hình VGG16 chỉ bao gồm ConvNet (bỏ Fully conencted layer)

Mô hình **logistic regression với nhiều output** có 2 dạng:

1. **Dạng thứ nhất** là một neural network, không có hidden layer, hàm activation ở output layer là [softmax function](https://nttuan8.com/bai-7-gioi-thieu-keras-va-bai-toan-phan-loai-anh/#Xay_dung_model), loss function là hàm [categorical-cross entropy](https://nttuan8.com/bai-7-gioi-thieu-keras-va-bai-toan-phan-loai-anh/#Loss_function), giống như bài [phân loại ảnh](https://nttuan8.com/bai-7-gioi-thieu-keras-va-bai-toan-phan-loai-anh/).
    
    ![Untitled](images/Untitled%202.png)
    
2. **Dạng thứ hai** giống như bài logistic regression, tức là model chỉ phân loại 2 class. Mỗi lần ta sẽ phân loại 1 class với tất cả các class còn lại.
    
    ![Untitled](images/Untitled%203.png)
    

### 1.2. Fine tuning

Ta chỉ giữ lại phần ConvNet trong CNN và bỏ đi FCs. Sau đó thêm các Fully Connected layer mới vào output của ConvNet.

![Untitled](images/Untitled%204.png)

Khi train model, ta chia làm 2 giai đoạn:

**Giai đoạn 1**: Vì các fully connected layer ta mới thêm vào có các hệ số được khởi tạo ngẫu nhiên tuy nhiên các layer trong ConvNet của pre-trained model đã được train với ImageNet dataset nên ta sẽ không train (đóng băng/freeze) trên các layer trong ConvNet của model VGG16. Sau khoảng 20-30 epoch thì các hệ số ở các layer mới đã được học từ dữ liệu thì ta chuyển sang giai đoạn 2.

(Đọc thêm về Epoch tại đây [**Deep Learning Crash Course for Beginners**](https://www.notion.so/Deep-Learning-Crash-Course-for-Beginners-8c01dd2ad1834f7491025125d5085efe?pvs=21).)

![Untitled](images/Untitled%205.png)

**Giai đoạn 2**: Ta sẽ unfreeze các layer trên ConvNet của pre-trained model và train trên các layer của ConvNet của pre-trained model và các layer mới. Bạn có thể unfreeze tất cả các layer trong ConvNet của VGG16 hoặc chỉ unfreeze một vài layer cuối tùy vào thời gian và GPU bạn có.

![Untitled](images/Untitled%206.png)

<aside>
💡 **Nhận xét**

Accuracy của fine-tuning tốt hơn so với feature extractor tuy nhiên thời gian train của fine-tuning cũng lâu hơn rất nhiều. 

Giải thích đơn giản thì feature extractor chỉ lấy ra đặc điểm chung chung từ pre-trained model của ImageNet dataset cho các data của ta, nên không được chính xác lắm. 

Tuy nhiên ở phần fine-tuning ta thêm các layer mới, cũng như train lại 1 số layer ở trong ConvNet của VGG16 nên model giờ học được các thuộc tính, đặc điểm của các data của ta nên độ chính xác tốt hơn.

</aside>

### 1.3. Khi nào nên dùng transfer learning?

Có 2 yếu tố quan trọng nhất để dùng transfer learning đó là 

- Kích thước của dữ liệu bạn có
- Sự tương đồng của dữ liệu giữa mô hình bạn cần train và pre-trained model

Các trường hợp ta cần cân nhắc:

- **Dữ liệu nhỏ và tương tự với dữ liệu ở pre-trained model → Feature Extractor:** Vì dữ liệu nhỏ nên nếu dùng fine-tuning thì model sẽ bị overfitting. Hơn nữa là dữ liệu tương tự nhau nên là ConvNet của pre-trained model cũng lấy ra các đặc điểm ở dữ liệu của chúng ta. Do đó nên dùng feature extractor.
- **Dữ liệu lớn và tương tự với dữ liệu ở pre-trained model → Fine-tuning:** Giờ có nhiều dữ liệu ta không sợ overfitting do đó nên dùng fine-tuning.
- **Dữ liệu nhỏ nhưng khác với dữ liệu ở pre-trained model:** Vì dữ liệu nhỏ nên ta lên dùng feature extractor để tránh overfitting. Tuy nhiên do dữ liệu ta có và dữ liệu ở pre-trained model khác nhau, nên không nên dùng feature extractor với toàn bộ ConvNet của pre-trained model mà chỉ dùng các layer đầu. Lý do là vì các layer ở phía trước sẽ học các đặc điểm chung chung hơn (cạnh, góc,…), còn các layer phía sau trong ConvNet sẽ học các đặc điểm cụ thể hơn trong dataset (ví dụ mắt, mũi,..).
- **Dữ liệu bạn có lớn và khác với dữ liệu ở pre-trained model:** Ta có thể train model từ đầu, tuy nhiên sẽ tốt hơn nếu ta khởi tạo các giá trị weight của model với giá trị của pre-trained model và sau đó train bình thường.

**Lưu ý**

- Vì pre-trained model đã được train với kích thước ảnh cố định, nên khi dùng pre-trained model ta cần resize lại ảnh có kích ảnh bằng kích thước mà ConvNet của pre-trained model yêu cầu.
- Hệ số learning rate của ConvNet của pre-trained model nên được đặt với giá trị nhỏ vì nó đã được học ở pre-trained model nên ít cần cập nhật hơn so với các layer mới thêm.

## 2. Data augmentation

Ngoài transfer learning, có một kĩ thuật nữa giải quyết vấn đề có ít dữ liệu cho việc training model, đó là data augmentation. Augmentation là kĩ thuật tạo ra dữ liệu training từ dữ liệu mà ta đang có. Cùng xem một số kĩ thuật augmentation phổ biến với ảnh nhé

**Flip**: Lật ngược ảnh theo chiều dọc hoặc chiều ngang

![https://i0.wp.com/nttuan8.com/wp-content/uploads/2019/04/flip.png?resize=576%2C316&ssl=1](https://i0.wp.com/nttuan8.com/wp-content/uploads/2019/04/flip.png?resize=576%2C316&ssl=1)

**Rotation**: Quay ảnh theo nhiều góc khác nhau

![https://i0.wp.com/nttuan8.com/wp-content/uploads/2019/04/rotate.png?resize=561%2C299&ssl=1](https://i0.wp.com/nttuan8.com/wp-content/uploads/2019/04/rotate.png?resize=561%2C299&ssl=1)

**Scale**: Phóng to hoặc thu nhỏ ảnh

![https://i0.wp.com/nttuan8.com/wp-content/uploads/2019/04/scale.png?resize=568%2C290&ssl=1](https://i0.wp.com/nttuan8.com/wp-content/uploads/2019/04/scale.png?resize=568%2C290&ssl=1)

**Crop**: Cắt một vùng ảnh sau đó resize vùng ảnh đấy về kích thước ảnh ban đầu

![https://i0.wp.com/nttuan8.com/wp-content/uploads/2019/04/scale-1.png?resize=568%2C290&ssl=1](https://i0.wp.com/nttuan8.com/wp-content/uploads/2019/04/scale-1.png?resize=568%2C290&ssl=1)

**Translation**: dịch chuyển ảnh theo chiều x, y.

![https://i0.wp.com/nttuan8.com/wp-content/uploads/2019/04/translate.png?resize=548%2C281&ssl=1](https://i0.wp.com/nttuan8.com/wp-content/uploads/2019/04/translate.png?resize=548%2C281&ssl=1)

Tuy nhiên khi rotate hoặc translation thì ảnh bị những khoảng đen mà thường ảnh thực tế không có các khoảng đen đấy nên có một số cách để xử lý như: lấy giá trị từ cạnh của ảnh mới để cho các pixel bị đen, gán các giá trị đen bằng giá trị của ảnh đối xứng qua cạnh,…

Nên áp dụng kiểu augmentation nào thì tùy thuộc vào ngữ nghĩa của ảnh trong bài toán bạn đang giải quyết.