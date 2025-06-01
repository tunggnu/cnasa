Góc nhìn: An ninh mạng đang trở nên tệ hơn hay tốt hơn?
====================================================

Các sự cố an ninh mạng giờ đây là một rủi ro tiêu chuẩn khi kết nối bất kỳ hệ thống nào với Internet, và hiếm khi có một tuần trôi qua mà không có tin tức về một cuộc tấn công mới nào đó. Verizon, một công ty viễn thông lớn, thực hiện một cuộc đánh giá thường niên về tình trạng các cuộc tấn công mạng; vào năm 2024 họ đã đạt một cột mốc với hơn 10.000 sự cố, như tấn công ransomware và lừa đảo qua email, được đề cập trong báo cáo. Nếu đo bằng số lượng các cuộc tấn công thì có vẻ mọi thứ đang trở nên tệ hơn. Ngược lại, một phân tích về tác động kinh tế của các cuộc tấn công mạng do *The Economist* thực hiện lại cho rằng thời kỳ đỉnh cao của các cuộc tấn công mạng có thể đã qua. Theo phân tích kinh tế của họ, giai đoạn tồi tệ nhất của các cuộc tấn công là năm 2003–2004 với khoảng cách rất lớn.

Chắc chắn có một số khía cạnh mà an ninh mạng đang được cải thiện. Điều này có thể được lý giải bởi nhận thức ngày càng cao về sự cần thiết của bảo mật mạnh mẽ bất cứ khi nào xử lý thông tin quan trọng, cũng như sự phát triển của các kỹ thuật mới cho phép thực hiện các biện pháp bảo mật tốt hơn.

Một ví dụ về sự phát triển công nghệ đã cải thiện việc triển khai bảo mật là SDN (mạng định nghĩa bằng phần mềm). SDN đã cho phép một cách tiếp cận tương đối mới trong việc cung cấp sự cách ly giữa các hệ thống, được gọi là *microsegmentation* (phân đoạn vi mô).


Phân đoạn vi mô trái ngược với các phương pháp truyền thống trong việc phân đoạn mạng, trong đó các nhóm lớn máy tính kết nối vào một “vùng” và tường lửa được sử dụng để lọc lưu lượng giữa các vùng. Mặc dù điều này giúp cấu hình mạng trở nên đơn giản, nhưng nó cũng đồng nghĩa với việc nhiều máy nằm trong cùng một vùng ngay cả khi chúng không cần giao tiếp với nhau. Hơn nữa, độ phức tạp của các quy tắc tường lửa sẽ tăng dần theo thời gian khi ngày càng nhiều quy tắc cần được thêm vào để mô tả lưu lượng được phép đi từ vùng này sang vùng khác.

Ngược lại, SDN cho phép tạo ra các mạng ảo được xác định chính xác—các phân đoạn vi mô—quy định rõ những máy nào có thể giao tiếp với nhau và giao tiếp như thế nào. Ví dụ, một ứng dụng ba lớp có thể có chính sách phân đoạn vi mô riêng, quy định: các máy ở tầng giao tiếp với web của ứng dụng có thể nói chuyện với các máy ở tầng ứng dụng trên một tập hợp các cổng được chỉ định, nhưng các máy ở tầng giao tiếp với web không được phép nói chuyện với nhau. Đây là một chính sách rất khó thực hiện trong quá khứ; thay vào đó, tất cả các máy ở tầng giao tiếp với web sẽ nằm trên cùng một phân đoạn mạng, tự do giao tiếp với nhau.


Độ phức tạp trong việc cấu hình các phân đoạn là lý do khiến các máy từ nhiều ứng dụng khác nhau thường nằm trên cùng một phân đoạn, tạo cơ hội cho các cuộc tấn công lan rộng từ ứng dụng này sang ứng dụng khác. Việc di chuyển ngang của các cuộc tấn công trong các trung tâm dữ liệu đã được ghi nhận rõ ràng là một chiến lược then chốt của các cuộc tấn công mạng thành công trong nhiều năm qua.


Hãy xem xét cách bố trí các máy ảo và tường lửa trong :numref:`Figure %s <fig-standard-firewall>`. Giả sử chúng ta muốn đặt VM A và VM B vào các phân đoạn khác nhau và áp dụng một quy tắc tường lửa cho lưu lượng đi từ VM A sang VM B. Chúng ta phải ngăn VM A gửi lưu lượng trực tiếp đến VM B. Để làm điều này, chúng ta có thể cấu hình hai VLAN trong mạng vật lý, kết nối A vào một VLAN, B vào VLAN còn lại, rồi cấu hình định tuyến sao cho đường đi từ VLAN đầu tiên sang VLAN thứ hai phải đi qua tường lửa. Nếu tại một thời điểm nào đó VM A được chuyển sang một máy chủ khác, chúng ta lại phải đảm bảo VLAN phù hợp được kết nối tới máy chủ đó, kết nối VM A vào đó, và đảm bảo cấu hình định tuyến vẫn buộc lưu lượng đi qua tường lửa. Tình huống này có vẻ hơi gượng ép, nhưng nó cho thấy tại sao phân đoạn vi mô lại khó triển khai trước khi có SDN. Ngược lại, SDN cho phép chức năng tường lửa được triển khai ngay trong mỗi switch ảo (vS trong hình). Do đó, lưu lượng từ VM A đến VM B đi qua tường lửa mà không cần cấu hình định tuyến đặc biệt nào. Nhiệm vụ của bộ điều khiển SDN là tạo ra quy tắc tường lửa phù hợp để thực thi sự cách ly mong muốn giữa VM A và VM B (và xử lý khi VM A hoặc VM B di chuyển nếu có). Không có phép màu nào ở đây, nhưng SDN mang lại cho chúng ta một công cụ mới để quản lý mức độ cách ly chi tiết hơn một cách dễ dàng hơn.


.. _fig-standard-firewall:
.. figure:: figures/firewall-std.png
    :width: 600px
    :align: center

    Một tập hợp các máy ảo giao tiếp thông qua tường lửa

Sự phát triển của phân đoạn vi mô trong thập kỷ qua là một trong những động lực chính thúc đẩy việc áp dụng SDN trong doanh nghiệp. Nó đã trở thành nền tảng cho một thực tiễn tốt nhất về bảo mật được gọi là mạng “zero-trust” (không tin cậy). Zero trust có nghĩa là, càng nhiều càng tốt, mọi hệ thống trong mạng đều được giả định là không đáng tin cậy, và do đó nên được cách ly với tất cả các hệ thống khác ngoại trừ đúng những hệ thống mà nó cần truy cập để thực hiện nhiệm vụ được giao.

Tầm quan trọng của Internet trong việc vận hành các hệ thống trọng yếu và là nền tảng cho phần lớn thương mại toàn cầu đã khiến nó trở thành mục tiêu hấp dẫn cho tin tặc. Đồng thời, điều này cũng nhấn mạnh sự cần thiết phải phát triển và áp dụng các thực tiễn tốt nhất như mạng zero-trust. Khi chúng ta đọc về các vụ vi phạm ngày nay, thường là do một số thực tiễn tốt nhất nào đó đã không được tuân thủ. Báo cáo an ninh mạng của Verizon, bằng cách ghi lại những gì đã xảy ra trong hàng ngàn cuộc tấn công, cung cấp thông tin hữu ích về việc có bao nhiêu cuộc tấn công trong số đó có thể được ngăn chặn nhờ áp dụng các kỹ thuật vệ sinh mạng đã được thiết lập.

.. admonition:: Góc nhìn rộng hơn

   Báo cáo vi phạm dữ liệu của Verizon có thể được lấy từ
   `Data Breach Investigations Report
   <https://www.verizon.com/business/resources/reports/dbir/>`__.

   Tác động kinh tế của các cuộc tấn công mạng được phân tích bởi *The Economist*
   trong bài viết này: `Unexpectedly, the cost of big cyber-attacks is
   falling
   <https://www.economist.com/graphic-detail/2024/05/17/unexpectedly-the-cost-of-big-cyber-attacks-is-falling>`__,
   tháng 5 năm 2024.

   Một thảo luận sâu hơn về việc sử dụng các kỹ thuật SDN để cải thiện
   bảo mật được trình bày trong Chương 8 của cuốn sách `Software-Defined
   Networking: A Systems Approach
   <https://sdn.systemsapproach.org/>`__.
