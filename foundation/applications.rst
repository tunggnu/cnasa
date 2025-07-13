1.1 Ứng dụng
============

Hầu hết mọi người biết đến Internet thông qua các ứng dụng của nó: World Wide Web, email, mạng xã hội, nghe nhạc hoặc xem phim trực tuyến, hội nghị truyền hình, nhắn tin tức thời, chia sẻ tệp, chỉ kể một vài ví dụ. Nói cách khác, chúng ta tương tác với Internet với tư cách là *người dùng* của mạng. Người dùng Internet là nhóm người lớn nhất tương tác với Internet theo một cách nào đó, nhưng còn có một số nhóm quan trọng khác.

Có một nhóm người *tạo ra* các ứng dụng—một nhóm đã mở rộng rất nhiều trong những năm gần đây khi các nền tảng lập trình mạnh mẽ và các thiết bị mới như điện thoại thông minh đã tạo ra nhiều cơ hội phát triển ứng dụng nhanh chóng và đưa chúng đến với thị trường lớn.

Sau đó là những người *vận hành* hoặc *quản lý* mạng—chủ yếu là một công việc phía sau hậu trường, nhưng lại rất quan trọng và thường rất phức tạp. Với sự phổ biến của mạng gia đình, ngày càng nhiều người cũng trở thành, dù chỉ ở mức độ nhỏ, các nhà vận hành mạng.

Cuối cùng, có những người *thiết kế* và *xây dựng* các thiết bị và giao thức hợp thành Internet. Nhóm cuối cùng này là đối tượng truyền thống của các giáo trình về mạng như cuốn sách này và sẽ tiếp tục là trọng tâm chính của chúng ta. Tuy nhiên, xuyên suốt cuốn sách này, chúng ta cũng sẽ xem xét góc nhìn của các nhà phát triển ứng dụng và nhà vận hành mạng.

Xem xét các góc nhìn này sẽ giúp chúng ta hiểu rõ hơn về các yêu cầu đa dạng mà một mạng phải đáp ứng. Các nhà phát triển ứng dụng cũng sẽ có thể tạo ra các ứng dụng hoạt động tốt hơn nếu họ hiểu cách công nghệ nền tảng hoạt động và tương tác với các ứng dụng. Vì vậy, trước khi chúng ta bắt đầu tìm hiểu cách xây dựng một mạng, hãy xem xét kỹ hơn các loại ứng dụng mà các mạng ngày nay hỗ trợ.

1.1.1 Các loại ứng dụng
-----------------------

World Wide Web là ứng dụng Internet đã đưa Internet từ một công cụ khá mờ nhạt, chủ yếu được các nhà khoa học và kỹ sư sử dụng, trở thành hiện tượng phổ biến như ngày nay. Bản thân Web đã trở thành một nền tảng mạnh mẽ đến mức nhiều người nhầm lẫn nó với Internet, và thật khó để nói rằng Web chỉ là một ứng dụng đơn lẻ.

Ở dạng cơ bản, Web cung cấp một giao diện trực quan đơn giản. Người dùng xem các trang chứa đầy các đối tượng văn bản và đồ họa và nhấp vào các đối tượng mà họ muốn tìm hiểu thêm, và một trang mới tương ứng sẽ xuất hiện. Hầu hết mọi người cũng biết rằng ngay bên dưới bề mặt, mỗi đối tượng có thể chọn trên một trang đều được liên kết với một định danh cho trang hoặc đối tượng tiếp theo sẽ được xem. Định danh này, gọi là Uniform Resource Locator (URL), cung cấp một cách để xác định tất cả các đối tượng có thể được xem từ trình duyệt web của bạn. Ví dụ,

.. code-block:: html

   http://www.cs.princeton.edu/~llp/index.html

là URL cho một trang cung cấp thông tin về một trong các tác giả của cuốn sách này: chuỗi ``http`` chỉ ra rằng Giao thức Truyền tải Siêu văn bản (HTTP) sẽ được sử dụng để tải trang, ``www.cs.princeton.edu`` là tên của máy chủ cung cấp trang, và ``/~llp/index.html`` xác định duy nhất trang chủ của Larry tại trang web này.

Tuy nhiên, hầu hết người dùng web không biết rằng chỉ với một lần nhấp vào một URL như vậy, có thể hơn một tá thông điệp sẽ được trao đổi qua Internet, và còn nhiều hơn thế nếu trang web phức tạp với nhiều đối tượng nhúng. Việc trao đổi thông điệp này bao gồm tối đa sáu thông điệp để chuyển đổi tên máy chủ (``www.cs.princeton.edu``) thành địa chỉ Giao thức Internet (IP) (``128.112.136.35``), ba thông điệp để thiết lập một kết nối Giao thức Điều khiển Truyền tải (TCP) giữa trình duyệt của bạn và máy chủ này, bốn thông điệp để trình duyệt của bạn gửi yêu cầu HTTP “GET” và máy chủ phản hồi với trang được yêu cầu (và mỗi bên xác nhận đã nhận được thông điệp đó), và bốn thông điệp để đóng kết nối TCP. Tất nhiên, điều này chưa bao gồm hàng triệu thông điệp được các nút Internet trao đổi suốt cả ngày, chỉ để cho nhau biết rằng chúng tồn tại và sẵn sàng phục vụ các trang web, chuyển đổi tên thành địa chỉ, và chuyển tiếp thông điệp đến đích cuối cùng của chúng.

Một loại ứng dụng phổ biến khác của Internet là truyền tải âm thanh và video “trực tuyến”. Các dịch vụ như video theo yêu cầu và radio Internet sử dụng công nghệ này. Mặc dù chúng ta thường bắt đầu tại một trang web để khởi tạo một phiên phát trực tuyến, việc truyền tải âm thanh và video có một số điểm khác biệt quan trọng so với việc lấy một trang web đơn giản gồm văn bản và hình ảnh. Ví dụ, bạn thường không muốn tải xuống toàn bộ tệp video—một quá trình có thể mất vài phút—trước khi xem cảnh đầu tiên. Phát trực tuyến âm thanh và video ngụ ý việc truyền thông điệp từ người gửi đến người nhận kịp thời hơn, và người nhận sẽ hiển thị video hoặc phát âm thanh gần như ngay khi nhận được.

Lưu ý rằng sự khác biệt giữa các ứng dụng phát trực tuyến và việc truyền tải truyền thống văn bản, đồ họa và hình ảnh là con người tiêu thụ các luồng âm thanh và video một cách liên tục, và sự gián đoạn—dưới dạng âm thanh bị bỏ qua hoặc video bị dừng—là không thể chấp nhận được. Ngược lại, một trang thông thường (không phát trực tuyến) có thể được truyền tải và đọc từng phần. Sự khác biệt này ảnh hưởng đến cách mạng hỗ trợ các loại ứng dụng khác nhau này.

Một loại ứng dụng hơi khác là âm thanh và video *thời gian thực*. Các ứng dụng này có các ràng buộc về thời gian nghiêm ngặt hơn nhiều so với các ứng dụng phát trực tuyến. Khi sử dụng một ứng dụng thoại qua IP như Skype hoặc một ứng dụng hội nghị truyền hình, sự tương tác giữa các thành viên phải kịp thời. Khi một người ở đầu này ra hiệu, thì hành động đó phải được hiển thị ở đầu kia càng nhanh càng tốt.\ [#]_

.. [#] Không hẳn là “càng sớm càng tốt”… Nghiên cứu về yếu tố con người chỉ ra rằng 300 ms là giới hạn trên hợp lý cho độ trễ vòng lặp có thể chấp nhận được trong một cuộc gọi điện thoại trước khi con người phàn nàn, và độ trễ 100 ms thì nghe rất tốt.

Khi một người cố gắng ngắt lời người khác, người bị ngắt cần nghe điều đó càng sớm càng tốt và quyết định có cho phép ngắt lời hay tiếp tục nói đè lên người ngắt. Quá nhiều độ trễ trong môi trường như vậy sẽ khiến hệ thống không thể sử dụng được. So sánh với video theo yêu cầu, nếu mất vài giây từ khi người dùng bắt đầu video đến khi hình ảnh đầu tiên hiển thị, dịch vụ vẫn được coi là chấp nhận được. Ngoài ra, các ứng dụng tương tác thường bao gồm luồng âm thanh và/hoặc video theo cả hai hướng, trong khi một ứng dụng phát trực tuyến thường chỉ gửi video hoặc âm thanh theo một hướng.

.. _fig-vic:
.. figure:: figures/f01-01-9780123850591.png
   :width: 600px
   :align: center

   Một ứng dụng đa phương tiện bao gồm hội nghị truyền hình.

Các công cụ hội nghị truyền hình chạy trên Internet đã xuất hiện từ đầu những năm 1990 nhưng chỉ đạt được sự phổ biến rộng rãi trong vài năm gần đây, với một số sản phẩm thương mại trên thị trường. Một ví dụ về một hệ thống như vậy được minh họa trong :numref:`Hình %s <fig-vic>`. Cũng giống như việc tải một trang web có nhiều điều phức tạp hơn những gì bạn thấy, các ứng dụng video cũng vậy. Việc nén nội dung video để phù hợp với một mạng có băng thông tương đối thấp, ví dụ, hoặc đảm bảo rằng video và âm thanh luôn đồng bộ và đến kịp thời để mang lại trải nghiệm người dùng tốt là tất cả những vấn đề mà các nhà thiết kế mạng và giao thức phải quan tâm. Chúng ta sẽ xem xét những vấn đề này và nhiều vấn đề khác liên quan đến ứng dụng đa phương tiện ở các phần sau của cuốn sách.

Mặc dù chỉ là hai ví dụ, việc tải các trang web và tham gia hội nghị truyền hình cho thấy sự đa dạng của các ứng dụng có thể được xây dựng trên nền Internet và gợi ý về sự phức tạp trong thiết kế của Internet. Ở các phần sau của cuốn sách, chúng ta sẽ phát triển một phân loại đầy đủ hơn về các loại ứng dụng để giúp định hướng cho thảo luận về các quyết định thiết kế then chốt khi chúng ta tìm cách xây dựng, vận hành và sử dụng mạng. Cuốn sách kết thúc bằng việc xem lại hai ứng dụng cụ thể này, cũng như một số ứng dụng khác minh họa cho sự đa dạng của những gì có thể thực hiện trên Internet ngày nay.

Hiện tại, cái nhìn nhanh về một vài ứng dụng tiêu biểu này là đủ để chúng ta bắt đầu xem xét các vấn đề cần giải quyết nếu muốn xây dựng một mạng hỗ trợ sự đa dạng ứng dụng như vậy.
