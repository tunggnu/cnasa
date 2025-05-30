Góc nhìn: HTTP là “eo hẹp” mới của Internet
===========================================

Internet đã từng được mô tả là có kiến trúc *eo hẹp*, với một giao thức phổ quát ở giữa (IP), mở rộng ra để hỗ trợ nhiều giao thức truyền tải và ứng dụng phía trên (ví dụ: TCP, UDP, RTP, SunRPC, DCE-RPC, gRPC, SMTP, HTTP, SNMP) và có thể chạy trên nhiều công nghệ mạng phía dưới (ví dụ: Ethernet, PPP, WiFi, SONET, ATM). Cấu trúc tổng thể này là chìa khóa giúp Internet trở nên phổ biến: bằng cách giữ cho lớp IP mà mọi người phải đồng thuận ở mức tối thiểu, hàng ngàn “bông hoa” đã được phép nở rộ cả ở phía trên lẫn phía dưới. Đây hiện là một chiến lược được hiểu rộng rãi cho bất kỳ nền tảng nào muốn đạt được sự phổ cập.

Nhưng đã có điều gì đó khác xảy ra trong 30 năm qua. Khi không giải quyết tất cả các vấn đề mà Internet cuối cùng sẽ phải đối mặt khi phát triển (ví dụ: bảo mật, kiểm soát tắc nghẽn, di động, đáp ứng thời gian thực, v.v.), đã trở nên cần thiết phải bổ sung một loạt các tính năng mới vào kiến trúc Internet. Việc có địa chỉ phổ quát của IP và mô hình dịch vụ “best-effort” là điều kiện cần để được chấp nhận, nhưng chưa đủ để làm nền tảng cho tất cả các ứng dụng mà con người muốn xây dựng.

Chúng ta vẫn chưa thấy hết các giải pháp này—các chương sau sẽ mô tả cách Internet quản lý tắc nghẽn (:ref:`Chương 6 <Chapter 6: Congestion Control>`), cung cấp bảo mật (:ref:`Chương 8 <Chapter 8: Network Security>`), và hỗ trợ các ứng dụng đa phương tiện thời gian thực (:ref:`Chương 7 <Chapter 7: End-to-End Data>` và :ref:`9 <Chapter 9: Applications>`)—nhưng đây là thời điểm thích hợp để nhìn nhận lại giá trị của một “eo hẹp” phổ quát với sự tiến hóa tất yếu trong bất kỳ hệ thống lâu dài nào: “điểm cố định” mà xung quanh đó kiến trúc còn lại phát triển đã chuyển lên một vị trí mới trong ngăn xếp phần mềm. Nói ngắn gọn, HTTP đã trở thành “eo hẹp” mới; là phần chung/giả định của hạ tầng toàn cầu giúp mọi thứ khác trở nên khả thi. Điều này không xảy ra trong một đêm hay do một tuyên bố chính thức, mặc dù một số người đã dự đoán nó sẽ xảy ra. “Eo hẹp” này đã từ từ dịch chuyển lên ngăn xếp giao thức như một hệ quả của quá trình tiến hóa (để pha trộn ẩn dụ địa chất và sinh học).

.. _fig-waist:
.. figure:: figures/rpc/Slide3.png
   :width: 500px
   :align: center

   HTTP (cùng với TLS, TCP và IP) tạo thành “eo hẹp” của kiến trúc Internet ngày nay.

Gán nhãn “eo hẹp” chỉ cho HTTP là một sự đơn giản hóa quá mức. Thực tế, đây là nỗ lực của cả một nhóm, với sự kết hợp HTTP/TLS/TCP/IP hiện đang đóng vai trò là nền tảng chung của Internet.

-  HTTP cung cấp các định danh đối tượng toàn cầu (URI) và giao diện GET/PUT đơn giản.

-  TLS cung cấp bảo mật truyền thông đầu-cuối.

-  TCP cung cấp quản lý kết nối, truyền tải tin cậy và kiểm soát tắc nghẽn.

-  IP cung cấp địa chỉ máy chủ toàn cầu và một lớp trừu tượng mạng.

Nói cách khác, mặc dù bạn có thể tự phát minh thuật toán kiểm soát tắc nghẽn của riêng mình, TCP đã giải quyết vấn đề này khá tốt, nên hợp lý khi tái sử dụng giải pháp đó. Tương tự, dù bạn có thể tự tạo giao thức RPC riêng, HTTP đã cung cấp một giao thức hoàn toàn phù hợp (và vì đi kèm với bảo mật đã được kiểm chứng, nó còn có ưu điểm là không bị tường lửa doanh nghiệp chặn), nên một lần nữa, hợp lý hơn khi tái sử dụng thay vì phát minh lại bánh xe.

Ít rõ ràng hơn, HTTP cũng là nền tảng tốt để xử lý vấn đề di động. Nếu tài nguyên bạn muốn truy cập đã di chuyển, HTTP có thể trả về *phản hồi chuyển hướng* chỉ cho client đến vị trí mới. Tương tự, HTTP cho phép chèn *proxy lưu trữ* giữa client và server, giúp sao chép nội dung phổ biến ở nhiều vị trí và tiết kiệm thời gian cho client thay vì phải truy xuất dữ liệu từ xa trên Internet. (Cả hai khả năng này sẽ được bàn trong :ref:`Mục 9.1 <9.1 Traditional Applications>`.) Cuối cùng, HTTP còn được dùng để truyền tải đa phương tiện thời gian thực, theo cách gọi là *adaptive streaming* (truyền phát thích ứng). (Xem chi tiết trong :ref:`Mục 7.2 <7.2 Multimedia Data>`.)

.. admonition:: Góc nhìn rộng hơn

   Để tiếp tục đọc về quá trình “đám mây hóa” của Internet, xem
   :ref:`Góc nhìn: Điều phối lưu lượng dựa trên phần mềm (Software-Defined Traffic Engineering)`.

   Để tìm hiểu thêm về vai trò trung tâm của HTTP, chúng tôi khuyến nghị: `HTTP:
   An Evolvable Narrow Waist for the Future
   Internet <https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-5.pdf>`__,
   Tháng 1 năm 2012.
