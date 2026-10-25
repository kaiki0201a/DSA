## Ý tưởng
Merge sort dùng chiến lược chia để trị: chia mảng thành 2 nửa, đệ quy đến khi mảng còn một phần tử sau đó bắt đâu gộp.
## Cách hoạt động

- 1. Divide: tìm điểm giữa q của đoạn A[p..r] và tách thành A[p..q] và A[q+1..r]
- 2. Conquer: Gọi đệ quy trên từng nửa, dừng khi đoạn chỉ còn một phần tử, [p==r]
- 3. Combine: gọi merge để gộp 2 đoạn con đã sắp xếp thành 1 đoạn sắp xếp

### Hàm merge sử dụng 3 con trỏ
- 1. Sao chép hai nửa vào 2 mảng tạm L và M
- 2. Đặt i = 0 trên L, j = 0 trên M, k = p trên mảng chính.
- 3. Chừng nào chưa hết L hoặc M thì so sánh và chép phần tử nhỏ hơn vào A[k], sau đó k++.
- 4. Khi một mảng đã hết, chép toàn bộ phần còn lại vào A.
## Ví dụ

![[Pasted image 20261025161247.png]]
## Độ phức tạp
- O(nlogn) cho mọi trường hợp: mảng được chia đôi logn lần, mỗi tầng gộp tốn O(n).
- Space Complexity: O(n).
## Ứng dụng
- Bài toán đếm nghịch thế:
  - Trong bước gộp, mỗi lần phải lấy phần từ của nửa phải trước phần tử nửa nửa trái thì số nghịch thế tạo ra là cộng dồn với số phần tử còn lại của nửa trái.
## Code 