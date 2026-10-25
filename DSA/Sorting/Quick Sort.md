## Ý tưởng
Quick sort hoạt động theo ý tưởng chia để trị. Chọn một phần tử làm pivot, sắp xếp mảng sao cho các phần từ nhỏ hơn nằm bên trái và lớn hơn nằm bên phải (pivot đã đúng vị trí), rồi đệ quy trên 2 nửa.
## Cách hoạt động
- 1. Chọn pivot = A[hight] hoặc A[low] hoặc median hoặc median of three
- 2. Partition: Tìm  vị trí đúng của pivot
- 3. Đặt pivot vào đúng vị trí.
- 4. Gọi đệ quy cho đoạn bên trái và đoạn bên phải pivot.
## Ví dụ
![[Pasted image 20261025163031.png]]
## Độ phức tạp
- Best case: O(nlogn)
- Avg case: O(nlogn)
- Worse case: O(n^2) khi chọn pivot luôn là phần tử bé nhất hoặc lớn nhất.
- Space Complexity: O(log)
- Stablely: Yes
## Ứng dụng
- Khi ngôn ngử xử lý đệ quy tốt.
- Thời gian quan trọng: hằng số nhỏ nên thường nhanh hơn merge sort và heap sort
## Code 