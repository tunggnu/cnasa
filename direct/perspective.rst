2.1 Bức tranh Công nghệ
=======================

Trước khi đi sâu vào các thách thức được nêu ra trong phần bài toán ở đầu chương này, sẽ hữu ích nếu chúng ta có một cái nhìn tổng quan về bối cảnh, bao gồm một loạt các công nghệ liên kết khác nhau. Điều này một phần là do hoàn cảnh đa dạng mà người dùng đang cố gắng kết nối thiết bị của họ.

Ở một đầu của phổ, các nhà vận hành mạng xây dựng các mạng toàn cầu phải xử lý các liên kết kéo dài hàng trăm hoặc hàng nghìn kilômét, kết nối các router có kích thước bằng tủ lạnh. Ở đầu kia, một người dùng điển hình chủ yếu tiếp xúc với các liên kết như một cách để kết nối máy tính vào Internet hiện hữu. Đôi khi liên kết này sẽ là một liên kết không dây (Wi-Fi) trong quán cà phê; đôi khi là liên kết Ethernet trong tòa nhà văn phòng hoặc trường đại học; đôi khi là một chiếc điện thoại thông minh kết nối vào mạng di động; với một bộ phận dân số ngày càng lớn, đó là liên kết cáp quang do ISP cung cấp; và nhiều người khác sử dụng một dạng dây đồng hoặc cáp nào đó để kết nối. May mắn thay, có nhiều chiến lược chung được sử dụng trên các loại liên kết tưởng chừng khác biệt này để tất cả chúng đều có thể trở nên tin cậy và hữu ích cho các tầng cao hơn trong ngăn xếp giao thức. Chương này sẽ xem xét các chiến lược đó.

.. _fig-isp-access:
.. figure:: figures/f02-01-9780123850591.png
   :width: 650px
   :align: center

   Góc nhìn của người dùng cuối về Internet.

:numref:`Hình %s <fig-isp-access>` minh họa các loại liên kết khác nhau có thể được tìm thấy trong Internet ngày nay. Ở bên trái, chúng ta thấy nhiều thiết bị người dùng cuối khác nhau từ điện thoại thông minh, máy tính bảng đến máy tính đầy đủ chức năng được kết nối bằng nhiều phương thức khác nhau tới một ISP. Dù các liên kết đó có thể sử dụng công nghệ khác nhau, chúng đều trông giống nhau trong hình này—một đường thẳng nối thiết bị với một router. Có các liên kết kết nối các router với nhau bên trong ISP, cũng như các liên kết kết nối ISP với “phần còn lại của Internet”, bao gồm rất nhiều ISP khác và các host mà họ kết nối tới.

Tất cả các liên kết này trông giống nhau không chỉ vì chúng ta không phải là họa sĩ giỏi mà còn vì một phần vai trò của kiến trúc mạng là cung cấp một trừu tượng chung cho thứ gì đó phức tạp và đa dạng như một liên kết. Ý tưởng là laptop hoặc điện thoại thông minh của bạn không cần quan tâm nó được kết nối với loại liên kết nào—điều duy nhất quan trọng là nó có một liên kết tới Internet. Tương tự, một router không cần quan tâm liên kết nào kết nối nó với các router khác—nó có thể gửi một gói tin lên liên kết với kỳ vọng khá chắc chắn rằng gói tin sẽ đến được đầu bên kia của liên kết.

Làm thế nào để chúng ta khiến tất cả các loại liên kết khác nhau này trông đủ giống nhau đối với người dùng cuối và router? Về cơ bản, chúng ta phải xử lý tất cả các giới hạn vật lý và thiếu sót của các liên kết tồn tại trong thế giới thực. Chúng ta đã phác thảo một số vấn đề này trong phần bài toán mở đầu chương, nhưng trước khi có thể thảo luận về chúng, chúng ta cần giới thiệu một chút vật lý cơ bản. Tất cả các liên kết này đều được tạo thành từ một số vật liệu vật lý có thể truyền tín hiệu, như sóng vô tuyến hoặc các dạng bức xạ điện từ khác, nhưng điều chúng ta thực sự muốn làm là gửi *bit*. Ở các phần sau của chương này, chúng ta sẽ xem xét cách mã hóa bit để truyền trên môi trường vật lý, tiếp theo là các vấn đề khác đã đề cập ở trên. Đến cuối chương này, chúng ta sẽ hiểu cách gửi các gói tin hoàn chỉnh qua hầu như bất kỳ loại liên kết nào, bất kể môi trường vật lý là gì.

Một cách để phân loại các liên kết là dựa trên môi trường mà chúng sử dụng—thường là dây đồng dưới một số dạng, như cặp xoắn (một số loại Ethernet và điện thoại cố định) và cáp đồng trục (cable); sợi quang, được dùng cho cả cáp quang đến tận nhà và nhiều liên kết đường dài trong xương sống Internet; hoặc không khí/không gian tự do cho các liên kết không dây.

Một đặc điểm quan trọng khác của liên kết là *tần số*, đo bằng hertz, với tần số dao động của sóng điện từ. Khoảng cách giữa hai điểm cực đại hoặc cực tiểu liền kề của một sóng, thường đo bằng mét, được gọi là *bước sóng* của sóng đó. Vì tất cả sóng điện từ đều truyền với tốc độ ánh sáng (tốc độ này lại phụ thuộc vào môi trường), tốc độ đó chia cho tần số của sóng sẽ bằng bước sóng của nó. Chúng ta đã thấy ví dụ về đường dây điện thoại truyền giọng nói, mang tín hiệu điện từ liên tục trong dải từ 300 Hz đến 3300 Hz; một sóng 300 Hz truyền qua dây đồng sẽ có bước sóng là

.. centered:: SpeedOfLightInCopper / Frequency

.. centered:: = 2/3 × 3 × 10\ :sup:`8` / 300

.. centered:: = 667 × 10\ :sup:`3` *mét*

Nói chung, sóng điện từ trải dài trên một dải tần số rộng hơn nhiều, từ sóng vô tuyến, đến ánh sáng hồng ngoại, đến ánh sáng nhìn thấy, đến tia X và tia gamma. :numref:`Hình %s <fig-spectrum>` mô tả phổ điện từ và cho thấy các môi trường thường được sử dụng để truyền các dải tần số nào.

.. _fig-spectrum:
.. figure:: figures/f02-02-9780123850591.png
   :width: 600px
   :align: center

   Phổ điện từ.

Điều mà :numref:`Hình %s <fig-spectrum>` không thể hiện là mạng di động nằm ở đâu. Điều này hơi phức tạp vì các dải tần số được cấp phép cho mạng di động khác nhau trên toàn thế giới, và còn phức tạp hơn nữa bởi thực tế là các nhà vận hành mạng thường đồng thời hỗ trợ cả công nghệ cũ/lỗi thời và công nghệ mới/thế hệ tiếp theo, mỗi loại lại chiếm một dải tần số khác nhau. Tóm tắt ở mức cao là các công nghệ di động truyền thống nằm trong khoảng từ 700 MHz đến 2400 MHz, với các dải tần trung mới hiện nay ở 6 GHz, và các dải sóng milimet (mmWave) mở ra trên 24 GHz. Dải mmWave này có khả năng sẽ trở thành một phần quan trọng của mạng di động 5G.

Cho đến giờ, chúng ta hiểu một liên kết là một môi trường vật lý mang tín hiệu dưới dạng sóng điện từ. Các liên kết như vậy cung cấp nền tảng để truyền tải mọi loại thông tin, bao gồm loại dữ liệu mà chúng ta quan tâm—dữ liệu nhị phân (1 và 0). Chúng ta nói rằng dữ liệu nhị phân được *mã hóa* vào tín hiệu. Bài toán mã hóa dữ liệu nhị phân lên tín hiệu điện từ là một chủ đề phức tạp. Để giúp chủ đề này dễ tiếp cận hơn, chúng ta có thể coi nó được chia thành hai lớp. Lớp thấp hơn liên quan đến *điều chế*—thay đổi tần số, biên độ hoặc pha của tín hiệu để truyền thông tin. Một ví dụ đơn giản về điều chế là thay đổi công suất (biên độ) của một bước sóng đơn. Trực quan, điều này tương đương với việc bật tắt một bóng đèn. Vì vấn đề điều chế là thứ yếu trong thảo luận của chúng ta về liên kết như một khối xây dựng cho mạng máy tính, chúng ta chỉ đơn giản giả định rằng có thể truyền hai tín hiệu phân biệt—hãy coi chúng như tín hiệu “cao” và “thấp”—và chúng ta chỉ xét lớp trên, liên quan đến bài toán đơn giản hơn nhiều là mã hóa dữ liệu nhị phân lên hai tín hiệu này. Phần tiếp theo sẽ thảo luận về các kiểu mã hóa như vậy.

Một cách khác để phân loại liên kết là theo cách chúng được sử dụng. Các vấn đề kinh tế và triển khai khác nhau thường ảnh hưởng đến việc các loại liên kết khác nhau xuất hiện ở đâu. Hầu hết người dùng tương tác với Internet hoặc qua các mạng không dây (mà họ gặp ở quán cà phê, sân bay, trường đại học, v.v.) hoặc qua các liên kết “last-mile” (hoặc còn gọi là *mạng truy nhập*) do ISP cung cấp, như minh họa trong :numref:`Hình %s <fig-isp-access>`. Các loại liên kết này được tóm tắt trong :numref:`Bảng %s <tab-home>`. Chúng thường được chọn vì là cách hiệu quả về chi phí để tiếp cận hàng triệu người dùng. Ví dụ, DSL (Digital Subscriber Line) là một công nghệ cũ được triển khai trên các dây đồng cặp xoắn vốn đã có sẵn cho dịch vụ điện thoại truyền thống; G.Fast là một công nghệ dựa trên dây đồng thường dùng trong các tòa nhà căn hộ nhiều hộ, và PON (Passive Optical Network) là một công nghệ mới hơn thường dùng để kết nối nhà và doanh nghiệp qua cáp quang mới triển khai.

.. _tab-home:

.. table::  Các dịch vụ phổ biến cho kết nối last-mile tới nhà bạn.
   :widths: auto
   :align: center

   +-------------------+------------------+
   | Dịch vụ           | Băng thông       |
   +===================+==================+
   | DSL (dây đồng)    | lên tới 100 Mbps |
   +-------------------+------------------+
   | G.Fast (dây đồng) | lên tới 1 Gbps   |
   +-------------------+------------------+
   | PON (quang)       | lên tới 10 Gbps  |
   +-------------------+------------------+

Và tất nhiên còn có mạng *di động* hoặc *cellular* (còn gọi là 4G, nhưng đang nhanh chóng chuyển mình thành 5G) kết nối các thiết bị di động của chúng ta với Internet. Công nghệ này cũng có thể đóng vai trò là kết nối Internet duy nhất vào nhà hoặc văn phòng của chúng ta, nhưng còn có lợi ích bổ sung là cho phép chúng ta duy trì kết nối Internet khi di chuyển từ nơi này sang nơi khác.

Các công nghệ ví dụ này là những lựa chọn phổ biến cho kết nối last-mile tới nhà hoặc doanh nghiệp của bạn, nhưng chúng không đủ để xây dựng một mạng hoàn chỉnh từ đầu. Để làm được điều đó, bạn cũng cần một số liên kết xương sống đường dài để kết nối các thành phố với nhau. Các liên kết xương sống hiện đại ngày nay gần như hoàn toàn là cáp quang, và thường sử dụng công nghệ gọi là SONET (Synchronous Optical Network), vốn được phát triển ban đầu để đáp ứng các yêu cầu quản lý khắt khe của các nhà cung cấp dịch vụ điện thoại.

Cuối cùng, ngoài các liên kết last-mile, xương sống và di động, còn có các liên kết bạn tìm thấy bên trong một tòa nhà hoặc khuôn viên—thường được gọi là *mạng cục bộ* (LAN). Ethernet và người anh em không dây của nó là Wi-Fi là các công nghệ thống trị trong lĩnh vực này.

Bản khảo sát các loại liên kết này chắc chắn chưa đầy đủ, nhưng nó sẽ giúp bạn cảm nhận được sự đa dạng của các loại liên kết đang tồn tại và một số lý do dẫn đến sự đa dạng đó. Trong các phần tiếp theo, chúng ta sẽ thấy các giao thức mạng có thể tận dụng sự đa dạng đó như thế nào và trình bày một cái nhìn nhất quán về mạng cho các tầng cao hơn bất chấp mọi phức tạp ở tầng thấp và các yếu tố kinh tế.
