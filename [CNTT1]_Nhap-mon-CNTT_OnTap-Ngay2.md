
# BÀI LÀM – ÔN TẬP NGÀY 2
**Hệ điều hành:** Windows PowerShell

## Bài 1. Lọc log thủ công 

### Lệnh đã chạy

```powershell
Get-Content .\logs\access.log | Select-String "ERROR" | ForEach-Object { $_.Line } | Set-Content .\logs\error_report.txt
```

### 2 dòng lỗi đầu tiên trong error_report.txt

```text
2025-10-18 08:17:59 [ERROR] Khong tim thay trang /lich-thi (404)
2025-10-18 08:25:55 [ERROR] Het thoi gian cho (timeout)
```

## Bài 2. Đếm bằng Pipeline 

### Lệnh đã chạy

Lệnh 1 – Đếm WARNING trong access.log:

```powershell
Get-Content .\logs\access.log | Select-String "WARNING" | Measure-Object | Select-Object -ExpandProperty Count
```

Lệnh 2 – Đếm ERROR trong payment.log:

```powershell
Get-Content .\logs\payment.log | Select-String "ERROR" | Measure-Object | Select-Object -ExpandProperty Count
```

### Kết quả đếm

```text
WARNING trong access.log: 4
ERROR trong payment.log: 3
```

## Bài 3. Chạy script auto_warning.ps1 

### Nội dung file auto_warning.ps1

```powershell
# Script: auto_warning.ps1
# Tac gia: [NGUYEN BUI GIA BAO]
# Ngay tao: 2026-10-21

Write-Host "Dang loc canh bao thanh toan..."

$logPath = Join-Path $PSScriptRoot "logs\payment.log"
$reportPath = Join-Path $PSScriptRoot "logs\warning_report.txt"

Select-String -Path $logPath -Pattern "WARNING" |
    ForEach-Object { $_.Line } |
    Set-Content -Path $reportPath

Write-Host "Hoan thanh!"
```

### Lệnh chạy script

```powershell
.\auto_warning.ps1
```

### Nội dung file logs/warning_report.txt

```text
2025-10-18 09:19:13 [WARNING] Cong thanh toan phan hoi cham
2025-10-18 09:28:16 [WARNING] Nhieu lan nhap sai ma OTP
2025-10-18 09:43:01 [WARNING] So du vi gan bang hoc phi
2025-10-18 09:46:22 [WARNING] So du vi gan bang hoc phi
2025-10-18 09:49:43 [WARNING] So du vi gan bang hoc phi
```

## Bài 4. Báo cáo tổng hợp 

### Các lệnh đã chạy

Lệnh 1 – Đếm số dòng WARNING trong payment.log:

```powershell
$warningCount = (Select-String -Path .\logs\payment.log -Pattern "WARNING" | Measure-Object).Count
```

Lệnh 2 – Lấy các dòng ERROR trong access.log:

```powershell
$errorLines = Get-Content .\logs\access.log | Select-String "ERROR" | ForEach-Object { $_.Line }
```

Lệnh 3 – Tạo báo cáo tổng hợp:

```powershell
@(
    "=== TONG HOP ACCESS LOG ==="
    $errorLines
    "So dong WARNING trong payment.log: $warningCount"
) | Set-Content .\logs\tong_hop_ngay2.txt
```
## Bài 5. Câu hỏi ngắn

- **a) Lệnh `>` và `>>` khác nhau thế nào?**
  `>` ghi nội dung vào file và ghi đè nội dung cũ nếu file đã tồn tại. `>>` ghi nối tiếp vào cuối file, giữ nguyên nội dung đã có.

- **b) Nếu lệnh thứ hai dùng `>` thay cho `>>`, nội dung file thay đổi thế nào? Tại sao?**
  Nội dung cũ của file sẽ bị ghi đè bởi kết quả mới, khiến tiêu đề và các dòng đã ghi trước đó có thể bị mất. Nguyên nhân là `>` thay thế nội dung file thay vì nối thêm như `>>`.
- **c) Vì sao trong script nên dùng đường dẫn tương đối thay vì đường dẫn tuyệt đối?**
  Đường dẫn tương đối giúp script dễ chạy trên máy khác khi cấu trúc thư mục được giữ nguyên, không phụ thuộc vào ổ đĩa hay tên tài khoản cụ thể. Có thể dùng `$PSScriptRoot` kết hợp với đường dẫn tương đối để script tìm file ổn định hơn.


