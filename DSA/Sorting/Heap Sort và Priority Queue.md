## Heap

### Khái nhiệm và cách lưu.
Heap là cây nhị phân hoàn chỉnh (complete binary tree) thoả mãn tính chất Heap:
- Max-heap: mỗi nút lớn hơn hoặc bằng các nút con, nên phần tử lớn nhất ở gốc.
- Min-heap: mỗi nút nhơ hơn hoặc bằng các nút con, nên phần tử nhỏ nhất ở gốc.
Heap được lưu trong mảng: Bắt đầu từ index 1, phần tử con trái là 2i, phần tử con phải là 2i + 1, cha ở vị trí i/2 (lấy phần nguyên)
### Heapify - tạo Heap - Max Heap
Ý tưởng: Heapify từ dưới lên.

Ví dụ mảng(đây đang lưu từ index 0, nên con trái là 2i+1, con phải là 2i+2,
 cha là (i-1)/2)

- 1. Tạo complete binary tree từ array
- 2. Bắt đầu từ index đầu tiên không phải là nốt lá
  (với bắt đầu từ index 0 là n/2 - 1 và từ index 1 là n/2).
- 3. Đặt index vừa chọn là largest
- 4. So sánh với con trái và con phải và swap nếu con > cha cho đến khi nằm đúng vị trí.
- 5. Lặp lại cho đến đỉnh.

Ví dụ:
![[Pasted image 20261025170405.png]]
### Insert Element into Heap

- 1. Thêm phần từ cần thêm vào cuối của cây/mảng.
- 2. Heapify the tree(liên tục so sánh phần từ đó với cha của nó, swap cho đến khi đã thành một heap).
### Delete Element from Heap
- 1. Chọn phần từ cần xoá và swap với phần tử cuối
- 2. Cắt bỏ phần từ cuối và Heapify the tree.

### Heap sort:
#### Ý tưởng
Xem mảng như một max-heap. Phần từ lớn nhất ở gốc nên lần lượt pop và đưa về cuối mảng, heapify cho phần còn lại.
### Cách hoạt động từng bước
- 1. Build max-heap: gọi heapify từ n/2 về 1.
- 2. Swap đầu - cuối.
- 3. Pop cuối và thêm vào cuối mảng.
- 4. Heapìy phần còn lại(để đỉnh chìm xuống)
- 5. Lặp lại cho đến khi heap chỉ còn đúng 1 phần tử.

### Ví dụ:
![[Pasted image 20261025170520.png]]![[Pasted image 20261025170552.png]]![[Pasted image 20261025170640.png]]![[Pasted image 20261025170710.png]]

### Độ phức tạp:
- O(nlogn) cho mọi trường hợp: chiều cao cây là logn.
- Space Complexity: O(1)


## Priority Queue
#### Ý tưởng

Priority queue là queue đặc biệt trong đó mỗi phần tử có một mức ưu tiên và phần tử ưu tiên cao nhất được phục vụ trước. Các phần tử cùng mức ưu tiên được phục vụ theo thứ tự xếp hàng. Có thể cài đặt bằng mảng, danh sách liên kết, heap hoặc cây nhị phân tìm kiếm; heap cho hiệu quả tốt nhất.

|                       |      |          |          |
| --------------------- | ---- | -------- | -------- |
| Cài đặt               | peek | insert   | delete   |
| Danh sách liên kết    | O(1) | O(n)     | O(1)     |
| Binary heap           | O(1) | O(log n) | O(log n) |
| Cây nhị phân tìm kiếm | O(1) | O(log n) | O(log n) |

### Ứng dụng Huffman code
#### Ý tưởng
Huffman coding là phương pháp nén không mất dữ liệu. Mỗi ký tự đầu vào được gán một mã có độ dài thay đổi, tuỳ theo tần suất xuất hiện. Là thuật toán Greedy: ký tự xuất hiện nhiều nhất nhận mã ngắn nhất.

#### Các xây cây Huffman
 Cây được xây từ dưới lên bằng min-heap (priority queue) xếp theo tần suất.
 - 1. Tạo một nút lá cho mỗi ký tự khác nhau và đưa tất cả và min-heap, ký tự ít xuất hiện nhất nằm trên cùng.
 - 2. Lấy ra hai nút có tần suất nhỏ nhất.
 - 3. Tạo nút trong mới có tần suất bằng tổng hai nút đó: nút bé hơn nằm bên trái. Đưa nút mới tạo vào min-heap.
 - 4. Lặp lại bước 2 và 3 cho đến khi heap chỉ còn 1 nút đó là nút gốc.

#### Ví dụ:
{a: 5; b: 9; c: 12; d: 13; e: 16; f: 45}
Step 1: Build min-heap, [5, 9, 12, 13, 16, 45]
Step 2: Lấy 2 node từ min heap: 5-9 -> node mới 14 thêm vào min-heap, 
[12, 13, 14, 16, 45]

![[Pasted image 20261025172318.png]]
Step 3: 12-13 -> 25: [14, 16, 25, 45]
![[Pasted image 20261025172428.png|293]]![[Pasted image 20261025172458.png|302]]
Step 3: 14-16 -> 30: [25, 30, 45]
Step 4: 25-30->55: [45, 55]
Step 5: 45-55 -> 110: [110]

![[Pasted image 20261025172735.png]]


#### Mã hoá:: cạnh trái 0, cạnh phải 1
![[Pasted image 20261025172815.png]]

#### Giải mã:
- 1. Đặt con trỏ p ở gốc
- 2. Dò xuống đến lá, lấy ra phần từ và tiếp tục khi hết bit.