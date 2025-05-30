6.2 Các Kỷ Luật Xếp Hàng
========================

Bất kể cơ chế phân bổ tài nguyên còn lại đơn giản hay phức tạp đến đâu, mỗi bộ định tuyến đều phải triển khai một kỷ luật xếp hàng nào đó để kiểm soát cách các gói tin được lưu vào bộ đệm trong khi chờ truyền đi. Thuật toán xếp hàng có thể được xem như là phân bổ cả băng thông (gói nào được truyền) và không gian bộ đệm (gói nào bị loại bỏ). Nó cũng ảnh hưởng trực tiếp đến độ trễ mà một gói tin phải chịu bằng cách xác định thời gian chờ truyền của gói đó. Phần này giới thiệu hai thuật toán xếp hàng phổ biến—vào trước ra trước (FIFO) và xếp hàng công bằng (FQ)—và chỉ ra một số biến thể đã được đề xuất.

6.2.1 FIFO
----------

Ý tưởng của xếp hàng FIFO, còn gọi là vào trước ra trước (FCFS), rất đơn giản: Gói tin đến bộ định tuyến trước sẽ được truyền đi trước. Điều này được minh họa trong :numref:`Hình %s(a) <fig-fifo>`, cho thấy một FIFO với các “khe” chứa tối đa tám gói tin. Vì không gian bộ đệm tại mỗi bộ định tuyến là hữu hạn, nếu một gói tin đến mà hàng đợi (không gian bộ đệm) đã đầy, bộ định tuyến sẽ loại bỏ gói tin đó, như minh họa trong :numref:`Hình %s(b) <fig-fifo>`. Việc này được thực hiện mà không quan tâm đến luồng mà gói tin thuộc về hay tầm quan trọng của gói tin. Điều này đôi khi được gọi là *loại bỏ ở đuôi* (tail drop), vì các gói tin đến ở cuối FIFO sẽ bị loại bỏ.

.. _fig-fifo:
.. figure:: figures/f06-05-9780123850591.png
   :width: 400px
   :align: center

   Xếp hàng FIFO (a), và loại bỏ ở đuôi tại hàng đợi FIFO (b).

Lưu ý rằng loại bỏ ở đuôi và FIFO là hai ý tưởng tách biệt. FIFO là một *kỷ luật lập lịch*—nó xác định thứ tự các gói tin được truyền đi. Loại bỏ ở đuôi là một *chính sách loại bỏ*—nó xác định gói nào bị loại bỏ. Vì FIFO và loại bỏ ở đuôi là các ví dụ đơn giản nhất của kỷ luật lập lịch và chính sách loại bỏ, nên đôi khi chúng được xem như một gói—cách triển khai xếp hàng mặc định. Đáng tiếc, gói này thường chỉ được gọi đơn giản là *xếp hàng FIFO*, trong khi chính xác hơn nên gọi là *FIFO với loại bỏ ở đuôi*. Một phần sau sẽ cung cấp ví dụ về một chính sách loại bỏ khác, sử dụng thuật toán phức tạp hơn “Có bộ đệm trống không?” để quyết định khi nào loại bỏ gói tin. Chính sách loại bỏ như vậy có thể được dùng với FIFO, hoặc với các kỷ luật lập lịch phức tạp hơn.

FIFO với loại bỏ ở đuôi, là thuật toán xếp hàng đơn giản nhất, hiện là loại được sử dụng rộng rãi nhất trong các bộ định tuyến Internet tại thời điểm viết sách này. Cách tiếp cận đơn giản này đẩy toàn bộ trách nhiệm kiểm soát tắc nghẽn và phân bổ tài nguyên ra rìa mạng. Do đó, hình thức kiểm soát tắc nghẽn phổ biến nhất trên Internet hiện nay giả định không có sự hỗ trợ từ các bộ định tuyến: TCP chịu trách nhiệm phát hiện và phản ứng với tắc nghẽn. Chúng ta sẽ xem cách thức này hoạt động ở phần tiếp theo.

Một biến thể đơn giản của xếp hàng FIFO cơ bản là xếp hàng ưu tiên. Ý tưởng là đánh dấu mỗi gói tin với một mức ưu tiên; dấu này có thể được mang, ví dụ, trong tiêu đề IP, như sẽ được bàn ở phần sau. Các bộ định tuyến sau đó triển khai nhiều hàng đợi FIFO, mỗi hàng cho một lớp ưu tiên. Bộ định tuyến luôn truyền các gói từ hàng đợi ưu tiên cao nhất nếu hàng này không rỗng trước khi chuyển sang hàng ưu tiên tiếp theo. Trong mỗi mức ưu tiên, các gói vẫn được quản lý theo kiểu FIFO. Ý tưởng này là một sự khác biệt nhỏ so với mô hình chuyển giao nỗ lực tối đa (best-effort), nhưng nó không đi xa đến mức đảm bảo cho bất kỳ lớp ưu tiên nào. Nó chỉ cho phép các gói ưu tiên cao được “chen lên đầu hàng”.

Vấn đề với xếp hàng ưu tiên, tất nhiên, là hàng ưu tiên cao có thể làm “đói” các hàng còn lại; tức là, miễn là còn ít nhất một gói ưu tiên cao trong hàng, các hàng ưu tiên thấp hơn sẽ không được phục vụ. Để khả thi, cần có giới hạn cứng về lượng lưu lượng ưu tiên cao được đưa vào hàng. Rõ ràng là không thể cho phép người dùng tự đặt gói của mình thành ưu tiên cao một cách không kiểm soát; chúng ta phải hoặc ngăn họ làm điều này hoàn toàn, hoặc cung cấp một dạng “phản hồi” nào đó cho người dùng. Một cách rõ ràng là dùng kinh tế học—mạng có thể thu phí cao hơn để chuyển các gói ưu tiên cao so với gói ưu tiên thấp. Tuy nhiên, có nhiều thách thức lớn khi triển khai một cơ chế như vậy trong môi trường phi tập trung như Internet.

Một tình huống mà xếp hàng ưu tiên được sử dụng trên Internet là để bảo vệ các gói tin quan trọng nhất—thường là các bản cập nhật định tuyến cần thiết để ổn định bảng định tuyến sau khi có thay đổi cấu trúc liên kết. Thường có một hàng đặc biệt cho các gói này, có thể được nhận diện bằng Differentiated Services Code Point (trước đây là trường TOS) trong tiêu đề IP. Đây thực chất là một trường hợp đơn giản của ý tưởng “Dịch vụ Phân biệt”.

6.2.2 Xếp Hàng Công Bằng
------------------------

Vấn đề chính của xếp hàng FIFO là nó không phân biệt giữa các nguồn lưu lượng khác nhau, hay, theo ngôn ngữ đã giới thiệu ở phần trước, nó không tách các gói theo luồng mà chúng thuộc về. Điều này gây ra vấn đề ở hai cấp độ. Ở một cấp độ, không rõ liệu bất kỳ thuật toán kiểm soát tắc nghẽn nào được triển khai hoàn toàn ở nguồn có thể kiểm soát tắc nghẽn đủ tốt với rất ít sự hỗ trợ từ các bộ định tuyến hay không. Chúng ta sẽ tạm gác đánh giá này cho đến phần tiếp theo khi bàn về kiểm soát tắc nghẽn TCP. Ở một cấp độ khác, vì toàn bộ cơ chế kiểm soát tắc nghẽn được triển khai ở nguồn và xếp hàng FIFO không cung cấp phương tiện để kiểm soát mức độ tuân thủ của các nguồn, nên một nguồn (luồng) hoạt động không đúng có thể chiếm một phần tùy ý lớn của băng thông mạng. Xét lại Internet, rõ ràng là một ứng dụng nào đó có thể không dùng TCP và do đó bỏ qua cơ chế kiểm soát tắc nghẽn đầu-cuối của nó. (Các ứng dụng như điện thoại Internet hiện nay làm điều này.) Ứng dụng như vậy có thể “ngập lụt” các bộ định tuyến Internet bằng các gói của nó, khiến các gói của ứng dụng khác bị loại bỏ.

Xếp hàng công bằng (FQ) là một thuật toán được thiết kế để giải quyết vấn đề này. Ý tưởng của FQ là duy trì một hàng đợi riêng cho mỗi luồng hiện đang được bộ định tuyến xử lý. Bộ định tuyến sau đó phục vụ các hàng này theo kiểu vòng tròn, như minh họa trong :numref:`Hình %s <fig-fq>`. Khi một luồng gửi gói quá nhanh, hàng đợi của nó sẽ đầy. Khi hàng đợi đạt đến một độ dài nhất định, các gói bổ sung thuộc hàng đó sẽ bị loại bỏ. Bằng cách này, một nguồn không thể tùy ý tăng phần băng thông mạng mà nó chiếm dụng làm ảnh hưởng đến các luồng khác.

.. _fig-fq:
.. figure:: figures/f06-06-9780123850591.png
   :width: 350px
   :align: center

   Phục vụ vòng tròn bốn luồng tại một bộ định tuyến.

Lưu ý rằng FQ không liên quan đến việc bộ định tuyến thông báo cho các nguồn lưu lượng về trạng thái của bộ định tuyến hoặc giới hạn tốc độ gửi của nguồn. Nói cách khác, FQ vẫn được thiết kế để sử dụng kết hợp với một cơ chế kiểm soát tắc nghẽn đầu-cuối. Nó chỉ đơn giản là tách biệt lưu lượng để các nguồn hoạt động không đúng không ảnh hưởng đến các nguồn tuân thủ thuật toán đầu-cuối. FQ cũng đảm bảo công bằng giữa các luồng được quản lý bởi một thuật toán kiểm soát tắc nghẽn tốt.

Dù ý tưởng cơ bản đơn giản, vẫn có một số chi tiết cần xử lý đúng. Phức tạp chính là các gói được xử lý tại bộ định tuyến không nhất thiết có cùng độ dài. Để thực sự phân bổ băng thông của liên kết ra một cách công bằng, cần phải tính đến độ dài gói. Ví dụ, nếu một bộ định tuyến quản lý hai luồng, một luồng có gói 1000 byte và luồng kia có gói 500 byte (có thể do phân mảnh ở bộ định tuyến trước), thì phục vụ vòng tròn đơn giản các gói từ mỗi hàng sẽ khiến luồng đầu nhận hai phần ba băng thông, còn luồng thứ hai chỉ nhận một phần ba.

Điều chúng ta thực sự muốn là vòng tròn từng bit, nơi bộ định tuyến truyền một bit từ luồng 1, rồi một bit từ luồng 2, v.v. Rõ ràng, không khả thi để xen kẽ từng bit từ các gói khác nhau. Do đó, cơ chế FQ mô phỏng hành vi này bằng cách xác định thời điểm một gói sẽ hoàn thành truyền nếu được gửi theo kiểu vòng tròn từng bit, rồi dùng thời điểm hoàn thành này để sắp xếp các gói truyền đi.

Để hiểu thuật toán xấp xỉ vòng tròn từng bit, hãy xét hành vi của một luồng và tưởng tượng một đồng hồ nhảy một nhịp mỗi khi một bit được truyền từ tất cả các luồng đang hoạt động. (Một luồng được coi là hoạt động khi có dữ liệu trong hàng.) Với luồng này, ký hiệu :math:`P_i` là độ dài của gói *i*, :math:`S_i` là thời điểm bộ định tuyến bắt đầu truyền gói *i*, và :math:`F_i` là thời điểm bộ định tuyến hoàn thành truyền gói *i*. Nếu :math:`P_i` được tính bằng số nhịp đồng hồ cần để truyền gói *i* (lưu ý rằng thời gian tăng 1 nhịp mỗi khi luồng này nhận được 1 bit dịch vụ), thì dễ thấy rằng :math:`F_i = S_i + P_i`.

Khi nào chúng ta bắt đầu truyền gói *i*? Câu trả lời phụ thuộc vào việc gói *i* đến trước hay sau khi bộ định tuyến hoàn thành truyền gói *i-1* của luồng này. Nếu đến trước, thì về mặt logic, bit đầu tiên của gói *i* được truyền ngay sau bit cuối của gói *i-1*. Ngược lại, có thể bộ định tuyến đã hoàn thành truyền gói *i-1* từ lâu trước khi *i* đến, nghĩa là có một khoảng thời gian hàng đợi của luồng này rỗng, nên cơ chế vòng tròn không thể truyền gói nào từ luồng này. Nếu ký hiệu :math:`A_i` là thời điểm gói *i* đến bộ định tuyến, thì :math:`S_i = \max(F_{i-1}, A_i)`. Do đó, ta có thể tính:

.. math::

   F_i = \max(F_{i-1}, A_i) + P_i

Bây giờ chuyển sang trường hợp có nhiều luồng, ta thấy có một điểm cần lưu ý khi xác định :math:`A_i`. Ta không thể chỉ đơn giản đọc đồng hồ hệ thống khi gói đến. Như đã nói, ta muốn thời gian tăng một nhịp mỗi khi tất cả các luồng đang hoạt động nhận được một bit dịch vụ, nên cần một đồng hồ chạy chậm hơn khi có nhiều luồng. Cụ thể, đồng hồ phải tăng một nhịp khi *n* bit được truyền nếu có *n* luồng hoạt động. Đồng hồ này sẽ được dùng để tính :math:`A_i`.

Với mỗi luồng, ta tính :math:`F_i` cho mỗi gói đến bằng công thức trên. Sau đó, coi tất cả các :math:`F_i` là dấu thời gian, và gói tiếp theo được truyền luôn là gói có dấu thời gian nhỏ nhất—gói mà, theo lý luận trên, sẽ hoàn thành truyền trước các gói khác.

Điều này có nghĩa là một gói có thể đến trên một luồng và, vì nó ngắn hơn một gói từ luồng khác đã có trong hàng chờ truyền, nó có thể được chèn vào trước gói dài hơn đó. Tuy nhiên, điều này không có nghĩa là một gói mới đến có thể ngắt quãng một gói đang được truyền. Chính sự không có ngắt quãng này khiến việc triển khai FQ như mô tả không mô phỏng chính xác vòng tròn từng bit mà chúng ta muốn xấp xỉ.

.. _fig-fair-queuing:
.. figure:: figures/f06-07-9780123850591.png
   :width: 600px
   :align: center

   Ví dụ về xếp hàng công bằng: (a) Các gói có thời điểm hoàn thành sớm hơn được gửi trước; (b) việc gửi một gói đang truyền được hoàn tất.

Để thấy rõ hơn cách hoạt động của xếp hàng công bằng này, hãy xét ví dụ trong :numref:`Hình %s <fig-fair-queuing>`. Phần (a) cho thấy các hàng đợi của hai luồng; thuật toán chọn cả hai gói từ luồng 1 để truyền trước gói trong hàng của luồng 2, vì chúng có thời điểm hoàn thành sớm hơn. Ở (b), bộ định tuyến đã bắt đầu gửi một gói từ luồng 2 khi gói từ luồng 1 đến. Dù gói đến trên luồng 1 sẽ hoàn thành trước luồng 2 nếu dùng xếp hàng công bằng hoàn hảo từng bit, nhưng việc triển khai không ngắt quãng gói của luồng 2.

Có hai điều cần lưu ý về xếp hàng công bằng. Thứ nhất, liên kết không bao giờ bị bỏ trống miễn là còn ít nhất một gói trong hàng. Bất kỳ cơ chế xếp hàng nào có đặc điểm này được gọi là *bảo toàn công việc* (work conserving). Một hệ quả của việc bảo toàn công việc là nếu tôi chia sẻ một liên kết với nhiều luồng không gửi dữ liệu thì tôi có thể sử dụng toàn bộ băng thông cho luồng của mình. Tuy nhiên, khi các luồng khác bắt đầu gửi, chúng sẽ sử dụng phần của mình và băng thông dành cho luồng của tôi sẽ giảm.

Điều thứ hai là nếu liên kết được sử dụng tối đa và có *n* luồng gửi dữ liệu, tôi không thể sử dụng quá 1/n\ :sup:`th` băng thông liên kết. Nếu tôi cố gửi nhiều hơn, các gói của tôi sẽ được gán dấu thời gian ngày càng lớn, khiến chúng phải chờ lâu hơn trong hàng. Cuối cùng, hàng sẽ bị tràn—dù gói của tôi hay của người khác bị loại bỏ là do chính sách loại bỏ quyết định; FQ là một thuật toán lập lịch, giống như FIFO, có thể kết hợp với nhiều chính sách loại bỏ khác nhau.

Vì FQ là bảo toàn công việc, bất kỳ băng thông nào không được một luồng sử dụng sẽ tự động được các luồng khác sử dụng. Ví dụ, nếu có bốn luồng đi qua một bộ định tuyến, và tất cả đều gửi gói, thì mỗi luồng sẽ nhận một phần tư băng thông. Nhưng nếu một luồng không gửi đủ lâu để tất cả các gói của nó rời khỏi hàng, thì băng thông còn lại sẽ được chia cho ba luồng còn lại, mỗi luồng nhận một phần ba băng thông. Do đó, có thể xem FQ như cung cấp một phần băng thông tối thiểu đảm bảo cho mỗi luồng, với khả năng nhận nhiều hơn nếu các luồng khác không dùng hết phần của mình.

Có thể triển khai một biến thể của FQ, gọi là *xếp hàng công bằng có trọng số* (WFQ), cho phép gán trọng số cho mỗi luồng (hàng đợi). Trọng số này về mặt logic xác định số bit được truyền mỗi lần bộ định tuyến phục vụ hàng đó, qua đó kiểm soát tỷ lệ băng thông mà luồng nhận được. FQ đơn giản gán trọng số 1 cho mỗi hàng, nghĩa là về mặt logic chỉ 1 bit được truyền từ mỗi hàng mỗi vòng. Điều này dẫn đến mỗi luồng nhận :math:`1/n^{th}` băng thông khi có *n* luồng. Với WFQ, một hàng có thể có trọng số 2, hàng thứ hai trọng số 1, hàng thứ ba trọng số 3. Giả sử mỗi hàng luôn có gói chờ truyền, luồng đầu sẽ nhận một phần ba băng thông, luồng thứ hai một phần sáu, và luồng thứ ba một nửa băng thông.

Dù chúng ta mô tả WFQ theo luồng, lưu ý rằng nó có thể được triển khai trên *lớp* lưu lượng, nơi lớp được xác định theo cách khác ngoài các luồng đơn giản đã giới thiệu ở đầu chương. Ví dụ, có thể dùng một số bit trong tiêu đề IP để nhận diện lớp và gán hàng đợi cùng trọng số cho mỗi lớp. Đây chính là điều được đề xuất trong kiến trúc Dịch vụ Phân biệt sẽ trình bày ở phần sau.

Lưu ý rằng một bộ định tuyến thực hiện WFQ phải biết lấy trọng số cho mỗi hàng từ đâu, hoặc cấu hình thủ công hoặc nhận tín hiệu từ nguồn. Trong trường hợp sau, chúng ta đang tiến gần đến mô hình dựa trên đặt chỗ. Chỉ gán trọng số cho hàng cung cấp một dạng đặt chỗ khá yếu vì các trọng số này chỉ liên quan gián tiếp đến băng thông mà luồng nhận được. (Băng thông dành cho một luồng còn phụ thuộc vào số luồng khác chia sẻ liên kết.) Chúng ta sẽ thấy ở phần sau cách WFQ có thể được dùng như một thành phần của cơ chế phân bổ tài nguyên dựa trên đặt chỗ.

.. _key-policy-mechanism:
.. admonition:: Bài Học Chính

   Cuối cùng, chúng ta nhận thấy toàn bộ thảo luận về quản lý hàng đợi này minh họa một nguyên lý thiết kế hệ thống quan trọng gọi là *tách biệt chính sách và cơ chế*. Ý tưởng là xem mỗi cơ chế như một hộp đen cung cấp dịch vụ đa chiều có thể điều khiển bằng một tập các nút điều chỉnh. Một chính sách xác định một thiết lập cụ thể của các nút này nhưng không biết (hoặc không quan tâm) cách chính sách được triển khai. Trong trường hợp này, cơ chế là kỷ luật xếp hàng, còn chính sách là thiết lập cụ thể về luồng nào nhận mức dịch vụ nào (ví dụ, ưu tiên hay trọng số). Chúng ta sẽ bàn về một số chính sách có thể dùng với cơ chế WFQ ở phần sau. :ref:`[Tiếp theo] <key-red>`
