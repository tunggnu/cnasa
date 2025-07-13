Góc Nhìn: Bảo Mật Đang Trở Nên Tồi Hơn Hay Tốt Hơn?
===================================================

Các vi phạm bảo mật giờ đây là một rủi ro tiêu chuẩn khi kết nối bất kỳ hệ thống nào
với Internet, và hiếm khi có một tuần trôi qua mà không có tin tức về một
cuộc tấn công mới. Verizon, một tập đoàn viễn thông lớn, thực hiện một cuộc đánh giá hàng năm về tình hình
các cuộc tấn công mạng; vào năm 2024 họ đã đạt một cột mốc với hơn 10.000
sự cố, như các cuộc tấn công ransomware và phishing, được đề cập trong
báo cáo. Nếu đo bằng số lượng các cuộc tấn công, thì có vẻ như mọi thứ đang
trở nên tồi tệ hơn. Ngược lại, một phân tích về tác động kinh tế của
các cuộc tấn công mạng do *The Economist* thực hiện cho rằng
thời kỳ đỉnh cao của các cuộc tấn công mạng có thể đã qua. Thời kỳ tồi tệ nhất cho
các cuộc tấn công trong phân tích kinh tế của họ là năm 2003–2004 với khoảng cách lớn.

Chắc chắn có một số khía cạnh mà bảo mật đang được cải thiện. Điều này
có thể được ghi nhận cả nhờ nhận thức cao hơn về nhu cầu bảo mật mạnh mẽ
bất cứ khi nào thông tin quan trọng được xử lý, và nhờ sự
phát triển của các kỹ thuật mới cho phép thực hành bảo mật tốt hơn.

Một ví dụ về sự phát triển công nghệ đã cải thiện việc triển khai bảo mật
là SDN (mạng định nghĩa bằng phần mềm). SDN đã cho phép
một cách tiếp cận tương đối mới để cung cấp sự cách ly giữa các hệ thống, được gọi là
*microsegmentation* (phân đoạn vi mô).


Microsegmentation đối lập với các phương pháp truyền thống trong việc
phân đoạn mạng, trong đó các tập lớn máy
kết nối vào một “vùng” và tường lửa được sử dụng để lọc lưu lượng
đi qua giữa các vùng. Mặc dù điều này giúp cấu hình mạng tương đối đơn giản,
nhưng nó đồng nghĩa với việc nhiều máy nằm trong cùng một
vùng ngay cả khi không cần thiết phải giao tiếp với nhau. Hơn nữa,
độ phức tạp của các quy tắc tường lửa tăng dần theo thời gian khi ngày càng nhiều
quy tắc cần được thêm vào để mô tả lưu lượng được phép đi
từ vùng này sang vùng khác.

Ngược lại, SDN cho phép tạo ra các mạng ảo được xác định chính xác—
các microsegment—quy định cả những máy nào có thể giao tiếp với nhau
và cách chúng có thể làm điều đó. Ví dụ, một ứng dụng ba lớp
có thể có chính sách microsegmentation riêng, quy định: các máy
ở lớp giao tiếp với web của ứng dụng có thể nói chuyện với các máy ở
lớp ứng dụng trên một tập hợp các cổng được chỉ định, nhưng
các máy giao tiếp với web không được phép nói chuyện với nhau. Đây là một chính sách mà
trước đây rất khó thực hiện; thay vào đó, tất cả các máy giao tiếp với web
sẽ nằm trên cùng một đoạn mạng, tự do giao tiếp
với nhau.


Độ phức tạp của việc cấu hình các đoạn mạng là lý do khiến các máy
từ nhiều ứng dụng có thể sẽ nằm trên cùng một đoạn mạng, tạo ra
cơ hội cho một cuộc tấn công lan rộng từ ứng dụng này sang
ứng dụng khác. Sự di chuyển ngang của các cuộc tấn công trong các trung tâm dữ liệu
đã đượcghi nhận rõ ràng như một chiến lược then chốt của các cuộc tấn công mạng thành công
trong nhiều năm.


Xem xét cách bố trí các máy ảo và tường lửa trong :numref:`Figure %s
<fig-standard-firewall>`. Giả sử chúng ta muốn đặt VM A và VM B vào các đoạn mạng khác nhau
và áp dụng một quy tắc tường lửa cho lưu lượng đi từ VM A đến VM B. Chúng ta phải
ngăn VM A gửi lưu lượng trực tiếp đến VM B. Để làm điều này, chúng ta
có thể cấu hình hai VLAN trong mạng vật lý, kết nối A
vào một VLAN, và B vào VLAN còn lại, sau đó cấu hình định tuyến
sao cho đường đi từ VLAN đầu tiên đến VLAN thứ hai phải đi qua
tường lửa. Nếu tại một thời điểm nào đó VM A được chuyển sang một máy chủ khác,
chúng ta sẽ phải đảm bảo VLAN phù hợp được kéo đến *máy chủ đó*, kết nối VM
A vào đó, và đảm bảo cấu hình định tuyến vẫn buộc
lưu lượng đi qua tường lửa. Tình huống này có vẻ hơi
khiên cưỡng, nhưng nó cho thấy tại sao microsegmentation lại khó thực hiện
trước khi có SDN. Ngược lại, SDN cho phép chức năng tường lửa
được hiện thực trong mỗi switch ảo (vS trong
hình). Do đó, lưu lượng từ VM A đến VM B đi qua
tường lửa mà không cần cấu hình định tuyến đặc biệt nào. Công việc của
SDN controller là tạo ra quy tắc tường lửa phù hợp để đảm bảo
cách ly mong muốn giữa VM A và VM B (và xử lý việc di chuyển
của VM A và VM B nếu có). Không có phép màu nào ở đây, nhưng SDN mang lại cho chúng ta
một công cụ mới để quản lý mức độ cách ly tinh vi dễ dàng hơn nhiều.


.. _fig-standard-firewall:
.. figure:: figures/firewall-std.png
    :width: 600px
    :align: center

    Một tập hợp máy ảo giao tiếp thông qua tường lửa

Sự phát triển của microsegmentation trong thập kỷ qua là một trong những
động lực chính thúc đẩy việc áp dụng SDN trong doanh nghiệp. Nó đã trở thành
nền tảng cho một thực hành bảo mật tốt nhất được gọi là “zero-trust”
networking (mạng không tin cậy mặc định). Zero trust có nghĩa là, càng nhiều càng tốt,
mỗi hệ thống trong mạng được giả định là không tin cậy, và do đó nên được cách ly
khỏi tất cả các hệ thống khác ngoại trừ chính xác những hệ thống mà nó cần
truy cập để thực hiện công việc được giao.

Tầm quan trọng của Internet trong việc vận hành
các hệ thống trọng yếu và là nền tảng cho
phần lớn thương mại toàn cầu đã khiến nó trở thành mục tiêu hấp dẫn cho
các hacker. Đồng thời, điều này nhấn mạnh nhu cầu phát triển và áp dụng
các thực hành tốt nhất như mạng zero-trust. Khi chúng ta đọc về các vụ vi phạm
ngày nay, thường là do một thực hành tốt nhất nào đó đã không được
tuân thủ. Báo cáo an ninh mạng của Verizon, bằng cách ghi lại những gì đã
sai trong hàng ngàn cuộc tấn công, cung cấp thông tin hữu ích về
bao nhiêu trong số các cuộc tấn công đó có thể được ngăn chặn nhờ sử dụng các
kỹ thuật vệ sinh mạng đã được thiết lập.

.. admonition:: Góc Nhìn Rộng Hơn

   Báo cáo vi phạm dữ liệu của Verizon có thể được lấy từ
   `Data Breach Investigations Report
   <https://www.verizon.com/business/resources/reports/dbir/>`__.

   Tác động kinh tế của các cuộc tấn công mạng được phân tích bởi *The Economist*
   trong bài viết này: `Unexpectedly, the cost of big cyber-attacks is
   falling
   <https://www.economist.com/graphic-detail/2024/05/17/unexpectedly-the-cost-of-big-cyber-attacks-is-falling>`__,
   Tháng 5 năm 2024.

   Một thảo luận sâu hơn về việc sử dụng các kỹ thuật SDN để cải thiện
   bảo mật được trình bày trong Chương 8 của cuốn sách `Software-Defined
   Networking: A Systems Approach
   <https://sdn.systemsapproach.org/>`__.

