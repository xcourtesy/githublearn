# Tìm Hiểu KVM Hypervisor
* Tuần 2
* Người thực hiện: Nguyễn Xuân Lưu
## [1. Giới thiệu chung về công nghệ ảo hóa](#vir)
## [2. Giới thiệu chung về KVM](#kvm)

## <a name="vir"> </a>1. Giới thiệu chung về công nghệ ảo hóa
  **Ảo hóa** là một cách tiếp cận nhằm chia sẻ tài nguyên để đơn giản hóa việc quản lý và tăng giá trị sử dụng của tài nguyên công nghệ. Điều này dẫn đến việc tối ưu hóa các yêu cầu về kinh tế. Trong công nghệ, ảo hóa được hiểu đơn giản là công nghệ tạo ra môi trường phần cứng không thực.
  
  **Tại sao cần ảo hóa?**
  
  Vấn đề ảo hóa đặt ra ở những hệ thống lưu trữ, xử lý dữ liệu lớn. Lý do đầu tiên dẫn tới cần ảo hóa là việc sử dụng không đúng hiệu năng của phần cứng. Trước khi xu hướng ảo hóa xuất hiện, nhiều trung tâm dữ liệu chỉ phục vụ và lữu trữ ở mức 10% hoặc ít hơn dung lượng của chúng. Điều này thúc đẩy các nhà khoa học phải giải quyết vấn đề này. Ngày nay, khi công nghệ ảo hóa được áp dụng, hiệu năng sử dụng phần cứng của các tổ chức đã tăng từ 10% lên đến 70% - 80%.
  
  Tiếp đến, lý do thứ hai là do các trung tâm dữ liệu dễ dàng bị quá tải với khối lượng dữ liệu lớn từ nhiều dịch vụ khác nhau. Với sự phổ cập của Internet và các dịch vụ liên quan bao gồm email, website, video và ứng dụng di động, khối lượng dữ liệu lớn nếu chỉ được xử lý trên một hệ thống thì trung tâm dữ liệu dễ dàng bị quá tải. Phương án đưa ra để giải quyết là xây dựng thêm những trung tâm dữ liệu mới. Điều này thật quá tốn kém. Công nghệ ảo hóa cho phép chạy song song nhiều hệ thống khác nhau trên duy nhất một nền tảng phần cứng. Đồng nghĩa là, rắc rối với việc xử lý nhiều dịch vụ sẽ được giải quyết.
  
  Lý do thứ ba được đưa ra là vấn đề chi phí năng lượng. Các hệ thống lớn chạy rất tốn điện. Hiệu năng giảm xuống đồng nghĩa với lượng điện lãng phí tăng lên đáng kể. Việc sử dụng công nghệ ảo hóa tạo giảm chi phí sử dụng điện cho các tổ chức khi chính công nghệ này làm tăng hiệu năng của các thiết bị phần cứng.
  
  Cuối cùng, việc ảo hóa làm tăng hiệu quả quản lý hệ thống. Ảo hóa đồng nghĩa với giảm số thiết bị phần cứng được sử dụng. Vậy là, việc bảo trì, sửa chữa, thay mới, nâng cấp các thiết bị sẽ diễn ra ít hơn, chi phí cho các hoạt động này cũng giảm xuống. Tiếp nữa, ảo hóa cho phép các hệ thống chạy độc lập với phần cứng thật. Việc sao lưu trở nên dễ dàng. Kế đến, ảo hóa cho phép di chuyển các hệ thống từ phần cứng này sang phần cứng khác mà không cần mất thời gian tạm dừng hoạt động. Điều này là một ưu điểm vô cùng lớn của công nghệ ảo hóa.
  
  Và dù vì rất nhiều lý do, công nghệ ảo hóa đã được toàn thế giới chấp nhận, sử dụng và đang ảnh hưởng tới toàn bộ hoạt động của thế giới hiện nay.
  
 **Các loại ảo hóa**
 ![](/src-image/w2_1.png)
 
 Dựa trên hình chúng ta có thể thấy, ảo hóa sẽ được thực hiện trên mọi thành phần. Ví dụ, công nghệ SDN (Software Define Network) thực hiện ảo hóa mạng, công nghệ SDS (Software Define Storage) thực hiện ảo hóa lưu trữ, … Trong khuôn khổ tìm hiểu về KVM, chúng ta tập chung vào Software Virtualization.

## <a name="kvm"></a>2. Giới thiệu chung về KVM
