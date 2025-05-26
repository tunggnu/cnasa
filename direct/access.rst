2.8 Mạng Truy Cập
==================

Bên cạnh các kết nối Ethernet và Wi-Fi mà chúng ta thường sử dụng để kết nối Internet tại nhà, nơi làm việc, trường học và nhiều không gian công cộng, hầu hết chúng ta kết nối Internet thông qua một dịch vụ *truy cập* hoặc *băng thông rộng* mà chúng ta mua từ một ISP. Phần này mô tả hai công nghệ như vậy: *Mạng Quang Thụ Động* (PON), thường được gọi là cáp quang đến tận nhà, và *Mạng Di Động* kết nối các thiết bị di động của chúng ta. Trong cả hai trường hợp, các mạng này đều là đa truy cập (giống như Ethernet và Wi-Fi), nhưng như chúng ta sẽ thấy, cách tiếp cận điều phối truy cập của chúng khá khác biệt.

Để làm rõ hơn bối cảnh, các ISP (ví dụ, công ty viễn thông hoặc truyền hình cáp) thường vận hành một mạng trục quốc gia, và kết nối với rìa của mạng trục đó là hàng trăm hoặc hàng nghìn điểm biên, mỗi điểm phục vụ một thành phố hoặc khu vực lân cận. Các điểm biên này thường được gọi là *Trung Tâm* trong lĩnh vực viễn thông và *Head End* trong lĩnh vực truyền hình cáp, nhưng mặc dù tên gọi gợi ý “trung tâm” và “gốc của hệ thống phân cấp”, các điểm này thực chất nằm ở rìa mạng của ISP; phía ISP của “dặm cuối” kết nối trực tiếp với khách hàng. Các mạng truy cập PON và Di Động đều được neo tại các cơ sở này.\ [#]_

.. [#] DSL là công nghệ dựa trên đồng truyền thống tương ứng với PON. Các liên kết DSL cũng được kết thúc tại các Trung Tâm Viễn Thông, nhưng chúng tôi không mô tả công nghệ này vì nó đang bị loại bỏ dần.

2.8.1 Mạng Quang Thụ Động
-----------------------------

PON là công nghệ phổ biến nhất được sử dụng để cung cấp băng thông rộng dựa trên cáp quang tới các hộ gia đình và doanh nghiệp. PON áp dụng thiết kế điểm-đa điểm, nghĩa là mạng được cấu trúc dạng cây, với một điểm bắt đầu từ mạng của ISP rồi tỏa ra để phục vụ tối đa 1024 hộ gia đình. PON có tên gọi như vậy vì các bộ chia tín hiệu là thụ động: chúng chuyển tiếp tín hiệu quang xuống và lên mà không chủ động lưu trữ và chuyển tiếp khung dữ liệu. Theo cách này, chúng là biến thể quang học của các repeater được sử dụng trong Ethernet cổ điển. Việc đóng khung được thực hiện tại nguồn trong cơ sở của ISP, trong một thiết bị gọi là *Thiết Bị Đầu Cuối Tuyến Quang* (OLT), và tại các điểm cuối trong từng hộ gia đình, trong một thiết bị gọi là *Thiết Bị Mạng Quang* (ONU).

:numref:`Hình %s <fig-pon>` minh họa một ví dụ về PON, đơn giản hóa chỉ còn một ONU và một OLT. Trên thực tế, một Trung Tâm sẽ có nhiều OLT kết nối tới hàng nghìn hộ gia đình khách hàng. Để đầy đủ, :numref:`Hình %s <fig-pon>` cũng bao gồm hai chi tiết khác về cách PON được kết nối với mạng trục của ISP (và do đó, với phần còn lại của Internet). *Agg Switch* tổng hợp lưu lượng từ một tập hợp các OLT, và *BNG* (Broadband Network Gateway) là một thiết bị viễn thông thực hiện nhiều chức năng, trong đó có đo lường lưu lượng Internet để phục vụ mục đích tính cước. Đúng như tên gọi, BNG thực chất là cổng nối giữa mạng truy cập (mọi thứ bên trái BNG) và Internet (mọi thứ bên phải BNG).

.. _fig-pon:
.. figure:: figures/access/Slide1.png
   :width: 600px
   :align: center

   Một ví dụ về PON kết nối các OLT tại Trung Tâm với các ONU tại nhà và doanh nghiệp.

Vì các bộ chia là thụ động, PON phải triển khai một dạng giao thức đa truy cập nào đó. Cách tiếp cận của nó có thể tóm tắt như sau. Đầu tiên, lưu lượng lên và xuống được truyền trên hai bước sóng quang khác nhau, nên chúng hoàn toàn độc lập với nhau. Lưu lượng xuống bắt đầu từ OLT và tín hiệu được truyền xuống mọi liên kết trong PON. Do đó, mọi khung đều đến được mọi ONU. Thiết bị này sau đó kiểm tra một định danh duy nhất trong từng khung gửi trên bước sóng, và hoặc giữ lại khung (nếu định danh dành cho nó) hoặc loại bỏ (nếu không phải). Mã hóa được sử dụng để ngăn các ONU nghe lén lưu lượng của hàng xóm.

Lưu lượng lên sau đó được ghép kênh phân chia theo thời gian trên bước sóng lên, với mỗi ONU định kỳ được cấp lượt truyền. Vì các ONU phân bố trên một khu vực khá rộng (tính bằng kilômét) và ở các khoảng cách khác nhau tới OLT, nên không thực tế để chúng truyền dựa trên đồng hồ đồng bộ, như trong SONET. Thay vào đó, OLT truyền *grant* cho từng ONU, cấp cho chúng một khoảng thời gian để truyền. Nói cách khác, OLT duy nhất chịu trách nhiệm thực hiện chia sẻ vòng tròn trung tâm của PON. Điều này bao gồm khả năng OLT có thể cấp cho mỗi ONU một phần thời gian khác nhau, thực hiện các mức dịch vụ khác nhau.

PON giống với Ethernet ở chỗ nó định nghĩa một thuật toán chia sẻ đã phát triển theo thời gian để đáp ứng băng thông ngày càng cao. G-PON (Gigabit-PON) là loại được triển khai rộng rãi nhất hiện nay, hỗ trợ băng thông 2,25-Gbps. XGS-PON (10 Gigabit-PON) hiện đang bắt đầu được triển khai.

2.8.2 Mạng Di Động
----------------------

Mặc dù công nghệ điện thoại di động có nguồn gốc từ truyền thoại analog, các dịch vụ dữ liệu dựa trên chuẩn di động giờ đây đã trở thành tiêu chuẩn. Giống như Wi-Fi, mạng di động truyền dữ liệu ở các băng thông nhất định trong phổ radio. Không giống như Wi-Fi, cho phép bất kỳ ai cũng có thể sử dụng một kênh ở 2,4 hoặc 5 GHz (bạn chỉ cần thiết lập một trạm gốc, như nhiều người làm ở nhà), việc sử dụng độc quyền các băng tần khác nhau đã được đấu giá và cấp phép cho các nhà cung cấp dịch vụ, những người sau đó bán dịch vụ truy cập di động cho thuê bao của họ.

Các băng tần được sử dụng cho mạng di động khác nhau trên thế giới, và phức tạp bởi thực tế là các ISP thường đồng thời hỗ trợ cả công nghệ cũ/lỗi thời và công nghệ mới/thế hệ tiếp theo, mỗi loại chiếm một băng tần khác nhau. Tóm tắt ở mức cao là các công nghệ di động truyền thống sử dụng băng tần từ 700-MHz đến 2400-MHz, với các phân bổ phổ trung mới hiện nay ở 6-GHz và các phân bổ sóng milimet (mmWave) mở ra trên 24-GHz.

.. sidebar:: Dịch vụ Radio Băng Thông Công Dân (CBRS)

   Ngoài các băng tần được cấp phép, còn có một băng tần không cần giấy phép ở 3,5-GHz được dành riêng ở Bắc Mỹ, gọi là *Citizens Broadband Radio Service* (CBRS), mà bất kỳ ai có radio di động đều có thể sử dụng. Các băng tần không cần giấy phép tương tự cũng đang được thiết lập ở các quốc gia khác. Điều này mở ra cơ hội thiết lập các mạng di động riêng, ví dụ, trong khuôn viên trường đại học, doanh nghiệp hoặc nhà máy.

   Cụ thể hơn, băng tần CBRS cho phép ba tầng người dùng chia sẻ phổ: quyền sử dụng đầu tiên thuộc về chủ sở hữu gốc của phổ này, radar hải quân và trạm mặt đất vệ tinh; tiếp theo là người dùng ưu tiên nhận quyền này trên các băng tần 10MHz trong ba năm thông qua đấu giá khu vực; và cuối cùng là phần còn lại của dân số, những người có thể truy cập và sử dụng một phần băng tần này miễn là họ kiểm tra trước với cơ sở dữ liệu trung tâm của người dùng đã đăng ký.

Giống như 802.11, công nghệ di động dựa vào việc sử dụng các trạm gốc được kết nối với mạng có dây. Trong trường hợp mạng di động, các trạm gốc thường được gọi là *Broadband Base Units* (BBU), các thiết bị di động kết nối với chúng thường được gọi là *Thiết Bị Người Dùng* (UE), và tập hợp các BBU được neo tại một *Evolved Packet Core* (EPC) đặt tại Trung Tâm. Mạng không dây do EPC phục vụ thường được gọi là *Mạng Truy Cập Radio* (RAN).

BBU còn có tên gọi chính thức khác—Evolved NodeB, thường viết tắt là eNodeB hoặc eNB—trong đó NodeB là tên gọi của đơn vị radio trong các thế hệ mạng di động trước (và đã phát triển kể từ đó). Vì thế giới di động tiếp tục phát triển nhanh chóng và các eNB sắp được nâng cấp lên gNB, chúng tôi quyết định sử dụng thuật ngữ BBU chung và ít gây nhầm lẫn hơn.

:numref:`Hình %s <fig-ran>` minh họa một cấu hình khả dĩ của kịch bản đầu-cuối, với một số chi tiết bổ sung. EPC có nhiều thành phần con, bao gồm MME (Mobility Management Entity), HSS (Home Subscriber Server), và cặp S/PGW (Session/Packet Gateway); thành phần đầu tiên theo dõi và quản lý sự di chuyển của UE trong toàn RAN, thành phần thứ hai là cơ sở dữ liệu chứa thông tin liên quan đến thuê bao, và cặp Gateway xử lý và chuyển tiếp các gói giữa RAN và Internet (nó tạo thành *user plane* của EPC). Chúng tôi nói “một cấu hình khả dĩ” vì các chuẩn di động cho phép sự đa dạng lớn về số lượng S/PGW mà một MME quản lý, cho phép một MME duy nhất quản lý di động trên một khu vực địa lý rộng được phục vụ bởi nhiều Trung Tâm. Cuối cùng, mặc dù không được minh họa rõ trong :numref:`Hình %s <fig-ran>`, đôi khi mạng PON của ISP được sử dụng để kết nối các BBU từ xa về Trung Tâm.

.. _fig-ran:
.. figure:: figures/access/Slide2.png
   :width: 600px
   :align: center

   Một Mạng Truy Cập Radio (RAN) kết nối một tập thiết bị di động (UE) tới Evolved Packet Core (EPC) đặt tại Trung Tâm.

Khu vực địa lý được phục vụ bởi ăng-ten của một BBU gọi là *cell* (ô). Một BBU có thể phục vụ một cell duy nhất hoặc sử dụng nhiều ăng-ten định hướng để phục vụ nhiều cell. Các cell không có ranh giới rõ ràng và chúng chồng lấn lên nhau. Ở vùng chồng lấn, một UE có thể giao tiếp với nhiều BBU. Tuy nhiên, tại bất kỳ thời điểm nào, UE chỉ giao tiếp và chịu sự điều khiển của một BBU duy nhất. Khi thiết bị bắt đầu rời khỏi một cell, nó di chuyển vào vùng chồng lấn với một hoặc nhiều cell khác. BBU hiện tại cảm nhận tín hiệu từ điện thoại yếu dần và chuyển quyền điều khiển thiết bị cho trạm gốc nào nhận được tín hiệu mạnh nhất từ nó. Nếu thiết bị đang tham gia cuộc gọi hoặc phiên mạng tại thời điểm đó, phiên này phải được chuyển sang trạm gốc mới trong quá trình gọi là *handoff*. Quá trình ra quyết định cho handoff thuộc phạm vi quản lý của MME, vốn trước đây là phần mềm độc quyền của các nhà cung cấp thiết bị di động (mặc dù hiện nay đã bắt đầu xuất hiện các triển khai MME mã nguồn mở).

Đã có nhiều thế hệ giao thức triển khai mạng di động, thường gọi là 1G, 2G, 3G, v.v. Hai thế hệ đầu chỉ hỗ trợ thoại, với 3G đánh dấu chuyển đổi sang truy cập băng thông rộng, hỗ trợ tốc độ dữ liệu hàng trăm kilobit/giây. Ngày nay, ngành công nghiệp đang ở 4G (hỗ trợ tốc độ dữ liệu thường tính bằng vài megabit/giây) và đang chuyển sang 5G (hứa hẹn tăng tốc độ dữ liệu lên gấp mười lần).

Từ 3G trở đi, tên gọi thế hệ thực chất tương ứng với một chuẩn do 3GPP (3rd Generation Partnership Project) định nghĩa. Dù tên có “3G”, 3GPP tiếp tục định nghĩa chuẩn cho cả 4G và 5G, mỗi chuẩn tương ứng với một bản phát hành (release) của tiêu chuẩn. Release 15, hiện đã được công bố, được coi là ranh giới giữa 4G và 5G. Một tên gọi khác cho chuỗi các bản phát hành và thế hệ này là LTE, viết tắt của *Long-Term Evolution*. Điều quan trọng là, dù các chuẩn được công bố dưới dạng các bản phát hành rời rạc, toàn ngành công nghiệp đã đi theo một lộ trình tiến hóa khá rõ ràng gọi là LTE. Phần này sử dụng thuật ngữ LTE, nhưng sẽ nhấn mạnh các thay đổi của 5G khi thích hợp.

Đổi mới chính của giao diện vô tuyến LTE là cách nó phân bổ phổ radio sẵn có cho các UE. Không giống như Wi-Fi, vốn dựa trên cạnh tranh, LTE sử dụng chiến lược đặt chỗ. Sự khác biệt này bắt nguồn từ giả định cơ bản về mức sử dụng của mỗi hệ thống: Wi-Fi giả định mạng tải nhẹ (và do đó lạc quan truyền khi liên kết không dây rỗi và lùi lại nếu phát hiện cạnh tranh), trong khi mạng di động giả định (và hướng tới) mức sử dụng cao (và do đó phân bổ rõ ràng các “phần” phổ radio cho từng người dùng).

Cơ chế truy cập môi trường tiên tiến nhất của LTE gọi là *Ghép Kênh Phân Chia Tần Số Trực Giao Nhiều Người Dùng* (OFDMA). Ý tưởng là ghép dữ liệu trên một tập hợp 12 tần số con trực giao, mỗi tần số được điều chế độc lập. “Multiple Access” trong OFDMA ngụ ý rằng dữ liệu có thể được gửi đồng thời cho nhiều người dùng, mỗi người trên một tần số con khác nhau và trong một khoảng thời gian khác nhau. Các dải con này hẹp (ví dụ, 15kHz), nhưng việc mã hóa dữ liệu người dùng thành các ký hiệu OFDMA được thiết kế để giảm thiểu nguy cơ mất dữ liệu do nhiễu giữa các dải liền kề.

Việc sử dụng OFDMA dẫn đến việc hình dung phổ radio như một tài nguyên hai chiều, như minh họa trong :numref:`Hình %s <fig-ofdma>`. Đơn vị lập lịch nhỏ nhất, gọi là *Resource Element (RE)*, tương ứng với một dải rộng 15kHz quanh một tần số con và thời gian truyền một ký hiệu OFDMA. Số bit có thể mã hóa trong mỗi ký hiệu phụ thuộc vào tốc độ điều chế, ví dụ, sử dụng Điều chế Biên Độ Vuông Góc (QAM), 16-QAM cho 4 bit mỗi ký hiệu và 64-QAM cho 6 bit mỗi ký hiệu.

.. _fig-ofdma:
.. figure:: figures/access/Slide4.png
   :width: 800px
   :align: center

   Phổ radio sẵn có được biểu diễn trừu tượng bằng lưới 2-D các Resource Element có thể lập lịch.

Một bộ lập lịch đưa ra quyết định phân bổ ở mức các khối 7x12=84 resource element, gọi là *Physical Resource Block (PRB)*. :numref:`Hình %s <fig-ofdma>` minh họa hai PRB liên tiếp, trong đó các UE được biểu diễn bằng các khối màu khác nhau. Tất nhiên, thời gian tiếp tục trôi theo một trục, và tùy vào kích thước băng tần được cấp phép, có thể có nhiều khe tần số con hơn (và do đó nhiều PRB hơn) trên trục còn lại, nên bộ lập lịch thực chất là lập lịch một chuỗi các PRB để truyền.

Khoảng thời gian truyền 1ms (*Transmission Time Interval* - TTI) trong :numref:`Hình %s <fig-ofdma>` tương ứng với khung thời gian mà BBU nhận phản hồi từ UE về chất lượng tín hiệu mà chúng trải nghiệm. Phản hồi này, gọi là *Chỉ Báo Chất Lượng Kênh* (CQI), về cơ bản báo cáo tỷ số tín hiệu trên nhiễu quan sát được, ảnh hưởng đến khả năng UE phục hồi các bit dữ liệu. Trạm gốc sau đó sử dụng thông tin này để điều chỉnh cách phân bổ phổ radio cho các UE mà nó phục vụ.

Cho đến điểm này, mô tả về cách lập lịch phổ radio là đặc trưng cho 4G. Việc chuyển từ 4G sang 5G bổ sung thêm nhiều mức tự do trong cách lập lịch phổ radio, cho phép thích ứng mạng di động với nhiều loại thiết bị và ứng dụng đa dạng hơn.

Về cơ bản, 5G định nghĩa một họ các dạng sóng—khác với 4G chỉ quy định một dạng sóng—mỗi dạng tối ưu cho một dải trong phổ radio.\ [#]_ Các dải có tần số mang dưới 1GHz được thiết kế để cung cấp băng thông rộng di động và dịch vụ IoT quy mô lớn với trọng tâm là phạm vi phủ sóng. Các tần số mang từ 1GHz-6GHz được thiết kế để cung cấp băng thông rộng hơn, tập trung vào băng thông rộng di động và các ứng dụng quan trọng. Các tần số trên 24GHz (mmWave) được thiết kế để cung cấp băng thông siêu rộng trên phạm vi ngắn, tầm nhìn thẳng.

.. [#] Dạng sóng là thuộc tính độc lập tần số, biên độ và pha (hình dạng) của tín hiệu. Sóng sin là một ví dụ về dạng sóng.

Các dạng sóng khác nhau này ảnh hưởng đến việc lập lịch và khoảng cách tần số con (tức là “kích thước” của Resource Element vừa mô tả).

- Với các dải dưới 1GHz, 5G cho phép băng thông tối đa 50MHz. Trong trường hợp này, có hai dạng sóng: một với khoảng cách tần số con 15kHz và một với 30kHz. (Chúng ta đã dùng 15kHz trong ví dụ ở :numref:`Hình %s <fig-ofdma>`. Khoảng thời gian lập lịch tương ứng là 0,5ms và 0,25ms. (Chúng ta đã dùng 0,5ms trong ví dụ ở :numref:`Hình %s <fig-ofdma>`.)

-  Với các dải 1GHz-6GHz, băng thông tối đa lên tới 100MHz. Tương ứng, có ba dạng sóng với khoảng cách tần số con 15kHz, 30kHz và 60kHz, tương ứng với các khoảng thời gian lập lịch 0,5ms, 0,25ms và 0,125ms.

-  Với các dải sóng milimet, băng thông có thể lên tới 400MHz. Có hai dạng sóng, với khoảng cách tần số con 60kHz và 120kHz. Cả hai đều có khoảng thời gian lập lịch 0,125ms.

Dải tùy chọn này quan trọng vì nó bổ sung thêm một mức tự do cho bộ lập lịch. Ngoài việc phân bổ các khối tài nguyên cho người dùng, nó còn có khả năng điều chỉnh động kích thước các khối tài nguyên bằng cách thay đổi dạng sóng được sử dụng trong dải mà nó chịu trách nhiệm lập lịch.

Dù là 4G hay 5G, thuật toán lập lịch là một bài toán tối ưu hóa đầy thách thức, với mục tiêu đồng thời (a) tối đa hóa mức sử dụng băng tần sẵn có, và (b) đảm bảo mỗi UE nhận được mức dịch vụ mà nó yêu cầu. Thuật toán này không được 3GPP quy định, mà là tài sản trí tuệ độc quyền của các nhà cung cấp BBU.