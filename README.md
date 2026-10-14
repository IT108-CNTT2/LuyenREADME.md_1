# Thống kê sự cố TIMEOUT trên máy chủ.

## Yêu Cầu
- Sử dụng mô hình Pipeline: Đọc tệp -> Lọc từ khóa -> Đếm kết quả
- Thực hiện bằng duy nhất 1 dòng lệnh trên powershell

## 1. Giới thiệu 
**Phòng DevOps cần thống kê nhanh: "Có bao nhiêu sự cố TIMEOUT xảy ra trong tệp log hôm nay?". Yêu cầu kết quả trả về phải là một con số tổng duy nhất thay vì in ra hàng nghìn dòng văn bản.**

## 2. Phân tích
- Liệt kê các file log và đọc các tệp log
- Lọc các dòng từ khóa TIMEOUT và bỏ qua các dòng có từ khóa NOT TIMEOUT
- Đếm các dòng có từ TIMEOUT

## 3. Giải

```Powershell
# Liệt kê các file log
ls -File "*.log" -R
# Đọc các tệp log 
Get-Content
# Duyệt các dòng có từ khóa TIMEOUT
Select-String "TIMEOUT"
#Bỏ qua các dòng có từ khóa NOT TIMEOUT
Where-Object { $_ -notmatch "NOT TIMEOUT"}
#Đếm các dòng có từ khóa TIMEOUT
.count

#Sử dụng toán tử pipe để kết hợp 
(ls -File "*.log" -R | Get-Content | Select-String "TIMEOUT"  | Where-Object { $_ -notmatch "NOT TIMEOUT"}).count
```

## 4. So sánh 
| Tiêu chí | Powershell | Gitbash |
| :--- | :--- | :--- |
| **Lệnh** | `(ls -File "*.log" -R \| Get-Content \| Select-String "TIMEOUT" \| Where-Object { $_ -notmatch "NOT TIMEOUT" }).count` | `grep -rn "TIMEOUT" --include="*.log" . \| grep -v "NOT TIMEOUT" \| wc -l` |
| **Môi trường chạy** | Mặc định trên Windows (Windows 10/11, Server). | Cần cài đặt Git for Windows. |
| **Tính đơn giản & Trực quan** | Rất rõ ràng, cấu trúc hướng đối tượng (Object-oriented). | Cú pháp gọn hơn đối với lệnh đơn, nhưng dùng các công cụ ghép nối truyền thống (pipe). |
| **Độ dài câu lệnh** | Khá tường minh, dễ đọc (`Get-Content`, `Select-String`). | Ngắn gọn hơn nếu dùng `grep -rn` trực tiếp. |
| **Hiệu suất (Performance)** | Xử lý file lớn có thể chậm hơn một chút do xử lý đối tượng. | Rất nhanh và tối ưu với các công cụ C như grep. |
| **Khả năng tương thích** | Chỉ chạy tốt trên hệ sinh thái Windows. | Tương thích hoàn hảo với Linux/macOS (phù hợp CI/CD, Docker). |
