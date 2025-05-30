6.4 Kiểm Soát Tắc Nghẽn Nâng Cao
===============================

Phần này đi sâu hơn vào kiểm soát tắc nghẽn. Khi làm như vậy, điều quan trọng là phải hiểu rằng chiến lược tiêu chuẩn của TCP là kiểm soát tắc nghẽn sau khi nó xảy ra, thay vì cố gắng tránh tắc nghẽn ngay từ đầu. Thực tế, TCP liên tục tăng tải mà nó áp đặt lên mạng nhằm tìm ra điểm mà tại đó tắc nghẽn xảy ra, rồi sau đó nó lùi lại khỏi điểm này. Nói cách khác, TCP *cần* tạo ra mất mát để tìm ra băng thông khả dụng của kết nối. Một giải pháp thay thế hấp dẫn là dự đoán khi nào tắc nghẽn sắp xảy ra và sau đó giảm tốc độ gửi dữ liệu của các máy chủ ngay trước khi các gói bắt đầu bị loại bỏ. Chúng tôi gọi chiến lược này là *tránh tắc nghẽn* để phân biệt với *kiểm soát tắc nghẽn*, nhưng có lẽ chính xác nhất là coi “tránh” như một tập con của “kiểm soát”.

Chúng tôi mô tả hai cách tiếp cận khác nhau để tránh tắc nghẽn. Cách tiếp cận đầu tiên bổ sung một lượng nhỏ chức năng vào bộ định tuyến để hỗ trợ nút đầu cuối trong việc dự đoán tắc nghẽn. Cách tiếp cận này thường được gọi là *Quản lý Hàng đợi Chủ động* (AQM). Cách tiếp cận thứ hai cố gắng tránh tắc nghẽn hoàn toàn từ các máy chủ đầu cuối. Cách tiếp cận này được triển khai trong TCP, khiến nó trở thành một biến thể của các cơ chế kiểm soát tắc nghẽn đã được mô tả ở phần trước.

6.4.1 Quản lý Hàng đợi Chủ động (DECbit, RED, ECN)
--------------------------------------------------

Cách tiếp cận đầu tiên yêu cầu thay đổi các bộ định tuyến, điều mà chưa bao giờ là cách ưu tiên của Internet khi giới thiệu các tính năng mới, nhưng dù vậy, vẫn là một nguồn gây tranh cãi liên tục trong 20 năm qua. Vấn đề là mặc dù nhìn chung mọi người đồng ý rằng các bộ định tuyến là vị trí lý tưởng để phát hiện sự khởi đầu của tắc nghẽn—tức là, khi hàng đợi của chúng bắt đầu đầy lên—nhưng vẫn chưa có sự đồng thuận về chính xác thuật toán nào là tốt nhất. Sau đây mô tả hai cơ chế kinh điển, và kết thúc bằng một thảo luận ngắn về tình hình hiện nay.

DECbit
~~~~~~

Cơ chế đầu tiên được phát triển để sử dụng trên Kiến trúc Mạng Kỹ thuật số (DNA), một mạng không kết nối với giao thức vận chuyển hướng kết nối. Do đó, cơ chế này cũng có thể được áp dụng cho TCP và IP. Như đã đề cập ở trên, ý tưởng ở đây là chia đều hơn trách nhiệm kiểm soát tắc nghẽn giữa các bộ định tuyến và các nút đầu cuối. Mỗi bộ định tuyến giám sát tải mà nó đang trải qua và thông báo rõ ràng cho các nút đầu cuối khi tắc nghẽn sắp xảy ra. Thông báo này được thực hiện bằng cách đặt một bit tắc nghẽn nhị phân trong các gói đi qua bộ định tuyến, do đó có tên là *DECbit*. Máy chủ đích sau đó sao chép bit tắc nghẽn này vào ACK mà nó gửi lại cho nguồn. Cuối cùng, nguồn điều chỉnh tốc độ gửi của mình để tránh tắc nghẽn. Phần thảo luận sau đây mô tả thuật toán chi tiết hơn, bắt đầu từ những gì xảy ra trong bộ định tuyến.

Một bit tắc nghẽn được thêm vào tiêu đề gói tin. Một bộ định tuyến đặt bit này trong một gói nếu độ dài hàng đợi trung bình của nó lớn hơn hoặc bằng 1 tại thời điểm gói đến. Độ dài hàng đợi trung bình này được đo trong một khoảng thời gian bao gồm chu kỳ bận+rảnh cuối cùng, cộng với chu kỳ bận hiện tại. (Bộ định tuyến *bận* khi nó đang truyền và *rảnh* khi nó không truyền.) :numref:`Hình %s <fig-decbit>` cho thấy độ dài hàng đợi tại một bộ định tuyến như một hàm của thời gian. Về cơ bản, bộ định tuyến tính diện tích dưới đường cong và chia giá trị này cho khoảng thời gian để tính độ dài hàng đợi trung bình. Sử dụng độ dài hàng đợi là 1 làm ngưỡng để đặt bit tắc nghẽn là một sự đánh đổi giữa việc xếp hàng đáng kể (và do đó thông lượng cao hơn) và tăng thời gian rảnh (và do đó độ trễ thấp hơn). Nói cách khác, độ dài hàng đợi là 1 dường như tối ưu hóa hàm công suất.

.. _fig-decbit:
.. figure:: figures/f06-14-9780123850591.png
   :width: 500px
   :align: center

   Tính toán độ dài hàng đợi trung bình tại một bộ định tuyến.

Bây giờ chuyển sang phần cơ chế ở phía máy chủ, nguồn ghi lại bao nhiêu gói của nó dẫn đến việc một bộ định tuyến nào đó đặt bit tắc nghẽn. Cụ thể, nguồn duy trì một cửa sổ tắc nghẽn, giống như trong TCP, và theo dõi xem tỷ lệ bao nhiêu gói trong cửa sổ cuối cùng có bit này được đặt. Nếu ít hơn 50% số gói có bit được đặt, thì nguồn tăng cửa sổ tắc nghẽn lên một gói. Nếu 50% hoặc nhiều hơn số gói trong cửa sổ cuối cùng có bit tắc nghẽn được đặt, thì nguồn giảm cửa sổ tắc nghẽn xuống 0,875 lần giá trị trước đó. Giá trị 50% được chọn làm ngưỡng dựa trên phân tích cho thấy nó tương ứng với đỉnh của đường cong công suất. Quy tắc “tăng 1, giảm 0,875” được chọn vì tăng cộng và giảm nhân giúp cơ chế ổn định.

Random Early Detection
~~~~~~~~~~~~~~~~~~~~~~

Cơ chế thứ hai, gọi là *phát hiện sớm ngẫu nhiên* (RED), tương tự như sơ đồ DECbit ở chỗ mỗi bộ định tuyến được lập trình để giám sát độ dài hàng đợi của chính nó và, khi phát hiện tắc nghẽn sắp xảy ra, sẽ thông báo cho nguồn điều chỉnh cửa sổ tắc nghẽn của mình. RED, do Sally Floyd và Van Jacobson phát minh vào đầu những năm 1990, khác với sơ đồ DECbit ở hai điểm chính.

Điểm đầu tiên là thay vì gửi một thông báo tắc nghẽn rõ ràng cho nguồn, RED thường được triển khai sao cho nó *ngầm* thông báo cho nguồn về tắc nghẽn bằng cách loại bỏ một trong các gói của nó. Do đó, nguồn được thông báo hiệu quả bằng timeout hoặc ACK trùng lặp tiếp theo. Nếu bạn chưa đoán ra, RED được thiết kế để sử dụng cùng với TCP, vốn hiện tại phát hiện tắc nghẽn bằng các timeout (hoặc một số phương pháp khác như ACK trùng lặp). Như phần “sớm” trong tên RED gợi ý, gateway loại bỏ gói sớm hơn mức cần thiết, để thông báo cho nguồn rằng nó nên giảm cửa sổ tắc nghẽn sớm hơn bình thường. Nói cách khác, bộ định tuyến loại bỏ một vài gói trước khi hết hoàn toàn bộ đệm, nhằm khiến nguồn giảm tốc độ, với hy vọng rằng điều này sẽ giúp không phải loại bỏ nhiều gói về sau.

Điểm khác biệt thứ hai giữa RED và DECbit là ở chi tiết cách RED quyết định khi nào loại bỏ một gói và gói nào sẽ bị loại bỏ. Để hiểu ý tưởng cơ bản, hãy xem xét một hàng đợi FIFO đơn giản. Thay vì chờ hàng đợi đầy hoàn toàn rồi buộc phải loại bỏ từng gói đến (chính sách loại bỏ ở đuôi của phần trước), chúng ta có thể quyết định loại bỏ mỗi gói đến với một *xác suất loại bỏ* nào đó bất cứ khi nào độ dài hàng đợi vượt quá một *mức loại bỏ*. Ý tưởng này gọi là *loại bỏ ngẫu nhiên sớm*. Thuật toán RED định nghĩa chi tiết cách giám sát độ dài hàng đợi và khi nào loại bỏ một gói.

Trong các đoạn sau, chúng tôi mô tả thuật toán RED như được Floyd và Jacobson đề xuất ban đầu. Chúng tôi lưu ý rằng đã có nhiều sửa đổi được đề xuất bởi cả các tác giả và các nhà nghiên cứu khác. Tuy nhiên, các ý tưởng chính vẫn giống như trình bày dưới đây, và hầu hết các triển khai hiện tại đều gần với thuật toán này.

Đầu tiên, RED tính toán độ dài hàng đợi trung bình bằng cách sử dụng trung bình động có trọng số tương tự như cách tính timeout TCP ban đầu. Tức là, ``AvgLen`` được tính như sau

::

   AvgLen = (1 - Weight) x AvgLen + Weight x SampleLen

trong đó 0 < ``Weight`` < 1 và ``SampleLen`` là độ dài hàng đợi khi thực hiện phép đo mẫu. Trong hầu hết các triển khai phần mềm, độ dài hàng đợi được đo mỗi khi một gói mới đến gateway. Trong phần cứng, nó có thể được tính tại một khoảng lấy mẫu cố định.

Lý do sử dụng độ dài hàng đợi trung bình thay vì tức thời là vì nó phản ánh chính xác hơn khái niệm tắc nghẽn. Do tính chất bùng nổ của lưu lượng Internet, hàng đợi có thể đầy rất nhanh rồi lại trống ngay sau đó. Nếu một hàng đợi dành phần lớn thời gian trống, thì có lẽ không nên kết luận rằng bộ định tuyến đang tắc nghẽn và yêu cầu các máy chủ giảm tốc độ. Do đó, phép tính trung bình động có trọng số cố gắng phát hiện tắc nghẽn kéo dài, như được chỉ ra ở phần bên phải của :numref:`Hình %s <fig-red-avg>`, bằng cách lọc bỏ các thay đổi ngắn hạn trong độ dài hàng đợi. Bạn có thể nghĩ về trung bình động như một bộ lọc thông thấp, trong đó ``Weight`` xác định hằng số thời gian của bộ lọc. Câu hỏi về cách chọn hằng số thời gian này sẽ được thảo luận bên dưới.

.. _fig-red-avg:
.. figure:: figures/f06-15-9780123850591.png
   :width: 500px
   :align: center

   Độ dài hàng đợi trung bình động có trọng số.

Thứ hai, RED có hai ngưỡng độ dài hàng đợi kích hoạt các hoạt động nhất định: ``MinThreshold`` và ``MaxThreshold``. Khi một gói đến gateway, RED so sánh ``AvgLen`` hiện tại với hai ngưỡng này, theo các quy tắc sau:

::

   if AvgLen <= MinThreshold
       queue the packet
   if MinThreshold < AvgLen < MaxThreshold
       calculate probability P
       drop the arriving packet with probability P
   if MaxThreshold <= AvgLen
       drop the arriving packet

Nếu độ dài hàng đợi trung bình nhỏ hơn ngưỡng dưới, không có hành động nào được thực hiện, và nếu độ dài hàng đợi trung bình lớn hơn ngưỡng trên, thì gói luôn bị loại bỏ. Nếu độ dài hàng đợi trung bình nằm giữa hai ngưỡng, thì gói mới đến sẽ bị loại bỏ với xác suất ``P`` nào đó. Tình huống này được minh họa trong :numref:`Hình %s <fig-red>`. Mối quan hệ xấp xỉ giữa ``P`` và ``AvgLen`` được thể hiện trong :numref:`Hình %s <fig-red-prob>`. Lưu ý rằng xác suất loại bỏ tăng dần khi ``AvgLen`` nằm giữa hai ngưỡng, đạt ``MaxP`` tại ngưỡng trên, tại đó nó nhảy lên 1. Lý do là, nếu ``AvgLen`` đạt ngưỡng trên, thì cách tiếp cận nhẹ nhàng (loại bỏ một vài gói) không hiệu quả và cần biện pháp mạnh: loại bỏ tất cả các gói đến. Một số nghiên cứu cho rằng một chuyển tiếp mượt mà hơn từ loại bỏ ngẫu nhiên sang loại bỏ hoàn toàn, thay vì cách tiếp cận gián đoạn như trên, có thể phù hợp hơn.

.. _fig-red:
.. figure:: figures/f06-16-9780123850591.png
   :width: 300px
   :align: center

   Các ngưỡng RED trên một hàng đợi FIFO.

.. _fig-red-prob:
.. figure:: figures/f06-17-9780123850591.png
   :width: 400px
   :align: center

   Hàm xác suất loại bỏ cho RED.

Mặc dù :numref:`Hình %s <fig-red-prob>` cho thấy xác suất loại bỏ là hàm chỉ của ``AvgLen``, nhưng thực tế phức tạp hơn một chút. Thực ra, ``P`` là hàm của cả ``AvgLen`` và thời gian kể từ lần loại bỏ gói trước đó. Cụ thể, nó được tính như sau:

::

   TempP = MaxP x (AvgLen - MinThreshold) / (MaxThreshold - MinThreshold)
   P = TempP/(1 - count x TempP)

``TempP`` là biến được vẽ trên trục y trong :numref:`Hình %s <fig-red-prob>`, ``count`` theo dõi số gói mới đến đã được xếp hàng (không bị loại bỏ), và ``AvgLen`` nằm giữa hai ngưỡng. ``P`` tăng dần khi ``count`` tăng, do đó làm cho việc loại bỏ trở nên ngày càng có khả năng xảy ra khi thời gian kể từ lần loại bỏ trước tăng lên. Điều này làm cho các lần loại bỏ gần nhau ít có khả năng xảy ra hơn so với các lần loại bỏ cách xa nhau. Bước bổ sung này trong tính toán ``P`` được các tác giả RED đưa vào khi họ nhận thấy rằng, nếu không có nó, các lần loại bỏ gói không được phân bố đều theo thời gian mà có xu hướng xảy ra thành cụm. Vì các gói từ một kết nối nhất định có khả năng đến theo từng đợt, việc loại bỏ thành cụm này có thể gây ra nhiều lần loại bỏ trong một kết nối. Điều này không mong muốn, vì chỉ cần một lần loại bỏ mỗi thời gian khứ hồi là đủ để khiến một kết nối giảm kích thước cửa sổ, trong khi nhiều lần loại bỏ có thể khiến nó quay lại chế độ khởi động chậm.

Ví dụ, giả sử chúng ta đặt ``MaxP`` là 0,02 và ``count`` được khởi tạo bằng 0. Nếu độ dài hàng đợi trung bình nằm giữa hai ngưỡng, thì ``TempP``, và giá trị ban đầu của ``P``, sẽ bằng một nửa ``MaxP``, tức là 0,01. Một gói đến, tất nhiên, có 99 trong 100 cơ hội được xếp vào hàng đợi tại thời điểm này. Với mỗi gói liên tiếp không bị loại bỏ, ``P`` tăng dần, và đến khi 50 gói đến mà không bị loại bỏ, ``P`` sẽ tăng gấp đôi lên 0,02. Trong trường hợp hiếm hoi 99 gói đến mà không bị mất, ``P`` đạt 1, đảm bảo rằng gói tiếp theo sẽ bị loại bỏ. Điều quan trọng của phần thuật toán này là nó đảm bảo phân bố các lần loại bỏ đều theo thời gian.

Ý định là, nếu RED loại bỏ một tỷ lệ nhỏ các gói khi ``AvgLen`` vượt quá ``MinThreshold``, điều này sẽ khiến một vài kết nối TCP giảm kích thước cửa sổ, từ đó giảm tốc độ các gói đến bộ định tuyến. Nếu mọi việc suôn sẻ, ``AvgLen`` sẽ giảm và tắc nghẽn được tránh. Độ dài hàng đợi có thể được giữ ngắn, trong khi thông lượng vẫn cao vì ít gói bị loại bỏ.

Lưu ý rằng, vì RED hoạt động trên độ dài hàng đợi trung bình theo thời gian, nên có thể độ dài hàng đợi tức thời dài hơn nhiều so với ``AvgLen``. Trong trường hợp này, nếu một gói đến mà không còn chỗ để đặt, thì nó sẽ bị loại bỏ. Khi điều này xảy ra, RED hoạt động ở chế độ loại bỏ ở đuôi. Một trong những mục tiêu của RED là ngăn chặn hành vi loại bỏ ở đuôi nếu có thể.

Tính chất ngẫu nhiên của RED mang lại một đặc điểm thú vị cho thuật toán. Vì RED loại bỏ các gói một cách ngẫu nhiên, xác suất RED quyết định loại bỏ gói của một luồng nào đó xấp xỉ tỷ lệ với phần băng thông mà luồng đó đang nhận tại bộ định tuyến đó. Điều này là vì một luồng gửi nhiều gói hơn sẽ cung cấp nhiều ứng viên hơn cho việc loại bỏ ngẫu nhiên. Do đó, có một mức độ phân bổ tài nguyên công bằng nhất định được tích hợp trong RED, mặc dù không chính xác tuyệt đối. Mặc dù có thể coi là công bằng, vì RED trừng phạt các luồng băng thông cao nhiều hơn các luồng băng thông thấp, nhưng nó cũng làm tăng xác suất TCP phải khởi động lại, điều này càng gây bất lợi cho các luồng băng thông cao.

.. _key-red:
.. admonition:: Ý chính

   Lưu ý rằng đã có khá nhiều phân tích về việc thiết lập các tham số RED—ví dụ, ``MaxThreshold``, ``MinThreshold``, ``MaxP`` và ``Weight``—tất cả nhằm tối ưu hóa hàm công suất (tỷ lệ thông lượng trên độ trễ). Hiệu suất của các tham số này cũng đã được xác nhận qua mô phỏng, và thuật toán đã được chứng minh là không quá nhạy cảm với chúng. Tuy nhiên, điều quan trọng là phải nhớ rằng tất cả các phân tích và mô phỏng này đều dựa trên một đặc trưng cụ thể của tải mạng. Đóng góp thực sự của RED là một cơ chế giúp bộ định tuyến quản lý độ dài hàng đợi chính xác hơn. Việc xác định chính xác độ dài hàng đợi tối ưu phụ thuộc vào hỗn hợp lưu lượng và vẫn là chủ đề nghiên cứu, với thông tin thực tế hiện đang được thu thập từ việc triển khai RED trong Internet. :ref:`[Tiếp theo] <key-layering>`

Xem xét việc thiết lập hai ngưỡng, ``MinThreshold`` và ``MaxThreshold``. Nếu lưu lượng khá bùng nổ, thì ``MinThreshold`` nên đủ lớn để duy trì mức sử dụng liên kết ở mức chấp nhận được. Ngoài ra, sự khác biệt giữa hai ngưỡng nên lớn hơn mức tăng điển hình của độ dài hàng đợi trung bình tính toán trong một RTT. Đặt ``MaxThreshold`` gấp đôi ``MinThreshold`` dường như là một quy tắc hợp lý với hỗn hợp lưu lượng trên Internet ngày nay. Ngoài ra, vì chúng ta kỳ vọng độ dài hàng đợi trung bình sẽ dao động giữa hai ngưỡng trong thời kỳ tải cao, nên cần có đủ không gian bộ đệm *trên* ``MaxThreshold`` để hấp thụ các đợt bùng nổ tự nhiên trong lưu lượng Internet mà không buộc bộ định tuyến phải chuyển sang chế độ loại bỏ ở đuôi.

Chúng tôi đã lưu ý ở trên rằng ``Weight`` xác định hằng số thời gian cho bộ lọc thông thấp trung bình động, và điều này cho chúng ta một gợi ý về cách chọn giá trị phù hợp cho nó. Hãy nhớ rằng RED cố gắng gửi tín hiệu đến các luồng TCP bằng cách loại bỏ các gói trong thời gian tắc nghẽn. Giả sử một bộ định tuyến loại bỏ một gói từ một kết nối TCP nào đó và sau đó ngay lập tức chuyển tiếp thêm một số gói từ cùng kết nối đó. Khi các gói đó đến bộ nhận, nó bắt đầu gửi các ACK trùng lặp cho bộ gửi. Khi bộ gửi thấy đủ số ACK trùng lặp, nó sẽ giảm kích thước cửa sổ. Vì vậy, từ lúc bộ định tuyến loại bỏ một gói cho đến khi cùng bộ định tuyến đó bắt đầu thấy sự giảm tải từ kết nối bị ảnh hưởng (về mặt giảm kích thước cửa sổ), ít nhất phải trôi qua một thời gian khứ hồi cho kết nối đó. Có lẽ không có nhiều ý nghĩa khi để bộ định tuyến phản ứng với tắc nghẽn ở các thang thời gian nhỏ hơn nhiều so với thời gian khứ hồi của các kết nối đi qua nó. Như đã đề cập trước đó, 100 ms là một ước lượng không tồi cho thời gian khứ hồi trung bình trên Internet. Do đó, ``Weight`` nên được chọn sao cho các thay đổi về độ dài hàng đợi trong các thang thời gian nhỏ hơn nhiều so với 100 ms sẽ bị lọc bỏ.

Vì RED hoạt động bằng cách gửi tín hiệu cho các luồng TCP để yêu cầu chúng giảm tốc độ, bạn có thể tự hỏi điều gì sẽ xảy ra nếu các tín hiệu đó bị bỏ qua. Điều này thường được gọi là vấn đề *luồng không phản hồi*. Các luồng không phản hồi sử dụng nhiều tài nguyên mạng hơn phần công bằng của chúng và có thể gây ra sụp đổ tắc nghẽn nếu có đủ nhiều luồng như vậy, giống như thời kỳ trước khi có kiểm soát tắc nghẽn TCP. Một số kỹ thuật được mô tả ở phần tiếp theo có thể giúp giải quyết vấn đề này bằng cách cô lập một số lớp lưu lượng khỏi các lớp khác. Ngoài ra còn có khả năng một biến thể của RED có thể loại bỏ mạnh hơn từ các luồng không phản hồi với các tín hiệu ban đầu mà nó gửi.

Explicit Congestion Notification
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

RED là cơ chế AQM được nghiên cứu nhiều nhất, nhưng nó chưa được triển khai rộng rãi, một phần do nó không mang lại hành vi lý tưởng trong mọi trường hợp. Tuy nhiên, nó là chuẩn mực để hiểu hành vi của AQM. Điều khác nữa xuất phát từ RED là nhận thức rằng TCP có thể làm tốt hơn nếu các bộ định tuyến gửi tín hiệu tắc nghẽn rõ ràng hơn.

Tức là, thay vì *loại bỏ* một gói và giả định TCP cuối cùng sẽ nhận ra (ví dụ, do nhận được một ACK trùng lặp), RED (hoặc bất kỳ thuật toán AQM nào) có thể làm tốt hơn nếu thay vào đó *đánh dấu* gói và tiếp tục gửi nó đến đích. Ý tưởng này đã được chuẩn hóa trong các thay đổi đối với tiêu đề IP và TCP được gọi là *Thông báo Tắc nghẽn Rõ ràng* (ECN).

Cụ thể, phản hồi này được thực hiện bằng cách sử dụng hai bit trong trường ``TOS`` của IP làm các bit ECN. Một bit được đặt bởi nguồn để chỉ ra rằng nó có khả năng ECN, tức là có thể phản ứng với thông báo tắc nghẽn. Bit này gọi là ``ECT`` (ECN-Capable Transport). Bit còn lại được đặt bởi các bộ định tuyến dọc theo đường đi đầu-cuối khi gặp tắc nghẽn, như được tính bởi bất kỳ thuật toán AQM nào mà nó đang chạy. Bit này gọi là ``CE`` (Congestion Encountered).

Ngoài hai bit này trong tiêu đề IP (không phụ thuộc vào giao thức vận chuyển), ECN còn bổ sung hai cờ tùy chọn vào tiêu đề TCP. Đầu tiên, ``ECE`` (ECN-Echo), truyền đạt từ bộ nhận đến bộ gửi rằng nó đã nhận được một gói có bit ``CE`` được đặt. Thứ hai, ``CWR`` (Congestion Window Reduced) truyền đạt từ bộ gửi đến bộ nhận rằng nó đã giảm cửa sổ tắc nghẽn.

Mặc dù ECN hiện là cách diễn giải tiêu chuẩn của hai trong tám bit trong trường ``TOS`` của tiêu đề IP và việc hỗ trợ ECN được khuyến nghị cao, nhưng nó không bắt buộc. Hơn nữa, không có một thuật toán AQM nào được khuyến nghị duy nhất, mà thay vào đó là một danh sách các yêu cầu mà một thuật toán AQM tốt nên đáp ứng. Giống như các thuật toán kiểm soát tắc nghẽn TCP, mỗi thuật toán AQM đều có ưu và nhược điểm riêng, vì vậy chúng ta cần nhiều loại khác nhau. Tuy nhiên, có một kịch bản cụ thể mà thuật toán kiểm soát tắc nghẽn TCP và thuật toán AQM được thiết kế để phối hợp với nhau: trung tâm dữ liệu. Chúng tôi sẽ quay lại trường hợp sử dụng này ở cuối phần này.

6.4.2 Các Phương Pháp Dựa Trên Nguồn (Vegas, BBR, DCTCP)
--------------------------------------------------------

Không giống như các sơ đồ tránh tắc nghẽn trước đó, vốn phụ thuộc vào sự hợp tác từ các bộ định tuyến, chúng tôi bây giờ mô tả một chiến lược phát hiện các giai đoạn đầu của tắc nghẽn—trước khi xảy ra mất mát—từ các máy chủ đầu cuối. Trước tiên, chúng tôi đưa ra tổng quan ngắn gọn về một tập hợp các cơ chế liên quan sử dụng các thông tin khác nhau để phát hiện các giai đoạn đầu của tắc nghẽn, sau đó mô tả chi tiết hai cơ chế cụ thể.

Ý tưởng chung của các kỹ thuật này là theo dõi một dấu hiệu từ mạng cho thấy hàng đợi của một bộ định tuyến nào đó đang tăng lên và tắc nghẽn sẽ xảy ra sớm nếu không có biện pháp gì. Ví dụ, nguồn có thể nhận thấy rằng khi các hàng đợi gói tăng lên trong các bộ định tuyến của mạng, có sự gia tăng có thể đo được về RTT cho mỗi gói liên tiếp mà nó gửi. Một thuật toán cụ thể khai thác quan sát này như sau: Cửa sổ tắc nghẽn thường tăng như trong TCP, nhưng cứ hai lần trễ khứ hồi thuật toán kiểm tra xem RTT hiện tại có lớn hơn trung bình của RTT nhỏ nhất và lớn nhất đã thấy cho đến nay không. Nếu có, thuật toán giảm cửa sổ tắc nghẽn đi một phần tám.

Một thuật toán thứ hai làm điều tương tự. Quyết định có thay đổi kích thước cửa sổ hiện tại hay không dựa trên cả thay đổi về RTT và kích thước cửa sổ. Cửa sổ được điều chỉnh một lần mỗi hai lần trễ khứ hồi dựa trên tích

::

   (CurrentWindow - OldWindow) x (CurrentRTT - OldRTT)

Nếu kết quả dương, nguồn giảm kích thước cửa sổ đi một phần tám; nếu kết quả âm hoặc bằng 0, nguồn tăng cửa sổ lên một kích thước gói tối đa. Lưu ý rằng cửa sổ thay đổi trong mỗi lần điều chỉnh; tức là nó dao động quanh điểm tối ưu.

Một thay đổi khác được quan sát khi mạng tiến gần đến tắc nghẽn là tốc độ gửi bị “phẳng” lại. Một sơ đồ thứ ba tận dụng thực tế này. Mỗi RTT, nó tăng kích thước cửa sổ lên một gói và so sánh thông lượng đạt được với thông lượng khi cửa sổ nhỏ hơn một gói. Nếu sự khác biệt nhỏ hơn một nửa thông lượng đạt được khi chỉ có một gói đang truyền—như ở đầu kết nối—thuật toán giảm cửa sổ đi một gói. Sơ đồ này tính toán thông lượng bằng cách chia số byte outstanding trong mạng cho RTT.

TCP Vegas
~~~~~~~~~

Cơ chế mà chúng tôi sẽ mô tả chi tiết tương tự như thuật toán cuối cùng ở chỗ nó xem xét thay đổi tốc độ thông lượng, hoặc cụ thể hơn, thay đổi tốc độ gửi. Tuy nhiên, nó khác với thuật toán trước ở cách tính toán thông lượng, và thay vì tìm kiếm sự thay đổi độ dốc của thông lượng, nó so sánh tốc độ thông lượng đo được với tốc độ thông lượng kỳ vọng. Thuật toán này, TCP Vegas, hiện không được triển khai rộng rãi trên Internet, nhưng chiến lược mà nó sử dụng đã được các triển khai khác áp dụng và hiện đang được triển khai.

Trực giác đằng sau thuật toán Vegas có thể thấy trong biểu đồ TCP tiêu chuẩn ở :numref:`Hình %s <fig-trace3>`. Biểu đồ trên cùng trong :numref:`Hình %s <fig-trace3>` vẽ cửa sổ tắc nghẽn của kết nối; nó cho thấy cùng thông tin như các biểu đồ trước đó trong phần này. Biểu đồ giữa và dưới cùng thể hiện thông tin mới: Biểu đồ giữa cho thấy tốc độ gửi trung bình đo tại nguồn, và biểu đồ dưới cùng cho thấy độ dài hàng đợi trung bình đo tại bộ định tuyến nút cổ chai. Cả ba biểu đồ đều được đồng bộ theo thời gian. Trong khoảng thời gian từ 4,5 đến 6,0 giây (vùng tô bóng), cửa sổ tắc nghẽn tăng (biểu đồ trên cùng). Chúng ta kỳ vọng thông lượng quan sát được cũng tăng, nhưng thay vào đó nó lại giữ nguyên (biểu đồ giữa). Điều này là vì thông lượng không thể tăng vượt quá băng thông khả dụng. Vượt qua điểm này, bất kỳ sự tăng nào của kích thước cửa sổ chỉ dẫn đến các gói chiếm không gian bộ đệm tại bộ định tuyến nút cổ chai (biểu đồ dưới cùng).

.. _fig-trace3:
.. figure:: figures/f06-18-9780123850591.png
   :width: 600px
   :align: center

   Cửa sổ tắc nghẽn so với tốc độ thông lượng quan sát được (ba biểu đồ được đồng bộ). Trên cùng, cửa sổ tắc nghẽn; giữa, thông lượng quan sát được; dưới cùng, không gian bộ đệm bị chiếm tại bộ định tuyến. Đường màu = `CongestionWindow`; chấm tròn = timeout; dấu gạch = thời điểm mỗi gói được truyền; vạch dọc = thời điểm một gói cuối cùng bị truyền lại được truyền lần đầu.

Một phép ẩn dụ hữu ích mô tả hiện tượng minh họa trong :numref:`Hình %s <fig-trace3>` là lái xe trên băng. Đồng hồ tốc độ (cửa sổ tắc nghẽn) có thể báo bạn đang đi 30 dặm/giờ, nhưng nhìn ra cửa sổ xe và thấy người đi bộ vượt qua bạn (tốc độ gửi đo được) bạn biết rằng bạn chỉ đi được 5 dặm/giờ. Năng lượng dư thừa bị hấp thụ bởi lốp xe (bộ đệm bộ định tuyến).

TCP Vegas sử dụng ý tưởng này để đo và kiểm soát lượng dữ liệu dư thừa mà kết nối này có trong mạng, trong đó “dữ liệu dư thừa” là dữ liệu mà nguồn sẽ không truyền nếu nó cố gắng khớp chính xác với băng thông khả dụng của mạng. Mục tiêu của TCP Vegas là duy trì lượng dữ liệu dư thừa “đúng” trong mạng. Rõ ràng, nếu một nguồn gửi quá nhiều dữ liệu dư thừa, nó sẽ gây ra độ trễ lớn và có thể dẫn đến tắc nghẽn. Ít rõ ràng hơn, nếu một kết nối gửi quá ít dữ liệu dư thừa, nó không thể phản ứng đủ nhanh với các đợt tăng băng thông khả dụng tạm thời. Hành động tránh tắc nghẽn của TCP Vegas dựa trên thay đổi lượng dữ liệu dư thừa ước tính trong mạng, không chỉ dựa trên các gói bị loại bỏ. Chúng tôi sẽ mô tả thuật toán chi tiết dưới đây.

Đầu tiên, định nghĩa ``BaseRTT`` của một luồng là RTT của một gói khi luồng không bị tắc nghẽn. Trong thực tế, TCP Vegas đặt ``BaseRTT`` là giá trị nhỏ nhất của tất cả các thời gian khứ hồi đo được; thường là RTT của gói đầu tiên được gửi bởi kết nối, trước khi hàng đợi bộ định tuyến tăng lên do lưu lượng do luồng này tạo ra. Nếu chúng ta giả định rằng chúng ta không làm tràn kết nối, thì thông lượng kỳ vọng được cho bởi

::

   ExpectedRate = CongestionWindow / BaseRTT

trong đó ``CongestionWindow`` là cửa sổ tắc nghẽn TCP, mà chúng ta giả định (cho mục đích thảo luận này) bằng với số byte đang truyền.

Thứ hai, TCP Vegas tính toán tốc độ gửi hiện tại, ``ActualRate``. Điều này được thực hiện bằng cách ghi lại thời gian gửi của một gói đặc biệt, ghi lại số byte được truyền giữa thời điểm gói đó được gửi và khi ACK của nó được nhận, tính toán RTT mẫu cho gói đặc biệt khi ACK của nó đến, và chia số byte truyền cho RTT mẫu. Phép tính này được thực hiện một lần mỗi thời gian khứ hồi.

Thứ ba, TCP Vegas so sánh ``ActualRate`` với ``ExpectedRate`` và điều chỉnh cửa sổ tương ứng. Ta đặt ``Diff = ExpectedRate - ActualRate``. Lưu ý rằng ``Diff`` luôn dương hoặc bằng 0 theo định nghĩa, vì ``ActualRate > ExpectedRate`` ngụ ý rằng chúng ta cần thay đổi ``BaseRTT`` thành RTT mẫu mới nhất. Chúng ta cũng định nghĩa hai ngưỡng, *α < β*, xấp xỉ tương ứng với việc có quá ít và quá nhiều dữ liệu dư thừa trong mạng. Khi ``Diff`` < *α*, TCP Vegas tăng cửa sổ tắc nghẽn tuyến tính trong RTT tiếp theo, và khi ``Diff`` > *β*, TCP Vegas giảm cửa sổ tắc nghẽn tuyến tính trong RTT tiếp theo. TCP Vegas giữ nguyên cửa sổ tắc nghẽn khi *α* < ``Diff`` < *β*.

Trực giác, chúng ta thấy rằng càng xa nhau giữa thông lượng thực tế và thông lượng kỳ vọng, thì càng có nhiều tắc nghẽn trong mạng, điều này ngụ ý rằng tốc độ gửi nên giảm. Ngưỡng *β* kích hoạt việc giảm này. Ngược lại, khi tốc độ thông lượng thực tế tiến gần đến tốc độ kỳ vọng, kết nối có nguy cơ không tận dụng hết băng thông khả dụng. Ngưỡng *α* kích hoạt việc tăng này. Mục tiêu tổng thể là giữ giữa *α* và *β* byte dư thừa trong mạng.

.. _fig-vegas:
.. figure:: figures/f06-19-9780123850591.png
   :width: 600px
   :align: center

   Biểu đồ thuật toán tránh tắc nghẽn TCP Vegas.
   Trên cùng, cửa sổ tắc nghẽn; dưới cùng, thông lượng kỳ vọng (đường màu) và thực tế (đường đen). Vùng tô bóng là vùng giữa các ngưỡng *α* và *β*.

:numref:`Hình %s <fig-vegas>` vẽ thuật toán tránh tắc nghẽn TCP Vegas. Biểu đồ trên cùng vẽ cửa sổ tắc nghẽn, cho thấy cùng thông tin như các biểu đồ khác trong chương này. Biểu đồ dưới cùng vẽ tốc độ thông lượng kỳ vọng và thực tế quyết định cách đặt cửa sổ tắc nghẽn. Chính biểu đồ dưới cùng này minh họa rõ nhất cách thuật toán hoạt động. Đường màu theo dõi ``ExpectedRate``, trong khi đường đen theo dõi ``ActualRate``. Dải tô bóng rộng là vùng giữa các ngưỡng *α* và *β*; đỉnh của dải tô bóng cách ``ExpectedRate`` *α* KBps, và đáy cách *β* KBps. Mục tiêu là giữ ``ActualRate`` giữa hai ngưỡng này, trong vùng tô bóng. Bất cứ khi nào ``ActualRate`` rơi xuống dưới vùng tô bóng (tức là, cách quá xa ``ExpectedRate``), TCP Vegas giảm cửa sổ tắc nghẽn vì lo ngại quá nhiều gói đang bị đệm trong mạng. Tương tự, khi ``ActualRate`` vượt lên trên vùng tô bóng (tức là, tiến quá gần ``ExpectedRate``), TCP Vegas tăng cửa sổ tắc nghẽn vì lo ngại không tận dụng hết băng thông mạng.

Vì thuật toán, như vừa trình bày, so sánh sự khác biệt giữa tốc độ thông lượng thực tế và kỳ vọng với các ngưỡng *α* và *β*, nên hai ngưỡng này được định nghĩa theo KBps. Tuy nhiên, có lẽ chính xác hơn là nghĩ theo số *bộ đệm* mà kết nối chiếm trong mạng. Ví dụ, trên một kết nối có ``BaseRTT`` là 100 ms và kích thước gói là 1 KB, nếu *α* = 30 KBps và *β* = 60 KBps, thì chúng ta có thể coi *α* là quy định kết nối cần chiếm ít nhất 3 bộ đệm dư thừa trong mạng và *β* là không nên chiếm quá 6 bộ đệm dư thừa. Trong thực tế, đặt *α* là 1 bộ đệm và *β* là 3 bộ đệm hoạt động tốt.

Cuối cùng, bạn sẽ nhận thấy rằng TCP Vegas giảm cửa sổ tắc nghẽn tuyến tính, dường như mâu thuẫn với quy tắc rằng giảm nhân là cần thiết để đảm bảo ổn định. Giải thích là TCP Vegas thực sự sử dụng giảm nhân khi xảy ra timeout; việc giảm tuyến tính vừa mô tả là một *giảm sớm* cửa sổ tắc nghẽn nên xảy ra trước khi tắc nghẽn xảy ra và các gói bắt đầu bị loại bỏ.

TCP BBR
~~~~~~~

BBR (Bottleneck Bandwidth and RTT) là một thuật toán kiểm soát tắc nghẽn TCP mới do các nhà nghiên cứu tại Google phát triển. Giống như Vegas, BBR dựa trên độ trễ, nghĩa là nó cố gắng phát hiện sự tăng bộ đệm để tránh tắc nghẽn và mất gói. Cả BBR và Vegas đều sử dụng RTT nhỏ nhất và RTT lớn nhất, được tính toán trong một khoảng thời gian nhất định, làm tín hiệu điều khiển chính.

BBR cũng giới thiệu các cơ chế mới để cải thiện hiệu suất, bao gồm pacing gói, dò băng thông và dò RTT. Pacing gói phân bổ các gói dựa trên ước lượng băng thông khả dụng. Điều này loại bỏ các đợt bùng nổ và xếp hàng không cần thiết, dẫn đến tín hiệu phản hồi tốt hơn. BBR cũng định kỳ tăng tốc độ, từ đó dò tìm băng thông khả dụng. Tương tự, BBR định kỳ giảm tốc độ, từ đó dò tìm RTT nhỏ nhất mới. Cơ chế dò RTT cố gắng tự đồng bộ, nghĩa là, khi có nhiều luồng BBR, các lần dò RTT của chúng xảy ra cùng lúc. Điều này cho cái nhìn chính xác hơn về RTT đường đi thực sự không tắc nghẽn, giải quyết một trong những vấn đề lớn của các cơ chế kiểm soát tắc nghẽn dựa trên độ trễ: có kiến thức chính xác về RTT đường đi không tắc nghẽn.

BBR đang được phát triển tích cực và thay đổi nhanh chóng. Một trọng tâm lớn là tính công bằng. Ví dụ, một số thí nghiệm cho thấy các luồng CUBIC nhận được băng thông ít hơn 100 lần khi cạnh tranh với các luồng BBR, và các thí nghiệm khác cho thấy thậm chí có thể xảy ra sự không công bằng giữa các luồng BBR. Một trọng tâm lớn khác là tránh tỷ lệ truyền lại cao, trong một số trường hợp lên tới 10% số gói bị truyền lại.

DCTCP
~~~~~

Chúng tôi kết thúc với một ví dụ về tình huống mà một biến thể của thuật toán kiểm soát tắc nghẽn TCP được thiết kế để phối hợp với ECN: trong các trung tâm dữ liệu đám mây. Sự kết hợp này gọi là DCTCP, viết tắt của *Data Center TCP*. Tình huống này là duy nhất vì một trung tâm dữ liệu là một hệ thống khép kín, do đó có thể triển khai một phiên bản TCP được thiết kế riêng mà không cần lo lắng về việc đối xử công bằng với các luồng TCP khác. Các trung tâm dữ liệu cũng đặc biệt ở chỗ chúng được xây dựng bằng các switch thương mại, và vì không cần lo lắng về các đường truyền dài rộng khắp lục địa, các switch thường được trang bị mà không dư thừa bộ đệm.

Ý tưởng rất đơn giản. DCTCP điều chỉnh ECN bằng cách ước lượng tỷ lệ byte gặp tắc nghẽn thay vì chỉ phát hiện rằng có tắc nghẽn sắp xảy ra. Tại các máy chủ đầu cuối, DCTCP sau đó điều chỉnh cửa sổ tắc nghẽn dựa trên ước lượng này. Thuật toán TCP tiêu chuẩn vẫn được áp dụng nếu một gói thực sự bị mất. Cách tiếp cận này được thiết kế để đạt khả năng chịu bùng nổ cao, độ trễ thấp và thông lượng cao với các switch có bộ đệm nông.

Thách thức chính mà DCTCP đối mặt là ước lượng tỷ lệ byte gặp tắc nghẽn. Mỗi switch rất đơn giản. Nếu một gói đến và switch thấy độ dài hàng đợi (K) vượt quá một ngưỡng nào đó; ví dụ,

.. centered:: K > (RTT × C)/7

trong đó C là tốc độ liên kết tính bằng gói mỗi giây, thì switch đặt bit CE trong tiêu đề IP. Sự phức tạp của RED không cần thiết.

Bộ nhận sau đó duy trì một biến boolean cho mỗi luồng, chúng tôi ký hiệu là ``SeenCE``, và thực hiện máy trạng thái sau cho mỗi gói nhận được:

-  Nếu bit CE được đặt và ``SeenCE=False``, đặt ``SeenCE`` thành True và gửi một ACK ngay lập tức.

-  Nếu bit CE không được đặt và ``SeenCE=True``, đặt ``SeenCE`` thành False và gửi một ACK ngay lập tức.

-  Ngược lại, bỏ qua bit CE.

Hệ quả không rõ ràng của trường hợp “ngược lại” là bộ nhận tiếp tục gửi các ACK trễ sau mỗi *n* gói, bất kể bit CE có được đặt hay không. Điều này đã được chứng minh là quan trọng để duy trì hiệu suất cao.

Cuối cùng, bộ gửi tính toán tỷ lệ byte gặp tắc nghẽn trong cửa sổ quan sát trước đó (thường chọn xấp xỉ RTT), là tỷ số giữa tổng số byte truyền và số byte được xác nhận với cờ ECE được đặt. DCTCP tăng cửa sổ tắc nghẽn giống hệt như thuật toán tiêu chuẩn, nhưng giảm cửa sổ tỷ lệ thuận với số byte gặp tắc nghẽn trong cửa sổ quan sát trước đó.
