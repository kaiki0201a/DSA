## Ý tưởng
Giống cách xếp là bài trên tay: coi phần từ đầu tiên là đã xếp, lần lượt lấy từng phần từ và chèn vào đúng vị trí trong phần đã sắp xếp ở bên trái.
## Cách hoạt động
- 1. Coi phần từ đầu tiên là đã sắp xếp.
- 2. Lấy phần từ kế tiếp, cất vào biến key.
- 3. So sánh key với các phần tử bên trái (đã được sắp xếp), từ phải sang trái. Phần tử nào lớn hơn key thì dịch sang phải một ô.
- 4. Dừng khi gặp phần tử nhỏ hơn hoặc bằng key hoặc hêt mảng, rồi đặt key vào ô trống vừa tạo ra.
- 5. Lặp lại cho đến phần từ cuối cùng.
## Ví dụ
A =[9, 5, 1, 4, 3]

| Vòng lặp | key | Bị dịch qua phải | Mảng            |
| :------: | --- | ---------------- | --------------- |
|    1     | 5   | 9                | [9, 9, 1, 4, 3] |
|          |     |                  | [5, 9, 1, 4, 3] |
|    2     | 1   | 9                | [5, 9, 9, 4, 3] |
|          | 1   | 5                | [5, 5, 9, 4, 3] |
|          |     |                  | [1, 5, 9, 4, 3] |
|    3     | 4   | 9                | [1, 5, 9, 9, 3] |
|          | 4   | 5                | [1, 5, 5, 9, 3] |
|          | 4   |                  | [1, 4, 5, 9, 3] |
|    4     | 3   | 9                | [1, 4, 5, 9, 9] |
|          | 3   | 5                | [1, 4, 5, 5, 9] |
|          | 3   | 4                | [1, 4, 4, 5, 9] |
|          |     |                  | [1, 3, 4, 5, 9] |
## Độ phức tạp
- Best case: O(n) khi mảng đã sắp xếp
- Avg-Worst case: O($n^2$): khi mảng bị đảo ngược, mỗi phần từ đều cần xếp đúng vị trí.
- Space Complexity: O(1) cho biến key.
- Stablely: Yes
## Ứng dụng
- Khi mảng có ít phần từ, chi phí cố định thấp và không cần bộ nhớ phụ.
- Mảng gần như đã sắp xếp.
## Code 