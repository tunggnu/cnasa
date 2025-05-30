Góc nhìn: Điều phối lưu lượng dựa trên phần mềm
===============================================

Vấn đề tổng quát mà chương này đề cập là làm thế nào để phân bổ băng thông mạng sẵn có cho một tập các luồng đầu-cuối. Dù là kiểm soát tắc nghẽn TCP, dịch vụ tích hợp hay dịch vụ phân biệt, đều có một giả định rằng băng thông mạng cơ sở được phân bổ là cố định: một liên kết 1-Gbps giữa điểm A và điểm B luôn là một liên kết 1-Gbps, và các thuật toán tập trung vào việc chia sẻ tốt nhất 1-Gbps đó giữa các người dùng cạnh tranh. Nhưng nếu không phải như vậy thì sao? Nếu bạn có thể “ngay lập tức” có thêm dung lượng, để liên kết 1-Gbps được nâng cấp thành 10-Gbps, hoặc thậm chí bạn có thể thêm một liên kết mới giữa hai điểm trước đó chưa từng kết nối thì sao?

Khả năng này là có thật, và nó thường được gọi là *điều phối lưu lượng* (traffic engineering), một thuật ngữ có từ những ngày đầu của mạng máy tính khi các nhà vận hành sẽ phân tích tải lưu lượng trên mạng của họ, và định kỳ thiết kế lại mạng để bổ sung dung lượng khi các liên kết hiện tại bị quá tải mãn tính. Trong những ngày đầu đó, quyết định bổ sung dung lượng không được đưa ra một cách nhẹ nhàng; bạn cần chắc chắn rằng xu hướng sử dụng mà bạn quan sát không chỉ là một biến động nhất thời vì sẽ mất rất nhiều thời gian và tiền bạc để thay đổi mạng. Trong trường hợp xấu nhất, có thể phải kéo cáp qua đại dương hoặc phóng vệ tinh lên không gian.

Nhưng với sự xuất hiện của các công nghệ như DWDM (:ref:`Mục 3.1 <3.1 Switching Basics>`) và MPLS (:ref:`Mục 4.4 <4.4 Multiprotocol Label Switching>`), chúng ta không phải lúc nào cũng cần kéo thêm cáp quang, mà thay vào đó có thể bật thêm các bước sóng hoặc thiết lập các mạch mới giữa bất kỳ cặp điểm nào. (Các điểm này không nhất thiết phải được kết nối trực tiếp bằng cáp quang. Ví dụ, một bước sóng giữa Boston và San Francisco có thể đi qua các ROADM ở Chicago và Denver, nhưng từ góc nhìn của tầng L2/L3, Boston và San Francisco được kết nối bởi một liên kết trực tiếp.) Điều này làm giảm đáng kể thời gian để có thêm dung lượng, nhưng việc cấu hình lại phần cứng vẫn cần can thiệp thủ công, nên định nghĩa “ngay lập tức” của chúng ta vẫn được đo bằng ngày, nếu không phải là tuần. Dù sao thì vẫn phải điền các mẫu đơn đặt hàng, thậm chí là ba bản!

Nhưng như chúng ta đã thấy nhiều lần, một khi bạn cung cấp các giao diện lập trình phù hợp, phần mềm có thể được sử dụng để giải quyết vấn đề, và “ngay lập tức” thực sự có thể là tức thời trên thực tế. Đây chính là điều mà các nhà cung cấp dịch vụ đám mây làm với các mạng trục riêng mà họ xây dựng để kết nối các trung tâm dữ liệu của mình. Ví dụ, Google đã công khai mô tả mạng WAN riêng của họ, gọi là B4, được xây dựng hoàn toàn bằng các switch bare-metal và SDN. B4 không thêm/bớt bước sóng để điều chỉnh băng thông giữa các nút—nó xây dựng động các đường hầm đầu-cuối bằng kỹ thuật gọi là *Equal-Cost Multipath* (ECMP), một lựa chọn thay thế cho CSPF đã được giới thiệu ở :ref:`Mục 4.4 <4.4 Multiprotocol Label Switching>`—nhưng sự linh hoạt mà nó mang lại là tương tự.

Một chương trình điều phối lưu lượng (Traffic Engineering, TE) sau đó sẽ cấu hình mạng theo nhu cầu của các lớp ứng dụng khác nhau. B4 xác định ba lớp như sau: (1) sao lưu dữ liệu người dùng (ví dụ: email, tài liệu, âm thanh/hình ảnh) đến các trung tâm dữ liệu từ xa để đảm bảo khả năng sẵn sàng; (2) truy cập lưu trữ từ xa bởi các tác vụ tính toán chạy trên các nguồn dữ liệu phân tán; và (3) đẩy dữ liệu quy mô lớn để đồng bộ trạng thái giữa nhiều trung tâm dữ liệu. Các lớp này được sắp xếp theo thứ tự tăng dần về lưu lượng, giảm dần về độ nhạy trễ, và giảm dần về mức ưu tiên tổng thể. Ví dụ, dữ liệu người dùng là lưu lượng thấp nhất trên B4, nhạy cảm với trễ nhất và có mức ưu tiên cao nhất.

Bằng cách tập trung hóa quá trình ra quyết định—một trong những lợi thế được cho là của SDN—Google đã có thể đẩy mức sử dụng liên kết lên gần 100%. Con số này cao gấp hai đến ba lần mức sử dụng trung bình 30-40% mà các liên kết WAN thường được cấu hình, nhằm cho phép các mạng đó xử lý cả các đợt bùng nổ lưu lượng và sự cố liên kết/switch. Nếu bạn có thể quyết định tập trung cách phân bổ tài nguyên trên toàn mạng, bạn có thể vận hành mạng gần với mức sử dụng tối đa hơn nhiều. Lưu ý rằng việc cấu hình liên kết trong mạng được thực hiện cho các lớp ứng dụng thô. Kiểm soát tắc nghẽn TCP vẫn hoạt động trên từng kết nối riêng lẻ, và các quyết định định tuyến vẫn được thực hiện dựa trên topo B4. (Bên lề, cũng đáng chú ý là vì B4 là một WAN riêng, Google có thể tự do chạy thuật toán kiểm soát tắc nghẽn riêng, như BBR, mà không lo gây bất công cho các thuật toán khác.)

Một bài học rút ra từ các hệ thống như B4 là ranh giới giữa điều phối lưu lượng và kiểm soát tắc nghẽn (cũng như giữa điều phối lưu lượng và định tuyến) là mờ nhạt. Có nhiều cơ chế khác nhau cùng giải quyết một vấn đề tổng quát, nên không có một ranh giới cứng nào xác định nơi một cơ chế dừng và cơ chế khác bắt đầu. Nói ngắn gọn, ranh giới giữa các lớp trở nên mềm (và dễ di chuyển) khi các lớp được triển khai bằng phần mềm thay vì phần cứng. Điều này ngày càng trở thành chuẩn mực.

.. admonition:: Góc nhìn rộng hơn

   Để tiếp tục đọc về quá trình “đám mây hóa” của Internet, xem
   :ref:`Góc nhìn: Dữ liệu lớn và Phân tích (Big Data and Analytics)`.

   Để tìm hiểu thêm về B4, chúng tôi khuyến nghị: `B4: Experience with a
   Globally Deployed Software Defined
   WAN <https://cseweb.ucsd.edu/~vahdat/papers/b4-sigcomm13.pdf>`__,
   Tháng 8 năm 2013.

   Để có cái nhìn toàn diện hơn về kiểm soát tắc nghẽn, bao gồm cả các phát triển mới liên quan đến TCP, xem cuốn sách đồng hành của chúng tôi:
   `TCP Congestion Control: A Systems Approach <https://tcpcc.systemsapproach.org>`__.
