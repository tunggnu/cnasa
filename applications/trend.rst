Góc nhìn: Đám mây là Internet mới
==========================================

Như chúng ta đã thấy ở cuối :ref:`Mục 9.1 <9.1 Traditional
Applications>`, đã có một sự chuyển dịch các ứng dụng Internet truyền
thống như email và máy chủ web từ các máy chạy tại chỗ sang các máy ảo
chạy trong các đám mây phổ thông. Điều này tương ứng với một sự thay đổi
về thuật ngữ (từ “Dịch vụ Web” sang “Dịch vụ Đám mây”) và trong nhiều
công nghệ nền tảng được sử dụng (từ Máy ảo sang micro-services Cloud
Native). Nhưng tác động của Đám mây lên cách các ứng dụng mạng được triển
khai ngày nay còn lớn hơn những gì sự chuyển dịch này gợi ý. Chính sự
kết hợp giữa các đám mây phổ thông và mạng phủ (overlay network) (tương
tự như những gì được mô tả ở :ref:`Mục 9.4 <9.4 Overlay
Networks>`) có thể cuối cùng sẽ tạo ra tác động lớn nhất.

Điều lớn nhất mà một ứng dụng dựa trên overlay cần để hoạt động hiệu quả
là một phạm vi phủ rộng, tức là, nhiều điểm hiện diện trên toàn thế giới.
Các router IP được triển khai rộng rãi, nên nếu bạn có quyền sử dụng một
tập hợp các router này làm các nút nền tảng trong mạng overlay của mình,
thì bạn đã sẵn sàng. Nhưng điều đó sẽ không xảy ra, vì không có nhà vận
hành mạng hay quản trị viên doanh nghiệp nào sẵn sàng để người lạ cài đặt
phần mềm overlay lên router của họ.

Lựa chọn tiếp theo của bạn có thể là huy động cộng đồng để cung cấp các
điểm đặt phần mềm overlay của bạn. Dựa vào lòng tốt của người lạ có thể
hiệu quả nếu tất cả cùng chia sẻ một mục tiêu chung, như tải nhạc miễn
phí, nhưng rất khó để một ứng dụng overlay mới lan truyền, và ngay cả khi
nó làm được, việc đảm bảo có đủ năng lực tại bất kỳ thời điểm nào để xử
lý toàn bộ lưu lượng mà ứng dụng của bạn tạo ra thường là vấn đề nan giải.
Đôi khi cách này hiệu quả với các dịch vụ miễn phí, nhưng không phù hợp
với bất kỳ ứng dụng nào bạn hy vọng kiếm tiền từ đó.

Giá như có một cách để trả tiền cho ai đó để được quyền cài đặt và chạy
phần mềm của bạn trên các máy chủ phân tán khắp thế giới. Tất nhiên, đó
chính là những gì các đám mây phổ thông như Amazon AWS, Microsoft Azure,
và Google Cloud Platform cung cấp. Với nhiều người, đám mây mang lại một
số lượng máy chủ dường như không giới hạn, nhưng thực ra điều quan trọng
không kém—thậm chí còn quan trọng hơn—là vị trí của các máy chủ này. Như
chúng ta đã bàn ở cuối :ref:`Chương 4 <Perspective: The Cloud is
Eating the Internet>`, chúng được phân bố rộng khắp hơn 150 địa điểm
kết nối tốt.

Giả sử, ví dụ, bạn muốn truyền phát một bộ sưu tập các kênh video hoặc
audio trực tiếp đến hàng triệu người dùng, hoặc bạn muốn hỗ trợ hàng
nghìn phiên họp hội nghị truyền hình, mỗi phiên kết nối một tá người tham
gia phân tán rộng rãi. Trong cả hai trường hợp, bạn xây dựng một cây
multicast overlay (một cây cho mỗi kênh video trong ví dụ đầu, và một cây
cho mỗi phiên hội nghị trong ví dụ thứ hai), với các nút overlay trong
cây được đặt tại một số tổ hợp các địa điểm đám mây trong số 150 địa điểm
đó. Sau đó bạn cho phép người dùng cuối, từ trình duyệt web đa năng hoặc
ứng dụng điện thoại thông minh chuyên dụng, kết nối đến cây multicast mà
họ chọn. Nếu bạn cần lưu trữ một số nội dung video/audio để phát lại sau
(như để hỗ trợ tua lại thời gian) thì bạn cũng có thể mua thêm dung lượng
lưu trữ tại một số hoặc tất cả các địa điểm đám mây đó, về cơ bản là tự
xây dựng một Mạng Phân phối Nội dung (CDN) của riêng mình.

Nhìn về lâu dài, trong khi Internet ban đầu được hình dung như một dịch
vụ truyền thông thuần túy, với các ứng dụng tính toán và lưu trữ tùy ý
được phát triển ở rìa mạng, thì ngày nay phần mềm ứng dụng trên thực tế
đã được nhúng vào (phân tán trên) mạng, và ngày càng khó xác định ranh
giới giữa Internet và Đám mây. Sự hòa trộn này sẽ chỉ ngày càng sâu sắc
khi đám mây tiến gần hơn đến rìa mạng (ví dụ, đến hàng nghìn địa điểm nơi
các mạng truy nhập được neo giữ) và hiệu quả kinh tế quy mô thúc đẩy các
thiết bị phần cứng dùng để xây dựng các site Internet/Cloud ngày càng trở
nên đồng nhất.

.. admonition:: Góc nhìn rộng hơn

   Để nhắc nhở bản thân về lý do tại sao quá trình "cloud hóa" Internet
   lại quan trọng, hãy xem :ref:`Perspective: Feature Velocity`.
