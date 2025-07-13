4.1 Internet toàn cầu
=====================

Tại thời điểm này, chúng ta đã thấy cách kết nối một tập hợp không đồng nhất các mạng để tạo thành một liên mạng (internetwork) và cách sử dụng hệ phân cấp đơn giản của địa chỉ IP để làm cho định tuyến trong một liên mạng có phần khả mở. Chúng tôi nói “có phần” khả mở bởi vì, mặc dù mỗi router không cần biết về tất cả các host kết nối vào Internet, nhưng trong mô hình đã mô tả cho đến nay, nó vẫn cần biết về tất cả các mạng kết nối vào Internet. Internet ngày nay có hàng trăm nghìn mạng được kết nối (hoặc nhiều hơn, tùy vào cách bạn đếm). Các giao thức định tuyến như chúng ta vừa thảo luận không thể mở rộng đến những con số như vậy. Phần này xem xét nhiều kỹ thuật giúp cải thiện đáng kể khả năng mở rộng và đã cho phép Internet phát triển như hiện nay.

.. _fig-inet-tree:
.. figure:: figures/f04-01-9780123850591.png
   :width: 600px
   :align: center

   Cấu trúc cây của Internet năm 1990.

Trước khi đi vào các kỹ thuật này, chúng ta cần có một hình dung tổng quát về Internet toàn cầu trông như thế nào. Nó không chỉ là một tập hợp ngẫu nhiên các mạng Ethernet được kết nối với nhau, mà thay vào đó nó có một hình dạng phản ánh thực tế rằng nó kết nối nhiều tổ chức khác nhau. :numref:`Hình %s <fig-inet-tree>` đưa ra một mô tả đơn giản về trạng thái của Internet năm 1990. Kể từ đó, cấu trúc liên kết của Internet đã trở nên phức tạp hơn nhiều so với hình này—chúng tôi sẽ trình bày một bức tranh chính xác hơn về Internet hiện tại ở phần sau—nhưng hình này là đủ cho bây giờ.

Một trong những đặc điểm nổi bật của cấu trúc liên kết này là nó bao gồm các site người dùng cuối (ví dụ, Đại học Stanford) kết nối vào các mạng nhà cung cấp dịch vụ (ví dụ, BARRNET là một mạng nhà cung cấp phục vụ các site ở khu vực Vịnh San Francisco). Năm 1990, nhiều nhà cung cấp phục vụ một vùng địa lý giới hạn và do đó được gọi là *mạng vùng* (regional networks). Các mạng vùng này lại được kết nối bởi một backbone toàn quốc. Năm 1990, backbone này được tài trợ bởi Quỹ Khoa học Quốc gia Hoa Kỳ (NSF) và do đó được gọi là *backbone NSFNET*.

NSFNET nhường chỗ cho Internet2, hiện vẫn vận hành một backbone thay mặt cho các tổ chức Nghiên cứu và Giáo dục ở Mỹ (có các mạng R&E tương tự ở các quốc gia khác), nhưng tất nhiên hầu hết mọi người lấy kết nối Internet từ các nhà cung cấp thương mại. Mặc dù chi tiết không được thể hiện trong hình, ngày nay các mạng nhà cung cấp lớn nhất (gọi là tier-1) thường được xây dựng từ hàng chục router cao cấp đặt tại các thành phố lớn (thường gọi là “thành phố NFL”) kết nối với nhau bằng các liên kết điểm-điểm (thường có băng thông 100 Gbps). Tương tự, mỗi site người dùng cuối thường không phải là một mạng đơn lẻ mà bao gồm nhiều mạng vật lý kết nối với nhau bằng switch và router.

Lưu ý rằng mỗi nhà cung cấp và người dùng cuối có khả năng là một thực thể độc lập về mặt quản trị. Điều này có một số hệ quả quan trọng đối với định tuyến. Ví dụ, rất có thể các nhà cung cấp khác nhau sẽ có ý tưởng khác nhau về giao thức định tuyến tốt nhất để sử dụng trong mạng của họ và về cách gán metric cho các liên kết trong mạng. Do sự độc lập này, mỗi mạng của nhà cung cấp thường là một *hệ tự trị* (autonomous system - AS). Chúng tôi sẽ định nghĩa thuật ngữ này chính xác hơn ở phần sau, nhưng hiện tại bạn có thể hiểu AS là một mạng được quản trị độc lập với các AS khác.

Thực tế là Internet có một cấu trúc có thể nhận biết được có thể được tận dụng khi chúng ta giải quyết vấn đề khả năng mở rộng. Thực tế, chúng ta cần đối mặt với hai vấn đề liên quan đến khả mở. Đầu tiên là khả mở của định tuyến. Chúng ta cần tìm cách giảm thiểu số lượng số mạng được mang theo trong các giao thức định tuyến và lưu trữ trong bảng định tuyến của các router. Thứ hai là sử dụng địa chỉ—tức là, đảm bảo không gian địa chỉ IP không bị tiêu tốn quá nhanh.

Xuyên suốt cuốn sách này, chúng ta thấy nguyên lý phân cấp được sử dụng lặp đi lặp lại để cải thiện khả năng mở rộng. Chúng ta đã thấy ở chương trước cách cấu trúc phân cấp của địa chỉ IP, đặc biệt với sự linh hoạt do CIDR (Classless Interdomain Routing) và subnetting cung cấp, có thể cải thiện khả năng mở rộng của định tuyến. Ở hai phần tiếp theo, chúng ta sẽ thấy các cách sử dụng phân cấp (và đối tác của nó, gom nhóm - aggregation) để cung cấp khả năng mở rộng lớn hơn, trước tiên trong một miền đơn và sau đó giữa các miền. Phần cuối cùng của mục này xem xét IP phiên bản 6, phát minh chủ yếu xuất phát từ các mối quan tâm về khả năng mở rộng.

4.1.1 Khu vực định tuyến (Routing Areas)
----------------------------------------

Là một ví dụ đầu tiên về việc sử dụng phân cấp để mở rộng hệ thống định tuyến, chúng ta sẽ xem xét cách các giao thức định tuyến trạng thái liên kết (như OSPF và IS-IS) có thể được sử dụng để phân chia một miền định tuyến thành các miền con gọi là *khu vực* (areas). (Thuật ngữ có thể khác nhau giữa các giao thức—ở đây chúng tôi dùng thuật ngữ của OSPF.) Bằng cách thêm một mức phân cấp này, chúng ta cho phép các miền đơn lẻ phát triển lớn hơn mà không làm quá tải các giao thức định tuyến hoặc phải dùng đến các giao thức định tuyến liên miền phức tạp hơn sẽ được mô tả sau.

Một khu vực là một tập các router được cấu hình quản trị để trao đổi thông tin trạng thái liên kết với nhau. Có một khu vực đặc biệt—khu vực backbone, còn gọi là khu vực 0. Một ví dụ về một miền định tuyến được chia thành các khu vực được thể hiện trong :numref:`Hình %s <fig-ospf-area>`. Các router R1, R2 và R3 là thành viên của khu vực backbone. Chúng cũng là thành viên của ít nhất một khu vực không phải backbone; R1 thực tế là thành viên của cả khu vực 1 và khu vực 2. Một router là thành viên của cả khu vực backbone và một khu vực không phải backbone được gọi là router biên khu vực (area border router - ABR). Lưu ý rằng đây là các router khác với các router ở rìa của một AS, vốn được gọi là router biên AS (AS border router) để rõ ràng.

.. _fig-ospf-area:
.. figure:: figures/f04-02-9780123850591.png
   :width: 500px
   :align: center

   Một miền được chia thành các khu vực.

Định tuyến trong một khu vực đơn lẻ diễn ra đúng như mô tả ở chương trước. Tất cả các router trong khu vực gửi quảng bá trạng thái liên kết cho nhau và do đó xây dựng một bản đồ đầy đủ, nhất quán về khu vực. Tuy nhiên, các quảng bá trạng thái liên kết của các router không phải router biên khu vực sẽ không rời khỏi khu vực nơi chúng được tạo ra. Điều này làm cho quá trình flooding và tính toán tuyến trở nên khả mở hơn nhiều. Ví dụ, router R4 ở khu vực 3 sẽ không bao giờ thấy quảng bá trạng thái liên kết từ router R8 ở khu vực 1. Do đó, nó sẽ không biết gì về cấu trúc liên kết chi tiết của các khu vực khác ngoài khu vực của nó.

Vậy làm thế nào để một router ở một khu vực xác định được next hop đúng cho một gói tin gửi đến một mạng ở khu vực khác? Câu trả lời sẽ rõ ràng nếu chúng ta hình dung đường đi của một gói tin phải đi từ một khu vực không phải backbone đến một khu vực khác như được chia thành ba phần. Đầu tiên, nó đi từ mạng nguồn đến khu vực backbone, sau đó băng qua backbone, rồi đi từ backbone đến mạng đích. Để làm được điều này, các router biên khu vực tổng hợp thông tin định tuyến mà chúng học được từ một khu vực và cung cấp nó trong các quảng bá của mình cho các khu vực khác. Ví dụ, R1 nhận các quảng bá trạng thái liên kết từ tất cả các router trong khu vực 1 và do đó có thể xác định chi phí đến bất kỳ mạng nào trong khu vực 1. Khi R1 gửi quảng bá trạng thái liên kết vào khu vực 0, nó quảng bá chi phí đến các mạng trong khu vực 1 như thể tất cả các mạng đó được kết nối trực tiếp với R1. Điều này cho phép tất cả các router khu vực 0 học được chi phí đến tất cả các mạng trong khu vực 1. Các router biên khu vực sau đó tổng hợp thông tin này và quảng bá vào các khu vực không phải backbone. Như vậy, tất cả các router đều học được cách đến tất cả các mạng trong miền.

Lưu ý rằng, trong trường hợp khu vực 2, có hai ABR và các router trong khu vực 2 sẽ phải chọn xem sử dụng router nào để đến backbone. Điều này khá dễ, vì cả R1 và R2 đều sẽ quảng bá chi phí đến các mạng khác nhau, nên sẽ rõ ràng router nào là lựa chọn tốt hơn khi các router trong khu vực 2 chạy thuật toán đường đi ngắn nhất. Ví dụ, khá rõ ràng rằng R1 sẽ là lựa chọn tốt hơn R2 cho các đích ở khu vực 1.

Khi chia một miền thành các khu vực, quản trị viên mạng phải đánh đổi giữa khả năng mở rộng và tối ưu hóa định tuyến. Việc sử dụng các khu vực buộc tất cả các gói tin đi từ khu vực này sang khu vực khác phải đi qua khu vực backbone, ngay cả khi có thể có một đường đi ngắn hơn. Ví dụ, ngay cả khi R4 và R5 được kết nối trực tiếp, các gói tin sẽ không đi giữa chúng vì chúng ở các khu vực không phải backbone khác nhau. Hóa ra, nhu cầu về khả năng mở rộng thường quan trọng hơn nhu cầu sử dụng đường đi ngắn nhất tuyệt đối.

.. _key-tradeoffs:
.. admonition:: Ý chính

   Điều này minh họa một nguyên tắc quan trọng trong thiết kế mạng. Thường xuyên có sự đánh đổi giữa khả năng mở rộng và một dạng tối ưu nào đó. Khi phân cấp được đưa vào, thông tin sẽ bị ẩn khỏi một số nút trong mạng, làm giảm khả năng của chúng trong việc ra quyết định hoàn hảo. Tuy nhiên, việc ẩn thông tin là thiết yếu để mở rộng giải pháp, vì nó giúp tất cả các nút không phải biết thông tin toàn cục. Trong các mạng lớn, khả năng mở rộng luôn là mục tiêu thiết kế cấp bách hơn so với việc chọn tuyến tối ưu. :ref:`[Tiếp theo] <key-scaling>`

Cuối cùng, chúng tôi lưu ý rằng có một mẹo giúp quản trị viên mạng linh hoạt hơn trong việc quyết định router nào thuộc khu vực 0. Mẹo này sử dụng ý tưởng về một *liên kết ảo* (virtual link) giữa các router. Một liên kết ảo được thiết lập bằng cách cấu hình một router không kết nối trực tiếp với khu vực 0 để trao đổi thông tin định tuyến backbone với một router có kết nối. Ví dụ, có thể cấu hình một liên kết ảo từ R8 đến R1, biến R8 thành một phần của backbone. R8 giờ sẽ tham gia flooding quảng bá trạng thái liên kết với các router khác trong khu vực 0. Chi phí của liên kết ảo từ R8 đến R1 được xác định bởi việc trao đổi thông tin định tuyến diễn ra trong khu vực 1. Kỹ thuật này có thể giúp cải thiện tối ưu hóa định tuyến.

4.1.2 Định tuyến liên miền (BGP)
--------------------------------

Ở đầu chương này, chúng tôi đã giới thiệu khái niệm rằng Internet được tổ chức thành các hệ tự trị (AS), mỗi hệ dưới sự kiểm soát của một thực thể quản trị duy nhất. Một mạng nội bộ phức tạp của một công ty có thể là một AS, cũng như mạng quốc gia của một nhà cung cấp dịch vụ Internet (ISP). :numref:`Hình %s <fig-autonomous>` cho thấy một mạng đơn giản với hai hệ tự trị.

.. _fig-autonomous:
.. figure:: figures/f04-03-9780123850591.png
   :width: 400px
   :align: center

   Một mạng với hai hệ tự trị.

Ý tưởng cơ bản đằng sau các hệ tự trị là cung cấp một cách bổ sung để gom nhóm thông tin định tuyến theo phân cấp trong một liên mạng lớn, từ đó cải thiện khả năng mở rộng. Bây giờ chúng ta chia bài toán định tuyến thành hai phần: định tuyến trong một hệ tự trị đơn lẻ và định tuyến giữa các hệ tự trị. Vì một tên gọi khác của hệ tự trị trên Internet là *miền định tuyến* (routing domain), chúng tôi gọi hai phần của bài toán định tuyến là định tuyến liên miền (interdomain) và định tuyến nội miền (intradomain). Ngoài việc cải thiện khả năng mở rộng, mô hình AS còn tách biệt định tuyến nội miền diễn ra trong một AS với định tuyến nội miền diễn ra ở AS khác. Do đó, mỗi AS có thể chạy bất kỳ giao thức định tuyến nội miền nào nó muốn. Nó thậm chí có thể dùng các tuyến tĩnh hoặc nhiều giao thức khác nhau nếu muốn. Bài toán định tuyến liên miền khi đó là làm sao để các AS khác nhau chia sẻ thông tin về khả năng tiếp cận—mô tả tập các địa chỉ IP có thể đến được qua một AS nhất định—với nhau.

Thách thức trong định tuyến liên miền
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Có lẽ thách thức quan trọng nhất của định tuyến liên miền ngày nay là nhu cầu để mỗi AS xác định *chính sách* định tuyến của riêng mình. Một ví dụ đơn giản về chính sách định tuyến được hiện thực tại một AS cụ thể có thể như sau: “Bất cứ khi nào có thể, tôi thích gửi lưu lượng qua AS X hơn là qua AS Y, nhưng tôi sẽ dùng AS Y nếu đó là đường duy nhất, và tôi không bao giờ muốn chuyển lưu lượng từ AS X sang AS Y hoặc ngược lại.” Một chính sách như vậy là điển hình khi tôi đã trả tiền cho cả AS X và AS Y để kết nối AS của mình với phần còn lại của Internet, và AS X là nhà cung cấp ưu tiên, còn AS Y là dự phòng. Vì tôi coi cả AS X và AS Y là nhà cung cấp (và giả sử tôi đã trả tiền cho họ để đóng vai trò này), tôi không mong muốn giúp họ bằng cách chuyển lưu lượng giữa họ qua mạng của mình (điều này gọi là lưu lượng *transit*). Càng kết nối với nhiều hệ tự trị, tôi càng có thể có nhiều chính sách phức tạp hơn, đặc biệt khi bạn xét đến các nhà cung cấp backbone, những người có thể kết nối với hàng chục nhà cung cấp khác và hàng trăm khách hàng, với các thỏa thuận kinh tế khác nhau (ảnh hưởng đến chính sách định tuyến) với từng bên.

Một mục tiêu thiết kế then chốt của định tuyến liên miền là các chính sách như ví dụ trên, và còn phức tạp hơn nhiều, phải được hệ thống định tuyến liên miền hỗ trợ. Để làm vấn đề khó hơn, tôi cần có khả năng hiện thực chính sách như vậy mà không cần sự trợ giúp từ các hệ tự trị khác, và trong điều kiện có thể có cấu hình sai hoặc hành vi độc hại từ các hệ tự trị khác. Hơn nữa, thường có mong muốn giữ các chính sách này *riêng tư*, vì các thực thể vận hành hệ tự trị—chủ yếu là các ISP—thường cạnh tranh với nhau và không muốn các thỏa thuận kinh tế của mình bị công khai.

Trong lịch sử Internet đã có hai giao thức định tuyến liên miền lớn. Đầu tiên là Exterior Gateway Protocol (EGP), vốn có nhiều hạn chế, nghiêm trọng nhất là nó giới hạn cấu trúc liên kết của Internet khá đáng kể. EGP được thiết kế khi Internet có cấu trúc cây, như minh họa ở :numref:`Hình %s <fig-inet-tree>`, và không cho phép cấu trúc liên kết trở nên tổng quát hơn. Lưu ý rằng trong cấu trúc cây đơn giản này chỉ có một backbone, và các hệ tự trị chỉ kết nối như cha-con chứ không phải ngang hàng.

Thay thế cho EGP là Border Gateway Protocol (BGP), đã trải qua bốn phiên bản (BGP-4). BGP thường được coi là một trong những phần phức tạp nhất của Internet. Chúng tôi sẽ trình bày một số điểm chính ở đây.

Không giống như EGP, BGP hầu như không giả định gì về cách các hệ tự trị được kết nối với nhau—chúng tạo thành một đồ thị bất kỳ. Mô hình này rõ ràng đủ tổng quát để đáp ứng các liên mạng không có cấu trúc cây, như hình ảnh đơn giản hóa về Internet đa nhà cung cấp trong :numref:`Hình %s <fig-inet-1995>`. (Thực tế là Internet vẫn có một số cấu trúc, như sẽ thấy bên dưới, nhưng không đơn giản như cây, và BGP không giả định gì về cấu trúc đó.)

.. _fig-inet-1995:
.. figure:: figures/f04-04-9780123850591.png
   :width: 600px
   :align: center

   Một Internet đa nhà cung cấp đơn giản.

Không giống như Internet cấu trúc cây đơn giản trong :numref:`Hình %s <fig-inet-tree>`, hoặc thậm chí hình ảnh khá đơn giản trong :numref:`Hình %s <fig-inet-1995>`, Internet ngày nay bao gồm một tập hợp các mạng kết nối phong phú, chủ yếu do các công ty tư nhân (ISP) vận hành thay vì chính phủ. Nhiều nhà cung cấp dịch vụ Internet (ISP) tồn tại chủ yếu để cung cấp dịch vụ cho “người tiêu dùng” (tức là cá nhân có máy tính tại nhà), trong khi những nhà cung cấp khác cung cấp dịch vụ giống như backbone cũ, kết nối các nhà cung cấp khác và đôi khi cả các tập đoàn lớn. Thường thì nhiều nhà cung cấp sắp xếp để kết nối với nhau tại một *điểm peering* duy nhất.

Để hiểu rõ hơn cách chúng ta có thể quản lý định tuyến giữa tập hợp phức tạp các hệ tự trị này, chúng ta có thể bắt đầu bằng cách định nghĩa một số thuật ngữ. Chúng ta định nghĩa *lưu lượng cục bộ* (local traffic) là lưu lượng bắt nguồn hoặc kết thúc tại các nút trong một AS, và *lưu lượng transit* (transit traffic) là lưu lượng đi qua một AS. Chúng ta có thể phân loại các hệ tự trị thành ba loại lớn:

-  Stub AS—một AS chỉ chuyển lưu lượng cục bộ. Công ty nhỏ trong :numref:`Hình %s <fig-inet-1995>` là ví dụ về stub AS.

-  Multihomed Stub AS—một AS có kết nối với nhiều AS khác nhưng từ chối chuyển lưu lượng transit, như công ty lớn ở đầu :numref:`Hình %s <fig-inet-1995>`.

-  Transit AS—một AS có kết nối với nhiều AS khác và được thiết kế để chuyển cả lưu lượng transit và cục bộ, như các nhà cung cấp backbone trong :numref:`Hình %s <fig-inet-1995>`.

Trong khi phần thảo luận về định tuyến ở chương trước tập trung vào việc tìm đường đi tối ưu dựa trên việc tối thiểu hóa một loại metric liên kết nào đó, mục tiêu của định tuyến liên miền phức tạp hơn nhiều. Đầu tiên, cần tìm *một* đường đi đến đích mong muốn mà không có vòng lặp. Thứ hai, các đường đi phải tuân thủ các chính sách của các hệ tự trị dọc theo đường đi—và như đã thấy, các chính sách này có thể gần như tùy ý phức tạp. Do đó, trong khi nội miền tập trung vào bài toán tối ưu hóa scalar rõ ràng, liên miền tập trung vào việc tìm một đường đi không lặp, *tuân thủ chính sách*—một bài toán tối ưu hóa phức tạp hơn nhiều.

Có thêm các yếu tố khác làm cho định tuyến liên miền trở nên khó khăn. Đầu tiên đơn giản là vấn đề quy mô. Một router backbone Internet phải có khả năng chuyển tiếp bất kỳ gói tin nào đến bất kỳ đâu trên Internet. Điều đó nghĩa là phải có một bảng định tuyến cung cấp khớp cho bất kỳ địa chỉ IP hợp lệ nào. Dù CIDR đã giúp kiểm soát số lượng prefix riêng biệt được mang trong định tuyến backbone Internet, vẫn còn rất nhiều thông tin định tuyến phải truyền đi—khoảng 700.000 prefix vào giữa năm 2018.

Một thách thức nữa trong định tuyến liên miền xuất phát từ tính tự trị của các miền. Lưu ý rằng mỗi miền có thể chạy giao thức định tuyến nội bộ riêng và dùng bất kỳ cách nào nó muốn để gán metric cho các đường đi. Điều này nghĩa là không thể tính toán chi phí đường đi có ý nghĩa cho một đường đi băng qua nhiều hệ tự trị. Một chi phí 1000 qua một nhà cung cấp có thể là đường rất tốt, nhưng lại là đường rất tệ với nhà cung cấp khác. Do đó, định tuyến liên miền chỉ quảng bá *khả năng tiếp cận* (reachability). Khái niệm khả năng tiếp cận về cơ bản là một tuyên bố rằng “bạn có thể đến mạng này qua AS này.” Điều này nghĩa là việc định tuyến liên miền chọn đường đi tối ưu về bản chất là không thể.

Tính tự trị của liên miền cũng đặt ra vấn đề về tin cậy. Nhà cung cấp A có thể không muốn tin vào một số quảng bá từ nhà cung cấp B vì sợ rằng B sẽ quảng bá thông tin định tuyến sai. Ví dụ, tin vào B khi anh ta quảng bá một đường đi tuyệt vời đến bất kỳ đâu trên Internet có thể là một lựa chọn thảm họa nếu B cấu hình router sai hoặc không đủ năng lực để chuyển lưu lượng.

Vấn đề tin cậy cũng liên quan đến nhu cầu hỗ trợ các chính sách phức tạp như đã nói ở trên. Ví dụ, tôi có thể chỉ muốn tin một nhà cung cấp khi anh ta quảng bá khả năng tiếp cận đến một số prefix nhất định, và do đó tôi sẽ có chính sách rằng, “Chỉ dùng AS X để đến các prefix :math:`p` và :math:`q`, nếu và chỉ nếu AS X quảng bá khả năng tiếp cận đến các prefix đó.”

Cơ bản về BGP
~~~~~~~~~~~~~

Mỗi AS có một hoặc nhiều *router biên* (border router) qua đó các gói tin đi vào và ra khỏi AS. Trong ví dụ đơn giản ở :numref:`Hình %s <fig-autonomous>`, các router R2 và R4 là router biên. (Qua các năm, router đôi khi còn được gọi là *gateway*, do đó có tên các giao thức BGP và EGP). Một router biên đơn giản là một router IP chịu trách nhiệm chuyển tiếp các gói tin giữa các hệ tự trị.

Mỗi AS tham gia BGP cũng phải có ít nhất một *BGP speaker*, một router “nói” BGP với các BGP speaker ở các hệ tự trị khác. Thông thường, router biên cũng là BGP speaker, nhưng không nhất thiết phải như vậy.

BGP không thuộc một trong hai lớp giao thức định tuyến chính, vector khoảng cách hay trạng thái liên kết. Không giống các giao thức này, BGP quảng bá *đường đi đầy đủ* dưới dạng một danh sách liệt kê các hệ tự trị để đến một mạng cụ thể. Vì lý do này, nó đôi khi được gọi là giao thức *vector đường đi* (path-vector). Việc quảng bá đường đi đầy đủ là cần thiết để cho phép các quyết định chính sách như đã mô tả ở trên được thực hiện theo ý muốn của từng AS. Nó cũng cho phép dễ dàng phát hiện các vòng lặp định tuyến.

.. _fig-bgpeg:
.. figure:: figures/f04-05-9780123850591.png
   :width: 500px
   :align: center

   Ví dụ về một mạng chạy BGP.

Để thấy cách hoạt động này, xét ví dụ mạng rất đơn giản trong :numref:`Hình %s <fig-bgpeg>`. Giả sử các nhà cung cấp là các mạng transit, còn các mạng khách hàng là stub. Một BGP speaker cho AS của nhà cung cấp A (AS 2) sẽ có thể quảng bá thông tin khả năng tiếp cận cho từng số mạng được gán cho khách hàng P và Q. Như vậy, nó sẽ nói, về cơ bản, “Các mạng 128.96, 192.4.153, 192.4.32, và 192.4.3 có thể đến trực tiếp từ AS 2.” Mạng backbone, khi nhận được quảng bá này, có thể quảng bá, “Các mạng 128.96, 192.4.153, 192.4.32, và 192.4.3 có thể đến qua đường đi (AS 1, AS 2).” Tương tự, nó có thể quảng bá, “Các mạng 192.12.69, 192.4.54, và 192.4.23 có thể đến qua đường đi (AS 1, AS 3).”

.. _fig-aspath:
.. figure:: figures/f04-06-9780123850591.png
   :width: 500px
   :align: center

   Ví dụ về vòng lặp giữa các hệ tự trị.

Một nhiệm vụ quan trọng của BGP là ngăn chặn việc thiết lập các đường đi vòng lặp. Ví dụ, xét mạng minh họa trong :numref:`Hình %s <fig-aspath>`. Nó khác với :numref:`Hình %s <fig-bgpeg>` chỉ ở chỗ thêm một liên kết giữa AS 2 và AS 3, nhưng hiệu quả là đồ thị các hệ tự trị giờ có một vòng lặp. Giả sử AS 1 biết rằng nó có thể đến mạng 128.96 qua AS 2, nên nó quảng bá điều này cho AS 3, rồi AS 3 lại quảng bá ngược lại cho AS 2. Nếu không có cơ chế ngăn vòng lặp, AS 2 có thể quyết định rằng AS 3 là đường đi ưu tiên cho các gói đến 128.96. Nếu AS 2 bắt đầu gửi các gói địa chỉ đến 128.96 cho AS 3, AS 3 sẽ gửi chúng cho AS 1; AS 1 lại gửi về AS 2; và chúng sẽ lặp mãi mãi. Điều này được ngăn chặn bằng cách mang theo toàn bộ đường đi AS trong các thông điệp định tuyến. Trong trường hợp này, quảng bá đường đi đến 128.96 nhận được bởi AS 2 từ AS 3 sẽ chứa một đường đi AS là (AS 3, AS 1, AS 2, AS 4). AS 2 thấy mình trong đường đi này, và do đó kết luận đây không phải là đường đi hữu ích cho nó.

Để kỹ thuật ngăn vòng lặp này hoạt động, các số AS mang trong BGP rõ ràng phải là duy nhất. Ví dụ, AS 2 chỉ có thể nhận ra mình trong đường đi AS ở ví dụ trên nếu không có AS nào khác nhận dạng giống như vậy. Số AS hiện nay dài 32 bit, và được gán bởi một cơ quan trung ương để đảm bảo tính duy nhất.

Một AS chỉ quảng bá các tuyến mà nó cho là đủ tốt cho chính nó. Tức là, nếu một BGP speaker có nhiều lựa chọn tuyến khác nhau đến một đích, nó sẽ chọn tuyến tốt nhất theo chính sách cục bộ của mình, và đó sẽ là tuyến nó quảng bá. Hơn nữa, một BGP speaker không có nghĩa vụ phải quảng bá bất kỳ tuyến nào đến một đích, ngay cả khi nó biết cách đến đó. Đây là cách một AS có thể hiện thực chính sách không cung cấp transit—bằng cách từ chối quảng bá các tuyến đến các prefix không nằm trong AS đó, dù nó biết cách đến.

Vì các liên kết có thể hỏng và chính sách có thể thay đổi, các BGP speaker cần có khả năng hủy các đường đi đã quảng bá trước đó. Điều này được thực hiện bằng một dạng quảng bá âm gọi là *withdrawn route* (tuyến bị rút lại). Cả thông tin khả năng tiếp cận dương và âm đều được mang trong một thông điệp cập nhật BGP, định dạng thể hiện trong :numref:`Hình %s <fig-bgpup>`. (Lưu ý rằng các trường trong hình này là bội số của 16 bit, không giống các định dạng gói khác trong chương này.)

.. _fig-bgpup:
.. figure:: figures/f04-07-9780123850591.png
   :width: 200px
   :align: center

   Định dạng gói cập nhật BGP-4.

Không giống các giao thức định tuyến mô tả ở chương trước, BGP được định nghĩa để chạy trên TCP, giao thức truyền tải tin cậy. Vì các BGP speaker có thể tin tưởng TCP là tin cậy, điều này nghĩa là bất kỳ thông tin nào đã được gửi từ một speaker đến speaker khác không cần phải gửi lại. Do đó, miễn là không có gì thay đổi, một BGP speaker chỉ cần gửi một thông điệp *keepalive* định kỳ, về cơ bản nói rằng, “Tôi vẫn ở đây và không có gì thay đổi.” Nếu router đó bị crash hoặc mất kết nối với peer, nó sẽ ngừng gửi keepalive, và các router khác đã học các tuyến từ nó sẽ giả định các tuyến đó không còn hợp lệ.

Các mối quan hệ và chính sách AS phổ biến
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Dù đã nói rằng các chính sách có thể tùy ý phức tạp, thực tế có một số chính sách phổ biến, phản ánh các mối quan hệ phổ biến giữa các hệ tự trị. Các mối quan hệ phổ biến nhất được minh họa trong :numref:`Hình %s <fig-as-rels>`. Ba mối quan hệ phổ biến và các chính sách đi kèm như sau:

.. _fig-as-rels:
.. figure:: figures/f04-08-9780123850591.png
   :width: 500px
   :align: center

   Các mối quan hệ AS phổ biến.

-  *Provider-Customer—* Nhà cung cấp kinh doanh việc kết nối khách hàng của mình với phần còn lại của Internet. Một khách hàng có thể là một công ty, hoặc một ISP nhỏ hơn (có thể có khách hàng riêng). Chính sách phổ biến là quảng bá tất cả các tuyến tôi biết cho khách hàng, và quảng bá các tuyến tôi học được từ khách hàng cho tất cả mọi người.

-  *Customer-Provider—* Ở chiều ngược lại, khách hàng muốn nhận lưu lượng gửi đến mình (và khách hàng của mình, nếu có) qua nhà cung cấp, và muốn gửi lưu lượng đến phần còn lại của Internet qua nhà cung cấp. Chính sách phổ biến là quảng bá các prefix của mình và các tuyến học từ khách hàng cho nhà cung cấp, quảng bá các tuyến học từ nhà cung cấp cho khách hàng, nhưng không quảng bá các tuyến học từ nhà cung cấp này cho nhà cung cấp khác. Phần cuối này đảm bảo khách hàng không trở thành trung gian chuyển lưu lượng giữa các nhà cung cấp, điều không có lợi nếu họ phải trả tiền cho các nhà cung cấp để chuyển lưu lượng cho mình.

-  *Peer—* Lựa chọn thứ ba là peering đối xứng giữa các hệ tự trị. Hai nhà cung cấp coi nhau là ngang hàng thường peer để có thể truy cập khách hàng của nhau mà không phải trả tiền cho nhà cung cấp khác. Chính sách điển hình là quảng bá các tuyến học từ khách hàng cho peer, quảng bá các tuyến học từ peer cho khách hàng, nhưng không quảng bá các tuyến từ peer cho bất kỳ nhà cung cấp nào hoặc ngược lại.

Một điều cần lưu ý về hình này là cách nó đưa lại một số cấu trúc cho Internet tưởng như không có cấu trúc. Ở đáy của hệ phân cấp là các mạng stub là khách hàng của một hoặc nhiều nhà cung cấp, và khi lên cao hơn ta thấy các nhà cung cấp có khách hàng là các nhà cung cấp khác. Ở đỉnh, ta có các nhà cung cấp có khách hàng và peer nhưng không là khách hàng của ai. Các nhà cung cấp này được gọi là *nhà cung cấp Tier-1*.

.. _key-scaling:
.. admonition:: Ý chính

   Quay lại câu hỏi thực sự: Tất cả điều này giúp chúng ta xây dựng mạng khả mở như thế nào? Đầu tiên, số lượng nút tham gia BGP vào cỡ số lượng hệ tự trị, nhỏ hơn nhiều so với số lượng mạng. Thứ hai, việc tìm một tuyến liên miền tốt chỉ là vấn đề tìm đường đến router biên phù hợp, mà mỗi AS chỉ có vài router như vậy. Như vậy, chúng ta đã chia nhỏ bài toán định tuyến thành các phần có thể quản lý, một lần nữa sử dụng một mức phân cấp mới để tăng khả năng mở rộng. Độ phức tạp của định tuyến liên miền giờ vào cỡ số lượng hệ tự trị, còn độ phức tạp của định tuyến nội miền vào cỡ số lượng mạng trong một AS. :ref:`[Tiếp theo] <key-e2e>`

Tích hợp định tuyến liên miền và nội miền
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Trong khi phần thảo luận trước minh họa cách một BGP speaker học thông tin định tuyến liên miền, câu hỏi vẫn còn là làm thế nào các router khác trong một miền nhận được thông tin này. Có một số cách để giải quyết vấn đề này.

Hãy bắt đầu với một tình huống rất đơn giản, cũng rất phổ biến. Trong trường hợp một stub AS chỉ kết nối với các hệ tự trị khác tại một điểm duy nhất, router biên rõ ràng là lựa chọn duy nhất cho tất cả các tuyến ra ngoài AS. Router này có thể đưa vào giao thức định tuyến nội miền một *tuyến mặc định* (default route). Về cơ bản, đây là một tuyên bố rằng bất kỳ mạng nào chưa được quảng bá rõ ràng trong giao thức nội miền đều có thể đến qua router biên. Nhớ lại từ phần thảo luận về chuyển tiếp IP ở chương trước rằng mục mặc định trong bảng chuyển tiếp được xét sau tất cả các mục cụ thể hơn, và nó khớp với bất kỳ địa chỉ nào không khớp với mục cụ thể nào.

Bước tiếp theo về độ phức tạp là để các router biên đưa vào các tuyến cụ thể mà chúng học được từ bên ngoài AS. Xét ví dụ, router biên của một AS nhà cung cấp kết nối với một AS khách hàng. Router đó có thể học rằng prefix mạng 192.4.54/24 nằm trong AS khách hàng, hoặc qua BGP hoặc do thông tin được cấu hình vào router biên. Nó có thể đưa vào giao thức định tuyến nội miền của AS nhà cung cấp một tuyến đến prefix đó. Đây sẽ là một quảng bá kiểu, “Tôi có một liên kết đến 192.4.54/24 với chi phí X.” Điều này khiến các router khác trong AS nhà cung cấp biết rằng router biên này là nơi gửi các gói đến prefix đó.

Mức độ phức tạp cuối cùng xuất hiện ở các mạng backbone, nơi học quá nhiều thông tin định tuyến từ BGP đến mức không thể đưa hết vào giao thức nội miền. Ví dụ, nếu một router biên muốn đưa vào 10.000 prefix mà nó học được từ một AS khác, nó sẽ phải gửi các gói trạng thái liên kết rất lớn cho các router khác trong AS, và các phép tính đường đi ngắn nhất của họ sẽ trở nên rất phức tạp. Vì lý do này, các router trong một mạng backbone sử dụng một biến thể của BGP gọi là *BGP nội bộ* (interior BGP - iBGP) để phân phối hiệu quả thông tin học được bởi các BGP speaker ở rìa AS cho tất cả các router khác trong AS. (Biến thể BGP khác, đã thảo luận ở trên, chạy giữa các hệ tự trị và gọi là *BGP ngoại bộ* - exterior BGP, hay eBGP). iBGP cho phép bất kỳ router nào trong AS học được router biên tốt nhất để gửi gói đến bất kỳ địa chỉ nào. Đồng thời, mỗi router trong AS theo dõi cách đến từng router biên bằng một giao thức nội miền thông thường mà không cần đưa thêm thông tin vào. Bằng cách kết hợp hai tập thông tin này, mỗi router trong AS có thể xác định next hop phù hợp cho tất cả các prefix.

.. _fig-ibgp:
.. figure:: figures/f04-09-9780123850591.png
   :width: 500px
   :align: center

   Ví dụ về định tuyến liên miền và nội miền. Tất cả các router chạy iBGP và một giao thức định tuyến nội miền. Các router biên A, D, và E cũng chạy eBGP với các hệ tự trị khác.

Để thấy tất cả điều này hoạt động như thế nào, xét ví dụ mạng đơn giản, đại diện cho một AS, trong :numref:`Hình %s <fig-ibgp>`. Ba router biên, A, D, và E, nói eBGP với các hệ tự trị khác và học cách đến các prefix khác nhau. Ba router biên này giao tiếp với nhau và với các router nội bộ B và C bằng cách xây dựng một mesh các phiên iBGP giữa tất cả các router trong AS. Bây giờ hãy tập trung vào cách router B xây dựng cái nhìn đầy đủ về cách chuyển tiếp gói đến bất kỳ prefix nào. Nhìn vào góc trên bên trái của :numref:`Hình %s <fig-ibgptab>`, thể hiện thông tin mà router B học được từ các phiên iBGP. Nó học được rằng một số prefix tốt nhất nên đến qua router A, một số qua D, và một số qua E. Đồng thời, tất cả các router trong AS cũng chạy một giao thức định tuyến nội miền nào đó như RIP hoặc OSPF. (Thuật ngữ chung cho các giao thức nội miền là interior gateway protocol, hay IGP.) Từ giao thức hoàn toàn riêng biệt này, B học cách đến các nút *bên trong* miền, như thể hiện ở bảng trên bên phải. Ví dụ, để đến router E, B cần gửi gói về phía router C. Cuối cùng, ở bảng dưới cùng, B tổng hợp toàn bộ bức tranh, kết hợp thông tin về các prefix ngoài học từ iBGP với thông tin về các tuyến nội bộ đến các router biên học từ IGP. Như vậy, nếu một prefix như 18.0/16 có thể đến qua router biên E, và đường đi nội bộ tốt nhất đến E là qua C, thì bất kỳ gói nào đến 18.0/16 nên được chuyển tiếp về phía C. Bằng cách này, bất kỳ router nào trong AS cũng có thể xây dựng bảng định tuyến đầy đủ cho bất kỳ prefix nào có thể đến qua một router biên nào đó của AS.

.. _fig-ibgptab:
.. figure:: figures/f04-10-9780123850591.png
   :width: 500px
   :align: center

   Bảng định tuyến BGP, bảng định tuyến IGP, và bảng kết hợp tại router B.
