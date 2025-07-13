Lời giới thiệu
==============

Bạn đọc: trước khi bắt đầu cuốn sách, hãy dành một chút thời gian và đặt
cỗ máy thời gian của bạn về năm 1996. Đó là khi ấn bản đầu tiên của cuốn
sách này được xuất bản. Bạn có nhớ năm 1996 không? Khi đó bạn đã ra đời
chưa? Mọi người thường quên rằng nền tảng của Internet đã được đặt ra từ
rất lâu rồi.

Năm 1996, NSFNET vừa mới bị ngừng hoạt động, và giai đoạn thương mại hóa
của Internet chỉ mới bắt đầu. Công cụ tìm kiếm đầu tiên (Alta Vista—bạn có
nhớ không?) vừa mới được trình diễn. Mạng phân phối nội dung (content
delivery network) chưa tồn tại—Akamai được thành lập hai năm sau đó vào
năm 1998, cùng năm Google chính thức ra đời. Cloud khi đó chỉ là một khái
niệm mờ ảo ở chân trời. Và chưa có khái niệm gì về băng thông rộng dân
dụng (residential broadband) hay mạng không dây cho người dùng cuối. Chúng
ta dùng modem quay số—modem 56K vừa mới được phát minh. Trước đó đã có
packet radio, nhưng chúng còn chậm hơn cả quay số và to bằng một chiếc tủ
lạnh đựng bia. Bạn cần một chiếc xe tải hoặc ít nhất là một chiếc Jeep để
có thể di động.

Và vào khoảng năm 1995, Larry và Bruce quyết định viết cuốn sách này. Có
thể, từ góc nhìn ngày nay, bạn sẽ thấy khó nhớ được một cuốn sách như thế
này quan trọng thế nào vào năm 1996. Nó đã ghi lại rất nhiều kiến thức
ngầm và làm cho bất kỳ ai muốn đọc đều có thể tiếp cận. Và thay vì chỉ kể
lại một loạt mô tả về các giao thức, nó dạy cách các phần kết nối với
nhau. Nó dạy cách Internet hoạt động, không chỉ đơn giản là các thành phần
là gì.

Một cách để suy nghĩ về sự phát triển của Internet là qua lăng kính của
người thiết kế ứng dụng. Suy cho cùng, mục đích của Internet như một hệ
thống truyền tải gói tin là để hỗ trợ các ứng dụng. Chỉ có dân kỹ thuật và
những người đam mê hiệu năng mới gửi gói tin chỉ để cho vui. Năm 1996, nếu
bạn muốn xây dựng một ứng dụng, hệ sinh thái khi đó chỉ có dịch vụ truyền
tải gói tin IP, TCP để làm mượt các mất mát ở tầng Internet, DNS, và chỉ
có vậy. Bất cứ thứ gì khác mà người thiết kế ứng dụng cần đều phải tự xây
dựng từ đầu.

Bây giờ, một người thiết kế ứng dụng có rất nhiều tài nguyên để xây dựng:
cloud và các mạng cloud, các mạng toàn cầu khác có thể kết nối các dịch vụ
với nhau, CDN, môi trường phát triển ứng dụng, v.v. Một số thứ này có vẻ
rất khác so với những gì chúng ta có năm 1996 và thực tế thì đúng là như
vậy. Hãy xét về cloud. (Tôi không thích cách đặt tên này—với tôi “cloud”
gợi lên điều gì đó mềm mại và bồng bềnh, nhưng nếu bạn từng thấy một trung
tâm dữ liệu to bằng sân bóng đá tiêu thụ hàng megawatt điện, bạn sẽ không
nghĩ nó mềm mại đâu. Nhưng thôi bỏ qua…) Các trung tâm dữ liệu đã trở nên
rất tinh vi về chi phí, hiệu quả năng lượng, hiệu năng và khả năng chịu
lỗi. Có rất nhiều điều để học về cách xây dựng một trung tâm dữ liệu hiện
đại. Nhưng các nguyên lý cơ bản vẫn giống nhau: chuyển tiếp gói tin
(packet forwarding), chia sẻ dung lượng theo thống kê, giao thức truyền
tải (transport protocol), giao thức định tuyến (routing protocol), theo
đuổi tính tổng quát và tiện ích rộng rãi, v.v.

Nhìn về phía trước, các công nghệ như cloud rõ ràng là trung tâm và ấn bản
này dành nhiều sự chú ý cho cloud. Các yêu cầu như tăng cường bảo mật là
rất quan trọng, và cuốn sách bàn thêm về các vấn đề liên quan đến bảo mật:
niềm tin (trust), nhận dạng (identity), và chủ đề nóng nhất hiện nay—
blockchain. Tuy nhiên, nếu bạn nhìn lại ấn bản đầu tiên, nhiều khái niệm
nền tảng vẫn giống nhau. Nhưng ấn bản này là phiên bản hiện đại của câu
chuyện, với các ví dụ cập nhật và công nghệ hiện đại. Chúc bạn đọc vui
vẻ.


| David Clark
| MIT
| Tháng 10 năm 2020
