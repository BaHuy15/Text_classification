#  Xây dựng mô hình phân loại văn bản và triển khai thành API

1. Chuẩn bị dữ liệu
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
   C
3. 
4. 
