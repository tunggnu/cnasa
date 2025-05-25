==================
 1.2 Yêu cầu
==================

Chúng ta đã đặt ra cho mình một mục tiêu đầy tham vọng: hiểu cách xây dựng một mạng máy tính từ đầu. Cách tiếp cận của chúng ta để đạt được mục tiêu này sẽ là bắt đầu từ những nguyên lý cơ bản và sau đó đặt ra những câu hỏi mà chúng ta tự nhiên sẽ hỏi nếu xây dựng một mạng thực tế. Ở mỗi bước, chúng ta sẽ sử dụng các giao thức hiện nay để minh họa các lựa chọn thiết kế khác nhau có sẵn, nhưng chúng ta sẽ không coi những hiện vật hiện có này là chân lý tuyệt đối. Thay vào đó, chúng ta sẽ đặt (và trả lời) câu hỏi *tại sao* các mạng lại được thiết kế như hiện nay. Mặc dù thật hấp dẫn khi chỉ dừng lại ở việc hiểu cách làm hiện tại, nhưng điều quan trọng là phải nhận ra các khái niệm nền tảng vì các mạng luôn thay đổi khi công nghệ phát triển và các ứng dụng mới được phát minh. Theo kinh nghiệm của chúng tôi, một khi bạn hiểu được các ý tưởng cơ bản, bất kỳ giao thức mới nào mà bạn gặp phải cũng sẽ tương đối dễ tiếp thu.

1.2.1 Các bên liên quan
-----------------------

Như đã đề cập ở trên, một người học về mạng có thể tiếp cận từ nhiều góc độ khác nhau. Khi chúng tôi viết ấn bản đầu tiên của cuốn sách này, phần lớn dân số hoàn toàn không có truy cập Internet, và những người có thì chủ yếu truy cập tại nơi làm việc, trường đại học hoặc qua modem quay số tại nhà. Số lượng ứng dụng phổ biến có thể đếm trên đầu ngón tay. Do đó, giống như hầu hết các cuốn sách cùng thời, sách của chúng tôi tập trung vào góc nhìn của người thiết kế thiết bị và giao thức mạng. Chúng tôi tiếp tục tập trung vào góc nhìn này, và hy vọng rằng sau khi đọc cuốn sách này, bạn sẽ biết cách thiết kế thiết bị và giao thức mạng của tương lai.

Tuy nhiên, chúng tôi cũng muốn đề cập đến góc nhìn của hai nhóm liên quan bổ sung: những người phát triển ứng dụng mạng và những người quản lý hoặc vận hành mạng. Hãy xem ba nhóm này có thể liệt kê các yêu cầu đối với một mạng như thế nào:

-  Một *lập trình viên ứng dụng* sẽ liệt kê các dịch vụ mà ứng dụng của họ cần: ví dụ, đảm bảo rằng mỗi thông điệp ứng dụng gửi sẽ được chuyển giao không lỗi trong một khoảng thời gian nhất định hoặc khả năng chuyển đổi linh hoạt giữa các kết nối mạng khác nhau khi người dùng di chuyển.
-  Một *nhà vận hành mạng* sẽ liệt kê các đặc điểm của một hệ thống dễ quản trị và vận hành: ví dụ, các lỗi có thể dễ dàng cô lập, thiết bị mới có thể được thêm vào mạng và cấu hình đúng, và dễ dàng kiểm soát việc sử dụng.
-  Một *nhà thiết kế mạng* sẽ liệt kê các thuộc tính của một thiết kế hiệu quả về chi phí: ví dụ, tài nguyên mạng được sử dụng hiệu quả và phân bổ công bằng cho các người dùng khác nhau. Các vấn đề về hiệu năng cũng có thể rất quan trọng.

Phần này cố gắng chắt lọc các yêu cầu của các bên liên quan khác nhau thành phần giới thiệu cấp cao về các yếu tố chính thúc đẩy thiết kế mạng và qua đó xác định các thách thức được đề cập trong phần còn lại của cuốn sách này.

1.2.2 Kết nối mở rộng quy mô
----------------------------

Bắt đầu từ điều hiển nhiên, một mạng phải cung cấp kết nối giữa một tập hợp các máy tính. Đôi khi chỉ cần xây dựng một mạng giới hạn kết nối một vài máy được chọn. Thực tế, vì lý do riêng tư và bảo mật, nhiều mạng riêng (doanh nghiệp) có mục tiêu rõ ràng là giới hạn tập hợp các máy được kết nối. Ngược lại, các mạng khác (Internet là ví dụ điển hình) được thiết kế để phát triển theo cách cho phép chúng có khả năng kết nối tất cả các máy tính trên thế giới. Một hệ thống được thiết kế để hỗ trợ phát triển đến quy mô bất kỳ được gọi là *có khả năng mở rộng* (scale). Sử dụng Internet làm mô hình, cuốn sách này đề cập đến thách thức về khả năng mở rộng.

Để hiểu đầy đủ hơn các yêu cầu về kết nối, chúng ta cần xem xét kỹ hơn cách các máy tính được kết nối trong một mạng. Kết nối xảy ra ở nhiều cấp độ khác nhau. Ở cấp thấp nhất, một mạng có thể bao gồm hai hoặc nhiều máy tính được kết nối trực tiếp bằng một số phương tiện vật lý, như cáp đồng trục hoặc cáp quang. Chúng ta gọi phương tiện vật lý như vậy là một *liên kết* (link), và thường gọi các máy tính mà nó kết nối là *nút* (node). (Đôi khi một nút là một phần cứng chuyên dụng hơn là một máy tính, nhưng chúng ta sẽ bỏ qua sự khác biệt này trong phạm vi thảo luận này.) Như minh họa trong :numref:`Hình %s <fig-direct>`, các liên kết vật lý đôi khi chỉ giới hạn cho một cặp nút (liên kết như vậy gọi là *point-to-point*), trong khi ở các trường hợp khác, nhiều hơn hai nút có thể chia sẻ một liên kết vật lý duy nhất (liên kết như vậy gọi là *multiple-access*). Các liên kết không dây, như mạng di động và Wi-Fi, là một lớp quan trọng của liên kết multiple-access. Luôn luôn đúng là các liên kết multiple-access bị giới hạn về kích thước, cả về khoảng cách địa lý mà chúng có thể bao phủ và số lượng nút mà chúng có thể kết nối. Vì lý do này, chúng thường được sử dụng để triển khai cái gọi là *last mile*, kết nối người dùng cuối với phần còn lại của mạng.

.. _fig-direct:
.. figure:: figures/f01-02-9780123850591.png
   :width: 500px
   :align: center

   Liên kết trực tiếp: (a) point-to-point; (b) multiple-access.

Nếu các mạng máy tính chỉ giới hạn trong các trường hợp mà tất cả các nút đều được kết nối trực tiếp với nhau qua một phương tiện vật lý chung, thì hoặc là mạng sẽ rất hạn chế về số lượng máy tính có thể kết nối, hoặc số lượng dây cáp đi ra từ mỗi nút sẽ nhanh chóng trở nên không thể quản lý và rất tốn kém. May mắn thay, kết nối giữa hai nút không nhất thiết phải là kết nối vật lý trực tiếp—kết nối gián tiếp có thể đạt được giữa một tập hợp các nút hợp tác. Hãy xem hai ví dụ sau về cách một tập hợp máy tính có thể được kết nối gián tiếp.

:numref:`Hình %s <fig-psn>` cho thấy một tập hợp các nút, mỗi nút được kết nối với một hoặc nhiều liên kết point-to-point. Những nút được kết nối với ít nhất hai liên kết sẽ chạy phần mềm chuyển tiếp dữ liệu nhận được trên một liên kết sang liên kết khác. Nếu được tổ chức một cách hệ thống, các nút chuyển tiếp này tạo thành một *mạng chuyển mạch* (switched network). Có nhiều loại mạng chuyển mạch, trong đó hai loại phổ biến nhất là *chuyển mạch kênh* (circuit switched) và *chuyển mạch gói* (packet switched). Loại đầu tiên được sử dụng chủ yếu trong hệ thống điện thoại, trong khi loại thứ hai được sử dụng cho phần lớn các mạng máy tính và sẽ là trọng tâm của cuốn sách này. (Chuyển mạch kênh, tuy nhiên, đang có sự trở lại trong lĩnh vực mạng quang học, điều này trở nên quan trọng khi nhu cầu về dung lượng mạng không ngừng tăng.) Đặc điểm quan trọng của mạng chuyển mạch gói là các nút trong mạng này gửi các khối dữ liệu rời rạc cho nhau. Hãy coi các khối dữ liệu này tương ứng với một phần dữ liệu ứng dụng như một tệp, một email hoặc một hình ảnh. Chúng ta gọi mỗi khối dữ liệu là một *gói* (packet) hoặc *thông điệp* (message), và hiện tại hai thuật ngữ này được dùng thay thế cho nhau.

.. _fig-psn:
.. figure:: figures/f01-03-9780123850591.png
   :width: 500px
   :align: center

   Mạng chuyển mạch.

Các mạng chuyển mạch gói thường sử dụng chiến lược gọi là *lưu trữ và chuyển tiếp* (store-and-forward). Đúng như tên gọi, mỗi nút trong mạng lưu trữ và chuyển tiếp trước tiên nhận toàn bộ gói qua một liên kết, lưu gói vào bộ nhớ trong, sau đó chuyển tiếp toàn bộ gói sang nút tiếp theo. Ngược lại, mạng chuyển mạch kênh trước tiên thiết lập một kênh chuyên dụng qua một chuỗi các liên kết rồi cho phép nút nguồn gửi một luồng bit qua kênh này đến nút đích. Lý do chính để sử dụng chuyển mạch gói thay vì chuyển mạch kênh trong mạng máy tính là hiệu quả, sẽ được bàn ở phần tiếp theo.

Đám mây trong :numref:`Hình %s <fig-psn>` phân biệt giữa các nút bên trong *triển khai* mạng (thường gọi là *switch*, chức năng chính là lưu trữ và chuyển tiếp gói) và các nút bên ngoài đám mây *sử dụng* mạng (truyền thống gọi là *host*, hỗ trợ người dùng và chạy chương trình ứng dụng). Cũng lưu ý rằng đám mây là một trong những biểu tượng quan trọng nhất của mạng máy tính. Nói chung, chúng ta dùng đám mây để biểu diễn bất kỳ loại mạng nào, dù là một liên kết point-to-point, một liên kết multiple-access hay một mạng chuyển mạch. Do đó, bất cứ khi nào bạn thấy một đám mây trong hình, bạn có thể coi nó là đại diện cho bất kỳ công nghệ mạng nào được đề cập trong cuốn sách này.\ [#]_

.. [#] Việc sử dụng đám mây để biểu diễn mạng có trước thuật ngữ *cloud computing* ít nhất vài thập kỷ, nhưng ngày càng có nhiều mối liên hệ phong phú giữa hai cách dùng này, sẽ được chúng tôi bàn trong phần *Perspective* ở cuối mỗi chương.

.. _fig-internet-cloud:
.. figure:: figures/f01-04-9780123850591.png
   :width: 500px
   :align: center

   Kết nối các mạng.

Một cách thứ hai để một tập hợp máy tính có thể được kết nối gián tiếp được minh họa trong :numref:`Hình %s <fig-internet-cloud>`. Trong trường hợp này, một tập hợp các mạng độc lập (đám mây) được kết nối với nhau để tạo thành một *internetwork*, hay gọi tắt là internet. Chúng tôi tuân theo quy ước của Internet khi gọi một mạng liên kết các mạng là internet viết thường, còn Internet TCP/IP mà chúng ta sử dụng hàng ngày là Internet viết hoa. Một nút được kết nối với hai hoặc nhiều mạng thường được gọi là *router* hoặc *gateway*, và nó đóng vai trò tương tự như switch—chuyển tiếp thông điệp từ mạng này sang mạng khác. Lưu ý rằng một internet cũng có thể được coi là một loại mạng khác, nghĩa là một internet có thể được xây dựng từ một tập hợp các internet. Do đó, chúng ta có thể xây dựng các mạng có quy mô bất kỳ bằng cách kết nối các đám mây để tạo thành các đám mây lớn hơn. Có thể nói rằng ý tưởng kết nối các mạng rất khác nhau là đổi mới nền tảng của Internet và sự phát triển thành công của Internet đến quy mô toàn cầu và hàng tỷ nút là kết quả của những quyết định thiết kế rất tốt của các kiến trúc sư Internet ban đầu, sẽ được bàn sau.

Chỉ vì một tập hợp host được kết nối trực tiếp hoặc gián tiếp với nhau không có nghĩa là chúng ta đã cung cấp thành công kết nối host-to-host. Yêu cầu cuối cùng là mỗi nút phải có thể xác định nút nào khác trên mạng mà nó muốn giao tiếp. Điều này được thực hiện bằng cách gán một *địa chỉ* cho mỗi nút. Địa chỉ là một chuỗi byte xác định một nút; tức là, mạng có thể sử dụng địa chỉ của một nút để phân biệt nó với các nút khác được kết nối với mạng. Khi một nút nguồn muốn mạng chuyển một thông điệp đến một nút đích nhất định, nó chỉ định địa chỉ của nút đích. Nếu các nút gửi và nhận không được kết nối trực tiếp, các switch và router của mạng sẽ sử dụng địa chỉ này để quyết định cách chuyển tiếp thông điệp đến đích. Quá trình xác định một cách hệ thống cách chuyển tiếp thông điệp dựa trên địa chỉ đích gọi là *định tuyến* (routing).

Giới thiệu ngắn gọn về địa chỉ và định tuyến này giả định rằng nút nguồn muốn gửi thông điệp đến một nút đích duy nhất (*unicast*). Mặc dù đây là trường hợp phổ biến nhất, cũng có thể nút nguồn muốn *broadcast* một thông điệp đến tất cả các nút trên mạng. Hoặc, nút nguồn muốn gửi thông điệp đến một số nút con trong mạng nhưng không phải tất cả, gọi là *multicast*. Do đó, ngoài địa chỉ riêng cho từng nút, một yêu cầu khác của mạng là hỗ trợ địa chỉ multicast và broadcast.

.. _key-nested:
.. admonition:: Ý chính

  Ý chính cần rút ra từ phần này là chúng ta có thể định nghĩa một *mạng* một cách đệ quy là gồm hai hoặc nhiều nút được kết nối bằng một liên kết vật lý, hoặc hai hoặc nhiều mạng được kết nối bằng một nút. Nói cách khác, một mạng có thể được xây dựng từ sự lồng ghép các mạng, trong đó ở cấp thấp nhất, mạng được triển khai bằng một phương tiện vật lý nào đó. Một trong những thách thức then chốt khi cung cấp kết nối mạng là xác định địa chỉ cho mỗi nút có thể truy cập trên mạng (dù là logic hay vật lý), và sử dụng các địa chỉ đó để chuyển tiếp thông điệp đến nút đích phù hợp. :ref:`[Tiếp theo] <key-stat-mux>`

1.2.3 Chia sẻ tài nguyên hiệu quả về chi phí
--------------------------------------------

Như đã nêu ở trên, cuốn sách này tập trung vào các mạng chuyển mạch gói. Phần này giải thích yêu cầu then chốt của mạng máy tính—hiệu quả—dẫn chúng ta đến chuyển mạch gói như là chiến lược lựa chọn.

Với một tập hợp các nút được kết nối gián tiếp qua các mạng lồng ghép, bất kỳ cặp host nào cũng có thể gửi thông điệp cho nhau qua một chuỗi các liên kết và nút. Tất nhiên, chúng ta muốn làm nhiều hơn là chỉ hỗ trợ một cặp host giao tiếp—chúng ta muốn cung cấp cho tất cả các cặp host khả năng trao đổi thông điệp. Vậy câu hỏi đặt ra là làm thế nào để tất cả các host muốn giao tiếp có thể chia sẻ mạng, đặc biệt là khi họ muốn sử dụng cùng lúc? Và, như thể vấn đề đó chưa đủ khó, làm thế nào để nhiều host chia sẻ cùng một *liên kết* khi tất cả đều muốn sử dụng cùng lúc?

Để hiểu cách các host chia sẻ một mạng, chúng ta cần giới thiệu một khái niệm cơ bản, *ghép kênh* (multiplexing), nghĩa là một tài nguyên hệ thống được chia sẻ cho nhiều người dùng. Ở mức trực quan, ghép kênh có thể được giải thích bằng phép so sánh với hệ thống máy tính chia sẻ thời gian, nơi một bộ xử lý vật lý duy nhất được chia sẻ (ghép kênh) cho nhiều công việc, mỗi công việc đều nghĩ rằng mình có bộ xử lý riêng. Tương tự, dữ liệu được gửi bởi nhiều người dùng có thể được ghép kênh qua các liên kết vật lý tạo nên một mạng.

Để thấy điều này hoạt động như thế nào, hãy xem mạng đơn giản minh họa trong :numref:`Hình %s <fig-mux>`, nơi ba host ở phía bên trái mạng (gửi S1-S3) đang gửi dữ liệu đến ba host ở phía bên phải (nhận R1-R3) bằng cách chia sẻ một mạng chuyển mạch chỉ có một liên kết vật lý. (Để đơn giản, giả sử host S1 gửi dữ liệu cho R1, v.v.) Trong trường hợp này, ba luồng dữ liệu—tương ứng với ba cặp host—được ghép kênh lên một liên kết vật lý duy nhất bởi switch 1 và sau đó được *tách kênh* (demultiplexed) trở lại thành các luồng riêng biệt bởi switch 2. Lưu ý rằng chúng ta cố tình không xác định chính xác “luồng dữ liệu” là gì. Trong phạm vi thảo luận này, giả sử mỗi host bên trái có một lượng lớn dữ liệu muốn gửi cho đối tác bên phải.

.. _fig-mux:
.. figure:: figures/f01-05-9780123850591.png
   :width: 500px
   :align: center

   Ghép kênh nhiều luồng logic lên một liên kết vật lý.

Có một số phương pháp khác nhau để ghép kênh nhiều luồng lên một liên kết vật lý. Một phương pháp phổ biến là *ghép kênh phân chia theo thời gian đồng bộ* (STDM). Ý tưởng của STDM là chia thời gian thành các khoảng bằng nhau và, theo vòng tròn, cho mỗi luồng một cơ hội gửi dữ liệu qua liên kết vật lý. Nói cách khác, trong khoảng thời gian 1, dữ liệu từ S1 đến R1 được truyền; trong khoảng thời gian 2, dữ liệu từ S2 đến R2 được truyền; ở khoảng 3, S3 gửi dữ liệu đến R3. Sau đó, luồng đầu tiên (S1 đến R1) lại được truyền, và quá trình lặp lại. Một phương pháp khác là *ghép kênh phân chia theo tần số* (FDM). Ý tưởng của FDM là truyền mỗi luồng qua liên kết vật lý ở một tần số khác nhau, giống như tín hiệu của các kênh truyền hình khác nhau được truyền ở các tần số khác nhau qua sóng vô tuyến hoặc cáp đồng trục.

Mặc dù dễ hiểu, cả STDM và FDM đều bị giới hạn ở hai điểm. Thứ nhất, nếu một trong các luồng (cặp host) không có dữ liệu để gửi, phần chia sẻ liên kết vật lý của nó—tức là, khoảng thời gian hoặc tần số của nó—vẫn bị bỏ trống, ngay cả khi một trong các luồng khác có dữ liệu cần truyền. Ví dụ, S3 phải chờ đến lượt sau S1 và S2 ở đoạn trước, ngay cả khi S1 và S2 không có gì để gửi. Đối với truyền thông máy tính, thời gian liên kết bị bỏ trống có thể rất lớn—ví dụ, hãy so sánh thời gian bạn đọc một trang web (liên kết bị bỏ trống) với thời gian bạn tải trang đó. Thứ hai, cả STDM và FDM đều bị giới hạn trong các trường hợp số lượng luồng tối đa là cố định và biết trước. Không thực tế để thay đổi kích thước khoảng thời gian hoặc thêm khoảng mới trong STDM, hoặc thêm tần số mới trong FDM.

Dạng ghép kênh giải quyết các hạn chế này, và được sử dụng nhiều nhất trong cuốn sách này, gọi là *ghép kênh thống kê* (statistical multiplexing). Dù tên gọi không giúp hiểu rõ khái niệm, ghép kênh thống kê thực ra khá đơn giản, với hai ý chính. Thứ nhất, nó giống STDM ở chỗ liên kết vật lý được chia sẻ theo thời gian—trước tiên dữ liệu từ một luồng được truyền qua liên kết vật lý, sau đó dữ liệu từ luồng khác, v.v. Tuy nhiên, không giống STDM, dữ liệu được truyền từ mỗi luồng theo nhu cầu thay vì theo một khoảng thời gian định trước. Do đó, nếu chỉ một luồng có dữ liệu để gửi, nó có thể truyền ngay mà không phải chờ đến lượt và không phải nhìn các khoảng thời gian của luồng khác trôi qua mà không dùng đến. Chính việc tránh thời gian nhàn rỗi này tạo nên hiệu quả của chuyển mạch gói.

Tuy nhiên, như đã định nghĩa đến đây, ghép kênh thống kê không có cơ chế đảm bảo tất cả các luồng cuối cùng đều có lượt truyền qua liên kết vật lý. Tức là, khi một luồng bắt đầu gửi dữ liệu, chúng ta cần một cách để giới hạn truyền, để các luồng khác cũng có lượt. Để giải quyết nhu cầu này, ghép kênh thống kê xác định một giới hạn trên về kích thước khối dữ liệu mà mỗi luồng được phép truyền tại một thời điểm. Khối dữ liệu giới hạn kích thước này thường được gọi là *gói* (packet), để phân biệt với *thông điệp* (message) có thể rất lớn mà chương trình ứng dụng muốn truyền. Vì mạng chuyển mạch gói giới hạn kích thước tối đa của gói, một host có thể không gửi được toàn bộ thông điệp trong một gói. Nguồn có thể cần chia nhỏ thông điệp thành nhiều gói, và phía nhận sẽ ghép lại thành thông điệp gốc.

.. _fig-statmux:
.. figure:: figures/f01-06-9780123850591.png
   :width: 500px
   :align: center

   Một switch ghép kênh các gói từ nhiều nguồn lên một liên kết chia sẻ.

Nói cách khác, mỗi luồng gửi một chuỗi các gói qua liên kết vật lý, với quyết định được đưa ra cho từng gói về việc gửi gói của luồng nào tiếp theo. Lưu ý rằng, nếu chỉ một luồng có dữ liệu để gửi, nó có thể gửi liên tiếp nhiều gói; tuy nhiên, nếu nhiều luồng có dữ liệu, các gói của chúng sẽ được xen kẽ trên liên kết. :numref:`Hình %s <fig-statmux>` minh họa một switch ghép kênh các gói từ nhiều nguồn lên một liên kết chia sẻ duy nhất.

Quyết định gửi gói nào tiếp theo trên một liên kết chia sẻ có thể được thực hiện theo nhiều cách khác nhau. Ví dụ, trong một mạng gồm các switch kết nối với nhau bằng các liên kết như trong :numref:`Hình %s <fig-mux>`, quyết định sẽ do switch truyền gói lên liên kết chia sẻ thực hiện. (Như sẽ thấy sau, không phải tất cả mạng chuyển mạch gói đều có switch, và có thể dùng các cơ chế khác để xác định gói nào được truyền tiếp theo.) Mỗi switch trong mạng chuyển mạch gói đưa ra quyết định này một cách độc lập, cho từng gói. Một trong những vấn đề mà nhà thiết kế mạng phải đối mặt là làm sao đưa ra quyết định này một cách công bằng. Ví dụ, một switch có thể được thiết kế để phục vụ các gói theo thứ tự đến trước, phục vụ trước (FIFO). Một cách khác là truyền các gói từ mỗi luồng khác nhau đang gửi dữ liệu qua switch theo vòng tròn. Điều này có thể được thực hiện để đảm bảo một số luồng nhận được một phần băng thông nhất định của liên kết hoặc không bao giờ bị trì hoãn quá lâu trong switch. Một mạng cố gắng phân bổ băng thông cho các luồng cụ thể đôi khi được gọi là hỗ trợ *chất lượng dịch vụ* (QoS).

Cũng lưu ý trong :numref:`Hình %s <fig-statmux>` rằng vì switch phải ghép kênh ba luồng gói vào một liên kết ra, có thể switch sẽ nhận gói nhanh hơn tốc độ liên kết chia sẻ có thể xử lý. Trong trường hợp này, switch buộc phải lưu các gói này vào bộ nhớ. Nếu switch nhận gói nhanh hơn tốc độ gửi trong một thời gian dài, cuối cùng switch sẽ hết bộ nhớ đệm và một số gói sẽ bị loại bỏ. Khi switch hoạt động trong trạng thái này, nó được gọi là *bị nghẽn* (congested).

.. _key-stat-mux:
.. admonition:: Ý chính

  Kết luận là ghép kênh thống kê xác định một cách hiệu quả về chi phí để nhiều người dùng (ví dụ, các luồng dữ liệu host-to-host) chia sẻ tài nguyên mạng (liên kết và nút) ở mức độ chi tiết. Nó xác định gói là đơn vị mà các liên kết mạng được phân bổ cho các luồng khác nhau, với mỗi switch có thể lập lịch sử dụng các liên kết vật lý mà nó kết nối trên cơ sở từng gói. Phân bổ công bằng dung lượng liên kết cho các luồng khác nhau và xử lý nghẽn khi xảy ra là những thách thức then chốt của ghép kênh thống kê. :ref:`[Tiếp theo] <key-semantic-gap>`

1.2.4 Hỗ trợ các dịch vụ chung
------------------------------

Phần trước tập trung vào các thách thức trong việc cung cấp kết nối hiệu quả về chi phí giữa một nhóm host, nhưng thật quá đơn giản nếu chỉ coi mạng máy tính là chuyển các gói giữa một tập hợp máy tính. Chính xác hơn, nên coi mạng là phương tiện để một tập hợp các tiến trình ứng dụng phân tán trên các máy tính đó giao tiếp với nhau. Nói cách khác, yêu cầu tiếp theo của mạng máy tính là các chương trình ứng dụng chạy trên các host kết nối mạng phải có khả năng giao tiếp một cách có ý nghĩa. Từ góc nhìn của nhà phát triển ứng dụng, mạng cần làm cho công việc của họ dễ dàng hơn.

Khi hai chương trình ứng dụng cần giao tiếp với nhau, rất nhiều điều phức tạp phải xảy ra ngoài việc chỉ gửi một thông điệp từ host này sang host khác. Một lựa chọn là các nhà thiết kế ứng dụng xây dựng tất cả chức năng phức tạp đó vào từng chương trình ứng dụng. Tuy nhiên, vì nhiều ứng dụng cần các dịch vụ chung, hợp lý hơn nhiều khi triển khai các dịch vụ chung đó một lần và cho phép nhà thiết kế ứng dụng xây dựng ứng dụng dựa trên các dịch vụ đó. Thách thức cho nhà thiết kế mạng là xác định tập hợp dịch vụ chung phù hợp. Mục tiêu là che giấu sự phức tạp của mạng khỏi ứng dụng mà không ràng buộc quá mức nhà thiết kế ứng dụng.

.. _fig-channel:
.. figure:: figures/f01-07-9780123850591.png
   :width: 500px
   :align: center

   Các tiến trình giao tiếp qua một kênh trừu tượng.

Trực quan, chúng ta coi mạng cung cấp các *kênh logic* (logical channels) mà qua đó các tiến trình cấp ứng dụng có thể giao tiếp với nhau; mỗi kênh cung cấp tập hợp dịch vụ mà ứng dụng đó yêu cầu. Nói cách khác, cũng như chúng ta dùng đám mây để trừu tượng hóa kết nối giữa một tập hợp máy tính, giờ đây chúng ta coi kênh là kết nối giữa hai tiến trình. :numref:`Hình %s <fig-channel>` cho thấy một cặp tiến trình cấp ứng dụng giao tiếp qua một kênh logic, kênh này được triển khai trên một đám mây kết nối một tập hợp host. Ta có thể coi kênh như một ống nối hai ứng dụng, để ứng dụng gửi có thể đưa dữ liệu vào một đầu và mong đợi dữ liệu đó được mạng chuyển đến ứng dụng ở đầu kia của ống.

Như mọi trừu tượng, các kênh logic tiến trình-đến-tiến trình được triển khai trên tập hợp các kênh vật lý host-đến-host. Đây là bản chất của phân lớp (layering), nền tảng của kiến trúc mạng sẽ được bàn ở phần tiếp theo.

Thách thức là nhận ra chức năng nào các kênh nên cung cấp cho chương trình ứng dụng. Ví dụ, ứng dụng có yêu cầu đảm bảo rằng các thông điệp gửi qua kênh sẽ được chuyển giao, hay chấp nhận nếu một số thông điệp không đến? Có cần thiết các thông điệp đến tiến trình nhận theo đúng thứ tự gửi, hay tiến trình nhận không quan tâm đến thứ tự? Mạng có cần đảm bảo không bên thứ ba nào có thể nghe lén kênh, hay bảo mật không phải là vấn đề? Nói chung, một mạng cung cấp nhiều loại kênh khác nhau, mỗi ứng dụng chọn loại phù hợp nhất với nhu cầu của mình. Phần còn lại của mục này minh họa cách suy nghĩ khi xác định các kênh hữu ích.

Xác định các mẫu giao tiếp chung
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Thiết kế các kênh trừu tượng bắt đầu bằng việc hiểu nhu cầu giao tiếp của một tập hợp ứng dụng đại diện, sau đó rút ra các yêu cầu giao tiếp chung, và cuối cùng tích hợp chức năng đáp ứng các yêu cầu này vào mạng.

Một trong những ứng dụng đầu tiên được hỗ trợ trên bất kỳ mạng nào là chương trình truy cập tệp như Giao thức Truyền tệp (FTP) hoặc Hệ thống Tệp Mạng (NFS). Mặc dù nhiều chi tiết khác nhau—ví dụ, toàn bộ tệp được truyền qua mạng hay chỉ từng khối tệp được đọc/ghi tại một thời điểm—thành phần giao tiếp của truy cập tệp từ xa được đặc trưng bởi một cặp tiến trình, một tiến trình yêu cầu đọc hoặc ghi tệp và một tiến trình đáp ứng yêu cầu đó. Tiến trình yêu cầu truy cập tệp gọi là *client*, và tiến trình hỗ trợ truy cập tệp gọi là *server*.

Đọc tệp bao gồm client gửi một thông điệp yêu cầu nhỏ đến server và server phản hồi bằng một thông điệp lớn chứa dữ liệu trong tệp. Ghi tệp thì ngược lại—client gửi một thông điệp lớn chứa dữ liệu cần ghi cho server, và server phản hồi bằng một thông điệp nhỏ xác nhận đã ghi lên đĩa.

Một thư viện số là một ứng dụng phức tạp hơn truyền tệp, nhưng cũng cần các dịch vụ giao tiếp tương tự. Ví dụ, *Hiệp hội Máy tính Hoa Kỳ* (ACM) vận hành một thư viện số lớn về tài liệu khoa học máy tính tại

.. code-block:: html

   http://portal.acm.org/dl.cfm

Thư viện này có nhiều tính năng tìm kiếm và duyệt giúp người dùng tìm bài báo mong muốn, nhưng cuối cùng phần lớn những gì nó làm là đáp ứng yêu cầu của người dùng về các tệp, như bản điện tử của các bài báo khoa học.

Sử dụng truy cập tệp, thư viện số và hai ứng dụng video được mô tả ở phần mở đầu (hội nghị truyền hình và video theo yêu cầu) làm mẫu đại diện, chúng ta có thể quyết định cung cấp hai loại kênh sau: kênh *yêu cầu/đáp ứng* (request/reply) và kênh *luồng thông điệp* (message stream). Kênh yêu cầu/đáp ứng sẽ được dùng bởi các ứng dụng truyền tệp và thư viện số. Nó đảm bảo rằng mỗi thông điệp gửi từ một phía sẽ được phía kia nhận và chỉ một bản sao của mỗi thông điệp được chuyển giao. Kênh yêu cầu/đáp ứng cũng có thể bảo vệ tính riêng tư và toàn vẹn của dữ liệu truyền qua nó, để các bên không được phép không thể đọc hoặc sửa đổi dữ liệu trao đổi giữa client và server.

Kênh luồng thông điệp có thể được dùng cho cả ứng dụng video theo yêu cầu và hội nghị truyền hình, với điều kiện nó được tham số hóa để hỗ trợ cả lưu lượng một chiều và hai chiều cũng như các đặc tính trễ khác nhau. Kênh luồng thông điệp có thể không cần đảm bảo tất cả thông điệp đều được chuyển giao, vì ứng dụng video vẫn hoạt động tốt ngay cả khi một số khung hình không đến. Tuy nhiên, nó cần đảm bảo các thông điệp được chuyển giao đến theo đúng thứ tự gửi, để tránh hiển thị khung hình sai thứ tự. Giống như kênh yêu cầu/đáp ứng, kênh luồng thông điệp cũng có thể cần đảm bảo tính riêng tư và toàn vẹn của dữ liệu video. Cuối cùng, kênh luồng thông điệp có thể cần hỗ trợ multicast, để nhiều bên có thể tham gia hội nghị hoặc xem video.

Mặc dù các nhà thiết kế mạng thường cố gắng tối giản số lượng loại kênh trừu tượng để phục vụ được nhiều ứng dụng nhất, nhưng có nguy cơ nếu quá ít loại kênh. Nói đơn giản, nếu bạn chỉ có một cái búa, mọi thứ đều giống như cái đinh. Ví dụ, nếu bạn chỉ có kênh luồng thông điệp và yêu cầu/đáp ứng, bạn sẽ dễ bị cám dỗ dùng chúng cho mọi ứng dụng mới, ngay cả khi không loại nào cung cấp đúng ngữ nghĩa mà ứng dụng cần. Do đó, các nhà thiết kế mạng có lẽ sẽ còn phát minh ra các loại kênh mới—và bổ sung tùy chọn cho các kênh hiện có—chừng nào các lập trình viên ứng dụng còn phát minh ra ứng dụng mới.

Cũng lưu ý rằng, độc lập với việc một kênh cung cấp chức năng gì, còn có câu hỏi chức năng đó được triển khai ở đâu. Trong nhiều trường hợp, dễ nhất là coi kết nối host-to-host của mạng cơ sở chỉ như một *đường truyền bit* (bit pipe), với mọi ngữ nghĩa giao tiếp cấp cao được cung cấp ở các host đầu cuối. Lợi thế của cách này là giữ cho các switch ở giữa mạng càng đơn giản càng tốt—chúng chỉ chuyển tiếp gói—nhưng yêu cầu các host đầu cuối phải gánh phần lớn gánh nặng hỗ trợ các kênh tiến trình-đến-tiến trình giàu ngữ nghĩa. Cách khác là đẩy thêm chức năng vào các switch, cho phép host đầu cuối là thiết bị “ngu” (ví dụ, điện thoại bàn). Chúng ta sẽ thấy câu hỏi về cách phân chia các dịch vụ mạng giữa switch và host đầu cuối là một vấn đề lặp lại trong thiết kế mạng.

Chuyển giao thông điệp tin cậy
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Như các ví dụ vừa xem xét cho thấy, chuyển giao thông điệp tin cậy là một trong những chức năng quan trọng nhất mà mạng có thể cung cấp. Tuy nhiên, rất khó xác định cách cung cấp độ tin cậy này nếu chưa hiểu mạng có thể gặp lỗi như thế nào. Điều đầu tiên cần nhận ra là mạng máy tính không tồn tại trong một thế giới hoàn hảo. Máy móc bị treo rồi khởi động lại, cáp quang bị đứt, nhiễu điện làm sai bit dữ liệu truyền, switch hết bộ nhớ đệm, và, như thể các vấn đề vật lý này chưa đủ lo lắng, phần mềm quản lý phần cứng có thể có lỗi và đôi khi chuyển tiếp gói vào “hư vô”. Do đó, một yêu cầu lớn của mạng là phục hồi khỏi một số loại lỗi nhất định, để chương trình ứng dụng không phải xử lý hoặc thậm chí không biết về chúng.

Có ba loại lỗi tổng quát mà nhà thiết kế mạng phải quan tâm. Thứ nhất, khi một gói được truyền qua liên kết vật lý, có thể xuất hiện *lỗi bit* (bit errors); tức là, một bit 1 bị chuyển thành 0 hoặc ngược lại. Đôi khi chỉ một bit bị lỗi, nhưng thường là *lỗi chuỗi* (burst error)—nhiều bit liên tiếp bị lỗi. Lỗi bit thường xảy ra do các tác động bên ngoài, như sét đánh, tăng áp, lò vi sóng, gây nhiễu truyền dữ liệu. Tin tốt là lỗi bit khá hiếm, trung bình chỉ ảnh hưởng đến một trên mỗi 10\ :sup:`6` đến 10\ :sup:`7` bit trên cáp đồng điển hình và một trên mỗi 10\ :sup:`12` đến 10\ :sup:`14` bit trên cáp quang điển hình. Như sẽ thấy, có các kỹ thuật phát hiện lỗi bit với xác suất cao. Khi phát hiện, đôi khi có thể sửa lỗi—nếu biết bit nào bị lỗi, chỉ cần lật lại—trong các trường hợp khác, hỏng nặng đến mức phải loại bỏ toàn bộ gói. Khi đó, phía gửi có thể phải gửi lại gói.

Loại lỗi thứ hai là ở cấp gói, không phải bit; tức là, một gói hoàn chỉnh bị mạng làm mất. Một lý do là gói chứa lỗi bit không thể sửa nên phải loại bỏ. Lý do phổ biến hơn là một trong các nút phải xử lý gói—ví dụ, switch chuyển tiếp từ liên kết này sang liên kết khác—bị quá tải đến mức không còn chỗ lưu gói và buộc phải loại bỏ. Đây là vấn đề nghẽn vừa bàn ở trên. Ít phổ biến hơn, phần mềm chạy trên một nút xử lý gói mắc lỗi. Ví dụ, nó có thể chuyển tiếp gói sai liên kết, khiến gói không bao giờ đến đích. Như sẽ thấy, một trong những khó khăn chính khi xử lý gói mất là phân biệt giữa gói thực sự bị mất và gói chỉ đến muộn.

Loại lỗi thứ ba là ở cấp nút và liên kết; tức là, một liên kết vật lý bị đứt, hoặc máy tính kết nối với nó bị treo. Điều này có thể do phần mềm bị treo, mất điện, hoặc do máy xúc đào bất cẩn. Lỗi do cấu hình sai thiết bị mạng cũng phổ biến. Mặc dù bất kỳ lỗi nào trong số này cuối cùng cũng có thể được khắc phục, chúng có thể ảnh hưởng nghiêm trọng đến mạng trong thời gian dài. Tuy nhiên, chúng không nhất thiết làm tê liệt hoàn toàn mạng. Trong mạng chuyển mạch gói, đôi khi có thể định tuyến vòng qua nút hoặc liên kết bị lỗi. Một trong những khó khăn khi xử lý loại lỗi thứ ba là phân biệt giữa máy tính bị hỏng và máy chỉ chậm, hoặc trong trường hợp liên kết, giữa liên kết bị đứt và liên kết rất kém gây nhiều lỗi bit.

.. _key-semantic-gap:
.. admonition:: Ý chính

   Ý chính cần rút ra là việc xác định các kênh hữu ích vừa phải hiểu yêu cầu của ứng dụng vừa phải nhận ra giới hạn của công nghệ nền tảng. Thách thức là lấp đầy khoảng cách giữa những gì ứng dụng mong đợi và những gì công nghệ nền tảng có thể cung cấp. Điều này đôi khi được gọi là *khoảng cách ngữ nghĩa* (semantic gap). :ref:`[Tiếp theo] <key-hourglass>`

1.2.5 Khả năng quản lý
----------------------

Một yêu cầu cuối cùng, thường bị bỏ qua hoặc để đến cuối (như chúng tôi làm ở đây), là mạng cần được quản lý. Quản lý mạng bao gồm nâng cấp thiết bị khi mạng phát triển để phục vụ nhiều lưu lượng hoặc người dùng hơn, khắc phục sự cố khi mạng gặp vấn đề hoặc hiệu năng không như mong muốn, và bổ sung tính năng mới hỗ trợ ứng dụng mới. Quản lý mạng trong lịch sử là một khía cạnh đòi hỏi nhiều nhân lực, và mặc dù khó có thể loại bỏ hoàn toàn con người khỏi quy trình, ngày càng nhiều vấn đề được giải quyết bằng tự động hóa và thiết kế tự phục hồi.

Yêu cầu này phần nào liên quan đến vấn đề khả năng mở rộng đã bàn ở trên—khi Internet mở rộng để hỗ trợ hàng tỷ người dùng và ít nhất hàng trăm triệu host, thách thức giữ cho toàn bộ hệ thống hoạt động đúng và cấu hình đúng thiết bị mới khi thêm vào ngày càng trở nên nan giải. Cấu hình một router trong mạng thường là công việc của chuyên gia; cấu hình hàng nghìn router và tìm ra lý do mạng lớn như vậy không hoạt động như mong đợi có thể vượt quá khả năng của bất kỳ cá nhân nào. Đó là lý do tại sao tự động hóa ngày càng quan trọng.

Một cách để làm cho mạng dễ quản lý hơn là tránh thay đổi. Khi mạng đã hoạt động, chỉ cần *không động vào nó!* Tư duy này bộc lộ mâu thuẫn cơ bản giữa *ổn định* (stability) và *tốc độ bổ sung tính năng* (feature velocity): tốc độ các khả năng mới được đưa vào mạng. Ưu tiên ổn định là cách ngành viễn thông (chưa kể quản trị viên hệ thống đại học và phòng CNTT doanh nghiệp) áp dụng trong nhiều năm, khiến nó trở thành một trong những ngành chậm thay đổi và ngại rủi ro nhất. Nhưng sự bùng nổ của điện toán đám mây gần đây đã thay đổi động lực đó, khiến cần cân bằng hơn giữa ổn định và tốc độ bổ sung tính năng. Tác động của đám mây lên mạng là chủ đề lặp lại trong suốt cuốn sách, và chúng tôi đặc biệt chú ý trong phần *Perspectives* ở cuối mỗi chương. Hiện tại, chỉ cần nói rằng quản lý một mạng phát triển nhanh có lẽ là thách thức trung tâm của mạng ngày nay.