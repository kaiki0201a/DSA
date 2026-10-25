## Ý tưởng
Mỗi lượt, chọn phần từ nhỏ nhất trong phần tử chưa sắp xếp rồi đưa nó về phần đầu. Sau mỗi lượt, phần đã sắp xép ở bên trái dài thêm 1 phần tử.
 Dùng 2 trỏ lưu vị trí cần tìm phần từ và một con trỏ để duyệt mảng
## Cách hoạt động
 - 1. Đặt phần từ đầu mảng làm min
 - 2. So sánh min với từng phần từ còn lại và tìm ra phần từ bé nhất
 - 3. Hoán đổi vị trí đầu với phần tử bé nhất tìm được
 - 4. Dịch trỏ qua bên phải và lặp lại cho đến khi mọi phần từ đúng chỗ (n-1) lượt.
## Ví dụ
A = [20, 12, 10, 15, 2]

| Vòng lặp | Phần tử chưa sắp xếp | Min | Mảng                |
| :------: | :------------------: | --- | ------------------- |
|    1     |  20, 12, 10, 15, 2   | 2   | [2, 12, 10, 15, 20] |
|    2     |    12, 10, 15, 20    | 10  | [2, 10, 12, 15, 20] |
|    3     |      12, 15, 20      | 12  | [2, 10, 12, 15, 20] |
|    4     |        15, 20        | 15  | [2, 10, 12, 15, 20] |

## Độ phức tạp
- Số phép so sánh là (n-1) + (n-2) + ... + 1 trong mọi trường hợp -> O($n^2$).
- Số lần hoán đổi: n-1.
- Space Comlexity: O(1).
- Stability: No
## Ứng dụng
- Khi mảng nhỏ, chi phí hoán đổi không quan trọng.
- Bắt buộc kiểm tra từng phần tử.
- Hoán đổi ít hơn bubble sort, nên tổng số lần ghi vào bộ nhớ là O(n), trong khi bubble sort là O($n^2$)
## Code 