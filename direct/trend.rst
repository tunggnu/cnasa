Góc nhìn: Cuộc đua ra biên mạng
===============================

Khi chúng ta bắt đầu khám phá cách phần mềm hóa đang chuyển đổi mạng lưới, cần nhận ra rằng chính mạng truy cập kết nối các hộ gia đình, doanh nghiệp và người dùng di động với Internet đang trải qua sự thay đổi mạnh mẽ nhất. Các mạng cáp quang đến tận nhà và mạng di động được mô tả trong :ref:`Mục 2.8 <2.8 Access Networks>` hiện đang được xây dựng từ các thiết bị phần cứng phức tạp (ví dụ, OLT, BNG, BBU, EPC). Không chỉ các thiết bị này vốn dĩ là đóng và độc quyền, mà các nhà cung cấp thường đóng gói một tập hợp chức năng rộng lớn và đa dạng vào mỗi thiết bị. Hệ quả là chúng trở nên đắt đỏ để xây dựng, phức tạp để vận hành và chậm thay đổi.

Để đáp lại, các nhà vận hành mạng đang chủ động chuyển đổi từ các thiết bị chuyên dụng này sang phần mềm mở chạy trên các máy chủ, switch và thiết bị truy cập phổ thông. Sáng kiến này thường được gọi là *CORD*, viết tắt của **C**\ entral **O**\ ffice **R**\ e-architected as a **D**\ atacenter (Trung tâm Viễn thông tái kiến trúc như một Trung tâm Dữ liệu), và như tên gọi gợi ý, ý tưởng là xây dựng Trung tâm Viễn thông (hoặc Head End của truyền hình cáp, dẫn đến từ viết tắt *HERD*) bằng chính các công nghệ được sử dụng trong các trung tâm dữ liệu lớn tạo nên đám mây.

Động lực để các nhà vận hành thực hiện điều này một phần là để hưởng lợi từ việc tiết kiệm chi phí khi thay thế thiết bị chuyên dụng bằng phần cứng phổ thông, nhưng chủ yếu là do nhu cầu tăng tốc đổi mới sáng tạo. Mục tiêu của họ là cho phép các lớp dịch vụ biên mới—ví dụ, An ninh công cộng, Xe tự hành, Nhà máy tự động hóa, Internet vạn vật (IoT), Giao diện người dùng nhập vai—hưởng lợi từ kết nối độ trễ thấp tới người dùng cuối, và quan trọng hơn, tới số lượng thiết bị ngày càng tăng mà người dùng đó sở hữu. Điều này dẫn đến một đám mây đa tầng tương tự như minh họa trong :numref:`Hình %s <fig-cord>`.

.. _fig-cord:
.. figure:: figures/access/Slide3.png
   :width: 600px
   :align: center

   Đám mây đa tầng mới nổi bao gồm các đám mây công cộng dựa trên trung tâm dữ liệu,
   các đám mây phân tán đặt tại IXP, và các đám mây biên dựa trên truy cập như CORD.
   Trong khi trên thế giới có khoảng 150 đám mây đặt tại IXP, chúng ta có thể kỳ vọng sẽ có hàng nghìn, thậm chí hàng chục nghìn đám mây biên.

Tất cả điều này là một phần của xu hướng ngày càng tăng là chuyển chức năng ra khỏi trung tâm dữ liệu và tiến gần hơn tới biên mạng, một xu hướng đặt các nhà cung cấp đám mây và nhà vận hành mạng vào thế đối đầu. Các nhà cung cấp đám mây, nhằm phục vụ các ứng dụng yêu cầu độ trễ thấp/băng thông cao, đang rời khỏi trung tâm dữ liệu và tiến ra biên, đồng thời các nhà vận hành mạng lại áp dụng các thực tiễn và công nghệ tốt nhất của đám mây cho biên mạng vốn đã tồn tại và triển khai mạng truy cập. Không thể nói trước mọi thứ sẽ diễn ra thế nào trong tương lai; cả hai ngành đều có những lợi thế riêng.

Một mặt, các nhà cung cấp đám mây tin rằng bằng cách phủ kín các khu vực đô thị bằng các cụm biên và trừu tượng hóa mạng truy cập, họ có thể xây dựng sự hiện diện biên với độ trễ đủ thấp và băng thông đủ cao để phục vụ thế hệ ứng dụng biên tiếp theo. Trong kịch bản này, mạng truy cập vẫn chỉ là một “đường ống bit” ngu ngốc, cho phép các nhà cung cấp đám mây phát huy thế mạnh: vận hành các dịch vụ đám mây quy mô lớn trên phần cứng phổ thông.

Mặt khác, các nhà vận hành mạng tin rằng bằng cách xây dựng mạng truy cập thế hệ tiếp theo dựa trên công nghệ đám mây, họ sẽ có thể đặt các ứng dụng biên ngay trong mạng truy cập. Kịch bản này có những lợi thế sẵn có: hạ tầng vật lý phân bố rộng khắp, hỗ trợ vận hành sẵn có, và hỗ trợ gốc cho cả di động lẫn dịch vụ đảm bảo chất lượng.

Dù thừa nhận cả hai khả năng này, vẫn còn một kết quả thứ ba không chỉ đáng cân nhắc mà còn đáng hướng tới: *dân chủ hóa biên mạng*. Ý tưởng là làm cho đám mây biên truy cập trở nên dễ tiếp cận với bất kỳ ai, chứ không chỉ là lãnh địa của các nhà cung cấp đám mây hoặc nhà vận hành mạng truyền thống. Có ba lý do để lạc quan về khả năng này:

1. Phần cứng và phần mềm cho mạng truy cập đang trở nên phổ thông hóa và mở. Đây là yếu tố then chốt vừa được đề cập. Nếu nó giúp các Telco và CableCo linh hoạt, thì nó cũng có thể mang lại giá trị tương tự cho bất kỳ ai.

2. Có nhu cầu thực tế. Các doanh nghiệp trong lĩnh vực ô tô, nhà máy, kho bãi ngày càng muốn triển khai mạng 5G riêng cho nhiều trường hợp tự động hóa vật lý (ví dụ, một gara nơi nhân viên valet điều khiển xe từ xa hoặc một nhà máy sử dụng robot tự động hóa).

3. Phổ tần đang trở nên sẵn có. 5G đang mở ra cho mô hình sử dụng không cần giấy phép hoặc giấy phép nhẹ ở Mỹ và Đức là hai ví dụ điển hình, các quốc gia khác cũng sẽ sớm theo sau. Điều này có nghĩa là 5G sẽ có khoảng 100-200 MHz phổ tần dành cho sử dụng riêng.

Tóm lại, mạng truy cập vốn dĩ là lãnh địa của các Telco, CableCo và các nhà cung cấp thiết bị độc quyền cho họ, nhưng việc phần mềm hóa và ảo hóa mạng truy cập đang mở ra cánh cửa cho bất kỳ ai (từ thành phố thông minh đến vùng nông thôn chưa được phục vụ, từ khu chung cư đến nhà máy sản xuất) có thể thiết lập một đám mây biên truy cập và kết nối nó với Internet công cộng. Chúng tôi kỳ vọng việc này sẽ trở nên dễ dàng như việc triển khai một bộ định tuyến WiFi ngày nay. Làm như vậy không chỉ đưa biên truy cập vào các môi trường mới (biên hơn), mà còn có tiềm năng mở mạng truy cập cho các nhà phát triển vốn luôn tìm đến nơi có cơ hội đổi mới sáng tạo.

.. admonition:: Góc nhìn rộng hơn

  Để tiếp tục đọc về quá trình “đám mây hóa” Internet, xem
  :ref:`Góc nhìn: Mạng ảo hóa xuyên suốt`.

  Để tìm hiểu thêm về sự chuyển đổi đang diễn ra trong các mạng truy cập, chúng tôi khuyến nghị: `CORD: Central Office Re-architected as a Datacenter, IEEE Communications, October 2016 <https://wiki.opencord.org/display/CORD/Documentation?preview=/1278027/1966399/PETERSON_CORD.pdf>`__ và `Democratizing the Network Edge SIGCOMM CCR, April 2019 <https://ccronline.sigcomm.org/2019/democratizing-the-network-edge/>`__.