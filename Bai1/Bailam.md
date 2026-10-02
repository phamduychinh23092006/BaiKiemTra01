# Bài 01 – Phần lý thuyết & câu hỏi ngắn

## Câu 1: Value Types và Reference Types trong C# (Stack vs Heap)

| Tiêu chí | Value Type | Reference Type |
|---|---|---|
| Ví dụ | `int`, `double`, `bool`, `char`, `struct`, `enum` | `class`, `string`, `object`, `interface`, `delegate`, mảng |
| Dữ liệu lưu | Chính giá trị | Địa chỉ (tham chiếu) tới đối tượng |
| Vùng nhớ | Thường ở **Stack** (biến cục bộ); nếu là field của một class thì nằm **inline trong Heap** cùng đối tượng chứa nó | Tham chiếu nằm ở Stack, **đối tượng thật nằm ở Heap** |
| Gán `a = b` | Sao chép giá trị, hai biến độc lập | Sao chép tham chiếu, hai biến cùng trỏ một đối tượng |
| Giá trị mặc định | `0`, `false`... (không thể `null`, trừ `Nullable<T>`) | `null` |
| Thu hồi bộ nhớ | Tự giải phóng khi hết phạm vi (pop khỏi stack) | Do **Garbage Collector** thu dọn |
| Hiệu năng | Nhanh, ít áp lực lên GC | Chậm hơn do cấp phát Heap và GC |

> Lưu ý: "Value type nằm trên Stack" là cách nói đơn giản hóa. Chính xác hơn: value type nằm tại nơi nó được khai báo (stack nếu là biến cục bộ, heap nếu là field của class hoặc phần tử mảng).

---

## Câu 2: Init-only Properties (`init`)

`init` được giới thiệu từ **C# 9**. Thuộc tính `init` chỉ cho phép gán giá trị **trong lúc khởi tạo đối tượng** (object initializer, constructor, hoặc `with` expression). Sau đó, thuộc tính trở thành **bất biến (immutable)**.

| | `set` thông thường | `init` |
|---|---|---|
| Gán trong constructor / object initializer | Được | Được |
| Gán sau khi đối tượng đã tạo xong | Được | **Lỗi biên dịch** |
| Tính bất biến | Không | Có (sau khởi tạo) |

**Trường hợp sử dụng thực tế:**
- Tạo đối tượng bất biến mà vẫn dùng cú pháp object initializer gọn gàng (không cần constructor nhiều tham số).
- DTO / Model / cấu hình ứng dụng (`appsettings`) / dữ liệu trả về từ API: tạo một lần rồi không bị sửa.
- Các thuộc tính định danh như `Id`, `MaSV`, `CreatedAt` không được phép đổi.
- Đảm bảo an toàn luồng (thread-safe) vì dữ liệu không thay đổi; kết hợp với `record` và `with` expression.


## Câu 3: Phương thức `virtual` (lớp cha) và `override` (lớp con) trong Đa hình

- **`virtual`** (lớp cha): đánh dấu phương thức **cho phép** lớp con định nghĩa lại. Lớp cha cung cấp cài đặt mặc định.
- **`override`** (lớp con): **ghi đè** phương thức `virtual` (hoặc `abstract`) của lớp cha bằng cài đặt riêng, giữ nguyên chữ ký (tên, tham số, kiểu trả về).

| | `virtual` | `override` |
|---|---|---|
| Nằm ở | Lớp cha | Lớp con |
| Vai trò | Cho phép được ghi đè, có thân hàm mặc định | Thay thế cài đặt của lớp cha |
| Bắt buộc | Không (lớp con có thể không override) | Chỉ dùng được khi cha có `virtual`/`abstract`/`override` |

Đa hình thể hiện ở chỗ: phương thức được gọi **được quyết định lúc chạy (runtime)** dựa trên kiểu thực của đối tượng, không phải kiểu của biến tham chiếu.

Nếu lớp con dùng `new` thay vì `override` thì chỉ là **ẩn phương thức (method hiding)**, khi gọi qua biến kiểu lớp cha sẽ vẫn chạy phương thức của cha, không có đa hình.


## Câu 4: Vì sao thành viên `static` không truy xuất được qua một thể hiện (object instance)?

- Thành viên `static` **thuộc về chính kiểu (class)**, chỉ có **một bản duy nhất** dùng chung, được tạo khi kiểu được nạp, không phụ thuộc vào việc có đối tượng nào hay không.
- Thành viên thuộc instance thì mỗi đối tượng tạo bằng `new` có bản riêng.
- Vì vậy C# quy định truy cập thành viên `static` **qua tên lớp**, không qua đối tượng. Truy cập qua instance sẽ báo lỗi biên dịch **CS0176**. Điều này tránh nhầm lẫn rằng dữ liệu là của riêng từng đối tượng, trong khi thực tế mọi đối tượng dùng chung một giá trị.

Ngược lại, phương thức `static` cũng không dùng được `this` và không truy cập trực tiếp thành viên instance, vì nó không gắn với đối tượng cụ thể nào.

