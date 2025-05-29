Góc nhìn: Đám mây đang “ăn” Internet
====================================

Đám mây (Cloud) và Internet là hai hệ thống cộng sinh. Chúng từng tách biệt về mặt lịch sử, nhưng ngày nay ranh giới giữa chúng ngày càng trở nên mờ nhạt. Nếu bắt đầu với định nghĩa trong sách giáo khoa, Internet cung cấp kết nối đầu-cuối giữa bất kỳ hai máy chủ nào (ví dụ, một laptop của khách và một máy chủ từ xa), còn đám mây hỗ trợ nhiều trung tâm dữ liệu quy mô nhà kho, mỗi trung tâm cung cấp một cách hiệu quả về chi phí để cấp nguồn, làm mát và vận hành một số lượng lớn máy chủ. Người dùng cuối kết nối đến trung tâm dữ liệu gần nhất qua Internet theo đúng cách họ kết nối đến một máy chủ trong một phòng máy từ xa.

Đó là một mô tả chính xác về mối quan hệ giữa Internet và Đám mây vào những ngày đầu của các nhà cung cấp đám mây thương mại như Amazon, Microsoft và Google. Ví dụ, đám mây của Amazon khoảng năm 2009 có hai trung tâm dữ liệu, một ở bờ đông và một ở bờ tây nước Mỹ. Tuy nhiên, ngày nay, mỗi nhà cung cấp đám mây lớn vận hành hàng chục trung tâm dữ liệu trải khắp toàn cầu, và không có gì ngạc nhiên khi chúng được đặt chiến lược gần các Điểm Trao đổi Internet (IXP), mỗi điểm cung cấp kết nối phong phú đến phần còn lại của Internet. Có hơn 150 IXP trên toàn thế giới, và mặc dù không phải nhà cung cấp đám mây nào cũng nhân bản một trung tâm dữ liệu đầy đủ gần mỗi điểm (nhiều địa điểm trong số này là các cơ sở đồng vị trí), có thể nói rằng nội dung được truy cập nhiều nhất của đám mây (ví dụ, các bộ phim Netflix phổ biến nhất, video YouTube, và ảnh Facebook) có thể được phân phối đến nhiều địa điểm như vậy.

Có hai hệ quả từ sự phân tán rộng rãi của đám mây này. Thứ nhất là đường đi đầu-cuối từ client đến server không nhất thiết phải đi qua toàn bộ Internet. Một người dùng có khả năng sẽ tìm thấy nội dung mình muốn truy cập đã được nhân bản tại một IXP gần đó—thường chỉ cách một bước AS—thay vì nằm ở phía bên kia địa cầu. Hệ quả thứ hai là các nhà cung cấp đám mây lớn không sử dụng Internet công cộng để kết nối các trung tâm dữ liệu phân tán của họ. Việc đồng bộ hóa nội dung giữa các trung tâm dữ liệu phân tán là phổ biến, nhưng họ thường thực hiện điều này qua một backbone riêng. Điều này cho phép họ tận dụng bất kỳ tối ưu hóa nào họ muốn mà không cần phải tương tác hoàn toàn với bất kỳ ai khác.

Nói cách khác, mặc dù các hình vẽ trong :ref:`Mục 4.1 <4.1 Global Internet>` thể hiện khá đúng hình dạng tổng thể của Internet, và BGP làm cho việc kết nối bất kỳ cặp host nào trở nên *có thể*, nhưng trên thực tế hầu hết người dùng tương tác với các ứng dụng chạy trong Đám mây, vốn trông giống như :numref:`Hình %s <fig-cloud>`. (Một chi tiết quan trọng mà hình vẽ không thể hiện là các nhà cung cấp Đám mây thường không tự xây dựng một mạng WAN bằng cách tự kéo cáp quang, mà thay vào đó họ thuê cáp quang từ các nhà cung cấp dịch vụ, nghĩa là backbone riêng của đám mây và backbone của nhà cung cấp dịch vụ thường dùng chung hạ tầng vật lý.)

.. _fig-cloud:
.. figure:: figures/structure/Slide1.png
   :width: 500px
   :align: center

   Đám mây được phân phối rộng khắp Internet
   với các backbone riêng.

Lưu ý rằng mặc dù có thể nhân bản *nội dung* trên nhiều địa điểm của đám mây, chúng ta vẫn chưa có công nghệ để nhân bản *con người*. Điều này có nghĩa là khi những người dùng phân tán rộng muốn giao tiếp với nhau—ví dụ, trong một cuộc gọi hội nghị video—cây multicast sẽ được phân phối trên đám mây. Nói cách khác, multicast thường không chạy trong các router backbone của nhà cung cấp dịch vụ (như :ref:`Mục 4.3 <4.3 Multicast>` gợi ý), mà thay vào đó chạy trong các tiến trình máy chủ phân phối trên một tập con của hơn 150 địa điểm đóng vai trò là các điểm kết nối chính của Internet. Một cây multicast được xây dựng theo cách này được gọi là overlay, chủ đề này sẽ được đề cập lại trong :ref:`Mục 9.4 <9.4 Overlay Networks>`.

.. admonition:: Góc nhìn rộng hơn

   Để tiếp tục đọc về quá trình “đám mây hóa” Internet, xem
   :ref:`Góc nhìn: HTTP là Eo thắt mới`.

   Để tìm hiểu thêm về dấu chân phân phối của Đám mây, chúng tôi khuyến nghị
   `How the Internet Travels Across the
   Ocean <https://www.nytimes.com/interactive/2019/03/10/technology/internet-cables-oceans.html>`__,
   New York Times, tháng 3 năm 2019.
