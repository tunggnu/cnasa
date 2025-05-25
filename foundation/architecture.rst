1.3 Kiến trúc
=============

Phần trước đã xác lập một tập hợp khá lớn các yêu cầu cho thiết kế mạng—một mạng máy tính phải cung cấp khả năng kết nối tổng quát, hiệu quả về chi phí, công bằng và mạnh mẽ giữa một số lượng lớn máy tính. Như thể điều này vẫn chưa đủ, các mạng không giữ nguyên ở một thời điểm cố định mà phải phát triển để thích nghi với cả những thay đổi trong các công nghệ nền tảng mà chúng dựa vào cũng như những thay đổi trong các yêu cầu do các chương trình ứng dụng đặt ra. Hơn nữa, các mạng phải có khả năng được quản lý bởi con người với các trình độ kỹ năng khác nhau. Thiết kế một mạng đáp ứng các yêu cầu này không phải là một nhiệm vụ nhỏ.

Để giúp xử lý sự phức tạp này, các nhà thiết kế mạng đã phát triển các bản thiết kế tổng quát—thường được gọi là *kiến trúc mạng*—để định hướng cho việc thiết kế và triển khai các mạng. Phần này sẽ định nghĩa cẩn thận hơn ý nghĩa của kiến trúc mạng bằng cách giới thiệu các ý tưởng trung tâm chung cho tất cả các kiến trúc mạng. Nó cũng giới thiệu hai kiến trúc được tham chiếu rộng rãi nhất—kiến trúc OSI (hoặc mô hình 7 tầng) và kiến trúc Internet.

1.3.1 Phân tầng và Giao thức
----------------------------

Trừu tượng hóa—việc ẩn các chi tiết triển khai đằng sau một giao diện được định nghĩa rõ ràng—là công cụ cơ bản mà các nhà thiết kế hệ thống sử dụng để quản lý sự phức tạp. Ý tưởng của một trừu tượng hóa là định nghĩa một mô hình có thể nắm bắt một khía cạnh quan trọng nào đó của hệ thống, đóng gói mô hình này trong một đối tượng cung cấp một giao diện có thể được thao tác bởi các thành phần khác của hệ thống, và ẩn đi các chi tiết về cách đối tượng đó được triển khai khỏi người dùng của đối tượng. Thách thức là xác định các trừu tượng vừa cung cấp một dịch vụ hữu ích trong nhiều tình huống khác nhau, vừa có thể được triển khai hiệu quả trên hệ thống nền tảng. Đây chính xác là điều chúng ta đã làm khi giới thiệu ý tưởng về một kênh trong phần trước: chúng ta đã cung cấp một trừu tượng cho các ứng dụng, giúp ẩn đi sự phức tạp của mạng khỏi người lập trình ứng dụng.

.. _fig-layers1:
.. figure:: figures/f01-08-9780123850591.png
   :width: 300px
   :align: center

   Ví dụ về một hệ thống mạng phân tầng.

Các trừu tượng tự nhiên dẫn đến việc phân tầng, đặc biệt là trong các hệ thống mạng. Ý tưởng chung là bạn bắt đầu với các dịch vụ do phần cứng nền tảng cung cấp rồi thêm vào một chuỗi các tầng, mỗi tầng cung cấp một mức dịch vụ cao hơn (trừu tượng hơn). Các dịch vụ được cung cấp ở các tầng cao được triển khai dựa trên các dịch vụ do các tầng thấp cung cấp. Dựa trên cuộc thảo luận về các yêu cầu ở phần trước, ví dụ, chúng ta có thể hình dung một mạng đơn giản có hai tầng trừu tượng nằm giữa chương trình ứng dụng và phần cứng nền tảng, như minh họa trong :numref:`Hình %s <fig-layers1>`. Tầng ngay phía trên phần cứng trong trường hợp này có thể cung cấp kết nối host-to-host, trừu tượng hóa đi thực tế rằng có thể tồn tại một cấu trúc liên kết mạng phức tạp bất kỳ giữa hai host. Tầng tiếp theo xây dựng dựa trên dịch vụ giao tiếp host-to-host có sẵn và cung cấp hỗ trợ cho các kênh process-to-process, trừu tượng hóa đi thực tế rằng mạng đôi khi có thể làm mất thông điệp, chẳng hạn.

Phân tầng cung cấp hai đặc điểm hữu ích. Thứ nhất, nó phân rã vấn đề xây dựng một mạng thành các thành phần dễ quản lý hơn. Thay vì triển khai một phần mềm nguyên khối làm mọi thứ bạn từng muốn, bạn có thể triển khai nhiều tầng, mỗi tầng giải quyết một phần của vấn đề. Thứ hai, nó cung cấp một thiết kế mô-đun hơn. Nếu bạn quyết định muốn thêm một dịch vụ mới, bạn có thể chỉ cần sửa đổi chức năng ở một tầng, tái sử dụng các chức năng do tất cả các tầng khác cung cấp.

Tuy nhiên, nghĩ về một hệ thống như một chuỗi tuyến tính các tầng là một sự đơn giản hóa quá mức. Nhiều khi có nhiều trừu tượng khác nhau được cung cấp ở một mức nhất định của hệ thống, mỗi trừu tượng cung cấp một dịch vụ khác nhau cho các tầng cao hơn nhưng đều xây dựng dựa trên cùng các trừu tượng tầng thấp. Để thấy điều này, hãy xem xét hai loại kênh đã thảo luận ở phần trước. Một loại cung cấp dịch vụ request/reply và một loại hỗ trợ dịch vụ message stream. Hai kênh này có thể là các lựa chọn thay thế ở một mức nào đó của hệ thống mạng nhiều tầng, như minh họa trong :numref:`Hình %s <fig-layers2>`.

.. _fig-layers2:
.. figure:: figures/f01-09-9780123850591.png
   :width: 300px
   :align: center

   Hệ thống phân tầng với các trừu tượng thay thế có sẵn ở một tầng nhất định.

Dựa trên cuộc thảo luận về phân tầng này, chúng ta đã sẵn sàng để thảo luận về kiến trúc của một mạng một cách chính xác hơn. Trước hết, các đối tượng trừu tượng tạo nên các tầng của một hệ thống mạng được gọi là *giao thức* (protocol). Tức là, một giao thức cung cấp một dịch vụ giao tiếp mà các đối tượng tầng cao hơn (chẳng hạn như tiến trình ứng dụng, hoặc có thể là các giao thức tầng cao hơn) sử dụng để trao đổi thông điệp. Ví dụ, chúng ta có thể hình dung một mạng hỗ trợ một giao thức request/reply và một giao thức message stream, tương ứng với các kênh request/reply và message stream đã thảo luận ở trên.

Mỗi giao thức định nghĩa hai giao diện khác nhau. Thứ nhất, nó định nghĩa một *giao diện dịch vụ* (service interface) cho các đối tượng khác trên cùng máy tính muốn sử dụng dịch vụ giao tiếp của nó. Giao diện dịch vụ này định nghĩa các thao tác mà các đối tượng cục bộ có thể thực hiện trên giao thức. Ví dụ, một giao thức request/reply sẽ hỗ trợ các thao tác cho phép một ứng dụng gửi và nhận thông điệp. Một triển khai của giao thức HTTP có thể hỗ trợ một thao tác để lấy một trang hypertext từ một máy chủ từ xa. Một ứng dụng như trình duyệt web sẽ gọi thao tác này bất cứ khi nào trình duyệt cần lấy một trang mới (ví dụ, khi người dùng nhấn vào một liên kết trên trang đang hiển thị).

Thứ hai, một giao thức định nghĩa một *giao diện peer* (peer interface) cho đối tác (peer) của nó trên một máy khác. Giao diện thứ hai này định nghĩa hình thức và ý nghĩa của các thông điệp được trao đổi giữa các peer giao thức để triển khai dịch vụ giao tiếp. Điều này sẽ xác định cách mà một giao thức request/reply trên một máy giao tiếp với peer của nó trên máy khác. Trong trường hợp HTTP, ví dụ, đặc tả giao thức định nghĩa chi tiết cách một lệnh *GET* được định dạng, các tham số nào có thể dùng với lệnh này, và máy chủ web nên phản hồi thế nào khi nhận được lệnh như vậy.

Tóm lại, một giao thức định nghĩa một dịch vụ giao tiếp mà nó xuất ra cục bộ (giao diện dịch vụ), cùng với một tập hợp các quy tắc điều chỉnh các thông điệp mà giao thức trao đổi với peer của nó để triển khai dịch vụ này (giao diện peer). Tình huống này được minh họa trong :numref:`Hình %s <fig-interfaces>`.

.. _fig-interfaces:
.. figure:: figures/f01-10-9780123850591.png
   :width: 500px
   :align: center

   Giao diện dịch vụ và giao diện peer.

Trừ tầng phần cứng, nơi các peer giao tiếp trực tiếp với nhau qua môi trường vật lý, giao tiếp peer-to-peer là gián tiếp—mỗi giao thức giao tiếp với peer của nó bằng cách gửi thông điệp cho một giao thức tầng thấp hơn, giao thức này lại chuyển thông điệp cho peer của *nó*. Ngoài ra, có thể có nhiều hơn một giao thức ở bất kỳ tầng nào, mỗi giao thức cung cấp một dịch vụ giao tiếp khác nhau. Do đó, chúng ta biểu diễn bộ giao thức tạo nên một hệ thống mạng bằng một *đồ thị giao thức* (protocol graph). Các nút của đồ thị tương ứng với các giao thức, và các cạnh biểu diễn quan hệ *phụ thuộc vào* (depends on). Ví dụ, :numref:`Hình %s <fig-protgraph>` minh họa một đồ thị giao thức cho hệ thống phân tầng giả định mà chúng ta đã thảo luận—các giao thức RRP (Request/Reply Protocol) và MSP (Message Stream Protocol) triển khai hai loại kênh process-to-process khác nhau, và cả hai đều phụ thuộc vào Host-to-Host Protocol (HHP) cung cấp dịch vụ kết nối host-to-host.

.. _fig-protgraph:
.. figure:: figures/f01-11-9780123850591.png
   :width: 500px
   :align: center

   Ví dụ về một đồ thị giao thức.

Trong ví dụ này, giả sử chương trình truy cập tệp trên host 1 muốn gửi một thông điệp tới peer của nó trên host 2 bằng dịch vụ giao tiếp do RRP cung cấp. Trong trường hợp này, ứng dụng tệp yêu cầu RRP gửi thông điệp thay cho nó. Để giao tiếp với peer của mình, RRP gọi các dịch vụ của HHP, HHP lại truyền thông điệp tới peer của nó trên máy khác. Khi thông điệp đã đến HHP trên host 2, HHP chuyển thông điệp lên RRP, RRP lại chuyển thông điệp cho ứng dụng tệp. Trong trường hợp cụ thể này, ứng dụng được cho là sử dụng *ngăn xếp giao thức* RRP/HHP.

Lưu ý rằng thuật ngữ *giao thức* được dùng theo hai nghĩa khác nhau. Đôi khi nó chỉ các giao diện trừu tượng—tức là, các thao tác được định nghĩa bởi giao diện dịch vụ và hình thức, ý nghĩa của các thông điệp trao đổi giữa các peer, và đôi khi nó chỉ module thực sự triển khai hai giao diện này. Để phân biệt giữa các giao diện và module triển khai các giao diện này, chúng ta thường gọi cái trước là *đặc tả giao thức* (protocol specification). Đặc tả thường được thể hiện bằng sự kết hợp giữa văn bản, mã giả, sơ đồ chuyển trạng thái, hình ảnh định dạng gói tin, và các ký hiệu trừu tượng khác. Nên đảm bảo rằng một giao thức có thể được triển khai theo nhiều cách khác nhau bởi các lập trình viên khác nhau, miễn là mỗi người tuân thủ đặc tả. Thách thức là đảm bảo rằng hai triển khai khác nhau của cùng một đặc tả có thể trao đổi thông điệp thành công. Hai hoặc nhiều module giao thức thực sự triển khai đúng đặc tả giao thức được gọi là *tương tác được* (interoperate) với nhau.

Chúng ta có thể hình dung nhiều giao thức và đồ thị giao thức khác nhau đáp ứng các yêu cầu giao tiếp của một tập hợp ứng dụng. May mắn thay, tồn tại các tổ chức tiêu chuẩn hóa, như Internet Engineering Task Force (IETF) và International Standards Organization (ISO), thiết lập các chính sách cho một đồ thị giao thức cụ thể. Chúng ta gọi tập hợp các quy tắc điều chỉnh hình thức và nội dung của một đồ thị giao thức là *kiến trúc mạng* (network architecture). Mặc dù vượt ra ngoài phạm vi cuốn sách này, các tổ chức tiêu chuẩn hóa đã thiết lập các quy trình rõ ràng để giới thiệu, xác thực và cuối cùng phê duyệt các giao thức trong các kiến trúc tương ứng của họ. Chúng tôi sẽ mô tả ngắn gọn các kiến trúc do IETF và ISO định nghĩa ngay sau đây, nhưng trước tiên còn hai điều nữa cần giải thích về cơ chế phân tầng giao thức.

1.3.2 Đóng gói (Encapsulation)
------------------------------

Hãy xem điều gì xảy ra trong :numref:`Hình %s <fig-protgraph>` khi một trong các chương trình ứng dụng gửi một thông điệp tới peer của nó bằng cách chuyển thông điệp cho RRP. Từ góc nhìn của RRP, thông điệp mà nó nhận từ ứng dụng là một chuỗi byte không được diễn giải. RRP không quan tâm các byte này đại diện cho một mảng số nguyên, một thông điệp email, một ảnh số hóa, hay bất cứ thứ gì; nó chỉ có nhiệm vụ gửi chúng tới peer của nó. Tuy nhiên, RRP phải truyền thông tin điều khiển tới peer, hướng dẫn nó cách xử lý thông điệp khi nhận được. RRP làm điều này bằng cách gắn một *header* (phần đầu) vào thông điệp. Nói chung, header là một cấu trúc dữ liệu nhỏ—từ vài byte đến vài chục byte—được các peer sử dụng để giao tiếp với nhau. Như tên gọi, header thường được gắn vào đầu thông điệp. Tuy nhiên, trong một số trường hợp, thông tin điều khiển peer-to-peer này được gửi ở cuối thông điệp, khi đó nó được gọi là *trailer*. Định dạng chính xác của header do RRP gắn vào được xác định bởi đặc tả giao thức của nó. Phần còn lại của thông điệp—tức là, dữ liệu được truyền thay mặt cho ứng dụng—được gọi là *body* hoặc *payload* của thông điệp. Chúng ta nói rằng dữ liệu của ứng dụng được *đóng gói* (encapsulated) trong thông điệp mới do RRP tạo ra.

.. _fig-encapsulation:
.. figure:: figures/f01-12-9780123850591.png
   :width: 500px
   :align: center

   Thông điệp tầng cao được đóng gói bên trong thông điệp tầng thấp.

Quá trình đóng gói này sau đó được lặp lại ở mỗi tầng của đồ thị giao thức; ví dụ, HHP đóng gói thông điệp của RRP bằng cách gắn một header của riêng nó. Nếu bây giờ giả sử HHP gửi thông điệp tới peer của nó qua một mạng nào đó, thì khi thông điệp đến host đích, nó được xử lý theo thứ tự ngược lại: HHP trước tiên diễn giải header HHP ở đầu thông điệp (tức là, thực hiện hành động phù hợp dựa trên nội dung của header) và chuyển phần body của thông điệp (nhưng không kèm header HHP) lên RRP, RRP lại thực hiện hành động được chỉ định bởi header RRP mà peer của nó đã gắn vào và chuyển phần body của thông điệp (nhưng không kèm header RRP) lên chương trình ứng dụng. Thông điệp được chuyển lên từ RRP tới ứng dụng trên host 2 chính là thông điệp mà ứng dụng đã chuyển xuống RRP trên host 1; ứng dụng không nhìn thấy bất kỳ header nào đã được gắn vào để triển khai các dịch vụ giao tiếp tầng thấp hơn. Toàn bộ quá trình này được minh họa trong :numref:`Hình %s <fig-encapsulation>`. Lưu ý rằng trong ví dụ này, các nút trong mạng (ví dụ, switch và router) có thể kiểm tra header HHP ở đầu thông điệp.

Lưu ý rằng khi chúng ta nói một giao thức tầng thấp không diễn giải thông điệp mà nó nhận từ một giao thức tầng cao, ý là nó không biết cách trích xuất bất kỳ ý nghĩa nào từ dữ liệu chứa trong thông điệp. Tuy nhiên, đôi khi giao thức tầng thấp áp dụng một số biến đổi đơn giản lên dữ liệu mà nó nhận, chẳng hạn như nén hoặc mã hóa nó. Trong trường hợp này, giao thức đang biến đổi toàn bộ body của thông điệp, bao gồm cả dữ liệu gốc của ứng dụng và tất cả các header do các giao thức tầng cao hơn gắn vào dữ liệu đó.

1.3.3 Ghép kênh và Tách kênh (Multiplexing and Demultiplexing)
--------------------------------------------------------------

Hãy nhớ rằng một ý tưởng cơ bản của chuyển mạch gói là ghép kênh nhiều luồng dữ liệu qua một liên kết vật lý duy nhất. Ý tưởng này cũng áp dụng lên xuống đồ thị giao thức, không chỉ ở các nút chuyển mạch. Trong :numref:`Hình %s <fig-protgraph>`, ví dụ, chúng ta có thể coi RRP như đang triển khai một kênh giao tiếp logic, với các thông điệp từ hai ứng dụng khác nhau được ghép kênh qua kênh này tại host nguồn và sau đó được tách kênh trở lại ứng dụng phù hợp tại host đích.

Trên thực tế, điều này đơn giản có nghĩa là header mà RRP gắn vào thông điệp của nó chứa một định danh ghi lại ứng dụng mà thông điệp thuộc về. Chúng ta gọi định danh này là *khóa tách kênh* (demultiplexing key), hoặc viết tắt là *demux key* của RRP. Tại host nguồn, RRP chèn demux key phù hợp vào header của nó. Khi thông điệp được chuyển tới RRP trên host đích, nó loại bỏ header, kiểm tra demux key, và tách kênh thông điệp tới đúng ứng dụng.

RRP không phải là duy nhất trong việc hỗ trợ ghép kênh; gần như mọi giao thức đều triển khai cơ chế này. Ví dụ, HHP có demux key riêng để xác định thông điệp nào chuyển lên RRP và thông điệp nào chuyển lên MSP. Tuy nhiên, không có sự thống nhất giữa các giao thức—ngay cả trong cùng một kiến trúc mạng—về chính xác demux key là gì. Một số giao thức dùng trường 8 bit (nghĩa là chỉ hỗ trợ được 256 giao thức tầng cao), số khác dùng trường 16 hoặc 32 bit. Ngoài ra, một số giao thức có một trường tách kênh duy nhất trong header, số khác có một cặp trường tách kênh. Trong trường hợp đầu, cùng một demux key được dùng ở cả hai phía giao tiếp, còn trong trường hợp sau, mỗi phía dùng một key khác nhau để xác định giao thức tầng cao (hoặc chương trình ứng dụng) mà thông điệp sẽ được chuyển tới.

1.3.4 Mô hình OSI
-----------------

ISO là một trong những tổ chức đầu tiên chính thức định nghĩa một cách chung để kết nối các máy tính. Kiến trúc của họ, gọi là *Open Systems Interconnection* (OSI) và minh họa trong :numref:`Hình %s <fig-osi>`, định nghĩa việc phân chia chức năng mạng thành bảy tầng, trong đó một hoặc nhiều giao thức triển khai chức năng được gán cho một tầng nhất định. Theo nghĩa này, sơ đồ trong :numref:`Hình %s <fig-osi>` không phải là một đồ thị giao thức, *per se*, mà là một *mô hình tham chiếu* cho một đồ thị giao thức. Nó thường được gọi là mô hình 7 tầng. Mặc dù hiện nay không còn mạng nào dựa trên OSI đang hoạt động, nhưng thuật ngữ mà nó định nghĩa vẫn được sử dụng rộng rãi, nên vẫn đáng để xem qua.

.. _fig-osi:
.. figure:: figures/f01-13-9780123850591.png
   :width: 600px
   :align: center

   Mô hình 7 tầng OSI.

Bắt đầu từ dưới lên, tầng *vật lý* (physical) xử lý việc truyền các bit thô qua một liên kết truyền thông. Tầng *liên kết dữ liệu* (data link) sau đó gom một dòng bit thành một tập hợp lớn hơn gọi là *frame* (khung). Các bộ điều hợp mạng, cùng với trình điều khiển thiết bị chạy trong hệ điều hành của nút, thường triển khai tầng liên kết dữ liệu. Điều này có nghĩa là các khung, chứ không phải bit thô, thực sự được chuyển tới các host. Tầng *mạng* (network) xử lý việc định tuyến giữa các nút trong một mạng chuyển mạch gói. Ở tầng này, đơn vị dữ liệu trao đổi giữa các nút thường được gọi là *gói* (packet) thay vì khung, mặc dù về cơ bản chúng giống nhau. Ba tầng dưới được triển khai trên tất cả các nút mạng, bao gồm cả switch trong mạng và các host kết nối bên ngoài mạng. Tầng *giao vận* (transport) sau đó triển khai cái mà đến giờ chúng ta gọi là *kênh process-to-process*. Ở đây, đơn vị dữ liệu trao đổi thường được gọi là *thông điệp* (message) thay vì gói hay khung. Tầng giao vận và các tầng cao hơn thường chỉ chạy trên các host đầu cuối chứ không chạy trên các switch hoặc router trung gian.

Bỏ qua lên tầng trên cùng (tầng thứ bảy) và đi ngược lại xuống, chúng ta có tầng *ứng dụng* (application). Các giao thức tầng ứng dụng bao gồm những thứ như Hypertext Transfer Protocol (HTTP), là nền tảng của World Wide Web và cho phép trình duyệt web yêu cầu các trang từ máy chủ web. Bên dưới đó, tầng *trình bày* (presentation) quan tâm đến định dạng dữ liệu trao đổi giữa các peer—ví dụ, một số nguyên là 16, 32 hay 64 bit, byte quan trọng nhất được truyền trước hay sau, hoặc một luồng video được định dạng thế nào. Cuối cùng, tầng *phiên* (session) cung cấp không gian tên được dùng để liên kết các luồng giao vận có thể khác nhau thuộc về một ứng dụng. Ví dụ, nó có thể quản lý một luồng âm thanh và một luồng video được kết hợp trong một ứng dụng hội nghị truyền hình.

1.3.5 Kiến trúc Internet
------------------------

Kiến trúc Internet, đôi khi còn gọi là kiến trúc TCP/IP theo tên hai giao thức chính của nó, được mô tả trong :numref:`Hình %s <fig-internet1>`. Một cách biểu diễn khác được đưa ra trong :numref:`Hình %s <fig-internet2>`. Kiến trúc Internet phát triển từ kinh nghiệm với một mạng chuyển mạch gói trước đó gọi là ARPANET. Cả Internet và ARPANET đều được tài trợ bởi Advanced Research Projects Agency (ARPA), một trong các cơ quan tài trợ nghiên cứu và phát triển của Bộ Quốc phòng Hoa Kỳ. Internet và ARPANET xuất hiện trước kiến trúc OSI, và kinh nghiệm xây dựng chúng đã ảnh hưởng lớn đến mô hình tham chiếu OSI.

.. _fig-internet1:
.. figure:: figures/f01-14-9780123850591.png
   :width: 300px
   :align: center

   Đồ thị giao thức Internet.

.. _fig-internet2:
.. figure:: figures/f01-15-9780123850591.png
   :width: 250px
   :align: center

   Cách nhìn thay thế về kiến trúc Internet. Tầng "subnetwork" trước đây được gọi là tầng “network” và nay thường được gọi là “Layer 2” (theo ảnh hưởng của mô hình OSI).

Mặc dù mô hình 7 tầng OSI có thể, với một chút tưởng tượng, được áp dụng cho Internet, nhưng thường sử dụng một ngăn xếp đơn giản hơn. Ở tầng thấp nhất là nhiều giao thức mạng khác nhau, ký hiệu là NET\ :sub:`1`, NET\ :sub:`2`, v.v. Trên thực tế, các giao thức này được triển khai bằng sự kết hợp giữa phần cứng (ví dụ, bộ điều hợp mạng) và phần mềm (ví dụ, trình điều khiển thiết bị mạng). Ví dụ, bạn có thể thấy các giao thức Ethernet hoặc không dây (như chuẩn Wi-Fi 802.11) ở tầng này. (Các giao thức này thực ra có thể gồm nhiều tầng con, nhưng kiến trúc Internet không giả định gì về chúng.) Tầng tiếp theo chỉ gồm một giao thức—*Internet Protocol* (IP). Đây là giao thức hỗ trợ việc kết nối nhiều công nghệ mạng thành một mạng logic duy nhất. Tầng trên IP gồm hai giao thức chính—*Transmission Control Protocol* (TCP) và *User Datagram Protocol* (UDP). TCP và UDP cung cấp các kênh logic thay thế cho các chương trình ứng dụng: TCP cung cấp kênh luồng byte tin cậy, còn UDP cung cấp kênh truyền datagram không tin cậy (*datagram* có thể coi là đồng nghĩa với message). Trong ngôn ngữ Internet, TCP và UDP đôi khi được gọi là các giao thức *end-to-end*, mặc dù gọi chúng là giao thức *transport* cũng đúng.

Chạy phía trên tầng giao vận là một loạt các giao thức ứng dụng, như HTTP, FTP, Telnet (đăng nhập từ xa), và Simple Mail Transfer Protocol (SMTP), cho phép các ứng dụng phổ biến tương tác với nhau. Để hiểu sự khác biệt giữa giao thức tầng ứng dụng và ứng dụng, hãy nghĩ về tất cả các trình duyệt web khác nhau từng tồn tại (ví dụ, Firefox, Chrome, Safari, Netscape, Mosaic, Internet Explorer). Cũng có số lượng lớn các triển khai máy chủ web khác nhau. Lý do bạn có thể dùng bất kỳ chương trình ứng dụng nào trong số này để truy cập một trang web cụ thể là vì tất cả đều tuân thủ cùng một giao thức tầng ứng dụng: HTTP. Đôi khi, cùng một thuật ngữ được dùng cho cả ứng dụng và giao thức tầng ứng dụng mà nó sử dụng (ví dụ, FTP thường được dùng để chỉ cả ứng dụng triển khai giao thức FTP).

Hầu hết những người làm việc trong lĩnh vực mạng đều quen thuộc với cả kiến trúc Internet và mô hình 7 tầng OSI, và có sự đồng thuận chung về cách các tầng ánh xạ giữa hai kiến trúc. Tầng ứng dụng của Internet được coi là tầng 7, tầng giao vận là tầng 4, tầng IP (internetworking hoặc chỉ network) là tầng 3, và tầng liên kết hoặc subnet bên dưới IP là tầng 2.

.. sidebar:: IETF và Tiêu chuẩn hóa

   Mặc dù chúng ta gọi nó là "kiến trúc Internet" thay vì "kiến trúc IETF", nhưng có thể nói rằng IETF là tổ chức tiêu chuẩn hóa chính chịu trách nhiệm định nghĩa nó, cũng như đặc tả nhiều giao thức của nó, như TCP, UDP, IP, DNS và BGP. Nhưng kiến trúc Internet cũng bao gồm nhiều giao thức do các tổ chức khác định nghĩa, bao gồm chuẩn ethernet và Wi-Fi 802.11 của IEEE, chuẩn HTTP/HTML của W3C, chuẩn mạng di động 4G và 5G của 3GPP, và chuẩn mã hóa video H.232 của ITU-T, chỉ kể một vài ví dụ.

   Ngoài việc định nghĩa kiến trúc và đặc tả giao thức, còn có các tổ chức khác hỗ trợ mục tiêu lớn hơn là khả năng tương tác. Một ví dụ là IANA (Internet Assigned Numbers Authority), đúng như tên gọi, chịu trách nhiệm phân phát các định danh duy nhất cần thiết để các giao thức hoạt động. IANA, đến lượt mình, là một bộ phận thuộc ICANN (Internet Corporation for Assigned Names and Numbers), một tổ chức phi lợi nhuận chịu trách nhiệm quản lý tổng thể Internet.

Kiến trúc Internet có ba đặc điểm đáng chú ý. Thứ nhất, như minh họa rõ nhất trong :numref:`Hình %s <fig-internet2>`, kiến trúc Internet không ngụ ý phân tầng nghiêm ngặt. Ứng dụng có thể bỏ qua các tầng giao vận đã định nghĩa và sử dụng trực tiếp IP hoặc một trong các mạng nền tảng. Thực tế, lập trình viên có thể tự do định nghĩa các trừu tượng kênh mới hoặc ứng dụng chạy trên bất kỳ giao thức nào hiện có.

Thứ hai, nếu bạn nhìn kỹ vào đồ thị giao thức trong :numref:`Hình %s <fig-internet1>`, bạn sẽ nhận thấy hình dạng đồng hồ cát—rộng ở trên, hẹp ở giữa, và rộng ở dưới. Hình dạng này thực sự phản ánh triết lý trung tâm của kiến trúc. Nghĩa là, IP đóng vai trò là điểm hội tụ của kiến trúc—nó định nghĩa một phương pháp chung để trao đổi các gói tin giữa một tập hợp lớn các mạng. Phía trên IP có thể có vô số giao thức giao vận, mỗi giao thức cung cấp một trừu tượng kênh khác nhau cho các chương trình ứng dụng. Do đó, vấn đề truyền thông điệp từ host này tới host khác hoàn toàn tách biệt với vấn đề cung cấp dịch vụ giao tiếp process-to-process hữu ích. Bên dưới IP, kiến trúc cho phép vô số công nghệ mạng khác nhau, từ Ethernet đến không dây đến các liên kết điểm-điểm đơn lẻ.

Một thuộc tính cuối cùng của kiến trúc Internet (hoặc chính xác hơn, của văn hóa IETF) là để một giao thức mới được chính thức đưa vào kiến trúc, phải có cả đặc tả giao thức và ít nhất một (tốt nhất là hai) triển khai đại diện của đặc tả đó. Sự tồn tại của các triển khai hoạt động là điều kiện cần để tiêu chuẩn được IETF thông qua. Giả định văn hóa này của cộng đồng thiết kế giúp đảm bảo rằng các giao thức của kiến trúc có thể được triển khai hiệu quả. Có lẽ giá trị mà văn hóa Internet đặt vào phần mềm hoạt động được thể hiện rõ nhất qua câu nói trên áo phông thường thấy tại các cuộc họp IETF:

   *Chúng tôi từ chối vua chúa, tổng thống và bỏ phiếu. Chúng tôi tin vào đồng thuận thô và mã nguồn chạy được.* **(David Clark)**

.. _key-hourglass:
.. admonition:: Ý chính

   Trong ba đặc điểm của kiến trúc Internet, triết lý thiết kế đồng hồ cát quan trọng đến mức cần nhắc lại. Phần eo hẹp của đồng hồ cát đại diện cho một tập hợp tối thiểu và được lựa chọn cẩn thận các khả năng toàn cục cho phép cả các ứng dụng tầng cao hơn và các công nghệ truyền thông tầng thấp hơn cùng tồn tại, chia sẻ khả năng và phát triển nhanh chóng. Mô hình eo hẹp này là then chốt cho khả năng thích nghi của Internet với các nhu cầu người dùng mới và công nghệ thay đổi. :ref:`[Tiếp theo] <key-pipe-full>`