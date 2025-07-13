4.4 Chuyển Mạch Nhãn Đa Giao Thức (Multiprotocol Label Switching)
=================================================================

Chúng ta tiếp tục thảo luận về các cải tiến cho IP bằng cách mô tả một bổ sung cho kiến trúc Internet được sử dụng rất rộng rãi nhưng phần lớn bị ẩn khỏi người dùng cuối. Cải tiến này, gọi là *Multiprotocol Label Switching* (MPLS), kết hợp một số đặc tính của mạch ảo với sự linh hoạt và độ tin cậy của datagram. Một mặt, MPLS gắn liền với kiến trúc dựa trên datagram của Giao thức Internet—nó dựa vào địa chỉ IP và các giao thức định tuyến IP để thực hiện công việc của mình. Mặt khác, các router hỗ trợ MPLS cũng chuyển tiếp gói tin bằng cách kiểm tra các nhãn ngắn, có độ dài cố định, và các nhãn này có phạm vi cục bộ, giống như trong mạng mạch ảo. Có lẽ chính sự kết hợp của hai công nghệ tưởng như đối lập này đã khiến MPLS nhận được sự đón nhận khá trái chiều trong cộng đồng kỹ thuật Internet.

Trước khi xem xét cách MPLS hoạt động, hợp lý để hỏi “nó dùng để làm gì?” Có rất nhiều tuyên bố về MPLS, nhưng có ba mục đích chính mà nó được sử dụng ngày nay:

-  Cho phép các thiết bị không có khả năng chuyển tiếp datagram IP theo cách thông thường có thể hỗ trợ IP

-  Chuyển tiếp các gói IP theo các tuyến đường rõ ràng—các tuyến đường được tính toán trước mà không nhất thiết phải trùng với các tuyến mà các giao thức định tuyến IP thông thường sẽ chọn

-  Hỗ trợ một số loại dịch vụ mạng riêng ảo (VPN)

Đáng chú ý là một trong những mục tiêu ban đầu—cải thiện hiệu năng—không nằm trong danh sách này. Điều này liên quan nhiều đến những tiến bộ đã đạt được trong các thuật toán chuyển tiếp cho router IP trong những năm gần đây và với tập hợp các yếu tố phức tạp ngoài xử lý header quyết định hiệu năng.

Cách tốt nhất để hiểu MPLS hoạt động như thế nào là xem một số ví dụ về việc sử dụng nó. Trong ba phần tiếp theo, chúng ta sẽ xem các ví dụ minh họa ba ứng dụng của MPLS vừa nêu trên.

4.4.1 Chuyển Tiếp Dựa Trên Đích Đến
------------------------------------

Một trong những bài báo đầu tiên giới thiệu ý tưởng gắn nhãn vào các gói IP là bài báo của Chandranmenon và Varghese mô tả một ý tưởng gọi là *threaded indices*. Một ý tưởng rất giống hiện nay đã được hiện thực trong các router hỗ trợ MPLS. Ví dụ sau đây cho thấy ý tưởng này hoạt động như thế nào.

.. _fig-mpls-dest:
.. figure:: figures/f04-18-9780123850591.png
   :width: 500px
   :align: center

   Bảng định tuyến trong mạng ví dụ.

Xét mạng trong :numref:`Figure %s <fig-mpls-dest>`. Mỗi trong hai router ở phía bên phải (R3 và R4) có một mạng kết nối, với các tiền tố ``18.1.1/24`` và ``18.3.3/24``. Các router còn lại (R1 và R2) có bảng định tuyến chỉ ra giao diện ra nào mà mỗi router sẽ sử dụng khi chuyển tiếp các gói đến một trong hai mạng đó.

Khi MPLS được bật trên một router, router sẽ cấp phát một nhãn cho mỗi tiền tố trong bảng định tuyến của nó và quảng bá cả nhãn và tiền tố mà nó đại diện cho các router láng giềng. Quảng bá này được thực hiện qua Giao thức Phân Phối Nhãn (Label Distribution Protocol). Điều này được minh họa trong :numref:`Figure %s <fig-label-dist>`. Router R2 đã cấp phát giá trị nhãn ``15`` cho tiền tố ``18.1.1`` và giá trị nhãn ``16`` cho tiền tố ``18.3.3``. Các nhãn này có thể được chọn tùy ý bởi router cấp phát và có thể được coi như các chỉ số vào bảng định tuyến. Sau khi cấp phát các nhãn, R2 quảng bá các ánh xạ nhãn đến các láng giềng; trong trường hợp này, chúng ta thấy R2 quảng bá một ánh xạ giữa nhãn ``15`` và tiền tố ``18.1.1`` cho R1. Ý nghĩa của quảng bá này là R2 đã nói, về cơ bản, “Hãy gắn nhãn ``15`` vào tất cả các gói gửi cho tôi mà có đích đến là tiền tố ``18.1.1``.” R1 lưu nhãn này vào một bảng cùng với tiền tố mà nó đại diện như là nhãn từ xa hoặc nhãn ra cho bất kỳ gói nào nó gửi đến tiền tố đó.

Trong :numref:`Figure %s(c) <fig-label-dist>`, chúng ta thấy một quảng bá nhãn khác từ router R3 đến R2 cho tiền tố ``18.1.1``, và R2 lưu nhãn từ xa mà nó học được từ R3 vào vị trí thích hợp trong bảng của nó.

.. _fig-label-dist:
.. figure:: figures/f04-19-9780123850591.png
   :width: 500px
   :align: center

   (a) R2 cấp phát nhãn và quảng bá ánh xạ đến R1. (b) R1 lưu
   các nhãn nhận được vào bảng. (c) R3 quảng bá một ánh xạ khác,
   và R2 lưu nhãn nhận được vào bảng.

Tại thời điểm này, chúng ta có thể xem điều gì xảy ra khi một gói được chuyển tiếp trong mạng này. Giả sử một gói có đích đến địa chỉ IP ``18.1.1.5`` đến từ bên trái router R1. Trong trường hợp này, R1 được gọi là *Label Edge Router* (LER); một LER thực hiện tra cứu IP đầy đủ trên các gói IP đến và sau đó gắn nhãn cho chúng dựa trên kết quả tra cứu. Trong trường hợp này, R1 sẽ thấy rằng ``18.1.1.5`` khớp với tiền tố ``18.1.1`` trong bảng chuyển tiếp của nó và mục này chứa cả giao diện ra và giá trị nhãn từ xa. Do đó, R1 sẽ gắn nhãn từ xa ``15`` vào gói trước khi gửi đi.

Khi gói đến R2, R2 chỉ nhìn vào nhãn trong gói, không phải địa chỉ IP. Bảng chuyển tiếp tại R2 chỉ ra rằng các gói đến với giá trị nhãn ``15`` nên được gửi ra giao diện 1 và nên mang giá trị nhãn ``24``, như đã được quảng bá bởi router R3. Do đó, R2 sẽ ghi đè, hoặc hoán đổi, nhãn và chuyển tiếp nó đến R3.

Điều gì đã đạt được qua tất cả việc gắn và hoán đổi nhãn này? Hãy chú ý rằng khi R2 chuyển tiếp gói trong ví dụ này, nó thực sự không cần phải kiểm tra địa chỉ IP. Thay vào đó, R2 chỉ nhìn vào nhãn đến. Như vậy, chúng ta đã thay thế việc tra cứu địa chỉ đích IP thông thường bằng một tra cứu nhãn. Để hiểu tại sao điều này quan trọng, hãy nhớ rằng, mặc dù địa chỉ IP luôn có cùng độ dài, các tiền tố IP lại có độ dài biến đổi, và thuật toán tra cứu địa chỉ đích IP cần tìm *khớp dài nhất*—tiền tố dài nhất khớp với các bit cao của địa chỉ IP của gói đang được chuyển tiếp. Ngược lại, cơ chế chuyển tiếp nhãn vừa mô tả là một thuật toán *khớp chính xác*. Có thể hiện thực một thuật toán khớp chính xác rất đơn giản, ví dụ, bằng cách sử dụng nhãn như một chỉ số vào một mảng, trong đó mỗi phần tử của mảng là một dòng trong bảng chuyển tiếp.

Lưu ý rằng, mặc dù thuật toán chuyển tiếp đã được thay đổi từ khớp dài nhất sang khớp chính xác, thuật toán định tuyến có thể là bất kỳ thuật toán định tuyến IP tiêu chuẩn nào (ví dụ, OSPF). Đường đi mà một gói sẽ theo trong môi trường này chính xác là đường đi mà nó sẽ theo nếu không có MPLS: đường đi được chọn bởi các thuật toán định tuyến IP. Tất cả những gì thay đổi là thuật toán chuyển tiếp.

Một khái niệm cơ bản quan trọng của MPLS được minh họa qua ví dụ này. Mỗi nhãn MPLS được liên kết với một *forwarding equivalence class* (FEC)—một tập hợp các gói sẽ nhận được cùng một xử lý chuyển tiếp tại một router cụ thể. Trong ví dụ này, mỗi tiền tố trong bảng định tuyến là một FEC; tức là, tất cả các gói khớp với tiền tố 18.1.1—bất kể các bit thấp của địa chỉ IP là gì—đều được chuyển tiếp theo cùng một đường đi. Do đó, mỗi router có thể cấp phát một nhãn ánh xạ đến 18.1.1, và bất kỳ gói nào chứa địa chỉ IP có các bit cao khớp với tiền tố đó đều có thể được chuyển tiếp bằng nhãn đó.

Như chúng ta sẽ thấy trong các ví dụ tiếp theo, FEC là một khái niệm rất mạnh mẽ và linh hoạt. FEC có thể được hình thành dựa trên hầu như bất kỳ tiêu chí nào; ví dụ, tất cả các gói tương ứng với một khách hàng cụ thể có thể được coi là thuộc cùng một FEC.

Quay lại ví dụ đang xét, chúng ta nhận thấy rằng việc thay đổi thuật toán chuyển tiếp từ chuyển tiếp IP thông thường sang hoán đổi nhãn có một hệ quả quan trọng: Các thiết bị trước đây không biết cách chuyển tiếp các gói IP có thể được sử dụng để chuyển tiếp lưu lượng IP trong một mạng MPLS. Ứng dụng nổi bật đầu tiên của kết quả này là với các switch ATM, vốn có thể hỗ trợ MPLS mà không cần thay đổi phần cứng chuyển tiếp của chúng. Các switch ATM hỗ trợ thuật toán chuyển tiếp hoán đổi nhãn vừa mô tả, và bằng cách cung cấp cho các switch này các giao thức định tuyến IP và một phương pháp phân phối ánh xạ nhãn, chúng có thể được biến thành *Label Switching Routers* (LSRs)—các thiết bị chạy các giao thức điều khiển IP nhưng sử dụng thuật toán chuyển tiếp nhãn. Gần đây hơn, ý tưởng tương tự đã được áp dụng cho các switch quang.

Trước khi chúng ta xem xét các lợi ích được cho là của việc biến một switch ATM thành LSR, chúng ta nên giải quyết một số điểm còn bỏ ngỏ. Chúng ta đã nói rằng các nhãn được “gắn” vào các gói, nhưng chúng được gắn ở đâu? Câu trả lời phụ thuộc vào loại liên kết mà các gói được truyền trên đó. Hai phương pháp phổ biến để mang nhãn trên các gói được thể hiện trong :numref:`Figure %s <fig-labels>`. Khi các gói IP được truyền dưới dạng các frame hoàn chỉnh, như trên hầu hết các loại liên kết bao gồm cả Ethernet và PPP, nhãn được chèn vào như một “shim” giữa header lớp 2 và header IP (hoặc header lớp 3 khác), như thể hiện ở phần dưới của hình. Tuy nhiên, nếu một switch ATM sẽ hoạt động như một MPLS LSR, thì nhãn cần phải nằm ở vị trí mà switch có thể sử dụng, tức là nó cần nằm trong header cell ATM, chính xác ở vị trí mà thông thường sẽ có trường nhận diện mạch ảo (VCI) và nhận diện đường ảo (VPI).

.. _fig-labels:
.. figure:: figures/f04-20-9780123850591.png
   :width: 600px
   :align: center

   (a) Nhãn trên một gói đóng gói ATM;  (b) nhãn trên
   một gói đóng gói frame.

Sau khi đã nghĩ ra một sơ đồ để một switch ATM có thể hoạt động như một LSR, chúng ta đã đạt được gì? Một điều cần lưu ý là bây giờ chúng ta có thể xây dựng một mạng sử dụng hỗn hợp các router IP thông thường, label edge router, và switch ATM hoạt động như LSR, và tất cả chúng sẽ sử dụng cùng một giao thức định tuyến. Để hiểu lợi ích của việc sử dụng cùng một giao thức, hãy xem xét phương án thay thế. Trong :numref:`Figure %s(a) <fig-overlay>`, chúng ta thấy một tập hợp các router được kết nối với nhau bằng các mạch ảo qua một mạng ATM, một cấu hình gọi là mạng *overlay*. Ở một thời điểm nào đó, các mạng kiểu này thường được xây dựng vì các switch ATM thương mại có tổng thông lượng cao hơn các router. Ngày nay, các mạng như vậy ít phổ biến hơn vì các router đã bắt kịp và thậm chí vượt qua các switch ATM. Tuy nhiên, các mạng này vẫn tồn tại do số lượng lớn switch ATM đã được lắp đặt trong các backbone mạng, điều này một phần là kết quả của khả năng hỗ trợ nhiều loại dịch vụ như mô phỏng mạch và dịch vụ mạch ảo của ATM.

.. _fig-overlay:
.. figure:: figures/f04-21-9780123850591.png
   :width: 400px
   :align: center

   (a) Các router kết nối với nhau bằng một overlay các
   mạch ảo. (b) Các router kết nối trực tiếp với LSR.

Trong một mạng overlay, mỗi router có thể được kết nối với mỗi router khác bằng một mạch ảo, nhưng trong trường hợp này để rõ ràng chúng ta chỉ vẽ các mạch từ R1 đến tất cả các router ngang hàng của nó. R1 có năm láng giềng định tuyến và cần trao đổi các thông điệp giao thức định tuyến với tất cả chúng—chúng ta nói rằng R1 có năm adjacency định tuyến. Ngược lại, trong :numref:`Figure %s(b) <fig-overlay>`, các switch ATM đã được thay thế bằng LSR. Không còn các mạch ảo kết nối các router nữa. Do đó, R1 chỉ có một adjacency, với LSR1. Trong các mạng lớn, chạy MPLS trên các switch dẫn đến giảm đáng kể số lượng adjacency mà mỗi router phải duy trì và có thể giảm đáng kể lượng công việc mà các router phải làm để cập nhật cho nhau về các thay đổi topo.

Một lợi ích thứ hai của việc chạy cùng giao thức định tuyến trên các router biên và trên các LSR là các router biên giờ đây có cái nhìn đầy đủ về topo của mạng. Điều này có nghĩa là nếu một liên kết hoặc nút nào đó bị lỗi bên trong mạng, các router biên sẽ có cơ hội tốt hơn để chọn một đường đi mới tốt hơn so với trường hợp các switch ATM tự động định tuyến lại các mạch ảo bị ảnh hưởng mà các router biên không biết.

Lưu ý rằng bước “thay thế” các switch ATM bằng LSR thực chất được thực hiện bằng cách thay đổi các giao thức chạy trên các switch, nhưng thường không cần thay đổi phần cứng chuyển tiếp; tức là, một switch ATM thường có thể được chuyển thành một MPLS LSR chỉ bằng cách nâng cấp phần mềm. Hơn nữa, một MPLS LSR có thể tiếp tục hỗ trợ các khả năng ATM tiêu chuẩn cùng lúc với việc chạy các giao thức điều khiển MPLS, trong chế độ gọi là “ships in the night”.

Ý tưởng chạy các giao thức điều khiển IP trên các thiết bị không thể chuyển tiếp gói IP một cách tự nhiên đã được mở rộng sang các mạng Wavelength Division Multiplexing (WDM) và Time Division Multiplexing (TDM) (ví dụ, SONET). Điều này được gọi là *Generalized MPLS* (GMPLS). Một phần động lực của GMPLS là cung cấp cho các router kiến thức topo về mạng quang, giống như trong trường hợp ATM. Thậm chí quan trọng hơn là thực tế rằng không có giao thức tiêu chuẩn nào để điều khiển các thiết bị quang, vì vậy MPLS tỏ ra là một lựa chọn tự nhiên cho công việc đó.

4.4.2 Định Tuyến Rõ Ràng (Explicit Routing)
--------------------------------------------

IP có một tùy chọn định tuyến nguồn (source routing), nhưng nó không được sử dụng rộng rãi vì nhiều lý do, bao gồm việc chỉ có thể chỉ định một số lượng hop giới hạn và vì nó thường được xử lý ngoài “đường nhanh” trên hầu hết các router.

MPLS cung cấp một cách thuận tiện để bổ sung các khả năng tương tự như định tuyến nguồn cho các mạng IP, mặc dù khả năng này thường được gọi là *explicit routing* (định tuyến rõ ràng) hơn là *source routing*. Một lý do cho sự phân biệt này là thường không phải nguồn thực sự của gói chọn đường đi. Thường thì đó là một trong các router bên trong mạng của nhà cung cấp dịch vụ. :numref:`Figure %s <fig-fish>` cho thấy một ví dụ về cách khả năng định tuyến rõ ràng của MPLS có thể được áp dụng. Loại mạng này thường được gọi là mạng *fish* vì hình dạng của nó (các router R1 và R2 tạo thành đuôi; R7 ở đầu).

.. _fig-fish:
.. figure:: figures/f04-22-9780123850591.png
   :width: 450px
   :align: center

   Một mạng yêu cầu định tuyến rõ ràng.

Giả sử nhà vận hành mạng trong :numref:`Figure %s <fig-fish>` đã xác định rằng bất kỳ lưu lượng nào đi từ R1 đến R7 nên theo đường R1-R3-R6-R7 và bất kỳ lưu lượng nào đi từ R2 đến R7 nên theo đường R2-R3-R4-R5-R7. Một lý do cho lựa chọn như vậy là để tận dụng tốt dung lượng có sẵn dọc theo hai đường riêng biệt từ R3 đến R7. Chúng ta có thể coi lưu lượng từ R1 đến R7 là tạo thành một forwarding equivalence class, và lưu lượng từ R2 đến R7 tạo thành một FEC thứ hai. Chuyển tiếp lưu lượng trong hai lớp này theo các đường khác nhau là khó với định tuyến IP thông thường, vì R3 thường không xem xét lưu lượng đến từ đâu khi đưa ra quyết định chuyển tiếp.

Vì MPLS sử dụng hoán đổi nhãn để chuyển tiếp gói, việc đạt được định tuyến mong muốn là khá dễ dàng nếu các router hỗ trợ MPLS. Nếu R1 và R2 gắn các nhãn khác nhau vào các gói trước khi gửi đến R3—do đó xác định chúng thuộc các FEC khác nhau—thì R3 có thể chuyển tiếp các gói từ R1 và R2 theo các đường khác nhau. Câu hỏi đặt ra là làm thế nào tất cả các router trong mạng đồng ý về nhãn nào sẽ dùng và cách chuyển tiếp các gói với nhãn cụ thể? Rõ ràng, chúng ta không thể sử dụng các thủ tục như đã mô tả ở phần trước để phân phối nhãn, vì các thủ tục đó thiết lập các nhãn khiến các gói đi theo các đường thông thường do định tuyến IP chọn, điều mà chúng ta đang cố tránh. Thay vào đó, cần một cơ chế mới. Hóa ra giao thức được sử dụng cho nhiệm vụ này là Resource Reservation Protocol (RSVP). Hiện tại chỉ cần biết rằng có thể gửi một thông điệp RSVP dọc theo một đường được chỉ định rõ ràng (ví dụ, R1-R3-R6-R7) và sử dụng nó để thiết lập các mục bảng chuyển tiếp nhãn dọc theo đường đó. Điều này rất giống với quá trình thiết lập một mạch ảo.

Một trong những ứng dụng của định tuyến rõ ràng là *traffic engineering* (kỹ thuật lưu lượng), tức là đảm bảo rằng có đủ tài nguyên trong mạng để đáp ứng các yêu cầu đặt ra. Kiểm soát chính xác các đường mà lưu lượng đi qua là một phần quan trọng của kỹ thuật lưu lượng. Định tuyến rõ ràng cũng có thể giúp mạng linh hoạt hơn khi gặp sự cố, sử dụng một khả năng gọi là *fast reroute* (chuyển hướng nhanh). Ví dụ, có thể tính trước một đường từ router A đến router B mà rõ ràng tránh một liên kết L nào đó. Nếu liên kết L bị lỗi, router A có thể gửi tất cả lưu lượng đến B theo đường đã tính trước. Sự kết hợp giữa việc tính trước đường dự phòng và định tuyến rõ ràng các gói theo đường đó có nghĩa là A không cần phải chờ các gói giao thức định tuyến lan truyền khắp mạng hoặc chờ các thuật toán định tuyến được thực thi bởi các nút khác nhau trong mạng. Trong một số trường hợp, điều này có thể giảm đáng kể thời gian chuyển hướng các gói quanh một điểm lỗi.

Một điểm cuối cùng cần lưu ý về định tuyến rõ ràng là các đường rõ ràng không nhất thiết phải được tính toán bởi nhà vận hành mạng như trong ví dụ trên. Các router có thể sử dụng nhiều thuật toán khác nhau để tự động tính toán các đường rõ ràng. Phổ biến nhất là *constrained shortest path first* (CSPF), là một thuật toán trạng thái liên kết, nhưng cũng xét đến các *ràng buộc* khác nhau. Ví dụ, nếu cần tìm một đường từ R1 đến R7 có thể mang tải 100 Mbps, ta có thể đặt ràng buộc là mỗi liên kết phải có ít nhất 100 Mbps dung lượng khả dụng. CSPF giải quyết loại bài toán này.

4.4.3 Mạng Riêng Ảo và Tunnel
-----------------------------

Một cách để xây dựng mạng riêng ảo (VPN) là sử dụng tunnel. Hóa ra MPLS có thể được coi là một cách để xây dựng tunnel, và điều này khiến nó phù hợp để xây dựng nhiều loại VPN khác nhau.

Dạng VPN MPLS đơn giản nhất để hiểu là VPN lớp 2. Trong loại VPN này, MPLS được sử dụng để tunnel dữ liệu lớp 2 (như frame Ethernet hoặc cell ATM) qua một mạng các router hỗ trợ MPLS. Một lý do để dùng tunnel là để cung cấp một loại dịch vụ mạng nào đó (như multicast) mà một số router trong mạng không hỗ trợ. Lý do tương tự cũng áp dụng ở đây: các router IP không phải là switch ATM, nên bạn không thể cung cấp dịch vụ mạch ảo ATM qua một mạng các router thông thường. Tuy nhiên, nếu bạn có một cặp router kết nối với nhau bằng một tunnel, chúng có thể gửi cell ATM qua tunnel và mô phỏng một mạch ATM. Thuật ngữ cho kỹ thuật này trong IETF là *pseudowire emulation*. :numref:`Figure %s <fig-atm-tunnel>` minh họa ý tưởng này.

.. _fig-atm-tunnel:
.. figure:: figures/f04-23-9780123850591.png
   :width: 600px
   :align: center

   Một mạch ATM được mô phỏng bằng một tunnel.

Chúng ta đã thấy cách xây dựng tunnel IP: Router ở đầu vào của tunnel bọc dữ liệu cần tunnel trong một header IP (header tunnel), đại diện cho địa chỉ của router ở đầu kia của tunnel và gửi dữ liệu như một gói IP thông thường. Router nhận sẽ nhận gói với địa chỉ của chính nó trong header, loại bỏ header tunnel, và tìm thấy dữ liệu đã được tunnel, sau đó xử lý dữ liệu đó. Cụ thể xử lý như thế nào phụ thuộc vào dữ liệu đó là gì. Ví dụ, nếu đó là một gói IP khác, nó sẽ được chuyển tiếp như một gói IP thông thường. Tuy nhiên, nó không nhất thiết phải là gói IP, miễn là router nhận biết cách xử lý các gói không phải IP. Chúng ta sẽ quay lại vấn đề xử lý dữ liệu không phải IP trong chốc lát.

Một tunnel MPLS không khác nhiều so với tunnel IP, ngoại trừ việc header tunnel là header MPLS thay vì header IP. Quay lại ví dụ đầu tiên, trong :numref:`Figure %s <fig-label-dist>`, chúng ta thấy router R1 gắn một nhãn (``15``) vào mỗi gói mà nó gửi đến tiền tố 18.1.1. Gói như vậy sẽ theo đường R1-R2-R3, với mỗi router trên đường chỉ kiểm tra nhãn MPLS. Như vậy, chúng ta thấy rằng không có yêu cầu nào bắt buộc R1 chỉ gửi các gói IP theo đường này—bất kỳ dữ liệu nào cũng có thể được bọc trong header MPLS và sẽ theo cùng đường, vì các router trung gian không bao giờ nhìn vào bên trong header MPLS. Ở khía cạnh này, header MPLS giống như header tunnel IP (chỉ khác là chỉ dài 4 byte thay vì 20 byte). Vấn đề duy nhất khi gửi lưu lượng không phải IP qua tunnel, dù là MPLS hay không, là xử lý lưu lượng không phải IP khi nó đến cuối tunnel. Giải pháp chung là mang một loại nhận diện phân kênh (demultiplexing identifier) nào đó trong payload tunnel để báo cho router ở cuối tunnel biết phải làm gì. Hóa ra nhãn MPLS là một lựa chọn hoàn hảo cho nhận diện này. Một ví dụ sẽ làm rõ điều này.

Giả sử chúng ta muốn tunnel các cell ATM từ một router đến một router khác qua một mạng các router hỗ trợ MPLS, như trong :numref:`Figure %s <fig-atm-tunnel>`. Hơn nữa, giả sử mục tiêu là mô phỏng một mạch ảo ATM; tức là, các cell đến đầu vào, hoặc đầu của tunnel, trên một cổng vào nhất định với một VCI nhất định và nên rời đầu ra của tunnel trên một cổng ra nhất định và có thể là VCI khác. Điều này có thể thực hiện bằng cách cấu hình các router đầu và cuối như sau:

-  Router đầu cần được cấu hình với cổng vào, VCI vào, nhãn phân kênh cho mạch mô phỏng này, và địa chỉ của router cuối tunnel.

-  Router cuối cần được cấu hình với cổng ra, VCI ra, và nhãn phân kênh.

Khi các router đã được cung cấp thông tin này, chúng ta có thể thấy cách một cell ATM sẽ được chuyển tiếp. :numref:`Figure %s <fig-pw-eg>` minh họa các bước.

1. Một cell ATM đến trên cổng vào chỉ định với giá trị VCI phù hợp (101 trong ví dụ này).

2. Router đầu gắn nhãn phân kênh xác định mạch mô phỏng.

3. Router đầu sau đó gắn một nhãn thứ hai, là nhãn tunnel sẽ đưa gói đến router cuối. Nhãn này được học bằng các cơ chế giống như đã mô tả ở phần khác trong chương này.

4. Các router giữa đầu và cuối chuyển tiếp gói chỉ dựa trên nhãn tunnel.

5. Router cuối loại bỏ nhãn tunnel, tìm nhãn phân kênh, và nhận diện mạch mô phỏng.

6. Router cuối thay đổi VCI ATM thành giá trị đúng (202 trong trường hợp này) và gửi ra cổng đúng.

.. _fig-pw-eg:
.. figure:: figures/f04-24-9780123850591.png
   :width: 600px
   :align: center

   Chuyển tiếp cell ATM dọc theo một tunnel.

Một điểm trong ví dụ này có thể gây ngạc nhiên là gói có hai nhãn được gắn vào. Đây là một trong những đặc điểm thú vị của MPLS—các nhãn có thể được xếp chồng lên một gói đến bất kỳ độ sâu nào. Điều này cung cấp một số khả năng mở rộng hữu ích. Trong ví dụ này, nó cho phép một tunnel duy nhất mang một số lượng lớn các mạch mô phỏng.

Các kỹ thuật mô tả ở đây có thể được áp dụng để mô phỏng nhiều dịch vụ lớp 2 khác, bao gồm Frame Relay và Ethernet. Đáng chú ý là các khả năng gần như giống hệt nhau có thể được cung cấp bằng các tunnel IP; lợi thế chính của MPLS ở đây là header tunnel ngắn hơn.

.. _fig-mpls-vpn:
.. figure:: figures/f04-25-9780123850591.png
   :width: 600px
   :align: center

   Ví dụ về VPN lớp 3. Khách hàng A và B mỗi người nhận được dịch vụ IP gần như riêng tư từ một nhà cung cấp duy nhất.

Trước khi MPLS được sử dụng để tunnel các dịch vụ lớp 2, nó cũng đã được sử dụng để hỗ trợ các VPN lớp 3. Chúng ta sẽ không đi sâu vào chi tiết của VPN lớp 3, vốn khá phức tạp, nhưng cần lưu ý rằng chúng là một trong những ứng dụng phổ biến nhất của MPLS ngày nay. VPN lớp 3 cũng sử dụng các ngăn xếp nhãn MPLS để tunnel các gói qua một mạng IP. Tuy nhiên, các gói được tunnel bản thân chúng là các gói IP—do đó gọi là VPN *lớp 3*. Trong một VPN lớp 3, một nhà cung cấp dịch vụ duy nhất vận hành một mạng các router hỗ trợ MPLS và cung cấp dịch vụ mạng IP “gần như riêng tư” cho bất kỳ số lượng khách hàng riêng biệt nào. Tức là, mỗi khách hàng của nhà cung cấp có một số site, và nhà cung cấp tạo ra ảo giác cho mỗi khách hàng rằng không có khách hàng nào khác trên mạng. Khách hàng nhìn thấy một mạng IP kết nối các site của riêng mình và không có site nào khác. Điều này có nghĩa là mỗi khách hàng được cách ly với tất cả các khách hàng khác cả về định tuyến lẫn địa chỉ. Khách hàng A không thể gửi gói trực tiếp đến khách hàng B, và *ngược lại*. Khách hàng A thậm chí có thể sử dụng các địa chỉ IP cũng đã được khách hàng B sử dụng. Ý tưởng cơ bản được minh họa trong :numref:`Figure %s <fig-mpls-vpn>`. Cũng như trong VPN lớp 2, MPLS được sử dụng để tunnel các gói từ một site đến site khác; tuy nhiên, việc cấu hình các tunnel được thực hiện tự động bằng một số kỹ thuật khá phức tạp sử dụng BGP, điều này nằm ngoài phạm vi của cuốn sách này.

Thực tế, khách hàng A thường *có thể* gửi dữ liệu cho khách hàng B theo một cách hạn chế nào đó. Nhiều khả năng, cả khách hàng A và khách hàng B đều có kết nối với Internet toàn cầu, do đó có thể khách hàng A gửi email, chẳng hạn, đến máy chủ thư bên trong mạng của khách hàng B. “Tính riêng tư” mà VPN cung cấp ngăn khách hàng A truy cập không giới hạn vào tất cả các máy và subnet bên trong mạng của khách hàng B.

Tóm lại, MPLS là một công cụ khá đa năng đã được áp dụng cho nhiều vấn đề mạng khác nhau. Nó kết hợp cơ chế chuyển tiếp hoán đổi nhãn vốn thường gắn với mạng mạch ảo với các giao thức định tuyến và điều khiển của mạng datagram IP để tạo ra một lớp mạng nằm đâu đó giữa hai thái cực truyền thống. Điều này mở rộng khả năng của mạng IP để cho phép, trong số những thứ khác, kiểm soát định tuyến chính xác hơn và hỗ trợ nhiều loại dịch vụ VPN.
