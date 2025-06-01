9.1 Ứng dụng truyền thống
=========================

Chúng ta bắt đầu thảo luận về các ứng dụng bằng cách tập trung vào hai ứng dụng phổ biến nhất—World Wide Web và email. Nói chung, cả hai ứng dụng này đều sử dụng mô hình yêu cầu/đáp ứng—người dùng gửi yêu cầu tới máy chủ, sau đó máy chủ phản hồi tương ứng. Chúng tôi gọi đây là các ứng dụng “truyền thống” vì chúng tiêu biểu cho loại ứng dụng đã tồn tại từ những ngày đầu của mạng máy tính (mặc dù Web mới hơn nhiều so với email nhưng có nguồn gốc từ các phương thức truyền tệp có trước nó). Ngược lại, các phần sau sẽ xem xét một lớp ứng dụng đã trở nên phổ biến gần đây hơn: các ứng dụng streaming (ví dụ, các ứng dụng đa phương tiện như video và âm thanh) và các ứng dụng dựa trên overlay khác nhau. (Lưu ý rằng có một chút mờ ranh giới giữa các lớp này, vì ngày càng nhiều dữ liệu đa phương tiện streaming được truy cập qua Web, nhưng hiện tại chúng ta sẽ tập trung vào cách sử dụng Web để yêu cầu trang, hình ảnh, v.v.)

Trước khi xem xét kỹ từng ứng dụng này, có ba điểm chung mà chúng ta cần đề cập. Thứ nhất, điều quan trọng là phải phân biệt giữa *chương trình* ứng dụng và *giao thức* ứng dụng. Ví dụ, HyperText Transport Protocol (HTTP) là một giao thức ứng dụng được sử dụng để lấy các trang Web từ các máy chủ từ xa. Nhiều chương trình ứng dụng khác nhau—tức là các trình duyệt web như Internet Explorer, Chrome, Firefox và Safari—mang lại cho người dùng giao diện và trải nghiệm khác nhau, nhưng tất cả đều sử dụng cùng một giao thức HTTP để giao tiếp với các máy chủ web qua Internet. Thực tế là giao thức này được công bố và chuẩn hóa cho phép các chương trình ứng dụng do nhiều công ty và cá nhân khác nhau phát triển có thể tương tác với nhau. Đó là lý do tại sao rất nhiều trình duyệt có thể tương tác với tất cả các máy chủ web (vốn cũng có nhiều loại khác nhau).

Phần này xem xét hai giao thức ứng dụng được sử dụng rộng rãi và chuẩn hóa:

-  Simple Mail Transfer Protocol (SMTP) được sử dụng để trao đổi thư điện tử.

-  HyperText Transport Protocol (HTTP) được sử dụng để giao tiếp giữa trình duyệt web và máy chủ web.

Thứ hai, chúng ta nhận thấy rằng nhiều giao thức tầng ứng dụng, bao gồm HTTP và SMTP, có một giao thức đi kèm quy định định dạng dữ liệu có thể trao đổi. Đây là một lý do tại sao các giao thức này tương đối đơn giản: phần lớn độ phức tạp được xử lý trong tiêu chuẩn đi kèm này. Ví dụ, SMTP là một giao thức để trao đổi các thông điệp thư điện tử, nhưng RFC 822 và Multipurpose Internet Mail Extensions (MIME) định nghĩa định dạng của các thông điệp email. Tương tự, HTTP là một giao thức để lấy các trang Web, nhưng HyperText Markup Language (HTML) là một đặc tả đi kèm quy định hình thức cơ bản của các trang đó.

Cuối cùng, vì các giao thức ứng dụng được mô tả trong phần này đều tuân theo cùng một mô hình giao tiếp yêu cầu/đáp ứng, bạn có thể mong đợi rằng chúng sẽ được xây dựng trên một giao thức vận chuyển Remote Procedure Call (RPC). Tuy nhiên, thực tế không phải vậy, mà chúng được triển khai trên TCP [#]_. Thực chất, mỗi giao thức này tự xây dựng một cơ chế giống RPC đơn giản trên một giao thức vận chuyển tin cậy (TCP). Chúng tôi nói “đơn giản” vì mỗi giao thức không được thiết kế để hỗ trợ các lời gọi thủ tục từ xa tùy ý như đã thảo luận ở chương trước, mà thay vào đó được thiết kế để gửi và phản hồi một tập hợp cụ thể các thông điệp yêu cầu. Thú vị là, cách tiếp cận của HTTP đã chứng tỏ rất mạnh mẽ, dẫn đến việc nó được áp dụng rộng rãi trong kiến trúc *Web Services*, với các cơ chế RPC tổng quát được xây dựng *trên HTTP* thay vì ngược lại. Sẽ nói thêm về chủ đề này ở cuối phần này.

.. [#] Điều này bắt đầu thay đổi với Web khi có HTTP/3 như sẽ được thảo luận ở phần sau của chương.

9.1.1 Thư điện tử (SMTP, MIME, IMAP)
------------------------------------

Email là một trong những ứng dụng mạng lâu đời nhất. Rốt cuộc, còn gì tự nhiên hơn việc muốn gửi một thông điệp tới người dùng ở đầu bên kia của một đường truyền xuyên quốc gia mà bạn vừa thiết lập xong? Đó là phiên bản thế kỷ 20 của “*Ông Watson, lại đây… Tôi muốn gặp ông.*” Thật ngạc nhiên, những người tiên phong của ARPANET thực ra không hình dung email là một ứng dụng chủ chốt khi mạng được tạo ra—mục tiêu thiết kế chính là truy cập từ xa vào tài nguyên tính toán—nhưng cuối cùng nó lại trở thành “killer app” đầu tiên của Internet.

Như đã lưu ý ở trên, điều quan trọng là (1) phân biệt giao diện người dùng (tức là chương trình đọc thư của bạn) với các giao thức truyền thông điệp bên dưới (như SMTP hoặc IMAP), và (2) phân biệt giữa giao thức truyền này với một tiêu chuẩn đi kèm (RFC 822 và MIME) định nghĩa định dạng của các thông điệp được trao đổi. Chúng ta bắt đầu bằng cách xem xét định dạng thông điệp.

Định dạng thông điệp
~~~~~~~~~~~~~~~~~~~

RFC 822 định nghĩa thông điệp gồm hai phần: *header* (phần đầu) và *body* (phần thân). Cả hai phần đều được biểu diễn dưới dạng văn bản ASCII. Ban đầu, phần thân được giả định là văn bản thuần túy. Điều này vẫn đúng, mặc dù RFC 822 đã được mở rộng bởi MIME để cho phép phần thân mang mọi loại dữ liệu. Dữ liệu này vẫn được biểu diễn dưới dạng văn bản ASCII, nhưng vì nó có thể là phiên bản mã hóa của, ví dụ, một hình ảnh JPEG, nên không nhất thiết phải đọc được bằng mắt thường. Sẽ nói thêm về MIME ở phần sau.

Phần header của thông điệp là một chuỗi các dòng kết thúc bằng ``<CRLF>``. (``<CRLF>`` là viết tắt của carriage-return cộng line-feed, là một cặp ký tự điều khiển ASCII thường dùng để chỉ kết thúc một dòng văn bản.) Phần header được tách biệt với phần thân thông điệp bằng một dòng trống. Mỗi dòng header chứa một loại và một giá trị, được phân tách bằng dấu hai chấm. Nhiều dòng header này quen thuộc với người dùng, vì họ được yêu cầu điền chúng khi soạn email; ví dụ, header xác định người nhận thông điệp, và header nói điều gì đó về mục đích của thông điệp. Các header khác được hệ thống chuyển thư bên dưới tự động điền vào. Ví dụ như (khi nào thông điệp được gửi), (người dùng nào đã gửi thông điệp), và (mỗi máy chủ thư đã xử lý thông điệp này). Tất nhiên còn nhiều dòng header khác; bạn đọc quan tâm có thể tham khảo RFC 822.

RFC 822 đã được mở rộng vào năm 1993 (và được cập nhật khá nhiều lần kể từ đó) để cho phép thông điệp email mang nhiều loại dữ liệu khác nhau: âm thanh, video, hình ảnh, tài liệu PDF, v.v. MIME gồm ba phần cơ bản. Phần đầu tiên là một tập hợp các dòng header bổ sung cho tập gốc do RFC 822 định nghĩa. Các dòng header này mô tả, theo nhiều cách khác nhau, dữ liệu được mang trong phần thân thông điệp. Chúng bao gồm (phiên bản MIME đang được sử dụng), (mô tả dễ đọc về nội dung thông điệp, tương tự dòng Subject), (loại dữ liệu chứa trong thông điệp), và (cách dữ liệu trong phần thân được mã hóa).

Phần thứ hai là các định nghĩa cho một tập hợp các loại nội dung (và các loại phụ). Ví dụ, MIME định nghĩa một số loại hình ảnh khác nhau, bao gồm ``image/gif`` và ``image/jpeg``, mỗi loại có ý nghĩa rõ ràng. Một ví dụ khác, ``text/plain`` chỉ văn bản thuần túy mà bạn có thể thấy trong một thông điệp kiểu 822, trong khi ``text/richtext`` biểu thị một thông điệp chứa văn bản “được đánh dấu” (văn bản có phông chữ đặc biệt, in nghiêng, v.v.). Một ví dụ thứ ba, MIME định nghĩa loại ``application``, trong đó các loại phụ tương ứng với đầu ra của các chương trình ứng dụng khác nhau (ví dụ, ``application/postscript`` và ``application/msword``).

MIME cũng định nghĩa loại ``multipart`` cho biết cách cấu trúc một thông điệp mang nhiều loại dữ liệu. Điều này giống như một ngôn ngữ lập trình định nghĩa cả kiểu cơ bản (ví dụ, số nguyên và số thực) và kiểu phức hợp (ví dụ, cấu trúc và mảng). Một loại phụ ``multipart`` có thể là ``mixed``, cho biết thông điệp chứa một tập hợp các phần dữ liệu độc lập theo thứ tự xác định. Mỗi phần sau đó có dòng header riêng mô tả loại của phần đó.

Phần thứ ba là một cách để mã hóa các loại dữ liệu khác nhau để chúng có thể được gửi trong thông điệp email ASCII. Vấn đề là, với một số loại dữ liệu (ví dụ, hình ảnh JPEG), bất kỳ byte 8 bit nào trong hình ảnh có thể chứa một trong 256 giá trị khác nhau. Chỉ một tập con các giá trị này là ký tự ASCII hợp lệ. Điều quan trọng là thông điệp email chỉ chứa ASCII, vì chúng có thể đi qua nhiều hệ thống trung gian (gateway, như mô tả bên dưới) giả định mọi email đều là ASCII và sẽ làm hỏng thông điệp nếu nó chứa ký tự không phải ASCII. Để giải quyết vấn đề này, MIME sử dụng một phương pháp mã hóa đơn giản dữ liệu nhị phân thành bộ ký tự ASCII. Phương pháp mã hóa này gọi là ``base64``. Ý tưởng là ánh xạ mỗi ba byte dữ liệu nhị phân gốc thành bốn ký tự ASCII. Điều này được thực hiện bằng cách nhóm dữ liệu nhị phân thành các đơn vị 24 bit và chia mỗi đơn vị thành bốn phần 6 bit. Mỗi phần 6 bit ánh xạ tới một trong 64 ký tự ASCII hợp lệ; ví dụ, 0 ánh xạ tới A, 1 tới B, v.v. Nếu bạn nhìn vào một thông điệp đã được mã hóa bằng base64, bạn sẽ chỉ thấy 52 chữ cái hoa và thường, 10 chữ số từ 0 đến 9, và các ký tự đặc biệt + và /. Đây là 64 giá trị đầu tiên trong bộ ký tự ASCII.

Ngoài ra, để việc đọc thư dễ dàng nhất có thể cho những ai vẫn dùng trình đọc thư chỉ hỗ trợ văn bản, một thông điệp MIME chỉ chứa văn bản thuần túy có thể được mã hóa bằng ASCII 7 bit. Cũng có một phương pháp mã hóa dễ đọc cho dữ liệu chủ yếu là ASCII.

Kết hợp tất cả lại, một thông điệp chứa một đoạn văn bản thuần, một hình JPEG, và một tệp PostScript sẽ trông như sau:

::

   MIME-Version: 1.0
   Content-Type: multipart/mixed;
   boundary="-------417CA6E2DE4ABCAFBC5"
   From: Alice Smith <Alice@systemsapproach.org>
   To: Bob@cs.Princeton.edu
   Subject: promised material
   Date: Mon, 07 Sep 1998 19:45:19 -0400

   ---------417CA6E2DE4ABCAFBC5
   Content-Type: text/plain; charset=us-ascii
   Content-Transfer-Encoding: 7bit

   Bob,

   Here are the jpeg image and draft report I promised.

   --Alice

   ---------417CA6E2DE4ABCAFBC5
   Content-Type: image/jpeg
   Content-Transfer-Encoding: base64
   ... unreadable encoding of a jpeg figure
   ---------417CA6E2DE4ABCAFBC5
   Content-Type: application/postscript; name="draft.ps"
   Content-Transfer-Encoding: 7bit
   ... readable encoding of a PostScript document

Trong ví dụ này, dòng trong phần header cho biết thông điệp này chứa nhiều phần, mỗi phần được đánh dấu bằng một chuỗi ký tự không xuất hiện trong dữ liệu thực tế. Mỗi phần sau đó có dòng ``Content-Type`` và ``Content-Transfer-Encoding`` riêng.

Truyền thông điệp
~~~~~~~~~~~~~~~~~

Trong nhiều năm, phần lớn email được chuyển từ máy này sang máy khác chỉ bằng SMTP. Mặc dù SMTP vẫn giữ vai trò trung tâm, hiện nay nó chỉ là một trong số các giao thức email, với Internet Message Access Protocol (IMAP) và Post Office Protocol (POP) là hai giao thức quan trọng khác để lấy thư. Chúng ta sẽ bắt đầu bằng cách xem xét SMTP, rồi chuyển sang IMAP ở phần dưới.

Để đặt SMTP vào đúng ngữ cảnh, chúng ta cần xác định các thành phần chính. Đầu tiên, người dùng tương tác với một *mail reader* (chương trình đọc thư) khi họ soạn, lưu trữ, tìm kiếm và đọc email. Có vô số chương trình đọc thư, cũng như có nhiều trình duyệt web để lựa chọn. Thời kỳ đầu của Internet, người dùng thường đăng nhập vào máy chứa *hộp thư* của mình, và chương trình đọc thư mà họ sử dụng là một ứng dụng cục bộ trích xuất thông điệp từ hệ thống tệp. Ngày nay, tất nhiên, người dùng truy cập hộp thư từ xa bằng laptop hoặc điện thoại; họ không cần đăng nhập vào máy chủ lưu trữ thư (mail server). Một giao thức truyền thư thứ hai, như POP hoặc IMAP, được dùng để tải email từ máy chủ thư về thiết bị của người dùng.

Thứ hai, có một *mail daemon* (hoặc tiến trình) chạy trên mỗi máy chủ chứa hộp thư. Bạn có thể coi tiến trình này, còn gọi là *message transfer agent* (MTA), như đóng vai trò bưu điện: Người dùng (hoặc chương trình đọc thư của họ) gửi cho daemon các thông điệp muốn gửi đi, daemon sử dụng SMTP chạy trên TCP để truyền thông điệp tới một daemon trên máy khác, và daemon này đặt các thông điệp nhận được vào hộp thư của người dùng (để chương trình đọc thư có thể tìm thấy sau đó). Vì SMTP là một giao thức mà bất kỳ ai cũng có thể hiện thực, về lý thuyết có thể có nhiều hiện thực khác nhau của mail daemon. Tuy nhiên, thực tế chỉ có một vài hiện thực phổ biến, với chương trình ``sendmail`` của Berkeley Unix và ``postfix`` là phổ biến nhất.

.. _fig-mail:
.. figure:: figures/f09-01-9780123850591.png
   :width: 600px
   :align: center

   Chuỗi các gateway thư lưu trữ và chuyển tiếp thông điệp email.

Mặc dù hoàn toàn có thể MTA trên máy gửi thiết lập một kết nối SMTP/TCP tới MTA trên máy chủ nhận, trong nhiều trường hợp thư sẽ đi qua một hoặc nhiều *mail gateway* trên đường từ máy gửi tới máy nhận. Cũng như các máy đầu cuối, các gateway này cũng chạy một tiến trình message transfer agent. Không phải ngẫu nhiên mà các nút trung gian này được gọi là *gateway* vì nhiệm vụ của chúng là lưu trữ và chuyển tiếp thông điệp email, giống như một “IP gateway” (mà chúng ta gọi là *router*) lưu trữ và chuyển tiếp các datagram IP. Khác biệt duy nhất là một mail gateway thường lưu thông điệp trên đĩa và sẵn sàng thử gửi lại cho máy tiếp theo trong nhiều ngày, trong khi một router IP lưu datagram trong bộ nhớ và chỉ thử gửi lại trong một phần nhỏ của giây. :numref:`Figure %s <fig-mail>` minh họa đường đi hai bước từ người gửi tới người nhận.

Bạn có thể hỏi, tại sao cần các mail gateway? Tại sao máy gửi không gửi trực tiếp tới máy nhận? Một lý do là người nhận không muốn địa chỉ email của mình chứa tên máy cụ thể mà họ đọc thư. Một lý do khác là vấn đề mở rộng: Trong các tổ chức lớn, thường có nhiều máy khác nhau lưu trữ *hộp thư* cho tổ chức. Ví dụ, thư gửi tới ``bob@cs.princeton.edu`` trước tiên được gửi tới một mail gateway của Khoa Khoa học Máy tính tại Princeton (tức là máy ``cs.princeton.edu``), rồi được chuyển tiếp—liên quan đến một kết nối thứ hai—tới máy cụ thể nơi Bob có hộp thư. Gateway chuyển tiếp duy trì một cơ sở dữ liệu ánh xạ người dùng tới máy chứa hộp thư của họ; người gửi không cần biết tên cụ thể này. (Danh sách các dòng header trong thông điệp sẽ giúp bạn truy vết các gateway mà một thông điệp đã đi qua.) Một lý do nữa, đặc biệt đúng trong những ngày đầu của email, là máy chủ chứa hộp thư của người dùng có thể không luôn hoạt động hoặc không thể truy cập, khi đó mail gateway sẽ giữ thông điệp cho đến khi có thể chuyển tiếp.

Bất kể có bao nhiêu mail gateway trên đường đi, một kết nối SMTP độc lập được sử dụng giữa mỗi cặp máy để chuyển thông điệp gần hơn tới người nhận. Mỗi phiên SMTP là một đối thoại giữa hai mail daemon, một bên đóng vai trò client, bên kia là server. Có thể nhiều thông điệp được truyền giữa hai máy trong một phiên. Vì RFC 822 định nghĩa thông điệp sử dụng ASCII làm biểu diễn cơ sở, không có gì ngạc nhiên khi SMTP cũng dựa trên ASCII. Điều này có nghĩa là một người dùng có thể giả vờ là chương trình client SMTP bằng cách gõ lệnh trực tiếp trên bàn phím.

SMTP dễ hiểu nhất qua một ví dụ đơn giản. Sau đây là một phiên trao đổi giữa máy gửi ``cs.princeton.edu`` và máy nhận ``systemsapproach.org``. Trong trường hợp này, người dùng Bob tại Princeton cố gắng gửi thư cho Alice và Tom tại Systems Approach. Một số dòng trống được thêm vào để dễ đọc hơn.

.. code-block:: shell

   HELO cs.princeton.edu
   250 Hello daemon@mail.cs.princeton.edu [128.12.169.24]

   MAIL FROM:<Bob@cs.princeton.edu>
   250 OK

   RCPT TO:<Alice@systemsapproach.org>
   250 OK

   RCPT TO:<Tom@systemsapproach.org>
   550 No such user here

   DATA
   354 Start mail input; end with <CRLF>.<CRLF>
   Blah blah blah...
   ...etc. etc. etc.
   <CRLF>.<CRLF>
   250 OK

   QUIT
   221 Closing connection

Như bạn thấy, SMTP bao gồm một chuỗi trao đổi giữa client và server. Trong mỗi trao đổi, client gửi một lệnh (ví dụ, ``QUIT``) và server phản hồi bằng một mã số (ví dụ, ``250``, ``550``, ``354``, ``221``). Server cũng trả về một giải thích dễ hiểu cho mã số đó (ví dụ, ``No such user here``). Trong ví dụ này, client đầu tiên xác định mình với server bằng lệnh ``HELO``. Nó cung cấp tên miền của mình làm đối số. Server xác minh tên này khớp với địa chỉ IP đang dùng cho kết nối TCP; bạn sẽ thấy server trả lại địa chỉ IP này cho client. Client sau đó hỏi server có chấp nhận thư cho hai người dùng khác nhau không; server trả lời “có” với một người và “không” với người kia. Sau đó, client gửi thông điệp, kết thúc bằng một dòng chỉ chứa dấu chấm (“.”). Cuối cùng, client kết thúc kết nối.

Tất nhiên còn nhiều lệnh và mã trả về khác. Ví dụ, server có thể trả lời lệnh ``RCPT`` của client bằng mã ``251``, cho biết người dùng không có hộp thư trên máy này, nhưng server hứa sẽ chuyển tiếp thông điệp tới một mail daemon khác. Nói cách khác, máy này đóng vai trò mail gateway. Một ví dụ khác, client có thể gửi lệnh ``VRFY`` để xác minh địa chỉ email của người dùng mà không cần gửi thông điệp thực sự.

Điểm đáng chú ý còn lại là các đối số cho lệnh ``MAIL`` và ``RCPT``; ví dụ, ``FROM:<Bob@cs.princeton.edu>`` và ``TO:<Alice@systemsapproach.org>``. Chúng trông rất giống các trường header 822, và ở một mức độ nào đó đúng là như vậy. Thực tế, mail daemon sẽ phân tích thông điệp để trích xuất thông tin cần thiết cho SMTP. Thông tin này tạo thành một *phong bì* (envelope) cho thông điệp. SMTP client sử dụng phong bì này để tham số hóa trao đổi với SMTP server. Một ghi chú lịch sử: Lý do ``sendmail`` trở nên phổ biến là vì không ai muốn tự hiện thực chức năng phân tích thông điệp này. Mặc dù ngày nay địa chỉ email trông khá đơn giản (ví dụ, ``Bob@cs.princeton.edu``), trước đây không phải lúc nào cũng vậy. Thời kỳ trước khi mọi người đều kết nối Internet, không hiếm gặp các địa chỉ dạng ``user%host@site!neighbor``.

Chương trình đọc thư
~~~~~~~~~~~~~~~~~~~

Bước cuối cùng là người dùng thực sự lấy các thông điệp từ hộp thư, đọc, trả lời, và có thể lưu lại để tham khảo sau này. Người dùng thực hiện tất cả các thao tác này bằng cách tương tác với chương trình đọc thư. Như đã đề cập, ban đầu chương trình này chỉ là một ứng dụng chạy trên cùng máy với hộp thư của người dùng, khi đó nó chỉ cần đọc và ghi file thực hiện hộp thư. Đây là trường hợp phổ biến trước thời laptop. Ngày nay, hầu hết người dùng truy cập hộp thư từ máy từ xa bằng một giao thức khác, như POP hoặc IMAP. Việc thảo luận giao diện người dùng của chương trình đọc thư nằm ngoài phạm vi cuốn sách này, nhưng chúng ta hoàn toàn có thể nói về giao thức truy cập. Ở đây, chúng ta xét IMAP.

IMAP giống SMTP ở nhiều điểm. Nó là giao thức client/server chạy trên TCP, trong đó client (chạy trên máy tính để bàn của người dùng) gửi lệnh dưới dạng các dòng văn bản ASCII kết thúc bằng ``<CRLF>``, và mail server (chạy trên máy duy trì hộp thư của người dùng) phản hồi tương tự. Trao đổi bắt đầu bằng việc client xác thực và xác định hộp thư muốn truy cập. Điều này có thể được biểu diễn bằng sơ đồ trạng thái đơn giản như ở :numref:`Figure %s <fig-imap>`. Trong sơ đồ này, ``LOGIN`` và ``LOGOUT`` là các lệnh ví dụ mà client có thể gửi, còn ``OK`` là một phản hồi có thể của server. Các lệnh phổ biến khác gồm ``FETCH`` và ``EXPUNGE``, với ý nghĩa rõ ràng. Các phản hồi bổ sung của server gồm ``NO`` (client không có quyền thực hiện thao tác đó) và ``BAD`` (lệnh không hợp lệ).

.. _fig-imap:
.. figure:: figures/f09-02-9780123850591.png
   :width: 400px
   :align: center

   Sơ đồ trạng thái IMAP.

Khi người dùng yêu cầu ``FETCH`` một thông điệp, server trả về thông điệp ở định dạng MIME và chương trình đọc thư sẽ giải mã nó. Ngoài bản thân thông điệp, IMAP còn định nghĩa một tập các *thuộc tính* thông điệp được trao đổi như một phần của các lệnh khác, độc lập với việc truyền thông điệp. Các thuộc tính này bao gồm thông tin như kích thước thông điệp và, thú vị hơn, các *cờ* (flags) khác nhau gắn với thông điệp (ví dụ, ``Seen``, ``Answered``, ``Deleted``, và ``Recent``). Các cờ này được dùng để giữ cho client và server đồng bộ; tức là, khi người dùng xóa một thông điệp trong chương trình đọc thư, client cần báo cho server biết. Sau này, nếu người dùng quyết định xóa vĩnh viễn các thông điệp đã xóa, client gửi lệnh ``EXPUNGE`` tới server, server sẽ thực sự loại bỏ các thông điệp đã xóa khỏi hộp thư.

Cuối cùng, lưu ý rằng khi người dùng trả lời một thông điệp, hoặc gửi thông điệp mới, chương trình đọc thư không chuyển tiếp thông điệp từ client tới mail server bằng IMAP, mà thay vào đó sử dụng SMTP. Điều này có nghĩa là mail server của người dùng thực chất là mail gateway đầu tiên trên đường từ máy tính cá nhân tới hộp thư của người nhận.

9.1.2 World Wide Web (HTTP)
---------------------------

World Wide Web đã thành công đến mức khiến Internet trở nên dễ tiếp cận với rất nhiều người, đến nỗi đôi khi người ta nghĩ Web đồng nghĩa với Internet. Thực tế, thiết kế của hệ thống trở thành Web bắt đầu vào khoảng năm 1989, lâu sau khi Internet đã trở thành một hệ thống được triển khai rộng rãi. Mục tiêu ban đầu của Web là tìm ra cách tổ chức và truy xuất thông tin, dựa trên các ý tưởng về siêu văn bản—các tài liệu liên kết với nhau—đã xuất hiện ít nhất từ những năm 1960.\ [#]_ Ý tưởng cốt lõi của siêu văn bản là một tài liệu có thể liên kết tới tài liệu khác, và giao thức (HTTP) cùng ngôn ngữ tài liệu (HTML) được thiết kế để đáp ứng mục tiêu đó.

.. [#] Một lịch sử ngắn gọn về Web do World Wide Web Consortium cung cấp truy nguồn gốc của nó tới một bài báo năm 1945 mô tả các liên kết giữa các tài liệu microfiche.

Một cách hữu ích để hình dung Web là tập hợp các client và server hợp tác, tất cả đều nói cùng một ngôn ngữ: HTTP. Hầu hết mọi người tiếp cận Web thông qua một chương trình client đồ họa hoặc trình duyệt web như Safari, Chrome, Firefox hoặc Internet Explorer. :numref:`Figure %s <fig-netscape>` cho thấy trình duyệt Safari đang được sử dụng, hiển thị một trang thông tin từ Đại học Princeton.

.. _fig-netscape:
.. figure:: figures/browser-screenshot.png
   :width: 600px
   :align: center

   Trình duyệt web Safari.

Nếu bạn muốn tổ chức thông tin thành một hệ thống các tài liệu hoặc đối tượng liên kết, cần có một cách để nhận diện các tài liệu nhằm liên kết tới chúng. Uniform Resource Locators (URL) đã trở nên quen thuộc với hầu hết chúng ta đến mức dễ quên rằng chúng chưa tồn tại từ lâu. Chúng cung cấp thông tin cho phép xác định vị trí các đối tượng trên Web, và có dạng như sau:

.. code-block:: html

   http://www.cs.princeton.edu/index.html

Nếu bạn mở URL này, trình duyệt web của bạn sẽ mở một kết nối TCP tới máy chủ web tại máy ``www.cs.princeton.edu`` và ngay lập tức lấy về và hiển thị tệp ``index.html``. Hầu hết các tệp trên Web chứa hình ảnh và văn bản, và nhiều tệp còn có các đối tượng khác như đoạn âm thanh, video, mã lệnh, v.v. Chúng cũng thường bao gồm các URL trỏ tới các tệp khác có thể nằm trên các máy khác, đây chính là cốt lõi của phần “siêu văn bản” trong HTTP và HTML. Trình duyệt web có cách để bạn nhận biết các URL (thường bằng cách làm nổi bật hoặc gạch chân một đoạn văn bản) và bạn có thể yêu cầu trình duyệt mở chúng. Các URL nhúng này là ví dụ về *liên kết siêu văn bản*. Khi bạn yêu cầu trình duyệt mở một URL nhúng (ví dụ, bằng cách nhấp chuột), nó sẽ lấy về và hiển thị tệp được chỉ định. Nhờ đó, bạn có thể dễ dàng di chuyển từ máy này sang máy khác trên mạng, theo các liên kết tới đủ loại thông tin. Khi bạn có phương tiện nhúng liên kết vào tài liệu và cho phép người dùng theo liên kết đó để lấy tài liệu khác, bạn đã có nền tảng của một hệ thống siêu văn bản.

Khi bạn yêu cầu trình duyệt xem một trang, trình duyệt (client) sẽ lấy trang từ server bằng HTTP, vốn truyền thống chạy trên TCP. Giống như SMTP, HTTP là một giao thức hướng văn bản. Ở cốt lõi, HTTP là một giao thức yêu cầu/đáp ứng, trong đó mỗi thông điệp có dạng tổng quát

::

   START_LINE <CRLF>
   MESSAGE_HEADER <CRLF>
   <CRLF>
   MESSAGE_BODY <CRLF>

trong đó, như trước, ``<CRLF>`` là carriage-return+line-feed. Dòng đầu tiên (``START_LINE``) cho biết đây là thông điệp yêu cầu hay đáp ứng. Thực chất, nó xác định “thủ tục từ xa” sẽ được thực thi (trong trường hợp thông điệp yêu cầu), hoặc *trạng thái* của yêu cầu (trong trường hợp thông điệp đáp ứng). Tập các dòng tiếp theo chỉ định một tập hợp các tùy chọn và tham số bổ sung cho yêu cầu hoặc đáp ứng. Có thể có không hoặc nhiều dòng ``MESSAGE_HEADER``—tập này kết thúc bằng một dòng trống—mỗi dòng giống như một dòng header trong email. HTTP định nghĩa nhiều loại header khác nhau, một số dành cho thông điệp yêu cầu, một số cho thông điệp đáp ứng, và một số cho dữ liệu mang trong phần thân thông điệp. Thay vì liệt kê đầy đủ tất cả các loại header, chúng ta chỉ đưa ra một số ví dụ tiêu biểu. Cuối cùng, sau dòng trống là nội dung thông điệp được yêu cầu (``MESSAGE_BODY``); phần này là nơi server đặt trang được yêu cầu khi đáp ứng, và thường để trống đối với thông điệp yêu cầu.

Tại sao HTTP được thiết kế để chạy trên TCP? Các nhà thiết kế không nhất thiết phải làm vậy, nhưng TCP cung cấp nhiều dịch vụ mà HTTP cần: truyền tin cậy (không ai muốn một trang web bị thiếu dữ liệu), điều khiển luồng và điều khiển tắc nghẽn. Tuy nhiên, như sẽ thấy bên dưới, có một số vấn đề phát sinh khi xây dựng giao thức yêu cầu/đáp ứng trên TCP, và những vấn đề này càng rõ ràng hơn khi xem xét chi tiết tương tác giữa các giao thức tầng ứng dụng và tầng vận chuyển. Điều này đã dẫn đến các phiên bản mới của HTTP và một giao thức vận chuyển mới, QUIC, sẽ được thảo luận bên dưới.

Thông điệp yêu cầu
~~~~~~~~~~~~~~~~~~

Dòng đầu tiên của một thông điệp yêu cầu HTTP chỉ định ba điều: thao tác sẽ thực hiện, trang web thao tác sẽ thực hiện trên đó, và phiên bản HTTP đang sử dụng. Mặc dù HTTP định nghĩa nhiều thao tác yêu cầu khác nhau—bao gồm cả thao tác *ghi* cho phép đăng một trang web lên server—hai thao tác phổ biến nhất là ``GET`` (lấy trang web được chỉ định) và ``HEAD`` (lấy thông tin trạng thái về trang web được chỉ định). ``GET`` rõ ràng được dùng khi trình duyệt muốn lấy và hiển thị một trang web. ``HEAD`` được dùng để kiểm tra tính hợp lệ của một liên kết siêu văn bản hoặc xem một trang cụ thể có bị thay đổi kể từ lần cuối trình duyệt lấy về hay không. Tập hợp đầy đủ các thao tác được tóm tắt ở :numref:`Table %s <tab-ops>`. Nghe có vẻ vô hại, nhưng lệnh ``POST`` lại là nguồn gốc của nhiều vấn đề (bao gồm cả spam) trên Internet.

.. _tab-ops:
.. table::  Các thao tác yêu cầu HTTP.
   :align: center
   :widths: auto

   +-----------+-----------------------------------------------------------+
   | Operation | Mô tả                                                    |
   +===========+===========================================================+
   | OPTIONS   | Yêu cầu thông tin về các tùy chọn có sẵn                  |
   +-----------+-----------------------------------------------------------+
   | GET       | Lấy tài liệu được xác định trong URL                      |
   +-----------+-----------------------------------------------------------+
   | HEAD      | Lấy thông tin về tài liệu được xác định trong URL         |
   +-----------+-----------------------------------------------------------+
   | POST      | Gửi thông tin (ví dụ, chú thích) tới server               |
   +-----------+-----------------------------------------------------------+
   | PUT       | Lưu tài liệu dưới URL chỉ định                            |
   +-----------+-----------------------------------------------------------+
   | DELETE    | Xóa URL chỉ định                                          |
   +-----------+-----------------------------------------------------------+
   | TRACE     | Yêu cầu lặp lại thông điệp                                |
   +-----------+-----------------------------------------------------------+
   | CONNECT   | Dùng cho proxy                                            |
   +-----------+-----------------------------------------------------------+

Ví dụ, dòng ``START_LINE``

::

   GET http://www.cs.princeton.edu/index.html HTTP/1.1

cho biết client muốn server trên máy đó trả về trang ``index.html``. Ví dụ này dùng URL tuyệt đối. Cũng có thể yêu cầu một đường dẫn trong ``START_LINE`` và chỉ định tên máy chủ trong một dòng ``MESSAGE_HEADER``; ví dụ,

.. code-block:: http

   GET /index.html HTTP/1.1
   Host: www.cs.princeton.edu

Ở đây, ``Host`` là một trong các trường ``MESSAGE_HEADER`` có thể có. Một trường thú vị khác là ``If-Modified-Since``, cho phép client yêu cầu có điều kiện một trang web—server chỉ trả về trang nếu nó đã bị thay đổi kể từ thời điểm được chỉ định trong dòng header đó.

Thông điệp đáp ứng
~~~~~~~~~~~~~~~~~~

Giống như thông điệp yêu cầu, thông điệp đáp ứng bắt đầu bằng một dòng ``START_LINE``. Trong trường hợp này, dòng này chỉ định phiên bản HTTP đang sử dụng, một mã ba chữ số cho biết yêu cầu có thành công hay không, và một chuỗi văn bản giải thích lý do phản hồi. Ví dụ, dòng ``START_LINE``

.. code-block:: http

   HTTP/1.1 202 Accepted

chỉ ra rằng server đã xử lý thành công yêu cầu, trong khi

.. code-block:: http

   HTTP/1.1 404 Not Found

chỉ ra rằng server không thể xử lý yêu cầu vì không tìm thấy trang. Có năm loại mã phản hồi tổng quát, với chữ số đầu tiên của mã cho biết loại. :numref:`Table %s <tab-codes>` tóm tắt năm loại mã này.

.. _tab-codes:
.. table::  Năm loại mã kết quả HTTP.
   :align: center
   :widths: auto

   +------+---------------+--------------------------------------------------------+
   | Mã   | Loại          | Ví dụ lý do                                            |
   +======+===============+========================================================+
   | 1xx  | Thông tin     | yêu cầu đã nhận, đang tiếp tục xử lý                   |
   +------+---------------+--------------------------------------------------------+
   | 2xx  | Thành công    | hành động đã nhận, hiểu và chấp nhận                   |
   +------+---------------+--------------------------------------------------------+
   | 3xx  | Chuyển hướng  | cần thực hiện thêm hành động để hoàn thành yêu cầu     |
   +------+---------------+--------------------------------------------------------+
   | 4xx  | Lỗi phía client | yêu cầu có cú pháp sai hoặc không thể thực hiện      |
   +------+---------------+--------------------------------------------------------+
   | 5xx  | Lỗi phía server | server không thực hiện được yêu cầu hợp lệ           |
   +------+---------------+--------------------------------------------------------+

Cũng như với các hậu quả bất ngờ của thông điệp yêu cầu ``POST``, đôi khi cũng bất ngờ khi các thông điệp đáp ứng khác nhau được sử dụng trong thực tế như thế nào. Ví dụ, chuyển hướng yêu cầu (cụ thể là mã 302) hóa ra là một cơ chế mạnh mẽ đóng vai trò lớn trong các mạng phân phối nội dung (CDN) bằng cách chuyển hướng yêu cầu tới bộ nhớ đệm gần nhất.

Tương tự như thông điệp yêu cầu, thông điệp đáp ứng có thể chứa một hoặc nhiều dòng ``MESSAGE_HEADER``. Các dòng này chuyển thêm thông tin về cho client. Ví dụ, dòng header ``Location`` chỉ ra rằng URL được yêu cầu hiện có ở vị trí khác. Do đó, nếu trang web của Khoa Khoa học Máy tính Princeton chuyển từ ``http://www.cs.princeton.edu/index.html`` sang ``http://www.princeton.edu/cs/index.html``, server tại địa chỉ cũ có thể phản hồi như sau:

.. code-block:: http

   HTTP/1.1 301 Moved Permanently
   Location: http://www.princeton.edu/cs/index.html

Trong trường hợp phổ biến, thông điệp đáp ứng cũng sẽ mang theo trang được yêu cầu. Trang này là một tài liệu HTML, nhưng vì nó có thể chứa dữ liệu phi văn bản (ví dụ, hình ảnh GIF), nên nó được mã hóa bằng MIME (xem phần trước). Một số dòng ``MESSAGE_HEADER`` cung cấp thuộc tính của nội dung trang, bao gồm (số byte trong nội dung), ``Expires`` (thời điểm nội dung bị coi là lỗi thời), và (thời điểm nội dung được sửa đổi lần cuối trên server).

Uniform Resource Identifiers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Các URL mà HTTP dùng làm địa chỉ là một loại *Uniform Resource Identifier* (URI). URI là một chuỗi ký tự nhận diện một tài nguyên, nơi tài nguyên có thể là bất cứ thứ gì có danh tính, như tài liệu, hình ảnh hoặc dịch vụ.

Định dạng của URI cho phép tích hợp nhiều loại định danh tài nguyên chuyên biệt vào không gian định danh URI. Phần đầu của URI là *scheme* chỉ ra cách nhận diện một loại tài nguyên cụ thể, ví dụ ``mailto`` cho địa chỉ email hoặc ``file`` cho tên tệp. Phần thứ hai của URI, tách biệt với phần đầu bằng dấu hai chấm, là *scheme-specific part*. Đây là định danh tài nguyên phù hợp với scheme ở phần đầu, như trong các URI ``mailto:santa@northpole.org`` và ``file:///C:/foo.html``.

Một tài nguyên không nhất thiết phải có thể truy xuất hoặc truy cập được. Chúng ta đã thấy một ví dụ về điều này ở chương trước—namespace của XML được nhận diện bằng URI trông rất giống URL, nhưng thực ra chúng không phải là *locator* vì chúng không cho biết cách xác định vị trí; chúng chỉ cung cấp một định danh toàn cục duy nhất cho namespace. Không có yêu cầu nào rằng bạn có thể truy xuất bất cứ thứ gì tại URI được chỉ định là target namespace của một tài liệu XML. Chúng ta sẽ thấy một ví dụ khác về URI không phải là URL ở phần sau.

Kết nối TCP
~~~~~~~~~~~

Phiên bản gốc của HTTP (1.0) thiết lập một kết nối TCP riêng cho mỗi mục dữ liệu được lấy từ server. Không khó để thấy đây là một cơ chế rất kém hiệu quả: các thông điệp thiết lập và đóng kết nối phải được trao đổi giữa client và server ngay cả khi client chỉ muốn kiểm tra xem mình đã có bản mới nhất của một trang hay chưa. Do đó, lấy một trang có chứa một số văn bản và một tá biểu tượng hoặc hình ảnh nhỏ khác sẽ dẫn đến việc thiết lập và đóng 13 kết nối TCP riêng biệt. :numref:`Figure %s <fig-oldhttp>` cho thấy trình tự các sự kiện khi lấy một trang chỉ có một đối tượng nhúng. Các đường màu biểu thị thông điệp TCP, còn các đường màu đen biểu thị các yêu cầu và đáp ứng HTTP. (Một số ACK của TCP không được hiển thị để tránh làm rối hình.) Bạn có thể thấy hai lần trễ vòng (round trip) được dùng để thiết lập kết nối TCP trong khi hai lần nữa (ít nhất) được dùng để lấy trang và hình ảnh. Ngoài tác động đến độ trễ, còn có chi phí xử lý trên server để xử lý việc thiết lập và đóng kết nối TCP bổ sung.

.. _fig-oldhttp:
.. figure:: figures/f09-04-9780123850591.png
   :width: 500px
   :align: center

   Hành vi HTTP 1.0.

Để khắc phục tình trạng này, HTTP phiên bản 1.1 đã giới thiệu *kết nối duy trì*—client và server có thể trao đổi nhiều thông điệp yêu cầu/đáp ứng trên cùng một kết nối TCP. Kết nối duy trì có nhiều lợi ích. Thứ nhất, chúng loại bỏ chi phí thiết lập kết nối, nhờ đó giảm tải cho server, giảm tải cho mạng do các gói TCP bổ sung, và giảm độ trễ cảm nhận của người dùng. Thứ hai, vì client có thể gửi nhiều thông điệp yêu cầu trên một kết nối TCP, cơ chế cửa sổ tắc nghẽn của TCP có thể hoạt động hiệu quả hơn. Điều này là vì không cần phải qua giai đoạn khởi động chậm cho mỗi trang. :numref:`Figure %s <fig-persist>` cho thấy giao dịch từ :numref:`Figure %s <fig-oldhttp>` sử dụng kết nối duy trì trong trường hợp kết nối đã mở sẵn (có thể do truy cập trước đó tới cùng server).

.. _fig-persist:
.. figure:: figures/f09-05-9780123850591.png
   :width: 500px
   :align: center

   Hành vi HTTP 1.1 với kết nối duy trì.

Tuy nhiên, kết nối duy trì không phải không có giá. Vấn đề là cả client lẫn server không nhất thiết biết nên giữ một kết nối TCP cụ thể mở trong bao lâu. Điều này đặc biệt quan trọng ở phía server, nơi có thể được yêu cầu giữ các kết nối mở thay mặt cho hàng nghìn client. Giải pháp là server phải đặt timeout và đóng kết nối nếu không nhận được yêu cầu nào trên kết nối trong một khoảng thời gian nhất định. Ngoài ra, cả client và server phải theo dõi xem phía bên kia có chủ động đóng kết nối hay không, và phải dùng thông tin đó làm tín hiệu để đóng phía mình. (Nhớ rằng cả hai phía phải đóng kết nối TCP trước khi nó thực sự kết thúc.) Lo ngại về sự phức tạp bổ sung này có thể là một lý do khiến kết nối duy trì không được sử dụng ngay từ đầu, nhưng ngày nay lợi ích của kết nối duy trì đã được thừa nhận là vượt trội so với nhược điểm.

Mặc dù 1.1 vẫn được hỗ trợ rộng rãi, phiên bản 2.0 đã được IETF phê duyệt chính thức vào năm 2015. Được gọi là HTTP/2, phiên bản mới này tương thích ngược với 1.1 (tức là nó giữ nguyên cú pháp trường header, mã trạng thái và URI), nhưng bổ sung hai tính năng mới.

Đầu tiên là giúp server web dễ dàng *nén nhỏ* thông tin gửi lại cho trình duyệt web. Nếu bạn xem kỹ cấu trúc HTML của một trang web điển hình, bạn sẽ thấy rất nhiều tham chiếu tới các thành phần nhỏ khác (ví dụ, hình ảnh, script, file style) mà trình duyệt cần để dựng trang. Thay vì buộc client phải yêu cầu các thành phần này (kỹ thuật gọi là *resource*), HTTP/2 cung cấp cơ chế cho server gói các resource cần thiết và chủ động *đẩy* chúng tới client mà không phải chờ client yêu cầu, nhờ đó giảm độ trễ do phải chờ round-trip. Tính năng này đi kèm với cơ chế nén giúp giảm số byte cần truyền. Mục tiêu là tối thiểu hóa độ trễ mà người dùng cuối cảm nhận từ lúc nhấp vào liên kết cho đến khi trang được dựng xong.

Tiến bộ lớn thứ hai của HTTP/2 là cho phép ghép kênh nhiều yêu cầu trên một kết nối TCP. Điều này vượt xa những gì phiên bản 1.1 hỗ trợ—cho phép một *chuỗi* yêu cầu dùng lại một kết nối TCP—bằng cách cho phép các yêu cầu này chồng lấn lên nhau. Cách HTTP/2 làm điều này nghe có vẻ quen thuộc: nó định nghĩa một trừu tượng *kênh* (về mặt kỹ thuật, các kênh gọi là *stream*), cho phép nhiều stream đồng thời hoạt động cùng lúc (mỗi stream có một *stream id* duy nhất), và giới hạn mỗi stream chỉ có một trao đổi yêu cầu/đáp ứng đang hoạt động tại một thời điểm.

HTTP/3 và QUIC
~~~~~~~~~~~~~~

Như phần thảo luận trước cho thấy, lịch sử của HTTP là một chuỗi các thay đổi dần dần để tận dụng tốt hơn TCP làm giao thức vận chuyển bên dưới. Nhưng có một vấn đề cơ bản không thể giải quyết triệt để: TCP cung cấp trừu tượng luồng byte, trong khi HTTP là giao thức yêu cầu/đáp ứng. Giải pháp tự nhiên là dùng một giao thức vận chuyển phù hợp hơn, nhưng như đã thấy ở :ref:`Chương 5 <Chapter 5: End-to-End Protocols>`, không có giao thức RPC chuẩn nào được chấp nhận rộng rãi như TCP.

Cuối cùng, giải pháp cho sự không tương thích này là tạo ra một giao thức vận chuyển mới là QUIC. QUIC được thiết kế rõ ràng để phù hợp với yêu cầu của HTTP, và HTTP/3 tận dụng giao thức vận chuyển cải tiến này. Ví dụ, QUIC hỗ trợ ghép kênh stream ngay ở tầng vận chuyển. Do đó, một gói tin bị mất chỉ ảnh hưởng đến stream gặp lỗi, thay vì làm dừng toàn bộ kết nối TCP trong khi chờ truyền lại gói bị mất. Đồng thời, gói bị mất đó vẫn cung cấp tín hiệu tắc nghẽn áp dụng cho tất cả các stream trong kết nối QUIC. Chúng ta sẽ tìm hiểu chi tiết hơn về QUIC ở :ref:`Mục 5.2 <5.2 Reliable Byte Stream (TCP)>`.

Một lợi thế quan trọng khác của QUIC so với TCP là cách nó xử lý các bước cần thiết để bảo mật kết nối HTTP. Trong khi trao đổi chứng chỉ và khóa mã hóa diễn ra sau khi thiết lập phiên TCP, QUIC xử lý các bước này như một phần của thiết lập phiên, giúp giảm đáng kể số vòng lặp cần thiết để thiết lập kết nối bảo mật trước khi truyền nội dung đầu tiên.

HTTP/3 đã được triển khai trong phần lớn các trình duyệt và đang được triển khai dần trên các server khắp Internet. Vẫn còn nhiều server chạy HTTP/2 và thậm chí cả HTTP/1.1, nên việc thương lượng phiên bản có thể sẽ là một phần của các hiện thực HTTP trong tương lai gần.

Bộ nhớ đệm (Caching)
~~~~~~~~~~~~~~~~~~~~

Một chiến lược hiện thực quan trọng giúp web dễ sử dụng hơn là lưu trữ các trang web vào bộ nhớ đệm (cache). Caching mang lại nhiều lợi ích. Từ góc độ client, một trang có thể lấy từ cache gần sẽ được hiển thị nhanh hơn nhiều so với việc phải lấy từ bên kia thế giới. Từ góc độ server, có một cache chặn và đáp ứng yêu cầu giúp giảm tải cho server.

Caching có thể được triển khai ở nhiều nơi khác nhau. Ví dụ, trình duyệt của người dùng có thể cache các trang vừa truy cập và chỉ cần hiển thị lại bản đã cache nếu người dùng truy cập lại trang đó. Một ví dụ khác, một site có thể hỗ trợ một cache dùng chung cho toàn site. Điều này cho phép người dùng tận dụng các trang đã được người khác tải về trước đó. Gần hơn về phía trung tâm Internet, các nhà cung cấp dịch vụ Internet (ISP) có thể cache các trang.\ [#]_ Lưu ý rằng, trong trường hợp thứ hai, người dùng trong site thường biết máy nào đang cache trang cho site và cấu hình trình duyệt để kết nối trực tiếp tới máy cache này. Nút này đôi khi được gọi là *proxy*. Ngược lại, các site kết nối tới ISP có thể không biết rằng ISP đang cache trang. Đơn giản là các yêu cầu HTTP từ các site khác nhau đi qua cùng một router ISP. Router này có thể kiểm tra thông điệp yêu cầu và xem URL của trang được yêu cầu. Nếu nó có trang trong cache, nó trả về luôn. Nếu không, nó chuyển tiếp yêu cầu tới server và quan sát phản hồi trả về. Khi có phản hồi, router lưu lại một bản với hy vọng có thể dùng để đáp ứng yêu cầu sau này.

.. [#] Có khá nhiều vấn đề với kiểu caching này, từ kỹ thuật đến quy định pháp lý. Một ví dụ về thách thức kỹ thuật là hiệu ứng của *đường đi bất đối xứng*, khi yêu cầu tới server và phản hồi về client không đi qua cùng một chuỗi router.

Dù trang được cache ở đâu, khả năng cache trang web quan trọng đến mức HTTP đã được thiết kế để hỗ trợ việc này dễ dàng hơn. Mấu chốt là cache cần đảm bảo không trả về bản lỗi thời của trang. Ví dụ, server gán ngày hết hạn (trường header ``Expires``) cho mỗi trang gửi về client (hoặc cache giữa server và client). Cache ghi nhớ ngày này và biết rằng không cần xác minh lại trang mỗi lần được yêu cầu cho đến khi ngày hết hạn đã qua. Sau thời điểm đó (hoặc nếu trường header này không được đặt), cache có thể dùng thao tác ``HEAD`` hoặc ``GET`` có điều kiện (``GET`` với dòng header) để xác minh mình có bản mới nhất của trang. Tổng quát hơn, có một tập hợp các *chỉ thị cache* mà mọi cơ chế cache dọc chuỗi yêu cầu/đáp ứng phải tuân thủ. Các chỉ thị này quy định tài liệu có thể cache hay không, cache trong bao lâu, tài liệu phải mới đến mức nào, v.v. Chúng ta sẽ xem xét vấn đề liên quan là CDN—về bản chất là các cache phân tán—ở phần sau.

9.1.3 Dịch vụ Web (Web Services)
--------------------------------

Đến đây, chúng ta đã tập trung vào tương tác giữa con người và server web. Ví dụ, một người dùng sử dụng trình duyệt web để tương tác với server, và tương tác diễn ra theo phản hồi từ người dùng (ví dụ, nhấp vào liên kết). Tuy nhiên, nhu cầu tương tác trực tiếp giữa các máy tính ngày càng tăng. Và, cũng như các ứng dụng mô tả ở trên cần giao thức, các ứng dụng giao tiếp trực tiếp với nhau cũng vậy. Chúng ta kết thúc phần này bằng cách xem xét các thách thức khi xây dựng số lượng lớn các giao thức ứng dụng-đến-ứng dụng và một số giải pháp đề xuất.

Phần lớn động lực cho việc cho phép giao tiếp trực tiếp giữa các ứng dụng đến từ thế giới kinh doanh. Lịch sử, các tương tác giữa các doanh nghiệp—công ty hoặc tổ chức khác—thường liên quan đến một số bước thủ công như điền đơn đặt hàng hoặc gọi điện để kiểm tra hàng tồn kho. Ngay cả trong một doanh nghiệp, cũng thường có các bước thủ công giữa các hệ thống phần mềm không thể tương tác trực tiếp vì được phát triển độc lập. Ngày càng nhiều, các tương tác thủ công này đang được thay thế bằng giao tiếp ứng dụng-đến-ứng dụng trực tiếp. Một ứng dụng đặt hàng ở doanh nghiệp A sẽ gửi thông điệp tới ứng dụng xử lý đơn hàng ở doanh nghiệp B, ứng dụng này sẽ phản hồi ngay lập tức cho biết đơn hàng có thể được thực hiện hay không. Có thể, nếu đơn hàng không được B đáp ứng, ứng dụng ở A sẽ ngay lập tức đặt hàng từ nhà cung cấp khác hoặc gửi yêu cầu tới một tập hợp nhà cung cấp.

Đây là một ví dụ đơn giản về điều chúng ta đang nói. Giả sử bạn mua một cuốn sách ở một nhà bán lẻ trực tuyến như Amazon. Khi sách được gửi đi, Amazon có thể gửi cho bạn số theo dõi qua email, rồi bạn có thể vào website của công ty vận chuyển—\ ``http://www.fedex.com``, chẳng hạn—để tra cứu kiện hàng. Tuy nhiên, bạn cũng có thể tra cứu kiện hàng trực tiếp từ website Amazon.com. Để làm được điều này, Amazon phải có khả năng gửi truy vấn tới FedEx, theo định dạng mà FedEx hiểu, diễn giải kết quả và hiển thị nó trên một trang web có thể chứa thông tin khác về đơn hàng của bạn. Đằng sau trải nghiệm người dùng khi nhận được tất cả thông tin về đơn hàng cùng lúc trên trang Amazon.com là thực tế Amazon và FedEx phải có một giao thức để trao đổi thông tin cần thiết để tra cứu kiện hàng—gọi là Giao thức Tra cứu Kiện hàng. Rõ ràng có rất nhiều giao thức tiềm năng kiểu này nên chúng ta cần có công cụ để đơn giản hóa việc đặc tả và xây dựng chúng.

Các ứng dụng mạng, ngay cả khi vượt qua ranh giới tổ chức, không phải là mới—email và duyệt web đều vượt qua ranh giới đó. Điều mới ở đây là quy mô. Không phải quy mô về kích thước mạng, mà là quy mô về số lượng loại ứng dụng mạng khác nhau. Cả đặc tả giao thức lẫn hiện thực các giao thức đó cho các ứng dụng truyền thống như email và truyền tệp thường được phát triển bởi một nhóm nhỏ chuyên gia mạng. Để cho phép phát triển nhanh chóng số lượng lớn các ứng dụng mạng tiềm năng, cần có các công nghệ đơn giản hóa và tự động hóa việc thiết kế và hiện thực giao thức ứng dụng.

Có hai kiến trúc được đề xuất làm giải pháp cho vấn đề này. Cả hai đều gọi là *Dịch vụ Web* (Web Services), lấy tên từ thuật ngữ chỉ các ứng dụng riêng lẻ cung cấp dịch vụ truy cập từ xa cho ứng dụng client để tạo thành ứng dụng mạng. Các thuật ngữ dùng để phân biệt hai kiến trúc Dịch vụ Web là *SOAP* và *REST*. Chúng ta sẽ bàn về ý nghĩa kỹ thuật của các thuật ngữ này ngay sau đây.

Kiến trúc SOAP tiếp cận vấn đề bằng cách làm cho việc tạo ra các giao thức tùy biến cho từng ứng dụng mạng trở nên khả thi, ít nhất về lý thuyết. Các yếu tố then chốt của cách tiếp cận này là một khung đặc tả giao thức, các bộ công cụ phần mềm để tự động sinh hiện thực giao thức từ đặc tả, và các đặc tả mô-đun có thể tái sử dụng giữa các giao thức.

Kiến trúc REST tiếp cận vấn đề bằng cách coi từng Dịch vụ Web là một tài nguyên của World Wide Web—được nhận diện bằng URI và truy cập qua HTTP. Về bản chất, kiến trúc REST chính là kiến trúc Web. Ưu điểm của kiến trúc Web là ổn định và đã chứng minh được khả năng mở rộng (theo nghĩa quy mô mạng). Có thể coi là một điểm yếu khi HTTP không phù hợp với kiểu gọi thủ tục từ xa (RPC) hoặc hướng thao tác thường thấy. Tuy nhiên, các nhà ủng hộ REST cho rằng các dịch vụ phong phú vẫn có thể được cung cấp bằng phong cách hướng dữ liệu hoặc truyền tài liệu, vốn rất phù hợp với HTTP.

Giao thức ứng dụng tùy biến (WSDL, SOAP)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Kiến trúc thường được gọi không chính thức là SOAP dựa trên *Web Services Description Language* (WSDL) và *SOAP*.\ [#]_ Cả hai tiêu chuẩn này đều do World Wide Web Consortium (W3C) ban hành. Đây là kiến trúc mà người ta thường nghĩ đến khi nói về Dịch vụ Web mà không có định tính nào đi kèm. Vì các tiêu chuẩn này vẫn đang phát triển, phần thảo luận ở đây chỉ là một ảnh chụp nhanh.

.. [#] Mặc dù tên *SOAP* ban đầu là một từ viết tắt, hiện nay nó không còn đại diện cho cụm từ nào nữa.

WSDL và SOAP là các khung để đặc tả và hiện thực giao thức ứng dụng và giao thức vận chuyển, tương ứng. Chúng thường được dùng cùng nhau, dù không bắt buộc. WSDL dùng để đặc tả các chi tiết ứng dụng như các thao tác được hỗ trợ, định dạng dữ liệu ứng dụng để gọi hoặc đáp ứng các thao tác đó, và liệu một thao tác có cần phản hồi hay không. Vai trò của SOAP là giúp dễ dàng định nghĩa một giao thức vận chuyển với đúng các đặc tính mong muốn về độ tin cậy, bảo mật, v.v.

Cả WSDL và SOAP chủ yếu là ngôn ngữ đặc tả giao thức. Cả hai đều dựa trên XML với mục tiêu làm cho đặc tả dễ tiếp cận với các công cụ phần mềm như trình biên dịch stub và dịch vụ thư mục. Trong thế giới có nhiều giao thức tùy biến, hỗ trợ tự động sinh hiện thực là rất quan trọng để tránh phải hiện thực thủ công từng giao thức. Phần mềm hỗ trợ thường là các bộ công cụ và máy chủ ứng dụng do các nhà cung cấp bên thứ ba phát triển, cho phép các nhà phát triển Dịch vụ Web tập trung hơn vào bài toán nghiệp vụ cần giải quyết (như tra cứu kiện hàng cho khách hàng).

Đặc tả giao thức ứng dụng
~~~~~~~~~~~~~~~~~~~~~~~~~

WSDL chọn mô hình giao thức ứng dụng dựa trên thao tác (operation). Một giao diện Dịch vụ Web trừu tượng gồm một tập các thao tác được đặt tên, mỗi thao tác đại diện cho một tương tác đơn giản giữa client và Dịch vụ Web. Một thao tác tương tự như một thủ tục có thể gọi từ xa trong hệ thống RPC. Một ví dụ từ W3C’s WSDL Primer là Dịch vụ Web đặt phòng khách sạn với hai thao tác, CheckAvailability và MakeReservation.

Mỗi thao tác chỉ định một *Mẫu Trao đổi Thông điệp* (Message Exchange Pattern, MEP) cho biết trình tự các thông điệp sẽ được truyền, bao gồm cả thông điệp lỗi khi có sự cố. Có một số MEP được định nghĩa sẵn, và có thể định nghĩa MEP tùy biến mới, nhưng thực tế chỉ có hai MEP được dùng: **In-Only** (một thông điệp từ client tới dịch vụ) và **In-Out** (một yêu cầu từ client và một phản hồi từ dịch vụ). Các mẫu này rất quen thuộc, và cho thấy chi phí hỗ trợ linh hoạt MEP có lẽ lớn hơn lợi ích.

MEP là các mẫu có chỗ trống thay vì loại hoặc định dạng thông điệp cụ thể, nên một phần của việc định nghĩa thao tác là chỉ định định dạng thông điệp nào sẽ được ánh xạ vào các chỗ trống trong mẫu. Định dạng thông điệp không được định nghĩa ở mức bit như các giao thức đã bàn trước đây, mà được định nghĩa như một mô hình dữ liệu trừu tượng dùng XML. XML Schema cung cấp một tập các kiểu dữ liệu cơ bản và cách định nghĩa kiểu dữ liệu phức hợp. Dữ liệu tuân theo định dạng do XML Schema định nghĩa—mô hình dữ liệu trừu tượng của nó—có thể được biểu diễn cụ thể bằng XML, hoặc dùng biểu diễn khác như “binary” Fast Infoset.

WSDL tách biệt rõ ràng phần giao thức có thể đặc tả trừu tượng—thao tác, MEP, định dạng thông điệp trừu tượng—với phần phải cụ thể hóa. Phần cụ thể của WSDL chỉ định giao thức nền, cách ánh xạ MEP lên nó, và định dạng bit-level của thông điệp trên đường truyền. Phần này gọi là *binding*, dù thực chất là hiện thực hoặc ánh xạ lên hiện thực. WSDL có các binding định nghĩa sẵn cho HTTP và các giao thức dựa trên SOAP, với các tham số cho phép nhà thiết kế giao thức tinh chỉnh ánh xạ lên các giao thức đó. Có khung để định nghĩa binding mới, nhưng các giao thức dựa trên SOAP chiếm ưu thế.

Một khía cạnh then chốt giúp WSDL giảm bớt vấn đề đặc tả số lượng lớn giao thức là thông qua tái sử dụng các mô-đun đặc tả. Đặc tả WSDL của một Dịch vụ Web có thể gồm nhiều tài liệu WSDL, và từng tài liệu WSDL cũng có thể được dùng trong các đặc tả Dịch vụ Web khác. Tính mô-đun này giúp việc phát triển đặc tả dễ dàng hơn và đảm bảo rằng nếu hai đặc tả cần có một số thành phần giống hệt nhau (ví dụ, để được hỗ trợ bởi cùng một công cụ), thì các thành phần đó thực sự giống nhau. Tính mô-đun này, cùng với các quy tắc mặc định của WSDL, cũng giúp đặc tả không trở nên quá dài dòng với người thiết kế giao thức.

Tính mô-đun của WSDL sẽ quen thuộc với bất kỳ ai từng phát triển phần mềm quy mô vừa trở lên. Một tài liệu WSDL không nhất thiết phải là một đặc tả hoàn chỉnh; nó có thể chỉ định nghĩa một định dạng thông điệp. Các đặc tả một phần được nhận diện duy nhất bằng XML Namespace; mỗi tài liệu WSDL chỉ định URI của *target namespace*, và mọi định nghĩa mới trong tài liệu được đặt tên trong ngữ cảnh namespace đó. Một tài liệu WSDL có thể tích hợp thành phần của tài liệu khác bằng cách *include* nếu cùng namespace hoặc *import* nếu namespace khác nhau.

Đặc tả giao thức vận chuyển
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Mặc dù SOAP đôi khi được gọi là giao thức, thực ra nó là một khung để định nghĩa giao thức. Như đặc tả SOAP 1.2 giải thích, “SOAP cung cấp một khung nhắn tin đơn giản với chức năng cốt lõi là hỗ trợ mở rộng.” SOAP dùng nhiều chiến lược giống WSDL, bao gồm định dạng thông điệp bằng XML Schema, binding tới giao thức nền, MEP, và các thành phần đặc tả tái sử dụng nhận diện bằng XML namespace.

SOAP dùng để định nghĩa các giao thức vận chuyển với đúng các đặc tính cần thiết để hỗ trợ một giao thức ứng dụng cụ thể. SOAP hướng tới việc làm cho việc định nghĩa nhiều giao thức như vậy trở nên khả thi nhờ dùng các thành phần tái sử dụng. Mỗi thành phần nắm bắt thông tin header và logic cần thiết để hiện thực một đặc tính cụ thể. Để định nghĩa một giao thức với một tập đặc tính nào đó, chỉ cần kết hợp các thành phần tương ứng. Hãy xem kỹ hơn khía cạnh này của SOAP.

SOAP 1.2 giới thiệu trừu tượng *feature* (tính năng), được đặc tả như sau:

   *Một feature của SOAP là một phần mở rộng của khung nhắn tin SOAP. Mặc dù SOAP không đặt ràng buộc nào lên phạm vi tiềm năng của các feature, ví dụ về feature có thể bao gồm “độ tin cậy”, “bảo mật”, “liên kết”, “định tuyến”, và các mẫu trao đổi thông điệp (MEP) như yêu cầu/đáp ứng, một chiều, và hội thoại peer-to-peer.*

Một đặc tả feature của SOAP phải bao gồm:

-  Một URI nhận diện feature

-  Thông tin trạng thái và xử lý, mô tả trừu tượng, cần thiết ở mỗi nút SOAP để hiện thực feature

-  Thông tin cần chuyển tiếp tới nút tiếp theo

-  (Nếu feature là MEP) vòng đời và quan hệ thời gian/nguyên nhân của các thông điệp trao đổi—ví dụ, phản hồi theo sau yêu cầu và gửi tới nguồn gốc của yêu cầu

Lưu ý rằng việc hình thức hóa khái niệm feature giao thức này khá thấp; nó gần như là thiết kế.

Với một tập feature, có hai chiến lược để định nghĩa một giao thức SOAP hiện thực chúng. Một là xếp lớp: binding SOAP lên một giao thức nền để thu được các feature. Ví dụ, có thể thu được giao thức yêu cầu/đáp ứng bằng cách binding SOAP lên HTTP, với một yêu cầu SOAP trong HTTP request và một phản hồi SOAP trong HTTP response. Vì đây là ví dụ phổ biến, SOAP có binding định nghĩa sẵn cho HTTP; có thể định nghĩa binding mới bằng SOAP Protocol Binding Framework.

Cách thứ hai, linh hoạt hơn, là dùng *header block*. Một thông điệp SOAP gồm một Envelope, chứa Header với các header block, và Body chứa payload gửi tới đích cuối. Cấu trúc thông điệp này minh họa ở :numref:`Figure %s <fig-soapFormat>`.

.. _fig-soapFormat:
.. figure:: figures/f09-06-9780123850591.png
   :width: 250px
   :align: center

   Cấu trúc thông điệp SOAP.

Có lẽ bạn đã quen với ý tưởng rằng một số thông tin header tương ứng với các feature cụ thể. Chữ ký số dùng để xác thực, số thứ tự dùng cho độ tin cậy, và checksum dùng để phát hiện lỗi thông điệp. Một header block của SOAP nhằm đóng gói thông tin header tương ứng với một feature cụ thể. Sự tương ứng này không phải lúc nào cũng một-một vì có thể nhiều header block liên quan đến một feature, hoặc một header block dùng cho nhiều feature. Một *SOAP module* là đặc tả cú pháp và ngữ nghĩa của một hoặc nhiều header block. Mỗi module nhằm cung cấp một hoặc nhiều feature và phải khai báo các feature nó hiện thực.

Mục tiêu của các module SOAP là có thể xây dựng một giao thức với một tập feature chỉ bằng cách đưa các đặc tả module tương ứng vào. Nếu giao thức của bạn cần đảm bảo tối đa một lần (at-most-once) và xác thực, chỉ cần đưa các module tương ứng vào đặc tả. Đây là một cách tiếp cận mới trong việc mô-đun hóa dịch vụ giao thức, thay thế cho việc xếp lớp giao thức đã thấy trong sách này. Nó giống như làm phẳng một chuỗi lớp giao thức thành một giao thức duy nhất, nhưng theo cách có cấu trúc. Vẫn còn phải xem các feature và module SOAP (giới thiệu ở phiên bản 1.2) sẽ hoạt động thực tế ra sao. Điểm yếu chính của sơ đồ này là các module có thể xung đột với nhau. Một đặc tả module phải chỉ rõ mọi tương tác *đã biết* với các module SOAP khác, nhưng rõ ràng điều này không giúp giải quyết triệt để vấn đề. Mặt khác, một tập feature và module cốt lõi cung cấp các thuộc tính quan trọng nhất có thể đủ nhỏ để được biết rõ và hiểu rõ.

Chuẩn hóa giao thức Dịch vụ Web
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Như đã nói, WSDL và SOAP không phải là giao thức; chúng là tiêu chuẩn để *đặc tả* giao thức. Để các doanh nghiệp khác nhau hiện thực Dịch vụ Web có thể tương tác với nhau, chỉ đồng ý dùng WSDL và SOAP để định nghĩa giao thức là chưa đủ; họ phải đồng ý—chuẩn hóa—các giao thức cụ thể. Ví dụ, bạn có thể hình dung các nhà bán lẻ trực tuyến và công ty vận chuyển muốn chuẩn hóa một giao thức để trao đổi thông tin, giống như ví dụ tra cứu kiện hàng ở đầu phần này. Việc chuẩn hóa này rất quan trọng cho cả hỗ trợ công cụ lẫn khả năng tương tác. Tuy nhiên, các ứng dụng mạng khác nhau trong kiến trúc này nhất thiết phải khác nhau ít nhất ở định dạng thông điệp và thao tác sử dụng.

Sự căng thẳng giữa chuẩn hóa và tùy biến này được giải quyết bằng cách thiết lập các tiêu chuẩn một phần gọi là *profile*. Một profile là một tập hướng dẫn thu hẹp hoặc ràng buộc các lựa chọn có thể có trong WSDL, SOAP và các tiêu chuẩn khác có thể được tham chiếu khi định nghĩa giao thức. Đồng thời, chúng có thể giải quyết các mơ hồ hoặc lỗ hổng trong các tiêu chuẩn đó. Trong thực tế, một profile thường chính thức hóa một tiêu chuẩn *de facto* đang nổi lên.

Profile rộng nhất và được áp dụng nhiều nhất là *WS-I Basic Profile*. Nó được đề xuất bởi Web Services Interoperability Organization (WS-I), một liên minh ngành công nghiệp, trong khi WSDL và SOAP do World Wide Web Consortium (W3C) đặc tả. Basic Profile giải quyết một số lựa chọn cơ bản nhất khi định nghĩa Dịch vụ Web. Đáng chú ý nhất, nó yêu cầu WSDL chỉ được binding với SOAP và SOAP chỉ được binding với HTTP và dùng phương thức HTTP POST. Nó cũng chỉ định phiên bản WSDL và SOAP phải sử dụng.

*WS-I Basic Security Profile* bổ sung các ràng buộc bảo mật cho Basic Profile bằng cách chỉ định cách sử dụng lớp SSL/TLS và yêu cầu tuân thủ *WS-Security* (Web Services Security). WS-Security chỉ định cách sử dụng các kỹ thuật hiện có như chứng chỉ khóa công khai X.509 và Kerberos để cung cấp các tính năng bảo mật trong giao thức SOAP.

WS-Security chỉ là tiêu chuẩn đầu tiên trong một loạt các tiêu chuẩn tầng SOAP ngày càng tăng do liên minh ngành công nghiệp OASIS (Organization for the Advancement of Structured Information Standards) thiết lập. Các tiêu chuẩn được gọi chung là *WS-*\ \* bao gồm WS-Reliability, WS-ReliableMessaging, WS-Coordination, và WS-AtomicTransaction.

Một giao thức ứng dụng tổng quát (REST)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Kiến trúc Dịch vụ Web WSDL/SOAP dựa trên giả định rằng cách tốt nhất để tích hợp ứng dụng qua mạng là thông qua các giao thức tùy biến cho từng ứng dụng. Kiến trúc này được thiết kế để làm cho việc đặc tả và hiện thực tất cả các giao thức đó trở nên khả thi. Ngược lại, kiến trúc Dịch vụ Web REST dựa trên giả định rằng cách tốt nhất để tích hợp ứng dụng qua mạng là áp dụng lại mô hình nền tảng của kiến trúc World Wide Web. Mô hình này, do kiến trúc sư Web Roy Fielding đề xuất, gọi là *REpresentational State Transfer* (REST). Không cần một kiến trúc REST mới cho Dịch vụ Web—kiến trúc Web hiện tại là đủ, dù có thể cần một số mở rộng. Trong kiến trúc Web, từng Dịch vụ Web được coi là tài nguyên nhận diện bằng URI và truy cập qua HTTP—một giao thức ứng dụng tổng quát với một không gian địa chỉ tổng quát.

Nơi WSDL có thao tác do người dùng định nghĩa, REST dùng tập nhỏ các phương thức HTTP sẵn có, như ``GET`` và ``POST`` (xem :numref:`Table %s <tab-ops>`). Vậy làm sao các phương thức đơn giản này cung cấp giao diện cho một Dịch vụ Web phong phú? Bằng cách áp dụng mô hình REST, trong đó độ phức tạp được chuyển từ giao thức sang payload. Payload là một biểu diễn trạng thái trừu tượng của tài nguyên. Ví dụ, một ``GET`` có thể trả về biểu diễn trạng thái hiện tại của tài nguyên, còn ``POST`` có thể gửi biểu diễn trạng thái mong muốn của tài nguyên.

Biểu diễn trạng thái tài nguyên là trừu tượng; nó không nhất thiết giống cách tài nguyên được hiện thực bởi một Dịch vụ Web cụ thể. Không cần truyền toàn bộ trạng thái tài nguyên trong mỗi thông điệp. Kích thước thông điệp có thể giảm bằng cách chỉ truyền phần trạng thái quan tâm (ví dụ, chỉ phần bị thay đổi). Và, vì Dịch vụ Web chia sẻ một giao thức và không gian địa chỉ với các tài nguyên web khác, các phần trạng thái có thể được truyền bằng tham chiếu—bằng URI—ngay cả khi chúng là Dịch vụ Web khác.

Cách tiếp cận này có thể tóm tắt là phong cách hướng dữ liệu hoặc truyền tài liệu, trái ngược với phong cách thủ tục. Định nghĩa một giao thức ứng dụng trong kiến trúc này là định nghĩa cấu trúc tài liệu (tức là biểu diễn trạng thái). XML và JavaScript Object Notation (JSON) nhẹ hơn là hai ngôn ngữ trình bày thường dùng cho trạng thái này. Khả năng tương tác phụ thuộc vào sự đồng thuận giữa Dịch vụ Web và client về biểu diễn trạng thái. Tất nhiên, điều này cũng đúng trong kiến trúc SOAP; Dịch vụ Web và client phải đồng thuận về định dạng payload. Khác biệt là trong kiến trúc SOAP, khả năng tương tác còn phụ thuộc vào đồng thuận về giao thức; trong kiến trúc REST, giao thức luôn là HTTP, nên nguồn gốc vấn đề tương tác này bị loại bỏ.

Một điểm mạnh của REST là tận dụng được hạ tầng đã triển khai để hỗ trợ Web. Ví dụ, proxy Web có thể thực thi bảo mật hoặc cache thông tin. Các mạng phân phối nội dung (CDN) hiện có có thể dùng để hỗ trợ ứng dụng RESTful.

Trái ngược với WSDL/SOAP, Web đã có thời gian để các tiêu chuẩn ổn định và chứng minh khả năng mở rộng rất tốt. Nó cũng đi kèm một số bảo mật dưới dạng Secure Socket Layer (SSL)/Transport Layer Security (TLS). Web và REST cũng có thể có lợi thế về khả năng tiến hóa. Mặc dù các khung WSDL và SOAP rất linh hoạt về những tính năng và binding mới có thể đưa vào định nghĩa giao thức, sự linh hoạt đó không còn ý nghĩa khi giao thức đã được định nghĩa. Các giao thức chuẩn hóa như HTTP được thiết kế với khả năng mở rộng tương thích ngược. Chính HTTP cũng có thể mở rộng qua header, phương thức mới, và loại nội dung mới. Nhà thiết kế giao thức dùng WSDL/SOAP cần tự thiết kế khả năng mở rộng cho từng giao thức tùy biến. Tất nhiên, nhà thiết kế biểu diễn trạng thái trong kiến trúc REST cũng phải thiết kế cho khả năng tiến hóa.

Một lĩnh vực mà WSDL/SOAP có thể có lợi thế là thích nghi hoặc bọc các ứng dụng “legacy” đã viết từ trước để phù hợp với Dịch vụ Web. Đây là điểm quan trọng vì hầu hết Dịch vụ Web trong tương lai gần sẽ dựa trên ứng dụng legacy. Các ứng dụng này thường có giao diện thủ tục phù hợp hơn với thao tác của WSDL hơn là trạng thái của REST. Cuộc cạnh tranh REST so với WSDL/SOAP có thể sẽ phụ thuộc vào việc xây dựng giao diện kiểu REST cho từng Dịch vụ Web dễ hay khó. Có thể một số Dịch vụ Web phù hợp với WSDL/SOAP, số khác phù hợp với REST.

Nhà bán lẻ trực tuyến Amazon, chẳng hạn, là một trong những đơn vị áp dụng sớm (2002) Dịch vụ Web. Thú vị là Amazon công khai hệ thống của mình qua *cả hai* kiến trúc Dịch vụ Web, và theo một số báo cáo, phần lớn lập trình viên dùng giao diện REST. Tất nhiên, đây chỉ là một ví dụ và có thể phản ánh các yếu tố đặc thù của Amazon.

Từ Dịch vụ Web đến Dịch vụ Đám mây (Cloud Services)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Nếu Dịch vụ Web là khi server web hiện thực ứng dụng của tôi gửi yêu cầu tới server web hiện thực ứng dụng của bạn, thì gọi là gì khi cả hai chúng ta đưa ứng dụng lên đám mây để hỗ trợ tải lớn? Ta có thể gọi cả hai là *Dịch vụ Đám mây* (Cloud Services) nếu muốn, nhưng liệu đó có phải là sự phân biệt không có khác biệt? Còn tùy.

Chuyển một tiến trình server từ máy vật lý trong phòng máy của tôi lên máy ảo chạy trong trung tâm dữ liệu của nhà cung cấp đám mây chuyển trách nhiệm duy trì máy từ quản trị viên hệ thống của tôi sang đội vận hành của nhà cung cấp đám mây, nhưng ứng dụng vẫn được thiết kế theo kiến trúc Dịch vụ Web. Ngược lại, nếu ứng dụng được thiết kế từ đầu để chạy trên nền tảng đám mây có khả năng mở rộng, ví dụ tuân thủ kiến trúc *micro-services*, thì ta gọi ứng dụng đó là *cloud native*. Vậy điểm khác biệt quan trọng là cloud native so với dịch vụ web legacy triển khai trên đám mây.

Chúng ta đã thấy kiến trúc micro-services ở Chương 5 khi mô tả gRPC, và dù khó có thể khẳng định micro-services vượt trội so với dịch vụ web, xu hướng hiện tại trong ngành gần như chắc chắn nghiêng về micro-services. Có lẽ thú vị hơn là cuộc tranh luận đang diễn ra về REST+Json so với gRPC+Protobufs như cơ chế RPC ưa thích để hiện thực micro-services. Hãy nhớ rằng cả hai đều chạy trên HTTP, chúng tôi để bạn đọc tự chọn phe và bảo vệ quan điểm của mình.
