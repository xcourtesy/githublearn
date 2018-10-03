# Tìm Hiểu QEMU

## Giới thiệu chung

QEMU (viết tắt của Quick Emulator) là một phần mềm mã nguồn mở cho phép phỏng tạo và ảo hóa máy ảo.

Các chế độ của QEMU

* User-mode emulation

Trong chế độ này, QEMU cho phép chạy các chương trình được biên dịch cho một kiểu kiến trúc sang một kiểu kiến trúc khác. Ví dụ, một chương trình trên windows chạy trên linux.

* System emulation

Trong chế độ này, QEMU phỏng tạo (Emulator) một hệ thống máy tính hoàn chỉnh, bao gồm các thiết bị ngoại vi. Nó có thể được dụng để tạo ra nhiều máy tính ảo trên một máy tính thật. QEMU có thể chạy nhiều hệ điều hành khách, bao gồm Linux, Solaris, Microsoft Windows, DOS và BSD. Nó hỗ trợ tập lệnh nhiều kiểu kiến trúc, bao gồm x86, MIPS, ARMv7, ARMv8, PowerPC, SPARC, ETRAX CRIS và MicroBlaze.

* KVM Hosting

Khi kết hợp cùng KVM, QEMU hỗ trợ phỏng tạo các thiết bị ngoại vi cho máy ảo, đồng thời, phần chạy các guest code của máy ảo sẽ được QEMU  gọi tới sự hỗ trợ của KVM.

* Xen Hosting

Đối với Xen, QEMU chỉ đảm nhiệm việc phỏng tạo phần cứng cho máy ảo. Việc thực thi các guest code của máy ảo hoàn toàn do XEN quản lý.


Các máy ảo thông qua sự hỗ trợ của QEMU có thể giao tiếp với các phần cứng vật lý của máy chủ, bao gồm hard disk, CD-ROM drives, network cards, audio interfaces và USB. Các thiết bị USB có thể được phỏng tạo hoàn toàn hoặc các thiết bị USB của máy chủ cũng có thể được sử dụng với các quyền của quản trị viên và không hoạt động với tất cả các loại thiết bị.

Các máy ảo có thể được lưu trữ ở định dạng đặc biệt ( qcow hoặc qcow2 ) ở bộ nhớ máy chủ. Chúng chỉ chiếm dung lượng đĩa mà máy ảo thực sự sử dụng. Bằng cách này, một ổ đĩa được mô phỏng cho máy ảo có kích thước 120 GB có thể chỉ chiếm cỡ vài trăm MB trên máy chủ. Định dạng QCOW2 cũng cho phép ghi lại sự khác biệt của file máy ảo hoạt động so với file máy ảo (chưa sửa đổi) trước khi cài đặt. Điều này cung cấp khả năng khôi phục nội dung của file máy ảo về trạng thái ban đầu. Ví dụ: khi máy ảo bị lỗi, do virus hoặc sự cố khác, thông qua định dạng file qcow2, máy ảo có thể khôi phục lại trạng thái trước đó. Đồng thời, QEMU có thể lưu và khôi phục trạng thái của máy ảo và tất cả các chương trình đang chạy trên máy ảo. Các hệ điều hành máy ảo không cần chỉnh sửa để chạy được bên trong QEMU.

QEMU có thể mô phỏng các card mạng và chia sẻ kết nối mạng của hệ thống máy chủ tới máy ảo bằng cách thực hiện dịch địa chỉ mạng, cho phép máy ảo sử dụng cùng một mạng với máy chủ. Các card mạng ảo cũng có thể kết nối với nhau để tạo một mạng ảo cục bộ.

QEMU không phụ thuộc vào các phương thức hiển thị đồ họa đầu vào đầu ra trên hệ thống máy chủ. Thay vào đó, nó có thể cho phép một người truy cập vào màn hình của hệ điều hành máy ảo thông qua một máy chủ VNC tích hợp.

## Cài đặt QEMU trên Ubuntu 16.04

## Kiến trúc QEMU
### Tổng Quan
Việc chạy một máy ảo bao gồm thực thi guest code, điều khiển bộ định thời, chạy các I/O, và phản hồi lại các lệnh giám sát hệ thống. Thực hiện tất cả điều này yêu cầu một kiến trúc phù hợp. Có hai kiến trúc phù hợp cho câc chương trình cần phản hồi sự kiện đến từ nhiều tài nguyên:

* Parallel Architecture: Chia công việc thành các tiến trình (processes) hoặc các luồng (threads) và thực thi song song.
* Event-driven Architecture: Phản hồi sự kiện bằng cách chạy một vòng lặp chính để nhận và xử lý sự kiện.

QEMU sử dụng kiến trúc hybrid bao gồm event-driven đi cùng các luồng. Điều này là hợp lý vì một vòng lặp sự kiện đơn không phù hợp với kiểu CPU đa lõi của máy chủ khi nó chỉ có một luồng thực thi đơn. Thêm vào đó, thi thoảng, sẽ đơn giản hơn nếu viết các luồng riêng cho việc thwujc thi các công việc riêng biệt hơn là tích hợp tất cả vào một kiến trúc event-driven. Tuy nhiên, lõi của QEMU là kiến trúc event-driven và phần lớn code thực thi theo kiểu kiến trúc đó.

### Event-driven

Kiến trúc event-driven tập trung vào một vòng lặp sự kiện chính, tại đó, các sự kiện sẽ được điều hướng tới thủ tục giải quyết nó.

Vòng lặp chính của QEMU gọi là main_loop_wait().

Hàm main_loop_wait() này được khai báo trong source code tại
```
include/qemu/main-loop.h
utils/main-loop.c
```

Nhiệm vụ của main_loop_wait() bao gồm:

* Đợi các file descriptor chuyển trạng thái có thể đọc hoặc có thể ghi. Các file decriptor chiếm vai trò cốt yếu vì các file, các cổng và vô số các tài nguyên khác đều là các file descriptor. 
* Chạy các bộ định thời
* Chạy các Bottom-Half (BH)

Khi một file descriptor trở nên sẵn sàng, một bộ định thời hết hạn hoặc một BH được lên lịch chạy, vòng lặp sẽ khởi tạo một lời gọi để phản hồi lại sự kiện trên.






