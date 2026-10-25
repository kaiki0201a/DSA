## Ý tưởng
Shell sort là bản tổng quát của insertion sort.
Thay vì chỉ so sánh các phần từ liền kề, nó sắp xếp trước các phần từ cách nhau 1 khoảng h, rồi giảm dần về 1.

### Dãy interval thường dùng:
- Dãy gốc của Shell: N/2, N/4, ..., 1.
- Knuth: 1, 4, 13, ..., ($3^k$ -1)/2
- ...
## Cách hoạt động
- 1. Chọn interval ban đầu = N/2.
- 2. Với mỗi interval, chạy insertion sort trên cách phần tử cách nhau đúng interval. So sánh phần từ i với i - interval. Swap nếu cần.
- 3. Chia đôi interval và lặp lại bước 2.
- 4. Khi interval = 1, mảng được sắp xếp hoàn toàn
## Ví dụ
A = [9, 8, 3, 7, 5, 6, 4, 1] , n = 8

| Interval | Cần swap         | Mảng                     |
| :------: | ---------------- | ------------------------ |
|    4     | 9 <-> 5          | [5, 8, 3, 7, 9, 6, 4, 1] |
|          | 8 <-> 6          | [5, 6, 3, 7, 9, 8, 4, 1] |
|          | 7 <-> 1          | [5, 6, 3, 1, 9, 8, 4, 7] |
|    2     | 5 <-> 3          | [3, 6, 5, 1, 9, 8, 4, 7] |
|          | 6 <-> 1          | [3, 1, 5, 6, 9, 8, 4, 7] |
|          | 9 <-> 4, 5 <-> 4 | [3, 1, 5, 6, 4, 8, 9, 7] |
|          |                  | [3, 1, 4, 6, 5, 8, 9, 7] |
|          | 8 <-> 7          | [3, 1, 4, 6, 5, 7, 9, 8] |
|    1     | 1 <-> 3          | [1, 3, 4, 6, 5, 7, 9, 8] |
|          | 6 <-> 5          | [1, 3, 4, 5, 6, 7, 9, 8] |
|          | 8 <-> 9          | [1, 3, 4, 5, 6, 7, 8, 9] |
## Độ phức tạp
- Tốt nhất và trung bình: O(nlogn).
- Worst case: Không vượt phá O(n^2) phụ thuộc vào dãy interval.
- Space Complexity: O(1)
- Stabely: No.
## Ứng dụng
- Khi việc gọi stack (đệ quy là gánh nặng): dùng shell sort chạy vòng lặp thuần.
- Khi insertion sort kém vì các phần từ gần giá trị nhau năm cách xa: các lượt interval lớn đưa chúng lại gần nhau nhanh hơn.
## Code 