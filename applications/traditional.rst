9.1 Ứng dụng truyền thống
=========================

Chúng ta bắt đầu thảo luận về các ứng dụng bằng cách tập trung vào hai
ứng dụng phổ biến nhất—World Wide Web và email. Nói một cách rộng rãi,
cả hai ứng dụng này đều sử dụng mô hình request/reply—người dùng gửi
yêu cầu đến máy chủ, sau đó máy chủ phản hồi tương ứng. Chúng tôi gọi
đây là các ứng dụng “truyền thống” vì chúng tiêu biểu cho loại ứng dụng
đã tồn tại từ những ngày đầu của mạng máy tính (mặc dù Web mới hơn email
nhưng có nguồn gốc từ các ứng dụng truyền file có trước đó). Ngược lại,
các phần sau sẽ xem xét một lớp ứng dụng trở nên phổ biến gần đây hơn:
các ứng dụng streaming (ví dụ, ứng dụng đa phương tiện như video và âm
thanh) và các ứng dụng dựa trên overlay khác nhau. (Lưu ý rằng có một
sự giao thoa giữa các lớp này, vì ngày càng nhiều dữ liệu đa phương tiện
streaming được truy cập qua Web, nhưng hiện tại chúng ta sẽ tập trung vào
việc sử dụng Web để yêu cầu các trang, hình ảnh, v.v.)

Trước khi xem xét kỹ từng ứng dụng này, có ba điểm chung cần lưu ý. Thứ
nhất là cần phân biệt giữa *chương trình ứng dụng* và *giao thức ứng
dụng*. Ví dụ, HyperText Transport Protocol (HTTP) là một giao thức ứng
dụng được sử dụng để truy xuất các trang Web từ các máy chủ từ xa. Nhiều
chương trình ứng dụng khác nhau—tức là các trình duyệt web như Internet
Explorer, Chrome, Firefox và Safari—cung cấp cho người dùng giao diện và
trải nghiệm khác nhau, nhưng tất cả đều sử dụng cùng một giao thức HTTP
để giao tiếp với các máy chủ web qua Internet. Thực tế là giao thức này
được công bố và chuẩn hóa cho phép các chương trình ứng dụng do nhiều
công ty và cá nhân khác nhau phát triển có thể tương tác với nhau. Đó là
lý do tại sao có rất nhiều trình duyệt có thể tương tác với tất cả các
máy chủ web (mà cũng có rất nhiều loại khác nhau).

Phần này xem xét hai giao thức ứng dụng được sử dụng rộng rãi và chuẩn
hóa:

-  Simple Mail Transfer Protocol (SMTP) được sử dụng để trao đổi thư
   điện tử.

-  HyperText Transport Protocol (HTTP) được sử dụng để giao tiếp giữa
   trình duyệt web và máy chủ web.

Thứ hai, chúng ta nhận thấy rằng nhiều giao thức tầng ứng dụng, bao gồm
HTTP và SMTP, có một giao thức đi kèm xác định định dạng dữ liệu có thể
trao đổi. Đây là một lý do tại sao các giao thức này tương đối đơn giản:
phần lớn độ phức tạp được quản lý trong tiêu chuẩn đi kèm này. Ví dụ,
SMTP là một giao thức để trao đổi thông điệp thư điện tử, nhưng RFC 822
và Multipurpose Internet Mail Extensions (MIME) xác định định dạng của
các thông điệp email. Tương tự, HTTP là một giao thức để lấy các trang
Web, nhưng HyperText Markup Language (HTML) là một đặc tả đi kèm xác
định hình thức cơ bản của các trang đó.

Cuối cùng, vì các giao thức ứng dụng được mô tả trong phần này đều tuân
theo cùng một mô hình giao tiếp request/reply, bạn có thể mong đợi rằng
chúng sẽ được xây dựng trên một giao thức vận chuyển Remote Procedure
Call (RPC). Tuy nhiên, điều này không đúng, vì chúng được hiện thực trên
TCP [#]_. Thực chất, mỗi giao thức tự xây dựng một cơ chế giống RPC đơn
giản trên một giao thức vận chuyển tin cậy (TCP). Chúng tôi nói “đơn
giản” vì mỗi giao thức không được thiết kế để hỗ trợ các lời gọi thủ tục
từ xa tùy ý như đã thảo luận ở chương trước, mà thay vào đó được thiết
kế để gửi và phản hồi một tập hợp cụ thể các thông điệp yêu cầu. Thật
thú vị, cách tiếp cận của HTTP đã chứng tỏ rất mạnh mẽ, dẫn đến việc nó
được áp dụng rộng rãi trong kiến trúc *Web Services*, với các cơ chế RPC
tổng quát được xây dựng *trên HTTP* thay vì ngược lại. Sẽ nói thêm về
chủ đề này ở cuối phần này.

.. [#] Điều này bắt đầu thay đổi với Web khi có HTTP/3 như sẽ thảo luận ở
       phần sau của chương.

9.1.1 Thư điện tử (SMTP, MIME, IMAP)
----------------------------------------

Email là một trong những ứng dụng mạng lâu đời nhất. Rốt cuộc, còn gì
tự nhiên hơn việc muốn gửi một thông điệp đến người dùng ở đầu bên kia
của một liên kết xuyên quốc gia mà bạn vừa thiết lập thành công? Đó là
phiên bản thế kỷ 20 của “*Mr. Watson, đến đây… Tôi muốn gặp anh.*” Thật
ngạc nhiên, những người tiên phong của ARPANET thực sự không hình dung
email là một ứng dụng chủ chốt khi mạng được tạo ra—truy cập từ xa vào
tài nguyên tính toán mới là mục tiêu thiết kế chính—nhưng cuối cùng nó
lại trở thành “killer app” đầu tiên của Internet.

Như đã lưu ý ở trên, điều quan trọng là (1) phân biệt giao diện người
dùng (tức là chương trình đọc thư của bạn) với các giao thức truyền
thông điệp bên dưới (như SMTP hoặc IMAP), và (2) phân biệt giữa giao
thức truyền này với một tiêu chuẩn đi kèm (RFC 822 và MIME) xác định
định dạng của các thông điệp được trao đổi. Chúng ta bắt đầu bằng cách
xem xét định dạng thông điệp.

Định dạng thông điệp
~~~~~~~~~~~~~~~~~~~~

RFC 822 định nghĩa thông điệp gồm hai phần: *header* và *body*. Cả hai
phần đều được biểu diễn dưới dạng văn bản ASCII. Ban đầu, phần body được
giả định chỉ là văn bản đơn giản. Điều này vẫn đúng, mặc dù RFC 822 đã
được mở rộng bởi MIME để cho phép phần body mang nhiều loại dữ liệu khác
nhau. Dữ liệu này vẫn được biểu diễn dưới dạng văn bản ASCII, nhưng vì
nó có thể là phiên bản mã hóa của, ví dụ, một hình ảnh JPEG, nên không
nhất thiết phải đọc được bằng mắt người. Sẽ nói thêm về MIME ở phần sau.

Phần header của thông điệp là một chuỗi các dòng kết thúc bằng ``<CRLF>``.
(``<CRLF>`` là viết tắt của carriage-return cộng line-feed, là một cặp
ký tự điều khiển ASCII thường dùng để chỉ kết thúc một dòng văn bản.)
Header được tách khỏi body bằng một dòng trống. Mỗi dòng header chứa một
loại và giá trị được phân tách bằng dấu hai chấm. Nhiều dòng header này
rất quen thuộc với người dùng, vì họ được yêu cầu điền khi soạn email;
ví dụ, header xác định người nhận thông điệp, và header nói điều gì đó
về mục đích của thông điệp. Các header khác được hệ thống chuyển phát
thư điền vào. Ví dụ bao gồm (khi thông điệp được truyền), (người dùng
gửi thông điệp), và (mỗi máy chủ thư đã xử lý thông điệp này). Tất nhiên
còn nhiều dòng header khác; người đọc quan tâm có thể tham khảo RFC 822.

RFC 822 được mở rộng vào năm 1993 (và được cập nhật khá nhiều lần kể từ
đó) để cho phép email mang nhiều loại dữ liệu khác nhau: âm thanh, video,
hình ảnh, tài liệu PDF, v.v. MIME gồm ba phần cơ bản. Phần đầu tiên là
một tập hợp các dòng header bổ sung cho tập gốc do RFC 822 định nghĩa.
Các dòng header này mô tả, theo nhiều cách khác nhau, dữ liệu được mang
trong body của thông điệp. Chúng bao gồm (phiên bản MIME đang dùng), (mô
tả dễ đọc về nội dung thông điệp, tương tự dòng ), (loại dữ liệu chứa
trong thông điệp), và (cách dữ liệu trong body được mã hóa).

Phần thứ hai là các định nghĩa cho một tập hợp các loại nội dung (và
các loại con). Ví dụ, MIME định nghĩa một số loại hình ảnh khác nhau,
bao gồm ``image/gif`` và ``image/jpeg``, mỗi loại có ý nghĩa rõ ràng.
Một ví dụ khác, ``text/plain`` chỉ văn bản đơn giản bạn có thể thấy
trong một thông điệp kiểu 822, trong khi ``text/richtext`` chỉ thông
điệp chứa văn bản “được đánh dấu” (văn bản dùng font đặc biệt, in nghiêng,
v.v.). Một ví dụ thứ ba, MIME định nghĩa loại ``application``, trong đó
các loại con tương ứng với đầu ra của các chương trình ứng dụng khác
nhau (ví dụ, ``application/postscript`` và ``application/msword``).

MIME cũng định nghĩa loại ``multipart`` chỉ cách cấu trúc một thông điệp
mang nhiều loại dữ liệu. Điều này giống như một ngôn ngữ lập trình định
nghĩa cả kiểu cơ bản (ví dụ, số nguyên và số thực) và kiểu phức hợp (ví
dụ, cấu trúc và mảng). Một loại con ``multipart`` có thể là ``mixed``,
chỉ rằng thông điệp chứa một tập hợp các phần dữ liệu độc lập theo thứ
tự xác định. Mỗi phần sau đó có dòng header riêng mô tả loại của phần đó.

Phần thứ ba là một cách để mã hóa các loại dữ liệu khác nhau để có thể
gửi trong thông điệp email ASCII. Vấn đề là, với một số loại dữ liệu
(ví dụ, hình ảnh JPEG), bất kỳ byte 8 bit nào trong hình cũng có thể
chứa một trong 256 giá trị khác nhau. Chỉ một tập con các giá trị này là
ký tự ASCII hợp lệ. Việc email chỉ chứa ASCII là quan trọng, vì chúng có
thể đi qua nhiều hệ thống trung gian (gateway, như mô tả bên dưới) giả
định mọi email đều là ASCII và sẽ làm hỏng thông điệp nếu nó chứa ký tự
không phải ASCII. Để giải quyết vấn đề này, MIME sử dụng một mã hóa đơn
giản dữ liệu nhị phân thành tập ký tự ASCII. Mã hóa này gọi là ``base64``.
Ý tưởng là ánh xạ mỗi ba byte dữ liệu nhị phân gốc thành bốn ký tự ASCII.
Điều này được thực hiện bằng cách nhóm dữ liệu nhị phân thành các đơn vị
24 bit và chia mỗi đơn vị thành bốn phần 6 bit. Mỗi phần 6 bit ánh xạ
thành một trong 64 ký tự ASCII hợp lệ; ví dụ, 0 ánh xạ thành A, 1 thành
B, v.v. Nếu bạn nhìn vào một thông điệp đã được mã hóa bằng base64, bạn
sẽ thấy chỉ có 52 chữ cái hoa/thường, 10 chữ số 0 đến 9, và các ký tự
đặc biệt + và /. Đây là 64 giá trị đầu tiên trong tập ký tự ASCII.

Một lưu ý nhỏ, để việc đọc thư dễ dàng hơn cho những ai vẫn dùng trình
đọc thư chỉ văn bản, một thông điệp MIME chỉ chứa văn bản thường có thể
được mã hóa bằng ASCII 7 bit. Cũng có một mã hóa dễ đọc cho dữ liệu chủ
yếu là ASCII.

Kết hợp tất cả lại, một thông điệp chứa văn bản thường, một hình JPEG,
và một file PostScript sẽ trông như sau:

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

Trong ví dụ này, dòng trong header thông điệp cho biết thông điệp này
chứa nhiều phần, mỗi phần được đánh dấu bằng một chuỗi ký tự không xuất
hiện trong dữ liệu thực tế. Mỗi phần sau đó có dòng ``Content-Type`` và
``Content-Transfer-Encoding`` riêng.

Truyền thông điệp
~~~~~~~~~~~~~~~~~

Trong nhiều năm, phần lớn email được chuyển từ máy này sang máy khác chỉ
bằng SMTP. Mặc dù SMTP vẫn đóng vai trò trung tâm, giờ đây nó chỉ là một
trong nhiều giao thức email, Internet Message Access Protocol (IMAP) và
Post Office Protocol (POP) là hai giao thức quan trọng khác để lấy thư.
Chúng ta sẽ bắt đầu bằng SMTP, sau đó chuyển sang IMAP.

Để đặt SMTP vào đúng ngữ cảnh, cần xác định các thành phần chính. Đầu
tiên, người dùng tương tác với một *mail reader* khi họ soạn, lưu trữ,
tìm kiếm và đọc email. Có vô số mail reader, giống như có nhiều trình
duyệt web để lựa chọn. Những ngày đầu Internet, người dùng thường đăng
nhập vào máy chứa *mailbox* của họ, và mail reader họ sử dụng là một
chương trình cục bộ trích xuất thông điệp từ hệ thống file. Ngày nay,
người dùng truy cập mailbox từ xa bằng laptop hoặc smartphone; họ không
cần đăng nhập vào máy chủ chứa thư (mail server). Một giao thức truyền
thư thứ hai, như POP hoặc IMAP, được dùng để tải email từ mail server về
thiết bị người dùng.

Thứ hai, có một *mail daemon* (hoặc process) chạy trên mỗi máy chứa
mailbox. Bạn có thể coi process này, còn gọi là *message transfer agent*
(MTA), như một bưu điện: Người dùng (hoặc mail reader của họ) đưa cho
daemon các thông điệp muốn gửi, daemon sử dụng SMTP chạy trên TCP để
truyền thông điệp đến daemon trên máy khác, và daemon đặt các thông điệp
nhận được vào mailbox của người dùng (để mail reader sau này lấy ra).
Vì SMTP là một giao thức mà bất kỳ ai cũng có thể hiện thực, về lý thuyết
có thể có nhiều hiện thực khác nhau của mail daemon. Tuy nhiên, thực tế
chỉ có một vài hiện thực phổ biến, với chương trình ``sendmail`` của
Berkeley Unix và ``postfix`` là phổ biến nhất.

.. _fig-mail:
.. figure:: figures/f09-01-9780123850591.png
   :width: 600px
   :align: center

   Chuỗi các mail gateway lưu trữ và chuyển tiếp thông điệp email.

Mặc dù hoàn toàn có thể MTA trên máy gửi thiết lập một kết nối SMTP/TCP
đến MTA trên mail server của người nhận, trong nhiều trường hợp email đi
qua một hoặc nhiều *mail gateway* trên đường từ máy gửi đến máy nhận.
Giống như các máy đầu cuối, các gateway này cũng chạy một process message
transfer agent. Không phải ngẫu nhiên mà các nút trung gian này được gọi
là *gateway* vì nhiệm vụ của chúng là lưu trữ và chuyển tiếp thông điệp
email, giống như một “IP gateway” (chúng ta gọi là *router*) lưu trữ và
chuyển tiếp datagram IP. Khác biệt duy nhất là mail gateway thường lưu
thông điệp trên đĩa và sẵn sàng thử gửi lại cho máy tiếp theo trong vài
ngày, trong khi router IP lưu datagram trong bộ nhớ và chỉ thử gửi lại
trong một phần nhỏ của giây. :numref:`Figure %s <fig-mail>` minh họa một
đường đi hai hop từ người gửi đến người nhận.

Bạn có thể hỏi, tại sao cần mail gateway? Tại sao máy gửi không gửi
thẳng đến máy nhận? Một lý do là người nhận không muốn địa chỉ email của
mình chứa tên máy cụ thể mà họ đọc thư. Một lý do khác là vấn đề mở rộng:
Trong các tổ chức lớn, thường có nhiều máy khác nhau chứa *mailbox* cho
tổ chức. Ví dụ, thư gửi đến ``bob@cs.princeton.edu`` trước tiên được gửi
đến một mail gateway của Khoa Khoa học Máy tính Princeton (tức là, máy
có tên ``cs.princeton.edu``), sau đó được chuyển tiếp—liên quan đến một
kết nối thứ hai—đến máy cụ thể chứa mailbox của Bob. Gateway chuyển tiếp
duy trì một cơ sở dữ liệu ánh xạ người dùng vào máy chứa mailbox của họ;
người gửi không cần biết tên cụ thể này. (Danh sách các dòng header trong
thông điệp sẽ giúp bạn truy vết các mail gateway mà một thông điệp đã đi
qua.) Một lý do nữa, đặc biệt đúng trong những ngày đầu email, là máy
chứa mailbox của người dùng có thể không luôn hoạt động hoặc không truy
cập được, khi đó mail gateway giữ thông điệp cho đến khi có thể chuyển
giao.

Bất kể có bao nhiêu mail gateway trên đường đi, một kết nối SMTP độc lập
được sử dụng giữa mỗi máy để chuyển thông điệp đến gần người nhận hơn.
Mỗi phiên SMTP là một cuộc đối thoại giữa hai mail daemon, một bên đóng
vai trò client, bên kia là server. Có thể nhiều thông điệp được truyền
giữa hai máy trong một phiên. Vì RFC 822 định nghĩa thông điệp dùng ASCII
làm biểu diễn cơ sở, không có gì ngạc nhiên khi SMTP cũng dựa trên ASCII.
Điều này có nghĩa là một người ngồi trước bàn phím hoàn toàn có thể giả
làm chương trình client SMTP.

SMTP dễ hiểu nhất qua một ví dụ đơn giản. Sau đây là một trao đổi giữa
máy gửi ``cs.princeton.edu`` và máy nhận ``systemsapproach.org``. Trong
trường hợp này, người dùng Bob ở Princeton cố gắng gửi thư cho Alice và
Tom ở Systems Approach. Các dòng trống được thêm vào cho dễ đọc.

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

Như bạn thấy, SMTP gồm một chuỗi trao đổi giữa client và server. Trong
mỗi trao đổi, client gửi một lệnh (ví dụ, ``QUIT``) và server phản hồi
bằng một mã (ví dụ, ``250``, ``550``, ``354``, ``221``). Server cũng trả
về một giải thích dễ đọc cho mã đó (ví dụ, ``No such user here``). Trong
ví dụ này, client đầu tiên xác định mình với server bằng lệnh ``HELO``.
Nó đưa tên miền của mình làm tham số. Server xác minh tên này khớp với
địa chỉ IP đang dùng cho kết nối TCP; bạn sẽ thấy server trả lại địa chỉ
IP này cho client. Client sau đó hỏi server có sẵn sàng nhận thư cho hai
người dùng khác nhau không; server trả lời “có” cho một và “không” cho
người còn lại. Sau đó client gửi thông điệp, kết thúc bằng một dòng chỉ
chứa dấu chấm (“.”). Cuối cùng, client kết thúc kết nối.

Tất nhiên còn nhiều lệnh và mã phản hồi khác. Ví dụ, server có thể phản
hồi lệnh ``RCPT`` của client bằng mã ``251``, chỉ rằng người dùng không
có mailbox trên máy này, nhưng server hứa sẽ chuyển tiếp thông điệp đến
một mail daemon khác. Nói cách khác, máy này đóng vai trò mail gateway.
Một ví dụ khác, client có thể gửi lệnh ``VRFY`` để xác minh địa chỉ email
người dùng, nhưng không thực sự gửi thông điệp cho người đó.

Điểm đáng chú ý duy nhất là các tham số cho lệnh ``MAIL`` và ``RCPT``;
ví dụ, ``FROM:<Bob@cs.princeton.edu>`` và ``TO:<Alice@systemsapproach.org>``
tương ứng. Chúng trông rất giống các trường header 822, và ở một mức độ
nào đó đúng như vậy. Thực tế, mail daemon phân tích thông điệp để trích
xuất thông tin cần thiết cho SMTP. Thông tin trích xuất này tạo thành
*một phong bì* cho thông điệp. Client SMTP sử dụng phong bì này để tham
số hóa trao đổi với server SMTP. Một ghi chú lịch sử: Lý do ``sendmail``
trở nên phổ biến là không ai muốn hiện thực lại chức năng phân tích thông
điệp này. Mặc dù ngày nay địa chỉ email trông khá đơn giản (ví dụ,
``Bob@cs.princeton.edu``), trước đây không phải lúc nào cũng vậy. Những
ngày trước khi mọi người đều kết nối Internet, không hiếm gặp địa chỉ
email dạng ``user%host@site!neighbor``.

Mail Reader
~~~~~~~~~~~

Bước cuối cùng là người dùng thực sự lấy các thông điệp từ mailbox, đọc,
trả lời, và có thể lưu lại để tham khảo sau. Người dùng thực hiện tất cả
các thao tác này bằng cách tương tác với mail reader. Như đã nói ở trên,
mail reader ban đầu chỉ là một chương trình chạy trên cùng máy với
mailbox của người dùng, khi đó nó chỉ cần đọc và ghi file thực hiện
mailbox. Đây là trường hợp phổ biến trước thời laptop. Ngày nay, hầu hết
người dùng truy cập mailbox từ máy từ xa bằng một giao thức khác, như
POP hoặc IMAP. Việc thảo luận giao diện người dùng của mail reader nằm
ngoài phạm vi cuốn sách này, nhưng việc nói về giao thức truy cập thì
không. Chúng ta sẽ xem xét IMAP.

IMAP giống SMTP ở nhiều điểm. Nó là một giao thức client/server chạy trên
TCP, trong đó client (chạy trên máy tính để bàn của người dùng) gửi lệnh
dưới dạng các dòng văn bản ASCII kết thúc bằng ``<CRLF>`` và mail server
(chạy trên máy duy trì mailbox của người dùng) phản hồi tương tự. Trao
đổi bắt đầu bằng việc client xác thực bản thân và xác định mailbox muốn
truy cập. Điều này có thể được biểu diễn bằng sơ đồ trạng thái đơn giản
trong :numref:`Figure %s <fig-imap>`. Trong sơ đồ này, ``LOGIN`` và
``LOGOUT`` là các lệnh ví dụ mà client có thể gửi, trong khi ``OK`` là
một phản hồi có thể của server. Các lệnh phổ biến khác bao gồm ``FETCH``
và ``EXPUNGE``, với ý nghĩa rõ ràng. Các phản hồi server bổ sung gồm
``NO`` (client không có quyền thực hiện thao tác đó) và ``BAD`` (lệnh
không hợp lệ).

.. _fig-imap:
.. figure:: figures/f09-02-9780123850591.png
   :width: 400px
   :align: center

   Sơ đồ chuyển trạng thái IMAP.

Khi người dùng yêu cầu ``FETCH`` một thông điệp, server trả về nó ở định
dạng MIME và mail reader giải mã nó. Ngoài bản thân thông điệp, IMAP còn
định nghĩa một tập thuộc tính thông điệp được trao đổi như một phần của
các lệnh khác, độc lập với việc truyền thông điệp. Thuộc tính thông điệp
bao gồm thông tin như kích thước thông điệp và, thú vị hơn, các *cờ*
khác nhau gắn với thông điệp (ví dụ, ``Seen``, ``Answered``, ``Deleted``,
và ``Recent``). Các cờ này được dùng để giữ client và server đồng bộ;
tức là, khi người dùng xóa một thông điệp trong mail reader, client cần
báo cho mail server biết. Sau này, nếu người dùng quyết định xóa vĩnh
viễn các thông điệp đã xóa, client gửi lệnh ``EXPUNGE`` cho server, server
sẽ thực sự xóa các thông điệp đã bị xóa trước đó khỏi mailbox.

Cuối cùng, lưu ý rằng khi người dùng trả lời một thông điệp, hoặc gửi
một thông điệp mới, mail reader không chuyển tiếp thông điệp từ client
đến mail server bằng IMAP, mà thay vào đó sử dụng SMTP. Điều này có nghĩa
mail server của người dùng thực chất là mail gateway đầu tiên trên đường
từ máy tính để bàn đến mailbox của người nhận.

9.1.2 World Wide Web (HTTP)
---------------------------

World Wide Web đã thành công đến mức khiến Internet trở nên quen thuộc
với rất nhiều người, đôi khi dường như nó đồng nghĩa với Internet. Thực
ra, thiết kế của hệ thống trở thành Web bắt đầu khoảng năm 1989, lâu sau
khi Internet đã trở thành một hệ thống được triển khai rộng rãi. Mục tiêu
ban đầu của Web là tìm cách tổ chức và truy xuất thông tin, dựa trên các
ý tưởng về hypertext—các tài liệu liên kết với nhau—đã xuất hiện từ ít
nhất những năm 1960.\ [#]_ Ý tưởng cốt lõi của hypertext là một tài liệu
có thể liên kết đến tài liệu khác, và giao thức (HTTP) cùng ngôn ngữ tài
liệu (HTML) được thiết kế để đáp ứng mục tiêu đó.

.. [#] A short history of the Web provided by the World Wide Web
       consortium traces its roots to a 1945 article describing links
       between microfiche documents.

Một cách hữu ích để hình dung Web là một tập hợp các client và server
hợp tác, tất cả đều nói cùng một ngôn ngữ: HTTP. Hầu hết mọi người tiếp
cận Web thông qua một chương trình client đồ họa hoặc trình duyệt web như
Safari, Chrome, Firefox hoặc Internet Explorer. :numref:`Figure %s
<fig-netscape>` cho thấy trình duyệt Safari đang được sử dụng, hiển thị
một trang thông tin từ Đại học Princeton.

.. _fig-netscape:
.. figure:: figures/browser-screenshot.png
   :width: 600px
   :align: center

   Trình duyệt web Safari.

Nếu bạn muốn tổ chức thông tin thành một hệ thống các tài liệu hoặc đối
tượng liên kết, cần có một cách để xác định tài liệu để bạn có thể liên
kết đến chúng. Uniform Resource Locator (URL) giờ đã quá quen thuộc với
chúng ta đến mức dễ quên rằng chúng chưa tồn tại từ lâu. Chúng cung cấp
thông tin cho phép xác định vị trí các đối tượng trên Web, và chúng có
dạng như sau:

.. code-block:: html

   http://www.cs.princeton.edu/index.html

Nếu bạn mở URL này, trình duyệt web của bạn sẽ mở một kết nối TCP đến
máy chủ web tại máy có tên ``www.cs.princeton.edu`` và ngay lập tức lấy
và hiển thị file có tên ``index.html``. Hầu hết các file trên Web chứa
hình ảnh và văn bản, và nhiều file có các đối tượng khác như đoạn âm
thanh, video, đoạn mã, v.v. Chúng cũng thường chứa các URL trỏ đến các
file khác có thể nằm trên các máy khác, đây là cốt lõi của phần
“hypertext” trong HTTP và HTML. Một trình duyệt web có cách để bạn nhận
biết URL (thường bằng cách làm nổi bật hoặc gạch chân một đoạn văn bản)
và sau đó bạn có thể yêu cầu trình duyệt mở chúng. Các URL nhúng này là
một ví dụ về *liên kết hypertext*. Khi bạn yêu cầu trình duyệt mở một
URL nhúng (ví dụ, bằng cách nhấp chuột vào đó), nó sẽ lấy và hiển thị
file được chỉ định. Nhờ đó, bạn rất dễ dàng nhảy từ máy này sang máy
khác trên mạng, theo các liên kết đến đủ loại thông tin. Một khi bạn có
cách nhúng liên kết vào tài liệu và cho phép người dùng theo liên kết đó
để lấy tài liệu khác, bạn đã có nền tảng của một hệ thống hypertext.

Khi bạn yêu cầu trình duyệt xem một trang, trình duyệt (client) sẽ lấy
trang từ server bằng HTTP, vốn truyền thống chạy trên TCP. Giống như
SMTP, HTTP là một giao thức hướng văn bản. Ở lõi, HTTP là một giao thức
request/response, trong đó mỗi thông điệp có dạng tổng quát

::

   START_LINE <CRLF>
   MESSAGE_HEADER <CRLF>
   <CRLF>
   MESSAGE_BODY <CRLF>

trong đó, như trước, ``<CRLF>`` là carriage-return+line-feed. Dòng đầu
tiên (``START_LINE``) chỉ ra đây là thông điệp yêu cầu hay phản hồi. Thực
chất, nó xác định “thủ tục từ xa” sẽ được thực thi (nếu là thông điệp
yêu cầu), hoặc *trạng thái* của yêu cầu (nếu là thông điệp phản hồi).
Tập các dòng tiếp theo xác định một tập hợp các tùy chọn và tham số bổ
sung cho yêu cầu hoặc phản hồi. Có thể có không hoặc nhiều dòng
``MESSAGE_HEADER``—tập này kết thúc bằng một dòng trống—mỗi dòng trông
giống như một dòng header trong email. HTTP định nghĩa nhiều loại header
khác nhau, một số dành cho thông điệp yêu cầu, một số cho thông điệp
phản hồi, một số cho dữ liệu mang trong body. Thay vì liệt kê đầy đủ tất
cả các loại header, chúng tôi chỉ đưa ra một số ví dụ tiêu biểu. Cuối
cùng, sau dòng trống là nội dung thông điệp được yêu cầu
(``MESSAGE_BODY``); phần này là nơi server đặt trang được yêu cầu khi
phản hồi, và thường rỗng đối với thông điệp yêu cầu.

Tại sao HTTP được thiết kế để chạy trên TCP? Các nhà thiết kế không bắt
buộc phải làm vậy, nhưng TCP cung cấp nhiều dịch vụ mà HTTP cần: truyền
tin cậy (ai muốn một trang Web bị thiếu dữ liệu?), điều khiển luồng, và
điều khiển tắc nghẽn. Tuy nhiên, như sẽ thấy bên dưới, có một số vấn đề
nảy sinh khi xây dựng giao thức request/response trên TCP, điều này càng
rõ khi bạn xem xét chi tiết các tương tác giữa giao thức ứng dụng và vận
chuyển. Điều này đã dẫn đến các phiên bản HTTP mới và một giao thức vận
chuyển mới, QUIC, sẽ được thảo luận bên dưới.

Thông điệp yêu cầu
~~~~~~~~~~~~~~~~~~

Dòng đầu tiên của một thông điệp yêu cầu HTTP xác định ba điều: thao tác
sẽ thực hiện, trang Web thao tác sẽ thực hiện trên đó, và phiên bản HTTP
được sử dụng. Mặc dù HTTP định nghĩa nhiều thao tác yêu cầu khác nhau—
bao gồm các thao tác *ghi* cho phép đăng một trang Web lên server—hai
thao tác phổ biến nhất là ``GET`` (lấy trang Web chỉ định) và ``HEAD``
(lấy thông tin trạng thái về trang Web chỉ định). ``GET`` rõ ràng được
dùng khi trình duyệt muốn lấy và hiển thị một trang Web. ``HEAD`` được
dùng để kiểm tra tính hợp lệ của một liên kết hypertext hoặc xem một
trang đã bị thay đổi kể từ lần cuối trình duyệt lấy nó chưa. Tập đầy đủ
các thao tác được tóm tắt ở :numref:`Table %s <tab-ops>`. Nghe có vẻ vô
hại, nhưng lệnh ``POST`` lại là nguồn gốc của nhiều rắc rối (bao gồm cả
spam) trên Internet.

.. _tab-ops:
.. table::  Các thao tác yêu cầu HTTP.
   :align: center
   :widths: auto

   +-----------+-----------------------------------------------------------+
   | Operation | Description                                               |
   +===========+===========================================================+
   | OPTIONS   | Yêu cầu thông tin về các tùy chọn có sẵn                  |
   +-----------+-----------------------------------------------------------+
   | GET       | Lấy tài liệu được xác định trong URL                      |
   +-----------+-----------------------------------------------------------+
   | HEAD      | Lấy thông tin meta về tài liệu xác định trong URL         |
   +-----------+-----------------------------------------------------------+
   | POST      | Gửi thông tin (ví dụ, chú thích) đến server               |
   +-----------+-----------------------------------------------------------+
   | PUT       | Lưu tài liệu dưới URL chỉ định                            |
   +-----------+-----------------------------------------------------------+
   | DELETE    | Xóa URL chỉ định                                          |
   +-----------+-----------------------------------------------------------+
   | TRACE     | Yêu cầu lặp lại thông điệp                                |
   +-----------+-----------------------------------------------------------+
   | CONNECT   | Dùng cho proxy                                            |
   +-----------+-----------------------------------------------------------+

Ví dụ, ``START_LINE``

::

   GET http://www.cs.princeton.edu/index.html HTTP/1.1

nói rằng client muốn server trên host trả về trang có tên ``index.html``.
Ví dụ này dùng URL tuyệt đối. Cũng có thể yêu cầu một đường dẫn trong
``START_LINE`` và chỉ định tên host trong một dòng ``MESSAGE_HEADER``; ví
dụ,

.. code-block:: http

   GET /index.html HTTP/1.1
   Host: www.cs.princeton.edu

Ở đây, ``Host`` là một trong các trường ``MESSAGE_HEADER`` có thể có.
Một trường thú vị khác là ``If-Modified-Since``, cho phép client yêu cầu
có điều kiện một trang Web—server chỉ trả về trang nếu nó đã bị thay đổi
kể từ thời điểm chỉ định trong dòng header đó.

Thông điệp phản hồi
~~~~~~~~~~~~~~~~~~~

Giống như thông điệp yêu cầu, thông điệp phản hồi bắt đầu bằng một dòng
``START_LINE``. Trong trường hợp này, dòng này xác định phiên bản HTTP
được sử dụng, một mã ba chữ số chỉ yêu cầu thành công hay không, và một
chuỗi văn bản giải thích lý do phản hồi. Ví dụ, ``START_LINE``

.. code-block:: http

   HTTP/1.1 202 Accepted

chỉ rằng server đã đáp ứng được yêu cầu, trong khi

.. code-block:: http

   HTTP/1.1 404 Not Found

chỉ rằng server không thể đáp ứng vì không tìm thấy trang. Có năm loại
mã phản hồi tổng quát, với chữ số đầu tiên của mã chỉ loại.
:numref:`Table %s <tab-codes>` tóm tắt năm loại mã này.

.. _tab-codes:
.. table::  Năm loại mã kết quả HTTP.
   :align: center
   :widths: auto

   +------+--------------+------------------------------------------------+
   | Code | Type         | Example Reasons                                |
   +======+==============+================================================+
   | 1xx  | Thông tin    | yêu cầu đã nhận, đang xử lý tiếp               |
   +------+--------------+------------------------------------------------+
   | 2xx  | Thành công   | hành động đã nhận, hiểu và chấp nhận           |
   +------+--------------+------------------------------------------------+
   | 3xx  | Chuyển hướng | cần thực hiện thêm để hoàn tất yêu cầu         |
   +------+--------------+------------------------------------------------+
   | 4xx  | Lỗi client   | yêu cầu sai cú pháp hoặc không thể thực hiện   |
   +------+--------------+------------------------------------------------+
   | 5xx  | Lỗi server   | server không thực hiện được một yêu cầu hợp lệ |
   +------+--------------+------------------------------------------------+

Cũng giống như hậu quả bất ngờ của thông điệp yêu cầu ``POST``, đôi khi
thật ngạc nhiên cách các thông điệp phản hồi khác nhau được sử dụng trong
thực tế. Ví dụ, chuyển hướng yêu cầu (cụ thể là mã 302) hóa ra là một cơ
chế mạnh mẽ đóng vai trò lớn trong Content Distribution Network (CDN) bằng
cách chuyển hướng yêu cầu đến cache gần đó.

Tương tự thông điệp yêu cầu, thông điệp phản hồi có thể chứa một hoặc
nhiều dòng ``MESSAGE_HEADER``. Các dòng này chuyển thêm thông tin về cho
client. Ví dụ, dòng header ``Location`` chỉ rằng URL được yêu cầu hiện có
ở vị trí khác. Như vậy, nếu trang Web của Khoa Khoa học Máy tính Princeton
chuyển từ ``http://www.cs.princeton.edu/index.html`` sang
``http://www.princeton.edu/cs/index.html``, thì server tại địa chỉ cũ có
thể phản hồi như sau

.. code-block:: http

   HTTP/1.1 301 Moved Permanently
   Location: http://www.princeton.edu/cs/index.html

Trong trường hợp phổ biến, thông điệp phản hồi cũng mang theo trang được
yêu cầu. Trang này là tài liệu HTML, nhưng vì có thể mang dữ liệu không
phải văn bản (ví dụ, hình GIF), nó được mã hóa bằng MIME (xem phần trước).
Một số dòng ``MESSAGE_HEADER`` cho biết thuộc tính nội dung trang, bao gồm
(số byte trong nội dung), ``Expires`` (thời điểm nội dung bị coi là lỗi
thời), và (thời điểm nội dung được sửa đổi lần cuối trên server).

Uniform Resource Identifiers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Các URL mà HTTP dùng làm địa chỉ là một loại *Uniform Resource Identifier*
(URI). URI là một chuỗi ký tự xác định một tài nguyên, nơi tài nguyên có
thể là bất kỳ thứ gì có danh tính, như tài liệu, hình ảnh, hoặc dịch vụ.

Định dạng URI cho phép nhiều loại định danh tài nguyên chuyên biệt được
tích hợp vào không gian định danh URI. Phần đầu của URI là *scheme* chỉ
một cách xác định loại tài nguyên nào đó, như ``mailto`` cho địa chỉ email
hoặc ``file`` cho tên file. Phần thứ hai của URI, tách khỏi phần đầu bằng
dấu hai chấm, là *scheme-specific part*. Nó là một định danh tài nguyên
phù hợp với scheme ở phần đầu, như trong các URI ``mailto:santa@northpole.org``
và ``file:///C:/foo.html``.

Một tài nguyên không nhất thiết phải lấy được hoặc truy cập được. Chúng ta
đã thấy một ví dụ về điều này ở chương trước—namespace của extensible markup
language (XML) được xác định bằng URI trông rất giống URL, nhưng thực ra
chúng không phải là *locator* vì chúng không cho bạn biết cách xác định vị
trí cái gì; chúng chỉ cung cấp một định danh toàn cục duy nhất cho namespace.
Không có yêu cầu nào rằng bạn có thể lấy được gì tại URI được dùng làm
namespace mục tiêu của tài liệu XML. Chúng ta sẽ thấy một ví dụ khác về URI
không phải là URL ở phần sau.

Kết nối TCP
~~~~~~~~~~~~~~~

Phiên bản gốc của HTTP (1.0) thiết lập một kết nối TCP riêng biệt cho
mỗi mục dữ liệu được truy xuất từ máy chủ. Không khó để thấy đây là một
cơ chế rất kém hiệu quả: các thông điệp thiết lập và đóng kết nối phải
được trao đổi giữa client và server ngay cả khi tất cả những gì client
muốn làm chỉ là xác minh rằng nó đã có bản sao mới nhất của một trang.
Do đó, việc truy xuất một trang có chứa một số văn bản và một tá biểu
tượng hoặc đồ họa nhỏ khác sẽ dẫn đến 13 kết nối TCP riêng biệt được
thiết lập và đóng lại. :numref:`Hình %s <fig-oldhttp>` cho thấy trình tự
các sự kiện khi lấy một trang chỉ có một đối tượng nhúng. Các đường màu
chỉ các thông điệp TCP, trong khi các đường màu đen chỉ các yêu cầu và
phản hồi HTTP. (Một số TCP ACK không được hiển thị để tránh làm rối
hình vẽ.) Bạn có thể thấy hai lần round trip được dùng để thiết lập kết
nối TCP trong khi ít nhất hai lần nữa được dùng để lấy trang và hình
ảnh. Ngoài tác động đến độ trễ, còn có chi phí xử lý trên máy chủ để xử
lý việc thiết lập và kết thúc kết nối TCP bổ sung.

.. _fig-oldhttp:
.. figure:: figures/f09-04-9780123850591.png
   :width: 500px
   :align: center

   Hành vi HTTP 1.0.

Để khắc phục tình trạng này, HTTP phiên bản 1.1 đã giới thiệu *kết nối
liên tục*—client và server có thể trao đổi nhiều thông điệp
request/response qua cùng một kết nối TCP. Kết nối liên tục có nhiều ưu
điểm. Đầu tiên, chúng loại bỏ chi phí thiết lập kết nối, từ đó giảm tải
cho máy chủ, giảm tải cho mạng do các gói TCP bổ sung, và giảm độ trễ
cảm nhận của người dùng. Thứ hai, vì một client có thể gửi nhiều thông
điệp yêu cầu xuống một kết nối TCP duy nhất, cơ chế cửa sổ nghẽn của
TCP có thể hoạt động hiệu quả hơn. Điều này là do không cần phải trải
qua giai đoạn slow start cho mỗi trang. :numref:`Hình %s <fig-persist>`
cho thấy giao dịch từ :numref:`Hình %s <fig-oldhttp>` sử dụng kết nối
liên tục trong trường hợp kết nối đã được mở (có thể do truy cập trước
đó đến cùng máy chủ).

.. _fig-persist:
.. figure:: figures/f09-05-9780123850591.png
   :width: 500px
   :align: center

   Hành vi HTTP 1.1 với kết nối liên tục.

Tuy nhiên, kết nối liên tục không phải không có giá. Vấn đề là cả client
và server đều không nhất thiết biết nên giữ một kết nối TCP cụ thể mở
bao lâu. Điều này đặc biệt quan trọng ở phía server, nơi có thể được yêu
cầu giữ các kết nối mở thay mặt cho hàng nghìn client. Giải pháp là
server phải đặt thời gian chờ và đóng kết nối nếu không nhận được yêu
cầu nào trên kết nối trong một khoảng thời gian nhất định. Ngoài ra, cả
client và server đều phải theo dõi xem phía bên kia có chọn đóng kết nối
hay không, và họ phải sử dụng thông tin đó như một tín hiệu rằng họ cũng
nên đóng phía của mình. (Nhớ rằng cả hai phía phải đóng kết nối TCP
trước khi nó thực sự kết thúc.) Những lo ngại về sự phức tạp bổ sung này
có thể là một lý do tại sao kết nối liên tục không được sử dụng ngay từ
đầu, nhưng ngày nay người ta chấp nhận rộng rãi rằng lợi ích của kết nối
liên tục vượt xa các nhược điểm.

Mặc dù 1.1 vẫn được hỗ trợ rộng rãi, phiên bản 2.0 đã được IETF phê
duyệt chính thức vào năm 2015. Được gọi là HTTP/2, phiên bản mới này
tương thích ngược với 1.1 (tức là nó sử dụng cùng cú pháp cho các trường
header, mã trạng thái và URI), nhưng nó bổ sung hai tính năng mới.

Đầu tiên là giúp máy chủ web dễ dàng *nén nhỏ* thông tin gửi lại cho
trình duyệt web. Nếu bạn nhìn kỹ vào cấu trúc HTML của một trang web
điển hình, bạn sẽ thấy vô số tham chiếu đến các thành phần nhỏ khác
(như hình ảnh, script, file style) mà trình duyệt cần để hiển thị trang.
Thay vì buộc client phải yêu cầu các thành phần này (về mặt kỹ thuật gọi
là *resource*) trong các yêu cầu tiếp theo, HTTP/2 cung cấp một cơ chế
để server đóng gói các resource cần thiết và chủ động *đẩy* chúng đến
client mà không phải chịu độ trễ round-trip do buộc client phải yêu cầu.
Tính năng này đi kèm với một cơ chế nén giúp giảm số byte cần phải đẩy.
Mục tiêu toàn bộ là giảm thiểu độ trễ mà người dùng cuối trải nghiệm từ
lúc họ nhấp vào một hyperlink cho đến khi trang được chọn được hiển thị
đầy đủ.

Tiến bộ lớn thứ hai của HTTP/2 là cho phép ghép kênh nhiều yêu cầu trên
một kết nối TCP duy nhất. Điều này vượt xa những gì phiên bản 1.1 hỗ
trợ—cho phép một *chuỗi* các yêu cầu tái sử dụng một kết nối TCP—bằng
cách cho phép các yêu cầu này chồng lấn lên nhau. Cách HTTP/2 làm điều
này nghe có vẻ quen thuộc: nó định nghĩa một trừu tượng *kênh* (về mặt
kỹ thuật, các kênh này được gọi là *stream*), cho phép nhiều stream đồng
thời hoạt động tại một thời điểm (mỗi stream được gán một *stream id*
duy nhất), và giới hạn mỗi stream chỉ có một trao đổi request/reply đang
hoạt động tại một thời điểm.

HTTP/3 và QUIC
~~~~~~~~~~~~~~

Như phần thảo luận trước cho thấy, lịch sử của HTTP bao gồm một loạt các
thay đổi tăng dần để tận dụng tốt hơn TCP làm tầng vận chuyển bên dưới.
Nhưng có một vấn đề cơ bản không thể giải quyết triệt để: TCP cung cấp
một trừu tượng luồng byte, trong khi HTTP là một giao thức
request/response. Giải pháp tự nhiên là áp dụng một tầng vận chuyển phù
hợp hơn, nhưng như chúng ta đã thấy ở :ref:`Chương 5 <Chapter 5:
End-to-End Protocols>`, không có giao thức RPC chuẩn nào được chấp nhận
rộng rãi như TCP.

Cuối cùng, giải pháp cho sự không tương thích này là tạo ra một giao
thức vận chuyển mới trong QUIC. QUIC được thiết kế rõ ràng để phù hợp
tốt với các yêu cầu của HTTP, và HTTP/3 tận dụng tầng vận chuyển được
cải tiến này. Ví dụ, QUIC hỗ trợ rõ ràng việc ghép kênh stream ở tầng
vận chuyển. Do đó, một mất mát gói chỉ ảnh hưởng đến việc truyền stream
bị mất, thay vì gây ra sự dừng toàn bộ kết nối TCP trong khi chờ gói bị
mất được truyền lại. Đồng thời, gói bị mất đó cung cấp tín hiệu nghẽn
cho tất cả các stream trong kết nối QUIC. Chúng ta sẽ tìm hiểu QUIC kỹ
hơn ở :ref:`Mục 5.2 <5.2 Reliable Byte Stream (TCP)>`.

Một lợi thế đáng kể khác của QUIC so với TCP là cách nó xử lý các bước
cần thiết để bảo mật một kết nối HTTP. Trong khi việc trao đổi chứng
chỉ và khóa mã hóa diễn ra sau khi thiết lập phiên TCP, QUIC xử lý các
bước này như một phần của việc thiết lập phiên, dẫn đến giảm đáng kể số
lần round-trip cần thiết để thiết lập một kết nối bảo mật trước khi nội
dung đầu tiên được truyền.

HTTP/3 đã được triển khai trong phần lớn các trình duyệt và đang được
triển khai dần trên các máy chủ trên Internet. Vẫn còn rất nhiều máy
chủ chạy HTTP/2 và thậm chí cả HTTP/1.1, vì vậy việc thương lượng phiên
bản có khả năng sẽ là một phần của các triển khai HTTP trong tương lai
gần.

Bộ nhớ đệm (Caching)
~~~~~~~~~~~~~~~~~~~~

Một chiến lược triển khai quan trọng giúp web dễ sử dụng hơn là lưu
bộ nhớ đệm các trang Web. Bộ nhớ đệm mang lại nhiều lợi ích. Từ góc độ
client, một trang có thể được lấy từ bộ nhớ đệm gần đó sẽ được hiển thị
nhanh hơn nhiều so với việc phải lấy từ bên kia thế giới. Từ góc độ
server, có một bộ nhớ đệm chặn và đáp ứng yêu cầu sẽ giảm tải cho máy
chủ.

Bộ nhớ đệm có thể được triển khai ở nhiều nơi khác nhau. Ví dụ, trình
duyệt của người dùng có thể lưu bộ nhớ đệm các trang vừa truy cập và chỉ
cần hiển thị bản sao đã lưu nếu người dùng truy cập lại cùng trang đó.
Một ví dụ khác, một site có thể hỗ trợ một bộ nhớ đệm toàn site. Điều
này cho phép người dùng tận dụng các trang đã được tải xuống trước đó
bởi người dùng khác. Gần hơn ở giữa Internet, các Nhà cung cấp Dịch vụ
Internet (ISP) có thể lưu bộ nhớ đệm các trang.\ [#]_ Lưu ý rằng, trong
trường hợp thứ hai, người dùng trong site có thể biết máy nào đang lưu
bộ nhớ đệm các trang thay mặt cho site, và họ cấu hình trình duyệt để
kết nối trực tiếp đến máy chủ lưu bộ nhớ đệm đó. Nút này đôi khi được
gọi là *proxy*. Ngược lại, các site kết nối đến ISP có thể không biết
rằng ISP đang lưu bộ nhớ đệm các trang. Đơn giản là các yêu cầu HTTP từ
các site khác nhau đi qua cùng một router của ISP. Router này có thể
xem bên trong thông điệp yêu cầu và nhìn vào URL của trang được yêu
cầu. Nếu nó có trang đó trong bộ nhớ đệm, nó sẽ trả về. Nếu không, nó
chuyển tiếp yêu cầu đến server và theo dõi phản hồi trả về theo hướng
ngược lại. Khi điều đó xảy ra, router lưu một bản sao với hy vọng có
thể dùng nó để đáp ứng một yêu cầu trong tương lai.

.. [#] Có khá nhiều vấn đề với kiểu lưu bộ nhớ đệm này, từ kỹ thuật đến
       quy định pháp lý. Một ví dụ về thách thức kỹ thuật là hiệu ứng
       của *đường đi bất đối xứng*, khi yêu cầu đến server và phản hồi
       về client không đi qua cùng một chuỗi router.

Dù các trang được lưu bộ nhớ đệm ở đâu, khả năng lưu bộ nhớ đệm các
trang Web đủ quan trọng để HTTP được thiết kế nhằm làm cho công việc này
dễ dàng hơn. Mấu chốt là bộ nhớ đệm cần đảm bảo không trả về một phiên
bản lỗi thời của trang. Ví dụ, server gán một ngày hết hạn (trường
header ``Expires``) cho mỗi trang nó gửi lại cho client (hoặc cho một bộ
nhớ đệm giữa server và client). Bộ nhớ đệm nhớ ngày này và biết rằng nó
không cần xác minh lại trang mỗi lần được yêu cầu cho đến khi ngày hết
hạn đó đã qua. Sau thời điểm đó (hoặc nếu trường header này không được
thiết lập) bộ nhớ đệm có thể sử dụng thao tác ``HEAD`` hoặc ``GET``
có điều kiện (``GET`` với dòng header) để xác minh rằng nó có bản sao
mới nhất của trang. Nói chung, có một tập hợp các *chỉ thị bộ nhớ đệm*
mà tất cả các cơ chế lưu bộ nhớ đệm dọc theo chuỗi request/response
phải tuân thủ. Các chỉ thị này xác định liệu một tài liệu có thể được
lưu bộ nhớ đệm hay không, có thể lưu trong bao lâu, tài liệu phải mới
đến mức nào, v.v. Chúng ta sẽ xem xét vấn đề liên quan đến CDN—về cơ
bản là các bộ nhớ đệm phân tán—ở phần sau.

9.1.3 Dịch vụ Web (Web Services)
--------------------------------

Cho đến nay chúng ta đã tập trung vào các tương tác giữa con người và
máy chủ web. Ví dụ, một người dùng sử dụng trình duyệt web để tương tác
với máy chủ, và tương tác diễn ra để đáp ứng đầu vào từ người dùng (ví
dụ, bằng cách nhấp vào các liên kết). Tuy nhiên, ngày càng có nhu cầu
về tương tác trực tiếp giữa máy tính với máy tính. Và, cũng như các ứng
dụng được mô tả ở trên cần giao thức, các ứng dụng giao tiếp trực tiếp
với nhau cũng vậy. Chúng ta sẽ kết thúc phần này bằng cách xem xét các
thách thức khi xây dựng số lượng lớn các giao thức ứng dụng-đến-ứng
dụng và một số giải pháp được đề xuất.

Phần lớn động lực cho việc cho phép giao tiếp trực tiếp giữa các ứng
dụng đến từ thế giới kinh doanh. Lịch sử, các tương tác giữa các doanh
nghiệp—các doanh nghiệp hoặc tổ chức khác—thường liên quan đến một số
bước thủ công như điền vào mẫu đơn đặt hàng hoặc gọi điện thoại để xác
định xem một sản phẩm nào đó còn hàng hay không. Ngay cả trong một doanh
nghiệp, cũng phổ biến có các bước thủ công giữa các hệ thống phần mềm
không thể tương tác trực tiếp vì chúng được phát triển độc lập. Ngày
càng nhiều, các tương tác thủ công như vậy đang được thay thế bằng giao
tiếp trực tiếp giữa các ứng dụng. Một ứng dụng đặt hàng tại doanh
nghiệp A sẽ gửi một thông điệp đến một ứng dụng thực hiện đơn hàng tại
doanh nghiệp B, ứng dụng này sẽ phản hồi ngay lập tức cho biết liệu đơn
hàng có thể được thực hiện hay không. Có thể, nếu đơn hàng không thể
được B thực hiện, ứng dụng tại A sẽ ngay lập tức đặt hàng từ nhà cung
cấp khác hoặc yêu cầu báo giá từ một nhóm nhà cung cấp.

Đây là một ví dụ đơn giản về những gì chúng ta đang nói đến. Giả sử bạn
mua một cuốn sách tại một nhà bán lẻ trực tuyến như Amazon. Sau khi
cuốn sách của bạn đã được gửi đi, Amazon có thể gửi cho bạn số theo dõi
trong email, và sau đó bạn có thể truy cập trang web của công ty vận
chuyển—\ ``http://www.fedex.com``, chẳng hạn—và theo dõi kiện hàng.
Tuy nhiên, bạn cũng có thể theo dõi kiện hàng trực tiếp từ trang web
Amazon.com. Để làm được điều này, Amazon phải có khả năng gửi một truy
vấn đến FedEx, theo định dạng mà FedEx hiểu, diễn giải kết quả, và hiển
thị nó trên một trang Web có thể chứa thông tin khác về đơn hàng của
bạn. Đằng sau trải nghiệm người dùng khi nhận được tất cả thông tin về
đơn hàng cùng lúc trên trang Web Amazon.com là thực tế rằng Amazon và
FedEx phải có một giao thức để trao đổi thông tin cần thiết để theo dõi
kiện hàng—gọi nó là Giao thức Theo dõi Kiện hàng. Rõ ràng là có quá
nhiều giao thức tiềm năng kiểu này nên chúng ta cần có một số công cụ
để đơn giản hóa việc đặc tả và xây dựng chúng.

Các ứng dụng mạng, ngay cả những ứng dụng vượt qua ranh giới tổ chức,
không phải là mới—email và duyệt web đều vượt qua các ranh giới đó. Điều
mới ở đây là quy mô. Không phải quy mô về kích thước mạng, mà là quy mô
về số lượng các loại ứng dụng mạng khác nhau. Cả đặc tả giao thức và
việc triển khai các giao thức đó cho các ứng dụng truyền thống như thư
điện tử và truyền tệp thường được phát triển bởi một nhóm nhỏ các chuyên
gia mạng. Để cho phép số lượng lớn các ứng dụng mạng tiềm năng được phát
triển nhanh chóng, cần phải có một số công nghệ giúp đơn giản hóa và tự
động hóa nhiệm vụ thiết kế và triển khai giao thức ứng dụng.

Hai kiến trúc đã được đề xuất như các giải pháp cho vấn đề này. Cả hai
kiến trúc đều được gọi là *Dịch vụ Web* (Web Services), lấy tên từ thuật
ngữ chỉ các ứng dụng riêng lẻ cung cấp dịch vụ truy cập từ xa cho các
ứng dụng client để tạo thành các ứng dụng mạng. Các thuật ngữ được sử
dụng như cách gọi tắt không chính thức để phân biệt hai kiến trúc Dịch
vụ Web là *SOAP* và *REST*. Chúng ta sẽ bàn về ý nghĩa kỹ thuật của các
thuật ngữ này ngay sau đây.

Cách tiếp cận của kiến trúc SOAP đối với vấn đề này là làm cho việc tạo
ra các giao thức được tùy chỉnh cho từng ứng dụng mạng trở nên khả thi,
ít nhất về mặt lý thuyết. Các yếu tố then chốt của cách tiếp cận này là
một khung cho đặc tả giao thức, các bộ công cụ phần mềm để tự động tạo
ra các triển khai giao thức từ đặc tả, và các đặc tả từng phần dạng mô
đun có thể tái sử dụng giữa các giao thức.

Cách tiếp cận của kiến trúc REST đối với vấn đề này là coi từng Dịch vụ
Web như một tài nguyên của World Wide Web—được xác định bởi URI và truy
cập qua HTTP. Về cơ bản, kiến trúc REST chính là kiến trúc Web. Các ưu
điểm của kiến trúc Web bao gồm tính ổn định và khả năng mở rộng đã được
chứng minh (theo nghĩa quy mô mạng). Có thể coi là một điểm yếu khi HTTP
không phù hợp với phong cách thủ tục hoặc hướng thao tác thông thường
khi gọi một dịch vụ từ xa. Tuy nhiên, những người ủng hộ REST lập luận
rằng các dịch vụ phong phú vẫn có thể được cung cấp bằng cách sử dụng
phong cách hướng dữ liệu hoặc truyền tài liệu mà HTTP rất phù hợp.

Giao thức Ứng dụng Tùy chỉnh (WSDL, SOAP)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Kiến trúc thường được gọi không chính thức là SOAP dựa trên *Ngôn ngữ
Mô tả Dịch vụ Web* (WSDL) và *SOAP*.\ [#]_ Cả hai tiêu chuẩn này đều do
World Wide Web Consortium (W3C) ban hành. Đây là kiến trúc mà mọi người
thường nghĩ đến khi sử dụng thuật ngữ Dịch vụ Web mà không có bổ nghĩa
nào phía trước. Vì các tiêu chuẩn này vẫn đang phát triển, nên phần
trình bày ở đây chỉ là một ảnh chụp nhanh.

.. [#] Mặc dù tên *SOAP* ban đầu là một từ viết tắt, nhưng hiện tại nó
       không còn đại diện cho cụm từ nào nữa.

WSDL và SOAP là các khung để đặc tả và triển khai các giao thức ứng dụng
và giao thức vận chuyển, tương ứng. Chúng thường được sử dụng cùng nhau,
mặc dù điều đó không bắt buộc. WSDL được dùng để đặc tả các chi tiết
cụ thể của ứng dụng như các thao tác nào được hỗ trợ, định dạng dữ liệu
ứng dụng để gọi hoặc phản hồi các thao tác đó, và liệu một thao tác có
liên quan đến phản hồi hay không. Vai trò của SOAP là giúp dễ dàng định
nghĩa một giao thức vận chuyển với chính xác các ngữ nghĩa mong muốn về
các tính năng giao thức như độ tin cậy và bảo mật.

Cả WSDL và SOAP chủ yếu bao gồm một ngôn ngữ đặc tả giao thức. Cả hai
ngôn ngữ đều dựa trên XML với mục tiêu làm cho các đặc tả dễ tiếp cận
với các công cụ phần mềm như trình biên dịch stub và dịch vụ thư mục.
Trong thế giới có nhiều giao thức tùy chỉnh, hỗ trợ tự động hóa việc tạo
ra các triển khai là rất quan trọng để tránh phải tự tay hiện thực từng
giao thức. Phần mềm hỗ trợ thường ở dạng các bộ công cụ và máy chủ ứng
dụng do các nhà cung cấp bên thứ ba phát triển, cho phép các nhà phát
triển Dịch vụ Web riêng lẻ tập trung hơn vào bài toán nghiệp vụ cần giải
quyết (chẳng hạn như theo dõi kiện hàng mà khách hàng đã mua).

Định nghĩa Giao thức Ứng dụng
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

WSDL đã chọn mô hình *thao tác* thủ tục cho các giao thức ứng dụng. Một
giao diện Dịch vụ Web trừu tượng bao gồm một tập hợp các thao tác có tên,
mỗi thao tác đại diện cho một tương tác đơn giản giữa client và Dịch vụ
Web. Một thao tác tương tự như một thủ tục có thể gọi từ xa trong hệ
thống RPC. Một ví dụ từ W3C’s WSDL Primer là một Dịch vụ Web đặt phòng
khách sạn với hai thao tác, CheckAvailability và MakeReservation.

Mỗi thao tác xác định một *Mẫu Trao đổi Thông điệp* (MEP) cho biết trình
tự các thông điệp sẽ được truyền, bao gồm cả các thông điệp lỗi sẽ được
gửi khi có lỗi làm gián đoạn luồng thông điệp. Một số MEP được định
nghĩa sẵn, và có thể định nghĩa các MEP tùy chỉnh mới, nhưng trên thực
tế chỉ có hai MEP được sử dụng: **In-Only** (một thông điệp từ client
đến dịch vụ) và **In-Out** (một yêu cầu từ client và một phản hồi tương
ứng từ dịch vụ). Các mẫu này có lẽ rất quen thuộc, và cho thấy rằng chi
phí hỗ trợ tính linh hoạt của MEP có thể lớn hơn lợi ích.

Các MEP là các mẫu có chỗ trống thay vì các kiểu hoặc định dạng thông
điệp cụ thể, vì vậy một phần của việc định nghĩa một thao tác là xác
định các định dạng thông điệp nào sẽ được ánh xạ vào các chỗ trống trong
mẫu. Định dạng thông điệp không được định nghĩa ở mức bit như các giao
thức mà chúng ta đã bàn. Thay vào đó, chúng được định nghĩa như một mô
hình dữ liệu trừu tượng sử dụng XML. XML Schema cung cấp một tập hợp các
kiểu dữ liệu nguyên thủy và các cách định nghĩa kiểu dữ liệu phức hợp.
Dữ liệu tuân theo định dạng do XML Schema định nghĩa—mô hình dữ liệu
trừu tượng của nó—có thể được biểu diễn cụ thể bằng XML, hoặc có thể sử
dụng một biểu diễn khác, như biểu diễn “nhị phân” Fast Infoset.

WSDL tách biệt rõ ràng các phần của giao thức có thể được đặc tả trừu
tượng—thao tác, MEP, định dạng thông điệp trừu tượng—khỏi các phần phải
cụ thể. Phần cụ thể của WSDL xác định một giao thức nền, cách các MEP
được ánh xạ lên nó, và định dạng bit-level nào được sử dụng cho thông
điệp trên đường truyền. Phần này của đặc tả được gọi là *binding*, mặc
dù mô tả nó là một triển khai, hoặc ánh xạ lên một triển khai thì đúng
hơn. WSDL có các binding định nghĩa sẵn cho HTTP và các giao thức dựa
trên SOAP, với các tham số cho phép nhà thiết kế giao thức tinh chỉnh
việc ánh xạ lên các giao thức đó. Có một khung để định nghĩa các binding
mới, nhưng các giao thức SOAP chiếm ưu thế.

Một khía cạnh then chốt về cách WSDL giảm nhẹ vấn đề đặc tả số lượng lớn
giao thức là thông qua việc tái sử dụng những gì về cơ bản là các mô-đun
đặc tả. Đặc tả WSDL của một Dịch vụ Web có thể được tạo thành từ nhiều
tài liệu WSDL, và từng tài liệu WSDL riêng lẻ cũng có thể được sử dụng
trong các đặc tả Dịch vụ Web khác. Tính mô-đun này giúp việc phát triển
một đặc tả dễ dàng hơn và giúp đảm bảo rằng, nếu hai đặc tả được cho là
có một số thành phần giống hệt nhau (ví dụ, để chúng có thể được hỗ trợ
bởi cùng một công cụ), thì các thành phần đó thực sự giống hệt nhau.
Tính mô-đun này, cùng với các quy tắc mặc định của WSDL, cũng giúp giữ
cho các đặc tả không trở nên quá dài dòng đối với các nhà thiết kế giao
thức.

Tính mô-đun của WSDL sẽ quen thuộc với bất kỳ ai đã phát triển các phần
mềm có quy mô vừa phải trở lên. Một tài liệu WSDL không nhất thiết phải
là một đặc tả hoàn chỉnh; nó có thể, ví dụ, chỉ định nghĩa một định dạng
thông điệp duy nhất. Các đặc tả từng phần được nhận diện duy nhất bằng
cách sử dụng XML Namespace; mỗi tài liệu WSDL chỉ định URI của một
*target namespace*, và bất kỳ định nghĩa mới nào trong tài liệu đều được
đặt tên trong ngữ cảnh của namespace đó. Một tài liệu WSDL có thể kết
hợp các thành phần của tài liệu khác bằng cách *include* tài liệu thứ
hai nếu cả hai cùng chia sẻ target namespace hoặc *import* nếu target
namespace khác nhau.

Định nghĩa Giao thức Vận chuyển
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Mặc dù đôi khi SOAP được gọi là một giao thức, nhưng tốt hơn nên coi nó
là một khung để định nghĩa giao thức. Như đặc tả SOAP 1.2 giải thích,
“SOAP cung cấp một khung nhắn tin đơn giản mà chức năng cốt lõi liên
quan đến khả năng mở rộng.” SOAP sử dụng nhiều chiến lược giống như
WSDL, bao gồm các định dạng thông điệp được định nghĩa bằng XML Schema,
binding đến các giao thức nền, Mẫu Trao đổi Thông điệp, và các thành
phần đặc tả có thể tái sử dụng được nhận diện bằng XML namespace.

SOAP được dùng để định nghĩa các giao thức vận chuyển với chính xác các
tính năng cần thiết để hỗ trợ một giao thức ứng dụng cụ thể. SOAP hướng
tới việc làm cho việc định nghĩa nhiều giao thức như vậy trở nên khả thi
bằng cách sử dụng các thành phần có thể tái sử dụng. Mỗi thành phần nắm
bắt thông tin header và logic cần thiết để hiện thực một tính năng cụ
thể. Để định nghĩa một giao thức với một tập hợp tính năng nhất định,
chỉ cần kết hợp các thành phần tương ứng. Hãy xem kỹ hơn khía cạnh này
của SOAP.

SOAP 1.2 giới thiệu một trừu tượng *feature*, mà đặc tả mô tả như sau:

   *Một feature của SOAP là một phần mở rộng của khung nhắn tin SOAP.
   Mặc dù SOAP không đặt ra ràng buộc nào về phạm vi tiềm năng của các
   feature như vậy, các feature ví dụ có thể bao gồm “độ tin cậy,”
   “bảo mật,” “liên kết,” “định tuyến,” và các mẫu trao đổi thông điệp
   (MEP) như request/response, một chiều, và hội thoại peer-to-peer.*

Một đặc tả feature của SOAP phải bao gồm:

-  Một URI nhận diện feature

-  Thông tin trạng thái và xử lý, được mô tả trừu tượng, cần thiết tại
   mỗi nút SOAP để hiện thực feature

-  Thông tin cần chuyển tiếp đến nút tiếp theo

-  (Nếu feature là một MEP) vòng đời và quan hệ thời gian/nguyên nhân
   của các thông điệp được trao đổi—ví dụ, phản hồi theo sau yêu cầu và
   được gửi đến nguồn gốc của yêu cầu

Lưu ý rằng việc hình thức hóa khái niệm feature của giao thức này khá
thấp; nó gần như là một thiết kế.

Với một tập hợp feature, có hai chiến lược để định nghĩa một giao thức
SOAP sẽ hiện thực chúng. Một là bằng cách xếp lớp: binding SOAP lên một
giao thức nền sao cho có thể dẫn xuất các feature. Ví dụ, chúng ta có
thể có được một giao thức request/response bằng cách binding SOAP lên
HTTP, với một yêu cầu SOAP trong một yêu cầu HTTP và một phản hồi SOAP
trong một phản hồi HTTP. Vì đây là một ví dụ phổ biến, SOAP có một
binding định nghĩa sẵn cho HTTP; các binding mới có thể được định nghĩa
bằng SOAP Protocol Binding Framework.

Cách thứ hai và linh hoạt hơn để hiện thực feature liên quan đến *header
block*. Một thông điệp SOAP bao gồm một Envelope, chứa một Header mà
trong đó chứa các header block, và một Body, chứa payload gửi đến người
nhận cuối cùng. Cấu trúc thông điệp này được minh họa trong
:numref:`Hình %s <fig-soapFormat>`.

.. _fig-soapFormat:
.. figure:: figures/f09-06-9780123850591.png
   :width: 250px
   :align: center

   Cấu trúc thông điệp SOAP.

Bây giờ bạn nên quen với ý tưởng rằng một số thông tin header tương ứng
với các feature cụ thể. Chữ ký số được dùng để hiện thực xác thực, số
thứ tự được dùng cho độ tin cậy, và checksum được dùng để phát hiện lỗi
thông điệp. Một header block của SOAP nhằm đóng gói thông tin header
tương ứng với một feature cụ thể. Sự tương ứng này không phải lúc nào
cũng một-một vì nhiều header block có thể liên quan đến một feature, hoặc
một header block có thể được dùng trong nhiều feature. Một *module SOAP*
là một đặc tả về cú pháp và ngữ nghĩa của một hoặc nhiều header block.
Mỗi module nhằm cung cấp một hoặc nhiều feature và phải khai báo các
feature mà nó hiện thực.

Mục tiêu của các module SOAP là có thể tạo ra một giao thức với một tập
hợp feature chỉ bằng cách đưa vào mỗi đặc tả module tương ứng. Nếu giao
thức của bạn yêu cầu ngữ nghĩa at-most-once và xác thực, hãy đưa các
module tương ứng vào đặc tả của bạn. Điều này đại diện cho một cách tiếp
cận mới trong việc mô-đun hóa các dịch vụ giao thức, một thay thế cho
việc xếp lớp giao thức mà chúng ta đã thấy trong suốt cuốn sách này. Nó
giống như làm phẳng một chuỗi các lớp giao thức thành một giao thức duy
nhất, nhưng theo cách có cấu trúc. Vẫn còn phải xem các feature và module
của SOAP, được giới thiệu trong phiên bản 1.2 của SOAP, sẽ hoạt động tốt
đến đâu trong thực tế. Điểm yếu chính của sơ đồ này là các module có thể
can thiệp lẫn nhau. Một đặc tả module được yêu cầu phải chỉ rõ bất kỳ
tương tác *đã biết* nào với các module SOAP khác, nhưng rõ ràng điều đó
không giúp giải quyết nhiều vấn đề. Mặt khác, một tập hợp nhỏ các feature
và module cốt lõi cung cấp các thuộc tính quan trọng nhất có thể đủ nhỏ
để được biết rõ và hiểu rõ.

Chuẩn hóa Giao thức Dịch vụ Web
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Như chúng ta đã nói, WSDL và SOAP không phải là giao thức; chúng là các
tiêu chuẩn để *đặc tả* giao thức. Để các doanh nghiệp khác nhau triển
khai Dịch vụ Web có thể tương tác với nhau, chỉ đồng ý sử dụng WSDL và
SOAP để định nghĩa giao thức của họ là chưa đủ; họ phải đồng ý—chuẩn
hóa—các giao thức cụ thể. Ví dụ, bạn có thể hình dung rằng các nhà bán
lẻ trực tuyến và các công ty vận chuyển có thể muốn chuẩn hóa một giao
thức để trao đổi thông tin, tương tự như ví dụ đơn giản về theo dõi kiện
hàng ở đầu phần này. Việc chuẩn hóa này rất quan trọng cho việc hỗ trợ
công cụ cũng như khả năng tương tác. Tuy nhiên, các ứng dụng mạng khác
nhau trong kiến trúc này nhất thiết phải khác nhau ít nhất ở định dạng
thông điệp và các thao tác mà chúng sử dụng.

Sự căng thẳng giữa chuẩn hóa và tùy biến này được giải quyết bằng cách
thiết lập các tiêu chuẩn từng phần gọi là *profile*. Một profile là một
tập hợp các hướng dẫn thu hẹp hoặc ràng buộc các lựa chọn có sẵn trong
WSDL, SOAP và các tiêu chuẩn khác có thể được tham chiếu khi định nghĩa
một giao thức. Chúng cũng có thể đồng thời giải quyết các điểm mơ hồ
hoặc lỗ hổng trong các tiêu chuẩn đó. Trên thực tế, một profile thường
hợp thức hóa một tiêu chuẩn *de facto* đang nổi lên.

Profile rộng nhất và được áp dụng rộng rãi nhất được gọi là *WS-I Basic
Profile*. Nó được đề xuất bởi Web Services Interoperability Organization
(WS-I), một liên minh ngành công nghiệp, trong khi WSDL và SOAP được
W3C đặc tả. Basic Profile giải quyết một số lựa chọn cơ bản nhất khi
định nghĩa một Dịch vụ Web. Đáng chú ý nhất là nó yêu cầu WSDL chỉ được
ràng buộc với SOAP và SOAP chỉ được ràng buộc với HTTP và sử dụng phương
thức HTTP POST. Nó cũng chỉ rõ phiên bản nào của WSDL và SOAP phải được
sử dụng.

*WS-I Basic Security Profile* bổ sung các ràng buộc bảo mật cho Basic
Profile bằng cách chỉ rõ cách sử dụng tầng SSL/TLS và yêu cầu tuân thủ
*WS-Security* (Web Services Security). WS-Security chỉ rõ cách sử dụng
các kỹ thuật hiện có như chứng chỉ khóa công khai X.509 và Kerberos để
cung cấp các tính năng bảo mật trong các giao thức SOAP.

WS-Security chỉ là tiêu chuẩn đầu tiên trong một loạt các tiêu chuẩn ở
tầng SOAP ngày càng tăng do liên minh ngành công nghiệp OASIS (Tổ chức
Thúc đẩy Tiêu chuẩn Thông tin Có cấu trúc) thiết lập. Các tiêu chuẩn
được biết đến chung là *WS-*\ \* bao gồm WS-Reliability,
WS-ReliableMessaging, WS-Coordination, và WS-AtomicTransaction.

Một Giao thức Ứng dụng Tổng quát (REST)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Kiến trúc Dịch vụ Web WSDL/SOAP dựa trên giả định rằng cách tốt nhất để
tích hợp các ứng dụng qua mạng là thông qua các giao thức được tùy chỉnh
cho từng ứng dụng. Kiến trúc đó được thiết kế để làm cho việc đặc tả và
triển khai tất cả các giao thức đó trở nên khả thi. Ngược lại, kiến trúc
Dịch vụ Web REST dựa trên giả định rằng cách tốt nhất để tích hợp các
ứng dụng qua mạng là áp dụng lại mô hình nền tảng của kiến trúc World
Wide Web. Mô hình này, do kiến trúc sư Web Roy Fielding đề xuất, được gọi
là *REpresentational State Transfer* (REST). Không cần một kiến trúc REST
mới cho Dịch vụ Web—kiến trúc Web hiện tại là đủ, mặc dù có thể cần một
số mở rộng. Trong kiến trúc Web, từng Dịch vụ Web được coi là tài nguyên
được xác định bởi URI và truy cập qua HTTP—một giao thức ứng dụng tổng
quát duy nhất với một sơ đồ định địa chỉ tổng quát duy nhất.

Trong khi WSDL có các thao tác do người dùng định nghĩa, REST sử dụng tập
nhỏ các phương thức HTTP có sẵn, như ``GET`` và ``POST`` (xem
:numref:`Bảng %s <tab-ops>`). Vậy làm thế nào các phương thức đơn giản
này có thể cung cấp giao diện cho một Dịch vụ Web phong phú? Bằng cách
áp dụng mô hình REST, trong đó độ phức tạp được chuyển từ giao thức sang
payload. Payload là một biểu diễn trạng thái trừu tượng của tài nguyên.
Ví dụ, một ``GET`` có thể trả về một biểu diễn trạng thái hiện tại của
tài nguyên, và một ``POST`` có thể gửi một biểu diễn trạng thái mong
muốn của tài nguyên.

Biểu diễn trạng thái của tài nguyên là trừu tượng; nó không nhất thiết
phải giống cách tài nguyên thực sự được hiện thực bởi một thể hiện Dịch
vụ Web cụ thể. Không cần phải truyền toàn bộ trạng thái tài nguyên trong
mỗi thông điệp. Kích thước thông điệp có thể được giảm bằng cách chỉ
truyền các phần trạng thái quan tâm (ví dụ, chỉ các phần đang được sửa
đổi). Và, vì Dịch vụ Web chia sẻ một giao thức và không gian địa chỉ với
các tài nguyên web khác, các phần trạng thái có thể được truyền bằng
tham chiếu—bằng URI—ngay cả khi chúng là các Dịch vụ Web khác.

Cách tiếp cận này được tóm tắt tốt nhất là phong cách hướng dữ liệu hoặc
truyền tài liệu, trái ngược với phong cách thủ tục. Định nghĩa một giao
thức ứng dụng trong kiến trúc này bao gồm định nghĩa cấu trúc tài liệu
(tức là biểu diễn trạng thái). XML và JavaScript Object Notation (JSON)
nhẹ hơn là các ngôn ngữ trình bày được sử dụng thường xuyên nhất cho
trạng thái này. Khả năng tương tác phụ thuộc vào sự đồng thuận giữa một
Dịch vụ Web và các client của nó về biểu diễn trạng thái. Tất nhiên, điều
này cũng đúng trong kiến trúc SOAP; một Dịch vụ Web và client của nó phải
đồng thuận về định dạng payload. Sự khác biệt là trong kiến trúc SOAP,
khả năng tương tác còn phụ thuộc vào sự đồng thuận về giao thức; trong
kiến trúc REST, giao thức luôn là HTTP, nên nguồn gốc của các vấn đề về
khả năng tương tác được loại bỏ.

Một trong những điểm bán hàng của REST là nó tận dụng hạ tầng đã được
triển khai để hỗ trợ Web. Ví dụ, các proxy Web có thể thực thi bảo mật
hoặc lưu bộ nhớ đệm thông tin. Các mạng phân phối nội dung (CDN) hiện có
có thể được sử dụng để hỗ trợ các ứng dụng RESTful.

So với WSDL/SOAP, Web đã có thời gian để các tiêu chuẩn ổn định và chứng
minh rằng nó mở rộng rất tốt. Nó cũng đi kèm với một số bảo mật dưới
dạng Secure Socket Layer (SSL)/Transport Layer Security (TLS). Web và
REST cũng có thể có lợi thế về khả năng tiến hóa. Mặc dù các *framework*
WSDL và SOAP rất linh hoạt về những tính năng và binding mới có thể đưa
vào định nghĩa giao thức, sự linh hoạt đó trở nên không liên quan khi
giao thức đã được định nghĩa. Các giao thức chuẩn hóa như HTTP được thiết
kế với khả năng mở rộng theo cách tương thích ngược. Khả năng mở rộng
của HTTP thể hiện ở các header, phương thức mới, và loại nội dung mới.
Các nhà thiết kế giao thức sử dụng WSDL/SOAP cần thiết kế khả năng mở
rộng như vậy vào từng giao thức tùy chỉnh của họ. Tất nhiên, các nhà
thiết kế biểu diễn trạng thái trong kiến trúc REST cũng phải thiết kế
cho khả năng tiến hóa.

Một lĩnh vực mà WSDL/SOAP có thể có lợi thế là trong việc thích nghi hoặc
bọc các ứng dụng “legacy” đã viết trước đó để phù hợp với Dịch vụ Web.
Đây là một điểm quan trọng vì hầu hết Dịch vụ Web trong tương lai gần sẽ
dựa trên các ứng dụng legacy. Các ứng dụng này thường có giao diện thủ
tục dễ ánh xạ hơn vào các thao tác của WSDL so với trạng thái của REST.
Cuộc cạnh tranh REST so với WSDL/SOAP có thể sẽ phụ thuộc vào việc dễ
hay khó khi xây dựng giao diện kiểu REST cho từng Dịch vụ Web. Chúng ta
có thể thấy rằng một số Dịch vụ Web phù hợp hơn với WSDL/SOAP và số khác
với REST.

Nhà bán lẻ trực tuyến Amazon, thực tế, là một trong những người áp dụng
Dịch vụ Web sớm (2002). Thú vị là, Amazon đã làm cho hệ thống của mình
có thể truy cập công khai qua *cả hai* kiến trúc Dịch vụ Web, và theo
một số báo cáo, phần lớn các nhà phát triển sử dụng giao diện REST. Tất
nhiên, đây chỉ là một dữ liệu và có thể phản ánh các yếu tố đặc thù của
Amazon.

Từ Dịch vụ Web đến Dịch vụ Đám mây
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Nếu Dịch vụ Web là cách chúng ta gọi khi máy chủ web triển khai ứng dụng
của tôi gửi một yêu cầu đến máy chủ web triển khai ứng dụng của bạn, thì
chúng ta gọi là gì khi cả hai cùng đưa ứng dụng của mình lên đám mây để
có thể hỗ trợ tải công việc mở rộng? Chúng ta có thể gọi cả hai là
*Dịch vụ Đám mây* nếu muốn, nhưng đó có phải là một sự phân biệt không
có sự khác biệt? Còn tùy.

Việc chuyển một tiến trình máy chủ từ một máy vật lý chạy trong phòng
máy của tôi sang một máy ảo chạy trong trung tâm dữ liệu của nhà cung
cấp đám mây chuyển trách nhiệm duy trì máy hoạt động từ quản trị viên
hệ thống của tôi sang đội vận hành của nhà cung cấp đám mây, nhưng ứng
dụng vẫn được thiết kế theo kiến trúc Dịch vụ Web. Mặt khác, nếu ứng
dụng được thiết kế từ đầu để chạy trên một nền tảng đám mây có khả
năng mở rộng, ví dụ bằng cách tuân theo *kiến trúc micro-services*,
thì chúng ta gọi ứng dụng đó là *cloud native*. Vậy sự khác biệt quan
trọng là cloud native so với dịch vụ web cũ được triển khai trên đám
mây.

Chúng ta đã thấy sơ qua kiến trúc micro-services ở Chương 5 khi mô tả
gRPC, và mặc dù rất khó để khẳng định chắc chắn micro-services vượt
trội hơn dịch vụ web, xu hướng hiện tại trong ngành gần như chắc chắn
nghiêng về micro-services. Có lẽ thú vị hơn là cuộc tranh luận đang
diễn ra về REST+Json so với gRPC+Protbufs như cơ chế RPC ưa thích để
triển khai micro-services. Hãy nhớ rằng cả hai đều chạy trên HTTP, chúng
tôi để lại cho bạn đọc bài tập chọn một bên và bảo vệ quan điểm của
mình.
