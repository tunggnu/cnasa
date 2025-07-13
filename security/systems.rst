8.5 Các Hệ Thống Ví Dụ
======================

Chúng ta đã thấy nhiều thành phần cần thiết để cung cấp một hoặc hai
khía cạnh của bảo mật. Những thành phần này bao gồm các thuật toán mật mã,
cơ chế phân phối khóa trước, và các giao thức xác thực. Trong phần này,
chúng ta sẽ xem xét một số hệ thống hoàn chỉnh sử dụng các thành phần này.

Các hệ thống này có thể được phân loại sơ bộ theo lớp giao thức mà chúng
hoạt động. Các hệ thống hoạt động ở lớp ứng dụng bao gồm Pretty Good Privacy
(PGP), cung cấp bảo mật cho thư điện tử, và Secure Shell (SSH), một tiện ích
đăng nhập từ xa an toàn. Ở lớp vận chuyển, có tiêu chuẩn Transport Layer Security
(TLS) của IETF và giao thức cũ hơn mà nó kế thừa, Secure Socket Layer (SSL).
Các giao thức IPsec (IP Security), như tên gọi, hoạt động ở lớp IP (lớp mạng).
802.11i cung cấp bảo mật ở lớp liên kết của mạng không dây. Phần này mô tả
các đặc điểm nổi bật của từng phương pháp tiếp
cận
này.

Bạn có thể tự hỏi tại sao bảo mật lại phải được cung cấp ở nhiều lớp khác nhau.
Một lý do là các mối đe dọa khác nhau đòi hỏi các biện pháp phòng thủ khác nhau,
và điều này thường dẫn đến việc bảo vệ một lớp giao thức khác nhau. Ví dụ, nếu
mối quan tâm chính của bạn là một người ở tòa nhà bên cạnh nghe lén lưu lượng
giữa máy tính xách tay của bạn và điểm truy cập 802.11, thì bạn có thể muốn bảo
mật ở lớp liên kết. Tuy nhiên, nếu bạn muốn thực sự chắc chắn rằng mình đang kết
nối đến trang web của ngân hàng và ngăn chặn tất cả dữ liệu bạn gửi đến ngân hàng
khỏi bị đọc bởi các nhân viên tò mò của một nhà cung cấp dịch vụ Internet nào đó,
thì một thứ gì đó kéo dài từ máy của bạn đến máy chủ của ngân hàng—như lớp vận chuyển—
có thể là nơi phù hợp để bảo vệ lưu lượng. Như thường lệ, không có giải pháp nào phù hợp
cho
tất
cả.

Các hệ thống bảo mật được mô tả dưới đây có khả năng thay đổi các thuật toán mật mã
mà chúng sử dụng. Ý tưởng làm cho một hệ thống bảo mật độc lập với thuật toán là rất tốt,
bởi vì bạn không bao giờ biết khi nào thuật toán mật mã ưa thích của mình có thể bị chứng minh
là không đủ mạnh cho mục đích của bạn. Sẽ rất tốt nếu bạn có thể nhanh chóng chuyển sang một
thuật toán mới mà không phải thay đổi đặc tả giao thức hoặc hiện thực. Lưu ý sự tương tự với
việc có thể thay đổi khóa mà không cần thay đổi thuật toán; nếu một trong các thuật toán mật mã
của bạn hóa ra có lỗ hổng, sẽ rất tuyệt nếu toàn bộ kiến trúc bảo mật của bạn không cần phải thiết
kế lại ngay
lập
tức.

8.5.1 Pretty Good Privacy (PGP)
-------------------------------

Pretty Good Privacy (PGP) là một phương pháp được sử dụng rộng rãi để cung cấp
bảo mật cho thư điện tử. Nó cung cấp xác thực, tính bí mật, toàn vẹn dữ liệu,
và không thể chối bỏ. Ban đầu được phát triển bởi Phil Zimmerman, nó đã phát triển
thành một tiêu chuẩn của IETF được gọi là OpenPGP. Như chúng ta đã thấy ở phần trước,
PGP nổi bật vì sử dụng mô hình “web of trust” để phân phối khóa thay vì một hệ phân cấp
dạng
cây.

Tính bí mật và xác thực phía người nhận của PGP phụ thuộc vào việc người nhận
của một thông điệp email có một khóa công khai mà người gửi biết. Để cung cấp xác thực
người gửi và không thể chối bỏ, người gửi phải có một khóa công khai mà người nhận biết.
Những khóa công khai này được phân phối trước bằng chứng chỉ và một PKI kiểu web-of-trust.
PGP hỗ trợ RSA và DSS cho chứng chỉ khóa công khai. Các chứng chỉ này cũng có thể chỉ định
các thuật toán mật mã nào được hỗ trợ hoặc ưu tiên bởi chủ sở hữu khóa. Các chứng chỉ cung cấp
liên kết giữa địa chỉ email và khóa
công
khai.

.. _fig-pgpMessage:
.. figure:: figures/f08-13-9780123850591.png
   :width: 400px
   :align: center

   Các bước của PGP để chuẩn bị một thông điệp gửi email từ Alice đến Bob.

Xét ví dụ sau về việc sử dụng PGP để cung cấp cả xác thực người gửi và tính bí mật.
Giả sử Alice có một thông điệp muốn gửi email cho Bob. Ứng dụng PGP của Alice thực hiện
các bước được minh họa trong :numref:`Figure %s <fig-pgpMessage>`. Đầu tiên, thông điệp
được Alice ký số; MD5, SHA-1, và họ SHA-2 là các hàm băm có thể được sử dụng trong chữ ký số.
Ứng dụng PGP của cô ấy sau đó sinh ra một khóa phiên mới chỉ cho thông điệp này; AES và 3DES
là các mã hóa khóa bí mật được hỗ trợ. Thông điệp đã ký số được mã hóa bằng khóa phiên, sau đó
chính khóa phiên được mã hóa bằng khóa công khai của Bob và được nối vào thông điệp. Ứng dụng PGP
của Alice nhắc nhở cô ấy về mức độ tin cậy mà cô ấy đã gán cho khóa công khai của Bob trước đó,
dựa trên số lượng chứng chỉ cô ấy có cho Bob và độ tin cậy của những người đã ký các chứng chỉ đó.
Cuối cùng, không phải vì bảo mật mà vì email phải được gửi dưới dạng ASCII, một mã hóa base64 được
áp dụng cho thông điệp để chuyển nó sang dạng tương thích ASCII. Khi nhận được thông điệp PGP
trong
email,
ứng dụng PGP của Bob đảo ngược quá trình này từng bước để lấy lại thông điệp bản rõ gốc và
xác
nhận
chữ
ký
số
của Alice—và nhắc Bob về mức độ tin cậy mà anh ấy có với khóa công khai của Alice.

Email có những đặc điểm riêng cho phép PGP nhúng một giao thức xác thực đủ tốt vào giao thức truyền dữ liệu
một thông điệp này, tránh được nhu cầu trao đổi thông điệp trước (và né được một số
phức tạp đã mô tả ở phần trước).
Chữ ký số của Alice là đủ để xác thực cô ấy. Mặc dù không có bằng chứng rằng thông điệp
là kịp thời, nhưng email hợp lệ
cũng không đảm bảo tính kịp thời. Cũng không có bằng chứng rằng thông điệp là nguyên bản,
nhưng Bob là người dùng email
và có lẽ là một con người chịu lỗi, có thể xử lý các email trùng lặp (điều này cũng không phải
là bất thường trong vận hành bình thường).
Alice có thể chắc chắn rằng chỉ Bob mới đọc được thông điệp vì khóa phiên đã được
mã hóa bằng khóa công khai của anh ấy.
Mặc dù giao thức này không chứng minh cho Alice rằng Bob thực sự ở đó và đã nhận email,
một email xác thực từ Bob gửi lại cho Alice
có thể làm điều này.

Phần thảo luận trên là một ví dụ điển hình về lý do tại sao các cơ chế bảo mật
ở lớp ứng dụng lại hữu ích.
Chỉ khi hiểu đầy đủ cách ứng dụng hoạt động, bạn mới có thể đưa ra lựa chọn đúng
về những tấn công nào cần phòng chống
(như email giả mạo) so với những gì có thể bỏ qua (như email bị trễ hoặc bị phát lại).

8.5.2 Secure Shell (SSH)
------------------------

Giao thức Secure Shell (SSH) được sử dụng để cung cấp dịch vụ đăng nhập từ xa,
thay thế cho Telnet kém an toàn được sử dụng trong những ngày đầu của Internet.
(SSH cũng có thể được dùng để thực thi lệnh từ xa và truyền file,
nhưng chúng ta sẽ tập trung trước vào cách SSH hỗ trợ đăng nhập từ xa.)
SSH thường được sử dụng để cung cấp xác thực client/server
mạnh mẽ/toàn vẹn thông điệp—trong đó client SSH chạy trên máy tính để bàn của
người dùng
và server SSH chạy trên một máy từ xa mà người dùng muốn đăng nhập vào—nhưng
nó cũng hỗ trợ tính bí mật. Telnet không cung cấp bất kỳ khả năng nào trong số này.
Lưu ý rằng “SSH” thường được dùng để chỉ cả giao thức SSH
và các ứng dụng sử dụng nó; bạn cần xác định ý nghĩa từ ngữ cảnh.

Để hiểu rõ hơn tầm quan trọng của SSH trên Internet ngày nay, hãy xem xét một số kịch bản sử dụng nó.
Ví dụ, những người làm việc từ xa thường đăng ký với các ISP
cung cấp kết nối cáp quang tốc độ cao đến nhà,
và họ sử dụng các ISP này (cộng với một chuỗi các ISP khác) để kết nối
đến các máy do công ty của họ vận hành.
Điều này có nghĩa là khi một người làm việc từ xa đăng nhập vào một
máy trong trung tâm dữ liệu của công ty,
cả mật khẩu và tất cả dữ liệu gửi/nhận đều có thể đi qua bất kỳ số lượng mạng không tin cậy nào.
SSH cung cấp một cách để mã hóa dữ liệu gửi qua các kết nối này
và tăng cường cơ chế xác thực được sử dụng để đăng nhập.
(Một tình huống tương tự xảy ra khi nhân viên đó kết nối làm việc qua Wi-Fi công cộng ở Starbucks.)
Một cách sử dụng SSH khác là đăng nhập từ xa vào một router,
có thể để thay đổi cấu hình hoặc đọc file log;
rõ ràng, quản trị viên mạng muốn chắc chắn rằng mình có thể đăng nhập
vào router một cách an toàn và không bên trái phép nào
có thể đăng nhập hoặc chặn các lệnh gửi đến router hoặc đầu ra gửi về cho quản trị viên.

Phiên bản mới nhất của SSH, phiên bản 2, bao gồm ba giao thức:

-  SSH-TRANS, một giao thức lớp vận chuyển

-  SSH-AUTH, một giao thức xác thực

-  SSH-CONN, một giao thức kết nối

Chúng ta tập trung vào hai giao thức đầu, liên quan đến đăng nhập từ xa.
Chúng ta sẽ bàn ngắn gọn về mục đích của SSH-CONN ở cuối phần này.

SSH-TRANS cung cấp một kênh mã hóa giữa máy client và server.
Nó chạy trên một kết nối TCP.
Bất cứ khi nào người dùng sử dụng ứng dụng SSH để đăng nhập vào một máy từ xa,
bước đầu tiên là thiết lập một kênh SSH-TRANS giữa hai máy đó.
Hai máy thiết lập kênh bảo mật này bằng cách đầu tiên
để client xác thực server bằng RSA.
Sau khi xác thực, client và server thiết lập một khóa phiên
mà họ sẽ sử dụng để mã hóa bất kỳ dữ liệu nào gửi qua kênh.
Mô tả cấp cao này bỏ qua một số chi tiết, bao gồm cả việc giao thức
SSH-TRANS có một quá trình thương lượng thuật toán mã hóa mà hai bên sẽ sử dụng.
Ví dụ, AES thường được chọn. Ngoài ra, SSH-TRANS bao gồm
kiểm tra toàn vẹn thông điệp cho tất cả dữ liệu trao đổi qua kênh.

Một vấn đề không thể bỏ qua là làm thế nào client có được khóa công khai
của server mà nó cần để xác thực server.
Nghe có vẻ lạ, nhưng server sẽ gửi khóa công khai của nó cho client tại thời điểm kết nối.
Lần đầu tiên một client kết nối đến một server cụ thể,
ứng dụng SSH sẽ cảnh báo người dùng rằng nó chưa từng kết nối với máy này trước đây
và hỏi người dùng có muốn tiếp tục không. Mặc dù đây là một việc rủi ro,
vì SSH thực tế không thể xác thực server,
người dùng thường trả lời “yes” cho câu hỏi này. Ứng dụng SSH
sau đó sẽ ghi nhớ khóa công khai của server,
và lần sau khi người dùng kết nối đến cùng máy đó, nó sẽ so sánh
khóa đã lưu với khóa mà server trả về.
Nếu chúng giống nhau, SSH xác thực server. Nếu khác, ứng dụng SSH
lại cảnh báo người dùng rằng có điều gì đó không ổn,
và người dùng có thể chọn hủy kết nối. Ngoài ra, người dùng cẩn trọng
có thể lấy khóa công khai của server qua một kênh ngoài băng tần,
lưu nó trên máy client, và do đó không bao giờ phải chịu rủi ro “lần đầu”.

Khi kênh SSH-TRANS đã được thiết lập, bước tiếp theo là người dùng thực sự
đăng nhập vào máy, hay cụ thể hơn là xác thực bản thân với server.
SSH cho phép ba cơ chế khác nhau để làm điều này.
Đầu tiên, vì hai máy đang giao tiếp qua một kênh bảo mật,
người dùng chỉ cần gửi mật khẩu của mình cho server.
Điều này không an toàn khi dùng Telnet vì mật khẩu sẽ được gửi dưới dạng bản rõ,
nhưng với SSH thì mật khẩu được mã hóa trong kênh SSH-TRANS.
Cơ chế thứ hai sử dụng mã hóa khóa công khai. Điều này yêu cầu người dùng
đã đặt khóa công khai của mình trên server.
Cơ chế thứ ba, gọi là *host-based authentication*, về cơ bản nói rằng
bất kỳ người dùng nào tự nhận là ai đó từ một tập hợp các máy tin cậy
sẽ tự động được tin là cùng người dùng đó trên server.
Host-based authentication yêu cầu client *host* xác thực bản thân
với server khi kết nối lần đầu;
SSH-TRANS tiêu chuẩn chỉ xác thực server theo mặc định.

Điều quan trọng bạn nên rút ra từ phần này là SSH là một ứng dụng khá trực tiếp
của các giao thức và thuật toán
chúng ta đã thấy trong chương này. Tuy nhiên, điều đôi khi
làm SSH khó hiểu là tất cả các khóa mà người dùng phải tạo và quản lý,
nơi giao diện cụ thể phụ thuộc vào hệ điều hành. Ví dụ, gói OpenSSH
chạy trên hầu hết các máy Unix hỗ trợ một lệnh để tạo cặp khóa công khai/riêng.
Các khóa này sau đó được lưu trong các file khác nhau trong thư mục home
của người dùng. Ví dụ, file ``~/.ssh/known_hosts`` ghi lại các khóa
của tất cả các máy mà người dùng đã đăng nhập vào,
file ``~/.ssh/authorized_keys`` chứa các khóa công khai cần thiết
để xác thực người dùng
khi anh/chị ấy đăng nhập vào máy này (tức là dùng ở phía server),
và file ``~/.ssh/id_rsa`` chứa các khóa riêng cần thiết
để xác thực người dùng
trên các máy từ xa (tức là dùng ở phía client).

.. _fig-ssh-tunnel:
.. figure:: figures/f08-14-9780123850591.png
   :width: 500px
   :align: center

   Sử dụng SSH port forwarding để bảo vệ các ứng dụng dựa trên TCP khác.

Cuối cùng, SSH đã chứng tỏ hữu ích đến mức nó được mở rộng để hỗ trợ các ứng dụng khác,
như gửi và nhận email. Ý tưởng là chạy các ứng dụng này qua một “SSH tunnel” bảo mật.
Khả năng này được gọi là *port forwarding*, và nó sử dụng giao thức SSH-CONN.
Ý tưởng được minh họa trong :numref:`Figure %s <fig-ssh-tunnel>`, nơi ta thấy một client trên máy A
giao tiếp gián tiếp với một server trên máy B bằng cách chuyển tiếp lưu lượng qua một kết nối SSH.
Cơ chế này được gọi là *port forwarding* vì khi các thông điệp đến cổng SSH nổi tiếng trên server,
SSH trước tiên sẽ giải mã nội dung và sau đó “chuyển tiếp” dữ liệu đến cổng thực mà server đang lắng nghe.
Đây chỉ là một loại tunnel khác, trong trường hợp này cung cấp tính bí mật và xác thực.
Có thể cung cấp một dạng mạng riêng ảo (VPN) bằng cách sử dụng các
tunnel
SSH
theo
cách
này.

8.5.3 Bảo mật tầng giao vận (TLS, SSL, HTTPS)
------------------------------------------------

Để hiểu các mục tiêu thiết kế và yêu cầu cho tiêu chuẩn Transport Layer Security (TLS)
và Secure Socket Layer (SSL) mà TLS dựa vào, sẽ hữu ích
khi xem xét một trong những vấn đề chính mà chúng nhằm giải quyết.
Khi World Wide Web trở nên phổ biến và các doanh nghiệp bắt đầu quan tâm đến nó,
rõ ràng là cần có một mức độ bảo mật nào đó cho các giao dịch trên Web.
Ví dụ điển hình là mua hàng bằng thẻ tín dụng.
Có một số vấn đề cần quan tâm khi gửi thông tin thẻ tín dụng
đến một máy tính trên Web. Đầu tiên, bạn có thể lo lắng rằng
thông tin sẽ bị chặn trên đường truyền và sau đó bị sử dụng
để mua hàng trái phép. Bạn cũng có thể lo lắng về việc chi tiết giao dịch
bị sửa đổi, như thay đổi số tiền mua.
Và bạn chắc chắn muốn biết rằng máy tính mà bạn đang gửi
thông tin thẻ tín dụng thực sự thuộc về nhà cung cấp mà bạn muốn,
chứ không phải một bên khác. Do đó, chúng ta thấy ngay nhu cầu về tính bí mật,
toàn vẹn, và xác thực trong các giao dịch Web.
Giải pháp đầu tiên được sử dụng rộng rãi cho vấn đề này là SSL,
ban đầu được phát triển bởi Netscape và sau đó là nền tảng cho tiêu chuẩn TLS
của
IETF.

Các nhà thiết kế SSL và TLS nhận ra rằng các vấn đề này không chỉ riêng
cho các giao dịch Web (tức là các giao dịch sử dụng HTTP)
và thay vào đó xây dựng một giao thức đa dụng nằm giữa
một giao thức ứng dụng như HTTP và một giao thức vận chuyển như TCP.
Lý do gọi đây là “bảo mật lớp vận chuyển” là vì, từ góc nhìn của ứng dụng,
lớp giao thức này trông giống như một giao thức vận chuyển bình thường
ngoại trừ việc nó an toàn. Tức là, người gửi có thể mở kết nối
và gửi byte để truyền, và lớp vận chuyển bảo mật sẽ chuyển chúng đến người nhận
với tính bí mật, toàn vẹn, và xác thực cần thiết.
Bằng cách chạy lớp vận chuyển bảo mật trên TCP, tất cả các tính năng bình thường của TCP
(độ tin cậy, kiểm soát lưu lượng, kiểm soát tắc nghẽn, v.v.)
cũng được cung cấp cho ứng dụng. Sơ đồ các lớp giao thức này được minh họa
trong
:numref:`Figure %s <fig-tls-stack>`.

.. _fig-tls-stack:
.. figure:: figures/f08-15-9780123850591.png
   :width: 300px
   :align: center

   Lớp vận chuyển bảo mật được chèn giữa lớp ứng dụng và lớp TCP.

Khi HTTP được sử dụng theo cách này, nó được gọi là HTTPS (HTTP bảo mật).
Thực tế, bản thân HTTP không thay đổi.
Nó chỉ gửi dữ liệu đến và nhận dữ liệu từ lớp SSL/TLS thay vì TCP.
Để thuận tiện, một cổng TCP mặc định đã được gán cho HTTPS (443).
Tức là, nếu bạn cố gắng kết nối đến một server trên cổng TCP 443,
bạn có thể sẽ nói chuyện với giao thức SSL/TLS, giao thức này sẽ
chuyển dữ liệu của bạn đến HTTP nếu mọi việc xác thực và giải mã diễn ra suôn sẻ.
Mặc dù có các hiện thực độc lập của SSL/TLS,
nhưng phổ biến hơn là hiện thực được đóng gói cùng các ứng dụng cần nó,
chủ yếu là trình duyệt web.

Trong phần còn lại của thảo luận về bảo mật lớp vận chuyển,
chúng ta tập trung vào TLS.
Mặc dù SSL và TLS không tương thích với nhau, chúng chỉ khác nhau ở một số điểm nhỏ,
nên hầu hết mô tả về TLS dưới đây cũng áp dụng cho SSL.

Giao Thức Bắt Tay
~~~~~~~~~~~~~~~~~

Một cặp thành viên TLS thương lượng tại thời gian chạy sẽ sử dụng mật mã nào.
Các thành viên thương lượng lựa chọn:

-  Hàm băm toàn vẹn dữ liệu (MD5, SHA-1, v.v.), dùng để hiện thực HMAC

-  mã hóa khóa bí mật cho tính bí mật (các lựa chọn bao gồm
   DES, 3DES, và AES)

-  Phương pháp thiết lập khóa phiên (các lựa chọn bao gồm Diffie-Hellman,
   và các giao thức xác thực khóa công khai sử dụng DSS)

Điều thú vị là các thành viên cũng có thể thương lượng
việc sử dụng thuật toán nén,
không phải vì điều này mang lại lợi ích bảo mật,
mà vì nó dễ thực hiện khi bạn đã quyết định thực hiện
các thao tác tốn kém trên từng byte dữ liệu.

Trong TLS, mã hóa bảo mật sử dụng hai khóa, một cho mỗi chiều,
và tương tự hai vector khởi tạo.
Các HMAC cũng được khóa bằng các khóa khác nhau cho hai thành viên.
Do đó, bất kể lựa chọn mã hóa và hàm băm,
một phiên TLS thực tế cần sáu khóa.
TLS sinh ra tất cả các khóa này từ một *master secret* chung.
Master secret là một giá trị 384-bit (48-byte) kế thừa từ một phần từ “khóa phiên”
(session key) là kết quả của giao thức thiết lập khóa phiên của TLS.

Phần của TLS chịu trách nhiệm thương lượng các lựa chọn
và thiết lập master secret chung được gọi là *giao thức bắt tay* (handshake protocol).
(Việc truyền dữ liệu thực tế được thực hiện bởi *giao thức bản ghi* (record protocol) của TLS.)
Giao thức bắt tay về bản chất là một giao thức thiết lập khóa phiên,
với master secret thay cho khóa phiên.
Vì TLS hỗ trợ nhiều phương pháp thiết lập khóa phiên, nên có các biến thể giao thức tương ứng.
Hơn nữa, giao thức bắt tay hỗ trợ lựa chọn giữa xác thực lẫn nhau
của cả hai thành viên, xác thực chỉ một thành viên
(đây là trường hợp phổ biến nhất, như xác thực một website
nhưng không xác thực người dùng), hoặc không xác thực gì cả (Diffie-Hellman ẩn danh).
Do đó, giao thức bắt tay kết hợp nhiều giao thức thiết lập khóa phiên
thành một giao thức duy nhất.

:numref:`Figure %s <fig-tls-hand>` cho thấy giao thức bắt tay ở mức cao.
Client ban đầu gửi một danh sách các tổ hợp thuật toán mật mã mà nó hỗ trợ,
theo thứ tự ưu tiên giảm dần.
Server phản hồi, đưa ra tổ hợp thuật toán mật mã duy nhất mà
nó chọn từ danh sách của client.
Các thông điệp này cũng chứa một *client nonce* và một *server nonce*,
lần lượt sẽ được sử dụng để sinh master secret sau
này.

.. _fig-tls-hand:
.. figure:: figures/f08-16-9780123850591.png
   :width: 300px
   :align: center

   Giao thức bắt tay để thiết lập phiên TLS.

Tại thời điểm này, giai đoạn thương lượng đã hoàn tất.
Server bây giờ gửi các thông điệp bổ sung dựa trên giao thức thiết lập khóa phiên đã thương lượng.
Điều này có thể bao gồm gửi một chứng chỉ khóa công khai
hoặc một tập hợp các tham số Diffie-Hellman.
Nếu server yêu cầu xác thực client, nó sẽ gửi một thông điệp riêng để chỉ ra điều đó.
Client sau đó phản hồi với phần của mình trong giao thức trao đổi khóa đã thương lượng.

Bây giờ client và server đều có thông tin cần thiết để sinh master secret.
“Khóa phiên” mà họ trao đổi thực ra không phải là một khóa,
mà là cái mà TLS gọi là *pre-master secret*.
Master secret được tính toán (bằng một thuật toán công khai)
từ pre-master secret này, client nonce, và server nonce.
Sử dụng các khóa sinh ra từ master secret, client sau đó gửi một thông điệp
bao gồm một hàm băm của tất cả các thông điệp bắt tay trước đó,
server phản hồi lại bằng một thông điệp tương tự.
Điều này cho phép họ phát hiện bất kỳ sai lệch nào giữa
các thông điệp bắt tay đã gửi và nhận,
chẳng hạn như nếu một kẻ tấn công trung gian sửa đổi thông điệp client
ban đầu chưa mã hóa để làm yếu các lựa chọn thuật toán mật mã.

Giao Thức Bản Ghi
~~~~~~~~~~~~~~~~~

Trong một phiên được thiết lập bởi giao thức bắt tay, giao thức bản ghi
(record protocol) của TLS bổ sung tính bí mật và toàn vẹn cho dịch vụ
vận chuyển bên dưới. Các thông điệp được gửi xuống từ lớp ứng dụng sẽ:

1. Được phân mảnh hoặc gộp thành các khối có kích thước thuận tiện cho
   các bước tiếp theo

2. Có thể được nén

3. Được bảo vệ toàn vẹn bằng HMAC

4. Được mã hóa bằng mã hóa khóa bí mật

5. Được chuyển xuống lớp vận chuyển (thường là TCP) để truyền đi

Giao thức bản ghi sử dụng HMAC làm bộ xác thực. HMAC sử dụng bất kỳ hàm băm nào
(MD5, SHA-1, v.v.) đã được các thành viên thương lượng.
Client và server có các khóa khác nhau để tính HMAC,
làm cho việc phá mã càng khó hơn.
Hơn nữa, mỗi thông điệp giao thức bản ghi được gán một số thứ tự,
số này được đưa vào khi tính HMAC—mặc dù số thứ tự này
không bao giờ xuất hiện rõ ràng trong thông điệp.
Số thứ tự ngầm này ngăn chặn các cuộc tấn công phát lại hoặc sắp xếp lại thông điệp.
Điều này là cần thiết vì, mặc dù TCP có thể chuyển các thông điệp tuần tự,
không trùng lặp lên lớp trên trong điều kiện bình thường,
nhưng các giả định đó không bao gồm trường hợp có kẻ tấn công có thể chặn,
sửa đổi, hoặc gửi thông điệp TCP giả mạo.
Mặt khác, chính các đảm bảo chuyển giao của TCP cho phép TLS
dựa vào việc một thông điệp TLS hợp lệ sẽ có số thứ tự ngầm tiếp theo.

Một tính năng thú vị khác của giao thức TLS là khả năng
tiếp tục một phiên (session resumption).
Để hiểu động cơ ban đầu cho điều này, sẽ hữu ích khi hiểu
cách HTTP ban đầu sử dụng các kết nối TCP.
(Một số chi tiết về HTTP sẽ được trình bày ở chương sau.)
Mỗi thao tác HTTP, như lấy một trang từ server, yêu cầu mở một kết nối TCP mới.
Lấy một trang duy nhất với nhiều đối tượng đồ họa nhúng có thể cần nhiều kết nối TCP.
Mở một kết nối TCP yêu cầu một quá trình bắt tay ba bước
trước khi truyền dữ liệu có thể bắt đầu.
Khi kết nối TCP đã sẵn sàng nhận dữ liệu, client sau đó cần bắt đầu giao thức bắt tay TLS,
mất ít nhất hai lần khứ hồi nữa (và tiêu tốn một lượng tài nguyên xử lý
và băng thông mạng) trước khi dữ liệu ứng dụng thực sự có thể được gửi.
Khả năng tiếp tục phiên của TLS được thiết kế để giảm bớt vấn đề này.

Ý tưởng của tiếp tục phiên là tối ưu hóa bằng cách bỏ qua
quá trình bắt tay trong những trường hợp client
và server đã thiết lập một trạng thái chung trước đó.
Client chỉ cần đưa ID phiên từ một phiên đã thiết lập trước đó vào thông điệp bắt tay ban đầu.
Nếu server thấy rằng nó vẫn còn trạng thái cho phiên đó,
và tùy chọn tiếp tục đã được thương lượng khi phiên đó được tạo,
thì server có thể trả lời client với một chỉ báo thành công,
và việc truyền dữ liệu có thể bắt đầu sử dụng các thuật toán
và tham số đã thương lượng trước đó.
Nếu ID phiên không khớp với bất kỳ trạng thái phiên nào được lưu ở server,
hoặc nếu tiếp tục không được phép cho phiên đó,
thì server sẽ quay lại quá trình bắt tay bình thường.

Lý do phần thảo luận trên nhấn mạnh động cơ *ban đầu* là
vì việc phải thực hiện bắt tay TCP cho mỗi đối tượng nhúng trong một trang web
dẫn đến quá nhiều chi phí, độc lập với TLS, nên HTTP cuối cùng đã
được tối ưu hóa để hỗ trợ *kết nối liên tục* (persistent connections)
(cũng sẽ được bàn ở chương sau).
Vì việc tối ưu hóa HTTP đã giảm giá trị
của tiếp tục phiên trong TLS (cộng với nhận thức rằng việc tái sử dụng cùng ID phiên
và khóa master secret trong một loạt các phiên tiếp tục là một rủi ro bảo mật),
TLS đã thay đổi cách tiếp cận tiếp tục phiên trong phiên bản mới nhất (1.3).

Trong TLS 1.3, client gửi một *session ticket* không rõ nội dung,
được server mã hóa, cho server khi tiếp tục phiên.
Ticket này chứa tất cả thông tin cần thiết để tiếp tục phiên.
Cùng một master secret được sử dụng qua các lần bắt tay,
nhưng hành vi mặc định là thực hiện trao đổi khóa phiên khi tiếp tục.

.. _key-layering:
.. admonition:: Bài Học Rút Ra

   Chúng tôi nhấn mạnh sự thay đổi này trong TLS vì nó minh họa thách thức
   khi xác định lớp nào nên giải quyết một vấn đề nhất định.
   Khi xét riêng, tiếp tục phiên như được hiện thực trong
   phiên bản trước của TLS có vẻ là một ý tưởng hay,
   nhưng nó cần được xem xét trong bối cảnh trường hợp sử dụng chủ đạo, đó là HTTP.
   Khi chi phí của việc thực hiện nhiều kết nối TCP đã được HTTP giải quyết,
   phương trình cho cách tiếp tục nên được TLS hiện thực đã thay đổi.
   Bài học lớn hơn là chúng ta cần tránh tư duy cứng nhắc về lớp nào là đúng
   để hiện thực một chức năng—câu trả lời thay đổi theo thời gian
   khi mạng phát triển—nơi cần một phân tích tổng thể/xuyên lớp
   để có thiết kế đúng. :ref:`[Next] <key-naming>`

8.5.4 Bảo mật IP
-------------------------

Có lẽ nỗ lực tham vọng nhất để tích hợp bảo mật vào Internet diễn ra ở lớp IP.
Hỗ trợ cho bảo mật IP (IP Security—IPsec), như kiến trúc này được gọi,
là tùy chọn trong IPv4 nhưng bắt buộc trong IPv6.

IPsec thực chất là một khung (thay vì một giao thức hoặc hệ thống đơn lẻ)
để cung cấp tất cả các dịch vụ bảo mật đã thảo luận trong chương này.
IPsec cung cấp ba mức độ tự do. Thứ nhất, nó có tính mô-đun cao, cho phép người dùng
(hoặc có lẽ là quản trị viên hệ thống) chọn từ nhiều thuật toán mật mã
và giao thức bảo mật chuyên biệt. Thứ hai, IPsec cho phép người dùng chọn từ
một danh mục lớn các thuộc tính bảo mật, bao gồm kiểm soát truy cập,
toàn vẹn, xác thực, tính nguyên bản, và tính bí mật.
Thứ ba, IPsec có thể được sử dụng để bảo vệ các luồng hẹp
(ví dụ, các gói thuộc về một kết nối TCP cụ thể giữa một cặp máy chủ)
hoặc các luồng rộng (ví dụ, tất cả các gói
chảy giữa một cặp router).

Khi nhìn ở mức cao, IPsec bao gồm hai phần.
Phần đầu tiên là một cặp giao thức hiện thực các dịch vụ bảo mật sẵn có.
Chúng là Authentication Header (AH), cung cấp kiểm soát truy cập,
toàn vẹn thông điệp không kết nối, xác thực,
và bảo vệ chống phát lại, và Encapsulating Security Payload (ESP),
hỗ trợ các dịch vụ này cộng thêm tính bí mật.
AH hiếm khi được sử dụng nên chúng ta tập trung vào ESP ở đây.
Phần thứ hai là hỗ trợ quản lý khóa, nằm dưới một giao thức
gọi là Internet Security Association and Key Management Protocol (ISAKMP).

Trừu tượng liên kết hai phần này là *security association* (SA).
SA là một kết nối simplex (một chiều) với một hoặc nhiều thuộc tính bảo mật sẵn có.
Bảo vệ một giao tiếp hai chiều giữa một cặp
máy chủ—tương ứng với một kết nối TCP,
chẳng hạn—cần hai SA, một cho mỗi chiều.
Mặc dù IP là một giao thức không kết nối, bảo mật lại phụ thuộc vào
thông tin trạng thái kết nối như khóa và số thứ tự.
Khi được tạo, một SA được gán một số ID gọi là
*security parameters index* (SPI) bởi máy nhận.
Kết hợp SPI này và địa chỉ IP đích xác định duy nhất một SA.
Một header ESP bao gồm SPI để máy chủ nhận có thể xác định gói đến thuộc về SA nào
và do đó áp dụng thuật toán và khóa nào cho gói đó.

Các SA được thiết lập, thương lượng, sửa đổi, và xóa bằng ISAKMP.
Nó định nghĩa các định dạng gói để trao đổi dữ liệu sinh khóa và xác thực.
Các định dạng này không quá thú vị vì chúng chỉ cung cấp một khung—dạng
cụ thể của khóa và dữ liệu xác thực phụ thuộc vào kỹ thuật sinh khóa,
mã hóa, và cơ chế xác thực được sử dụng. Hơn nữa,
ISAKMP không chỉ định
một giao thức trao đổi khóa cụ thể,
mặc dù nó gợi ý Internet Key Exchange (IKE) là một khả năng, và IKE v2
là cái được sử dụng trong thực tế.

ESP là giao thức được sử dụng để truyền dữ liệu an toàn qua một SA đã thiết lập.
Trong IPv4, header ESP nằm sau header IP; trong IPv6, nó là một header mở rộng.
Định dạng của nó sử dụng cả header và trailer,
như minh họa trong :numref:`Figure %s <fig-esp>`.
Trường ``SPI`` cho phép máy chủ nhận xác định security association mà gói thuộc về.
Trường ``SeqNum`` bảo vệ chống lại các cuộc tấn công phát lại.
Trường ``PayloadData`` của gói chứa dữ liệu được mô tả bởi trường ``NextHdr``.
Nếu tính bí mật được chọn, dữ liệu sẽ được mã hóa bằng
bất kỳ mã hóa nào được liên kết với SA.
Trường ``PadLength`` ghi lại lượng padding đã thêm vào dữ liệu; padding
đôi khi cần thiết vì, ví dụ,
mã hóa yêu cầu bản rõ phải là bội số của một số byte nhất định
hoặc để đảm bảo ciphertext kết thúc ở ranh giới 4 byte.
Cuối cùng, ``AuthenticationData`` mang bộ xác thực.

.. _fig-esp:
.. figure:: figures/f08-17-9780123850591.png
   :width: 500px
   :align: center

   Định dạng ESP của IPSec.

IPsec hỗ trợ *tunnel mode* cũng như *transport mode* đơn giản hơn.
Mỗi SA hoạt động ở một trong hai chế độ này. Trong một SA chế độ transport,
payload data của ESP đơn giản là một thông điệp cho một lớp cao hơn như UDP hoặc TCP.
Ở chế độ này, IPsec hoạt động như một lớp giao thức trung gian,
giống như SSL/TLS giữa TCP và lớp cao hơn.
Khi nhận được một thông điệp ESP, payload của nó được chuyển lên giao thức lớp
cao hơn.

Tuy nhiên, trong một SA chế độ tunnel, payload data của ESP bản thân nó
là một gói IP, như trong :numref:`Figure %s <fig-espTunnelPacket>`.
Nguồn và đích của gói IP bên trong này
có thể khác với gói IP bên ngoài.
Khi nhận được một thông điệp ESP, payload của nó
được chuyển tiếp như một gói IP bình thường.
Cách sử dụng ESP phổ biến nhất là xây dựng một “IPsec tunnel”
giữa hai router, thường là firewall.
Ví dụ, một công ty muốn kết nối hai site qua Internet
có thể mở một cặp SA chế độ tunnel giữa một router ở mỗi site.
Một gói IP đi ra từ một site, tại router đi ra, sẽ trở thành payload
của một thông điệp ESP gửi đến router ở site kia.
Router nhận sẽ gỡ bỏ payload IP packet và chuyển tiếp nó đến đích thực sự.

.. _fig-espTunnelPacket:
.. figure:: figures/f08-18-9780123850591.png
   :width: 600px
   :align: center

   Một gói IP với một gói IP lồng bên trong được đóng gói bằng ESP ở chế độ tunnel.
   Lưu ý rằng các gói bên trong và bên ngoài có địa chỉ khác nhau.

Các tunnel này cũng có thể được cấu hình để sử dụng ESP với tính bí mật và xác thực,
do đó ngăn chặn truy cập trái phép vào dữ liệu đi qua liên kết ảo này
và đảm bảo không có dữ liệu giả mạo nào được nhận ở đầu bên kia của tunnel.
Hơn nữa, các tunnel có thể cung cấp tính bí mật lưu lượng,
vì việc ghép nhiều luồng qua một tunnel duy nhất
che giấu thông tin về lượng lưu lượng
giữa các điểm cuối cụ thể. Một mạng các tunnel như vậy có thể được sử dụng
để hiện thực một mạng riêng ảo hoàn chỉnh.
Các máy chủ giao tiếp qua VPN thậm chí không cần biết rằng nó tồn tại.

8.5.5 Bảo Mật Không Dây (802.11i)
---------------------------------

Liên kết không dây đặc biệt dễ bị đe dọa bảo mật do thiếu
bảo vệ vật lý cho môi trường truyền dẫn.
Mặc dù sự tiện lợi của 802.11 đã thúc đẩy việc chấp nhận rộng rãi công nghệ này,
thiếu bảo mật đã là một vấn đề lặp đi lặp lại.
Ví dụ, rất dễ dàng cho một nhân viên của công ty kết nối
một điểm truy cập 802.11 vào mạng công ty.
Vì sóng vô tuyến xuyên qua hầu hết các bức tường,
nếu điểm truy cập không có biện pháp bảo mật đúng,
một kẻ tấn công có thể truy cập vào mạng công ty từ bên ngoài tòa nhà.
Tương tự, một máy tính với card mạng không dây bên trong tòa nhà
có thể kết nối đến một điểm truy cập bên ngoài tòa nhà,
có thể khiến nó bị tấn công, chưa kể đến phần còn lại của mạng công ty
nếu máy tính đó cũng có kết nối Ethernet.

Do đó, đã có nhiều nỗ lực để bảo vệ các liên kết Wi-Fi.
Điều khá bất ngờ là một trong những kỹ thuật bảo mật đầu tiên
phát triển cho 802.11, gọi là Wired Equivalent Privacy (WEP),
hóa ra lại có lỗ hổng nghiêm trọng và rất dễ bị phá vỡ.

Tiêu chuẩn IEEE 802.11i cung cấp xác thực, toàn vẹn thông điệp,
và tính bí mật cho 802.11 (Wi-Fi) ở lớp liên kết.
*WPA3* (Wi-Fi Protected Access 3) thường được dùng như một từ đồng nghĩa với 802.11i,
mặc dù về mặt kỹ thuật nó là một nhãn hiệu của Wi-Fi Alliance
chứng nhận sản phẩm tuân thủ 802.11i.

Để tương thích ngược, 802.11i bao gồm các định nghĩa về
các thuật toán bảo mật thế hệ đầu tiên—bao gồm cả WEP—mà
hiện nay đã được biết là có lỗ hổng bảo mật lớn.
Chúng ta sẽ tập trung vào các thuật toán mới, mạnh hơn của 802.11i.

Xác thực 802.11i hỗ trợ hai chế độ.
Trong cả hai chế độ, kết quả cuối cùng của xác thực thành công là một Pairwise Master Key chung.
*Chế độ cá nhân*, còn gọi là *Pre-Shared Key (PSK) mode*,
cung cấp bảo mật yếu hơn nhưng thuận tiện và kinh tế hơn cho các tình huống
như mạng 802.11 gia đình.
Thiết bị không dây và Access Point (AP)
được cấu hình trước với một *passphrase* chung—về cơ bản là một mật khẩu rất dài—từ đó
Pairwise Master Key được sinh ra bằng phương pháp mật mã.

Chế độ xác thực mạnh hơn của 802.11i dựa trên khung IEEE 802.1X
để kiểm soát truy cập vào LAN,
sử dụng một Authentication Server (AS)
như trong :numref:`Figure %s <fig-AuthenServer>`.
AS và AP phải được kết nối bằng một kênh bảo mật và thậm chí có thể
được hiện thực như một thiết bị duy nhất,
nhưng về mặt logic chúng là riêng biệt. AP chuyển tiếp các thông điệp
xác thực giữa thiết bị không dây và AS.
Giao thức dùng cho xác thực gọi là *Extensible Authentication Protocol* (EAP).
EAP được thiết kế để hỗ trợ nhiều phương pháp xác thực—thẻ thông minh,
Kerberos, mật khẩu dùng một lần,
xác thực khóa công khai, v.v.—cũng như xác thực một chiều và xác thực lẫn nhau.
Vì vậy, EAP nên được coi là một khung xác thực hơn là một giao thức.
Các giao thức cụ thể tuân thủ EAP, trong đó có nhiều loại, được gọi là *EAP methods*.
Ví dụ, EAP-TLS là một phương pháp EAP dựa trên xác thực TLS.

.. _fig-AuthenServer:
.. figure:: figures/f08-19-9780123850591.png
   :width: 500px
   :align: center

   Sử dụng Authentication Server trong 802.11i.

802.11i không đặt ra bất kỳ giới hạn nào về cơ sở xác thực
mà phương pháp EAP có thể sử dụng.
Tuy nhiên, nó yêu cầu một phương pháp EAP thực hiện xác thực *lẫn nhau*,
vì chúng ta không chỉ muốn ngăn kẻ tấn công truy cập mạng qua AP của mình,
mà còn muốn ngăn kẻ tấn công lừa thiết bị không dây của mình bằng một AP giả mạo, độc hại.
Kết quả cuối cùng của xác thực thành công là một Pairwise Master Key
được chia sẻ giữa thiết bị không dây và AS,
sau đó AS chuyển nó cho AP.

Một trong những khác biệt chính giữa chế độ dựa trên AS mạnh hơn và
chế độ cá nhân yếu hơn là chế độ đầu tiên dễ dàng hỗ trợ một khóa duy nhất cho mỗi client.
Điều này giúp dễ dàng thay đổi tập hợp client có thể xác thực bản thân
(ví dụ, thu hồi quyền truy cập của một client)
mà không cần thay đổi bí mật lưu trên mọi client.

Khi đã có Pairwise Master Key, thiết bị không dây và AP thực hiện một giao thức
thiết lập khóa phiên gọi là 4-way handshake
để thiết lập Pairwise Transient Key. Pairwise Transient Key
thực chất là một tập hợp các khóa bao gồm một khóa phiên gọi là *Temporal Key*.
Khóa phiên này được sử dụng bởi giao thức gọi là *CCMP*,
cung cấp tính bí mật và toàn vẹn dữ liệu cho 802.11i.

CCMP là viết tắt của CTR (Counter Mode) với CBC-MAC
(Cipher-Block Chaining with Message Authentication Code) Protocol.
CCMP sử dụng AES ở chế độ counter để mã hóa cho tính bí mật.
Nhớ lại rằng trong mã hóa counter,
các giá trị liên tiếp của một bộ đếm được tích hợp vào việc mã hóa các khối bản rõ liên tiếp.

CCMP sử dụng một Message Authentication Code (MAC) làm bộ xác thực.
Thuật toán MAC dựa trên CBC,
mặc dù CCMP không sử dụng CBC trong mã hóa bảo mật.
Thực chất, CBC được thực hiện mà không truyền bất kỳ khối CBC nào,
chỉ để khối CBC cuối cùng được dùng làm MAC
(chỉ 8 byte đầu tiên thực sự được sử dụng).
Vai trò của vector khởi tạo được đảm nhiệm bởi một khối đầu tiên
được xây dựng đặc biệt bao gồm một số gói 48-bit—một số thứ tự.
(Số gói này cũng được tích hợp vào mã hóa bảo mật và dùng để phát hiện các cuộc tấn công phát lại.)
MAC sau đó được mã hóa cùng với bản rõ để ngăn chặn
các cuộc tấn công ngày sinh nhật,
vốn dựa vào việc tìm các thông điệp khác nhau có cùng bộ xác thực.

8.5.6 Tường Lửa
---------------

Trong khi phần lớn chương này tập trung vào việc sử dụng mật mã
để cung cấp các tính năng bảo mật như xác thực và tính bí mật,
có một tập hợp các vấn đề bảo mật không dễ dàng giải quyết bằng các phương tiện mật mã.
Ví dụ, sâu và virus lây lan bằng cách khai thác lỗi trong hệ điều hành
và chương trình ứng dụng (và đôi khi cả sự cả tin của con người),
và không có lượng mật mã nào có thể giúp bạn nếu máy của bạn có lỗ hổng chưa được vá.
Vì vậy, các phương pháp khác thường được sử dụng để ngăn chặn
các loại lưu lượng có thể gây hại khác nhau.
Tường lửa là một trong những cách phổ biến nhất để làm điều này.

Tường lửa là một hệ thống thường đặt tại một điểm kết nối
giữa một site mà nó bảo vệ và phần còn lại của mạng,
như minh họa trong :numref:`Figure %s <fig-firewall>`.
Nó thường được hiện thực như một “appliance” hoặc một phần của router,
mặc dù một “tường lửa cá nhân” có thể được hiện thực trên máy người dùng cuối.
Bảo mật dựa trên tường lửa phụ thuộc vào việc tường lửa
là kết nối duy nhất đến site từ bên ngoài;
không nên có cách nào để vượt qua tường lửa qua các gateway khác,
kết nối không dây, hoặc kết nối quay số.
Ẩn dụ “tường” có phần gây hiểu nhầm trong bối cảnh mạng
vì rất nhiều lưu lượng đi qua tường lửa.
Một cách để nghĩ về tường lửa là mặc định nó chặn lưu lượng
trừ phi lưu lượng đó được cho phép đi qua.
Ví dụ, nó có thể lọc tất cả các thông điệp đến trừ những thông điệp
gửi đến một tập hợp địa chỉ IP hoặc cổng TCP cụ thể.

.. _fig-firewall:
.. figure:: figures/f08-20-9780123850591.png
   :width: 600px
   :align: center

   Một tường lửa lọc các gói chảy giữa một site
   và phần còn lại của Internet.

Thực chất, tường lửa chia mạng thành một vùng tin cậy hơn bên trong tường lửa
và một vùng ít tin cậy hơn bên ngoài tường lửa.
Điều này hữu ích nếu bạn không muốn người dùng bên ngoài
truy cập vào một máy chủ hoặc dịch vụ cụ thể trong site của mình.
Phần lớn sự phức tạp đến từ việc bạn muốn cho phép
các loại truy cập khác nhau cho các người dùng bên ngoài khác nhau,
từ công chúng nói chung, đối tác kinh doanh, đến các thành viên tổ chức ở xa.
Tường lửa cũng có thể áp đặt các hạn chế lên lưu lượng đi ra
để ngăn chặn một số tấn công và hạn chế thiệt hại
nếu một kẻ tấn công thành công truy cập vào bên trong tường lửa.

Vị trí của tường lửa cũng thường là ranh giới giữa các vùng
có thể định địa chỉ toàn cầu và các vùng sử dụng địa chỉ cục bộ.
Do đó, chức năng Network Address Translation (NAT) và
chức năng tường lửa thường được tìm thấy trong cùng một thiết bị,
mặc dù về mặt logic chúng là riêng biệt.

Tường lửa có thể được sử dụng để tạo nhiều *vùng tin cậy* (zones of trust),
chẳng hạn như một hệ phân cấp các vùng ngày càng tin cậy hơn.
Một cách sắp xếp phổ biến bao gồm ba vùng tin cậy: mạng nội bộ,
*DMZ* (“demilitarized zone”); và phần còn lại của Internet.
DMZ được dùng để chứa các dịch vụ như DNS và máy chủ email cần truy cập từ bên ngoài.
Cả mạng nội bộ và thế giới bên ngoài đều có thể truy cập DMZ,
nhưng các máy chủ trong DMZ không thể truy cập mạng nội bộ;
do đó, một kẻ tấn công thành công xâm nhập một máy chủ trong DMZ
vẫn không thể truy cập mạng nội bộ.
DMZ có thể được khôi phục định kỳ về trạng thái sạch.

Tường lửa lọc dựa trên thông tin IP, TCP, và UDP, cùng các thông tin khác.
Chúng được cấu hình với một bảng địa chỉ đặc trưng cho các gói
mà chúng sẽ, và sẽ không, chuyển tiếp.
Bằng “địa chỉ”, chúng tôi muốn nói nhiều hơn chỉ địa chỉ IP đích,
mặc dù đó là một khả năng.
Thông thường, mỗi mục trong bảng là một bộ 4 phần tử:
Nó cho địa chỉ IP và số cổng TCP (hoặc UDP) cho cả nguồn và đích.

Ví dụ, một tường lửa có thể được cấu hình để lọc (không chuyển tiếp)
tất cả các gói khớp với mô tả sau:

.. code:: c

   (192.12.13.14, 1234, 128.7.6.5, 80)

Mẫu này nói rằng hãy loại bỏ tất cả các gói từ cổng 1234
trên máy 192.12.13.14 gửi đến cổng 80 trên máy 128.7.6.5.
(Cổng 80 là cổng TCP nổi tiếng cho HTTP.) Tất nhiên,
thường không thực tế để liệt kê mọi máy nguồn mà bạn muốn lọc gói,
nên các mẫu có thể bao gồm ký tự đại diện. Ví dụ,

.. code:: c

   (*,  *, 128.7.6.5, 80)

nói rằng hãy lọc tất cả các gói gửi đến cổng 80 trên 128.7.6.5,
bất kể máy nguồn hoặc cổng nào gửi gói.
Lưu ý rằng các mẫu địa chỉ như vậy yêu cầu tường lửa đưa ra quyết định
chuyển tiếp/lọc dựa trên số cổng lớp 4,
ngoài địa chỉ máy chủ lớp 3. Vì lý do này,
tường lửa lớp mạng đôi khi được gọi là *switch lớp 4*.

Trong phần thảo luận trên, tường lửa chuyển tiếp mọi thứ trừ khi được chỉ định
lọc một số loại gói nhất định.
Một tường lửa cũng có thể lọc mọi thứ trừ khi được chỉ định chuyển tiếp,
hoặc sử dụng kết hợp hai chiến lược.
Ví dụ, thay vì chặn truy cập đến cổng 80 trên máy 128.7.6.5,
tường lửa có thể được chỉ định chỉ cho phép truy cập đến cổng 25
(cổng SMTP mail) trên một máy chủ mail cụ thể, như

.. code:: c

   (*,  *, 128.19.20.21, 25)

nhưng chặn tất cả lưu lượng khác. Kinh nghiệm cho thấy tường lửa
rất thường xuyên được cấu hình sai, cho phép truy cập không an toàn.
Một phần của vấn đề là các quy tắc lọc có thể chồng lấn nhau một
cách phức tạp, khiến quản trị viên hệ thống khó diễn đạt đúng ý định lọc.
Một nguyên tắc thiết kế tối đa hóa bảo mật là cấu hình tường lửa
để loại bỏ tất cả các gói trừ những gói được cho phép rõ ràng.
Tất nhiên, điều này có nghĩa là một số ứng dụng hợp lệ có thể bị
vô hiệu hóa một cách tình cờ; giả sử người dùng của các ứng dụng đó cuối cùng sẽ nhận ra
và yêu cầu quản trị viên hệ thống thực hiện thay đổi phù hợp.

Nhiều ứng dụng client/server gán động một cổng cho client.
Nếu một client bên trong tường lửa khởi tạo truy cập đến một server
bên ngoài, phản hồi của server sẽ được gửi đến cổng được gán động.
Điều này đặt ra một vấn đề: Làm thế nào để cấu hình tường lửa
cho phép gói phản hồi của server bất kỳ nhưng không cho phép một gói tương tự
mà không có yêu cầu từ client? Điều này không thể thực hiện
với một *tường lửa không trạng thái* (stateless firewall), vì nó đánh giá
từng gói một cách độc lập. Nó đòi hỏi một *tường lửa có trạng thái* (stateful firewall),
theo dõi trạng thái của từng kết nối. Một gói đến được gửi đến một cổng
được gán động sẽ chỉ được cho phép nếu nó là phản hồi hợp lệ
trong trạng thái hiện tại của kết nối trên cổng đó.

Tường lửa hiện đại cũng hiểu và lọc dựa trên nhiều giao thức lớp
ứng dụng cụ thể như HTTP, Telnet, hoặc FTP.
Chúng sử dụng thông tin cụ thể của giao thức đó, như URL trong
trường hợp HTTP, để quyết định có loại bỏ một thông điệp hay không.

Ưu Nhược Điểm Của Tường Lửa
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Tốt nhất, tường lửa bảo vệ một mạng khỏi truy cập không mong muốn
từ phần còn lại của Internet;
nó không thể cung cấp bảo mật cho giao tiếp hợp lệ giữa bên trong và bên ngoài tường lửa.
Ngược lại, các cơ chế bảo mật dựa trên mật mã được mô tả trong chương
này có khả năng cung cấp giao tiếp an toàn
giữa bất kỳ thành viên nào ở bất kỳ đâu. Nếu vậy, tại sao tường lửa lại phổ biến đến vậy?
Một lý do là tường lửa có thể được triển khai một cách đơn phương,
sử dụng các sản phẩm thương mại trưởng thành,
trong khi bảo mật dựa trên mật mã đòi hỏi hỗ trợ ở cả hai đầu của giao tiếp.
Một lý do cơ bản hơn cho sự thống trị của tường lửa là chúng tập trung hóa bảo mật vào một nơi,
thực chất là tách bảo mật ra khỏi phần còn lại của mạng.
Quản trị viên hệ thống có thể quản lý tường lửa để cung cấp bảo mật,
giải phóng người dùng và ứng dụng bên trong tường lửa khỏi các mối quan tâm bảo mật—
ít nhất là một số loại mối quan tâm bảo mật.

Đáng tiếc, tường lửa có những hạn chế nghiêm trọng.
Vì tường lửa không hạn chế giao tiếp giữa các máy bên trong tường lửa,
kẻ tấn công nào thành công chạy mã bên trong một site có thể truy cập tất cả các máy cục bộ.
Làm thế nào kẻ tấn công có thể vào bên trong tường lửa?
Kẻ tấn công có thể là một nhân viên bất mãn có quyền truy cập hợp lệ,
hoặc phần mềm của kẻ tấn công có thể được ẩn trong một
phần mềm được cài đặt từ CD hoặc tải về từ Web.
Có thể vượt qua tường lửa bằng cách sử dụng kết nối không dây hoặc quay số.

Một vấn đề khác là bất kỳ bên nào được cấp quyền truy cập qua
tường lửa của bạn, như đối tác kinh doanh hoặc nhân viên ở xa,
trở thành một lỗ hổng bảo mật. Nếu bảo mật của họ không tốt
như của bạn, kẻ tấn công có thể xâm nhập bảo mật của bạn
bằng cách xâm nhập bảo mật của họ.

Một trong những vấn đề nghiêm trọng nhất đối với tường lửa
là chúng dễ bị khai thác lỗi trong các máy bên trong tường lửa.
Những lỗi như vậy thường xuyên được phát hiện, nên quản trị viên
hệ thống phải liên tục theo dõi các thông báo về chúng.
Quản trị viên thường không làm như vậy, vì các vi phạm bảo mật
tường lửa thường khai thác các lỗ hổng bảo mật đã được biết đến từ lâu và có giải pháp đơn giản.

*Malware* (viết tắt của “malicious software”) là thuật ngữ chỉ
phần mềm được thiết kế để hoạt động trên máy tính theo cách bị
che giấu và không mong muốn bởi người dùng máy tính.
Virus, sâu, và spyware là các loại malware phổ biến. (*Virus*
đôi khi được dùng đồng nghĩa với *malware*, nhưng chúng tôi sẽ dùng nó theo nghĩa hẹp hơn,
chỉ một loại malware cụ thể.) Mã malware không nhất thiết phải là
mã đối tượng thực thi gốc; nó cũng có thể là mã thông dịch như script hoặc macro thực thi
như các macro dùng trong Microsoft Word.

*Virus* và *sâu* được đặc trưng bởi khả năng tự sao chép và lây lan;
sự khác biệt giữa chúng là sâu là một chương trình hoàn chỉnh tự nhân bản,
trong khi virus là một đoạn mã được chèn
(và tự chèn bản sao của nó)
vào một phần mềm hoặc file khác, để nó được thực thi
như một phần của phần mềm đó
hoặc khi mở file. Virus và sâu thường gây ra các vấn đề như tiêu tốn
băng thông mạng chỉ là tác dụng phụ của việc cố gắng lây lan bản sao của chúng.
Tệ hơn, chúng cũng có thể cố ý phá hoại hệ thống hoặc làm
suy yếu bảo mật theo nhiều cách khác nhau.
Chúng có thể, ví dụ, cài đặt một *backdoor*—phần mềm cho phép truy cập
từ xa vào hệ thống mà không cần xác thực thông thường.
Điều này có thể dẫn đến việc tường lửa phơi bày một dịch vụ lẽ ra phải
tự cung cấp quy trình xác thực nhưng đã bị backdoor làm suy yếu.

Spyware là phần mềm mà không được phép, thu thập và
truyền thông tin riêng tư về hệ thống máy tính hoặc người dùng của nó.
Thường thì spyware được nhúng bí mật vào một chương trình
hữu ích khác và lây lan khi người dùng cố ý cài đặt bản sao.
Vấn đề đối với tường lửa là việc truyền thông tin
riêng tư trông giống như giao tiếp hợp lệ.

Một câu hỏi tự nhiên là liệu tường lửa (hoặc bảo mật mật mã)
có thể ngăn malware xâm nhập hệ thống ngay từ đầu không.
Phần lớn malware thực sự được truyền qua mạng, mặc dù nó cũng
có thể được truyền qua thiết bị lưu trữ di động như CD và thẻ nhớ.
Chắc chắn đây là một lý do ủng hộ cách tiếp cận “chặn mọi thứ
không được cho phép rõ ràng” mà nhiều quản trị viên
áp dụng trong cấu hình tường lửa của họ.

Một cách tiếp cận được sử dụng để phát hiện malware là tìm kiếm
các đoạn mã từ malware đã biết, đôi khi gọi là *chữ ký* (signature).
Cách tiếp cận này có những thách thức riêng, vì malware
được thiết kế khéo léo có thể thay đổi biểu diễn của nó theo nhiều cách khác nhau.
Cũng có thể ảnh hưởng đến hiệu năng mạng khi thực hiện
kiểm tra chi tiết như vậy với dữ liệu vào mạng.
Bảo mật mật mã cũng không thể loại bỏ vấn đề này, mặc dù nó
cung cấp một phương tiện để xác thực nguồn gốc của một phần mềm
và phát hiện bất kỳ sự giả mạo nào, chẳng hạn như khi virus chèn bản sao của nó.

Liên quan đến tường lửa là các hệ thống gọi là *intrusion detection systems*
(IDS) và *intrusion prevention systems* (IPS). Các hệ thống này cố gắng
phát hiện hoạt động bất thường, chẳng hạn như một lượng lớn lưu lượng bất thường
nhắm vào một máy chủ hoặc số cổng cụ thể, và tạo cảnh báo cho quản trị viên mạng hoặc
thậm chí thực hiện hành động trực tiếp để hạn chế một cuộc tấn công có thể xảy ra.
Mặc dù hiện nay đã có các sản phẩm thương mại trong lĩnh vực này,
nhưng nó vẫn là một lĩnh vực đang phát triển.
