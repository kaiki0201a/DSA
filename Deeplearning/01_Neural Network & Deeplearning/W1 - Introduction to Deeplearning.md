## I. What is Neural Network?

### 1. Đơn vị Nơ-ron Đơn lẻ (Single Neuron)
 Bài toán ví dụ: Dự đoán giá nhà (y) dựa vào diện tích nhà (x).
![[Pasted image 20260930225955.png]]
 Cơ chế: Mô hình thiết lập đường tuyến tính biểu diễn mối quan hệ giữa diện tích và giá nhà. Vì giá nhà không bao giờ âm, nên hàm kích hoạt thường được sử dụng là ReLU (Rectified Linear Unit) được áp dụng.
 
> [!NOTE] 
> Hàm ReLU: max(0,z)

### 2. Mạng Nơ-ron Hoàn chỉnh
Khi mở rộng bài toán với nhiều đặc trưng đầu vào, ví dụ 4 đặc trưng: $x_1$ (diện tích), $x_2$ (số phòng ngủ), $x_3$ (mã bưu chính), $x_4$ (mức độ giàu có khu vực).
![[Pasted image 20260930230926.png]]
Lúc này mạng nơ-ron được kết nối thành các tầng ẩn (hidden layers).

Mạng tự động nhóm các đầu vào để tạo ra các đơn vị ẩn (hidden units) biểu diễn các khái niệm trung gian cấp cao hơn:
	- Size + bedrooms -> Family size
	- Zip code -> Walkability
	- Zip code + Wealth -> School quality

Các đơn vị ẩn này tiếp tục được kết hợp để tính toán ra giá nhà dự đoán cuối cùng (y).

**Điểm cốt lõi**: Con người không cần tự định nghĩa hay trích xuất các thuộc tính trung này; mạng nơ-ron tự động học cách phát hiện chúng từ dữ liệu
> [!SUMMARY]
> Mạng nơ ron: mô hình tính toán gồm các node liên kết chặt chẽ, mỗi node thực hiện phép biến đổi tuyến tính + phi tuyến tính

## II. Supervised Learning with Neural Network

### 1. Bản chất của Học có Giám sát
Hầu hết các ứng dụng thương mại của Deep Learning hoạt động theo cơ chế Học có giám sát (Supervised Learning), tức là học hàm ánh xạ từ đầu vào x sang đầu ra y với dữ liệu train đã được gắn nhãn.
### 2. Ứng dụng 
![[Pasted image 20260930232234.png]]
### 3. Ví dụ các Neural Network
![[Pasted image 20260930232331.png]]

### 4. Dữ liệu có cấu trúc vs Không có cấu trúc
Dữ liệu có cấu trúc (Structured Data): Dữ liệu bảng chứa các cột thuộc tính được định nghĩa rõ ràng.

Dữ liệu không có cấu trúc (Unstructured Data): Tín hiệu âm thanh thô, điểm ảnh kỹ thuật số, hoặc văn bản tự nhiên.

**Ưu thế của Deep Learning**: Thay thế quá trình trích xuất đặc trưng thủ công (feature engineering) tốn nhiều công sức bằng khả năng học biểu diễn tự động từ dữ liệu phi cấu trúc thô.

Ví dụ của Structured Data vs Unstructured Data
![[Pasted image 20260930233216.png]]
## III. Why Deep Learning is Taking Off?

### 1. Quy mô Dữ liệu (Data Scale)
![[Pasted image 20260930233750.png]]
Các thuật toán ML truyền thống bị bão hoà hiệu suất khi lượng dữ liệu tăng lên nhiều.
Trong khí đó, Large NN liên tục cải thiện hiệu suất khi tăng thêm dữ liệu.

### 2. Ba yếu tố chính thúc đẩy học sâu
- Dữ liệu (Data): Sự bùng nổ dữ liệu số hoá từ Internet và cảm biến gía rẻ.
- Năng lực tính toán (Computation): Sự phát triển mạnh mẽ của phần cứng song song (như GPU) giúp tăng tốc độ huấn luyện.
- Cải tiến thuật toán (Algorithms)

Vòng lặp thực nghiệm nhanh:
![[Pasted image 20260930234207.png]]
## IV. Core Components of an ML System
### 1. Cấu trúc hệ thống ML

Mọi hệ thống Học máy/Học sâu đều được cấu thành từ 4 thành phần cơ bản:
1. Dữ liệu (Data): Các mẫu ví dụ dùng để train và test.
2. Mô hình (Models): Kiến trúc toán học biến đổi đầu vào thành dự đoán.
3. Hàm mục tiêu/ Hàm chi phí (Objective / Loss Functions): Thước đo đánh giá chất lượng dự đoán của mô hình so với thực tế.
4. Thuật toán tối ưu (Optimization Algorithms): Phương pháp cập nhật trọng số mô hình để tối thiểu hoá hàm chi phí (vd: Gradient Descent, SGD, Adam)