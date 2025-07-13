1.4 Phần mềm
============
Kiến trúc mạng và các đặc tả giao thức là những yếu tố thiết yếu, nhưng một bản thiết kế tốt thôi là chưa đủ để giải thích cho sự thành công vượt bậc của Internet: Số lượng máy tính kết nối Internet đã tăng theo cấp số nhân trong hơn ba thập kỷ (mặc dù con số chính xác rất khó xác định). Số lượng người dùng Internet ước tính vào khoảng 4,1 tỷ vào cuối năm 2018—tương đương khoảng một nửa dân số thế giới.

Điều gì giải thích cho sự thành công của Internet? Chắc chắn có nhiều yếu tố góp phần (bao gồm cả một kiến trúc tốt), nhưng một điều khiến Internet trở nên thành công vượt trội là rất nhiều chức năng của nó được cung cấp bởi phần mềm chạy trên các máy tính đa dụng. Ý nghĩa của điều này là chức năng mới có thể được bổ sung dễ dàng chỉ với “một chút lập trình nhỏ.” Kết quả là, các ứng dụng và dịch vụ mới liên tục xuất hiện với tốc độ đáng kinh ngạc.

Một yếu tố liên quan là sự gia tăng mạnh mẽ về sức mạnh tính toán có sẵn trong các máy tính phổ thông. Mặc dù về nguyên tắc, các mạng máy tính luôn có khả năng truyền tải bất kỳ loại thông tin nào, như mẫu giọng nói số hóa, hình ảnh số hóa, v.v., nhưng tiềm năng này không thực sự hấp dẫn nếu các máy tính gửi và nhận dữ liệu đó quá chậm để làm được điều gì hữu ích với thông tin đó. Hầu như tất cả các máy tính ngày nay đều có khả năng phát lại âm thanh và video số hóa với tốc độ và độ phân giải khá sử dụng được.

Trong những năm kể từ khi ấn bản đầu tiên của cuốn sách này xuất hiện, việc viết các ứng dụng mạng đã trở thành một hoạt động phổ biến chứ không còn là công việc chỉ dành cho một số chuyên gia. Nhiều yếu tố đã góp phần vào điều này, bao gồm các công cụ tốt hơn giúp công việc dễ dàng hơn và sự mở rộng của các thị trường mới như ứng dụng cho điện thoại thông minh.

Điều cần lưu ý là biết cách triển khai phần mềm mạng là một phần thiết yếu để hiểu về mạng máy tính, và mặc dù khả năng bạn sẽ không được giao nhiệm vụ triển khai một giao thức cấp thấp như IP, nhưng rất có thể bạn sẽ có lý do để triển khai một giao thức cấp ứng dụng—“killer app” khó nắm bắt có thể mang lại danh tiếng và tài sản không tưởng. Để giúp bạn bắt đầu, phần này giới thiệu một số vấn đề liên quan đến việc triển khai một ứng dụng mạng trên nền Internet. Thông thường, các chương trình như vậy vừa là một ứng dụng (tức là, thiết kế để tương tác với người dùng) vừa là một giao thức (tức là, giao tiếp với các đối tác qua mạng).

1.4.1 Socket API
-----------------
Điểm khởi đầu khi triển khai một ứng dụng mạng là giao diện mà mạng cung cấp. Vì hầu hết các giao thức mạng đều được triển khai bằng phần mềm (đặc biệt là các giao thức ở tầng cao trong ngăn xếp giao thức), và gần như tất cả các hệ điều hành đều triển khai các giao thức mạng như một phần của hệ điều hành, khi chúng ta nói đến giao diện “do mạng cung cấp”, chúng ta thường nói đến giao diện mà hệ điều hành cung cấp cho hệ thống mạng của nó. Giao diện này thường được gọi là *giao diện lập trình ứng dụng* (API) mạng.

Mặc dù mỗi hệ điều hành đều có thể tự do định nghĩa API mạng của riêng mình (và hầu hết đều có), theo thời gian, một số API này đã trở nên được hỗ trợ rộng rãi; tức là, chúng đã được chuyển sang các hệ điều hành khác ngoài hệ điều hành gốc. Điều này đã xảy ra với *giao diện socket* ban đầu được cung cấp bởi bản phân phối Unix của Berkeley, hiện nay được hỗ trợ trên hầu như tất cả các hệ điều hành phổ biến, và là nền tảng của các giao diện đặc thù ngôn ngữ, như thư viện socket của Java hoặc Python. Chúng tôi sử dụng Linux và C cho tất cả các ví dụ mã trong cuốn sách này, Linux vì nó là mã nguồn mở và C vì nó vẫn là ngôn ngữ được lựa chọn cho các thành phần mạng bên trong. (Cũng có lợi thế là phơi bày tất cả các chi tiết cấp thấp, điều này hữu ích để hiểu các ý tưởng nền tảng.)

.. sidebar:: Sự bùng nổ ứng dụng nhờ Sockets

   Thật khó để đánh giá hết tầm quan trọng của Socket API. Nó xác định ranh giới giữa các ứng dụng chạy trên Internet và các chi tiết về cách Internet được triển khai. Nhờ Sockets cung cấp một giao diện rõ ràng và ổn định, việc viết ứng dụng Internet đã bùng nổ thành một ngành công nghiệp hàng tỷ đô la. Bắt đầu từ mô hình client/server đơn giản và một vài chương trình ứng dụng như email, truyền tệp và đăng nhập từ xa, giờ đây ai cũng có thể truy cập vô số ứng dụng đám mây từ điện thoại thông minh của mình. Phần này đặt nền tảng bằng cách nhắc lại sự đơn giản của một chương trình client mở socket để trao đổi thông điệp với chương trình server, nhưng ngày nay một hệ sinh thái phần mềm phong phú đã được xây dựng trên Socket API. Lớp này bao gồm vô số công cụ dựa trên đám mây giúp giảm rào cản cho việc triển khai các ứng dụng có khả năng mở rộng. Chúng tôi sẽ quay lại mối liên hệ giữa đám mây và mạng trong mọi chương, bắt đầu từ phần *Perspective* ở cuối Chương 1.

Trước khi mô tả giao diện socket, điều quan trọng là bạn phải tách biệt hai mối quan tâm trong đầu. Mỗi giao thức cung cấp một tập hợp *dịch vụ* nhất định, và API cung cấp một *cú pháp* để các dịch vụ đó có thể được gọi trên một hệ thống máy tính cụ thể. Việc triển khai sau đó chịu trách nhiệm ánh xạ tập hợp các thao tác và đối tượng cụ thể do API định nghĩa lên tập hợp dịch vụ trừu tượng do giao thức định nghĩa. Nếu bạn làm tốt việc định nghĩa giao diện, thì sẽ có thể sử dụng cú pháp của giao diện để gọi các dịch vụ của nhiều giao thức khác nhau. Tính tổng quát như vậy chắc chắn là mục tiêu của giao diện socket, mặc dù nó còn lâu mới hoàn hảo.

Trừu tượng chính của giao diện socket, không ngạc nhiên, là *socket*. Một cách hay để hình dung socket là điểm mà một tiến trình ứng dụng cục bộ gắn vào mạng. Giao diện định nghĩa các thao tác để tạo socket, gắn socket vào mạng, gửi/nhận thông điệp qua socket và đóng socket. Để đơn giản hóa thảo luận, chúng tôi chỉ giới hạn việc minh họa cách sử dụng socket với TCP.

Bước đầu tiên là tạo một socket, được thực hiện với thao tác sau:

.. code-block:: c

   int socket(int domain, int type, int protocol);

Lý do thao tác này nhận ba tham số là vì giao diện socket được thiết kế đủ tổng quát để hỗ trợ bất kỳ bộ giao thức nền tảng nào. Cụ thể, tham số ``domain`` xác định *họ giao thức* sẽ được sử dụng: ``PF_INET`` biểu thị họ Internet, ``PF_UNIX`` biểu thị cơ sở ống Unix, và ``PF_PACKET`` biểu thị truy cập trực tiếp vào giao diện mạng (tức là, bỏ qua ngăn xếp giao thức TCP/IP). Tham số ``type`` chỉ ra ngữ nghĩa của giao tiếp. ``SOCK_STREAM`` dùng để biểu thị luồng byte. ``SOCK_DGRAM`` là lựa chọn thay thế biểu thị dịch vụ hướng thông điệp, như UDP cung cấp. Tham số ``protocol`` xác định giao thức cụ thể sẽ được sử dụng. Trong trường hợp của chúng ta, tham số này là ``UNSPEC`` vì kết hợp ``PF_INET`` và ``SOCK_STREAM`` đã ngầm định là TCP. Cuối cùng, giá trị trả về từ ``socket`` là một *handle* cho socket vừa tạo—tức là, một định danh để chúng ta có thể tham chiếu socket này về sau. Nó được truyền làm tham số cho các thao tác tiếp theo trên socket này.

Bước tiếp theo phụ thuộc vào việc bạn là client hay server. Trên máy chủ, tiến trình ứng dụng thực hiện *mở thụ động*—server thông báo rằng nó sẵn sàng chấp nhận kết nối, nhưng chưa thực sự thiết lập kết nối. Server làm điều này bằng cách gọi ba thao tác sau:

.. code-block:: c

   int bind(int socket, struct sockaddr *address, int addr_len);
   int listen(int socket, int backlog);
   int accept(int socket, struct sockaddr *address, int *addr_len);

Thao tác ``bind``, như tên gọi, gắn socket vừa tạo với ``address`` chỉ định. Đây là địa chỉ mạng của thành phần *cục bộ*—server. Lưu ý rằng, khi dùng với giao thức Internet, ``address`` là một cấu trúc dữ liệu bao gồm cả địa chỉ IP của server và số cổng TCP. Cổng được dùng để gián tiếp xác định tiến trình. Chúng là một dạng *khóa tách kênh* (demux keys). Số cổng thường là một số nổi tiếng cụ thể cho dịch vụ được cung cấp; ví dụ, web server thường chấp nhận kết nối ở cổng 80.

Thao tác ``listen`` sau đó xác định số lượng kết nối có thể chờ trên socket chỉ định. Cuối cùng, thao tác ``accept`` thực hiện mở thụ động. Đây là thao tác chặn, không trả về cho đến khi một thành phần từ xa thiết lập kết nối, và khi hoàn thành sẽ trả về một socket *mới* tương ứng với kết nối vừa thiết lập, và tham số ``address`` chứa địa chỉ của thành phần *từ xa*. Lưu ý rằng khi ``accept`` trả về, socket gốc truyền vào vẫn tồn tại và vẫn tương ứng với mở thụ động; nó được dùng cho các lần gọi ``accept`` tiếp theo.

Trên máy client, tiến trình ứng dụng thực hiện *mở chủ động*; tức là, nó chỉ định muốn giao tiếp với ai bằng cách gọi thao tác sau:

.. code-block:: c

   int connect(int socket, struct sockaddr *address, int addr_len);

Thao tác này không trả về cho đến khi TCP thiết lập kết nối thành công, lúc đó ứng dụng có thể bắt đầu gửi dữ liệu. Trong trường hợp này, ``address`` chứa địa chỉ của thành phần từ xa. Thực tế, client thường chỉ chỉ định địa chỉ của thành phần từ xa và để hệ thống tự điền thông tin cục bộ. Trong khi server thường lắng nghe ở một cổng nổi tiếng, client thường không quan tâm mình dùng cổng nào; hệ điều hành chỉ đơn giản chọn một cổng chưa dùng.

Khi kết nối đã được thiết lập, các tiến trình ứng dụng gọi hai thao tác sau để gửi và nhận dữ liệu:

.. code-block:: c

   int send(int socket, char *message, int msg_len, int flags);
   int recv(int socket, char *buffer, int buf_len, int flags);

Thao tác đầu tiên gửi ``message`` qua ``socket`` chỉ định, còn thao tác thứ hai nhận một thông điệp từ ``socket`` chỉ định vào ``buffer``. Cả hai thao tác đều nhận một tập hợp ``flags`` để kiểm soát một số chi tiết của thao tác.

1.4.2 Ví dụ Client/Server
-------------------------
Chúng ta sẽ trình bày việc triển khai một chương trình client/server đơn giản sử dụng giao diện socket để gửi thông điệp qua kết nối TCP. Chương trình cũng sử dụng các tiện ích mạng khác của Linux, sẽ được giới thiệu dần. Ứng dụng của chúng ta cho phép người dùng trên một máy nhập và gửi văn bản cho người dùng trên máy khác. Đây là phiên bản đơn giản hóa của chương trình ``talk`` trên Linux, tương tự như chương trình lõi của các ứng dụng nhắn tin tức thời.

Client
~~~~~~
Chúng ta bắt đầu với phía client, nhận tên máy từ xa làm tham số. Nó gọi tiện ích Linux để chuyển tên này thành địa chỉ IP của host từ xa. Bước tiếp theo là xây dựng cấu trúc dữ liệu địa chỉ (``sin``) mà giao diện socket mong đợi. Lưu ý rằng cấu trúc này chỉ rõ chúng ta sẽ dùng socket để kết nối Internet (``AF_INET``). Trong ví dụ, chúng ta dùng cổng TCP 5432 làm cổng server nổi tiếng; đây là cổng chưa được gán cho dịch vụ Internet nào khác. Bước cuối cùng để thiết lập kết nối là gọi ``socket`` và ``connect``. Khi thao tác trả về, kết nối đã được thiết lập và chương trình client đi vào vòng lặp chính, đọc văn bản từ đầu vào chuẩn và gửi qua socket.

.. code-block:: c

   #include <stdio.h>
   #include <sys/types.h>
   #include <sys/socket.h>
   #include <netinet/in.h>
   #include <netdb.h>
   #include <stdlib.h>
   #include <unistd.h>
   #include <string.h>

   #define SERVER_PORT 5432
   #define MAX_LINE 256

   int
   main(int argc, char * argv[])
   {
     FILE *fp;
     struct hostent *hp;
     struct sockaddr_in sin;
     char *host;
     char buf[MAX_LINE];
     int s;
     int len;

     if (argc==2) {
       host = argv[1];
     }
     else {
       fprintf(stderr, "usage: simplex-talk host\n");
       exit(1);
     }

     /* translate host name into peer's IP address */
     hp = gethostbyname(host);
     if (!hp) {
       fprintf(stderr, "simplex-talk: unknown host: %s\n", host);
       exit(1);
     }

     /* build address data structure */
     bzero((char *)&sin, sizeof(sin));
     sin.sin_family = AF_INET;
     bcopy(hp->h_addr, (char *)&sin.sin_addr, hp->h_length);
     sin.sin_port = htons(SERVER_PORT);

     /* active open */
     if ((s = socket(PF_INET, SOCK_STREAM, 0)) < 0) {
       perror("simplex-talk: socket");
       exit(1);
     }
     if (connect(s, (struct sockaddr *)&sin, sizeof(sin)) < 0)
     {
       perror("simplex-talk: connect");
       close(s);
       exit(1);
     }
     /* main loop: get and send lines of text */
     while (fgets(buf, sizeof(buf), stdin)) {
       buf[MAX_LINE-1] = '\0';
       len = strlen(buf) + 1;
       send(s, buf, len, 0);
     }
   }

Server
~~~~~~
Server cũng đơn giản không kém. Đầu tiên nó xây dựng cấu trúc dữ liệu địa chỉ bằng cách điền số cổng của chính nó (``SERVER_PORT``). Bằng cách không chỉ định địa chỉ IP, chương trình ứng dụng sẵn sàng chấp nhận kết nối trên bất kỳ địa chỉ IP nào của host cục bộ. Tiếp theo, server thực hiện các bước chuẩn bị cho mở thụ động; nó tạo socket, gắn socket vào địa chỉ cục bộ, và đặt số lượng kết nối chờ tối đa được phép. Cuối cùng, vòng lặp chính chờ một host từ xa cố gắng kết nối, và khi có, nó nhận và in ra các ký tự nhận được trên kết nối.

.. code-block:: c

   #include <stdio.h>
   #include <sys/types.h>
   #include <sys/socket.h>
   #include <netinet/in.h>
   #include <netdb.h>
   #include <stdlib.h>
   #include <unistd.h>
   #include <string.h>

   #define SERVER_PORT  5432
   #define MAX_PENDING  5
   #define MAX_LINE     256

   int
   main()
   {
     struct sockaddr_in sin;
     char buf[MAX_LINE];
     int buf_len;
     socklen_t addr_len;
     int s, new_s;

     /* build address data structure */
     bzero((char *)&sin, sizeof(sin));
     sin.sin_family = AF_INET;
     sin.sin_addr.s_addr = INADDR_ANY;
     sin.sin_port = htons(SERVER_PORT);

     /* setup passive open */
     if ((s = socket(PF_INET, SOCK_STREAM, 0)) < 0) {
       perror("simplex-talk: socket");
       exit(1);
     }
     if ((bind(s, (struct sockaddr *)&sin, sizeof(sin))) < 0) {
       perror("simplex-talk: bind");
       exit(1);
     }
     listen(s, MAX_PENDING);

    /* wait for connection, then receive and print text */
     while(1) {
       if ((new_s = accept(s, (struct sockaddr *)&sin, &addr_len)) < 0) {
         perror("simplex-talk: accept");
         exit(1);
       }
       while (buf_len = recv(new_s, buf, sizeof(buf), 0))
         fputs(buf, stdout);
       close(new_s);
     }
   }
