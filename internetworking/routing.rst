3.4 Định tuyến (Routing)
========================

Cho đến nay trong chương này, chúng ta đã giả định rằng các switch và router có đủ thông tin về cấu trúc liên kết mạng để có thể chọn đúng cổng để gửi mỗi gói tin ra ngoài. Trong trường hợp mạch ảo, định tuyến chỉ là vấn đề đối với gói yêu cầu thiết lập kết nối; tất cả các gói tiếp theo sẽ đi theo cùng một đường với gói yêu cầu. Trong các mạng datagram, bao gồm cả mạng IP, định tuyến là vấn đề đối với mọi gói tin. Trong cả hai trường hợp, một switch hoặc router cần phải xem địa chỉ đích và xác định cổng ra nào là lựa chọn tốt nhất để chuyển gói tin đến địa chỉ đó. Như chúng ta đã thấy ở phần trước, switch thực hiện quyết định này bằng cách tham khảo bảng chuyển tiếp (forwarding table). Vấn đề cơ bản của định tuyến là làm thế nào các switch và router có được thông tin trong bảng chuyển tiếp của mình.

.. _key-forwarding:
.. admonition:: Ý chính

   Chúng tôi nhấn mạnh lại một sự phân biệt quan trọng, thường bị bỏ qua, giữa *chuyển tiếp* (forwarding) và *định tuyến* (routing). Chuyển tiếp là quá trình nhận một gói tin, tra cứu địa chỉ đích của nó trong bảng, và gửi gói tin theo hướng được xác định bởi bảng đó. Chúng ta đã thấy nhiều ví dụ về chuyển tiếp ở phần trước. Đây là một quá trình đơn giản, được xác định rõ ràng và thực hiện cục bộ tại mỗi nút, và thường được gọi là *mặt phẳng dữ liệu* (data plane) của mạng. Định tuyến là quá trình xây dựng các bảng chuyển tiếp. Nó phụ thuộc vào các thuật toán phân tán phức tạp, và thường được gọi là *mặt phẳng điều khiển* (control plane) của mạng.  :ref:`[Tiếp theo] <key-routing-alg>`

Mặc dù các thuật ngữ *bảng chuyển tiếp* (forwarding table) và *bảng định tuyến* (routing table) đôi khi được dùng thay thế cho nhau, ở đây chúng tôi sẽ phân biệt chúng. Bảng chuyển tiếp được sử dụng khi một gói tin đang được chuyển tiếp và do đó phải chứa đủ thông tin để thực hiện chức năng chuyển tiếp. Điều này có nghĩa là một dòng trong bảng chuyển tiếp chứa ánh xạ từ một tiền tố mạng đến một giao diện ra và một số thông tin MAC, như địa chỉ Ethernet của next hop. Ngược lại, bảng định tuyến là bảng được xây dựng bởi các thuật toán định tuyến như một bước chuẩn bị để xây dựng bảng chuyển tiếp. Nó thường chứa ánh xạ từ các tiền tố mạng đến các next hop. Nó cũng có thể chứa thông tin về cách thông tin này được học, để router có thể quyết định khi nào nên loại bỏ một số thông tin.

Việc bảng định tuyến và bảng chuyển tiếp thực sự là hai cấu trúc dữ liệu riêng biệt hay không là một lựa chọn hiện thực, nhưng có nhiều lý do để giữ chúng tách biệt. Ví dụ, bảng chuyển tiếp cần được cấu trúc để tối ưu hóa quá trình tra cứu địa chỉ khi chuyển tiếp gói tin, trong khi bảng định tuyến cần được tối ưu hóa cho mục đích tính toán các thay đổi trong cấu trúc liên kết. Trong nhiều trường hợp, bảng chuyển tiếp thậm chí có thể được hiện thực bằng phần cứng chuyên dụng, trong khi điều này hiếm khi, nếu có, được áp dụng cho bảng định tuyến.

:numref:`Bảng %s <tab-rtab>` đưa ra ví dụ về một dòng trong bảng định tuyến, cho biết rằng tiền tố mạng 18/8 sẽ được chuyển đến next hop router có địa chỉ IP 171.69.245.10

.. _tab-rtab:
.. table:: Ví dụ một dòng trong bảng định tuyến.
   :align: center
   :widths: auto

   +---------------+---------------+
   | Prefix/Length | Next Hop      |
   +===============+===============+
   | 18/8          | 171.69.245.10 |
   +---------------+---------------+

Ngược lại, :numref:`Bảng %s <tab-ftab>` đưa ra ví dụ về một dòng trong bảng chuyển tiếp, chứa thông tin về cách chính xác để chuyển tiếp một gói tin đến next hop đó: Gửi nó qua interface số 0 với địa chỉ MAC 8:0:2b:e4:b:1:2. Lưu ý rằng thông tin cuối cùng này được cung cấp bởi Giao thức phân giải địa chỉ (ARP).

.. _tab-ftab:
.. table:: Ví dụ một dòng trong bảng chuyển tiếp.
   :align: center
   :widths: auto

   +---------------+-----------+-----------------+
   | Prefix/Length | Interface | MAC Address     |
   +===============+===========+=================+
   | 18/8          | if0       | 8:0:2b:e4:b:1:2 |
   +---------------+-----------+-----------------+

Trước khi đi vào chi tiết về định tuyến, chúng ta cần tự nhắc lại câu hỏi then chốt nên đặt ra bất cứ khi nào cố gắng xây dựng một cơ chế cho Internet: “Giải pháp này có khả mở không?” Câu trả lời cho các thuật toán và giao thức được mô tả trong phần này là “không nhiều lắm.” Chúng được thiết kế cho các mạng có quy mô vừa phải—tối đa vài trăm nút, trên thực tế. Tuy nhiên, các giải pháp mà chúng tôi mô tả đóng vai trò là nền tảng cho hạ tầng định tuyến phân cấp được sử dụng trong Internet ngày nay. Cụ thể, các giao thức được mô tả trong phần này được gọi chung là các giao thức định tuyến *nội miền* (intradomain), hay *giao thức cổng nội bộ* (Interior Gateway Protocols - IGPs). Để hiểu các thuật ngữ này, chúng ta cần định nghĩa một *miền định tuyến* (routing domain). Một định nghĩa thực tế là một liên mạng trong đó tất cả các router đều dưới cùng một sự kiểm soát quản trị (ví dụ, một khuôn viên trường đại học, hoặc mạng của một nhà cung cấp dịch vụ Internet). Ý nghĩa của định nghĩa này sẽ rõ ràng hơn ở chương sau khi chúng ta xem xét các giao thức định tuyến *liên miền* (interdomain). Hiện tại, điều quan trọng cần nhớ là chúng ta đang xét vấn đề định tuyến trong bối cảnh các mạng nhỏ đến vừa, không phải một mạng có quy mô như Internet.

3.4.1 Mạng dưới dạng đồ thị
---------------------------

Về bản chất, định tuyến là một bài toán lý thuyết đồ thị. :numref:`Hình %s <fig-graph-route>` cho thấy một đồ thị biểu diễn một mạng. Các nút của đồ thị, được gán nhãn từ A đến F, có thể là host, switch, router hoặc mạng. Trong phần thảo luận ban đầu, chúng ta sẽ tập trung vào trường hợp các nút là router. Các cạnh của đồ thị tương ứng với các liên kết mạng. Mỗi cạnh có một *chi phí* (cost) liên kết, cho biết mức độ mong muốn khi gửi lưu lượng qua liên kết đó. Việc gán chi phí cho các cạnh sẽ được thảo luận ở phần sau.

Lưu ý rằng các mạng (đồ thị) ví dụ được sử dụng xuyên suốt chương này có các cạnh vô hướng được gán một chi phí duy nhất. Đây thực ra là một sự đơn giản hóa nhẹ. Chính xác hơn là nên coi các cạnh là có hướng, nghĩa là sẽ có một cặp cạnh giữa mỗi nút—một cạnh theo mỗi hướng, và mỗi cạnh có chi phí riêng.

.. _fig-graph-route:
.. figure:: figures/f03-28-9780123850591.png
   :width: 400px
   :align: center

   Mạng được biểu diễn dưới dạng đồ thị.

Vấn đề cơ bản của định tuyến là tìm đường đi có chi phí thấp nhất giữa bất kỳ hai nút nào, trong đó chi phí của một đường đi bằng tổng chi phí của tất cả các cạnh tạo nên đường đi đó. Đối với một mạng đơn giản như trong :numref:`Hình %s <fig-graph-route>`, bạn có thể tưởng tượng chỉ cần tính tất cả các đường đi ngắn nhất và nạp chúng vào bộ nhớ không bay hơi trên mỗi nút. Cách tiếp cận tĩnh như vậy có một số hạn chế:

-  Nó không xử lý được khi nút hoặc liên kết bị lỗi.

-  Nó không tính đến việc thêm các nút hoặc liên kết mới.

-  Nó ngụ ý rằng chi phí cạnh không thể thay đổi, mặc dù chúng ta có thể muốn chi phí liên kết thay đổi theo thời gian (ví dụ, gán chi phí cao cho liên kết đang bị tải nặng).

Vì những lý do này, định tuyến trong hầu hết các mạng thực tế được thực hiện bằng cách chạy các giao thức định tuyến giữa các nút. Các giao thức này cung cấp một cách phân tán, động để giải quyết bài toán tìm đường đi chi phí thấp nhất trong điều kiện có thể xảy ra lỗi liên kết, lỗi nút và thay đổi chi phí cạnh. Lưu ý từ *phân tán* trong câu trước; rất khó để làm cho các giải pháp tập trung có khả năng mở rộng, nên tất cả các giao thức định tuyến được sử dụng rộng rãi đều dùng thuật toán phân tán.

Tính chất phân tán của các thuật toán định tuyến là một trong những lý do chính khiến lĩnh vực này trở thành một chủ đề nghiên cứu và phát triển phong phú—có rất nhiều thách thức trong việc làm cho các thuật toán phân tán hoạt động tốt. Ví dụ, các thuật toán phân tán làm nảy sinh khả năng hai router tại một thời điểm có thể có ý tưởng khác nhau về đường đi ngắn nhất đến một đích nào đó. Thực tế, mỗi router có thể nghĩ rằng router kia gần đích hơn và quyết định gửi gói tin cho router kia. Rõ ràng, các gói tin như vậy sẽ bị kẹt trong một vòng lặp cho đến khi sự khác biệt giữa hai router được giải quyết, và tốt nhất là nên giải quyết càng sớm càng tốt. Đây chỉ là một ví dụ về loại vấn đề mà các giao thức định tuyến phải xử lý.

Để bắt đầu phân tích, chúng ta giả định rằng chi phí cạnh trong mạng đã biết. Chúng ta sẽ xem xét hai lớp giao thức định tuyến chính: *vector khoảng cách* (distance vector) và *trạng thái liên kết* (link state). Ở phần sau, chúng ta sẽ quay lại vấn đề tính toán chi phí cạnh một cách hợp lý.

3.4.2 Vector khoảng cách (RIP)
------------------------------

Ý tưởng đằng sau thuật toán vector khoảng cách được gợi ý ngay từ tên gọi. (Tên gọi phổ biến khác cho lớp thuật toán này là Bellman-Ford, theo tên các nhà phát minh.) Mỗi nút xây dựng một mảng một chiều (vector) chứa “khoảng cách” (chi phí) đến tất cả các nút khác và phân phối vector đó cho các láng giềng trực tiếp của mình. Giả định ban đầu cho định tuyến vector khoảng cách là mỗi nút biết chi phí của liên kết đến từng láng giềng trực tiếp. Các chi phí này có thể được cung cấp khi router được cấu hình bởi quản trị viên mạng. Một liên kết bị hỏng sẽ được gán chi phí vô cực.

.. _fig-dvroute:
.. figure:: figures/f03-29-9780123850591.png
   :width: 400px
   :align: center

   Định tuyến vector khoảng cách: một mạng ví dụ.

.. _tab-dvtab1:
.. table:: Khoảng cách ban đầu lưu tại mỗi nút (Toàn cục).
   :align: center
   :widths: auto

   +---+---+---+---+---+---+---+---+
   |   | A | B | C | D | E | F | G |
   +===+===+===+===+===+===+===+===+
   | A | 0 | 1 | 1 | ∞ | 1 | 1 | ∞ |
   +---+---+---+---+---+---+---+---+
   | B | 1 | 0 | 1 | ∞ | ∞ | ∞ | ∞ |
   +---+---+---+---+---+---+---+---+
   | C | 1 | 1 | 0 | 1 | ∞ | ∞ | ∞ |
   +---+---+---+---+---+---+---+---+
   | D | ∞ | ∞ | 1 | 0 | ∞ | ∞ | 1 |
   +---+---+---+---+---+---+---+---+
   | E | 1 | ∞ | ∞ | ∞ | 0 | ∞ | ∞ |
   +---+---+---+---+---+---+---+---+
   | F | 1 | ∞ | ∞ | ∞ | ∞ | 0 | 1 |
   +---+---+---+---+---+---+---+---+
   | G | ∞ | ∞ | ∞ | 1 | ∞ | 1 | 0 |
   +---+---+---+---+---+---+---+---+

Để thấy cách thuật toán định tuyến vector khoảng cách hoạt động, dễ nhất là xét một ví dụ như trong :numref:`Hình %s <fig-dvroute>`. Trong ví dụ này, chi phí của mỗi liên kết được đặt là 1, nên đường đi chi phí thấp nhất đơn giản là đường đi có ít bước nhảy nhất. (Vì tất cả các cạnh đều có cùng chi phí, chúng tôi không hiển thị chi phí trên đồ thị.) Chúng ta có thể biểu diễn kiến thức của mỗi nút về khoảng cách đến tất cả các nút khác dưới dạng bảng như :numref:`Bảng %s <tab-dvtab1>`. Lưu ý rằng mỗi nút chỉ biết thông tin trong một hàng của bảng (hàng mang tên nó ở cột bên trái). Cái nhìn toàn cục được trình bày ở đây không có tại bất kỳ điểm đơn lẻ nào trong mạng.

Chúng ta có thể coi mỗi hàng trong :numref:`Bảng %s <tab-dvtab1>` là danh sách khoảng cách từ một nút đến tất cả các nút khác, đại diện cho niềm tin hiện tại của nút đó. Ban đầu, mỗi nút đặt chi phí là 1 cho các láng giềng trực tiếp và vô cực cho tất cả các nút khác. Do đó, A ban đầu tin rằng nó có thể đến B trong một bước nhảy và D là không thể đến được. Bảng định tuyến lưu tại A phản ánh tập hợp niềm tin này và bao gồm tên của next hop mà A sẽ dùng để đến bất kỳ nút nào có thể đến được. Ban đầu, bảng định tuyến của A sẽ như :numref:`Bảng %s <tab-dvtab2>`.

.. _tab-dvtab2:
.. table::  Bảng định tuyến ban đầu tại nút A.
   :align: center
   :widths: auto

   +-------------+------+---------+
   | Destination | Cost | NextHop |
   +=============+======+=========+
   | B           | 1    | B       |
   +-------------+------+---------+
   | C           | 1    | C       |
   +-------------+------+---------+
   | D           | ∞    | —       |
   +-------------+------+---------+
   | E           | 1    | E       |
   +-------------+------+---------+
   | F           | 1    | F       |
   +-------------+------+---------+
   | G           | ∞    | —       |
   +-------------+------+---------+

Bước tiếp theo trong định tuyến vector khoảng cách là mỗi nút gửi một thông điệp đến các láng giềng trực tiếp, chứa danh sách khoảng cách cá nhân của nó. Ví dụ, nút F nói với nút A rằng nó có thể đến nút G với chi phí 1; A cũng biết nó có thể đến F với chi phí 1, nên nó cộng các chi phí này để được chi phí đến G qua F. Tổng chi phí này là 2, nhỏ hơn chi phí hiện tại là vô cực, nên A ghi nhận rằng nó có thể đến G với chi phí 2 qua F. Tương tự, A học từ C rằng D có thể đến từ C với chi phí 1; nó cộng với chi phí đến C (1) và quyết định rằng D có thể đến qua C với chi phí 2, tốt hơn chi phí cũ là vô cực. Đồng thời, A học từ C rằng B có thể đến từ C với chi phí 1, nên nó kết luận rằng chi phí đến B qua C là 2. Vì điều này tệ hơn chi phí hiện tại đến B (1), thông tin mới này bị bỏ qua. Lúc này, A có thể cập nhật bảng định tuyến với chi phí và next hop cho tất cả các nút trong mạng. Kết quả được thể hiện trong :numref:`Bảng %s <tab-dvtab3>`.

.. _tab-dvtab3:
.. table:: Bảng định tuyến cuối cùng tại nút A.
   :align: center
   :widths: auto

   +-------------+------+---------+
   | Destination | Cost | NextHop |
   +=============+======+=========+
   | B           | 1    | B       |
   +-------------+------+---------+
   | C           | 1    | C       |
   +-------------+------+---------+
   | D           | 2    | C       |
   +-------------+------+---------+
   | E           | 1    | E       |
   +-------------+------+---------+
   | F           | 1    | F       |
   +-------------+------+---------+
   | G           | 2    | F       |
   +-------------+------+---------+

Khi không có thay đổi về cấu trúc liên kết, chỉ cần một vài lần trao đổi thông tin giữa các láng giềng là mỗi nút đã có bảng định tuyến hoàn chỉnh. Quá trình đạt được thông tin định tuyến nhất quán cho tất cả các nút gọi là *hội tụ* (convergence). :numref:`Bảng %s <tab-dvtab4>` cho thấy tập hợp cuối cùng các chi phí từ mỗi nút đến tất cả các nút khác khi định tuyến đã hội tụ. Chúng tôi nhấn mạnh rằng không có nút nào trong mạng có tất cả thông tin trong bảng này—mỗi nút chỉ biết nội dung bảng định tuyến của riêng nó. Vẻ đẹp của một thuật toán phân tán như thế này là nó cho phép tất cả các nút đạt được cái nhìn nhất quán về mạng mà không cần một cơ quan trung tâm.

.. _tab-dvtab4:
.. table:: Khoảng cách cuối cùng lưu tại mỗi nút (Toàn cục).
   :align: center
   :widths: auto

   +---+---+---+---+---+---+---+---+
   |   | A | B | C | D | E | F | G |
   +===+===+===+===+===+===+===+===+
   | A | 0 | 1 | 1 | 2 | 1 | 1 | 2 |
   +---+---+---+---+---+---+---+---+
   | B | 1 | 0 | 1 | 2 | 2 | 2 | 3 |
   +---+---+---+---+---+---+---+---+
   | C | 1 | 1 | 0 | 1 | 2 | 2 | 2 |
   +---+---+---+---+---+---+---+---+
   | D | 2 | 2 | 1 | 0 | 3 | 2 | 1 |
   +---+---+---+---+---+---+---+---+
   | E | 1 | 2 | 2 | 3 | 0 | 2 | 3 |
   +---+---+---+---+---+---+---+---+
   | F | 1 | 2 | 2 | 2 | 2 | 0 | 1 |
   +---+---+---+---+---+---+---+---+
   | G | 2 | 3 | 2 | 1 | 3 | 1 | 0 |
   +---+---+---+---+---+---+---+---+

Có một vài chi tiết cần bổ sung trước khi kết thúc thảo luận về định tuyến vector khoảng cách. Đầu tiên, lưu ý rằng có hai trường hợp khác nhau khi một nút quyết định gửi bản cập nhật định tuyến cho các láng giềng. Một là cập nhật *định kỳ*. Trong trường hợp này, mỗi nút tự động gửi một bản cập nhật sau một khoảng thời gian nhất định, ngay cả khi không có gì thay đổi. Điều này giúp các nút khác biết rằng nút này vẫn đang hoạt động. Nó cũng đảm bảo rằng họ tiếp tục nhận được thông tin mà họ có thể cần nếu các tuyến hiện tại trở nên không khả thi. Tần suất cập nhật định kỳ này thay đổi tùy theo giao thức, nhưng thường là vài giây đến vài phút. Cơ chế thứ hai, đôi khi gọi là cập nhật *kích hoạt* (triggered), xảy ra bất cứ khi nào một nút phát hiện lỗi liên kết hoặc nhận được cập nhật từ một láng giềng khiến nó phải thay đổi một trong các tuyến trong bảng định tuyến. Bất cứ khi nào bảng định tuyến của một nút thay đổi, nó gửi cập nhật cho các láng giềng, điều này có thể dẫn đến thay đổi trong bảng của họ, khiến họ gửi cập nhật cho các láng giềng của mình.

Bây giờ hãy xem điều gì xảy ra khi một liên kết hoặc nút bị lỗi. Các nút phát hiện đầu tiên sẽ gửi danh sách khoảng cách mới cho các láng giềng, và thông thường hệ thống sẽ nhanh chóng ổn định về trạng thái mới. Về câu hỏi làm thế nào một nút phát hiện lỗi, có một vài cách khác nhau. Một cách là một nút liên tục kiểm tra liên kết đến nút khác bằng cách gửi một gói điều khiển và xem có nhận được xác nhận không. Một cách khác là một nút xác định rằng liên kết (hoặc nút ở đầu kia liên kết) đã chết nếu nó không nhận được bản cập nhật định tuyến định kỳ như mong đợi trong một vài chu kỳ cập nhật gần nhất.

Để hiểu điều gì xảy ra khi một nút phát hiện lỗi liên kết, hãy xét trường hợp F phát hiện liên kết đến G bị lỗi. Đầu tiên, F đặt khoảng cách mới đến G là vô cực và truyền thông tin đó cho A. Vì A biết rằng đường đi 2 bước đến G là qua F, A cũng sẽ đặt khoảng cách đến G là vô cực. Tuy nhiên, với bản cập nhật tiếp theo từ C, A sẽ biết rằng C có đường đi 2 bước đến G. Như vậy, A sẽ biết rằng nó có thể đến G trong 3 bước qua C, nhỏ hơn vô cực, nên A sẽ cập nhật bảng của mình. Khi nó quảng bá điều này cho F, F sẽ biết rằng nó có thể đến G với chi phí 4 qua A, nhỏ hơn vô cực, và hệ thống lại trở nên ổn định.

Đáng tiếc, những trường hợp hơi khác có thể khiến mạng không ổn định. Giả sử, ví dụ, liên kết từ A đến E bị hỏng. Ở vòng cập nhật tiếp theo, A quảng bá khoảng cách vô cực đến E, nhưng B và C quảng bá khoảng cách 2 đến E. Tùy vào thời điểm, có thể xảy ra như sau: B, khi nghe rằng E có thể đến trong 2 bước từ C, kết luận rằng nó có thể đến E trong 3 bước và quảng bá điều này cho A; A kết luận rằng nó có thể đến E trong 4 bước và quảng bá điều này cho C; C kết luận rằng nó có thể đến E trong 5 bước; và cứ thế. Chu trình này chỉ dừng lại khi khoảng cách đạt đến một giá trị đủ lớn để được coi là vô cực. Trong thời gian đó, không nút nào thực sự biết rằng E không thể đến được, và các bảng định tuyến của mạng không ổn định. Tình huống này gọi là vấn đề *đếm đến vô cực* (count to infinity).

Có một số giải pháp một phần cho vấn đề này. Đầu tiên là sử dụng một số nhỏ tương đối để xấp xỉ vô cực. Ví dụ, ta có thể quyết định rằng số bước nhảy tối đa để đi qua một mạng nhất định sẽ không bao giờ vượt quá 15, nên ta có thể chọn 16 làm giá trị đại diện cho vô cực. Điều này ít nhất giới hạn thời gian cần để đếm đến vô cực. Tất nhiên, nó cũng có thể gây vấn đề nếu mạng phát triển đến mức một số nút cách nhau hơn 15 bước.

Một kỹ thuật để cải thiện thời gian hội tụ định tuyến gọi là *split horizon*. Ý tưởng là khi một nút gửi bản cập nhật định tuyến cho các láng giềng, nó không gửi lại các tuyến mà nó học được từ mỗi láng giềng cho chính láng giềng đó. Ví dụ, nếu B có tuyến (E, 2, A) trong bảng, thì nó biết chắc đã học tuyến này từ A, nên bất cứ khi nào B gửi cập nhật định tuyến cho A, nó không bao gồm tuyến (E, 2) trong cập nhật đó. Trong một biến thể mạnh hơn gọi là *split horizon with poison reverse*, B thực sự gửi tuyến đó lại cho A, nhưng gán thông tin âm cho tuyến để đảm bảo rằng A sẽ không sử dụng B để đến E. Ví dụ, B gửi tuyến (E, ∞) cho A. Vấn đề với cả hai kỹ thuật này là chúng chỉ hiệu quả với các vòng lặp định tuyến liên quan đến hai nút. Với các vòng lặp lớn hơn, cần các biện pháp mạnh hơn. Tiếp tục ví dụ trên, nếu B và C chờ một thời gian sau khi nghe về lỗi liên kết từ A trước khi quảng bá các tuyến đến E, họ sẽ phát hiện ra rằng không ai thực sự có tuyến đến E. Đáng tiếc, cách tiếp cận này làm chậm quá trình hội tụ của giao thức; tốc độ hội tụ là một trong những ưu điểm chính của đối thủ, định tuyến trạng thái liên kết, sẽ được thảo luận ở phần sau.

Hiện thực hóa
~~~~~~~~~~~~~

Mã hiện thực thuật toán này rất đơn giản; chúng tôi chỉ trình bày một số phần cơ bản ở đây. Cấu trúc ``Route`` định nghĩa mỗi mục trong bảng định tuyến, và hằng số ``MAX_TTL`` xác định thời gian một mục được giữ trong bảng trước khi bị loại bỏ.

.. code-block:: c

   #define MAX_ROUTES      128     /* maximum size of routing table */
   #define MAX_TTL         120     /* time (in seconds) until route expires */

   typedef struct {
       NodeAddr  Destination;    /* address of destination */
       NodeAddr  NextHop;        /* address of next hop */
       int        Cost;          /* distance metric */
       u_short   TTL;            /* time to live */
   } Route;

   int      numRoutes = 0;
   Route    routingTable[MAX_ROUTES];

Hàm cập nhật bảng định tuyến của nút cục bộ dựa trên một tuyến mới được đưa ra bởi ``mergeRoute``. Mặc dù không được hiển thị, một hàm timer sẽ định kỳ quét danh sách các tuyến trong bảng định tuyến của nút, giảm trường ``TTL`` (thời gian sống) của mỗi tuyến, và loại bỏ bất kỳ tuyến nào có TTL bằng 0. Lưu ý rằng trường ``TTL`` được đặt lại thành ``MAX_TTL`` bất cứ khi nào tuyến được xác nhận lại bởi một thông điệp cập nhật từ một nút láng giềng.

.. code-block:: c

   void
   mergeRoute (Route *new)
   {
       int i;

       for (i = 0; i < numRoutes; ++i)
       {
           if (new->Destination == routingTable[i].Destination)
           {
               if (new->Cost + 1 < routingTable[i].Cost)
               {
                   /* found a better route: */
                   break;
               } else if (new->NextHop == routingTable[i].NextHop) {
                   /* metric for current next-hop may have changed: */
                   break;
               } else {
                   /* route is uninteresting---just ignore it */
                   return;
               }
           }
       }
       if (i == numRoutes)
       {
           /* this is a completely new route; is there room for it? */
           if (numRoutes < MAXROUTES)
           {
               ++numRoutes;
           } else {
               /* can`t fit this route in table so give up */
               return;
           }
       }
       routingTable[i] = *new;
       /* reset TTL */
       routingTable[i].TTL = MAX_TTL;
       /* account for hop to get to next node */
       ++routingTable[i].Cost;
   }

Cuối cùng, thủ tục ``updateRoutingTable`` là hàm chính gọi ``mergeRoute`` để tích hợp tất cả các tuyến chứa trong một bản cập nhật định tuyến nhận được từ một nút láng giềng.

.. code-block:: c

   void
   updateRoutingTable (Route *newRoute, int numNewRoutes)
   {
       int i;

       for (i=0; i < numNewRoutes; ++i)
       {
           mergeRoute(&newRoute[i]);
       }
   }

Giao thức Routing Information Protocol (RIP)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một trong những giao thức định tuyến được sử dụng rộng rãi nhất trong các mạng IP là Routing Information Protocol (RIP). Việc nó được sử dụng rộng rãi trong những ngày đầu của IP phần lớn là do nó được phân phối cùng với phiên bản Unix Berkeley Software Distribution (BSD) phổ biến, từ đó nhiều phiên bản thương mại của Unix được phát triển. Nó cũng cực kỳ đơn giản. RIP là ví dụ kinh điển của một giao thức định tuyến xây dựng trên thuật toán vector khoảng cách vừa mô tả.

Các giao thức định tuyến trong liên mạng khác biệt rất ít so với mô hình đồ thị lý tưởng hóa ở trên. Trong một liên mạng, mục tiêu của các router là học cách chuyển tiếp gói tin đến các *mạng* khác nhau. Do đó, thay vì quảng bá chi phí đến các router khác, các router quảng bá chi phí đến các mạng. Ví dụ, trong :numref:`Hình %s <fig-rip-eg>`, router C sẽ quảng bá cho router A rằng nó có thể đến các mạng 2 và 3 (mà nó kết nối trực tiếp) với chi phí 0, các mạng 5 và 6 với chi phí 1, và mạng 4 với chi phí 2.

.. _fig-rip-eg:
.. figure:: figures/f03-30-9780123850591.png
   :width: 300px
   :align: center

   Mạng ví dụ chạy RIP.

.. _fig-rip:
.. figure:: figures/f03-31-9780123850591.png
   :width: 300px
   :align: center

   Định dạng gói RIPv2.

Chúng ta có thể thấy điều này trong định dạng gói RIP (phiên bản 2) ở :numref:`Hình %s <fig-rip>`. Phần lớn của gói tin là các bộ ba ``(address, mask, distance)``. Tuy nhiên, nguyên lý của thuật toán định tuyến vẫn giống nhau. Ví dụ, nếu router A học được từ router B rằng mạng X có thể đến với chi phí thấp hơn qua B so với next hop hiện tại trong bảng định tuyến, A sẽ cập nhật chi phí và thông tin next hop cho số mạng đó tương ứng.

RIP thực chất là một hiện thực khá đơn giản của định tuyến vector khoảng cách. Các router chạy RIP gửi quảng bá của mình mỗi 30 giây; một router cũng gửi thông điệp cập nhật bất cứ khi nào một cập nhật từ router khác khiến nó phải thay đổi bảng định tuyến. Một điểm đáng chú ý là nó hỗ trợ nhiều họ địa chỉ, không chỉ IP—đó là lý do có trường ``Family`` trong các quảng bá. RIP phiên bản 2 (RIPv2) cũng giới thiệu các subnet mask như đã mô tả ở phần trước, trong khi RIP phiên bản 1 làm việc với địa chỉ IP phân lớp cũ.

Như sẽ thấy bên dưới, có thể sử dụng nhiều loại metric hoặc chi phí khác nhau cho các liên kết trong một giao thức định tuyến. RIP chọn cách đơn giản nhất, với tất cả chi phí liên kết đều bằng 1, giống như ví dụ ở trên. Do đó, nó luôn cố gắng tìm đường đi ít bước nhảy nhất. Các giá trị hợp lệ cho khoảng cách là từ 1 đến 15, với 16 đại diện cho vô cực. Điều này cũng giới hạn RIP chỉ chạy trên các mạng khá nhỏ—những mạng không có đường đi dài hơn 15 bước.

3.4.3 Trạng thái liên kết (OSPF)
--------------------------------

Định tuyến trạng thái liên kết là lớp giao thức định tuyến nội miền lớn thứ hai. Các giả định ban đầu cho định tuyến trạng thái liên kết khá giống với định tuyến vector khoảng cách. Mỗi nút được giả định có khả năng xác định trạng thái của liên kết đến các láng giềng (hoạt động hay không) và chi phí của mỗi liên kết. Một lần nữa, chúng ta muốn cung cấp cho mỗi nút đủ thông tin để nó có thể tìm đường đi chi phí thấp nhất đến bất kỳ đích nào. Ý tưởng cơ bản của các giao thức trạng thái liên kết rất đơn giản: Mỗi nút biết cách đến các láng giềng trực tiếp, và nếu chúng ta đảm bảo rằng tổng thể kiến thức này được phổ biến đến mọi nút, thì mỗi nút sẽ có đủ thông tin về mạng để xây dựng bản đồ hoàn chỉnh của mạng. Đây rõ ràng là điều kiện đủ (dù không phải điều kiện cần) để tìm đường đi ngắn nhất đến bất kỳ điểm nào trong mạng. Do đó, các giao thức định tuyến trạng thái liên kết dựa vào hai cơ chế: phổ biến tin cậy thông tin trạng thái liên kết, và tính toán các tuyến từ tổng hợp tất cả kiến thức trạng thái liên kết đã thu thập.

Phổ biến tin cậy (Reliable Flooding)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*Phổ biến tin cậy* là quá trình đảm bảo rằng tất cả các nút tham gia giao thức định tuyến đều nhận được một bản sao thông tin trạng thái liên kết từ tất cả các nút khác. Như tên gọi *flooding* gợi ý, ý tưởng cơ bản là một nút gửi thông tin trạng thái liên kết của mình ra tất cả các liên kết trực tiếp; mỗi nút nhận được thông tin này sau đó chuyển tiếp nó ra tất cả các liên kết của chính nó. Quá trình này tiếp tục cho đến khi thông tin đã đến tất cả các nút trong mạng.

Cụ thể hơn, mỗi nút tạo ra một gói cập nhật, còn gọi là *gói trạng thái liên kết* (link-state packet - LSP), chứa các thông tin sau:

-  ID của nút tạo ra LSP

-  Danh sách các láng giềng trực tiếp của nút đó, cùng với chi phí liên kết đến từng láng giềng

-  Số thứ tự (sequence number)

-  Thời gian sống (time to live) cho gói này

Hai mục đầu tiên cần thiết để tính toán tuyến; hai mục sau dùng để làm cho quá trình flooding đến tất cả các nút trở nên tin cậy. Đảm bảo tin cậy bao gồm việc đảm bảo bạn có bản sao mới nhất của thông tin, vì có thể có nhiều LSP mâu thuẫn từ một nút đi qua mạng. Làm cho flooding tin cậy đã chứng tỏ là khá khó khăn. (Ví dụ, một phiên bản sớm của định tuyến trạng thái liên kết dùng trong ARPANET đã khiến mạng này bị lỗi năm 1981.)

Flooding hoạt động như sau. Đầu tiên, việc truyền LSP giữa các router liền kề được đảm bảo tin cậy bằng cách sử dụng xác nhận và truyền lại giống như trong giao thức tầng liên kết tin cậy. Tuy nhiên, cần thêm một số bước nữa để flooding một LSP đến tất cả các nút trong mạng một cách tin cậy.

Xét một nút X nhận được một bản sao LSP bắt nguồn từ một nút khác Y. Lưu ý rằng Y có thể là bất kỳ router nào khác trong cùng miền định tuyến với X. X kiểm tra xem nó đã lưu một bản sao LSP từ Y chưa. Nếu chưa, nó lưu LSP. Nếu đã có, nó so sánh số thứ tự; nếu LSP mới có số thứ tự lớn hơn, nó được coi là mới hơn và LSP đó được lưu, thay thế bản cũ. Số thứ tự nhỏ hơn (hoặc bằng) ngụ ý LSP cũ hơn (hoặc không mới hơn) bản đã lưu, nên sẽ bị loại bỏ và không cần hành động gì thêm. Nếu LSP nhận được là bản mới hơn, X sẽ gửi một bản sao LSP đó cho tất cả các láng giềng ngoại trừ láng giềng vừa gửi LSP đến. Việc không gửi lại cho nút vừa gửi giúp kết thúc quá trình flooding một LSP. Vì X chuyển tiếp LSP cho tất cả các láng giềng, những nút này lại làm tương tự, bản sao mới nhất của LSP cuối cùng sẽ đến tất cả các nút.

.. _fig-flood:
.. figure:: figures/f03-32-9780123850591.png
   :width: 500px
   :align: center

   Flooding các gói trạng thái liên kết: (a) LSP đến nút X; (b) X flooding LSP đến A và C; (c) A và C flooding LSP đến B (nhưng không gửi lại X); (d) flooding hoàn tất.

:numref:`Hình %s <fig-flood>` cho thấy một LSP được flooding trong một mạng nhỏ. Mỗi nút được tô bóng khi nó lưu LSP mới. Ở :numref:`Hình %s(a) <fig-flood>`, LSP đến nút X, X gửi nó cho các láng giềng A và C ở :numref:`Hình %s(b) <fig-flood>`. A và C không gửi lại cho X, mà gửi tiếp cho B. Vì B nhận được hai bản sao giống hệt nhau của LSP, nó sẽ nhận bản đến trước và bỏ qua bản thứ hai như là bản trùng lặp. Sau đó, nó chuyển tiếp LSP cho D, nút không còn láng giềng nào để flooding, và quá trình hoàn tất.

Cũng giống như RIP, mỗi nút tạo ra LSP trong hai trường hợp. Hoặc là bộ đếm thời gian định kỳ hết hạn, hoặc có thay đổi về cấu trúc liên kết khiến nút phải tạo LSP mới. Tuy nhiên, lý do dựa trên cấu trúc liên kết duy nhất để một nút tạo LSP là nếu một trong các liên kết trực tiếp hoặc láng giềng trực tiếp của nó bị hỏng. Việc phát hiện lỗi liên kết đôi khi có thể được thực hiện bởi giao thức tầng liên kết. Việc mất láng giềng hoặc mất kết nối đến láng giềng có thể được phát hiện bằng các gói “hello” định kỳ. Mỗi nút gửi các gói này cho các láng giềng trực tiếp theo khoảng thời gian xác định. Nếu một khoảng thời gian đủ dài trôi qua mà không nhận được “hello” từ láng giềng, liên kết đến láng giềng đó sẽ bị coi là hỏng, và một LSP mới sẽ được tạo ra để phản ánh thực tế này.

Một trong những mục tiêu thiết kế quan trọng của cơ chế flooding trong giao thức trạng thái liên kết là thông tin mới nhất phải được flooding đến tất cả các nút càng nhanh càng tốt, trong khi thông tin cũ phải bị loại bỏ khỏi mạng và không được phép lưu hành. Ngoài ra, rõ ràng là nên giảm thiểu tổng lượng lưu lượng định tuyến được gửi quanh mạng; dù sao thì đây chỉ là overhead từ góc nhìn của người dùng thực sự sử dụng mạng cho ứng dụng của họ. Một số đoạn sau đây mô tả một số cách để đạt được các mục tiêu này.

Một cách đơn giản để giảm overhead là tránh tạo LSP trừ khi thực sự cần thiết. Điều này có thể thực hiện bằng cách dùng bộ đếm thời gian rất dài—thường là hàng giờ—cho việc tạo LSP định kỳ. Vì giao thức flooding thực sự tin cậy khi có thay đổi cấu trúc liên kết, có thể giả định rằng các thông điệp “không có gì thay đổi” không cần gửi thường xuyên.

Để đảm bảo thông tin cũ được thay thế bằng thông tin mới hơn, các LSP mang số thứ tự. Mỗi lần một nút tạo LSP mới, nó tăng số thứ tự lên 1. Không giống hầu hết các số thứ tự dùng trong giao thức, các số thứ tự này không được kỳ vọng sẽ quay vòng, nên trường này cần khá lớn (ví dụ, 64 bit). Nếu một nút bị tắt rồi bật lại, nó bắt đầu với số thứ tự 0. Nếu nút bị tắt lâu, tất cả các LSP cũ cho nút đó sẽ hết hạn (như mô tả bên dưới); nếu không, nút này cuối cùng sẽ nhận được bản sao LSP của chính nó với số thứ tự lớn hơn, nó có thể tăng lên và dùng làm số thứ tự của mình. Điều này đảm bảo LSP mới của nó thay thế bất kỳ LSP cũ nào còn sót lại từ trước khi nút bị tắt.

Các LSP cũng mang một trường thời gian sống (TTL). Trường này dùng để đảm bảo thông tin trạng thái liên kết cũ cuối cùng sẽ bị loại bỏ khỏi mạng. Một nút luôn giảm TTL của LSP mới nhận được trước khi flooding nó cho các láng giềng. Nó cũng “làm già” LSP khi lưu trữ trong nút. Khi TTL về 0, nút sẽ flooding lại LSP (với TTL bằng 0), điều này được tất cả các nút trong mạng hiểu là tín hiệu để xóa LSP đó.

Tính toán tuyến (Route Calculation)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Khi một nút đã có bản sao LSP từ tất cả các nút khác, nó có thể tính toán bản đồ hoàn chỉnh cho cấu trúc liên kết mạng, và từ bản đồ này nó có thể quyết định tuyến tốt nhất đến từng đích. Vậy, câu hỏi là chính xác làm thế nào nó tính toán các tuyến từ thông tin này. Giải pháp dựa trên một thuật toán nổi tiếng trong lý thuyết đồ thị—thuật toán đường đi ngắn nhất của Dijkstra.

Trước tiên, chúng ta định nghĩa thuật toán Dijkstra theo lý thuyết đồ thị. Hãy tưởng tượng một nút lấy tất cả các LSP mà nó nhận được và xây dựng một biểu diễn đồ thị của mạng, trong đó N là tập các nút trong đồ thị, l(i,j) là chi phí (trọng số) không âm gán cho cạnh giữa các nút i, j trong N và l(i, j) = ∞ nếu không có cạnh nối i và j. Trong mô tả sau, s thuộc N là nút đang thực hiện thuật toán để tìm đường đi ngắn nhất đến tất cả các nút khác trong N. Thuật toán duy trì hai biến: M là tập các nút đã được đưa vào bởi thuật toán, và C(n) là chi phí đường đi từ s đến mỗi nút n. Với các định nghĩa này, thuật toán được định nghĩa như sau:

::

   M = {s}
   for each n in N - {s}
       C(n) = l(s,n)
   while (N != M)
       M = M + {w} such that C(w) is the minimum for all w in (N-M)
       for each n in (N-M)
       C(n) = MIN(C(n), C(w)+l(w,n))

Về cơ bản, thuật toán hoạt động như sau. Ta bắt đầu với M chứa nút s này và khởi tạo bảng chi phí (mảng ``C(n)``) đến các nút khác bằng chi phí đã biết đến các nút kết nối trực tiếp. Sau đó, ta tìm nút có thể đến với chi phí thấp nhất (w) và thêm nó vào M. Cuối cùng, ta cập nhật bảng chi phí bằng cách xét chi phí đến các nút qua w. Ở dòng cuối của thuật toán, ta chọn tuyến mới đến nút n đi qua w nếu tổng chi phí từ nguồn đến w rồi theo liên kết từ w đến n nhỏ hơn tuyến cũ đến n. Quá trình này lặp lại cho đến khi tất cả các nút được đưa vào M.

Trong thực tế, mỗi switch tính bảng định tuyến của mình trực tiếp từ các LSP mà nó thu thập được bằng một hiện thực của thuật toán Dijkstra gọi là thuật toán *forward search*. Cụ thể, mỗi switch duy trì hai danh sách, gọi là ``Tentative`` và ``Confirmed``. Mỗi danh sách này chứa một tập các mục dạng ``(Destination, Cost, NextHop)``. Thuật toán hoạt động như sau:

1. Khởi tạo danh sách ``Confirmed`` với một mục cho chính mình; mục này có chi phí 0.

2. Với nút vừa được thêm vào danh sách ``Confirmed`` ở bước trước, gọi là ``Next`` và chọn LSP của nó.

3. Với mỗi láng giềng (``Neighbor``) của ``Next``, tính chi phí (``Cost``) để đến ``Neighbor`` bằng tổng chi phí từ chính mình đến ``Next`` và từ ``Next`` đến ``Neighbor``.

   1. Nếu ``Neighbor`` hiện không có trong ``Confirmed`` hoặc ``Tentative``, thêm ``(Neighbor, Cost, NextHop)`` vào ``Tentative``, trong đó ``NextHop`` là hướng đi để đến ``Next``.

   2. Nếu ``Neighbor`` hiện có trong ``Tentative``, và ``Cost`` nhỏ hơn chi phí hiện tại cho ``Neighbor``, thay thế mục hiện tại bằng ``(Neighbor, Cost, NextHop)``, trong đó ``NextHop`` là hướng đi để đến ``Next``.

4. Nếu danh sách ``Tentative`` rỗng, dừng lại. Nếu không, chọn mục có chi phí thấp nhất từ ``Tentative``, chuyển nó sang ``Confirmed``, và quay lại bước 2.

.. _fig-lsroute:
.. figure:: figures/f03-33-9780123850591.png
   :width: 350px
   :align: center

   Định tuyến trạng thái liên kết: một mạng ví dụ.

Điều này sẽ dễ hiểu hơn nhiều khi chúng ta xem một ví dụ. Xét mạng trong :numref:`Hình %s <fig-lsroute>`. Lưu ý rằng, không giống ví dụ trước, mạng này có nhiều chi phí cạnh khác nhau. :numref:`Bảng %s <tab-ls-trace>` theo dõi các bước xây dựng bảng định tuyến cho nút D. Chúng tôi ký hiệu hai cổng ra của D bằng tên các nút mà chúng kết nối, B và C. Lưu ý cách thuật toán dường như đi theo hướng sai (như đường đi chi phí 11 đến B là mục đầu tiên được thêm vào ``Tentative``), nhưng cuối cùng vẫn tìm được đường đi chi phí thấp nhất đến tất cả các nút.

.. _tab-ls-trace:
.. table:: Các bước xây dựng bảng định tuyến cho nút D.

  +---------+-------------------+-------------------+-------------------+
  | Step    | Confirmed         | Tentative         | Comments          |
  +=========+===================+===================+===================+
  | 1       | (D,0,–)           |                   | Vì D là thành     |
  |         |                   |                   | viên mới duy nhất |
  |         |                   |                   | của danh sách     |
  |         |                   |                   | Confirmed, xem    |
  |         |                   |                   | LSP của nó.       |
  +---------+-------------------+-------------------+-------------------+
  | 2       | (D,0,–)           | (B,11,B) (C,2,C)  | LSP của D cho     |
  |         |                   |                   | biết có thể đến B |
  |         |                   |                   | qua B với chi phí |
  |         |                   |                   | 11, tốt nhất nên  |
  |         |                   |                   | đưa vào           |
  |         |                   |                   | ``Tentative``;    |
  |         |                   |                   | tương tự cho C.   |
  +---------+-------------------+-------------------+-------------------+
  | 3       | (D,0,–) (C,2,C)   | (B,11,B)          | Đưa mục chi phí   |
  |         |                   |                   | thấp nhất trong   |
  |         |                   |                   | ``Tentative`` (C) |
  |         |                   |                   | vào ``Confirmed`` |
  |         |                   |                   | rồi xem LSP của   |
  |         |                   |                   | thành viên mới    |
  |         |                   |                   | (C).              |
  +---------+-------------------+-------------------+-------------------+
  | 4       | (D,0,–) (C,2,C)   | (B,5,C) (A,12,C)  | Đến B qua C chi   |
  |         |                   |                   | phí 5, thay thế   |
  |         |                   |                   | (B,11,B). LSP của |
  |         |                   |                   | C cho biết có thể |
  |         |                   |                   | đến A với chi phí |
  |         |                   |                   | 12.               |
  +---------+-------------------+-------------------+-------------------+
  | 5       | (D,0,–) (C,2,C)   | (A,12,C)          | Đưa mục chi phí   |
  |         | (B,5,C)           |                   | thấp nhất trong   |
  |         |                   |                   | ``Tentative`` (B) |
  |         |                   |                   | vào ``Confirmed``,|
  |         |                   |                   | rồi xem LSP của   |
  |         |                   |                   | nó.               |
  +---------+-------------------+-------------------+-------------------+
  | 6       | (D,0,–) (C,2,C)   | (A,10,C)          | Đến A qua B chi   |
  |         | (B,5,C)           |                   | phí 5, thay thế   |
  |         |                   |                   | mục ``Tentative`` |
  |         |                   |                   | hiện tại.         |
  +---------+-------------------+-------------------+-------------------+
  | 7       | (D,0,–) (C,2,C)   |                   | Đưa mục chi phí   |
  |         | (B,5,C) (A,10,C)  |                   | thấp nhất trong   |
  |         |                   |                   | ``Tentative`` (A) |
  |         |                   |                   | vào ``Confirmed``,|
  |         |                   |                   | và hoàn tất.      |
  +---------+-------------------+-------------------+-------------------+

Thuật toán định tuyến trạng thái liên kết có nhiều đặc tính tốt: Nó đã được chứng minh là hội tụ nhanh, không tạo ra nhiều lưu lượng, và phản ứng nhanh với thay đổi cấu trúc liên kết hoặc lỗi nút. Nhược điểm là lượng thông tin lưu tại mỗi nút (một LSP cho mỗi nút khác trong mạng) có thể khá lớn. Đây là một trong những vấn đề cơ bản của định tuyến và là một ví dụ cho vấn đề tổng quát hơn về khả mở. Một số giải pháp cho cả vấn đề cụ thể (lượng lưu trữ tiềm năng cần thiết tại mỗi nút) và vấn đề tổng quát (khả mở) sẽ được thảo luận ở phần sau.

.. _key-routing-alg:
.. admonition:: Ý chính

   Vector khoảng cách và trạng thái liên kết đều là các thuật toán định tuyến phân tán, nhưng chúng áp dụng các chiến lược khác nhau. Trong vector khoảng cách, mỗi nút chỉ nói chuyện với các láng giềng trực tiếp, nhưng nó nói cho họ biết tất cả những gì nó biết (tức là, khoảng cách đến tất cả các nút). Trong trạng thái liên kết, mỗi nút nói chuyện với tất cả các nút khác, nhưng chỉ nói cho họ biết những gì nó biết chắc chắn (tức là, chỉ trạng thái các liên kết trực tiếp). Trái ngược với cả hai thuật toán này, chúng ta sẽ xem xét một cách tiếp cận tập trung hơn cho định tuyến ở :ref:`Mục 3.5 <3.5 Implementation>` khi giới thiệu Software Defined Networking (SDN). :ref:`[Tiếp theo] <key-kiss>`

Giao thức Open Shortest Path First (OSPF)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một trong những giao thức định tuyến trạng thái liên kết được sử dụng rộng rãi nhất là OSPF. Từ “Open” nghĩa là nó là một chuẩn mở, không độc quyền, được tạo ra dưới sự bảo trợ của Internet Engineering Task Force (IETF). “SPF” là viết tắt của một tên gọi khác cho định tuyến trạng thái liên kết. OSPF bổ sung khá nhiều tính năng cho thuật toán trạng thái liên kết cơ bản vừa mô tả, bao gồm:

-  *Xác thực thông điệp định tuyến*—Một đặc điểm của các thuật toán định tuyến phân tán là chúng phân tán thông tin từ một nút đến nhiều nút khác, và toàn bộ mạng có thể bị ảnh hưởng bởi thông tin sai từ một nút. Vì lý do này, nên đảm bảo rằng tất cả các nút tham gia giao thức đều đáng tin cậy. Xác thực thông điệp định tuyến giúp đạt được điều này. Các phiên bản đầu của OSPF dùng mật khẩu 8 byte đơn giản để xác thực. Đây không phải là hình thức xác thực đủ mạnh để ngăn chặn người dùng độc hại chuyên nghiệp, nhưng giúp giảm một số vấn đề do cấu hình sai hoặc tấn công đơn giản. (Một hình thức xác thực tương tự cũng được thêm vào RIP ở phiên bản 2.) Sau này đã bổ sung xác thực mật mã mạnh hơn.

-  *Phân cấp bổ sung*—Phân cấp là một trong những công cụ cơ bản để làm cho hệ thống có khả năng mở rộng hơn. OSPF giới thiệu thêm một lớp phân cấp vào định tuyến bằng cách cho phép một miền được chia thành các *khu vực* (areas). Điều này có nghĩa là một router trong miền không nhất thiết phải biết cách đến mọi mạng trong miền đó—nó có thể chỉ cần biết cách đến đúng khu vực. Như vậy, giảm được lượng thông tin cần truyền và lưu trữ tại mỗi nút.

-  *Cân bằng tải*—OSPF cho phép nhiều tuyến đến cùng một nơi được gán cùng chi phí và sẽ phân phối lưu lượng đều qua các tuyến đó, giúp sử dụng tốt hơn năng lực mạng hiện có.

.. _fig-ospf:
.. figure:: figures/f03-34-9780123850591.png
   :width: 400px
   :align: center

   Định dạng header OSPF.

Có nhiều loại thông điệp OSPF khác nhau, nhưng tất cả đều bắt đầu với cùng một header, như trong :numref:`Hình %s <fig-ospf>`. Trường ``Version`` hiện được đặt là 2, và trường ``Type`` có thể nhận giá trị từ 1 đến 5. ``SourceAddr`` xác định người gửi thông điệp, và ``AreaId`` là định danh 32 bit của khu vực mà nút đó thuộc về. Toàn bộ gói tin, trừ dữ liệu xác thực, được bảo vệ bởi một checksum 16 bit dùng cùng thuật toán với header IP. ``Authentication type`` là 0 nếu không dùng xác thực; nếu không, có thể là 1 (dùng mật khẩu đơn giản) hoặc 2 (dùng checksum xác thực mật mã). Trong hai trường hợp sau, trường ``Authentication`` mang mật khẩu hoặc checksum mật mã.

Trong năm loại thông điệp OSPF, loại 1 là thông điệp “hello”, router gửi cho các peer để báo rằng nó vẫn còn sống và kết nối như mô tả ở trên. Các loại còn lại dùng để yêu cầu, gửi và xác nhận nhận được thông điệp trạng thái liên kết. Khối xây dựng cơ bản của thông điệp trạng thái liên kết trong OSPF là quảng bá trạng thái liên kết (LSA). Một thông điệp có thể chứa nhiều LSA. Chúng tôi cung cấp một số chi tiết về LSA ở đây.

Như bất kỳ giao thức định tuyến liên mạng nào, OSPF phải cung cấp thông tin về cách đến các mạng. Do đó, OSPF phải cung cấp nhiều thông tin hơn so với giao thức dựa trên đồ thị đơn giản ở trên. Cụ thể, một router chạy OSPF có thể tạo các gói trạng thái liên kết quảng bá một hoặc nhiều mạng mà nó kết nối trực tiếp. Ngoài ra, một router kết nối với router khác qua một liên kết phải quảng bá chi phí đến router đó qua liên kết. Hai loại quảng bá này là cần thiết để tất cả các router trong miền xác định được chi phí đến mọi mạng trong miền và next hop phù hợp cho từng mạng.

.. _fig-ospf-lsa:
.. figure:: figures/f03-35-9780123850591.png
   :width: 450px
   :align: center

   Quảng bá trạng thái liên kết OSPF.

:numref:`Hình %s <fig-ospf-lsa>` cho thấy định dạng gói cho quảng bá trạng thái liên kết loại 1. Loại 1 LSA quảng bá chi phí các liên kết giữa các router. Loại 2 LSA dùng để quảng bá các mạng mà router quảng bá kết nối, các loại khác dùng để hỗ trợ phân cấp bổ sung như mô tả ở phần sau. Nhiều trường trong LSA sẽ quen thuộc từ phần thảo luận trước. ``LS Age`` tương đương với thời gian sống, chỉ khác là nó tăng dần và LSA hết hạn khi tuổi đạt giá trị tối đa xác định. Trường ``Type`` cho biết đây là LSA loại 1.

Trong LSA loại 1, ``Link state ID`` và ``Advertising router`` là giống nhau. Mỗi trường mang một định danh 32 bit cho router tạo ra LSA này. Có thể dùng nhiều chiến lược để gán ID này, nhưng điều quan trọng là nó phải duy nhất trong miền định tuyến và một router phải nhất quán dùng cùng một router ID. Một cách để chọn router ID đáp ứng yêu cầu này là chọn địa chỉ IP thấp nhất trong tất cả các địa chỉ IP gán cho router đó. (Nhớ rằng một router có thể có địa chỉ IP khác nhau trên mỗi interface.)

``LS sequence number`` dùng đúng như mô tả ở trên để phát hiện LSA cũ hoặc trùng lặp. ``LS checksum`` tương tự như các checksum khác đã thấy ở các giao thức khác; nó dùng để kiểm tra dữ liệu không bị hỏng. Nó bao phủ tất cả các trường trong gói trừ ``LS Age``, nên không cần tính lại checksum mỗi khi ``LS Age`` tăng. ``Length`` là độ dài tính bằng byte của toàn bộ LSA.

Bây giờ chúng ta đến phần thông tin trạng thái liên kết thực tế. Điều này hơi phức tạp do có thông tin TOS (type of service). Bỏ qua phần đó, mỗi liên kết trong LSA được biểu diễn bởi một ``Link ID``, một số ``Link Data``, và một ``metric``. Hai trường đầu xác định liên kết; một cách phổ biến là dùng router ID của router ở đầu kia liên kết làm ``Link ID`` và dùng ``Link Data`` để phân biệt nếu có nhiều liên kết song song. ``Metric`` tất nhiên là chi phí của liên kết. ``Type`` cho biết loại liên kết—ví dụ, liên kết điểm-điểm.

Thông tin TOS có mặt để cho phép OSPF chọn các tuyến khác nhau cho các gói IP dựa trên giá trị trong trường TOS của chúng. Thay vì gán một metric duy nhất cho một liên kết, có thể gán các metric khác nhau tùy vào giá trị TOS của dữ liệu. Ví dụ, nếu có một liên kết trong mạng rất tốt cho lưu lượng nhạy trễ, ta có thể gán metric thấp cho giá trị TOS đại diện cho trễ thấp và metric cao cho các giá trị khác. OSPF sau đó sẽ chọn đường đi ngắn nhất khác cho các gói có trường TOS đặt giá trị đó. Đáng chú ý là, tại thời điểm viết sách, khả năng này chưa được triển khai rộng rãi.

3.4.4 Metric
------------

Phần thảo luận trước giả định rằng chi phí liên kết, hay metric, đã biết khi thực hiện thuật toán định tuyến. Phần này, chúng ta xem xét một số cách tính chi phí liên kết đã chứng tỏ hiệu quả trong thực tế. Một ví dụ đã thấy, khá hợp lý và rất đơn giản, là gán chi phí 1 cho tất cả các liên kết—khi đó đường đi chi phí thấp nhất sẽ là đường đi ít bước nhảy nhất. Tuy nhiên, cách tiếp cận này có một số nhược điểm. Đầu tiên, nó không phân biệt các liên kết dựa trên độ trễ. Do đó, một liên kết vệ tinh với độ trễ 250 ms trông cũng hấp dẫn như một liên kết mặt đất với độ trễ 1 ms. Thứ hai, nó không phân biệt các tuyến dựa trên băng thông, khiến một liên kết 1 Mbps trông cũng tốt như một liên kết 10 Gbps. Cuối cùng, nó không phân biệt các liên kết dựa trên tải hiện tại, khiến không thể định tuyến tránh các liên kết quá tải. Hóa ra, vấn đề cuối cùng này là khó nhất vì bạn đang cố gắng gói gọn các đặc tính phức tạp và động của một liên kết vào một giá trị scalar duy nhất.

ARPANET là nơi thử nghiệm cho nhiều cách tiếp cận khác nhau để tính toán chi phí liên kết. (Nó cũng là nơi chứng minh sự ổn định vượt trội của định tuyến trạng thái liên kết so với vector khoảng cách; cơ chế ban đầu dùng vector khoảng cách, sau này dùng trạng thái liên kết.) Phần thảo luận sau đây theo dõi sự phát triển của metric định tuyến ARPANET và qua đó khám phá các khía cạnh tinh tế của vấn đề.

Metric định tuyến ARPANET ban đầu đo số lượng gói tin đang xếp hàng chờ truyền trên mỗi liên kết, nghĩa là một liên kết có 10 gói chờ truyền sẽ được gán trọng số chi phí lớn hơn một liên kết có 5 gói chờ truyền. Tuy nhiên, dùng độ dài hàng đợi làm metric định tuyến không hiệu quả, vì độ dài hàng đợi là một thước đo tải nhân tạo—nó di chuyển các gói về phía hàng đợi ngắn nhất thay vì về phía đích, một tình huống quá quen thuộc với những ai từng nhảy hàng ở siêu thị. Nói chính xác hơn, cơ chế định tuyến ARPANET ban đầu gặp vấn đề là không tính đến băng thông hoặc độ trễ của liên kết.

Phiên bản thứ hai của thuật toán định tuyến ARPANET tính đến cả băng thông và độ trễ liên kết, dùng độ trễ thay vì chỉ độ dài hàng đợi làm thước đo tải. Cách làm như sau. Đầu tiên, mỗi gói đến được đóng dấu thời gian đến router (``ArrivalTime``); thời gian rời router (``DepartTime``) cũng được ghi lại. Thứ hai, khi nhận được ACK tầng liên kết từ phía bên kia, node tính độ trễ cho gói đó như sau

::

   Delay = (DepartTime - ArrivalTime) + TransmissionTime + Latency

trong đó ``TransmissionTime`` và ``Latency`` được xác định tĩnh cho liên kết và phản ánh băng thông và độ trễ liên kết. Lưu ý rằng ``DepartTime - ArrivalTime`` là thời gian gói bị trễ (xếp hàng) trong node do tải. Nếu không nhận được ACK mà gói bị timeout, thì ``DepartTime`` được đặt lại thành thời điểm gói được *truyền lại*. Khi đó, ``DepartTime - ArrivalTime`` phản ánh độ tin cậy của liên kết—càng phải truyền lại nhiều, liên kết càng kém tin cậy và càng nên tránh. Cuối cùng, trọng số gán cho mỗi liên kết được lấy từ độ trễ trung bình của các gói vừa gửi qua liên kết đó.

Dù cải tiến hơn cơ chế ban đầu, cách tiếp cận này cũng có nhiều vấn đề. Khi tải nhẹ, nó hoạt động khá tốt vì hai yếu tố tĩnh của độ trễ chiếm ưu thế. Khi tải nặng, một liên kết bị nghẽn sẽ bắt đầu quảng bá chi phí rất cao. Điều này khiến tất cả lưu lượng chuyển khỏi liên kết đó, để nó nhàn rỗi, rồi lại quảng bá chi phí thấp, thu hút lại toàn bộ lưu lượng, và cứ thế. Hệ quả của sự bất ổn này là, khi tải nặng, nhiều liên kết thực tế sẽ dành nhiều thời gian ở trạng thái nhàn rỗi, điều hoàn toàn không mong muốn khi tải nặng.

Một vấn đề khác là dải giá trị metric quá lớn. Ví dụ, một liên kết 9.6 kbps tải nặng có thể trông đắt gấp 127 lần một liên kết 56 kbps tải nhẹ. (Lưu ý, đây là ARPANET khoảng năm 1975.) Điều này nghĩa là thuật toán định tuyến sẽ chọn đường đi 126 bước qua các liên kết 56 kbps tải nhẹ thay vì một bước qua liên kết 9.6 kbps. Dù chuyển bớt lưu lượng khỏi đường quá tải là ý hay, làm cho nó trở nên quá không hấp dẫn đến mức mất hết lưu lượng là quá mức. Dùng 126 bước khi chỉ cần 1 bước là lãng phí tài nguyên mạng. Ngoài ra, các liên kết vệ tinh bị phạt quá mức, nên một liên kết vệ tinh 56 kbps nhàn rỗi trông đắt hơn nhiều so với một liên kết mặt đất 9.6 kbps nhàn rỗi, dù liên kết vệ tinh sẽ cho hiệu năng tốt hơn cho ứng dụng băng thông cao.

Cách tiếp cận thứ ba giải quyết các vấn đề này. Thay đổi lớn nhất là nén dải động của metric đáng kể, tính đến loại liên kết, và làm mượt biến động metric theo thời gian.

Việc làm mượt được thực hiện bằng nhiều cơ chế. Đầu tiên, đo độ trễ được chuyển thành mức sử dụng liên kết, và số này được lấy trung bình với mức sử dụng báo cáo lần trước để làm giảm thay đổi đột ngột. Thứ hai, có một giới hạn cứng về mức metric có thể thay đổi từ một chu kỳ đo đến chu kỳ tiếp theo. Bằng cách làm mượt thay đổi chi phí, khả năng tất cả các nút cùng bỏ một tuyến cùng lúc giảm đi nhiều.

Việc nén dải động được thực hiện bằng cách đưa mức sử dụng đo được, loại liên kết và tốc độ liên kết vào một hàm được minh họa trong :numref:`Hình %s <fig-metric>`. Lưu ý:

.. _fig-metric:
.. figure:: figures/f03-36-9780123850591.png
   :width: 600px
   :align: center

   Metric định tuyến ARPANET sửa đổi so với mức sử dụng liên kết.

-  Một liên kết tải nặng không bao giờ có chi phí lớn hơn ba lần chi phí khi nhàn rỗi.

-  Liên kết đắt nhất chỉ đắt gấp bảy lần liên kết rẻ nhất.

-  Một liên kết vệ tinh tốc độ cao hấp dẫn hơn liên kết mặt đất tốc độ thấp.

-  Chi phí là hàm của mức sử dụng liên kết chỉ ở mức tải vừa đến cao.

Tất cả các yếu tố này nghĩa là một liên kết ít có khả năng bị bỏ hoàn toàn, vì tăng chi phí gấp ba lần chỉ khiến nó kém hấp dẫn cho một số đường đi, trong khi vẫn là lựa chọn tốt nhất cho các đường khác. Độ dốc, điểm ngắt và điểm bù cho các đường cong trong :numref:`Hình %s <fig-metric>` được xác định qua rất nhiều thử nghiệm, và được tinh chỉnh cẩn thận để cho hiệu năng tốt.

Dù có tất cả các cải tiến này, thực tế là trong phần lớn các triển khai mạng thực tế, metric thay đổi rất hiếm khi, nếu có, và chỉ dưới sự kiểm soát của quản trị viên mạng, không tự động như mô tả ở trên. Lý do một phần là vì quan điểm phổ biến hiện nay cho rằng metric thay đổi động là quá bất ổn, dù thực ra không nhất thiết phải vậy. Có lẽ quan trọng hơn, nhiều mạng ngày nay không còn sự chênh lệch lớn về tốc độ và độ trễ liên kết như từng có ở ARPANET. Do đó, metric tĩnh là chuẩn. Một cách phổ biến để đặt metric là dùng một hằng số nhân với (1/băng_thông_liên_kết).

.. _key-kiss:
.. admonition:: Ý chính

   Tại sao chúng ta vẫn kể câu chuyện về một thuật toán hàng chục năm tuổi không còn dùng nữa? Vì nó minh họa hoàn hảo hai bài học giá trị. Thứ nhất là các hệ thống máy tính thường *được thiết kế lặp lại dựa trên kinh nghiệm.* Chúng ta hiếm khi làm đúng ngay lần đầu, nên quan trọng là triển khai một giải pháp đơn giản sớm hơn là cứ mãi ở giai đoạn thiết kế, và mong đợi sẽ cải tiến dần theo thời gian. Thứ hai là nguyên tắc KISS nổi tiếng: *Keep it Simple, Stupid* (Hãy giữ mọi thứ đơn giản). Khi xây dựng một hệ thống phức tạp, đơn giản thường là tốt hơn. Cơ hội để phát minh các tối ưu hóa phức tạp là rất nhiều, và rất hấp dẫn để theo đuổi. Dù đôi khi các tối ưu hóa như vậy có giá trị ngắn hạn, thật ngạc nhiên là một cách tiếp cận đơn giản lại thường chứng tỏ là tốt nhất về lâu dài. Bởi vì khi một hệ thống có nhiều thành phần chuyển động, như Internet chắc chắn là như vậy, giữ cho mỗi phần càng đơn giản càng tốt thường là cách tốt nhất.  :ref:`[Tiếp theo] <key-control-data>`
