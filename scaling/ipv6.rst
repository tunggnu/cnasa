4.2 IP Phiên bản 6
==================

Động lực để định nghĩa một phiên bản mới của IP rất đơn giản: để đối phó với việc cạn kiệt không gian địa chỉ IP. CIDR đã giúp đáng kể trong việc kiểm soát tốc độ tiêu thụ không gian địa chỉ Internet và cũng giúp kiểm soát sự tăng trưởng của thông tin bảng định tuyến cần thiết trong các router của Internet. Tuy nhiên, các kỹ thuật này không còn đủ nữa. Đặc biệt, gần như không thể đạt được hiệu quả sử dụng địa chỉ 100%, vì vậy không gian địa chỉ đã bị tiêu thụ hết trước khi host thứ 4 tỷ được kết nối vào Internet. Ngay cả khi chúng ta có thể sử dụng hết 4 tỷ địa chỉ, giờ đây rõ ràng rằng địa chỉ IP cần được gán cho nhiều thiết bị hơn ngoài các máy tính truyền thống, bao gồm điện thoại thông minh, tivi, thiết bị gia dụng và drone. Với cái nhìn rõ ràng của hindsight, không gian địa chỉ 32 bit là khá nhỏ.

4.2.1 Góc nhìn lịch sử
----------------------

IETF bắt đầu xem xét vấn đề mở rộng không gian địa chỉ IP vào năm 1991, và một số phương án đã được đề xuất. Vì địa chỉ IP được mang trong header của mỗi gói IP, việc tăng kích thước địa chỉ đòi hỏi phải thay đổi header gói. Điều này đồng nghĩa với một phiên bản mới của Giao thức Internet và, kéo theo đó, cần phần mềm mới cho mọi host và router trên Internet. Rõ ràng đây không phải là vấn đề nhỏ—nó là một thay đổi lớn cần được cân nhắc rất kỹ lưỡng.

Nỗ lực định nghĩa một phiên bản mới của IP ban đầu được gọi là IP Next Generation, hay IPng. Khi công việc tiến triển, một số phiên bản chính thức đã được gán, vì vậy IPng trở thành IPv6. Lưu ý rằng phiên bản IP được thảo luận cho đến nay trong chương này là phiên bản 4 (IPv4). Sự gián đoạn rõ ràng trong đánh số là do số phiên bản 5 đã được sử dụng cho một giao thức thử nghiệm nhiều năm trước.

Tầm quan trọng của việc chuyển sang một phiên bản IP mới đã gây ra hiệu ứng quả cầu tuyết. Cảm nhận chung của các nhà thiết kế mạng là nếu bạn sẽ thực hiện một thay đổi lớn như vậy thì cũng nên sửa càng nhiều vấn đề khác của IP càng tốt cùng lúc. Do đó, IETF đã kêu gọi các bài báo trắng từ bất kỳ ai quan tâm, yêu cầu đóng góp ý kiến về các tính năng có thể mong muốn trong một phiên bản IP mới. Ngoài nhu cầu hỗ trợ định tuyến và địa chỉ hóa khả mở, một số mục trong danh sách mong muốn cho IPng bao gồm:

-  Hỗ trợ dịch vụ thời gian thực

-  Hỗ trợ bảo mật

-  Tự động cấu hình (tức là khả năng để các host tự động cấu hình các thông tin như địa chỉ IP và tên miền của chính mình)

-  Tăng cường chức năng định tuyến, bao gồm hỗ trợ host di động

Điều thú vị là, mặc dù nhiều tính năng này vắng mặt trong IPv4 vào thời điểm thiết kế IPv6, nhưng hỗ trợ cho tất cả chúng đã xuất hiện trong IPv4 những năm gần đây, thường sử dụng các kỹ thuật tương tự ở cả hai giao thức. Có thể cho rằng việc được tự do coi IPv6 như một tờ giấy trắng đã tạo điều kiện cho việc thiết kế các khả năng mới cho IP, sau đó được bổ sung ngược trở lại vào IPv4.

Ngoài danh sách mong muốn, một tính năng hoàn toàn không thể thương lượng đối với IPv6 là phải có một kế hoạch chuyển đổi từ phiên bản IP hiện tại (phiên bản 4) sang phiên bản mới. Với Internet quá lớn và không có kiểm soát tập trung, hoàn toàn không thể có một “ngày cờ hiệu” mà mọi người đều tắt host và router của mình để cài đặt phiên bản IP mới. Các kiến trúc sư dự kiến một giai đoạn chuyển đổi dài trong đó một số host và router sẽ chỉ chạy IPv4, một số sẽ chạy cả IPv4 và IPv6, và một số sẽ chỉ chạy IPv6. Có lẽ họ không ngờ rằng giai đoạn chuyển đổi này sẽ kéo dài gần 30 năm.

4.2.2 Địa chỉ và Định tuyến
---------------------------

Trước hết, IPv6 cung cấp không gian địa chỉ 128 bit, so với 32 bit của phiên bản 4. Do đó, trong khi phiên bản 4 có thể địa chỉ hóa 4 tỷ nút nếu hiệu quả gán địa chỉ đạt 100%, thì IPv6 có thể địa chỉ hóa 3.4 × 10\ :sup:`38` nút, cũng giả sử hiệu quả 100%. Tuy nhiên, như chúng ta đã thấy, hiệu quả gán địa chỉ 100% là điều khó xảy ra. Một số phân tích về các sơ đồ địa chỉ khác, như mạng điện thoại Pháp và Mỹ, cũng như IPv4, đã đưa ra một số con số thực nghiệm về hiệu quả gán địa chỉ. Dựa trên các ước tính bi quan nhất từ nghiên cứu này, không gian địa chỉ IPv6 được dự đoán sẽ cung cấp hơn 1500 địa chỉ cho mỗi foot vuông bề mặt Trái Đất, điều này chắc chắn là đủ ngay cả khi các lò nướng bánh trên Sao Kim cũng có địa chỉ IP.

Phân bổ không gian địa chỉ
~~~~~~~~~~~~~~~~~~~~~~~~~~

Dựa trên hiệu quả của CIDR trong IPv4, địa chỉ IPv6 cũng không phân lớp, nhưng không gian địa chỉ vẫn được chia nhỏ theo các bit đầu. Thay vì chỉ định các lớp địa chỉ khác nhau, các bit đầu chỉ định các mục đích sử dụng khác nhau của địa chỉ IPv6. Phân bổ prefix hiện tại được liệt kê trong :numref:`Bảng %s <tab-v6tab>`.

.. _tab-v6tab:
.. table:: Phân bổ prefix địa chỉ cho IPv6.
   :align: center
   :widths: auto

   +-----------------+---------------------+
   | Prefix          | Sử dụng             |
   +=================+=====================+
   | 00…0 (128 bit)  | Không xác định      |
   +-----------------+---------------------+
   | 00…1 (128 bit)  | Loopback            |
   +-----------------+---------------------+
   | 1111 1111       | Địa chỉ multicast   |
   +-----------------+---------------------+
   | 1111 1110 10    | Link-local unicast  |
   +-----------------+---------------------+
   | Các giá trị khác| Global Unicast      |
   +-----------------+---------------------+

Việc phân bổ không gian địa chỉ này cần được bàn thêm một chút. Đầu tiên, toàn bộ chức năng của ba lớp địa chỉ chính của IPv4 (A, B, và C) nằm trong dải “các giá trị khác”. Địa chỉ Global Unicast, như chúng ta sẽ thấy ngay sau đây, rất giống với địa chỉ IPv4 không phân lớp, chỉ là dài hơn nhiều. Đây là loại địa chỉ quan trọng nhất hiện tại, chiếm hơn 99% tổng không gian địa chỉ IPv6. (Tại thời điểm viết sách, địa chỉ unicast IPv6 đang được phân bổ từ khối bắt đầu bằng ``001``, phần còn lại của không gian địa chỉ—khoảng 87%—được dành cho sử dụng trong tương lai.)

Không gian địa chỉ multicast (rõ ràng) dành cho multicast, đóng vai trò tương tự như địa chỉ lớp D trong IPv4. Lưu ý rằng địa chỉ multicast rất dễ nhận biết—chúng bắt đầu bằng một byte toàn bit 1. Chúng ta sẽ xem cách sử dụng các địa chỉ này ở phần sau.

Ý tưởng đằng sau địa chỉ link-local là cho phép một host tự tạo ra một địa chỉ có thể hoạt động trên mạng mà nó kết nối mà không cần quan tâm đến tính duy nhất toàn cầu của địa chỉ. Điều này hữu ích cho tự động cấu hình, như sẽ thấy bên dưới. Tương tự, địa chỉ site-local nhằm cho phép tạo ra các địa chỉ hợp lệ trong một site (ví dụ, mạng nội bộ doanh nghiệp) không kết nối với Internet lớn hơn; một lần nữa, tính duy nhất toàn cầu không phải là vấn đề.

Trong không gian địa chỉ global unicast có một số loại địa chỉ đặc biệt quan trọng. Một nút có thể được gán một địa chỉ IPv6 tương thích IPv4 bằng cách mở rộng địa chỉ IPv4 32 bit thành 128 bit bằng các số 0. Một nút chỉ hiểu IPv4 có thể được gán một địa chỉ IPv6 ánh xạ IPv4 bằng cách thêm vào trước địa chỉ IPv4 2 byte toàn bit 1 rồi mở rộng bằng số 0 cho đủ 128 bit. Hai loại địa chỉ đặc biệt này có vai trò trong quá trình chuyển đổi từ IPv4 sang IPv6 (xem khung bên về chủ đề này).

Ký hiệu địa chỉ
~~~~~~~~~~~~~~~

Cũng như IPv4, có một số ký hiệu đặc biệt để ghi địa chỉ IPv6. Biểu diễn chuẩn là ``x:x:x:x:x:x:x:x``, trong đó mỗi ``x`` là biểu diễn thập lục phân của một đoạn 16 bit của địa chỉ. Một ví dụ là

::

   47CD:1234:4422:AC02:0022:1234:A456:0124

Bất kỳ địa chỉ IPv6 nào cũng có thể được viết theo ký hiệu này. Vì có một số loại địa chỉ IPv6 đặc biệt, nên có một số ký hiệu đặc biệt hữu ích trong một số trường hợp. Ví dụ, một địa chỉ có nhiều số 0 liên tiếp có thể được viết ngắn gọn hơn bằng cách bỏ qua tất cả các trường 0. Như vậy,

::

   47CD:0000:0000:0000:0000:0000:A456:0124

có thể được viết là

::

   47CD::A456:0124

Rõ ràng, dạng viết tắt này chỉ có thể dùng cho một dãy số 0 liên tiếp trong một địa chỉ để tránh mơ hồ.

Hai loại địa chỉ IPv6 có chứa địa chỉ IPv4 nhúng có ký hiệu riêng giúp việc trích xuất địa chỉ IPv4 dễ dàng hơn. Ví dụ, địa chỉ IPv6 ánh xạ IPv4 của một host có địa chỉ IPv4 là 128.96.33.81 có thể được viết là

::

   ::FFFF:128.96.33.81

Tức là, 32 bit cuối được viết theo ký hiệu IPv4, thay vì là hai số thập lục phân phân tách bằng dấu hai chấm. Lưu ý rằng dấu hai chấm kép ở đầu chỉ các số 0 ở đầu.

Địa chỉ Global Unicast
~~~~~~~~~~~~~~~~~~~~~~

Loại địa chỉ quan trọng nhất mà IPv6 phải cung cấp là địa chỉ unicast thông thường. Nó phải làm điều này theo cách hỗ trợ tốc độ bổ sung host mới vào Internet nhanh chóng và cho phép định tuyến được thực hiện một cách khả mở khi số lượng mạng vật lý trong Internet tăng lên. Do đó, trọng tâm của IPv6 là kế hoạch phân bổ địa chỉ unicast xác định cách địa chỉ unicast sẽ được gán cho các nhà cung cấp dịch vụ, hệ tự trị, mạng, host và router.

Thực tế, kế hoạch phân bổ địa chỉ được đề xuất cho địa chỉ unicast IPv6 cực kỳ giống với kế hoạch đang được triển khai với CIDR trong IPv4. Để hiểu cách nó hoạt động và cung cấp khả năng mở rộng, sẽ hữu ích khi định nghĩa một số thuật ngữ mới. Ta có thể coi một AS không chuyển tiếp (tức là stub hoặc multihomed AS) là một *subscriber* (người đăng ký), và một AS chuyển tiếp là một *provider* (nhà cung cấp). Hơn nữa, ta có thể chia nhỏ nhà cung cấp thành *trực tiếp* và *gián tiếp*. Loại đầu kết nối trực tiếp với người đăng ký. Loại sau chủ yếu kết nối các nhà cung cấp khác, không kết nối trực tiếp với người đăng ký, và thường được gọi là *mạng backbone*.

Với tập định nghĩa này, ta thấy Internet không chỉ là một tập hợp các hệ tự trị kết nối tùy ý; nó có một số phân cấp nội tại. Khó khăn nằm ở việc tận dụng phân cấp này mà không phát minh ra các cơ chế sẽ thất bại khi phân cấp không được tuân thủ nghiêm ngặt, như đã xảy ra với EGP. Ví dụ, sự phân biệt giữa nhà cung cấp trực tiếp và gián tiếp trở nên mờ nhạt khi một người đăng ký kết nối với backbone hoặc khi một nhà cung cấp trực tiếp bắt đầu kết nối với nhiều nhà cung cấp khác.

Cũng như CIDR, mục tiêu của kế hoạch phân bổ địa chỉ IPv6 là cung cấp gom nhóm thông tin định tuyến để giảm gánh nặng cho các router nội miền. Ý tưởng then chốt một lần nữa là sử dụng một prefix địa chỉ—một tập các bit liên tiếp ở đầu địa chỉ—để gom nhóm thông tin khả năng tiếp cận cho một số lượng lớn mạng và thậm chí nhiều hệ tự trị. Cách chính để đạt được điều này là gán một prefix địa chỉ cho một nhà cung cấp trực tiếp, sau đó nhà cung cấp đó sẽ gán các prefix dài hơn bắt đầu bằng prefix đó cho các người đăng ký của mình. Như vậy, một nhà cung cấp có thể quảng bá một prefix duy nhất cho tất cả người đăng ký của mình.

Tất nhiên, nhược điểm là nếu một site quyết định đổi nhà cung cấp, nó sẽ cần lấy một prefix địa chỉ mới và đánh số lại tất cả các nút trong site. Điều này có thể là một công việc khổng lồ, đủ để ngăn hầu hết mọi người đổi nhà cung cấp. Vì lý do này, vẫn đang có nghiên cứu về các sơ đồ địa chỉ khác, như địa chỉ hóa theo địa lý, trong đó địa chỉ của một site là hàm của vị trí thay vì nhà cung cấp mà nó kết nối. Tuy nhiên, hiện tại, địa chỉ hóa dựa trên nhà cung cấp là cần thiết để định tuyến hoạt động hiệu quả.

Lưu ý rằng mặc dù việc gán địa chỉ IPv6 về cơ bản tương đương với cách gán địa chỉ trong IPv4 kể từ khi có CIDR, IPv6 có lợi thế lớn là không có một cơ sở địa chỉ đã gán lớn phải tích hợp vào kế hoạch của nó.

Một câu hỏi là liệu có hợp lý không khi gom nhóm phân cấp ở các mức khác trong phân cấp. Ví dụ, liệu tất cả các nhà cung cấp nên lấy prefix địa chỉ của mình từ trong một prefix được cấp cho backbone mà họ kết nối không? Vì hầu hết các nhà cung cấp kết nối với nhiều backbone, điều này có lẽ không hợp lý. Ngoài ra, vì số lượng nhà cung cấp nhỏ hơn nhiều so với số lượng site, lợi ích của việc gom nhóm ở mức này cũng ít hơn.

Một nơi mà gom nhóm có thể hợp lý là ở cấp quốc gia hoặc lục địa. Ranh giới lục địa tạo thành các phân chia tự nhiên trong cấu trúc liên kết Internet. Nếu tất cả các địa chỉ ở châu Âu, ví dụ, có một prefix chung, thì có thể gom nhóm rất nhiều, và hầu hết các router ở các lục địa khác chỉ cần một mục bảng định tuyến cho tất cả các mạng có prefix châu Âu. Các nhà cung cấp ở châu Âu sẽ chọn prefix của mình sao cho bắt đầu bằng prefix châu Âu. Theo sơ đồ này, một địa chỉ IPv6 có thể trông như :numref:`Hình %s <fig-v6addr>`. ``RegistryID`` có thể là một định danh được gán cho một registry địa chỉ châu Âu, với các ID khác được gán cho các lục địa hoặc quốc gia khác. Lưu ý rằng prefix sẽ có độ dài khác nhau trong kịch bản này. Ví dụ, một nhà cung cấp có ít khách hàng có thể có prefix dài hơn (và do đó ít không gian địa chỉ hơn) so với nhà cung cấp có nhiều khách hàng.

.. _fig-v6addr:
.. figure:: figures/f04-11-9780123850591.png
   :width: 500px
   :align: center

   Một địa chỉ unicast dựa trên nhà cung cấp của IPv6.

Một tình huống khó có thể xảy ra khi một người đăng ký kết nối với nhiều nhà cung cấp. Người đăng ký nên dùng prefix nào cho site của mình? Không có giải pháp hoàn hảo cho vấn đề này. Ví dụ, giả sử một người đăng ký kết nối với hai nhà cung cấp, X và Y. Nếu người đăng ký lấy prefix từ X, thì Y phải quảng bá một prefix không liên quan gì đến các người đăng ký khác của mình và do đó không thể gom nhóm. Nếu người đăng ký đánh số một phần AS của mình với prefix của X và một phần với prefix của Y, anh ta có nguy cơ một nửa site của mình không thể truy cập được nếu kết nối với một nhà cung cấp bị mất. Một giải pháp khá hiệu quả nếu X và Y có nhiều người đăng ký chung là họ có ba prefix giữa họ: một cho người đăng ký chỉ của X, một cho người đăng ký chỉ của Y, và một cho các site là người đăng ký của cả X và Y.

4.2.3 Định dạng gói
-------------------

Mặc dù IPv6 mở rộng IPv4 theo nhiều cách, định dạng header của nó thực ra đơn giản hơn. Sự đơn giản này là kết quả của nỗ lực loại bỏ các chức năng không cần thiết khỏi giao thức. :numref:`Hình %s <fig-v6header>` cho thấy kết quả.

Như nhiều header khác, header này bắt đầu với trường ``Version``, được đặt là 6 cho IPv6. Trường ``Version`` nằm ở cùng vị trí so với đầu header như trường ``Version`` của IPv4 để phần mềm xử lý header có thể ngay lập tức quyết định định dạng header nào cần tìm. Các trường ``TrafficClass`` và ``FlowLabel`` đều liên quan đến các vấn đề chất lượng dịch vụ.

Trường ``PayloadLen`` cho biết độ dài của gói, không tính header IPv6, tính bằng byte. Trường ``NextHeader`` thay thế thông minh cả trường tùy chọn IP và trường ``Protocol`` của IPv4. Nếu cần tùy chọn, chúng sẽ được mang trong một hoặc nhiều header đặc biệt sau header IP, và điều này được chỉ ra bởi giá trị của trường ``NextHeader``. Nếu không có header đặc biệt nào, trường ``NextHeader`` là khóa demux xác định giao thức tầng cao hơn chạy trên IP (ví dụ, TCP hoặc UDP); tức là, nó đóng vai trò giống như trường ``Protocol`` của IPv4. Ngoài ra, phân mảnh giờ được xử lý như một header tùy chọn, nghĩa là các trường liên quan đến phân mảnh của IPv4 không còn trong header IPv6. Trường ``HopLimit`` đơn giản là ``TTL`` của IPv4, được đổi tên để phản ánh cách nó thực sự được sử dụng.

.. _fig-v6header:
.. figure:: figures/f04-12-9780123850591.png
   :width: 500px
   :align: center

   Header gói IPv6.

Cuối cùng, phần lớn của header được chiếm bởi địa chỉ nguồn và đích, mỗi địa chỉ dài 16 byte (128 bit). Như vậy, header IPv6 luôn dài 40 byte. Xét rằng địa chỉ IPv6 dài gấp bốn lần địa chỉ IPv4, điều này so sánh khá tốt với header IPv4, vốn dài 20 byte nếu không có tùy chọn.

Cách IPv6 xử lý tùy chọn là một cải tiến lớn so với IPv4. Trong IPv4, nếu có bất kỳ tùy chọn nào, mọi router đều phải phân tích toàn bộ trường tùy chọn để xem có tùy chọn nào liên quan không. Điều này là do các tùy chọn đều nằm ở cuối header IP, như một tập hợp không có thứ tự các bộ ‘(type, length, value)’. Ngược lại, IPv6 coi tùy chọn là *header mở rộng* mà nếu có, phải xuất hiện theo thứ tự xác định. Điều này nghĩa là mỗi router có thể nhanh chóng xác định xem có tùy chọn nào liên quan đến nó không; trong hầu hết các trường hợp là không. Thường chỉ cần nhìn vào trường ``NextHeader`` là đủ. Kết quả là xử lý tùy chọn hiệu quả hơn nhiều trong IPv6, điều này rất quan trọng đối với hiệu năng router. Ngoài ra, định dạng mới của tùy chọn dưới dạng header mở rộng nghĩa là chúng có thể có độ dài tùy ý, trong khi ở IPv4 bị giới hạn tối đa 44 byte. Chúng ta sẽ xem một số tùy chọn được sử dụng như thế nào ở phần dưới.

.. _fig-v6ext:
.. figure:: figures/f04-13-9780123850591.png
   :width: 400px
   :align: center

   Header mở rộng phân mảnh của IPv6.

Mỗi tùy chọn có loại header mở rộng riêng. Loại của mỗi header mở rộng được xác định bởi giá trị của trường ``NextHeader`` trong header đứng trước nó, và mỗi header mở rộng chứa một trường ``NextHeader`` để xác định header tiếp theo. Header mở rộng cuối cùng sẽ được theo sau bởi một header tầng vận chuyển (ví dụ, TCP) và trong trường hợp này giá trị của trường ``NextHeader`` giống như giá trị của trường ``Protocol`` trong header IPv4. Như vậy, trường ``NextHeader`` có hai vai trò; nó có thể xác định loại header mở rộng tiếp theo, hoặc, trong header mở rộng cuối cùng, nó đóng vai trò là khóa demux xác định giao thức tầng cao hơn chạy trên IPv6.

Xét ví dụ về header phân mảnh, thể hiện trong :numref:`Hình %s <fig-v6ext>`. Header này cung cấp chức năng tương tự các trường phân mảnh trong header IPv4, nhưng chỉ xuất hiện khi cần phân mảnh. Giả sử đây là header mở rộng duy nhất, thì trường ``NextHeader`` của header IPv6 sẽ chứa giá trị ``44``, là giá trị chỉ định header phân mảnh. Trường ``NextHeader`` của chính header phân mảnh chứa giá trị mô tả header tiếp theo. Một lần nữa, giả sử không có header mở rộng nào khác, thì header tiếp theo có thể là header TCP, dẫn đến ``NextHeader`` chứa giá trị ``6``, giống như trường ``Protocol`` trong IPv4. Nếu header phân mảnh được theo sau bởi, ví dụ, một header xác thực, thì trường ``NextHeader`` của header phân mảnh sẽ chứa giá trị ``51``.

4.2.4 Các khả năng nâng cao
---------------------------

Như đã đề cập ở đầu mục này, động lực chính đằng sau sự phát triển của IPv6 là để hỗ trợ sự phát triển liên tục của Internet. Tuy nhiên, một khi header IP phải thay đổi vì địa chỉ, thì cánh cửa mở ra cho nhiều thay đổi khác, hai trong số đó được mô tả dưới đây. Nhưng IPv6 còn bao gồm nhiều tính năng bổ sung, hầu hết được đề cập ở nơi khác trong sách này; ví dụ, di động, bảo mật, chất lượng dịch vụ. Điều thú vị là, ở hầu hết các lĩnh vực này, khả năng của IPv4 và IPv6 gần như không phân biệt được, nên động lực chính cho IPv6 vẫn là nhu cầu về địa chỉ lớn hơn.

Tự động cấu hình
~~~~~~~~~~~~~~~~

Mặc dù sự phát triển của Internet rất ấn tượng, một yếu tố đã cản trở việc chấp nhận công nghệ nhanh hơn là thực tế việc kết nối Internet thường đòi hỏi khá nhiều kiến thức quản trị hệ thống. Đặc biệt, mỗi host kết nối Internet cần được cấu hình với một lượng thông tin tối thiểu, như địa chỉ IP hợp lệ, subnet mask cho liên kết mà nó kết nối, và địa chỉ máy chủ tên miền. Do đó, không thể chỉ đơn giản mở hộp một máy tính mới và kết nối vào Internet mà không có cấu hình trước. Một mục tiêu của IPv6, do đó, là cung cấp hỗ trợ tự động cấu hình, đôi khi gọi là *plug-and-play*.

Như đã thấy ở chương trước, tự động cấu hình là khả thi với IPv4, nhưng nó phụ thuộc vào sự tồn tại của một máy chủ được cấu hình để cấp phát địa chỉ và thông tin cấu hình khác cho các client DHCP. Định dạng địa chỉ dài hơn trong IPv6 giúp cung cấp một dạng tự động cấu hình mới hữu ích gọi là *stateless autoconfiguration*, không cần máy chủ.

Nhớ lại rằng địa chỉ unicast IPv6 là phân cấp, và phần ít quan trọng nhất là interface ID. Do đó, ta có thể chia nhỏ bài toán tự động cấu hình thành hai phần:

1. Lấy một interface ID duy nhất trên liên kết mà host kết nối.

2. Lấy đúng prefix địa chỉ cho subnet này.

Phần đầu hóa ra khá dễ, vì mọi host trên một liên kết đều phải có địa chỉ tầng liên kết duy nhất. Ví dụ, tất cả các host trên Ethernet đều có địa chỉ Ethernet 48 bit duy nhất. Địa chỉ này có thể được chuyển thành một địa chỉ link-local hợp lệ bằng cách thêm prefix phù hợp từ :numref:`Bảng %s <tab-v6tab>` (``1111 1110 10``) rồi thêm đủ số 0 cho đủ 128 bit. Với một số thiết bị—ví dụ, máy in hoặc host trên mạng nhỏ không có router, không kết nối mạng khác—địa chỉ này có thể là đủ. Những thiết bị cần địa chỉ hợp lệ toàn cầu phụ thuộc vào một router trên cùng liên kết định kỳ quảng bá prefix phù hợp cho liên kết. Rõ ràng, điều này yêu cầu router được cấu hình với prefix địa chỉ đúng, và prefix này được chọn sao cho còn đủ không gian ở cuối (ví dụ, 48 bit) để gắn địa chỉ tầng liên kết phù hợp.

Khả năng nhúng địa chỉ tầng liên kết dài tới 48 bit vào địa chỉ IPv6 là một trong những lý do chọn kích thước địa chỉ lớn như vậy. Không chỉ 128 bit cho phép nhúng, mà còn dư nhiều không gian cho phân cấp địa chỉ nhiều tầng như đã bàn ở trên.

Định tuyến do nguồn chỉ định
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một trong các header mở rộng của IPv6 là header định tuyến. Khi không có header này, định tuyến cho IPv6 rất giống với IPv4 dưới CIDR. Header định tuyến chứa một danh sách các địa chỉ IPv6 đại diện cho các nút hoặc vùng topo mà gói tin nên đi qua trên đường đến đích. Một vùng topo có thể là, ví dụ, mạng của một nhà cung cấp backbone. Chỉ định rằng các gói phải đi qua mạng này là một cách để hiện thực lựa chọn nhà cung cấp trên từng gói. Như vậy, một host có thể chỉ định muốn một số gói đi qua nhà cung cấp rẻ, số khác qua nhà cung cấp có độ tin cậy cao, số khác nữa qua nhà cung cấp mà host tin tưởng về bảo mật.

Để cung cấp khả năng chỉ định thực thể topo thay vì từng nút riêng lẻ, IPv6 định nghĩa một địa chỉ *anycast*. Địa chỉ anycast được gán cho một tập các interface, và các gói gửi đến địa chỉ đó sẽ đến “gần nhất” trong số các interface đó, với “gần nhất” được xác định bởi các giao thức định tuyến. Ví dụ, tất cả các router của một nhà cung cấp backbone có thể được gán một địa chỉ anycast duy nhất, dùng trong header định tuyến.
