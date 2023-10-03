# 2. Phân loại các thuật toán Machine Learning

## Nguồn bài viết

[Bài 2: Phân nhóm các thuật toán Machine Learning](https://machinelearningcoban.com/2016/12/27/categories/)

## Phân loại dựa trên phương thức học

### Supervised Learning - Học có giám sát

- **Định nghĩa**: Supervised Learning là thuật toán dự đoán đầu ra (outcome) của dữ liệu mới (new input) dựa trên các cặp (input, outcome) đã biết từ trước.
    - Cặp dữ liệu này còn được gọi là (data, label).
    - Supervised Learning là nhóm phổ biến nhất.
- **Định nghĩa theo cách toán học**
    
    ********************************Supervised Learning******************************** là khi ta có tập Input $X = \{ x_1, x_2,...\}$ và một tập Outcome tương ứng $Y = \{ y_1, y_2, ... \}$, với mỗi $\{ x_i, y_i \}$ là một vector.
    
    Các cặp dữ liệu $\{ x_i, y_i \}$ được gọi là Training Data.
    
    Từ Training Data, chúng ta cần tạo ra một ****hàm $f(x)$** để ánh xạ mỗi phần tử từ $X$ sang $Y$
    
    Từ đó, mỗi khi có một $x$ mới, ta có thể tìm được nhãn của nó $y = f(x)$
    
- **Ví dụ**
    - VD1: Trong nhận dạng chữ viết tay, ta có ảnh của hàng nghìn ví dụ của mỗi chứ số được việt bởi nhiều người khác nhau. Ta đưa các bức ảnh này vào một thuật toán, chỉ cho nó biết mỗi bức ảnh tương ứng với chữ số nào. Sau khi thuật toán tạo ra một mô hìn, tức làm một hàm số mà đầu vào là một bức ảnh và đầu ra là một chữ số, khi nhận được một bức ảnh mới, nó sẽ dự đoán bức ảnh đó chứa chữ số nào.
- **************Phân loại**************
    - **Classification (Phân loại)**: Một bài toán được gọi là Classification nếu các label của input data được chia thành một số hữu hạn nhóm.
        
        Ví dụ: Gmail xác định xem một email có phải là spam hay không; các hãng tín dụng xác định xem một khách hàng có khả năng thanh toán nợ hay không. Ở đây label được chia thành “Có” hoặc “Không”.
        
    - **Regression (Hồi quy)**: Nếu label không được chia thành các nhóm mà là một giá trị thực cụ thể.
        
        Ví dụ: Một căn nhà rộng $x$  $m^2$, có $y$ phòng ngủ và cách trung tâm thành phố $z$ km sẽ có giá là bao nhiêu? Ở đây label sẽ là giá của căn nhà, là một số thực.
        
    
    Gần đây, Microsoft có một ứng dụng dự đoán giới tích và tuổi dựa trên khuôn mặt. Phần dự đoán giới tính là ****************************Classification****************************, phần dự đoán tuổi là ********************Regression********************. (*Chú ý rằng phần dự đoán tuổi cũng có thể coi là **Classification** nếu ta coi tuổi là một số nguyên dương không lớn hơn 150, chúng ta sẽ có 150 class (lớp) khác nhau.)*
    

### Unsupervised Learning - Học không giám sát

- **Định nghĩa**: Unsupervised Learning là việc ta học mà chỉ có Input - X mà không có Output - Y (nhãn - label) tương ứng. Ta chỉ có Input, tính toán mà không biết Output tương ứng của nó.
    
    Thuật toán Unsupervised Learning sẽ dựa vào cấu trúc của một dữ liệu để thực hiện một công việc nào đó.
    
    Ví dụ: Phân nhóm (Clustering) hoặc giảm số chiều của dữ liệu (Dimension Reduction) để thuận tiện cho việc lưu trữ và tính toán.
    
    Những thuật toán loại này được gọi là Unsupervised learning vì không giống như Supervised learning, chúng ta không biết câu trả lời chính xác cho mỗi dữ liệu đầu vào. Giống như khi ta học, không có thầy cô giáo nào chỉ cho ta biết đó là chữ A hay chữ B. Cụm *khô*ng giám sát được đặt tên theo nghĩa này.
    
- **Định nghĩa một cách toán học**: Unsupervised Learning là khi ta chỉ có dữ liệu vào $X$ mà không biết nhãn $Y$ tương ứng
- **Phân loại**
    - **********Clusterting (Phân nhóm)**********: Là bài toán phân nhóm toàn bộ dữ liệu $X$ thành các nhóm nhỏ dựa trên sự liên quan giữa các dữ liệu trong mỗi nhóm.
        
        Ví dụ: Phân loại mảnh giấy dựa trên hình dạng tam giác, vuông, tròn,…
        
    - **Association**: Là bài toán giúp chúng ta khám phá ra một quy luật dựa trên nhiều dữ liệu cho trước.
        
        Ví dụ: Những khách hàng nam mua quần hóa thường có xu hướng mua thêm đồng hồ hoặc thắt lưng; Khán giả xem phim Spider Man thường xem thêm phim Bat Man. Dựa vào đó tạo ra một hệ thống gợi ý - Recommendation System.
        

### Semi-Supervised Learning - Học bán giám sát

- **Định nghĩa**: Semi-Supervised Learning là các bài toán khi ta cho một lượng lớn dữ liệu vào $X$ nhưng chỉ một phần trong chúng được gán nhãn.
    
    Những bài toán này nằm giữa hai nhóm được nêu bên trên.
    
- **Ví dụ**: Khi có một bức ảnh về việc mổ đẻ thì nó sẽ được gán nhãn *****y học*****.
    
    Thực tế cho thấy rất nhiều các bài toán Machine Learning thuộc vào nhóm này vì việc thu thập dữ liệu có nhãn tốn rất nhiều thời gian và có chi phí cao. Rất nhiều loại dữ liệu thậm chí cần phải có chuyên gia mới gán nhãn được (ảnh y học chẳng hạn). Ngược lại, dữ liệu chưa có nhãn có thể được thu thập với chi phí thấp từ internet.
    

### Reinforcement Learning - Học củng cố

- **Định nghĩa**: Reinforcement Learning là các bài toán giúp cho một hệ thống tự xác định hành vi dựa trên hoàn cảnh để đạt được lợi ích cao nhất.
    
    Hiện tại, Reinforcement Learning chủ yếu được áp dụng vào lý thuyết trò chơi, các thuật toán cần xác định nước đi tiếp theo để đạt được điểm số cao nhất.
    
- **Ví dụ**
    
    **Ví dụ 1:** [AlphaGo gần đây nổi tiếng với việc chơi cờ vây thắng cả con người](https://gogameguru.com/tag/deepmind-alphago-lee-sedol/). [Cờ vây được xem là có độ phức tạp cực kỳ cao](https://www.tastehit.com/blog/google-deepmind-alphago-how-it-works/) với tổng số nước đi là xấp xỉ $10^{761}$, so với cờ vua là $10^{120}$ và tổng số nguyên tử trong toàn vũ trụ là khoảng  $10^{80}$!! Vì vậy, thuật toán phải chọn ra 1 nước đi tối ưu trong số hàng nhiều tỉ tỉ lựa chọn, và tất nhiên, không thể áp dụng thuật toán tương tự như [IBM Deep Blue](https://en.wikipedia.org/wiki/Deep_Blue_(chess_computer)) (IBM Deep Blue đã thắng con người trong môn cờ vua 20 năm trước). Về cơ bản, AlphaGo bao gồm các thuật toán thuộc cả Supervised learning và Reinforcement learning. Trong phần Supervised learning, dữ liệu từ các ván cờ do con người chơi với nhau được đưa vào để huấn luyện. Tuy nhiên, mục đích cuối cùng của AlphaGo không phải là chơi như con người mà phải thậm chí thắng cả con người. Vì vậy, sau khi *học* xong các ván cờ của con người, AlphaGo tự chơi với chính nó với hàng triệu ván chơi để tìm ra các nước đi mới tối ưu hơn. Thuật toán trong phần tự chơi này được xếp vào loại Reinforcement learning. (Xem thêm tại [Google DeepMind’s AlphaGo: How it works](https://www.tastehit.com/blog/google-deepmind-alphago-how-it-works/)).
    
    **Ví dụ 2:** [Huấn luyện cho máy tính chơi game Mario](https://www.youtube.com/watch?v=qv6UVOQ0F44). Đây là một chương trình thú vị dạy máy tính chơi game Mario. Game này đơn giản hơn cờ vây vì tại một thời điểm, người chơi chỉ phải bấm một số lượng nhỏ các nút (di chuyển, nhảy, bắn đạn) hoặc không cần bấm nút nào. Đồng thời, phản ứng của máy cũng đơn giản hơn và lặp lại ở mỗi lần chơi (tại thời điểm cụ thể sẽ xuất hiện một chướng ngại vật cố định ở một vị trí cố định). Đầu vào của thuật toán là sơ đồ của màn hình tại thời điểm hiện tại, nhiệm vụ của thuật toán là với đầu vào đó, tổ hợp phím nào nên được bấm. Việc huấn luyện này được dựa trên điểm số cho việc di chuyển được bao xa trong thời gian bao lâu trong game, càng xa và càng nhanh thì được điểm thưởng càng cao (điểm thưởng này không phải là điểm của trò chơi mà là điểm do chính người lập trình tạo ra). Thông qua huấn luyện, thuật toán sẽ tìm ra một cách tối ưu để tối đa số điểm trên, qua đó đạt được mục đích cuối cùng là cứu công chúa.
    

---

## Phân loại dựa trên chức năng

Có một cách phân nhóm thứ hai dựa trên chức năng của các thuật toán. Trong phần này, tôi xin chỉ liệt kê các thuật toán. Thông tin cụ thể sẽ được trình bày trong các bài viết khác tại blog này. Trong quá trình viết, tôi có thể sẽ thêm bớt một số thuật toán.

### **Regression Algorithms**

1. [Linear Regression](https://machinelearningcoban.com/2016/12/28/linearregression/)
2. [Logistic Regression](https://machinelearningcoban.com/2017/01/27/logisticregression/#sigmoid-function)
3. Stepwise Regression

### **Classification Algorithms**

1. Linear Classifier
2. [Support Vector Machine (SVM)](https://machinelearningcoban.com/2017/04/09/smv/)
3. [Kernel SVM](https://machinelearningcoban.com/2017/04/22/kernelsmv/)
4. Sparse Representation-based classification (SRC)

### **Instance-based Algorithms**

1. [k-Nearest Neighbor (kNN)](https://machinelearningcoban.com/2017/01/08/knn/)
2. Learning Vector Quantization (LVQ)

### **Regularization Algorithms**

1. Ridge Regression
2. Least Absolute Shrinkage and Selection Operator (LASSO)
3. Least-Angle Regression (LARS)

### **Bayesian Algorithms**

1. Naive Bayes
2. Gaussian Naive Bayes

### **Clustering Algorithms**

1. [k-Means clustering](https://machinelearningcoban.com/2017/01/01/kmeans/)
2. k-Medians
3. Expectation Maximization (EM)

### **Artificial Neural Network Algorithms**

1. [Perceptron](https://machinelearningcoban.com/2017/01/21/perceptron/)
2. [Softmax Regression](https://machinelearningcoban.com/2017/02/17/softmax/)
3. [Multi-layer Perceptron](https://machinelearningcoban.com/2017/02/24/mlp/)
4. [Back-Propagation](https://machinelearningcoban.com/2017/02/24/mlp/#-backpropagation)

### **Dimensionality Reduction Algorithms**

1. [Principal Component Analysis (PCA)](https://machinelearningcoban.com/2017/06/15/pca/)
2. [Linear Discriminant Analysis (LDA)](https://machinelearningcoban.com/2017/06/30/lda/)

### **Ensemble Algorithms**

1. Boosting
2. AdaBoost
3. Random Forest

Và còn rất nhiều các thuật toán khác.

---

## **Tài liệu tham khảo**

1. [A Tour of Machine Learning Algorithms](http://machinelearningmastery.com/a-tour-of-machine-learning-algorithms/)
2. [Điểm qua các thuật toán Machine Learning hiện đại](https://ongxuanhong.wordpress.com/2015/10/22/diem-qua-cac-thuat-toan-machine-learning-hien-dai/)