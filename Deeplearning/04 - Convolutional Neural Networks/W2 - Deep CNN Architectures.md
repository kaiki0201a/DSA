
## 0.  Tại sao phải học "case study"?

Mỗi kiến trúc là câu trả lời cho điểm nghẽn của kiến trúc trước.
Lịch sử cổ điển:

``LeNet-5 → AlexNet → VGG, rồi ResNet, Inception, và sau đó MobileNet, EfficientNet.``

**Các công cụ tính toán:**
	- Kích thước đầu ra của Conv: 
		  Output = ⌊(n + 2p − f) / s⌋ + 1
	- Tham số của một lớp Conv:
		TS = (f × f × $n_{in}$ + 1) × $n_{out}$
	- Chi phí tính toán:
		Cost = (số tham số của một filter) × (số vị trí filter trượt tới) × (số filter)
**Tham số** là "bộ nhớ" mạng cần lưu, còn **chi phí** là "sức" máy phải bỏ ra mỗi lần chạy. Có mạng ít tham số nhưng rất tốn sức tính và ngược lại. Đây cũng là chìa khoá để hiểu Inception và MobileNet ở phần sau.
## 1. LetNet-5, AlexNet, VGG

### 1.1. LeNet-5
Dùng để nhận dạng **chữ số viết tay** với đầu vào 32x32. 

![[Pasted image 20261003183827.png]]

#### Đếm tham số (bản đơn giản hóa)

- CONV1: (5×5×1 + 1) × 6 = **156**
- CONV2: (5×5×6 + 1) × 16 = **2.416**
- FC 400→120: 400 × 120 + 120 = **48.120**
- FC 120→84: 120 × 84 + 84 = **10.164**
- Đầu ra 84→10: 84 × 10 + 10 = **850**
Tổng khoảng 60k tham số.
Nhận xét: 
	- Hình dạng "phễu": cao và rộng giảm dần (32 -> 28 -> 14 -> 10 -> 5), số kênh tăng dần (1 -> 6 -> 16).
	- Phần lớn tham số nằm ở lớp FC.

Điểm nghẽn: LeNet chạy tốt trên chữ số, những nó không mở rộng ra ảnh thật như chó mèo, xe cộ được.
### 1.2. AlexNet

#### **Câu hỏi: CNN đã xuất hiện nhưng sao chưa thắng các phương pháp khác ở quá khứ?**

Từ 1998 đến 2012, người ta vẫn dùng các phương pháp "đặc trưng thiết kế tay" (SIFT, HOG,...) kết hợp bộ phân loại đơn giản. Mạng nơ-ron bị coi là chậm và khó. Tại sao lúc đấy CNN chưa hiệu quả.

**Ba nguyên nhân chính**:
	1. Thiếu dữ liệu
	2. Thiếu sức tính
	3. Thiếu các thuật toán tối ưu

#### **Kiến trúc AlexNet**
![[Pasted image 20261003184548.png]]

#### **Ba khác biệt quan trọng so với LeNet**
**a). Kernel 11x11 ở lớp đầu vào và stride 4.**
Ảnh ImageNet lớn hơn rất nhiều so với chữ số 32x32, nên một vật thể chiếm nhiều pixel hơn. Cần "cửa sổ" to để nhìn bao quát ở stride lướn để thu nhỏ nhanh, tránh tốn kém ngay từ lớp đầu. Các lớp sau mới dùng kernel nhỏ dần: 5x5, 3x3.

**b). ReLu thay cho sigmoid**
Nhược điểm của sigmoid là bị "kẹt" khi giá trị quá lớn hoặc quá nhỏ (đường cong gần như thẳng nên gradient ~ 0), có nghĩa là tham số gần như không được cập nhật. ReLu giúp khắc phụ một phần vì có slope = 1 ở nửa dương.

**c). Dropout và tăng cường dữ liệu**
AlexNet có hàng chục triệu tham số, nên rất dễ "học vẹt". Dropout (xác suất 0,5 ở hai lớp FC) ép mạng không được đựa quá nhiều vào bất kỳ nơ-ron nào.
Tăng cường dữ liệu (lât, cắt, đổi màu) thì cho mạng thấy nhiều biến thể hơn. cả 2 cách đều giúp cống overfitting.

Tổng tham số của AlexNet khoảng 62 triệu lớn hơn rất nhiều so với LeNet.

#### **Vấn đề mới**
AlexNet chứng minh được CNN sâu hiệu quả, nhưng mỗi lớp đều do người thiết kế chọn một cách tuỳ hứng. Kernel 11 hay 5, bao nhiêu kênh? Không có quy tắc nào chỉ sau nên người đi sau không biết nên đi dường nào. Và điểm thứ 2 là lớp FC phình rất to.

### 1.3. VGG, đưa ra một "quy tắc xây dựng"
#### **Câu hỏi của nhóm VGG (Oxford)**
"Nếu ta bỏ hết các lựa chọn tuỳ hứng, chỉ dùng **một loại kernel duy nhất"**, thì sâu và rộng cái nào quan trọng hơn?"
 - Sâu là nhiều lớp kernel hơn
 - Rộng là kích thước lớn hơn
 - 
 **Ý tưởng 1: chỉ dùng kernel 3x3**
 Hãy cùng làm một thí nghiệm tưởng tượng, chỉ nhìn một hàng pixel (1 chiều)
 - Lớp 1 dùng cửa sổ 3: Mỗi đầu ra nhìn thấy 3 pixel gốc
 - Lớp 2 lại dùng cửa sổ 3 trên các đầu ra lớp 1. Mỗi đầu ra lớp 2 gộp 3 đầu ra liền kề của lớp 1, mà ba cái đó lại bao trùm 5 pixel gốc.
 Vậy hai lớp 3x3 "nhìn" được vùng 5x5 bằng đúng một lớp 5x5. Ba lớp 3x3 thì nhìn được 7x7. Vùng mà một giá trị đầu ra nhìn thấy gọi là "receptive field" (trường tiếp nhận).
 Vậy xếp chồng nhiều 3x3 có lợi gì so với một kernel to?
 - Ít tham số hơn. Giả sử c kênh vào và ra. Ba lớp 3 x 3 x 3 x $c^2$ = 27$c^2$. Một lớp 7x7: 49$c^2$.
 - Nhiều phi tuyến hơn. Mỗi lớp có một ReLU, nên mạng biểu diễn được hàm phức tạp hơn.
 Kết luận của VGG: mạng sâu và hẹp thắng mạng nông và rộng.

**Ý tưởng 2: gom thành khối**
Có một vấn đề nữa. Nếu mỗi lớp Conv đều theo sau bởi POOL (chia đôi cao và rộng), thì với ảnh 224 bạn chỉ chia đôi khoảng 7-8 lần là hết ảnh. Vậy tối đa là 8 lớp Conv. Muốn sâu hơn thì sao?
Giải thích theo VGG: **giữa hai lần pooling, đặt nhiều Conv 3x3 liên tiếp, dùng padding "same" để giữ nguyên kích thước.** Một khối VGG là:
> [CONV 3×3, same] × (vài lần) → MAX-POOL 2×2, s=2

#### Kiến trúc VGG: 
![[Pasted image 20261003224357.png]]

Có 13 lớp CONV và 3 lớp FC nên gọi là VGG-16
Quy luật: **mỗi khối chia đôi cao và rộng, nhân đôi số kênh**. Điều này AlexNet không có.

#### **Vấn đề mới**
Tham số của VGG-16 là khoảng 138 triệu. Quá lớn. Và có vấn đề khi thử xếp chồng sâu hơn nữa.
## 2. ResNet & Skip Connections

### **Hiện tượng kỳ lạ**
Trực giác của chúng ta: thêm lớp thì mạng ít nhất cũng không kém hơn, vì thêm lớp thì khả năng biểu diễn tăng. Nhưng thực nghiệm cho thấy với mạng thường (plain), sau một độ sâu nhất định, **sai số huấn luyện (training error) lại tăng**.
![[Pasted image 20261003225039.png]]
Điều này không phải là overfitting. Mạng sâu hơn mà ngay cả trên dữ liệu training đã làm kết quả tệ hơn, nghĩa là **bài toán tối ưu trở nên quá khó:** bộ tối ưu không tìm được nghiệm tốt.

Hãy thử đặt câu hỏi: nếu mạng 20 lớp đạt kết quả tốt, thì mạng 56 lớp chỉ cần để 36 lớp thêm vào "không làm gì" là ít nhất cũng bằng mạng 20 lớp. Vậy tại sao nó lại tệ hơn? Bởi vì với mạng thường, **học hàm đồng nhất qua nhiều lớp phi tuyến là việc rất khó**.

### **Ý tưởng cúa ResNet: làm cho "không làm gì" trở thành việc dễ nhất**
Thay vì bắt lớp học cả hàm, ta cho nó học phần chênh lệch:
![[Pasted image 20261003225415.png]]
![[Pasted image 20261003225330.png]]

Thay vì chỉ đưa $z^{[l+2]}$ vào hàm kích hoạt, ta cộng thêm đầu vào của. $a^{[l]}$. Phần cộng thêm này đi qua một **đường tắt (skip connection)** bỏ qua hai lớp.

### **Vì sao ResNets hiệu quả?**
Giả sử trọng số và bias của hai lớp thêm vào bị kéo về gần 0. Khi đó $z^{[l+2]}$ ~ 0 nên:
$a^{[l+2]}$ = g(0 + $a^{[l]}$) = max(0, $a^{[l]}$) = $a^{[l]}$.

### **Kiến trúc Plain vs ResNet**
![[Pasted image 20261003230354.png]]

### Vấn đề mới
Đi sâu được rồi, nhưng **chi phí tính toán thì sao?** Mạng sâu và rộng học rất tốn chi phí và tài nguyên. Và còn một câu hỏi nữa là: mỗi lớp nên dùng kernel nào?
## 3. Inception Network, "đừng chọn, cứ dùng hết"
### 3.1 Công cụ phụ trợ: tích chập 1x1
Nếu ảnh chỉ có 1 kênh, Conv 1x1 chỉ làm một việc vô nghĩa: nhân cả ảnh với một số. Nhưng ảnh có nhiều kênh thì khác.
![[Pasted image 20261003230954.png]]

Giả sử đầu vào là 6x6x32. Một filter 1x1 thực ra có kích thước 1x1x32. Tại mỗi pixel. nó lấy 32 số theo chiều kênh, nhân với 32 trọng số rồi cộng lại (kèm ReLU). Hãy nghĩ nó như một mạng FC thu nhỏ chạy riêng ở từng pixel, nhìn xuyên qua các kênh. Nếu có n filter như vậy, đầu ra là 6x6xn. Đây là ý tưởng "Network in Network"

**Tác dụng thực tế:** thay đổi **số kênh**. Pooling thu nhỏ chiều cao và rộng, còn 1x1 conv thu nhỏ (hoặc mở rộng) **chiều sâu**.

### 3.2. Câu hỏi của Inception
#### "Mỗi lớp nên dùng kernel 1x1, 3x3, 5x5 hay pooling?"
Câu trả lời của Inception là: **dùng tất cả song song**, rồi ghép kết quả theo chiều kênh. 

![[Pasted image 20261003231504.png]]

Nếu dùng cả 4 nhánh padding same để giữ 28x28, thì kênh ghép được:
64 (1x1)  +  128 (3x3) +  32 (5x5) + 32(pool) = 256 kênh.
Mạng tự quyết định nhánh nào quan trọng. Thay vì người thiết kế phải đoán.

#### **Chi phí tính toán**
Hãy xem riêng nhánh 5x5:
![[Pasted image 20261003231806.png]]

- Mỗi filter: 5x5x192 =4800 tham số.
- Mỗi vị trí: 28x28 = 784
- Số filter: 32.
KQ Chi phí: 4800 x 784 x 32 = 120 M phép nhân

##### **Giải pháp Bottle Neck 1x1** 
![[Pasted image 20261003232231.png]]

Trước khi qua Conv 5x5, hãy nén 192 kênh xuống còn 16 kênh bằng CONV1. Lúc này chi phí tính toán giảm khoảng 10 lần mà kích thước đầu vào và đầu ra không đổi.
Điều thú vị là: nên kênh nghe như mất thông tin, nhưng thực tế các kênh thường dư thừa (nhiều kênh mang thông tin tương tự nhau), nên nén hợp lý mất rất ít thông tin.

#### Khối Inception hoàn chỉnh:
![[Pasted image 20261003232526.png]]

#### GoogLeNet và cái tên vui
Xếp chồng nhiều khối Inception, ta được GooLeNet.
![[Pasted image 20261004004322.png]]
Ngoài đầu ra cuối, còn có các nhánh softmax phụ gắn giữa các mạng. Ý tưởng là buộc các lớp trung gian cũng phải học đặc trưng đủ tốt để dự đoán lớp, và điều này có tác dụng điều chuẩn.

#### Vấn đề mới
Dù đã tiết kiệm, các mạng này vẫn quá nặng để chạy trên điện thoại hoặc thiết bị nhúng.

## 4. MobileNet và EfficientNet, thu nhỏ nhưng vẫn hiệu quả

### 4.1. MobileNet v1: tách hai việc làm một
Hãy xem một tích chập thường đang làm hai việc cùng lúc:
- 1. Lọc theo không gian.
- 2. Trộn thông tin giữa các kênh.
Chi phí cao vì mỗi filter làm cả 2 việc trên mọi kênh. Ý tưởng MobileNet: **làm hai việc đó riêng, mỗi việc một bước rẻ.**

Ví dụ  ảnh 6×6×3, filter 3×3×3, 5 filter, đầu ra 4×4×5.
#### **Tích chập thường:**
![[Pasted image 20261004004828.png]]

Chi phí khoảng 2160 phép nhân.

#### Tích chập tách theo chiều sâu (depthwise separable)
![[Pasted image 20261004005031.png]]

- Bước 1, Depthwise: mỗi kênh có một filter 3x3 riêng, chỉ xử lý kênh của chính nó. Output 4x4x3.
![[Pasted image 20261004005334.png]]
- Bước 2, Pointwise: dùng filter: 1x1x3 (Conv 1x1) để trộn các kênh ra 4x4x5:
![[Pasted image 20261004005404.png]]

Total cost khoảng 672, chi phí còn khoảng 31%.
#### Công thức tính tổng quát cho tỉ lệ chi phí
![[Pasted image 20261004005528.png]]

### 4.2. MobileNet v2: khối bottleneck đảo ngược
Phiên bản 2 gói gọn mỗi khối thành 3 bước:
- 1. Expansion (1x1): nở số kênh ra rộng hơn.
- 2. Depthwise (3x3): lọc không gian ngay trong không gian rộng đó.
- 3. Projection (1x1): nén ngược về ít kênh
![[Pasted image 20261004005752.png]]

![[Pasted image 20261004005859.png]]

### 4.3.  EffientNet: hãy chỉnh có phương pháp
Giả sử bạn có một mạng cơ sở và muốn mạnh hơn. Có 3 núm để vặn
- r, resolution: độ phân giải ảnh đầu vào. Ảnh lớn thì thấy chi tiết mịn hơn.
- d, depth: độ sâu, thêm lớp thì trừu tượng hoá nhiều tần hơn, và receptive field lớn hơn.
- w, width: độ rộng số kênh mỗi lớp. Rộng hơn thì nhìn được nhiều đăc trưng hơn mỗi tầng.
![[Pasted image 20261004010313.png]]

Cách cũ là vặn từng núm riêng lẻ, nhưng mỗi núm vặn một mình sẽ nhanh chóng bão hoà. Ví dụ: ảnh to mà mạng nông thì không đủ tầng để khai thác chi tiết đó.
Compound scaling của EfficientNet: **vặn cả 3 núm cùng lúc, theo tỉ lệ cân đôi**. 

## 5. Dùng những mạng này trong thực tế
Thực tế, chúng ta hầu như không huấn luyện một mạng như AlexNet từ con số 0 mà thường làm theo những cách sau

### 5.1. Transfer Learning

Tình huống: bạn có 2 chú mèo, Tigger và Misty, muốn một bộ phân loại 3 lớp: Tigges, Misty, hoặc Neither (không phải cả 2). Bạn chỉ có vài trăm bức ảnh thì phải làm sao?
Ý tương: lấy một mạng đã huấn luyện sẵn trên dữ liệu lớn ví dụ ImageNet. Các lớp đầu của nó đã học cạnh, kết cấu, hình dạng đơn giản dùng chung cho mọi ảnh. Ta không cần phải học lại.

Tuỳ vào số lượng dữ liệu và loại phân nhãn ta có thể có 3 cách làm chính sau:

| Số lượng dữ liệu | Cách làm                                                               |
| :--------------- | :--------------------------------------------------------------------- |
| Rất ít           | Đóng băng toàn bộ mạng, thay đổi lớp softmax từ 1000 -> 3              |
| Vừa              | Đóng băng các lớn đầu, huấn luyện vài lớp cuối và đầu ra mới           |
| Nhiều            | Dùng trọng số pretrained làm điểm khởi đầu rồi huấn luyện toàn bộ mạng |
![[Pasted image 20261004011349.png]]

Trong framework, "đóng băng" thường là đặt tham số ``trainable = False``

### 5.3. Data Augmentation, "nhân bảo dữ liệu"
Khi có ít ảnh thì ta tạo thêm biến thể từ ảnh có sẵn, giữ nguyên nhãn:

Các biến đổi thường gặp:
**Biến đổi hình học:**
- Mirroing: lật gương ảnh
- Random cropping: cắt ngẫu nhiên một vùng.
- Rotation, shearing, local warping: xoay, làm nghiêng, biến dạng cục bộ
![[Pasted image 20261004011840.png]]
**Biến đổi màu**
![[Pasted image 20261004011825.png]]

Khi triển khai: việc biến đổi nên làm ngay trong lúc huấn luyện.

## 6. Tips, các lỗi và tổng kết

### 6.1. Tips để winning competitions
![[Pasted image 20261004012242.png]]


### 6.2. Ba bài học xuyên suốt:
- 1. Thiết kế dạng khối giúp ta nghĩ ở mực trừu tượng cao hơn.
- 2. Nút thắc thường là tốt ưu hoặc chi phí, không phải khả năng biểu diễn. Skip connection, bottleneck 1x1 đều là cách giải các nút thắt này.
- 3. Đừng huấn luyện từ con số 0. Hãy dùng pretrained model và fine-tuning

### 6.3. Các lỗi dễ mắc

- **Quên đổi chế độ train/eval.** Dropout và Batch Norm hoạt động khác nhau lúc huấn luyện và lúc dự đoán. Quên chuyển sang chế độ eval khi kiểm tra sẽ cho kết quả sai và dao động.
- **Sai kích thước đầu vào** khi dùng mạng pretrained. Mạng đã "quen" cỡ ảnh nhất định (ví dụ 224×224) và cách chuẩn hóa pixel nhất định. Hãy làm đúng như khi nó được huấn luyện.
- **Augmentation làm đổi nhãn** (ví dụ lật chữ số).
- **Batch size quá nhỏ khi dùng BN.** Batch size 1 thì BN không học được gì, và d2l gợi ý khoảng 50 đến 100 là hợp lý.
- **Nhầm "tham số ít" với "chạy nhanh".** Tham số là bộ nhớ, còn chi phí tính toán là sức máy. Hai thứ này khác nhau.

### 6.4. Tổng kết


| Mạng             | Vấn đề của người đi trước           | Cách giải                                                  | Cái giá kéo theo                    |
| ---------------- | ----------------------------------- | ---------------------------------------------------------- | ----------------------------------- |
| **LeNet-5**      | Máy chưa nhận dạng được chữ số      | CONV + POOL + FC (~62 nghìn tham số)                       | Không mở rộng được ra ảnh thật      |
| **AlexNet**      | Thiếu dữ liệu, sức tính, mẹo        | ImageNet + GPU + ReLU + dropout + augmentation (~62 triệu) | Thiết kế lớp tùy hứng; FC phình to  |
| **VGG**          | Không có quy tắc thiết kế           | Khối nhiều CONV 3×3, sâu và hẹp (~138 triệu)               | Rất nặng; đi sâu hơn thì khó tối ưu |
| **ResNet**       | Sâu hơn lại huấn luyện tệ hơn       | Đường tắt, học phần dư                                     | Chi phí vẫn cao                     |
| **Inception**    | Không biết chọn kernel; chi phí cao | Nhánh song song + bottleneck 1×1                           | Kiến trúc phức tạp                  |
| **MobileNet**    | Quá nặng cho thiết bị nhỏ           | Depthwise separable, bottleneck đảo ngược                  | Cần cân bằng độ chính xác           |
| **EfficientNet** | Mở rộng mạng thiếu phương pháp      | Compound scaling của r, d, w                               |                                     |
