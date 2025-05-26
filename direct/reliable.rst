2.5 Truyền Dữ Liệu Đáng Tin Cậy
===============================

Như chúng ta đã thấy ở phần trước, các khung dữ liệu đôi khi bị hỏng trong quá trình truyền, với một mã kiểm tra lỗi như CRC được sử dụng để phát hiện các lỗi này. Mặc dù một số mã kiểm tra lỗi đủ mạnh để sửa lỗi, nhưng trên thực tế, chi phí phụ trội thường quá lớn để xử lý phạm vi lỗi bit và lỗi chùm có thể xuất hiện trên một liên kết mạng. Ngay cả khi các mã sửa lỗi được sử dụng (ví dụ, trên các liên kết không dây), một số lỗi sẽ quá nghiêm trọng để có thể sửa được. Kết quả là, một số khung dữ liệu bị hỏng phải bị loại bỏ. Một giao thức ở tầng liên kết muốn truyền dữ liệu đáng tin cậy phải bằng cách nào đó phục hồi từ các khung bị loại bỏ (mất mát) này.

Cần lưu ý rằng tính đáng tin cậy là một chức năng *có thể* được cung cấp ở tầng liên kết, nhưng nhiều công nghệ liên kết hiện đại bỏ qua chức năng này. Hơn nữa, truyền dữ liệu đáng tin cậy thường được cung cấp ở các tầng cao hơn, bao gồm cả tầng vận chuyển và đôi khi là tầng ứng dụng. Việc nên cung cấp ở đâu là một vấn đề còn nhiều tranh luận và phụ thuộc vào nhiều yếu tố. Chúng tôi mô tả các nguyên lý cơ bản của truyền dữ liệu đáng tin cậy ở đây, vì các nguyên lý này là chung cho các tầng, nhưng bạn nên biết rằng chúng tôi không chỉ nói về chức năng ở tầng liên kết.

Truyền dữ liệu đáng tin cậy thường được thực hiện bằng sự kết hợp của hai cơ chế cơ bản—*thông báo xác nhận* và *hết thời gian chờ*. Một thông báo xác nhận (viết tắt là ACK) là một khung điều khiển nhỏ mà một giao thức gửi lại cho đối tác của nó để báo rằng nó đã nhận được một khung trước đó. Khung điều khiển ở đây nghĩa là một tiêu đề không có dữ liệu, mặc dù một giao thức có thể *ghép* ACK vào một khung dữ liệu mà nó tình cờ gửi theo hướng ngược lại. Việc nhận được một thông báo xác nhận cho biết với người gửi rằng khung của nó đã được chuyển thành công. Nếu người gửi không nhận được thông báo xác nhận sau một khoảng thời gian hợp lý, thì nó sẽ *gửi lại* khung ban đầu. Hành động chờ một khoảng thời gian hợp lý này được gọi là *hết thời gian chờ*.

Chiến lược tổng quát sử dụng thông báo xác nhận và hết thời gian chờ để thực hiện truyền dữ liệu đáng tin cậy đôi khi được gọi là *yêu cầu lặp lại tự động* (viết tắt là ARQ). Phần này mô tả ba thuật toán ARQ khác nhau bằng ngôn ngữ tổng quát; tức là, chúng tôi không đưa ra thông tin chi tiết về các trường tiêu đề của một giao thức cụ thể.

2.5.1 Dừng-và-Đợi
-----------------

Thuật toán ARQ đơn giản nhất là thuật toán *dừng-và-đợi*. Ý tưởng của dừng-và-đợi rất đơn giản: Sau khi truyền một khung, người gửi chờ một thông báo xác nhận trước khi truyền khung tiếp theo. Nếu thông báo xác nhận không đến sau một khoảng thời gian nhất định, người gửi sẽ hết thời gian chờ và gửi lại khung ban đầu.

.. _fig-ack-timeout:
.. figure:: figures/f02-17-9780123850591.png
   :width: 500px
   :align: center

   Dòng thời gian minh họa bốn kịch bản khác nhau cho
   thuật toán dừng-và-đợi. (a) ACK được nhận trước khi bộ đếm thời gian hết hạn; (b) khung ban đầu bị mất; (c) ACK bị mất; (d) bộ đếm thời gian hết hạn quá sớm.

:numref:`Hình %s <fig-ack-timeout>` minh họa dòng thời gian cho bốn kịch bản khác nhau xuất phát từ thuật toán cơ bản này. Bên gửi được thể hiện ở bên trái, bên nhận được mô tả ở bên phải, và thời gian trôi từ trên xuống dưới. :numref:`Hình %s(a) <fig-ack-timeout>` cho thấy tình huống ACK được nhận trước khi bộ đếm thời gian hết hạn; (b) và (c) cho thấy tình huống khung ban đầu và ACK, tương ứng, bị mất; và (d) cho thấy tình huống bộ đếm thời gian hết hạn quá sớm. Hãy nhớ rằng “mất” ở đây nghĩa là khung bị hỏng trong quá trình truyền, lỗi này được phát hiện bởi mã kiểm tra lỗi ở phía nhận, và khung sau đó bị loại bỏ.

Các dòng thời gian gói tin được trình bày trong phần này là ví dụ về một công cụ thường được sử dụng trong giảng dạy, giải thích và thiết kế giao thức. Chúng hữu ích vì chúng thể hiện trực quan hành vi theo thời gian của một hệ thống phân tán—một điều có thể khá khó để phân tích. Khi thiết kế một giao thức, bạn thường phải chuẩn bị cho những điều bất ngờ—hệ thống bị treo, một thông điệp bị mất, hoặc điều gì đó mà bạn mong đợi xảy ra nhanh lại mất nhiều thời gian. Những sơ đồ như vậy thường giúp chúng ta hiểu điều gì có thể xảy ra sai và do đó giúp nhà thiết kế giao thức chuẩn bị cho mọi tình huống.

Có một điểm tinh tế quan trọng trong thuật toán dừng-và-đợi. Giả sử người gửi gửi một khung và người nhận xác nhận nó, nhưng thông báo xác nhận bị mất hoặc bị trễ khi đến. Tình huống này được minh họa trong dòng thời gian (c) và (d) của :numref:`Hình %s <fig-ack-timeout>`. Trong cả hai trường hợp, người gửi hết thời gian chờ và gửi lại khung ban đầu, nhưng người nhận sẽ nghĩ rằng đó là khung tiếp theo, vì nó đã nhận và xác nhận đúng khung đầu tiên. Điều này có thể dẫn đến việc chuyển giao các bản sao trùng lặp của một khung. Để giải quyết vấn đề này, tiêu đề cho một giao thức dừng-và-đợi thường bao gồm một số thứ tự 1 bit—tức là, số thứ tự có thể nhận các giá trị 0 và 1—và các số thứ tự được sử dụng cho mỗi khung sẽ luân phiên, như minh họa trong :numref:`Hình %s <fig-stop-wait>`. Do đó, khi người gửi gửi lại khung 0, người nhận có thể xác định rằng nó đang thấy bản sao thứ hai của khung 0 chứ không phải bản đầu tiên của khung 1 và do đó có thể bỏ qua nó (người nhận vẫn xác nhận nó, trong trường hợp ACK đầu tiên bị mất).

.. _fig-stop-wait:
.. figure:: figures/f02-18-9780123850591.png
   :width: 250px
   :align: center

   Dòng thời gian cho dừng-và-đợi với số thứ tự 1 bit.

Điểm hạn chế chính của thuật toán dừng-và-đợi là nó chỉ cho phép người gửi có một khung đang chờ trên liên kết tại một thời điểm, và điều này có thể thấp hơn nhiều so với năng lực của liên kết. Hãy xem xét, ví dụ, một liên kết 1,5 Mbps với thời gian khứ hồi 45 ms. Liên kết này có tích số trễ × băng thông là 67,5 Kb, hoặc khoảng 8 KB. Vì người gửi chỉ có thể gửi một khung mỗi vòng khứ hồi, và giả sử kích thước khung là 1 KB, điều này ngụ ý tốc độ gửi tối đa là

.. centered:: Số bit mỗi khung / Thời gian mỗi khung = 1024 x 8 / 0.045 = 182 kbps

hoặc khoảng một phần tám năng lực của liên kết. Để sử dụng hết liên kết, chúng ta muốn người gửi có thể truyền tối đa tám khung trước khi phải chờ xác nhận.

.. _key-pipe-full:
.. admonition::  Bài Học Chính

   Ý nghĩa của tích số trễ × băng thông là nó đại diện cho lượng dữ liệu có thể đang trong quá trình truyền. Chúng ta muốn có thể gửi từng đó dữ liệu mà không phải chờ xác nhận đầu tiên. Nguyên lý ở đây thường được gọi là *giữ cho ống luôn đầy*. Các thuật toán được trình bày trong hai phần tiếp theo sẽ làm chính xác điều này. :ref:`[Tiếp theo] <key-separate-concerns>`

2.5.2 Cửa Sổ Trượt
------------------

Hãy xem lại kịch bản trong đó liên kết có tích số trễ × băng thông là 8 KB và các khung có kích thước 1 KB. Chúng ta muốn người gửi sẵn sàng truyền khung thứ chín ngay khi ACK cho khung đầu tiên đến. Thuật toán cho phép chúng ta làm điều này được gọi là *cửa sổ trượt*, và một dòng thời gian minh họa được đưa ra trong :numref:`Hình %s <fig-slide-win>`.

.. _fig-slide-win:
.. figure:: figures/f02-19-9780123850591.png
   :width: 250px
   :align: center

   Dòng thời gian cho thuật toán cửa sổ trượt.

Thuật Toán Cửa Sổ Trượt
~~~~~~~~~~~~~~~~~~~~~~~

Thuật toán cửa sổ trượt hoạt động như sau. Đầu tiên, người gửi gán một *số thứ tự*, ký hiệu ``SeqNum``, cho mỗi khung. Hiện tại, hãy bỏ qua thực tế rằng ``SeqNum`` được triển khai bằng một trường tiêu đề có kích thước hữu hạn và giả sử rằng nó có thể tăng vô hạn. Người gửi duy trì ba biến: *kích thước cửa sổ gửi*, ký hiệu ``SWS``, cho biết giới hạn trên của số khung đang chờ (chưa được xác nhận) mà người gửi có thể truyền; ``LAR`` là số thứ tự của *ACK cuối cùng đã nhận*; và ``LFS`` là số thứ tự của *khung cuối cùng đã gửi*. Người gửi cũng duy trì bất biến sau:

::

   LFS - LAR <= SWS

Tình huống này được minh họa trong :numref:`Hình %s <fig-sw-sender>`.

.. _fig-sw-sender:
.. figure:: figures/f02-20-9780123850591.png
   :width: 400px
   :align: center

   Cửa sổ trượt ở phía gửi.

Khi một thông báo xác nhận đến, người gửi di chuyển ``LAR`` sang phải, cho phép người gửi truyền thêm một khung nữa. Ngoài ra, người gửi liên kết một bộ đếm thời gian với mỗi khung nó truyền, và nó sẽ truyền lại khung nếu bộ đếm thời gian hết hạn trước khi nhận được ACK. Lưu ý rằng người gửi phải sẵn sàng lưu trữ tối đa ``SWS`` khung vì nó phải chuẩn bị truyền lại chúng cho đến khi chúng được xác nhận.

Người nhận duy trì ba biến sau: *kích thước cửa sổ nhận*, ký hiệu ``RWS``, cho biết giới hạn trên của số khung đến không theo thứ tự mà người nhận sẵn sàng chấp nhận; ``LAF`` là số thứ tự của *khung lớn nhất có thể chấp nhận*; và ``LFR`` là số thứ tự của *khung cuối cùng đã nhận*. Người nhận cũng duy trì bất biến sau:

::

   LAF - LFR <= RWS

Tình huống này được minh họa trong :numref:`Hình %s <fig-sw-rcvr>`.

.. _fig-sw-rcvr:
.. figure:: figures/f02-21-9780123850591.png
   :width: 400px
   :align: center

   Cửa sổ trượt ở phía nhận.

Khi một khung với số thứ tự ``SeqNum`` đến, người nhận thực hiện hành động sau. Nếu ``SeqNum <= LFR`` hoặc ``SeqNum > LAF``, thì khung nằm ngoài cửa sổ của người nhận và bị loại bỏ. Nếu ``LFR < SeqNum <= LAF``, thì khung nằm trong cửa sổ nhận và được chấp nhận. Bây giờ người nhận cần quyết định có gửi ACK hay không. Gọi ``SeqNumToAck`` là số thứ tự lớn nhất chưa được xác nhận, sao cho tất cả các khung có số thứ tự nhỏ hơn hoặc bằng ``SeqNumToAck`` đã được nhận. Người nhận xác nhận việc nhận ``SeqNumToAck``, ngay cả khi các gói có số thứ tự cao hơn đã được nhận. Thông báo xác nhận này được gọi là xác nhận tích lũy. Sau đó, nó đặt ``LFR = SeqNumToAck`` và điều chỉnh ``LAF = LFR + RWS``.

Ví dụ, giả sử ``LFR = 5`` (tức là ACK cuối cùng người nhận gửi là cho số thứ tự 5), và ``RWS = 4``. Điều này ngụ ý rằng ``LAF = 9``. Nếu các khung 7 và 8 đến, chúng sẽ được lưu trữ vì chúng nằm trong cửa sổ nhận. Tuy nhiên, không cần gửi ACK vì khung 6 chưa đến. Các khung 7 và 8 được coi là đến không theo thứ tự. (Về mặt kỹ thuật, người nhận có thể gửi lại ACK cho khung 5 khi các khung 7 và 8 đến.) Nếu khung 6 sau đó đến—có thể nó đến muộn vì bị mất lần đầu và phải truyền lại, hoặc đơn giản là bị trễ—người nhận xác nhận khung 8, tăng ``LFR`` lên 8, và đặt ``LAF`` thành 12.\ [#]_ Nếu khung 6 thực sự bị mất, thì một bộ đếm thời gian sẽ hết hạn ở phía gửi, khiến nó truyền lại khung 6.

.. [#] Mặc dù khó có khả năng một gói bị trễ hoặc đến không theo thứ tự trên một liên kết điểm-điểm, thuật toán này cũng được sử dụng trên các kết nối nhiều bước nhảy nơi những sự chậm trễ như vậy có thể xảy ra.

Chúng ta nhận thấy rằng khi một bộ đếm thời gian hết hạn, lượng dữ liệu đang truyền giảm xuống, vì người gửi không thể mở rộng cửa sổ cho đến khi khung 6 được xác nhận. Điều này có nghĩa là khi xảy ra mất gói, cơ chế này không còn giữ cho ống luôn đầy. Càng mất nhiều thời gian để phát hiện ra một gói bị mất, vấn đề này càng nghiêm trọng.

Lưu ý rằng, trong ví dụ này, người nhận có thể đã gửi một *thông báo xác nhận âm* (NAK) cho khung 6 ngay khi khung 7 đến. Tuy nhiên, điều này là không cần thiết vì cơ chế hết thời gian chờ của người gửi là đủ để phát hiện tình huống này, và việc gửi NAK làm tăng thêm độ phức tạp cho phía nhận. Ngoài ra, như đã đề cập, việc gửi thêm các ACK cho khung 5 khi các khung 7 và 8 đến là hợp lệ; trong một số trường hợp, người gửi có thể sử dụng các ACK trùng lặp như một dấu hiệu cho thấy một khung đã bị mất. Cả hai cách tiếp cận đều giúp cải thiện hiệu suất bằng cách cho phép phát hiện sớm các gói bị mất.

Một biến thể khác của cơ chế này là sử dụng *xác nhận chọn lọc* (selective acknowledgments). Tức là, người nhận có thể xác nhận chính xác những khung mà nó đã nhận thay vì chỉ xác nhận khung có số thứ tự cao nhất nhận được theo thứ tự. Vì vậy, trong ví dụ trên, người nhận có thể xác nhận việc nhận các khung 7 và 8. Việc cung cấp nhiều thông tin hơn cho người gửi giúp người gửi dễ dàng giữ cho ống luôn đầy hơn nhưng làm tăng độ phức tạp khi triển khai.

Kích thước cửa sổ gửi được chọn dựa trên số lượng khung mà chúng ta muốn có thể đang chờ trên liên kết tại một thời điểm; ``SWS`` dễ dàng tính toán cho một tích số trễ × băng thông nhất định. Ngược lại, người nhận có thể đặt ``RWS`` theo ý muốn. Hai thiết lập phổ biến là ``RWS = 1``, nghĩa là người nhận sẽ không lưu trữ bất kỳ khung nào đến không theo thứ tự, và ``RWS = SWS``, nghĩa là người nhận có thể lưu trữ bất kỳ khung nào mà người gửi truyền. Không có ý nghĩa gì khi đặt ``RWS > SWS`` vì không thể có nhiều hơn ``SWS`` khung đến không theo thứ tự.

Số Thứ Tự Hữu Hạn và Cửa Sổ Trượt
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Bây giờ chúng ta quay lại một giả định đơn giản mà chúng ta đã đưa ra trong thuật toán—giả định rằng số thứ tự có thể tăng vô hạn. Trên thực tế, số thứ tự của một khung được chỉ định trong một trường tiêu đề có kích thước hữu hạn. Ví dụ, một trường 3 bit nghĩa là có tám số thứ tự khả dụng, 0..7. Điều này khiến chúng ta phải sử dụng lại số thứ tự hoặc, nói cách khác, số thứ tự sẽ quay vòng. Điều này dẫn đến vấn đề phân biệt giữa các lần xuất hiện khác nhau của cùng một số thứ tự, điều này ngụ ý rằng số lượng số thứ tự khả dụng phải lớn hơn số khung đang chờ tối đa. Ví dụ, dừng-và-đợi cho phép một khung đang chờ tại một thời điểm và có hai số thứ tự khác nhau.

Giả sử chúng ta có nhiều hơn một số trong không gian số thứ tự so với số khung có thể đang chờ; tức là, ``SWS <= MaxSeqNum - 1``, trong đó ``MaxSeqNum`` là số lượng số thứ tự khả dụng. Điều này có đủ không? Câu trả lời phụ thuộc vào ``RWS``. Nếu ``RWS = 1``, thì ``MaxSeqNum >= SWS + 1`` là đủ. Nếu ``RWS`` bằng ``SWS``, thì chỉ có một số thứ tự nhiều hơn kích thước cửa sổ gửi là không đủ. Để thấy điều này, hãy xem xét tình huống có tám số thứ tự 0 đến 7, và ``SWS = RWS = 7``. Giả sử người gửi truyền các khung 0..6, chúng được nhận thành công, nhưng các ACK bị mất. Người nhận bây giờ đang chờ các khung 7, 0..5, nhưng người gửi hết thời gian chờ và gửi lại các khung 0..6. Thật không may, người nhận đang chờ lần xuất hiện thứ hai của các khung 0..5 nhưng lại nhận được lần xuất hiện đầu tiên của các khung này. Đây chính xác là tình huống mà chúng ta muốn tránh.

Hóa ra kích thước cửa sổ gửi không được lớn hơn một nửa số lượng số thứ tự khả dụng khi ``RWS = SWS``, hoặc nói chính xác hơn,

::

   SWS < (MaxSeqNum + 1)/ 2

Trực giác mà điều này nói lên là giao thức cửa sổ trượt luân phiên giữa hai nửa không gian số thứ tự, giống như dừng-và-đợi luân phiên giữa số thứ tự 0 và 1. Sự khác biệt duy nhất là nó liên tục trượt giữa hai nửa thay vì luân phiên rời rạc.

Lưu ý rằng quy tắc này chỉ áp dụng cho trường hợp ``RWS = SWS``. Chúng tôi để lại cho bạn bài tập xác định quy tắc tổng quát hơn cho các giá trị tùy ý của ``RWS`` và ``SWS``. Ngoài ra, mối quan hệ giữa kích thước cửa sổ và không gian số thứ tự phụ thuộc vào một giả định rất rõ ràng mà dễ bị bỏ qua, đó là các khung không bị đảo thứ tự trong quá trình truyền. Điều này không thể xảy ra trên một liên kết điểm-điểm trực tiếp vì không có cách nào để một khung vượt qua khung khác trong quá trình truyền. Tuy nhiên, chúng ta sẽ thấy thuật toán cửa sổ trượt được sử dụng trong các môi trường khác, và chúng ta sẽ cần xây dựng một quy tắc khác.

Triển Khai Cửa Sổ Trượt
~~~~~~~~~~~~~~~~~~~~~~~

Các thủ tục sau đây minh họa cách chúng ta có thể triển khai phía gửi và phía nhận của thuật toán cửa sổ trượt. Các thủ tục này được lấy từ một giao thức hoạt động thực tế có tên, rất phù hợp, là Giao Thức Cửa Sổ Trượt (SWP). Để không phải quan tâm đến các giao thức liền kề trong đồ thị giao thức, chúng tôi gọi giao thức nằm trên SWP là giao thức cấp cao (HLP) và giao thức nằm dưới SWP là giao thức tầng liên kết (LLP).

Chúng ta bắt đầu bằng cách định nghĩa một cặp cấu trúc dữ liệu. Đầu tiên, tiêu đề khung rất đơn giản: Nó chứa một số thứ tự (``SeqNum``) và một số xác nhận (``AckNum``). Nó cũng chứa một trường ``Flags`` cho biết liệu khung là ACK hay mang dữ liệu.

.. code-block:: c

   typedef uint8_t SwpSeqno;

   typedef struct {
       SwpSeqno   SeqNum;   /* số thứ tự của khung này */
       SwpSeqno   AckNum;   /* xác nhận khung đã nhận */
       uint8_t     Flags;   /* tối đa 8 bit cờ */
   } SwpHdr;

Tiếp theo, trạng thái của thuật toán cửa sổ trượt có cấu trúc như sau. Đối với phía gửi của giao thức, trạng thái này bao gồm các biến ``LAR`` và ``LFS``, như đã mô tả ở phần trước, cũng như một hàng đợi chứa các khung đã được truyền nhưng chưa được xác nhận (``sendQ``). Trạng thái gửi cũng bao gồm một *semaphore đếm* gọi là ``sendWindowNotFull``. Chúng ta sẽ thấy cách sử dụng nó bên dưới, nhưng nói chung, semaphore là một nguyên thủy đồng bộ hóa hỗ trợ các thao tác ``semWait`` và ``semSignal``. Mỗi lần gọi ``semSignal`` sẽ tăng semaphore lên 1, và mỗi lần gọi ``semWait`` sẽ giảm ``s`` đi 1, với tiến trình gọi bị chặn (tạm dừng) nếu việc giảm semaphore khiến giá trị của nó nhỏ hơn 0. Một tiến trình bị chặn trong khi gọi ``semWait`` sẽ được phép tiếp tục ngay khi đủ các thao tác ``semSignal`` được thực hiện để tăng giá trị semaphore lên trên 0.

Đối với phía nhận của giao thức, trạng thái bao gồm biến ``NFE``. Đây là *khung tiếp theo mong đợi*, khung có số thứ tự lớn hơn một so với khung cuối cùng đã nhận (LFR), như đã mô tả ở phần trước. Cũng có một hàng đợi chứa các khung đã nhận không theo thứ tự (``recvQ``). Cuối cùng, mặc dù không được hiển thị, kích thước cửa sổ gửi và nhận được xác định bởi các hằng số ``SWS`` và ``RWS``, tương ứng.

.. code-block:: c

   typedef struct {
       /* trạng thái phía gửi: */
       SwpSeqno    LAR;        /* số thứ tự ACK cuối cùng đã nhận */
       SwpSeqno    LFS;        /* khung cuối cùng đã gửi */
       Semaphore   sendWindowNotFull;
       SwpHdr      hdr;        /* tiêu đề đã khởi tạo sẵn */
       struct sendQ_slot {
           Event   timeout;    /* sự kiện liên kết với hết thời gian gửi */
           Msg     msg;
       }   sendQ[SWS];

       /* trạng thái phía nhận: */
       SwpSeqno    NFE;       /* số thứ tự khung tiếp theo mong đợi */
       struct recvQ_slot {
           int     received;  /* tin nhắn có hợp lệ không? */
           Msg     msg;
       }   recvQ[RWS];
   } SwpState;

Phía gửi của SWP được triển khai bởi thủ tục ``sendSWP``. Thủ tục này khá đơn giản. Đầu tiên, ``semWait`` khiến tiến trình này bị chặn trên semaphore cho đến khi có thể gửi thêm một khung. Khi được phép tiếp tục, ``sendSWP`` đặt số thứ tự trong tiêu đề của khung, lưu một bản sao của khung vào hàng đợi truyền (``sendQ``), lên lịch một sự kiện hết thời gian để xử lý trường hợp khung không được xác nhận, và gửi khung đến giao thức tầng dưới, mà chúng tôi gọi là ``LINK``.

Một chi tiết đáng chú ý là lời gọi ``store_swp_hdr`` ngay trước lời gọi ``msgAddHdr``. Thủ tục này chuyển đổi cấu trúc C chứa tiêu đề SWP (``state->hdr``) thành một chuỗi byte có thể được gắn an toàn vào đầu tin nhắn (``hbuf``). Thủ tục này (không được hiển thị) phải chuyển đổi từng trường số nguyên trong tiêu đề sang thứ tự byte mạng và loại bỏ bất kỳ phần đệm nào mà trình biên dịch đã thêm vào cấu trúc C. Vấn đề về thứ tự byte là một vấn đề không nhỏ, nhưng hiện tại chỉ cần giả định rằng thủ tục này đặt bit có ý nghĩa nhất của một số nguyên nhiều từ vào byte có địa chỉ cao nhất.

Một điểm phức tạp khác trong thủ tục này là việc sử dụng ``semWait`` và semaphore ``sendWindowNotFull``. ``sendWindowNotFull`` được khởi tạo bằng kích thước cửa sổ gửi của người gửi, ``SWS`` (việc khởi tạo này không được hiển thị). Mỗi lần người gửi truyền một khung, thao tác ``semWait`` sẽ giảm giá trị này và chặn người gửi nếu giá trị giảm xuống 0. Mỗi lần một ACK được nhận, thao tác ``semSignal`` được gọi trong ``deliverSWP`` (xem bên dưới) sẽ tăng giá trị này, do đó giải phóng bất kỳ người gửi nào đang chờ.

.. code-block:: c

   static int
   sendSWP(SwpState *state, Msg *frame)
   {
       struct sendQ_slot *slot;
       char hbuf[HLEN];

       /* chờ cửa sổ gửi mở */
       semWait(&state->sendWindowNotFull);
       state->hdr.SeqNum = ++state->LFS;
       slot = &state->sendQ[state->hdr.SeqNum % SWS];
       store_swp_hdr(state->hdr, hbuf);
       msgAddHdr(frame, hbuf, HLEN);
       msgSaveCopy(&slot->msg, frame);
       slot->timeout = evSchedule(swpTimeout, slot, SWP_SEND_TIMEOUT);
       return send(LINK, frame);
   }

Trước khi tiếp tục đến phía nhận của SWP, chúng ta cần làm rõ một sự không nhất quán. Một mặt, chúng ta đã nói rằng một giao thức cấp cao gọi dịch vụ của một giao thức cấp thấp bằng cách gọi thao tác ``send``, vì vậy chúng ta mong đợi rằng một giao thức muốn gửi một tin nhắn qua SWP sẽ gọi ``send(SWP, packet)``. Mặt khác, thủ tục thực hiện thao tác gửi của SWP được gọi là ``sendSWP``, và đối số đầu tiên của nó là một biến trạng thái (``SwpState``). Vậy điều gì đang xảy ra? Câu trả lời là hệ điều hành cung cấp mã liên kết chuyển đổi lời gọi chung đến ``send`` thành một lời gọi cụ thể cho giao thức ``sendSWP``. Mã liên kết này ánh xạ đối số đầu tiên cho ``send`` (biến giao thức đặc biệt ``SWP``) thành cả con trỏ hàm đến ``sendSWP`` và con trỏ đến trạng thái giao thức mà SWP cần để thực hiện công việc của nó. Lý do chúng ta để giao thức cấp cao gọi gián tiếp hàm cụ thể của giao thức thông qua lời gọi hàm chung là vì chúng ta muốn giới hạn lượng thông tin mà giao thức cấp cao biết về giao thức cấp thấp. Điều này giúp dễ dàng thay đổi cấu hình đồ thị giao thức trong tương lai.

Bây giờ chúng ta chuyển sang triển khai cụ thể của SWP cho thao tác ``deliver``, được đưa ra trong thủ tục ``deliverSWP``. Thủ tục này thực sự xử lý hai loại tin nhắn đến khác nhau: ACK cho các khung đã gửi trước đó từ nút này và các khung dữ liệu đến tại nút này. Theo một nghĩa nào đó, phần xử lý ACK của thủ tục này là đối tác với phía gửi của thuật toán trong ``sendSWP``. Quyết định xem tin nhắn đến là ACK hay khung dữ liệu được thực hiện bằng cách kiểm tra trường ``Flags`` trong tiêu đề. Lưu ý rằng triển khai này không hỗ trợ ghép ACK vào các khung dữ liệu.

Khi khung đến là ACK, ``deliverSWP`` đơn giản tìm vị trí trong hàng đợi truyền (``sendQ``) tương ứng với ACK, hủy sự kiện hết thời gian, và giải phóng khung đã lưu ở vị trí đó. Công việc này thực sự được thực hiện trong một vòng lặp vì ACK có thể là xác nhận tích lũy. Điều duy nhất cần chú ý trong trường hợp này là lời gọi thủ tục con ``swpInWindow``. Thủ tục con này, được đưa ra bên dưới, đảm bảo rằng số thứ tự của khung được xác nhận nằm trong phạm vi các ACK mà người gửi hiện đang mong đợi nhận.

Khi khung đến chứa dữ liệu, ``deliverSWP`` đầu tiên gọi ``msgStripHdr`` và ``load_swp_hdr`` để trích xuất tiêu đề từ khung. Thủ tục ``load_swp_hdr`` là đối tác với ``store_swp_hdr`` đã đề cập trước đó; nó chuyển đổi một chuỗi byte thành cấu trúc dữ liệu C chứa tiêu đề SWP. Sau đó, ``deliverSWP`` gọi ``swpInWindow`` để đảm bảo số thứ tự của khung nằm trong phạm vi số thứ tự mà nó mong đợi. Nếu đúng, thủ tục sẽ lặp qua tập hợp các khung liên tiếp đã nhận và chuyển chúng lên giao thức cấp cao hơn bằng cách gọi thủ tục ``deliverHLP``. Nó cũng gửi một ACK tích lũy trở lại người gửi, nhưng làm điều này bằng cách lặp qua hàng đợi nhận (nó không sử dụng biến ``SeqNumToAck`` như mô tả trong phần lý thuyết ở trên).

.. code-block:: c

   static int
   deliverSWP(SwpState *state, Msg *frame)
   {
       SwpHdr   hdr;
       char     *hbuf;

       hbuf = msgStripHdr(frame, HLEN);
       load_swp_hdr(&hdr, hbuf)
       if (hdr.Flags & FLAG_ACK_VALID)
       {
           /* nhận một xác nhận—thực hiện phía GỬI */
           if (swpInWindow(hdr.AckNum, state->LAR + 1, state->LFS))
           {
               do
               {
                   struct sendQ_slot *slot;

                   slot = &state->sendQ[++state->LAR % SWS];
                   evCancel(slot->timeout);
                   msgDestroy(&slot->msg);
                   semSignal(&state->sendWindowNotFull);
               } while (state->LAR != hdr.AckNum);
           }
       }

       if (hdr.Flags & FLAG_HAS_DATA)
       {
           struct recvQ_slot *slot;

           /* nhận gói dữ liệu—thực hiện phía NHẬN */
           slot = &state->recvQ[hdr.SeqNum % RWS];
           if (!swpInWindow(hdr.SeqNum, state->NFE, state->NFE + RWS - 1))
           {
               /* loại bỏ tin nhắn */
               return SUCCESS;
           }
           msgSaveCopy(&slot->msg, frame);
           slot->received = TRUE;
           if (hdr.SeqNum == state->NFE)
           {
               Msg m;

               while (slot->received)
               {
                   deliver(HLP, &slot->msg);
                   msgDestroy(&slot->msg);
                   slot->received = FALSE;
                   slot = &state->recvQ[++state->NFE % RWS];
               }
               /* gửi ACK: */
               prepare_ack(&m, state->NFE - 1);
               send(LINK, &m);
               msgDestroy(&m);
           }
       }
       return SUCCESS;
   }

Cuối cùng, ``swpInWindow`` là một thủ tục con đơn giản kiểm tra xem một số thứ tự nhất định có nằm giữa một số thứ tự nhỏ nhất và lớn nhất hay không.

.. code-block:: c

   static bool
   swpInWindow(SwpSeqno seqno, SwpSeqno min, SwpSeqno max)
   {
       SwpSeqno pos, maxpos;

       pos    = seqno - min;       /* pos *nên* nằm trong khoảng [0..MAX) */
       maxpos = max - min + 1;     /* maxpos nằm trong khoảng [0..MAX] */
       return pos < maxpos;
   }

Thứ Tự Khung và Điều Khiển Luồng
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Giao thức cửa sổ trượt có lẽ là thuật toán nổi tiếng nhất trong mạng máy tính. Tuy nhiên, điều dễ gây nhầm lẫn về thuật toán này là nó có thể phục vụ ba vai trò khác nhau. Vai trò đầu tiên là vai trò mà chúng ta đã tập trung trong phần này—truyền dữ liệu đáng tin cậy qua một liên kết không đáng tin cậy. (Nói chung, thuật toán có thể được sử dụng để truyền tin nhắn đáng tin cậy qua một mạng không đáng tin cậy.) Đây là chức năng cốt lõi của thuật toán.

Vai trò thứ hai mà thuật toán cửa sổ trượt có thể phục vụ là duy trì thứ tự mà các khung được truyền. Điều này dễ thực hiện ở phía nhận—vì mỗi khung có một số thứ tự, người nhận chỉ cần đảm bảo rằng nó không chuyển một khung lên giao thức cấp cao hơn cho đến khi nó đã chuyển tất cả các khung có số thứ tự nhỏ hơn. Tức là, người nhận lưu trữ (không chuyển tiếp) các khung đến không theo thứ tự. Phiên bản thuật toán cửa sổ trượt được mô tả trong phần này có duy trì thứ tự khung, mặc dù chúng ta có thể tưởng tượng một biến thể trong đó người nhận chuyển các khung lên giao thức tiếp theo mà không chờ tất cả các khung trước đó được chuyển giao. Một câu hỏi chúng ta nên tự hỏi là liệu chúng ta thực sự cần giao thức cửa sổ trượt để giữ thứ tự khung ở tầng liên kết, hay thay vào đó, chức năng này nên được thực hiện bởi một giao thức ở tầng cao hơn trong ngăn xếp.

Vai trò thứ ba mà thuật toán cửa sổ trượt đôi khi đảm nhận là hỗ trợ *điều khiển luồng*—một cơ chế phản hồi cho phép người nhận điều tiết người gửi. Cơ chế này được sử dụng để ngăn người gửi gửi quá nhiều dữ liệu khiến người nhận không xử lý kịp. Điều này thường được thực hiện bằng cách bổ sung giao thức cửa sổ trượt để người nhận không chỉ xác nhận các khung đã nhận mà còn thông báo cho người gửi biết số khung mà nó còn chỗ để nhận. Số khung mà người nhận có thể nhận tương ứng với lượng bộ nhớ đệm còn trống mà nó có. Cũng như việc duy trì thứ tự, chúng ta cần đảm bảo rằng điều khiển luồng là cần thiết ở tầng liên kết trước khi tích hợp nó vào giao thức cửa sổ trượt.

.. _key-separate-concerns:
.. admonition::  Bài Học Chính

   Một khái niệm quan trọng cần rút ra từ thảo luận này là nguyên lý thiết kế hệ thống mà chúng tôi gọi là *phân tách các mối quan tâm*. Tức là, bạn phải cẩn thận phân biệt giữa các chức năng khác nhau đôi khi được kết hợp trong một cơ chế, và bạn phải đảm bảo rằng mỗi chức năng là cần thiết và được hỗ trợ theo cách hiệu quả nhất. Trong trường hợp này, truyền dữ liệu đáng tin cậy, truyền dữ liệu theo thứ tự và điều khiển luồng đôi khi được kết hợp trong một giao thức cửa sổ trượt, và chúng ta nên tự hỏi liệu đây có phải là điều đúng đắn cần làm ở tầng liên kết hay không. :ref:`[Tiếp theo] <key-aggregation>`

2.5.3 Kênh Logic Đồng Thời
--------------------------

Giao thức tầng liên kết được sử dụng trong ARPANET gốc cung cấp một lựa chọn thú vị thay thế cho giao thức cửa sổ trượt, ở chỗ nó có thể giữ cho ống luôn đầy trong khi vẫn sử dụng thuật toán dừng-và-đợi đơn giản. Một hệ quả quan trọng của cách tiếp cận này là các khung được gửi qua một liên kết nhất định không được giữ theo thứ tự nào cả. Giao thức này cũng không ngụ ý gì về điều khiển luồng.

Ý tưởng cơ bản của giao thức ARPANET, mà chúng tôi gọi là *kênh logic đồng thời*, là ghép nhiều kênh logic lên một liên kết điểm-điểm duy nhất và chạy thuật toán dừng-và-đợi trên mỗi kênh logic này. Không có mối quan hệ nào được duy trì giữa các khung được gửi trên bất kỳ kênh logic nào, nhưng vì mỗi kênh logic có thể có một khung đang chờ nên người gửi có thể giữ cho liên kết luôn đầy.

Cụ thể hơn, người gửi giữ 3 bit trạng thái cho mỗi kênh: một biến boolean, cho biết kênh hiện đang bận hay không; số thứ tự 1 bit để sử dụng lần tiếp theo khi gửi khung trên kênh logic này; và số thứ tự tiếp theo mong đợi trên một khung đến trên kênh này. Khi nút có một khung cần gửi, nó sử dụng kênh nhàn rỗi có số nhỏ nhất, và ngoài ra nó hoạt động giống như dừng-và-đợi.

Trên thực tế, ARPANET hỗ trợ 8 kênh logic trên mỗi liên kết mặt đất và 16 trên mỗi liên kết vệ tinh. Trong trường hợp liên kết mặt đất, tiêu đề cho mỗi khung bao gồm một số kênh 3 bit và một số thứ tự 1 bit, tổng cộng là 4 bit. Đây chính xác là số bit mà giao thức cửa sổ trượt yêu cầu để hỗ trợ tối đa 8 khung đang chờ trên liên kết khi ``RWS = SWS``.