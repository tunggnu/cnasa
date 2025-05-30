6.3 Kiểm soát tắc nghẽn TCP
==========================

Phần này mô tả ví dụ nổi bật nhất về kiểm soát tắc nghẽn đầu-cuối đang được sử dụng hiện nay, được triển khai bởi TCP. Chiến lược cốt lõi của TCP là gửi các gói vào mạng mà không cần đặt trước và sau đó phản ứng với các sự kiện quan sát được xảy ra. TCP chỉ giả định hàng đợi FIFO trong các bộ định tuyến của mạng, nhưng cũng hoạt động với các chiến lược xếp hàng khác.

Kiểm soát tắc nghẽn TCP được giới thiệu vào Internet vào cuối những năm 1980 bởi Van Jacobson, khoảng tám năm sau khi ngăn xếp giao thức TCP/IP bắt đầu hoạt động. Ngay trước thời điểm này, Internet đang phải chịu cảnh sụp đổ do tắc nghẽn—các máy chủ sẽ gửi các gói của họ vào Internet nhanh nhất có thể theo cửa sổ quảng bá, tắc nghẽn sẽ xảy ra tại một bộ định tuyến nào đó (dẫn đến các gói bị loại bỏ), và các máy chủ sẽ hết thời gian chờ và truyền lại các gói của họ, dẫn đến tắc nghẽn càng nghiêm trọng hơn.

Nói chung, ý tưởng của kiểm soát tắc nghẽn TCP là mỗi nguồn xác định bao nhiêu dung lượng có sẵn trong mạng, để nó biết có bao nhiêu gói có thể an toàn đang trong quá trình truyền. Khi một nguồn đã có số lượng gói này đang truyền, nó sử dụng sự xuất hiện của một ACK như một tín hiệu rằng một trong các gói của nó đã rời khỏi mạng và do đó an toàn để chèn một gói mới vào mạng mà không làm tăng mức độ tắc nghẽn. Bằng cách sử dụng các ACK để điều tiết việc truyền các gói, TCP được gọi là *tự đồng bộ*. Tất nhiên, việc xác định dung lượng có sẵn ban đầu không phải là nhiệm vụ dễ dàng. Tệ hơn nữa, vì các kết nối khác đến và đi, băng thông có sẵn thay đổi theo thời gian, nghĩa là bất kỳ nguồn nào cũng phải có khả năng điều chỉnh số lượng gói mà nó đang truyền. Phần này mô tả các thuật toán mà TCP sử dụng để giải quyết những vấn đề này và các vấn đề khác.

Lưu ý rằng, mặc dù chúng tôi mô tả các cơ chế kiểm soát tắc nghẽn TCP từng cái một, tạo cảm giác như chúng tôi đang nói về ba cơ chế độc lập, nhưng chỉ khi chúng được kết hợp lại thì mới có kiểm soát tắc nghẽn TCP. Ngoài ra, mặc dù chúng tôi sẽ bắt đầu ở đây với biến thể kiểm soát tắc nghẽn TCP thường được gọi là *TCP tiêu chuẩn*, chúng ta sẽ thấy rằng thực tế có khá nhiều biến thể kiểm soát tắc nghẽn TCP đang được sử dụng ngày nay, và các nhà nghiên cứu vẫn tiếp tục khám phá các cách tiếp cận mới để giải quyết vấn đề này. Một số cách tiếp cận mới này sẽ được thảo luận bên dưới.

6.3.1 Tăng tuyến tính/Giảm bội số
---------------------------------

TCP duy trì một biến trạng thái mới cho mỗi kết nối, gọi là ``CongestionWindow``, được nguồn sử dụng để giới hạn lượng dữ liệu được phép truyền tại một thời điểm nhất định. Cửa sổ tắc nghẽn là đối trọng của kiểm soát tắc nghẽn với cửa sổ quảng bá của kiểm soát luồng. TCP được sửa đổi sao cho số byte dữ liệu chưa được xác nhận tối đa được phép là giá trị nhỏ nhất giữa cửa sổ tắc nghẽn và cửa sổ quảng bá. Do đó, sử dụng các biến đã được định nghĩa trong chương trước, cửa sổ hiệu quả của TCP được sửa đổi như sau:

::

   MaxWindow = MIN(CongestionWindow, AdvertisedWindow)
   EffectiveWindow = MaxWindow -  (LastByteSent - LastByteAcked)

Nghĩa là, ``MaxWindow`` thay thế ``AdvertisedWindow`` trong phép tính ``EffectiveWindow``. Do đó, một nguồn TCP chỉ được phép gửi không nhanh hơn thành phần chậm nhất—mạng hoặc máy đích—có thể xử lý.

Vấn đề, tất nhiên, là làm thế nào để TCP biết được giá trị phù hợp cho ``CongestionWindow``. Không giống như ``AdvertisedWindow``, được gửi bởi phía nhận của kết nối, không ai gửi một ``CongestionWindow`` phù hợp cho phía gửi của TCP. Câu trả lời là nguồn TCP đặt ``CongestionWindow`` dựa trên mức độ tắc nghẽn mà nó cảm nhận được trong mạng. Điều này bao gồm việc giảm cửa sổ tắc nghẽn khi mức độ tắc nghẽn tăng lên và tăng cửa sổ tắc nghẽn khi mức độ tắc nghẽn giảm xuống. Kết hợp lại, cơ chế này thường được gọi là *tăng tuyến tính/giảm bội số* (AIMD); lý do cho cái tên dài dòng này sẽ trở nên rõ ràng bên dưới.

Câu hỏi then chốt là, làm thế nào nguồn xác định được rằng mạng đang tắc nghẽn và nó nên giảm cửa sổ tắc nghẽn? Câu trả lời dựa trên quan sát rằng lý do chính khiến các gói không được chuyển giao, và dẫn đến hết thời gian chờ, là do một gói đã bị loại bỏ do tắc nghẽn. Hiếm khi một gói bị loại bỏ do lỗi trong quá trình truyền. Do đó, TCP coi hết thời gian chờ là dấu hiệu của tắc nghẽn và giảm tốc độ truyền. Cụ thể, mỗi khi xảy ra hết thời gian chờ, nguồn đặt ``CongestionWindow`` bằng một nửa giá trị trước đó. Việc giảm một nửa ``CongestionWindow`` cho mỗi lần hết thời gian chờ tương ứng với phần “giảm bội số” của AIMD.

Mặc dù ``CongestionWindow`` được định nghĩa theo byte, nhưng dễ hiểu hơn về giảm bội số nếu chúng ta nghĩ theo đơn vị gói. Ví dụ, giả sử ``CongestionWindow`` hiện được đặt là 16 gói. Nếu phát hiện mất gói, ``CongestionWindow`` được đặt thành 8. (Thông thường, mất gói được phát hiện khi xảy ra hết thời gian chờ, nhưng như chúng ta sẽ thấy bên dưới, TCP có một cơ chế khác để phát hiện gói bị loại bỏ.) Các lần mất gói bổ sung sẽ khiến ``CongestionWindow`` giảm xuống 4, sau đó là 2, và cuối cùng là 1 gói. ``CongestionWindow`` không được phép nhỏ hơn kích thước của một gói, hoặc theo thuật ngữ của TCP, là *kích thước đoạn tối đa*.

.. _fig-linear:
.. figure:: figures/f06-08-9780123850591.png
   :width: 200px
   :align: center

   Các gói đang truyền trong quá trình tăng tuyến tính, với mỗi
   gói được thêm vào mỗi RTT.

Một chiến lược kiểm soát tắc nghẽn chỉ giảm kích thước cửa sổ rõ ràng là quá thận trọng. Chúng ta cũng cần có khả năng tăng cửa sổ tắc nghẽn để tận dụng dung lượng mới có trong mạng. Đây là phần “tăng tuyến tính” của AIMD, và nó hoạt động như sau. Mỗi khi nguồn gửi thành công một lượng gói bằng ``CongestionWindow``—tức là mỗi gói được gửi trong thời gian trễ vòng lặp (RTT) vừa qua đã được xác nhận—nó sẽ cộng thêm tương đương 1 gói vào ``CongestionWindow``. Sự tăng tuyến tính này được minh họa trong :numref:`Hình %s <fig-linear>`. Lưu ý rằng, trên thực tế, TCP không chờ đến khi nhận đủ một cửa sổ ACK mới cộng thêm 1 gói vào cửa sổ tắc nghẽn, mà thay vào đó tăng ``CongestionWindow`` một chút cho mỗi ACK nhận được. Cụ thể, cửa sổ tắc nghẽn được tăng như sau mỗi khi một ACK đến:

::

   Increment = MSS x (MSS/CongestionWindow)
   CongestionWindow += Increment

Nghĩa là, thay vì tăng ``CongestionWindow`` thêm toàn bộ ``MSS`` byte mỗi RTT, chúng ta tăng nó thêm một phần của ``MSS`` mỗi khi nhận được một ACK. Giả sử mỗi ACK xác nhận việc nhận ``MSS`` byte, thì phần đó là ``MSS/CongestionWindow``.

.. _fig-sawtooth:
.. figure:: figures/f06-09-9780123850591.png
   :width: 600px
   :align: center

   Mẫu hình răng cưa TCP điển hình.

Mẫu hình tăng và giảm liên tục của cửa sổ tắc nghẽn này tiếp tục trong suốt thời gian tồn tại của kết nối. Thực tế, nếu bạn vẽ giá trị hiện tại của ``CongestionWindow`` theo thời gian, bạn sẽ nhận được một mẫu hình răng cưa, như minh họa trong :numref:`Hình %s <fig-sawtooth>`. Khái niệm quan trọng cần hiểu về AIMD là nguồn sẵn sàng giảm cửa sổ tắc nghẽn với tốc độ nhanh hơn nhiều so với tốc độ tăng cửa sổ tắc nghẽn. Điều này trái ngược với chiến lược tăng tuyến tính/giảm tuyến tính, trong đó cửa sổ sẽ tăng thêm 1 gói khi nhận được một ACK và giảm đi 1 khi xảy ra hết thời gian chờ. Người ta đã chứng minh rằng AIMD là điều kiện cần thiết để một cơ chế kiểm soát tắc nghẽn ổn định.

Một giải thích trực quan về lý do tại sao TCP giảm cửa sổ mạnh mẽ và tăng nó một cách thận trọng là hậu quả của việc có cửa sổ quá lớn là cộng dồn. Điều này là do khi cửa sổ quá lớn, các gói bị loại bỏ sẽ được truyền lại, làm cho tắc nghẽn càng nghiêm trọng hơn. Việc thoát khỏi trạng thái này càng nhanh càng tốt là rất quan trọng.

Cuối cùng, vì hết thời gian chờ là dấu hiệu của tắc nghẽn kích hoạt giảm bội số, TCP cần cơ chế hết thời gian chờ chính xác nhất có thể. Chúng ta đã đề cập đến cơ chế hết thời gian chờ của TCP trong một chương trước, nên không nhắc lại ở đây. Hai điều chính cần nhớ về cơ chế đó là (1) thời gian chờ được đặt dựa trên cả RTT trung bình và độ lệch chuẩn của giá trị trung bình đó, và (2) do chi phí đo từng lần truyền với đồng hồ chính xác, TCP chỉ lấy mẫu thời gian trễ vòng lặp một lần mỗi RTT (thay vì mỗi gói) bằng một đồng hồ thô (500 ms).

6.3.2 Khởi động chậm
--------------------

Cơ chế tăng tuyến tính vừa mô tả là cách tiếp cận phù hợp khi nguồn đang hoạt động gần với dung lượng có sẵn của mạng, nhưng nó mất quá nhiều thời gian để tăng tốc một kết nối khi bắt đầu từ đầu. Do đó, TCP cung cấp một cơ chế thứ hai, trớ trêu thay lại gọi là *khởi động chậm*, được sử dụng để tăng cửa sổ tắc nghẽn nhanh chóng từ trạng thái khởi đầu. Khởi động chậm thực tế tăng cửa sổ tắc nghẽn theo cấp số nhân, thay vì tuyến tính.

Cụ thể, nguồn bắt đầu bằng cách đặt ``CongestionWindow`` thành một gói. Khi ACK cho gói này đến, TCP cộng thêm 1 vào ``CongestionWindow`` và sau đó gửi hai gói. Khi nhận được hai ACK tương ứng, TCP tăng ``CongestionWindow`` thêm 2—mỗi ACK một—và tiếp theo gửi bốn gói. Kết quả cuối cùng là TCP thực tế nhân đôi số lượng gói đang truyền mỗi RTT. :numref:`Hình %s <fig-exponential>` cho thấy sự tăng trưởng số lượng gói đang truyền trong quá trình khởi động chậm. So sánh điều này với sự tăng trưởng tuyến tính của tăng tuyến tính được minh họa trong :numref:`Hình %s <fig-linear>`.

.. _fig-exponential:
.. figure:: figures/f06-10-9780123850591.png
   :width: 200px
   :align: center

   Các gói đang truyền trong quá trình khởi động chậm.

Tại sao một cơ chế tăng theo cấp số nhân lại được gọi là “chậm” nghe có vẻ khó hiểu lúc đầu, nhưng có thể giải thích nếu đặt trong bối cảnh lịch sử phù hợp. Chúng ta cần so sánh khởi động chậm không phải với cơ chế tuyến tính của phần trước, mà với hành vi ban đầu của TCP. Hãy xem điều gì xảy ra khi một kết nối được thiết lập và nguồn bắt đầu gửi các gói—tức là khi nó hiện không có gói nào đang truyền. Nếu nguồn gửi nhiều gói như cửa sổ quảng bá cho phép—điều mà TCP đã làm trước khi phát triển khởi động chậm—thì ngay cả khi có một lượng băng thông khá lớn có sẵn trong mạng, các bộ định tuyến có thể không xử lý được lượng lớn gói này. Tất cả phụ thuộc vào lượng bộ nhớ đệm có sẵn tại các bộ định tuyến. Do đó, khởi động chậm được thiết kế để giãn cách các gói ra để không xảy ra hiện tượng bùng nổ này. Nói cách khác, mặc dù tăng trưởng theo cấp số nhân nhanh hơn tăng tuyến tính, khởi động chậm vẫn “chậm” hơn nhiều so với việc gửi toàn bộ lượng dữ liệu mà cửa sổ quảng bá cho phép cùng một lúc.

Thực tế có hai tình huống khác nhau mà khởi động chậm được sử dụng. Tình huống đầu tiên là ngay khi bắt đầu một kết nối, lúc này nguồn không biết sẽ có bao nhiêu gói có thể truyền tại một thời điểm. (Hãy nhớ rằng ngày nay TCP chạy trên mọi thứ từ các liên kết 1 Mbps đến 40 Gbps, nên không có cách nào để nguồn biết được dung lượng của mạng.) Trong tình huống này, khởi động chậm tiếp tục nhân đôi ``CongestionWindow`` mỗi RTT cho đến khi xảy ra mất gói, lúc đó một lần hết thời gian chờ sẽ khiến giảm bội số chia đôi ``CongestionWindow``.

Tình huống thứ hai mà khởi động chậm được sử dụng tinh tế hơn; nó xảy ra khi kết nối bị ngắt trong khi chờ hết thời gian chờ. Hãy nhớ cách thuật toán cửa sổ trượt của TCP hoạt động—khi một gói bị mất, nguồn cuối cùng sẽ gửi hết dữ liệu mà cửa sổ quảng bá cho phép, và sau đó bị chặn trong khi chờ một ACK sẽ không bao giờ đến. Cuối cùng, một lần hết thời gian chờ xảy ra, nhưng lúc này không còn gói nào đang truyền, nghĩa là nguồn sẽ không nhận được ACK nào để “đồng bộ” việc truyền các gói mới. Thay vào đó, nguồn sẽ nhận được một ACK cộng dồn duy nhất mở lại toàn bộ cửa sổ quảng bá, nhưng như đã giải thích ở trên, nguồn sau đó sử dụng khởi động chậm để khởi động lại luồng dữ liệu thay vì gửi toàn bộ lượng dữ liệu mà cửa sổ quảng bá cho phép cùng một lúc.

Mặc dù nguồn lại sử dụng khởi động chậm, nhưng lúc này nó biết nhiều thông tin hơn so với khi bắt đầu kết nối. Cụ thể, nguồn có một giá trị ``CongestionWindow`` hiện tại (và hữu ích); đây là giá trị ``CongestionWindow`` tồn tại trước khi mất gói cuối cùng, chia đôi do mất gói. Chúng ta có thể coi đây là *cửa sổ tắc nghẽn mục tiêu*. Khởi động chậm được sử dụng để tăng tốc độ gửi lên đến giá trị này, và sau đó tăng tuyến tính được sử dụng vượt quá điểm này. Lưu ý rằng chúng ta có một vấn đề ghi nhớ nhỏ cần giải quyết, đó là chúng ta muốn nhớ cửa sổ tắc nghẽn mục tiêu do giảm bội số cũng như cửa sổ tắc nghẽn *thực tế* đang được sử dụng bởi khởi động chậm. Để giải quyết vấn đề này, TCP giới thiệu một biến tạm thời để lưu trữ cửa sổ mục tiêu, thường gọi là ``CongestionThreshold``, được đặt bằng giá trị ``CongestionWindow`` sau khi giảm bội số. Biến ``CongestionWindow`` sau đó được đặt lại thành một gói, và được tăng thêm một gói cho mỗi ACK nhận được cho đến khi đạt ``CongestionThreshold``, tại thời điểm đó nó được tăng thêm một gói mỗi RTT.

Nói cách khác, TCP tăng cửa sổ tắc nghẽn như được định nghĩa trong đoạn mã sau:

.. code-block:: c

   {
       u_int    cw = state->CongestionWindow;
       u_int    incr = state->maxseg;

       if (cw > state->CongestionThreshold)
           incr = incr * incr / cw;
       state->CongestionWindow = MIN(cw + incr, TCP_MAXWIN);
   }

trong đó ``state`` đại diện cho trạng thái của một kết nối TCP cụ thể và xác định giới hạn trên về kích thước cửa sổ tắc nghẽn được phép tăng.

:numref:`Hình %s <fig-trace1>` theo dõi cách ``CongestionWindow`` của TCP tăng và giảm theo thời gian và minh họa sự kết hợp giữa khởi động chậm và tăng tuyến tính/giảm bội số. Dữ liệu này được lấy từ một kết nối TCP thực tế và cho thấy giá trị hiện tại của ``CongestionWindow``—đường màu—theo thời gian.

.. _fig-trace1:
.. figure:: figures/f06-11-9780123850591.png
   :width: 600px
   :align: center

   Hành vi của kiểm soát tắc nghẽn TCP. Đường màu = giá trị
   của CongestionWindow theo thời gian; chấm tròn ở đầu đồ thị
   = hết thời gian chờ; dấu gạch ở đầu đồ thị = thời điểm mỗi gói được
   truyền; thanh dọc = thời điểm một gói cuối cùng được truyền lại lần đầu.

Có một số điều cần chú ý về dữ liệu này. Đầu tiên là sự tăng nhanh của cửa sổ tắc nghẽn ở đầu kết nối. Điều này tương ứng với giai đoạn khởi động chậm ban đầu. Giai đoạn khởi động chậm tiếp tục cho đến khi một số gói bị mất vào khoảng 0,4 giây kể từ khi bắt đầu kết nối, lúc đó ``CongestionWindow`` dừng lại ở khoảng 34 KB. (Tại sao lại có nhiều gói bị mất trong khởi động chậm sẽ được thảo luận bên dưới.) Lý do cửa sổ tắc nghẽn dừng lại là không có ACK nào đến, do một số gói đã bị mất. Thực tế, không có gói mới nào được gửi trong thời gian này, như được thể hiện bằng việc không có dấu gạch nào ở đầu đồ thị. Một lần hết thời gian chờ cuối cùng xảy ra vào khoảng 2 giây, lúc đó cửa sổ tắc nghẽn bị chia đôi (tức là giảm từ khoảng 34 KB xuống còn khoảng 17 KB) và ``CongestionThreshold`` được đặt bằng giá trị này. Khởi động chậm sau đó đặt lại ``CongestionWindow`` thành một gói và bắt đầu tăng từ đó.

Không có đủ chi tiết trong dữ liệu để thấy chính xác điều gì xảy ra khi một vài gói bị mất ngay sau 2 giây, nên chúng ta chuyển sang giai đoạn tăng tuyến tính của cửa sổ tắc nghẽn diễn ra giữa 2 và 4 giây. Điều này tương ứng với tăng tuyến tính. Vào khoảng 4 giây, ``CongestionWindow`` lại dừng lại, một lần nữa do mất gói. Bây giờ, vào khoảng 5,5 giây:

1. Một lần hết thời gian chờ xảy ra, khiến cửa sổ tắc nghẽn bị chia đôi, giảm từ khoảng 22 KB xuống còn 11 KB, và ``CongestionThreshold`` được đặt bằng giá trị này.

2. ``CongestionWindow`` được đặt lại thành một gói, khi phía gửi vào giai đoạn khởi động chậm.

3. Khởi động chậm khiến ``CongestionWindow`` tăng theo cấp số nhân cho đến khi đạt ``CongestionThreshold``.

4. ``CongestionWindow`` sau đó tăng tuyến tính.

Mẫu hình này lặp lại vào khoảng 8 giây khi một lần hết thời gian chờ khác xảy ra.

Chúng ta quay lại câu hỏi tại sao lại có nhiều gói bị mất trong giai đoạn khởi động chậm ban đầu. Lúc này, TCP đang cố gắng tìm hiểu có bao nhiêu băng thông có sẵn trên mạng. Đây là một nhiệm vụ khó khăn. Nếu nguồn không chủ động ở giai đoạn này—ví dụ, nếu nó chỉ tăng cửa sổ tắc nghẽn tuyến tính—thì sẽ mất rất nhiều thời gian để phát hiện ra băng thông có sẵn. Điều này có thể ảnh hưởng nghiêm trọng đến thông lượng đạt được cho kết nối này. Ngược lại, nếu nguồn chủ động ở giai đoạn này, như TCP trong quá trình tăng trưởng theo cấp số nhân, thì nguồn có nguy cơ bị mất một nửa lượng gói của cửa sổ do mạng loại bỏ.

Để thấy điều gì có thể xảy ra trong quá trình tăng trưởng theo cấp số nhân, hãy xem xét tình huống nguồn vừa gửi thành công 16 gói qua mạng, khiến nó nhân đôi cửa sổ tắc nghẽn lên 32. Tuy nhiên, giả sử mạng chỉ đủ dung lượng để hỗ trợ 16 gói từ nguồn này. Kết quả có thể là 16 trong số 32 gói được gửi theo cửa sổ tắc nghẽn mới sẽ bị mạng loại bỏ; thực tế, đây là trường hợp xấu nhất, vì một số gói sẽ được lưu vào bộ nhớ đệm của một số bộ định tuyến. Vấn đề này sẽ ngày càng nghiêm trọng khi tích số trễ × băng thông của mạng tăng lên. Ví dụ, một tích số trễ × băng thông là 500 KB nghĩa là mỗi kết nối có khả năng mất tới 500 KB dữ liệu ở đầu mỗi kết nối. Tất nhiên, điều này giả định cả nguồn và đích đều triển khai phần mở rộng “cửa sổ lớn”.

Các phương án thay thế cho khởi động chậm, trong đó nguồn cố gắng ước lượng băng thông có sẵn bằng các phương pháp tinh vi hơn, cũng đã được nghiên cứu. Một ví dụ gọi là *khởi động nhanh* (quick-start). Ý tưởng cơ bản là một máy gửi TCP có thể yêu cầu tốc độ gửi ban đầu lớn hơn mức khởi động chậm cho phép bằng cách đặt tốc độ yêu cầu vào gói SYN dưới dạng một tùy chọn IP. Các bộ định tuyến trên đường đi có thể kiểm tra tùy chọn này, đánh giá mức độ tắc nghẽn hiện tại trên liên kết đầu ra cho luồng này, và quyết định xem tốc độ đó có chấp nhận được không, nếu tốc độ thấp hơn có thể chấp nhận được, hoặc nếu nên sử dụng khởi động chậm tiêu chuẩn. Khi gói SYN đến máy nhận, nó sẽ chứa hoặc là tốc độ được tất cả các bộ định tuyến trên đường đi chấp nhận, hoặc là chỉ báo rằng một hoặc nhiều bộ định tuyến không thể hỗ trợ yêu cầu khởi động nhanh. Trong trường hợp đầu tiên, máy gửi TCP sử dụng tốc độ đó để bắt đầu truyền; trong trường hợp thứ hai, nó quay lại sử dụng khởi động chậm tiêu chuẩn. Nếu TCP được phép bắt đầu gửi ở tốc độ cao hơn, một phiên có thể nhanh chóng đạt đến điểm lấp đầy đường truyền, thay vì mất nhiều vòng lặp để làm điều đó.

Rõ ràng một trong những thách thức của loại cải tiến này cho TCP là nó đòi hỏi sự hợp tác nhiều hơn từ các bộ định tuyến so với TCP tiêu chuẩn. Nếu chỉ một bộ định tuyến trên đường đi không hỗ trợ khởi động nhanh, thì hệ thống sẽ quay lại sử dụng khởi động chậm tiêu chuẩn. Do đó, có thể sẽ mất nhiều thời gian trước khi các loại cải tiến này được áp dụng rộng rãi trên Internet; hiện tại, chúng có nhiều khả năng được sử dụng trong các môi trường mạng kiểm soát (ví dụ, mạng nghiên cứu).

6.3.3 Truyền lại nhanh và phục hồi nhanh
----------------------------------------

Các cơ chế mô tả ở trên là một phần của đề xuất ban đầu để thêm kiểm soát tắc nghẽn vào TCP. Tuy nhiên, người ta sớm phát hiện ra rằng việc triển khai hết thời gian chờ thô của TCP dẫn đến các khoảng thời gian dài mà kết nối bị ngắt trong khi chờ bộ đếm thời gian hết hạn. Vì lý do này, một cơ chế mới gọi là *truyền lại nhanh* đã được thêm vào TCP. Truyền lại nhanh là một phương pháp dựa trên kinh nghiệm đôi khi kích hoạt việc truyền lại một gói bị mất sớm hơn cơ chế hết thời gian chờ thông thường. Cơ chế truyền lại nhanh không thay thế hết thời gian chờ thông thường; nó chỉ bổ sung cho chức năng đó.

Ý tưởng của truyền lại nhanh rất đơn giản. Mỗi khi một gói dữ liệu đến phía nhận, phía nhận sẽ phản hồi bằng một xác nhận, ngay cả khi số thứ tự này đã được xác nhận trước đó. Do đó, khi một gói đến không theo thứ tự—khi TCP chưa thể xác nhận dữ liệu trong gói đó vì dữ liệu trước đó chưa đến—TCP sẽ gửi lại cùng một xác nhận như lần trước. Việc gửi lại xác nhận giống nhau lần thứ hai này được gọi là một *ACK trùng lặp*. Khi phía gửi nhìn thấy một ACK trùng lặp, nó biết rằng phía bên kia chắc chắn đã nhận được một gói không theo thứ tự, điều này cho thấy một gói trước đó có thể đã bị mất. Vì cũng có thể gói trước đó chỉ bị trễ chứ không bị mất, phía gửi sẽ chờ cho đến khi thấy một số lượng ACK trùng lặp nhất định rồi mới truyền lại gói bị mất. Trên thực tế, TCP chờ đến khi thấy ba ACK trùng lặp trước khi truyền lại gói.

.. _fig-tcp-fast:
.. figure:: figures/f06-12-9780123850591.png
   :width: 300px
   :align: center

   Truyền lại nhanh dựa trên các ACK trùng lặp.

:numref:`Hình %s <fig-tcp-fast>` minh họa cách các ACK trùng lặp dẫn đến truyền lại nhanh. Trong ví dụ này, đích nhận được các gói 1 và 2, nhưng gói 3 bị mất trong mạng. Do đó, đích sẽ gửi một ACK trùng lặp cho gói 2 khi gói 4 đến, lại gửi khi gói 5 đến, v.v. (Để đơn giản, ví dụ này sử dụng các gói 1, 2, 3, v.v., thay vì quan tâm đến số thứ tự của từng byte.) Khi phía gửi nhìn thấy ACK trùng lặp thứ ba cho gói 2—ACK được gửi vì phía nhận đã nhận được gói 6—nó sẽ truyền lại gói 3. Lưu ý rằng khi bản sao truyền lại của gói 3 đến đích, phía nhận sẽ gửi một ACK cộng dồn cho tất cả các gói đến và bao gồm cả gói 6 về phía nguồn.

.. _fig-trace2:
.. figure:: figures/f06-13-9780123850591.png
   :width: 600px
   :align: center

   Dữ liệu TCP với truyền lại nhanh. Đường màu
   = CongestionWindow; chấm tròn = hết thời gian chờ; dấu gạch = thời điểm
   mỗi gói được truyền; thanh dọc = thời điểm một gói cuối cùng được truyền lại lần đầu.

:numref:`Hình %s <fig-trace2>` minh họa hành vi của một phiên bản TCP với cơ chế truyền lại nhanh. Thật thú vị khi so sánh dữ liệu này với dữ liệu trong :numref:`Hình %s <fig-trace1>`, nơi truyền lại nhanh chưa được triển khai—các khoảng thời gian dài mà cửa sổ tắc nghẽn giữ nguyên và không có gói nào được gửi đã bị loại bỏ. Nói chung, kỹ thuật này có thể loại bỏ khoảng một nửa số lần hết thời gian chờ thô trên một kết nối TCP điển hình, dẫn đến cải thiện khoảng 20% thông lượng so với những gì có thể đạt được nếu không có nó. Tuy nhiên, lưu ý rằng chiến lược truyền lại nhanh không loại bỏ hoàn toàn các lần hết thời gian chờ thô. Điều này là do với kích thước cửa sổ nhỏ sẽ không có đủ gói đang truyền để tạo ra đủ số ACK trùng lặp. Nếu mất đủ nhiều gói—ví dụ, như xảy ra trong giai đoạn khởi động chậm ban đầu—thuật toán cửa sổ trượt cuối cùng sẽ chặn phía gửi cho đến khi xảy ra hết thời gian chờ. Trên thực tế, cơ chế truyền lại nhanh của TCP có thể phát hiện tối đa ba gói bị mất trên mỗi cửa sổ.

Cuối cùng, còn một cải tiến nữa có thể thực hiện. Khi cơ chế truyền lại nhanh báo hiệu tắc nghẽn, thay vì giảm cửa sổ tắc nghẽn về một gói và chạy khởi động chậm, có thể sử dụng các ACK vẫn còn trong đường truyền để điều tiết việc gửi các gói. Cơ chế này, gọi là *phục hồi nhanh*, thực tế loại bỏ giai đoạn khởi động chậm xảy ra giữa lúc truyền lại nhanh phát hiện gói bị mất và tăng tuyến tính bắt đầu. Ví dụ, phục hồi nhanh tránh được giai đoạn khởi động chậm giữa 3,8 và 4 giây trong :numref:`Hình %s <fig-trace2>` và thay vào đó chỉ đơn giản là giảm cửa sổ tắc nghẽn một nửa (từ 22 KB xuống 11 KB) và tiếp tục tăng tuyến tính. Nói cách khác, khởi động chậm chỉ được sử dụng ở đầu kết nối và bất cứ khi nào xảy ra hết thời gian chờ thô. Ở tất cả các thời điểm khác, cửa sổ tắc nghẽn tuân theo mô hình tăng tuyến tính/giảm bội số thuần túy.

6.3.4 TCP CUBIC
---------------

Một biến thể của thuật toán TCP tiêu chuẩn vừa mô tả, gọi là CUBIC, là thuật toán kiểm soát tắc nghẽn mặc định được phân phối cùng Linux. Mục tiêu chính của CUBIC là hỗ trợ các mạng có tích số trễ × băng thông lớn, đôi khi gọi là *mạng dài-béo* (long-fat networks). Các mạng như vậy gặp phải vấn đề thuật toán TCP gốc cần quá nhiều vòng lặp để đạt đến dung lượng có sẵn của đường truyền đầu-cuối. CUBIC làm điều này bằng cách chủ động hơn trong việc tăng kích thước cửa sổ, nhưng tất nhiên vấn đề là phải chủ động hơn mà không gây ảnh hưởng xấu đến các luồng khác.

Một khía cạnh quan trọng trong cách tiếp cận của CUBIC là điều chỉnh cửa sổ tắc nghẽn theo các khoảng thời gian đều đặn, dựa trên lượng thời gian đã trôi qua kể từ sự kiện tắc nghẽn cuối cùng (ví dụ, sự xuất hiện của một ACK trùng lặp), thay vì chỉ khi các ACK đến (cái sau phụ thuộc vào RTT). Điều này cho phép CUBIC hoạt động công bằng khi cạnh tranh với các luồng có RTT ngắn, vốn sẽ nhận ACK thường xuyên hơn.

.. _fig-cubic:
.. figure:: figures/tcp/Slide1.png
   :width: 500px
   :align: center

   Hàm bậc ba tổng quát minh họa sự thay đổi của cửa sổ tắc nghẽn
   theo thời gian.

Khía cạnh quan trọng thứ hai của CUBIC là sử dụng một hàm bậc ba để điều chỉnh cửa sổ tắc nghẽn. Ý tưởng cơ bản dễ hiểu nhất khi nhìn vào hình dạng tổng quát của một hàm bậc ba, có ba giai đoạn: tăng chậm, bằng phẳng, tăng nhanh. Một ví dụ tổng quát được thể hiện trong :numref:`Hình %s <fig-cubic>`, trong đó chúng tôi đã chú thích thêm một thông tin: kích thước cửa sổ tắc nghẽn lớn nhất đạt được ngay trước sự kiện tắc nghẽn cuối cùng làm mục tiêu (ký hiệu :math:`W_{max}`). Ý tưởng là bắt đầu nhanh nhưng làm chậm tốc độ tăng khi đến gần :math:`W_{max}`, thận trọng và gần như không tăng khi ở gần :math:`W_{max}`, và sau đó tăng tốc độ khi rời xa :math:`W_{max}`. Giai đoạn sau thực chất là dò tìm một :math:`W_{max}` mới có thể đạt được.

Cụ thể, CUBIC tính toán cửa sổ tắc nghẽn như một hàm của thời gian (t) kể từ sự kiện tắc nghẽn cuối cùng

.. math::

   \mathsf{CWND(t)} = \mathsf{C} \times \mathsf{(t-K)}^{3} + \mathsf{W}_{max}

trong đó

.. math::

   \mathsf{K} =  \sqrt[3]{\mathsf{W}_{max} \times (1 - \beta{})/\mathsf{C}}

C là hằng số tỷ lệ và :math:`\beta` là hệ số giảm bội số. CUBIC đặt giá trị này là 0,7 thay vì 0,5 như TCP tiêu chuẩn. Nhìn lại :numref:`Hình %s <fig-cubic>`, CUBIC thường được mô tả là chuyển từ một hàm lõm sang một hàm lồi (trong khi hàm tuyến tính của TCP tiêu chuẩn chỉ là lồi).
