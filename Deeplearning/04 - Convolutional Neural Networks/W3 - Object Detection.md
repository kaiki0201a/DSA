### 1. Object Localization: từ "ảnh này là gì?" đến "mỗi vật nằm đâu, mỗi pixel thuộc về ai?"

### 1.1. Bức tranh lớn - bế tắc phân loại ảnh CNN
Mở đầu với 3 bài toán sắp xếp theo độ khó:
![[Pasted image 20261004225635.png]]
Sự khác biệt chính: ở phân loại ta giả định ảnh chỉ có một vật chính còn thực tế thường có nhiều vật, và ta muốn biết cả nhãn lẫn vị trí.

Khó khăn cốt lõi là số vật thay đổi theo từng ảnh, nên đầu ra kích thước không cố định. Mọi thuật toán thường trả lời cho ba câu hỏi:
- 1. **Biểu diễn** khung bao thế nào?
- 2. **Chấm điểm** một khung đoán có tốt không ? (IoU)
- 3. **Chọn ứng viên** ở đâu và dọn các ứng viên thừa ra sao? (
  Sliding window, YOLO, anchor, region proposal, NMS)

### 1.2. Từ phân loại đến định vị đối tượng (Object Localization)

**Ý tưởng**
Chúng ta đã có các thuật toán, mạng phân loại có sãn. Ta chỉ cần bổ sung các đầu ra chỉ vị trí

**Vecto nhãn y**
Ví dụ 4 lớp: người đi bộ, xe hơi, xe máy, nền (không có gì). Lúc đấy nhãn gồm 8 bộ:
![[Pasted image 20261004230508.png]]

- $p_c$: có vật thể hay không (1/0)
- $b_x$, $b_y$: toạ độ tâm khung. 
- $b_h$, $b_w$: cao và rộng của khung.
- $c_1$, $c_2$, $c_3$: one-hot (lớp nào).
**Hàm mất mát**
Có hai trường hợp:
- Nếu $y_1$ = 1: tính tổng bình phương sai số trên cả 8 thành phần.
- Nếu $y_1$ = 0: chỉ tính (ŷ₁ − y₁)².
### 2. Landmark Detection, điểm thay vì khung

Nếu cho mạng xuất toạ độ các điểm mốc (l₁ₓ, l₁ᵧ, l₂ₓ, l₂ᵧ, …) thay vì khung bao, ta có landmark detection. Ví dụ: khoé mắt, khoé miệng trên khuôn mặt, hoặc các khớp cơ thể để ước lượng tư thế. Cấu trúc ConvNet gần như không đổi, chỉ khác đầu ra.

![[Pasted image 20261004231209.png]]

## 3. Object Detection - Bài toán nhận dạng nhiều đối tượng.

Khi bức ảnh chứa nhiều đổi tượng cùng lúc, mô hình phải vừa tìm vị trí vừa phân loại tất cả các đối tượng đó.
Vd:
![[Pasted image 20261004231608.png]]
### 3.1. Ý tương ngây thơ: Sliding Windows Detection
Cách làm:
- 1. Huấn luyện một mạng ConvNet(CNN) phân loại các bức ảnh vuông nhỏ.
- 2. Lấy một cửa số kích thước 64x64 trượt lần lượt qua từng vị trí bức ảnh và đưa qua ConvNet để phân loại.
- 3. Lặp lại các bước trên với các cửa sổ kích thước lớn hơn (vd: 128 x 128,...)

![[Pasted image 20261004231634.png]]

**Vấn đề**: Chi phí tính toán cực kỳ lớn. Việc cắt hàng ngàn mảnh ảnh nhỏ rồi cho chạy qua ConvNet độc lập nhiều lần khiến thuật toán cực kỳ chậm và không ứng dụng trong thực tế.

### 3.2. Đột phá tốc độ: Chuyển đổi tầng FV thành Tầng Tích Chập (Convolutional Implementation of Sliding Windows).

Năm 2014, Sermanet et al giới thiệu giải pháp biến đổi các tầng kết nối mật độ cao (FC) ở cuối magnj thành các **Tầng tích chập** 1x1.
- Khi biến đổi như vậy: ta có thể đưa toàn bộ bức ảnh lớn hơn vào ConvNet duy nhất một lần (Single Forward Pas).
- Mạng sẽ tự động thực hiện phép trượt cửa sổ trên toàn bộ ma trận Feature Map một cách song song trên GPU.
![[Pasted image 20261004232724.png]]
Hai bản làm đúng cùng một phép tính. Kiểm tra tham số: FC nhận 5x5x16 = 400 số nên có 400 x 400 = 160.000 trọng số. Lớp Conv 5x5 với 400 filter 5x5x16 cũng có 160.000 tham số.
![[Pasted image 20261004233114.png]]
Mạng có thể nhận kích thước bât kỳ và các cửa sổ chồng lấn nhau rất nhiều, nên phần tính toán chung được dùng lại thay vì tính đi tính lại giúp tăng tốc độ.
![[Pasted image 20261004232805.png]]
**Vấn đề cần giải quyết**: Khung vẫn "dính" theo lưới trượt, chưa chính xác để dự đoán đúng vị trí biên vật và nhận dạng vật.
## 4. YOLO Algorithm (YOU ONLY LOOK ONCE), để mạng tự dự đoán khung

**Ý tưởng**:
- 1. Chia ảnh thành lưới (thường là 19x19).
- 2. Mỗi ô có một vecto nhãn 8 chiều để nhận dạng vật và khung.
- 3. Quy tắc gán: mỗi vật thuộc về ô chứa trung điểm của nó, dù khung của vật tràn sang các ô khác.
![[Pasted image 20261004233816.png]]
Mô hình chạy qua 1 mạng ConvNet duy nhất và xuất ra ma trận Tensor kết quả có kích thước S x S x (5+C) với C là số lớp đối tượng.

**Quy ước toạ độ**
Mỗi ô lưới là một hệ toạ độ riêng, góc trên-trái (0,0), góc dưới-phải (1,1).
- $b_x$, $b_y$ nằm trong [0, 1] vì tâm vật phải nằm trong ô.
- $b_h$, $b_w$: có thể lớn hơn 1 vì xe có thể nằm tràn ra khỏi ô.
![[Pasted image 20261004234259.png]]

**Lý do YOLO nhanh**: Là một ConvNet chạy một lượt lan truyền xuôi duy nhất để dự đoán vị trí và phân loại trên toàn bộ ảnh.
## 5. Non-Max Suppression & IoU, chấm điểm khung và dọn khung thừa

Khi chạy YOLO hoặc bất kỳ thuật toán Object Detection nào, ta thường gặp 2 thử thách lớn:
- 1. Nhiều ô lưới lân cận cùng dự đoán chung một vật thể.
- 2. Hai hay nhiều vật thể nằm đè lên nhau trong cùng một ô lưới.
### 5.1. IoU (Intersection over Union), thước đo "khung đoán có đúng không?"
IoU đo lường mức độ đè lên nhau **Khung dự đoán** (Predicted Box) và **Khung thực tế (Ground Truth Box)**:
$$\text{IoU} = \frac{\text{Diện tích vùng Giao (Intersection)}}{\text{Diện tích vùng Hợp (Union)}}$$
Ta có thể chọn ngưỡng phù hợp với IoU để xác định đúng hay không (thường là 0.5)
![[Pasted image 20261005000155.png]]

### 5.2. Thuật toán Triệt tiêu Lọc cực đại (NMS).
**Vấn đề**: một chiếc xe có thể có nhiều ô lưới cùng nhận nó, nên cùng một vật có nhiều khung khi dự đoán.
![[Pasted image 20261005000356.png]]

NMS giúp giữ lại duy nhất khung tốt nhất và loại bỏ khung thừa bằng cách:
- 1. Loại bỏ tất cả các khung có xác suất $p_c \le 0.6$.
- 2. Lấy khung có $p_c$ lớn nhất còn lại và chọn đó là dự đoán chính thức.
- 3. Loại bỏ tất cả các khung còn lại có chỉ số IoU $\ge$ 0.5 đối với khung vừa chọn ở bước 2.
- 4. Lặp lại bước 2 và 3 cho đến khi hết các khung dự đoán cho từng lớp đối tượng.
![[Pasted image 20261005000855.png]]

## 6. Anchor Boxes & SSD, giải quyết vấn đề vật chồng lấp lên nhau và khi muốn thử mọi hình dạng.

### 6.1. Vấn đề của YOLO cơ bản
Mỗi lưới chỉ có một vecto y, nên chỉ ghi được một vật. Nhưng có tình huống trung điểm của hai vật rơi vào cùng một ô mà chỉ có một vecto y nên không đủ chỗ.
![[Pasted image 20261005001102.png|429]]

### 6.2. Ý tưởng Anchor
Chuẩn bị trước vài hình dạng khung mẫu, ví dụ 2 cái:
- Anchor 1: cao và hẹp (hợp với người đứng).
- Anchor 2: thấp và rộng (hợp với xe hơi).
![[Pasted image 20261005001242.png|466]]

Lúc này vecto y nhân đôi, khối đầu cho anchor 1 và khối sau cho anchor 2. Quy tắc gán nhãn mới:
- Trước: mỗi vật thuộc ô chứa trung điểm.
- Mới: mỗi vật thuộc **ô chứa trung điểm và anchor của ô đó có IoU lớn nhất** với khung vật.
![[Pasted image 20261005001720.png]]

### 6.3. Phát hiện đa kích thước & SSD
**Vấn đề**: dùng anchor sao cho hợp lý.

**Quan sát đánh giá**: vật nhỏ có nhiều cách xuất hiện hơn vật lớn:
- Để tìm vật nhỏ: dùng anchor nhỏ, đặt dày.
- Để tìm vật lớn: dùng anchor lớn, đặt thưa.

**Mô hình Single Shot Multibox Detector**
Thuật toán SSD trích xuất các Feature Map ở nhiều tầng độ khác nhau của mạng CNN. Thay vì chọn thủ công, ta lấy anchor tại mỗi ô của một feature map.
Feature map nhỏ thì ít tâm và tâm cách xa nhau. Vd:

| Feature map | Cỡ anchor s | Mục đích                 |     |
| ----------- | ----------- | ------------------------ | --- |
| 4×4         | 0,15        | vật nhỏ                  |     |
| 2×2         | 0,4         | vật vừa                  |     |
| 1×1         | 0,8         | vật lớn (tâm ở giữa ảnh) |     |
- Ở các tầng ẩn nông (đầu mạng), Feature Map có độ phân giải không gian lớn (ví dụ $32 \times 32$), phù hợp để tạo các Anchor Box nhỏ nhận diện **vật thể nhỏ**.
- Ở các tầng ẩn sâu (cuối mạng), Feature Map bị thu nhỏ kích thước không gian (ví dụ $2 \times 2$), phù hợp để tạo các Anchor Box lớn nhận diện **vật thể lớn**.
- Tất cả các dự đoán đa quy mô này sau đó được gộp lại và xử lý qua NMS.

**Vì sao feature map sâu hợp với vật lớn?**
Như đã biết về receptive field: các đơn vị ở lớp càng sâu "nhìn" vùng ảnh rộng. Mỗi ô của feature map ở một vị trí không gian có c kênh, và c đơn vị này cùng chung một receptive field trên ảnh. Do đó, ta dùng c giá trị đó để dự đoán lớp và offset cho các anchor quang vùng đó. Feature map gần đầu ra có receptive field rộng, nên hợp với vật lớn,

## 7. R-CNN, Fast RCNN, Faster RCNN
## 8. Semantic Segmentation & FCN, bước nhảy lên mức pixel
### 8.1. Khác biệt giữa Object Detection và Semantic Segmentation
- Object Detection: Trả về các khung hình chữ nhật (Bounding Boxes) xung quanh đối tượng.
- Semantic Segmentation: Phân loại lớp nhãn cho **TỪNG ĐIỂM ẢNH** 
  (per-pixel Classification) trong bức ảnh mà không phân biệt các thể hiện riêng biệt.
![[Pasted image 20261005003621.png]]

### 8.2. Motivation for U-Net

Hãy tượng tượng bạn cần nhìn ảnh MRI não. Một detector vẽ cho bạn một khung chữ nhật quanh khối u. Có ích, nhưng khung đó chứa cả các mô khoẻ mạnh ở bên trong. Điều bạn cần thực sự là đường viền chính xác của khối u để loại bỏ.
![[Pasted image 20261005004319.png]]

### 8.3. Ba bài toán nghe giống nhau nhưng khác nhau:
Ví dụ ảnh có 2 con chó, ba bài toán thường gặp là:

| Bài toán                  | Nó làm gì                                                                                              | Với ảnh hai con chó                                                             |
| ------------------------- | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| **Image segmentation**    | chia ảnh thành các vùng dựa trên sự giống nhau giữa các pixel (màu, kết cấu), **không biết** vật là gì | có thể chia một con chó thành "vùng mõm và mắt màu đen" và "vùng thân màu vàng" |
| **Semantic segmentation** | tô mỗi pixel theo **loại**                                                                             | mọi pixel của cả hai con chó cùng một màu "chó"                                 |
| **Instance segmentation** | tô theo loại và còn **phân biệt từng cá thể**                                                          | con chó thứ nhất một màu, con chó thứ hai một màu khác                          |
### 8.4. Bài toán kích thước và phép "đóng dấu" - Transposed convolution

#### **Vấn đề cần giải** 
Một CNN thường: ảnh vào 320x480, qua pooling và stride thì thu nhỏ dần dần còn 20x30. Tốt cho việc hiểu ảnh nhưng ta cần đầu ra 320x480 để gán nhãn cho từng pixel.
Cần một phép toán làm điều ngược lại: phóng to feature map. Đó là transposed Convolution.

![[Pasted image 20261005005017.png]]

Nếu tích chập là nhiều số trong một vùng nhỏ -> một số.
Thì transposed convolution làm điều ngược lại: phát tán một số -> một vùng nhiều số.
![[Pasted image 20261005005207.png]]
#### Hai núm chỉnh: stride và padding (ngược trực giác!)

Điều hay bị nhầm: ở transposed convolution, **padding và stride áp dụng lên đầu ra**, không phải đầu vào như tích chập thường.

### 8.5. U-Net, đi xuống để hiểu, đi lên để vẽ, nhìn lại để vẽ nét
#### Hai nhu cầu mâu thuẫn
Để gán nhãn từng pixel, mạng cần cả hai thứ:
- "Cái này là gì?": cần nhìn rộng, nhiều ngữ cảnh? Muốn vậy phải thu nhỏ ảnh qua bao nhiêu lớp.
- "Biên của nó nằm chính xác ở đâu": cần chi tiết mịn/ Nhưng chi tiết mịn đã bị bỏ bớt qua pooling.
U-Net giải bài toán này bằng 3 phần

#### Bức tranh chữ U
![[Pasted image 20261005005936.png]]

##### Pha 1: Encoder (đi xuống), "hiểu bức tranh".
Conv + ReLU + MaxPool, lặp lại. Mỗi bước, ảnh nhỏ hơn và đặc trưng trừu tượng hơn. Ở đáy chữ U, mạng biết rõ trong ảnh có cái gì, nhưng bản đồ đã rất nhỏ nên mất các chi tiết biên.
- Receptive field càng sâu, mỗi đơn vị càng nhìn được vùng ảnh rộng.
##### Pha 2: Bottle Neck
Trước khi phóng ảnh to lên ta cần giảm số kênh đi sau khi encoder, lúc này ta cần Conv 1x1 để giảm chi phí tính toán mà không ảnh hưởng nhiều đến kết quả.

##### Pha 3: Decoder(đi lên), "vẽ lại"
Dùng transposed convolution để phóng to dần. Nhưng nếu chỉ phóng to từ thông tin ở đáy, bức vẽ sẽ nhoè, giống như phóng to một tâm ảnh nhỏ.

##### Pha 4: Skip connection, "ngoái lại nhìn ảnh gốc"/
Đây là chi tiết làm U-Net đặc biệt. Ở mỗi cấp độ phân giải, đặc trưng chi tiết của encoder được chuyển thẳng sang decoder cùng cấp (thường bằng cách nối thêm kênh). Nhờ vậy decoder vừa có "ý nghĩa" từ đường đi sâu, vừa có "nét vẽ tốt" từ skip connection.

##### Bước cuối: lớp đầu ra
Sau Decoder cuối cùng, U-Net sử dụng một Conv 1x1 để chuyển số channel thành số lớp cần phân đoạna