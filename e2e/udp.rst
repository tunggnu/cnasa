5.1 Bộ phân kênh đơn giản (UDP)
===============================

Giao thức vận chuyển đơn giản nhất là giao thức mở rộng dịch vụ chuyển phát từ máy này đến máy khác của mạng nền tảng thành dịch vụ giao tiếp giữa các tiến trình. Có thể có nhiều tiến trình chạy trên mỗi máy chủ, vì vậy giao thức cần bổ sung một mức phân kênh, cho phép nhiều tiến trình ứng dụng trên mỗi máy chủ cùng chia sẻ mạng. Ngoài yêu cầu này, giao thức vận chuyển không bổ sung thêm chức năng nào khác cho dịch vụ nỗ lực tối đa mà mạng nền tảng cung cấp. Giao thức Datagram Người dùng của Internet (UDP) là một ví dụ về giao thức vận chuyển như vậy.

Vấn đề đáng chú ý duy nhất trong giao thức như vậy là dạng địa chỉ dùng để xác định tiến trình đích. Mặc dù các tiến trình có thể *trực tiếp* xác định nhau bằng một mã định danh tiến trình (pid) do hệ điều hành gán, cách tiếp cận này chỉ thực tế trong một hệ phân tán đóng, nơi một hệ điều hành duy nhất chạy trên tất cả các máy chủ và gán cho mỗi tiến trình một mã định danh duy nhất. Cách tiếp cận phổ biến hơn, và cũng là cách UDP sử dụng, là các tiến trình *gián tiếp* xác định nhau thông qua một bộ định vị trừu tượng, thường gọi là *cổng* (port). Ý tưởng cơ bản là tiến trình nguồn gửi thông điệp đến một cổng và tiến trình đích nhận thông điệp từ một cổng.

Phần đầu của một giao thức đầu-cuối thực hiện chức năng phân kênh này thường chứa một định danh (cổng) cho cả bên gửi (nguồn) và bên nhận (đích) của thông điệp. Ví dụ, phần đầu UDP được minh họa ở :numref:`Hình %s <fig-udp-format>`. Lưu ý rằng trường cổng UDP chỉ dài 16 bit. Điều này có nghĩa là có tối đa 64K cổng khả dụng, rõ ràng là không đủ để xác định tất cả các tiến trình trên mọi máy chủ trong Internet. May mắn thay, các cổng không được diễn giải trên toàn bộ Internet, mà chỉ trên một máy chủ cụ thể. Tức là, một tiến trình thực sự được xác định bởi một cổng trên một máy chủ nào đó: một cặp (cổng, máy chủ). Cặp này tạo thành khóa phân kênh cho giao thức UDP.

Vấn đề tiếp theo là làm thế nào để một tiến trình biết được cổng của tiến trình mà nó muốn gửi thông điệp đến. Thông thường, một tiến trình khách sẽ khởi tạo trao đổi thông điệp với một tiến trình máy chủ. Khi khách đã liên hệ với máy chủ, máy chủ biết cổng của khách (từ trường ``SrcPrt`` trong phần đầu thông điệp) và có thể trả lời lại. Vấn đề thực sự, do đó, là làm thế nào để khách biết được cổng của máy chủ ngay từ đầu. Cách tiếp cận phổ biến là máy chủ nhận thông điệp tại một *cổng nổi tiếng* (well-known port). Tức là, mỗi máy chủ nhận thông điệp tại một cổng cố định được công bố rộng rãi, giống như dịch vụ điện thoại khẩn cấp ở Hoa Kỳ tại số điện thoại nổi tiếng 911. Ví dụ, trên Internet, máy chủ tên miền (DNS) nhận thông điệp tại cổng nổi tiếng 53 trên mỗi máy chủ, dịch vụ thư điện tử lắng nghe tại cổng 25, và chương trình ``talk`` của Unix nhận thông điệp tại cổng nổi tiếng 517, v.v. Bảng ánh xạ này được công bố định kỳ trong một RFC và có sẵn trên hầu hết các hệ thống Unix trong file ``/etc/services``. Đôi khi một cổng nổi tiếng chỉ là điểm khởi đầu cho giao tiếp: Khách và máy chủ sử dụng cổng nổi tiếng để thỏa thuận về một cổng khác mà họ sẽ dùng cho các trao đổi tiếp theo, nhường cổng nổi tiếng cho các khách khác.

.. _fig-udp-format:
.. figure:: figures/f05-01-9780123850591.png
   :width: 400px
   :align: center

   Định dạng phần đầu UDP.

Một chiến lược thay thế là tổng quát hóa ý tưởng này, chỉ có một cổng nổi tiếng duy nhất—nơi dịch vụ *port mapper* nhận thông điệp. Một tiến trình khách sẽ gửi thông điệp đến cổng nổi tiếng của port mapper để hỏi cổng mà nó nên dùng để liên hệ với dịch vụ “bất kỳ”, và port mapper sẽ trả về cổng phù hợp. Chiến lược này giúp dễ dàng thay đổi cổng gắn với các dịch vụ khác nhau theo thời gian và cho phép mỗi máy chủ sử dụng một cổng khác nhau cho cùng một dịch vụ.

Như vừa đề cập, cổng chỉ là một khái niệm trừu tượng. Cách hiện thực cụ thể khác nhau giữa các hệ thống, hay chính xác hơn là giữa các hệ điều hành. Ví dụ, API socket được mô tả ở Chương 1 là một ví dụ về hiện thực cổng. Thông thường, một cổng được hiện thực bằng một hàng đợi thông điệp, như minh họa ở :numref:`Hình %s <fig-udp-queue>`. Khi một thông điệp đến, giao thức (ví dụ UDP) sẽ nối thông điệp vào cuối hàng đợi. Nếu hàng đợi đầy, thông điệp sẽ bị loại bỏ. UDP không có cơ chế điều khiển luồng để báo cho bên gửi giảm tốc độ. Khi một tiến trình ứng dụng muốn nhận thông điệp, một thông điệp sẽ được lấy ra từ đầu hàng đợi. Nếu hàng đợi rỗng, tiến trình sẽ bị chặn cho đến khi có thông điệp mới.

.. _fig-udp-queue:
.. figure:: figures/f05-02-9780123850591.png
   :width: 400px
   :align: center

   Hàng đợi thông điệp UDP.

Cuối cùng, mặc dù UDP không thực hiện điều khiển luồng hay đảm bảo chuyển phát tin cậy/đúng thứ tự, nó vẫn cung cấp thêm một chức năng ngoài việc phân kênh thông điệp đến tiến trình ứng dụng—đó là đảm bảo tính toàn vẹn của thông điệp bằng cách sử dụng tổng kiểm (checksum). (Tổng kiểm UDP là tùy chọn trong IPv4 nhưng bắt buộc trong IPv6.) Thuật toán tổng kiểm UDP cơ bản giống với IP—tức là, cộng các từ 16 bit bằng toán học bù một và lấy bù một của kết quả. Tuy nhiên, dữ liệu đầu vào cho tổng kiểm lại hơi khác thường.

Tổng kiểm UDP lấy đầu vào là phần đầu UDP, nội dung thân thông điệp, và một thành phần gọi là *pseudoheader*. Pseudoheader gồm ba trường từ phần đầu IP—số hiệu giao thức, địa chỉ IP nguồn, và địa chỉ IP đích—cộng với trường độ dài UDP. (Đúng vậy, trường độ dài UDP được tính hai lần trong phép tính tổng kiểm.) Động cơ của việc có pseudoheader là để xác minh rằng thông điệp này đã được chuyển phát giữa đúng hai điểm cuối. Ví dụ, nếu địa chỉ IP đích bị thay đổi trong quá trình truyền, khiến gói tin bị chuyển nhầm, điều này sẽ được phát hiện bởi tổng kiểm UDP.
