Vấn đề: Làm thế nào để các tiến trình giao tiếp với nhau
--------------------------------------------------------

Nhiều công nghệ có thể được sử dụng để kết nối một tập hợp các máy tính lại với nhau, từ các mạng Ethernet đơn giản và mạng không dây cho đến các liên mạng quy mô toàn cầu. Khi đã được kết nối, vấn đề tiếp theo là biến dịch vụ chuyển gói tin từ máy này sang máy khác thành một kênh giao tiếp giữa các tiến trình. Đây là vai trò của tầng *vận chuyển* trong kiến trúc mạng, tầng này, bởi vì nó hỗ trợ giao tiếp giữa các chương trình ứng dụng chạy trên các nút đầu cuối, đôi khi còn được gọi là giao thức *end-to-end* (đầu-cuối).

Có hai lực lượng định hình giao thức end-to-end. Từ phía trên, các tiến trình ở tầng ứng dụng sử dụng dịch vụ của nó có những yêu cầu nhất định. Danh sách sau đây liệt kê một số thuộc tính phổ biến mà một giao thức vận chuyển có thể được kỳ vọng cung cấp:

-  Đảm bảo chuyển phát thông điệp

-  Chuyển phát thông điệp theo đúng thứ tự gửi đi

-  Đảm bảo mỗi thông điệp chỉ được chuyển phát tối đa một lần

-  Hỗ trợ các thông điệp có kích thước tùy ý lớn

-  Hỗ trợ đồng bộ hóa giữa bên gửi và bên nhận

-  Cho phép bên nhận áp dụng điều khiển luồng đối với bên gửi

-  Hỗ trợ nhiều tiến trình ứng dụng trên mỗi máy chủ

Lưu ý rằng danh sách này không bao gồm tất cả các chức năng mà các tiến trình ứng dụng có thể mong muốn từ mạng. Ví dụ, nó không bao gồm các tính năng bảo mật như xác thực hay mã hóa, những thứ thường được cung cấp bởi các giao thức nằm trên tầng vận chuyển. (Chúng tôi sẽ bàn về các chủ đề liên quan đến bảo mật ở chương sau.)

Từ phía dưới, mạng nền tảng mà giao thức vận chuyển hoạt động trên đó có những giới hạn nhất định về mức độ dịch vụ mà nó có thể cung cấp. Một số giới hạn điển hình của mạng bao gồm:

-  Làm mất thông điệp

-  Chuyển phát thông điệp sai thứ tự

-  Chuyển phát nhiều bản sao của cùng một thông điệp

-  Giới hạn kích thước thông điệp ở một mức hữu hạn nào đó

-  Chuyển phát thông điệp sau một khoảng trễ tùy ý dài

Một mạng như vậy được gọi là cung cấp dịch vụ ở mức *nỗ lực tối đa* (*best-effort*), điển hình như Internet.

Thách thức đặt ra là phải phát triển các thuật toán biến các đặc tính chưa hoàn hảo của mạng nền tảng thành mức dịch vụ cao mà các chương trình ứng dụng yêu cầu. Các giao thức vận chuyển khác nhau sử dụng các tổ hợp thuật toán khác nhau để đạt được điều này. Chương này sẽ xem xét các thuật toán đó trong bối cảnh của bốn loại dịch vụ tiêu biểu—một dịch vụ phân kênh bất đồng bộ đơn giản, một dịch vụ truyền dòng byte tin cậy, một dịch vụ yêu cầu/đáp ứng, và một dịch vụ dành cho các ứng dụng thời gian thực.

Trong trường hợp dịch vụ phân kênh và dịch vụ truyền dòng byte, chúng tôi sử dụng Giao thức Datagram Người dùng của Internet (UDP) và Giao thức Điều khiển Truyền vận (TCP) tương ứng để minh họa cách các dịch vụ này được cung cấp trong thực tế. Đối với dịch vụ yêu cầu/đáp ứng, chúng tôi sẽ bàn về vai trò của nó trong dịch vụ Gọi Thủ tục Từ xa (RPC) và những tính năng mà nó đòi hỏi. Internet không có một giao thức RPC duy nhất, vì vậy chúng tôi sẽ kết thúc phần này bằng mô tả ba giao thức RPC được sử dụng rộng rãi: SunRPC, DCE-RPC và gRPC.

Cuối cùng, các ứng dụng thời gian thực đặt ra những yêu cầu đặc biệt đối với giao thức vận chuyển, chẳng hạn như cần mang thông tin thời gian cho phép các mẫu âm thanh hoặc video được phát lại đúng thời điểm. Chúng tôi sẽ xem xét các yêu cầu mà ứng dụng đặt ra cho loại giao thức này và ví dụ phổ biến nhất, Giao thức Vận chuyển Thời gian Thực (RTP).
