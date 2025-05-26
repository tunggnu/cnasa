3.1 Cơ bản về chuyển mạch
=========================

Nói một cách đơn giản nhất, một switch là một cơ chế cho phép chúng ta liên kết các liên kết để tạo thành một mạng lớn hơn. Một switch là một thiết bị nhiều đầu vào, nhiều đầu ra, chuyển các gói tin từ một đầu vào đến một hoặc nhiều đầu ra. Do đó, switch bổ sung cấu trúc hình sao (xem :numref:`Hình %s <fig-star>`) vào tập các cấu trúc mạng khả dĩ. Mạng hình sao có một số đặc tính hấp dẫn:

-  Mặc dù một switch có số lượng đầu vào và đầu ra cố định, điều này giới hạn số lượng máy chủ có thể kết nối với một switch đơn, nhưng có thể xây dựng các mạng lớn bằng cách liên kết nhiều switch với nhau.

-  Chúng ta có thể kết nối các switch với nhau và với các máy chủ bằng các liên kết điểm-điểm, điều này thường có nghĩa là chúng ta có thể xây dựng các mạng có phạm vi địa lý rộng lớn.

-  Thêm một máy chủ mới vào mạng bằng cách kết nối nó với một switch không nhất thiết làm giảm hiệu năng của mạng đối với các máy chủ khác đã kết nối.

.. _fig-star:
.. figure:: figures/f03-01-9780123850591.png
   :width: 400px
   :align: center

   Một switch cung cấp cấu trúc hình sao.

Khẳng định cuối cùng này không thể áp dụng cho các mạng dùng chung môi trường truyền dẫn đã được thảo luận ở chương trước. Ví dụ, không thể để hai máy chủ trên cùng một đoạn Ethernet 10-Mbps truyền liên tục ở 10 Mbps vì họ chia sẻ cùng một môi trường truyền dẫn. Mỗi máy chủ trên mạng chuyển mạch đều có liên kết riêng tới switch, nên hoàn toàn có thể nhiều máy chủ truyền ở tốc độ liên kết tối đa (băng thông), miễn là switch được thiết kế với đủ tổng công suất. Cung cấp tổng thông lượng cao là một trong những mục tiêu thiết kế của switch; chúng ta sẽ quay lại chủ đề này sau. Nói chung, các mạng chuyển mạch được coi là *khả mở* hơn (tức là có khả năng mở rộng tới số lượng nút lớn hơn) so với các mạng dùng chung môi trường truyền dẫn nhờ khả năng hỗ trợ nhiều máy chủ ở tốc độ tối đa.

.. sidebar:: Ghép kênh phân chia bước sóng mật độ cao (DWDM)

   Việc chúng ta tập trung vào các mạng chuyển mạch gói làm lu mờ thực tế rằng, đặc biệt trong các mạng diện rộng, lớp truyền vật lý bên dưới là hoàn toàn quang học: không có gói tin nào cả. Ở lớp này, thiết bị DWDM (*Dense Wavelength Division Multiplexing*) thương mại có thể truyền một số lượng lớn bước sóng quang học (màu sắc) trên một sợi quang đơn. Ví dụ, có thể truyền dữ liệu trên 100 hoặc nhiều hơn các bước sóng khác nhau, và mỗi bước sóng có thể mang tới 100 Gbps dữ liệu.

   Kết nối các sợi quang này là một thiết bị quang học gọi là ROADM (*Reconfigurable Optical Add/Drop Multiplexers*). Một tập hợp các ROADM (nút) và sợi quang (liên kết) tạo thành một mạng truyền tải quang, nơi mỗi ROADM có thể chuyển tiếp từng bước sóng dọc theo một đường đi nhiều bước, tạo thành một mạch logic đầu-cuối. Từ góc nhìn của mạng chuyển mạch gói được xây dựng trên lớp truyền tải quang này, một bước sóng, dù đi qua nhiều ROADM, vẫn xuất hiện như một liên kết điểm-điểm giữa hai switch, trên đó có thể chạy SONET hoặc Ethernet 100-Gbps làm giao thức đóng khung. Tính năng cấu hình lại của ROADM cho phép thay đổi các bước sóng đầu-cuối này, thực chất tạo ra một cấu trúc liên kết mới ở tầng chuyển mạch gói.

Một switch được kết nối với một tập hợp các liên kết và, với mỗi liên kết này, chạy giao thức tầng liên kết dữ liệu phù hợp để giao tiếp với nút ở đầu kia của liên kết. Nhiệm vụ chính của switch là nhận các gói tin đến trên một trong các liên kết của nó và truyền chúng trên một liên kết khác. Chức năng này đôi khi được gọi là *chuyển mạch* hoặc *chuyển tiếp*, và theo kiến trúc OSI (Open Systems Interconnection), nó được coi là chức năng của tầng mạng. (Đây là một trường hợp mà phân tầng OSI không hoàn toàn phản ánh thực tế, như chúng ta sẽ thấy sau.)

Vậy câu hỏi đặt ra là: switch quyết định đặt mỗi gói tin lên liên kết đầu ra nào như thế nào? Câu trả lời chung là nó nhìn vào tiêu đề của gói tin để tìm một định danh mà nó dùng để ra quyết định. Chi tiết cách sử dụng định danh này có thể khác nhau, nhưng có hai cách tiếp cận phổ biến. Đầu tiên là cách tiếp cận *datagram* hoặc *không kết nối*. Thứ hai là cách tiếp cận *mạch ảo* hoặc *hướng kết nối*. Cách thứ ba, *định tuyến nguồn* (source routing), ít phổ biến hơn hai cách kia, nhưng cũng có một số ứng dụng hữu ích.

Một điểm chung cho mọi mạng là chúng ta cần có cách nhận diện các nút cuối. Các định danh này thường được gọi là *địa chỉ*. Chúng ta đã thấy các ví dụ về địa chỉ, như địa chỉ 48-bit dùng cho Ethernet. Yêu cầu duy nhất đối với địa chỉ Ethernet là không có hai nút nào trên mạng có cùng địa chỉ. Điều này được đảm bảo bằng cách chắc chắn rằng mọi card Ethernet đều được gán một định danh *toàn cục duy nhất*. Trong phần thảo luận tiếp theo, chúng ta giả định rằng mỗi máy chủ có một địa chỉ toàn cục duy nhất. Sau này, chúng ta sẽ xét các thuộc tính hữu ích khác mà một địa chỉ có thể có, nhưng tính duy nhất toàn cục là đủ để bắt đầu.

Một giả định khác cần đặt ra là phải có cách nhận diện các cổng vào và ra của mỗi switch. Có ít nhất hai cách hợp lý để nhận diện cổng: Một là đánh số từng cổng, hai là nhận diện cổng bằng tên của nút (switch hoặc máy chủ) mà nó kết nối tới. Hiện tại, chúng ta sẽ dùng cách đánh số cổng.

3.1.1 Datagram
--------------

Ý tưởng đằng sau datagram cực kỳ đơn giản: Chỉ cần đưa vào mỗi gói tin đủ thông tin để bất kỳ switch nào cũng có thể quyết định cách chuyển nó tới đích. Tức là, mỗi gói tin chứa đầy đủ địa chỉ đích. Xét ví dụ mạng minh họa trong :numref:`Hình %s <fig-dgram>`, trong đó các máy chủ có địa chỉ A, B, C, v.v. Để quyết định chuyển tiếp một gói tin, switch tra cứu *bảng chuyển tiếp* (đôi khi gọi là *bảng định tuyến*), ví dụ như minh họa trong :numref:`Bảng %s <tab-fwdtab>`. Bảng này cho thấy thông tin chuyển tiếp mà switch 2 cần để chuyển tiếp datagram trong mạng ví dụ. Việc xác định bảng như vậy khá dễ khi bạn có bản đồ đầy đủ của một mạng đơn giản như hình minh họa; ta có thể tưởng tượng một quản trị viên mạng cấu hình bảng này tĩnh. Việc tạo bảng chuyển tiếp trong các mạng lớn, phức tạp với cấu trúc động và nhiều đường đi giữa các đích thì khó hơn nhiều. Bài toán khó hơn này gọi là *định tuyến* (routing) và sẽ được bàn ở phần sau. Ta có thể coi định tuyến là một quá trình diễn ra ở hậu trường để, khi một gói dữ liệu xuất hiện, ta đã có thông tin đúng trong bảng chuyển tiếp để có thể chuyển tiếp, hoặc chuyển mạch, gói tin đó.

.. _fig-dgram:
.. figure:: figures/f03-02-9780123850591.png
   :width: 500px
   :align: center

   Chuyển tiếp datagram: một ví dụ mạng.

.. _tab-fwdtab:
.. table:: Bảng chuyển tiếp cho Switch 2.
   :align: center
   :widths: auto

   +-------------+------+
   | Đích        | Cổng |
   +=============+======+
   | A           | 3    |
   +-------------+------+
   | B           | 0    |
   +-------------+------+
   | C           | 3    |
   +-------------+------+
   | D           | 3    |
   +-------------+------+
   | E           | 2    |
   +-------------+------+
   | F           | 1    |
   +-------------+------+
   | G           | 0    |
   +-------------+------+
   | H           | 0    |
   +-------------+------+

Mạng datagram có các đặc điểm sau:

-  Một máy chủ có thể gửi gói tin đi bất cứ đâu vào bất kỳ lúc nào, vì bất kỳ gói tin nào đến switch đều có thể được chuyển tiếp ngay lập tức (giả sử bảng chuyển tiếp đã được điền đúng). Vì lý do này, mạng datagram thường được gọi là *không kết nối*; điều này trái ngược với các mạng *hướng kết nối* mô tả bên dưới, trong đó cần thiết lập một số *trạng thái kết nối* trước khi gửi gói dữ liệu đầu tiên.

-  Khi một máy chủ gửi gói tin, nó không biết liệu mạng có thể chuyển được gói đó hay không, hoặc thậm chí máy chủ đích có đang hoạt động hay không.

-  Mỗi gói tin được chuyển tiếp độc lập với các gói trước đó có thể đã được gửi tới cùng đích. Do đó, hai gói liên tiếp từ máy chủ A tới máy chủ B có thể đi theo các đường hoàn toàn khác nhau (có thể do thay đổi bảng chuyển tiếp ở một switch nào đó trong mạng).

-  Một switch hoặc liên kết bị lỗi có thể không gây ảnh hưởng nghiêm trọng tới liên lạc nếu có thể tìm một đường thay thế vòng qua chỗ lỗi và cập nhật bảng chuyển tiếp tương ứng.

Điểm cuối cùng này đặc biệt quan trọng trong lịch sử mạng datagram. Một trong những mục tiêu thiết kế quan trọng của Internet là khả năng chịu lỗi, và lịch sử đã chứng minh nó khá hiệu quả trong việc đạt mục tiêu này. Vì mạng dựa trên datagram là công nghệ chủ đạo được thảo luận trong sách này, chúng ta sẽ hoãn các ví dụ minh họa cho các phần tiếp theo, và chuyển sang hai lựa chọn chính còn lại.

3.1.2 Chuyển mạch mạch ảo (Virtual Circuit Switching)
-----------------------------------------------------

Một kỹ thuật thứ hai cho chuyển mạch gói sử dụng khái niệm *mạch ảo* (VC). Cách tiếp cận này, còn gọi là *mô hình hướng kết nối*, yêu cầu thiết lập một kết nối ảo từ máy chủ nguồn tới máy chủ đích trước khi gửi bất kỳ dữ liệu nào. Để hiểu cách hoạt động, xét :numref:`Hình %s <fig-vcircuit>`, nơi máy chủ A muốn gửi gói tin tới máy chủ B. Ta có thể coi đây là quá trình hai giai đoạn. Giai đoạn đầu là “thiết lập kết nối”. Giai đoạn hai là truyền dữ liệu. Ta sẽ xét từng giai đoạn.

.. _fig-vcircuit:
.. figure:: figures/f03-03-9780123850591.png
   :width: 500px
   :align: center

   Ví dụ về mạng mạch ảo.

Trong giai đoạn thiết lập kết nối, cần thiết lập “trạng thái kết nối” ở mỗi switch giữa máy chủ nguồn và đích. Trạng thái kết nối cho một kết nối gồm một mục trong “bảng VC” ở mỗi switch mà kết nối đi qua. Một mục trong bảng VC trên một switch gồm:

-  Một *định danh mạch ảo* (VCI) xác định duy nhất kết nối tại switch này và sẽ được mang trong tiêu đề của các gói thuộc kết nối này

-  Một giao diện vào mà các gói cho VC này đến switch

-  Một giao diện ra mà các gói cho VC này rời switch

-  Một VCI có thể khác sẽ được dùng cho các gói đi ra

Ý nghĩa của một mục như vậy là: Nếu một gói đến trên giao diện vào chỉ định và gói đó chứa giá trị VCI chỉ định trong tiêu đề, thì gói đó sẽ được gửi ra giao diện ra chỉ định với giá trị VCI ra chỉ định được đặt vào tiêu đề.

Lưu ý rằng sự kết hợp giữa VCI của các gói khi chúng được nhận tại switch *và* giao diện mà chúng được nhận xác định duy nhất kết nối ảo. Tất nhiên, có thể có nhiều kết nối ảo được thiết lập trong switch cùng lúc. Ngoài ra, ta thấy rằng giá trị VCI vào và ra thường không giống nhau. Do đó, VCI không phải là định danh toàn cục cho kết nối; nó chỉ có ý nghĩa trên một liên kết nhất định (tức là có *phạm vi cục bộ liên kết*).

Mỗi khi tạo một kết nối mới, cần gán một VCI mới cho kết nối đó trên mỗi liên kết mà kết nối sẽ đi qua. Cũng cần đảm bảo rằng VCI được chọn trên một liên kết nhất định chưa được dùng bởi kết nối nào khác trên liên kết đó.

Có hai cách tiếp cận chính để thiết lập trạng thái kết nối. Một là để quản trị viên mạng cấu hình trạng thái, khi đó mạch ảo là “vĩnh viễn”. Tất nhiên, nó cũng có thể bị xóa bởi quản trị viên, nên mạch ảo vĩnh viễn (PVC) có thể coi là một VC sống lâu hoặc được cấu hình thủ công. Ngoài ra, một máy chủ có thể gửi thông điệp vào mạng để thiết lập trạng thái. Cách này gọi là *báo hiệu* (signalling), và các mạch ảo kết quả gọi là *mạch ảo chuyển mạch* (SVC). Đặc điểm nổi bật của SVC là máy chủ có thể thiết lập và xóa VC động mà không cần quản trị viên mạng can thiệp. Lưu ý rằng SVC nên gọi chính xác là VC báo hiệu, vì chính việc báo hiệu (không phải chuyển mạch) phân biệt SVC với PVC.

Giả sử quản trị viên mạng muốn tạo thủ công một kết nối ảo mới từ máy chủ A tới máy chủ B. Đầu tiên, quản trị viên cần xác định một đường đi qua mạng từ A tới B. Trong mạng ví dụ ở :numref:`Hình %s <fig-vcircuit>`, chỉ có một đường như vậy, nhưng nói chung có thể không phải vậy. Quản trị viên sau đó chọn một giá trị VCI chưa dùng trên mỗi liên kết cho kết nối. Trong ví dụ, giả sử chọn giá trị VCI 5 cho liên kết từ A tới switch 1, và 11 cho liên kết từ switch 1 tới switch 2. Khi đó, switch 1 cần có một mục trong bảng VC được cấu hình như trong :numref:`Bảng %s <tab-vctab>`.

.. _tab-vctab:
.. table:: Ví dụ mục bảng mạch ảo cho Switch 1.
   :align: center
   :widths: auto

   +--------------------+--------------+--------------------+--------------+
   | Giao diện vào      | VCI vào      | Giao diện ra       | VCI ra       |
   +====================+==============+====================+==============+
   | 2                  | 5            | 1                  | 11           |
   +--------------------+--------------+--------------------+--------------+

Tương tự, giả sử chọn VCI 7 để nhận diện kết nối này trên liên kết từ switch 2 tới switch 3 và VCI 4 cho liên kết từ switch 3 tới B. Khi đó, switch 2 và 3 cần được cấu hình với các mục bảng VC như trong :numref:`Bảng %s <tab-vctab2>` và :numref:`Bảng %s <tab-vctab3>`. Lưu ý rằng giá trị VCI “ra” ở một switch là giá trị VCI “vào” ở switch tiếp theo.

.. _tab-vctab2:
.. table:: Mục bảng mạch ảo tại Switch 2.
   :align: center
   :widths: auto

   +--------------------+--------------+--------------------+--------------+
   | Giao diện vào      | VCI vào      | Giao diện ra       | VCI ra       |
   +====================+==============+====================+==============+
   | 3                  | 11           | 2                  | 7            |
   +--------------------+--------------+--------------------+--------------+

.. _tab-vctab3:
.. table:: Mục bảng mạch ảo tại Switch 3.
   :align: center
   :widths: auto

   +--------------------+--------------+--------------------+--------------+
   | Giao diện vào      | VCI vào      | Giao diện ra       | VCI ra       |
   +====================+==============+====================+==============+
   | 0                  | 7            | 1                  | 4            |
   +--------------------+--------------+--------------------+--------------+

.. _fig-vcdat:
.. figure:: figures/f03-04-9780123850591.png
   :width: 500px
   :align: center

   Một gói tin được gửi vào mạng mạch ảo.

Khi các bảng VC đã được thiết lập, giai đoạn truyền dữ liệu có thể tiến hành, như minh họa trong :numref:`Hình %s <fig-vcdat>`. Với bất kỳ gói tin nào muốn gửi tới B, A đặt giá trị VCI 5 vào tiêu đề gói và gửi tới switch 1. Switch 1 nhận bất kỳ gói nào như vậy trên giao diện 2, và dùng kết hợp giao diện và VCI trong tiêu đề gói để tìm mục bảng VC phù hợp. Như trong :numref:`Bảng %s <tab-vctab>`, mục bảng này bảo switch 1 chuyển tiếp gói ra giao diện 1 và đặt giá trị VCI 11 vào tiêu đề khi gửi. Như vậy, gói sẽ đến switch 2 trên giao diện 3 mang VCI 11. Switch 2 tra cứu giao diện 3 và VCI 11 trong bảng VC (như trong :numref:`Bảng %s <tab-vctab2>`) và gửi gói tới switch 3 sau khi cập nhật giá trị VCI trong tiêu đề gói phù hợp, như minh họa trong :numref:`Hình %s <fig-vcdat2>`. Quá trình này tiếp tục cho đến khi đến B với VCI 4 trong gói. Với B, điều này nhận diện gói là đến từ A.

Trong các mạng thực tế có quy mô vừa phải trở lên, việc cấu hình đúng bảng VC ở nhiều switch sẽ nhanh chóng trở nên quá tải nếu làm thủ công như trên. Do đó, gần như luôn dùng công cụ quản lý mạng hoặc một dạng báo hiệu (hoặc cả hai), ngay cả khi thiết lập VC “vĩnh viễn”. Với PVC, báo hiệu được khởi tạo bởi quản trị viên mạng, còn SVC thường được thiết lập bằng báo hiệu từ một trong các máy chủ. Giờ ta xét cách cùng một VC vừa mô tả có thể được thiết lập bằng báo hiệu từ máy chủ.

.. _fig-vcdat2:
.. figure:: figures/f03-05-9780123850591.png
   :width: 500px
   :align: center

   Một gói tin đi qua mạng mạch ảo.

Để bắt đầu quá trình báo hiệu, A gửi một thông điệp thiết lập vào mạng—tức là tới switch 1. Thông điệp thiết lập này chứa, ngoài các thông tin khác, địa chỉ đích đầy đủ của B. Thông điệp thiết lập cần đi tới tận B để tạo trạng thái kết nối cần thiết ở mọi switch trên đường đi. Ta thấy rằng việc đưa thông điệp thiết lập tới B khá giống việc gửi một datagram tới B, ở chỗ các switch phải biết gửi thông điệp thiết lập ra cổng nào để cuối cùng tới B. Tạm thời, giả sử các switch biết đủ về cấu trúc mạng để làm điều đó, nên thông điệp thiết lập sẽ đi qua switch 2 và 3 trước khi tới B.

Khi switch 1 nhận yêu cầu kết nối, ngoài việc gửi tiếp tới switch 2, nó tạo một mục mới trong bảng mạch ảo cho kết nối mới này. Mục này giống hệt như đã mô tả ở :numref:`Bảng %s <tab-vctab>`. Khác biệt chính là giờ đây việc gán giá trị VCI chưa dùng trên giao diện được thực hiện bởi switch cho cổng đó. Trong ví dụ này, switch chọn giá trị 5. Bảng mạch ảo giờ có thông tin: “Khi gói đến cổng 2 với định danh 5, gửi ra cổng 1.” Một vấn đề khác là, bằng cách nào đó, A sẽ cần biết rằng nó nên đặt giá trị VCI 5 vào các gói gửi tới B; ta sẽ thấy cách điều này xảy ra bên dưới.

Khi switch 2 nhận thông điệp thiết lập, nó thực hiện quá trình tương tự; trong ví dụ này, nó chọn giá trị 11 làm VCI vào. Tương tự, switch 3 chọn 7 làm giá trị VCI vào. Mỗi switch có thể chọn bất kỳ số nào nó muốn, miễn là số đó chưa được dùng cho kết nối nào khác trên cổng đó của switch. Như đã nói, VCI có phạm vi cục bộ liên kết; tức là không có ý nghĩa toàn cục.

Cuối cùng, thông điệp thiết lập đến B. Giả sử B hoạt động tốt và sẵn sàng nhận kết nối từ A, nó cũng gán một giá trị VCI vào, ở đây là 4. Giá trị VCI này có thể được B dùng để nhận diện mọi gói đến từ A.

Giờ, để hoàn tất kết nối, mọi người cần được thông báo về giá trị VCI mà hàng xóm phía dưới dùng cho kết nối này. B gửi một thông điệp xác nhận thiết lập kết nối tới switch 3 và kèm theo giá trị VCI mà nó chọn (4). Giờ switch 3 có thể hoàn tất mục bảng mạch ảo cho kết nối này, vì nó biết giá trị ra phải là 4. Switch 3 gửi xác nhận tới switch 2, chỉ định VCI 7. Switch 2 gửi tiếp tới switch 1, chỉ định VCI 11. Cuối cùng, switch 1 chuyển xác nhận tới A, báo cho nó dùng VCI 5 cho kết nối này.

Tại thời điểm này, mọi thành phần đều biết đủ để cho phép lưu lượng chảy từ A tới B. Mỗi switch có mục bảng mạch ảo hoàn chỉnh cho kết nối. Hơn nữa, A đã nhận xác nhận chắc chắn rằng mọi thứ đã sẵn sàng tới tận B. Lúc này, các mục bảng kết nối đã có ở cả ba switch giống như ví dụ cấu hình thủ công ở trên, nhưng toàn bộ quá trình diễn ra tự động nhờ thông điệp báo hiệu từ A. Giai đoạn truyền dữ liệu giờ có thể bắt đầu và giống hệt như trường hợp PVC.

Khi A không còn muốn gửi dữ liệu tới B, nó xóa kết nối bằng cách gửi thông điệp teardown tới switch 1. Switch xóa mục liên quan khỏi bảng và chuyển tiếp thông điệp tới các switch khác trên đường đi, các switch này cũng xóa mục bảng phù hợp. Lúc này, nếu A gửi một gói với VCI 5 tới switch 1, nó sẽ bị loại bỏ như thể kết nối chưa từng tồn tại.

Có một số điểm cần lưu ý về chuyển mạch mạch ảo:

-  Vì A phải chờ yêu cầu kết nối tới đầu xa của mạng và quay lại trước khi có thể gửi gói dữ liệu đầu tiên, nên có ít nhất một vòng trễ (RTT) trước khi dữ liệu được gửi.

-  Trong khi yêu cầu kết nối chứa địa chỉ đầy đủ của B (có thể khá lớn, là định danh toàn cục trên mạng), mỗi gói dữ liệu chỉ chứa một định danh nhỏ, chỉ duy nhất trên một liên kết. Do đó, chi phí trên mỗi gói do tiêu đề gây ra giảm so với mô hình datagram. Quan trọng hơn, việc tra cứu nhanh vì số mạch ảo có thể dùng làm chỉ số vào bảng thay vì là khóa phải tra cứu.

-  Nếu một switch hoặc liên kết trong kết nối bị lỗi, kết nối sẽ bị đứt và cần thiết lập lại kết nối mới. Ngoài ra, kết nối cũ cần được xóa để giải phóng bộ nhớ bảng trong các switch.

-  Vấn đề switch quyết định gửi yêu cầu kết nối ra liên kết nào đã được lướt qua. Thực chất, đây cũng là bài toán xây dựng bảng chuyển tiếp cho datagram, đòi hỏi một dạng *thuật toán định tuyến*. Định tuyến sẽ được mô tả ở phần sau, và các thuật toán ở đó áp dụng cho cả định tuyến yêu cầu thiết lập lẫn datagram.

Một điểm hay của mạch ảo là khi máy chủ nhận được tín hiệu sẵn sàng gửi dữ liệu, nó biết khá nhiều về mạng—ví dụ, thực sự có đường tới máy nhận và máy nhận sẵn sàng nhận dữ liệu. Cũng có thể cấp phát tài nguyên cho mạch ảo khi thiết lập. Ví dụ, X.25 (một công nghệ mạng dựa trên mạch ảo đã lỗi thời) dùng chiến lược ba phần sau:

1. Cấp phát bộ đệm cho mỗi mạch ảo khi khởi tạo mạch.

2. Chạy giao thức cửa sổ trượt giữa từng cặp nút dọc theo mạch ảo, và giao thức này được bổ sung kiểm soát lưu lượng để ngăn nút gửi vượt quá bộ đệm cấp phát ở nút nhận.

3. Mạch bị từ chối bởi một nút nếu không đủ bộ đệm tại nút đó khi xử lý thông điệp yêu cầu kết nối.

Bằng cách làm ba điều này, mỗi nút được đảm bảo có đủ bộ đệm để xếp hàng các gói đến trên mạch đó. Chiến lược cơ bản này thường gọi là *kiểm soát lưu lượng từng bước* (hop-by-hop flow control).

So sánh, mạng datagram không có giai đoạn thiết lập kết nối, và mỗi switch xử lý từng gói độc lập, khiến việc cấp phát tài nguyên trong mạng datagram trở nên không rõ ràng. Thay vào đó, mỗi gói đến cạnh tranh với tất cả các gói khác cho không gian bộ đệm. Nếu không còn bộ đệm trống, gói đến sẽ bị loại bỏ. Tuy nhiên, ngay cả trong mạng datagram, một máy chủ nguồn thường gửi một chuỗi gói tới cùng một máy chủ đích. Mỗi switch có thể phân biệt các gói đang xếp hàng dựa trên cặp nguồn/đích, nhờ đó đảm bảo các gói thuộc mỗi cặp nguồn/đích nhận được phần công bằng bộ đệm của switch.

Trong mô hình mạch ảo, ta có thể tưởng tượng cung cấp cho mỗi mạch một *chất lượng dịch vụ* (QoS) khác nhau. Trong bối cảnh này, thuật ngữ *chất lượng dịch vụ* thường có nghĩa là mạng cung cấp cho người dùng một dạng đảm bảo liên quan đến hiệu năng, điều này ngụ ý các switch dành riêng tài nguyên cần thiết để đáp ứng đảm bảo đó. Ví dụ, các switch dọc theo một mạch ảo có thể cấp phát một phần băng thông của mỗi liên kết ra cho mạch đó. Một ví dụ khác, một chuỗi switch có thể đảm bảo các gói thuộc một mạch nhất định không bị trễ (xếp hàng) quá một khoảng thời gian nhất định.

Đã có nhiều ví dụ thành công về công nghệ mạch ảo qua các năm, nổi bật là X.25, Frame Relay, và Asynchronous Transfer Mode (ATM). Tuy nhiên, với thành công của mô hình không kết nối của Internet, không công nghệ nào trong số này còn phổ biến ngày nay. Một trong những ứng dụng phổ biến nhất của mạch ảo trong nhiều năm là xây dựng *mạng riêng ảo* (VPN), sẽ được bàn ở phần sau. Ngay cả ứng dụng đó hiện nay cũng chủ yếu được hỗ trợ bằng công nghệ dựa trên Internet.

Asynchronous Transfer Mode (ATM)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Asynchronous Transfer Mode (ATM) có lẽ là công nghệ mạng dựa trên mạch ảo nổi tiếng nhất, dù hiện nay đã qua thời kỳ đỉnh cao triển khai. ATM trở thành công nghệ quan trọng vào những năm 1980 và đầu 1990 vì nhiều lý do, trong đó không nhỏ là do ngành viễn thông ủng hộ, vốn lúc đó ít tham gia vào mạng máy tính (ngoài việc cung cấp liên kết cho người khác xây mạng). ATM cũng xuất hiện đúng thời điểm, là công nghệ chuyển mạch tốc độ cao khi các công nghệ dùng chung môi trường như Ethernet và token ring bắt đầu trở nên quá chậm với nhiều người dùng mạng máy tính. Ở một số khía cạnh, ATM là công nghệ cạnh tranh với chuyển mạch Ethernet, và nhiều người coi nó là đối thủ của IP.

.. _fig-atmcell:
.. figure:: figures/f03-06-9780123850591.png
   :width: 550px
   :align: center

   Định dạng cell ATM tại UNI.

Cách tiếp cận của ATM có một số đặc điểm thú vị, đáng để xem xét kỹ hơn. Hình định dạng gói ATM—thường gọi là *cell* ATM—trong :numref:`Hình %s <fig-atmcell>` sẽ minh họa các điểm chính. Ta sẽ bỏ qua các bit điều khiển luồng tổng quát (GFC), vốn ít được dùng, và bắt đầu với 24 bit được gắn nhãn VPI (virtual path identifier—8 bit) và VCI (virtual circuit identifier—16 bit). Nếu coi hai trường này là một trường 24 bit, chúng tương ứng với định danh mạch ảo đã giới thiệu ở trên. Lý do chia trường thành hai phần là để cho phép một mức phân cấp: Tất cả các mạch có cùng VPI có thể, trong một số trường hợp, được xử lý như một nhóm (một đường ảo) và có thể được chuyển mạch cùng nhau chỉ cần nhìn VPI, đơn giản hóa công việc của switch khi có thể bỏ qua các bit VCI và giảm đáng kể kích thước bảng VC.

Chuyển sang byte tiêu đề cuối cùng, ta thấy một kiểm tra chẵn lẻ chu kỳ 8 bit (CRC), gọi là *kiểm tra lỗi tiêu đề* (``HEC``). Nó dùng CRC-8 và cung cấp khả năng phát hiện lỗi và sửa lỗi một bit trên tiêu đề cell. Bảo vệ tiêu đề cell đặc biệt quan trọng vì lỗi ở ``VCI`` sẽ khiến cell bị chuyển nhầm.

Điểm đáng chú ý nhất về cell ATM, và lý do nó được gọi là cell chứ không phải packet, là nó chỉ có một kích thước: 53 byte. Tại sao lại như vậy? Một lý do lớn là để thuận tiện cho việc xây dựng switch phần cứng. Khi ATM được tạo ra vào giữa và cuối thập niên 1980, Ethernet 10-Mbps là công nghệ tiên tiến nhất về tốc độ. Để nhanh hơn nữa, hầu hết mọi người nghĩ đến phần cứng. Ngoài ra, trong thế giới điện thoại, người ta nghĩ lớn khi nói về switch—switch điện thoại thường phục vụ hàng chục nghìn khách hàng. Gói cố định độ dài hóa ra rất hữu ích nếu muốn xây dựng switch phần cứng nhanh, khả mở cao. Có hai lý do chính:

1. Dễ xây dựng phần cứng cho các tác vụ đơn giản, và việc xử lý gói đơn giản hơn khi đã biết trước độ dài từng gói.

2. Nếu tất cả các gói đều cùng độ dài, có thể có nhiều phần tử chuyển mạch cùng làm việc song song, mỗi phần tử mất cùng thời gian để xử lý.

Lý do thứ hai này, cho phép song song hóa, giúp tăng khả mở của thiết kế switch. Nói rằng chỉ có thể xây switch phần cứng song song nhanh bằng cell cố định là quá lời. Tuy nhiên, rõ ràng cell giúp đơn giản hóa việc xây phần cứng như vậy, và thời điểm chuẩn ATM được định nghĩa đã có nhiều kiến thức về cách xây switch cell phần cứng. Nguyên lý này vẫn còn được áp dụng trong nhiều switch và router ngày nay, ngay cả khi chúng xử lý gói độ dài thay đổi—chúng cắt các gói thành cell để chuyển từ cổng vào sang cổng ra, nhưng tất cả đều là xử lý nội bộ của switch.

Có một lý do khác ủng hộ cell ATM nhỏ, liên quan đến độ trễ đầu-cuối. ATM được thiết kế để truyền cả thoại (ứng dụng chủ đạo lúc đó) và dữ liệu. Vì thoại băng thông thấp nhưng yêu cầu trễ nghiêm ngặt, điều tối kỵ là một gói thoại nhỏ bị xếp hàng sau một gói dữ liệu lớn tại switch. Nếu buộc tất cả các gói đều nhỏ (tức là kích thước cell), thì các gói dữ liệu lớn vẫn có thể được hỗ trợ bằng cách ghép nhiều cell thành một gói, và ta được lợi là có thể xen kẽ chuyển tiếp cell thoại và cell dữ liệu tại mọi switch trên đường từ nguồn tới đích. Ý tưởng dùng cell nhỏ để cải thiện độ trễ đầu-cuối vẫn còn tồn tại trong các mạng truy cập di động ngày nay.

Sau khi quyết định dùng gói nhỏ, cố định độ dài, câu hỏi tiếp theo là nên cố định ở độ dài bao nhiêu? Nếu làm quá ngắn, lượng thông tin tiêu đề cần mang so với dữ liệu thực trong một cell sẽ lớn, nên tỷ lệ băng thông liên kết thực sự dùng để mang dữ liệu giảm. Nghiêm trọng hơn, nếu xây thiết bị xử lý cell ở tốc độ tối đa cell/giây, thì khi cell ngắn, tổng tốc độ dữ liệu giảm tỷ lệ thuận với kích thước cell. Ví dụ là card mạng ghép cell thành đơn vị lớn hơn trước khi chuyển lên máy chủ. Hiệu năng thiết bị như vậy phụ thuộc trực tiếp vào kích thước cell. Ngược lại, nếu cell quá lớn, sẽ lãng phí băng thông do phải đệm dữ liệu truyền để đủ một cell. Nếu kích thước payload cell là 48 byte mà muốn gửi 1 byte, phải gửi 47 byte đệm. Nếu việc này xảy ra nhiều, hiệu suất sử dụng liên kết sẽ rất thấp. Kết hợp tỷ lệ tiêu đề/dữ liệu cao và tần suất gửi cell chưa đầy đã dẫn tới một số lãng phí đáng kể trong mạng ATM mà một số người gọi là *thuế cell*.

Cuối cùng, 48 byte được chọn cho payload cell ATM như một sự thỏa hiệp. Có nhiều lý do ủng hộ cell lớn hơn hoặc nhỏ hơn, và 48 không làm ai thực sự hài lòng—một số là lũy thừa của hai chắc chắn sẽ tốt hơn cho máy tính xử lý.

3.1.3 Định tuyến nguồn (Source Routing)
---------------------------------------

Cách tiếp cận thứ ba cho chuyển mạch, không dùng mạch ảo cũng không dùng datagram thông thường, gọi là *định tuyến nguồn* (source routing). Tên gọi xuất phát từ việc mọi thông tin về cấu trúc mạng cần thiết để chuyển mạch một gói qua mạng đều được cung cấp bởi máy chủ nguồn.

Có nhiều cách triển khai định tuyến nguồn. Một cách là gán số cho mỗi đầu ra của mỗi switch và đặt số đó vào tiêu đề gói. Chức năng chuyển mạch khi đó rất đơn giản: Với mỗi gói đến một đầu vào, switch đọc số cổng trong tiêu đề và truyền gói ra đầu ra đó. Tuy nhiên, vì thường có nhiều switch trên đường từ máy gửi tới máy nhận, tiêu đề gói cần chứa đủ thông tin để mọi switch trên đường đi xác định đầu ra cần chuyển gói. Một cách là đặt một danh sách có thứ tự các cổng switch vào tiêu đề và xoay danh sách sao cho switch tiếp theo luôn ở đầu danh sách. :numref:`Hình %s <fig-source-route>` minh họa ý tưởng này.

.. _fig-source-route:
.. figure:: figures/f03-07-9780123850591.png
   :width: 500px
   :align: center

   Định tuyến nguồn trong mạng chuyển mạch (switch đọc số ngoài cùng bên phải).

Trong ví dụ này, gói cần đi qua ba switch để từ A tới B. Ở switch 1, nó cần ra cổng 1, ở switch tiếp theo ra cổng 0, và ở switch thứ ba ra cổng 3. Như vậy, tiêu đề ban đầu khi gói rời A chứa danh sách cổng (3, 0, 1), giả sử mỗi switch đọc phần tử ngoài cùng bên phải. Để switch tiếp theo nhận được thông tin phù hợp, mỗi switch xoay danh sách sau khi đọc mục của mình. Như vậy, tiêu đề gói khi rời switch 1 tới switch 2 là (1, 3, 0); switch 2 lại xoay và gửi gói với (0, 1, 3) trong tiêu đề. Dù không minh họa, switch 3 lại xoay nữa, khôi phục tiêu đề về như khi A gửi.

Có một số điểm cần lưu ý về cách tiếp cận này. Thứ nhất, nó giả định A biết đủ về cấu trúc mạng để tạo tiêu đề có đủ chỉ dẫn đúng cho mọi switch trên đường đi. Điều này phần nào tương tự bài toán xây dựng bảng chuyển tiếp trong mạng datagram hoặc xác định nơi gửi gói thiết lập trong mạng mạch ảo. Tuy nhiên, trên thực tế, thường là switch đầu vào mạng (thay vì máy chủ đầu cuối kết nối với switch đó) sẽ gắn định tuyến nguồn.

Thứ hai, ta không thể dự đoán tiêu đề cần lớn bao nhiêu, vì nó phải chứa một trường thông tin cho mỗi switch trên đường đi. Điều này ngụ ý tiêu đề có thể có độ dài thay đổi không giới hạn, trừ khi ta biết chắc chắn số switch tối đa mà một gói sẽ đi qua.

Thứ ba, có một số biến thể của cách tiếp cận này. Ví dụ, thay vì xoay tiêu đề, mỗi switch có thể chỉ cần loại bỏ phần tử đầu tiên khi dùng nó. Tuy nhiên, xoay có lợi thế so với loại bỏ: B nhận được bản đầy đủ của tiêu đề, có thể giúp nó xác định đường về A. Một biến thể khác là tiêu đề mang một con trỏ tới mục “cổng tiếp theo”, để mỗi switch chỉ cần cập nhật con trỏ thay vì xoay tiêu đề; cách này có thể hiệu quả hơn khi triển khai. Ba cách này được minh họa trong :numref:`Hình %s <fig-sroute-apps>`. Trong mỗi trường hợp, mục mà switch này cần đọc là ``A``, và mục mà switch tiếp theo cần đọc là ``B``.

.. _fig-sroute-apps:
.. figure:: figures/f03-08-9780123850591.png
   :width: 550px
   :align: center

   Ba cách xử lý tiêu đề cho định tuyến nguồn:
   (a) xoay; (b) loại bỏ; (c) con trỏ. Các nhãn đọc từ phải sang trái.

Định tuyến nguồn có thể dùng trong cả mạng datagram và mạng mạch ảo. Ví dụ, Giao thức Internet, vốn là giao thức datagram, có tùy chọn định tuyến nguồn cho phép một số gói được định tuyến nguồn, trong khi phần lớn vẫn chuyển mạch như datagram thông thường. Định tuyến nguồn cũng được dùng trong một số mạng mạch ảo như cách để gửi yêu cầu thiết lập ban đầu dọc đường từ nguồn tới đích.

Định tuyến nguồn đôi khi được phân loại là *nghiêm ngặt* hoặc *lỏng*. Trong định tuyến nguồn nghiêm ngặt, mọi nút trên đường đi phải được chỉ định, trong khi định tuyến nguồn lỏng chỉ chỉ định một tập nút cần đi qua, không nói rõ cách đi từ nút này sang nút kia. Định tuyến nguồn lỏng có thể coi là một tập các điểm trung gian thay vì một đường đi hoàn chỉnh. Tùy chọn lỏng này hữu ích để giảm lượng thông tin mà nguồn cần có để tạo định tuyến nguồn. Trong bất kỳ mạng đủ lớn nào, sẽ khó để một máy chủ có đủ thông tin về đường đi hoàn chỉnh tới mọi đích. Nhưng cả hai loại định tuyến nguồn đều có ứng dụng trong một số kịch bản, như sẽ thấy ở các chương sau.