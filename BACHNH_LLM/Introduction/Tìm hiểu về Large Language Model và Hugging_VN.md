# Tìm hiểu về Large Language Model và Hugging Face Transformer

URL: https://www.youtube.com/watch?v=yS05Wr6h2TU

## 1. Large Language Model (LLM)

Giả sử, ta có nuôi một con vẹt. Con vẹt này sẽ nghe ta nói câu “Tôi đó quá, tôi muốn ăn **bánh mì**”. Cho đến một ngày, ta chỉ cần nói “Tôi đói quá, tôi muốn ăn…”, con vẹt sẽ tự động trả lời là “bánh mì”.

![Untitled](images/Untitled.png)

Đây chính là **Language Model**. Nó là một thứ được train rất nhiều để rồi khi ta đưa input là prompt (câu nói chưa hoàn thiện), LM sẽ cho ta output phù hợp với prompt đó.

Dù con vẹt không hiểu ta đang nói gì, nhưng vì con vẹt nghe ta nói hàng ngày, nó sẽ có thể hoàn thiện câu nói của ta bằng từ có xác suất cao nhất.

Con vẹt hàng ngày nó lớn dần, nó bay đi khắp nơi. Sau một thời gian, nó nghe rất nhiều câu nói từ nhiều người khác nhau nên nó có thể trả lời được những câu hỏi phức tạp hơn rất nhiều.

Nó không còn chỉ là hoàn thiện câu nói nữa mà nó có thể trả lời những câu hỏi phức tạp. Lúc này con vẹt giống như một **Large Language Model** - một thực thể đã được train bởi một lượng dữ liệu rất lớn.

![Untitled](images/Untitled%201.png)

**Language Model** là một model xác suất mà có thể sinh ra từ ngữ dựa trên lượng dữ liệu mà nó được train. Nhưng thật ra, nó không hiểu ngôn ngữ đó.

![Untitled](images/Untitled%202.png)

Cách thức train:

- Bắt model học một lượng lớn thông tin
- Khi nó đã học đủ nhiều thì nó có thể đưa ra output phù hợp

Các model được train bởi các data set khác nhau thì output chúng đưa ra cũng khác nhau.

![Untitled](images/Untitled%203.png)

**Large Language Model (LLM)** là một loại thuật toán mà sử dụng deep learning và được train bởi những data set rất lớn để hiểu, tóm tắt, sinh, dự đoán các thông tin.

![Untitled](images/Untitled%204.png)

### 1.1. Tại sao lại gọi là “large”?

Gọi là large - lớn bởi vì:

1. **Có số lượng các parameter cực lớn** (mạng nơ-ron có nhiều layer, unit; các parameters sẽ được sinh ra từ đường kết nối các unit với nhau)
    - GPT-3.5 có số lượng parameter là 1.3 tỉ (về sau tăng lên 175 tỉ)
    - GPT-4 có số lượng parameter là 1.76 nghìn tỉ
2. **Được train trên một lượng data set cực lớn**
    - GPT-3.5 được train bởi 45TB text data

Để train ChatGPT, ta cần khoảng 10.000 Nvidia GPUs

### 1.2. LLM có thể làm gì?

Nó có thể thực hiện nhiều công việc xử lý ngôn ngữ tự nhiên như:

- Dịch ngôn ngữ
- Tổng hợp, tóm tắt văn bản
- Trả lời câu hỏi
- Phân tích cảm xúc
- Sinh ra câu trả lời phù hợp với input, ngữ cảnh của người dùng

## 2. Transfromers, Hugging Face

### 2.1. Kiến trúc của Transformer

![Untitled](images/Untitled%205.png)

Transformer là kiến trúc cốt lõi của các LLM.

Tại sao cần sử dụng Transformer? Các kiến trúc Recurrent Neural Network cũng có thể xử lý NNTN rồi mà?

![Untitled](images/Untitled%206.png)

- Kiến trúc của mạng RNN là đưa lần lượt từng tư → Không thể train song song → Mất thời gian, không tận dụng được lợi thế của GPU
- Đối với những câu văn dài, từ đầu sẽ mất liên hệ với từ ở cuối

Vì vậy, ta sử dụng kiến trúc Transformer. Với transformer, ta có thể ném cả câu văn vào mạng để xử lý và output, ta cũng có cả câu văn thay vì phải đưa từng từ vào như RNN.

![Untitled](images/Untitled%207.png)

Vì các từ được ném vào một lúc nên ta cần:

- Positional Encoding để đánh dấu xem từ nào vào trước, từ nào vào sau
- Attention và Self-Attention: Giúp các từ ngữ trong câu liên hệ với nhau

## 3. Ứng dụng của LLM và Finetuning

Ứng dụng của LLM:

- Chăm sóc sức khỏe
- Tài chính
- Pháp lý
- Dịch vụ khách hàng: Chatbot, trợ lý ảo
- Sáng tạo nội dung
- Giao dục
- Giải trí và nghệ thuật

Một LLM có thể dùng cho tất cả các công việc, lĩnh vực mà ta cần. Tuy nhiên, **finetune** sẽ cho sẽ hiệu quả tốt hơn.

**Finetuning** muốn nói đến việc ta lấy một pre-trained model và train nó dựa trên một tập dataset cụ thể hơn về một lĩnh vực nào đó (dataset này thường nhỏ hơn nhiều so với dataset của pre-trained model nhưng sẽ chuyên sâu về một lĩnh vực hơn)

Có 3 cách để finetuning

- Self-supervised: Lấy một model về, cho nó dataset text để nó tự học, sau một thời gian, nó có thể sinh ra những câu văn giống như dataset mà ta cho nó học
- Supervised: Lấy một model về, cho nó data set dưới dạng (input, output). Lúc này, output của nó sẽ giống dạng hỏi - đáp
- Reinforcement learning: Phức tạp

Quy trình của supervised learning

- Chọn Task
- Chuẩn bị dataset
- Chọn model gốc
- Finetune model

Trong việc finetune, ta có 3 sự lựa chọn

- Retrain all parameters: Train lại toàn bộ tham số
- Transfer Learning: Chỉ train vài lớp cuối
- Parameter Efficient Fine-tuning (PEFT) (hoặc LoRA): Phức tạp

## 4. Một số khái niệm khác

Input size: 4096 token,…

Max length: 1024,…

Temperature - Độ sáng tạo của LLM: 0.1 → 1.0