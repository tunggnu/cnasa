2.7 Mạng Không Dây
==================

Các công nghệ không dây khác biệt với các liên kết có dây ở một số điểm quan trọng, đồng thời cũng chia sẻ nhiều đặc tính chung. Giống như liên kết có dây, các vấn đề về lỗi bit là mối quan tâm lớn—thường còn nghiêm trọng hơn do môi trường nhiễu không thể đoán trước của hầu hết các liên kết không dây. Việc đóng khung và đảm bảo độ tin cậy cũng cần được giải quyết. Không giống như liên kết có dây, năng lượng là một vấn đề lớn đối với không dây, đặc biệt vì các liên kết không dây thường được sử dụng bởi các thiết bị di động nhỏ (như điện thoại và cảm biến) có nguồn năng lượng hạn chế (ví dụ, pin nhỏ). Hơn nữa, bạn không thể cứ phát sóng ở công suất tùy ý cao với bộ phát radio—có những lo ngại về việc gây nhiễu cho các thiết bị khác và thường có quy định về lượng công suất mà một thiết bị có thể phát ra ở bất kỳ tần số nào.

Môi trường không dây vốn dĩ là đa truy cập; rất khó để hướng tín hiệu radio của bạn chỉ đến một bộ thu duy nhất hoặc tránh nhận tín hiệu radio từ bất kỳ bộ phát nào đủ mạnh trong khu vực của bạn. Do đó, kiểm soát truy cập môi trường là một vấn đề trung tâm đối với các liên kết không dây. Và, bởi vì khó kiểm soát ai nhận được tín hiệu của bạn khi bạn truyền qua không khí, các vấn đề về nghe lén cũng cần được xem xét.

Có rất nhiều công nghệ không dây khác nhau, mỗi loại đánh đổi các yếu tố khác nhau. Một cách đơn giản để phân loại các công nghệ này là theo tốc độ dữ liệu mà chúng cung cấp và khoảng cách giữa các nút giao tiếp. Các khác biệt quan trọng khác bao gồm phần nào của phổ điện từ mà chúng sử dụng (bao gồm việc có cần giấy phép hay không) và mức tiêu thụ năng lượng. Trong phần này, chúng tôi thảo luận về hai công nghệ không dây nổi bật: Wi-Fi (tên chính thức là 802.11) và Bluetooth. Phần tiếp theo sẽ thảo luận về mạng di động trong bối cảnh dịch vụ truy cập ISP. :numref:`Bảng %s <tab-wireless-tech>` cung cấp tổng quan về các công nghệ này và cách chúng so sánh với nhau.

.. _tab-wireless-tech:
.. table:: Tổng quan các công nghệ không dây hàng đầu.
   :widths: auto
   :align: center

   +-------------+-----------------------+-----------------+--------------+
   |             | Bluetooth (802.15.1)  | Wi-Fi (802.11)  | 4G Cellular  |
   +=============+=======================+=================+==============+
   | Độ dài      | 10 m                  | 100 m           | Hàng chục    |
   | liên kết    |                       |                 | kilômét      |
   +-------------+-----------------------+-----------------+--------------+
   | Tốc độ      | 2 Mbps (chia sẻ)      | 150-450 Mbps    | 1-5 Mbps     |
   | dữ liệu     |                       |                 |              |
   +-------------+-----------------------+-----------------+--------------+
   | Ứng dụng    | Kết nối thiết bị      | Kết nối máy     | Kết nối điện |
   | điển hình   | ngoại vi với máy tính | tính với trạm   | thoại di động|
   |             |                       | gốc có dây      | với trạm gốc |
   +-------------+-----------------------+-----------------+--------------+
   | Công nghệ   | USB                   | Ethernet        | PON          |
   | có dây      |                       |                 |              |
   | tương tự    |                       |                 |              |
   +-------------+-----------------------+-----------------+--------------+

Bạn có thể nhớ rằng *băng thông* đôi khi có nghĩa là độ rộng của một dải tần số tính bằng hertz và đôi khi là tốc độ dữ liệu của một liên kết. Vì cả hai khái niệm này đều xuất hiện trong thảo luận về mạng không dây, chúng tôi sẽ sử dụng *băng thông* ở đây theo nghĩa hẹp hơn—độ rộng của một dải tần số—và sử dụng thuật ngữ *tốc độ dữ liệu* để mô tả số bit mỗi giây có thể truyền qua liên kết, như trong :numref:`Bảng %s <tab-wireless-tech>`.

2.7.1 Các Vấn Đề Cơ Bản
-----------------------

Vì các liên kết không dây đều chia sẻ cùng một môi trường, thách thức là chia sẻ môi trường đó một cách hiệu quả, mà không gây nhiễu lẫn nhau quá mức. Phần lớn việc chia sẻ này được thực hiện bằng cách phân chia theo tần số và không gian. Việc sử dụng độc quyền một tần số nhất định trong một khu vực địa lý nhất định có thể được cấp cho một thực thể riêng biệt như một công ty. Có thể giới hạn vùng phủ sóng của tín hiệu điện từ vì các tín hiệu này sẽ yếu đi, hoặc *suy hao*, theo khoảng cách từ nguồn phát. Để giảm vùng phủ sóng của tín hiệu, hãy giảm công suất của bộ phát.

Việc phân bổ này thường do các cơ quan chính phủ quyết định, như Ủy ban Truyền thông Liên bang (FCC) ở Hoa Kỳ. Các dải tần số cụ thể được phân bổ cho các mục đích nhất định. Một số dải dành riêng cho chính phủ. Các dải khác dành cho các mục đích như radio AM, radio FM, truyền hình, thông tin vệ tinh và điện thoại di động. Các tần số cụ thể trong các dải này sau đó được cấp phép cho các tổ chức cá nhân sử dụng trong các khu vực địa lý nhất định. Cuối cùng, một số dải tần số được dành cho sử dụng không cần giấy phép—các dải mà không cần giấy phép.

Các thiết bị sử dụng tần số không cần giấy phép vẫn phải tuân theo một số hạn chế nhất định để việc chia sẻ không bị kiểm soát này có thể hoạt động. Quan trọng nhất là giới hạn công suất phát. Điều này giới hạn phạm vi của tín hiệu, làm giảm khả năng gây nhiễu cho tín hiệu khác. Ví dụ, một điện thoại không dây (thiết bị không cần giấy phép phổ biến) có thể có phạm vi khoảng 30 mét.

Một ý tưởng thường xuất hiện khi phổ tần được chia sẻ giữa nhiều thiết bị và ứng dụng là *phổ trải* (spread spectrum). Ý tưởng của phổ trải là trải tín hiệu trên một dải tần số rộng hơn, nhằm giảm thiểu tác động của nhiễu từ các thiết bị khác. (Phổ trải ban đầu được thiết kế cho mục đích quân sự, nên các “thiết bị khác” này thường cố tình gây nhiễu tín hiệu.) Ví dụ, *nhảy tần số* là một kỹ thuật phổ trải, trong đó tín hiệu được truyền trên một chuỗi tần số ngẫu nhiên; tức là, truyền ở một tần số, sau đó là tần số thứ hai, rồi thứ ba, v.v. Chuỗi tần số này không thực sự ngẫu nhiên mà được tính toán bằng thuật toán tạo số giả ngẫu nhiên. Bộ thu sử dụng cùng thuật toán với bộ phát và khởi tạo với cùng giá trị ban đầu; do đó, nó có thể nhảy tần đồng bộ với bộ phát để nhận đúng khung. Cách này giúp giảm nhiễu bằng cách làm cho hai tín hiệu khó sử dụng cùng một tần số trong hơn một vài bit rời rạc.

Một kỹ thuật phổ trải thứ hai, gọi là *trực tiếp* (direct sequence), bổ sung dư thừa để tăng khả năng chịu nhiễu. Mỗi bit dữ liệu được biểu diễn bằng nhiều bit trong tín hiệu truyền, để nếu một số bit bị nhiễu làm hỏng, vẫn còn đủ dư thừa để khôi phục bit gốc. Với mỗi bit cần truyền, bộ phát thực tế gửi phép XOR giữa bit đó và n bit ngẫu nhiên. Giống như nhảy tần, chuỗi bit ngẫu nhiên này được tạo bởi một bộ sinh số giả ngẫu nhiên mà cả bộ phát và bộ thu đều biết. Các giá trị truyền này, gọi là *mã chipping* n bit, trải tín hiệu trên một dải tần số rộng gấp n lần so với khung thông thường. :numref:`Hình %s <fig-chipping>` cho ví dụ về chuỗi chipping 4 bit.

.. _fig-chipping:
.. figure:: figures/f02-27-9780123850591.png
   :width: 500px
   :align: center

   Ví dụ chuỗi chipping 4 bit.

Các phần khác nhau của phổ điện từ có các đặc tính khác nhau, khiến một số phù hợp hơn cho truyền thông, số khác thì không. Ví dụ, một số có thể xuyên qua tòa nhà, số khác thì không. Chính phủ chỉ quản lý phần phổ thông tin chính: dải radio và vi ba. Khi nhu cầu về phổ thông tin tăng lên, có sự quan tâm lớn đến phổ đang trở nên khả dụng khi truyền hình analog bị loại bỏ để chuyển sang truyền hình số.

Trong nhiều mạng không dây ngày nay, chúng ta thấy có hai loại điểm cuối khác nhau. Một điểm cuối, đôi khi gọi là *trạm gốc* (base station), thường không di động nhưng có kết nối có dây (hoặc ít nhất là băng thông cao) tới Internet hoặc các mạng khác, như minh họa trong :numref:`Hình %s <fig-wirelessBaseStation>`. Nút ở đầu kia của liên kết—ở đây là nút khách—thường di động và dựa vào liên kết tới trạm gốc để giao tiếp với các nút khác.

Lưu ý rằng trong :numref:`Hình %s <fig-wirelessBaseStation>`, chúng tôi sử dụng cặp đường lượn sóng để biểu diễn trừu tượng “liên kết” không dây giữa hai thiết bị (ví dụ, giữa trạm gốc và một nút khách). Một điểm thú vị của truyền thông không dây là nó tự nhiên hỗ trợ truyền thông điểm-đa điểm, vì sóng radio do một thiết bị phát ra có thể được nhiều thiết bị nhận đồng thời. Tuy nhiên, thường hữu ích khi tạo ra trừu tượng liên kết điểm-điểm cho các giao thức tầng cao hơn, và chúng ta sẽ thấy các ví dụ về cách điều này hoạt động ở phần sau.

Lưu ý rằng trong :numref:`Hình %s <fig-wirelessBaseStation>`, việc giao tiếp giữa các nút không phải trạm gốc (nút khách) được định tuyến qua trạm gốc. Điều này mặc dù sóng radio phát ra từ một nút khách có thể được các nút khách khác nhận—mô hình trạm gốc chung không cho phép giao tiếp trực tiếp giữa các nút khách.

.. _fig-wirelessBaseStation:
.. figure:: figures/f02-28-9780123850591.png
   :width: 500px
   :align: center

   Một mạng không dây sử dụng trạm gốc.

Cấu trúc này ngụ ý ba mức độ di động khác nhau về chất lượng. Mức đầu tiên là không di động, ví dụ khi bộ thu phải ở vị trí cố định để nhận truyền dẫn định hướng từ trạm gốc. Mức thứ hai là di động trong phạm vi một trạm gốc, như với Bluetooth. Mức thứ ba là di động giữa các trạm gốc, như với điện thoại di động và Wi-Fi.

.. _fig-wirelessMesh:
.. figure:: figures/f02-29-9780123850591.png
   :width: 500px
   :align: center

   Mạng mesh hoặc ad hoc không dây.

Một cấu trúc thay thế đang ngày càng được quan tâm là mạng *mesh* hoặc *ad hoc*. Trong mạng mesh không dây, các nút là ngang hàng; tức là không có nút trạm gốc đặc biệt. Thông điệp có thể được chuyển tiếp qua chuỗi các nút ngang hàng miễn là mỗi nút nằm trong phạm vi của nút trước đó. Điều này được minh họa trong :numref:`Hình %s <fig-wirelessMesh>`. Điều này cho phép phần không dây của mạng mở rộng vượt ra ngoài phạm vi giới hạn của một radio đơn lẻ. Xét về cạnh tranh giữa các công nghệ, điều này cho phép một công nghệ tầm ngắn mở rộng phạm vi và có thể cạnh tranh với công nghệ tầm xa hơn. Mesh cũng cung cấp khả năng chịu lỗi bằng cách cung cấp nhiều tuyến đường cho một thông điệp đi từ điểm A đến điểm B. Một mạng mesh có thể được mở rộng dần dần, với chi phí tăng dần. Tuy nhiên, mạng mesh yêu cầu các nút không phải trạm gốc phải có phần cứng và phần mềm đủ phức tạp, có thể làm tăng chi phí và tiêu thụ năng lượng—một yếu tố quan trọng với thiết bị dùng pin. Mạng mesh không dây là chủ đề nghiên cứu đáng kể, nhưng vẫn còn khá mới so với mạng có trạm gốc. Mạng cảm biến không dây, một công nghệ mới nổi khác, thường tạo thành các mesh không dây.

Sau khi đã đề cập đến một số vấn đề chung của không dây, hãy cùng xem chi tiết hai công nghệ không dây phổ biến.

2.7.2 Wi-Fi (802.11)
--------------------

Hầu hết độc giả sẽ từng sử dụng mạng không dây dựa trên chuẩn IEEE 802.11, thường gọi là *Wi-Fi*. Wi-Fi thực ra là một thương hiệu, thuộc sở hữu của một nhóm thương mại gọi là Wi-Fi Alliance, tổ chức chứng nhận sản phẩm tuân thủ 802.11. Giống như Ethernet, 802.11 được thiết kế để sử dụng trong khu vực địa lý hạn chế (nhà, văn phòng, khuôn viên), và thách thức chính là điều phối truy cập vào môi trường truyền thông chia sẻ—trong trường hợp này là các tín hiệu lan truyền trong không gian.

Đặc Tính Vật Lý
~~~~~~~~~~~~~~~

802.11 định nghĩa một số lớp vật lý khác nhau hoạt động ở các dải tần số khác nhau và cung cấp nhiều tốc độ dữ liệu khác nhau.

Chuẩn 802.11 gốc định nghĩa hai lớp vật lý dựa trên radio, một sử dụng nhảy tần (trên 79 dải tần 1 MHz) và một sử dụng phổ trải trực tiếp (với chuỗi chipping 11 bit). Cả hai đều cung cấp tốc độ dữ liệu khoảng 2 Mbps. Sau đó, chuẩn lớp vật lý 802.11b được bổ sung, sử dụng biến thể của phổ trải trực tiếp, hỗ trợ tới 11 Mbps. Cả ba chuẩn này đều hoạt động ở dải tần số 2.4 GHz không cần giấy phép. Tiếp theo là 802.11a, cung cấp tới 54 Mbps sử dụng biến thể của ghép kênh phân chia tần số gọi là *OFDM* (orthogonal frequency division multiplexing). 802.11a chạy ở dải tần 5 GHz không cần giấy phép. 802.11g ra đời sau, cũng sử dụng OFDM, cung cấp tới 54 Mbps. 802.11g tương thích ngược với 802.11b (và quay lại dải 2.4 GHz).

Tại thời điểm viết sách, nhiều thiết bị hỗ trợ 802.11n hoặc 802.11ac, thường đạt tốc độ dữ liệu mỗi thiết bị từ 150 Mbps đến 450 Mbps. Sự cải thiện này một phần nhờ sử dụng nhiều ăng-ten và cho phép băng thông kênh không dây lớn hơn. Việc sử dụng nhiều ăng-ten thường gọi là *MIMO* (multiple-input, multiple-output). Chuẩn mới nhất, 802.11ax, hứa hẹn cải thiện thông lượng đáng kể nữa, một phần nhờ áp dụng nhiều kỹ thuật mã hóa và điều chế được sử dụng trong mạng di động 4G/5G, sẽ được mô tả ở phần sau.

Các sản phẩm thương mại thường hỗ trợ nhiều phiên bản 802.11; nhiều trạm gốc hỗ trợ cả năm biến thể (a, b, g, n, ac). Điều này không chỉ đảm bảo tương thích với bất kỳ thiết bị nào hỗ trợ một trong các chuẩn mà còn cho phép hai sản phẩm chọn tùy chọn băng thông cao nhất cho môi trường cụ thể.

Đáng chú ý là trong khi tất cả các chuẩn 802.11 đều định nghĩa tốc độ bit *tối đa* có thể hỗ trợ, chúng cũng hỗ trợ các tốc độ thấp hơn (ví dụ, 802.11a cho phép các tốc độ 6, 9, 12, 18, 24, 36, 48 và 54 Mbps). Ở tốc độ thấp hơn, việc giải mã tín hiệu truyền trong môi trường nhiễu dễ dàng hơn. Các sơ đồ điều chế khác nhau được sử dụng để đạt các tốc độ bit khác nhau. Ngoài ra, lượng thông tin dư thừa dưới dạng mã sửa lỗi cũng thay đổi. Thông tin dư thừa nhiều hơn nghĩa là khả năng chịu lỗi bit cao hơn nhưng làm giảm tốc độ dữ liệu hiệu dụng (vì nhiều bit truyền là dư thừa).

Các hệ thống cố gắng chọn tốc độ bit tối ưu dựa trên môi trường nhiễu mà chúng gặp phải; các thuật toán chọn tốc độ bit có thể khá phức tạp. Thú vị là các chuẩn 802.11 không quy định cách tiếp cận cụ thể mà để các nhà sản xuất tự quyết định. Cách tiếp cận cơ bản là ước lượng tỷ lệ lỗi bit bằng cách đo trực tiếp tỷ số tín hiệu trên nhiễu (SNR) ở lớp vật lý hoặc ước lượng SNR bằng cách đo tần suất các gói được truyền và xác nhận thành công. Trong một số phương pháp, bộ phát sẽ thỉnh thoảng thử tốc độ bit cao hơn bằng cách gửi một hoặc vài gói ở tốc độ đó để xem có thành công không.

Tránh Va Chạm
~~~~~~~~~~~~~

Thoạt nhìn, có thể nghĩ rằng một giao thức không dây sẽ tuân theo cùng thuật toán như Ethernet—chờ đến khi liên kết rỗi trước khi truyền và lùi lại nếu xảy ra va chạm—và, về cơ bản, 802.11 cũng làm như vậy. Tuy nhiên, điểm phức tạp thêm cho không dây là, trong khi một nút trên Ethernet nhận được mọi truyền của các nút khác và có thể truyền và nhận cùng lúc, thì điều này không đúng với các nút không dây. Điều này làm cho việc phát hiện va chạm trở nên phức tạp hơn. Lý do các nút không dây thường không thể truyền và nhận cùng lúc (trên cùng tần số) là công suất phát ra lớn hơn nhiều so với tín hiệu nhận được, làm nhiễu mạch thu. Lý do một nút có thể không nhận được truyền của nút khác là vì nút đó có thể ở quá xa hoặc bị vật cản. Tình huống này phức tạp hơn tưởng tượng, như sẽ thấy dưới đây.

.. _fig-wifiHiddenNode:
.. figure:: figures/f02-30-9780123850591.png
   :width: 400px
   :align: center

   Vấn đề nút ẩn. Dù A và C không thấy nhau, tín hiệu của họ có thể va chạm tại B. (Phạm vi của B không được vẽ.)

Xét tình huống trong :numref:`Hình %s <fig-wifiHiddenNode>`, nơi A và C đều trong phạm vi của B nhưng không thấy nhau. Giả sử cả A và C đều muốn giao tiếp với B nên mỗi bên gửi cho B một khung. A và C không biết về nhau vì tín hiệu của họ không tới được đối phương. Hai khung này va chạm tại B, nhưng không giống Ethernet, cả A và C đều không biết về va chạm này. A và C được gọi là *nút ẩn* đối với nhau.

.. _fig-wifiExposedNode:
.. figure:: figures/f02-31-9780123850591.png
   :width: 325px
   :align: center

   Vấn đề nút lộ. Dù B và C nghe được tín hiệu của nhau, không có nhiễu nếu B truyền cho A trong khi C truyền cho D. (Phạm vi của A và D không được vẽ.)

Một vấn đề liên quan, gọi là *nút lộ* (exposed node), xảy ra trong tình huống minh họa ở :numref:`Hình %s <fig-wifiExposedNode>`, nơi mỗi trong bốn nút chỉ có thể gửi và nhận tín hiệu tới các nút liền kề trái và phải. Ví dụ, B có thể trao đổi khung với A và C nhưng không tới D, trong khi C có thể tới B và D nhưng không tới A. Giả sử B đang gửi cho A. Nút C biết về giao tiếp này vì nó nghe được truyền của B. Tuy nhiên, sẽ là sai lầm nếu C kết luận rằng nó không thể truyền cho ai chỉ vì nó nghe được truyền của B. Ví dụ, nếu C muốn truyền cho D, điều này không thành vấn đề vì truyền của C tới D sẽ không ảnh hưởng đến việc A nhận từ B. (Nó sẽ ảnh hưởng nếu A gửi cho B, nhưng trong ví dụ này B đang truyền.)

802.11 giải quyết các vấn đề này bằng cách sử dụng CSMA/CA, trong đó CA là *tránh va chạm* (collision avoidance), trái ngược với *phát hiện va chạm* (collision detection) của CSMA/CD trên Ethernet. Có một số thành phần để làm việc này.

Phần cảm nhận sóng mang có vẻ đơn giản: Trước khi gửi gói, bộ phát kiểm tra xem có nghe thấy truyền nào khác không; nếu không, nó gửi. Tuy nhiên, do vấn đề nút ẩn, chỉ chờ không có tín hiệu từ các bộ phát khác không đảm bảo sẽ không có va chạm từ góc nhìn của bộ thu. Vì vậy, một phần của CSMA/CA là ACK rõ ràng từ bộ thu gửi về bộ phát. Nếu gói được giải mã thành công và vượt qua CRC ở bộ thu, bộ thu gửi ACK lại cho bộ phát.

Lưu ý rằng nếu xảy ra va chạm, toàn bộ gói sẽ bị hỏng. Vì vậy, 802.11 bổ sung cơ chế tùy chọn gọi là RTS-CTS (Ready to Send–Clear to Send). Cơ chế này phần nào giải quyết vấn đề nút ẩn. Bộ phát gửi một RTS—một gói ngắn—cho bộ thu dự định, và nếu gói này được nhận thành công, bộ thu trả lời bằng một gói ngắn khác, CTS. Dù RTS có thể không được nút ẩn nghe thấy, CTS có thể sẽ được nghe. Điều này báo cho các nút trong phạm vi bộ thu rằng họ không nên gửi gì trong một thời gian—thời gian truyền dự kiến được ghi trong RTS và CTS. Sau thời gian đó cộng thêm một khoảng nhỏ, môi trường được coi là rỗi và nút khác có thể thử gửi.

Tất nhiên, hai nút có thể cùng phát hiện liên kết rỗi và cùng lúc gửi RTS, gây va chạm RTS. Các bộ phát nhận ra va chạm khi không nhận được CTS sau một thời gian, khi đó mỗi bên chờ ngẫu nhiên trước khi thử lại. Thời gian chờ của mỗi nút được xác định bởi thuật toán lùi theo hàm mũ giống như trên Ethernet.

Sau khi trao đổi RTS-CTS thành công, bộ phát gửi gói dữ liệu và, nếu mọi việc suôn sẻ, nhận được ACK cho gói đó. Nếu không nhận được ACK kịp thời, bộ phát sẽ thử lại xin quyền sử dụng kênh như trên. Lúc này, các nút khác cũng có thể đang cố truy cập kênh.

Hệ Thống Phân Phối
~~~~~~~~~~~~~~~~~~

Như mô tả ở trên, 802.11 phù hợp cho mạng có cấu trúc mesh (*ad hoc*), và chuẩn 802.11s cho mạng mesh đang gần hoàn thiện. Tuy nhiên, hiện nay hầu hết các mạng 802.11 sử dụng cấu trúc dựa trên trạm gốc.

Thay vì tất cả các nút đều bình đẳng, một số nút được phép di chuyển (ví dụ, laptop của bạn) và một số được kết nối với hạ tầng mạng có dây. 802.11 gọi các trạm gốc này là *điểm truy cập* (AP), và chúng được kết nối với nhau qua cái gọi là *hệ thống phân phối*. :numref:`Hình %s <fig-wireless2>` minh họa một hệ thống phân phối kết nối ba điểm truy cập, mỗi điểm phục vụ các nút trong một vùng. Mỗi điểm truy cập hoạt động trên một kênh trong dải tần phù hợp, và thường mỗi AP sẽ ở kênh khác với hàng xóm.

.. _fig-wireless2:
.. figure:: figures/f02-32-9780123850591.png
   :width: 500px
   :align: center

   Các điểm truy cập kết nối với hệ thống phân phối.

Chi tiết của hệ thống phân phối không quan trọng ở đây—nó có thể là Ethernet chẳng hạn. Điều quan trọng là mạng phân phối hoạt động ở tầng liên kết, cùng tầng với các liên kết không dây. Nói cách khác, nó không phụ thuộc vào giao thức tầng cao hơn (như tầng mạng).

Dù hai nút có thể giao tiếp trực tiếp nếu trong phạm vi nhau, ý tưởng của cấu hình này là mỗi nút liên kết với một điểm truy cập. Để nút A giao tiếp với E, ví dụ, A gửi khung tới điểm truy cập của nó (AP-1), AP-1 chuyển khung qua hệ thống phân phối tới AP-3, AP-3 truyền khung tới E. Cách AP-1 biết chuyển thông điệp tới AP-3 nằm ngoài phạm vi 802.11; có thể dùng giao thức cầu nối. 802.11 quy định cách các nút chọn điểm truy cập và, thú vị hơn, cách thuật toán này hoạt động khi các nút di chuyển giữa các vùng.

Kỹ thuật chọn điểm truy cập gọi là *quét* (scanning) và gồm bốn bước:

1. Nút gửi một khung ``Probe``.

2. Tất cả các AP trong phạm vi trả lời bằng khung ``Probe Response``.

3. Nút chọn một điểm truy cập và gửi cho AP đó một khung ``Association Request``.

4. AP trả lời bằng khung ``Association Response``.

Một nút thực hiện giao thức này mỗi khi tham gia mạng, cũng như khi không hài lòng với AP hiện tại. Điều này có thể xảy ra, ví dụ, khi tín hiệu từ AP hiện tại yếu đi do nút di chuyển xa. Khi một nút kết nối với AP mới, AP mới sẽ thông báo cho AP cũ về sự thay đổi (diễn ra ở bước 4) qua hệ thống phân phối.

.. _fig-wireless3:
.. figure:: figures/f02-33-9780123850591.png
   :width: 500px
   :align: center

   Di động của nút.

Xét tình huống trong :numref:`Hình %s <fig-wireless3>`, nơi nút C di chuyển từ vùng của AP-1 sang vùng của AP-2. Khi di chuyển, nó gửi các khung ``Probe``, cuối cùng nhận được ``Probe Response`` từ AP-2. Đến một lúc nào đó, C ưu tiên AP-2 hơn AP-1 và liên kết với AP đó.

Cơ chế vừa mô tả gọi là *quét chủ động* (active scanning) vì nút chủ động tìm kiếm điểm truy cập. AP cũng định kỳ gửi khung ``Beacon`` quảng bá khả năng của mình; bao gồm các tốc độ truyền mà AP hỗ trợ. Đây gọi là *quét thụ động* (passive scanning), và nút có thể chuyển sang AP này dựa trên khung ``Beacon`` chỉ bằng cách gửi khung ``Association Request`` lại cho AP.

Định Dạng Khung
~~~~~~~~~~~~~~~

Phần lớn định dạng khung 802.11, như minh họa ở :numref:`Hình %s <fig-802.11-format>`, đúng như mong đợi. Khung chứa địa chỉ nguồn và đích, mỗi địa chỉ 48 bit; tối đa 2312 byte dữ liệu; và CRC 32 bit. Trường ``Control`` chứa ba trường con quan trọng (không hiển thị): trường ``Type`` 6 bit cho biết khung mang dữ liệu, là khung RTS hay CTS, hay dùng cho thuật toán quét, và cặp trường 1 bit—gọi là ``ToDS`` và ``FromDS``—được mô tả dưới đây.

.. _fig-802.11-format:
.. figure:: figures/f02-34-9780123850591.png
   :width: 550px
   :align: center

   Định dạng khung 802.11.

Điểm đặc biệt của định dạng khung 802.11 là nó có bốn, thay vì hai, địa chỉ. Cách diễn giải các địa chỉ này phụ thuộc vào giá trị của các bit ``ToDS`` và ``FromDS`` trong trường ``Control`` của khung. Điều này để xử lý trường hợp khung phải được chuyển qua hệ thống phân phối, nghĩa là bộ phát gốc không nhất thiết là nút truyền gần nhất. Tương tự với địa chỉ đích. Trong trường hợp đơn giản nhất, khi một nút gửi trực tiếp cho nút khác, cả hai bit DS đều là 0, ``Addr1`` là nút đích, ``Addr2`` là nút nguồn. Trong trường hợp phức tạp nhất, cả hai bit DS đều là 1, nghĩa là thông điệp đi từ nút không dây lên hệ thống phân phối, rồi từ hệ thống phân phối tới nút không dây khác. Khi cả hai bit đều bật, ``Addr1`` là đích cuối cùng, ``Addr2`` là bộ phát gần nhất (chuyển khung từ hệ thống phân phối tới đích cuối), ``Addr3`` là đích trung gian (nhận khung từ nút không dây và chuyển lên hệ thống phân phối), và ``Addr4`` là nguồn gốc. Trong ví dụ ở :numref:`Hình %s <fig-wireless2>`, ``Addr1`` là E, ``Addr2`` là AP-3, ``Addr3`` là AP-1, và ``Addr4`` là A.

Bảo Mật Liên Kết Không Dây
~~~~~~~~~~~~~~~~~~~~~~~~~~

Một trong những vấn đề khá rõ ràng của liên kết không dây so với dây hoặc cáp quang là bạn không thể chắc chắn dữ liệu của mình đã đi đâu. Bạn có thể biết nó đã được nhận bởi bộ thu dự định, nhưng không thể biết có bao nhiêu bộ thu khác cũng nhận được truyền của bạn. Vì vậy, nếu bạn quan tâm đến quyền riêng tư của dữ liệu, mạng không dây là một thách thức.

Ngay cả khi bạn không quan tâm đến quyền riêng tư dữ liệu—hoặc đã xử lý nó theo cách khác—bạn có thể lo lắng về việc người dùng trái phép chèn dữ liệu vào mạng của bạn. Nếu không gì khác, người dùng đó có thể tiêu tốn tài nguyên mà bạn muốn dành cho mình, như băng thông hữu hạn giữa nhà bạn và ISP.

Vì những lý do này, mạng không dây thường đi kèm với một số cơ chế kiểm soát truy cập cả vào liên kết lẫn dữ liệu truyền. Các cơ chế này thường được gọi chung là *bảo mật không dây*. Chuẩn WPA2 được sử dụng rộng rãi sẽ được mô tả ở Chương 8.

2.7.3 Bluetooth (802.15.1)
--------------------------

Bluetooth lấp đầy khoảng trống truyền thông tầm rất ngắn giữa điện thoại di động, PDA, máy tính xách tay và các thiết bị cá nhân hoặc ngoại vi khác. Ví dụ, Bluetooth có thể dùng để kết nối điện thoại với tai nghe hoặc máy tính xách tay với bàn phím. Nói chung, Bluetooth là giải pháp tiện lợi hơn so với nối hai thiết bị bằng dây. Trong các ứng dụng này, không cần phạm vi hay băng thông lớn. Điều này nghĩa là radio Bluetooth có thể sử dụng công suất phát rất thấp, vì công suất phát là một trong những yếu tố chính ảnh hưởng đến băng thông và phạm vi của liên kết không dây. Điều này phù hợp với các ứng dụng mục tiêu của thiết bị Bluetooth—hầu hết đều dùng pin (như tai nghe điện thoại phổ biến) nên rất quan trọng là không tiêu tốn nhiều năng lượng.

Bluetooth hoạt động ở dải tần không cần giấy phép 2.45 GHz. Liên kết Bluetooth có băng thông điển hình khoảng 1 đến 3 Mbps và phạm vi khoảng 10 m. Vì lý do này, và vì các thiết bị giao tiếp thường thuộc về một cá nhân hoặc nhóm, Bluetooth đôi khi được xếp vào loại Mạng Cá Nhân (PAN).

Bluetooth được quy định bởi một liên minh công nghiệp gọi là *Bluetooth Special Interest Group*. Nó quy định một bộ giao thức đầy đủ, vượt ra ngoài tầng liên kết để định nghĩa các giao thức ứng dụng, gọi là *profile*, cho nhiều ứng dụng khác nhau. Ví dụ, có profile để đồng bộ PDA với máy tính cá nhân. Một profile khác cho phép máy tính di động truy cập LAN có dây giống như 802.11, dù đây không phải mục tiêu ban đầu của Bluetooth. Chuẩn IEEE 802.15.1 dựa trên Bluetooth nhưng loại bỏ các giao thức ứng dụng.

Cấu hình mạng Bluetooth cơ bản, gọi là *piconet*, gồm một thiết bị chủ và tối đa bảy thiết bị phụ, như minh họa ở :numref:`Hình %s <fig-wirelessBluetoothPiconet>`. Mọi giao tiếp đều giữa chủ và phụ; các thiết bị phụ không giao tiếp trực tiếp với nhau. Vì vai trò của phụ đơn giản hơn, phần cứng và phần mềm Bluetooth của chúng có thể đơn giản và rẻ hơn.

.. _fig-wirelessBluetoothPiconet:
.. figure:: figures/f02-35-9780123850591.png
   :width: 400px
   :align: center

   Một piconet Bluetooth.

Vì Bluetooth hoạt động ở dải tần không cần giấy phép, nó phải sử dụng kỹ thuật phổ trải để xử lý nhiễu có thể có trong dải này. Nó sử dụng nhảy tần với 79 *kênh* (tần số), mỗi kênh dùng trong 625 μs. Điều này cung cấp một khe thời gian tự nhiên để Bluetooth sử dụng cho ghép kênh phân chia theo thời gian đồng bộ. Một khung chiếm 1, 3 hoặc 5 khe liên tiếp. Chỉ thiết bị chủ mới có thể bắt đầu truyền ở các khe lẻ. Thiết bị phụ có thể bắt đầu truyền ở khe chẵn—nhưng chỉ để đáp lại yêu cầu từ chủ ở khe trước đó, nhờ đó tránh tranh chấp giữa các thiết bị phụ.

Một thiết bị phụ có thể được *parked*; tức là, được đặt ở trạng thái không hoạt động, tiêu thụ năng lượng thấp. Thiết bị parked không thể giao tiếp trên piconet; chỉ có thể được kích hoạt lại bởi chủ. Một piconet có thể có tối đa 255 thiết bị parked ngoài các thiết bị phụ đang hoạt động.

Trong lĩnh vực truyền thông tầm rất ngắn, công suất thấp còn có một số công nghệ khác ngoài Bluetooth. Một trong số đó là ZigBee, do liên minh ZigBee phát triển và chuẩn hóa thành IEEE 802.15.4. Nó được thiết kế cho các tình huống yêu cầu băng thông thấp và tiêu thụ năng lượng rất thấp để kéo dài tuổi thọ pin. Nó cũng nhằm đơn giản và rẻ hơn Bluetooth, giúp dễ tích hợp vào các thiết bị giá rẻ như *cảm biến*. Cảm biến đang trở thành một lớp thiết bị mạng ngày càng quan trọng, khi công nghệ phát triển đến mức các thiết bị nhỏ, rẻ có thể được triển khai số lượng lớn để giám sát các yếu tố như nhiệt độ, độ ẩm, tiêu thụ năng lượng trong tòa nhà.
