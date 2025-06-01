9.4 Mạng phủ (Overlay Networks)
===============================

Ngay từ khi ra đời, Internet đã áp dụng một mô hình rõ ràng, trong đó các bộ định tuyến bên trong mạng chịu trách nhiệm chuyển tiếp các gói tin từ nguồn đến đích, và các chương trình ứng dụng chạy trên các máy chủ kết nối ở rìa mạng. Mô hình client/server được minh họa bởi các ứng dụng được thảo luận ở hai phần đầu của chương này chắc chắn tuân thủ mô hình này.

Tuy nhiên, trong vài năm gần đây, ranh giới giữa *chuyển tiếp gói tin* và *xử lý ứng dụng* đã trở nên mờ nhạt hơn. Các ứng dụng mới đang được phân phối trên toàn Internet, và trong nhiều trường hợp, các ứng dụng này tự đưa ra quyết định chuyển tiếp của riêng mình. Những ứng dụng lai mới này đôi khi có thể được triển khai bằng cách mở rộng các bộ định tuyến và switch truyền thống để hỗ trợ một lượng nhỏ xử lý đặc thù cho ứng dụng. Ví dụ, các *switch lớp 7* ngồi trước các cụm máy chủ và chuyển tiếp các yêu cầu HTTP đến một máy chủ cụ thể dựa trên URL được yêu cầu. Tuy nhiên, *mạng phủ* đang nhanh chóng nổi lên như một cơ chế được lựa chọn để đưa các chức năng mới vào Internet.

.. _fig-overlay-net:
.. figure:: figures/f09-19-9780123850591.png
   :width: 300px
   :align: center

   Mạng phủ được xếp lớp trên một mạng vật lý.

Bạn có thể nghĩ về một mạng phủ như một mạng logic được triển khai trên một mạng nền tảng bên dưới. Theo định nghĩa này, Internet ban đầu là một mạng phủ trên các liên kết do mạng điện thoại cũ cung cấp. :numref:`Hình %s <fig-overlay-net>` mô tả một mạng phủ được triển khai trên một mạng nền tảng bên dưới. Mỗi nút trong mạng phủ cũng tồn tại trong mạng nền tảng; nó xử lý và chuyển tiếp các gói tin theo cách đặc thù cho ứng dụng. Các liên kết kết nối các nút phủ được triển khai dưới dạng các đường hầm (tunnel) qua mạng nền tảng. Nhiều mạng phủ có thể cùng tồn tại trên cùng một mạng nền tảng—mỗi mạng triển khai hành vi đặc thù cho ứng dụng của riêng mình—và các mạng phủ có thể được lồng ghép, cái này trên cái kia. Ví dụ, tất cả các mạng phủ được đề cập trong phần này đều coi Internet ngày nay là mạng nền tảng.

.. _fig-overlay-tunnel:
.. figure:: figures/f09-20-9780123850591.png
   :width: 500px
   :align: center

   Các nút phủ tạo đường hầm qua các nút vật lý.

Chúng ta đã từng thấy các ví dụ về đường hầm, ví dụ như để triển khai mạng riêng ảo (VPN). Để nhắc lại ngắn gọn, các nút ở hai đầu đường hầm coi đường đi nhiều bước giữa chúng như một liên kết logic duy nhất, các nút bị đường hầm đi qua sẽ chuyển tiếp các gói tin dựa trên tiêu đề ngoài, mà không biết rằng các nút đầu cuối đã gắn thêm một tiêu đề trong. :numref:`Hình %s <fig-overlay-tunnel>` cho thấy ba nút phủ (A, B và C) được kết nối bởi một cặp đường hầm. Trong ví dụ này, nút phủ B có thể đưa ra quyết định chuyển tiếp cho các gói tin từ A đến C dựa trên tiêu đề trong (``IHdr``), sau đó gắn thêm tiêu đề ngoài (``OHdr``) xác định C là đích trong mạng nền tảng. Các nút A, B và C có thể diễn giải cả tiêu đề trong và ngoài, trong khi các bộ định tuyến trung gian chỉ hiểu tiêu đề ngoài. Tương tự, A, B và C có địa chỉ trong cả mạng phủ và mạng nền tảng, nhưng chúng không nhất thiết giống nhau; ví dụ, địa chỉ nền tảng của chúng có thể là địa chỉ IP 32 bit, trong khi địa chỉ phủ có thể là địa chỉ thử nghiệm 128 bit. Thực tế, mạng phủ không nhất thiết phải sử dụng địa chỉ truyền thống mà có thể định tuyến dựa trên URL, tên miền, truy vấn XML, hoặc thậm chí là nội dung của gói tin.

9.4.1 Định tuyến trong mạng phủ
------------------------------

Loại mạng phủ đơn giản nhất là loại chỉ tồn tại để hỗ trợ một chiến lược định tuyến thay thế; không có xử lý bổ sung ở tầng ứng dụng tại các nút phủ. Bạn có thể coi VPN là một ví dụ về mạng phủ định tuyến, nhưng nó không định nghĩa một chiến lược hay thuật toán thay thế mà chỉ là các mục nhập bảng định tuyến thay thế để được xử lý bởi thuật toán chuyển tiếp IP tiêu chuẩn. Trong trường hợp này, mạng phủ được gọi là sử dụng “IP tunnel”, và khả năng sử dụng các VPN này được hỗ trợ trong nhiều bộ định tuyến thương mại.

Tuy nhiên, giả sử bạn muốn sử dụng một thuật toán định tuyến mà các nhà cung cấp bộ định tuyến thương mại không sẵn lòng tích hợp vào sản phẩm của họ. Bạn sẽ làm thế nào? Bạn chỉ cần chạy thuật toán của mình trên một tập hợp các máy đầu cuối, và tạo đường hầm qua các bộ định tuyến Internet. Các máy này sẽ hoạt động như các bộ định tuyến trong mạng phủ: Là máy chủ, chúng có thể chỉ kết nối với Internet qua một liên kết vật lý, nhưng là một nút trong mạng phủ, chúng sẽ kết nối với nhiều láng giềng qua các đường hầm.

Vì mạng phủ, gần như theo định nghĩa, là một cách để đưa công nghệ mới vào mà không phụ thuộc vào quá trình tiêu chuẩn hóa, nên không có mạng phủ tiêu chuẩn nào để chúng ta lấy làm ví dụ. Thay vào đó, chúng tôi minh họa ý tưởng chung về mạng phủ định tuyến bằng cách mô tả một số hệ thống thử nghiệm đã được các nhà nghiên cứu mạng xây dựng.

Các phiên bản IP thử nghiệm
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Mạng phủ là lý tưởng để triển khai các phiên bản IP thử nghiệm mà bạn hy vọng sẽ trở thành tiêu chuẩn trong tương lai. Ví dụ, IP multicast bắt đầu như một phần mở rộng của IP và thậm chí đến ngày nay vẫn chưa được bật trên nhiều bộ định tuyến Internet. MBone (multicast backbone) là một mạng phủ triển khai IP multicast trên nền định tuyến unicast do Internet cung cấp. Một số công cụ hội nghị đa phương tiện đã được phát triển và triển khai trên MBone. Ví dụ, các cuộc họp IETF—kéo dài một tuần và thu hút hàng nghìn người tham dự—trong nhiều năm đã được phát sóng qua MBone. (Ngày nay, sự phổ biến rộng rãi của các công cụ hội nghị thương mại đã thay thế cách tiếp cận dựa trên MBone.)

Giống như VPN, MBone sử dụng cả IP tunnel và địa chỉ IP, nhưng khác với VPN, MBone triển khai một thuật toán chuyển tiếp khác—chuyển tiếp các gói tin đến tất cả các láng giềng phía dưới trong cây multicast đường đi ngắn nhất. Là một mạng phủ, các bộ định tuyến hỗ trợ multicast tạo đường hầm qua các bộ định tuyến cũ, với hy vọng một ngày nào đó sẽ không còn bộ định tuyến cũ nữa.

6-BONE là một mạng phủ tương tự được sử dụng để triển khai IPv6 một cách từng bước. Giống như MBone, 6-BONE sử dụng các đường hầm để chuyển tiếp các gói tin qua các bộ định tuyến IPv4. Tuy nhiên, khác với MBone, các nút 6-BONE không chỉ đơn giản cung cấp một cách diễn giải mới cho địa chỉ 32 bit của IPv4. Thay vào đó, chúng chuyển tiếp các gói tin dựa trên không gian địa chỉ 128 bit của IPv6. 6-BONE cũng hỗ trợ multicast IPv6. (Ngày nay, các bộ định tuyến thương mại đã hỗ trợ IPv6, nhưng một lần nữa, mạng phủ là một cách tiếp cận giá trị trong khi một công nghệ mới đang được đánh giá và tinh chỉnh.)

Multicast hệ thống đầu cuối
~~~~~~~~~~~~~~~~~~~~~~~~~~

Mặc dù IP multicast phổ biến với các nhà nghiên cứu và một số phân khúc cộng đồng mạng, việc triển khai nó trên Internet toàn cầu vẫn còn rất hạn chế. Đáp lại, các ứng dụng dựa trên multicast như hội nghị truyền hình gần đây đã chuyển sang một chiến lược thay thế, gọi là *multicast hệ thống đầu cuối* (end system multicast). Ý tưởng của multicast hệ thống đầu cuối là chấp nhận rằng IP multicast sẽ không bao giờ trở nên phổ biến và thay vào đó để các máy đầu cuối tham gia vào một ứng dụng multicast cụ thể tự triển khai cây multicast của riêng mình.

Trước khi mô tả cách multicast hệ thống đầu cuối hoạt động, điều quan trọng là phải hiểu rằng, không giống như VPN và MBone, multicast hệ thống đầu cuối giả định chỉ các máy chủ Internet (không phải bộ định tuyến Internet) tham gia vào mạng phủ. Hơn nữa, các máy này thường trao đổi thông điệp với nhau qua các đường hầm UDP thay vì IP tunnel, giúp dễ dàng triển khai dưới dạng chương trình ứng dụng thông thường. Điều này cho phép coi mạng nền tảng như một đồ thị đầy đủ kết nối, vì mọi máy chủ trên Internet đều có thể gửi thông điệp đến mọi máy chủ khác. Trừu tượng hóa, multicast hệ thống đầu cuối giải quyết bài toán sau: Bắt đầu với một đồ thị đầy đủ kết nối đại diện cho Internet, mục tiêu là tìm cây multicast nhúng bao phủ tất cả các thành viên nhóm.

Lưu ý rằng có một phiên bản đơn giản hơn của bài toán này, nhờ sự phổ biến của các máy ảo đám mây trên toàn thế giới. Các “hệ thống đầu cuối” hỗ trợ multicast có thể là các máy ảo chạy tại nhiều địa điểm. Vì các địa điểm này được biết trước và khá cố định, có thể xây dựng một cây multicast tĩnh trong đám mây, và các máy đầu cuối thực chỉ cần kết nối đến vị trí đám mây gần nhất. Tuy nhiên, để đầy đủ, phần sau đây mô tả cách tiếp cận ở dạng tổng quát nhất.

.. _fig-topology:
.. figure:: figures/f09-21-9780123850591.png
   :width: 400px
   :align: center

   Các cây multicast thay thế được ánh xạ lên một cấu trúc vật lý.

Vì chúng ta coi Internet nền tảng là đầy đủ kết nối, một giải pháp ngây thơ là để mỗi nguồn kết nối trực tiếp đến từng thành viên của nhóm. Nói cách khác, multicast hệ thống đầu cuối có thể được triển khai bằng cách để mỗi nút gửi thông điệp unicast đến mọi thành viên nhóm. Để thấy vấn đề của cách làm này, đặc biệt so với việc triển khai IP multicast trong các bộ định tuyến, hãy xem cấu trúc ví dụ trong :numref:`Hình %s <fig-topology>`. :numref:`Hình %s <fig-topology>` mô tả một cấu trúc vật lý ví dụ, trong đó R1 và R2 là các bộ định tuyến được kết nối bởi một liên kết xuyên lục địa băng thông thấp; A, B, C và D là các máy chủ đầu cuối; và độ trễ liên kết được cho dưới dạng trọng số cạnh. Giả sử A muốn gửi một thông điệp multicast đến ba máy còn lại, :numref:`Hình %s <fig-topology>` cho thấy cách truyền unicast ngây thơ sẽ hoạt động. Rõ ràng điều này là không mong muốn vì cùng một thông điệp phải đi qua liên kết A-R1 ba lần, và hai bản sao của thông điệp đi qua R1-R2. :numref:`Hình %s <fig-topology>` mô tả cây multicast IP được xây dựng bởi Giao thức Định tuyến Multicast Vector Khoảng cách (DVMRP). Rõ ràng, cách tiếp cận này loại bỏ các thông điệp dư thừa. Tuy nhiên, nếu không có sự hỗ trợ từ các bộ định tuyến, điều tốt nhất mà multicast hệ thống đầu cuối có thể hy vọng là một cây tương tự như cây được hiển thị trong :numref:`Hình %s <fig-topology>`. Multicast hệ thống đầu cuối định nghĩa một kiến trúc để xây dựng cây này.

.. _fig-layered-overlays:
.. figure:: figures/f09-22-9780123850591.png
   :width: 300px
   :align: center

   Cây multicast nhúng trong một mạng phủ.

Cách tiếp cận chung là hỗ trợ nhiều lớp mạng phủ, mỗi lớp trích xuất một đồ thị con từ lớp phủ bên dưới, cho đến khi chúng ta chọn được đồ thị con mà ứng dụng mong muốn. Đối với multicast hệ thống đầu cuối, điều này diễn ra qua hai giai đoạn: Đầu tiên, chúng ta xây dựng một mạng phủ *mesh* đơn giản trên nền Internet đầy đủ kết nối, sau đó chọn một cây multicast trong mesh này. Ý tưởng được minh họa trong :numref:`Hình %s <fig-layered-overlays>`, giả sử bốn máy đầu cuối A, B, C và D. Bước đầu tiên là quan trọng nhất: Khi đã chọn được một mesh phủ phù hợp, chúng ta chỉ cần chạy một thuật toán định tuyến multicast tiêu chuẩn (ví dụ, DVMRP) trên đó để xây dựng cây multicast. Chúng ta cũng có lợi thế là bỏ qua vấn đề mở rộng mà multicast toàn Internet gặp phải vì mesh trung gian có thể chỉ bao gồm các nút muốn tham gia vào một nhóm multicast cụ thể.

Chìa khóa để xây dựng mesh phủ trung gian là chọn một cấu trúc gần giống với cấu trúc vật lý của Internet nền tảng, nhưng chúng ta phải làm điều này mà không ai nói cho chúng ta biết Internet nền tảng thực sự trông như thế nào vì chúng ta chỉ chạy trên các máy đầu cuối chứ không phải bộ định tuyến. Chiến lược chung là các máy đầu cuối đo độ trễ khứ hồi đến các nút khác và chỉ thêm liên kết vào mesh khi chúng hài lòng với kết quả đo được. Cách làm như sau.

Đầu tiên, giả sử mesh đã tồn tại, mỗi nút trao đổi danh sách tất cả các nút khác mà nó tin là thành viên mesh với các láng giềng kết nối trực tiếp. Khi một nút nhận được danh sách thành viên từ một láng giềng, nó tích hợp thông tin đó vào danh sách thành viên của mình và chuyển tiếp danh sách kết quả cho các láng giềng. Thông tin này cuối cùng sẽ lan truyền qua mesh, giống như trong một giao thức định tuyến vector khoảng cách.

Khi một máy chủ muốn tham gia vào mạng phủ multicast, nó phải biết địa chỉ IP của ít nhất một nút đã có trong mạng phủ. Sau đó, nó gửi một thông điệp “join mesh” đến nút này. Điều này kết nối nút mới vào mesh bằng một cạnh đến nút đã biết. Thông thường, nút mới có thể gửi thông điệp join đến nhiều nút hiện tại, do đó tham gia mesh bằng nhiều liên kết. Khi một nút đã kết nối vào mesh bằng một tập hợp liên kết, nó định kỳ gửi thông điệp “keepalive” đến các láng giềng, cho biết nó vẫn muốn là thành viên nhóm.

Khi một nút rời nhóm, nó gửi thông điệp “leave mesh” đến các láng giềng kết nối trực tiếp, và thông tin này được lan truyền đến các nút khác trong mesh thông qua danh sách thành viên như đã mô tả ở trên. Ngoài ra, một nút có thể bị lỗi hoặc chỉ đơn giản quyết định rời nhóm mà không báo trước, trong trường hợp này các láng giềng sẽ phát hiện ra nó không còn gửi thông điệp “keep alive” nữa. Một số trường hợp nút rời đi không ảnh hưởng nhiều đến mesh, nhưng nếu một nút phát hiện mesh bị phân mảnh do một nút rời đi, nó sẽ tạo một cạnh mới đến một nút ở phân mảnh khác bằng cách gửi thông điệp “join mesh”. Lưu ý rằng nhiều láng giềng có thể đồng thời quyết định rằng mesh đã bị phân mảnh, dẫn đến việc thêm nhiều cạnh nối giữa các phân mảnh vào mesh.

Như đã mô tả đến đây, chúng ta sẽ có một mesh là đồ thị con của Internet đầy đủ kết nối ban đầu, nhưng nó có thể có hiệu năng chưa tối ưu vì (1) lựa chọn láng giềng ban đầu thêm các liên kết ngẫu nhiên vào cấu trúc, (2) sửa chữa phân mảnh có thể thêm các cạnh cần thiết lúc đó nhưng không hữu ích lâu dài, (3) thành viên nhóm có thể thay đổi do các nút tham gia/rời động, và (4) điều kiện mạng nền tảng có thể thay đổi. Điều cần thiết là hệ thống phải đánh giá giá trị của từng cạnh, dẫn đến việc thêm các cạnh mới vào mesh và loại bỏ các cạnh hiện có theo thời gian.

Để thêm các cạnh mới, mỗi nút *i* định kỳ kiểm tra một thành viên ngẫu nhiên *j* mà nó chưa kết nối trong mesh, đo độ trễ khứ hồi của cạnh *(i,j)*, sau đó đánh giá tính hữu ích của việc thêm cạnh này. Nếu tính hữu ích vượt quá một ngưỡng nhất định, liên kết *(i,j)* được thêm vào mesh. Đánh giá tính hữu ích của việc thêm cạnh *(i,j)* có thể như sau:

::

   EvaluateUtility(j)
       utility = 0
       for each member m not equal to i
           CL = độ trễ hiện tại đến nút m dọc theo đường đi qua mesh
           NL = độ trễ mới đến nút m qua mesh nếu thêm cạnh (i,j)
           if (NL < CL) then
               utility += (CL - NL)/CL
       return utility

Việc quyết định loại bỏ một cạnh cũng tương tự, ngoại trừ mỗi nút *i* tính toán chi phí của từng liên kết đến láng giềng hiện tại *j* như sau:

::

   EvaluateCost(j)
       Cost[i,j] = số thành viên mà i sử dụng j làm bước tiếp theo
       Cost[j,i] = số thành viên mà j sử dụng i làm bước tiếp theo
       return max(Cost[i,j], Cost[j,i])

Sau đó, nó chọn láng giềng có chi phí thấp nhất, và loại bỏ nếu chi phí thấp hơn một ngưỡng nhất định.

Cuối cùng, vì mesh được duy trì bằng một giao thức gần giống vector khoảng cách, việc chạy DVMRP để tìm cây multicast phù hợp trong mesh là điều đơn giản. Lưu ý rằng, mặc dù không thể chứng minh giao thức vừa mô tả sẽ tạo ra mạng mesh tối ưu, cho phép DVMRP chọn cây multicast tốt nhất, cả mô phỏng và kinh nghiệm thực tế đều cho thấy nó hoạt động khá tốt.

Mạng phủ chống chịu lỗi (Resilient Overlay Networks)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một chức năng khác mà mạng phủ có thể thực hiện là tìm các đường đi thay thế cho các ứng dụng unicast truyền thống. Các mạng phủ này khai thác thực tế rằng bất đẳng thức tam giác không luôn đúng trong Internet. :numref:`Hình %s <fig-triangle>` minh họa điều này. Không hiếm khi có ba địa điểm trong Internet—gọi là A, B và C—sao cho độ trễ giữa A và B lớn hơn tổng độ trễ từ A đến C và từ C đến B. Tức là, đôi khi bạn sẽ tốt hơn nếu gửi các gói tin gián tiếp qua một nút trung gian thay vì gửi trực tiếp đến đích.

.. _fig-triangle:
.. figure:: figures/f09-23-9780123850591.png
   :width: 600px
   :align: center

   Bất đẳng thức tam giác không nhất thiết đúng trong các mạng.

Tại sao lại như vậy? Thực ra, Border Gateway Protocol (BGP) chưa bao giờ hứa sẽ tìm đường đi *ngắn nhất* giữa hai địa điểm bất kỳ; nó chỉ cố gắng tìm *một* đường đi nào đó. Để làm mọi thứ phức tạp hơn, các đường đi của BGP bị ảnh hưởng mạnh bởi các vấn đề chính sách, như ai trả tiền cho ai để chuyển lưu lượng của họ. Điều này thường xảy ra, ví dụ, tại các điểm kết nối giữa các ISP trục chính. Tóm lại, việc bất đẳng thức tam giác không đúng trong Internet không có gì đáng ngạc nhiên.

Chúng ta khai thác thực tế này như thế nào? Bước đầu tiên là nhận ra rằng có một sự đánh đổi cơ bản giữa khả năng mở rộng và tối ưu của một thuật toán định tuyến. Một mặt, BGP có thể mở rộng đến các mạng rất lớn, nhưng thường không chọn được đường đi tốt nhất và phản ứng chậm với sự cố mạng. Mặt khác, nếu bạn chỉ quan tâm đến việc tìm đường đi tốt nhất giữa một số ít địa điểm, bạn có thể làm tốt hơn nhiều trong việc giám sát chất lượng của mọi đường đi có thể sử dụng, cho phép bạn chọn đường đi tốt nhất tại mọi thời điểm.

Một mạng phủ thử nghiệm, gọi là Resilient Overlay Network (RON), đã làm đúng như vậy. RON chỉ mở rộng đến vài chục nút vì nó sử dụng chiến lược *N × N* để giám sát chặt chẽ (bằng các phép đo chủ động) ba khía cạnh chất lượng đường đi—độ trễ, băng thông khả dụng và xác suất mất gói—giữa mọi cặp địa điểm. Nhờ đó, nó vừa có thể chọn đường đi tối ưu giữa bất kỳ cặp nút nào, vừa có thể thay đổi đường đi nhanh chóng khi điều kiện mạng thay đổi. Kinh nghiệm cho thấy RON có thể cải thiện hiệu năng ứng dụng ở mức vừa phải, nhưng quan trọng hơn, nó phục hồi sau sự cố mạng nhanh hơn nhiều. Ví dụ, trong một khoảng thời gian 64 giờ năm 2001, một phiên bản RON chạy trên 12 nút đã phát hiện 32 sự cố kéo dài hơn 30 phút, và có thể phục hồi tất cả chỉ trong chưa đầy 20 giây trung bình. Thí nghiệm này cũng cho thấy chỉ cần chuyển tiếp dữ liệu qua một nút trung gian là đủ để phục hồi sau sự cố Internet.

Vì RON không được thiết kế để mở rộng, không thể dùng RON để giúp máy chủ A bất kỳ giao tiếp với máy chủ B bất kỳ; A và B phải biết trước rằng họ sẽ giao tiếp và cùng tham gia vào một RON. Tuy nhiên, RON có vẻ là ý tưởng hay trong một số bối cảnh, như khi kết nối vài chục chi nhánh công ty trên Internet hoặc cho phép bạn và 50 người bạn lập mạng phủ riêng để chạy một ứng dụng nào đó. (Ngày nay, ý tưởng này được áp dụng thực tế với tên gọi *Software-Defined WAN*, hay *SD-WAN*.) Câu hỏi thực sự là điều gì xảy ra khi mọi người đều chạy RON của riêng mình. Liệu việc hàng triệu RON liên tục đo đạc đường đi có làm nghẽn mạng, và liệu có ai còn thấy cải thiện khi nhiều RON cạnh tranh cùng một đường đi? Những câu hỏi này vẫn chưa có lời giải.

.. _key-virtualization:
.. admonition:: Bài học then chốt

   Tất cả các mạng phủ này minh họa một khái niệm trung tâm của mạng máy tính nói chung: *ảo hóa* (virtualization). Tức là, có thể xây dựng một mạng ảo từ các tài nguyên trừu tượng (logic) trên nền một mạng vật lý được xây dựng từ các tài nguyên vật lý. Hơn nữa, có thể xếp chồng các mạng ảo này lên nhau và cho nhiều mạng ảo cùng tồn tại ở cùng một tầng. Mỗi mạng ảo, đến lượt nó, cung cấp các khả năng mới có giá trị cho một tập người dùng, ứng dụng hoặc mạng tầng cao hơn. :ref:`[Tiếp theo] <key-nested>`

9.4.2 Mạng ngang hàng (Peer-to-Peer Networks)
---------------------------------------------

Các ứng dụng chia sẻ nhạc như Napster và KaZaA đã đưa thuật ngữ “peer-to-peer” vào ngôn ngữ phổ thông. Nhưng thực sự, một hệ thống “peer-to-peer” nghĩa là gì? Chắc chắn trong bối cảnh chia sẻ file MP3, nó có nghĩa là không phải tải nhạc từ một trang trung tâm, mà thay vào đó có thể truy cập file nhạc trực tiếp từ bất kỳ ai trên Internet có lưu trữ bản sao trên máy tính của họ. Nói rộng hơn, chúng ta có thể nói rằng một mạng ngang hàng cho phép một cộng đồng người dùng gộp tài nguyên (nội dung, lưu trữ, băng thông mạng, băng thông đĩa, CPU), nhờ đó cung cấp khả năng truy cập kho lưu trữ lớn hơn, hội nghị video/audio lớn hơn, tìm kiếm và tính toán phức tạp hơn, v.v. so với bất kỳ người dùng đơn lẻ nào có thể tự trang bị.

Thường thì các thuộc tính như *phi tập trung* và *tự tổ chức* được nhắc đến khi nói về mạng ngang hàng, nghĩa là các nút tự tổ chức thành mạng mà không cần điều phối tập trung. Nếu bạn nghĩ kỹ, các thuật ngữ này cũng có thể dùng để mô tả chính Internet. Tuy nhiên, trớ trêu thay, Napster không phải là một hệ thống peer-to-peer thực sự theo định nghĩa này vì nó phụ thuộc vào một thư mục trung tâm các file đã biết, và người dùng phải tìm kiếm thư mục này để biết máy nào cung cấp file cụ thể. Chỉ bước cuối cùng—tải file—mới diễn ra giữa hai máy của người dùng, nhưng điều này không khác gì giao dịch client/server truyền thống. Khác biệt duy nhất là server thuộc sở hữu của một người giống bạn thay vì một tập đoàn lớn.

Vậy chúng ta quay lại câu hỏi ban đầu: Điều gì thú vị ở mạng ngang hàng? Một câu trả lời là cả quá trình tìm kiếm đối tượng quan tâm và quá trình tải đối tượng đó về máy của bạn đều diễn ra mà không cần liên hệ với một cơ quan trung tâm, đồng thời hệ thống có thể mở rộng đến hàng triệu nút. Một hệ thống peer-to-peer có thể thực hiện hai nhiệm vụ này một cách phi tập trung hóa ra lại là một mạng phủ, trong đó các nút là các máy chủ sẵn sàng chia sẻ đối tượng (ví dụ, nhạc và các file khác), và các liên kết (đường hầm) kết nối các nút này đại diện cho chuỗi máy bạn phải đi qua để tìm đối tượng mong muốn. Mô tả này sẽ rõ ràng hơn sau khi chúng ta xem hai ví dụ.

Gnutella
~~~~~~~~

Gnutella là một mạng ngang hàng đời đầu cố gắng phân biệt giữa việc trao đổi nhạc (có thể vi phạm bản quyền) và chia sẻ file nói chung (được coi là tốt vì chúng ta được dạy chia sẻ từ nhỏ). Điều thú vị ở Gnutella là nó là một trong những hệ thống đầu tiên không phụ thuộc vào thư mục trung tâm các đối tượng. Thay vào đó, các thành viên Gnutella tự sắp xếp thành một mạng phủ giống như trong :numref:`Hình %s <fig-gnutella>`. Tức là, mỗi nút chạy phần mềm Gnutella (tức là triển khai giao thức Gnutella) biết về một tập các máy khác cũng chạy phần mềm Gnutella. Quan hệ “A và B biết nhau” tương ứng với các cạnh trong đồ thị này. (Chúng ta sẽ nói về cách đồ thị này hình thành sau.)

.. _fig-gnutella:
.. figure:: figures/f09-24-9780123850591.png
   :width: 300px
   :align: center

   Ví dụ về cấu trúc mạng ngang hàng Gnutella.

Bất cứ khi nào người dùng trên một nút muốn tìm một đối tượng, Gnutella gửi một thông điệp QUERY cho đối tượng đó—ví dụ, chỉ định tên file—đến các láng giềng trong đồ thị. Nếu một láng giềng có đối tượng, nó phản hồi lại nút gửi truy vấn bằng thông điệp QUERY RESPONSE, chỉ ra nơi có thể tải đối tượng (ví dụ, địa chỉ IP và cổng TCP). Nút đó sau đó có thể dùng thông điệp GET hoặc PUT để truy cập đối tượng. Nếu nút không giải quyết được truy vấn, nó chuyển tiếp thông điệp QUERY đến từng láng giềng (trừ nút đã gửi truy vấn), và quá trình lặp lại. Nói cách khác, Gnutella phát tán (flood) mạng phủ để tìm đối tượng mong muốn. Gnutella đặt TTL cho mỗi truy vấn để việc phát tán không kéo dài vô hạn.

Ngoài TTL và chuỗi truy vấn, mỗi thông điệp QUERY chứa một định danh truy vấn duy nhất (QID), nhưng không chứa danh tính nguồn gốc của thông điệp. Thay vào đó, mỗi nút duy trì bản ghi các thông điệp QUERY đã thấy gần đây: cả QID và láng giềng đã gửi QUERY. Nó dùng lịch sử này theo hai cách. Thứ nhất, nếu nhận được QUERY có QID trùng với một truy vấn đã thấy gần đây, nút sẽ không chuyển tiếp QUERY đó. Điều này giúp cắt các vòng lặp chuyển tiếp nhanh hơn TTL. Thứ hai, bất cứ khi nào nút nhận được QUERY RESPONSE từ láng giềng phía dưới, nó biết phải chuyển tiếp phản hồi đó đến láng giềng phía trên đã gửi QUERY ban đầu. Nhờ vậy, phản hồi sẽ quay lại nút gốc mà không nút trung gian nào biết ai là người muốn tìm đối tượng đó.

Quay lại câu hỏi về cách đồ thị phát triển, một nút chắc chắn phải biết ít nhất một nút khác khi tham gia mạng phủ Gnutella. Nút mới được nối vào mạng phủ bằng ít nhất một liên kết này. Sau đó, một nút có thể biết thêm các nút khác nhờ các thông điệp QUERY RESPONSE, cả cho các đối tượng nó yêu cầu và các phản hồi tình cờ đi qua nó. Một nút có thể tự do quyết định giữ lại những nút nào làm láng giềng. Giao thức Gnutella cung cấp thông điệp PING và PONG để một nút kiểm tra xem láng giềng còn tồn tại không và nhận phản hồi tương ứng.

Rõ ràng Gnutella như mô tả ở đây không phải là một giao thức đặc biệt thông minh, và các hệ thống sau này đã cố gắng cải tiến nó. Một hướng cải tiến là cách truyền truy vấn. Flooding có ưu điểm là đảm bảo tìm được đối tượng mong muốn với số bước ít nhất, nhưng không mở rộng tốt. Có thể chuyển tiếp truy vấn ngẫu nhiên, hoặc theo xác suất thành công dựa trên kết quả trước đó. Một hướng khác là chủ động sao chép đối tượng, vì càng nhiều bản sao thì càng dễ tìm thấy. Hoặc có thể phát triển một chiến lược hoàn toàn khác, sẽ được bàn tiếp theo.

Mạng phủ có cấu trúc (Structured Overlays)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Cùng lúc các hệ thống chia sẻ file cạnh tranh lấp chỗ trống Napster để lại, cộng đồng nghiên cứu bắt đầu khám phá một thiết kế thay thế cho mạng ngang hàng. Chúng tôi gọi các mạng này là *có cấu trúc*, để phân biệt với cách phát triển ngẫu nhiên (không cấu trúc) của mạng Gnutella. Mạng phủ không cấu trúc như Gnutella sử dụng thuật toán xây dựng và duy trì mạng phủ đơn giản, nhưng chỉ cung cấp tìm kiếm ngẫu nhiên, không đáng tin cậy. Ngược lại, mạng phủ có cấu trúc được thiết kế tuân theo một cấu trúc đồ thị cụ thể cho phép định vị đối tượng đáng tin cậy và hiệu quả (độ trễ được giới hạn xác suất), đổi lại là sự phức tạp bổ sung khi xây dựng và duy trì mạng phủ.

Nếu bạn nghĩ về mục tiêu ở mức cao, có hai câu hỏi cần xem xét: (1) Làm thế nào để ánh xạ đối tượng lên các nút, và (2) Làm thế nào để định tuyến một yêu cầu đến nút chịu trách nhiệm cho đối tượng đó? Bắt đầu với câu hỏi đầu tiên, có thể phát biểu đơn giản: Làm thế nào để ánh xạ một đối tượng tên *x* thành địa chỉ của một nút *n* có thể phục vụ đối tượng đó? Trong khi các mạng ngang hàng truyền thống không kiểm soát được nút nào lưu trữ đối tượng *x*, nếu chúng ta kiểm soát được cách phân phối đối tượng trên mạng, có thể sẽ tìm kiếm hiệu quả hơn sau này.

Một kỹ thuật nổi tiếng để ánh xạ tên thành địa chỉ là dùng bảng băm (hash table), sao cho

.. centered:: *hash(x) → n*

nghĩa là đối tượng *x* được đặt trên nút *n*, và sau này một client muốn tìm *x* chỉ cần băm *x* để xác định nó nằm trên nút *n*. Cách này có ưu điểm là phân phối đối tượng đều trên các nút, nhưng thuật toán băm thông thường gặp một vấn đề lớn: Cho phép bao nhiêu giá trị *n*? (Trong thuật ngữ băm, có bao nhiêu bucket?) Ngây thơ, ta có thể chọn 101 giá trị băm, và dùng hàm băm modulo; tức là,

::

   hash(x)
       return x % 101

Không may, nếu có hơn 101 nút sẵn sàng lưu trữ đối tượng, thì không tận dụng được hết. Ngược lại, nếu chọn số lớn hơn số nút tối đa, sẽ có giá trị *x* băm ra địa chỉ không tồn tại. Cũng có vấn đề không nhỏ là chuyển giá trị trả về của hàm băm thành địa chỉ IP thực tế.

.. _fig-unitcircle:
.. figure:: figures/f09-25-9780123850591.png
   :width: 300px
   :align: center

   Cả nút và đối tượng đều được băm lên không gian ID, đối tượng được lưu ở nút gần nhất trong không gian này.

Để giải quyết các vấn đề này, mạng ngang hàng có cấu trúc dùng thuật toán gọi là *băm nhất quán* (consistent hashing), băm tập đối tượng *x* đều lên một không gian ID lớn. :numref:`Hình %s <fig-unitcircle>` minh họa không gian ID 128 bit dưới dạng vòng tròn, nơi thuật toán được dùng để đặt cả đối tượng

.. centered:: *hash(ObjectName) → ObjectID*

và nút

.. centered:: *hash(IPAddr) → NodeID*

lên vòng tròn này. Vì không gian ID 128 bit là rất lớn, khó có khả năng một đối tượng băm ra đúng ID mà địa chỉ IP của một máy cũng băm ra. Để xử lý điều này, mỗi đối tượng được lưu trên nút có ID *gần nhất* (trong không gian 128 bit) với ID của đối tượng. Nói cách khác, dùng hàm băm chất lượng cao để ánh xạ cả nút và đối tượng vào cùng một không gian ID lớn, thưa; sau đó ánh xạ đối tượng đến nút dựa trên khoảng cách số học giữa các ID. Như băm thông thường, cách này phân phối đối tượng khá đều, nhưng khác ở chỗ chỉ một số ít đối tượng phải di chuyển khi một nút (bucket) tham gia hoặc rời mạng.

.. _fig-locate:
.. figure:: figures/f09-26-9780123850591.png
   :width: 300px
   :align: center

   Đối tượng được định vị bằng cách định tuyến qua mạng phủ ngang hàng.

Chuyển sang câu hỏi thứ hai—làm thế nào để người dùng muốn truy cập đối tượng *x* biết nút nào gần nhất với ID của *x* trong không gian này? Một câu trả lời là mỗi nút giữ bảng đầy đủ các ID nút và địa chỉ IP tương ứng, nhưng điều này không khả thi với mạng lớn. Cách thay thế, được các mạng ngang hàng có cấu trúc sử dụng, là *định tuyến thông điệp đến nút đó!* Nói cách khác, nếu xây dựng mạng phủ một cách thông minh—tức là chọn các mục nhập bảng định tuyến của nút một cách thông minh—thì có thể tìm nút chỉ bằng cách định tuyến dần về phía nó. Cách tiếp cận này đôi khi được gọi là *bảng băm phân tán* (DHT), vì về mặt khái niệm, bảng băm được phân tán trên tất cả các nút trong mạng.

:numref:`Hình %s <fig-locate>` minh họa điều xảy ra với không gian ID 28 bit đơn giản. Để cụ thể, ta xét cách tiếp cận của mạng ngang hàng *Pastry*. Các hệ thống khác hoạt động tương tự.

Giả sử bạn ở nút có id ``65a1fc`` (hex) và muốn tìm đối tượng có ID ``d46a1c``. Bạn nhận ra ID của mình không có gì chung với đối tượng, nhưng bạn biết một nút có cùng tiền tố ``d``. Nút đó gần hơn bạn trong không gian ID 128 bit, nên bạn chuyển tiếp thông điệp cho nó. (Chúng tôi không đưa ra định dạng thông điệp, nhưng bạn có thể nghĩ nó như “tìm đối tượng ``d46a1c``”.) Giả sử nút ``d13da3`` biết một nút khác có tiền tố dài hơn với đối tượng, nó lại chuyển tiếp tiếp. Quá trình tiến gần hơn trong không gian ID tiếp tục cho đến khi đến nút không biết nút nào gần hơn. Nút này, theo định nghĩa, là nút lưu trữ đối tượng. Lưu ý rằng khi di chuyển logic qua “không gian ID”, thông điệp thực sự được chuyển tiếp qua các nút trên Internet nền tảng.

Mỗi nút duy trì cả bảng định tuyến (xem bên dưới) và địa chỉ IP của một tập nhỏ các ID nút lớn hơn và nhỏ hơn về số học. Đây gọi là *leaf set* của nút. Leaf set có ý nghĩa là, khi thông điệp được định tuyến đến bất kỳ nút nào trong leaf set của nút lưu trữ đối tượng, nút đó có thể chuyển tiếp trực tiếp đến đích cuối cùng. Nói cách khác, leaf set giúp chuyển thông điệp đúng và hiệu quả đến nút gần nhất về số học, dù có thể có nhiều nút cùng chia sẻ tiền tố dài nhất với ID đối tượng. Hơn nữa, leaf set làm cho định tuyến bền vững hơn vì bất kỳ nút nào trong leaf set đều có thể định tuyến thông điệp như nhau. Nếu một nút không thể tiếp tục định tuyến, một láng giềng trong leaf set có thể làm thay. Tóm lại, thủ tục định tuyến như sau:

::

   Route(D)
       if D nằm trong phạm vi leaf set của tôi
           chuyển tiếp đến thành viên gần nhất về số học trong leaf set
       else
           let l = độ dài tiền tố chung
           let d = giá trị của chữ số thứ l trong địa chỉ D
           if RouteTab[l,d] tồn tại
               chuyển tiếp đến RouteTab[l,d]
           else
               chuyển tiếp đến nút đã biết có tiền tố ít nhất dài bằng
               và gần hơn về số học so với nút này

Bảng định tuyến, ký hiệu ``RouteTab``, là một mảng hai chiều. Nó có một hàng cho mỗi chữ số hex trong ID (có 32 chữ số trong ID 128 bit) và một cột cho mỗi giá trị hex (có 16 giá trị). Mỗi mục ở hàng *i* có tiền tố dài *i* giống nút này, và trong hàng đó, mục ở cột *j* có giá trị hex *j* ở vị trí thứ *i+1*. :numref:`Hình %s <fig-rt>` cho thấy ba hàng đầu của bảng định tuyến ví dụ cho nút ``65a1fcx``, với *x* là hậu tố chưa xác định. Hình này cho thấy tiền tố ID được khớp bởi mỗi mục trong bảng. Nó không hiển thị giá trị thực tế trong mục—địa chỉ IP của nút tiếp theo để định tuyến.

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

Thêm một nút vào mạng phủ giống như định tuyến thông điệp “tìm đối tượng” đến một đối tượng. Nút mới phải biết ít nhất một thành viên hiện tại. Nó yêu cầu thành viên này định tuyến thông điệp “add node” đến nút gần nhất về số học với ID của nút mới, như minh họa trong :numref:`Hình %s <fig-addnode>`. Qua quá trình định tuyến này, nút mới biết về các nút có tiền tố chung và bắt đầu điền bảng định tuyến. Theo thời gian, khi các nút khác tham gia mạng phủ, các nút hiện tại cũng có thể bổ sung thông tin về nút mới vào bảng định tuyến của mình. Họ làm điều này khi nút mới thêm tiền tố dài hơn so với bảng hiện tại. Các láng giềng trong leaf set cũng trao đổi bảng định tuyến với nhau, nghĩa là theo thời gian thông tin định tuyến lan truyền qua mạng phủ.

Bạn có thể nhận thấy rằng mặc dù mạng phủ có cấu trúc cung cấp giới hạn xác suất về số bước định tuyến cần thiết để tìm một đối tượng—số bước trong Pastry bị giới hạn bởi :math:`log_{16}N`, với N là số nút trong mạng phủ—mỗi bước có thể góp phần lớn vào độ trễ. Vì mỗi nút trung gian có thể ở vị trí ngẫu nhiên trên Internet. (Trường hợp xấu nhất, mỗi nút ở một châu lục khác nhau!) Thực tế, trong mạng phủ toàn cầu dùng thuật toán như trên, độ trễ kỳ vọng của mỗi bước là trung bình độ trễ giữa mọi cặp nút trên Internet! May mắn thay, có thể làm tốt hơn nhiều trong thực tế. Ý tưởng là chọn mỗi mục bảng định tuyến sao cho nó trỏ đến một nút gần về vật lý trong mạng nền tảng, trong số các nút có tiền tố ID phù hợp. Làm như vậy đạt được độ trễ đầu-cuối chỉ lớn hơn một chút so với độ trễ giữa nguồn và đích.

Cuối cùng, thảo luận đến đây tập trung vào bài toán tổng quát định vị đối tượng trong mạng ngang hàng. Với hạ tầng định tuyến như vậy, có thể xây dựng các dịch vụ khác nhau. Ví dụ, dịch vụ chia sẻ file sẽ dùng tên file làm tên đối tượng. Để tìm file, bạn băm tên file thành ID đối tượng rồi định tuyến thông điệp “tìm đối tượng” đến ID đó. Hệ thống cũng có thể sao lưu mỗi file trên nhiều nút để tăng tính sẵn sàng. Lưu nhiều bản trên leaf set của nút mà file thường được định tuyến đến là một cách. Lưu ý rằng dù các nút này là láng giềng trong không gian ID, chúng có thể phân tán vật lý trên Internet. Do đó, mất điện ở cả một thành phố có thể làm mất các bản sao gần nhau trong hệ thống file truyền thống, nhưng một hoặc nhiều bản sao vẫn sống sót trong mạng ngang hàng.

Các dịch vụ khác ngoài chia sẻ file cũng có thể xây dựng trên bảng băm phân tán. Xét ứng dụng multicast, chẳng hạn. Thay vì xây dựng cây multicast từ mesh, có thể xây dựng cây từ các cạnh trong mạng phủ có cấu trúc, nhờ đó chia sẻ chi phí xây dựng và duy trì mạng phủ cho nhiều ứng dụng và nhóm multicast.

BitTorrent
~~~~~~~~~~

BitTorrent là một giao thức chia sẻ file ngang hàng do Bram Cohen phát triển. Nó dựa trên việc sao chép file, hay đúng hơn là sao chép các đoạn của file, gọi là *mảnh* (piece). Một mảnh bất kỳ thường có thể được tải từ nhiều peer, ngay cả khi chỉ một peer có toàn bộ file. Lợi ích chính của việc sao chép trong BitTorrent là tránh nút cổ chai khi chỉ có một nguồn cho file. Điều này đặc biệt hữu ích khi biết rằng mỗi máy tính chỉ có tốc độ phục vụ file qua uplink Internet giới hạn, thường khá thấp do tính bất đối xứng của đa số kết nối băng rộng. Vẻ đẹp của BitTorrent là việc sao chép diễn ra tự nhiên trong quá trình tải: Ngay khi một peer tải xong một mảnh, nó trở thành nguồn cho mảnh đó. Càng nhiều peer tải các mảnh, càng nhiều bản sao được tạo ra, phân phối tải hợp lý, và càng nhiều tổng băng thông để chia sẻ file cho người khác. Các mảnh được tải theo thứ tự ngẫu nhiên để tránh trường hợp các peer đều thiếu cùng một tập mảnh.

Mỗi file được chia sẻ qua một mạng BitTorrent độc lập, gọi là *swarm*. (Một swarm có thể chia sẻ một tập file, nhưng ta mô tả trường hợp một file cho đơn giản.) Vòng đời của một swarm điển hình như sau. Swarm bắt đầu với một peer đơn lẻ có bản sao đầy đủ file. Một nút muốn tải file sẽ tham gia swarm, trở thành thành viên thứ hai, và bắt đầu tải các mảnh từ peer gốc. Khi làm vậy, nó trở thành nguồn cho các mảnh đã tải, dù chưa có toàn bộ file. (Thực tế, các peer thường rời swarm sau khi tải xong, dù được khuyến khích ở lại lâu hơn.) Các nút khác tham gia swarm và bắt đầu tải các mảnh từ nhiều peer, không chỉ peer gốc. Xem :numref:`Hình %s <fig-bitTorrentSwarm>`.

.. _fig-bitTorrentSwarm:
.. figure:: figures/f09-29-9780123850591.png
   :width: 500px
   :align: center

   Các peer trong một swarm BitTorrent tải từ các peer khác có thể chưa có toàn bộ file.

Nếu file vẫn được nhiều người quan tâm, với dòng peer mới thay thế những người rời swarm, swarm có thể tồn tại vô thời hạn; nếu không, nó có thể thu nhỏ lại chỉ còn peer gốc cho đến khi có peer mới tham gia.

Sau khi có cái nhìn tổng quan về BitTorrent, ta có thể hỏi làm thế nào các yêu cầu được định tuyến đến các peer có mảnh mong muốn. Để gửi yêu cầu, một người muốn tải phải tham gia swarm. Nó bắt đầu bằng cách tải một file chứa thông tin về file và swarm. File này, có thể dễ dàng sao chép, thường được tải từ máy chủ web và tìm thấy qua các liên kết trên trang web. Nó chứa:

-  Kích thước file mục tiêu

-  Kích thước mảnh

-  Giá trị băm SHA-1 tính trước cho từng mảnh

-  URL của *tracker* của swarm

Tracker là một máy chủ theo dõi thành viên hiện tại của swarm. Ta sẽ thấy sau rằng BitTorrent có thể mở rộng để loại bỏ điểm tập trung này, tránh nút cổ chai hoặc lỗi.

Người muốn tải sau đó tham gia swarm, trở thành peer, bằng cách gửi thông điệp đến tracker với địa chỉ mạng và peer ID tự sinh ngẫu nhiên. Thông điệp cũng mang băm SHA-1 của phần chính file, dùng làm ID swarm.

Gọi peer mới là P. Tracker trả lời P bằng một danh sách một phần các peer với ID và địa chỉ mạng, và P thiết lập kết nối TCP với một số peer này. Lưu ý P chỉ kết nối trực tiếp với một phần của swarm, dù có thể quyết định liên hệ thêm peer hoặc yêu cầu thêm từ tracker. Để thiết lập kết nối BitTorrent với một peer sau khi đã có kết nối TCP, P gửi peer ID và swarm ID của mình, peer kia trả lời với peer ID và swarm ID của nó. Nếu swarm ID không khớp, hoặc peer ID trả về không đúng, kết nối bị hủy.

Kết nối BitTorrent kết quả là đối xứng: mỗi bên có thể tải từ bên kia. Mỗi bên bắt đầu bằng cách gửi bitmap báo các mảnh mình có, để mỗi peer biết trạng thái ban đầu của bên kia. Bất cứ khi nào một downloader (D) tải xong một mảnh, nó gửi thông điệp xác định mảnh đó cho các peer kết nối trực tiếp, để họ cập nhật trạng thái của D. Đây chính là câu trả lời cho câu hỏi làm thế nào yêu cầu tải mảnh được định tuyến đến peer có mảnh, vì mỗi peer biết peer nào kết nối trực tiếp có mảnh đó. Nếu D cần mảnh mà không peer nào kết nối có, nó có thể kết nối thêm peer (có thể lấy thêm từ tracker) hoặc chờ các peer kết nối tải được mảnh từ các kết nối của họ.

Làm thế nào để các đối tượng—ở đây là các mảnh—được ánh xạ lên các peer? Thực ra mỗi peer cuối cùng sẽ có tất cả các mảnh, nên câu hỏi là peer có mảnh nào tại một thời điểm trước khi có đủ, hoặc tương đương là thứ tự tải mảnh. Câu trả lời là các peer tải mảnh theo thứ tự ngẫu nhiên, để tránh trường hợp có tập con hoặc tập cha nghiêm ngặt các mảnh của bất kỳ peer nào.

BitTorrent như mô tả đến đây sử dụng tracker trung tâm, là điểm lỗi duy nhất cho swarm và có thể là nút cổ chai hiệu năng. Ngoài ra, việc cung cấp tracker có thể gây phiền toái cho ai muốn chia sẻ file qua BitTorrent. Các phiên bản mới của BitTorrent hỗ trợ swarm “không tracker” dùng DHT. Phần mềm BitTorrent hỗ trợ trackerless triển khai không chỉ peer BitTorrent mà còn cả *peer finder* (BitTorrent gọi là *node*), dùng để tìm peer.

Peer finder tạo mạng phủ riêng, dùng giao thức riêng qua UDP để triển khai DHT. Hơn nữa, mạng peer finder bao gồm các finder có peer thuộc các swarm khác nhau. Nói cách khác, mỗi swarm là mạng riêng các peer BitTorrent, còn mạng peer finder trải rộng qua nhiều swarm.

Peer finder tự sinh finder ID ngẫu nhiên, cùng kích thước (160 bit) với swarm ID. Mỗi finder duy trì bảng nhỏ chủ yếu chứa các finder (và peer liên kết) có ID gần với mình, cộng một số finder xa hơn. Thuật toán sau đảm bảo các finder có ID gần swarm ID sẽ biết về peer của swarm đó; đồng thời cung cấp cách tra cứu. Khi finder F cần tìm peer của swarm, nó gửi yêu cầu đến các finder trong bảng có ID gần swarm ID. Nếu finder được hỏi biết peer nào của swarm, nó trả về thông tin liên hệ. Nếu không, nó trả về thông tin các finder trong bảng gần swarm, để F truy vấn tiếp.

Sau khi tìm kiếm cạn kiệt, vì không còn finder nào gần swarm hơn, F chèn thông tin liên hệ của mình và peer liên kết vào các finder gần swarm nhất. Kết quả là các peer của swarm được lưu trong bảng của các finder gần swarm.

Cách này giả định F đã là thành viên mạng finder, tức là đã biết cách liên hệ một số finder khác. Điều này đúng với các cài đặt finder đã chạy trước đó, vì chúng phải lưu thông tin về finder khác, kể cả qua các lần chạy. Nếu swarm dùng tracker, các peer có thể báo cho finder về finder khác (đảo vai peer và finder) vì giao thức peer BitTorrent đã mở rộng để trao đổi thông tin finder. Nhưng, làm sao finder mới cài biết finder khác? File cho swarm trackerless chứa thông tin liên hệ một hoặc vài finder, thay vì URL tracker, cho trường hợp này.

Một điểm đặc biệt của BitTorrent là nó đối mặt trực tiếp với vấn đề công bằng, hay “công dân mạng tốt”. Các giao thức thường phụ thuộc vào hành vi tốt của peer mà không thể cưỡng chế. Ví dụ, một peer Ethernet không trung thực có thể đạt hiệu năng tốt hơn bằng thuật toán backoff tích cực hơn exponential backoff, hoặc peer TCP không trung thực có thể đạt hiệu năng tốt hơn bằng cách không hợp tác kiểm soát nghẽn.

Hành vi tốt mà BitTorrent phụ thuộc là các peer tải lên mảnh cho peer khác. Vì người dùng BitTorrent thường chỉ muốn tải file càng nhanh càng tốt, có xu hướng viết peer chỉ tải về mà không tải lên—đây là peer xấu. Để ngăn chặn, giao thức BitTorrent có cơ chế cho phép peer thưởng hoặc phạt nhau. Nếu một peer không tải lên cho peer khác, peer kia có thể *choke* peer xấu: quyết định ngừng tải lên cho peer đó, ít nhất tạm thời, và gửi thông điệp báo. Cũng có thông điệp báo peer đã được unchoke. Cơ chế choke cũng được dùng để peer giới hạn số kết nối BitTorrent đang hoạt động, nhằm duy trì hiệu năng TCP tốt. Có nhiều thuật toán choke, và thiết kế một thuật toán tốt là cả một nghệ thuật.

9.4.3 Mạng phân phối nội dung (Content Distribution Networks)
------------------------------------------------------------

Chúng ta đã thấy HTTP chạy trên TCP cho phép trình duyệt lấy trang từ máy chủ web. Tuy nhiên, ai từng chờ lâu để tải trang web đều biết hệ thống còn xa mới hoàn hảo. Xét rằng xương sống Internet giờ đây là các liên kết 40-Gbps, không rõ tại sao lại như vậy. Nói chung, khi tải trang web, có bốn nút cổ chai tiềm năng:

-  *Dặm đầu tiên.* Internet có thể có liên kết tốc độ cao, nhưng điều đó không giúp bạn tải trang nhanh hơn khi bạn kết nối bằng đường DSL 1.5Mbps hoặc kết nối không dây kém.

-  *Dặm cuối.* Liên kết kết nối máy chủ với Internet có thể bị quá tải bởi quá nhiều yêu cầu, dù tổng băng thông của liên kết khá cao.

-  *Bản thân máy chủ.* Máy chủ có tài nguyên hữu hạn (CPU, RAM, băng thông đĩa, v.v.) và có thể bị quá tải bởi quá nhiều yêu cầu đồng thời.

-  *Điểm kết nối ISP.* Một số ISP hợp thành xương sống Internet có thể có đường ống nội bộ tốc độ cao, nhưng họ không có động lực cung cấp kết nối tốc độ cao cho ISP khác. Nếu bạn kết nối ISP A và máy chủ ở ISP B, trang bạn yêu cầu có thể bị rớt ở điểm A và B kết nối với nhau.

Không ai ngoài bạn có thể làm gì với vấn đề đầu tiên, nhưng có thể dùng sao chép để giải quyết các vấn đề còn lại. Các hệ thống làm điều này thường gọi là *Mạng phân phối nội dung* (CDN). Akamai vận hành CDN nổi tiếng nhất.

Ý tưởng của CDN là phân phối địa lý một tập *máy chủ thay thế* (server surrogate) lưu cache các trang thường được lưu ở một tập *máy chủ backend*. Nhờ đó, thay vì hàng triệu người dùng phải chờ khi có tin nóng—tình huống này gọi là *flash crowd*—có thể phân tán tải lên nhiều máy chủ. Hơn nữa, thay vì phải đi qua nhiều ISP để đến ``www.cnn.com``, nếu các máy chủ thay thế này được phân bố trên tất cả các ISP xương sống, thì có thể đến một máy chủ mà không phải đi qua điểm kết nối ISP. Rõ ràng, duy trì hàng nghìn máy chủ thay thế trên Internet là quá đắt cho bất kỳ trang nào muốn cải thiện truy cập trang web của mình. CDN thương mại cung cấp dịch vụ này cho nhiều trang, nhờ đó chia sẻ chi phí cho nhiều khách hàng.

Dù gọi là máy chủ thay thế, thực ra chúng cũng có thể coi là cache. Nếu không có trang được client yêu cầu, chúng hỏi máy chủ backend. Tuy nhiên, trên thực tế, máy chủ backend chủ động sao chép dữ liệu sang các máy chủ thay thế thay vì chờ yêu cầu. Thường chỉ các trang tĩnh, không phải nội dung động, được phân phối qua các máy chủ thay thế. Client phải truy cập máy chủ backend cho nội dung thay đổi thường xuyên (ví dụ, điểm số thể thao, giá cổ phiếu) hoặc sinh ra từ tính toán (ví dụ, truy vấn cơ sở dữ liệu).

.. _fig-cdn:
.. figure:: figures/f09-30-9780123850591.png
   :width: 600px
   :align: center

   Các thành phần trong mạng phân phối nội dung (CDN).

Có một tập máy chủ phân phối địa lý lớn vẫn chưa giải quyết hết vấn đề. Để hoàn thiện, CDN cũng cần cung cấp một tập *bộ chuyển hướng* (redirector) chuyển tiếp yêu cầu client đến máy chủ phù hợp nhất, như minh họa trong :numref:`Hình %s <fig-cdn>`. Mục tiêu chính của redirector là chọn máy chủ cho mỗi yêu cầu sao cho *thời gian phản hồi* cho client là tốt nhất. Mục tiêu phụ là hệ thống xử lý được nhiều yêu cầu mỗi giây nhất có thể, tức là *thông lượng hệ thống*, chủ yếu khi hệ thống quá tải, như khi flash crowd truy cập một tập trang nhỏ hoặc khi bị tấn công DDoS, như CNN, Yahoo và nhiều trang lớn từng gặp năm 2000.

CDN dùng nhiều yếu tố để quyết định phân phối yêu cầu client. Ví dụ, để giảm thời gian phản hồi, redirector có thể chọn máy chủ dựa trên *gần mạng* (network proximity). Ngược lại, để tăng thông lượng hệ thống, nên cân bằng tải đều trên các máy chủ. Cả hai đều được cải thiện nếu cơ chế phân phối xét đến *tính cục bộ*; tức là, chọn máy chủ có khả năng đã có trang được yêu cầu trong cache. Kết hợp các yếu tố nào là tối ưu vẫn còn tranh luận. Phần này xét một số khả năng.

Cơ chế
~~~~~~

Như mô tả đến đây, redirector chỉ là một hàm trừu tượng, dù nghe giống việc router làm vì nó chuyển tiếp yêu cầu giống như router chuyển tiếp gói tin. Thực tế, có nhiều cơ chế để triển khai chuyển hướng. Lưu ý, giả định mỗi redirector biết địa chỉ mọi máy chủ sẵn có. (Từ đây, bỏ từ “thay thế” và chỉ nói về máy chủ.) Thực tế, có một số giao tiếp ngoài băng để cập nhật thông tin này khi máy chủ thay đổi.

Đầu tiên, chuyển hướng có thể triển khai bằng cách bổ sung DNS để trả về địa chỉ máy chủ khác nhau cho client. Ví dụ, khi client hỏi phân giải tên ``www.cnn.com``, máy chủ DNS có thể trả về địa chỉ máy chủ lưu trang CNN có tải nhẹ nhất. Hoặc, với một tập máy chủ, chỉ cần trả về địa chỉ theo vòng tròn. Lưu ý, độ chi tiết của chuyển hướng dựa trên DNS thường ở mức site (ví dụ, ``cnn.com``) chứ không phải URL cụ thể (ví dụ, ``https://www.cnn.com/2020/11/12/politics/biden-wins-arizona/index.html``). Tuy nhiên, khi trả về liên kết nhúng, máy chủ có thể viết lại URL, nhờ đó trỏ client đến máy chủ phù hợp nhất cho đối tượng cụ thể.

CDN thương mại thường dùng kết hợp viết lại URL và chuyển hướng dựa trên DNS. Vì lý do mở rộng, máy chủ DNS cấp cao trước tiên trỏ đến máy chủ DNS vùng, máy này trả về địa chỉ máy chủ thực. Để phản hồi nhanh với thay đổi, các máy chủ DNS điều chỉnh TTL của bản ghi tài nguyên trả về xuống rất ngắn, ví dụ 20 giây. Điều này cần thiết để client không cache kết quả và luôn quay lại DNS để lấy ánh xạ URL-máy chủ mới nhất.

Một khả năng khác là dùng tính năng chuyển hướng HTTP: Client gửi yêu cầu đến một máy chủ, máy chủ trả về máy chủ mới (tốt hơn) để client liên hệ lấy trang. Không may, chuyển hướng dựa trên máy chủ gây thêm một vòng lặp qua Internet, và tệ hơn, máy chủ có thể bị quá tải bởi chính nhiệm vụ chuyển hướng. Thay vào đó, nếu có một nút gần client (ví dụ, proxy web cục bộ) biết các máy chủ sẵn có, nó có thể chặn yêu cầu và hướng client đến máy chủ phù hợp. Trong trường hợp này, redirector phải nằm ở điểm nghẽn để mọi yêu cầu đi qua, hoặc client phải hợp tác bằng cách gửi yêu cầu qua proxy (giống proxy truyền thống, không phải proxy trong suốt).

Bạn có thể tự hỏi CDN liên quan gì đến mạng phủ, và dù coi CDN là mạng phủ có phần gượng ép, chúng có một điểm chung quan trọng. Giống như nút phủ, redirector dựa trên proxy đưa ra quyết định định tuyến ở tầng ứng dụng. Thay vì chuyển tiếp gói tin dựa trên địa chỉ và kiến thức về cấu trúc mạng, nó chuyển tiếp yêu cầu HTTP dựa trên URL và kiến thức về vị trí, tải của tập máy chủ. Kiến trúc Internet hiện nay không hỗ trợ chuyển hướng trực tiếp—ý là client gửi yêu cầu HTTP đến redirector, redirector chuyển tiếp đến đích—nên chuyển hướng thường được triển khai gián tiếp bằng cách redirector trả về địa chỉ đích phù hợp và client tự liên hệ máy chủ.

Chính sách
~~~~~~~~~~

Bây giờ xét một số chính sách mà redirector có thể dùng để chuyển tiếp yêu cầu. Thực ra, chúng ta đã đề xuất một chính sách đơn giản—vòng tròn. Một cách tương tự là chọn ngẫu nhiên một máy chủ sẵn có. Cả hai đều phân phối tải đều trên CDN, nhưng không giảm thời gian phản hồi cho client.

Rõ ràng, cả hai cách trên đều không xét đến gần mạng, nhưng quan trọng không kém, chúng cũng bỏ qua tính cục bộ. Tức là, các yêu cầu cho cùng một URL được chuyển đến các máy chủ khác nhau, làm giảm khả năng trang được phục vụ từ cache bộ nhớ của máy chủ. Điều này buộc máy chủ phải lấy trang từ đĩa, hoặc thậm chí từ máy chủ backend. Làm thế nào để một tập redirector phân tán đảm bảo các yêu cầu cho cùng một trang đến cùng một máy chủ (hoặc một nhóm nhỏ máy chủ) mà không cần điều phối toàn cục? Câu trả lời khá đơn giản: Tất cả redirector dùng một dạng băm để ánh xạ URL vào một tập giá trị nhỏ. Lợi ích chính là không cần giao tiếp giữa các redirector để phối hợp; bất kể redirector nào nhận URL, quá trình băm cho cùng một kết quả.

Vậy thế nào là một thuật toán băm tốt? Thuật toán băm *modulo* cổ điển—băm mỗi URL modulo số máy chủ—không phù hợp ở đây. Vì nếu số máy chủ thay đổi, phép modulo sẽ khiến phần lớn trang bị đổi máy chủ. Dù không mong đợi thay đổi tập máy chủ thường xuyên, việc thêm máy chủ mới gây thay đổi lớn là không mong muốn.

Một cách thay thế là dùng thuật toán *băm nhất quán* như phần trước. Cụ thể, mỗi redirector băm từng máy chủ lên vòng tròn đơn vị. Sau đó, với mỗi URL đến, redirector cũng băm URL lên vòng tròn, và URL được gán cho máy chủ gần nhất trên vòng tròn với giá trị băm của nó. Nếu một nút hỏng, tải của nó chuyển sang láng giềng (trên vòng tròn), nên việc thêm hoặc loại bỏ máy chủ chỉ gây thay đổi cục bộ. Lưu ý, khác với mạng ngang hàng, nơi thông điệp được định tuyến qua các nút để tìm máy chủ gần nhất với đối tượng, mỗi redirector biết cách ánh xạ tập máy chủ lên vòng tròn, nên có thể tự chọn máy chủ “gần nhất”.

Chiến lược này dễ dàng mở rộng để xét đến tải máy chủ. Giả sử redirector biết tải hiện tại của từng máy chủ. Thông tin này có thể không hoàn toàn cập nhật, nhưng redirector có thể đếm số lần chuyển tiếp yêu cầu đến từng máy chủ trong vài giây gần nhất và dùng làm ước lượng tải. Khi nhận URL, redirector băm URL cộng từng máy chủ và sắp xếp các giá trị. Danh sách này xác định thứ tự redirector xét các máy chủ. Redirector đi xuống danh sách cho đến khi gặp máy chủ có tải dưới ngưỡng. Lợi ích so với băm nhất quán thuần túy là thứ tự máy chủ khác nhau cho mỗi URL, nên nếu một máy chủ hỏng, tải của nó được phân phối đều cho các máy khác. Cách này là cơ sở cho Giao thức Định tuyến Mảng Cache (CARP) và được mô tả bằng mã giả dưới đây.

::

   SelectServer(URL, S)
       for each server s in server set S
           score[s] = hash(URL, address[s])
       sort score
       for each server s in decreasing order of score
           if Load(s) < threshold then
               return s
       return server with highest score

Khi tải tăng, cách này chuyển từ chỉ dùng máy chủ đầu tiên trong danh sách sang phân phối yêu cầu cho nhiều máy chủ. Một số trang thường do máy chủ bận xử lý cũng sẽ được các máy chủ ít bận hơn chia sẻ. Vì quá trình này dựa trên tải tổng thể máy chủ chứ không phải độ phổ biến của từng trang, các máy chủ lưu trang phổ biến có thể có nhiều máy chủ chia sẻ tải hơn các máy chủ lưu trang ít phổ biến. Trong quá trình này, một số trang ít phổ biến cũng sẽ được sao chép chỉ vì chúng chủ yếu nằm trên máy chủ bận. Đồng thời, nếu một số trang trở nên cực kỳ phổ biến, có thể tất cả máy chủ đều phải phục vụ chúng.

Cuối cùng, có thể đưa yếu tố gần mạng vào quyết định theo ít nhất hai cách. Đầu tiên là làm mờ ranh giới giữa tải máy chủ và gần mạng bằng cách đo thời gian máy chủ phản hồi yêu cầu và dùng số đo này làm tham số “tải máy chủ” trong thuật toán trên. Cách này ưu tiên máy chủ gần/ít tải hơn máy chủ xa/nhiều tải. Cách thứ hai là xét gần mạng ở giai đoạn sớm hơn bằng cách giới hạn tập máy chủ ứng viên (*S*) chỉ còn các máy chủ gần. Vấn đề khó là quyết định máy chủ nào đủ gần. Một cách là chỉ chọn máy chủ cùng ISP với client. Cách tinh vi hơn là xem bản đồ hệ tự trị (AS) do BGP tạo và chỉ chọn máy chủ trong một số hop từ client làm ứng viên. Tìm cân bằng giữa gần mạng và cục bộ cache là chủ đề nghiên cứu tiếp tục.
