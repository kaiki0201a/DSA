## Ý tưởng
Radix Sort nhóm và sắp xếp các số theo từng chữ số cùng hàng, bắt đầu từ hàng đơn vị đến hàng chục, hàng trăm,... cho đến chữ số cao nhât. Ở mỗi hàng thường dùng thuật toán ổn định như counting sort để sắp xếp.
## Cách hoạt động
- 1. Tìm phần từ lớn nhất để biết số chữ số d cần xử lý.
- 2. Với mỗi hàng, từ hàng đơn vị đến hàng cao nhất, sắp xếp ổn định toàn bộ mảng theo chữ số hàng đó.
- 3. Sau lượt xử lý hàng cao nhất, mảng đã được sắp xếp.
## Ví dụ
A = [121, 432, 564, 23, 1, 45, 788]

| Lượt | Chữ số xét  | Các chữ số hàng đơn vị | Mảng sau sắp xếp              |
| :--: | ----------- | ---------------------- | ----------------------------- |
|  1   | Hàng đơn vị | 1, 2, 4, 3, 1, 5, 8    | 121, 1, 432, 23, 564, 45, 788 |
|  2   | Hàng chục   | 2, 0, 3, 2, 6, 4, 8    | 1, 121, 23, 432, 45, 564, 788 |
|  3   | Hàng trăm   | 0, 1, 0, 4, 0, 5, 7    | 1, 23, 45, 121, 432, 564, 788 |

## Độ phức tạp
- Với counting sort làm thuật toán trung gian thời gian là O(d(n+k)) với mọi trường hợp.
- Stablely: Yes.
## Ứng dụng
- Khi dữ liệu là các số nằm trong khoảng giá trị rộng nhưng có số chữ số hạn chế.
## Code 