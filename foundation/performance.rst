1.5 Hiệu năng
=============

Cho đến thời điểm này, chúng ta chủ yếu tập trung vào các khía cạnh chức năng của mạng. Tuy nhiên, giống như bất kỳ hệ thống máy tính nào, mạng máy tính cũng được kỳ vọng phải hoạt động hiệu quả. Điều này là do hiệu quả của các phép tính phân tán trên mạng thường phụ thuộc trực tiếp vào mức độ hiệu quả mà mạng truyền tải dữ liệu của phép tính đó. Mặc dù câu ngạn ngữ lập trình cũ “trước tiên hãy làm cho đúng rồi hãy làm cho nhanh” vẫn đúng, nhưng trong lĩnh vực mạng, thường cần phải “thiết kế hướng tới hiệu năng”. Do đó, điều quan trọng là phải hiểu các yếu tố khác nhau ảnh hưởng đến hiệu năng mạng.

1.5.1 Băng thông và Độ trễ
--------------------------

Hiệu năng mạng được đo lường theo hai cách cơ bản: *băng thông* (còn gọi là *throughput* - thông lượng) và *độ trễ* (còn gọi là *delay* - độ trễ). Băng thông của một mạng được xác định bởi số bit có thể được truyền qua mạng trong một khoảng thời gian nhất định. Ví dụ, một mạng có thể có băng thông 10 triệu bit/giây (Mbps), nghĩa là nó có thể truyền 10 triệu bit mỗi giây. Đôi khi, việc nghĩ về băng thông dưới góc độ mất bao lâu để truyền từng bit dữ liệu cũng hữu ích. Ví dụ, trên một mạng 10-Mbps, mất 0,1 micro giây (μs) để truyền mỗi bit.

Băng thông và thông lượng là hai thuật ngữ có sự khác biệt tinh tế. Trước hết, băng thông thực sự là một phép đo độ rộng của một dải tần số. Ví dụ, các đường dây điện thoại truyền thống hỗ trợ một dải tần số từ 300 đến 3300 Hz; nó được cho là có băng thông 3300 Hz - 300 Hz = 3000 Hz. Nếu bạn thấy từ *băng thông* được sử dụng trong một tình huống mà nó được đo bằng hertz, thì có lẽ nó đề cập đến phạm vi tín hiệu có thể được truyền tải.

Khi chúng ta nói về băng thông của một liên kết truyền thông, chúng ta thường đề cập đến số bit mỗi giây có thể được truyền trên liên kết đó. Điều này đôi khi cũng được gọi là *tốc độ dữ liệu* (data rate). Chúng ta có thể nói rằng băng thông của một liên kết Ethernet là 10 Mbps. Tuy nhiên, cũng có thể phân biệt giữa tốc độ dữ liệu tối đa có sẵn trên liên kết và số bit mỗi giây mà chúng ta thực tế có thể truyền qua liên kết đó. Chúng ta thường sử dụng từ *thông lượng* (throughput) để chỉ *hiệu năng đo được* của một hệ thống. Do đó, vì nhiều nguyên nhân gây kém hiệu quả trong triển khai, một cặp nút được kết nối bằng một liên kết có băng thông 10 Mbps có thể chỉ đạt được thông lượng 2 Mbps. Điều này có nghĩa là một ứng dụng trên một host có thể gửi dữ liệu đến host còn lại với tốc độ 2 Mbps.

Cuối cùng, chúng ta thường nói về *yêu cầu băng thông* của một ứng dụng. Đây là số bit mỗi giây mà ứng dụng cần truyền qua mạng để hoạt động chấp nhận được. Đối với một số ứng dụng, điều này có thể là “bao nhiêu cũng được”; đối với ứng dụng khác, có thể là một con số cố định (tốt nhất là không vượt quá băng thông liên kết có sẵn); và đối với ứng dụng khác nữa, có thể là một con số thay đổi theo thời gian. Chúng ta sẽ bàn thêm về chủ đề này ở phần sau của mục này.

Mặc dù bạn có thể nói về băng thông của toàn bộ mạng, đôi khi bạn muốn chính xác hơn, ví dụ, tập trung vào băng thông của một liên kết vật lý đơn lẻ hoặc của một kênh logic giữa hai tiến trình. Ở cấp vật lý, băng thông không ngừng được cải thiện, dường như không có giới hạn. Trực quan, nếu bạn coi một giây thời gian như một đoạn thước đo và băng thông là số bit có thể đặt vừa trong đoạn đó, thì bạn có thể coi mỗi bit như một xung có độ rộng nhất định. Ví dụ, mỗi bit trên một liên kết 1-Mbps có độ rộng 1 μs, trong khi mỗi bit trên một liên kết 2-Mbps có độ rộng 0,5 μs, như minh họa trong :numref:`Hình %s <fig-bit-width>`. Công nghệ truyền và nhận càng tinh vi, mỗi bit càng có thể được thu hẹp lại, do đó băng thông càng cao. Đối với các kênh logic giữa hai tiến trình, băng thông cũng bị ảnh hưởng bởi các yếu tố khác, bao gồm số lần phần mềm triển khai kênh phải xử lý, và có thể biến đổi, từng bit dữ liệu.

.. _fig-bit-width:
.. figure:: figures/f01-16-9780123850591.png
   :width: 400px
   :align: center

   Các bit được truyền ở một băng thông nhất định có thể được coi là có độ rộng nhất định: (a) bit truyền ở 1 Mbps (mỗi bit rộng 1 micro giây); (b) bit truyền ở 2 Mbps (mỗi bit rộng 0,5 micro giây).

Chỉ số hiệu năng thứ hai, độ trễ, tương ứng với thời gian cần thiết để một thông điệp đi từ đầu này đến đầu kia của mạng. (Cũng như với băng thông, chúng ta có thể tập trung vào độ trễ của một liên kết đơn lẻ hoặc của một kênh end-to-end.) Độ trễ được đo hoàn toàn theo đơn vị thời gian. Ví dụ, một mạng xuyên lục địa có thể có độ trễ 24 mili giây (ms); tức là, mất 24 ms để một thông điệp đi từ bờ biển này sang bờ biển kia của Bắc Mỹ. Có nhiều tình huống mà điều quan trọng hơn là biết mất bao lâu để gửi một thông điệp từ đầu này đến đầu kia của mạng và quay lại, thay vì chỉ độ trễ một chiều. Chúng ta gọi đây là *thời gian khứ hồi* (RTT) của mạng.

Chúng ta thường nghĩ về độ trễ như có ba thành phần. Thứ nhất là độ trễ lan truyền theo tốc độ ánh sáng. Độ trễ này xảy ra vì không có gì, kể cả một bit trên dây, có thể di chuyển nhanh hơn tốc độ ánh sáng. Nếu bạn biết khoảng cách giữa hai điểm, bạn có thể tính toán độ trễ lan truyền theo tốc độ ánh sáng, mặc dù bạn phải cẩn thận vì ánh sáng truyền qua các môi trường khác nhau với tốc độ khác nhau: Nó truyền với tốc độ 3.0 × 10\ :sup:`8` m/s trong chân không, 2.3 × 10\ :sup:`8` m/s trong cáp đồng, và 2.0 × 10\ :sup:`8` m/s trong sợi quang. Thứ hai là thời gian cần thiết để truyền một đơn vị dữ liệu. Điều này phụ thuộc vào băng thông mạng và kích thước gói dữ liệu mang dữ liệu đó. Thứ ba, có thể có độ trễ xếp hàng bên trong mạng, vì các switch chuyển mạch gói thường cần lưu trữ gói một thời gian trước khi chuyển tiếp lên liên kết ra. Vậy, chúng ta có thể định nghĩa tổng độ trễ như sau

::

   Độ trễ = Lan truyền + Truyền + Xếp hàng
   Lan truyền =  Khoảng cách/Tốc độ ánh sáng
   Truyền = Kích thước/Băng thông

trong đó ``Khoảng cách`` là chiều dài dây mà dữ liệu sẽ truyền qua, ``Tốc độ ánh sáng`` là tốc độ ánh sáng hiệu dụng trên dây đó, ``Kích thước`` là kích thước gói, và ``Băng thông`` là băng thông mà gói được truyền. Lưu ý rằng nếu thông điệp chỉ chứa một bit và chúng ta đang nói về một liên kết đơn lẻ (không phải toàn mạng), thì các thành phần ``Truyền`` và ``Xếp hàng`` không liên quan, và độ trễ chỉ là độ trễ lan truyền.

Băng thông và độ trễ kết hợp để xác định đặc tính hiệu năng của một liên kết hoặc kênh nhất định. Tuy nhiên, tầm quan trọng tương đối của chúng phụ thuộc vào ứng dụng. Đối với một số ứng dụng, độ trễ quan trọng hơn băng thông. Ví dụ, một client gửi một thông điệp 1 byte đến server và nhận lại một thông điệp 1 byte là trường hợp bị giới hạn bởi độ trễ. Giả sử không có phép tính phức tạp nào khi chuẩn bị phản hồi, ứng dụng sẽ hoạt động rất khác trên một kênh xuyên lục địa với RTT 100 ms so với một kênh trong cùng phòng với RTT 1 ms. Tuy nhiên, việc kênh là 1 Mbps hay 100 Mbps lại không quan trọng lắm, vì trường hợp đầu tiên thời gian truyền một byte (``Truyền``) là 8 μs và trường hợp sau là ``Truyền`` = 0,08 μs.

Ngược lại, hãy xem xét một chương trình thư viện số được yêu cầu lấy một ảnh 25 megabyte (MB)—băng thông càng lớn thì càng trả ảnh về cho người dùng nhanh hơn. Ở đây, băng thông của kênh là yếu tố quyết định hiệu năng. Để thấy điều này, giả sử kênh có băng thông 10 Mbps. Sẽ mất 20 giây để truyền ảnh (25 × 10\ :sup:`6` × 8 bit / 10 × 10\ :sup:`6` Mbps = 20 giây), nên việc ảnh nằm ở đầu kia của kênh 1 ms hay 100 ms là không quan trọng; sự khác biệt giữa thời gian phản hồi 20,001 giây và 20,1 giây là không đáng kể.

.. _fig-latency:
.. figure:: figures/f01-17-9780123850591.png
   :width: 600px
   :align: center

   Độ trễ cảm nhận (thời gian phản hồi) so với thời gian khứ hồi cho các kích thước đối tượng và tốc độ liên kết khác nhau.

:numref:`Hình %s <fig-latency>` cho bạn cảm nhận về việc độ trễ hay băng thông có thể chi phối hiệu năng trong các trường hợp khác nhau. Đồ thị cho thấy mất bao lâu để truyền các đối tượng có kích thước khác nhau (1 byte, 2 KB, 1 MB) qua các mạng có RTT từ 1 đến 100 ms và tốc độ liên kết 1,5 hoặc 10 Mbps. Chúng tôi sử dụng thang đo logarit để thể hiện hiệu năng tương đối. Với đối tượng 1 byte (ví dụ, một phím bấm), độ trễ gần như đúng bằng RTT, nên bạn không thể phân biệt giữa mạng 1,5 Mbps và 10 Mbps. Với đối tượng 2 KB (ví dụ, một email), tốc độ liên kết tạo ra khác biệt lớn trên mạng RTT 1 ms nhưng không đáng kể trên mạng RTT 100 ms. Với đối tượng 1 MB (ví dụ, một ảnh số), RTT không còn quan trọng—tốc độ liên kết mới là yếu tố quyết định hiệu năng trên toàn dải RTT.

Lưu ý rằng xuyên suốt cuốn sách này, chúng tôi sử dụng các thuật ngữ *độ trễ* và *độ trễ* (delay) một cách tổng quát để chỉ thời gian cần thiết để thực hiện một chức năng cụ thể, như truyền một thông điệp hoặc di chuyển một đối tượng. Khi chúng tôi đề cập đến thời gian cụ thể mà tín hiệu truyền từ đầu này đến đầu kia của một liên kết, chúng tôi dùng thuật ngữ *độ trễ lan truyền* (propagation delay). Ngoài ra, chúng tôi làm rõ trong ngữ cảnh thảo luận liệu chúng tôi đang nói về độ trễ một chiều hay thời gian khứ hồi.

Ngoài lề, máy tính ngày càng nhanh đến mức khi kết nối chúng với mạng, đôi khi hữu ích (ít nhất là về mặt hình tượng) khi nghĩ theo đơn vị *lệnh trên mỗi dặm* (instructions per mile). Hãy xem điều gì xảy ra khi một máy tính có thể thực thi 100 tỷ lệnh mỗi giây gửi một thông điệp qua một kênh có RTT 100 ms. (Để dễ tính, giả sử thông điệp đi qua quãng đường 5000 dặm.) Nếu máy tính đó ngồi chờ suốt 100 ms để nhận phản hồi, thì nó đã bỏ lỡ cơ hội thực thi 10 tỷ lệnh, tức là 2 triệu lệnh trên mỗi dặm. Việc gửi thông điệp qua mạng phải thực sự xứng đáng để biện minh cho sự lãng phí này.

1.5.2 Tích số Độ trễ × Băng thông
---------------------------------

Cũng hữu ích khi nói về tích số của hai chỉ số này, thường gọi là *tích số độ trễ × băng thông* (delay × bandwidth product). Trực quan, nếu chúng ta coi một kênh giữa hai tiến trình như một ống rỗng (xem :numref:`Hình %s <fig-pipe>`), trong đó độ trễ tương ứng với chiều dài ống và băng thông là đường kính ống, thì tích số độ trễ × băng thông cho ta thể tích của ống—số bit tối đa có thể đang truyền trong ống tại bất kỳ thời điểm nào. Nói cách khác, nếu độ trễ (đo bằng thời gian) tương ứng với chiều dài ống, thì với độ rộng mỗi bit (cũng đo bằng thời gian) bạn có thể tính được có bao nhiêu bit nằm trong ống. Ví dụ, một kênh xuyên lục địa với độ trễ một chiều 50 ms và băng thông 45 Mbps có thể chứa

.. centered:: 50 × 10\ :sup:`-3` × 45 × 10\ :sup:`6` *bit/giây* = 2,25 × 10\ :sup:`6` *bit*

hoặc xấp xỉ 280 KB dữ liệu. Nói cách khác, kênh (ống) ví dụ này chứa số byte tương đương với bộ nhớ của một máy tính cá nhân đầu những năm 1980.

.. _fig-pipe:
.. figure:: figures/f01-18-9780123850591.png
   :width: 400px
   :align: center

   Mạng như một ống dẫn.

Tích số độ trễ × băng thông quan trọng khi xây dựng các mạng hiệu năng cao vì nó tương ứng với số bit mà phía gửi phải truyền trước khi bit đầu tiên đến phía nhận. Nếu phía gửi mong đợi phía nhận báo hiệu rằng các bit đã bắt đầu đến, và tín hiệu này cũng mất một độ trễ kênh nữa để quay lại phía gửi, thì phía gửi có thể truyền tối đa một lượng dữ liệu bằng *RTT × băng thông* trước khi nhận được tín hiệu xác nhận mọi thứ ổn. Các bit trong ống được gọi là “đang bay” (in flight), nghĩa là nếu phía nhận bảo phía gửi dừng truyền, nó vẫn có thể nhận thêm tối đa một lượng dữ liệu bằng RTT × băng thông trước khi phía gửi kịp phản ứng. Trong ví dụ trên, lượng này tương ứng với 5,5 × 10\ :sup:`6` bit (671 KB) dữ liệu. Ngược lại, nếu phía gửi không lấp đầy ống—tức là không truyền đủ một lượng dữ liệu bằng tích số RTT × băng thông trước khi dừng lại chờ tín hiệu—thì phía gửi sẽ không tận dụng hết mạng.

Lưu ý rằng hầu hết thời gian chúng ta quan tâm đến trường hợp RTT, mà chúng tôi đơn giản gọi là tích số độ trễ × băng thông, mà không nói rõ “độ trễ” là RTT (tức là lấy độ trễ một chiều nhân đôi). Thông thường, việc “độ trễ” trong tích số độ trễ × băng thông là độ trễ một chiều hay RTT sẽ được làm rõ trong ngữ cảnh. :numref:`Bảng %s <tab-delay-bw>` cho thấy một số ví dụ về tích số RTT × băng thông cho một số liên kết mạng điển hình.

.. _tab-delay-bw:
.. table::  Một số ví dụ về tích số độ trễ × băng thông.
   :align: center
   :widths: auto

   +---------------------+-----------+------------------+---------+-----------------+
   | Loại liên kết       | Băng thông| Khoảng cách 1 chiều| RTT    | RTT x Băng thông|
   +=====================+===========+==================+=========+=================+
   | Wireless LAN        | 54 Mbps   | 50 m             | 0,33 μs | 18 bit          |
   +---------------------+-----------+------------------+---------+-----------------+
   | Vệ tinh             | 1 Gbps    | 35.000 km        | 230 ms  | 230 Mb          |
   +---------------------+-----------+------------------+---------+-----------------+
   | Cáp quang xuyên quốc| 10 Gbps   | 4.000 km         | 40 ms   | 400 Mb          |
   +---------------------+-----------+------------------+---------+-----------------+

1.5.3 Mạng tốc độ cao
---------------------

Sự gia tăng dường như không ngừng của băng thông khiến các nhà thiết kế mạng bắt đầu nghĩ về điều gì sẽ xảy ra trong giới hạn, hay nói cách khác, tác động lên thiết kế mạng khi có băng thông vô hạn.

Mặc dù các mạng tốc độ cao mang lại sự thay đổi lớn về băng thông có sẵn cho các ứng dụng, nhưng ở nhiều khía cạnh, tác động của chúng đến cách chúng ta nghĩ về mạng lại nằm ở chỗ *không* thay đổi khi băng thông tăng: tốc độ ánh sáng. Như Scotty trong *Star Trek* từng nói, “Bạn không thể thay đổi các định luật vật lý.” Nói cách khác, “tốc độ cao” không có nghĩa là độ trễ cũng được cải thiện cùng tốc độ với băng thông; RTT xuyên lục địa của một liên kết 1-Gbps vẫn là 100 ms như với liên kết 1-Mbps.

Để thấy được ý nghĩa của việc băng thông ngày càng tăng trong khi độ trễ cố định, hãy xem cần gì để truyền một tệp 1-MB qua mạng 1-Mbps so với mạng 1-Gbps, cả hai đều có RTT 100 ms. Với mạng 1-Mbps, cần 80 lần RTT để truyền tệp; trong mỗi RTT, truyền được 1,25% tệp. Ngược lại, cùng tệp 1-MB đó thậm chí không lấp đầy 1 RTT của liên kết 1-Gbps, vốn có tích số độ trễ × băng thông là 12,5 MB.

:numref:`Hình %s <fig-bw-lat>` minh họa sự khác biệt giữa hai mạng. Thực chất, tệp 1-MB giống như một dòng dữ liệu cần truyền qua mạng 1-Mbps, trong khi nó giống như một gói đơn lẻ trên mạng 1-Gbps. Để làm rõ hơn, hãy xem tệp 1-MB đối với mạng 1-Gbps cũng giống như một *gói* 1-KB đối với mạng 1-Mbps.

.. _fig-bw-lat:
.. figure:: figures/f01-19-9780123850591.png
   :width: 500px
   :align: center

   Mối quan hệ giữa băng thông và độ trễ. Một tệp 1-MB sẽ lấp đầy liên kết 1-Mbps 80 lần nhưng chỉ lấp đầy 1/12 liên kết 1-Gbps.

Một cách khác để nhìn nhận vấn đề là nhiều dữ liệu có thể được truyền trong mỗi RTT trên mạng tốc độ cao, đến mức một RTT trở thành một khoảng thời gian đáng kể. Do đó, trong khi bạn có thể không bận tâm về sự khác biệt giữa truyền tệp mất 101 RTT so với 100 RTT (chênh lệch chỉ 1%), thì đột nhiên sự khác biệt giữa 1 RTT và 2 RTT lại rất lớn—tăng 100%. Nói cách khác, độ trễ, thay vì thông lượng, bắt đầu chi phối cách chúng ta nghĩ về thiết kế mạng.

Có lẽ cách tốt nhất để hiểu mối quan hệ giữa thông lượng và độ trễ là quay lại những điều cơ bản. Thông lượng end-to-end hiệu dụng có thể đạt được trên một mạng được xác định bởi công thức đơn giản

.. centered:: Thông lượng = Kích thước truyền / Thời gian truyền

trong đó Thời gian truyền bao gồm không chỉ các thành phần một chiều đã nêu ở phần trước, mà còn cả thời gian bổ sung để yêu cầu hoặc thiết lập truyền. Thông thường, chúng tôi biểu diễn mối quan hệ này như sau

.. centered:: Thời gian truyền = RTT + 1/Băng thông × Kích thước truyền

Chúng tôi sử dụng trong phép tính này để tính cả thời gian gửi thông điệp yêu cầu qua mạng và dữ liệu được gửi lại. Ví dụ, giả sử người dùng muốn lấy một tệp 1-MB qua mạng 1-Gbps với RTT 100 ms. Điều này bao gồm cả thời gian truyền 1 MB (1 / 1 Gbps × 1 MB = 8 ms) và RTT 100 ms, tổng thời gian truyền là 108 ms. Điều này có nghĩa là thông lượng hiệu dụng sẽ là

.. centered:: 1 MB / 108 ms = 74,1 Mbps

không phải 1 Gbps. Rõ ràng, truyền lượng dữ liệu lớn hơn sẽ giúp cải thiện thông lượng hiệu dụng, trong giới hạn, khi kích thước truyền vô hạn thì thông lượng hiệu dụng sẽ tiệm cận băng thông mạng. Ngược lại, nếu phải chịu nhiều hơn 1 RTT—ví dụ, để truyền lại các gói bị mất—thì thông lượng hiệu dụng cho bất kỳ truyền nào có kích thước hữu hạn sẽ bị giảm và sẽ thấy rõ nhất với các truyền nhỏ.

1.5.4 Yêu cầu của ứng dụng
--------------------------

Phần thảo luận này đã xem xét hiệu năng dưới góc nhìn tập trung vào mạng; tức là, chúng ta nói về những gì một liên kết hoặc kênh nhất định có thể hỗ trợ. Giả định ngầm là các chương trình ứng dụng có nhu cầu đơn giản—chúng muốn càng nhiều băng thông càng tốt. Điều này chắc chắn đúng với chương trình thư viện số vừa đề cập, khi lấy một ảnh 250-MB; băng thông càng lớn, chương trình càng trả ảnh về cho người dùng nhanh hơn.

Tuy nhiên, một số ứng dụng có thể xác định giới hạn trên về lượng băng thông chúng cần. Ứng dụng video là ví dụ điển hình. Giả sử muốn truyền phát một video có kích thước bằng một phần tư màn hình TV tiêu chuẩn; tức là, độ phân giải 352 x 240 pixel. Nếu mỗi pixel được biểu diễn bằng 24 bit thông tin, như với màu 24-bit, thì kích thước mỗi khung hình là (352 × 240 × 24) / 8 = 247,5 KB. Nếu ứng dụng cần hỗ trợ tốc độ 30 khung hình/giây, thì có thể yêu cầu thông lượng 75 Mbps. Khả năng mạng cung cấp nhiều băng thông hơn không còn ý nghĩa với ứng dụng như vậy vì nó chỉ có từng đó dữ liệu cần truyền trong một khoảng thời gian nhất định.

Đáng tiếc, tình hình không đơn giản như ví dụ này gợi ý. Vì sự khác biệt giữa hai khung hình liên tiếp trong luồng video thường nhỏ, nên có thể nén video bằng cách chỉ truyền sự khác biệt giữa các khung hình. Mỗi khung hình cũng có thể được nén vì không phải mọi chi tiết trong ảnh đều được mắt người nhận biết. Video nén không chảy với tốc độ không đổi, mà thay đổi theo thời gian tùy vào các yếu tố như mức độ chuyển động, chi tiết trong ảnh và thuật toán nén sử dụng. Do đó, có thể nói về nhu cầu băng thông trung bình, nhưng tốc độ tức thời có thể cao hoặc thấp hơn.

Vấn đề then chốt là khoảng thời gian mà trung bình được tính. Giả sử ứng dụng video ví dụ này có thể nén xuống chỉ còn cần 2 Mbps trung bình. Nếu nó truyền 1 megabit trong 1 giây và 3 megabit trong giây tiếp theo, thì trong 2 giây nó truyền trung bình 2 Mbps; tuy nhiên, điều này không giúp ích gì cho một kênh chỉ được thiết kế để hỗ trợ tối đa 2 megabit trong bất kỳ giây nào. Rõ ràng, chỉ biết nhu cầu băng thông trung bình của ứng dụng là chưa đủ.

Tuy nhiên, nhìn chung, có thể đặt giới hạn trên về kích thước một đợt truyền (burst) mà ứng dụng như vậy có thể gửi. Một đợt truyền có thể được mô tả bằng tốc độ cực đại duy trì trong một khoảng thời gian nhất định. Hoặc, có thể mô tả là số byte có thể gửi ở tốc độ cực đại trước khi quay về tốc độ trung bình hoặc thấp hơn. Nếu tốc độ cực đại này vượt quá khả năng kênh, thì dữ liệu dư thừa sẽ phải được lưu vào bộ đệm ở đâu đó, để truyền sau. Biết được kích thước đợt truyền giúp nhà thiết kế mạng phân bổ đủ bộ đệm để chứa đợt truyền đó.

Tương tự như việc nhu cầu băng thông của ứng dụng có thể không phải là “bao nhiêu cũng được”, yêu cầu về độ trễ của ứng dụng cũng có thể phức tạp hơn chỉ là “càng ít càng tốt”. Với độ trễ, đôi khi điều quan trọng không phải là độ trễ một chiều của mạng là 100 ms hay 500 ms, mà là độ trễ thay đổi thế nào giữa các gói. Sự biến thiên độ trễ này gọi là *jitter*.

Hãy xem xét tình huống nguồn gửi một gói mỗi 33 ms, như với ứng dụng video truyền 30 khung hình/giây. Nếu các gói đến đích cách nhau đúng 33 ms, thì có thể suy ra độ trễ mà mỗi gói trải qua trong mạng là như nhau. Nếu khoảng cách giữa các lần gói đến đích—gọi là *khoảng cách giữa các gói* (inter-packet gap)—thay đổi, thì độ trễ mà chuỗi gói trải qua cũng thay đổi, và mạng được cho là đã gây ra jitter cho luồng gói, như minh họa trong :numref:`Hình %s <fig-jitter>`. Sự biến thiên này thường không xuất hiện trên một liên kết vật lý đơn lẻ, nhưng có thể xảy ra khi các gói trải qua các độ trễ xếp hàng khác nhau trong mạng chuyển mạch gói nhiều chặng. Độ trễ xếp hàng này tương ứng với thành phần độ trễ đã định nghĩa ở phần trước, và thay đổi theo thời gian.

.. _fig-jitter:
.. figure:: figures/f01-20-9780123850591.png
   :width: 600px
   :align: center

   Jitter do mạng gây ra.

Để hiểu ý nghĩa của jitter, giả sử các gói được truyền qua mạng chứa các khung hình video, và để hiển thị các khung hình này lên màn hình, phía nhận cần nhận được một khung hình mới mỗi 33 ms. Nếu một khung hình đến sớm, phía nhận chỉ cần lưu lại cho đến khi đến thời điểm hiển thị. Đáng tiếc, nếu một khung hình đến muộn, phía nhận sẽ không có khung hình cần thiết để cập nhật màn hình đúng lúc, và chất lượng video sẽ giảm; hình ảnh sẽ không mượt. Lưu ý rằng không cần thiết phải loại bỏ hoàn toàn jitter, chỉ cần biết nó tệ đến mức nào. Lý do là nếu phía nhận biết được giới hạn trên và dưới về độ trễ mà một gói có thể gặp, nó có thể trì hoãn thời điểm bắt đầu phát video (tức là hiển thị khung hình đầu tiên) đủ lâu để đảm bảo rằng về sau luôn có khung hình để hiển thị đúng lúc. Phía nhận trì hoãn khung hình, thực chất là làm mượt jitter, bằng cách lưu nó vào bộ đệm.