Lời giới thiệu cho Ấn bản Thứ nhất
==================================

Thuật ngữ *spaghetti code* (mã rối) được hiểu rộng rãi như một lời
xúc phạm. Tất cả các nhà khoa học máy tính giỏi đều tôn thờ "vị thần
mô-đun", bởi vì tính mô-đun mang lại nhiều lợi ích, bao gồm lợi ích
tối thượng là không cần cùng lúc hiểu được tất cả các phần của một vấn đề
để giải quyết nó. Tính mô-đun do đó đóng vai trò trong việc trình bày ý
tưởng trong một cuốn sách, cũng như trong việc viết mã. Nếu tài liệu của
một cuốn sách được tổ chức một cách hiệu quả—mô-đun—người đọc có thể
bắt đầu từ đầu và thực sự đi đến cuối cùng.

Lĩnh vực giao thức mạng có lẽ là độc nhất vô nhị ở chỗ “tính mô-đun đúng đắn”
đã được truyền lại cho chúng ta dưới dạng một tiêu chuẩn quốc tế: mô hình
tham chiếu bảy lớp của các giao thức mạng từ ISO. Mô hình này, phản ánh
cách tiếp cận phân lớp đối với tính mô-đun, gần như luôn được sử dụng làm
điểm khởi đầu cho các cuộc thảo luận về tổ chức giao thức, dù thiết kế đó
tuân theo mô hình hay khác biệt với nó.

Có vẻ như việc tổ chức một cuốn sách về mạng máy tính xung quanh mô hình
phân lớp này là điều hiển nhiên. Tuy nhiên, có một nguy cơ khi làm như vậy,
bởi vì mô hình OSI thực sự không thành công trong việc tổ chức các khái
niệm cốt lõi của mạng máy tính. Những yêu cầu cơ bản như độ tin cậy, điều
khiển lưu lượng (flow control), hoặc bảo mật có thể được giải quyết ở hầu
hết, nếu không phải tất cả, các lớp của OSI. Thực tế này đã dẫn đến sự
nhầm lẫn lớn trong việc cố gắng hiểu mô hình tham chiếu. Đôi khi nó thậm
chí còn đòi hỏi phải tạm ngưng sự hoài nghi. Thật vậy, một cuốn sách được
tổ chức nghiêm ngặt theo mô hình phân lớp có một số đặc điểm của mã
spaghetti.

Điều này dẫn chúng ta đến cuốn sách này. Peterson và Davie tuân theo mô
hình phân lớp truyền thống, nhưng họ không giả vờ rằng mô hình này thực sự
giúp hiểu các vấn đề lớn trong mạng máy tính. Thay vào đó, các tác giả tổ
chức việc thảo luận các khái niệm cơ bản theo cách độc lập với phân lớp.
Do đó, sau khi đọc cuốn sách, người đọc sẽ hiểu về điều khiển lưu lượng,
điều khiển tắc nghẽn (congestion control), tăng cường độ tin cậy, biểu diễn
dữ liệu (data representation), và đồng bộ hóa (synchronization), và sẽ
hiểu riêng biệt các tác động của việc giải quyết những vấn đề này ở một
hoặc một số lớp truyền thống.

Đây là một cuốn sách kịp thời. Nó xem xét các giao thức quan trọng đang
được sử dụng ngày nay—đặc biệt là các giao thức Internet. Peterson và
Davie có sự tham gia lâu dài và nhiều kinh nghiệm với Internet. Do đó,
cuốn sách của họ phản ánh không chỉ các vấn đề lý thuyết trong thiết kế
giao thức, mà còn các yếu tố thực tế quan trọng trong thực tiễn. Cuốn sách
xem xét một số giao thức vừa mới xuất hiện, vì vậy người đọc có thể yên
tâm về một góc nhìn cập nhật. Nhưng quan trọng nhất, việc thảo luận các
vấn đề cơ bản được trình bày theo cách xuất phát từ bản chất cơ bản của
vấn đề, không phải từ các ràng buộc của mô hình tham chiếu phân lớp hay
các chi tiết của các giao thức ngày nay. Ở khía cạnh này, những gì cuốn
sách này trình bày vừa mang tính thời sự vừa vượt thời gian. Sự kết hợp
giữa tính thực tiễn, các ví dụ hiện đại, và giải thích cẩn thận về các
nguyên lý cơ bản khiến cuốn sách này trở nên độc đáo.



| David Clark
| Viện Công nghệ Massachusetts
| 1996
