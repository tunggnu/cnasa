Vấn đề: Ứng dụng cần giao thức riêng
------------------------------------

Chúng ta đã bắt đầu cuốn sách này bằng cách nói về các chương trình ứng
dụng—mọi thứ từ trình duyệt web đến các công cụ hội nghị truyền hình—mà
người dùng muốn chạy trên các mạng máy tính. Trong các chương tiếp theo,
chúng ta đã phát triển, từng khối xây dựng một, hạ tầng mạng cần thiết để
làm cho các ứng dụng như vậy trở nên khả thi. Bây giờ chúng ta đã đi một
vòng tròn, quay trở lại các ứng dụng mạng. Các ứng dụng này vừa là
giao thức mạng (theo nghĩa là chúng trao đổi thông điệp với các peer trên
các máy khác)
vừa là chương trình ứng dụng truyền thống (theo nghĩa là
chúng tương tác với hệ thống cửa sổ, hệ thống file, và cuối cùng là
người dùng). Chương này khám phá một số ứng dụng mạng phổ biến
hiện nay.

Việc xem xét các ứng dụng làm nổi bật *cách tiếp cận hệ thống* mà chúng ta đã
nhấn mạnh xuyên suốt cuốn sách này. Tức là, cách tốt nhất để xây dựng
các ứng dụng mạng hiệu quả là hiểu các khối xây dựng
mà một mạng có thể cung cấp và cách các khối đó có thể tương tác với nhau.
Ví dụ, một ứng dụng mạng cụ thể có thể cần
sử dụng một giao thức vận chuyển tin cậy, các cơ chế xác thực và bảo mật,
và các khả năng phân bổ tài nguyên của mạng nền tảng bên dưới.
Các ứng dụng thường hoạt động tốt nhất khi lập trình viên ứng dụng
biết cách tận dụng tốt nhất các tiện ích này (và cũng có rất
nhiều ví dụ ngược lại về các ứng dụng sử dụng kém các khả năng mạng sẵn có).
Các ứng dụng thường cũng cần các giao thức riêng của mình,
trong nhiều trường hợp sử dụng cùng các nguyên lý mà chúng ta đã
thấy trong việc khảo sát các giao thức tầng thấp hơn trước đó. Do đó, trọng tâm
của chương này là cách kết hợp các ý tưởng và kỹ thuật
đã mô tả để xây dựng các ứng dụng mạng hiệu quả. Nói
cách khác, nếu bạn từng tưởng tượng mình sẽ viết một ứng dụng mạng,
thì theo định nghĩa bạn cũng sẽ trở thành một người thiết kế giao thức (và
người hiện thực).

Chúng ta tiếp tục bằng cách xem xét nhiều ứng dụng mạng quen thuộc,
và cả không quen thuộc.
Chúng bao gồm từ trao đổi email và duyệt Web,
đến tích hợp các ứng dụng giữa các doanh nghiệp, đến các ứng dụng đa
phương tiện
như hội nghị truyền hình, đến quản lý một tập hợp các thành phần mạng,
đến các mạng ngang hàng và mạng phân phối nội dung mới nổi.
Danh sách này chắc chắn không đầy đủ, nhưng nó giúp minh họa
nhiều nguyên lý then chốt trong thiết kế và xây dựng ứng dụng.
Các ứng dụng cần lựa chọn các khối xây dựng phù hợp
có sẵn ở các tầng khác, hoặc bên trong mạng hoặc trong
các ngăn xếp giao thức trên host,
và sau đó bổ sung các dịch vụ nền tảng đó để
cung cấp dịch vụ truyền thông chính xác mà ứng dụng yêu cầu.
