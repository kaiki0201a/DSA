## Ý tưởng

Bubble sort liên tục so sánh hai phần từ liền kề nhau và hoán đổi chúng nếu chúng sai thứ tự. Sau mỗi lượt, phần từ lớn nhất của phần chưa sắp xếp sẽ "nổi" về cuối mảng như bọt khí nổi lên mặt nước.
## Cách hoạt động

- 1. Bắt đầu từ phần từ đầu, so sánh với phần tử kề bên phải; nếu phần từ bên trái lớn hơn thì hoán đổi.
- 2. Dịch sang phải một vị trí và lặp lại phép so sánh-hoán đổi cho đến hết phần tử chưa sắp xếp.
- 3. Bắt đầu lượt mới từ đầu mảng. Mỗi lượt có thể dùng sớm hơn một vị trí vì phần từ cuối đã được sắp xếp.
- 4. Dừng khi không còn swap hay tất cả đều đã đúng vị trí.
## Ví dụ:
A = [-2, 45, 0, 11, -9]

| Vòng lặp | Các phép hoán đổi | Mảng                |
| :------: | ----------------- | ------------------- |
|    1     | 45 <-> 0          | [-2, 0, 45, 11, -9] |
|          | 45 <-> 11         | [-2, 0, 11, 45, -9] |
|          | 45 <-> -9         | [-2, 0, 11, -9, 45] |
|    2     | -9 <-> 11         | [-2, 0, -9, 11, 45] |
|    3     | 0 <-> -9          | [-2, -9, 0, 11, 45] |
|    4     | -2 <-> -9         | [-9, -2, 0, 11, 45] |

## Độ phức tạp
- Best case:　O(n) khi mảng đã sắp xếp, chỉ duyệt 1 lần không hoán đổi.
- Avg case: O(n^2)
- Worst case: O(n^2) khi mảng bị đảo ngược, số phép so sánh và swap là 
  (n-1) + (n-2) + ... + 1
- Space Comlexity: O(1) cho biến boolean swap
## Ứng dụng
- Khi hiệu năng không phải là mối quan tâm và cần code đơn giản
## Code

