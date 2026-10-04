Hướng Dẫn Thực Chiến: Mổ Xẻ PCAP
**Bước 1**: Lọc Rác (Filtering)
Mở file .pcap lên, anh em sẽ thấy một mớ bòng bong hàng ngàn gói tin. Bây giờ thì dùng ngay mấy cái filter tớ liệt kê  này trên thanh tìm kiếm để lọc ra những thứ bất thường:

**http.request** hoặc **dns**: Tụi *malware* rình rập kiểu gì cũng phải resolve tên miền hoặc gọi HTTP về server để nhận lệnh. Lọc cái này là lòi ra mấy cái domain lạ hoắc hoặc IP khả nghi ngay lập tức.

**tcp.flags.syn == 1** and **tcp.flags.ack == 0**: Dùng để xem có thanh niên nào đang quét cổng (Port Scan) hệ thống của mình không. (Giải thích nhẹ: Hacker đang gửi cờ SYN để dò hỏi cổng mở, nhưng không hoàn thành quá trình bắt tay 3 bước của TCP).

**ip.addr ==**: Nếu đã bắt được một IP lạ từ 2 lệnh trên, dùng lệnh này để soi riêng mọi đường đi nước bước của thằng IP đó.

**Bước 2**: Bắt Quả Tang Quá Trình nhả Mã Độc 
Nếu qua bước 1, anh em nghi ngờ hacker đã tải thêm file *.exe* hoặc *payload* độc hại về máy nạn nhân qua giao thức HTTP, anh em xài trick này để lôi cổ cái file đó ra khỏi traffic mà không cần chạy nó:

Vào *File -> Export Objects -> HTTP*.

Nó sẽ hiện ra danh sách toàn bộ các file được tải về.

Bấm Save là lấy được con *malware* ra.
Tips nhỏ: Lấy được file rồi thì đem quăng lên **VirusTotal** để check xem có bao nhiêu trình diệt virus báo đỏ, hoặc ném vào máy ảo để dịch ngược (Reverse Engineering) tiếp.

**Bước 3**: Đọc Lén Tin Nhắn Của Hacker 
Để xem chi tiết cuộc trò chuyện hay thao tác giữa máy nạn nhân và server của hacker:

Chuột phải vào một gói tin đáng ngờ.

Chọn *Follow -> TCP Stream* (hoặc HTTP Stream).

Lúc này, nếu hacker dùng giao thức không mã hóa (như FTP, Telnet hoặc HTTP thường), toàn bộ nội dung lệnh, file name, hay thậm chí mật khẩu (clear-text) sẽ hiện ra rõ mồn một.
Tips nhỏ: Chữ **màu ĐỎ** là dữ liệu máy tính của mình (client) gửi đi, chữ **màu XANH** là dữ liệu từ server của hacker trả về.

 Tài Liệu & Source Code Đính Kèm Cho Anh Em Vọc
1. Kho PCAP nhiễm Malware thực tế để luyện tập
Học là phải đi đôi với hành. Anh em muốn có file .pcap chứa malware thật (Emotet, Trickbot, v.v.) để vọc kỹ năng phân tích thì vào repo này.

LƯU Ý CỰC CĂNG: Nếu extract file độc hại ra thì nhớ dùng máy ảo (VMware/VirtualBox) nhé, click nhầm trên máy thật là toang lun cái máy anh em đếy.

🔗 brad-anton/network-forensics-pcaps

2. Tool Python tự động phân tích PCAP
Nếu anh em làm biếng ngồi soi từng dòng Wireshark đau mắt, tớ share luôn cái repo code Python dùng thư viện pyshark tự động cắn file pcap và gen ra báo cáo phân tích bằng file PDF cực xịn (thống kê IP, DNS, chẩn đoán rủi ro). Tải source này về đọc hiểu và build thêm chức năng là hết nước chấm:

🔗 dincbrk/pcap-analyzer

Bài viết tới đây thôi, anh em vọc thử đi, bí chỗ nào mở Issue hoặc comment tớ chỉ cho.🐧
