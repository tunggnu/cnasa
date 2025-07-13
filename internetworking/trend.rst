Góc nhìn: Mạng ảo ở mọi tầng
============================

Gần như từ khi các mạng chuyển mạch gói xuất hiện, đã có những ý tưởng về cách ảo hóa chúng, bắt đầu từ các mạch ảo. Nhưng chính xác thì ảo hóa một mạng nghĩa là gì?

Bộ nhớ ảo là một ví dụ hữu ích. Bộ nhớ ảo tạo ra một trừu tượng về một vùng bộ nhớ lớn và riêng tư, dù bộ nhớ vật lý bên dưới có thể được chia sẻ bởi nhiều ứng dụng và thực tế nhỏ hơn nhiều so với vùng bộ nhớ ảo biểu kiến. Trừu tượng này cho phép lập trình viên vận hành dưới ảo tưởng rằng có rất nhiều bộ nhớ và không ai khác đang sử dụng nó, trong khi bên dưới, hệ thống quản lý bộ nhớ xử lý các việc như ánh xạ bộ nhớ ảo vào tài nguyên vật lý và tránh xung đột giữa các người dùng.

Tương tự, ảo hóa máy chủ cung cấp trừu tượng về một máy ảo (VM), có đầy đủ các tính năng của một máy vật lý. Một lần nữa, có thể có nhiều VM được hỗ trợ trên một máy chủ vật lý duy nhất, và hệ điều hành cùng người dùng trên máy ảo hoàn toàn không biết rằng VM đó đang được ánh xạ lên các tài nguyên vật lý.

Một điểm then chốt là ảo hóa tài nguyên tính toán giữ nguyên các trừu tượng và giao diện đã tồn tại trước khi chúng được ảo hóa. Điều này quan trọng vì nó có nghĩa là người dùng của các trừu tượng đó không cần thay đổi gì—họ thấy một bản sao trung thực của tài nguyên được ảo hóa. Ảo hóa cũng có nghĩa là các người dùng khác nhau (đôi khi gọi là *tenant*) không thể can thiệp lẫn nhau. Vậy điều gì xảy ra khi chúng ta cố gắng ảo hóa một mạng?

VPN, như mô tả ở :ref:`Mục 3.3 <3.3 Internet (IP)>`, là một trong những thành công sớm của mạng ảo. Chúng cho phép các nhà cung cấp dịch vụ mang đến cho khách hàng doanh nghiệp ảo tưởng rằng họ có một mạng riêng tư, dù trên thực tế họ đang chia sẻ các liên kết và switch vật lý với nhiều người dùng khác. Tuy nhiên, VPN chỉ ảo hóa một vài tài nguyên, nổi bật là bảng địa chỉ và bảng định tuyến. Ảo hóa mạng theo nghĩa phổ biến ngày nay tiến xa hơn, ảo hóa mọi khía cạnh của mạng. Điều đó có nghĩa là một mạng ảo nên hỗ trợ tất cả các trừu tượng cơ bản của một mạng vật lý. Theo nghĩa này, chúng tương tự như máy ảo, với việc hỗ trợ tất cả tài nguyên của một máy chủ: CPU, lưu trữ, I/O, v.v.

Để đạt được điều này, VLAN, như mô tả ở :ref:`Mục 3.2 <3.2 Switched Ethernet>`, là cách chúng ta thường ảo hóa một mạng L2. VLAN tỏ ra rất hữu ích cho các doanh nghiệp muốn cô lập các nhóm nội bộ khác nhau (ví dụ, phòng ban, phòng lab), cho mỗi nhóm cảm giác như có một LAN riêng. VLAN cũng được xem là một cách đầy hứa hẹn để ảo hóa mạng L2 trong các trung tâm dữ liệu đám mây, cho phép mỗi tenant có mạng L2 riêng để cô lập lưu lượng của họ khỏi lưu lượng của các tenant khác. Nhưng có một vấn đề: 4096 VLAN khả dụng là không đủ để đáp ứng tất cả tenant mà một đám mây có thể phục vụ, và để phức tạp hơn, trong môi trường đám mây, mạng cần kết nối các *máy ảo* thay vì các máy vật lý mà các VM đó chạy trên đó.

Để giải quyết vấn đề này, một chuẩn khác gọi là *Virtual Extensible LAN* (VXLAN) đã được giới thiệu. Không giống như cách tiếp cận ban đầu, vốn thực chất đóng gói một frame ethernet ảo bên trong một frame ethernet khác, VXLAN đóng gói một frame ethernet ảo bên trong một gói UDP. Điều này nghĩa là một mạng ảo dựa trên VXLAN (thường được gọi là *overlay network*) chạy trên một mạng dựa trên IP, vốn lại chạy trên ethernet vật lý bên dưới (hoặc có thể chỉ trong một VLAN của ethernet vật lý). VXLAN cũng cho phép một tenant đám mây có nhiều VLAN riêng, cho phép họ phân tách lưu lượng nội bộ của mình. Điều này nghĩa là cuối cùng có thể có một VLAN được đóng gói trong một overlay VXLAN, lại được đóng gói trong một VLAN.

Điều mạnh mẽ của ảo hóa là khi được thực hiện đúng, nó cho phép lồng một tài nguyên ảo hóa bên trong một tài nguyên ảo hóa khác, bởi vì sau cùng, một tài nguyên ảo nên hành xử giống như tài nguyên vật lý và chúng ta biết cách ảo hóa tài nguyên vật lý! Nói cách khác, khả năng ảo hóa một tài nguyên ảo là bằng chứng tốt nhất rằng bạn đã làm tốt việc ảo hóa tài nguyên vật lý gốc. Để mượn lại thần thoại về World Turtle: Đó là mạng ảo ở mọi tầng.

.. _fig-vxlan:
.. figure:: figures/impl/Slide8.png
   :width: 500px
   :align: center

   Header VXLAN được đóng gói trong một gói UDP/IP.

Header VXLAN thực tế rất đơn giản, như thể hiện trong :numref:`Hình %s <fig-vxlan>`. Nó bao gồm một trường *Virtual Network Id* (VNI) 24-bit, cùng một số bit cờ và bit dự trữ. Nó cũng ngụ ý một thiết lập cụ thể cho các trường cổng nguồn và đích của UDP (xem :ref:`Mục 5.1 <5.1 Simple Demultiplexor (UDP)>`), với cổng đích 4789 được dành riêng chính thức cho VXLAN. Việc xác định cách nhận diện duy nhất các mạng LAN ảo (VLAN tag) và mạng ảo (VXLAN VID) là phần dễ. Điều này là bởi vì đóng gói là nền tảng cơ bản của ảo hóa; tất cả những gì bạn cần thêm là một định danh cho biết gói đóng gói này thuộc về người dùng nào trong số nhiều người dùng khả dĩ.

Phần khó là đối mặt với ý tưởng về các mạng ảo được lồng (đóng gói) bên trong các mạng ảo, vốn là phiên bản đệ quy của mạng. Thách thức khác là hiểu cách tự động hóa việc tạo, quản lý, di chuyển và xóa các mạng ảo, và ở khía cạnh này vẫn còn rất nhiều chỗ để cải thiện. Làm chủ được thách thức này sẽ là trọng tâm của lĩnh vực mạng trong thập kỷ tới, và dù một phần công việc này chắc chắn sẽ diễn ra trong các môi trường độc quyền, vẫn có các nền tảng ảo hóa mạng mã nguồn mở (ví dụ, dự án *Tungsten Fabric* của Linux Foundation) đang dẫn đầu xu hướng.

.. admonition:: Góc nhìn rộng hơn

   Để tiếp tục đọc về quá trình “đám mây hóa” Internet, xem
   :ref:`Góc nhìn: The Cloud is Eating the Internet`.

   Để tìm hiểu thêm về sự trưởng thành của mạng ảo, chúng tôi khuyến nghị:
    * `Network Virtualiztion Revisited <https://systemsapproach.org/2023/10/16/network-virtualization-revisited/>`__,  2023.

    * `Tungsten Fabric <https://tungstenfabric.github.io/website/>`__, 2018.
