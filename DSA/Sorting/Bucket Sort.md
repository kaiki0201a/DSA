## Ý tưởng
Bucket Sort chia các phần tử vào nhiều nhóm gọi là bucket theo khoảng giá trị, sắp xếp từng bucket bằng một thuật toán phù hợp, thường là insertion sort, rồi ghép các bucket theo từng thứ tự. Còn được biết đến là cách tiếp cận scatter-gather: rat phần tử vào bucket, sắp xếp rồi gom bucket lại.
## Cách hoạt động
- 1. Tạo một mảng bucket (mỗi ô là một bucket rỗng) gồm n buckets.
- 2. Chèn từng phần tử vào bucket[i] phụ hợp với range của bucket đó.
  Với giá trị thuộc không thuộc [0,1)
  Ví trí = Phần nguyên của ((giá trị phần tử - min)/Số lượng bucket)
  Với giá trị thuộc [0,1)
  Vị trí = phần nguyên (giá trị phần tử * số buckets)
 - 3. Sắp xếp các phần tử bên trong từng bucket
 - 4. Gồm các bucket lại.
## Ví dụ
A = [0.42, 0.32, 0.33, 0.52, 0.37, 0.47, 0.51]
Ta dùng 10 buckets với công thứ index = phần nguyên(n * 10)


| Bước                | Kết quả                                                                |
| ------------------- | ---------------------------------------------------------------------- |
| Scatter             | bucket 3: 0.32, 0.33, 0.37; bucket 4: 0.42, 0.47; bucket 5: 0.52, 0.51 |
| Sắp xếp từng bucket | bucket 3: 0.32, 0.33, 0.37; bucket 4: 0.42, 0.47; bucket 5: 0.51, 0.52 |
| Gather              | [0.32, 0.33, 0.37, 0.42, 0.47, 0.51, 0.52]                             |
## Độ phức tạp
- Best case: O(n+k) khi phân bố đều
- Average case: O(n)
- Worse case: O(n^2) khi các phần tử quá gần nhau, gần như nằm trong cùng bucket
- Space Complexity: O(n+k)
## Ứng dụng
- Dữ liệu phân bố đều trên một khoảng: mỗi bucket nhận số phần tử xấp xỉ nhau nên mỗi bucket rất nhỏ, sắp xếp rất nhanh.
## Code 

