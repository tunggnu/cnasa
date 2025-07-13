5.3 Gọi Thủ Tục Từ Xa (RPC)
===========================

Một mô hình giao tiếp phổ biến được các chương trình ứng dụng sử dụng khi được cấu trúc theo cặp *client/server* là giao dịch thông điệp yêu cầu/phản hồi: Một client gửi một thông điệp yêu cầu đến server, và server phản hồi bằng một thông điệp trả lời, với client bị chặn (tạm dừng thực thi) để chờ phản hồi. :numref:`Hình %s <fig-rpc-timeline>` minh họa tương tác cơ bản giữa client và server trong một trao đổi như vậy.

.. _fig-rpc-timeline:
.. figure:: figures/f05-13-9780123850591.png
   :width: 300px
   :align: center

   Dòng thời gian cho RPC.

Một giao thức vận chuyển hỗ trợ mô hình yêu cầu/phản hồi phức tạp hơn nhiều so với một thông điệp UDP đi một chiều rồi một thông điệp UDP đi chiều ngược lại. Nó cần xử lý việc xác định đúng tiến trình trên các máy chủ từ xa và liên kết các yêu cầu với các phản hồi. Nó cũng có thể cần vượt qua một số hoặc tất cả các hạn chế của mạng nền được nêu ra ở phần đầu chương này. Trong khi TCP vượt qua các hạn chế này bằng cách cung cấp dịch vụ dòng byte tin cậy, nó cũng không hoàn toàn phù hợp với mô hình yêu cầu/phản hồi. Phần này mô tả một loại giao thức vận chuyển thứ ba, gọi là *Gọi Thủ Tục Từ Xa* (RPC), phù hợp hơn với nhu cầu của các ứng dụng trao đổi thông điệp yêu cầu/phản hồi.

5.3.1 Những điều cơ bản về RPC
------------------------------

RPC về mặt kỹ thuật không phải là một giao thức—nó nên được xem như một cơ chế tổng quát để cấu trúc các hệ thống phân tán. RPC phổ biến vì nó dựa trên ngữ nghĩa của lời gọi thủ tục cục bộ—chương trình ứng dụng gọi một thủ tục mà không quan tâm nó là cục bộ hay từ xa và bị chặn cho đến khi lời gọi trả về. Lập trình viên ứng dụng phần lớn không cần biết thủ tục đó là cục bộ hay từ xa, giúp đơn giản hóa công việc của họ rất nhiều. Khi các thủ tục được gọi thực chất là các phương thức của đối tượng từ xa trong ngôn ngữ hướng đối tượng, RPC được gọi là *gọi phương thức từ xa* (RMI). Mặc dù khái niệm RPC đơn giản, có hai vấn đề chính khiến nó phức tạp hơn so với lời gọi thủ tục cục bộ:

-  Mạng giữa tiến trình gọi và tiến trình được gọi có các thuộc tính phức tạp hơn nhiều so với bus nội bộ của máy tính. Ví dụ, nó có thể giới hạn kích thước thông điệp và có xu hướng làm mất hoặc đảo thứ tự thông điệp.

-  Các máy tính mà tiến trình gọi và tiến trình được gọi chạy trên đó có thể có kiến trúc và định dạng dữ liệu rất khác nhau.

Do đó, một cơ chế RPC hoàn chỉnh thực sự bao gồm hai thành phần chính:

1. Một giao thức quản lý các thông điệp được gửi giữa các tiến trình client và server, đồng thời xử lý các thuộc tính không mong muốn của mạng nền.

2. Hỗ trợ ngôn ngữ lập trình và trình biên dịch để đóng gói các tham số thành một thông điệp yêu cầu trên máy client và sau đó chuyển đổi thông điệp này thành các tham số trên máy server, cũng như với giá trị trả về (phần này của cơ chế RPC thường được gọi là *trình biên dịch stub*).

:numref:`Hình %s <fig-rpc-stub>` mô tả sơ đồ những gì xảy ra khi một client gọi một thủ tục từ xa. Đầu tiên, client gọi một stub cục bộ cho thủ tục, truyền vào các tham số cần thiết. Stub này ẩn đi việc thủ tục là từ xa bằng cách chuyển đổi các tham số thành một thông điệp yêu cầu và sau đó gọi một giao thức RPC để gửi thông điệp yêu cầu đến máy chủ. Ở phía server, giao thức RPC chuyển thông điệp yêu cầu đến stub server, stub này chuyển đổi nó thành các tham số cho thủ tục rồi gọi thủ tục cục bộ. Sau khi thủ tục server hoàn thành, nó trả về trong một thông điệp phản hồi, thông điệp này được giao cho giao thức RPC để truyền ngược lại client. Giao thức RPC ở client chuyển thông điệp này lên stub client, stub này chuyển nó thành giá trị trả về và trả về cho chương trình client.

.. _fig-rpc-stub:
.. figure:: figures/f05-14-9780123850591.png
   :width: 500px
   :align: center

   Cơ chế RPC hoàn chỉnh.

Phần này chỉ xem xét các khía cạnh liên quan đến giao thức của một cơ chế RPC. Tức là, nó bỏ qua các stub và tập trung vào giao thức RPC, đôi khi còn gọi là giao thức yêu cầu/phản hồi, truyền thông điệp giữa client và server. Việc chuyển đổi tham số thành thông điệp và ngược lại được trình bày ở nơi khác. Cũng cần lưu ý rằng các chương trình client và server được viết bằng một ngôn ngữ lập trình nào đó, nghĩa là một cơ chế RPC có thể hỗ trợ stub Python, stub Java, stub GoLang, v.v., mỗi loại có các đặc trưng riêng về cách gọi thủ tục.

Thuật ngữ *RPC* đề cập đến một loại giao thức chứ không phải một tiêu chuẩn cụ thể như TCP, vì vậy các giao thức RPC cụ thể khác nhau về chức năng mà chúng thực hiện. Và, không giống như TCP là giao thức dòng byte tin cậy thống trị, không có một giao thức RPC nào chiếm ưu thế tuyệt đối. Do đó, trong phần này chúng ta sẽ nói nhiều hơn về các lựa chọn thiết kế thay thế.

Định danh trong RPC
~~~~~~~~~~~~~~~~~~~

Hai chức năng mà bất kỳ giao thức RPC nào cũng phải thực hiện là:

-  Cung cấp không gian tên để định danh duy nhất thủ tục cần gọi.

-  Ghép mỗi thông điệp phản hồi với thông điệp yêu cầu tương ứng.

Vấn đề đầu tiên có một số điểm tương đồng với việc định danh các nút trong mạng (ví dụ như địa chỉ IP). Một trong những lựa chọn thiết kế khi định danh là không gian tên phẳng hay phân cấp. Một không gian tên phẳng chỉ đơn giản gán một định danh duy nhất, không có cấu trúc (ví dụ, một số nguyên) cho mỗi thủ tục, và số này sẽ được mang trong một trường của thông điệp yêu cầu RPC. Điều này đòi hỏi một cơ chế phối hợp trung tâm để tránh gán cùng số thủ tục cho hai thủ tục khác nhau. Ngoài ra, giao thức có thể triển khai không gian tên phân cấp, tương tự như đường dẫn file, chỉ yêu cầu “basename” của file là duy nhất trong thư mục của nó. Cách tiếp cận này có thể đơn giản hóa việc đảm bảo tính duy nhất của tên thủ tục. Một không gian tên phân cấp cho RPC có thể được triển khai bằng cách định nghĩa một tập trường trong định dạng thông điệp yêu cầu, mỗi trường cho một cấp tên trong không gian tên hai hoặc ba cấp.

Chìa khóa để ghép một thông điệp phản hồi với thông điệp yêu cầu tương ứng là định danh duy nhất cặp yêu cầu-phản hồi bằng một trường ID thông điệp. Một thông điệp phản hồi sẽ có trường ID thông điệp giống với thông điệp yêu cầu. Khi module RPC client nhận được phản hồi, nó dùng ID thông điệp để tìm kiếm yêu cầu đang chờ tương ứng. Để giao dịch RPC trông giống như một lời gọi thủ tục cục bộ với bên gọi, bên gọi sẽ bị chặn cho đến khi nhận được thông điệp phản hồi. Khi phản hồi đến, bên gọi bị chặn được xác định dựa trên số yêu cầu trong phản hồi, giá trị trả về của thủ tục từ xa được lấy từ phản hồi, và bên gọi được bỏ chặn để trả về với giá trị đó.

Một trong những thách thức lặp lại trong RPC là xử lý các phản hồi không mong đợi, và điều này thể hiện ở các ID thông điệp. Ví dụ, hãy xem xét tình huống (dù hiếm nhưng thực tế): Một máy client gửi một thông điệp yêu cầu với ID thông điệp là 0, sau đó bị treo và khởi động lại, rồi gửi một thông điệp yêu cầu không liên quan, cũng với ID thông điệp là 0. Server có thể không biết client đã bị treo và khởi động lại, và khi thấy một thông điệp yêu cầu với ID 0, nó xác nhận và loại bỏ nó như là bản sao. Client sẽ không bao giờ nhận được phản hồi cho yêu cầu đó.

Một cách để loại bỏ vấn đề này là sử dụng *boot ID*. Boot ID của một máy là một số được tăng lên mỗi lần máy khởi động lại; số này được đọc từ bộ nhớ không mất dữ liệu (ví dụ, đĩa hoặc flash), tăng lên, và ghi lại vào thiết bị lưu trữ trong quá trình khởi động máy. Số này sau đó được đưa vào mọi thông điệp gửi đi từ máy đó. Nếu một thông điệp được nhận với ID thông điệp cũ nhưng boot ID mới, nó được nhận diện là một thông điệp mới. Thực chất, ID thông điệp và boot ID kết hợp lại tạo thành một ID duy nhất cho mỗi giao dịch.

Vượt qua các hạn chế của mạng
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Các giao thức RPC thường thực hiện thêm các chức năng để xử lý thực tế rằng mạng không phải là kênh hoàn hảo. Hai chức năng như vậy là:

-  Đảm bảo chuyển phát thông điệp tin cậy

-  Hỗ trợ kích thước thông điệp lớn thông qua phân mảnh và lắp ráp lại

Một giao thức RPC có thể “định nghĩa lại vấn đề này” bằng cách chọn chạy trên một giao thức tin cậy như TCP, nhưng trong nhiều trường hợp, giao thức RPC tự triển khai lớp chuyển phát tin cậy trên một nền không tin cậy (ví dụ, UDP/IP). Một giao thức RPC như vậy có thể triển khai tin cậy bằng cách sử dụng xác nhận và timeout, tương tự như TCP.

Thuật toán cơ bản khá đơn giản, như minh họa bởi dòng thời gian trong :numref:`Hình %s <fig-chan-timeline1>`. Client gửi một thông điệp yêu cầu và server xác nhận nó. Sau đó, sau khi thực hiện thủ tục, server gửi một thông điệp phản hồi và client xác nhận phản hồi đó.

.. _fig-chan-timeline1:
.. figure:: figures/f05-15-9780123850591.png
   :width: 200px
   :align: center

   Dòng thời gian đơn giản cho một giao thức RPC tin cậy.

Bất kỳ thông điệp nào mang dữ liệu (yêu cầu hoặc phản hồi) hoặc ACK xác nhận thông điệp đó đều có thể bị mất trên mạng. Để xử lý khả năng này, cả client và server đều lưu một bản sao của mỗi thông điệp mà họ gửi cho đến khi nhận được ACK cho nó. Mỗi bên cũng đặt một bộ định thời RETRANSMIT và gửi lại thông điệp nếu bộ định thời này hết hạn. Cả hai bên đặt lại bộ định thời này và thử lại một số lần nhất định trước khi từ bỏ và giải phóng thông điệp.

Nếu một client RPC nhận được một thông điệp phản hồi, rõ ràng thông điệp yêu cầu tương ứng đã được server nhận. Do đó, thông điệp phản hồi tự nó là một *xác nhận ngầm*, và bất kỳ xác nhận bổ sung nào từ server là không cần thiết về mặt logic. Tương tự, một thông điệp yêu cầu có thể xác nhận ngầm thông điệp phản hồi trước đó—giả sử giao thức thực hiện các giao dịch yêu cầu-phản hồi tuần tự, nghĩa là một giao dịch phải hoàn thành trước khi giao dịch tiếp theo bắt đầu. Đáng tiếc, tính tuần tự này sẽ hạn chế nghiêm trọng hiệu suất RPC.

Một cách giải quyết là giao thức RPC triển khai một trừu tượng *kênh* (channel). Trong một kênh nhất định, các giao dịch yêu cầu/phản hồi là tuần tự—chỉ có thể có một giao dịch hoạt động trên một kênh tại một thời điểm—nhưng có thể có nhiều kênh. Nói cách khác, trừu tượng kênh cho phép *phân kênh* nhiều giao dịch yêu cầu/phản hồi RPC giữa một cặp client/server.

Mỗi thông điệp bao gồm một trường ID kênh để chỉ ra thông điệp thuộc về kênh nào. Một thông điệp yêu cầu trong một kênh nhất định sẽ xác nhận ngầm thông điệp phản hồi trước đó trong kênh đó, nếu nó chưa được xác nhận. Một chương trình ứng dụng có thể mở nhiều kênh tới server nếu muốn có nhiều giao dịch yêu cầu/phản hồi đồng thời giữa chúng (ứng dụng sẽ cần nhiều luồng). Như minh họa ở :numref:`Hình %s <fig-implicitAckTimeline>`, thông điệp phản hồi đóng vai trò xác nhận thông điệp yêu cầu, và một yêu cầu tiếp theo xác nhận phản hồi trước đó. Lưu ý rằng chúng ta đã thấy một cách tiếp cận rất giống—gọi là *kênh logic đồng thời*—ở phần trước như một cách cải thiện hiệu suất của cơ chế tin cậy dừng-và-đợi.

.. _fig-implicitAckTimeline:
.. figure:: figures/f05-16-9780123850591.png
   :width: 200px
   :align: center

   Dòng thời gian cho giao thức RPC tin cậy sử dụng xác nhận ngầm.

Một phức tạp khác mà RPC phải xử lý là server có thể mất một thời gian tùy ý để tạo ra kết quả, và tệ hơn, nó có thể bị treo trước khi tạo ra phản hồi. Hãy nhớ rằng chúng ta đang nói về khoảng thời gian sau khi server đã xác nhận yêu cầu nhưng trước khi gửi phản hồi. Để giúp client phân biệt giữa server chậm và server chết, phía client của RPC có thể định kỳ gửi thông điệp “Bạn còn sống không?” đến server, và phía server phản hồi bằng một ACK. Ngoài ra, server có thể chủ động gửi thông điệp “Tôi vẫn còn sống” đến client mà không cần client yêu cầu trước. Cách tiếp cận này mở rộng tốt hơn vì nó đặt gánh nặng quản lý bộ định thời timeout lên các client.

Tính tin cậy của RPC có thể bao gồm thuộc tính gọi là *ngữ nghĩa tối đa một lần* (at-most-once semantics). Điều này có nghĩa là với mỗi thông điệp yêu cầu mà client gửi, tối đa chỉ một bản sao của thông điệp đó được chuyển đến server. Mỗi lần client gọi một thủ tục từ xa, thủ tục đó chỉ được thực thi tối đa một lần trên máy server. Chúng ta nói “tối đa một lần” thay vì “chính xác một lần” vì luôn có khả năng mạng hoặc máy server bị lỗi, khiến không thể chuyển được dù chỉ một bản sao của thông điệp yêu cầu.

Để thực hiện ngữ nghĩa tối đa một lần, RPC phía server phải nhận diện các yêu cầu trùng lặp (và bỏ qua chúng), ngay cả khi nó đã phản hồi thành công cho yêu cầu gốc. Do đó, nó phải duy trì một số trạng thái nhận diện các yêu cầu trước đó. Một cách là nhận diện yêu cầu bằng số thứ tự, do đó server chỉ cần nhớ số thứ tự gần nhất. Đáng tiếc, điều này sẽ giới hạn RPC chỉ có một yêu cầu đang chờ (tới một server nhất định) tại một thời điểm, vì một yêu cầu phải hoàn thành trước khi yêu cầu tiếp theo có thể được gửi. Một lần nữa, các kênh cung cấp giải pháp. Server có thể nhận diện các yêu cầu trùng lặp bằng cách nhớ số thứ tự hiện tại cho mỗi kênh, mà không giới hạn client chỉ có một yêu cầu tại một thời điểm.

Mặc dù ngữ nghĩa tối đa một lần nghe có vẻ hiển nhiên, không phải tất cả các giao thức RPC đều hỗ trợ hành vi này. Một số hỗ trợ ngữ nghĩa được gọi đùa là *không hoặc nhiều lần* (zero-or-more semantics); tức là, mỗi lần gọi ở client có thể dẫn đến thủ tục từ xa được thực thi không hoặc nhiều lần. Không khó để hiểu điều này sẽ gây vấn đề cho một thủ tục từ xa thay đổi biến trạng thái cục bộ (ví dụ, tăng một bộ đếm) hoặc có tác động bên ngoài (ví dụ, phóng tên lửa) mỗi lần nó được gọi. Mặt khác, nếu thủ tục từ xa là *idempotent*—gọi nhiều lần cũng như chỉ gọi một lần—thì cơ chế RPC không cần hỗ trợ ngữ nghĩa tối đa một lần; một triển khai đơn giản hơn (có thể nhanh hơn) là đủ.

Cũng như với tính tin cậy, hai lý do khiến một giao thức RPC có thể triển khai phân mảnh và lắp ráp lại thông điệp là vì nó không được cung cấp bởi tầng giao thức nền hoặc vì nó có thể được triển khai hiệu quả hơn bởi giao thức RPC. Xét trường hợp RPC được triển khai trên UDP/IP và dựa vào IP để phân mảnh/lắp ráp lại. Nếu chỉ một mảnh của thông điệp không đến trong một khoảng thời gian nhất định, IP sẽ loại bỏ các mảnh đã đến và thông điệp bị coi là mất. Cuối cùng, giao thức RPC (giả sử nó triển khai tin cậy) sẽ timeout và gửi lại thông điệp. Ngược lại, một giao thức RPC tự triển khai phân mảnh/lắp ráp lại và tích cực ACK hoặc NACK (xác nhận âm) từng mảnh riêng lẻ. Các mảnh bị mất sẽ được phát hiện và gửi lại nhanh hơn, và chỉ các mảnh bị mất mới được gửi lại, không phải toàn bộ thông điệp.

Giao thức đồng bộ và bất đồng bộ
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một cách để phân loại giao thức là dựa vào việc nó là *đồng bộ* hay *bất đồng bộ*. Ý nghĩa chính xác của các thuật ngữ này phụ thuộc vào vị trí trong hệ phân cấp giao thức mà bạn sử dụng chúng. Ở tầng vận chuyển, tốt nhất nên xem chúng như xác định hai cực của một phổ thay vì hai lựa chọn loại trừ lẫn nhau. Thuộc tính then chốt của bất kỳ điểm nào trên phổ này là tiến trình gửi biết được gì sau khi thao tác gửi thông điệp trả về. Nói cách khác, nếu giả sử một chương trình ứng dụng gọi thao tác ``send`` trên một giao thức vận chuyển, thì chính xác ứng dụng biết gì về thành công của thao tác khi ``send`` trả về?

Ở đầu *bất đồng bộ* của phổ, ứng dụng hoàn toàn không biết gì khi ``send`` trả về. Nó không biết liệu thông điệp đã được nhận bởi đối tác hay chưa, thậm chí không biết chắc thông điệp đã rời khỏi máy cục bộ hay chưa. Ở đầu *đồng bộ* của phổ, thao tác ``send`` thường trả về một thông điệp phản hồi. Tức là, ứng dụng không chỉ biết thông điệp nó gửi đã được đối tác nhận, mà còn biết đối tác đã trả lời. Do đó, các giao thức đồng bộ triển khai trừu tượng yêu cầu/phản hồi, trong khi các giao thức bất đồng bộ được dùng nếu bên gửi muốn gửi nhiều thông điệp mà không phải chờ phản hồi. Theo định nghĩa này, các giao thức RPC thường là giao thức đồng bộ.

Mặc dù chúng ta chưa bàn về chúng trong chương này, có những điểm thú vị nằm giữa hai cực này. Ví dụ, giao thức vận chuyển có thể triển khai ``send`` sao cho nó bị chặn (không trả về) cho đến khi thông điệp đã được nhận thành công tại máy từ xa, nhưng trả về trước khi đối tác trên máy đó thực sự xử lý và phản hồi. Điều này đôi khi được gọi là *giao thức datagram tin cậy*.

5.3.2 Các triển khai RPC (SunRPC, DCE, gRPC)
--------------------------------------------

Bây giờ chúng ta chuyển sang thảo luận về một số ví dụ triển khai giao thức RPC. Những ví dụ này sẽ làm nổi bật một số lựa chọn thiết kế khác nhau mà các nhà thiết kế giao thức đã thực hiện. Ví dụ đầu tiên là SunRPC, một giao thức RPC được sử dụng rộng rãi còn gọi là Open Network Computing RPC (ONC RPC). Ví dụ thứ hai, gọi là DCE-RPC, là một phần của Môi trường Tính toán Phân tán (DCE). DCE là một tập hợp các tiêu chuẩn và phần mềm để xây dựng hệ thống phân tán do Open Software Foundation (OSF) định nghĩa, một liên minh các công ty máy tính ban đầu gồm IBM, Digital Equipment Corporation, và Hewlett-Packard; ngày nay, OSF được gọi là The Open Group. Ví dụ thứ ba là gRPC, một cơ chế RPC phổ biến mà Google đã mã nguồn mở, dựa trên một cơ chế RPC mà họ đã sử dụng nội bộ để triển khai các dịch vụ đám mây trong các trung tâm dữ liệu của mình.

Ba ví dụ này đại diện cho những lựa chọn thiết kế khác nhau trong không gian giải pháp RPC, nhưng để bạn không nghĩ rằng chúng là những lựa chọn duy nhất, chúng tôi sẽ mô tả ba cơ chế giống RPC khác (WSDL, SOAP, và REST) trong bối cảnh dịch vụ web ở Chương 9.

SunRPC
~~~~~~

SunRPC trở thành tiêu chuẩn *de facto* nhờ được phân phối rộng rãi cùng với các workstation Sun và vai trò trung tâm của nó trong Hệ thống Tập tin Mạng (NFS) nổi tiếng của Sun. IETF sau đó đã thông qua nó như một giao thức Internet tiêu chuẩn dưới tên ONC RPC.

SunRPC có thể được triển khai trên nhiều giao thức vận chuyển khác nhau. :numref:`Hình %s <fig-sunrpc>` minh họa đồ thị giao thức khi SunRPC được triển khai trên UDP. Như đã đề cập ở phần trước, một người theo chủ nghĩa tầng nghiêm ngặt có thể không đồng tình với ý tưởng chạy một giao thức vận chuyển trên một giao thức vận chuyển, hoặc cho rằng RPC phải là thứ gì đó khác ngoài giao thức vận chuyển vì nó xuất hiện “trên” tầng vận chuyển. Thực tế, quyết định thiết kế chạy RPC trên tầng vận chuyển hiện có là hợp lý, như sẽ thấy rõ trong phần thảo luận sau.

.. _fig-sunrpc:
.. figure:: figures/f05-17-9780123850591.png
   :width: 100px
   :align: center

   Đồ thị giao thức cho SunRPC trên UDP.

SunRPC sử dụng định danh hai tầng để xác định thủ tục từ xa: một số chương trình 32 bit và một số thủ tục 32 bit. (Cũng có một số phiên bản 32 bit, nhưng chúng ta bỏ qua trong phần thảo luận này.) Ví dụ, server NFS được gán số chương trình ``x00100003``, và trong chương trình này ``getattr`` là thủ tục ``1``, ``setattr`` là thủ tục ``2``, ``read`` là thủ tục ``6``, ``write`` là thủ tục ``8``, v.v. Số chương trình và số thủ tục được truyền trong phần đầu thông điệp yêu cầu SunRPC, các trường được thể hiện ở :numref:`Hình %s <fig-sunrpc-format>`. Server—có thể hỗ trợ nhiều số chương trình—chịu trách nhiệm gọi thủ tục được chỉ định của chương trình được chỉ định. Một yêu cầu SunRPC thực chất là yêu cầu gọi chương trình và thủ tục được chỉ định trên máy cụ thể mà yêu cầu được gửi tới, mặc dù cùng số chương trình có thể được triển khai trên các máy khác trong cùng mạng. Do đó, địa chỉ của máy server (ví dụ, địa chỉ IP) là tầng thứ ba ngầm định của địa chỉ RPC.

.. _fig-sunrpc-format:
.. figure:: figures/f05-18-9780123850591.png
   :width: 400px
   :align: center

   Định dạng phần đầu SunRPC: (a) yêu cầu; (b) phản hồi.

Các số chương trình khác nhau có thể thuộc về các server khác nhau trên cùng một máy. Các server này có các khóa phân kênh tầng vận chuyển khác nhau (ví dụ, cổng UDP), hầu hết không phải là số nổi tiếng mà được gán động. Các khóa phân kênh này được gọi là *bộ chọn vận chuyển* (transport selectors). Làm thế nào để một client SunRPC muốn nói chuyện với một chương trình cụ thể biết được bộ chọn vận chuyển nào để dùng để đến đúng server? Giải pháp là gán một địa chỉ nổi tiếng cho *chỉ một* chương trình trên máy từ xa và để chương trình đó xử lý việc thông báo cho client biết bộ chọn vận chuyển nào để dùng để đến các chương trình khác trên máy. Phiên bản gốc của chương trình SunRPC này gọi là *Port Mapper*, chỉ hỗ trợ UDP và TCP làm giao thức nền. Số chương trình của nó là ``x00100000``, và cổng nổi tiếng là ``111``. RPCBIND, phát triển từ Port Mapper, hỗ trợ các giao thức vận chuyển nền tùy ý. Khi mỗi server SunRPC khởi động, nó gọi một thủ tục đăng ký RPCBIND trên chính máy chủ của mình để đăng ký bộ chọn vận chuyển và các số chương trình mà nó hỗ trợ. Một client từ xa sau đó có thể gọi một thủ tục tra cứu RPCBIND để tìm bộ chọn vận chuyển cho một số chương trình cụ thể.

Để cụ thể hơn, hãy xét ví dụ sử dụng Port Mapper với UDP. Để gửi một thông điệp yêu cầu đến thủ tục ``read`` của NFS, client đầu tiên gửi một thông điệp yêu cầu đến Port Mapper tại cổng UDP nổi tiếng ``111``, yêu cầu thủ tục ``3`` được gọi để ánh xạ số chương trình ``x00100003`` sang cổng UDP nơi chương trình NFS hiện đang chạy. Client sau đó gửi một thông điệp yêu cầu SunRPC với số chương trình ``x00100003`` và số thủ tục ``6`` đến cổng UDP này, và module SunRPC lắng nghe tại cổng đó sẽ gọi thủ tục ``read`` của NFS. Client cũng lưu vào bộ nhớ đệm ánh xạ số chương trình sang số cổng để không phải quay lại Port Mapper mỗi lần muốn nói chuyện với chương trình NFS.\ [#]_

.. [#] Trên thực tế, NFS là một chương trình quan trọng đến mức nó đã được gán cổng UDP nổi tiếng riêng, nhưng để minh họa, chúng ta giả định không phải như vậy.

Để ghép một thông điệp phản hồi với thông điệp yêu cầu tương ứng, để kết quả RPC có thể trả về đúng bên gọi, cả phần đầu thông điệp yêu cầu và phản hồi đều bao gồm trường ``XID`` (transaction ID), như trong :numref:`Hình %s <fig-sunrpc-format>`. ``XID`` là một ID giao dịch duy nhất chỉ được dùng bởi một yêu cầu và phản hồi tương ứng. Sau khi server đã phản hồi thành công cho một yêu cầu, nó không nhớ ``XID`` nữa. Vì vậy, SunRPC không thể đảm bảo ngữ nghĩa tối đa một lần.

Chi tiết về ngữ nghĩa của SunRPC phụ thuộc vào giao thức vận chuyển nền. Nó không tự triển khai tin cậy, nên chỉ tin cậy nếu tầng vận chuyển nền tin cậy. (Tất nhiên, bất kỳ ứng dụng nào chạy trên SunRPC cũng có thể tự triển khai cơ chế tin cậy ở tầng trên SunRPC.) Khả năng gửi thông điệp yêu cầu và phản hồi lớn hơn MTU mạng cũng phụ thuộc vào tầng vận chuyển nền. Nói cách khác, SunRPC không cố gắng cải thiện tầng vận chuyển nền về mặt tin cậy và kích thước thông điệp. Vì SunRPC có thể chạy trên nhiều giao thức vận chuyển khác nhau, điều này mang lại sự linh hoạt mà không làm phức tạp thiết kế giao thức RPC.

Quay lại định dạng phần đầu SunRPC ở :numref:`Hình %s <fig-sunrpc-format>`, thông điệp yêu cầu chứa các trường ``Credentials`` và ``Verifier`` có độ dài biến đổi, cả hai đều được client dùng để xác thực với server—tức là, cung cấp bằng chứng rằng client có quyền gọi server. Cách một client xác thực với server là một vấn đề chung mà bất kỳ giao thức nào muốn cung cấp mức độ bảo mật hợp lý đều phải giải quyết. Chủ đề này sẽ được bàn chi tiết hơn ở chương khác.

DCE-RPC
~~~~~~~

DCE-RPC là giao thức RPC cốt lõi của hệ thống DCE và là nền tảng cho cơ chế RPC của Microsoft DCOM và ActiveX. Nó có thể được sử dụng với trình biên dịch stub Network Data Representation (NDR) được mô tả ở chương khác, nhưng nó cũng là giao thức RPC nền tảng cho Common Object Request Broker Architecture (CORBA), một tiêu chuẩn công nghiệp để xây dựng hệ thống phân tán hướng đối tượng.

DCE-RPC, giống như SunRPC, có thể được triển khai trên nhiều giao thức vận chuyển bao gồm UDP và TCP. Nó cũng giống SunRPC ở chỗ định nghĩa sơ đồ địa chỉ hai tầng: giao thức vận chuyển phân kênh đến đúng server, DCE-RPC phân phối đến thủ tục cụ thể được server xuất ra, và client tham khảo dịch vụ ánh xạ endpoint (giống Port Mapper của SunRPC) để biết cách đến đúng server. Tuy nhiên, khác với SunRPC, DCE-RPC triển khai ngữ nghĩa gọi tối đa một lần. (Thực ra, DCE-RPC hỗ trợ nhiều ngữ nghĩa gọi, bao gồm cả ngữ nghĩa idempotent giống SunRPC, nhưng tối đa một lần là hành vi mặc định.) Có một số khác biệt khác giữa hai cách tiếp cận, sẽ được làm rõ ở các đoạn sau.

.. _fig-dce:
.. figure:: figures/f05-19-9780123850591.png
   :width: 200px
   :align: center

   Trao đổi thông điệp điển hình của DCE-RPC.

:numref:`Hình %s <fig-dce>` cho thấy dòng thời gian cho trao đổi thông điệp điển hình, mỗi thông điệp được gắn nhãn theo loại DCE-RPC. Client gửi thông điệp ``Request``, server cuối cùng phản hồi bằng thông điệp ``Response``, và client xác nhận (``Ack``) phản hồi. Tuy nhiên, thay vì server xác nhận thông điệp yêu cầu, client định kỳ gửi thông điệp ``Ping`` đến server, server phản hồi bằng thông điệp ``Working`` để báo rằng thủ tục từ xa vẫn đang thực hiện. Nếu phản hồi của server đến đủ nhanh, không có ``Ping`` nào được gửi. Mặc dù không được thể hiện trong hình, các loại thông điệp khác cũng được hỗ trợ. Ví dụ, client có thể gửi thông điệp ``Quit`` đến server, yêu cầu hủy một lời gọi trước đó vẫn đang thực hiện; server phản hồi bằng thông điệp ``Quack`` (quit acknowledgment). Ngoài ra, server có thể phản hồi thông điệp ``Request`` bằng thông điệp ``Reject`` (báo rằng lời gọi bị từ chối), và có thể phản hồi thông điệp ``Ping`` bằng thông điệp ``Nocall`` (báo rằng server chưa từng nghe về client đó).

Mỗi giao dịch yêu cầu/phản hồi trong DCE-RPC diễn ra trong ngữ cảnh của một *hoạt động* (activity). Một hoạt động là một kênh yêu cầu/phản hồi logic giữa một cặp đối tác. Tại bất kỳ thời điểm nào, chỉ có thể có một giao dịch thông điệp hoạt động trên một kênh. Giống như cách tiếp cận kênh logic đồng thời đã mô tả ở trên, các chương trình ứng dụng phải mở nhiều kênh nếu muốn có nhiều giao dịch yêu cầu/phản hồi đồng thời giữa chúng. Hoạt động mà một thông điệp thuộc về được xác định bởi trường ``ActivityId`` của thông điệp. Trường ``SequenceNum`` phân biệt các lời gọi được thực hiện như một phần của cùng một hoạt động; nó có cùng mục đích như trường ``XID`` (transaction id) của SunRPC. Khác với SunRPC, DCE-RPC theo dõi số thứ tự cuối cùng đã dùng trong một hoạt động cụ thể, để đảm bảo ngữ nghĩa tối đa một lần. Để phân biệt các phản hồi được gửi trước và sau khi máy server khởi động lại, DCE-RPC sử dụng trường ``ServerBoot`` để lưu boot ID của máy.

Một lựa chọn thiết kế khác trong DCE-RPC khác với SunRPC là hỗ trợ phân mảnh và lắp ráp lại trong giao thức RPC. Như đã đề cập, ngay cả khi giao thức nền như IP cung cấp phân mảnh/lắp ráp lại, một thuật toán tinh vi hơn được triển khai ở tầng RPC có thể giúp phục hồi nhanh hơn và giảm băng thông khi các mảnh bị mất. Trường ``FragmentNum`` xác định duy nhất mỗi mảnh tạo nên một thông điệp yêu cầu hoặc phản hồi. Mỗi mảnh DCE-RPC được gán một số mảnh duy nhất (0, 1, 2, 3, ...). Cả client và server đều triển khai cơ chế xác nhận chọn lọc, hoạt động như sau. (Chúng tôi mô tả cơ chế này theo hướng client gửi thông điệp yêu cầu phân mảnh đến server; cơ chế tương tự áp dụng khi server gửi phản hồi phân mảnh đến client.)

Đầu tiên, mỗi mảnh tạo nên thông điệp yêu cầu chứa cả ``FragmentNum`` duy nhất và một cờ chỉ ra liệu gói này là mảnh của một lời gọi (``frag``) hay là mảnh cuối cùng của một lời gọi; các thông điệp yêu cầu vừa một gói mang cờ này. Server biết đã nhận đủ thông điệp yêu cầu khi có gói cuối cùng và không có khoảng trống trong các số mảnh. Thứ hai, để đáp lại mỗi mảnh đến, server gửi một thông điệp ``Fack`` (fragment acknowledgment) đến client. Xác nhận này xác định số mảnh lớn nhất mà server đã nhận thành công. Nói cách khác, xác nhận là tích lũy, giống như trong TCP. Ngoài ra, server còn xác nhận chọn lọc bất kỳ số mảnh cao hơn nào đã nhận ngoài thứ tự. Nó làm điều này bằng một vector bit xác định các mảnh ngoài thứ tự so với mảnh đúng thứ tự cao nhất đã nhận. Cuối cùng, client phản hồi bằng cách gửi lại các mảnh bị thiếu.

:numref:`Hình %s <fig-fack>` minh họa cách hoạt động này. Giả sử server đã nhận thành công các mảnh đến số 20, cộng với các mảnh 23, 25, và 26. Server phản hồi bằng một ``Fack`` xác định mảnh 20 là mảnh đúng thứ tự cao nhất, cộng với một bit-vector (``SelAck``) với các bit thứ ba (23=20+3), năm (25=20+5), và sáu (26=20+6) được bật. Để hỗ trợ một vector bit gần như tùy ý dài, kích thước vector (tính bằng từ 32 bit) được cho trong trường ``SelAckLen``.

.. _fig-fack:
.. figure:: figures/f05-20-9780123850591.png
   :width: 500px
   :align: center

   Phân mảnh với xác nhận chọn lọc.

Với việc DCE-RPC hỗ trợ thông điệp rất lớn—trường ``FragmentNum`` dài 16 bit, nghĩa là có thể hỗ trợ 64K mảnh—không phù hợp để giao thức gửi tất cả các mảnh tạo nên một thông điệp càng nhanh càng tốt vì làm vậy có thể làm tràn bộ nhận. Thay vào đó, DCE-RPC triển khai thuật toán điều khiển luồng rất giống với TCP. Cụ thể, mỗi thông điệp ``Fack`` không chỉ xác nhận các mảnh đã nhận mà còn thông báo cho bên gửi biết có thể gửi thêm bao nhiêu mảnh. Đây là mục đích của trường ``WindowSize`` trong :numref:`Hình %s <fig-fack>`, phục vụ đúng chức năng như trường ``AdvertisedWindow`` của TCP, chỉ khác là đếm mảnh thay vì byte. DCE-RPC cũng triển khai cơ chế điều khiển tắc nghẽn giống TCP. Với độ phức tạp của điều khiển tắc nghẽn, không ngạc nhiên khi một số giao thức RPC tránh nó bằng cách tránh phân mảnh.

Tóm lại, các nhà thiết kế có khá nhiều lựa chọn khi thiết kế một giao thức RPC. SunRPC chọn cách tiếp cận tối giản và chỉ bổ sung rất ít cho tầng vận chuyển nền ngoài những thứ thiết yếu như xác định đúng thủ tục và nhận diện thông điệp. DCE-RPC bổ sung nhiều chức năng hơn, với khả năng cải thiện hiệu suất trong một số môi trường, đổi lại là độ phức tạp cao hơn.

gRPC
~~~~

Dù có nguồn gốc từ Google, gRPC không phải là viết tắt của Google RPC. Chữ “g” có ý nghĩa khác nhau ở mỗi phiên bản. Ở phiên bản 1.10 nó là “glamorous”, ở 1.18 là “goose”. Theo FAQ chính thức của gRPC, hiện nay nó là một từ viết tắt đệ quy: gRPC nghĩa là “gRPC Remote Procedure Call”. Các kỹ sư Google thật sáng tạo. Tuy nhiên, gRPC phổ biến vì nó cung cấp cho mọi người—dưới dạng mã nguồn mở—kinh nghiệm hàng thập kỷ của Google trong việc sử dụng RPC để xây dựng các dịch vụ đám mây quy mô lớn.

Trước khi đi vào chi tiết, có một số khác biệt lớn giữa gRPC và hai ví dụ trước. Khác biệt lớn nhất là gRPC được thiết kế cho dịch vụ đám mây thay vì mô hình client/server đơn giản trước đó. Sự khác biệt này về cơ bản là một mức độ gián tiếp bổ sung. Trong thế giới client/server, client gọi một phương thức trên một tiến trình server cụ thể chạy trên một máy chủ cụ thể. Một tiến trình server được giả định là đủ để phục vụ các lời gọi từ tất cả các client có thể gọi nó.

Với dịch vụ đám mây, client gọi một phương thức trên một *dịch vụ*, để hỗ trợ các lời gọi từ số lượng client tùy ý cùng lúc, dịch vụ này được triển khai bởi một số lượng tiến trình server có thể mở rộng, mỗi tiến trình có thể chạy trên một máy chủ khác nhau. Đây là nơi đám mây phát huy tác dụng: các trung tâm dữ liệu cung cấp số lượng máy chủ dường như vô hạn để mở rộng dịch vụ đám mây. Khi nói “có thể mở rộng”, ý là số lượng tiến trình server giống nhau bạn chọn tạo ra phụ thuộc vào tải (tức là số lượng client muốn được phục vụ tại một thời điểm) và số lượng đó có thể điều chỉnh động theo thời gian. Một chi tiết nữa là dịch vụ đám mây thường không tạo tiến trình mới, mà thay vào đó khởi động một *container* mới, về cơ bản là một tiến trình được đóng gói trong một môi trường cô lập chứa tất cả các gói phần mềm cần thiết để chạy. Docker là ví dụ điển hình về nền tảng container ngày nay.

.. _fig-rpc-service:
.. figure:: figures/rpc/Slide1.png
   :width: 400px
   :align: center

   Sử dụng RPC để gọi một dịch vụ đám mây có thể mở rộng.

Quay lại khẳng định rằng một dịch vụ về cơ bản là một mức độ gián tiếp bổ sung đặt lên trên server, tất cả điều này nghĩa là bên gọi xác định dịch vụ muốn gọi, và một *bộ cân bằng tải* sẽ chuyển lời gọi đó đến một trong nhiều tiến trình server (container) sẵn có triển khai dịch vụ đó, như minh họa ở :numref:`Hình %s <fig-rpc-service>`. Bộ cân bằng tải có thể được triển khai theo nhiều cách, bao gồm thiết bị phần cứng, nhưng thường được triển khai bằng một tiến trình proxy chạy trong máy ảo (cũng được lưu trữ trên đám mây) thay vì là thiết bị vật lý.

Có một tập hợp các thực tiễn tốt nhất để triển khai mã server thực tế cuối cùng phản hồi yêu cầu đó, và một số cơ chế đám mây bổ sung để tạo/hủy container và cân bằng tải các yêu cầu giữa các container đó. Kubernetes là ví dụ điển hình về hệ thống quản lý container như vậy ngày nay, và *kiến trúc micro-services* là tên gọi cho các thực tiễn tốt nhất trong xây dựng dịch vụ theo cách gốc đám mây này. Cả hai đều là chủ đề thú vị, nhưng vượt quá phạm vi cuốn sách này.

Điều chúng ta quan tâm ở đây là giao thức vận chuyển ở lõi của gRPC. Ở đây lại có một khác biệt lớn so với hai giao thức ví dụ trước, không phải về các vấn đề cơ bản cần giải quyết, mà về cách tiếp cận của gRPC để giải quyết chúng. Nói ngắn gọn, gRPC “giao phó” nhiều vấn đề cho các giao thức khác, để lại cho gRPC chủ yếu là đóng gói các khả năng đó thành một hình thức dễ sử dụng. Hãy xem chi tiết.

Đầu tiên, gRPC chạy trên TCP thay vì UDP, nghĩa là nó giao phó các vấn đề quản lý kết nối và truyền tin cậy các thông điệp yêu cầu và phản hồi có kích thước tùy ý. Thứ hai, gRPC thực sự chạy trên một phiên bản TCP bảo mật gọi là *Transport Layer Security* (TLS)—một lớp mỏng nằm trên TCP trong ngăn xếp giao thức—nghĩa là nó giao phó trách nhiệm bảo mật kênh truyền thông để kẻ xấu không thể nghe lén hoặc chiếm đoạt trao đổi thông điệp. Thứ ba, gRPC thực sự, thực sự chạy trên HTTP/2 (bản thân nó được xếp trên TCP và TLS), nghĩa là gRPC giao phó thêm hai vấn đề nữa: (1) mã hóa/nén dữ liệu nhị phân hiệu quả vào một thông điệp, (2) phân kênh nhiều lời gọi thủ tục từ xa trên một kết nối TCP duy nhất. Nói cách khác, gRPC mã hóa định danh phương thức từ xa dưới dạng URI, các tham số yêu cầu cho phương thức từ xa dưới dạng nội dung trong thông điệp HTTP, và giá trị trả về từ phương thức từ xa trong phản hồi HTTP. Toàn bộ ngăn xếp gRPC được mô tả ở :numref:`Hình %s <fig-grpc-stack>`, cũng bao gồm các thành phần đặc thù ngôn ngữ. (Một điểm mạnh của gRPC là hỗ trợ nhiều ngôn ngữ lập trình, chỉ một phần nhỏ được thể hiện ở :numref:`Hình %s <fig-grpc-stack>`.)

.. _fig-grpc-stack:
.. figure:: figures/rpc/Slide2.png
   :width: 400px
   :align: center

   Lõi gRPC xếp trên HTTP, TLS, TCP và hỗ trợ nhiều ngôn ngữ.

Chúng tôi sẽ bàn về TLS ở Chương 8 (trong bối cảnh các chủ đề bảo mật rộng hơn) và HTTP ở Chương 9 (trong bối cảnh các giao thức cấp ứng dụng truyền thống). Nhưng chúng ta thấy mình rơi vào một vòng phụ thuộc thú vị: RPC là một kiểu giao thức vận chuyển dùng để triển khai ứng dụng phân tán, HTTP là ví dụ về giao thức cấp ứng dụng, nhưng gRPC lại chạy trên HTTP thay vì ngược lại.

Giải thích ngắn gọn là việc phân tầng cung cấp một cách tiện lợi để con người hình dung các hệ thống phức tạp, nhưng thực ra chúng ta đang cố gắng giải quyết một tập hợp vấn đề (ví dụ, truyền tin cậy thông điệp kích thước tùy ý, nhận diện người gửi và người nhận, ghép thông điệp yêu cầu với phản hồi, v.v.) và cách các giải pháp này được đóng gói thành các giao thức, rồi các giao thức đó được xếp tầng lên nhau, là kết quả của những thay đổi dần dần theo thời gian. Bạn có thể cho rằng đó là một tai nạn lịch sử. Nếu Internet bắt đầu với một cơ chế RPC phổ biến như TCP, HTTP có thể đã được triển khai trên nó (cũng như hầu hết các giao thức cấp ứng dụng khác được mô tả ở Chương 9) và Google sẽ dành thời gian cải tiến giao thức đó thay vì phát minh ra một giao thức riêng (như họ và nhiều người khác đã làm với TCP). Thay vào đó, web trở thành ứng dụng sát thủ của Internet, nghĩa là giao thức ứng dụng của nó (HTTP) được toàn bộ hạ tầng Internet hỗ trợ: Firewall, Load Balancer, Mã hóa, Xác thực, Nén, v.v. Vì tất cả các thành phần mạng này được thiết kế để hoạt động tốt với HTTP, HTTP thực tế đã trở thành giao thức vận chuyển yêu cầu/phản hồi phổ quát của Internet.

Quay lại các đặc điểm riêng của gRPC, giá trị lớn nhất mà nó mang lại là tích hợp *streaming* vào cơ chế RPC, tức là, gRPC hỗ trợ bốn kiểu mẫu yêu cầu/phản hồi khác nhau:

1. RPC đơn giản: Client gửi một thông điệp yêu cầu và server phản hồi bằng một thông điệp trả lời.

2. RPC streaming phía server: Client gửi một thông điệp yêu cầu và server phản hồi bằng một luồng các thông điệp trả lời. Client hoàn thành khi nhận đủ các phản hồi từ server.

3. RPC streaming phía client: Client gửi một luồng các yêu cầu đến server, và server gửi lại một phản hồi duy nhất, thường (nhưng không nhất thiết) sau khi nhận đủ các yêu cầu từ client.

4. RPC streaming hai chiều: Lời gọi được khởi tạo bởi client, nhưng sau đó, client và server có thể đọc và ghi các yêu cầu và phản hồi theo bất kỳ thứ tự nào; các luồng hoàn toàn độc lập.

Sự tự do bổ sung này trong cách client và server tương tác nghĩa là giao thức vận chuyển gRPC cần gửi thêm metadata và thông điệp điều khiển—ngoài các thông điệp yêu cầu và phản hồi thực tế—giữa hai bên. Ví dụ bao gồm mã ``Error`` và ``Status`` (để chỉ thành công hoặc lý do thất bại), ``Timeouts`` (để chỉ thời gian client sẵn sàng chờ phản hồi), ``PING`` (thông báo giữ kết nối để chỉ một bên vẫn đang chạy), ``EOS`` (thông báo kết thúc luồng để chỉ không còn yêu cầu hay phản hồi nào nữa), và ``GOAWAY`` (thông báo từ server đến client rằng sẽ không nhận thêm stream mới nào nữa). Không giống nhiều giao thức khác trong sách này, nơi chúng tôi trình bày định dạng phần đầu giao thức, cách thông tin điều khiển này được truyền giữa hai bên phần lớn do giao thức vận chuyển nền quyết định, trong trường hợp này là HTTP/2. Ví dụ, như sẽ thấy ở Chương 9, HTTP đã bao gồm một tập trường phần đầu và mã phản hồi mà gRPC tận dụng.

Bạn có thể muốn xem qua phần HTTP ở Chương 9 trước khi tiếp tục, nhưng phần sau khá dễ hiểu. Một yêu cầu RPC đơn giản (không streaming) có thể bao gồm thông điệp HTTP sau từ client đến server:

.. code-block:: html

   HEADERS (flags = END_HEADERS)
   :method = POST
   :scheme = http
   :path = /google.pubsub.v2.PublisherService/CreateTopic
   :authority = pubsub.googleapis.com
   grpc-timeout = 1S
   content-type = application/grpc+proto
   grpc-encoding = gzip
   authorization = Bearer y235.wef315yfh138vh31hv93hv8h3v
   DATA (flags = END_STREAM)
   <Length-Prefixed Message>

dẫn đến thông điệp phản hồi sau từ server về client:

.. code-block:: html

   HEADERS (flags = END_HEADERS)
   :status = 200
   grpc-encoding = gzip
   content-type = application/grpc+proto
   DATA
   <Length-Prefixed Message>
   HEADERS (flags = END_STREAM, END_HEADERS)
   grpc-status = 0 # OK
   trace-proto-bin = jher831yy13JHy3hc

Trong ví dụ này, ``HEADERS`` và ``DATA`` là hai thông điệp điều khiển HTTP tiêu chuẩn, về cơ bản phân biệt giữa “phần đầu thông điệp” và “payload của thông điệp”. Cụ thể, mỗi dòng sau ``HEADERS`` (nhưng trước ``DATA``) là một cặp ``attribute = value`` tạo nên phần đầu (hãy nghĩ mỗi dòng như một trường phần đầu); các cặp bắt đầu bằng dấu hai chấm (ví dụ, ``:status = 200``) là một phần của tiêu chuẩn HTTP (ví dụ, trạng thái ``200`` chỉ thành công); các cặp không bắt đầu bằng dấu hai chấm là các tuỳ chỉnh riêng của gRPC (ví dụ, ``grpc-encoding = gzip`` chỉ ra dữ liệu trong thông điệp đã được nén bằng ``gzip``, và ``grpc-timeout = 1S`` chỉ client đặt timeout một giây).

Còn một chi tiết cuối cùng cần giải thích. Dòng phần đầu

.. code-block:: html

   content-type = application/grpc+proto

chỉ ra rằng phần thân thông điệp (được phân định bởi dòng ``DATA``) chỉ có ý nghĩa với chương trình ứng dụng (tức là phương thức server) mà client này yêu cầu dịch vụ. Cụ thể hơn, chuỗi ``+proto`` chỉ ra rằng bên nhận sẽ có thể diễn giải các bit trong thông điệp theo một đặc tả *Protocol Buffer* (viết tắt là ``proto``). Protocol Buffer là cách gRPC xác định cách các tham số được truyền cho server được mã hóa vào một thông điệp, từ đó dùng để sinh ra các stub nằm giữa cơ chế RPC nền và các hàm thực tế được gọi (xem :numref:`Hình %s <fig-rpc-stub>`). Chủ đề này sẽ được bàn ở Chương 7.

.. _key-micro-service:
.. admonition:: Bài học then chốt

   Điểm mấu chốt là các cơ chế phức tạp như RPC, từng được đóng gói thành một gói phần mềm nguyên khối (như SunRPC và DCE-RPC), ngày nay được xây dựng bằng cách lắp ghép nhiều thành phần nhỏ, mỗi thành phần giải quyết một vấn đề hẹp. gRPC vừa là ví dụ cho cách tiếp cận đó, vừa là công cụ thúc đẩy việc áp dụng rộng rãi hơn. Kiến trúc micro-services đề cập ở trên áp dụng chiến lược “xây từ các phần nhỏ” cho toàn bộ ứng dụng đám mây (ví dụ, Uber, Lyft, Netflix, Yelp, Spotify), nơi gRPC thường là cơ chế giao tiếp giữa các phần nhỏ đó. :ref:`[Tiếp theo] <key-alf>`
