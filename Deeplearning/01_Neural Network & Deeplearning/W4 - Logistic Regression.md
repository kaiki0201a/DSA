## I.  Binary Classification
### 1. Bài toán Phân loại nhị phân

Khái niệm: Bài toán phân loại nhị phân có đầu ra y chỉ nhận một trong hai giá trị: 0 hoặc 1.
Ví dụ: Nhận diện mèo

![[Pasted image 20261001003245.png]]
###  2. Biểu diễn Ảnh dưới dạng Vecto đặc trưng

Một bức ảnh kỹ thuật số kích thước 64 x 64 pixels có 3 kênh màu (RGB)

Toàn bộ điểm ảnh được trải phẳng thành một vecto cột duy nhất $x \in \mathbb{R}^{n_x}$
$$ {n_x} = 64 \times 64 \times 3 = 12{,}288 \text{ đặc trưng}$$

### 3. Hệ thống Ký hiệu Toán học Ma trận

- m: Tổng số mẫu huấn luyện trong tập dữ liệu.
- ($x^{(i)})$, $y^{(i)})$: Cặp dữ liệu gồm đặc trưng đầu vào và nhãn thực tế của mẫu thứ i
- **Ma trận đầu vào X**: Gom toàn bộ m mẫu xếp theo chiều dọc làm các cột:
	$$X = \begin{bmatrix} | & | & & | \\ x^{(1)} & x^{(2)} & \dots & x^{(m)} \\ | & | & & | \end{bmatrix} \in \mathbb{R}^{n_x \times m}$$
	
 - **Ma trận nhãn $Y$**: Vectơ hàng chứa $m$ nhãn: $$Y = \begin{bmatrix} y^{(1)} & y^{(2)} & \dots & y^{(m)} \end{bmatrix} \in \mathbb{R}^{1 \times m}$$
## II. Logistic Regression
### 1. Cấu trúc Mô hình & Hàm Sigmoid
Mục tiêu: Cho trước đặc trưng x, mô hình cần dự đoán xác suất $\hat{y} = P(y = 1 \mid x)$ sao cho $0 \le \hat{y} \le 1$.

Tham số mô hình:
	- Vecto trọng số $w \in \mathbb{R}^{n_x}$
	- Hệ số chênh bias $b \in \mathbb{R}^{n_x}$
Tổng tuyến tính: z = $w^T$ x + b.

Hàm kích hoạt Sigmoid đưa ra dự đoán:
$$\hat{y} = a = \sigma(z) = \frac{1}{1 + e^{-z}}$$
![[Pasted image 20261001005112.png]]

Đặc tính của Sigmoid:
	$z \to +\infty \implies e^{-z} \to 0 \implies \sigma(z) \to 1$.
	$z \to -\infty \implies e^{-z} \to +\infty \implies \sigma(z) \to 0$
	$z = 0 \implies \sigma(0) = 0.5$.

### 2. Loss Function

Hàm mất mát đo lường mức độ sai lệch giữa kết quả dự đoán $\hat{y}$ và nhãn thực tế y trên **một mãu đơn lẻ**.

	- Tại sao không dùng Hàm bình phương tối thiểu (MSE-Mean Squared Error)?
	VÌ hàm chi phí này kết hợp với Sigmoid sẽ trở thành hàm không lồi (non-convex). Chứa nhiều cực trị địa phương khiến Gradient Descent dễ bị mắc kẹt và không tìm được cực trị toàn cục.

![[Pasted image 20261001010124.png|484]]

Hàm Loss Cross-Entropy (Binary Cross-Entropy):
$$\mathcal{L}(\hat{y}, y) = -\left( y \log(\hat{y}) + (1 - y) \log(1 - \hat{y}) \right)$$

- **Nếu** $y = 1$**:** $\mathcal{L}(\hat{y}, 1) = -\log(\hat{y})$. Để Loss nhỏ nhất, mô hình buộc phải dự đoán $\hat{y}$ tiến sát $1$.
- **Nếu** $y = 0$**:** $\mathcal{L}(\hat{y}, 0) = -\log(1 - \hat{y})$. Để Loss nhỏ nhất, mô hình buộc phải dự đoán $\hat{y}$ tiến sát $0$.
### 3. Cost Function - J
Hàm chi phí J(w, b) là trung bình côcnjg các giá trị loss của toàn bộ m mẫu huấn luyện:
$$J(w, b) = \frac{1}{m} \sum_{i=1}^{m} \mathcal{L}(\hat{y}^{(i)}, y^{(i)}) = -\frac{1}{m} \sum_{i=1}^{m} \left[ y^{(i)} \log(\hat{y}^{(i)}) + (1 - y^{(i)}) \log(1 - \hat{y}^{(i)}) \right]$$
### 4. Gradient Descent
Tìm w, b sao cho tối thiểu hoá hàm Cost Function J(w,b) bằng cách tính đạo hàm
![[Pasted image 20261001011411.png]]

Lặp lại quá trình;
$$w := w - \alpha \cdot dw$$ $$b := b - \alpha \cdot db$$
![[Pasted image 20261001011517.png]]

### 5. Đồ thị tính toán và Lan truyền ngược
Quá trình tính toán Hồi quy Logistic cho một mẫu cơ bản được biểu diễn theo 3 bước:

![[Pasted image 20261001011731.png]]

Để lan truyền ngược, ý nghĩa là tính đạo hàm và tối ưu ngược từ phải sang trái để cập nhật trọng số w, b.

- Đạo hàm theo a:
	$$da = \frac{\partial \mathcal{L}}{\partial a} = -\frac{y}{a} + \frac{1 - y}{1 - a}$$
- Đạo hàm theo z
$$dz = \frac{\partial \mathcal{L}}{\partial z} = \frac{\partial \mathcal{L}}{\partial a} \cdot \frac{d a}{d z} = a - y$$

- Đạo hàm theo các tham số w và b:
$$dw_1 = x_1 \cdot dz, \quad dw_2 = x_2 \cdot dz, \quad db = dz$$
- Cập nhật trọng số bằng Gradient Descent:
$$w := w - \alpha \cdot dw$$$$b := b - \alpha \cdot db$$
### 6. Kỹ thuật Vector hoá và Broadcasting
