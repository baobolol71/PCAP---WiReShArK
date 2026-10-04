# PCAP---WiReShArK
## 🔬 Phân Tích Thực Chiến: Mổ Xẻ PCAP Tìm Dấu Vết Hacker

### 1. Kịch Bản (Scenario)
Giả sử hệ thống phát hiện một máy tính trong mạng có dấu hiệu kết nối đến một máy chủ C2 (Command and Control) đáng ngờ. Bạn được cung cấp một file `capture.pcap` ghi lại toàn bộ lưu lượng mạng trong thời điểm đó. Nhiệm vụ của bạn là tìm ra: Hacker đã làm gì? File độc hại nào đã được tải xuống?

### 2. Các Bước Phân Tích Chi Tiết

#### Bước 1: Tổng Quan và Lọc "Rác" (Filtering)
Khi mở file `.pcap` bằng Wireshark, bạn sẽ thấy hàng ngàn gói tin. Đừng vội cuộn chuột, hãy sử dụng **Display Filters** để thu hẹp phạm vi tìm kiếm.

**Các Filter cơ bản nhưng lợi hại:**
*   `http.request`: Lọc tất cả các gói tin gửi yêu cầu HTTP. Malware thường dùng HTTP POST để gửi dữ liệu đi hoặc HTTP GET để tải payload (mã độc) về.
*   `dns`: Xem các truy vấn phân giải tên miền. Tìm kiếm các domain lạ, hoặc domain có chuỗi ký tự ngẫu nhiên (DGA - Domain Generation Algorithm).
*   `ip.addr == `: Lọc toàn bộ lưu lượng đi/đến một IP cụ thể sau khi bạn đã khoanh vùng được IP của kẻ tấn công hoặc IP của máy nạn nhân.
*   `tcp.flags.syn == 1 and tcp.flags.ack == 0`: Tìm dấu hiệu của hành vi quét cổng (Port Scanning) từ bên ngoài vào hệ thống.

#### Bước 2: Trích Xuất Tang Vật (Export Objects)
Nếu trong bước 1, bạn phát hiện một gói tin HTTP GET tải về một file khả nghi (ví dụ: `update.exe`, `invoice.doc`, `script.vbs`), bạn có thể trích xuất file đó ra khỏi file PCAP để đem đi phân tích động (Dynamic Analysis) hoặc quét bằng VirusTotal.

**Cách thực hiện:**
1. Trên thanh công cụ, chọn menu **File** -> **Export Objects** -> **HTTP...**
2. Một cửa sổ sẽ hiện ra danh sách các file được truyền tải qua giao thức HTTP.
3. Tìm file nghi ngờ dựa trên Host name hoặc Filename, chọn **Save** để lưu ra máy.
*(Lưu ý: Chỉ thực hiện lưu và phân tích file nghi ngờ trên máy ảo Sandbox, tuyệt đối không click mở file trên máy thật).*

#### Bước 3: Theo Dõi Chuỗi Giao Tiếp (Follow TCP Stream)
Để hiểu rõ Hacker và máy nạn nhân đang "nói chuyện" gì với nhau, bạn cần xem toàn bộ dữ liệu của một phiên kết nối.

**Cách thực hiện:**
1. Click chuột phải vào một gói tin đáng ngờ (ví dụ: một gói tin TCP hoặc HTTP).
2. Chọn **Follow** -> **TCP Stream** (hoặc HTTP Stream).
3. Một cửa sổ mới sẽ hiện ra, hiển thị toàn bộ luồng dữ liệu giao tiếp. 
4. Thông thường, dữ liệu màu đỏ là do client gửi đi, màu xanh là server trả về. Tại đây, nếu giao thức không được mã hóa (clear-text), bạn có thể đọc được các câu lệnh hacker thực thi, cấu trúc thư mục bị liệt kê, hoặc thậm chí là thông tin đăng nhập bị đánh cắp.

### 3. Bài Tập Thực Hành (Hands-on Lab)
Để làm chủ kỹ năng này, hãy tải file mẫu dưới đây về và tự thực hành 3 bước trên:
*   [Kho PCAP Thực Hành - Malware Traffic Analysis](https://www.malware-traffic-analysis.net/)
*   **Mục tiêu cần đạt:** Tìm được địa chỉ IP của máy nạn nhân bị nhiễm, xác định tên miền (domain) C2 độc hại và trích xuất thành công file malware từ PCAP.
