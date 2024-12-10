#  Xây dựng mô hình phân loại văn bản và triển khai thành API

## 1. Chuẩn bị dữ liệu
   - Tải dữ liệu News Aggregator Data Set về máy.
   - Xử lý dữ liệu
     Sau khi kiểm tra dữ liệu trên, em thấy rằng các cột chứa nhiều giá trị NaN ( ô trống). Nên em sẽ fill giá trị NaN bằng string "empty" hoặc khoảng trắng sau đó tạo một cột example để join
     string của từng cột với nhau và loại bỏ các cột 'TITLE', 'URL', 'PUBLISHER', 'CATEGORY', 'STORY', 'HOSTNAME', 'TIMESTAMP'
   - Kết quả:
     ![image](https://github.com/user-attachments/assets/de2f606c-00c1-4e16-a075-4f54132a90f0)
     output được lưu: dataset/content.csv
     
   ```shell
   # Code thực hiện
   python src/text_processing/process_data.py
   ```
## 2. Xử lý dữ liệu
   Tiếp tục đọc file content.csv được tạo ra ở bước trên. Em thấy rằng
   - Categories được ký hiệu bởi các chữ cái, ví dụ  "b": "business", "t": "science and technology", "e": "entertainment", "m": "health" nên vì vậy em sẽ tạo 1 dictionary để map các ký tự với label tương ứng
   labelmap = {"b": "business", "t": "science and technology", "e": "entertainment", "m": "health"}
   - Các cột cách nhau bởi tab nên em sẽ đọc giá trị trong cột example và tách string theo tab("/t") để lấy title và category
   - Tạo một list rỗng để thêm (title, category)
   - Xáo trộn danh sách một cách ngẫu nhiên
   - Chia thành tập dữ liệu train/valid/test theo tỉ lệ 80/10/10
   - Lưu thành file train.txt, valid.txt, test.txt và train.csv valid.csv test.csv                 
   Code thực hiện
   ```shell
   # Code thực hiện
   python src/text_processing/prepare_data.py
   ```
   Kết quả                       
   ![image](https://github.com/user-attachments/assets/f5583bee-735e-4cee-9a45-2659e3238be1)
                          
## 4. Khám phá dữ liệu
   Thống kê số lượng label của 3 tập train/valid/test. Dưới đây là kết quả phân tích                
   ![image](https://github.com/user-attachments/assets/6c454af0-0043-44dc-b4d1-42b31317f069)                   
                         
   Thống kê số lượng từ min/max/average
   ![image](https://github.com/user-attachments/assets/6243abc6-0e72-4126-a876-924b13a8605c)              
    Code thực hiện
   ```shell
   # Code thực hiện
   python src/text_processing/analyze_data.py
   ```

## 5. Phân tích và lựa chọn mô hình
   + Tài nguyên sử dụng: CPU
   + Lựa chọn mô hình: Sử dụng pretrained model mistral
   + Tools : Unsloth để tăng tốc độ training và inference và có hỗ trợ chuyển đổi mô hình sang định dạng GGUF để xài trên máy local
     
## 6. Finetune mô hình
    Code thực hiện
    ```shell
   # Code thực hiện
   Finetune_text_classification.ipynb
   ```
   Sau khi train xong, push mô hình lên huggingface
   
   Link: 
   https://huggingface.co/BaHuy1521/mistral-7b-bnb-4bit-GGUF 
   
8. Đánh giá mô hình
   
   ```shell
   # Code thực hiện
   python src/evaluate/evaluate.py
   ```
   
   Kết quả
   ![image](https://github.com/user-attachments/assets/975c83b9-1192-45ae-bb14-572f02fb0ab2)

   Phân tích chi tiết từng nhãn
   entertainment
   Precision, Recall, F1-score = 0.00: Mô hình hoàn toàn không dự đoán được nhãn này. Điều này có thể do thiếu mẫu hoặc nhãn bị mất cân bằng trong dữ liệu huấn luyện.
   business
   Precision = 0.85: Khi mô hình dự đoán là "business", phần lớn dự đoán là đúng.
   Recall = 0.09: Tuy nhiên, mô hình chỉ tìm được 9% tổng số mẫu thực sự thuộc "business". Điều này cho thấy recall rất kém, có thể do mô hình không nhận diện được hết các mẫu "business".
   science and technology
   Precision = 0.93: Tương tự như "business", khi mô hình dự đoán là "science and technology", dự đoán rất chính xác.
   Recall = 0.10: Chỉ 10% các mẫu thực sự thuộc nhãn này được phát hiện, cho thấy mô hình khó nhận diện nhãn này.
   health
   Precision = 0.79, Recall = 0.24: Kết quả tốt hơn một chút so với các nhãn khác, nhưng recall vẫn thấp. Điều này cho thấy mô hình chỉ phát hiện một phần nhỏ các mẫu "health".
   None
   Precision = 1.00: Khi mô hình dự đoán "None", nó luôn đúng.
   Recall = 0.24: Tuy nhiên, chỉ 24% tổng số mẫu thực sự thuộc nhãn "None" được nhận diện.
   
   Nhận xét chung
   Độ chính xác tổng thể (accuracy): 15%. Đây là một con số rất thấp, cho thấy mô hình không hoạt động tốt trong việc phân loại các nhãn trên tập dữ liệu này.
   Đánh giá tổng quát (macro avg và weighted avg):
   Macro avg: Precision, recall, và F1-score lần lượt là 0.71, 0.14, và 0.22. Điều này cho thấy mô hình không đồng đều giữa các nhãn, vì các nhãn có ít mẫu được ưu tiên thấp hơn.
   Weighted avg: Được tính dựa trên số lượng mẫu trong từng nhãn. Với precision là 0.87, nhưng recall và F1-score đều thấp, điều này chứng minh rằng mô hình có thể dự đoán đúng một số nhãn phổ biến hơn nhưng thất bại trong việc tái hiện các nhãn ít phổ biến.

## 8. Ý tưởng  cải thiện 

8.1. Xử lý dữ liệu mất cân bằng:

Sử dụng các kỹ thuật như oversampling (SMOTE) hoặc undersampling để cân bằng các nhãn.
Tăng cường dữ liệu cho các nhãn hiếm (data augmentation).
Cải thiện recall:

8.2. Sử dụng weighted loss function trong quá trình huấn luyện để giảm thiểu tác động của sự mất cân bằng dữ liệu.
Huấn luyện lại mô hình với tập dữ liệu đa dạng hơn.
Kiểm tra và tiền xử lý dữ liệu:

8.3. Đảm bảo rằng dữ liệu huấn luyện và kiểm tra cùng phân phối.
Loại bỏ các mẫu nhiễu hoặc không liên quan trong dữ liệu.
      
8.4. Thử các mô hình khác:
Sử dụng các mô hình (e.g., BERT, RoBERTa) hoặc tinh chỉnh mô hình hiện tại để đạt hiệu suất tốt hơn.

8.5. Kết quả cải thiện khi sử dụng BERT                            
Link train/test : https://colab.research.google.com/drive/1mP1sUhK0l6qQpdNsmcDVVjPp0EGkULjK?usp=sharing                                            
![image](https://github.com/user-attachments/assets/dc846450-42f6-40e1-84a6-bb4c0446db95)  

## 9. Triển khai mô hình thành API
   - Download mô hình từ huggingface và lưu file model vào thư mục model
   - Sử dụng Fastapi để triển khai microservice
   
 ```shell
   # Code thực hiện
   python src/main.py
 ```
Kiểm tra endpoint tại: http://localhost:2005/docs#/ 
      
Kết quả chạy                            
![image](https://github.com/user-attachments/assets/0f161a35-06de-458c-a20f-3ef570c8dc1f)

