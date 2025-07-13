4.3 Multicast
=============

Các mạng đa truy nhập như Ethernet hiện thực multicast bằng phần cứng.
Tuy nhiên, có những ứng dụng cần khả năng multicast rộng hơn, hiệu quả ở quy mô Internet.
Ví dụ, khi một đài phát thanh được phát sóng qua Internet, cùng một dữ liệu phải được gửi đến tất cả các máy chủ nơi người dùng đã bật kênh đó.
Trong ví dụ này, giao tiếp là một-nhiều. Các ví dụ khác về ứng dụng một-nhiều bao gồm truyền cùng một tin tức, giá cổ phiếu hiện tại, cập nhật phần mềm,
hoặc các kênh truyền hình đến nhiều máy chủ. Ví dụ sau thường được gọi là IPTV.

Cũng có những ứng dụng mà giao tiếp là nhiều-nhiều, như hội nghị truyền hình đa phương tiện, trò chơi trực tuyến nhiều người chơi,
hoặc mô phỏng phân tán. Trong các trường hợp này, các thành viên của một nhóm nhận dữ liệu từ nhiều nguồn gửi, thường là từ nhau.
Từ bất kỳ nguồn gửi cụ thể nào, tất cả họ đều nhận cùng một dữ liệu.

Giao tiếp IP thông thường, trong đó mỗi gói phải được định địa chỉ và gửi đến một máy chủ duy nhất, không phù hợp với các ứng dụng như vậy.
Nếu một ứng dụng có dữ liệu cần gửi đến một nhóm, nó sẽ phải gửi một gói riêng biệt với dữ liệu giống hệt nhau đến từng thành viên của nhóm.
Sự dư thừa này tiêu tốn nhiều băng thông hơn mức cần thiết. Hơn nữa, lưu lượng dư thừa này không được phân phối đều mà tập trung quanh máy gửi,
và có thể dễ dàng vượt quá khả năng của máy gửi và các mạng, router lân cận.

Để hỗ trợ tốt hơn cho giao tiếp nhiều-nhiều và một-nhiều, IP cung cấp multicast ở cấp IP tương tự như multicast ở cấp liên kết
được cung cấp bởi các mạng đa truy nhập như Ethernet. Bây giờ khi chúng ta giới thiệu khái niệm multicast cho IP,
chúng ta cũng cần một thuật ngữ cho dịch vụ truyền thống một-một của IP đã được mô tả đến nay: Dịch vụ đó được gọi là *unicast*.

Mô hình multicast IP cơ bản là mô hình nhiều-nhiều dựa trên các *nhóm* multicast, trong đó mỗi nhóm có một *địa chỉ multicast* IP riêng.
Các máy chủ là thành viên của một nhóm sẽ nhận bản sao của bất kỳ gói nào gửi đến địa chỉ multicast của nhóm đó.
Một máy chủ có thể thuộc nhiều nhóm, và nó có thể tham gia hoặc rời nhóm tự do bằng cách thông báo cho router cục bộ của nó
bằng một giao thức mà chúng ta sẽ thảo luận ngay sau đây. Do đó, trong khi chúng ta nghĩ về địa chỉ unicast là gắn với một nút hoặc một giao diện,
địa chỉ multicast lại gắn với một nhóm trừu tượng, thành viên của nhóm này thay đổi động theo thời gian.
Hơn nữa, mô hình dịch vụ multicast IP ban đầu cho phép *bất kỳ* máy chủ nào gửi lưu lượng multicast đến một nhóm;
nó không cần phải là thành viên của nhóm, và có thể có bất kỳ số lượng nguồn gửi nào đến một nhóm nhất định.

Khi sử dụng multicast IP để gửi cùng một gói đến từng thành viên của nhóm, một máy chủ chỉ cần gửi một bản sao của gói được định địa chỉ đến địa chỉ multicast của nhóm.
Máy gửi không cần biết địa chỉ IP unicast của từng thành viên nhóm vì, như chúng ta sẽ thấy, kiến thức đó được phân phối giữa các router trong liên mạng.
Tương tự, máy gửi không cần gửi nhiều bản sao của gói vì các router sẽ tạo bản sao bất cứ khi nào chúng phải chuyển tiếp gói qua nhiều liên kết.
So với việc sử dụng IP unicast để chuyển cùng một gói đến nhiều máy nhận, multicast IP mở rộng tốt hơn vì nó loại bỏ lưu lượng dư thừa (các gói)
có thể đã được gửi nhiều lần qua cùng một liên kết, đặc biệt là các liên kết gần máy gửi.

Multicast nhiều-nhiều ban đầu của IP đã được bổ sung hỗ trợ cho một dạng multicast một-nhiều.
Trong mô hình multicast một-nhiều này, gọi là *Source-Specific Multicast* (SSM), một máy nhận chỉ định cả một nhóm multicast và một máy gửi cụ thể.
Máy nhận sau đó sẽ nhận các multicast gửi đến nhóm đã chỉ định, nhưng chỉ nếu chúng đến từ nguồn gửi đã chỉ định.
Nhiều ứng dụng multicast Internet (ví dụ, phát thanh radio) phù hợp với mô hình SSM.
Để phân biệt với SSM, mô hình nhiều-nhiều ban đầu của IP đôi khi được gọi là *Any Source Multicast* (ASM).

Một máy chủ báo hiệu mong muốn tham gia hoặc rời một nhóm multicast bằng cách giao tiếp với router cục bộ của nó
bằng một giao thức đặc biệt cho mục đích đó. Trong IPv4, giao thức đó là *Internet Group Management Protocol* (IGMP);
trong IPv6, nó là *Multicast Listener Discovery* (MLD).
Router sau đó có trách nhiệm đảm bảo multicast hoạt động đúng với máy chủ đó.
Bởi vì một máy chủ có thể không rời khỏi nhóm multicast khi cần thiết (sau khi bị crash hoặc lỗi khác, chẳng hạn),
router định kỳ thăm dò mạng để xác định những nhóm nào vẫn còn được các máy chủ kết nối quan tâm.

4.3.1 Địa chỉ Multicast
-----------------------

IP có một dải con trong không gian địa chỉ của nó dành riêng cho địa chỉ multicast.
Trong IPv4, các địa chỉ này được gán trong không gian địa chỉ lớp D, và IPv6 cũng có một phần không gian địa chỉ dành riêng cho địa chỉ nhóm multicast.
Một số dải con của dải multicast được dành cho multicast nội miền, vì vậy chúng có thể được sử dụng lại độc lập bởi các miền khác nhau.

Như vậy có 28 bit địa chỉ multicast khả dụng trong IPv4 nếu bỏ qua tiền tố chung của tất cả địa chỉ multicast.
Điều này gây ra một vấn đề khi cố gắng tận dụng multicast phần cứng trên một mạng LAN.
Hãy lấy trường hợp của Ethernet. Địa chỉ multicast của Ethernet chỉ có 23 bit nếu bỏ qua tiền tố chung của chúng.
Nói cách khác, để tận dụng multicast của Ethernet, IP phải ánh xạ địa chỉ multicast IP 28 bit vào địa chỉ multicast Ethernet 23 bit.
Điều này được hiện thực bằng cách lấy 23 bit thấp nhất của bất kỳ địa chỉ multicast IP nào để dùng làm địa chỉ multicast Ethernet của nó và bỏ qua 5 bit cao nhất.
Như vậy, 32 (2\ :sup:`5`) địa chỉ IP ánh xạ vào mỗi địa chỉ Ethernet.

   Trong phần này, chúng tôi sử dụng Ethernet như một ví dụ điển hình về công nghệ mạng hỗ trợ multicast bằng phần cứng,
   nhưng điều tương tự cũng đúng với PON (Passive Optical Networks), là công nghệ mạng truy nhập thường được dùng để cung cấp cáp quang đến nhà.
   Thực tế, IP Multicast qua PON hiện là cách phổ biến để cung cấp IPTV đến các hộ gia đình.

Khi một máy chủ trên Ethernet tham gia một nhóm multicast IP, nó cấu hình giao diện Ethernet của mình để nhận bất kỳ gói nào có địa chỉ multicast Ethernet tương ứng.
Đáng tiếc, điều này khiến máy nhận không chỉ nhận lưu lượng multicast mà nó mong muốn mà còn nhận cả lưu lượng gửi đến bất kỳ nhóm multicast IP nào khác
cùng ánh xạ vào địa chỉ Ethernet đó, nếu chúng được định tuyến đến Ethernet này.
Do đó, IP ở máy nhận phải kiểm tra header IP của bất kỳ gói multicast nào để xác định xem gói đó thực sự thuộc về nhóm mong muốn hay không.
Tóm lại, sự không khớp về kích thước địa chỉ multicast có nghĩa là lưu lượng multicast có thể gây gánh nặng lên các máy chủ thậm chí không quan tâm đến nhóm mà lưu lượng được gửi đến.
May mắn thay, trong một số mạng chuyển mạch (như Ethernet chuyển mạch), vấn đề này có thể được giảm nhẹ bằng các cơ chế mà switch nhận biết các gói không mong muốn và loại bỏ chúng.

Một câu hỏi gây bối rối là làm thế nào các nguồn gửi và máy nhận biết được địa chỉ multicast nào cần sử dụng ngay từ đầu.
Điều này thường được xử lý bằng các phương tiện ngoài băng tần, và có một số công cụ khá tinh vi để quảng bá địa chỉ nhóm trên Internet.

4.3.2 Định tuyến Multicast (DVMRP, PIM, MSDP)
---------------------------------------------

Bảng chuyển tiếp unicast của một router chỉ ra, với bất kỳ địa chỉ IP nào, liên kết nào sẽ được dùng để chuyển tiếp gói unicast.
Để hỗ trợ multicast, một router phải có thêm bảng chuyển tiếp multicast chỉ ra, dựa trên địa chỉ multicast, liên kết nào—có thể nhiều hơn một—sẽ được dùng để chuyển tiếp gói multicast (router sẽ nhân bản gói nếu phải chuyển tiếp qua nhiều liên kết).
Như vậy, trong khi các bảng chuyển tiếp unicast tập hợp lại chỉ định một tập các đường đi, các bảng chuyển tiếp multicast tập hợp lại chỉ định một tập các cây: *cây phân phối multicast*.
Hơn nữa, để hỗ trợ Source-Specific Multicast (và, thực tế, cho một số loại Any Source Multicast), các bảng chuyển tiếp multicast phải chỉ ra liên kết nào sẽ được dùng dựa trên kết hợp giữa địa chỉ multicast và địa chỉ IP (unicast) của nguồn gửi, một lần nữa chỉ định một tập các cây.

Định tuyến multicast là quá trình xác định các cây phân phối multicast hoặc, cụ thể hơn, quá trình xây dựng các bảng chuyển tiếp multicast.
Cũng như định tuyến unicast, một giao thức định tuyến multicast không chỉ cần “hoạt động”; nó còn phải mở rộng hợp lý khi mạng phát triển, và phải đáp ứng tính tự chủ của các miền định tuyến khác nhau.

DVMRP
~~~~~

Định tuyến vector-khoảng cách dùng trong unicast có thể được mở rộng để hỗ trợ multicast.
Giao thức kết quả được gọi là *Distance Vector Multicast Routing Protocol*, hay DVMRP.
DVMRP là giao thức định tuyến multicast đầu tiên được sử dụng rộng rãi.

Nhớ lại rằng, trong thuật toán vector-khoảng cách, mỗi router duy trì một bảng các bộ ba ``Destination, Cost, NextHop``,
và trao đổi danh sách các cặp ``(Destination, Cost)`` với các láng giềng kết nối trực tiếp.
Mở rộng thuật toán này để hỗ trợ multicast là một quá trình hai giai đoạn.
Đầu tiên, chúng ta tạo một cơ chế quảng bá cho phép một gói được chuyển tiếp đến tất cả các mạng trên internet.
Thứ hai, chúng ta cần tinh chỉnh cơ chế này để loại bỏ các mạng không có máy chủ thuộc nhóm multicast.
Do đó, DVMRP là một trong số các giao thức định tuyến multicast được mô tả là giao thức *flood-and-prune*.

Với một bảng định tuyến unicast, mỗi router biết rằng đường đi ngắn nhất hiện tại đến một ``destination`` nào đó đi qua ``NextHop``.
Do đó, bất cứ khi nào nó nhận được một gói multicast từ nguồn S, router sẽ chuyển tiếp gói trên tất cả các liên kết ra (trừ liên kết mà gói đến)
nếu và chỉ nếu gói đến qua liên kết nằm trên đường đi ngắn nhất đến S (tức là, gói đến *từ* ``NextHop`` liên kết với S trong bảng định tuyến).
Chiến lược này thực chất là flood các gói ra ngoài từ S nhưng không vòng lại về S.

Có hai nhược điểm lớn với cách tiếp cận này. Thứ nhất là nó thực sự flood toàn mạng; nó không có cơ chế tránh các LAN không có thành viên nhóm multicast.
Chúng ta sẽ giải quyết vấn đề này bên dưới. Hạn chế thứ hai là một gói nhất định sẽ được chuyển tiếp qua một LAN bởi mỗi router kết nối với LAN đó.
Điều này là do chiến lược chuyển tiếp flood gói trên tất cả các liên kết trừ liên kết mà gói đến, mà không quan tâm liệu các liên kết đó có thuộc cây đường đi ngắn nhất gốc tại nguồn hay không.

Giải pháp cho hạn chế thứ hai này là loại bỏ các gói broadcast trùng lặp được tạo ra khi nhiều hơn một router kết nối với một LAN nhất định.
Một cách để làm điều này là chỉ định một router làm router *cha* cho mỗi liên kết, so với nguồn, chỉ router cha mới được phép chuyển tiếp gói multicast từ nguồn đó qua LAN.
Router có đường đi ngắn nhất đến nguồn S được chọn làm cha; nếu hai router hòa nhau thì chọn router có địa chỉ nhỏ nhất.
Một router có thể biết mình có phải là cha cho LAN (so với từng nguồn) dựa trên các thông điệp vector-khoảng cách nó trao đổi với láng giềng.

Lưu ý rằng tinh chỉnh này yêu cầu mỗi router giữ, cho mỗi nguồn, một bit cho mỗi liên kết của nó để chỉ ra liệu nó có phải là cha cho cặp nguồn/liên kết đó hay không.
Hãy nhớ rằng trong môi trường internet, một nguồn là một mạng, không phải một máy chủ, vì router internet chỉ quan tâm đến việc chuyển tiếp gói giữa các mạng.
Cơ chế kết quả đôi khi được gọi là *Reverse Path Broadcast* (RPB) hoặc *Reverse Path Forwarding* (RPF).
Đường đi là ngược vì chúng ta xét đường đi ngắn nhất về phía *nguồn* khi đưa ra quyết định chuyển tiếp, so với định tuyến unicast, vốn tìm đường đi ngắn nhất đến một *đích*.

Cơ chế RPB vừa mô tả hiện thực quảng bá đường đi ngắn nhất. Bây giờ chúng ta muốn loại bỏ tập các mạng nhận mỗi gói gửi đến nhóm G để loại trừ các mạng không có máy chủ là thành viên của G.
Điều này có thể thực hiện qua hai giai đoạn. Đầu tiên, chúng ta cần nhận biết khi nào một mạng *lá* không có thành viên nhóm.
Xác định một mạng là lá thì dễ—nếu router cha như mô tả ở trên là router duy nhất trên mạng, thì mạng đó là lá.
Xác định xem có thành viên nhóm nào trên mạng không được thực hiện bằng cách để mỗi máy chủ là thành viên nhóm G định kỳ thông báo điều này qua mạng,
như đã mô tả ở phần multicast link-state trước đó. Router sau đó dùng thông tin này để quyết định có chuyển tiếp gói multicast gửi đến G qua LAN này hay không.

Giai đoạn thứ hai là truyền thông tin “không có thành viên G ở đây” lên cây đường đi ngắn nhất.
Điều này được thực hiện bằng cách để router bổ sung tập hợp các nhóm mà mạng lá quan tâm nhận gói multicast vào các cặp ``(Destination, Cost)`` nó gửi cho láng giềng.
Thông tin này sau đó có thể được truyền từ router này sang router khác, để với mỗi liên kết của mình, một router biết cho nhóm nào nó nên chuyển tiếp gói multicast.

Lưu ý rằng việc đưa tất cả thông tin này vào bản cập nhật định tuyến là khá tốn kém.
Trong thực tế, thông tin này chỉ được trao đổi khi một nguồn bắt đầu gửi gói đến nhóm đó.
Nói cách khác, chiến lược là dùng RPB, chỉ thêm một lượng nhỏ overhead vào thuật toán vector-khoảng cách cơ bản, cho đến khi một địa chỉ multicast cụ thể trở nên hoạt động.
Lúc đó, các router không quan tâm nhận gói gửi đến nhóm đó sẽ lên tiếng, và thông tin đó được truyền đến các router khác.

PIM-SM
~~~~~~

*Protocol Independent Multicast*, hay PIM, được phát triển để giải quyết các vấn đề mở rộng của các giao thức định tuyến multicast trước đó.
Cụ thể, người ta nhận ra rằng các giao thức hiện có không mở rộng tốt trong môi trường mà chỉ một tỷ lệ nhỏ router muốn nhận lưu lượng cho một nhóm nhất định.
Ví dụ, phát lưu lượng đến tất cả router cho đến khi họ yêu cầu loại bỏ khỏi phân phối không phải là lựa chọn thiết kế tốt nếu hầu hết router không muốn nhận lưu lượng ngay từ đầu.
Tình huống này đủ phổ biến để PIM chia không gian vấn đề thành *sparse mode* và *dense mode*, trong đó sparse và dense chỉ tỷ lệ router muốn multicast.
PIM dense mode (PIM-DM) dùng thuật toán flood-and-prune như DVMRP và gặp vấn đề mở rộng tương tự.
PIM sparse mode (PIM-SM) đã trở thành giao thức định tuyến multicast chủ đạo và là trọng tâm thảo luận ở đây.
Khía cạnh “protocol independent” của PIM, nhân tiện, chỉ việc, không như các giao thức trước như DVMRP, PIM không phụ thuộc vào bất kỳ loại định tuyến unicast nào—nó có thể dùng với bất kỳ giao thức định tuyến unicast nào, như chúng ta sẽ thấy bên dưới.

Trong PIM-SM, các router tham gia rõ ràng vào cây phân phối multicast bằng các thông điệp giao thức PIM gọi là thông điệp ``Join``.
Lưu ý sự tương phản với cách tiếp cận của DVMRP là tạo cây broadcast trước rồi loại bỏ các router không quan tâm.
Câu hỏi đặt ra là gửi các thông điệp ``Join`` đó đến đâu vì, rốt cuộc, bất kỳ máy chủ nào (và bất kỳ số lượng máy chủ nào) cũng có thể gửi đến nhóm multicast.
Để giải quyết điều này, PIM-SM gán cho mỗi nhóm một router đặc biệt gọi là *rendezvous point* (RP).
Nói chung, một số router trong một miền được cấu hình làm RP ứng viên, và PIM-SM định nghĩa một tập thủ tục để tất cả router trong miền có thể đồng ý về router sẽ dùng làm RP cho một nhóm nhất định.
Các thủ tục này khá phức tạp, vì chúng phải xử lý nhiều kịch bản khác nhau, như lỗi của một RP ứng viên và việc phân chia miền thành hai mạng riêng biệt do một số lỗi liên kết hoặc nút.
Trong phần còn lại của thảo luận này, chúng ta giả định tất cả router trong một miền biết địa chỉ IP unicast của RP cho một nhóm nhất định.

Một cây chuyển tiếp multicast được xây dựng nhờ các router gửi thông điệp ``Join`` đến RP.
PIM-SM cho phép xây dựng hai loại cây: *shared* tree, có thể được tất cả nguồn gửi dùng, và *source-specific* tree, chỉ được một nguồn gửi cụ thể dùng.
Chế độ hoạt động bình thường tạo shared tree trước, sau đó là một hoặc nhiều source-specific tree nếu có đủ lưu lượng để biện minh.
Vì việc xây dựng cây cài đặt trạng thái vào các router dọc theo cây, điều quan trọng là mặc định chỉ có một cây cho một nhóm, không phải một cây cho mỗi nguồn gửi đến một nhóm.

.. _fig-pim-shared:
.. figure:: figures/f04-14-9780123850591.png
   :width: 600px
   :align: center

   Hoạt động PIM: (a) R4 gửi thông điệp Join đến RP và tham gia shared tree; (b) R5 tham gia shared tree; (c) RP xây dựng source-specific tree đến R1 bằng cách gửi thông điệp Join đến R1; (d) R4 và R5 xây dựng source-specific tree đến R1 bằng cách gửi thông điệp Join đến R1.

Khi một router gửi thông điệp ``Join`` về phía RP cho nhóm G, nó được gửi bằng truyền tải IP unicast thông thường.
Điều này được minh họa trong :numref:`Figure %s(a) <fig-pim-shared>`, trong đó router R4 gửi một ``Join`` đến rendezvous point cho một nhóm nào đó.
Thông điệp ``Join`` ban đầu là “wildcarded”; tức là, nó áp dụng cho tất cả nguồn gửi.
Một thông điệp ``Join`` rõ ràng phải đi qua một chuỗi router trước khi đến RP (ví dụ, R2).
Mỗi router dọc đường xem xét ``Join`` và tạo một mục bảng chuyển tiếp cho shared tree, gọi là mục (\*, G) (trong đó \* nghĩa là “tất cả nguồn gửi”).
Để tạo mục bảng chuyển tiếp, nó xem giao diện mà ``Join`` đến và đánh dấu giao diện đó là giao diện mà nó nên chuyển tiếp gói dữ liệu cho nhóm này.
Sau đó, nó xác định giao diện nào sẽ dùng để chuyển tiếp ``Join`` về phía RP. Đây sẽ là giao diện duy nhất chấp nhận gói đến nhóm này.
Nó sau đó chuyển tiếp ``Join`` về phía RP. Cuối cùng, thông điệp đến RP, hoàn tất việc xây dựng nhánh cây.
Shared tree được xây dựng như vậy được thể hiện bằng đường liền nét từ RP đến R4 trong :numref:`Figure %s(a) <fig-pim-shared>`.

Khi nhiều router gửi ``Join`` về phía RP, chúng tạo ra các nhánh mới cho cây, như minh họa trong :numref:`Figure %s(b) <fig-pim-shared>`.
Lưu ý rằng, trong trường hợp này, ``Join`` chỉ cần đi đến R2, router này có thể thêm nhánh mới vào cây chỉ bằng cách thêm một giao diện ra mới vào mục bảng chuyển tiếp đã tạo cho nhóm này.
R2 không cần chuyển tiếp ``Join`` đến RP. Cũng lưu ý rằng kết quả cuối cùng của quá trình này là xây dựng một cây có gốc là RP.

Tại thời điểm này, giả sử một máy chủ muốn gửi thông điệp đến nhóm.
Để làm điều này, nó tạo một gói với địa chỉ nhóm multicast phù hợp làm đích và gửi nó đến một router trên mạng cục bộ của nó gọi là *designated router* (DR).
Giả sử DR là R1 trong :numref:`Figure %s <fig-pim-shared>`.
Hiện tại không có trạng thái nào cho nhóm multicast này giữa R1 và RP, nên thay vì chỉ chuyển tiếp gói multicast, R1 *tunnel* nó đến RP.
Tức là, R1 đóng gói gói multicast bên trong một thông điệp PIM ``Register`` mà nó gửi đến địa chỉ IP unicast của RP.
Giống như một đầu cuối tunnel IP, RP nhận gói gửi đến nó, xem payload của thông điệp ``Register``, và tìm thấy bên trong một gói IP gửi đến địa chỉ multicast của nhóm này.
RP, tất nhiên, biết phải làm gì với gói như vậy—nó gửi gói ra shared tree mà RP là gốc.
Trong ví dụ :numref:`Figure %s <fig-pim-shared>`, điều này nghĩa là RP gửi gói đến R2, router này có thể chuyển tiếp đến R4 và R5.
Việc chuyển phát hoàn chỉnh một gói từ R1 đến R4 và R5 được thể hiện trong :numref:`Figure %s <fig-pim-deliver>`.
Chúng ta thấy gói tunnel đi từ R1 đến RP với một header IP bổ sung chứa địa chỉ unicast của RP, và sau đó gói multicast gửi đến G đi dọc theo shared tree đến R4 và R5.

Tại thời điểm này, chúng ta có thể bị cám dỗ tuyên bố thành công, vì tất cả máy chủ đều có thể gửi đến tất cả máy nhận theo cách này.
Tuy nhiên, có một số không hiệu quả về băng thông và chi phí xử lý trong việc đóng gói và giải đóng gói các gói trên đường đến RP,
nên RP buộc các router trung gian phải biết về nhóm này để tránh tunnel.
Nó gửi một thông điệp ``Join`` về phía máy gửi (:numref:`Figure %s(c) <fig-pim-shared>`).
Khi ``Join`` này đi về phía máy gửi, nó khiến các router dọc đường (R3) biết về nhóm, để DR có thể gửi gói đến nhóm dưới dạng multicast *gốc* (tức là, không tunnel).

.. _fig-pim-deliver:
.. figure:: figures/f04-15-9780123850591.png
   :width: 500px
   :align: center

   Chuyển phát một gói dọc theo shared tree. R1 tunnel gói đến RP, RP chuyển tiếp gói dọc theo shared tree đến R4 và R5.

Một chi tiết quan trọng cần lưu ý ở giai đoạn này là thông điệp ``Join`` do RP gửi đến máy gửi là dành riêng cho nguồn gửi đó,
trong khi các thông điệp trước đó do R4 và R5 gửi áp dụng cho tất cả nguồn gửi.
Do đó, hiệu ứng của ``Join`` mới là tạo trạng thái *dành riêng cho nguồn gửi* trong các router giữa nguồn xác định và RP.
Điều này được gọi là trạng thái (S, G), vì nó áp dụng cho một nguồn gửi đến một nhóm, và đối lập với trạng thái (\*, G) được cài đặt giữa các máy nhận và RP áp dụng cho tất cả nguồn gửi.
Như vậy, trong :numref:`Figure %s(c) <fig-pim-shared>`, ta thấy một đường đi dành riêng cho nguồn từ R1 đến RP (đường nét đứt) và một cây hợp lệ cho tất cả nguồn gửi từ RP đến máy nhận (đường liền nét).

Tối ưu hóa tiếp theo có thể là thay thế toàn bộ shared tree bằng một source-specific tree.
Điều này là mong muốn vì đường đi từ nguồn gửi đến máy nhận qua RP có thể dài hơn đáng kể so với đường đi ngắn nhất có thể.
Điều này một lần nữa có thể được kích hoạt bởi tốc độ dữ liệu cao từ một nguồn gửi nào đó.
Trong trường hợp này, router ở đầu hạ lưu của cây—giả sử R4 trong ví dụ—gửi một thông điệp ``Join`` dành riêng cho nguồn về phía nguồn.
Khi nó đi theo đường đi ngắn nhất về phía nguồn, các router dọc đường tạo trạng thái (S, G) cho cây này, và kết quả là một cây có gốc tại nguồn, thay vì RP.
Giả sử cả R4 và R5 đều chuyển sang source-specific tree, ta sẽ có cây như trong :numref:`Figure %s(d) <fig-pim-shared>`.
Lưu ý rằng cây này không còn liên quan đến RP nữa. Chúng tôi đã loại bỏ shared tree khỏi hình để đơn giản hóa sơ đồ,
nhưng thực tế tất cả router có máy nhận cho một nhóm phải ở lại shared tree phòng khi có nguồn gửi mới xuất hiện.

Bây giờ chúng ta có thể thấy tại sao PIM là protocol independent.
Tất cả các cơ chế xây dựng và duy trì cây của nó tận dụng định tuyến unicast mà không phụ thuộc vào bất kỳ giao thức định tuyến unicast cụ thể nào.
Việc hình thành cây hoàn toàn được xác định bởi các đường đi mà thông điệp ``Join`` đi theo, được xác định bởi lựa chọn đường đi ngắn nhất của định tuyến unicast.
Do đó, chính xác thì PIM là “unicast routing protocol independent”, so với DVMRP.
Lưu ý rằng PIM rất gắn bó với Internet Protocol—nó không độc lập về mặt giao thức lớp mạng.

Thiết kế của PIM-SM một lần nữa minh họa những thách thức trong việc xây dựng các mạng mở rộng và cách mà khả năng mở rộng đôi khi đối lập với một số loại tối ưu nào đó.
Shared tree chắc chắn mở rộng tốt hơn source-specific tree, vì nó giảm tổng trạng thái trong router xuống còn bậc số nhóm thay vì số nguồn gửi nhân số nhóm.
Tuy nhiên, source-specific tree có thể cần thiết để đạt hiệu quả định tuyến và sử dụng băng thông liên kết hiệu quả.

Interdomain Multicast (MSDP)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

PIM-SM có một số hạn chế đáng kể khi nói đến multicast liên miền.
Cụ thể, sự tồn tại của một RP duy nhất cho một nhóm đi ngược lại nguyên tắc rằng các miền là tự chủ.
Với một nhóm multicast nhất định, tất cả các miền tham gia sẽ phụ thuộc vào miền nơi RP được đặt.
Hơn nữa, nếu có một nhóm multicast cụ thể mà một nguồn gửi và một số máy nhận cùng chia sẻ một miền,
lưu lượng multicast vẫn phải được định tuyến ban đầu từ nguồn gửi đến các máy nhận đó qua bất kỳ miền nào có RP cho nhóm multicast đó.
Do đó, giao thức PIM-SM thường không được sử dụng qua các miền, chỉ trong một miền.

Để mở rộng multicast qua các miền sử dụng PIM-SM, giao thức Multicast Source Discovery Protocol (MSDP) đã được phát triển.
MSDP được dùng để kết nối các miền khác nhau—mỗi miền chạy PIM-SM nội bộ, với RP riêng—bằng cách kết nối các RP của các miền khác nhau.
Mỗi RP có một hoặc nhiều RP MSDP peer ở các miền khác. Mỗi cặp MSDP peer được kết nối bằng một kết nối TCP mà giao thức MSDP chạy trên đó.
Tất cả các MSDP peer cho một nhóm multicast nhất định tạo thành một mesh lỏng lẻo được dùng như một mạng broadcast.
Các thông điệp MSDP được broadcast qua mesh các RP peer bằng thuật toán Reverse Path Broadcast mà chúng ta đã thảo luận trong ngữ cảnh DVMRP.

MSDP broadcast thông tin gì qua mesh các RP? Không phải thông tin thành viên nhóm; khi một máy chủ tham gia nhóm, thông tin đó chỉ đi xa nhất là RP của miền nó.
Thay vào đó, đó là thông tin nguồn gửi—multicast sender.
Mỗi RP biết các nguồn gửi trong miền của nó vì nó nhận được thông điệp ``Register`` bất cứ khi nào có nguồn gửi mới xuất hiện.
Mỗi RP định kỳ dùng MSDP để broadcast thông điệp ``Source Active`` đến các peer, cung cấp địa chỉ IP của nguồn gửi, địa chỉ nhóm multicast, và địa chỉ IP của RP gốc.

.. _fig-msdp:
.. figure:: figures/f04-16-9780123850591.png
   :width: 500px
   :align: center

   Hoạt động MSDP: (a) Nguồn gửi SR gửi thông điệp Register đến RP của miền nó, RP1; sau đó RP1 gửi thông điệp Join dành riêng cho nguồn đến SR và một thông điệp MSDP Source Active đến MSDP peer ở Miền B, RP2; sau đó RP2 gửi thông điệp Join dành riêng cho nguồn đến SR. (b) Kết quả là RP1 và RP2 nằm trong source-specific tree cho nguồn gửi SR.

Nếu một MSDP peer RP nhận được một broadcast như vậy có máy nhận hoạt động cho nhóm multicast đó, nó gửi một thông điệp ``Join`` dành riêng cho nguồn, thay mặt RP đó, đến máy chủ nguồn gửi, như minh họa trong :numref:`Figure %s(a) <fig-msdp>`.
Thông điệp ``Join`` xây dựng một nhánh của source-specific tree đến RP này, như trong :numref:`Figure %s(b) <fig-msdp>`.
Kết quả là mọi RP thuộc mạng MSDP và có máy nhận hoạt động cho một nhóm multicast cụ thể đều được thêm vào source-specific tree của nguồn gửi mới.
Khi một RP nhận được multicast từ nguồn gửi, RP dùng shared tree của nó để chuyển tiếp multicast đến các máy nhận trong miền của nó.

Source-Specific Multicast (PIM-SSM)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Mô hình dịch vụ ban đầu của PIM, giống như các giao thức multicast trước đó, là mô hình nhiều-nhiều.
Máy nhận tham gia một nhóm, và bất kỳ máy chủ nào cũng có thể gửi đến nhóm.
Tuy nhiên, vào cuối những năm 1990, người ta nhận ra rằng có thể hữu ích khi bổ sung mô hình một-nhiều.
Rất nhiều ứng dụng multicast, rốt cuộc, chỉ có một nguồn gửi hợp lệ, như diễn giả tại một hội nghị được truyền qua Internet.
Chúng ta đã thấy rằng PIM-SM có thể tạo cây đường đi ngắn nhất dành riêng cho nguồn như một tối ưu hóa sau khi dùng shared tree ban đầu.
Trong thiết kế PIM ban đầu, tối ưu hóa này là vô hình với máy chủ—chỉ router tham gia cây dành riêng cho nguồn.
Tuy nhiên, khi nhu cầu về mô hình dịch vụ một-nhiều được nhận ra, người ta quyết định làm cho khả năng định tuyến dành riêng cho nguồn của PIM-SM rõ ràng cho máy chủ.
Thực tế, điều này chủ yếu yêu cầu thay đổi IGMP và tương tự của nó trong IPv6, MLD, hơn là PIM.
Khả năng mới được công khai này hiện được gọi là PIM-SSM (PIM Source-Specific Multicast).

PIM-SSM giới thiệu một khái niệm mới, *channel*, là sự kết hợp giữa địa chỉ nguồn S và địa chỉ nhóm G.
Địa chỉ nhóm G trông giống như một địa chỉ multicast IP thông thường, và cả IPv4 và IPv6 đều đã phân bổ dải con của không gian địa chỉ multicast cho SSM.
Để sử dụng PIM-SSM, một máy chủ chỉ định cả nhóm và nguồn trong thông điệp IGMP Membership Report gửi đến router cục bộ.
Router đó sau đó gửi một thông điệp ``Join`` dành riêng cho nguồn của PIM-SM về phía nguồn, do đó thêm một nhánh cho chính nó vào source-specific tree,
giống như đã mô tả ở trên cho PIM-SM “bình thường”, nhưng bỏ qua toàn bộ giai đoạn shared-tree.
Vì cây kết quả là dành riêng cho nguồn, chỉ nguồn gửi được chỉ định mới có thể gửi gói trên cây đó.

Việc giới thiệu PIM-SSM đã mang lại một số lợi ích đáng kể, đặc biệt vì nhu cầu multicast một-nhiều tương đối cao:

-  Multicast đi trực tiếp hơn đến máy nhận.

-  Địa chỉ của một channel thực chất là địa chỉ nhóm multicast cộng với địa chỉ nguồn.
   Do đó, với một dải địa chỉ nhóm multicast nhất định sẽ chỉ dùng cho SSM, nhiều miền có thể sử dụng cùng một địa chỉ nhóm multicast độc lập và không xung đột,
   miễn là họ chỉ dùng nó với nguồn gửi trong miền của mình.

-  Vì chỉ nguồn gửi được chỉ định mới có thể gửi đến một nhóm SSM, nguy cơ tấn công dựa trên máy chủ độc hại làm quá tải router hoặc máy nhận bằng lưu lượng multicast giả mạo giảm đi.

-  PIM-SSM có thể được sử dụng qua các miền giống hệt như trong một miền, không cần dựa vào thứ gì như MSDP.

Do đó, SSM là một bổ sung rất hữu ích cho mô hình dịch vụ multicast.

Bidirectional Trees (BIDIR-PIM)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Chúng ta kết thúc phần thảo luận về multicast với một cải tiến khác cho PIM gọi là *Bidirectional PIM*.
BIDIR-PIM là một biến thể gần đây của PIM-SM rất phù hợp cho multicast nhiều-nhiều trong một miền,
đặc biệt khi nguồn gửi và máy nhận cho một nhóm có thể là cùng một máy, như trong hội nghị truyền hình đa bên, chẳng hạn.
Như trong PIM-SM, máy nhận tiềm năng tham gia nhóm bằng cách gửi thông điệp IGMP Membership Report (không được dành riêng cho nguồn),
và một shared tree gốc tại RP được dùng để chuyển tiếp gói multicast đến máy nhận.
Tuy nhiên, không như PIM-SM, shared tree cũng có các nhánh đến *nguồn gửi*.
Điều này sẽ không có ý nghĩa với cây một chiều của PIM-SM, nhưng cây của BIDIR-PIM là hai chiều—một router nhận gói multicast từ một nhánh hạ lưu có thể chuyển tiếp nó cả lên cây và xuống các nhánh khác.
Đường đi để chuyển một gói đến bất kỳ máy nhận nào chỉ đi lên cây xa nhất cần thiết trước khi đi xuống nhánh đến máy nhận đó.
Xem đường multicast từ R1 đến R2 trong :numref:`Figure %s(b) <fig-pim-bidir>` để biết ví dụ.
R4 chuyển tiếp một gói multicast xuống R2 cùng lúc chuyển một bản sao của gói đó lên R5.

Một điểm đáng ngạc nhiên của BIDIR-PIM là thực tế không cần có RP.
Tất cả những gì cần là một địa chỉ có thể định tuyến, được gọi là địa chỉ RP mặc dù nó không nhất thiết là địa chỉ của một RP hay bất cứ thứ gì cả.
Sao lại như vậy? Một ``Join`` từ máy nhận được chuyển tiếp về phía địa chỉ RP cho đến khi nó đến một router có giao diện trên liên kết nơi địa chỉ RP sẽ tồn tại, nơi Join kết thúc.
:numref:`Figure %s(a) <fig-pim-bidir>` cho thấy một ``Join`` từ R2 kết thúc tại R5, và một ``Join`` từ R3 kết thúc tại R6.
Việc chuyển tiếp lên của một gói multicast tương tự cũng chảy về phía địa chỉ RP cho đến khi nó đến một router có giao diện trên liên kết nơi địa chỉ RP sẽ tồn tại,
sau đó router chuyển tiếp gói multicast lên liên kết đó như bước cuối cùng của chuyển tiếp lên, đảm bảo tất cả router khác trên liên kết đó nhận được gói.
:numref:`Figure %s(b) <fig-pim-bidir>` minh họa luồng lưu lượng multicast bắt nguồn từ R1.

.. _fig-pim-bidir:
.. figure:: figures/f04-17-9780123850591.png
   :width: 400px
   :align: center

   Hoạt động BIDIR-PIM: (a) R2 và R3 gửi thông điệp Join về phía địa chỉ RP, kết thúc khi đến một router trên liên kết địa chỉ RP. (b) Một gói multicast từ R1 được chuyển tiếp lên liên kết địa chỉ RP và xuống bất cứ đâu nó giao với một nhánh thành viên nhóm.

BIDIR-PIM cho đến nay không thể dùng qua các miền. Mặt khác, nó có một số ưu điểm so với PIM-SM cho multicast nhiều-nhiều trong một miền:

-  Không có quá trình đăng ký nguồn gửi vì các router đã biết cách định tuyến gói multicast về phía địa chỉ RP.

-  Các đường đi trực tiếp hơn so với dùng shared tree của PIM-SM vì chúng chỉ đi lên cây xa nhất cần thiết, không phải đến tận RP.

-  Cây hai chiều dùng ít trạng thái hơn nhiều so với cây dành riêng cho nguồn của PIM-SM vì không bao giờ có trạng thái dành riêng cho nguồn. (Mặt khác, đường đi sẽ dài hơn so với cây dành riêng cho nguồn.)

-  RP không thể là nút cổ chai, và thực tế không cần RP thực sự.

Một kết luận có thể rút ra từ việc có quá nhiều cách tiếp cận khác nhau với multicast chỉ trong PIM là multicast là một không gian vấn đề khó để tìm giải pháp tối ưu.
Bạn cần quyết định tiêu chí nào muốn tối ưu (sử dụng băng thông, trạng thái router, độ dài đường đi, v.v.) và loại ứng dụng nào bạn đang cố hỗ trợ (một-nhiều, nhiều-nhiều, v.v.)
trước khi có thể chọn chế độ multicast “tốt nhất” cho nhiệm vụ.
