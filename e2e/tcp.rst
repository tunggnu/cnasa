5.2 Luồng Byte Đáng Tin Cậy (TCP)
=================================

Trái ngược với một giao thức phân kênh đơn giản như UDP, một giao thức vận chuyển tinh vi hơn là giao thức cung cấp dịch vụ luồng byte, kết nối, đáng tin cậy. Một dịch vụ như vậy đã chứng tỏ hữu ích đối với nhiều loại ứng dụng vì nó giúp ứng dụng không phải lo lắng về việc mất hoặc sắp xếp lại dữ liệu. Giao thức Điều khiển Truyền vận Internet (Transmission Control Protocol - TCP) có lẽ là giao thức được sử dụng rộng rãi nhất thuộc loại này; nó cũng là giao thức được điều chỉnh cẩn thận nhất. Vì hai lý do này mà phần này nghiên cứu TCP một cách chi tiết, mặc dù chúng tôi sẽ xác định và thảo luận các lựa chọn thiết kế thay thế ở cuối phần.

Xét về các thuộc tính của giao thức vận chuyển đã được nêu ra ở phần đầu chương này, TCP đảm bảo việc truyền luồng byte một cách đáng tin cậy, theo đúng thứ tự. Đây là một giao thức song công hoàn toàn, nghĩa là mỗi kết nối TCP hỗ trợ một cặp luồng byte, mỗi luồng đi theo một hướng. Nó cũng bao gồm một cơ chế điều khiển luồng cho mỗi luồng byte này, cho phép phía nhận giới hạn lượng dữ liệu mà phía gửi có thể truyền tại một thời điểm nhất định. Cuối cùng, giống như UDP, TCP hỗ trợ một cơ chế phân kênh cho phép nhiều chương trình ứng dụng trên bất kỳ máy chủ nào có thể đồng thời trò chuyện với các đối tác của chúng.

Ngoài các tính năng trên, TCP còn triển khai một cơ chế điều khiển tắc nghẽn được điều chỉnh rất kỹ lưỡng. Ý tưởng của cơ chế này là điều tiết tốc độ gửi dữ liệu của TCP, không phải để tránh phía gửi vượt quá khả năng của phía nhận, mà là để tránh phía gửi làm quá tải mạng. Việc mô tả cơ chế điều khiển tắc nghẽn của TCP sẽ được hoãn lại cho đến chương tiếp theo, nơi chúng tôi thảo luận về nó trong bối cảnh lớn hơn về cách tài nguyên mạng được phân bổ một cách công bằng.

Vì nhiều người thường nhầm lẫn giữa điều khiển tắc nghẽn và điều khiển luồng, chúng tôi xin nhắc lại sự khác biệt. *Điều khiển luồng* liên quan đến việc ngăn chặn phía gửi vượt quá khả năng của phía nhận. *Điều khiển tắc nghẽn* liên quan đến việc ngăn chặn quá nhiều dữ liệu được đưa vào mạng, dẫn đến các switch hoặc liên kết bị quá tải. Do đó, điều khiển luồng là vấn đề đầu-cuối, trong khi điều khiển tắc nghẽn liên quan đến cách các máy chủ và mạng tương tác với nhau.

5.2.1 Các Vấn Đề Đầu-Cuối
-------------------------

Trọng tâm của TCP là thuật toán cửa sổ trượt. Mặc dù đây là thuật toán cơ bản giống như thường được sử dụng ở tầng liên kết, nhưng vì TCP chạy trên Internet thay vì một liên kết vật lý điểm-điểm, nên có nhiều khác biệt quan trọng. Phần này xác định những khác biệt đó và giải thích cách chúng làm phức tạp TCP. Các phần tiếp theo sẽ mô tả cách TCP giải quyết những phức tạp này và các vấn đề khác.

Thứ nhất, trong khi thuật toán cửa sổ trượt ở tầng liên kết chạy trên một liên kết vật lý duy nhất luôn kết nối cùng hai máy tính, thì TCP hỗ trợ các kết nối logic giữa các tiến trình đang chạy trên bất kỳ hai máy tính nào trong Internet. Điều này có nghĩa là TCP cần một giai đoạn thiết lập kết nối rõ ràng, trong đó hai phía của kết nối đồng ý trao đổi dữ liệu với nhau. Sự khác biệt này tương tự như việc phải quay số cho bên kia, thay vì có một đường dây điện thoại chuyên dụng. TCP cũng có một giai đoạn kết thúc kết nối rõ ràng. Một trong những điều xảy ra trong quá trình thiết lập kết nối là hai bên thiết lập một số trạng thái chung để thuật toán cửa sổ trượt có thể bắt đầu. Việc kết thúc kết nối là cần thiết để mỗi máy chủ biết rằng có thể giải phóng trạng thái này.

Thứ hai, trong khi một liên kết vật lý duy nhất luôn kết nối cùng hai máy tính có thời gian khứ hồi (RTT) cố định, các kết nối TCP có thể có thời gian khứ hồi rất khác nhau. Ví dụ, một kết nối TCP giữa một máy chủ ở San Francisco và một máy chủ ở Boston, cách nhau hàng nghìn km, có thể có RTT là 100 ms, trong khi một kết nối TCP giữa hai máy chủ trong cùng một phòng, chỉ cách nhau vài mét, có thể có RTT chỉ 1 ms. Cùng một giao thức TCP phải hỗ trợ cả hai loại kết nối này. Tệ hơn nữa, kết nối TCP giữa các máy chủ ở San Francisco và Boston có thể có RTT là 100 ms lúc 3 giờ sáng, nhưng lại là 500 ms lúc 3 giờ chiều. Thậm chí, sự biến động về RTT có thể xảy ra ngay trong một kết nối TCP chỉ kéo dài vài phút. Điều này có nghĩa là đối với thuật toán cửa sổ trượt, cơ chế timeout kích hoạt việc truyền lại phải thích ứng được. (Chắc chắn, timeout cho một liên kết điểm-điểm phải là một tham số có thể thiết lập, nhưng không cần phải điều chỉnh bộ đếm thời gian này cho từng cặp nút cụ thể.)

Sự khác biệt thứ ba là các gói tin có thể bị sắp xếp lại khi đi qua Internet, điều này không thể xảy ra trên một liên kết điểm-điểm, nơi gói đầu tiên được đưa vào một đầu của liên kết phải là gói đầu tiên xuất hiện ở đầu kia. Các gói bị trễ nhẹ không gây ra vấn đề vì thuật toán cửa sổ trượt có thể sắp xếp lại các gói đúng thứ tự bằng cách sử dụng số thứ tự. Vấn đề thực sự là các gói có thể bị trễ đến mức nào, hay nói cách khác, một gói có thể đến đích muộn đến mức nào. Trong trường hợp xấu nhất, một gói có thể bị trì hoãn trong Internet cho đến khi trường thời gian sống (``TTL``) của IP hết hạn, lúc đó gói sẽ bị loại bỏ (và do đó không còn nguy cơ nó đến muộn). Biết rằng IP loại bỏ các gói sau khi ``TTL`` hết hạn, TCP giả định rằng mỗi gói có một thời gian sống tối đa. Thời gian sống chính xác, gọi là *thời gian sống tối đa của đoạn* (maximum segment lifetime - MSL), là một lựa chọn kỹ thuật. Giá trị khuyến nghị hiện tại là 120 giây. Lưu ý rằng IP không trực tiếp thực thi giá trị 120 giây này; nó chỉ là một ước lượng bảo thủ mà TCP đưa ra về thời gian một gói có thể tồn tại trong Internet. Hệ quả là TCP phải chuẩn bị cho trường hợp các gói rất cũ đột nhiên xuất hiện ở phía nhận, có thể gây nhầm lẫn cho thuật toán cửa sổ trượt.

Thứ tư, các máy tính kết nối với một liên kết điểm-điểm thường được thiết kế để hỗ trợ liên kết đó. Ví dụ, nếu tích số độ trễ × băng thông của một liên kết được tính là 8 KB—nghĩa là kích thước cửa sổ được chọn để cho phép tối đa 8 KB dữ liệu chưa được xác nhận tại một thời điểm—thì có khả năng các máy tính ở hai đầu liên kết đều có khả năng đệm tối đa 8 KB dữ liệu. Thiết kế hệ thống khác đi sẽ là ngớ ngẩn. Ngược lại, hầu như bất kỳ loại máy tính nào cũng có thể kết nối với Internet, khiến lượng tài nguyên dành cho mỗi kết nối TCP rất khác nhau, đặc biệt khi một máy chủ có thể hỗ trợ hàng trăm kết nối TCP cùng lúc. Điều này có nghĩa là TCP phải bao gồm một cơ chế để mỗi phía “học” được tài nguyên (ví dụ, dung lượng bộ đệm) mà phía kia có thể cung cấp cho kết nối. Đây chính là vấn đề điều khiển luồng.

Thứ năm, vì phía truyền của một liên kết kết nối trực tiếp không thể gửi nhanh hơn băng thông của liên kết cho phép, và chỉ có một máy chủ bơm dữ liệu vào liên kết, nên không thể vô tình làm tắc nghẽn liên kết. Nói cách khác, tải trên liên kết được thể hiện rõ ràng dưới dạng hàng đợi các gói ở phía gửi. Ngược lại, phía gửi của một kết nối TCP không biết các liên kết nào sẽ được đi qua để đến đích. Ví dụ, máy gửi có thể được kết nối trực tiếp với một Ethernet khá nhanh—và có khả năng gửi dữ liệu với tốc độ 10 Gbps—nhưng đâu đó ở giữa mạng lại có một liên kết 1,5 Mbps phải đi qua. Tệ hơn nữa, dữ liệu được tạo ra bởi nhiều nguồn khác nhau có thể cùng cố gắng đi qua liên kết chậm này. Điều này dẫn đến vấn đề tắc nghẽn mạng. Việc thảo luận về chủ đề này sẽ được hoãn lại cho đến chương tiếp theo.

Chúng tôi kết thúc phần thảo luận về các vấn đề đầu-cuối này bằng cách so sánh cách tiếp cận của TCP trong việc cung cấp dịch vụ truyền tin cậy/đúng thứ tự với cách tiếp cận được sử dụng bởi các mạng dựa trên mạch ảo như mạng X.25 quan trọng trong lịch sử. Trong TCP, mạng IP bên dưới được giả định là không đáng tin cậy và có thể truyền các thông điệp không theo thứ tự; TCP sử dụng thuật toán cửa sổ trượt trên cơ sở đầu-cuối để cung cấp truyền tin cậy/đúng thứ tự. Ngược lại, các mạng X.25 sử dụng giao thức cửa sổ trượt bên trong mạng, trên cơ sở từng chặng. Giả định đằng sau cách tiếp cận này là nếu các thông điệp được truyền tin cậy và đúng thứ tự giữa mỗi cặp nút trên đường đi từ máy chủ nguồn đến máy chủ đích, thì dịch vụ đầu-cuối cũng đảm bảo truyền tin cậy/đúng thứ tự.

Vấn đề với cách tiếp cận sau là một chuỗi các đảm bảo từng chặng không nhất thiết tạo thành một đảm bảo đầu-cuối. Thứ nhất, nếu một liên kết không đồng nhất (ví dụ, Ethernet) được thêm vào một đầu của đường đi, thì không có gì đảm bảo rằng chặng này sẽ duy trì cùng dịch vụ như các chặng khác. Thứ hai, chỉ vì giao thức cửa sổ trượt đảm bảo rằng các thông điệp được truyền đúng từ nút A đến nút B, rồi từ nút B đến nút C, không có nghĩa là nút B hoạt động hoàn hảo. Ví dụ, các nút mạng đã từng gây ra lỗi trong thông điệp khi chuyển chúng từ bộ đệm vào sang bộ đệm ra. Chúng cũng đã từng vô tình sắp xếp lại các thông điệp. Do những khoảng thời gian dễ bị tổn thương nhỏ này, vẫn cần phải cung cấp các kiểm tra đầu-cuối thực sự để đảm bảo dịch vụ truyền tin cậy/đúng thứ tự, ngay cả khi các tầng thấp hơn của hệ thống cũng triển khai chức năng đó.

.. _key-e2e:
.. admonition::  Bài Học Rút Ra

   Phần thảo luận này minh họa cho một trong những nguyên tắc quan trọng nhất trong thiết kế hệ thống—*lập luận đầu-cuối* (end-to-end argument). Tóm lại, lập luận đầu-cuối nói rằng một chức năng (trong ví dụ của chúng ta là cung cấp truyền tin cậy/đúng thứ tự) không nên được cung cấp ở các tầng thấp hơn của hệ thống trừ khi nó có thể được triển khai hoàn chỉnh và chính xác ở tầng đó. Do đó, quy tắc này ủng hộ cách tiếp cận TCP/IP. Tuy nhiên, quy tắc này không tuyệt đối. Nó cho phép các chức năng được cung cấp không hoàn chỉnh ở tầng thấp hơn như một tối ưu hóa hiệu năng. Đây là lý do tại sao hoàn toàn phù hợp với lập luận đầu-cuối khi thực hiện phát hiện lỗi (ví dụ, CRC) trên cơ sở từng chặng; phát hiện và truyền lại một gói bị lỗi qua một chặng tốt hơn là phải truyền lại toàn bộ tệp từ đầu-cuối.  :ref:`[Tiếp theo] <key-dumb-receiver>`

5.2.2 Định Dạng Đoạn
--------------------

TCP là một giao thức hướng byte, nghĩa là phía gửi ghi các byte vào một kết nối TCP và phía nhận đọc các byte ra khỏi kết nối TCP. Mặc dù “luồng byte” mô tả dịch vụ mà TCP cung cấp cho các tiến trình ứng dụng, bản thân TCP không truyền từng byte riêng lẻ qua Internet. Thay vào đó, TCP trên máy chủ nguồn đệm đủ số byte từ tiến trình gửi để lấp đầy một gói có kích thước hợp lý rồi gửi gói này đến đối tác của nó trên máy chủ đích. TCP trên máy chủ đích sau đó làm rỗng nội dung của gói vào bộ đệm nhận, và tiến trình nhận đọc từ bộ đệm này khi thuận tiện. Tình huống này được minh họa trong :numref:`Hình %s <fig-tcp-stream>`, trong đó, để đơn giản, chỉ cho thấy dữ liệu chảy theo một hướng. Hãy nhớ rằng, nói chung, một kết nối TCP hỗ trợ các luồng byte chảy theo cả hai hướng.

.. _fig-tcp-stream:
.. figure:: figures/f05-03-9780123850591.png
   :width: 500px
   :align: center

   Cách TCP quản lý một luồng byte.

Các gói được trao đổi giữa các đối tác TCP trong :numref:`Hình %s <fig-tcp-stream>` được gọi là *đoạn* (segment), vì mỗi gói mang một đoạn của luồng byte. Mỗi đoạn TCP chứa phần đầu được mô tả sơ đồ trong :numref:`Hình %s <fig-tcp-format>`. Ý nghĩa của hầu hết các trường này sẽ trở nên rõ ràng trong suốt phần này. Hiện tại, chúng tôi chỉ giới thiệu chúng.

.. _fig-tcp-format:
.. figure:: figures/f05-04-9780123850591.png
   :width: 400px
   :align: center

   Định dạng phần đầu TCP.

Trường ``SrcPort`` và ``DstPort`` xác định cổng nguồn và cổng đích, tương tự như trong UDP. Hai trường này, cộng với địa chỉ IP nguồn và đích, kết hợp lại để xác định duy nhất mỗi kết nối TCP. Nghĩa là, khóa phân kênh của TCP được xác định bởi bộ 4-tuple

.. code:: c

   (SrcPort, SrcIPAddr, DstPort, DstIPAddr)

Lưu ý rằng vì các kết nối TCP có thể được thiết lập và đóng, nên có thể một kết nối giữa một cặp cổng cụ thể được thiết lập, sử dụng để gửi và nhận dữ liệu, rồi đóng lại, và sau đó cùng cặp cổng đó lại được sử dụng cho một kết nối khác. Chúng tôi đôi khi gọi tình huống này là hai *phiên bản* khác nhau của cùng một kết nối.

Các trường ``Acknowledgement``, ``SequenceNum`` và ``AdvertisedWindow`` đều liên quan đến thuật toán cửa sổ trượt của TCP. Vì TCP là giao thức hướng byte, mỗi byte dữ liệu đều có một số thứ tự. Trường ``SequenceNum`` chứa số thứ tự của byte đầu tiên của dữ liệu được mang trong đoạn này, và các trường ``Acknowledgement`` và ``AdvertisedWindow`` mang thông tin về luồng dữ liệu đi theo hướng ngược lại. Để đơn giản hóa phần thảo luận, chúng tôi bỏ qua thực tế rằng dữ liệu có thể chảy theo cả hai hướng, và tập trung vào dữ liệu có một ``SequenceNum`` cụ thể chảy theo một hướng và các giá trị ``Acknowledgement`` và ``AdvertisedWindow`` chảy theo hướng ngược lại, như minh họa trong :numref:`Hình %s <fig-tcp-flow>`. Việc sử dụng ba trường này sẽ được mô tả đầy đủ hơn ở phần sau của chương.

.. _fig-tcp-flow:
.. figure:: figures/f05-05-9780123850591.png
   :width: 500px
   :align: center

   Minh họa đơn giản (chỉ cho thấy một hướng)
   của quá trình TCP, với dữ liệu chảy theo một hướng và ACK theo hướng ngược lại.

Trường ``Flags`` 6 bit được sử dụng để truyền thông tin điều khiển giữa các đối tác TCP. Các cờ có thể bao gồm ``SYN``, ``FIN``, ``RESET``, ``PUSH``, ``URG`` và ``ACK``. Cờ ``SYN`` và ``FIN`` được sử dụng khi thiết lập và kết thúc một kết nối TCP, tương ứng. Việc sử dụng chúng sẽ được mô tả ở phần sau. Cờ ``ACK`` được đặt bất cứ khi nào trường ``Acknowledgement`` hợp lệ, ngụ ý rằng phía nhận nên chú ý đến nó. Cờ ``URG`` cho biết đoạn này chứa dữ liệu khẩn cấp. Khi cờ này được đặt, trường ``UrgPtr`` chỉ ra nơi dữ liệu không khẩn cấp bắt đầu trong đoạn này. Dữ liệu khẩn cấp nằm ở đầu thân đoạn, lên đến và bao gồm giá trị ``UrgPtr`` byte trong đoạn. Cờ ``PUSH`` cho biết phía gửi đã gọi thao tác push, báo hiệu cho phía nhận của TCP rằng nó nên thông báo cho tiến trình nhận về điều này. Chúng tôi sẽ thảo luận thêm về hai tính năng cuối này ở phần sau. Cuối cùng, cờ ``RESET`` cho biết phía nhận đã bị nhầm lẫn—ví dụ, vì nó nhận được một đoạn mà nó không mong đợi—và muốn hủy kết nối.

Cuối cùng, trường ``Checksum`` được sử dụng giống hệt như trong UDP—nó được tính trên phần đầu TCP, dữ liệu TCP và pseudoheader, bao gồm địa chỉ nguồn, địa chỉ đích và trường độ dài từ phần đầu IP. Checksum là bắt buộc đối với TCP trong cả IPv4 và IPv6. Ngoài ra, vì phần đầu TCP có độ dài thay đổi (có thể gắn thêm các tùy chọn sau các trường bắt buộc), nên có trường ``HdrLen`` cho biết độ dài của phần đầu tính theo từ 32 bit. Trường này còn được gọi là trường ``Offset``, vì nó đo khoảng cách từ đầu gói đến đầu dữ liệu.

5.2.3 Thiết Lập và Kết Thúc Kết Nối
------------------------------------------------

Một kết nối TCP bắt đầu khi phía khách (gọi) thực hiện mở chủ động đến phía máy chủ (bị gọi). Giả sử phía máy chủ đã thực hiện mở bị động trước đó, hai phía sẽ trao đổi các thông điệp để thiết lập kết nối. (Nhớ lại từ Chương 1 rằng bên muốn khởi tạo kết nối thực hiện mở chủ động, còn bên sẵn sàng chấp nhận kết nối thực hiện mở bị động.\ [#]_) Chỉ sau khi giai đoạn thiết lập kết nối kết thúc, hai phía mới bắt đầu gửi dữ liệu. Tương tự, ngay khi một bên hoàn thành việc gửi dữ liệu, nó đóng một hướng của kết nối, khiến TCP bắt đầu một vòng trao đổi thông điệp kết thúc kết nối. Lưu ý rằng, trong khi thiết lập kết nối là hoạt động không đối xứng (một bên mở bị động và bên kia mở chủ động), thì kết thúc kết nối là đối xứng (mỗi bên phải đóng kết nối một cách độc lập). Do đó, có thể một bên đã đóng, nghĩa là nó không thể gửi dữ liệu nữa, nhưng bên kia vẫn giữ nửa còn lại của kết nối hai chiều mở và tiếp tục gửi dữ liệu.

.. [#] Chính xác hơn, TCP cho phép thiết lập kết nối đối xứng,
       với cả hai bên cố gắng mở kết nối cùng lúc,
       nhưng trường hợp phổ biến là một bên mở chủ động và bên kia mở bị động.

Bắt Tay Ba Bước
~~~~~~~~~~~~~~~

Thuật toán được TCP sử dụng để thiết lập và kết thúc kết nối gọi là *bắt tay ba bước* (three-way handshake). Chúng tôi sẽ mô tả thuật toán cơ bản trước, sau đó chỉ ra cách TCP sử dụng nó. Bắt tay ba bước bao gồm việc trao đổi ba thông điệp giữa phía khách và phía máy chủ, như minh họa trên dòng thời gian trong :numref:`Hình %s <fig-twh-timeline>`.

.. _fig-twh-timeline:
.. figure:: figures/f05-06-9780123850591.png
   :width: 400px
   :align: center

   Dòng thời gian cho thuật toán bắt tay ba bước.

Ý tưởng là hai bên muốn đồng ý về một tập hợp các tham số, trong trường hợp thiết lập kết nối TCP là các số thứ tự bắt đầu mà hai phía dự định sử dụng cho các luồng byte tương ứng của mình. Nói chung, các tham số có thể là bất kỳ thông tin nào mà mỗi bên muốn bên kia biết. Đầu tiên, phía khách (người chủ động) gửi một đoạn đến phía máy chủ (người bị động) thông báo số thứ tự ban đầu mà nó dự định sử dụng (``Flags`` = ``SYN``, ``SequenceNum`` = x). Phía máy chủ sau đó đáp lại bằng một đoạn duy nhất vừa xác nhận số thứ tự của phía khách (``Flags = ACK, Ack = x + 1``) vừa thông báo số thứ tự bắt đầu của chính nó (``Flags = SYN, SequenceNum = y``). Nghĩa là, cả hai bit ``SYN`` và ``ACK`` đều được đặt trong trường ``Flags`` của thông điệp thứ hai này. Cuối cùng, phía khách đáp lại bằng đoạn thứ ba xác nhận số thứ tự của phía máy chủ (``Flags = ACK, Ack = y + 1``). Lý do mỗi bên xác nhận một số thứ tự lớn hơn một đơn vị so với số đã gửi là vì trường ``Acknowledgement`` thực tế xác định “số thứ tự tiếp theo mong đợi”, qua đó ngầm xác nhận tất cả các số thứ tự trước đó. Mặc dù không được thể hiện trên dòng thời gian này, một bộ đếm thời gian được đặt cho mỗi trong hai đoạn đầu tiên, và nếu không nhận được phản hồi như mong đợi thì đoạn sẽ được truyền lại.

Bạn có thể tự hỏi tại sao phía khách và phía máy chủ phải trao đổi số thứ tự bắt đầu với nhau khi thiết lập kết nối. Sẽ đơn giản hơn nếu mỗi bên chỉ bắt đầu từ một số thứ tự “nổi tiếng”, chẳng hạn như 0. Thực tế, đặc tả TCP yêu cầu mỗi bên của kết nối chọn một số thứ tự bắt đầu ngẫu nhiên. Lý do là để bảo vệ chống lại hai phiên bản của cùng một kết nối sử dụng lại cùng số thứ tự quá sớm—tức là, khi vẫn còn khả năng một đoạn từ phiên bản trước của kết nối có thể gây nhiễu cho phiên bản sau của kết nối.

Sơ Đồ Chuyển Trạng Thái
~~~~~~~~~~~~~~~~~~~~~~~~

TCP đủ phức tạp để đặc tả của nó bao gồm một sơ đồ chuyển trạng thái. Một bản sao của sơ đồ này được đưa ra trong :numref:`Hình %s <fig-tcp-std>`. Sơ đồ này chỉ cho thấy các trạng thái liên quan đến việc mở kết nối (mọi thứ phía trên ESTABLISHED) và kết thúc kết nối (mọi thứ phía dưới ESTABLISHED). Mọi hoạt động diễn ra khi kết nối đang mở—tức là, hoạt động của thuật toán cửa sổ trượt—được ẩn trong trạng thái ESTABLISHED.

.. _fig-tcp-std:
.. figure:: figures/f05-07-9780123850591.png
   :width: 600px
   :align: center

   Sơ đồ chuyển trạng thái TCP.

Sơ đồ chuyển trạng thái của TCP khá dễ hiểu. Mỗi ô vuông biểu thị một trạng thái mà một đầu của kết nối TCP có thể gặp phải. Tất cả các kết nối bắt đầu ở trạng thái CLOSED. Khi kết nối tiến triển, nó di chuyển từ trạng thái này sang trạng thái khác theo các cung. Mỗi cung được gắn nhãn theo dạng *sự kiện/hành động*. Do đó, nếu một kết nối đang ở trạng thái LISTEN và nhận được một đoạn SYN (tức là một đoạn có cờ ``SYN`` được đặt), kết nối sẽ chuyển sang trạng thái SYN_RCVD và thực hiện hành động trả lời bằng một đoạn ``ACK+SYN``.

Lưu ý rằng có hai loại sự kiện kích hoạt chuyển trạng thái: (1) một đoạn đến từ đối tác (ví dụ, sự kiện trên cung từ LISTEN đến SYN_RCVD), hoặc (2) tiến trình ứng dụng cục bộ gọi một thao tác trên TCP (ví dụ, sự kiện *active open* trên cung từ CLOSED đến SYN_SENT). Nói cách khác, sơ đồ chuyển trạng thái của TCP về cơ bản xác định *ngữ nghĩa* của cả giao diện peer-to-peer và giao diện dịch vụ của nó. *Cú pháp* của hai giao diện này được xác định bởi định dạng đoạn (như minh họa trong :numref:`Hình %s <fig-tcp-format>`) và bởi một số giao diện lập trình ứng dụng, chẳng hạn như socket API.

Bây giờ hãy lần theo các chuyển đổi điển hình qua sơ đồ trong :numref:`Hình %s <fig-tcp-std>`. Hãy nhớ rằng ở mỗi đầu của kết nối, TCP thực hiện các chuyển đổi trạng thái khác nhau. Khi mở kết nối, phía máy chủ trước tiên gọi thao tác mở bị động trên TCP, khiến TCP chuyển sang trạng thái LISTEN. Sau đó, phía khách thực hiện mở chủ động, khiến đầu kết nối của nó gửi một đoạn SYN đến phía máy chủ và chuyển sang trạng thái SYN_SENT. Khi đoạn SYN đến phía máy chủ, nó chuyển sang trạng thái SYN_RCVD và đáp lại bằng một đoạn SYN+ACK. Sự xuất hiện của đoạn này khiến phía khách chuyển sang trạng thái ESTABLISHED và gửi một ACK trở lại phía máy chủ. Khi ACK này đến, phía máy chủ cuối cùng chuyển sang trạng thái ESTABLISHED. Nói cách khác, chúng ta vừa lần theo quá trình bắt tay ba bước.

Có ba điều cần lưu ý về nửa sơ đồ chuyển trạng thái liên quan đến thiết lập kết nối. Thứ nhất, nếu ACK của phía khách gửi cho phía máy chủ bị mất, tương ứng với bước thứ ba của bắt tay ba bước, thì kết nối vẫn hoạt động bình thường. Điều này là vì phía khách đã ở trạng thái ESTABLISHED, nên tiến trình ứng dụng cục bộ có thể bắt đầu gửi dữ liệu cho phía bên kia. Mỗi đoạn dữ liệu này sẽ có cờ ``ACK`` được đặt, và giá trị đúng trong trường ``Acknowledgement``, nên phía máy chủ sẽ chuyển sang trạng thái ESTABLISHED khi đoạn dữ liệu đầu tiên đến. Đây thực sự là một điểm quan trọng về TCP—mọi đoạn đều báo cáo số thứ tự mà phía gửi mong đợi nhận tiếp theo, ngay cả khi điều này lặp lại cùng số thứ tự đã có trong một hoặc nhiều đoạn trước đó.

Điều thứ hai cần lưu ý về sơ đồ chuyển trạng thái là có một chuyển đổi lạ ra khỏi trạng thái LISTEN bất cứ khi nào tiến trình cục bộ gọi thao tác *send* trên TCP. Tức là, có thể một bên bị động xác định cả hai đầu của kết nối (tức là, chính nó và phía đối tác mà nó sẵn sàng cho phép kết nối), rồi sau đó thay đổi ý định về việc chờ phía bên kia và thay vào đó chủ động thiết lập kết nối. Theo hiểu biết của chúng tôi, đây là một tính năng của TCP mà không tiến trình ứng dụng nào thực sự tận dụng.

Điều cuối cùng cần lưu ý về sơ đồ là các cung không được thể hiện. Cụ thể, hầu hết các trạng thái liên quan đến việc gửi một đoạn cho phía bên kia cũng đặt một bộ đếm thời gian mà cuối cùng sẽ khiến đoạn được gửi lại nếu không nhận được phản hồi như mong đợi. Các lần truyền lại này không được thể hiện trong sơ đồ chuyển trạng thái. Nếu sau nhiều lần thử mà không nhận được phản hồi như mong đợi, TCP sẽ từ bỏ và quay lại trạng thái CLOSED.

Chuyển sang quá trình kết thúc kết nối, điều quan trọng cần nhớ là tiến trình ứng dụng ở cả hai phía của kết nối phải độc lập đóng nửa kết nối của mình. Nếu chỉ một bên đóng kết nối, điều này có nghĩa là nó không còn dữ liệu để gửi, nhưng vẫn sẵn sàng nhận dữ liệu từ phía bên kia. Điều này làm phức tạp sơ đồ chuyển trạng thái vì nó phải tính đến khả năng cả hai bên gọi thao tác *close* cùng lúc, cũng như khả năng một bên gọi close trước, rồi sau đó bên kia mới gọi close. Do đó, ở mỗi phía có ba tổ hợp chuyển đổi đưa một kết nối từ trạng thái ESTABLISHED về trạng thái CLOSED:

-  Bên này đóng trước:
   ESTABLISHED :math:`\rightarrow`
   FIN_WAIT_1 :math:`\rightarrow` FIN_WAIT_2 :math:`\rightarrow` TIME_WAIT :math:`\rightarrow` CLOSED.

-  Bên kia đóng trước:
   ESTABLISHED :math:`\rightarrow`
   CLOSE_WAIT :math:`\rightarrow` LAST_ACK :math:`\rightarrow` CLOSED.

-  Cả hai bên đóng cùng lúc:
   ESTABLISHED :math:`\rightarrow`
   FIN_WAIT_1 :math:`\rightarrow` CLOSING :math:`\rightarrow` TIME_WAIT :math:`\rightarrow` CLOSED.

Thực tế còn có một chuỗi chuyển đổi thứ tư, mặc dù hiếm gặp, dẫn đến trạng thái CLOSED; nó đi theo cung từ FIN_WAIT_1 đến TIME_WAIT. Chúng tôi để bạn tự tìm hiểu xem tổ hợp hoàn cảnh nào dẫn đến khả năng thứ tư này.

Điều chính cần nhận ra về việc kết thúc kết nối là một kết nối ở trạng thái TIME_WAIT không thể chuyển sang trạng thái CLOSED cho đến khi nó đã chờ gấp đôi thời gian tối đa mà một datagram IP có thể tồn tại trong Internet (tức là 120 giây). Lý do là, mặc dù phía cục bộ đã gửi một ACK để đáp lại đoạn FIN của phía bên kia, nó không biết chắc rằng ACK đã được chuyển thành công. Do đó, phía bên kia có thể truyền lại đoạn FIN của nó, và đoạn FIN thứ hai này có thể bị trì hoãn trong mạng. Nếu kết nối được phép chuyển trực tiếp sang trạng thái CLOSED, thì một cặp tiến trình ứng dụng khác có thể đến và mở cùng một kết nối (tức là sử dụng cùng cặp số cổng), và đoạn FIN bị trì hoãn từ phiên bản trước của kết nối sẽ ngay lập tức khởi động việc kết thúc phiên bản sau của kết nối đó.

5.2.4 Thuật Toán Cửa Sổ Trượt Xét Lại
-------------------------------------

Chúng ta đã sẵn sàng để thảo luận về biến thể thuật toán cửa sổ trượt của TCP, phục vụ nhiều mục đích: (1) đảm bảo truyền dữ liệu đáng tin cậy, (2) đảm bảo dữ liệu được truyền đúng thứ tự, và (3) thực thi điều khiển luồng giữa phía gửi và phía nhận. Việc sử dụng thuật toán cửa sổ trượt của TCP giống như ở tầng liên kết đối với hai chức năng đầu tiên. Sự khác biệt của TCP so với thuật toán tầng liên kết là nó tích hợp luôn chức năng điều khiển luồng. Cụ thể, thay vì có một cửa sổ trượt kích thước cố định, phía nhận *quảng bá* kích thước cửa sổ cho phía gửi. Điều này được thực hiện bằng trường ``AdvertisedWindow`` trong phần đầu TCP. Phía gửi bị giới hạn chỉ được phép có tối đa ``AdvertisedWindow`` byte dữ liệu chưa được xác nhận tại bất kỳ thời điểm nào. Phía nhận chọn giá trị phù hợp cho ``AdvertisedWindow`` dựa trên lượng bộ nhớ được phân bổ cho kết nối để đệm dữ liệu. Ý tưởng là giữ cho phía gửi không vượt quá bộ đệm của phía nhận. Chúng tôi sẽ thảo luận kỹ hơn về vấn đề này ở phần dưới.

Truyền Đáng Tin Cậy và Đúng Thứ Tự
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Để thấy cách phía gửi và phía nhận của TCP tương tác với nhau để triển khai truyền đáng tin cậy và đúng thứ tự, hãy xem xét tình huống minh họa trong :numref:`Hình %s <fig-tcp-fc>`. TCP ở phía gửi duy trì một bộ đệm gửi. Bộ đệm này được sử dụng để lưu trữ dữ liệu đã gửi nhưng chưa được xác nhận, cũng như dữ liệu đã được tiến trình gửi ghi nhưng chưa được truyền. Ở phía nhận, TCP duy trì một bộ đệm nhận. Bộ đệm này lưu trữ dữ liệu đến không theo thứ tự, cũng như dữ liệu đến đúng thứ tự (tức là không còn byte nào bị thiếu trước đó trong luồng) nhưng tiến trình ứng dụng chưa kịp đọc.

.. _fig-tcp-fc:
.. figure:: figures/f05-08-9780123850591.png
   :width: 500px
   :align: center

   Mối quan hệ giữa bộ đệm gửi TCP (a) và bộ đệm nhận (b).

Để phần thảo luận sau đây dễ theo dõi hơn, ban đầu chúng tôi bỏ qua thực tế rằng cả bộ đệm và số thứ tự đều có kích thước hữu hạn và do đó cuối cùng sẽ bị tràn (wrap around). Ngoài ra, chúng tôi không phân biệt giữa một con trỏ vào bộ đệm nơi một byte dữ liệu cụ thể được lưu trữ và số thứ tự của byte đó.

Xét phía gửi trước, ba con trỏ được duy trì vào bộ đệm gửi, mỗi con trỏ có ý nghĩa rõ ràng: ``LastByteAcked``, ``LastByteSent`` và ``LastByteWritten``. Rõ ràng,

::

   LastByteAcked <= LastByteSent

vì phía nhận không thể xác nhận một byte chưa được gửi, và

::

   LastByteSent <= LastByteWritten

vì TCP không thể gửi một byte mà tiến trình ứng dụng chưa ghi. Ngoài ra, không cần lưu các byte bên trái ``LastByteAcked`` trong bộ đệm vì chúng đã được xác nhận, và không cần lưu các byte bên phải ``LastByteWritten`` vì chúng chưa được tạo ra.

Một tập hợp con trỏ (số thứ tự) tương tự được duy trì ở phía nhận: ``LastByteRead``, ``NextByteExpected`` và ``LastByteRcvd``. Các bất đẳng thức ở đây ít trực quan hơn, do vấn đề truyền không theo thứ tự. Mối quan hệ đầu tiên

::

   LastByteRead < NextByteExpected

là đúng vì một byte không thể được tiến trình ứng dụng đọc cho đến khi nó đã được nhận *và* tất cả các byte trước đó cũng đã được nhận. ``NextByteExpected`` trỏ đến byte ngay sau byte mới nhất đáp ứng tiêu chí này. Thứ hai,

::

   NextByteExpected <= LastByteRcvd + 1

vì nếu dữ liệu đến đúng thứ tự, ``NextByteExpected`` trỏ đến byte sau ``LastByteRcvd``, còn nếu dữ liệu đến không theo thứ tự, thì ``NextByteExpected`` trỏ đến đầu của khoảng trống đầu tiên trong dữ liệu, như trong :numref:`Hình %s <fig-tcp-fc>`. Lưu ý rằng các byte bên trái ``LastByteRead`` không cần lưu trong bộ đệm vì chúng đã được tiến trình ứng dụng đọc, và các byte bên phải ``LastByteRcvd`` không cần lưu vì chúng chưa đến.

Điều Khiển Luồng
~~~~~~~~~~~~~~~~

Hầu hết phần thảo luận trên giống với thuật toán cửa sổ trượt tiêu chuẩn; điểm khác biệt duy nhất là lần này chúng tôi nhấn mạnh việc các tiến trình ứng dụng gửi và nhận đang lấp đầy và làm rỗng bộ đệm cục bộ của chúng. (Phần thảo luận trước đó đã bỏ qua thực tế rằng dữ liệu đến từ một nút phía trên đang lấp đầy bộ đệm gửi và dữ liệu được truyền đến một nút phía dưới đang làm rỗng bộ đệm nhận.)

Bạn nên chắc chắn rằng mình hiểu phần này trước khi tiếp tục, vì bây giờ là lúc hai thuật toán khác biệt rõ rệt hơn. Trong phần tiếp theo, chúng tôi đưa trở lại thực tế rằng cả hai bộ đệm đều có kích thước hữu hạn, ký hiệu là ``MaxSendBuffer`` và ``MaxRcvBuffer``, mặc dù chúng tôi không quan tâm đến chi tiết cách chúng được triển khai. Nói cách khác, chúng tôi chỉ quan tâm đến số byte được lưu trữ trong bộ đệm, không phải vị trí lưu trữ cụ thể.

Nhớ lại rằng trong giao thức cửa sổ trượt, kích thước cửa sổ xác định lượng dữ liệu có thể được gửi mà không cần chờ xác nhận từ phía nhận. Do đó, phía nhận điều tiết phía gửi bằng cách quảng bá một cửa sổ không lớn hơn lượng dữ liệu mà nó có thể đệm. Lưu ý rằng TCP ở phía nhận phải giữ

::

   LastByteRcvd - LastByteRead <= MaxRcvBuffer

để tránh tràn bộ đệm. Do đó, nó quảng bá kích thước cửa sổ là

::

   AdvertisedWindow = MaxRcvBuffer - ((NextByteExpected - 1) - LastByteRead)

đại diện cho lượng không gian còn trống trong bộ đệm. Khi dữ liệu đến, phía nhận xác nhận nó miễn là tất cả các byte trước đó cũng đã đến. Ngoài ra, ``LastByteRcvd`` tăng lên, nghĩa là cửa sổ quảng bá có thể bị thu hẹp lại. Việc nó có bị thu hẹp hay không phụ thuộc vào tốc độ tiến trình ứng dụng cục bộ tiêu thụ dữ liệu. Nếu tiến trình cục bộ đọc dữ liệu nhanh như tốc độ dữ liệu đến (khiến ``LastByteRead`` tăng cùng tốc độ với ``LastByteRcvd``), thì cửa sổ quảng bá vẫn mở (tức là ``AdvertisedWindow = MaxRcvBuffer``). Tuy nhiên, nếu tiến trình nhận bị chậm, có thể vì nó thực hiện một thao tác tốn kém trên mỗi byte dữ liệu đọc được, thì cửa sổ quảng bá sẽ nhỏ dần với mỗi đoạn đến, cho đến khi cuối cùng về 0.

TCP ở phía gửi phải tuân thủ cửa sổ quảng bá mà nó nhận được từ phía nhận. Điều này có nghĩa là tại bất kỳ thời điểm nào, nó phải đảm bảo rằng

::

   LastByteSent - LastByteAcked <= AdvertisedWindow

Nói cách khác, phía gửi tính toán một *cửa sổ hiệu dụng* giới hạn lượng dữ liệu nó có thể gửi:

::

   EffectiveWindow = AdvertisedWindow - (LastByteSent - LastByteAcked)

Rõ ràng, ``EffectiveWindow`` phải lớn hơn 0 thì phía nguồn mới có thể gửi thêm dữ liệu. Do đó, có thể xảy ra trường hợp một đoạn đến xác nhận x byte, cho phép phía gửi tăng ``LastByteAcked`` lên x, nhưng vì tiến trình nhận không đọc dữ liệu, cửa sổ quảng bá bây giờ nhỏ hơn x byte so với trước đó. Trong trường hợp này, phía gửi có thể giải phóng không gian bộ đệm, nhưng không thể gửi thêm dữ liệu.

Trong khi tất cả điều này đang diễn ra, phía gửi cũng phải đảm bảo rằng tiến trình ứng dụng cục bộ không làm tràn bộ đệm gửi—tức là,

::

   LastByteWritten - LastByteAcked <= MaxSendBuffer

Nếu tiến trình gửi cố gắng ghi y byte vào TCP, nhưng

::

   (LastByteWritten - LastByteAcked) + y > MaxSendBuffer

thì TCP sẽ chặn tiến trình gửi và không cho phép nó tạo thêm dữ liệu.

Bây giờ có thể hiểu được cách một tiến trình nhận chậm cuối cùng sẽ dừng một tiến trình gửi nhanh. Đầu tiên, bộ đệm nhận đầy, khiến cửa sổ quảng bá thu hẹp về 0. Một cửa sổ quảng bá bằng 0 nghĩa là phía gửi không thể truyền thêm dữ liệu, ngay cả khi dữ liệu trước đó đã được xác nhận thành công. Cuối cùng, không thể truyền thêm dữ liệu nghĩa là bộ đệm gửi đầy, điều này cuối cùng khiến TCP chặn tiến trình gửi. Ngay khi tiến trình nhận bắt đầu đọc dữ liệu trở lại, TCP phía nhận có thể mở lại cửa sổ, cho phép TCP phía gửi truyền dữ liệu ra khỏi bộ đệm của nó. Khi dữ liệu này cuối cùng được xác nhận, ``LastByteAcked`` tăng lên, không gian bộ đệm chứa dữ liệu đã xác nhận trở nên trống, và tiến trình gửi được bỏ chặn và tiếp tục.

Chỉ còn một chi tiết cần giải quyết—làm thế nào phía gửi biết rằng cửa sổ quảng bá không còn bằng 0? Như đã đề cập ở trên, TCP *luôn* gửi một đoạn để đáp lại một đoạn dữ liệu nhận được, và phản hồi này chứa các giá trị mới nhất cho các trường ``Acknowledge`` và ``AdvertisedWindow``, ngay cả khi các giá trị này không thay đổi so với lần gửi trước. Vấn đề là như sau. Khi phía nhận đã quảng bá cửa sổ bằng 0, phía gửi không được phép gửi thêm dữ liệu, nghĩa là nó không có cách nào để phát hiện rằng cửa sổ quảng bá không còn bằng 0 vào một thời điểm nào đó trong tương lai. TCP phía nhận không tự động gửi các đoạn không chứa dữ liệu; nó chỉ gửi chúng để đáp lại một đoạn dữ liệu đến.

TCP xử lý tình huống này như sau. Bất cứ khi nào phía bên kia quảng bá cửa sổ bằng 0, phía gửi vẫn tiếp tục gửi một đoạn chứa 1 byte dữ liệu sau một khoảng thời gian nhất định. Nó biết rằng dữ liệu này có thể sẽ không được chấp nhận, nhưng vẫn thử, vì mỗi đoạn 1 byte này sẽ kích hoạt một phản hồi chứa giá trị cửa sổ quảng bá hiện tại. Cuối cùng, một trong các đoạn thăm dò 1 byte này sẽ nhận được phản hồi báo cáo cửa sổ quảng bá khác 0.

Lưu ý rằng các thông điệp 1 byte này được gọi là *Zero Window Probes* và trên thực tế chúng được gửi mỗi 5 đến 60 giây. Về byte dữ liệu nào sẽ được gửi trong thăm dò: đó là byte dữ liệu thực tiếp theo nằm ngoài cửa sổ. (Nó phải là dữ liệu thực phòng trường hợp phía nhận chấp nhận nó.)

.. _key-dumb-receiver:
.. admonition::  Bài Học Rút Ra

   Lưu ý rằng lý do phía gửi định kỳ gửi đoạn thăm dò này là vì TCP được thiết kế để phía nhận càng đơn giản càng tốt—nó chỉ đơn giản phản hồi các đoạn từ phía gửi, và không bao giờ tự khởi tạo bất kỳ hoạt động nào. Đây là một ví dụ về một quy tắc thiết kế giao thức được công nhận rộng rãi (mặc dù không phải lúc nào cũng được áp dụng), mà chúng tôi, do chưa có tên hay hơn, gọi là quy tắc *người gửi thông minh/người nhận đơn giản* (smart sender/dumb receiver). Hãy nhớ rằng chúng ta đã thấy một ví dụ khác về quy tắc này khi thảo luận về việc sử dụng NAK trong thuật toán cửa sổ trượt. :ref:`[Tiếp theo] <key-open-source>`

Bảo Vệ Chống Tràn Số Thứ Tự
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Phần này và phần tiếp theo xem xét kích thước của các trường ``SequenceNum`` và ``AdvertisedWindow`` và ý nghĩa của chúng đối với tính đúng đắn và hiệu năng của TCP. Trường ``SequenceNum`` của TCP dài 32 bit, và trường ``AdvertisedWindow`` dài 16 bit, nghĩa là TCP dễ dàng đáp ứng yêu cầu của thuật toán cửa sổ trượt rằng không gian số thứ tự phải lớn gấp đôi kích thước cửa sổ: 2\ :sup:`32` >> 2 × 2\ :sup:`16`. Tuy nhiên, yêu cầu này không phải là điều thú vị về hai trường này. Hãy xem xét từng trường một.

Ý nghĩa của không gian số thứ tự 32 bit là số thứ tự được sử dụng trên một kết nối có thể bị tràn—một byte có số thứ tự S có thể được gửi tại một thời điểm, rồi sau đó một byte thứ hai có cùng số thứ tự S lại được gửi. Một lần nữa, chúng tôi giả định rằng các gói không thể tồn tại trong Internet lâu hơn giá trị MSL khuyến nghị. Do đó, hiện tại chúng ta cần đảm bảo rằng số thứ tự không bị tràn trong vòng 120 giây. Việc điều này có xảy ra hay không phụ thuộc vào tốc độ truyền dữ liệu qua Internet—tức là, tốc độ tiêu thụ không gian số thứ tự 32 bit. (Phần thảo luận này giả định rằng chúng ta đang cố gắng tiêu thụ không gian số thứ tự nhanh nhất có thể, nhưng thực tế sẽ như vậy nếu chúng ta làm tốt việc giữ cho đường truyền luôn đầy.) :numref:`Bảng %s <tab-eqnum>` cho thấy thời gian để không gian số thứ tự 32 bit bị tràn trên các mạng có băng thông khác nhau.

.. _tab-eqnum:
.. table::  Thời Gian Để Không Gian Số Thứ Tự 32 Bit Bị Tràn.
   :align: center
   :widths: auto

   +--------------------------+-----------------------+
   | Băng thông               | Thời gian đến khi tràn|
   +==========================+=======================+
   | T1 (1.5 Mbps)            | 6.4 giờ               |
   +--------------------------+-----------------------+
   | T3 (45 Mbps)             | 13 phút               |
   +--------------------------+-----------------------+
   | Fast Ethernet (100 Mbps) | 6 phút                |
   +--------------------------+-----------------------+
   | OC-3 (155 Mbps)          | 4 phút                |
   +--------------------------+-----------------------+
   | OC-48 (2.5 Gbps)         | 14 giây               |
   +--------------------------+-----------------------+
   | OC-192 (10 Gbps)         | 3 giây                |
   +--------------------------+-----------------------+
   | 10GigE (10 Gbps)         | 3 giây                |
   +--------------------------+-----------------------+

Như bạn thấy, không gian số thứ tự 32 bit là đủ ở các băng thông vừa phải, nhưng vì các liên kết OC-192 hiện đã phổ biến trong xương sống Internet, và hầu hết các máy chủ hiện đều có giao diện Ethernet 10Gig (hoặc 10 Gbps), chúng ta đã vượt xa điểm mà 32 bit là quá nhỏ. May mắn thay, IETF đã đưa ra một phần mở rộng cho TCP giúp mở rộng hiệu quả không gian số thứ tự để bảo vệ chống lại việc số thứ tự bị tràn. Phần mở rộng này và các phần mở rộng liên quan sẽ được mô tả ở phần sau.

Giữ Cho Đường Truyền Luôn Đầy
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Ý nghĩa của trường ``AdvertisedWindow`` 16 bit là nó phải đủ lớn để cho phép phía gửi giữ cho đường truyền luôn đầy. Rõ ràng, phía nhận có thể không mở cửa sổ lớn như trường ``AdvertisedWindow`` cho phép; chúng tôi quan tâm đến trường hợp phía nhận có đủ bộ đệm để xử lý lượng dữ liệu lớn nhất mà trường ``AdvertisedWindow`` cho phép.

Trong trường hợp này, không chỉ băng thông mạng mà tích số độ trễ x băng thông mới quyết định trường ``AdvertisedWindow`` cần lớn đến mức nào—cửa sổ cần được mở đủ lớn để cho phép truyền một lượng dữ liệu bằng tích số độ trễ × băng thông. Giả sử RTT là 100 ms (một con số điển hình cho một kết nối xuyên quốc gia ở Hoa Kỳ), :numref:`Bảng %s <tab-adv-win>` cho biết tích số độ trễ × băng thông cho một số công nghệ mạng.

.. _tab-adv-win:
.. table::  Kích Thước Cửa Sổ Yêu Cầu Cho RTT 100 ms
   :align: center
   :widths: auto

   +--------------------------+-----------------------------+
   | Băng thông               | Tích số độ trễ × băng thông |
   +==========================+=============================+
   | T1 (1.5 Mbps)            | 18 KB                       |
   +--------------------------+-----------------------------+
   | T3 (45 Mbps)             | 549 KB                      |
   +--------------------------+-----------------------------+
   | Fast Ethernet (100 Mbps) | 1.2 MB                      |
   +--------------------------+-----------------------------+
   | OC-3 (155 Mbps)          | 1.8 MB                      |
   +--------------------------+-----------------------------+
   | OC-48 (2.5 Gbps)         | 29.6 MB                     |
   +--------------------------+-----------------------------+
   | OC-192 (10 Gbps)         | 118.4 MB                    |
   +--------------------------+-----------------------------+
   | 10GigE (10 Gbps)         | 118.4 MB                    |
   +--------------------------+-----------------------------+

Như bạn thấy, trường ``AdvertisedWindow`` của TCP còn tệ hơn cả trường ``SequenceNum``—nó không đủ lớn để xử lý ngay cả một kết nối T3 xuyên quốc gia Hoa Kỳ, vì một trường 16 bit chỉ cho phép quảng bá cửa sổ tối đa 64 KB. Chính phần mở rộng TCP đã đề cập ở trên cung cấp một cơ chế để tăng hiệu quả kích thước cửa sổ quảng bá.

5.2.5 Kích hoạt truyền tải
--------------------------

Tiếp theo, chúng ta xem xét một vấn đề khá tinh tế: làm thế nào TCP quyết định truyền một đoạn dữ liệu. Như đã mô tả trước đó, TCP hỗ trợ một trừu tượng luồng byte; nghĩa là, các chương trình ứng dụng ghi các byte vào luồng, và nhiệm vụ của TCP là quyết định xem đã có đủ byte để gửi một đoạn dữ liệu hay chưa. Những yếu tố nào quyết định được sự lựa chọn này?

Nếu chúng ta bỏ qua khả năng kiểm soát luồng—tức là, giả sử cửa sổ mở hoàn toàn, như sẽ xảy ra khi một kết nối mới bắt đầu—thì TCP có ba cơ chế để kích hoạt việc truyền một đoạn dữ liệu. Thứ nhất, TCP duy trì một biến, thường được gọi là *kích thước đoạn tối đa* (``MSS``), và nó gửi một đoạn ngay khi nó đã thu thập được ``MSS`` byte từ tiến trình gửi. ``MSS`` thường được đặt bằng kích thước của đoạn lớn nhất mà TCP có thể gửi mà không làm phân mảnh IP cục bộ. Tức là, ``MSS`` được đặt bằng đơn vị truyền tối đa (MTU) của mạng kết nối trực tiếp, trừ đi kích thước của các header TCP và IP. Cơ chế thứ hai kích hoạt TCP truyền một đoạn là tiến trình gửi đã yêu cầu rõ ràng việc gửi. Cụ thể, TCP hỗ trợ một thao tác *push*, và tiến trình gửi gọi thao tác này nhằm xả sạch bộ đệm các byte chưa gửi. Cơ chế kích hoạt cuối cùng cho việc truyền một đoạn là khi một bộ đếm thời gian (timer) búng; đoạn dữ liệu kết quả chứa số byte hiện có trong bộ đệm truyền tải. Tuy nhiên, như chúng ta sẽ sớm thấy, “timer” này không hoàn toàn như bạn mong đợi.

Syndrome Cửa sổ Ngớ Ngẩn
~~~~~~~~~~~~~~~~~~~~~~~~~

Dĩ nhiên, chúng ta không thể bỏ qua khả năng kiểm soát luồng, vốn đóng vai trò rõ ràng trong việc hạn chế người gửi. Nếu người gửi có ``MSS`` byte dữ liệu cần gửi và cửa sổ mở đủ lớn ít nhất bằng như vậy, thì người gửi sẽ truyền một đoạn đầy đủ. Tuy nhiên, giả sử rằng người gửi đang tích lũy byte để gửi, nhưng cửa sổ hiện đang đóng. Bây giờ, giả sử một ACK đến mở cửa sổ đủ cho người gửi truyền, ví dụ, ``MSS/2`` byte. Liệu người gửi có nên truyền một đoạn nửa đầy hay nên chờ cho đến khi cửa sổ mở đủ một ``MSS``? Đặc tả ban đầu đã im lặng về điểm này, và các triển khai TCP sớm đã quyết định tiếp tục và truyền một đoạn nửa đầy. Rốt cuộc, không có cách nào biết được sau bao lâu cửa sổ sẽ mở rộng thêm.

Hóa ra, chiến lược chủ động tận dụng bất kỳ cửa sổ nào sẵn có dẫn đến tình trạng được gọi là *hội chứng cửa sổ ngớ ngẩn*. :numref:`Figure %s <fig-sillywindow>` giúp hình dung những gì xảy ra. Nếu bạn nghĩ về một luồng TCP như một băng chuyền với các thùng “đầy” (các đoạn dữ liệu) đi theo một hướng và thùng rỗng (ACK) đi ngược lại, thì các đoạn có kích thước ``MSS`` tương ứng với các thùng lớn và các đoạn 1 byte tương ứng với các thùng rất nhỏ. Miễn là người gửi gửi các đoạn có kích thước ``MSS`` và người nhận ACK ít nhất một ``MSS`` dữ liệu mỗi lần, mọi thứ đều hoạt động tốt (:numref:`Figure %s(a) <fig-sillywindow>`). Nhưng, nếu người nhận phải giảm cửa sổ, khiến một lúc nào đó người gửi không thể gửi đủ một ``MSS`` dữ liệu, thì nếu người gửi ngay lập tức lấp đầy một thùng nhỏ hơn ``MSS`` khi nó có thể, thì người nhận sẽ ACK với số byte nhỏ hơn, dẫn đến việc thùng nhỏ đó được đưa vào hệ thống và tiếp tục lưu động mãi mãi. Tức là, nó được lấp đầy và rỗng đi ngay tại mỗi đầu mà không bao giờ được hợp nhất với các thùng liền kề để tạo thành các thùng lớn hơn, như trong :numref:`Figure %s(b) <fig-sillywindow>`. Tình huống này đã được phát hiện khi các triển khai TCP sớm thường xuyên tự thấy mình lấp đầy mạng với các đoạn rất nhỏ.

.. _fig-sillywindow:
.. figure:: figures/f05-09-9780123850591.png
   :width: 500px
   :align: center

   Hội chứng cửa sổ ngớ ngẩn. (a) Miễn là người gửi gửi các đoạn có kích thước MSS và người nhận ACK một MSS mỗi lần, hệ thống hoạt động trôi chảy. (b) Ngay khi người gửi gửi ít hơn một MSS, hoặc người nhận ACK ít hơn một MSS, một “thùng” nhỏ được đưa vào hệ thống và tiếp tục lưu động.

Lưu ý rằng hội chứng cửa sổ ngớ ngẩn chỉ là vấn đề khi người gửi truyền một đoạn nhỏ hoặc người nhận mở cửa sổ với lượng nhỏ. Nếu không có trường hợp nào xảy ra, thì thùng nhỏ sẽ không bao giờ được đưa vào luồng. Không thể cấm việc gửi các đoạn nhỏ; ví dụ, ứng dụng có thể thực hiện một thao tác *push* sau khi gửi chỉ một byte. Tuy nhiên, có thể ngăn người nhận đưa vào một thùng nhỏ (tức là, một cửa sổ mở nhỏ) bằng cách quy định rằng sau khi quảng bá cửa sổ 0, người nhận phải chờ có đủ không gian bằng một ``MSS`` trước khi quảng bá cửa sổ mở.

Nagle’s Algorithm
~~~~~~~~~~~~~~~~~~

Quay trở lại người gửi TCP, nếu có dữ liệu để gửi nhưng cửa sổ hiện mở nhỏ hơn ``MSS``, thì ta có thể muốn chờ một khoảng thời gian trước khi gửi dữ liệu hiện có, nhưng câu hỏi đặt ra là chờ bao lâu? Nếu chờ quá lâu, các ứng dụng tương tác như Telnet sẽ bị ảnh hưởng; nếu không chờ đủ lâu, ta có nguy cơ gửi nhiều gói nhỏ và rơi vào hội chứng cửa sổ ngớ ngẩn. Câu trả lời là giới thiệu một bộ đếm thời gian (timer) và truyền dữ liệu khi bộ đếm kết thúc.

Trong khi ta có thể sử dụng bộ đếm thời gian dựa trên đồng hồ — ví dụ, búng mỗi 100 ms — Nagle đã giới thiệu một giải pháp *tự đồng bộ* (self-clocking) tinh tế. Ý tưởng là miễn là TCP có bất kỳ dữ liệu nào đang được truyền, người gửi cuối cùng sẽ nhận được một ACK. ACK này có thể được coi như kích hoạt của timer, kích hoạt việc truyền thêm dữ liệu. Thuật toán Nagle cung cấp một quy tắc đơn giản, thống nhất cho việc quyết định khi nào truyền:

::

   Khi ứng dụng tạo ra dữ liệu để gửi
       nếu cả dữ liệu có sẵn và cửa sổ >= MSS
           gửi một đoạn đầy đủ
       else
           nếu có dữ liệu chưa được ACK đang được truyền
               lưu trữ dữ liệu mới cho đến khi có ACK đến
           else
               gửi tất cả dữ liệu mới ngay lập tức

Nói cách khác, luôn được phép gửi một đoạn đầy đủ nếu cửa sổ cho phép. Cũng được phép gửi ngay một lượng dữ liệu nhỏ nếu không có đoạn nào đang truyền, nhưng nếu có bất cứ dữ liệu nào đang trong chuyến bay, người gửi phải đợi cho đến khi nhận được ACK trước khi truyền đoạn tiếp theo. Do đó, một ứng dụng tương tác như Telnet, khi liên tục gửi một byte một lần, sẽ gửi dữ liệu với tốc độ một đoạn mỗi RTT. Một số đoạn sẽ chứa chỉ một byte, trong khi những đoạn khác có thể chứa nhiều byte tùy thuộc vào tốc độ gõ của người dùng trong một thời gian đi-đi về. Vì một số ứng dụng không thể chấp nhận độ trễ như vậy cho mỗi lần ghi vào kết nối TCP, giao diện socket cho phép ứng dụng tắt thuật toán Nagle bằng cách đặt tùy chọn ``TCP_NODELAY``. Việc đặt tùy chọn này có nghĩa là dữ liệu sẽ được truyền đi ngay lập tức.

5.2.6 Truyền lại Thích ứng
-----------------------------

Bởi vì TCP đảm bảo việc truyền dữ liệu tin cậy, nó sẽ truyền lại mỗi đoạn nếu không nhận được ACK trong một khoảng thời gian nhất định. TCP đặt thời gian chờ này như một hàm của RTT kỳ vọng giữa hai đầu kết nối. Đáng tiếc, xét đến phạm vi RTT có thể xảy ra giữa bất kỳ cặp host nào trên Internet, cũng như sự biến đổi của RTT giữa cùng hai host theo thời gian, thì việc chọn một giá trị thời gian chờ thích hợp không hề đơn giản. Để giải quyết vấn đề này, TCP sử dụng một cơ chế truyền lại thích ứng. Bây giờ chúng ta mô tả cơ chế này và cách nó đã phát triển theo thời gian khi cộng đồng Internet có thêm nhiều kinh nghiệm sử dụng TCP.

Thuật toán Gốc
~~~~~~~~~~~~~~~~~~

Chúng ta bắt đầu với một thuật toán đơn giản để tính toán giá trị thời gian chờ giữa một cặp host. Đây là thuật toán ban đầu được mô tả trong đặc tả TCP—và phần mô tả sau đây trình bày nó theo cách đó—nhưng nó có thể được sử dụng bởi bất kỳ giao thức đầu-cuối nào.

Ý tưởng là duy trì một trung bình động của RTT và sau đó tính thời gian chờ như một hàm của RTT đó. Cụ thể, mỗi khi TCP gửi một đoạn dữ liệu, nó ghi lại thời gian. Khi một ACK cho đoạn đó đến, TCP lại đọc lại thời gian, và lấy hiệu giữa hai thời điểm này làm ``SampleRTT``. TCP sau đó tính toán một ``EstimatedRTT`` như một trung bình trọng số giữa ước lượng trước đó và mẫu mới này. Tức là,

::

   EstimatedRTT = alpha x EstimatedRTT + (1 - alpha) x SampleRTT

Tham số ``alpha`` được chọn để *làm mềm* giá trị ``EstimatedRTT``. Một giá trị ``alpha`` nhỏ sẽ theo dõi các thay đổi của RTT nhưng có thể bị ảnh hưởng quá mức bởi những dao động tạm thời. Ngược lại, một giá trị ``alpha`` lớn thì ổn định hơn nhưng có thể không nhanh chóng thích ứng với những thay đổi thực sự. Đặc tả TCP ban đầu khuyến nghị đặt ``alpha`` giữa 0.8 và 0.9. Sau đó, TCP sử dụng ``EstimatedRTT`` để tính thời gian chờ một cách khá bảo thủ:

::

   TimeOut = 2 x EstimatedRTT

Thuật toán Karn/Partridge
~~~~~~~~~~~~~~~~~~~~~~~~~

Sau vài năm sử dụng trên Internet, một khuyết điểm khá rõ ràng đã được phát hiện trong thuật toán đơn giản này. Vấn đề là một ACK không thực sự xác nhận việc truyền tải; nó thực sự xác nhận việc nhận dữ liệu. Nói cách khác, mỗi khi một đoạn được truyền lại và sau đó một ACK đến cho người gửi, thì không thể xác định được ACK đó nên được liên kết với lần truyền đầu tiên hay lần truyền thứ hai của đoạn đó để đo mẫu ``SampleRTT``. Việc cần biết liên kết ACK với lần truyền nào để tính một ``SampleRTT`` chính xác là cần thiết. Như được minh họa trong :numref:`Figure %s <fig-tcp-karn>`, nếu bạn giả định rằng ACK là cho lần truyền ban đầu nhưng thực chất nó là cho lần truyền thứ hai, thì ``SampleRTT`` sẽ bị quá lớn (a); nếu bạn giả định rằng ACK là cho lần truyền thứ hai nhưng thực sự nó là cho lần truyền đầu tiên, thì ``SampleRTT`` sẽ bị quá nhỏ (b).

.. _fig-tcp-karn:
.. figure:: figures/f05-10-9780123850591.png
   :width: 500px
   :align: center

   Liên kết ACK với (a) lần truyền ban đầu
   so với (b) lần truyền lại.

Giải pháp, được đề xuất vào năm 1987, thật bất ngờ đơn giản. Mỗi khi TCP truyền lại một đoạn, nó ngừng lấy mẫu RTT; nó chỉ đo ``SampleRTT`` cho các đoạn chỉ được gửi một lần. Giải pháp này được gọi là thuật toán Karn/Partridge, theo tên của những người phát minh ra nó. Sửa đổi mà họ đề xuất cũng bao gồm một thay đổi nhỏ thứ hai đối với cơ chế thời gian chờ của TCP. Mỗi khi TCP truyền lại, nó đặt thời gian chờ kế tiếp bằng gấp đôi thời gian chờ lần trước, thay vì dựa trên ``EstimatedRTT`` vừa có. Tức là, Karn và Partridge đề xuất rằng TCP sử dụng cơ chế trở lại lũy thừa (exponential backoff), tương tự như cách hoạt động của Ethernet. Động cơ cho việc sử dụng trở lại lũy thừa rất đơn giản: Tắc nghẽn là nguyên nhân có khả năng cao nhất gây mất đoạn, nghĩa là nguồn TCP không nên phản ứng quá mạnh đối với một lần hết thời gian. Trên thực tế, số lần kết nối hết thời gian càng nhiều, thì nguồn nên càng thận trọng hơn. Chúng ta sẽ thấy ý tưởng này được hiện hữu trong một cơ chế tinh vi hơn nhiều trong chương tiếp theo.

Thuật toán Jacobson/Karels
~~~~~~~~~~~~~~~~~~~~~~~~~~

Thuật toán Karn/Partridge được giới thiệu vào thời điểm Internet đang chịu áp lực tắc nghẽn cao. Cách tiếp cận của họ được thiết kế để khắc phục một số nguyên nhân gốc của tắc nghẽn đó, nhưng mặc dù là một cải tiến, tắc nghẽn vẫn chưa được loại bỏ hoàn toàn. Năm sau đó (1988), hai nhà nghiên cứu khác—Jacobson và Karels—đã đề xuất một thay đổi sâu sắc hơn cho TCP nhằm đối phó với tắc nghẽn. Phần lớn thay đổi được đề xuất đó được mô tả trong chương tiếp theo. Ở đây, chúng ta tập trung vào khía cạnh của đề xuất đó liên quan đến việc quyết định khi nào kích hoạt thời gian chờ và truyền lại một đoạn.

Nhắc thêm, cần rõ ràng rằng cơ chế thời gian chờ có liên quan đến tắc nghẽn—nếu bạn kích hoạt thời gian chờ quá sớm, bạn có thể truyền lại không cần thiết một đoạn, chỉ làm tăng tải cho mạng. Lý do khác cần một giá trị thời gian chờ chính xác là vì một thời gian chờ được hiểu là dấu hiệu của tắc nghẽn, từ đó kích hoạt cơ chế kiểm soát tắc nghẽn. Cuối cùng, lưu ý rằng không có gì đặc biệt về việc tính toán thời gian chờ theo phương pháp Jacobson/Karels đối với TCP; phương pháp này có thể được áp dụng cho bất kỳ giao thức đầu-cuối nào.

Vấn đề chính với cách tính toán ban đầu là nó không tính đến phương sai của các mẫu RTT. Một cách trực giác, nếu sự biến thiên giữa các mẫu là nhỏ, thì ``EstimatedRTT`` có thể được tin cậy hơn và không có lý do gì để nhân đôi ước lượng đó để tính toán thời gian chờ. Ngược lại, nếu có sự biến thiên lớn giữa các mẫu, thì giá trị thời gian chờ nên được điều chỉnh cho phù hợp với thành phần phương sai.

Trong cách tiếp cận mới, người gửi đo một ``SampleRTT`` mới như trước. Sau đó, nó gộp mẫu mới này vào phép tính thời gian chờ như sau:

::

   Difference = SampleRTT - EstimatedRTT
   EstimatedRTT = EstimatedRTT + ( delta x Difference)
   Deviation = Deviation + delta (|Difference| - Deviation)

trong đó ``delta`` nằm trong khoảng từ 0 đến 1. Tức là, chúng ta tính cả trung bình RTT và sự biến thiên của nó.

Sau đó, TCP tính thời gian chờ như một hàm của cả ``EstimatedRTT`` và ``Deviation`` như sau:

::

   TimeOut = mu x EstimatedRTT + phi x Deviation

trong đó dựa trên kinh nghiệm, ``mu`` thường được đặt là 1 và ``phi`` là 4. Do đó, khi độ biến thiên nhỏ, ``TimeOut`` gần với ``EstimatedRTT``; còn một độ biến thiên lớn sẽ khiến phần ``Deviation`` chi phối việc tính toán.

Triển khai
~~~~~~~~~~

Có hai điểm cần lưu ý về việc triển khai thời gian chờ trong TCP. Điểm đầu tiên là có thể triển khai phép tính cho ``EstimatedRTT`` và ``Deviation`` mà không cần sử dụng số học dấu phẩy động. Thay vào đó, toàn bộ phép tính được nhân tỷ lệ bởi 2\ :sup:`n`, với delta được chọn là 1/2\ :sup:`n`. Điều này cho phép chúng ta sử dụng số học nguyên, thực hiện phép nhân và chia bằng cách dịch bit (shifts), từ đó đạt được hiệu suất cao hơn. Phép tính kết quả được cho bởi đoạn mã dưới đây, với n=3 (tức là, ``delta = 1/8``). Lưu ý rằng ``EstimatedRTT`` và ``Deviation`` được lưu dạng đã nhân tỷ lệ, trong khi giá trị của ``SampleRTT`` ban đầu và ``TimeOut`` cuối cùng là các giá trị thực, chưa nhân tỷ lệ. Nếu bạn cảm thấy đoạn mã khó theo dõi, hãy thử cắm một số giá trị thực vào và xác minh rằng nó cho kết quả giống với các phương trình trên.

::

   {
       SampleRTT -= (EstimatedRTT >> 3);
       EstimatedRTT += SampleRTT;
       if (SampleRTT < 0)
           SampleRTT = -SampleRTT;
       SampleRTT -= (Deviation >> 3);
       Deviation += SampleRTT;
       TimeOut = (EstimatedRTT >> 3) + (Deviation >> 1);
   }

Điểm thứ hai cần lưu ý là thuật toán Jacobson/Karels chỉ tốt bằng đồng hồ được sử dụng để lấy thời gian hiện tại. Trên các triển khai Unix điển hình vào thời điểm đó, độ chính xác của đồng hồ có thể lên tới 500 ms, điều này lớn hơn đáng kể so với RTT trung bình xuyên quốc gia, khoảng từ 100 đến 200 ms. Tệ hơn, việc triển khai TCP trên Unix chỉ kiểm tra xem có nên kích hoạt thời gian chờ mỗi khi đồng hồ 500 ms búng, và chỉ lấy một mẫu RTT mỗi RTT. Sự kết hợp của hai yếu tố này có thể dẫn đến việc thời gian chờ xảy ra 1 giây sau khi đoạn dữ liệu được truyền. Một lần nữa, mở rộng cho TCP bao gồm một cơ chế giúp phép tính RTT này trở nên chính xác hơn.

Tất cả các thuật toán truyền lại mà chúng ta đã thảo luận đều dựa trên thời gian chờ nhận ACK, điều báo hiệu rằng một đoạn có lẽ đã bị mất. Lưu ý rằng thời gian chờ không, tuy nhiên, cho biết người gửi có nhận được bất kỳ đoạn nào gửi sau đoạn bị mất hay không. Điều này là do các ACK của TCP được tích lũy; chúng chỉ xác định đoạn cuối cùng được nhận mà không có khoảng trống trước đó. Việc nhận được các đoạn xảy ra sau một khoảng trống trở nên thường xuyên hơn khi các mạng nhanh dẫn đến cửa sổ lớn hơn. Nếu các ACK cũng báo cho người gửi biết các đoạn tiếp nối, nếu có, đã được nhận, thì người gửi có thể thông minh hơn trong việc quyết định gửi lại đoạn nào, rút ra kết luận tốt hơn về trạng thái tắc nghẽn, và tính toán RTT chính xác hơn. Một mở rộng của TCP hỗ trợ điều này được mô tả ở một phần sau.

.. _key-open-source:
.. admonition::  Key Takeaway

   Có một điểm nữa cần nêu về việc tính toán thời gian chờ. Đây là một việc làm khá tinh vi đến mức, có cả một RFC được dành riêng cho chủ đề này: `RFC 6298 <https://tools.ietf.org/html/rfc6298>`__. Thông điệp rút ra là đôi khi việc chỉ định đầy đủ một giao thức bao gồm quá nhiều chi tiết nhỏ đến mức ranh giới giữa đặc tả và triển khai trở nên mờ nhạt. Điều này đã xảy ra hơn một lần với TCP, khiến một số người tranh luận rằng “triển khai **là** đặc tả.” Tuy nhiên, điều đó không nhất thiết là điều xấu miễn là triển khai tham chiếu có sẵn dưới dạng phần mềm mã nguồn mở. Nói chung hơn, ngành công nghiệp đang chứng kiến sự phát triển ngày càng quan trọng của phần mềm mã nguồn mở trong khi tiêu chuẩn mở dần mất đi tầm quan trọng. :ref:`[Next] <key-micro-service>`

5.2.7 Ranh giới Hồ sơ
---------------------

Vì TCP là một giao thức luồng byte, số byte được ghi bởi người gửi không nhất thiết bằng số byte được đọc bởi người nhận. Ví dụ, ứng dụng có thể ghi 8 byte, sau đó 2 byte, sau đó 20 byte vào một kết nối TCP, trong khi ở phía nhận, ứng dụng đọc 5 byte mỗi lần qua một vòng lặp lặp lại 6 lần. TCP không chèn ranh giới hồ sơ giữa byte thứ 8 và thứ 9, cũng không giữa byte thứ 10 và thứ 11. Điều này trái ngược với một giao thức định hướng thông điệp, chẳng hạn như UDP, trong đó thông điệp được gửi có độ dài chính xác bằng với thông điệp được nhận.

Mặc dù TCP là một giao thức luồng byte, nhưng nó có hai tính năng khác nhau mà người gửi có thể sử dụng để chèn ranh giới hồ sơ vào luồng byte này, giúp thông báo cho người nhận cách chia tách luồng byte thành các hồ sơ. (Khả năng đánh dấu ranh giới hồ sơ là hữu ích, ví dụ, trong nhiều ứng dụng cơ sở dữ liệu.) Cả hai tính năng này ban đầu được đưa vào TCP vì các lý do hoàn toàn khác nhau; chỉ qua thời gian chúng mới được sử dụng cho mục đích này.

Cơ chế đầu tiên là tính năng dữ liệu khẩn cấp, được triển khai qua cờ ``URG`` và trường ``UrgPtr`` trong header TCP. Ban đầu, cơ chế dữ liệu khẩn cấp được thiết kế để cho phép ứng dụng gửi dữ liệu *ngoài luồng* tới đối tác. “Ngoài luồng” ở đây có nghĩa là dữ liệu tách biệt khỏi luồng dữ liệu bình thường (ví dụ, một lệnh dừng hoạt động đang diễn ra). Dữ liệu ngoài luồng được xác định trong đoạn thông qua trường ``UrgPtr`` và sẽ được chuyển đến tiến trình nhận ngay khi đến, dù điều đó có nghĩa là chuyển nó trước dữ liệu với số thứ tự sớm hơn. Qua thời gian, tuy nhiên, tính năng này không còn được sử dụng; vì vậy thay vì biểu thị “dữ liệu khẩn cấp”, nó đã được sử dụng để biểu thị “dữ liệu đặc biệt”, chẳng hạn như dấu hiệu ranh giới hồ sơ. Cách sử dụng này phát triển vì, giống như thao tác push, phía nhận TCP phải thông báo cho ứng dụng rằng dữ liệu khẩn cấp đã đến. Tức là, chính dữ liệu khẩn cấp không quan trọng, mà là thực tế rằng tiến trình gửi có thể gửi hiệu quả một tín hiệu tới người nhận, điều này là quan trọng.

Cơ chế thứ hai để chèn dấu hiệu kết thúc hồ sơ vào luồng byte là thao tác *push*. Ban đầu, cơ chế này được thiết kế để cho phép tiến trình gửi thông báo cho TCP rằng nó nên gửi (xả) tất cả các byte đã thu thập được tới đối tác. Thao tác *push* có thể được sử dụng để triển khai ranh giới hồ sơ vì đặc tả nói rằng TCP phải gửi tất cả dữ liệu đã được lưu trong bộ đệm ở nguồn khi ứng dụng gọi thao tác push, và, tùy chọn, TCP ở phía nhận sẽ thông báo cho ứng dụng mỗi khi một đoạn đến với cờ PUSH được bật. Nếu phía nhận hỗ trợ tùy chọn này (giao diện socket thì không), thao tác push có thể được dùng để chia luồng TCP thành các hồ sơ.

Tất nhiên, chương trình ứng dụng luôn có quyền tự chèn ranh giới hồ sơ mà không cần hỗ trợ từ TCP. Ví dụ, nó có thể gửi một trường biểu thị độ dài của hồ sơ sẽ được gửi sau đó, hoặc nó có thể tự chèn dấu hiệu ranh giới hồ sơ vào luồng dữ liệu.

5.2.8 Các Mở Rộng TCP
---------------------

Chúng ta đã đề cập đến bốn điểm khác nhau trong phần này rằng hiện nay có các mở rộng cho TCP giúp giảm bớt một số vấn đề mà TCP gặp phải khi mạng bên dưới trở nên nhanh hơn. Những mở rộng này được thiết kế nhằm tác động càng nhỏ đến TCP càng tốt. Cụ thể, chúng được thực hiện dưới dạng các tùy chọn có thể được thêm vào header TCP. (Chúng ta đã lướt qua điểm này trước đó, nhưng lý do tại sao header TCP có trường ``HdrLen`` là vì header có thể có độ dài biến đổi; phần biến đổi của header TCP chứa các tùy chọn đã được thêm vào.) Ý nghĩa của việc thêm những mở rộng này dưới dạng tùy chọn thay vì thay đổi lõi của header TCP là các host vẫn có thể giao tiếp qua TCP ngay cả khi chúng không triển khai các tùy chọn đó. Những host triển khai các mở rộng tùy chọn, ngược lại, có thể tận dụng chúng. Hai bên thỏa thuận rằng họ sẽ sử dụng các tùy chọn này trong giai đoạn thiết lập kết nối của TCP.

Mở rộng đầu tiên giúp cải thiện cơ chế thời gian chờ của TCP. Thay vì đo RTT bằng cách sử dụng một sự kiện dựa trên đồng hồ thô, TCP có thể đọc đồng hồ hệ thống thực sự khi nó sắp gửi một đoạn, và chèn thời gian đó—hãy coi đó là một *timestamp* 32 bit—vào header của đoạn. Phía nhận sau đó sẽ phản hồi lại timestamp này trong ACK, và người gửi trừ timestamp đó khỏi thời gian hiện tại để đo được RTT. Về cơ bản, tùy chọn timestamp cung cấp một vị trí thuận tiện cho TCP lưu lại hồ sơ thời điểm một đoạn được truyền; nó lưu thời gian trực tiếp trong đoạn. Lưu ý rằng các đầu cuối trong kết nối không cần đồng bộ đồng hồ, vì timestamp được ghi và đọc tại cùng một đầu.

Mở rộng thứ hai giải quyết vấn đề trường ``SequenceNum`` 32 bit của TCP bị cuộn quá sớm trên mạng tốc độ cao. Thay vì định nghĩa một trường số thứ tự 64 bit mới, TCP sử dụng timestamp 32 bit đã mô tả để mở rộng hiệu quả không gian số thứ tự. Nói cách khác, TCP quyết định xem có chấp nhận hay từ chối một đoạn dựa trên một định danh 64 bit, trong đó trường ``SequenceNum`` chiếm 32 bit thấp và timestamp chiếm 32 bit cao. Vì timestamp luôn tăng, nó phục vụ để phân biệt giữa hai lần xuất hiện khác nhau của cùng một số thứ tự. Lưu ý rằng timestamp trong bối cảnh này chỉ được dùng để bảo vệ chống lại hiện tượng cuộn (wraparound); nó không được xem như một phần của số thứ tự cho mục đích sắp xếp hay xác nhận dữ liệu.

Mở rộng thứ ba cho phép TCP quảng bá một cửa sổ lớn hơn, từ đó cho phép nó lấp đầy các đường truyền với tích của độ trễ × băng thông lớn hơn, nhờ vào các mạng tốc độ cao. Mở rộng này liên quan đến một tùy chọn định nghĩa một *hệ số tỉ lệ* cho cửa sổ quảng bá. Tức là, thay vì diễn giải số xuất hiện trong trường ``AdvertisedWindow`` là số byte mà người gửi được phép giữ chưa được ACK, tùy chọn này cho phép hai bên TCP đồng ý rằng trường ``AdvertisedWindow`` đếm các khối dữ liệu lớn hơn (ví dụ, số đơn vị 16 byte mà người gửi có thể giữ chưa được ACK). Nói cách khác, tùy chọn mở rộng cửa sổ xác định số bit mà mỗi bên nên dịch trái trường ``AdvertisedWindow`` trước khi sử dụng nội dung của nó để tính cửa sổ hiệu quả.

Mở rộng thứ tư cho phép TCP bổ sung thêm vào phần ACK tích lũy của nó với các ACK chọn lọc cho bất kỳ đoạn bổ sung nào đã được nhận nhưng không liền kề với tất cả các đoạn trước đó. Đây là tùy chọn *selective acknowledgment* hay *SACK*. Khi tùy chọn SACK được sử dụng, phía nhận vẫn tiếp tục xác nhận các đoạn như thông thường—ý nghĩa của trường ``Acknowledge`` không thay đổi—nhưng nó cũng sử dụng các trường tùy chọn trong header để xác nhận các khối dữ liệu bổ sung đã được nhận. Điều này cho phép người gửi chỉ truyền lại những đoạn bị mất theo thông tin ACK chọn lọc.

Nếu không có SACK, chỉ có hai chiến lược hợp lý cho người gửi. Chiến lược bi quan phản ứng với một lần hết thời gian bằng cách truyền lại không chỉ đoạn bị hết thời gian mà còn cả các đoạn được gửi sau đó. Về hiệu quả, chiến lược bi quan giả định tồi tệ nhất: rằng tất cả các đoạn đó đều đã bị mất. Nhược điểm của chiến lược bi quan là có thể truyền lại không cần thiết những đoạn đã được nhận thành công lần đầu. Chiến lược thứ hai là chiến lược lạc quan, phản ứng với hết thời gian bằng cách chỉ truyền lại đoạn bị lỗi. Về hiệu quả, cách tiếp cận lạc quan giả định kịch bản tốt nhất: rằng chỉ có một đoạn bị mất. Nhược điểm của chiến lược lạc quan là nó rất chậm, không cần thiết, khi mất liên tiếp một chuỗi các đoạn, như có thể xảy ra khi có tắc nghẽn. Nó chậm vì mất của từng đoạn không được phát hiện cho đến khi người gửi nhận được ACK cho lần truyền lại của đoạn trước. Do đó, nó tiêu tốn một RTT cho mỗi đoạn cho đến khi truyền lại tất cả các đoạn bị mất trong chuỗi. Với tùy chọn SACK, một chiến lược tốt hơn có thể được sử dụng bởi người gửi: chỉ truyền lại những đoạn lấp đầy khoảng trống giữa các đoạn đã được ACK chọn lọc.

Nhân tiện, những mở rộng này không phải là toàn bộ câu chuyện. Chúng ta sẽ thấy một số mở rộng khác trong chương tiếp theo khi xem cách TCP xử lý tắc nghẽn. Cơ quan Quản lý Số đã được ủy quyền của Internet (IANA) theo dõi tất cả các tùy chọn được định nghĩa cho TCP (và cho nhiều giao thức Internet khác). Xem tài liệu tham khảo ở cuối chương để biết đường liên kết đến đăng ký số giao thức của IANA.

5.2.9 Hiệu Năng
-----------------

Nhắc lại rằng Chương 1 đã giới thiệu hai chỉ số định lượng để đánh giá hiệu năng mạng: độ trễ và throughput. Như đã đề cập trong phần thảo luận đó, các chỉ số này không chỉ bị ảnh hưởng bởi phần cứng bên dưới (ví dụ, độ trễ truyền và băng thông liên kết) mà còn bởi chi phí xử lý phần mềm. Bây giờ, khi chúng ta có một đồ thị giao thức dựa trên phần mềm đầy đủ, bao gồm các giao thức vận chuyển thay thế, chúng ta có thể thảo luận cách đo lường hiệu năng một cách có ý nghĩa. Tầm quan trọng của các phép đo này là chúng thể hiện hiệu năng mà các chương trình ứng dụng cảm nhận được.

.. _fig-experiment:
.. figure:: figures/f05-11-9780123850591.png
   :width: 500px
   :align: center

   Hệ thống đo: Hai máy trạm Linux và một cặp liên kết Ethernet Gbps.

Chúng tôi bắt đầu, như mọi báo cáo kết quả thực nghiệm nên làm, bằng cách mô tả phương pháp thực nghiệm của chúng tôi. Điều này bao gồm thiết bị dùng trong các thí nghiệm; trong trường hợp này, mỗi máy trạm có một cặp bộ xử lý Xeon 2.4-GHz hai nhân chạy Linux. Để cho phép tốc độ trên 1 Gbps, một cặp card giao diện mạng Ethernet (được dán nhãn là NIC) được sử dụng trên mỗi máy. Mạng Ethernet trải dài trong một phòng máy, do đó độ trễ truyền không phải là vấn đề, biến đây thành thước đo của chi phí xử lý của bộ vi xử lý/phần mềm. Một chương trình kiểm tra chạy trên giao diện socket cố gắng chuyển dữ liệu nhanh nhất có thể từ máy này sang máy kia. :numref:`Figure %s <fig-experiment>` minh họa cấu hình này.

Bạn có thể nhận thấy rằng cấu hình thực nghiệm này không thực sự tiên tiến về phần cứng hay tốc độ liên kết. Mục của phần này không phải là để cho thấy một giao thức cụ thể có thể chạy nhanh đến mức nào, mà là để minh họa phương pháp chung trong việc đo lường và báo cáo hiệu năng giao thức.

Bài kiểm tra throughput được thực hiện với nhiều kích thước thông điệp khác nhau sử dụng một công cụ đánh giá chuẩn gọi là TTCP. Kết quả của bài kiểm tra throughput được trình bày trong :numref:`Figure %s <fig-xput>`. Điều quan trọng cần lưu ý ở biểu đồ này là throughput cải thiện khi các thông điệp có kích thước lớn hơn. Điều này hợp lý—mỗi thông điệp đi kèm với một lượng chi phí xử lý nhất định, nên một thông điệp lớn hơn đồng nghĩa với việc chi phí đó được phân bổ trên nhiều byte hơn. Đường cong throughput trở nên phẳng ở trên 1 KB, khi chi phí xử lý cho mỗi thông điệp trở nên không đáng kể so với số lượng byte lớn mà ngăn xếp giao thức phải xử lý.

.. _fig-xput:
.. figure:: figures/f05-12-9780123850591.png
   :width: 400px
   :align: center

   Throughput đo được sử dụng TCP, với các kích thước thông điệp khác nhau.

Cần lưu ý rằng throughput tối đa nhỏ hơn 2 Gbps, tốc độ liên kết có sẵn trong cấu hình này. Cần có thêm thử nghiệm và phân tích kết quả để xác định nút thắt (bottleneck) nằm ở đâu (hoặc nếu có hơn một nút thắt). Ví dụ, việc xem xét tải CPU có thể cho biết liệu CPU có phải là nút thắt hay không hoặc bộ nhớ, hiệu suất card giao diện mạng hay vấn đề khác.

Chúng tôi cũng lưu ý rằng mạng trong thí nghiệm này cơ bản là “hoàn hảo”. Nó hầu như không có độ trễ hay mất mát, nên các yếu tố duy nhất ảnh hưởng đến hiệu năng là việc triển khai TCP và phần cứng/phần mềm của máy trạm. Ngược lại, phần lớn các mạng mà chúng ta gặp phải không hoàn hảo, đặc biệt là các liên kết có băng thông hạn chế, liên kết “last-mile” và các liên kết không dây dễ bị mất gói. Trước khi chúng ta có thể đánh giá đầy đủ cách các liên kết này ảnh hưởng đến hiệu năng của TCP, chúng ta cần hiểu cách TCP xử lý *tắc nghẽn*, chủ đề của chương tiếp theo.

Trong lịch sử mạng, tốc độ liên kết ngày càng tăng đã đe dọa vượt qua khả năng cung cấp của các ứng dụng. Ví dụ, một nỗ lực nghiên cứu lớn đã được bắt đầu ở Hoa Kỳ vào năm 1989 để xây dựng “mạng gigabit”, với mục tiêu không chỉ là xây dựng các liên kết và bộ chuyển mạch có thể chạy ở 1 Gbps hoặc cao hơn mà còn là truyền tải throughput đó đến cho một tiến trình ứng dụng đơn lẻ. Đã có một số vấn đề thực sự (ví dụ, card giao diện mạng, kiến trúc máy trạm và hệ điều hành đều phải được thiết kế với throughput từ mạng đến ứng dụng trong tâm trí) và cũng có những vấn đề cảm nhận mà về sau chứng tỏ không nghiêm trọng. Một trong những mối quan tâm trong số đó là lo ngại rằng các giao thức vận chuyển hiện có, TCP đặc biệt, có thể không đáp ứng được yêu cầu của hoạt động gigabit.

Hóa ra, TCP đã làm tốt việc bắt kịp các yêu cầu ngày càng tăng của các mạng tốc độ cao và các ứng dụng. Một trong những yếu tố quan trọng nhất là việc giới thiệu mở rộng cửa sổ để xử lý các tích của băng thông và độ trễ lớn hơn. Tuy nhiên, thường có sự khác biệt lớn giữa hiệu năng lý thuyết của TCP và hiệu năng thực tế đạt được. Những vấn đề tương đối đơn giản như việc sao chép dữ liệu nhiều hơn mức cần thiết khi đi từ card giao diện mạng lên ứng dụng có thể làm giảm hiệu năng, cũng như bộ nhớ đệm không đủ khi tích của băng thông và độ trễ lớn. Và động học của TCP đủ phức tạp (như sẽ trở nên rõ ràng hơn trong chương tiếp theo) đến mức các tương tác tinh vi giữa hành vi mạng, hành vi ứng dụng và chính giao thức TCP có thể ảnh hưởng đáng kể đến hiệu năng.

Cho mục đích của chúng ta, điều đáng chú ý là TCP vẫn tiếp tục hoạt động rất tốt khi tốc độ mạng tăng lên, và khi gặp phải giới hạn nào đó (thường liên quan đến tắc nghẽn, tích của băng thông và độ trễ tăng lên, hoặc cả hai), các nhà nghiên cứu sẽ nhanh chóng tìm ra giải pháp. Chúng ta đã thấy một số giải pháp trong chương này, và sẽ thấy thêm trong chương tiếp theo.

5.2.10 Các Lựa Chọn Thiết Kế Thay Thế (SCTP, QUIC)
--------------------------------------------------

Mặc dù TCP đã chứng tỏ là một giao thức ổn định đáp ứng nhu cầu của một phạm vi ứng dụng rất rộng, không gian thiết kế của các giao thức vận chuyển lại rất lớn. TCP không phải là điểm duy nhất hợp lệ trong không gian thiết kế đó. Chúng ta kết thúc phần thảo luận về TCP bằng cách xem xét các lựa chọn thiết kế thay thế. Trong khi chúng tôi đưa ra giải thích tại sao các nhà thiết kế TCP lại đưa ra những lựa chọn như vậy, chúng tôi cũng nhận thấy rằng có các giao thức khác đã chọn những hướng đi khác, và có thể sẽ xuất hiện nhiều giao thức như vậy trong tương lai.

Đầu tiên, chúng ta đã gợi ý ngay từ chương đầu tiên của cuốn sách rằng có ít nhất hai lớp giao thức vận chuyển thú vị: các giao thức hướng luồng như TCP và các giao thức yêu cầu/phản hồi như RPC. Nói cách khác, chúng ta đã ngầm chia không gian thiết kế làm đôi và đặt TCP vào nửa hướng luồng. Ta có thể chia nhỏ thêm các giao thức hướng luồng thành hai nhóm—đáng tin cậy và không đáng tin cậy—trong đó nhóm đầu chứa TCP và nhóm thứ hai phù hợp hơn với các ứng dụng video tương tác mà có thể chấp nhận mất một khung hình hơn là chịu được độ trễ liên quan đến việc truyền lại.

Bài tập phân loại giao thức vận chuyển này rất thú vị và có thể được tiếp tục với mức độ chi tiết ngày càng cao, nhưng thế giới không đơn giản như màu đen và trắng như ta mong muốn. Hãy xem xét khả năng của TCP như một giao thức vận chuyển cho các ứng dụng yêu cầu/phản hồi, ví dụ. TCP là một giao thức toàn chiều, nên rất dễ để mở một kết nối TCP giữa client và server, gửi thông điệp yêu cầu theo một hướng và gửi thông điệp phản hồi theo hướng kia. Tuy nhiên, có hai phức tạp phát sinh. Đầu tiên là TCP là giao thức hướng *byte* thay vì hướng *tin nhắn*, và các ứng dụng yêu cầu/phản hồi luôn xử lý các tin nhắn. (Chúng ta sẽ khám phá vấn đề byte so với tin nhắn chi tiết hơn ngay sau.) Phức tạp thứ hai là trong các trường hợp khi cả thông điệp yêu cầu và phản hồi vừa đủ chứa trong một gói mạng, một giao thức yêu cầu/phản hồi được thiết kế tốt chỉ cần hai gói để thực hiện trao đổi, trong khi TCP cần ít nhất chín gói: ba để thiết lập kết nối, hai cho trao đổi thông điệp, và bốn để hủy kết nối. Tất nhiên, nếu thông điệp yêu cầu hoặc phản hồi đủ lớn đến mức cần nhiều gói mạng (ví dụ, có thể mất 100 gói để gửi một thông điệp phản hồi 100.000 byte), thì chi phí thiết lập và hủy kết nối trở nên không đáng kể. Nói cách khác, không phải lúc nào một giao thức cụ thể cũng không thể hỗ trợ một chức năng nào đó; đôi khi chỉ có một thiết kế là hiệu quả hơn thiết kế kia trong các hoàn cảnh cụ thể.

Thứ hai, như vừa đề cập, bạn có thể đặt câu hỏi tại sao TCP lại chọn cung cấp dịch vụ luồng byte đáng tin cậy thay vì dịch vụ luồng tin nhắn đáng tin cậy; tin nhắn sẽ là lựa chọn tự nhiên cho một ứng dụng cơ sở dữ liệu muốn trao đổi hồ sơ. Có hai câu trả lời cho câu hỏi này. Đầu tiên, một giao thức hướng tin nhắn theo định nghĩa phải thiết lập một giới hạn trên về kích thước tin nhắn. Rốt cuộc, một tin nhắn vô hạn là một luồng byte. Với bất kỳ kích thước tin nhắn nào được một giao thức chọn, luôn có những ứng dụng muốn gửi tin nhắn lớn hơn, điều này khiến giao thức vận chuyển trở nên vô dụng và buộc ứng dụng phải tự triển khai các dịch vụ tương tự giao thức vận chuyển. Lý do thứ hai là, mặc dù các giao thức hướng tin nhắn chắc chắn phù hợp hơn với các ứng dụng muốn gửi hồ sơ cho nhau, bạn có thể dễ dàng chèn ranh giới hồ sơ vào một luồng byte để triển khai chức năng này.

Quyết định thứ ba trong thiết kế TCP là nó chuyển giao các byte *theo thứ tự* cho ứng dụng. Điều này có nghĩa là TCP có thể giữ lại các byte nhận được không theo thứ tự từ mạng, chờ đợi các byte bị thiếu để lấp đầy khoảng trống. Điều này vô cùng hữu ích cho nhiều ứng dụng nhưng lại trở nên không cần thiết nếu ứng dụng có khả năng xử lý dữ liệu không theo thứ tự. Ví dụ, một trang Web chứa nhiều đối tượng nhúng không cần tất cả các đối tượng được chuyển giao theo thứ tự mới bắt đầu hiển thị trang. Thực tế, có một lớp ứng dụng mà ưu tiên xử lý dữ liệu không theo thứ tự ở tầng ứng dụng để nhận dữ liệu sớm hơn khi các gói bị mất hoặc bị xáo trộn trong mạng. Mong muốn hỗ trợ các ứng dụng như vậy đã dẫn đến việc tạo ra không phải một mà là hai giao thức vận chuyển tiêu chuẩn của IETF. Giao thức đầu tiên trong số đó là SCTP, hay còn được gọi là *Stream Control Transmission Protocol*. SCTP cung cấp dịch vụ chuyển giao một phần theo thứ tự, thay vì dịch vụ hoàn toàn theo thứ tự của TCP. (SCTP cũng đưa ra một số quyết định thiết kế khác biệt so với TCP, bao gồm hướng tin nhắn và hỗ trợ nhiều địa chỉ IP cho một phiên làm việc.) Gần đây hơn, IETF đã tiêu chuẩn hóa một giao thức được tối ưu hóa cho lưu lượng Web, được biết đến với tên QUIC. Sẽ có thêm thông tin về QUIC ngay sau.

Thứ tư, TCP đã chọn triển khai các giai đoạn thiết lập/hủy kết nối rõ ràng, nhưng điều này không bắt buộc. Trong trường hợp thiết lập kết nối, có thể gửi tất cả các tham số cần thiết kèm theo thông điệp dữ liệu đầu tiên. TCP đã chọn cách tiếp cận thận trọng hơn, cho phép phía nhận có cơ hội từ chối kết nối trước khi bất kỳ dữ liệu nào đến. Trong trường hợp hủy kết nối, chúng ta có thể đóng kết nối một cách im lặng khi nó không hoạt động trong một khoảng thời gian dài, nhưng điều này sẽ gây khó khăn cho các ứng dụng như đăng nhập từ xa muốn giữ kết nối sống sót trong nhiều tuần; các ứng dụng đó sẽ buộc phải gửi thông điệp “keep alive” ngoài luồng để giữ trạng thái kết nối ở phía bên kia không bị mất.

Cuối cùng, TCP là một giao thức dựa trên cửa sổ, nhưng đây không phải là khả năng duy nhất. Lựa chọn thay thế là thiết kế *dựa trên tốc độ* (rate-based), trong đó phía nhận cho biết tốc độ—được biểu diễn bằng byte hoặc gói mỗi giây—mà nó sẵn sàng chấp nhận dữ liệu đến. Ví dụ, phía nhận có thể báo cho người gửi biết rằng nó có thể xử lý 100 gói mỗi giây. Có một mối đối nghịch thú vị giữa cửa sổ và tốc độ, vì số lượng gói (hay byte) trong cửa sổ, chia cho RTT, chính là tốc độ. Ví dụ, một kích thước cửa sổ 10 gói với RTT 100 ms ngụ ý rằng người gửi có thể truyền với tốc độ 100 gói mỗi giây. Việc tăng hoặc giảm kích thước cửa sổ được quảng bá chính là cách phía nhận điều chỉnh tốc độ mà người gửi được phép truyền. Trong TCP, thông tin này được phản hồi lại cho người gửi qua trường ``AdvertisedWindow`` của ACK cho mỗi đoạn. Một trong những vấn đề then chốt của giao thức dựa trên tốc độ là tần suất mà tốc độ mong muốn—có thể thay đổi theo thời gian—được truyền lại cho nguồn: có phải cho mỗi gói, một lần mỗi RTT, hay chỉ khi tốc độ thay đổi? Trong khi chúng ta vừa xem xét cửa sổ so với tốc độ trong bối cảnh điều khiển luồng, thì đây còn là một vấn đề được tranh cãi mãnh liệt trong bối cảnh kiểm soát tắc nghẽn, điều mà chúng ta sẽ thảo luận trong chương tiếp theo.

QUIC
~~~~

QUIC có nguồn gốc từ Google vào năm 2012 và sau đó được phát triển thành một tiêu chuẩn được đề xuất tại IETF. Khác với nhiều nỗ lực khác nhằm bổ sung thêm các giao thức vận chuyển trên Internet, QUIC đã được triển khai rộng rãi. Như được thảo luận trong :ref:`Chapter 9 <Chapter 9: Applications>`, QUIC được thúc đẩy phần lớn bởi thách thức trong việc khớp các ngữ nghĩa yêu cầu/phản hồi của HTTP với bản chất hướng luồng của TCP. Những vấn đề này càng trở nên rõ ràng theo thời gian, do các yếu tố như sự trỗi dậy của các mạng không dây có độ trễ cao, sự sẵn có của nhiều mạng cho một thiết bị (ví dụ: Wi-Fi và di động), và việc sử dụng ngày càng nhiều các kết nối có mã hóa, có xác thực trên Web (như đã được thảo luận trong :ref:`Chapter 8 <Chapter 8: Network Security>`). Trong khi một mô tả đầy đủ về QUIC vượt quá phạm vi của chúng ta, một số quyết định thiết kế then chốt của nó đáng được bàn luận.

.. sidebar:: Multipath TCP

          Không phải lúc nào cũng cần phải định nghĩa một giao thức mới nếu bạn
          nhận thấy rằng một giao thức hiện có không phục vụ đầy đủ cho một
          trường hợp sử dụng cụ thể. Đôi khi có thể thực hiện những thay đổi
          đáng kể trong cách triển khai một giao thức hiện có, nhưng vẫn giữ vững
          đặc tả gốc.
          Multipath TCP là một ví dụ về tình huống như vậy.

          Ý tưởng của Multipath TCP là điều khiển các gói tin qua
          nhiều đường dẫn khác nhau trên Internet, ví dụ, bằng cách
          sử dụng hai địa chỉ IP khác nhau cho một trong các đầu cuối.
          Điều này đặc biệt hữu ích khi truyền dữ liệu đến một thiết bị di động
          đang kết nối cả Wi-Fi và mạng di động (và do đó có hai địa chỉ IP duy nhất).
          Vì các mạng không dây có thể gặp mất gói tin nghiêm trọng, nên khả năng
          sử dụng cả hai để truyền gói có thể cải thiện đáng kể trải nghiệm người dùng.
          Điều then chốt là phía nhận của TCP phải tái tạo lại luồng byte ban đầu
          theo đúng thứ tự trước khi chuyển dữ liệu lên ứng dụng, ứng dụng không nhận ra
          rằng nó đang hoạt động trên Multipath TCP. (Điều này trái ngược với các ứng dụng
          chủ động mở hai hoặc nhiều kết nối TCP để đạt hiệu năng tốt hơn.)

Nếu độ trễ mạng cao—ví dụ 100 ms hoặc hơn—thì vài RTT có thể nhanh chóng tích lũy thành một sự phiền nhiễu rõ rệt đối với người dùng cuối. Việc thiết lập một phiên HTTP qua TCP với Transport Layer Security (:ref:`Section 8.5 <8.5 Example Systems>`) thường mất ít nhất ba lượt đi-đi về (một cho việc thiết lập phiên TCP và hai cho việc thiết lập các tham số mã hóa) trước khi thông điệp HTTP đầu tiên có thể được gửi đi. Các nhà thiết kế QUIC nhận ra rằng độ trễ này—kết quả trực tiếp của cách tiếp cận thiết kế giao thức theo tầng—có thể được giảm đáng kể nếu việc thiết lập kết nối và các cuộc bắt tay an ninh cần thiết được kết hợp và tối ưu hóa sao cho số lượt đi-đi về là tối thiểu.

Cũng cần lưu ý rằng sự có mặt của nhiều giao diện mạng có thể ảnh hưởng đến thiết kế. Nếu điện thoại di động của bạn mất kết nối Wi-Fi và cần chuyển sang mạng di động, thì điều đó thường đòi hỏi cả một lần hết thời gian TCP trên một kết nối và một chuỗi các cuộc bắt tay mới trên kết nối kia. Việc làm cho kết nối có thể duy trì qua các kết nối ở tầng mạng khác nhau là một mục tiêu thiết kế khác của QUIC.

Cuối cùng, như đã đề cập ở trên, mô hình luồng byte tin cậy của TCP không phù hợp lắm với một yêu cầu trang Web, khi cần truy xuất nhiều đối tượng và quá trình hiển thị trang có thể bắt đầu trước khi tất cả đối tượng đã đến. Trong khi một giải pháp thay thế cho điều này có thể là mở nhiều kết nối TCP song song, cách tiếp cận này (đã được sử dụng trong những ngày đầu của Web) có những hạn chế riêng, đặc biệt về kiểm soát tắc nghẽn (xem :ref:`Chapter 6 <Chapter 6: Congestion Control>`). Cụ thể, mỗi kết nối chạy vòng lặp kiểm soát tắc nghẽn riêng, nên trải nghiệm tắc nghẽn của một kết nối không được phản ánh ở các kết nối khác, và mỗi kết nối cố gắng xác định lượng băng thông thích hợp để sử dụng một cách độc lập. QUIC đã giới thiệu ý tưởng của các luồng (streams) trong một kết nối, cho phép các đối tượng được chuyển giao không theo thứ tự trong khi vẫn giữ được cái nhìn tổng thể về tắc nghẽn trên tất cả các luồng.

Thật thú vị, đến khi QUIC ra đời, đã có rất nhiều quyết định thiết kế được đưa ra gây ra những thách thức đối với việc triển khai một giao thức vận chuyển mới. Đáng chú ý, nhiều “middleboxes” như NAT và firewall (xem :ref:`Section 8.5 <8.5 Example Systems>`) đã hiểu biết đủ về các giao thức vận chuyển phổ biến hiện có (TCP và UDP) đến mức không thể tin cậy để truyền qua một giao thức vận chuyển mới. Do đó, QUIC thực sự được xây dựng trên nền UDP. Nói cách khác, nó là một giao thức vận chuyển chạy trên một giao thức vận chuyển. Điều này không phải là điều hiếm gặp như sự chú trọng của chúng ta vào lớp có thể gợi ý, như được minh họa trong hai phần nhỏ tiếp theo. Lựa chọn này được thực hiện nhằm mục đích làm cho QUIC có thể được triển khai trên Internet như hiện nay, và nó đã khá thành công.

QUIC triển khai thiết lập kết nối nhanh chóng với mã hóa và xác thực ngay trong RTT đầu tiên. Nó cung cấp một định danh kết nối tồn tại xuyên suốt các thay đổi trong mạng bên dưới. Nó hỗ trợ ghép nối nhiều luồng lên một kết nối vận chuyển duy nhất, nhằm tránh hiện tượng nghẽn đầu dòng có thể xảy ra khi một gói bị mất trong khi các dữ liệu hữu ích khác vẫn tiếp tục đến. Và nó giữ nguyên (và ở một số khía cạnh còn cải thiện) các đặc tính tránh nghẽn của TCP, một khía cạnh quan trọng của các giao thức vận chuyển mà chúng ta quay lại ở :ref:`Chapter 6 <Chapter 6: Congestion Control>`.

HTTP đã trải qua một số phiên bản (1.0, 1.1, 2.0, được thảo luận trong :ref:`Section 9.1 <9.1 Traditional Applications>`) với nỗ lực ánh xạ các yêu cầu của nó một cách rõ ràng hơn lên các khả năng của TCP. Với sự ra đời của QUIC, HTTP/3 giờ đây có thể tận dụng một tầng vận chuyển được thiết kế tỉ mỉ để đáp ứng các yêu cầu ứng dụng của Web.

QUIC là một phát triển rất thú vị trong thế giới các giao thức vận chuyển. Nhiều hạn chế của TCP đã được biết đến trong nhiều thập kỷ, nhưng QUIC đại diện cho một trong những nỗ lực thành công nhất cho đến nay nhằm khẳng định một điểm nhìn khác trong không gian thiết kế. Bởi vì QUIC được lấy cảm hứng từ kinh nghiệm với HTTP và Web—những thứ xuất hiện rất lâu sau khi TCP đã được thiết lập vững chắc trên Internet—nó đưa ra một nghiên cứu trường hợp hấp dẫn về những hệ quả không lường trước của thiết kế theo tầng và về sự tiến hóa của Internet.
