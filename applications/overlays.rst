9.4 Mạng phủ (Overlay Networks)
===============================

Từ khi ra đời, Internet đã áp dụng một mô hình rõ ràng, trong đó các
router bên trong mạng chịu trách nhiệm chuyển tiếp gói tin từ nguồn đến
đích, và các chương trình ứng dụng chạy trên các máy chủ kết nối ở rìa
mạng. Mô hình client/server được minh họa bởi các ứng dụng được thảo
luận trong hai phần đầu của chương này chắc chắn tuân thủ mô hình này.

Tuy nhiên, trong vài năm gần đây, ranh giới giữa *chuyển tiếp gói tin*
và *xử lý ứng dụng* đã trở nên mờ nhạt hơn. Các ứng dụng mới đang được
phân tán trên toàn Internet, và trong nhiều trường hợp các ứng dụng này
tự đưa ra quyết định chuyển tiếp riêng. Những ứng dụng lai mới này đôi
khi có thể được triển khai bằng cách mở rộng các router và switch truyền
thống để hỗ trợ một lượng nhỏ xử lý đặc thù ứng dụng. Ví dụ, các
*switch lớp 7* (level-7 switches) được đặt trước các cụm máy chủ và
chuyển tiếp các yêu cầu HTTP đến một máy chủ cụ thể dựa trên URL được
yêu cầu. Tuy nhiên, *mạng phủ* (overlay networks) đang nhanh chóng nổi
lên như một cơ chế được lựa chọn để đưa các chức năng mới vào Internet.

.. _fig-overlay-net:
.. figure:: figures/f09-19-9780123850591.png
   :width: 300px
   :align: center

   Mạng phủ được xếp lớp trên một mạng vật lý.

Bạn có thể nghĩ về một overlay như một mạng logic được triển khai trên
một mạng nền tảng nào đó. Theo định nghĩa này, Internet ban đầu là một
mạng phủ trên các liên kết do mạng điện thoại cũ cung cấp.
:numref:`Hình %s <fig-overlay-net>` mô tả một overlay được triển khai
trên một mạng nền tảng. Mỗi nút trong overlay cũng tồn tại trong mạng
nền tảng; nó xử lý và chuyển tiếp các gói tin theo cách đặc thù ứng
dụng. Các liên kết kết nối các nút overlay được triển khai dưới dạng các
tunnel xuyên qua mạng nền tảng. Nhiều mạng phủ có thể cùng tồn tại trên
một mạng nền tảng—mỗi mạng triển khai hành vi đặc thù ứng dụng riêng—
và các overlay có thể được lồng nhau, lớp này trên lớp khác. Ví dụ, tất
cả các mạng phủ được thảo luận trong phần này đều coi Internet ngày nay
là mạng nền tảng.

.. _fig-overlay-tunnel:
.. figure:: figures/f09-20-9780123850591.png
   :width: 500px
   :align: center

   Các nút overlay tunnel qua các nút vật lý.

Chúng ta đã thấy các ví dụ về tunneling, ví dụ, để triển khai mạng riêng
ảo (VPN). Để nhắc lại ngắn gọn, các nút ở hai đầu tunnel coi đường đi
multi-hop giữa chúng như một liên kết logic đơn, các nút bị tunnel qua
chuyển tiếp gói tin dựa trên header ngoài, không bao giờ biết rằng các
nút đầu cuối đã gắn thêm một header bên trong. :numref:`Hình %s
<fig-overlay-tunnel>` cho thấy ba nút overlay (A, B, và C) được kết nối
bởi một cặp tunnel. Trong ví dụ này, nút overlay B có thể đưa ra quyết
định chuyển tiếp cho các gói tin từ A đến C dựa trên header bên trong
(``IHdr``), sau đó gắn một header ngoài (``OHdr``) xác định C là đích
trong mạng nền tảng. Các nút A, B, và C có thể diễn giải cả header trong
và ngoài, trong khi các router trung gian chỉ hiểu header ngoài. Tương
tự, A, B, và C có địa chỉ trong cả mạng overlay và mạng nền tảng, nhưng
chúng không nhất thiết giống nhau; ví dụ, địa chỉ nền tảng của chúng có
thể là địa chỉ IP 32-bit, trong khi địa chỉ overlay có thể là địa chỉ
thử nghiệm 128-bit. Thực tế, overlay không nhất thiết phải dùng địa chỉ
truyền thống mà có thể định tuyến dựa trên URL, tên miền, truy vấn XML,
hoặc thậm chí nội dung của gói tin.

9.4.1 Overlay định tuyến (Routing Overlays)
-------------------------------------------

Loại overlay đơn giản nhất là loại chỉ tồn tại để hỗ trợ một chiến lược
định tuyến thay thế; không có xử lý mức ứng dụng bổ sung nào được thực
hiện tại các nút overlay. Bạn có thể coi mạng riêng ảo (VPN) là một ví
dụ về overlay định tuyến, nhưng nó không định nghĩa một chiến lược hay
thuật toán thay thế mà chỉ là các mục bảng định tuyến thay thế để được
xử lý bởi thuật toán chuyển tiếp IP tiêu chuẩn. Trong trường hợp này,
overlay được cho là sử dụng “IP tunnel”, và khả năng sử dụng các VPN này
được hỗ trợ trong nhiều router thương mại.

Tuy nhiên, giả sử bạn muốn sử dụng một thuật toán định tuyến mà các nhà
sản xuất router thương mại không sẵn lòng tích hợp vào sản phẩm của họ.
Bạn sẽ làm thế nào? Bạn chỉ cần chạy thuật toán của mình trên một tập
hợp các máy đầu cuối, và tunnel qua các router Internet. Các máy này sẽ
hành xử như các router trong mạng overlay: Là máy chủ, chúng có thể chỉ
kết nối với Internet qua một liên kết vật lý, nhưng là một nút trong
overlay, chúng sẽ kết nối với nhiều láng giềng qua các tunnel.

Vì overlay, gần như theo định nghĩa, là một cách để đưa công nghệ mới
vào mà không phụ thuộc vào quá trình chuẩn hóa, nên không có overlay
chuẩn nào để chúng ta lấy làm ví dụ. Thay vào đó, chúng tôi minh họa ý
tưởng chung về overlay định tuyến bằng cách mô tả một số hệ thống thử
nghiệm đã được các nhà nghiên cứu mạng xây dựng.

Các phiên bản thử nghiệm của IP
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Overlay là lý tưởng để triển khai các phiên bản thử nghiệm của IP mà bạn
hy vọng cuối cùng sẽ thay thế toàn bộ thế giới. Ví dụ, IP multicast bắt
đầu như một phần mở rộng của IP và thậm chí đến ngày nay vẫn chưa được
kích hoạt trên nhiều router Internet. MBone (multicast backbone) là một
mạng phủ triển khai IP multicast trên nền định tuyến unicast do Internet
cung cấp. Một số công cụ hội nghị đa phương tiện đã được phát triển và
triển khai trên MBone. Ví dụ, các cuộc họp IETF—kéo dài một tuần và thu
hút hàng nghìn người tham dự—trong nhiều năm đã được phát sóng qua
MBone. (Ngày nay, sự phổ biến rộng rãi của các công cụ hội nghị thương
mại đã thay thế cách tiếp cận dựa trên MBone.)

Giống như VPN, MBone sử dụng cả IP tunnel và địa chỉ IP, nhưng không
giống VPN, MBone triển khai một thuật toán chuyển tiếp khác—chuyển tiếp
gói tin đến tất cả các láng giềng phía dưới trong cây multicast đường đi
ngắn nhất. Là một overlay, các router hiểu multicast tunnel qua các
router legacy, với hy vọng một ngày nào đó sẽ không còn router legacy
nữa.

6-BONE là một overlay tương tự được sử dụng để triển khai IPv6 một cách
từng bước. Giống như MBone, 6-BONE sử dụng tunnel để chuyển tiếp gói tin
qua các router IPv4. Tuy nhiên, không giống MBone, các nút 6-BONE không
chỉ đơn giản cung cấp một cách diễn giải mới cho địa chỉ 32-bit của
IPv4. Thay vào đó, chúng chuyển tiếp gói tin dựa trên không gian địa chỉ
128-bit của IPv6. 6-BONE cũng hỗ trợ multicast IPv6. (Ngày nay, các
router thương mại đã hỗ trợ IPv6, nhưng một lần nữa, overlay là một cách
tiếp cận giá trị trong khi một công nghệ mới đang được đánh giá và tinh
chỉnh.)

End System Multicast
~~~~~~~~~~~~~~~~~~~~

Mặc dù IP multicast phổ biến với các nhà nghiên cứu và một số phân khúc
cộng đồng mạng, việc triển khai nó trên Internet toàn cầu vẫn còn rất
hạn chế. Đáp lại, các ứng dụng dựa trên multicast như hội nghị truyền
hình gần đây đã chuyển sang một chiến lược thay thế, gọi là *end system
multicast*. Ý tưởng của end system multicast là chấp nhận rằng IP
multicast sẽ không bao giờ trở nên phổ biến và thay vào đó để các máy
chủ đầu cuối tham gia vào một ứng dụng multicast cụ thể tự triển khai
cây multicast của riêng mình.

Trước khi mô tả cách end system multicast hoạt động, điều quan trọng là
phải hiểu rằng, không giống như VPN và MBone, end system multicast giả
định chỉ các máy chủ Internet (trái ngược với router Internet) tham gia
vào overlay. Hơn nữa, các máy chủ này thường trao đổi thông điệp với
nhau qua UDP tunnel thay vì IP tunnel, giúp dễ dàng triển khai dưới dạng
chương trình ứng dụng thông thường. Điều này cho phép coi mạng nền tảng
là một đồ thị đầy đủ kết nối, vì mọi máy chủ trong Internet đều có thể
gửi thông điệp đến mọi máy chủ khác. Trừu tượng hóa, end system
multicast giải quyết bài toán sau: Bắt đầu với một đồ thị đầy đủ kết
nối đại diện cho Internet, mục tiêu là tìm cây multicast nhúng bao phủ
tất cả các thành viên nhóm.

Lưu ý rằng có một phiên bản đơn giản hơn của bài toán này, được hỗ trợ
bởi sự sẵn có dễ dàng của các máy ảo cloud-hosted trên toàn thế giới.
Các “end system” hiểu multicast có thể là các máy ảo chạy tại nhiều site.
Vì các site này nổi tiếng và tương đối cố định, có thể xây dựng một cây
multicast tĩnh trong đám mây, và các máy chủ đầu cuối thực tế chỉ cần kết
nối đến vị trí cloud gần nhất. Nhưng để đầy đủ, phần sau mô tả cách tiếp
cận ở dạng tổng quát nhất.

.. _fig-topology:
.. figure:: figures/f09-21-9780123850591.png
   :width: 400px
   :align: center

   Các cây multicast thay thế được ánh xạ lên một topo vật lý.

Vì chúng ta coi Internet nền tảng là đầy đủ kết nối, một giải pháp ngây
thơ là để mỗi nguồn kết nối trực tiếp đến từng thành viên nhóm. Nói cách
khác, end system multicast có thể được triển khai bằng cách để mỗi nút
gửi thông điệp unicast đến mọi thành viên nhóm. Để thấy vấn đề của cách
làm này, đặc biệt so với việc triển khai IP multicast trong router, hãy
xem topo ví dụ trong :numref:`Hình %s <fig-topology>`.
:numref:`Hình %s <fig-topology>` mô tả một topo vật lý ví dụ, nơi R1 và
R2 là các router được kết nối bởi một liên kết xuyên lục địa băng thông
thấp; A, B, C, và D là các máy chủ đầu cuối; và độ trễ liên kết được cho
dưới dạng trọng số cạnh. Giả sử A muốn gửi một thông điệp multicast đến
ba máy chủ còn lại, :numref:`Hình %s <fig-topology>` cho thấy cách truyền
unicast ngây thơ sẽ hoạt động. Rõ ràng đây là điều không mong muốn vì
cùng một thông điệp phải đi qua liên kết A-R1 ba lần, và hai bản sao của
thông điệp đi qua R1-R2. :numref:`Hình %s <fig-topology>` mô tả cây IP
multicast được xây dựng bởi Distance Vector Multicast Routing Protocol
(DVMRP). Rõ ràng, cách tiếp cận này loại bỏ các thông điệp dư thừa.
Không có sự hỗ trợ từ các router, điều tốt nhất có thể hy vọng với end
system multicast là một cây tương tự như cây được hiển thị trong
:numref:`Hình %s <fig-topology>`. End system multicast định nghĩa một kiến
trúc để xây dựng cây này.

.. _fig-layered-overlays:
.. figure:: figures/f09-22-9780123850591.png
   :width: 300px
   :align: center

   Cây multicast nhúng trong một mạng phủ.

Cách tiếp cận chung là hỗ trợ nhiều lớp mạng phủ, mỗi lớp trích xuất một
đồ thị con từ overlay bên dưới, cho đến khi chúng ta chọn được đồ thị con
mà ứng dụng mong muốn. Đối với end system multicast, điều này diễn ra
trong hai giai đoạn: Đầu tiên, chúng ta xây dựng một overlay *mesh* đơn
giản trên Internet đầy đủ kết nối, sau đó chọn một cây multicast trong
mesh này. Ý tưởng được minh họa trong :numref:`Hình %s
<fig-layered-overlays>`, giả sử bốn máy chủ đầu cuối A, B, C, và D. Bước
đầu tiên là quan trọng: Khi đã chọn được một mesh overlay phù hợp, chỉ
cần chạy một thuật toán định tuyến multicast tiêu chuẩn (ví dụ, DVMRP)
trên đó để xây dựng cây multicast. Chúng ta cũng có thể bỏ qua vấn đề
khả năng mở rộng mà multicast toàn Internet phải đối mặt vì mesh trung
gian có thể được chọn chỉ bao gồm các nút muốn tham gia vào một nhóm
multicast cụ thể.

Chìa khóa để xây dựng mesh overlay trung gian là chọn một topo xấp xỉ
topo vật lý của Internet nền tảng, nhưng chúng ta phải làm điều này mà
không ai nói cho biết Internet nền tảng thực sự trông như thế nào vì chỉ
chạy trên các máy chủ đầu cuối chứ không phải router. Chiến lược chung
là để các máy chủ đầu cuối đo độ trễ roundtrip đến các nút khác và chỉ
thêm liên kết vào mesh khi họ hài lòng với kết quả đo được. Cách làm như
sau.

Đầu tiên, giả sử mesh đã tồn tại, mỗi nút trao đổi danh sách tất cả các
nút khác mà nó tin là thành phần của mesh với các láng giềng kết nối trực
tiếp. Khi một nút nhận được danh sách thành viên từ một láng giềng, nó
kết hợp thông tin đó vào danh sách thành viên của mình và chuyển tiếp danh
sách kết quả cho các láng giềng. Thông tin này cuối cùng lan truyền qua
mesh, giống như trong một giao thức định tuyến vector khoảng cách.

Khi một máy chủ muốn tham gia overlay multicast, nó phải biết địa chỉ IP
của ít nhất một nút khác đã ở trong overlay. Nó gửi một thông điệp “join
mesh” đến nút này. Điều này kết nối nút mới vào mesh bằng một cạnh đến
nút đã biết. Nói chung, nút mới có thể gửi thông điệp join đến nhiều nút
hiện tại, do đó tham gia mesh bằng nhiều liên kết. Khi một nút đã kết nối
vào mesh bằng một tập hợp liên kết, nó định kỳ gửi thông điệp “keepalive”
cho các láng giềng, cho họ biết rằng nó vẫn muốn là thành viên nhóm.

Khi một nút rời nhóm, nó gửi thông điệp “leave mesh” cho các láng giềng
kết nối trực tiếp, và thông tin này được lan truyền đến các nút khác trong
mesh qua danh sách thành viên như mô tả ở trên. Ngoài ra, một nút có thể
bị lỗi hoặc chỉ đơn giản quyết định rời nhóm mà không báo trước, trong
trường hợp đó các láng giềng phát hiện ra rằng nó không còn gửi thông điệp
“keep alive” nữa. Một số nút rời đi không ảnh hưởng nhiều đến mesh, nhưng
nếu một nút phát hiện mesh bị phân mảnh do một nút rời đi, nó tạo một cạnh
mới đến một nút ở phân mảnh khác bằng cách gửi thông điệp “join mesh”. Lưu
ý rằng nhiều láng giềng có thể đồng thời quyết định rằng mesh đã bị phân
mảnh, dẫn đến nhiều cạnh nối chéo được thêm vào mesh.

Như mô tả đến đây, chúng ta sẽ có một mesh là đồ thị con của Internet đầy
đủ kết nối ban đầu, nhưng có thể hiệu năng chưa tối ưu vì (1) lựa chọn
láng giềng ban đầu thêm các liên kết ngẫu nhiên vào topo, (2) sửa chữa
phân mảnh có thể thêm các cạnh cần thiết lúc đó nhưng không hữu ích lâu
dài, (3) thành viên nhóm có thể thay đổi do các nút động tham gia/rời đi,
và (4) điều kiện mạng nền tảng có thể thay đổi. Điều cần làm là hệ thống
phải đánh giá giá trị của từng cạnh, dẫn đến việc thêm các cạnh mới vào
mesh và loại bỏ các cạnh hiện có theo thời gian.

Để thêm cạnh mới, mỗi nút *i* định kỳ thăm dò một thành viên ngẫu nhiên
*j* mà nó chưa kết nối trong mesh, đo độ trễ round-trip của cạnh *(i,j)*,
sau đó đánh giá lợi ích của việc thêm cạnh này. Nếu lợi ích vượt quá một
ngưỡng nhất định, liên kết *(i,j)* được thêm vào mesh. Đánh giá lợi ích
của việc thêm cạnh *(i,j)* có thể như sau:

::

   EvaluateUtility(j)
       utility = 0
       for each member m not equal to i
           CL = current latency to node m along route through mesh
           NL = new latency to node m along mesh if edge (i,j) is added}
           if (NL < CL) then
               utility += (CL - NL)/CL
       return utility

Việc quyết định loại bỏ một cạnh cũng tương tự, ngoại trừ mỗi nút *i* tính
toán chi phí của từng liên kết đến láng giềng hiện tại *j* như sau:

::

   EvaluateCost(j)
       Cost[i,j] = number of members for which i uses j as next hop
       Cost[j,i] = number of members for which j uses i as next hop
       return max(Cost[i,j], Cost[j,i])

Sau đó nó chọn láng giềng có chi phí thấp nhất, và loại bỏ nếu chi phí
thấp hơn một ngưỡng nhất định.

Cuối cùng, vì mesh được duy trì bằng một giao thức gần giống vector khoảng
cách, việc chạy DVMRP để tìm cây multicast phù hợp trong mesh là điều đơn
giản. Lưu ý rằng, mặc dù không thể chứng minh giao thức vừa mô tả sẽ tạo
ra mạng mesh tối ưu, cho phép DVMRP chọn cây multicast tốt nhất, cả mô
phỏng và kinh nghiệm thực tế đều cho thấy nó hoạt động khá tốt.

Mạng phủ chống chịu lỗi (Resilient Overlay Networks)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một chức năng khác mà overlay có thể thực hiện là tìm các tuyến thay thế
cho các ứng dụng unicast truyền thống. Các overlay như vậy khai thác quan
sát rằng bất đẳng thức tam giác không đúng trong Internet.
:numref:`Hình %s <fig-triangle>` minh họa điều này. Không hiếm khi tìm
thấy ba site trong Internet—gọi là A, B, và C—sao cho độ trễ giữa A và B
lớn hơn tổng độ trễ từ A đến C và từ C đến B. Tức là, đôi khi bạn sẽ tốt
hơn nếu gửi gói tin gián tiếp qua một nút trung gian thay vì gửi trực tiếp
đến đích.

.. _fig-triangle:
.. figure:: figures/f09-23-9780123850591.png
   :width: 600px
   :align: center

   Bất đẳng thức tam giác không nhất thiết đúng trong mạng.

Tại sao lại như vậy? Giao thức Border Gateway Protocol (BGP) chưa bao giờ
hứa sẽ tìm tuyến *ngắn nhất* giữa hai site bất kỳ; nó chỉ cố gắng tìm
*một* tuyến nào đó. Làm cho vấn đề phức tạp hơn, các tuyến của BGP bị ảnh
hưởng mạnh bởi các vấn đề chính sách, như ai trả tiền cho ai để chuyển
lưu lượng của họ. Điều này thường xảy ra, ví dụ, tại các điểm peering giữa
các ISP backbone lớn. Tóm lại, việc bất đẳng thức tam giác không đúng
trong Internet không có gì đáng ngạc nhiên.

Chúng ta khai thác quan sát này như thế nào? Bước đầu tiên là nhận ra có
một sự đánh đổi cơ bản giữa khả năng mở rộng và tối ưu của thuật toán định
tuyến. Một mặt, BGP mở rộng đến các mạng rất lớn, nhưng thường không chọn
được tuyến tốt nhất và phản ứng chậm với sự cố mạng. Mặt khác, nếu bạn chỉ
quan tâm đến việc tìm tuyến tốt nhất giữa một số ít site, bạn có thể làm
tốt hơn nhiều trong việc giám sát chất lượng mọi đường đi có thể dùng, cho
phép bạn chọn tuyến tốt nhất tại mọi thời điểm.

Một overlay thử nghiệm, gọi là Resilient Overlay Network (RON), đã làm
chính xác điều này. RON chỉ mở rộng đến vài chục nút vì nó sử dụng chiến
lược *N × N* để giám sát chặt chẽ (bằng thăm dò chủ động) ba khía cạnh
chất lượng đường đi—độ trễ, băng thông khả dụng, và xác suất mất gói—
giữa mọi cặp site. Nhờ đó, nó có thể vừa chọn tuyến tối ưu giữa mọi cặp
nút, vừa nhanh chóng thay đổi tuyến khi điều kiện mạng thay đổi. Kinh
nghiệm cho thấy RON có thể mang lại cải thiện hiệu năng vừa phải cho các
ứng dụng, nhưng quan trọng hơn, nó phục hồi sau sự cố mạng nhanh hơn rất
nhiều. Ví dụ, trong một khoảng thời gian 64 giờ năm 2001, một instance
RON chạy trên 12 nút phát hiện 32 sự cố kéo dài hơn 30 phút, và có thể
phục hồi tất cả trong chưa đầy 20 giây trung bình. Thí nghiệm này cũng
gợi ý rằng chuyển tiếp dữ liệu qua chỉ một nút trung gian thường đủ để
phục hồi sau sự cố Internet.

Vì RON không được thiết kế để mở rộng, không thể dùng RON để giúp máy A
ngẫu nhiên giao tiếp với máy B ngẫu nhiên; A và B phải biết trước rằng họ
có khả năng giao tiếp và cùng tham gia một RON. Tuy nhiên, RON có vẻ là
ý tưởng tốt trong một số bối cảnh, như khi kết nối vài chục site doanh
nghiệp trải rộng trên Internet hoặc cho phép bạn và 50 người bạn lập overlay
riêng để chạy một ứng dụng nào đó. (Ngày nay, ý tưởng này được áp dụng
thực tế với tên gọi tiếp thị *Software-Defined WAN*, hay *SD-WAN*.) Câu
hỏi thực sự là điều gì xảy ra khi ai cũng chạy RON riêng của mình. Liệu
việc hàng triệu RON thăm dò đường đi tích cực có làm ngập mạng, và liệu
có ai thấy hành vi cải thiện khi nhiều RON cạnh tranh cùng đường đi?
Những câu hỏi này vẫn chưa có lời giải.

.. _key-virtualization:
.. admonition:: Ý chính

   Tất cả các overlay này minh họa một khái niệm trung tâm của mạng máy
   tính nói chung: *ảo hóa* (virtualization). Tức là, có thể xây dựng
   một mạng ảo từ các tài nguyên trừu tượng (logic) trên một mạng vật lý
   được xây dựng từ các tài nguyên vật lý. Hơn nữa, có thể xếp chồng các
   mạng ảo này lên nhau và cho nhiều mạng ảo cùng tồn tại ở cùng một
   tầng. Mỗi mạng ảo, đến lượt nó, cung cấp các khả năng mới có giá trị
   cho một tập người dùng, ứng dụng, hoặc mạng tầng cao hơn nào đó.
   :ref:`[Next] <key-nested>`

9.4.2 Mạng ngang hàng (Peer-to-Peer Networks)
---------------------------------------------

Các ứng dụng chia sẻ nhạc như Napster và KaZaA đã đưa thuật ngữ
“peer-to-peer” vào ngôn ngữ phổ thông. Nhưng thực sự “peer-to-peer” có
nghĩa là gì? Chắc chắn trong bối cảnh chia sẻ file MP3 nó có nghĩa là
không phải tải nhạc từ một site trung tâm, mà thay vào đó có thể truy cập
file nhạc trực tiếp từ bất kỳ ai trên Internet tình cờ có bản sao lưu trữ
trên máy tính của họ. Nói rộng hơn, chúng ta có thể nói rằng một mạng
peer-to-peer cho phép một cộng đồng người dùng gộp tài nguyên của họ (nội
dung, lưu trữ, băng thông mạng, băng thông đĩa, CPU), nhờ đó cung cấp
khả năng truy cập kho lưu trữ lớn hơn, hội nghị video/audio lớn hơn, tìm
kiếm và tính toán phức tạp hơn, v.v. so với bất kỳ người dùng đơn lẻ nào
có thể tự mình chi trả.

Rất thường xuyên, các thuộc tính như *phi tập trung* (decentralized) và
*tự tổ chức* (self-organizing) được nhắc đến khi bàn về mạng peer-to-peer,
nghĩa là các nút tự tổ chức thành mạng mà không cần điều phối tập trung.
Nếu bạn nghĩ kỹ, các thuật ngữ này cũng có thể dùng để mô tả chính
Internet. Trớ trêu thay, Napster không phải là hệ thống peer-to-peer thực
sự theo định nghĩa này vì nó phụ thuộc vào một registry trung tâm các file
đã biết, và người dùng phải tìm kiếm trong thư mục này để biết máy nào
cung cấp file cụ thể. Chỉ bước cuối cùng—thực sự tải file—diễn ra giữa
hai máy của hai người dùng, nhưng đây chỉ là một giao dịch client/server
truyền thống. Khác biệt duy nhất là server thuộc sở hữu của ai đó giống
bạn thay vì một tập đoàn lớn.

Vậy chúng ta quay lại câu hỏi ban đầu: Điều gì thú vị ở mạng peer-to-peer?
Một câu trả lời là cả quá trình xác định vị trí một đối tượng quan tâm và
quá trình tải đối tượng đó về máy cục bộ đều diễn ra mà không cần liên hệ
với một cơ quan trung tâm, đồng thời hệ thống có thể mở rộng đến hàng
triệu nút. Một hệ thống peer-to-peer có thể thực hiện hai nhiệm vụ này
một cách phi tập trung hóa ra lại là một mạng phủ, nơi các nút là các máy
chủ sẵn sàng chia sẻ đối tượng quan tâm (ví dụ, nhạc và các file khác),
và các liên kết (tunnel) kết nối các nút này đại diện cho chuỗi máy bạn
phải ghé qua để truy tìm đối tượng mong muốn. Mô tả này sẽ rõ ràng hơn
sau khi chúng ta xem hai ví dụ.

Gnutella
~~~~~~~~

Gnutella là một mạng peer-to-peer đầu tiên cố gắng phân biệt giữa việc
trao đổi nhạc (có thể vi phạm bản quyền của ai đó) và chia sẻ file nói
chung (chắc chắn là tốt vì chúng ta được dạy chia sẻ từ nhỏ). Điều thú vị
ở Gnutella là nó là một trong những hệ thống đầu tiên không phụ thuộc vào
registry trung tâm các đối tượng. Thay vào đó, các thành viên Gnutella tự
sắp xếp thành một mạng phủ giống như :numref:`Hình %s <fig-gnutella>`.
Tức là, mỗi nút chạy phần mềm Gnutella (tức là hiện thực giao thức
Gnutella) biết về một tập hợp các máy khác cũng chạy phần mềm Gnutella.
Quan hệ “A và B biết nhau” tương ứng với các cạnh trong đồ thị này. (Chúng
ta sẽ nói về cách đồ thị này hình thành sau.)

.. _fig-gnutella:
.. figure:: figures/f09-24-9780123850591.png
   :width: 300px
   :align: center

   Topo ví dụ của một mạng peer-to-peer Gnutella.

Bất cứ khi nào người dùng trên một nút muốn tìm một đối tượng, Gnutella
gửi một thông điệp QUERY cho đối tượng đó—ví dụ, chỉ định tên file—đến các
láng giềng trong đồ thị. Nếu một trong các láng giềng có đối tượng, nó
phản hồi cho nút gửi truy vấn bằng thông điệp QUERY RESPONSE, chỉ ra nơi
có thể tải đối tượng (ví dụ, địa chỉ IP và cổng TCP). Nút đó sau đó có
thể dùng thông điệp GET hoặc PUT để truy cập đối tượng. Nếu nút không thể
giải quyết truy vấn, nó chuyển tiếp thông điệp QUERY đến từng láng giềng
(trừ nút đã gửi truy vấn), và quá trình lặp lại. Nói cách khác, Gnutella
flood overlay để tìm đối tượng mong muốn. Gnutella đặt TTL cho mỗi truy
vấn để flood không kéo dài vô hạn.

Ngoài TTL và chuỗi truy vấn, mỗi thông điệp QUERY chứa một định danh truy
vấn duy nhất (QID), nhưng không chứa danh tính nguồn gốc thông điệp. Thay
vào đó, mỗi nút duy trì bản ghi các thông điệp QUERY đã thấy gần đây: cả
QID và láng giềng đã gửi QUERY. Nó dùng lịch sử này theo hai cách. Thứ
nhất, nếu nó nhận được QUERY có QID trùng với cái đã thấy gần đây, nút sẽ
không chuyển tiếp QUERY. Điều này giúp cắt vòng lặp chuyển tiếp nhanh hơn
TTL. Thứ hai, bất cứ khi nào nút nhận được QUERY RESPONSE từ láng giềng
phía dưới, nó biết phải chuyển tiếp phản hồi cho láng giềng phía trên đã
gửi QUERY. Nhờ đó, phản hồi quay lại nút gốc mà không nút trung gian nào
biết ai là người muốn tìm đối tượng này ban đầu.

Quay lại câu hỏi về cách đồ thị phát triển, một nút chắc chắn phải biết
ít nhất một nút khác khi tham gia overlay Gnutella. Nút mới được gắn vào
overlay bằng ít nhất một liên kết này. Sau đó, một nút học về các nút khác
nhờ các thông điệp QUERY RESPONSE, cả cho các đối tượng nó yêu cầu và các
phản hồi tình cờ đi qua nó. Một nút tự do quyết định giữ nút nào trong số
các nút phát hiện được làm láng giềng. Giao thức Gnutella cung cấp các
thông điệp PING và PONG để một nút kiểm tra xem láng giềng còn tồn tại
không và nhận phản hồi tương ứng.

Rõ ràng Gnutella như mô tả ở đây không phải là giao thức đặc biệt thông
minh, và các hệ thống sau này đã cố gắng cải thiện nó. Một hướng cải tiến
là cách truyền truy vấn. Flooding có ưu điểm là đảm bảo tìm được đối tượng
mong muốn với số hop ít nhất, nhưng không mở rộng tốt. Có thể chuyển tiếp
truy vấn ngẫu nhiên, hoặc theo xác suất thành công dựa trên kết quả quá
khứ. Một hướng khác là chủ động sao chép đối tượng, vì càng nhiều bản sao
của một đối tượng thì càng dễ tìm thấy. Hoặc có thể phát triển một chiến
lược hoàn toàn khác, sẽ được bàn tiếp theo.

Overlay có cấu trúc (Structured Overlays)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Cùng lúc các hệ thống chia sẻ file cố gắng lấp đầy khoảng trống Napster
để lại, cộng đồng nghiên cứu bắt đầu khám phá một thiết kế thay thế cho
mạng peer-to-peer. Chúng tôi gọi các mạng này là *có cấu trúc*
(structured), để phân biệt với cách phát triển ngẫu nhiên (không cấu
trúc) của mạng Gnutella. Overlay không cấu trúc như Gnutella dùng thuật
toán xây dựng và duy trì overlay đơn giản, nhưng chỉ cung cấp tìm kiếm
ngẫu nhiên, không đáng tin cậy. Ngược lại, overlay có cấu trúc được thiết
kế để tuân theo một cấu trúc đồ thị cụ thể cho phép xác định vị trí đối
tượng đáng tin cậy và hiệu quả (độ trễ được giới hạn xác suất), đổi lại
là tăng độ phức tạp khi xây dựng và duy trì overlay.

Nếu bạn nghĩ về mục tiêu ở mức cao, có hai câu hỏi cần xem xét: (1) Làm
thế nào để ánh xạ đối tượng lên các nút, và (2) Làm thế nào để định tuyến
một yêu cầu đến nút chịu trách nhiệm cho một đối tượng nhất định? Bắt đầu
với câu hỏi đầu tiên, có thể phát biểu đơn giản: Làm thế nào để ánh xạ
một đối tượng có tên *x* thành địa chỉ của một nút *n* có thể phục vụ đối
tượng đó? Trong khi mạng peer-to-peer truyền thống không kiểm soát được
nút nào lưu trữ đối tượng *x*, nếu chúng ta kiểm soát được cách phân phối
đối tượng trên mạng, có thể sẽ tìm được đối tượng dễ dàng hơn sau này.

Một kỹ thuật nổi tiếng để ánh xạ tên thành địa chỉ là dùng bảng băm
(hash table), sao cho

.. centered:: *hash(x) → n*

ngụ ý đối tượng *x* được đặt trên nút *n*, và sau này client muốn tìm *x*
chỉ cần băm *x* để xác định nó nằm trên nút *n*. Cách tiếp cận dựa trên
băm có ưu điểm là phân phối đối tượng đều trên tập nút, nhưng thuật toán
băm thông thường gặp vấn đề nghiêm trọng: Cho phép bao nhiêu giá trị *n*?
(Nói theo thuật ngữ băm, có bao nhiêu bucket?) Ngây thơ, ta có thể quyết
định có, ví dụ, 101 giá trị băm, và dùng hàm băm modulo; tức là,

::

   hash(x)
       return x % 101

Không may, nếu có nhiều hơn 101 nút sẵn sàng lưu trữ đối tượng, thì không
tận dụng được tất cả. Ngược lại, nếu chọn số lớn hơn số nút tối đa, sẽ có
giá trị *x* băm ra địa chỉ không tồn tại. Cũng có vấn đề không nhỏ là
chuyển giá trị trả về của hàm băm thành địa chỉ IP thực tế.

.. _fig-unitcircle:
.. figure:: figures/f09-25-9780123850591.png
   :width: 300px
   :align: center

   Cả nút và đối tượng đều được băm vào không gian ID, đối tượng được lưu
   tại nút gần nhất trong không gian này.

Để giải quyết các vấn đề này, mạng peer-to-peer có cấu trúc dùng thuật
toán gọi là *consistent hashing*, băm tập đối tượng *x* đều trên một
không gian ID lớn. :numref:`Hình %s <fig-unitcircle>` hình dung không gian
ID 128-bit như một vòng tròn, nơi thuật toán được dùng để đặt cả đối tượng

.. centered:: *hash(ObjectName) → ObjectID*

và nút

.. centered:: *hash(IPAddr) → NodeID*

lên vòng tròn này. Vì không gian ID 128-bit là rất lớn, khó có khả năng
một đối tượng băm ra đúng ID mà IP của máy nào đó băm ra. Để xử lý điều
này, mỗi đối tượng được lưu trên nút có ID *gần nhất* trong không gian
128-bit với ID của đối tượng. Nói cách khác, dùng hàm băm chất lượng cao
để ánh xạ cả nút và đối tượng vào cùng một không gian ID lớn, thưa; sau
đó ánh xạ đối tượng vào nút dựa trên khoảng cách số học giữa các ID. Như
băm thông thường, cách này phân phối đối tượng khá đều trên các nút,
nhưng, không như băm thông thường, chỉ một số ít đối tượng phải di chuyển
khi một nút (bucket) tham gia hoặc rời đi.

.. _fig-locate:
.. figure:: figures/f09-26-9780123850591.png
   :width: 300px
   :align: center

   Đối tượng được xác định vị trí bằng cách định tuyến qua mạng phủ
   peer-to-peer.

Chuyển sang câu hỏi thứ hai—làm sao người dùng muốn truy cập đối tượng *x*
biết nút nào gần nhất với ID của *x* trong không gian này? Một câu trả lời
có thể là mỗi nút giữ bảng đầy đủ các ID nút và địa chỉ IP tương ứng, nhưng
điều này không thực tế với mạng lớn. Cách thay thế, được mạng peer-to-peer
có cấu trúc sử dụng, là *định tuyến thông điệp đến nút này!* Nói cách
khác, nếu xây dựng overlay một cách thông minh—tức là chọn các mục bảng
định tuyến của nút một cách thông minh—thì có thể tìm nút chỉ bằng cách
định tuyến dần về phía nó. Cách tiếp cận này đôi khi được gọi là *bảng
băm phân tán* (DHT), vì về mặt khái niệm, bảng băm được phân tán trên tất
cả các nút trong mạng.

:numref:`Hình %s <fig-locate>` minh họa điều gì xảy ra với không gian ID
28-bit đơn giản. Để thảo luận cụ thể, ta xét cách tiếp cận của mạng
peer-to-peer gọi là *Pastry*. Các hệ thống khác hoạt động tương tự.

Giả sử bạn ở nút có id ``65a1fc`` (hex) và muốn tìm đối tượng có ID
``d46a1c``. Bạn nhận ra ID của mình không chung gì với đối tượng, nhưng
biết một nút có chung ít nhất tiền tố ``d``. Nút đó gần hơn bạn trong
không gian ID 128-bit, nên bạn chuyển tiếp thông điệp cho nó. (Chúng tôi
không đưa ra định dạng thông điệp, nhưng bạn có thể nghĩ nó như “locate
object ``d46a1c``”.) Giả sử nút ``d13da3`` biết một nút khác có tiền tố
chung dài hơn với đối tượng, nó lại chuyển tiếp thông điệp. Quá trình này
tiếp tục cho đến khi đến một nút không biết nút nào gần hơn. Nút này, theo
định nghĩa, là nút lưu trữ đối tượng. Lưu ý rằng khi di chuyển logic qua
“không gian ID”, thông điệp thực tế được chuyển tiếp, nút này sang nút
khác, qua Internet nền tảng.

Mỗi nút duy trì cả bảng định tuyến (sẽ nói sau) và địa chỉ IP của một tập
hợp nhỏ các ID nút lớn hơn và nhỏ hơn về số học. Đây gọi là *leaf set* của
nút. Leaf set có ý nghĩa là, khi thông điệp được định tuyến đến bất kỳ nút
nào trong leaf set của nút lưu trữ đối tượng, nút đó có thể chuyển tiếp
trực tiếp đến đích cuối cùng. Nói cách khác, leaf set giúp chuyển thông
điệp đúng và hiệu quả đến nút gần nhất về số học, dù có thể có nhiều nút
chung tiền tố dài nhất với ID đối tượng. Hơn nữa, leaf set làm cho định
tuyến bền vững hơn vì bất kỳ nút nào trong leaf set đều có thể định tuyến
thông điệp tốt như nhau. Nếu một nút không thể tiếp tục định tuyến, một
láng giềng trong leaf set có thể làm thay. Tóm lại, thủ tục định tuyến
được định nghĩa như sau:

::

   Route(D)
       if D is within range of my leaf set
           forward to numerically closest member in leaf set
       else
           let l = length of shared prefix
           let d = value of l-th digit in D's address
           if RouteTab[l,d] exists
               forward to RouteTab[l,d]
           else
               forward to known node with at least as long a shared prefix
               and numerically closer than this node

Bảng định tuyến, ký hiệu ``RouteTab``, là một mảng hai chiều. Nó có một
dòng cho mỗi chữ số hex trong ID (có 32 chữ số như vậy trong ID 128-bit)
và một cột cho mỗi giá trị hex (rõ ràng có 16 giá trị). Mỗi mục ở dòng *i*
chung tiền tố độ dài *i* với nút này, và trong dòng đó, mục ở cột *j* có
giá trị hex *j* ở vị trí *i+1*. :numref:`Hình %s <fig-rt>` cho thấy ba
dòng đầu của bảng định tuyến ví dụ cho nút ``65a1fcx``, với *x* là hậu tố
không xác định. Hình này cho thấy tiền tố ID được mỗi mục trong bảng khớp.
Không hiển thị giá trị thực tế trong mục—đó là địa chỉ IP của nút tiếp
theo để định tuyến.

.. _fig-rt:
.. figure:: figures/f09-27-9780123850591.png
   :width: 500px
   :align: center

   Bảng định tuyến ví dụ tại nút có ID 65alcx

.. _fig-addnode:
.. figure:: figures/f09-28-9780123850591.png
   :width: 500px
   :align: center

   Thêm một nút vào mạng.

Thêm một nút vào overlay hoạt động giống như định tuyến thông điệp “locate
object” đến một đối tượng. Nút mới phải biết ít nhất một thành viên hiện
tại. Nó yêu cầu thành viên này định tuyến thông điệp “add node” đến nút
gần nhất về số học với ID của nút tham gia, như minh họa trong
:numref:`Hình %s <fig-addnode>`. Qua quá trình định tuyến này, nút mới học
về các nút có tiền tố chung và bắt đầu điền bảng định tuyến. Theo thời
gian, khi các nút khác tham gia overlay, các nút hiện có cũng có thể thêm
thông tin về nút mới vào bảng định tuyến nếu nút mới thêm tiền tố dài hơn
so với bảng hiện tại. Các láng giềng trong leaf set cũng trao đổi bảng
định tuyến với nhau, nghĩa là theo thời gian thông tin định tuyến lan
truyền qua overlay.

Bạn đọc có thể nhận thấy rằng mặc dù overlay có cấu trúc cung cấp giới hạn
xác suất về số hop định tuyến cần để xác định vị trí một đối tượng—số hop
trong Pastry bị giới hạn bởi :math:`log_{16}N`, với N là số nút trong
overlay—mỗi hop có thể góp phần đáng kể vào độ trễ. Đó là vì mỗi nút trung
gian có thể ở vị trí ngẫu nhiên trên Internet. (Trường hợp xấu nhất, mỗi
nút ở một châu lục khác nhau!) Thực tế, trong một overlay toàn cầu dùng
thuật toán như trên, độ trễ kỳ vọng của mỗi hop là trung bình độ trễ giữa
mọi cặp nút trên Internet! May mắn thay, có thể làm tốt hơn nhiều trong
thực tế. Ý tưởng là chọn mỗi mục bảng định tuyến sao cho nó trỏ đến một
nút gần về vật lý trong mạng nền tảng, trong số các nút có tiền tố ID phù
hợp cho mục đó. Làm như vậy đạt được độ trễ định tuyến đầu-cuối chỉ lớn
hơn một chút so với độ trễ giữa nguồn và đích.

Cuối cùng, thảo luận đến đây tập trung vào bài toán tổng quát xác định vị
trí đối tượng trong mạng peer-to-peer. Với hạ tầng định tuyến như vậy, có
thể xây dựng các dịch vụ khác nhau. Ví dụ, dịch vụ chia sẻ file sẽ dùng
tên file làm tên đối tượng. Để xác định vị trí file, bạn băm tên file ra
ID đối tượng tương ứng rồi định tuyến thông điệp “locate object” đến ID
này. Hệ thống cũng có thể sao chép mỗi file trên nhiều nút để tăng khả
dụng. Lưu nhiều bản trên leaf set của nút mà file thường được định tuyến
đến là một cách. Lưu ý rằng dù các nút này là láng giềng trong không gian
ID, chúng có thể phân tán vật lý trên Internet. Do đó, mất điện toàn thành
phố có thể làm sập các bản sao vật lý gần nhau trong hệ thống file truyền
thống, nhưng một hoặc nhiều bản sao vẫn sống sót trong mạng peer-to-peer.

Các dịch vụ khác ngoài chia sẻ file cũng có thể xây dựng trên bảng băm
phân tán. Xét ứng dụng multicast, chẳng hạn. Thay vì xây dựng cây
multicast từ mesh, có thể xây dựng cây từ các cạnh trong overlay có cấu
trúc, nhờ đó phân bổ chi phí xây dựng và duy trì overlay cho nhiều ứng
dụng và nhóm multicast.

BitTorrent
~~~~~~~~~~

BitTorrent là một giao thức chia sẻ file peer-to-peer do Bram Cohen phát
triển. Nó dựa trên việc sao chép file hoặc, đúng hơn, sao chép các đoạn
file, gọi là *piece*. Bất kỳ piece nào thường có thể tải từ nhiều peer,
dù chỉ một peer có toàn bộ file. Lợi ích chính của việc sao chép trong
BitTorrent là tránh nút cổ chai khi chỉ có một nguồn cho file. Điều này
đặc biệt hữu ích khi xét rằng bất kỳ máy tính nào cũng có tốc độ phục vụ
file qua uplink Internet giới hạn, thường khá thấp do tính bất đối xứng
của hầu hết mạng băng rộng. Vẻ đẹp của BitTorrent là việc sao chép diễn
ra tự nhiên trong quá trình tải: Ngay khi một peer tải xong một piece, nó
trở thành nguồn cho piece đó. Càng nhiều peer tải các piece, càng nhiều
piece được sao chép, phân bổ tải hợp lý, và càng nhiều tổng băng thông để
chia sẻ file với người khác. Các piece được tải theo thứ tự ngẫu nhiên để
tránh tình trạng các peer đều thiếu cùng một tập piece.

Mỗi file được chia sẻ qua một mạng BitTorrent độc lập, gọi là *swarm*.
(Một swarm có thể chia sẻ một tập file, nhưng ta mô tả trường hợp một file
cho đơn giản.) Vòng đời của một swarm điển hình như sau. Swarm bắt đầu là
một peer đơn lẻ có bản sao đầy đủ file. Một nút muốn tải file tham gia
swarm, trở thành thành viên thứ hai, và bắt đầu tải các piece từ peer gốc.
Khi làm vậy, nó trở thành nguồn cho các piece đã tải, dù chưa tải xong
toàn bộ file. (Thực tế, peer thường rời swarm sau khi tải xong, dù được
khuyến khích ở lại lâu hơn.) Các nút khác tham gia swarm và bắt đầu tải
piece từ nhiều peer, không chỉ peer gốc. Xem :numref:`Hình %s
<fig-bitTorrentSwarm>`.

.. _fig-bitTorrentSwarm:
.. figure:: figures/f09-29-9780123850591.png
   :width: 500px
   :align: center

   Các peer trong một swarm BitTorrent tải từ các peer khác có thể chưa có
   toàn bộ file.

Nếu file vẫn được nhiều người quan tâm, với dòng peer mới thay thế những
người rời swarm, swarm có thể hoạt động vô thời hạn; nếu không, nó có thể
thu nhỏ lại chỉ còn peer gốc cho đến khi có peer mới tham gia.

Sau khi có cái nhìn tổng quan về BitTorrent, ta có thể hỏi làm sao các
yêu cầu được định tuyến đến peer có piece mong muốn. Để gửi yêu cầu, một
người muốn tải phải tham gia swarm. Nó bắt đầu bằng cách tải một file chứa
meta-information về file và swarm. File này, có thể được sao chép dễ dàng,
thường được tải từ web server và tìm thấy qua các liên kết trên trang web.
Nó chứa:

-  Kích thước file mục tiêu

-  Kích thước piece

-  Giá trị băm SHA-1 tính trước cho từng piece

-  URL của *tracker* của swarm

Tracker là một server theo dõi thành viên hiện tại của swarm. Ta sẽ thấy
sau này BitTorrent có thể mở rộng để loại bỏ điểm tập trung này, cùng nguy
cơ nghẽn hoặc lỗi.

Người muốn tải sau đó tham gia swarm, trở thành peer, bằng cách gửi thông
điệp cho tracker với địa chỉ mạng và peer ID tự sinh ngẫu nhiên. Thông
điệp cũng mang băm SHA-1 của phần chính file, dùng làm swarm ID.

Gọi peer mới là P. Tracker trả lời P với danh sách một phần các peer gồm
ID và địa chỉ mạng, và P thiết lập kết nối TCP với một số peer này. Lưu ý
P chỉ kết nối trực tiếp với một tập con của swarm, dù có thể liên hệ thêm
peer hoặc yêu cầu tracker cung cấp thêm. Để thiết lập kết nối BitTorrent
với một peer sau khi đã có kết nối TCP, P gửi peer ID và swarm ID của mình,
peer kia trả lời với peer ID và swarm ID của nó. Nếu swarm ID không khớp,
hoặc peer ID trả về không đúng mong đợi, kết nối bị hủy.

Kết nối BitTorrent kết quả là đối xứng: Mỗi bên có thể tải từ bên kia. Mỗi
bên bắt đầu bằng cách gửi bitmap báo piece mình có, để mỗi peer biết trạng
thái ban đầu của bên kia. Bất cứ khi nào downloader (D) tải xong một piece,
nó gửi thông điệp xác định piece đó cho từng peer kết nối trực tiếp, để
các peer cập nhật trạng thái của D. Đây chính là câu trả lời cho câu hỏi
làm sao yêu cầu tải piece được định tuyến đến peer có piece, vì mỗi peer
biết peer kết nối trực tiếp nào có piece. Nếu D cần piece mà không peer
nào kết nối có, nó có thể kết nối thêm peer (có thể lấy thêm từ tracker)
hoặc tải các piece khác hy vọng peer kết nối sẽ lấy được piece từ các peer
khác của họ.

Đối tượng—ở đây là piece—được ánh xạ lên các peer như thế nào? Thực ra mỗi
peer cuối cùng sẽ có tất cả piece, nên câu hỏi là peer có piece nào tại
một thời điểm trước khi có đủ, hoặc tương đương, thứ tự tải piece. Câu trả
lời là họ tải piece theo thứ tự ngẫu nhiên, để tránh có tập piece là tập
con hoặc tập cha nghiêm ngặt của peer khác.

BitTorrent mô tả đến đây sử dụng tracker trung tâm, là điểm lỗi duy nhất
cho swarm và có thể là nút cổ chai hiệu năng. Ngoài ra, cung cấp tracker
có thể phiền phức cho ai muốn chia sẻ file qua BitTorrent. Các phiên bản
BitTorrent mới hơn hỗ trợ swarm “không tracker” dùng hiện thực dựa trên
DHT. Phần mềm client BitTorrent hỗ trợ trackerless hiện thực không chỉ
peer BitTorrent mà còn cái gọi là *peer finder* (BitTorrent gọi đơn giản
là *node*), peer dùng để tìm peer.

Peer finder tạo mạng phủ riêng, dùng giao thức riêng qua UDP để hiện thực
DHT. Hơn nữa, mạng peer finder gồm các peer finder có peer liên kết thuộc
các swarm khác nhau. Nói cách khác, mỗi swarm tạo một mạng peer BitTorrent
riêng, còn mạng peer finder trải rộng các swarm.

Peer finder tự sinh finder ID ngẫu nhiên, cùng kích thước (160 bit) với
swarm ID. Mỗi finder duy trì bảng nhỏ chủ yếu gồm các finder (và peer liên
kết) có ID gần với mình, cộng một số finder ID xa hơn. Thuật toán sau đảm
bảo finder có ID gần swarm ID sẽ biết về peer của swarm đó; đồng thời cung
cấp cách tra cứu. Khi finder F cần tìm peer của swarm, nó gửi yêu cầu cho
các finder trong bảng có ID gần swarm ID. Nếu finder liên hệ biết peer
nào, nó trả về thông tin liên hệ. Nếu không, nó trả về thông tin liên hệ
của các finder trong bảng gần swarm, để F truy vấn tiếp.

Sau khi tìm kiếm cạn kiệt, vì không còn finder nào gần swarm hơn, F chèn
thông tin liên hệ của mình và peer liên kết vào các finder gần swarm nhất.
Kết quả là peer của swarm được đưa vào bảng của các finder gần swarm.

Cách trên giả định F đã là thành viên mạng finder, đã biết cách liên hệ
một số finder khác. Điều này đúng với các cài đặt finder đã chạy trước đó,
vì họ phải lưu thông tin về finder khác, kể cả qua các lần chạy. Nếu swarm
dùng tracker, peer của nó có thể báo cho finder về finder khác (đảo vai
peer và finder) vì giao thức peer BitTorrent đã mở rộng để trao đổi thông
tin liên hệ finder. Nhưng, làm sao finder mới cài biết finder khác? File
cho swarm không tracker chứa thông tin liên hệ một hoặc vài finder, thay
vì tracker URL, cho trường hợp này.

Một điểm đặc biệt của BitTorrent là nó đối mặt trực tiếp với vấn đề công
bằng, hay “công dân mạng” tốt. Giao thức thường phụ thuộc vào hành vi tốt
của peer mà không thể cưỡng chế. Ví dụ, một peer Ethernet không trung thực
có thể đạt hiệu năng tốt hơn bằng thuật toán backoff tích cực hơn exponential
backoff, hoặc peer TCP không trung thực có thể đạt hiệu năng tốt hơn bằng
cách không hợp tác kiểm soát nghẽn.

Hành vi tốt mà BitTorrent phụ thuộc là peer upload piece cho peer khác. Vì
người dùng BitTorrent điển hình chỉ muốn tải file càng nhanh càng tốt, có
cám dỗ hiện thực peer cố tải hết piece mà upload càng ít càng tốt—đây là
peer xấu. Để ngăn hành vi xấu, giao thức BitTorrent có cơ chế cho phép
peer thưởng hoặc phạt nhau. Nếu một peer cư xử xấu bằng cách không upload
cho peer khác, peer thứ hai có thể *choke* peer xấu: quyết định ngừng upload
cho peer xấu, ít nhất tạm thời, và gửi thông điệp báo như vậy. Cũng có loại
thông điệp báo peer đã được unchoke. Cơ chế choke cũng được dùng để peer
giới hạn số kết nối BitTorrent đang hoạt động, để duy trì hiệu năng TCP tốt.
Có nhiều thuật toán choke, và thiết kế một thuật toán tốt là một nghệ thuật.

9.4.3 Mạng phân phối nội dung (Content Distribution Networks)
-------------------------------------------------------------

Chúng ta đã thấy HTTP chạy trên TCP cho phép trình duyệt web lấy trang từ
máy chủ web. Tuy nhiên, ai từng chờ đợi lâu để một trang web trả về đều
biết hệ thống còn xa mới hoàn hảo. Xét rằng backbone Internet giờ được xây
dựng từ các liên kết 40-Gbps, không rõ tại sao lại như vậy. Nói chung,
khi tải trang web có bốn nút cổ chai tiềm năng:

-  *Dặm đầu tiên* (first mile). Internet có thể có liên kết dung lượng
   cao, nhưng điều đó không giúp bạn tải trang nhanh hơn khi bạn kết nối
   bằng đường DSL 1.5Mbps hoặc liên kết không dây kém.

-  *Dặm cuối* (last mile). Liên kết kết nối server với Internet có thể bị
   quá tải bởi quá nhiều yêu cầu, dù tổng băng thông của liên kết khá cao.

-  *Chính server*. Server có lượng tài nguyên hữu hạn (CPU, RAM, băng
   thông đĩa, v.v.) và có thể bị quá tải bởi quá nhiều yêu cầu đồng thời.

-  *Điểm peering*. Một số ít ISP cùng nhau tạo backbone Internet có thể có
   đường ống băng thông cao nội bộ, nhưng họ không có động lực cung cấp
   kết nối dung lượng cao cho các peer. Nếu bạn kết nối ISP A và server
   kết nối ISP B, trang bạn yêu cầu có thể bị drop tại điểm A và B peering.

Không ai ngoài bạn có thể làm gì với vấn đề đầu tiên, nhưng có thể dùng
sao chép để giải quyết các vấn đề còn lại. Các hệ thống làm điều này
thường gọi là *Mạng phân phối nội dung* (Content Distribution Networks,
CDN). Akamai vận hành CDN nổi tiếng nhất.

Ý tưởng của CDN là phân phối địa lý một tập hợp *server surrogate* lưu
cache các trang thường được duy trì ở một tập *backend server*. Nhờ đó,
thay vì hàng triệu người dùng phải chờ khi có tin nóng—tình huống này gọi
là *flash crowd*—có thể phân bổ tải này trên nhiều server. Hơn nữa, thay
vì phải đi qua nhiều ISP để đến ``www.cnn.com``, nếu các server surrogate
được phân bổ trên tất cả ISP backbone, thì có thể đến một server mà không
phải qua điểm peering. Rõ ràng, duy trì hàng nghìn server surrogate trên
Internet là quá đắt cho bất kỳ site nào muốn cung cấp truy cập tốt hơn cho
trang web của mình. CDN thương mại cung cấp dịch vụ này cho nhiều site,
nhờ đó phân bổ chi phí cho nhiều khách hàng.

Dù gọi là server surrogate, thực ra chúng cũng có thể coi là cache. Nếu
không có trang được client yêu cầu, chúng hỏi backend server. Trong thực
tế, backend server chủ động sao chép dữ liệu sang các surrogate thay vì
chờ surrogate yêu cầu khi cần. Cũng chỉ các trang tĩnh, không phải nội
dung động, được phân phối trên surrogate. Client phải đến backend server
cho nội dung thay đổi thường xuyên (ví dụ, điểm thể thao, giá cổ phiếu)
hoặc sinh ra từ tính toán (ví dụ, truy vấn database).

.. _fig-cdn:
.. figure:: figures/f09-30-9780123850591.png
   :width: 600px
   :align: center

   Các thành phần trong một Mạng phân phối nội dung (CDN).

Có một tập server phân phối địa lý lớn chưa giải quyết hết vấn đề. Để hoàn
chỉnh, CDN cũng cần cung cấp một tập *redirector* chuyển tiếp yêu cầu
client đến server phù hợp nhất, như minh họa trong :numref:`Hình %s
<fig-cdn>`. Mục tiêu chính của redirector là chọn server cho mỗi yêu cầu
sao cho *thời gian đáp ứng* cho client là tốt nhất. Mục tiêu phụ là hệ
thống xử lý được nhiều yêu cầu mỗi giây nhất có thể dựa trên phần cứng
(network link và web server) hỗ trợ. Số yêu cầu trung bình có thể đáp ứng
trong một khoảng thời gian—gọi là *throughput hệ thống*—chủ yếu là vấn đề
khi hệ thống quá tải, như khi flash crowd truy cập một tập trang nhỏ hoặc
kẻ tấn công DDoS nhắm vào một site, như đã xảy ra với CNN, Yahoo, và một
số site nổi tiếng khác tháng 2/2000.

CDN dùng nhiều yếu tố để quyết định phân phối yêu cầu client. Ví dụ, để
giảm thời gian đáp ứng, redirector có thể chọn server dựa trên *gần về
mạng* (network proximity). Ngược lại, để tăng throughput toàn hệ thống,
nên cân bằng tải đều trên tập server. Cả throughput và thời gian đáp ứng
đều được cải thiện nếu cơ chế phân phối xét đến *locality*; tức là, chọn
server có khả năng đã có trang được yêu cầu trong cache. Kết hợp chính xác
các yếu tố nào nên dùng trong CDN vẫn còn tranh luận. Phần này xét một số
khả năng.

Cơ chế (Mechanisms)
~~~~~~~~~~~~~~~~~~~

Như mô tả đến đây, redirector chỉ là một hàm trừu tượng, dù nghe giống
việc router có thể làm vì nó logic chuyển tiếp thông điệp yêu cầu giống
như router chuyển tiếp gói tin. Thực tế, có một số cơ chế có thể dùng để
hiện thực chuyển tiếp. Lưu ý, cho mục đích thảo luận này, giả định mỗi
redirector biết địa chỉ mọi server sẵn có. (Từ đây, bỏ từ “surrogate” và
chỉ nói về tập server.) Trong thực tế, một số hình thức trao đổi thông tin
out-of-band diễn ra để cập nhật thông tin khi server đến/đi.

Đầu tiên, chuyển tiếp có thể hiện thực bằng cách tăng cường DNS để trả về
địa chỉ server khác nhau cho client. Ví dụ, khi client hỏi phân giải tên
``www.cnn.com``, DNS server có thể trả về địa chỉ IP của server lưu trang
CNN được biết là tải nhẹ nhất. Hoặc, với một tập server, chỉ cần trả về
địa chỉ theo vòng tròn. Lưu ý, độ hạt của chuyển tiếp dựa trên DNS thường
ở mức site (ví dụ, ``cnn.com``) thay vì URL cụ thể (ví dụ,
``https://www.cnn.com/2020/11/12/politics/biden-wins-arizona/index.html``).
Tuy nhiên, khi trả về liên kết nhúng, server có thể rewrite URL, nhờ đó
chỉ client đến server phù hợp nhất cho đối tượng cụ thể.

CDN thương mại về cơ bản dùng kết hợp rewrite URL và chuyển tiếp dựa trên
DNS. Vì lý do khả năng mở rộng, DNS cấp cao trước tiên trỏ đến DNS cấp
vùng, DNS này trả về địa chỉ server thực tế. Để phản ứng nhanh với thay
đổi, DNS chỉnh TTL của resource record trả về thành thời gian rất ngắn,
ví dụ 20 giây. Điều này cần thiết để client không cache kết quả và do đó
không quay lại DNS lấy ánh xạ URL-server mới nhất.

Một khả năng khác là dùng tính năng HTTP redirect: Client gửi yêu cầu đến
server, server trả về server mới (tốt hơn) mà client nên liên hệ để lấy
trang. Không may, chuyển tiếp dựa trên server gây thêm một round-trip qua
Internet, và tệ hơn, server có thể bị quá tải bởi chính nhiệm vụ chuyển
tiếp. Thay vào đó, nếu có một nút gần client (ví dụ, proxy web cục bộ) biết
các server sẵn có, nó có thể chặn thông điệp yêu cầu và hướng client yêu
cầu trang từ server phù hợp. Trong trường hợp này, redirector phải nằm ở
điểm choke để mọi yêu cầu rời site đi qua nó, hoặc client phải hợp tác bằng
cách gửi yêu cầu trực tiếp cho proxy (như proxy truyền thống, không phải
proxy trong suốt).

Lúc này bạn có thể tự hỏi CDN liên quan gì đến mạng phủ, và dù coi CDN là
overlay có phần gượng ép, chúng chia sẻ một đặc điểm rất quan trọng. Như
một nút overlay, redirector dựa trên proxy đưa ra quyết định định tuyến ở
mức ứng dụng. Thay vì chuyển tiếp gói tin dựa trên địa chỉ và hiểu biết về
topo mạng, nó chuyển tiếp yêu cầu HTTP dựa trên URL và hiểu biết về vị trí
và tải của tập server. Kiến trúc Internet hiện nay không hỗ trợ chuyển tiếp
trực tiếp—ý là client gửi yêu cầu HTTP cho redirector, redirector chuyển
tiếp đến đích—nên chuyển tiếp thường được hiện thực gián tiếp bằng cách
redirector trả về địa chỉ đích phù hợp và client tự liên hệ server.

Chính sách (Policies)
~~~~~~~~~~~~~~~~~~~~~

Giờ xét một số chính sách ví dụ mà redirector có thể dùng để chuyển tiếp
yêu cầu. Thực ra, ta đã gợi ý một chính sách đơn giản—vòng tròn (round-robin).
Một cách tương tự là chọn ngẫu nhiên một server sẵn có. Cả hai cách này đều
phân bổ tải đều trên CDN, nhưng không giảm thời gian đáp ứng cảm nhận của
client.

Rõ ràng cả hai cách trên đều không xét đến gần về mạng, nhưng, quan trọng
không kém, cũng bỏ qua locality. Tức là, các yêu cầu cho cùng một URL được
chuyển đến các server khác nhau, làm giảm khả năng trang được phục vụ từ
cache RAM của server được chọn. Điều này buộc server phải lấy trang từ đĩa,
hoặc thậm chí từ backend server. Làm sao một tập redirector phân tán có thể
khiến các yêu cầu cho cùng một trang đến cùng một server (hoặc một tập nhỏ
server) mà không cần phối hợp toàn cục? Câu trả lời rất đơn giản: Tất cả
redirector dùng một dạng băm nào đó để ánh xạ URL một cách xác định vào một
tập giá trị nhỏ. Lợi ích chính là không cần trao đổi thông tin giữa các
redirector để đạt phối hợp; bất kể redirector nào nhận URL, quá trình băm
cho cùng kết quả.

Vậy thế nào là một thuật toán băm tốt? Thuật toán băm *modulo* cổ điển—
băm mỗi URL modulo số server—không phù hợp cho môi trường này. Vì nếu số
server thay đổi, phép tính modulo sẽ khiến phần lớn trang đổi server. Dù
không mong đợi tập server thay đổi thường xuyên, việc thêm server mới gây
tái phân bổ lớn là không mong muốn.

Một cách thay thế là dùng cùng thuật toán *consistent hashing* đã bàn ở
phần trước. Cụ thể, mỗi redirector trước tiên băm mọi server vào vòng tròn
đơn vị. Sau đó, với mỗi URL đến, redirector cũng băm URL ra một giá trị
trên vòng tròn, và URL được gán cho server gần nhất trên vòng tròn với giá
trị băm của nó. Nếu một nút lỗi trong sơ đồ này, tải của nó chuyển sang
láng giềng (trên vòng tròn), nên thêm hoặc loại bỏ server chỉ gây thay đổi
cục bộ trong phân bổ yêu cầu. Lưu ý, khác với peer-to-peer, nơi thông điệp
được định tuyến từ nút này sang nút khác để tìm server có ID gần đối tượng,
mỗi redirector biết cách tập server ánh xạ lên vòng tròn, nên có thể tự
chọn server “gần nhất”.

Chiến lược này dễ dàng mở rộng để xét đến tải server. Giả sử redirector
biết tải hiện tại của mỗi server sẵn có. Thông tin này có thể không hoàn
toàn cập nhật, nhưng có thể tưởng tượng redirector chỉ cần đếm số lần đã
chuyển tiếp yêu cầu cho mỗi server trong vài giây gần nhất và dùng số này
làm ước lượng tải hiện tại. Khi nhận URL, redirector băm URL cộng từng
server sẵn có và sắp xếp các giá trị. Danh sách đã sắp xếp này xác định
thứ tự redirector sẽ xét các server. Redirector duyệt danh sách cho đến
khi tìm server có tải dưới ngưỡng. Lợi ích so với băm đều là thứ tự server
khác nhau cho mỗi URL, nên nếu một server lỗi, tải của nó được phân bổ đều
cho các máy khác. Cách này là cơ sở cho Cache Array Routing Protocol (CARP)
và được mô tả giả mã dưới đây.

::

   SelectServer(URL, S)
       for each server s in server set S
           score[s] = hash(URL, address[s])
       sort score
       for each server s in decreasing order of score
           if Load(s) < threshold then
               return s
       return server with highest score

Khi tải tăng, sơ đồ này chuyển từ chỉ dùng server đầu tiên trong danh sách
sang phân bổ yêu cầu trên nhiều server. Một số trang thường do server bận
xử lý cũng sẽ được server ít bận hơn chia sẻ tải. Vì quá trình này dựa trên
tải tổng thể server chứ không phải độ phổ biến từng trang, server lưu một
số trang phổ biến có thể thấy nhiều server chia sẻ tải hơn server lưu trang
không phổ biến. Trong quá trình này, một số trang không phổ biến sẽ được
sao chép trong hệ thống chỉ vì chúng chủ yếu được lưu trên server bận. Đồng
thời, nếu một số trang trở nên cực kỳ phổ biến, có thể tất cả server trong
hệ thống sẽ chịu trách nhiệm phục vụ chúng.

Cuối cùng, có thể đưa gần về mạng vào bài toán theo ít nhất hai cách. Đầu
tiên là làm mờ ranh giới giữa tải server và gần về mạng bằng cách đo thời
gian server phản hồi yêu cầu và dùng số đo này làm tham số “tải server” cho
thuật toán trên. Chiến lược này ưu tiên server gần/ít tải hơn server xa/nhiều
tải. Cách thứ hai là xét gần về mạng ở giai đoạn sớm hơn bằng cách giới hạn
tập server ứng viên (*S*) cho thuật toán trên chỉ gồm các server gần. Vấn
đề khó hơn là xác định server nào đủ gần. Một cách là chỉ chọn server cùng
ISP với client. Cách tinh vi hơn là xem bản đồ hệ tự trị (autonomous system)
do BGP tạo ra và chỉ chọn server trong một số hop từ client làm ứng viên.
Tìm cân bằng đúng giữa gần về mạng và locality cache server là chủ đề nghiên
cứu đang tiếp diễn.
