## Ý tưởng
Couting Sort đếm số lần xuất hiện của từng giá trị rồi dùng bảng đếm đó để biết mỗi phần từ nằm ở vị tí nào trong bảng kết quả. 
## Cách hoạt động
- 1. Tìm phần tử lớn nhất max
- 2. Tạo mảng count có max + 1 ô, tất cả là 0
- 3. Duyệt mảng gốc, tăng count[giá trị] lên 1 khi gặp giá trị đó.
- 4. Biến count thành tổng tích luỹ: count[i] += count[i-1]. Có nghĩa vị trí đó cho biết có bao nhiêu phần tử <=.
- 5. Duyệt mảng gốc từ cuối về đầu: đặt phần từ v vào output[count[v] - 1], rồi giảm count[v] đi 1.
## Ví dụ
A = [4, 2, 2, 8, 3, 3, 1]; max = 8
Mảng đếm [0, 1, 2, 2, 1, 0, 0, 0, 1]
Mảng tích luỹ [0, 1, 3, 5, 6, 6, 6, 6, 7]
## Độ phức tạp
- Bốn vòng lặp có chi phí O(max), O(n); tổng O(n+max) cho mọi trường hợp.
- Space Complexity O(max).
- Stablely: Yes.
## Ứng dụng
- Các số nguyên nhỏ có nhiều giá trị trùng nhau.
- Khi cần linear time complexity.
- Làm thuật toán trung gian giúp giữ ổn định.
## Code 