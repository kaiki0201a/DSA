## 1. Computer Vision Problems

MLP (lớp kết nối đầy đủ) phù hợp với **dữ liệu dạng bảng**: mỗi cột là một đặc trưng và ta không biết trước đặc trưng nào, nên để mạng tự học mọi mối quan hệ

Ảnh thì khác. Hảy thử tính:
	- Một ảnh có 1 megapixel khoảng $10^6$ giá trị đầu vào.
	- Nếu lớp ẩn chỉ có 1.000 nơron, lớp đầu tiên đã cần $10^6 \times 10^3$ = $10^9$ tham số.
	- Con số này đang còn là tối thiểu vì 1.000 nơ ron là quá ít để biểu diễn ảnh tốt.
Hậu quả: Cần rất nhiều bộ nhớ, rất nhiều GPU để xử lý.
Nhưng con người và máy tính vẫn phân biệt được chó và mèo, nên ảnh phải có **cấu trúc** mà ta khai thác được. CNN là cách tận dụng cấu trúc đó
## 2. Edge Detection $ Filters

Để trích xuất các đặc trưng như đường biên (edges), góc, hay nét vẽ ta dùng một **bộ lọc (Filter / Kernel)** có kích thước nhỏ (ví dụ 3 x 3) trượt qua toàn bộ bức ảnh. Tại mỗi vị trí, ta tính tích vô hướng giữa bộ lọc và vùng ảnh tương ứng.

![[Pasted image 20261002215317.png]]

Một vài bộ lọc khác
Lọc theo chiều ngang:

![[Pasted image 20261002215405.png]]
![[Pasted image 20261002215540.png]]


Hoặc chúng ta có thể tự học bộ lọc bằng Backpropagation: Thay vì tự tay chọn các số trong bộ lọc, mạng CNN coi các con số này là **weights** để tự động học từ dữ liệu.

![[Pasted image 20261002215552.png]]
## 3. Padding & Strided Convolution

### 3.1. Kỹ thuật Đệm (Padding - p)

Nếu áp dụng trực tiếp bộ lọc kích thước f x f lên ảnh có kích thước n x n, kích thước đầu ra sẽ bị thu nhỏ thành (n - f + 1) x (n - f +1).
Điều này gây ra 2 nhược điểm chính:
	- Ảnh bị thu nhỏ liên tục qua từng tầng (Shrinking output)
	- Các điểm ảnh ở viền biên chỉ được bộ lọc trượt qua ít lần hơn rất nhiều so với các ảnh ở trung tâm, dẫn đến mất mát thông tin ở vùng biên.

Để khắc phục, ta thêm một dải các số ``0`` xung quanh viền ảnh (Padding p):
Ví dụ với p = 2:
![[Pasted image 20261002220023.png]]

Hai loại Padding thường gặp là:
	- Valid Convolution: Không dùng Padding (p = 0) 
	  -> Output: (n - f + 1) x (n- f+1 )
	- Same Convolution: Chọn p sao cho kích thước đầu ra đúng bằng kích thước đầu vào (n + 2p - f + 1 = n, lúc này p = $\frac{f - 1}{2}$ nên p thường được chọn là số lẻ.
### 3.2. Bước nhảy (Stride - s)
Stride (s) là khoảng cách (số điểm ảnh) mà bộ lọc di chuyển mỗi lần trượt theo chiều ngang hoặc chiều dọc.
![[Pasted image 20261002220720.png]]

### 3.3. Công thức tính kích thước đầu ra tổng quát
Nếu ảnh đầu vào có kích thước n x n, dùng bộ lọc f x f, đệm p và bước nhảy s, kích thước Output là:
$$\left\lfloor \frac{n + 2p - f}{s} + 1 \right\rfloor \times \left\lfloor \frac{n + 2p - f}{s} + 1 \right\rfloor$$
## 4. Convolutions over Volums

### 4.1. Tích chập trên Ảnh RGB  (Đa kênh)
Ảnh màu RGB có 3 kênh (Red, Green, Blue) với kích thước $n_H$ x $n_W$ x $n_C$
![[Pasted image 20261002221551.png]]

- Quy tắc bắt buộc: Số kênh của bộ lọc phải bằng đúng số kênh của Input 
- Khi trượt bộc lọc 3D này qua ảnh 3D, ta nhân từng phân tử trên cả 3 kênh rồi cộng lại để tạo thành 1 ma trận 2D đầu ra
### 4.2. Multiple Filters
Để phát hiện nhiều đặc trưng cùng lúc (ví dụ đường biên dọc, đường biên ngang, nét cong, màu sắc), ta sử dụng $n_C^{[l]}$ bộ lọc khác nhau. Kết quả của từng bộ lọc sẽ được xếp chồng lên nhau tạo thành 1 khối tensor 3D đầu ra có độ sâu đúng bằng số lượng bộ lọc:

![[Pasted image 20261002222003.png]]

### 4.3. Convolutional Layer l
Kích thước đầu vào: $n_H^{[l-1]} \times n_W^{[l-1]} \times n_C^{[l-1]}$
Kích thước bộ lọc: $f^{[l]} \times f^{[l]} \times n_C^{[l-1]}$
Kích thước đầu ra: $n_H^{[l]} \times n_W^{[l]} \times n_C^{[l]}$
**Số lượng tham số học trong tầng l**:
$$\text{Total Params} = \left( f^{[l]} \times f^{[l]} \times n_C^{[l-1]} + 1 \right) \times n_C^{[l]}$$
(trong đó +1 là hệ số chênh bias cho mỗi bộ lọc)
## 5. Pooling Layers

Tầng Pooling dùng để giảm kích thước không gian, giúp giảm chi phí tính toán và tăng khả năng bất biến với các dịch chuyển nhỏ của đối tượng.

### 5.1. Hai loại Pooling phổ biến:
![[Pasted image 20261002223058.png]]
![[Pasted image 20261002223110.png]]

### 5.2. Điểm quan trọng của Tầng Pooling:
Tầng Pooling chỉ có các siêu tham số (Hyperparameters) như kích thước cửa sổ f và bước nhảy s.
Tầng Pooling **Không có THAM SỐ cần học**.
Xử lý đa kênh: giữ nguyên số lượng kênh đầu ra bằng số lượng kênh đầu vào

## 6. Tại sao mạng CNN lại hiệu quả.

Mạng CNN tiết kiếm được hàng triệu tham số so với mạng MLP nhờ vào 2 nguyên lý cốt lõi:
	- Parameter Sharing: Mỗi bộ lọc phát hiện đặc trưng (như đường biên dọc) hoat động hiệu quả ở vùng góc trên bên trái bức ảnh cũng sẽ hoạt động tốt ở bất kỳ vị trí nào khác. Do đó, ta dùng chung một bộ tham số cho toàn bộ bức ảnh.
	- Sparsity of Connections: Ở mỗi tầng, mỗi giá trị đầu ra chỉ phụ thuộc vào một vùng không gian nhỏ (cục bộ) chứ không kết nối với toàn cục
## 6. LetNet & Full CNN Pipeline

Kiến trúc LeNet-5 là mô hình CNN kinh điển đầu tiên được ứng dụng thành công trong thương mại để nhận diện chữ số viết tay.

![[Pasted image 20261002224309.png]]

![[Pasted image 20261002224323.png]]

Triển khai Mã nguồn LeNet-5 bằng Python
```
import torch 
import torch.nn as nn 
class LeNet5(nn.Module): 
	def __init__(self, num_classes=10): 
		super(LeNet5, self).__init__() 
		
		# Khối Tích chập 1 (Convolutional Block 1) 
		# Đầu vào: (Batch, 1, 28, 28) 
		self.conv1 = nn.Conv2d(in_channels=1, out_channels=6, kernel_size=5, padding=2) 
		self.act1 = nn.Sigmoid() # Hoặc nn.ReLU()
		self.pool1 = nn.AvgPool2d(kernel_size=2, stride=2) 
		
		#Khối Tích chập 2 (Convolutional Block 2) 
		# Đầu vào: (Batch, 6, 14, 14)  
		self.conv2 = nn.Conv2d(in_channels=6, out_channels=16, kernel_size=5) 
		self.act2 = nn.Sigmoid() # Hoặc nn.ReLU() 
		self.pool2 = nn.AvgPool2d(kernel_size=2, stride=2) 
		
		# Khối Kết nối Mật độ cao (Fully Connected Layers / MLP) 
		# Đầu vào: (Batch, 16 * 5 * 5) = (Batch, 400) 
		
		self.flatten = nn.Flatten() 
		self.fc1 = nn.Linear(in_features=16 * 5 * 5, out_features=120) 
		self.act3 = nn.Sigmoid() # Hoặc nn.ReLU() 
		self.fc2 = nn.Linear(in_features=120, out_features=84)
		self.act4 = nn.Sigmoid() # Hoặc nn.ReLU() 
		self.fc3 = nn.Linear(in_features=84, out_features=num_classes) 
		
		def forward(self, x): 
		# Forward qua khối Conv 1 
		x = self.conv1(x) 
		x = self.act1(x) 
		x = self.pool1(x) 
		
		# Forward qua khối Conv 2 
		x = self.conv2(x) 
		x = self.act2(x) 
		x = self.pool2(x) 
		
		# Trải phẳng và Forward qua các tầng FC 
		x = self.flatten(x) 
		x = self.fc1(x) 
		x = self.act3(x) 
		x = self.fc2(x) 
		x = self.act4(x) 
		x = self.fc3(x) return x
```