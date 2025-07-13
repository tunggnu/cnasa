2.6 Mạng Truy Cập Đa Điểm
=========================

Được phát triển vào giữa những năm 1970 bởi các nhà nghiên cứu tại Trung tâm Nghiên cứu Palo Alto của Xerox (PARC), Ethernet cuối cùng đã trở thành công nghệ mạng cục bộ (LAN) thống trị, vượt lên từ một nhóm các công nghệ cạnh tranh. Ngày nay, nó chủ yếu cạnh tranh với các mạng không dây 802.11 nhưng vẫn cực kỳ phổ biến trong các mạng khuôn viên trường và trung tâm dữ liệu. Tên gọi chung hơn cho công nghệ đứng sau Ethernet là Carrier Sense, Multiple Access with Collision Detect (CSMA/CD) – Cảm nhận sóng mang, truy cập đa điểm với phát hiện va chạm.

Như tên gọi CSMA cho thấy, Ethernet là một mạng truy cập đa điểm, nghĩa là một tập hợp các nút gửi và nhận khung dữ liệu qua một liên kết chia sẻ. Bạn có thể hình dung Ethernet giống như một xe buýt mà nhiều trạm được cắm vào đó. “Cảm nhận sóng mang” trong CSMA/CD nghĩa là tất cả các nút có thể phân biệt được liên kết đang rỗi hay bận, và “phát hiện va chạm” nghĩa là một nút sẽ lắng nghe khi nó truyền và do đó có thể phát hiện khi một khung mà nó đang truyền bị can thiệp (va chạm) với một khung do nút khác truyền.

Ethernet có nguồn gốc từ một mạng radio gói tin đầu tiên, gọi là Aloha, được phát triển tại Đại học Hawaii để hỗ trợ truyền thông máy tính trên các đảo Hawaii. Giống như mạng Aloha, vấn đề cơ bản mà Ethernet phải đối mặt là làm thế nào để điều phối việc truy cập vào một môi trường chia sẻ một cách công bằng và hiệu quả (ở Aloha, môi trường là khí quyển, còn ở Ethernet ban đầu là cáp đồng trục). Ý tưởng cốt lõi ở cả Aloha và Ethernet là một thuật toán kiểm soát thời điểm mỗi nút có thể truyền.

Các liên kết Ethernet hiện đại chủ yếu là điểm-điểm; tức là, chúng kết nối một máy chủ với một *switch* Ethernet, hoặc kết nối các switch với nhau. Do đó, thuật toán “truy cập đa điểm” không còn được sử dụng nhiều trong các Ethernet có dây ngày nay, nhưng một biến thể của nó lại được sử dụng trong các mạng không dây, như mạng 802.11 (còn gọi là Wi-Fi). Do ảnh hưởng to lớn của Ethernet, chúng tôi chọn mô tả thuật toán cổ điển của nó ở đây, sau đó giải thích cách nó được điều chỉnh cho Wi-Fi ở phần tiếp theo. Chúng tôi cũng sẽ thảo luận về các switch Ethernet ở nơi khác. Hiện tại, chúng ta sẽ tập trung vào cách một liên kết Ethernet đơn lẻ hoạt động.

Digital Equipment Corporation và Intel Corporation đã cùng Xerox định nghĩa một chuẩn Ethernet 10-Mbps vào năm 1978. Chuẩn này sau đó trở thành cơ sở cho chuẩn IEEE 802.3, vốn còn định nghĩa thêm nhiều loại môi trường vật lý mà Ethernet có thể hoạt động, bao gồm các phiên bản 100-Mbps, 1-Gbps, 10-Gbps, 40-Gbps và 100-Gbps.

2.6.1 Đặc Tính Vật Lý
---------------------

Các đoạn Ethernet ban đầu được triển khai bằng cáp đồng trục dài tới 500 m. (Ethernet hiện đại sử dụng cặp dây đồng xoắn, thường là loại “Category 5”, hoặc cáp quang, và trong một số trường hợp có thể dài hơn 500 m khá nhiều.) Loại cáp này tương tự như loại dùng cho truyền hình cáp. Các máy chủ kết nối vào một đoạn Ethernet bằng cách “tap” vào nó. Một *transceiver* (bộ thu phát), là một thiết bị nhỏ gắn trực tiếp vào điểm tap, phát hiện khi đường truyền đang rỗi và điều khiển tín hiệu khi máy chủ truyền dữ liệu. Nó cũng nhận các tín hiệu đến. Bộ thu phát này lại kết nối với một bộ chuyển đổi Ethernet (Ethernet adaptor), được cắm vào máy chủ. Cấu hình này được minh họa trong :numref:`Hình %s <fig-tap>`.

.. _fig-tap:
.. figure:: figures/f02-22-9780123850591.png
   :width: 300px
   :align: center

   Bộ thu phát và bộ chuyển đổi Ethernet.

Nhiều đoạn Ethernet có thể được nối với nhau bằng *repeater* (hoặc một biến thể nhiều cổng gọi là *hub*). Repeater là một thiết bị chuyển tiếp tín hiệu số, giống như bộ khuếch đại chuyển tiếp tín hiệu tương tự; repeater không hiểu bit hay khung dữ liệu. Không được có quá bốn repeater giữa bất kỳ cặp máy chủ nào, nghĩa là một Ethernet cổ điển chỉ có thể kéo dài tối đa 2500 m. Ví dụ, chỉ cần hai repeater giữa bất kỳ cặp máy chủ nào là đã hỗ trợ một cấu hình tương tự như minh họa trong :numref:`Hình %s <fig-net-repeat>`; tức là, một đoạn chạy dọc theo trục chính của tòa nhà với một đoạn trên mỗi tầng.

.. _fig-net-repeat:
.. figure:: figures/f02-23-9780123850591.png
   :width: 500px
   :align: center

   Repeater Ethernet, kết nối các đoạn để tạo thành một miền va chạm lớn hơn.

Bất kỳ tín hiệu nào được đặt lên Ethernet bởi một máy chủ đều được phát sóng trên toàn bộ mạng; tức là, tín hiệu được truyền đi theo cả hai hướng, và repeater cũng như hub sẽ chuyển tiếp tín hiệu trên tất cả các đoạn đầu ra. Các bộ kết thúc (terminator) gắn ở cuối mỗi đoạn sẽ hấp thụ tín hiệu và ngăn nó dội lại, gây nhiễu cho các tín hiệu phía sau. Đặc tả Ethernet ban đầu sử dụng sơ đồ mã hóa Manchester như đã mô tả ở phần trước, trong khi mã hóa 4B/5B (hoặc tương tự là 8B/10B) được sử dụng ngày nay trên các Ethernet tốc độ cao hơn.

Điều quan trọng cần hiểu là dù một Ethernet trải dài trên một đoạn đơn, một chuỗi tuyến tính các đoạn nối bằng repeater, hay nhiều đoạn nối theo cấu hình hình sao, dữ liệu được truyền bởi bất kỳ máy chủ nào trên Ethernet đó đều đến được tất cả các máy chủ khác. Đây là tin tốt. Tin xấu là tất cả các máy chủ này đều cạnh tranh quyền truy cập vào cùng một liên kết, và do đó, chúng được cho là nằm trong cùng một *miền va chạm* (collision domain). Phần truy cập đa điểm của Ethernet chủ yếu là xử lý sự cạnh tranh cho liên kết phát sinh trong một miền va chạm.

2.6.2 Giao Thức Truy Cập
------------------------

Bây giờ chúng ta chuyển sang thuật toán kiểm soát truy cập vào một liên kết Ethernet chia sẻ. Thuật toán này thường được gọi là *media access control* (MAC) của Ethernet. Nó thường được triển khai bằng phần cứng trên bộ chuyển đổi mạng. Chúng tôi sẽ không mô tả phần cứng *per se*, mà tập trung vào thuật toán mà nó thực hiện. Tuy nhiên, trước tiên, chúng tôi sẽ mô tả định dạng khung và địa chỉ của Ethernet.

Định Dạng Khung
~~~~~~~~~~~~~~~

Mỗi khung Ethernet được xác định bởi định dạng trong :numref:`Hình %s <fig-enet-format>`. Phần tiền tố 64 bit cho phép bộ nhận đồng bộ với tín hiệu; nó là một chuỗi các số 0 và 1 xen kẽ. Cả máy chủ nguồn và đích đều được xác định bằng địa chỉ 48 bit. Trường loại gói tin đóng vai trò là khóa phân kênh; nó xác định khung này sẽ được chuyển cho giao thức tầng cao nào. Mỗi khung chứa tối đa 1500 byte dữ liệu. Tối thiểu, một khung phải chứa ít nhất 46 byte dữ liệu, ngay cả khi điều này có nghĩa là máy chủ phải đệm thêm dữ liệu trước khi truyền. Lý do cho kích thước khung tối thiểu này là khung phải đủ dài để phát hiện va chạm; chúng ta sẽ bàn thêm về điều này bên dưới. Cuối cùng, mỗi khung bao gồm một CRC 32 bit. Giống như giao thức HDLC đã mô tả ở phần trước, Ethernet là một giao thức đóng khung theo bit. Lưu ý rằng từ góc nhìn của máy chủ, một khung Ethernet có phần đầu 14 byte: hai địa chỉ 6 byte và một trường loại 2 byte. Bộ chuyển đổi gửi sẽ gắn thêm phần tiền tố và CRC trước khi truyền, và bộ chuyển đổi nhận sẽ loại bỏ chúng.

.. _fig-enet-format:
.. figure:: figures/f02-25-9780123850591.png
   :width: 400px
   :align: center

   Định dạng khung Ethernet.

Địa Chỉ
~~~~~~~

Mỗi máy chủ trên Ethernet—thực ra là mọi máy chủ Ethernet trên thế giới—đều có một địa chỉ Ethernet duy nhất. Về mặt kỹ thuật, địa chỉ này thuộc về bộ chuyển đổi, không phải máy chủ; nó thường được ghi sẵn vào ROM. Địa chỉ Ethernet thường được in dưới dạng dễ đọc cho con người, là một chuỗi sáu số phân tách bằng dấu hai chấm. Mỗi số tương ứng với 1 byte của địa chỉ 6 byte và được biểu diễn bằng một cặp ký tự thập lục phân, mỗi ký tự cho một nửa byte (4 bit); các số 0 ở đầu được lược bỏ. Ví dụ, ``8:0:2b:e4:b1:2`` là dạng dễ đọc của địa chỉ Ethernet

::

   00001000  00000000  00101011  11100100  10110001  00000010

Để đảm bảo mỗi bộ chuyển đổi có một địa chỉ duy nhất, mỗi nhà sản xuất thiết bị Ethernet được cấp một tiền tố khác nhau phải được gắn vào địa chỉ trên mọi bộ chuyển đổi mà họ sản xuất. Ví dụ, Advanced Micro Devices được cấp tiền tố 24 bit ``080020`` (hoặc ``8:0:20``). Nhà sản xuất sau đó đảm bảo các hậu tố địa chỉ mà họ tạo ra là duy nhất.

Mỗi khung được truyền trên Ethernet đều được mọi bộ chuyển đổi kết nối với Ethernet đó nhận được. Mỗi bộ chuyển đổi nhận biết các khung được gửi đến địa chỉ của nó và chỉ chuyển các khung đó lên máy chủ. (Một bộ chuyển đổi cũng có thể được lập trình để chạy ở chế độ *promiscuous*, khi đó nó chuyển tất cả các khung nhận được lên máy chủ, nhưng đây không phải là chế độ bình thường.) Ngoài các địa chỉ *unicast* này, một địa chỉ Ethernet gồm toàn số 1 được coi là địa chỉ *broadcast*; tất cả các bộ chuyển đổi sẽ chuyển các khung gửi đến địa chỉ broadcast lên máy chủ. Tương tự, một địa chỉ có bit đầu tiên là 1 nhưng không phải địa chỉ broadcast được gọi là địa chỉ *multicast*. Một máy chủ có thể lập trình bộ chuyển đổi của mình để chấp nhận một tập hợp các địa chỉ multicast. Địa chỉ multicast được dùng để gửi thông điệp đến một tập con các máy chủ trên Ethernet (ví dụ, tất cả các máy chủ tệp). Tóm lại, một bộ chuyển đổi Ethernet nhận tất cả các khung và chấp nhận

-  Các khung gửi đến địa chỉ của chính nó

-  Các khung gửi đến địa chỉ broadcast

-  Các khung gửi đến địa chỉ multicast, nếu nó đã được chỉ định lắng nghe địa chỉ đó

-  Tất cả các khung, nếu nó được đặt ở chế độ promiscuous

Nó chỉ chuyển lên máy chủ các khung mà nó chấp nhận.

Thuật Toán Truyền Dữ Liệu
~~~~~~~~~~~~~~~~~~~~~~~~~

Như chúng ta vừa thấy, phía nhận của giao thức Ethernet khá đơn giản; phần thông minh thực sự được triển khai ở phía gửi. Thuật toán truyền dữ liệu được định nghĩa như sau.

Khi bộ chuyển đổi có một khung cần gửi và đường truyền đang rỗi, nó truyền khung ngay lập tức; không có thương lượng gì với các bộ chuyển đổi khác. Giới hạn trên 1500 byte trong thông điệp nghĩa là bộ chuyển đổi chỉ có thể chiếm đường truyền trong một khoảng thời gian cố định.

Khi một bộ chuyển đổi có khung cần gửi và đường truyền đang bận, nó chờ cho đến khi đường truyền rỗi rồi truyền ngay lập tức. (Chính xác hơn, tất cả các bộ chuyển đổi chờ 9,6 μs sau khi một khung kết thúc trước khi bắt đầu truyền khung tiếp theo. Điều này đúng cho cả bộ gửi khung đầu tiên cũng như các nút đang lắng nghe để đường truyền trở nên rỗi.) Ethernet được gọi là giao thức *1-persistent* vì một bộ chuyển đổi có khung cần gửi sẽ truyền với xác suất 1 bất cứ khi nào đường truyền bận trở nên rỗi. Nói chung, một thuật toán *p-persistent* sẽ truyền với xác suất :math:`0 \le p \le 1` sau khi đường truyền trở nên rỗi và trì hoãn với xác suất *q = 1 - p*. Lý do chọn *p<1* là vì có thể có nhiều bộ chuyển đổi đang chờ đường truyền bận trở nên rỗi, và chúng ta không muốn tất cả đều bắt đầu truyền cùng lúc. Nếu mỗi bộ chuyển đổi truyền ngay với xác suất, ví dụ, 33%, thì tối đa ba bộ chuyển đổi có thể chờ truyền và khả năng chỉ một bộ sẽ bắt đầu truyền khi đường truyền rỗi là cao. Tuy nhiên, trên thực tế, một bộ chuyển đổi Ethernet luôn truyền ngay lập tức sau khi phát hiện mạng đã rỗi và điều này đã tỏ ra rất hiệu quả.

Để hoàn thiện câu chuyện về các giao thức *p-persistent* cho trường hợp *p<1*, bạn có thể thắc mắc một bộ gửi bị “thua” khi tung đồng xu (tức là quyết định trì hoãn) sẽ phải chờ bao lâu trước khi có thể truyền. Câu trả lời cho mạng Aloha, vốn phát triển kiểu giao thức này, là chia thời gian thành các khe rời rạc, mỗi khe tương ứng với thời gian cần để truyền một khung đầy đủ. Bất cứ khi nào một nút có khung cần gửi và nó phát hiện một khe rỗi, nó sẽ truyền với xác suất *p* và trì hoãn đến khe tiếp theo với xác suất *q = 1 - p*. Nếu khe tiếp theo cũng rỗi, nút lại quyết định truyền hoặc trì hoãn, với xác suất *p* và *q* tương ứng. Nếu khe tiếp theo không rỗi—tức là, một trạm khác đã quyết định truyền—thì nút chỉ chờ đến khe rỗi tiếp theo và thuật toán lặp lại.

Quay lại với Ethernet, vì không có kiểm soát tập trung nên có thể hai (hoặc nhiều) bộ chuyển đổi bắt đầu truyền cùng lúc, hoặc vì cả hai đều thấy đường truyền rỗi hoặc vì cả hai đều đã chờ đường truyền bận trở nên rỗi. Khi điều này xảy ra, hai (hoặc nhiều) khung được cho là *va chạm* trên mạng. Mỗi bộ gửi, nhờ Ethernet hỗ trợ phát hiện va chạm, có thể xác định rằng một va chạm đang diễn ra. Ngay khi một bộ chuyển đổi phát hiện khung của nó đang va chạm với khung khác, nó sẽ chắc chắn truyền một chuỗi gây nhiễu 32 bit rồi dừng truyền. Do đó, một bộ gửi sẽ gửi tối thiểu 96 bit trong trường hợp va chạm: 64 bit tiền tố cộng 32 bit chuỗi gây nhiễu.

Một trường hợp mà một bộ chuyển đổi chỉ gửi 96 bit—đôi khi gọi là *runt frame*—là khi hai máy chủ ở gần nhau. Nếu hai máy chủ ở xa nhau hơn, chúng sẽ phải truyền lâu hơn, và do đó gửi nhiều bit hơn, trước khi phát hiện va chạm. Thực tế, kịch bản tệ nhất xảy ra khi hai máy chủ ở hai đầu đối diện của Ethernet. Để chắc chắn rằng khung vừa gửi không bị va chạm với khung khác, bộ gửi có thể cần gửi tới 512 bit. Không phải ngẫu nhiên mà mỗi khung Ethernet phải dài ít nhất 512 bit (64 byte): 14 byte tiêu đề cộng 46 byte dữ liệu cộng 4 byte CRC.

Tại sao lại là 512 bit? Câu trả lời liên quan đến một câu hỏi khác bạn có thể đặt ra về Ethernet: Tại sao chiều dài của nó chỉ giới hạn ở 2500 m? Tại sao không phải 10 hay 1000 km? Câu trả lời cho cả hai câu hỏi đều liên quan đến thực tế là càng xa nhau, hai nút càng mất nhiều thời gian để một khung do một nút gửi đến được nút kia, và mạng dễ bị va chạm trong thời gian này.

.. _fig-worst:
.. figure:: figures/f02-26-9780123850591.png
   :width: 350px
   :align: center

   Kịch bản tệ nhất: (a) A gửi một khung tại thời điểm t;
   (b) Khung của A đến B tại thời điểm t+d; (c) B bắt đầu truyền
   tại thời điểm t+d và va chạm với khung của A; (d) Khung “runt” (32 bit)
   của B đến A tại thời điểm t+2×d.

:numref:`Hình %s <fig-worst>` minh họa kịch bản tệ nhất, khi các máy chủ A và B ở hai đầu đối diện của mạng. Giả sử A bắt đầu truyền một khung tại thời điểm t, như trong (a). Mất một độ trễ liên kết (ký hiệu là d) để khung đến được B. Do đó, bit đầu tiên của khung A đến B tại thời điểm t+d, như trong (b). Giả sử ngay trước khi khung của A đến (tức là B vẫn thấy đường truyền rỗi), B bắt đầu truyền khung của mình. Khung của B sẽ ngay lập tức va chạm với khung của A, và va chạm này sẽ được B phát hiện (c). B sẽ gửi chuỗi gây nhiễu 32 bit như mô tả ở trên. (Khung của B sẽ là “runt”.) Đáng tiếc, A sẽ không biết va chạm xảy ra cho đến khi khung của B đến được nó, điều này sẽ xảy ra sau một độ trễ liên kết nữa, tại thời điểm t+2×d, như trong (d). A phải tiếp tục truyền cho đến thời điểm này để phát hiện mọi va chạm có thể xảy ra. Nói cách khác, A phải truyền trong *2×d* để chắc chắn phát hiện tất cả các va chạm. Xét rằng một Ethernet cấu hình tối đa dài 2500 m, và có thể có tới bốn repeater giữa hai máy chủ bất kỳ, độ trễ vòng đã được xác định là 51,2 μs, tương ứng với 512 bit trên Ethernet 10-Mbps. Một cách nhìn khác là chúng ta cần giới hạn độ trễ tối đa của Ethernet ở một giá trị khá nhỏ (ví dụ, 51,2 μs) để thuật toán truy cập hoạt động; do đó, chiều dài tối đa của một Ethernet phải vào khoảng 2500 m.

Khi một bộ chuyển đổi phát hiện va chạm và dừng truyền, nó sẽ chờ một khoảng thời gian nhất định rồi thử lại. Mỗi lần thử truyền nhưng thất bại, bộ chuyển đổi sẽ nhân đôi khoảng thời gian chờ trước khi thử lại. Chiến lược nhân đôi khoảng chờ giữa các lần thử truyền lại này là một kỹ thuật chung gọi là *exponential backoff* (lùi theo hàm mũ). Cụ thể hơn, bộ chuyển đổi đầu tiên sẽ chờ 0 hoặc 51,2 μs, chọn ngẫu nhiên. Nếu lần này thất bại, nó sẽ chờ 0, 51,2, 102,4 hoặc 153,6 μs (chọn ngẫu nhiên) trước khi thử lại; tức là k × 51,2 với k=0..3. Sau va chạm thứ ba, nó sẽ chờ *k × 51,2* với k = 0..2³ - 1, lại chọn ngẫu nhiên. Nói chung, thuật toán chọn ngẫu nhiên một *k* trong khoảng 0 đến 2ⁿ - 1 và chờ k × 51,2 μs, với *n* là số lần va chạm đã gặp. Bộ chuyển đổi sẽ bỏ cuộc sau một số lần thử nhất định và báo lỗi truyền cho máy chủ. Thông thường, các bộ chuyển đổi thử lại tối đa 16 lần, mặc dù thuật toán lùi theo hàm mũ sẽ giới hạn *n* trong công thức trên ở mức 10.

2.6.3 Tuổi Thọ của Ethernet
---------------------------

Ethernet đã là công nghệ mạng cục bộ thống trị trong hơn 30 năm. Ngày nay nó thường được triển khai điểm-điểm thay vì tap vào cáp đồng trục, thường chạy ở tốc độ 1 hoặc 10 Gbps thay vì 10 Mbps, và cho phép các gói jumbo lên tới 9000 byte dữ liệu thay vì 1500 byte. Tuy nhiên, nó vẫn tương thích ngược với tiêu chuẩn ban đầu. Điều này khiến chúng ta nên nói vài lời về lý do tại sao Ethernet lại thành công đến vậy, để chúng ta có thể hiểu những đặc tính nào nên được mô phỏng với bất kỳ công nghệ nào muốn thay thế nó.

Thứ nhất, Ethernet cực kỳ dễ quản trị và bảo trì: Không có bảng định tuyến hay cấu hình nào cần cập nhật, và việc thêm một máy chủ mới vào mạng rất dễ dàng. Thật khó tưởng tượng một mạng nào đơn giản hơn để quản trị. Thứ hai, nó rẻ: cáp/quang khá rẻ, và chi phí duy nhất khác là bộ chuyển đổi mạng trên mỗi máy chủ. Ethernet đã ăn sâu vào thực tế vì những lý do này, và bất kỳ phương pháp dựa trên switch nào muốn thay thế nó đều đòi hỏi đầu tư thêm vào hạ tầng (các switch), ngoài chi phí cho mỗi bộ chuyển đổi. Biến thể dựa trên switch của Ethernet cuối cùng đã thành công trong việc thay thế Ethernet truy cập đa điểm, nhưng chủ yếu là vì nó có thể *triển khai từng bước*—với một số máy chủ kết nối điểm-điểm tới switch trong khi các máy chủ khác vẫn tap vào cáp đồng trục và kết nối với repeater hoặc hub—tất cả vẫn giữ được sự đơn giản trong quản trị mạng.
