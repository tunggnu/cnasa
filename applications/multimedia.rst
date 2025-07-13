9.2 Ứng dụng Đa phương tiện
===========================

Cũng giống như các ứng dụng truyền thống được mô tả ở phần trước,
các ứng dụng đa phương tiện như điện thoại và hội nghị truyền hình
cần có các giao thức riêng. Phần lớn kinh nghiệm ban đầu trong việc
thiết kế các giao thức cho ứng dụng đa phương tiện đến từ các công cụ
MBone—các ứng dụng như ``vat`` và ``vic`` được phát triển cho
MBone, một mạng phủ hỗ trợ IP multicast để cho phép hội nghị nhiều
bên. (Sẽ nói thêm về mạng phủ bao gồm cả MBone ở phần tiếp theo.)
Ban đầu, mỗi ứng dụng tự triển khai giao thức (hoặc các giao thức)
riêng của mình, nhưng sau đó nhận ra rằng nhiều ứng dụng đa phương
tiện có các yêu cầu chung. Điều này cuối cùng đã dẫn đến sự phát triển
của một số giao thức đa năng dành cho các ứng dụng đa phương tiện.

Chúng ta đã thấy một số giao thức mà các ứng dụng đa phương tiện sử
dụng. Giao thức Truyền tải Thời gian Thực (RTP) cung cấp nhiều chức
năng chung cho các ứng dụng đa phương tiện như truyền tải thông tin
thời gian và xác định các phương thức mã hóa và loại phương tiện của
một ứng dụng.

Giao thức Đặt trước Tài nguyên (RSVP) có thể được sử dụng để yêu cầu
phân bổ tài nguyên trong mạng nhằm đảm bảo chất lượng dịch vụ (QoS)
mong muốn cho một ứng dụng. Chúng ta sẽ xem cách phân bổ tài nguyên
tương tác với các khía cạnh khác của ứng dụng đa phương tiện ở phần
sau của mục này.

Ngoài các giao thức cho truyền tải đa phương tiện và phân bổ tài
nguyên, nhiều ứng dụng đa phương tiện cũng cần một giao thức báo hiệu
hoặc giao thức *điều khiển phiên*. Ví dụ, giả sử bạn muốn thực hiện
các cuộc gọi điện thoại qua Internet (Voice over IP, hay VoIP). Bạn
cần một cơ chế để thông báo cho người nhận dự định của cuộc gọi rằng
bạn muốn nói chuyện với cô ấy, chẳng hạn bằng cách gửi một thông báo
đến một thiết bị đa phương tiện nào đó để nó phát ra âm thanh chuông.
Bạn cũng muốn hỗ trợ các tính năng như chuyển tiếp cuộc gọi, gọi ba
bên, v.v. Giao thức Khởi tạo Phiên (SIP) và H.323 là các ví dụ về các
giao thức giải quyết các vấn đề điều khiển phiên; chúng ta sẽ bắt đầu
bàn về các ứng dụng đa phương tiện bằng cách xem xét các giao thức này.

9.2.1 Điều khiển phiên và điều khiển cuộc gọi (SDP, SIP, H.323)
---------------------------------------------------------------

Để hiểu một số vấn đề của điều khiển phiên, hãy xem xét vấn đề sau.
Giả sử bạn muốn tổ chức một hội nghị truyền hình vào một thời điểm
nhất định và cung cấp nó cho nhiều người tham gia. Có thể bạn đã quyết
định mã hóa luồng video bằng chuẩn MPEG-2, sử dụng địa chỉ multicast
IP 224.1.1.1 để truyền dữ liệu, và gửi nó bằng RTP qua cổng UDP số
4000. Làm thế nào để bạn cung cấp tất cả thông tin đó cho những người
tham gia dự kiến? Một cách là đưa tất cả thông tin đó vào email và gửi
đi, nhưng lý tưởng là nên có một định dạng và giao thức chuẩn để phổ
biến loại thông tin này. IETF đã định nghĩa các giao thức cho mục đích
này. Các giao thức đã được định nghĩa bao gồm

-  Giao thức Mô tả Phiên (SDP)

-  Giao thức Thông báo Phiên (SAP)

-  Giao thức Khởi tạo Phiên (SIP)

-  Giao thức Điều khiển Hội nghị Đơn giản (SCCP)

Bạn có thể nghĩ rằng có quá nhiều giao thức cho một nhiệm vụ có vẻ đơn
giản, nhưng có nhiều khía cạnh của vấn đề và nhiều tình huống khác
nhau cần giải quyết. Ví dụ, có sự khác biệt giữa việc thông báo rằng
một phiên hội nghị nào đó sẽ được tổ chức trên MBone (việc này sẽ được
thực hiện bằng SDP và SAP) và việc cố gắng thực hiện một cuộc gọi điện
thoại Internet cho một người dùng nào đó vào một thời điểm cụ thể (có
thể thực hiện bằng SDP và SIP). Trong trường hợp đầu tiên, bạn có thể
coi nhiệm vụ của mình đã hoàn thành khi đã gửi tất cả thông tin phiên
theo định dạng chuẩn đến một địa chỉ multicast đã biết. Trong trường
hợp sau, bạn cần xác định vị trí một hoặc nhiều người dùng, gửi thông
báo cho họ về mong muốn nói chuyện (tương tự như gọi chuông điện
thoại), và có thể đàm phán một phương thức mã hóa âm thanh phù hợp cho
tất cả các bên. Chúng ta sẽ xem xét trước về SDP, giao thức chung cho
nhiều ứng dụng, sau đó là SIP, giao thức được sử dụng rộng rãi cho
nhiều ứng dụng tương tác như điện thoại Internet.

Giao thức Mô tả Phiên (SDP)
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Giao thức Mô tả Phiên (SDP) là một giao thức khá tổng quát có thể sử
dụng trong nhiều tình huống và thường được sử dụng kết hợp với một
hoặc nhiều giao thức khác (ví dụ, SIP). Nó truyền tải các thông tin
sau:

-  Tên và mục đích của phiên

-  Thời gian bắt đầu và kết thúc của phiên

-  Các loại phương tiện (ví dụ, âm thanh, video) tạo thành phiên

-  Thông tin chi tiết cần thiết để nhận phiên (ví dụ, địa chỉ multicast
   mà dữ liệu sẽ được gửi đến, giao thức truyền tải sẽ sử dụng, số cổng,
   phương thức mã hóa)

SDP cung cấp thông tin này được định dạng bằng ASCII sử dụng một chuỗi
các dòng văn bản, mỗi dòng có dạng ``<type>=<value>``. Một ví dụ về
thông điệp SDP sẽ minh họa các điểm chính.

::

   v=0
   o=larry 2890844526 2890842807 IN IP4 128.112.136.10
   s=Networking 101
   i=A class on computer networking
   u=http://www.cs.princeton.edu/
   e=larry@cs.princeton.edu
   c=IN IP4 224.2.17.12/127
   t=2873397496 2873404696
   m=audio 49170 RTP/AVP 0
   m=video 51372 RTP/AVP 31
   m=application 32416 udp wb

Lưu ý rằng SDP, giống như HTML, khá dễ đọc đối với con người nhưng có
các quy tắc định dạng nghiêm ngặt giúp máy móc có thể diễn giải dữ liệu
một cách rõ ràng. Ví dụ, đặc tả SDP định nghĩa tất cả các loại thông
tin có thể xuất hiện, thứ tự xuất hiện, định dạng và các từ khóa dành
riêng cho từng loại được định nghĩa.

Điều đầu tiên cần chú ý là mỗi loại thông tin được xác định bằng một ký
tự duy nhất. Ví dụ, dòng này cho biết “version” có giá trị bằng 0; tức
là thông điệp này được định dạng theo phiên bản 0 của SDP. Dòng tiếp
theo cung cấp “origin” của phiên, chứa đủ thông tin để xác định duy
nhất phiên. ``larry`` là tên người dùng tạo phiên, và ``128.112.136.10``
là địa chỉ IP của máy tính của anh ấy. Số theo sau ``larry`` là một
định danh phiên được chọn để duy nhất trên máy đó. Tiếp theo là số
“version” cho thông báo SDP; nếu thông tin phiên được cập nhật bởi một
thông điệp sau đó, số version sẽ được tăng lên.

Ba dòng tiếp theo (``i``, ``s`` và ``u``) cung cấp tên phiên, mô tả
phiên và Uniform Resource Identifier (URI, như đã mô tả ở phần trước
trong chương này)—thông tin hữu ích cho người dùng khi quyết định có
tham gia phiên này hay không. Thông tin như vậy có thể được hiển thị
trên giao diện người dùng của công cụ thư mục phiên hiển thị các sự
kiện hiện tại và sắp tới đã được quảng bá bằng SDP. Dòng tiếp theo
(``e=...``) chứa địa chỉ email của người liên hệ về phiên. :numref:`Hình
%s <fig-sdr>` hiển thị ảnh chụp màn hình của một công cụ thư mục phiên
(đã lỗi thời) gọi là ``sdr`` cùng với mô tả của một số phiên đã được
thông báo tại thời điểm chụp ảnh.

.. _fig-sdr:
.. figure:: figures/f09-07-9780123850591.png
   :width: 500px
   :align: center

   Một công cụ thư mục phiên hiển thị thông tin trích xuất từ các thông
   điệp SDP.

Tiếp theo là các chi tiết kỹ thuật cho phép một chương trình ứng dụng
tham gia phiên. Dòng bắt đầu bằng ``c=...`` cung cấp địa chỉ IP
multicast mà dữ liệu cho phiên này sẽ được gửi đến; người dùng cần tham
gia nhóm multicast này để nhận phiên. Tiếp theo là thời gian bắt đầu và
kết thúc của phiên (được mã hóa dưới dạng số nguyên theo Giao thức Thời
gian Mạng - NTP). Cuối cùng, chúng ta đến thông tin về các phương tiện
cho phiên này. Phiên này có ba loại phương tiện—âm thanh, video và ứng
dụng bảng trắng chia sẻ gọi là ``wb``. Với mỗi loại phương tiện có một
dòng thông tin được định dạng như sau:

::

   m=<media> <port> <transport> <format>

Các loại phương tiện khá dễ hiểu, và số cổng trong mỗi trường hợp là
cổng UDP. Khi xem trường “transport”, ta thấy ứng dụng ``wb`` chạy trực
tiếp trên UDP, trong khi âm thanh và video được truyền tải bằng
“RTP/AVP”. Điều này có nghĩa là chúng chạy trên RTP và sử dụng *hồ sơ
ứng dụng* gọi là *AVP*. Hồ sơ ứng dụng này định nghĩa một số phương
thức mã hóa khác nhau cho âm thanh và video; trong trường hợp này, âm
thanh sử dụng mã hóa 0 (là mã hóa sử dụng tần số lấy mẫu 8 kHz và 8 bit
mỗi mẫu) và video sử dụng mã hóa 31, đại diện cho phương thức mã hóa
H.261. Các “magic number” cho các phương thức mã hóa này được định
nghĩa trong RFC định nghĩa hồ sơ AVP; cũng có thể mô tả các phương thức
mã hóa không chuẩn trong SDP.

Cuối cùng, chúng ta thấy mô tả về loại phương tiện “wb”. Tất cả thông
tin mã hóa cho dữ liệu này là đặc thù cho ứng dụng ``wb``, do đó chỉ
cần cung cấp tên ứng dụng trong trường “format”. Điều này tương tự như
việc đặt ``application/wb`` trong một thông điệp MIME.

Bây giờ chúng ta đã biết cách mô tả các phiên, hãy xem cách chúng có
thể được khởi tạo. Một cách sử dụng SDP là thông báo các hội nghị đa
phương tiện, bằng cách gửi các thông điệp SDP đến một địa chỉ multicast
đã biết. Công cụ thư mục phiên được hiển thị trong :numref:`Hình %s
<fig-sdr>` sẽ hoạt động bằng cách tham gia nhóm multicast đó và hiển
thị thông tin thu thập được từ các thông điệp SDP nhận được. SDP cũng
được sử dụng trong việc cung cấp video giải trí qua IP (thường gọi là
IPTV) để cung cấp thông tin về nội dung video trên mỗi kênh truyền hình.

SDP cũng đóng vai trò quan trọng khi kết hợp với Giao thức Khởi tạo
Phiên (SIP). Với việc VoIP được sử dụng rộng rãi (tức là hỗ trợ các
ứng dụng giống điện thoại qua mạng IP) và hội nghị truyền hình dựa
trên IP, SIP hiện là một trong những thành viên quan trọng của bộ giao
thức Internet.

SIP
~~~

SIP là một giao thức tầng ứng dụng có nét tương đồng với HTTP, dựa trên
mô hình request/response tương tự. Tuy nhiên, nó được thiết kế cho các
loại ứng dụng khá khác biệt và do đó cung cấp các khả năng rất khác so
với HTTP. Các khả năng do SIP cung cấp có thể được nhóm thành năm loại:

-  Xác định vị trí người dùng—Xác định thiết bị đúng để liên lạc nhằm
   kết nối với một người dùng cụ thể

-  Khả năng sẵn sàng của người dùng—Xác định xem người dùng có sẵn sàng
   hoặc có thể tham gia một phiên giao tiếp cụ thể hay không

-  Khả năng của người dùng—Xác định các yếu tố như lựa chọn phương tiện
   và phương thức mã hóa sẽ sử dụng

-  Thiết lập phiên—Thiết lập các tham số phiên như số cổng sẽ được các
   bên giao tiếp sử dụng

-  Quản lý phiên—Một loạt chức năng bao gồm chuyển tiếp phiên (ví dụ,
   để thực hiện “chuyển tiếp cuộc gọi”) và thay đổi các tham số phiên

Hầu hết các chức năng này khá dễ hiểu, nhưng vấn đề xác định vị trí
cần được bàn thêm. Một điểm khác biệt quan trọng giữa SIP và, chẳng
hạn, HTTP, là SIP chủ yếu được sử dụng cho giao tiếp giữa người với
người. Do đó, việc xác định vị trí các *người dùng* cá nhân là rất
quan trọng, không chỉ là máy tính. Và, không giống như email, chỉ xác
định vị trí máy chủ mà người dùng sẽ kiểm tra sau này và gửi thông điệp
đến đó là không đủ—chúng ta cần biết người dùng đang ở đâu *ngay bây
giờ* nếu muốn giao tiếp với họ theo thời gian thực. Điều này càng phức
tạp hơn bởi thực tế là một người dùng có thể chọn giao tiếp bằng nhiều
thiết bị khác nhau, chẳng hạn sử dụng máy tính để bàn khi ở văn phòng
và thiết bị cầm tay khi đi công tác. Nhiều thiết bị có thể hoạt động
đồng thời và có khả năng rất khác nhau (ví dụ, một máy nhắn tin chữ và
một “điện thoại” video trên PC). Lý tưởng là người dùng khác có thể xác
định vị trí và giao tiếp với thiết bị phù hợp vào bất kỳ thời điểm nào.
Hơn nữa, người dùng phải có quyền kiểm soát khi nào, ở đâu và từ ai họ
nhận cuộc gọi.

Để cho phép người dùng kiểm soát mức độ phù hợp đối với các cuộc gọi
của mình, SIP giới thiệu khái niệm proxy. Một proxy SIP có thể được
xem như một điểm liên lạc cho người dùng mà các yêu cầu liên lạc ban
đầu với họ sẽ được gửi đến. Proxy cũng thực hiện các chức năng thay mặt
người gọi. Chúng ta có thể thấy proxy hoạt động như thế nào qua một ví
dụ.

.. _fig-sipproxy:
.. figure:: figures/f09-08-9780123850591.png
   :width: 600px
   :align: center

   Thiết lập liên lạc thông qua các proxy SIP.

Xem xét hai người dùng trong :numref:`Hình %s <fig-sipproxy>`. Điều
đầu tiên cần chú ý là mỗi người dùng có một tên theo định dạng
``user@domain``, rất giống địa chỉ email. Khi người dùng Bruce muốn
khởi tạo một phiên với Larry, anh ấy gửi thông điệp SIP ban đầu đến
proxy cục bộ cho miền của mình, ``cisco.com``. Trong số các thông tin
khác, thông điệp ban đầu này chứa một *SIP URI*—đây là một dạng định
danh tài nguyên thống nhất trông như sau:

::

   SIP:larry@princeton.edu

Một SIP URI cung cấp nhận diện đầy đủ cho người dùng, nhưng (khác với
URL) không cung cấp vị trí của họ, vì vị trí đó có thể thay đổi theo
thời gian. Chúng ta sẽ xem cách xác định vị trí người dùng ngay sau
đây.

Khi nhận được thông điệp ban đầu từ Bruce, proxy xem xét SIP URI và
suy ra rằng thông điệp này nên được gửi đến proxy. Tạm thời, giả sử
proxy có quyền truy cập vào một cơ sở dữ liệu nào đó cho phép nó lấy
được ánh xạ từ tên sang địa chỉ IP của một hoặc nhiều thiết bị mà Larry
hiện muốn nhận thông điệp. Proxy do đó có thể chuyển tiếp thông điệp
đến thiết bị (các thiết bị) mà Larry chọn. Gửi thông điệp đến nhiều
thiết bị được gọi là *forking* và có thể thực hiện song song hoặc nối
tiếp (ví dụ, gửi đến điện thoại di động nếu anh ấy không trả lời điện
thoại bàn).

Thông điệp ban đầu từ Bruce đến Larry có thể là thông điệp SIP
``invite``, trông như sau:

::

   INVITE sip:larry@princeton.edu SIP/2.0
   Via: SIP/2.0/UDP bsd-pc.cisco.com;branch=z9hG4bK433yte4
   To: Larry <sip:larry@princeton.edu>
   From: Bruce <sip:bruce@cisco.com>;tag=55123
   Call-ID: xy745jj210re3@bsd-pc.cisco.com
   CSeq: 271828 INVITE
   Contact: <sip:bruce@bsd-pc.cisco.com>
   Content-Type: application/sdp
   Content-Length: 142

Dòng đầu tiên xác định loại chức năng cần thực hiện (``invite``); tài
nguyên cần thực hiện, bên được gọi (``sip:larry@princeton.edu``); và
phiên bản giao thức (2.0). Các dòng header tiếp theo có lẽ trông quen
thuộc vì giống với các dòng header trong thông điệp email. SIP định
nghĩa rất nhiều trường header, chỉ một số trường được mô tả ở đây. Lưu
ý rằng header ``Via:`` trong ví dụ này xác định thiết bị gửi thông điệp
này. Các header ``Content-Type:`` và ``Content-Length:`` mô tả nội dung
của thông điệp sau phần header, giống như trong thông điệp email mã hóa
MIME. Trong trường hợp này, nội dung là một thông điệp SDP. Thông điệp
này sẽ mô tả các yếu tố như loại phương tiện (âm thanh, video, v.v.)
mà Bruce muốn trao đổi với Larry và các thuộc tính khác của phiên như
các loại codec mà anh ấy hỗ trợ. Lưu ý rằng trường trong SIP cho phép
sử dụng bất kỳ giao thức nào cho mục đích này, mặc dù SDP là phổ biến
nhất.

Quay lại ví dụ, khi thông điệp ``invite`` đến proxy, không chỉ proxy
chuyển tiếp thông điệp đến ``princeton.edu``, mà nó còn phản hồi cho
người gửi ``invite``. Cũng giống như HTTP, tất cả các phản hồi đều có
mã phản hồi, và tổ chức mã cũng tương tự như HTTP. Trong :numref:`Hình
%s <fig-sipeg>` chúng ta có thể thấy một chuỗi các thông điệp và phản
hồi SIP.

.. _fig-sipeg:
.. figure:: figures/f09-09-9780123850591.png
   :width: 650px
   :align: center

   Luồng thông điệp cho một phiên SIP cơ bản.

Thông điệp phản hồi đầu tiên trong hình này là phản hồi tạm thời
``100 trying``, cho biết thông điệp đã được proxy của người gọi nhận mà
không có lỗi. Khi ``invite`` được chuyển đến điện thoại của Larry, nó
báo hiệu cho Larry và phản hồi bằng thông điệp ``180 ringing``. Khi
thông điệp này đến máy tính của Bruce, nó có thể phát ra “nhạc chuông”.
Giả sử Larry sẵn sàng và có thể giao tiếp với Bruce, anh ấy có thể nhấc
máy, khiến thông điệp ``200 OK`` được gửi đi. Máy tính của Bruce phản
hồi bằng ``ACK``, và phương tiện (ví dụ, luồng âm thanh đóng gói RTP)
có thể bắt đầu truyền giữa hai bên. Lưu ý rằng tại thời điểm này hai
bên đã biết địa chỉ của nhau, nên ``ACK`` có thể được gửi trực tiếp,
bỏ qua các proxy. Các proxy giờ không còn tham gia vào cuộc gọi nữa.
Lưu ý rằng luồng phương tiện do đó thường sẽ đi theo một đường khác
trong mạng so với các thông điệp báo hiệu ban đầu. Hơn nữa, ngay cả khi
một hoặc cả hai proxy bị lỗi tại thời điểm này, cuộc gọi vẫn có thể
tiếp tục bình thường. Cuối cùng, khi một bên muốn kết thúc phiên, nó
gửi thông điệp ``BYE``, thông điệp này sẽ nhận được phản hồi ``200 OK``
trong điều kiện bình thường.

Có một số chi tiết chúng ta đã lướt qua. Một là việc đàm phán các đặc
tính phiên. Có thể Bruce muốn giao tiếp bằng cả âm thanh và video nhưng
điện thoại của Larry chỉ hỗ trợ âm thanh. Do đó, điện thoại của Larry
sẽ gửi một thông điệp SDP trong ``200 OK`` mô tả các thuộc tính phiên
có thể chấp nhận được đối với Larry và thiết bị, xét đến các tùy chọn
được đề xuất trong ``invite`` của Bruce. Bằng cách này, các tham số
phiên được cả hai bên chấp nhận sẽ được thống nhất trước khi luồng
phương tiện bắt đầu.

Vấn đề lớn khác mà chúng ta đã lướt qua là xác định đúng thiết bị cho
Larry. Đầu tiên, máy tính của Bruce phải gửi ``invite`` đến proxy
``cisco.com``. Thông tin này có thể được cấu hình sẵn trên máy tính,
hoặc có thể học được qua DHCP. Sau đó, proxy ``cisco.com`` phải tìm
proxy ``princeton.edu``. Việc này có thể thực hiện bằng một loại tra
cứu DNS đặc biệt sẽ trả về địa chỉ IP của proxy SIP cho miền đó. (Chúng
ta sẽ bàn về cách DNS làm điều này ở phần tiếp theo.) Cuối cùng, proxy
``princeton.edu`` phải tìm một thiết bị mà Larry có thể liên lạc. Thông
thường, một máy chủ proxy có quyền truy cập vào cơ sở dữ liệu vị trí có
thể được cập nhật bằng nhiều cách. Cấu hình thủ công là một lựa chọn,
nhưng một lựa chọn linh hoạt hơn là sử dụng khả năng *đăng ký* của SIP.

Người dùng có thể đăng ký với dịch vụ vị trí bằng cách gửi thông điệp
SIP ``register`` đến “registrar” cho miền của mình. Thông điệp này tạo
liên kết giữa “address of record” và “contact address”. “Address of
record” có thể là một SIP URI là địa chỉ đã biết của người dùng (ví dụ,
``sip:larry@princeton.edu``) và “contact address” sẽ là địa chỉ mà người
dùng hiện có thể liên lạc (ví dụ, ``sip:larry@llp-ph.cs.princeton.edu``).
Đây chính là liên kết mà proxy ``princeton.edu`` cần trong ví dụ của
chúng ta.

Lưu ý rằng một người dùng có thể đăng ký ở nhiều vị trí và nhiều người
dùng có thể đăng ký trên một thiết bị. Ví dụ, có thể tưởng tượng một
nhóm người bước vào phòng họp được trang bị điện thoại IP và tất cả họ
đăng ký trên đó để có thể nhận cuộc gọi trên điện thoại đó.

SIP là một giao thức rất phong phú và linh hoạt, có thể hỗ trợ nhiều
kịch bản gọi phức tạp cũng như các ứng dụng ít hoặc không liên quan gì
đến điện thoại. Ví dụ, SIP hỗ trợ các thao tác cho phép chuyển cuộc gọi
đến máy chủ “music-on-hold” hoặc máy chủ hộp thư thoại. Cũng dễ thấy
nó có thể được sử dụng cho các ứng dụng như nhắn tin tức thời, và việc
chuẩn hóa các phần mở rộng SIP cho các mục đích như vậy vẫn đang được
tiến hành.

H.323
~~~~~

Liên minh Viễn thông Quốc tế (ITU) cũng rất tích cực trong lĩnh vực
điều khiển cuộc gọi, điều này không có gì ngạc nhiên vì nó liên quan
đến điện thoại, lĩnh vực truyền thống của tổ chức này. May mắn thay,
đã có sự phối hợp đáng kể giữa IETF và ITU trong trường hợp này, nên
các giao thức phần nào tương thích với nhau. Khuyến nghị chính của ITU
cho truyền thông đa phương tiện qua mạng gói được gọi là *H.323*, kết
nối nhiều khuyến nghị khác, bao gồm H.225 cho điều khiển cuộc gọi. Bộ
khuyến nghị đầy đủ của H.323 lên đến hàng trăm trang, và giao thức này
nổi tiếng là phức tạp, nên chỉ có thể giới thiệu ngắn gọn ở đây.

H.323 phổ biến như một giao thức cho điện thoại Internet, bao gồm cả
cuộc gọi video, và chúng ta sẽ xem xét loại ứng dụng này ở đây. Một
thiết bị khởi tạo hoặc kết thúc cuộc gọi được gọi là thiết bị đầu cuối
H.323; có thể là một máy trạm chạy ứng dụng điện thoại Internet, hoặc
một “thiết bị” được thiết kế riêng—một thiết bị giống điện thoại với
phần mềm mạng và cổng Ethernet, chẳng hạn. Các thiết bị đầu cuối H.323
có thể nói chuyện trực tiếp với nhau, nhưng các cuộc gọi thường được
trung gian bởi một thiết bị gọi là *gatekeeper*. Gatekeeper thực hiện
nhiều chức năng như chuyển đổi giữa các định dạng địa chỉ khác nhau
được sử dụng cho cuộc gọi điện thoại và kiểm soát số lượng cuộc gọi có
thể thực hiện cùng lúc để giới hạn băng thông sử dụng bởi các ứng dụng
H.323. H.323 cũng bao gồm khái niệm *gateway*, kết nối mạng H.323 với
các loại mạng khác. Sử dụng phổ biến nhất của gateway là kết nối mạng
H.323 với mạng điện thoại chuyển mạch công cộng (PSTN) như minh họa
trong :numref:`Hình %s <fig-h323>`. Điều này cho phép người dùng chạy
ứng dụng H.323 trên máy tính nói chuyện với người dùng điện thoại thông
thường trên mạng điện thoại công cộng. Một chức năng hữu ích của
gatekeeper là giúp thiết bị đầu cuối tìm gateway, có thể chọn trong
nhiều lựa chọn để tìm gateway gần đích cuối cùng của cuộc gọi. Điều này
rõ ràng hữu ích trong thế giới mà điện thoại thông thường nhiều hơn
điện thoại trên PC rất nhiều. Khi thiết bị đầu cuối H.323 gọi đến một
điểm cuối là điện thoại thông thường, gateway trở thành điểm cuối thực
sự cho cuộc gọi H.323 và chịu trách nhiệm chuyển đổi phù hợp cả thông
tin báo hiệu và luồng phương tiện cần truyền qua mạng điện thoại.

.. _fig-h323:
.. figure:: figures/f09-10-9780123850591.png
   :width: 500px
   :align: center

   Các thiết bị trong mạng H.323.

Một phần quan trọng của H.323 là giao thức H.245, được sử dụng để đàm
phán các thuộc tính của cuộc gọi, tương tự như việc sử dụng SDP mô tả
ở trên. Các thông điệp H.245 có thể liệt kê một số chuẩn codec âm
thanh mà nó có thể hỗ trợ; điểm cuối xa của cuộc gọi sẽ trả lời bằng
danh sách các codec mà nó hỗ trợ, và hai bên có thể chọn một chuẩn mã
hóa mà cả hai đều chấp nhận được. H.245 cũng có thể được sử dụng để
báo hiệu số cổng UDP sẽ được RTP và Real-Time Control Protocol (RTCP)
sử dụng cho luồng phương tiện (hoặc nhiều luồng—một cuộc gọi có thể bao
gồm cả âm thanh và video, chẳng hạn) cho cuộc gọi này. Khi việc này
hoàn tất, cuộc gọi có thể tiến hành, với RTP được sử dụng để truyền
tải các luồng phương tiện và RTCP mang thông tin điều khiển liên quan.

9.2.2 Phân bổ tài nguyên cho ứng dụng đa phương tiện
-----------------------------------------------------

Như chúng ta vừa thấy, các giao thức điều khiển phiên như SIP và H.323
có thể được sử dụng để khởi tạo và kiểm soát giao tiếp trong các ứng
dụng đa phương tiện, trong khi RTP cung cấp các chức năng tầng truyền
tải cho các luồng dữ liệu của ứng dụng. Mảnh ghép cuối cùng để các ứng
dụng đa phương tiện hoạt động là đảm bảo rằng các tài nguyên phù hợp
được phân bổ trong mạng để đáp ứng nhu cầu chất lượng dịch vụ của ứng
dụng. Chúng ta đã trình bày một số phương pháp phân bổ tài nguyên ở
chương trước. Động lực phát triển các công nghệ này chủ yếu là để hỗ
trợ các ứng dụng đa phương tiện. Vậy các ứng dụng tận dụng khả năng
phân bổ tài nguyên của mạng như thế nào?

Cần lưu ý rằng nhiều ứng dụng đa phương tiện chạy thành công trên các
mạng “best-effort”, như Internet công cộng. Sự đa dạng của các dịch vụ
VoIP thương mại (như Skype) là minh chứng cho việc bạn chỉ cần lo lắng
về phân bổ tài nguyên khi tài nguyên không dồi dào—và ở nhiều nơi trên
Internet ngày nay, tài nguyên dồi dào là điều bình thường.

Một giao thức như RTCP có thể giúp các ứng dụng trên mạng best-effort,
bằng cách cung cấp cho ứng dụng thông tin chi tiết về chất lượng dịch
vụ mà mạng đang cung cấp. Hãy nhớ rằng RTCP mang thông tin về tỷ lệ
mất gói và đặc tính trễ giữa các bên tham gia ứng dụng đa phương tiện.
Ứng dụng có thể sử dụng thông tin này để thay đổi phương thức mã hóa—
chuyển sang codec tốc độ thấp hơn, chẳng hạn, khi băng thông khan hiếm.
Lưu ý rằng, mặc dù có thể bị cám dỗ chuyển sang codec gửi thêm thông
tin dư thừa khi tỷ lệ mất gói cao, điều này không được khuyến khích;
nó tương tự như *tăng* kích thước cửa sổ của TCP khi có mất gói, hoàn
toàn ngược lại với những gì cần làm để tránh sụp đổ nghẽn.

Như đã bàn ở chương trước, Dịch vụ Phân biệt (DiffServ) có thể được sử
dụng để cung cấp phân bổ tài nguyên khá cơ bản và có khả năng mở rộng
cho các ứng dụng. Một ứng dụng đa phương tiện có thể đặt mã điểm dịch
vụ phân biệt (DSCP) trong header IP của các gói mà nó tạo ra nhằm đảm
bảo cả gói phương tiện và gói điều khiển nhận được chất lượng dịch vụ
phù hợp. Ví dụ, thông thường các gói phương tiện thoại được đánh dấu là
“EF” (expedited forwarding) để chúng được xếp vào hàng đợi độ trễ thấp
hoặc ưu tiên trong các router trên đường đi, trong khi các gói báo hiệu
cuộc gọi (ví dụ, SIP) thường được đánh dấu với một loại “AF” (assured
forwarding) nào đó để chúng được xếp hàng riêng biệt với lưu lượng
best-effort và do đó giảm nguy cơ bị mất.

Tất nhiên, chỉ nên đánh dấu các gói bên trong máy chủ gửi hoặc thiết bị
nếu các thiết bị mạng như router chú ý đến DSCP. Thông thường, các
router trên Internet công cộng bỏ qua DSCP, cung cấp dịch vụ best-effort
cho tất cả các gói. Tuy nhiên, các mạng doanh nghiệp hoặc công ty có
khả năng sử dụng DiffServ cho lưu lượng đa phương tiện nội bộ của họ,
và thường làm như vậy. Ngoài ra, ngay cả người dùng Internet gia đình
cũng có thể cải thiện chất lượng VoIP hoặc các ứng dụng đa phương tiện
khác chỉ bằng cách sử dụng DiffServ trên hướng gửi đi của kết nối
Internet, như minh họa trong :numref:`Hình %s <fig-ds-bb>`. Điều này
hiệu quả vì sự bất đối xứng của nhiều kết nối Internet băng rộng: Nếu
liên kết gửi đi chậm hơn đáng kể (tức là hạn chế tài nguyên hơn) so với
liên kết nhận, thì phân bổ tài nguyên bằng DiffServ trên liên kết đó có
thể đủ để tạo ra sự khác biệt về chất lượng cho các ứng dụng nhạy cảm
với độ trễ và mất mát.

.. _fig-ds-bb:
.. figure:: figures/f09-11-9780123850591.png
   :width: 500px
   :align: center

   Dịch vụ Phân biệt áp dụng cho ứng dụng VoIP. Xếp hàng DiffServ chỉ áp
   dụng trên liên kết gửi đi từ router khách hàng đến ISP.

Mặc dù DiffServ hấp dẫn vì sự đơn giản, rõ ràng nó không thể đáp ứng
nhu cầu của ứng dụng trong mọi điều kiện. Ví dụ, giả sử băng thông gửi
đi trong :numref:`Hình %s <fig-ds-bb>` chỉ là 100 kbps, và khách hàng
cố gắng thực hiện hai cuộc gọi VoIP, mỗi cuộc sử dụng codec 64 kbps.
Rõ ràng liên kết gửi đi giờ đã quá tải hơn 100%, điều này sẽ dẫn đến
độ trễ xếp hàng lớn và mất gói. Không có cách xếp hàng thông minh nào
trong router của khách hàng có thể khắc phục điều đó.

Đặc điểm của nhiều ứng dụng đa phương tiện là, thay vì cố nhồi nhét quá
nhiều cuộc gọi vào một đường truyền quá hẹp, tốt hơn là chặn một cuộc
gọi trong khi cho phép cuộc gọi khác tiếp tục. Tức là, tốt hơn là một
người có thể trò chuyện thành công trong khi người khác nghe tín hiệu
bận, hơn là cả hai đều trải nghiệm chất lượng âm thanh không chấp nhận
được cùng lúc. Chúng ta đôi khi gọi các ứng dụng như vậy là có *đường
tiện ích dốc*, nghĩa là tiện ích (tính hữu ích) của ứng dụng giảm mạnh
khi chất lượng dịch vụ do mạng cung cấp giảm. Các ứng dụng đa phương
tiện thường có đặc điểm này, trong khi nhiều ứng dụng truyền thống thì
không. Email, chẳng hạn, vẫn hoạt động tốt ngay cả khi độ trễ lên đến
hàng giờ.

Các ứng dụng có đường tiện ích dốc thường phù hợp với một số hình thức
kiểm soát truy nhập (admission control). Nếu bạn không thể chắc chắn
rằng tài nguyên luôn đủ để hỗ trợ tải của ứng dụng, thì kiểm soát truy
nhập cung cấp cách nói “không” với một số ứng dụng trong khi cho phép
ứng dụng khác nhận tài nguyên cần thiết.

Chúng ta đã thấy một cách thực hiện kiểm soát truy nhập bằng RSVP ở
chương trước, và sẽ quay lại vấn đề này ngay sau đây, nhưng các ứng
dụng đa phương tiện sử dụng giao thức điều khiển phiên cung cấp một số
lựa chọn kiểm soát truy nhập khác. Điểm mấu chốt ở đây là các giao thức
điều khiển phiên như SIP hoặc H.323 thường liên quan đến một số trao
đổi thông điệp giữa điểm cuối và một thực thể khác (proxy SIP hoặc
gatekeeper H.323) khi bắt đầu cuộc gọi hoặc phiên. Điều này cung cấp
một cách thuận tiện để nói “không” với một cuộc gọi mới khi tài nguyên
không đủ.

Ví dụ, hãy xem xét mạng trong :numref:`Hình %s <fig-cm-cac>`. Giả sử
liên kết diện rộng từ văn phòng chi nhánh đến văn phòng chính có đủ băng
thông cho ba cuộc gọi VoIP đồng thời sử dụng codec 64 kbps. Mỗi điện
thoại đều cần liên lạc với proxy SIP hoặc gatekeeper H.323 cục bộ khi
bắt đầu cuộc gọi, nên proxy/gatekeeper có thể dễ dàng gửi lại thông
điệp yêu cầu điện thoại IP phát tín hiệu bận nếu liên kết đó đã đầy.
Proxy hoặc gatekeeper thậm chí có thể xử lý trường hợp một điện thoại
IP thực hiện nhiều cuộc gọi cùng lúc và các tốc độ codec khác nhau có
thể được sử dụng. Tuy nhiên, sơ đồ này chỉ hoạt động nếu không thiết bị
nào khác có thể làm quá tải liên kết mà không thông qua gatekeeper hoặc
proxy trước. Xếp hàng DiffServ có thể được sử dụng để đảm bảo, ví dụ,
một PC truyền tệp không ảnh hưởng đến các cuộc gọi VoIP. Nhưng, giả sử
một ứng dụng VoIP nào đó không liên lạc với gatekeeper hoặc proxy được
kích hoạt ở văn phòng từ xa. Ứng dụng như vậy, nếu có thể đánh dấu gói
phù hợp và vào cùng hàng đợi với lưu lượng VoIP hiện có, rõ ràng có thể
làm quá tải liên kết mà không nhận được phản hồi từ proxy hoặc
gatekeeper.

.. _fig-cm-cac:
.. figure:: figures/f09-12-9780123850591.png
   :width: 500px
   :align: center

   Kiểm soát truy nhập sử dụng giao thức điều khiển phiên.

Một vấn đề khác với phương pháp vừa mô tả là nó phụ thuộc vào việc
gatekeeper hoặc proxy biết đường đi mà mỗi ứng dụng sẽ sử dụng. Trong
topo đơn giản của :numref:`Hình %s <fig-cm-cac>` điều này không phải là
vấn đề lớn, nhưng trong các mạng phức tạp hơn nó có thể nhanh chóng trở
nên không thể quản lý. Chỉ cần tưởng tượng trường hợp văn phòng từ xa
có hai kết nối khác nhau ra bên ngoài để thấy rằng chúng ta đang yêu
cầu proxy hoặc gatekeeper hiểu không chỉ SIP hoặc H.323 mà còn cả
định tuyến, sự cố liên kết và điều kiện mạng hiện tại. Điều này có thể
nhanh chóng trở nên không thể quản lý.

Chúng ta gọi loại kiểm soát truy nhập vừa mô tả là *off-path*, nghĩa là
thiết bị đưa ra quyết định kiểm soát truy nhập không nằm trên đường dữ
liệu nơi cần phân bổ tài nguyên. Lựa chọn rõ ràng là kiểm soát truy
nhập *on-path*, và ví dụ tiêu chuẩn về giao thức thực hiện kiểm soát
truy nhập on-path trong mạng IP là Giao thức Đặt trước Tài nguyên
(RSVP). Chúng ta đã thấy ở chương trước cách RSVP có thể được sử dụng
để đảm bảo tài nguyên được phân bổ dọc theo đường đi, và việc sử dụng
RSVP trong các ứng dụng như mô tả ở phần này là khá đơn giản. Chi tiết
còn lại là cách giao thức kiểm soát truy nhập tương tác với giao thức
điều khiển phiên.

.. _fig-sip-sync:
.. figure:: figures/f09-13-9780123850591.png
   :width: 500px
   :align: center

   Phối hợp báo hiệu SIP và đặt trước tài nguyên.

Phối hợp các hành động của giao thức kiểm soát truy nhập (hoặc đặt
trước tài nguyên) và giao thức điều khiển phiên không phải là khoa học
tên lửa, nhưng cần chú ý đến chi tiết. Ví dụ, hãy xem xét một cuộc gọi
điện thoại đơn giản giữa hai bên. Trước khi có thể đặt trước tài
nguyên, bạn cần biết cuộc gọi sẽ sử dụng bao nhiêu băng thông, nghĩa là
cần biết các codec sẽ sử dụng. Điều đó ngụ ý cần thực hiện một phần
điều khiển phiên trước, để trao đổi thông tin về các codec mà hai điện
thoại hỗ trợ. Tuy nhiên, bạn không thể thực hiện *toàn bộ* điều khiển
phiên trước, vì không muốn điện thoại đổ chuông trước khi quyết định
kiểm soát truy nhập được đưa ra, phòng trường hợp kiểm soát truy nhập
thất bại. :numref:`Hình %s <fig-sip-sync>` minh họa tình huống này nơi
SIP được sử dụng cho điều khiển phiên và RSVP được sử dụng để đưa ra
quyết định kiểm soát truy nhập (thành công trong trường hợp này).

Điều chính cần chú ý ở đây là sự xen kẽ giữa các tác vụ điều khiển
phiên và phân bổ tài nguyên. Các đường liền nét đại diện cho thông điệp
SIP, các đường đứt nét đại diện cho thông điệp RSVP. Lưu ý rằng các
thông điệp SIP được truyền trực tiếp từ điện thoại đến điện thoại trong
ví dụ này (tức là không hiển thị proxy SIP), trong khi các thông điệp
RSVP cũng được xử lý bởi các router ở giữa khi kiểm tra tài nguyên đủ
để chấp nhận cuộc gọi.

Chúng ta bắt đầu với trao đổi thông tin codec ban đầu trong hai thông
điệp SIP đầu tiên (hãy nhớ rằng SDP được sử dụng để liệt kê các codec
khả dụng, cùng các thông tin khác). ``PRACK`` là “provisional
acknowledgment”. Khi các thông điệp này đã được trao đổi, các thông
điệp RSVP ``PATH``, chứa mô tả về lượng tài nguyên sẽ cần, có thể được
gửi như bước đầu tiên để đặt trước tài nguyên theo cả hai hướng của
cuộc gọi. Tiếp theo, các thông điệp ``RESV`` có thể được gửi lại để
thực sự đặt trước tài nguyên. Khi điện thoại khởi tạo nhận được ``RESV``,
nó có thể gửi thông điệp SDP cập nhật báo cáo rằng tài nguyên đã được
đặt trước theo một hướng. Khi điện thoại được gọi nhận được cả thông
điệp đó và ``RESV`` từ điện thoại kia, nó có thể bắt đầu đổ chuông và
thông báo cho điện thoại kia rằng tài nguyên đã được đặt trước theo cả
hai hướng (bằng thông điệp SDP) và cũng thông báo cho điện thoại gọi
rằng nó đang đổ chuông. Từ đây, báo hiệu SIP và luồng phương tiện bình
thường, tương tự như minh họa trong :numref:`Hình %s <fig-sipeg>`, sẽ
tiếp tục.

Một lần nữa chúng ta thấy việc xây dựng ứng dụng đòi hỏi phải hiểu sự
tương tác giữa các khối xây dựng khác nhau (SIP và RSVP, trong trường
hợp này). Các nhà thiết kế SIP thực tế đã thực hiện một số thay đổi
trong giao thức để cho phép sự xen kẽ chức năng giữa các giao thức có
nhiệm vụ khác nhau, do đó chúng tôi nhấn mạnh nhiều lần trong cuốn sách
này về việc tập trung vào hệ thống hoàn chỉnh thay vì chỉ nhìn vào một
lớp hoặc thành phần riêng biệt với các phần khác của hệ thống.
