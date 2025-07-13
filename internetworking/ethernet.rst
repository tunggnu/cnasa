3.2 Ethernet chuyển mạch
========================

Sau khi đã thảo luận một số ý tưởng cơ bản về chuyển mạch, giờ chúng ta sẽ tập trung kỹ hơn vào một công nghệ chuyển mạch cụ thể: *Ethernet chuyển mạch* (*Switched Ethernet*). Các switch dùng để xây dựng các mạng như vậy, thường được gọi là *switch tầng 2* (*L2 switches*), được sử dụng rộng rãi trong các mạng campus và doanh nghiệp. Trước đây, chúng thường được gọi là *bridge* vì chúng được dùng để “bắc cầu” các đoạn Ethernet nhằm xây dựng một *LAN mở rộng* (*extended LAN*). Nhưng ngày nay, hầu hết các mạng triển khai Ethernet theo cấu hình điểm-điểm, với các liên kết này được kết nối với nhau qua các switch tầng 2 để tạo thành một Ethernet chuyển mạch.

Phần sau đây bắt đầu với góc nhìn lịch sử (dùng bridge để kết nối một tập các đoạn Ethernet), rồi chuyển sang góc nhìn phổ biến ngày nay (dùng switch tầng 2 để kết nối một tập các liên kết điểm-điểm). Nhưng dù ta gọi thiết bị đó là bridge hay switch—và mạng bạn xây là LAN mở rộng hay Ethernet chuyển mạch—thì cả hai đều hoạt động *chính xác* như nhau.

Đầu tiên, giả sử bạn có một cặp mạng Ethernet mà bạn muốn kết nối với nhau. Một cách tiếp cận là đặt một repeater giữa chúng. Tuy nhiên, điều này sẽ không khả thi nếu làm vậy vượt quá các giới hạn vật lý của Ethernet. (Nhớ rằng không được có quá bốn repeater giữa bất kỳ cặp máy chủ nào và tổng chiều dài không quá 2500 m.) Một lựa chọn khác là đặt một nút với hai card mạng Ethernet giữa hai mạng này và để nút đó chuyển tiếp frame từ một Ethernet sang Ethernet còn lại. Nút này sẽ khác với repeater, vốn hoạt động ở mức bit, không phải frame, và chỉ đơn giản sao chép các bit nhận được từ một giao diện sang giao diện khác. Thay vào đó, nút này sẽ thực hiện đầy đủ các giao thức phát hiện va chạm và truy cập môi trường của Ethernet trên từng giao diện. Do đó, các giới hạn về chiều dài và số lượng máy chủ của Ethernet, vốn liên quan đến quản lý va chạm, sẽ không áp dụng cho cặp Ethernet kết nối theo cách này. Thiết bị này hoạt động ở chế độ promiscuous, nhận tất cả các frame truyền trên bất kỳ Ethernet nào và chuyển tiếp chúng sang Ethernet còn lại.

Ở dạng đơn giản nhất, bridge chỉ nhận các frame LAN ở đầu vào và chuyển tiếp chúng ra tất cả các đầu ra khác. Chiến lược đơn giản này được dùng bởi các bridge đời đầu nhưng có một số hạn chế nghiêm trọng như ta sẽ thấy bên dưới. Qua nhiều năm, nhiều cải tiến đã được bổ sung để làm cho bridge trở thành một cơ chế hiệu quả để kết nối một tập các LAN. Phần còn lại của mục này sẽ trình bày các chi tiết thú vị hơn.

3.2.1 Bridge học địa chỉ (Learning Bridges)
-------------------------------------------

Tối ưu hóa đầu tiên mà ta có thể thực hiện với bridge là nhận ra rằng nó không cần chuyển tiếp tất cả các frame mà nó nhận được. Xét bridge trong :numref:`Hình %s <fig-elan2>`. Bất cứ khi nào một frame từ host A gửi tới host B đến cổng 1, bridge không cần chuyển tiếp frame đó ra cổng 2. Vậy làm sao bridge biết được các host nằm ở cổng nào?

.. _fig-elan2:
.. figure:: figures/f03-09-9780123850591.png
   :width: 500px
   :align: center

   Minh họa bridge học địa chỉ.

Một lựa chọn là để con người tải một bảng vào bridge giống như trong :numref:`Bảng %s <tab-learn>`. Khi đó, bất cứ khi nào bridge nhận một frame ở cổng 1 gửi tới host A, nó sẽ không chuyển tiếp frame ra cổng 2; không cần thiết vì host A đã trực tiếp nhận frame trên LAN kết nối với cổng 1. Bất cứ khi nào một frame gửi tới host A được nhận ở cổng 2, bridge sẽ chuyển tiếp frame ra cổng 1.

.. _tab-learn:
.. table:: Bảng chuyển tiếp do bridge duy trì.
   :align: center
   :widths: auto

   +------+------+
   | Host | Cổng |
   +======+======+
   | A    | 1    |
   +------+------+
   | B    | 1    |
   +------+------+
   | C    | 1    |
   +------+------+
   | X    | 2    |
   +------+------+
   | Y    | 2    |
   +------+------+
   | Z    | 2    |
   +------+------+

Việc để con người duy trì bảng này là quá phiền phức, và có một mẹo đơn giản để bridge tự học thông tin này. Ý tưởng là mỗi bridge sẽ kiểm tra địa chỉ *nguồn* trong tất cả các frame mà nó nhận được. Do đó, khi host A gửi một frame tới một host ở bất kỳ phía nào của bridge, bridge sẽ nhận frame này và ghi nhận rằng vừa nhận một frame từ host A ở cổng 1. Bằng cách này, bridge có thể xây dựng một bảng giống như :numref:`Bảng %s <tab-learn>`.

Lưu ý rằng bridge sử dụng bảng như vậy thực hiện một phiên bản của mô hình datagram (hoặc không kết nối) đã mô tả trước đó. Mỗi gói mang một địa chỉ toàn cục, và bridge quyết định gửi gói ra đầu ra nào bằng cách tra cứu địa chỉ đó trong bảng.

Khi bridge vừa khởi động, bảng này rỗng; các mục được thêm dần theo thời gian. Ngoài ra, mỗi mục có một thời gian timeout, và bridge sẽ loại bỏ mục đó sau một khoảng thời gian nhất định. Điều này nhằm bảo vệ trường hợp một host—và do đó địa chỉ LAN của nó—bị chuyển sang mạng khác. Do đó, bảng này không nhất thiết phải đầy đủ. Nếu bridge nhận một frame gửi tới một host chưa có trong bảng, nó sẽ chuyển tiếp frame ra tất cả các cổng còn lại. Nói cách khác, bảng này chỉ là một tối ưu hóa để lọc bớt một số frame; nó không bắt buộc để đảm bảo tính đúng đắn.

3.2.2 Triển khai (Implementation)
---------------------------------

Mã thực hiện thuật toán bridge học địa chỉ khá đơn giản, và chúng tôi phác thảo ở đây. Cấu trúc ``BridgeEntry`` định nghĩa một mục trong bảng chuyển tiếp của bridge; các mục này được lưu trong một cấu trúc ``Map`` (hỗ trợ các thao tác ``mapCreate``, ``mapBind``, và ``mapResolve``) để cho phép tra cứu mục hiệu quả khi các gói đến từ các nguồn đã có trong bảng. Hằng số ``MAX_TTL`` xác định thời gian một mục được giữ trong bảng trước khi bị loại bỏ.

.. code-block:: c

   #define BRIDGE_TAB_SIZE   1024  /* max size of bridging table */
   #define MAX_TTL           120   /* time (in seconds) before an entry is flushed */

   typedef struct {
       MacAddr     destination;    /* MAC address of a node */
       int         ifnumber;       /* interface to reach it */
       u_short     TTL;            /* time to live */
       Binding     binding;        /* binding in the Map */
   } BridgeEntry;

   int     numEntries = 0;
   Map     bridgeMap = mapCreate(BRIDGE_TAB_SIZE, sizeof(BridgeEntry));

Hàm cập nhật bảng chuyển tiếp khi một gói mới đến được gọi là ``updateTable``. Tham số truyền vào là địa chỉ MAC nguồn chứa trong gói và số hiệu giao diện mà nó nhận được. Một hàm khác, không trình bày ở đây, sẽ được gọi định kỳ, quét các mục trong bảng chuyển tiếp và giảm trường ``TTL`` (thời gian sống) của mỗi mục, loại bỏ các mục có ``TTL`` bằng 0. Lưu ý rằng ``TTL`` được đặt lại thành ``MAX_TTL`` mỗi khi một gói đến để làm mới mục bảng hiện có và giao diện mà đích có thể đến được cũng được cập nhật theo gói nhận gần nhất.

.. code-block:: c

   void
   updateTable (MacAddr src, int inif)
   {
       BridgeEntry       *b;

       if (mapResolve(bridgeMap, &src, (void **)&b) == FALSE )
       {
           /* địa chỉ này chưa có trong bảng, thử thêm vào */
           if (numEntries < BRIDGE_TAB_SIZE)
           {
               b = NEW(BridgeEntry);
               b->binding = mapBind( bridgeMap, &src, b);
               /* dùng địa chỉ nguồn của gói làm địa chỉ đích trong bảng */
               b->destination = src;
               numEntries++;
           }
           else
           {
               /* không thể thêm địa chỉ này vào bảng lúc này, bỏ qua */
               return;
           }
       }
       /* đặt lại TTL và dùng giao diện vào mới nhất */
       b->TTL = MAX_TTL;
       b->ifnumber = inif;
   }

Lưu ý rằng triển khai này dùng chiến lược đơn giản khi bảng bridge đã đầy—nó chỉ đơn giản không thêm địa chỉ mới. Nhớ rằng bảng bridge không cần phải đầy đủ để chuyển tiếp đúng; nó chỉ tối ưu hóa hiệu năng. Nếu có mục nào trong bảng không còn được dùng, nó sẽ hết hạn và bị loại bỏ, tạo chỗ cho mục mới. Một cách tiếp cận khác là dùng một thuật toán thay thế bộ nhớ đệm khi bảng đầy; ví dụ, ta có thể tìm và loại bỏ mục có TTL nhỏ nhất để nhường chỗ cho mục mới.

3.2.3 Thuật toán cây bao phủ (Spanning Tree Algorithm)
------------------------------------------------------

Chiến lược trên hoạt động tốt cho đến khi mạng có một vòng lặp, khi đó nó sẽ thất bại nghiêm trọng—các frame có thể bị chuyển tiếp mãi mãi. Điều này dễ thấy trong ví dụ ở :numref:`Hình %s <fig-elan3>`, nơi các switch S1, S4 và S6 tạo thành một vòng lặp.

.. _fig-elan3:
.. figure:: figures/impl/Slide5.png
   :width: 500px
   :align: center

   Ethernet chuyển mạch có vòng lặp.

Lưu ý rằng giờ chúng ta chuyển từ gọi mỗi thiết bị chuyển tiếp là bridge (kết nối các đoạn có thể nối tới nhiều thiết bị khác) sang gọi là switch tầng 2 (kết nối các liên kết điểm-điểm chỉ tới một thiết bị khác). Để ví dụ dễ quản lý, chúng tôi chỉ đưa vào ba host. Trong thực tế, các switch thường có 16, 24 hoặc 48 cổng, nghĩa là chúng có thể kết nối tới từng đó host (và các switch khác).

Trong mạng chuyển mạch ví dụ, giả sử một gói vào switch S4 từ Host C và địa chỉ đích chưa có trong bảng chuyển tiếp của bất kỳ switch nào: S4 gửi một bản sao gói ra hai cổng còn lại: tới switch S1 và S6. Switch S6 chuyển tiếp gói tới S1 (đồng thời, S1 chuyển tiếp gói tới S6), cả hai lại chuyển tiếp gói về S4. S4 vẫn chưa có đích này trong bảng, nên lại chuyển tiếp gói ra hai cổng còn lại. Không có gì ngăn chu trình này lặp lại mãi mãi, với các gói lặp vòng giữa S1, S4 và S6.

Tại sao một Ethernet chuyển mạch (hoặc LAN mở rộng) lại có vòng lặp? Một khả năng là mạng được quản lý bởi nhiều quản trị viên, ví dụ vì nó trải rộng qua nhiều phòng ban trong tổ chức. Trong trường hợp này, có thể không ai biết toàn bộ cấu hình mạng, nghĩa là một switch đóng vòng lặp có thể được thêm vào mà không ai biết. Một kịch bản khác, phổ biến hơn, là các vòng lặp được xây dựng có chủ đích—để cung cấp dự phòng khi có sự cố. Dù sao, một mạng không có vòng lặp chỉ cần một liên kết bị hỏng là bị chia tách thành hai phần riêng biệt.

Dù nguyên nhân là gì, switch phải có khả năng xử lý đúng các vòng lặp. Vấn đề này được giải quyết bằng cách cho các switch chạy một thuật toán *cây bao phủ* phân tán. Nếu bạn coi mạng là một đồ thị có thể có vòng lặp (chu trình), thì cây bao phủ là một đồ thị con của đồ thị này bao phủ (span) tất cả các đỉnh nhưng không chứa chu trình nào. Tức là, cây bao phủ giữ lại tất cả các đỉnh của đồ thị gốc nhưng loại bỏ một số cạnh. Ví dụ, :numref:`Hình %s <fig-graphs>` cho thấy một đồ thị có chu trình ở bên trái và một trong nhiều cây bao phủ ở bên phải.

.. _fig-graphs:
.. figure:: figures/f03-11-9780123850591.png
   :width: 500px
   :align: center

   Ví dụ về (a) đồ thị có chu trình; (b) cây bao phủ tương ứng.

Ý tưởng về cây bao phủ khá đơn giản: Đó là một tập con của cấu trúc liên kết mạng thực tế không có vòng lặp và bao phủ tất cả các thiết bị trong mạng. Phần khó là làm sao tất cả các switch phối hợp để đạt được một quan điểm chung về cây bao phủ. Dù sao, một cấu trúc liên kết thường có thể có nhiều cây bao phủ. Câu trả lời nằm ở giao thức cây bao phủ, sẽ được mô tả ngay sau đây.

Thuật toán cây bao phủ, do Radia Perlman phát triển khi làm việc tại Digital Equipment Corporation, là một giao thức để một tập các switch đồng thuận về một cây bao phủ cho một mạng nhất định. (Chuẩn IEEE 802.1 dựa trên thuật toán này.) Trong thực tế, điều này có nghĩa là mỗi switch quyết định các cổng mà nó sẽ và sẽ không chuyển tiếp frame. Theo một nghĩa nào đó, bằng cách loại bỏ các cổng khỏi cấu trúc liên kết, mạng được giảm thành một cây không chu trình. Thậm chí có thể một switch sẽ không tham gia chuyển tiếp frame, điều này thoạt nhìn có vẻ lạ. Tuy nhiên, thuật toán là động, nghĩa là các switch luôn sẵn sàng tự cấu hình lại thành một cây bao phủ mới nếu có switch bị hỏng, và do đó các cổng và switch không dùng đến cung cấp khả năng dự phòng cần thiết để phục hồi khi có sự cố.

Ý tưởng chính của cây bao phủ là các switch chọn các cổng mà chúng sẽ chuyển tiếp frame. Thuật toán chọn cổng như sau. Mỗi switch có một định danh duy nhất; ở đây, chúng tôi dùng nhãn S1, S2, S3, v.v. Thuật toán đầu tiên bầu switch có ID nhỏ nhất làm gốc của cây bao phủ; cách bầu chọn sẽ được mô tả bên dưới. Switch gốc luôn chuyển tiếp frame ra tất cả các cổng của nó. Tiếp theo, mỗi switch tính đường ngắn nhất tới gốc và ghi nhận cổng nào nằm trên đường này. Cổng này cũng được chọn là đường ưu tiên tới gốc của switch. Cuối cùng, để xét khả năng có switch khác kết nối tới các cổng của mình, switch bầu một *switch đại diện* chịu trách nhiệm chuyển tiếp frame về phía gốc. Switch đại diện là switch gần gốc nhất. Nếu hai hoặc nhiều switch cùng gần gốc như nhau, thì dùng ID để phân xử, ID nhỏ nhất thắng. Tất nhiên, mỗi switch có thể kết nối tới nhiều switch khác, nên nó tham gia bầu switch đại diện cho từng cổng. Thực chất, điều này nghĩa là mỗi switch quyết định nó có phải là switch đại diện so với từng cổng không. Switch chuyển tiếp frame qua các cổng mà nó là switch đại diện.

.. _fig-elan4:
.. figure:: figures/impl/Slide6.png
   :width: 500px
   :align: center

   Cây bao phủ với một số cổng không được chọn.

:numref:`Hình %s <fig-elan4>` cho thấy cây bao phủ tương ứng với mạng ở :numref:`Hình %s <fig-elan3>`. Trong ví dụ này, S1 là gốc vì nó có ID nhỏ nhất. Lưu ý rằng S3 và S5 kết nối với nhau, nhưng S5 là switch đại diện vì nó gần gốc hơn. Tương tự, S5 và S7 kết nối với nhau, nhưng lần này S5 là switch đại diện vì nó có ID nhỏ hơn; cả hai đều cách S1 một khoảng như nhau.

Dù con người có thể nhìn vào mạng trong :numref:`Hình %s <fig-elan3>` và tính toán cây bao phủ trong :numref:`Hình %s <fig-elan4>` theo các quy tắc trên, các switch không có đặc quyền nhìn thấy toàn bộ cấu trúc mạng, càng không thể nhìn vào switch khác để biết ID của chúng. Thay vào đó, chúng phải trao đổi các thông điệp cấu hình với nhau rồi quyết định mình có phải là gốc hay switch đại diện dựa trên các thông điệp này.

Cụ thể, thông điệp cấu hình chứa ba thông tin:

1. ID của switch gửi thông điệp.

2. ID của switch mà switch gửi tin cho là gốc.

3. Khoảng cách, tính bằng số bước nhảy, từ switch gửi tin tới switch gốc.

Mỗi switch ghi nhận thông điệp cấu hình *tốt nhất* mà nó thấy trên mỗi cổng (“tốt nhất” được định nghĩa bên dưới), bao gồm cả thông điệp nhận từ switch khác và thông điệp chính nó gửi.

Ban đầu, mỗi switch nghĩ mình là gốc, nên gửi thông điệp cấu hình ra tất cả các cổng, nhận mình là gốc và khoảng cách tới gốc là 0. Khi nhận được thông điệp cấu hình qua một cổng, switch kiểm tra xem thông điệp mới có tốt hơn thông tin hiện tại ghi nhận cho cổng đó không. Thông điệp mới được coi là *tốt hơn* nếu một trong các điều sau đúng:

-  Nó nhận diện một gốc có ID nhỏ hơn.

-  Nó nhận diện một gốc có ID bằng nhưng khoảng cách ngắn hơn.

-  ID gốc và khoảng cách bằng nhau, nhưng switch gửi tin có ID nhỏ hơn.

Nếu thông điệp mới tốt hơn thông tin hiện tại, switch loại bỏ thông tin cũ và lưu thông tin mới. Tuy nhiên, trước đó nó cộng thêm 1 vào trường khoảng cách tới gốc vì switch này cách gốc xa hơn switch gửi tin một bước.

Khi một switch nhận thông điệp cấu hình cho thấy nó không phải là gốc—tức là thông điệp từ switch có ID nhỏ hơn—switch ngừng tự gửi thông điệp cấu hình và chỉ chuyển tiếp thông điệp từ switch khác, sau khi cộng thêm 1 vào trường khoảng cách. Tương tự, khi switch nhận thông điệp cấu hình cho thấy nó không phải là switch đại diện cho cổng đó—tức là thông điệp từ switch gần gốc hơn hoặc cùng khoảng cách nhưng ID nhỏ hơn—switch ngừng gửi thông điệp cấu hình qua cổng đó. Như vậy, khi hệ thống ổn định, chỉ switch gốc còn gửi thông điệp cấu hình, các switch khác chỉ chuyển tiếp thông điệp này qua các cổng mà chúng là switch đại diện. Lúc này, một cây bao phủ đã được xây dựng, và tất cả các switch đồng thuận về các cổng được dùng cho cây bao phủ. Chỉ các cổng này được dùng để chuyển tiếp gói dữ liệu.

Hãy xem ví dụ. Giả sử trong :numref:`Hình %s <fig-elan4>`, vừa có điện lại cho một campus, nên tất cả switch khởi động cùng lúc. Tất cả switch sẽ bắt đầu bằng việc nhận mình là gốc. Ta ký hiệu thông điệp cấu hình từ nút X, nhận mình cách d bước từ gốc Y là (Y,d,X). Tập trung vào hoạt động tại S3, một chuỗi sự kiện sẽ diễn ra như sau:

1. S3 nhận (S2, 0, S2).

2. Vì 2 < 3, S3 chấp nhận S2 là gốc.

3. S3 cộng thêm 1 vào khoảng cách do S2 quảng bá (0) và gửi (S2, 1, S3) tới S5.

4. Trong khi đó, S2 chấp nhận S1 là gốc vì ID nhỏ hơn, và gửi (S1, 1, S2) tới S3.

5. S5 chấp nhận S1 là gốc và gửi (S1, 1, S5) tới S3.

6. S3 chấp nhận S1 là gốc, và ghi nhận cả S2 và S5 đều gần gốc hơn nó, nhưng S2 có ID nhỏ hơn, nên S2 nằm trên đường ưu tiên tới gốc của S3.

Điều này khiến S3 có các cổng hoạt động như trong :numref:`Hình %s <fig-elan4>`. Lưu ý rằng Host A và B không thể liên lạc qua đường ngắn nhất (qua S5) vì frame phải “chảy lên cây rồi xuống lại”, nhưng đó là cái giá phải trả để tránh vòng lặp.

Ngay cả khi hệ thống đã ổn định, switch gốc vẫn tiếp tục gửi thông điệp cấu hình định kỳ, và các switch khác tiếp tục chuyển tiếp các thông điệp này như vừa mô tả. Nếu một switch nào đó bị hỏng, các switch phía dưới sẽ không nhận được thông điệp cấu hình, và sau một thời gian chờ nhất định, chúng lại nhận mình là gốc, thuật toán lại khởi động để bầu gốc và switch đại diện mới.

Một điều quan trọng cần lưu ý là dù thuật toán có thể cấu hình lại cây bao phủ khi một switch bị hỏng, nó không thể chuyển tiếp frame qua các đường thay thế để tránh một switch bị tắc nghẽn.

3.2.4 Broadcast và Multicast
----------------------------

Phần thảo luận trước tập trung vào cách switch chuyển tiếp frame unicast từ một cổng sang cổng khác. Vì mục tiêu của switch là mở rộng LAN một cách trong suốt qua nhiều mạng, và vì hầu hết LAN đều hỗ trợ cả broadcast và multicast, nên switch cũng phải hỗ trợ hai tính năng này. Broadcast thì đơn giản—mỗi switch chuyển tiếp frame có địa chỉ broadcast ra tất cả các cổng đang hoạt động (được chọn) trừ cổng nhận frame.

Multicast có thể được triển khai tương tự, mỗi host tự quyết định có nhận thông điệp hay không. Thực tế cũng làm như vậy. Tuy nhiên, vì không phải tất cả host đều là thành viên của một nhóm multicast cụ thể, nên có thể làm tốt hơn. Cụ thể, thuật toán cây bao phủ có thể được mở rộng để cắt tỉa các mạng mà frame multicast không cần được chuyển tiếp. Xét một frame gửi tới nhóm M bởi host A trong :numref:`Hình %s <fig-elan4>`. Nếu host C không thuộc nhóm M, thì không cần switch S4 chuyển tiếp frame qua mạng đó.

Làm sao một switch biết có nên chuyển tiếp frame multicast qua một cổng không? Nó học giống như cách switch học có nên chuyển tiếp frame unicast qua một cổng—bằng cách quan sát địa chỉ *nguồn* mà nó nhận được qua cổng đó. Tất nhiên, các nhóm thường không phải là nguồn của frame, nên ta phải “lách luật” một chút. Cụ thể, mỗi host là thành viên của nhóm M phải định kỳ gửi một frame với địa chỉ nhóm M ở trường nguồn của tiêu đề frame. Frame này sẽ có địa chỉ đích là địa chỉ multicast cho các switch.

Dù mở rộng multicast như vừa mô tả từng được đề xuất, nó không được áp dụng rộng rãi. Thay vào đó, multicast được triển khai giống hệt như broadcast.

3.2.5 LAN ảo (VLANs)
--------------------

Một hạn chế của switch là không khả mở. Không thực tế khi kết nối quá nhiều switch, mà trong thực tế *nhiều* thường nghĩa là “vài chục”. Một lý do là thuật toán cây bao phủ khả mở tuyến tính; tức là không có cơ chế phân cấp cho tập switch. Lý do thứ hai là switch chuyển tiếp tất cả frame broadcast. Dù hợp lý khi tất cả host trong một môi trường hạn chế (ví dụ, một phòng ban) nhìn thấy broadcast của nhau, nhưng không hợp lý khi tất cả host trong một môi trường lớn hơn (ví dụ, một công ty hay trường đại học lớn) phải nhận broadcast của nhau. Nói cách khác, broadcast không khả mở, và do đó mạng dựa trên tầng 2 không khả mở.

Một cách để tăng khả mở là *LAN ảo* (VLAN). VLAN cho phép một LAN mở rộng được chia thành nhiều LAN dường như tách biệt. Mỗi VLAN được gán một định danh (đôi khi gọi là *màu*), và gói chỉ có thể đi từ một đoạn sang đoạn khác nếu cả hai cùng có định danh đó. Điều này giới hạn số đoạn trong một LAN mở rộng sẽ nhận bất kỳ gói broadcast nào.

.. _fig-vlan:
.. figure:: figures/impl/Slide7.png
   :width: 350px
   :align: center

   Hai LAN ảo chia sẻ một backbone chung.

Ta có thể thấy VLAN hoạt động qua ví dụ. :numref:`Hình %s <fig-vlan>` cho thấy bốn host và hai switch. Nếu không có VLAN, bất kỳ gói broadcast nào từ bất kỳ host nào cũng sẽ tới tất cả các host khác. Giờ giả sử ta định nghĩa các đoạn kết nối tới host W và X thuộc một VLAN, gọi là VLAN 100. Ta cũng định nghĩa các đoạn kết nối tới host Y và Z thuộc VLAN 200. Để làm điều này, ta cần cấu hình một VLAN ID trên mỗi cổng của switch S1 và S2. Liên kết giữa S1 và S2 được coi là thuộc cả hai VLAN.

Khi một gói do host X gửi đến switch S2, switch nhận thấy nó đến từ một cổng được cấu hình thuộc VLAN 100. Nó chèn một tiêu đề VLAN giữa tiêu đề Ethernet và payload. Phần quan trọng của tiêu đề VLAN là VLAN ID; ở đây, ID được đặt là 100. Switch giờ áp dụng các quy tắc chuyển tiếp thông thường cho gói, với ràng buộc bổ sung là gói không được gửi ra giao diện không thuộc VLAN 100. Như vậy, trong mọi trường hợp, gói—even broadcast—sẽ không được gửi ra giao diện tới host Z, vốn thuộc VLAN 200. Tuy nhiên, gói sẽ được chuyển tiếp tới switch S1, switch này cũng áp dụng quy tắc tương tự và có thể chuyển tiếp gói tới host W nhưng không tới host Y.

Một điểm hấp dẫn của VLAN là có thể thay đổi cấu trúc logic mà không cần di chuyển dây hay đổi địa chỉ. Ví dụ, nếu muốn liên kết tới host Z thuộc VLAN 100 và cho phép X, W, Z cùng thuộc một LAN ảo, ta chỉ cần thay đổi một cấu hình trên switch S2.

Hỗ trợ VLAN chỉ cần mở rộng đơn giản cho đặc tả tiêu đề 802.1 gốc, chèn một trường VLAN ID 12 bit (``VID``) giữa các trường ``SrcAddr`` và ``Type``, như minh họa trong :numref:`Hình %s <fig-vlan-tag>`. (VID này thường gọi là *VLAN Tag*.) Thực tế có 32 bit được chèn vào giữa tiêu đề, nhưng 16 bit đầu dùng để đảm bảo tương thích ngược với đặc tả gốc (dùng ``Type = 0x8100`` để chỉ frame này có mở rộng VLAN); bốn bit còn lại chứa thông tin điều khiển dùng để ưu tiên frame. Điều này nghĩa là có thể ánh xạ :math:`2^{12} = 4096` mạng ảo lên một LAN vật lý duy nhất.

.. _fig-vlan-tag:
.. figure:: figures/impl/Slide4.png
   :width: 500px
   :align: center

   Thẻ VLAN 802.1Q nhúng trong tiêu đề Ethernet (802.1).

Kết thúc phần này, cần lưu ý một hạn chế khác của mạng xây dựng bằng cách kết nối các switch tầng 2: thiếu hỗ trợ dị chủng. Tức là, switch bị giới hạn trong các loại mạng mà chúng có thể kết nối. Cụ thể, switch sử dụng tiêu đề frame của mạng và chỉ có thể hỗ trợ các mạng có định dạng địa chỉ giống hệt nhau. Ví dụ, switch có thể dùng để kết nối mạng Ethernet và 802.11 với nhau, vì chúng có định dạng tiêu đề chung, nhưng switch không dễ dàng tổng quát hóa cho các loại mạng khác có định dạng địa chỉ khác, như ATM, SONET, PON hoặc mạng di động. Phần tiếp theo sẽ giải thích cách khắc phục hạn chế này, cũng như mở rộng mạng chuyển mạch lên quy mô lớn hơn nữa.
