5.4 Vận chuyển cho Thời gian Thực (RTP)
=======================================

Vào những ngày đầu của chuyển mạch gói, hầu hết các ứng dụng đều quan tâm đến việc truyền tệp, mặc dù ngay từ năm 1981, đã có các thử nghiệm nhằm truyền tải lưu lượng thời gian thực, như các mẫu giọng nói số hóa. Chúng ta gọi một ứng dụng là “thời gian thực” khi nó có yêu cầu nghiêm ngặt về việc chuyển giao thông tin đúng thời điểm. Thoại qua IP (VoIP) là một ví dụ kinh điển về ứng dụng thời gian thực vì bạn không thể dễ dàng trò chuyện với ai đó nếu phải mất hơn một phần nhỏ của giây để nhận được phản hồi. Như chúng ta sẽ thấy ngay sau đây, các ứng dụng thời gian thực đặt ra một số yêu cầu cụ thể đối với giao thức vận chuyển mà các giao thức đã thảo luận trong chương này chưa đáp ứng tốt.

.. _fig-zoom:
.. figure:: figures/rpc/Slide4.png
   :width: 600px
   :align: center

   Giao diện người dùng của một công cụ hội nghị truyền hình.

Các ứng dụng đa phương tiện—những ứng dụng liên quan đến video, âm thanh và dữ liệu—đôi khi được chia thành hai loại: ứng dụng *tương tác* và ứng dụng *streaming*. :numref:`Hình %s <fig-zoom>` cho thấy các tác giả đang sử dụng một công cụ hội nghị điển hình cho loại tương tác. Cùng với VoIP, đây là những loại ứng dụng có yêu cầu thời gian thực nghiêm ngặt nhất.

Các ứng dụng streaming thường truyền các luồng âm thanh hoặc video từ máy chủ đến máy khách và được đại diện bởi các sản phẩm thương mại như Spotify. Video streaming, tiêu biểu như YouTube và Netflix, đã trở thành một trong những dạng lưu lượng chủ đạo trên Internet. Vì các ứng dụng streaming không có sự tương tác giữa người với người, nên chúng đặt ra yêu cầu thời gian thực ít nghiêm ngặt hơn đối với các giao thức nền. Tuy nhiên, tính kịp thời vẫn rất quan trọng—ví dụ, bạn muốn video bắt đầu phát ngay sau khi nhấn “play”, và khi đã phát, các gói đến muộn sẽ khiến video bị dừng hoặc gây ra suy giảm hình ảnh. Vì vậy, dù các ứng dụng streaming không hoàn toàn là thời gian thực, chúng vẫn có đủ điểm chung với các ứng dụng đa phương tiện tương tác để xứng đáng có một giao thức chung cho cả hai loại ứng dụng.

Đến đây, có thể thấy rằng các nhà thiết kế giao thức vận chuyển cho ứng dụng thời gian thực và đa phương tiện phải đối mặt với thách thức thực sự trong việc xác định các yêu cầu đủ rộng để đáp ứng nhu cầu của nhiều loại ứng dụng rất khác nhau. Họ cũng phải chú ý đến sự tương tác giữa các ứng dụng khác nhau, như việc đồng bộ hóa các luồng âm thanh và video. Chúng ta sẽ thấy dưới đây các mối quan tâm này đã ảnh hưởng đến thiết kế của giao thức vận chuyển thời gian thực chủ yếu hiện nay: *Giao thức Vận chuyển Thời gian Thực* (RTP).

Phần lớn RTP thực chất bắt nguồn từ các chức năng giao thức vốn được nhúng trong chính ứng dụng. Hai trong số những ứng dụng đầu tiên như vậy là ``vic`` và ``vat``, ứng dụng đầu hỗ trợ video thời gian thực và ứng dụng sau hỗ trợ âm thanh thời gian thực. Cả hai ứng dụng ban đầu đều chạy trực tiếp trên UDP, trong khi các nhà thiết kế xác định những tính năng cần thiết để xử lý bản chất thời gian thực của giao tiếp. Sau này, họ nhận ra rằng các tính năng này có thể hữu ích cho nhiều ứng dụng khác và đã định nghĩa một giao thức với các tính năng đó. Giao thức này cuối cùng được chuẩn hóa thành RTP.

RTP có thể chạy trên nhiều giao thức tầng dưới khác nhau, nhưng vẫn thường chạy trên UDP. Điều này dẫn đến ngăn xếp giao thức như minh họa ở :numref:`Hình %s <fig-vat-stack>`. Lưu ý rằng chúng ta đang chạy một giao thức vận chuyển trên một giao thức vận chuyển. Không có quy tắc nào cấm điều đó, và thực tế điều này rất hợp lý, vì UDP cung cấp mức chức năng tối thiểu, và việc phân kênh cơ bản dựa trên số cổng lại chính là thứ RTP cần làm điểm khởi đầu. Vì vậy, thay vì tạo lại số cổng trong RTP, RTP giao chức năng phân kênh cho UDP.

.. _fig-vat-stack:
.. figure:: figures/f05-22-9780123850591.png
   :width: 300px
   :align: center

   Ngăn xếp giao thức cho ứng dụng đa phương tiện sử dụng RTP.

5.4.1 Các yêu cầu
-----------------

Yêu cầu cơ bản nhất đối với một giao thức đa phương tiện đa dụng là nó cho phép các ứng dụng tương tự nhau có thể tương tác với nhau. Ví dụ, hai ứng dụng hội nghị âm thanh được phát triển độc lập phải có thể giao tiếp với nhau. Điều này ngay lập tức gợi ý rằng các ứng dụng nên sử dụng cùng một phương pháp mã hóa và nén giọng nói; nếu không, dữ liệu gửi từ một bên sẽ không thể hiểu được với bên nhận. Vì có khá nhiều phương pháp mã hóa giọng nói khác nhau, mỗi phương pháp có sự đánh đổi riêng giữa chất lượng, yêu cầu băng thông và chi phí tính toán, nên có lẽ sẽ không hợp lý nếu chỉ cho phép sử dụng một phương pháp duy nhất. Thay vào đó, giao thức của chúng ta nên cung cấp cách để bên gửi thông báo cho bên nhận biết phương pháp mã hóa nào nó muốn sử dụng, và có thể thương lượng cho đến khi tìm được phương pháp mà cả hai bên đều hỗ trợ.

Tương tự như âm thanh, có rất nhiều phương pháp mã hóa video khác nhau. Do đó, chức năng chung đầu tiên mà RTP có thể cung cấp là khả năng truyền đạt lựa chọn phương pháp mã hóa. Lưu ý rằng điều này cũng giúp xác định loại ứng dụng (ví dụ, âm thanh hay video); một khi biết thuật toán mã hóa nào đang được sử dụng, ta cũng biết loại dữ liệu nào đang được mã hóa.

Một yêu cầu quan trọng khác là cho phép bên nhận luồng dữ liệu xác định mối quan hệ thời gian giữa các dữ liệu nhận được. Các ứng dụng thời gian thực cần đặt dữ liệu nhận được vào *bộ đệm phát lại* để làm mượt độ trễ dao động (jitter) có thể phát sinh trong quá trình truyền qua mạng. Do đó, cần có một cơ chế đánh dấu thời gian cho dữ liệu để bên nhận có thể phát lại đúng thời điểm.

Liên quan đến việc xác định thời gian của một luồng đa phương tiện là vấn đề đồng bộ hóa nhiều phương tiện trong một hội nghị. Ví dụ rõ ràng nhất là đồng bộ hóa luồng âm thanh và video xuất phát từ cùng một bên gửi. Như chúng ta sẽ thấy dưới đây, đây là một vấn đề phức tạp hơn so với việc xác định thời gian phát lại cho một luồng đơn.

Một chức năng quan trọng khác cần cung cấp là chỉ báo mất gói. Lưu ý rằng một ứng dụng có giới hạn độ trễ chặt chẽ thường không thể sử dụng giao thức vận chuyển tin cậy như TCP vì việc truyền lại dữ liệu để sửa lỗi mất gói có thể khiến gói đến quá muộn để còn hữu ích. Do đó, ứng dụng phải có khả năng xử lý các gói bị mất, và bước đầu tiên là phát hiện ra chúng thực sự bị mất. Ví dụ, một ứng dụng video sử dụng mã hóa MPEG có thể thực hiện các hành động khác nhau khi mất gói, tùy thuộc vào việc gói đó thuộc khung I, khung B hay khung P.

Mất gói cũng là chỉ báo tiềm năng của tắc nghẽn. Vì các ứng dụng đa phương tiện thường không chạy trên TCP, chúng cũng không được hưởng các tính năng tránh tắc nghẽn của TCP. Tuy nhiên, nhiều ứng dụng đa phương tiện có khả năng phản ứng với tắc nghẽn—ví dụ, bằng cách thay đổi các tham số của thuật toán mã hóa để giảm băng thông sử dụng. Rõ ràng, để làm được điều này, bên nhận cần thông báo cho bên gửi biết đang xảy ra mất gói để bên gửi điều chỉnh tham số mã hóa.

Một chức năng chung khác giữa các ứng dụng đa phương tiện là khái niệm chỉ báo ranh giới khung (frame boundary). Một khung trong ngữ cảnh này là đặc trưng của ứng dụng. Ví dụ, sẽ hữu ích nếu thông báo cho ứng dụng video biết một tập hợp gói nào đó tương ứng với một khung hình. Trong ứng dụng âm thanh, việc đánh dấu bắt đầu của một “talkspurt” (một chuỗi âm thanh hoặc từ ngữ liên tiếp, sau đó là im lặng) cũng rất hữu ích. Bên nhận có thể nhận diện các khoảng im lặng giữa các talkspurt và tận dụng chúng để điều chỉnh điểm phát lại. Điều này dựa trên quan sát rằng việc rút ngắn hoặc kéo dài nhẹ các khoảng lặng giữa các từ không bị người dùng nhận ra, trong khi rút ngắn hoặc kéo dài bản thân từ ngữ thì lại rất dễ nhận thấy và gây khó chịu.

Một chức năng cuối cùng mà chúng ta có thể muốn đưa vào giao thức là một cách xác định bên gửi thân thiện với người dùng hơn là địa chỉ IP. Như minh họa ở :numref:`Hình %s <fig-zoom>`, các ứng dụng hội nghị âm thanh và video có thể hiển thị các chuỗi ký tự trên bảng điều khiển của chúng, do đó giao thức ứng dụng nên hỗ trợ việc liên kết một chuỗi ký tự như vậy với một luồng dữ liệu.

Bên cạnh các chức năng mà giao thức của chúng ta cần cung cấp, còn có một yêu cầu bổ sung: Nó nên sử dụng băng thông một cách hợp lý. Nói cách khác, chúng ta không muốn thêm quá nhiều bit phụ vào mỗi gói dưới dạng phần đầu dài. Lý do là các gói âm thanh—một trong những loại dữ liệu đa phương tiện phổ biến nhất—có xu hướng nhỏ, để giảm thời gian lấp đầy chúng bằng các mẫu. Gói âm thanh dài sẽ dẫn đến độ trễ cao do đóng gói, ảnh hưởng tiêu cực đến chất lượng hội thoại. (Đây là một trong những yếu tố khi chọn độ dài cell ATM.) Vì các gói dữ liệu bản thân đã ngắn, một phần đầu lớn sẽ khiến tỷ lệ băng thông đường truyền dành cho phần đầu tăng lên, giảm dung lượng dành cho dữ liệu “hữu ích”. Chúng ta sẽ thấy một số khía cạnh trong thiết kế RTP bị ảnh hưởng bởi yêu cầu giữ phần đầu ngắn.

Bạn có thể tranh luận liệu mọi tính năng vừa mô tả *thực sự* cần thiết cho một giao thức vận chuyển thời gian thực hay không, và có thể còn nhiều tính năng khác có thể bổ sung. Ý chính ở đây là làm cho cuộc sống của lập trình viên ứng dụng dễ dàng hơn bằng cách cung cấp cho họ một tập hợp trừu tượng và khối xây dựng hữu ích cho ứng dụng của mình. Ví dụ, bằng cách đưa cơ chế đánh dấu thời gian vào RTP, chúng ta giúp mọi lập trình viên ứng dụng thời gian thực không phải tự phát minh lại. Chúng ta cũng tăng khả năng hai ứng dụng thời gian thực khác nhau có thể tương tác với nhau.

5.4.2 Thiết kế RTP
------------------

Sau khi đã thấy danh sách khá dài các yêu cầu cho giao thức vận chuyển đa phương tiện, chúng ta chuyển sang chi tiết của giao thức được xác định để đáp ứng các yêu cầu đó. Giao thức này, RTP, được phát triển trong IETF và được sử dụng rộng rãi. Chuẩn RTP thực ra định nghĩa một cặp giao thức, RTP và Giao thức Điều khiển Vận chuyển Thời gian Thực (RTCP). RTP dùng để trao đổi dữ liệu đa phương tiện, còn RTCP dùng để định kỳ gửi thông tin điều khiển liên quan đến một luồng dữ liệu nhất định. Khi chạy trên UDP, luồng dữ liệu RTP và luồng điều khiển RTCP liên quan sử dụng các cổng tầng vận chuyển liên tiếp nhau. Dữ liệu RTP sử dụng số cổng chẵn và thông tin điều khiển RTCP sử dụng số cổng lẻ liền kề sau đó.

Vì RTP được thiết kế để hỗ trợ nhiều loại ứng dụng, nó cung cấp một cơ chế linh hoạt để các ứng dụng mới có thể được phát triển mà không phải sửa đổi lại giao thức RTP. Với mỗi lớp ứng dụng (ví dụ, âm thanh), RTP định nghĩa một *profile* và một hoặc nhiều *format*. Profile cung cấp một loạt thông tin đảm bảo sự hiểu biết chung về các trường trong phần đầu RTP cho lớp ứng dụng đó, như sẽ thấy rõ khi chúng ta xem xét phần đầu chi tiết. Đặc tả format giải thích cách dữ liệu theo sau phần đầu RTP sẽ được diễn giải. Ví dụ, phần đầu RTP có thể được theo sau bởi một chuỗi byte, mỗi byte đại diện cho một mẫu âm thanh lấy tại một khoảng thời gian xác định sau mẫu trước đó. Ngoài ra, format dữ liệu có thể phức tạp hơn nhiều; một luồng video mã hóa MPEG, chẳng hạn, sẽ cần có cấu trúc phức tạp để biểu diễn các loại thông tin khác nhau.

.. _key-alf:
.. admonition::  Bài học then chốt

   Thiết kế của RTP thể hiện một nguyên lý kiến trúc gọi là *Application Level Framing* (ALF) – Đóng khung ở tầng ứng dụng. Nguyên lý này được Clark và Tennenhouse đề xuất năm 1990 như một cách tiếp cận mới để thiết kế giao thức cho các ứng dụng đa phương tiện mới nổi. Họ nhận ra rằng các ứng dụng này khó có thể được phục vụ tốt bởi các giao thức hiện có như TCP, và hơn nữa, có thể không phù hợp với bất kỳ giao thức “một cho tất cả” nào. Cốt lõi của nguyên lý này là niềm tin rằng ứng dụng hiểu rõ nhất nhu cầu của chính nó. Ví dụ, một ứng dụng video MPEG biết cách tốt nhất để phục hồi khi mất khung hình và phản ứng khác nhau nếu mất khung I hay khung B. Ứng dụng cũng hiểu rõ nhất cách phân mảnh dữ liệu để truyền—ví dụ, tốt hơn nên gửi dữ liệu từ các khung khác nhau trong các datagram khác nhau, để một gói bị mất chỉ làm hỏng một khung, không phải hai. Đó là lý do tại sao RTP để lại nhiều chi tiết giao thức cho các tài liệu profile và format đặc thù ứng dụng. :ref:`[Tiếp theo] <key-congestion>`

Định dạng phần đầu
~~~~~~~~~~~~~~~~~~

:numref:`Hình %s <fig-rtp-hdr>` cho thấy định dạng phần đầu được sử dụng bởi RTP. 12 byte đầu tiên luôn có mặt, trong khi các định danh nguồn đóng góp chỉ được sử dụng trong một số trường hợp nhất định. Sau phần đầu này có thể có các phần mở rộng tùy chọn, như mô tả bên dưới. Cuối cùng, phần đầu được theo sau bởi payload RTP, định dạng của nó do ứng dụng quyết định. Mục đích của phần đầu này là chỉ chứa các trường có khả năng được nhiều ứng dụng khác nhau sử dụng, vì bất cứ thứ gì quá đặc thù cho một ứng dụng sẽ hiệu quả hơn nếu được mang trong payload RTP chỉ cho ứng dụng đó.

.. _fig-rtp-hdr:
.. figure:: figures/f05-23-9780123850591.png
   :width: 500px
   :align: center

   Định dạng phần đầu RTP.

Hai bit đầu là định danh phiên bản, chứa giá trị 2 trong phiên bản RTP được triển khai tại thời điểm viết sách. Bạn có thể nghĩ rằng các nhà thiết kế giao thức khá táo bạo khi cho rằng 2 bit là đủ cho tất cả các phiên bản RTP trong tương lai, nhưng hãy nhớ rằng từng bit đều quý giá trong phần đầu RTP. Hơn nữa, việc sử dụng profile cho các ứng dụng khác nhau làm giảm khả năng phải sửa đổi nhiều lần giao thức RTP gốc. Dù sao, nếu cần một phiên bản RTP khác ngoài phiên bản 2, có thể xem xét thay đổi định dạng phần đầu để có thể có nhiều phiên bản tương lai hơn. Ví dụ, một phần đầu RTP mới với giá trị 3 ở trường phiên bản có thể có một trường “subversion” ở nơi khác trong phần đầu.

Bit tiếp theo là bit *padding* (``P``), được đặt trong trường hợp payload RTP được đệm thêm vì lý do nào đó. Dữ liệu RTP có thể được đệm để lấp đầy một khối có kích thước nhất định theo yêu cầu của thuật toán mã hóa, chẳng hạn. Trong trường hợp này, tổng độ dài của phần đầu RTP, dữ liệu và padding sẽ được truyền bởi phần đầu giao thức tầng dưới (ví dụ, phần đầu UDP), và byte cuối cùng của padding sẽ chứa số byte cần bỏ qua. Điều này được minh họa ở :numref:`Hình %s <fig-rtp-pad>`. Lưu ý rằng cách đệm này loại bỏ nhu cầu có trường độ dài trong phần đầu RTP (giúp giữ phần đầu ngắn); trong trường hợp phổ biến không có padding, độ dài được suy ra từ giao thức tầng dưới.

.. _fig-rtp-pad:
.. figure:: figures/f05-24-9780123850591.png
   :width: 600px
   :align: center

   Đệm một gói RTP.

Bit *extension* (``X``) được dùng để chỉ sự hiện diện của phần đầu mở rộng, được định nghĩa cho một ứng dụng cụ thể và nằm sau phần đầu chính. Các phần đầu này hiếm khi được sử dụng, vì thường có thể định nghĩa một phần đầu đặc thù payload như một phần của định nghĩa format payload cho ứng dụng cụ thể.

Sau bit ``X`` là trường 4 bit đếm số *nguồn đóng góp* (contributing sources), nếu có, được đưa vào phần đầu. Các nguồn đóng góp sẽ được bàn ở phần dưới.

Chúng ta đã đề cập ở trên về nhu cầu thường xuyên phải chỉ báo khung; điều này được cung cấp bởi bit marker, có cách sử dụng phụ thuộc vào profile. Với ứng dụng thoại, nó có thể được đặt ở đầu một talkspurt, chẳng hạn. Trường loại payload 7 bit theo sau; nó chỉ ra loại dữ liệu đa phương tiện nào được mang trong gói này. Một cách sử dụng trường này là cho phép ứng dụng chuyển đổi giữa các phương pháp mã hóa dựa trên thông tin về tài nguyên mạng hoặc phản hồi về chất lượng ứng dụng. Cách sử dụng chính xác của loại payload cũng do profile ứng dụng quyết định.

Lưu ý rằng loại payload thường không được dùng làm khóa phân kênh để chuyển dữ liệu đến các ứng dụng khác nhau (hoặc đến các luồng khác nhau trong cùng một ứng dụng, như luồng âm thanh và video cho hội nghị truyền hình). Điều này là vì việc phân kênh thường được cung cấp ở tầng dưới (ví dụ, bởi UDP, như đã mô tả ở phần trước). Do đó, hai luồng đa phương tiện sử dụng RTP thường dùng các số cổng UDP khác nhau.

Số thứ tự (sequence number) được dùng để cho phép bên nhận luồng RTP phát hiện các gói bị mất hoặc sai thứ tự. Bên gửi chỉ cần tăng giá trị này lên một cho mỗi gói gửi đi. Lưu ý rằng RTP không làm gì khi phát hiện gói bị mất, khác với TCP, vốn vừa sửa lỗi mất gói (bằng truyền lại) vừa coi mất gói là chỉ báo tắc nghẽn (có thể khiến nó giảm kích thước cửa sổ). Thay vào đó, ứng dụng sẽ quyết định phải làm gì khi mất gói vì quyết định này phụ thuộc rất nhiều vào ứng dụng. Ví dụ, một ứng dụng video có thể quyết định rằng tốt nhất khi mất gói là phát lại khung hình cuối cùng nhận đúng. Một số ứng dụng cũng có thể quyết định thay đổi thuật toán mã hóa để giảm băng thông khi mất gói, nhưng đây không phải là chức năng của RTP. Sẽ không hợp lý nếu RTP quyết định giảm tốc độ gửi, vì điều này có thể khiến ứng dụng trở nên vô dụng.

Chức năng của trường timestamp là cho phép bên nhận phát lại các mẫu đúng khoảng thời gian và cho phép đồng bộ hóa các luồng đa phương tiện khác nhau. Vì các ứng dụng khác nhau có thể yêu cầu độ phân giải thời gian khác nhau, bản thân RTP không quy định đơn vị đo thời gian. Thay vào đó, timestamp chỉ là bộ đếm “tick”, trong đó thời gian giữa các tick phụ thuộc vào phương pháp mã hóa sử dụng. Ví dụ, một ứng dụng âm thanh lấy mẫu mỗi 125 μs có thể dùng giá trị đó làm độ phân giải đồng hồ. Độ phân giải đồng hồ là một trong những chi tiết được quy định trong profile hoặc format payload của ứng dụng.

Giá trị timestamp trong gói là một số đại diện cho thời điểm *mẫu đầu tiên* trong gói được tạo ra. Timestamp không phản ánh thời gian thực trong ngày; chỉ sự chênh lệch giữa các timestamp mới quan trọng. Ví dụ, nếu khoảng lấy mẫu là 125 μs và mẫu đầu tiên trong gói n+1 được tạo ra 10 ms sau mẫu đầu tiên trong gói n, thì số lần lấy mẫu giữa hai mẫu này là

.. centered:: TimeBetweenPackets / TimePerSample

.. centered:: = (10 × 10\ :sup:`-3`\ ) / (125 × 10\ :sup:`-6`\ ) = 80

Giả sử độ phân giải đồng hồ giống khoảng lấy mẫu, thì timestamp trong gói n+1 sẽ lớn hơn timestamp trong gói n là 80. Lưu ý rằng có thể gửi ít hơn 80 mẫu do các kỹ thuật nén như phát hiện im lặng, nhưng timestamp vẫn cho phép bên nhận phát lại các mẫu với mối quan hệ thời gian chính xác.

Nguồn đồng bộ hóa (SSRC) là một số 32 bit xác định duy nhất một nguồn của luồng RTP. Trong một hội nghị đa phương tiện, mỗi bên gửi chọn ngẫu nhiên một SSRC và phải giải quyết xung đột trong trường hợp hiếm có hai nguồn chọn cùng giá trị. Bằng cách làm cho định danh nguồn khác với địa chỉ mạng hoặc vận chuyển của nguồn, RTP đảm bảo tính độc lập với giao thức tầng dưới. Nó cũng cho phép một nút duy nhất với nhiều nguồn (ví dụ, nhiều camera) phân biệt các nguồn đó. Khi một nút tạo ra các luồng đa phương tiện khác nhau (ví dụ, âm thanh và video), không bắt buộc phải dùng cùng SSRC cho mỗi luồng, vì có các cơ chế trong RTCP (mô tả dưới đây) cho phép đồng bộ hóa liên phương tiện.

Nguồn đóng góp (CSRC) chỉ được dùng khi một số luồng RTP đi qua một bộ trộn (mixer). Bộ trộn có thể được dùng để giảm yêu cầu băng thông cho một hội nghị bằng cách nhận dữ liệu từ nhiều nguồn và gửi thành một luồng duy nhất. Ví dụ, các luồng âm thanh từ nhiều người nói cùng lúc có thể được giải mã và mã hóa lại thành một luồng âm thanh duy nhất. Trong trường hợp này, bộ trộn tự liệt kê mình là nguồn đồng bộ hóa nhưng cũng liệt kê các nguồn đóng góp—các giá trị SSRC của những người nói đã đóng góp vào gói đó.

5.4.3 Giao thức điều khiển
--------------------------

RTCP cung cấp một luồng điều khiển liên kết với luồng dữ liệu cho một ứng dụng đa phương tiện. Luồng điều khiển này cung cấp ba chức năng chính:

1. Phản hồi về hiệu năng của ứng dụng và mạng

2. Cách liên kết và đồng bộ hóa các luồng đa phương tiện khác nhau xuất phát từ cùng một bên gửi

3. Cách truyền đạt định danh của bên gửi để hiển thị trên giao diện người dùng.

Chức năng đầu tiên có thể hữu ích để phát hiện và phản ứng với tắc nghẽn. Một số ứng dụng có thể hoạt động ở các tốc độ khác nhau và có thể sử dụng dữ liệu hiệu năng để quyết định sử dụng thuật toán nén mạnh hơn nhằm giảm tắc nghẽn, hoặc gửi luồng chất lượng cao hơn khi ít tắc nghẽn. Phản hồi hiệu năng cũng hữu ích trong việc chẩn đoán sự cố mạng.

Bạn có thể nghĩ rằng chức năng thứ hai đã được cung cấp bởi định danh nguồn đồng bộ hóa (SSRC) của RTP, nhưng thực tế không phải vậy. Như đã đề cập, nhiều camera từ một nút có thể có các giá trị SSRC khác nhau. Hơn nữa, không có yêu cầu nào rằng luồng âm thanh và video từ cùng một nút phải dùng cùng SSRC. Vì có thể xảy ra xung đột giá trị SSRC, có thể cần thay đổi giá trị SSRC của một luồng. Để xử lý vấn đề này, RTCP sử dụng khái niệm *tên chuẩn* (CNAME) được gán cho bên gửi, sau đó liên kết với các giá trị SSRC khác nhau mà bên gửi đó có thể sử dụng thông qua các cơ chế RTCP.

Chỉ liên kết hai luồng chỉ là một phần của vấn đề đồng bộ hóa liên phương tiện. Vì các luồng khác nhau có thể có đồng hồ hoàn toàn khác nhau (với độ phân giải khác nhau và thậm chí sai số khác nhau), cần có cách đồng bộ hóa chính xác các luồng với nhau. RTCP giải quyết vấn đề này bằng cách truyền thông tin thời gian liên kết thời gian thực với các timestamp phụ thuộc tốc độ đồng hồ được mang trong các gói dữ liệu RTP.

RTCP định nghĩa một số loại gói khác nhau, bao gồm

-  Báo cáo bên gửi, cho phép các bên gửi chủ động trong một phiên báo cáo thống kê truyền và nhận

-  Báo cáo bên nhận, cho các bên nhận không phải là bên gửi dùng để báo cáo thống kê nhận

-  Mô tả nguồn, mang CNAME và các thông tin mô tả bên gửi khác

-  Gói điều khiển đặc thù ứng dụng

Các loại gói RTCP này được gửi qua giao thức tầng dưới, thường là UDP. Nhiều gói RTCP có thể được đóng gói vào một PDU của giao thức tầng dưới. Yêu cầu là phải có ít nhất hai gói RTCP trong mỗi PDU tầng dưới: Một là gói báo cáo; cái còn lại là gói mô tả nguồn. Các gói khác có thể được thêm vào cho đến khi đạt giới hạn kích thước của giao thức tầng dưới.

Trước khi xem xét sâu hơn nội dung của một gói RTCP, cần lưu ý rằng có một vấn đề tiềm ẩn khi mọi thành viên của một nhóm multicast đều gửi lưu lượng điều khiển định kỳ. Nếu không có biện pháp hạn chế, lưu lượng điều khiển này có thể tiêu tốn băng thông đáng kể. Trong một hội nghị âm thanh, ví dụ, thường chỉ có hai hoặc ba người nói cùng lúc, vì không ai nói cùng lúc với tất cả mọi người. Nhưng không có giới hạn xã hội nào đối với việc mọi người đều gửi lưu lượng điều khiển, và điều này có thể là vấn đề nghiêm trọng trong một hội nghị với hàng nghìn người tham gia. Để xử lý vấn đề này, RTCP có một tập hợp cơ chế cho phép các thành viên giảm tần suất báo cáo khi số lượng người tham gia tăng lên. Các quy tắc này khá phức tạp, nhưng mục tiêu cơ bản là: Giới hạn tổng lưu lượng RTCP ở một tỷ lệ nhỏ (thường là 5%) so với lưu lượng dữ liệu RTP. Để đạt mục tiêu này, các thành viên cần biết lượng băng thông dữ liệu có thể sử dụng (ví dụ, lượng cần để gửi ba luồng âm thanh) và số lượng người tham gia. Họ biết lượng băng thông từ các phương tiện ngoài RTP (gọi là *quản lý phiên*, sẽ bàn ở cuối phần này), và biết số lượng người tham gia từ các báo cáo RTCP của các thành viên khác. Vì các báo cáo RTCP có thể được gửi với tần suất rất thấp, có thể chỉ ước lượng được số người nhận hiện tại, nhưng thường là đủ. Ngoài ra, nên phân bổ nhiều băng thông RTCP hơn cho các bên gửi chủ động, vì hầu hết người tham gia muốn xem báo cáo từ họ—ví dụ, để biết ai đang nói.

Khi một thành viên đã xác định được lượng băng thông có thể dùng cho lưu lượng RTCP, họ sẽ gửi các báo cáo định kỳ với tốc độ phù hợp. Báo cáo bên gửi và bên nhận chỉ khác nhau ở chỗ báo cáo bên gửi có thêm một số thông tin về bên gửi. Cả hai loại báo cáo đều chứa thông tin về dữ liệu nhận được từ tất cả các nguồn trong kỳ báo cáo gần nhất.

Thông tin bổ sung trong báo cáo bên gửi bao gồm

-  Một timestamp chứa thời gian thực khi báo cáo này được tạo

-  Timestamp RTP tương ứng với thời điểm báo cáo được tạo

-  Tổng số gói và byte đã gửi bởi bên gửi này kể từ khi bắt đầu truyền

Lưu ý rằng hai giá trị đầu tiên có thể được dùng để đồng bộ hóa các luồng đa phương tiện khác nhau từ cùng một nguồn, ngay cả khi các luồng đó dùng độ phân giải đồng hồ khác nhau trong các luồng dữ liệu RTP, vì nó cung cấp khóa để chuyển đổi thời gian thực sang timestamp RTP.

Cả báo cáo bên gửi và bên nhận đều chứa một khối dữ liệu cho mỗi nguồn đã nghe kể từ báo cáo trước. Mỗi khối chứa các thống kê sau cho nguồn đó:

-  SSRC của nó

-  Tỷ lệ gói dữ liệu từ nguồn này bị mất kể từ báo cáo trước (tính bằng cách so sánh số gói nhận được với số gói mong đợi; giá trị này xác định từ số thứ tự RTP)

-  Tổng số gói bị mất từ nguồn này kể từ lần đầu tiên nghe thấy

-  Số thứ tự lớn nhất nhận được từ nguồn này (mở rộng thành 32 bit để xử lý trường hợp tràn số thứ tự)

-  Độ lệch thời gian đến dự kiến (jitter) ước lượng cho nguồn này (tính bằng cách so sánh khoảng cách giữa các gói nhận được với khoảng cách dự kiến tại thời điểm truyền)

-  Timestamp thực tế cuối cùng nhận được qua RTCP cho nguồn này

-  Độ trễ kể từ báo cáo bên gửi cuối cùng nhận được qua RTCP cho nguồn này

Như bạn có thể hình dung, người nhận thông tin này có thể biết được rất nhiều điều về trạng thái của phiên. Đặc biệt, họ có thể thấy nếu những người nhận khác nhận được chất lượng tốt hơn nhiều từ một bên gửi nào đó, điều này có thể là dấu hiệu cần đặt trước tài nguyên, hoặc có vấn đề trong mạng cần được xử lý. Ngoài ra, nếu một bên gửi nhận thấy nhiều người nhận bị mất nhiều gói của mình, họ có thể quyết định giảm tốc độ gửi hoặc sử dụng thuật toán mã hóa chống mất gói tốt hơn.

Khía cạnh cuối cùng của RTCP mà chúng ta sẽ xem xét là gói mô tả nguồn. Gói này chứa tối thiểu SSRC của bên gửi và CNAME của bên gửi. Tên chuẩn được tạo ra sao cho tất cả các ứng dụng tạo ra các luồng đa phương tiện có thể cần đồng bộ hóa (ví dụ, các luồng âm thanh và video được tạo riêng biệt từ cùng một người dùng) sẽ chọn cùng một CNAME dù có thể chọn các giá trị SSRC khác nhau. Điều này cho phép bên nhận xác định các luồng đa phương tiện đến từ cùng một người gửi. Định dạng phổ biến nhất của CNAME là ``user@host``, trong đó ``host`` là tên miền đầy đủ của máy gửi. Do đó, một ứng dụng được khởi động bởi người dùng có tên đăng nhập là ``jdoe`` trên máy ``cicada.cs.princeton.edu`` sẽ dùng chuỗi ``jdoe@cicada.cs.princeton.edu`` làm CNAME của mình. Số byte lớn và biến đổi trong biểu diễn này khiến nó không phù hợp làm định dạng của SSRC, vì SSRC được gửi trong mọi gói dữ liệu và phải xử lý theo thời gian thực. Việc cho phép liên kết CNAME với SSRC trong các thông điệp RTCP định kỳ giúp định dạng SSRC ngắn gọn và hiệu quả.

Các mục khác có thể được đưa vào gói mô tả nguồn, như tên thật và địa chỉ email của người dùng. Chúng được dùng để hiển thị trên giao diện người dùng và liên hệ với người tham gia, nhưng không thiết yếu với hoạt động của RTP như CNAME.

Giống như TCP, RTP và RTCP là một cặp giao thức khá phức tạp. Độ phức tạp này chủ yếu xuất phát từ mong muốn làm cho cuộc sống của lập trình viên ứng dụng dễ dàng hơn. Vì có vô số ứng dụng có thể có, thách thức khi thiết kế một giao thức vận chuyển là làm cho nó đủ tổng quát để đáp ứng nhu cầu rất đa dạng của nhiều ứng dụng khác nhau mà không khiến giao thức trở nên không thể triển khai. RTP đã chứng tỏ rất thành công về mặt này, trở thành nền tảng cho nhiều ứng dụng đa phương tiện thời gian thực chạy trên Internet ngày nay.
