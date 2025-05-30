6.5 Chất lượng dịch vụ (Quality of Service)
===========================================

Lời hứa của các mạng chuyển mạch gói đa dụng là chúng hỗ trợ mọi loại ứng dụng và dữ liệu, bao gồm cả các ứng dụng đa phương tiện truyền tải các luồng âm thanh và video số hóa. Trong những ngày đầu, một trở ngại để thực hiện lời hứa này là nhu cầu về các liên kết băng thông cao hơn. Đó không còn là vấn đề nữa, nhưng việc truyền âm thanh và video qua mạng còn nhiều điều hơn chỉ là cung cấp đủ băng thông.

Ví dụ, những người tham gia một cuộc trò chuyện điện thoại mong muốn có thể trò chuyện sao cho một người có thể đáp lại điều người kia vừa nói và được nghe gần như ngay lập tức. Do đó, tính kịp thời của việc truyền dữ liệu có thể rất quan trọng. Chúng ta gọi các ứng dụng nhạy cảm với tính kịp thời của dữ liệu là *ứng dụng thời gian thực* (real-time applications). Các ứng dụng thoại và video thường là ví dụ điển hình, nhưng còn có các ứng dụng khác như điều khiển công nghiệp—bạn muốn lệnh gửi đến cánh tay robot phải đến trước khi cánh tay đó va vào vật gì đó. Ngay cả các ứng dụng truyền tệp cũng có thể có ràng buộc về thời gian, ví dụ như yêu cầu cập nhật cơ sở dữ liệu phải hoàn thành qua đêm trước khi doanh nghiệp cần dữ liệu đó hoạt động trở lại vào ngày hôm sau.

Đặc điểm nổi bật của các ứng dụng thời gian thực là chúng cần một số đảm bảo *từ mạng* rằng dữ liệu có khả năng đến đúng lúc (theo một định nghĩa nào đó về “đúng lúc”). Trong khi một ứng dụng không thời gian thực có thể sử dụng chiến lược truyền lại đầu-cuối để đảm bảo dữ liệu đến *chính xác*, thì chiến lược này không thể đảm bảo tính kịp thời: Việc truyền lại chỉ làm tăng tổng độ trễ nếu dữ liệu đến muộn. Việc đến đúng lúc phải được cung cấp bởi chính mạng (các bộ định tuyến), không chỉ ở các đầu mạng (các máy chủ). Do đó, chúng ta kết luận rằng mô hình best-effort, trong đó mạng cố gắng truyền dữ liệu của bạn nhưng không hứa hẹn gì và để việc xử lý lỗi cho các đầu mạng, là không đủ cho các ứng dụng thời gian thực. Điều chúng ta cần là một mô hình dịch vụ mới, trong đó các ứng dụng cần đảm bảo cao hơn có thể yêu cầu mạng cung cấp. Mạng sau đó có thể đáp lại bằng cách đảm bảo sẽ làm tốt hơn hoặc có thể nói rằng hiện tại không thể hứa hẹn gì tốt hơn. Lưu ý rằng mô hình dịch vụ như vậy là một tập siêu của mô hình ban đầu: Các ứng dụng hài lòng với dịch vụ best-effort vẫn có thể sử dụng mô hình dịch vụ mới; chỉ là yêu cầu của chúng ít nghiêm ngặt hơn. Điều này ngụ ý rằng mạng sẽ xử lý một số gói tin khác với những gói khác—điều không có trong mô hình best-effort. Một mạng có thể cung cấp các mức dịch vụ khác nhau như vậy thường được nói là hỗ trợ chất lượng dịch vụ (QoS).

6.5.1 Yêu cầu của ứng dụng
--------------------------

Trước khi xem xét các giao thức và cơ chế khác nhau có thể được sử dụng để cung cấp chất lượng dịch vụ cho các ứng dụng, chúng ta nên cố gắng hiểu nhu cầu của các ứng dụng đó là gì. Đầu tiên, chúng ta có thể chia ứng dụng thành hai loại: thời gian thực và không thời gian thực. Loại sau đôi khi được gọi là *ứng dụng dữ liệu truyền thống* (traditional data applications), vì chúng thường là các ứng dụng chính trên các mạng dữ liệu. Chúng bao gồm hầu hết các ứng dụng phổ biến như SSH, truyền tệp, email, duyệt web, v.v. Tất cả các ứng dụng này đều có thể hoạt động mà không cần đảm bảo về việc truyền dữ liệu đúng lúc. Một thuật ngữ khác cho lớp ứng dụng không thời gian thực này là *co giãn* (elastic), vì chúng có thể “giãn” một cách linh hoạt khi độ trễ tăng lên. Lưu ý rằng các ứng dụng này có thể hưởng lợi từ độ trễ ngắn hơn, nhưng chúng không trở nên không sử dụng được khi độ trễ tăng. Ngoài ra, yêu cầu về độ trễ của chúng cũng khác nhau, từ các ứng dụng tương tác như SSH đến các ứng dụng không đồng bộ như email, với các ứng dụng truyền tải lớn tương tác như truyền tệp ở giữa.

.. _fig-audio:
.. figure:: figures/f06-20-9780123850591.png
   :width: 600px
   :align: center

   Một ứng dụng âm thanh.

Ví dụ về âm thanh thời gian thực
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Lấy ví dụ cụ thể về một ứng dụng thời gian thực, hãy xem xét một ứng dụng âm thanh tương tự như minh họa ở :numref:`Hình %s <fig-audio>`. Dữ liệu được tạo ra bằng cách thu thập các mẫu từ micro và số hóa chúng bằng bộ chuyển đổi tương tự-số (A-to-D). Các mẫu số được đặt vào các gói tin, truyền qua mạng và nhận ở đầu bên kia. Ở máy chủ nhận, dữ liệu phải được *phát lại* ở tốc độ phù hợp. Ví dụ, nếu các mẫu thoại được thu thập với tốc độ một mẫu mỗi 125 μs, chúng cũng phải được phát lại với tốc độ đó. Do đó, chúng ta có thể coi mỗi mẫu có một *thời điểm phát lại* cụ thể: thời điểm mà nó cần thiết ở máy chủ nhận. Trong ví dụ thoại, mỗi mẫu có thời điểm phát lại cách mẫu trước đó 125 μs. Nếu dữ liệu đến sau thời điểm phát lại phù hợp, dù do bị trễ trong mạng hay bị mất và truyền lại sau đó, thì về cơ bản nó trở nên vô dụng. Chính sự vô dụng hoàn toàn của dữ liệu đến muộn là đặc trưng của các ứng dụng thời gian thực. Với các ứng dụng co giãn, sẽ tốt hơn nếu dữ liệu đến đúng lúc, nhưng chúng ta vẫn có thể sử dụng khi nó đến muộn.

Một cách để làm cho ứng dụng thoại hoạt động là đảm bảo tất cả các mẫu mất cùng một lượng thời gian để đi qua mạng. Khi đó, vì các mẫu được đưa vào với tốc độ một mẫu mỗi 125 μs, chúng sẽ xuất hiện ở phía nhận với cùng tốc độ, sẵn sàng để phát lại. Tuy nhiên, nhìn chung rất khó đảm bảo tất cả dữ liệu đi qua mạng chuyển mạch gói đều trải qua cùng một độ trễ. Các gói gặp hàng đợi trong các switch hoặc router, và độ dài các hàng đợi này thay đổi theo thời gian, nghĩa là độ trễ cũng thay đổi theo thời gian và do đó có thể khác nhau với mỗi gói trong luồng âm thanh. Cách xử lý ở phía nhận là đệm một lượng dữ liệu dự phòng, nhờ đó luôn có một kho gói chờ được phát lại đúng lúc. Nếu một gói bị trễ ngắn, nó sẽ nằm trong bộ đệm cho đến khi đến thời điểm phát lại. Nếu nó bị trễ lâu, thì nó sẽ không cần lưu trong bộ đệm lâu trước khi được phát lại. Như vậy, chúng ta đã thêm một độ trễ cố định vào thời điểm phát lại của tất cả các gói như một hình thức bảo hiểm. Chúng ta gọi độ trễ này là *điểm phát lại* (playback point). Chỉ khi các gói bị trễ quá lâu trong mạng đến mức đến sau thời điểm phát lại thì bộ đệm phát lại mới bị cạn.

Hoạt động của bộ đệm phát lại được minh họa ở :numref:`Hình %s <fig-playback>`. Đường chéo bên trái cho thấy các gói được tạo ra với tốc độ đều đặn. Đường lượn sóng cho thấy khi các gói đến, với một lượng thời gian thay đổi sau khi được gửi, tùy thuộc vào những gì chúng gặp trong mạng. Đường chéo bên phải cho thấy các gói được phát lại với tốc độ đều đặn, sau khi nằm trong bộ đệm phát lại một thời gian. Miễn là đường phát lại đủ xa về bên phải theo thời gian, sự biến đổi độ trễ mạng sẽ không bị ứng dụng nhận biết. Tuy nhiên, nếu chúng ta di chuyển đường phát lại sang trái một chút, một số gói sẽ bắt đầu đến quá muộn để sử dụng.

.. _fig-playback:
.. figure:: figures/f06-21-9780123850591.png
   :width: 500px
   :align: center

   Bộ đệm phát lại.

Với ứng dụng âm thanh của chúng ta, có giới hạn về việc trì hoãn phát lại dữ liệu. Rất khó để trò chuyện nếu thời gian giữa lúc bạn nói và lúc người nghe nghe được vượt quá 300 ms. Do đó, điều chúng ta muốn từ mạng trong trường hợp này là đảm bảo tất cả dữ liệu sẽ đến trong vòng 300 ms. Nếu dữ liệu đến sớm, chúng ta đệm lại cho đến thời điểm phát lại phù hợp. Nếu đến muộn, chúng ta không sử dụng được và phải loại bỏ.

.. _fig-jitter2:
.. figure:: figures/f06-22-9780123850591.png
   :width: 500px
   :align: center

   Ví dụ về phân bố độ trễ cho một kết nối Internet.

Để hiểu rõ hơn về sự biến đổi độ trễ mạng, :numref:`Hình %s <fig-jitter2>` cho thấy độ trễ một chiều đo được trên một đường đi nhất định qua Internet trong một ngày cụ thể. Dù các con số cụ thể sẽ thay đổi tùy đường đi và ngày đo, yếu tố chính ở đây là *sự biến đổi* của độ trễ, điều này luôn xuất hiện trên hầu hết mọi đường đi vào bất kỳ thời điểm nào. Như thể hiện qua tỷ lệ phần trăm tích lũy ở phía trên đồ thị, 97% các gói trong trường hợp này có độ trễ không quá 100 ms. Điều này có nghĩa là nếu ứng dụng âm thanh ví dụ của chúng ta đặt điểm phát lại ở 100 ms, thì trung bình cứ 100 gói sẽ có 3 gói đến quá muộn để sử dụng. Một điều quan trọng cần chú ý là phần đuôi của đường cong—nó kéo dài rất xa về bên phải. Chúng ta sẽ phải đặt điểm phát lại trên 200 ms để đảm bảo tất cả các gói đến kịp thời.

Phân loại các ứng dụng thời gian thực
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Bây giờ chúng ta đã có ý tưởng cụ thể về cách các ứng dụng thời gian thực hoạt động, hãy xem xét một số lớp ứng dụng khác nhau để làm động lực cho mô hình dịch vụ của chúng ta. Phân loại này dựa nhiều vào công trình của Clark, Braden, Shenker và Zhang, các bài báo của họ có thể tìm thấy ở phần Đọc thêm của chương này. Phân loại ứng dụng được tóm tắt ở :numref:`Hình %s <fig-taxonomy>`.

.. _fig-taxonomy:
.. figure:: figures/f06-23-9780123850591.png
   :width: 500px
   :align: center

   Phân loại ứng dụng.

Đặc điểm đầu tiên để phân loại ứng dụng là khả năng chịu mất dữ liệu, trong đó “mất” có thể xảy ra vì một gói đến quá muộn để phát lại cũng như do các nguyên nhân thông thường trong mạng. Một mặt, một mẫu âm thanh bị mất có thể được nội suy từ các mẫu xung quanh mà ít ảnh hưởng đến chất lượng âm thanh cảm nhận. Chỉ khi mất nhiều mẫu thì chất lượng mới giảm đến mức lời nói trở nên không thể hiểu được. Mặt khác, một chương trình điều khiển robot có thể là ví dụ về ứng dụng thời gian thực không thể chịu mất dữ liệu—mất gói chứa lệnh dừng cánh tay robot là không chấp nhận được. Do đó, chúng ta có thể phân loại ứng dụng thời gian thực là *chịu lỗi* (tolerant) hoặc *không chịu lỗi* (intolerant) tùy vào việc chúng có thể chịu mất dữ liệu thỉnh thoảng hay không. (Lưu ý rằng nhiều ứng dụng thời gian thực chịu mất dữ liệu tốt hơn các ứng dụng không thời gian thực; ví dụ, so sánh ứng dụng âm thanh với truyền tệp, nơi mất một bit không được sửa có thể làm tệp hoàn toàn vô dụng.)

Cách thứ hai để phân loại ứng dụng thời gian thực là theo khả năng thích nghi. Ví dụ, một ứng dụng âm thanh có thể thích nghi với độ trễ mà các gói gặp phải khi đi qua mạng. Nếu chúng ta nhận thấy các gói gần như luôn đến trong vòng 300 ms kể từ khi gửi, chúng ta có thể đặt điểm phát lại tương ứng, đệm các gói đến sớm hơn 300 ms. Giả sử sau đó chúng ta quan sát thấy tất cả các gói đều đến trong vòng 100 ms kể từ khi gửi. Nếu chúng ta chuyển điểm phát lại lên 100 ms, người dùng ứng dụng có thể cảm nhận được sự cải thiện. Quá trình điều chỉnh điểm phát lại thực tế sẽ yêu cầu phát các mẫu với tốc độ tăng lên trong một thời gian. Với ứng dụng thoại, điều này có thể thực hiện mà người dùng hầu như không nhận ra, chỉ bằng cách rút ngắn các khoảng lặng giữa các từ. Do đó, điều chỉnh điểm phát lại khá dễ dàng trong trường hợp này, và đã được triển khai hiệu quả cho một số ứng dụng thoại như chương trình hội nghị âm thanh ``vat``. Lưu ý rằng điều chỉnh điểm phát lại có thể theo cả hai hướng, nhưng thực tế sẽ làm méo tín hiệu phát lại trong thời gian điều chỉnh, và mức độ ảnh hưởng sẽ phụ thuộc vào cách người dùng sử dụng dữ liệu.

Lưu ý rằng nếu chúng ta đặt điểm phát lại dựa trên giả định tất cả các gói sẽ đến trong vòng 100 ms và sau đó phát hiện một số gói đến hơi muộn, chúng ta sẽ phải loại bỏ chúng, trong khi nếu giữ điểm phát lại ở 300 ms thì không cần loại bỏ. Do đó, chỉ nên điều chỉnh điểm phát lại khi thực sự mang lại lợi ích cảm nhận được và khi có bằng chứng rằng số gói đến muộn sẽ đủ nhỏ. Chúng ta có thể làm điều này dựa trên lịch sử quan sát gần đây hoặc dựa vào đảm bảo từ mạng.

Chúng ta gọi các ứng dụng có thể điều chỉnh điểm phát lại là *ứng dụng thích nghi độ trễ* (delay-adaptive). Một lớp ứng dụng thích nghi khác là *thích nghi tốc độ* (rate adaptive). Ví dụ, nhiều thuật toán mã hóa video có thể đánh đổi giữa tốc độ bit và chất lượng. Do đó, nếu chúng ta thấy mạng hỗ trợ một băng thông nhất định, chúng ta có thể đặt tham số mã hóa tương ứng. Nếu sau này có thêm băng thông, chúng ta có thể thay đổi tham số để tăng chất lượng.

Các cách tiếp cận hỗ trợ QoS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Xét không gian yêu cầu ứng dụng đa dạng này, điều chúng ta cần là một mô hình dịch vụ phong phú hơn đáp ứng nhu cầu của mọi ứng dụng. Điều này dẫn đến một mô hình dịch vụ không chỉ có một lớp (best effort), mà có nhiều lớp, mỗi lớp đáp ứng nhu cầu của một nhóm ứng dụng. Để làm được điều này, chúng ta sẽ xem xét một số cách tiếp cận đã được phát triển để cung cấp nhiều mức chất lượng dịch vụ. Chúng có thể chia thành hai loại lớn:

-  Cách tiếp cận *tinh* (fine-grained), cung cấp QoS cho từng ứng dụng hoặc luồng riêng lẻ

-  Cách tiếp cận *thô* (coarse-grained), cung cấp QoS cho các lớp dữ liệu lớn hoặc lưu lượng tổng hợp

Ở loại đầu tiên, chúng ta có *Dịch vụ tích hợp* (Integrated Services), một kiến trúc QoS do IETF phát triển và thường gắn với Giao thức Đặt trước Tài nguyên (RSVP). Ở loại thứ hai là *Dịch vụ phân biệt* (Differentiated Services), có lẽ là cơ chế QoS được triển khai rộng rãi nhất hiện nay. Chúng ta sẽ lần lượt thảo luận về chúng ở hai phần tiếp theo.

Cuối cùng, như đã đề cập ở đầu mục này, việc bổ sung hỗ trợ QoS cho mạng không nhất thiết là toàn bộ câu chuyện về hỗ trợ ứng dụng thời gian thực. Chúng ta sẽ kết thúc phần này bằng cách xem xét lại những gì máy chủ đầu cuối có thể làm để hỗ trợ tốt hơn các luồng thời gian thực, bất kể các cơ chế QoS như Integrated hay Differentiated Services được triển khai rộng rãi đến đâu.

6.5.2 Dịch vụ tích hợp (RSVP)
-----------------------------

Thuật ngữ *Dịch vụ tích hợp* (Integrated Services, thường gọi tắt là IntServ) đề cập đến một loạt công trình do IETF thực hiện khoảng năm 1995-97. Nhóm làm việc IntServ đã phát triển các đặc tả cho một số *lớp dịch vụ* nhằm đáp ứng nhu cầu của các loại ứng dụng đã mô tả ở trên. Họ cũng định nghĩa cách RSVP có thể được sử dụng để đặt trước tài nguyên bằng các lớp dịch vụ này. Các đoạn sau đây cung cấp tổng quan về các đặc tả và cơ chế được sử dụng để triển khai chúng.

Các lớp dịch vụ
~~~~~~~~~~~~~~~

Một trong các lớp dịch vụ được thiết kế cho các ứng dụng không chịu lỗi. Các ứng dụng này yêu cầu một gói không bao giờ được đến muộn. Mạng nên đảm bảo độ trễ tối đa mà bất kỳ gói nào gặp phải có một giá trị xác định; ứng dụng sau đó có thể đặt điểm phát lại sao cho không gói nào đến sau thời điểm phát lại. Chúng ta giả định rằng các gói đến sớm luôn có thể xử lý bằng cách đệm. Dịch vụ này được gọi là *dịch vụ đảm bảo* (guaranteed service).

Ngoài dịch vụ đảm bảo, IETF đã xem xét một số dịch vụ khác, nhưng cuối cùng chọn một dịch vụ để đáp ứng nhu cầu của các ứng dụng chịu lỗi, thích nghi. Dịch vụ này gọi là *tải có kiểm soát* (controlled load) và được thúc đẩy bởi quan sát rằng các ứng dụng loại này hiện tại hoạt động khá tốt trên các mạng không bị tải nặng. Ví dụ, các ứng dụng âm thanh điều chỉnh điểm phát lại khi độ trễ mạng thay đổi và tạo ra chất lượng âm thanh hợp lý miễn là tỷ lệ mất gói ở mức khoảng 10% trở xuống.

Mục tiêu của dịch vụ tải có kiểm soát là mô phỏng một mạng tải nhẹ cho các ứng dụng yêu cầu dịch vụ này, ngay cả khi toàn mạng thực tế có thể đang bị tải nặng. Bí quyết là sử dụng một cơ chế xếp hàng như WFQ để tách biệt lưu lượng tải có kiểm soát khỏi các lưu lượng khác và một hình thức kiểm soát truy cập để giới hạn tổng lưu lượng tải có kiểm soát trên một liên kết sao cho tải được giữ ở mức hợp lý. Chúng ta sẽ bàn chi tiết về kiểm soát truy cập ở phần sau.

Rõ ràng, hai lớp dịch vụ này chỉ là một tập con của tất cả các lớp có thể cung cấp. Thực tế, các dịch vụ khác đã được đặc tả nhưng chưa bao giờ được chuẩn hóa trong công việc của IETF. Cho đến nay, hai dịch vụ trên (cùng với best effort truyền thống) đã chứng tỏ đủ linh hoạt để đáp ứng nhu cầu của nhiều loại ứng dụng.

Tổng quan về các cơ chế
~~~~~~~~~~~~~~~~~~~~~~

Bây giờ chúng ta đã bổ sung một số lớp dịch vụ mới vào mô hình best-effort, câu hỏi tiếp theo là làm thế nào để triển khai một mạng cung cấp các dịch vụ này cho ứng dụng. Phần này phác thảo các cơ chế chính. Lưu ý khi đọc phần này rằng các cơ chế được mô tả vẫn đang được cộng đồng thiết kế Internet hoàn thiện. Điều quan trọng là hiểu tổng thể các thành phần liên quan đến việc hỗ trợ mô hình dịch vụ đã nêu.

Đầu tiên, với dịch vụ best-effort, chúng ta chỉ cần nói cho mạng biết nơi muốn gửi gói và để mặc nó, còn với dịch vụ thời gian thực, chúng ta phải nói cho mạng biết thêm về loại dịch vụ mình cần. Chúng ta có thể cung cấp thông tin định tính như “sử dụng dịch vụ tải có kiểm soát” hoặc thông tin định lượng như “tôi cần độ trễ tối đa 100 ms”. Ngoài việc mô tả nhu cầu, chúng ta cần nói cho mạng biết về những gì sẽ đưa vào, vì một ứng dụng băng thông thấp sẽ cần ít tài nguyên mạng hơn ứng dụng băng thông cao. Tập hợp thông tin cung cấp cho mạng được gọi là *flowspec*. Tên này xuất phát từ ý tưởng rằng một tập hợp các gói liên quan đến một ứng dụng và có yêu cầu chung được gọi là *luồng* (flow), phù hợp với cách dùng thuật ngữ ở phần trước.

Thứ hai, khi chúng ta yêu cầu mạng cung cấp một dịch vụ cụ thể, mạng cần quyết định xem có thực sự cung cấp được không. Ví dụ, nếu 10 người dùng yêu cầu dịch vụ mà mỗi người sẽ sử dụng liên tục 2 Mbps băng thông, và tất cả cùng dùng một liên kết 10 Mbps, mạng sẽ phải từ chối một số người. Quá trình quyết định khi nào nên từ chối gọi là *kiểm soát truy cập* (admission control).

Thứ ba, chúng ta cần một cơ chế để người dùng mạng và các thành phần mạng trao đổi thông tin như yêu cầu dịch vụ, flowspec và quyết định kiểm soát truy cập. Điều này đôi khi gọi là *báo hiệu* (signalling), nhưng vì từ này có nhiều nghĩa, chúng ta gọi quá trình này là *đặt trước tài nguyên* (resource reservation), và nó được thực hiện bằng một giao thức đặt trước tài nguyên.

Cuối cùng, khi các luồng và yêu cầu của chúng đã được mô tả, quyết định kiểm soát truy cập đã được đưa ra, các switch và router trong mạng cần đáp ứng yêu cầu của các luồng. Một phần quan trọng của việc này là quản lý cách các gói được xếp hàng và lập lịch truyền trong switch và router. Cơ chế cuối cùng này là *lập lịch gói* (packet scheduling).

Flowspecs
~~~~~~~~~

Flowspec có hai phần tách biệt: phần mô tả đặc tính lưu lượng của luồng (gọi là *TSpec*) và phần mô tả dịch vụ yêu cầu từ mạng (gọi là *RSpec*). RSpec rất đặc thù cho từng dịch vụ và khá dễ mô tả. Ví dụ, với dịch vụ tải có kiểm soát, RSpec rất đơn giản: Ứng dụng chỉ yêu cầu dịch vụ tải có kiểm soát mà không cần tham số bổ sung. Với dịch vụ đảm bảo, bạn có thể chỉ định mục tiêu hoặc giới hạn độ trễ. (Trong đặc tả dịch vụ đảm bảo của IETF, bạn chỉ định một đại lượng khác từ đó có thể tính ra độ trễ.)

TSpec phức tạp hơn một chút. Như ví dụ trên đã chỉ ra, chúng ta cần cung cấp cho mạng đủ thông tin về băng thông sử dụng của luồng để có thể đưa ra quyết định kiểm soát truy cập hợp lý. Tuy nhiên, với hầu hết ứng dụng, băng thông không phải là một con số cố định; nó thay đổi liên tục. Ví dụ, một ứng dụng video thường tạo ra nhiều bit mỗi giây hơn khi cảnh thay đổi nhanh so với khi cảnh tĩnh. Chỉ biết băng thông trung bình dài hạn là không đủ, như ví dụ sau minh họa. Giả sử có 10 luồng đến một switch qua các cổng vào riêng biệt và tất cả rời đi qua một liên kết 10 Mbps. Giả sử trong một khoảng thời gian đủ dài, mỗi luồng không gửi quá 1 Mbps. Bạn có thể nghĩ rằng điều này không có vấn đề gì. Tuy nhiên, nếu đây là các ứng dụng tốc độ bit thay đổi, như video nén, thì đôi khi chúng sẽ gửi nhiều hơn tốc độ trung bình. Nếu đủ nguồn gửi vượt quá tốc độ trung bình, tổng tốc độ dữ liệu đến switch sẽ vượt quá 10 Mbps. Dữ liệu dư thừa này sẽ bị xếp hàng trước khi có thể gửi qua liên kết. Tình trạng này kéo dài càng lâu, hàng đợi càng dài. Các gói có thể bị loại bỏ và, ngay cả khi không đến mức đó, dữ liệu nằm trong hàng đợi sẽ bị trễ. Nếu các gói bị trễ đủ lâu, dịch vụ yêu cầu sẽ không được cung cấp.

Chính xác cách quản lý hàng đợi để kiểm soát độ trễ và tránh mất gói sẽ được bàn ở phần sau. Tuy nhiên, lưu ý rằng chúng ta cần biết đặc tính băng thông của nguồn thay đổi theo thời gian như thế nào. Một cách để mô tả đặc tính băng thông của nguồn là bộ lọc *token bucket*. Bộ lọc này được mô tả bởi hai tham số: tốc độ token *r* và độ sâu bucket *B*. Cách hoạt động như sau. Để gửi một byte, tôi phải có một token. Để gửi một gói dài *n*, tôi cần *n* token. Tôi bắt đầu không có token và tích lũy chúng với tốc độ *r* mỗi giây. Tôi không thể tích lũy quá *B* token. Điều này có nghĩa là tôi có thể gửi một burst tối đa *B* byte vào mạng nhanh nhất có thể, nhưng trong một khoảng thời gian đủ dài tôi không thể gửi quá *r* byte mỗi giây. Thông tin này rất hữu ích cho thuật toán kiểm soát truy cập khi quyết định có thể đáp ứng yêu cầu dịch vụ mới hay không.

.. _fig-token:
.. figure:: figures/f06-24-9780123850591.png
   :width: 300px
   :align: center

   Hai luồng có tốc độ trung bình bằng nhau nhưng mô tả token bucket khác nhau.

:numref:`Hình %s <fig-token>` minh họa cách token bucket có thể được dùng để mô tả yêu cầu băng thông của một luồng. Để đơn giản, giả sử mỗi luồng có thể gửi dữ liệu dưới dạng từng byte thay vì từng gói. Luồng A tạo dữ liệu với tốc độ đều 1 MBps, nên có thể mô tả bằng token bucket với tốc độ *r = 1* MBps và độ sâu bucket 1 byte. Điều này nghĩa là nó nhận token với tốc độ 1 MBps nhưng không thể lưu quá 1 token—nó dùng ngay lập tức. Luồng B cũng gửi với tốc độ trung bình 1 MBps trong dài hạn, nhưng làm như vậy bằng cách gửi 0,5 MBps trong 2 giây rồi 2 MBps trong 1 giây. Vì tốc độ token bucket *r* là tốc độ trung bình dài hạn, luồng B có thể mô tả bằng token bucket với tốc độ 1 MBps. Tuy nhiên, khác với luồng A, luồng B cần độ sâu bucket *B* ít nhất 1 MB, để có thể tích lũy token khi gửi dưới 1 MBps để dùng khi gửi 2 MBps. Trong 2 giây đầu, nó nhận token với tốc độ 1 MBps nhưng chỉ dùng 0,5 MBps, nên có thể tích lũy 2 × 0,5 = 1 MB token, sau đó dùng trong giây thứ ba (cùng với token mới tiếp tục nhận trong giây đó) để gửi dữ liệu với tốc độ 2 MBps. Kết thúc giây thứ ba, sau khi dùng hết token dư, nó lại bắt đầu tích lũy bằng cách gửi 0,5 MBps.

Điều thú vị là một luồng có thể được mô tả bằng nhiều token bucket khác nhau. Ví dụ đơn giản, luồng A có thể mô tả bằng token bucket giống luồng B, với tốc độ 1 MBps và độ sâu bucket 1 MB. Việc nó không cần tích lũy token không làm cho mô tả đó sai, nhưng có nghĩa là chúng ta không truyền đạt được thông tin hữu ích cho mạng—rằng luồng A thực sự rất ổn định về nhu cầu băng thông. Nói chung, nên mô tả càng rõ nhu cầu băng thông của ứng dụng càng tốt để tránh phân bổ quá mức tài nguyên mạng.

Kiểm soát truy cập
~~~~~~~~~~~~~~~~~~

Ý tưởng kiểm soát truy cập rất đơn giản: Khi một luồng mới muốn nhận một mức dịch vụ cụ thể, kiểm soát truy cập sẽ xem xét TSpec và RSpec của luồng và cố gắng quyết định liệu có thể cung cấp dịch vụ mong muốn cho lượng lưu lượng đó, với tài nguyên hiện có, mà không làm bất kỳ luồng nào đã được chấp nhận trước đó nhận dịch vụ kém hơn yêu cầu. Nếu có thể cung cấp dịch vụ, luồng được chấp nhận; nếu không, bị từ chối. Phần khó là xác định khi nào nên đồng ý và khi nào nên từ chối.

Kiểm soát truy cập phụ thuộc nhiều vào loại dịch vụ yêu cầu và kỷ luật xếp hàng được sử dụng trong router; chúng ta sẽ bàn về chủ đề sau ở phần sau. Với dịch vụ đảm bảo, bạn cần một thuật toán tốt để đưa ra quyết định đồng ý/từ chối dứt khoát. Quyết định khá đơn giản nếu sử dụng xếp hàng công bằng có trọng số (weighted fair queuing) ở mỗi router. Với dịch vụ tải có kiểm soát, quyết định có thể dựa trên kinh nghiệm, như “Lần trước tôi cho phép một luồng với TSpec này vào lớp này, độ trễ vượt quá giới hạn chấp nhận được, nên lần này tôi nên từ chối” hoặc “Độ trễ hiện tại còn rất xa giới hạn nên tôi có thể chấp nhận thêm một luồng nữa mà không gặp khó khăn.”

Kiểm soát truy cập không nên nhầm lẫn với *kiểm soát* (policing). Kiểm soát truy cập là quyết định theo từng luồng để chấp nhận hay không một luồng mới. Kiểm soát là chức năng áp dụng cho từng gói để đảm bảo một luồng tuân thủ TSpec đã dùng để đặt trước. Nếu một luồng không tuân thủ TSpec—ví dụ, gửi gấp đôi số byte mỗi giây so với khai báo—thì có thể ảnh hưởng đến dịch vụ của các luồng khác, và cần có biện pháp xử lý. Có nhiều lựa chọn, rõ ràng nhất là loại bỏ các gói vi phạm. Tuy nhiên, một lựa chọn khác là kiểm tra xem các gói đó có thực sự ảnh hưởng đến dịch vụ của luồng khác không. Nếu không ảnh hưởng, có thể gửi tiếp sau khi đánh dấu gói với một thẻ kiểu “Đây là gói không tuân thủ. Nếu cần loại bỏ gói, hãy loại bỏ nó trước.”

Kiểm soát truy cập liên quan chặt chẽ đến vấn đề *chính sách* (policy). Ví dụ, quản trị viên mạng có thể muốn cho phép các đặt trước do CEO công ty thực hiện được chấp nhận, trong khi từ chối các đặt trước của nhân viên cấp thấp hơn. Tất nhiên, yêu cầu của CEO vẫn có thể bị từ chối nếu tài nguyên không đủ, nên cả vấn đề chính sách và tài nguyên đều có thể được xem xét khi quyết định kiểm soát truy cập. Việc áp dụng chính sách vào mạng là lĩnh vực đang được quan tâm nhiều vào thời điểm viết sách này.

Giao thức đặt trước
~~~~~~~~~~~~~~~~~~

Trong khi các mạng hướng kết nối luôn cần một giao thức thiết lập để tạo trạng thái mạch ảo cần thiết trong switch, các mạng không kết nối như Internet không có các giao thức như vậy. Tuy nhiên, như đã trình bày ở phần này, chúng ta cần cung cấp nhiều thông tin hơn cho mạng khi muốn nhận dịch vụ thời gian thực. Dù đã có nhiều giao thức thiết lập được đề xuất cho Internet, giao thức được chú ý nhiều nhất hiện nay là RSVP. Nó đặc biệt thú vị vì khác biệt đáng kể với các giao thức báo hiệu truyền thống cho mạng hướng kết nối.

Một giả định then chốt của RSVP là nó không làm giảm tính bền vững vốn có của các mạng không kết nối ngày nay. Vì các mạng không kết nối dựa vào rất ít hoặc không lưu trạng thái trong mạng, nên các router có thể bị treo và khởi động lại, các liên kết có thể lên/xuống mà kết nối đầu-cuối vẫn được duy trì. RSVP cố gắng duy trì tính bền vững này bằng cách sử dụng ý tưởng *trạng thái mềm* (soft state) trong router. Trạng thái mềm—trái ngược với trạng thái cứng trong mạng hướng kết nối—không cần xóa tường minh khi không còn cần thiết. Thay vào đó, nó sẽ hết hạn sau một khoảng thời gian ngắn (ví dụ, một phút) nếu không được làm mới định kỳ. Chúng ta sẽ thấy sau cách điều này giúp tăng tính bền vững.

Một đặc điểm quan trọng khác của RSVP là nó hướng đến hỗ trợ các luồng multicast hiệu quả như các luồng unicast. Điều này không ngạc nhiên, vì nhiều ứng dụng đầu tiên có thể hưởng lợi từ QoS cải tiến cũng là ứng dụng multicast—ví dụ, công cụ hội nghị truyền hình. Một nhận định của các nhà thiết kế RSVP là hầu hết ứng dụng multicast có nhiều người nhận hơn người gửi, như một buổi giảng với đông người nghe và một người nói. Ngoài ra, người nhận có thể có yêu cầu khác nhau. Ví dụ, một người nhận chỉ muốn nhận dữ liệu từ một người gửi, trong khi người khác muốn nhận từ tất cả người gửi. Thay vì để người gửi theo dõi số lượng lớn người nhận, hợp lý hơn là để người nhận tự theo dõi nhu cầu của mình. Điều này dẫn đến cách tiếp cận *hướng người nhận* (receiver-oriented) của RSVP. Ngược lại, mạng hướng kết nối thường để việc đặt trước tài nguyên cho người gửi, giống như người gọi điện thoại là người khởi tạo việc phân bổ tài nguyên trong mạng điện thoại.

Tính trạng thái mềm và hướng người nhận của RSVP mang lại nhiều ưu điểm. Một trong số đó là rất dễ tăng hoặc giảm mức phân bổ tài nguyên cho người nhận. Vì mỗi người nhận định kỳ gửi thông điệp làm mới để giữ trạng thái mềm, nên dễ dàng gửi một đặt trước mới yêu cầu mức tài nguyên mới. Ngoài ra, trạng thái mềm xử lý tốt khả năng xảy ra lỗi mạng hoặc nút. Nếu máy chủ bị treo, tài nguyên do máy chủ đó đặt trước cho một luồng sẽ tự động hết hạn và được giải phóng. Để xem điều gì xảy ra khi router hoặc liên kết bị lỗi, chúng ta cần xem kỹ hơn về cơ chế đặt trước.

Ban đầu, xét trường hợp một người gửi và một người nhận cố gắng đặt trước tài nguyên cho lưu lượng giữa họ. Có hai việc cần làm trước khi người nhận có thể đặt trước. Đầu tiên, người nhận cần biết lưu lượng mà người gửi sẽ gửi để có thể đặt trước phù hợp, tức là cần biết TSpec của người gửi. Thứ hai, cần biết đường đi mà các gói sẽ theo từ người gửi đến người nhận, để có thể đặt trước tài nguyên tại mỗi router trên đường đi. Cả hai yêu cầu này đều có thể đáp ứng bằng cách gửi một thông điệp từ người gửi đến người nhận chứa TSpec. Rõ ràng, điều này chuyển TSpec đến người nhận. Ngoài ra, mỗi router xem thông điệp này (gọi là thông điệp PATH) khi nó đi qua, và xác định *đường ngược* sẽ dùng để gửi đặt trước từ người nhận về người gửi nhằm đặt trước tài nguyên tại mỗi router trên đường đi. Việc xây dựng cây multicast ban đầu được thực hiện bằng các cơ chế như mô tả ở chương khác.

Sau khi nhận được thông điệp PATH, người nhận gửi một đặt trước ngược lên cây multicast bằng thông điệp RESV. Thông điệp này chứa TSpec của người gửi và một RSpec mô tả yêu cầu của người nhận này. Mỗi router trên đường đi xem xét yêu cầu đặt trước và cố gắng phân bổ tài nguyên cần thiết để đáp ứng. Nếu đặt trước thành công, yêu cầu RESV được chuyển tiếp đến router tiếp theo. Nếu không, một thông điệp lỗi được trả về cho người nhận đã gửi yêu cầu. Nếu mọi việc suôn sẻ, đặt trước đúng sẽ được cài đặt tại mọi router giữa người gửi và người nhận. Miễn là người nhận muốn giữ đặt trước, nó sẽ gửi lại thông điệp RESV khoảng mỗi 30 giây.

Bây giờ chúng ta có thể thấy điều gì xảy ra khi router hoặc liên kết bị lỗi. Các giao thức định tuyến sẽ thích nghi với lỗi và tạo đường đi mới từ người gửi đến người nhận. Thông điệp PATH được gửi khoảng mỗi 30 giây, và có thể gửi sớm hơn nếu router phát hiện thay đổi bảng chuyển tiếp, nên thông điệp đầu tiên sau khi đường đi mới ổn định sẽ đến người nhận qua đường mới. Thông điệp RESV tiếp theo của người nhận sẽ theo đường mới và, nếu mọi việc suôn sẻ, sẽ đặt trước tài nguyên mới trên đường mới. Trong khi đó, các router không còn trên đường đi sẽ không nhận được thông điệp RESV nữa, và các đặt trước này sẽ hết hạn và được giải phóng. Như vậy, RSVP xử lý khá tốt các thay đổi về cấu trúc mạng, miễn là thay đổi định tuyến không quá thường xuyên.

.. _fig-pathmsg:
.. figure:: figures/f06-25-9780123850591.png
   :width: 500px
   :align: center

   Đặt trước tài nguyên trên cây multicast.

Tiếp theo, chúng ta cần xem xét cách xử lý multicast, nơi có thể có nhiều người gửi đến một nhóm và nhiều người nhận. Tình huống này được minh họa ở :numref:`Hình %s <fig-pathmsg>`. Đầu tiên, xét trường hợp nhiều người nhận cho một người gửi. Khi một thông điệp RESV đi lên cây multicast, nó có thể gặp một đoạn cây nơi đặt trước của người nhận khác đã được thiết lập. Có thể tài nguyên đặt trước ở phía trên điểm này đủ để phục vụ cả hai người nhận. Ví dụ, nếu người nhận A đã đặt trước đảm bảo độ trễ dưới 100 ms, và yêu cầu mới từ người nhận B là độ trễ dưới 200 ms, thì không cần đặt trước mới. Ngược lại, nếu yêu cầu mới là độ trễ dưới 50 ms, router cần kiểm tra xem có thể đáp ứng không; nếu có, sẽ gửi yêu cầu lên phía trên. Lần tiếp theo người nhận A yêu cầu độ trễ tối thiểu 100 ms, router không cần chuyển tiếp yêu cầu này. Nói chung, các đặt trước có thể được gộp lại theo cách này để đáp ứng nhu cầu của tất cả người nhận phía dưới điểm gộp.

Nếu cũng có nhiều người gửi trong cây, người nhận cần thu thập TSpec từ tất cả người gửi và đặt trước đủ lớn để chứa lưu lượng từ tất cả người gửi. Tuy nhiên, điều này không có nghĩa là phải cộng tất cả TSpec lại. Ví dụ, trong một hội nghị âm thanh với 10 người nói, không cần thiết phải phân bổ đủ tài nguyên cho 10 luồng âm thanh, vì nếu 10 người nói cùng lúc thì kết quả sẽ không thể nghe được. Do đó, có thể đặt trước đủ lớn cho hai người nói là đủ. Việc tính toán TSpec tổng hợp đúng từ tất cả TSpec của người gửi rõ ràng là phụ thuộc vào ứng dụng. Ngoài ra, có thể chỉ quan tâm đến một tập con người nói; RSVP có các kiểu đặt trước khác nhau để xử lý các lựa chọn như “Đặt trước cho tất cả người nói”, “Đặt trước cho bất kỳ :math:`n` người nói”, và “Đặt trước cho người nói A và B”.

Phân loại và lập lịch gói
~~~~~~~~~~~~~~~~~~~~~~~~~

Khi chúng ta đã mô tả lưu lượng và dịch vụ mạng mong muốn, đã cài đặt đặt trước phù hợp tại tất cả router trên đường đi, việc còn lại là các router thực sự cung cấp dịch vụ yêu cầu cho các gói dữ liệu. Có hai việc cần làm:

-  Gán mỗi gói với đặt trước phù hợp để xử lý đúng, gọi là *phân loại* (classifying) gói.

-  Quản lý các gói trong hàng đợi để nhận được dịch vụ đã yêu cầu, gọi là *lập lịch* (scheduling) gói.

Phần đầu tiên được thực hiện bằng cách kiểm tra tối đa năm trường trong gói: địa chỉ nguồn, địa chỉ đích, số hiệu giao thức, cổng nguồn và cổng đích. (Trong IPv6, trường ``FlowLabel`` trong tiêu đề có thể được dùng để tra cứu dựa trên một khóa ngắn hơn.) Dựa trên thông tin này, gói có thể được xếp vào lớp phù hợp. Ví dụ, nó có thể được phân loại vào lớp tải có kiểm soát, hoặc là một phần của luồng đảm bảo cần xử lý riêng biệt với các luồng đảm bảo khác. Nói ngắn gọn, có một ánh xạ từ thông tin đặc trưng luồng trong tiêu đề gói đến một mã lớp xác định cách gói được xử lý trong hàng đợi. Với luồng đảm bảo, có thể là ánh xạ một-một, còn với dịch vụ khác có thể là nhiều-một. Chi tiết phân loại liên quan chặt chẽ đến chi tiết quản lý hàng đợi.

Rõ ràng, một hàng đợi FIFO đơn giản trong router sẽ không đủ để cung cấp nhiều dịch vụ khác nhau và các mức độ trễ khác nhau trong mỗi dịch vụ. Một số kỷ luật quản lý hàng đợi tinh vi hơn đã được thảo luận ở phần trước, và có thể kết hợp một số trong số này trong router.

Chi tiết lập lịch gói lý tưởng không nên quy định trong mô hình dịch vụ. Thay vào đó, đây là lĩnh vực mà các nhà triển khai có thể sáng tạo để hiện thực hóa mô hình dịch vụ hiệu quả. Với dịch vụ đảm bảo, đã xác định rằng kỷ luật xếp hàng công bằng có trọng số, trong đó mỗi luồng có hàng đợi riêng với một phần băng thông nhất định, sẽ cung cấp giới hạn trễ đầu-cuối có thể tính toán được. Với tải có kiểm soát, có thể dùng các phương án đơn giản hơn. Một khả năng là coi toàn bộ lưu lượng tải có kiểm soát như một luồng tổng hợp (về mặt cơ chế lập lịch), với trọng số cho luồng này được đặt dựa trên tổng lưu lượng đã được chấp nhận trong lớp tải có kiểm soát. Vấn đề trở nên khó hơn khi xét rằng trong một router, nhiều dịch vụ khác nhau có thể được cung cấp đồng thời và mỗi dịch vụ có thể yêu cầu thuật toán lập lịch khác nhau. Do đó, cần một thuật toán quản lý hàng đợi tổng thể để quản lý tài nguyên giữa các dịch vụ khác nhau.

Vấn đề mở rộng quy mô
~~~~~~~~~~~~~~~~~~~~

Dù kiến trúc Dịch vụ tích hợp và RSVP là một cải tiến đáng kể so với mô hình best-effort của IP, nhiều nhà cung cấp dịch vụ Internet cảm thấy nó không phù hợp để triển khai. Lý do liên quan đến một trong những mục tiêu thiết kế cơ bản của IP: khả năng mở rộng. Trong mô hình best-effort, các router trong Internet lưu rất ít hoặc không lưu trạng thái về các luồng riêng lẻ đi qua. Do đó, khi Internet phát triển, điều duy nhất router cần làm để theo kịp là truyền nhiều bit hơn mỗi giây và xử lý bảng định tuyến lớn hơn, nhưng RSVP đặt ra khả năng mỗi luồng đi qua router có một đặt trước tương ứng. Để hiểu mức độ nghiêm trọng của vấn đề, giả sử mỗi luồng trên một liên kết OC-48 (2,5 Gbps) là một luồng âm thanh 64 kbps. Số luồng như vậy là

.. centered:: 2,5 × 10\ :sup:`9` / 64 × 10\ :sup:`3` = 39.000

Mỗi đặt trước đó cần một lượng trạng thái lưu trong bộ nhớ và làm mới định kỳ. Router cần phân loại, kiểm soát và xếp hàng cho từng luồng. Quyết định kiểm soát truy cập cần thực hiện mỗi khi một luồng yêu cầu đặt trước, và cần có cơ chế “đẩy lùi” người dùng (ví dụ, tính phí thẻ tín dụng) để họ không đặt trước tùy tiện với thời gian dài.

Những lo ngại về khả năng mở rộng này đã hạn chế việc triển khai rộng rãi IntServ. Vì lý do này, các cách tiếp cận khác không yêu cầu lưu trạng thái “theo luồng” nhiều như vậy đã được phát triển. Phần tiếp theo sẽ thảo luận về các cách tiếp cận này.

6.5.3 Dịch vụ phân biệt (EF, AF)
--------------------------------

Trong khi kiến trúc Dịch vụ tích hợp phân bổ tài nguyên cho từng luồng riêng lẻ, mô hình Dịch vụ phân biệt (Differentiated Services, thường gọi tắt là DiffServ) phân bổ tài nguyên cho một số ít lớp lưu lượng. Thực tế, một số cách tiếp cận DiffServ chỉ chia lưu lượng thành hai lớp. Đây là một cách tiếp cận hợp lý: Nếu bạn xem xét khó khăn mà các nhà vận hành mạng gặp phải chỉ để duy trì một Internet best-effort hoạt động trơn tru, thì việc bổ sung mô hình dịch vụ theo từng bước nhỏ là hợp lý.

Giả sử chúng ta quyết định nâng cao mô hình best-effort bằng cách thêm một lớp mới, gọi là “cao cấp” (premium). Rõ ràng, chúng ta cần một cách để xác định gói nào là cao cấp và gói nào là best-effort thông thường. Thay vì dùng một giao thức như RSVP để báo cho tất cả router biết một luồng đang gửi gói cao cấp, sẽ dễ dàng hơn nếu các gói tự nhận diện khi đến router. Điều này có thể thực hiện bằng cách dùng một bit trong tiêu đề gói—nếu bit đó là 1, gói là cao cấp; nếu là 0, gói là best-effort. Với ý tưởng này, có hai câu hỏi cần giải quyết:

-  Ai đặt bit cao cấp và trong trường hợp nào?

-  Router sẽ xử lý khác biệt thế nào khi thấy gói có bit này?

Có nhiều câu trả lời cho câu hỏi đầu tiên, nhưng cách phổ biến là đặt bit ở ranh giới quản trị. Ví dụ, router ở biên của mạng nhà cung cấp dịch vụ Internet có thể đặt bit cho các gói đến từ một giao diện kết nối với mạng của một công ty cụ thể. Nhà cung cấp dịch vụ Internet có thể làm vậy vì công ty đó đã trả tiền cho mức dịch vụ cao hơn best-effort. Cũng có thể không phải tất cả các gói đều được đánh dấu cao cấp; ví dụ, router có thể cấu hình để đánh dấu gói là cao cấp đến một tốc độ tối đa nào đó, các gói vượt quá sẽ là best-effort.

Giả sử các gói đã được đánh dấu, router gặp gói đánh dấu sẽ xử lý thế nào? Ở đây cũng có nhiều câu trả lời. Thực tế, IETF đã chuẩn hóa một tập các hành vi router áp dụng cho các gói đánh dấu. Chúng gọi là *hành vi từng bước* (per-hop behaviors, PHBs), nghĩa là xác định hành vi của từng router thay vì dịch vụ đầu-cuối. Vì có nhiều hành vi mới, cần nhiều hơn 1 bit trong tiêu đề gói để báo cho router biết áp dụng hành vi nào. IETF quyết định lấy byte ``TOS`` cũ trong tiêu đề IP, vốn ít được dùng, và định nghĩa lại. Sáu bit của byte này được dành cho các điểm mã DiffServ (DSCP), mỗi DSCP là một giá trị 6 bit xác định một PHB cụ thể áp dụng cho gói. (Hai bit còn lại dùng cho ECN.)

Chuyển tiếp ưu tiên (Expedited Forwarding, EF) PHB
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một trong những PHB đơn giản nhất là *chuyển tiếp ưu tiên* (expedited forwarding, EF). Các gói đánh dấu EF nên được router chuyển tiếp với độ trễ và mất mát tối thiểu. Cách duy nhất để router đảm bảo điều này cho tất cả gói EF là giới hạn nghiêm ngặt tốc độ gói EF đến router nhỏ hơn tốc độ router có thể chuyển tiếp gói EF. Ví dụ, một router với giao diện 100 Mbps cần đảm bảo tốc độ gói EF đến giao diện đó không vượt quá 100 Mbps. Có thể cũng muốn đảm bảo tốc độ này thấp hơn 100 Mbps để thỉnh thoảng còn thời gian gửi các gói khác như cập nhật định tuyến.

Việc giới hạn tốc độ gói EF được thực hiện bằng cách cấu hình các router ở biên miền quản trị để cho phép tốc độ tối đa nhất định của gói EF vào miền. Một cách đơn giản, dù bảo thủ, là đảm bảo tổng tốc độ tất cả gói EF vào miền nhỏ hơn băng thông của liên kết chậm nhất trong miền. Điều này đảm bảo rằng, ngay cả trong trường hợp xấu nhất khi tất cả gói EF hội tụ về liên kết chậm nhất, nó không bị quá tải và có thể cung cấp hành vi đúng.

Có nhiều chiến lược triển khai hành vi EF. Một là cho gói EF ưu tiên tuyệt đối so với tất cả gói khác. Một cách khác là thực hiện xếp hàng công bằng có trọng số giữa gói EF và các gói khác, với trọng số EF đủ cao để tất cả gói EF được chuyển nhanh. Cách này có ưu điểm hơn ưu tiên tuyệt đối: Các gói không phải EF vẫn được đảm bảo truy cập liên kết, ngay cả khi lưu lượng EF vượt mức. Điều này có thể khiến gói EF không nhận được hành vi đúng như quy định, nhưng cũng ngăn lưu lượng định tuyến thiết yếu bị loại khỏi mạng khi lưu lượng EF quá tải.

Chuyển tiếp đảm bảo (Assured Forwarding, AF) PHB
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*Chuyển tiếp đảm bảo* (assured forwarding, AF) PHB bắt nguồn từ cách tiếp cận gọi là *RED với In và Out* (RIO) hoặc RED có trọng số (Weighted RED), đều là các cải tiến của thuật toán RED cơ bản đã mô tả ở phần trước. :numref:`Hình %s <fig-rio-prob>` cho thấy cách RIO hoạt động; giống như RED, trục y là xác suất loại bỏ tăng khi độ dài hàng đợi trung bình tăng trên trục x. Nhưng bây giờ, với hai lớp lưu lượng, ta có hai đường xác suất loại bỏ riêng. RIO gọi hai lớp là “in” và “out” vì lý do sẽ rõ ngay sau đây. Vì đường “out” có ``MinThreshold`` thấp hơn đường “in”, nên khi tắc nghẽn nhẹ, chỉ các gói “out” bị loại bỏ bởi thuật toán RED. Nếu tắc nghẽn nghiêm trọng hơn, tỷ lệ loại bỏ gói “out” tăng, và khi độ dài hàng đợi trung bình vượt qua Min_in, RED bắt đầu loại bỏ cả gói “in”.

.. _fig-rio-prob:
.. figure:: figures/f06-26-9780123850591.png
   :width: 400px
   :align: center

   RED với xác suất loại bỏ In và Out.

Lý do gọi hai lớp gói là “in” và “out” xuất phát từ cách đánh dấu gói. Như đã nói, việc đánh dấu gói có thể thực hiện bởi router ở biên miền quản trị. Có thể coi router này là ranh giới giữa nhà cung cấp dịch vụ mạng và khách hàng. Khách hàng có thể là bất kỳ mạng nào khác—ví dụ, mạng của một công ty hoặc một nhà cung cấp dịch vụ khác. Khách hàng và nhà cung cấp thỏa thuận một hồ sơ dịch vụ đảm bảo (và có thể khách hàng trả tiền cho hồ sơ này). Hồ sơ có thể là “Khách hàng X được phép gửi tối đa :math:`y` Mbps lưu lượng đảm bảo”, hoặc phức tạp hơn. Dù hồ sơ thế nào, router biên có thể đánh dấu các gói đến từ khách hàng là “in” hoặc “out” theo hồ sơ. Trong ví dụ trên, miễn là khách hàng gửi dưới :math:`y` Mbps, tất cả gói sẽ được đánh dấu “in”, nhưng khi vượt quá tốc độ đó, các gói dư sẽ là “out”.

Kết hợp bộ đo hồ sơ ở biên và RIO trong tất cả router của nhà cung cấp dịch vụ nên cung cấp cho khách hàng sự đảm bảo cao (nhưng không phải tuyệt đối) rằng các gói trong hồ sơ sẽ được chuyển đi. Đặc biệt, nếu phần lớn các gói, bao gồm cả của khách hàng không trả thêm phí để có hồ sơ, là gói “out”, thì cơ chế RIO sẽ giữ tắc nghẽn đủ thấp để gói “in” hiếm khi bị loại bỏ. Rõ ràng, phải có đủ băng thông trong mạng để chỉ riêng gói “in” hiếm khi gây tắc nghẽn đến mức RIO bắt đầu loại bỏ gói “in”.

Giống như RED, hiệu quả của cơ chế như RIO phụ thuộc phần nào vào việc chọn tham số đúng, và có nhiều tham số hơn cho RIO. Chính xác cơ chế này hoạt động tốt thế nào trong mạng thực tế chưa rõ tại thời điểm viết sách này.

Một đặc điểm thú vị của RIO là nó không thay đổi thứ tự gói “in” và “out”. Ví dụ, nếu một kết nối TCP gửi gói qua bộ đo hồ sơ, và một số gói được đánh dấu “in” còn lại là “out”, các gói này sẽ nhận xác suất loại bỏ khác nhau trong hàng đợi router, nhưng sẽ được chuyển đến máy nhận theo đúng thứ tự gửi. Điều này quan trọng với hầu hết các triển khai TCP, vốn hoạt động tốt hơn khi gói đến đúng thứ tự, dù có thể xử lý sai thứ tự. Lưu ý rằng các cơ chế như fast retransmit có thể bị kích hoạt sai khi xảy ra sai thứ tự.

Ý tưởng RIO có thể tổng quát hóa để cung cấp nhiều hơn hai đường xác suất loại bỏ, và đây là ý tưởng đằng sau cách tiếp cận gọi là *RED có trọng số* (WRED). Trong trường hợp này, giá trị trường DSCP được dùng để chọn một trong nhiều đường xác suất loại bỏ, nhờ đó cung cấp nhiều lớp dịch vụ khác nhau.

Cách thứ ba để cung cấp Dịch vụ phân biệt là dùng giá trị DSCP để xác định gói sẽ vào hàng đợi nào trong bộ lập lịch xếp hàng công bằng có trọng số. Đơn giản nhất, có thể dùng một điểm mã để chỉ hàng đợi *best-effort* và một điểm mã khác cho hàng đợi *cao cấp* (premium). Sau đó cần chọn trọng số cho hàng đợi cao cấp sao cho gói cao cấp nhận dịch vụ tốt hơn gói best-effort. Điều này phụ thuộc vào tải gói cao cấp. Ví dụ, nếu cho hàng đợi cao cấp trọng số 1 và best-effort trọng số 4, điều này đảm bảo băng thông cho gói cao cấp là

.. centered:: B\ :sub:`cao cấp` = W\ :sub:`cao cấp` / (W\ :sub:`cao cấp`
              + W\ :sub:`best-effort`\ ) = 1/(1 + 4) = 0,2

Tức là, đã dành 20% băng thông cho gói cao cấp, nên nếu tải trung bình của lưu lượng cao cấp chỉ là 10% băng thông, thì lưu lượng cao cấp sẽ hoạt động như trên một mạng rất nhẹ tải và dịch vụ sẽ rất tốt. Đặc biệt, độ trễ của lớp cao cấp có thể giữ thấp, vì WFQ sẽ cố gắng truyền gói cao cấp ngay khi đến trong trường hợp này. Ngược lại, nếu tải lưu lượng cao cấp là 30%, nó sẽ hoạt động như một mạng tải nặng, và độ trễ cho gói cao cấp có thể rất cao—thậm chí còn tệ hơn gói best-effort. Do đó, cần biết tải và cẩn thận khi đặt trọng số cho loại dịch vụ này. Tuy nhiên, cách an toàn là đặt trọng số cho hàng đợi cao cấp bảo thủ. Nếu trọng số này cao hơn nhiều so với tải dự kiến, nó tạo ra biên an toàn và không ngăn lưu lượng best-effort sử dụng băng thông đã dành cho cao cấp nhưng không dùng hết.

Giống như WRED, có thể tổng quát hóa cách tiếp cận dựa trên WFQ này để cho phép nhiều hơn hai lớp đại diện bởi các điểm mã khác nhau. Hơn nữa, có thể kết hợp ý tưởng chọn hàng đợi với ưu tiên loại bỏ. Ví dụ, với 12 điểm mã có thể có bốn hàng đợi với trọng số khác nhau, mỗi hàng đợi có ba mức ưu tiên loại bỏ. Đây chính là cách IETF đã định nghĩa “dịch vụ đảm bảo”.

6.5.4 Điều khiển tắc nghẽn dựa trên phương trình
-----------------------------------------------

Chúng ta kết thúc phần bàn về QoS bằng cách quay lại điều khiển tắc nghẽn TCP, nhưng lần này trong bối cảnh ứng dụng thời gian thực. Nhớ rằng TCP điều chỉnh cửa sổ tắc nghẽn của máy gửi (và do đó tốc độ truyền) dựa trên các sự kiện ACK và timeout. Một điểm mạnh của cách tiếp cận này là không cần sự hợp tác từ router mạng; đây là chiến lược hoàn toàn dựa trên máy chủ. Chiến lược này bổ sung cho các cơ chế QoS đã bàn, vì (1) ứng dụng có thể dùng giải pháp dựa trên máy chủ mà không phụ thuộc vào hỗ trợ của router, và (2) ngay cả khi DiffServ được triển khai đầy đủ, vẫn có thể xảy ra hàng đợi router bị quá tải, và chúng ta muốn ứng dụng thời gian thực phản ứng hợp lý khi điều này xảy ra.

Dù muốn tận dụng thuật toán điều khiển tắc nghẽn của TCP, bản thân TCP không phù hợp cho ứng dụng thời gian thực. Một lý do là TCP là giao thức tin cậy, và ứng dụng thời gian thực thường không thể chịu được độ trễ do truyền lại. Tuy nhiên, nếu tách TCP khỏi cơ chế điều khiển tắc nghẽn, thêm điều khiển tắc nghẽn kiểu TCP vào một giao thức không tin cậy như UDP thì sao? Ứng dụng thời gian thực có thể sử dụng giao thức như vậy không?

Một mặt, đây là ý tưởng hấp dẫn vì nó khiến các luồng thời gian thực cạnh tranh công bằng với luồng TCP. Ngược lại (như hiện nay), các ứng dụng video dùng UDP không có điều khiển tắc nghẽn, và do đó “cướp” băng thông của các luồng TCP vốn giảm tốc khi tắc nghẽn. Mặt khác, hành vi răng cưa của thuật toán điều khiển tắc nghẽn TCP không phù hợp với ứng dụng thời gian thực; nó khiến tốc độ truyền của ứng dụng liên tục tăng giảm. Trong khi đó, ứng dụng thời gian thực hoạt động tốt nhất khi duy trì tốc độ truyền ổn định trong thời gian dài.

Có thể đạt được cả hai mục tiêu: tương thích với điều khiển tắc nghẽn TCP để đảm bảo công bằng, đồng thời duy trì tốc độ truyền ổn định cho ứng dụng? Các nghiên cứu gần đây cho thấy câu trả lời là có. Cụ thể, một số thuật toán điều khiển tắc nghẽn “thân thiện với TCP” đã được đề xuất. Các thuật toán này có hai mục tiêu chính. Một là điều chỉnh cửa sổ tắc nghẽn chậm. Điều này thực hiện bằng cách điều chỉnh theo các khoảng thời gian dài hơn (ví dụ, một RTT) thay vì từng gói. Điều này làm mượt tốc độ truyền. Thứ hai là thân thiện với TCP theo nghĩa công bằng với các luồng TCP cạnh tranh. Tính chất này thường được đảm bảo bằng cách đảm bảo hành vi của luồng tuân theo một phương trình mô hình hóa hành vi TCP. Do đó, cách tiếp cận này đôi khi gọi là *điều khiển tắc nghẽn dựa trên phương trình* (equation-based congestion control).

Chúng ta đã thấy dạng đơn giản của phương trình tốc độ TCP ở phần trước. Ở đây, chỉ cần lưu ý rằng phương trình có dạng tổng quát:

.. math::

   Rate \propto \left(\frac{1}{RTT \times \sqrt{\rho}}\right)

nghĩa là để thân thiện với TCP, tốc độ truyền phải tỉ lệ nghịch với thời gian khứ hồi (RTT) và căn bậc hai của tỷ lệ mất gói (:math:`\rho`). Nói cách khác, để xây dựng cơ chế điều khiển tắc nghẽn dựa trên mối quan hệ này, máy nhận phải định kỳ báo cáo tỷ lệ mất gói cho máy gửi (ví dụ, có thể báo không nhận được 10% trong 100 gói gần nhất), và máy gửi điều chỉnh tốc độ truyền lên hoặc xuống sao cho mối quan hệ này luôn đúng. Tất nhiên, ứng dụng vẫn phải thích nghi với thay đổi tốc độ khả dụng, nhưng như sẽ thấy ở chương sau, nhiều ứng dụng thời gian thực khá thích nghi.
