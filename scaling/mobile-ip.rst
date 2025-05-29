4.5 Định tuyến giữa các thiết bị di động
========================================

Có lẽ sẽ không quá ngạc nhiên khi biết rằng các thiết bị di động đặt ra một số thách thức cho kiến trúc Internet. Internet được thiết kế vào thời kỳ mà máy tính là những thiết bị lớn, cố định, và mặc dù các nhà thiết kế Internet có lẽ đã hình dung rằng các thiết bị di động có thể xuất hiện trong tương lai, nhưng có thể cho rằng việc hỗ trợ chúng không phải là ưu tiên hàng đầu. Ngày nay, tất nhiên, máy tính di động xuất hiện ở khắp mọi nơi, đặc biệt dưới dạng laptop và điện thoại thông minh, và ngày càng nhiều dưới các hình thức khác như drone. Trong phần này, chúng ta sẽ xem xét một số thách thức do sự xuất hiện của thiết bị di động đặt ra và một số cách tiếp cận hiện tại để hỗ trợ chúng.

4.5.1 Thách thức đối với mạng di động
-------------------------------------

Ngày nay, việc xuất hiện tại một điểm phát sóng không dây, kết nối Internet bằng 802.11 hoặc một giao thức mạng không dây nào đó, và có được dịch vụ Internet khá tốt là điều khá dễ dàng. Một công nghệ then chốt giúp điểm phát sóng trở nên khả thi là DHCP. Bạn có thể ngồi ở quán cà phê, mở laptop, nhận địa chỉ IP cho laptop của mình, và để laptop giao tiếp với router mặc định và máy chủ Hệ thống tên miền (DNS), và với một lớp ứng dụng rộng, bạn đã có mọi thứ mình cần.

Tuy nhiên, nếu nhìn kỹ hơn, rõ ràng trong một số kịch bản ứng dụng, chỉ cần nhận một địa chỉ IP mới mỗi khi bạn di chuyển—điều mà DHCP làm cho bạn—không phải lúc nào cũng đủ. Giả sử bạn đang sử dụng laptop hoặc điện thoại thông minh để thực hiện một cuộc gọi thoại qua IP (VoIP), và trong khi đang nói chuyện, bạn di chuyển từ điểm phát sóng này sang điểm phát sóng khác, hoặc thậm chí chuyển từ Wi-Fi sang mạng di động cho kết nối Internet của mình.

Rõ ràng, khi bạn di chuyển từ một mạng truy cập này sang mạng khác, bạn cần nhận một địa chỉ IP mới—một địa chỉ tương ứng với mạng mới. Nhưng, máy tính hoặc điện thoại ở đầu bên kia cuộc gọi của bạn không ngay lập tức biết bạn đã di chuyển đến đâu hoặc địa chỉ IP mới của bạn là gì. Do đó, nếu không có một cơ chế nào khác, các gói tin sẽ tiếp tục được gửi đến địa chỉ nơi bạn *đã từng* ở, chứ không phải nơi bạn đang ở bây giờ. Vấn đề này được minh họa trong :numref:`Hình %s <fig-mobileeg>`; khi nút di động di chuyển từ mạng 802.11 trong :numref:`Hình %s(a) <fig-mobileeg>` sang mạng di động trong :numref:`Hình %s(b) <fig-mobileeg>`, bằng cách nào đó các gói tin từ *nút tương ứng* cần phải tìm đường đến mạng mới và sau đó đến nút di động.

.. _fig-mobileeg:
.. figure:: figures/f04-26-22092018.png
   :width: 500px
   :align: center

   Chuyển tiếp gói tin từ một nút tương ứng đến một nút di động.

Có rất nhiều cách khác nhau để giải quyết vấn đề vừa mô tả, và chúng ta sẽ xem xét một số cách dưới đây. Giả sử rằng có một cách nào đó để chuyển hướng các gói tin sao cho chúng đến địa chỉ mới của bạn thay vì địa chỉ cũ, thì các vấn đề tiếp theo liên quan ngay lập tức đến bảo mật. Ví dụ, nếu có một cơ chế cho phép tôi nói rằng, “Địa chỉ IP mới của tôi là X,” làm thế nào để ngăn một kẻ tấn công đưa ra tuyên bố như vậy mà không có sự cho phép của tôi, từ đó cho phép hắn nhận các gói tin của tôi, hoặc chuyển hướng các gói tin của tôi đến một bên thứ ba không hay biết? Như vậy, chúng ta thấy rằng bảo mật và di động có liên quan khá chặt chẽ với nhau.

Một vấn đề mà thảo luận trên làm nổi bật là thực tế địa chỉ IP thực sự phục vụ hai nhiệm vụ. Chúng được dùng như một *định danh* cho một đầu cuối, và chúng cũng được dùng để *định vị* đầu cuối đó. Hãy coi định danh như một tên tồn tại lâu dài cho đầu cuối, và định vị như một thông tin có thể tạm thời hơn về cách chuyển tiếp gói tin đến đầu cuối đó. Miễn là các thiết bị không di chuyển, hoặc không di chuyển thường xuyên, việc dùng một địa chỉ cho cả hai mục đích này có vẻ khá hợp lý. Nhưng một khi các thiết bị bắt đầu di chuyển, bạn sẽ muốn có một định danh không thay đổi khi di chuyển—đôi khi gọi là *Định danh đầu cuối* hoặc *Định danh host*—và một *định vị* riêng biệt. Ý tưởng tách biệt định vị khỏi định danh đã xuất hiện từ lâu, và hầu hết các cách tiếp cận xử lý di động mô tả dưới đây đều cung cấp sự tách biệt này dưới một hình thức nào đó.

Giả định rằng địa chỉ IP không thay đổi xuất hiện ở nhiều nơi khác nhau. Ví dụ, các giao thức vận chuyển như TCP trong lịch sử đã giả định rằng địa chỉ IP giữ nguyên trong suốt vòng đời của một kết nối, vì vậy một cách tiếp cận có thể là thiết kế lại các giao thức vận chuyển để chúng có thể hoạt động với địa chỉ đầu cuối thay đổi.

Nhưng thay vì cố gắng thay đổi TCP, một lựa chọn phổ biến là để ứng dụng định kỳ thiết lập lại kết nối TCP trong trường hợp địa chỉ IP của client đã thay đổi. Nghe có vẻ lạ, nhưng nếu ứng dụng dựa trên HTTP (ví dụ, trình duyệt web như Chrome hoặc ứng dụng streaming như Netflix) thì đó chính xác là những gì đang diễn ra. Nói cách khác, chiến lược là để ứng dụng tự xử lý các tình huống khi địa chỉ IP của người dùng có thể thay đổi, thay vì cố gắng duy trì ảo tưởng rằng nó không thay đổi.

Mặc dù chúng ta đều quen thuộc với các đầu cuối di chuyển, cũng cần lưu ý rằng router cũng có thể di chuyển. Điều này chắc chắn ít phổ biến hơn di động đầu cuối ngày nay, nhưng có nhiều môi trường mà một router di động có thể hợp lý. Một ví dụ có thể là một đội phản ứng khẩn cấp cố gắng triển khai một mạng sau khi một thảm họa thiên nhiên đã phá hủy toàn bộ hạ tầng cố định. Có thêm các cân nhắc khi *tất cả* các nút trong một mạng, không chỉ các đầu cuối, đều di động, chủ đề này sẽ được bàn sau trong phần này.

Trước khi chúng ta xem xét một số cách tiếp cận hỗ trợ thiết bị di động, cần làm rõ một số điểm. Người ta thường nhầm lẫn giữa mạng không dây và di động. Rốt cuộc, di động và không dây thường đi cùng nhau vì lý do hiển nhiên. Nhưng truyền thông không dây thực chất là truyền dữ liệu từ A đến B mà không cần dây, trong khi di động là xử lý những gì xảy ra khi một nút di chuyển trong khi truyền thông. Rõ ràng, nhiều nút sử dụng kênh truyền không dây nhưng không di động, và đôi khi các nút di động sẽ sử dụng truyền thông có dây (dù điều này ít phổ biến hơn).

Cuối cùng, trong chương này chúng ta chủ yếu quan tâm đến cái gọi là *di động tầng mạng*. Tức là, chúng ta quan tâm đến cách xử lý các nút di chuyển từ mạng này sang mạng khác. Việc di chuyển từ điểm truy cập này sang điểm truy cập khác trong cùng một mạng 802.11 có thể được xử lý bằng các cơ chế riêng của 802.11, và mạng di động cũng có các cách xử lý di động, nhưng trong các hệ thống dị thể lớn như Internet, chúng ta cần hỗ trợ di động rộng hơn giữa các mạng.

4.5.2 Định tuyến đến các host di động (Mobile IP)
--------------------------------------------------

Mobile IP là cơ chế chính trong kiến trúc Internet ngày nay để giải quyết vấn đề định tuyến các gói tin đến các host di động. Nó bổ sung một vài khả năng mới nhưng không yêu cầu thay đổi gì từ các host không di động hoặc hầu hết các router—do đó có thể triển khai từng bước.

Host di động được giả định có một địa chỉ IP cố định, gọi là *địa chỉ nhà* (home address), với prefix mạng trùng với *mạng nhà* (home network) của nó. Đây là địa chỉ mà các host khác sẽ dùng khi gửi gói tin ban đầu đến host di động; vì nó không thay đổi, nó có thể được các ứng dụng lâu dài sử dụng khi host di chuyển. Ta có thể coi đây là định danh lâu dài của host.

Khi host di chuyển đến một mạng ngoài mạng nhà, nó thường nhận một địa chỉ mới trên mạng đó bằng một cách nào đó như DHCP. Địa chỉ này sẽ thay đổi mỗi khi host di chuyển sang mạng mới, nên ta có thể coi nó giống như định vị cho host, nhưng điều quan trọng là host không mất địa chỉ nhà cố định khi nhận địa chỉ mới trên mạng ngoài. Địa chỉ nhà này rất quan trọng để duy trì liên lạc khi di chuyển, như sẽ thấy dưới đây.

   Vì DHCP được phát triển cùng thời với Mobile IP, các tiêu chuẩn Mobile IP ban đầu không yêu cầu DHCP, nhưng ngày nay DHCP đã phổ biến.

Trong khi phần lớn các router không thay đổi, hỗ trợ di động đòi hỏi một số chức năng mới ở ít nhất một router, gọi là *đại diện nhà* (home agent) của nút di động. Router này nằm trên mạng nhà của host di động. Trong một số trường hợp, một router thứ hai với chức năng mở rộng, gọi là *đại diện ngoài* (foreign agent), cũng cần thiết. Router này nằm trên mạng mà nút di động kết nối khi rời mạng nhà. Trước tiên, chúng ta sẽ xem xét hoạt động của Mobile IP khi có sử dụng đại diện ngoài. Một mạng ví dụ với cả đại diện nhà và đại diện ngoài được minh họa trong :numref:`Hình %s <fig-mobile>`.

.. _fig-mobile:
.. figure:: figures/f04-27-9780123850591.png
   :width: 500px
   :align: center

   Host di động và các đại diện di động.

Cả đại diện nhà và đại diện ngoài đều định kỳ thông báo sự hiện diện của mình trên các mạng mà chúng kết nối bằng các thông điệp quảng bá đại diện. Một host di động cũng có thể chủ động yêu cầu quảng bá khi nó kết nối vào một mạng mới. Quảng bá của đại diện nhà cho phép host di động biết địa chỉ của đại diện nhà trước khi rời mạng nhà. Khi host di động kết nối vào mạng ngoài, nó nhận được quảng bá từ đại diện ngoài và đăng ký với đại diện này, cung cấp địa chỉ của đại diện nhà. Đại diện ngoài sau đó liên hệ với đại diện nhà, cung cấp một *địa chỉ care-of*. Đây thường là địa chỉ IP của đại diện ngoài.

Tại thời điểm này, ta thấy rằng bất kỳ host nào cố gửi gói tin đến host di động sẽ gửi với địa chỉ đích là địa chỉ nhà của nút đó. Chuyển tiếp IP thông thường sẽ khiến gói tin đến mạng nhà của nút di động, nơi đại diện nhà đang ngồi. Như vậy, ta có thể chia bài toán chuyển gói tin đến nút di động thành ba phần:

1. Làm thế nào đại diện nhà chặn được gói tin gửi đến nút di động?

2. Làm thế nào đại diện nhà chuyển tiếp gói tin đó đến đại diện ngoài?

3. Làm thế nào đại diện ngoài chuyển gói tin đến nút di động?

Vấn đề đầu tiên có vẻ dễ nếu chỉ nhìn vào :numref:`Hình %s <fig-mobile>`, trong đó đại diện nhà rõ ràng là đường duy nhất giữa host gửi và mạng nhà nên chắc chắn nhận được các gói tin gửi đến nút di động. Nhưng nếu nút gửi (nút tương ứng) nằm trên mạng 18, hoặc nếu có một router khác kết nối với mạng 18 cố chuyển gói tin mà không qua đại diện nhà thì sao? Để giải quyết vấn đề này, đại diện nhà thực sự giả mạo nút di động, sử dụng kỹ thuật gọi là *proxy ARP*. Điều này hoạt động giống như Giao thức phân giải địa chỉ (ARP), ngoại trừ việc đại diện nhà chèn địa chỉ IP của nút di động, thay vì của chính nó, vào các thông điệp ARP. Nó dùng địa chỉ phần cứng của chính mình, để tất cả các nút trên cùng mạng học cách gán địa chỉ phần cứng của đại diện nhà với địa chỉ IP của nút di động. Một điểm tinh tế của quá trình này là thông tin ARP có thể được cache ở các nút khác trên mạng. Để đảm bảo các cache này bị vô hiệu hóa kịp thời, đại diện nhà phát đi một thông điệp ARP ngay khi nút di động đăng ký với đại diện ngoài. Vì thông điệp ARP này không phải là phản hồi cho một yêu cầu ARP thông thường, nó được gọi là *gratuitous ARP*.

Vấn đề thứ hai là chuyển tiếp gói tin bị chặn đến đại diện ngoài. Ở đây ta sử dụng kỹ thuật tunneling đã mô tả ở nơi khác. Đại diện nhà đơn giản là bọc gói tin bên trong một header IP mới với đích là đại diện ngoài và truyền nó vào liên mạng. Tất cả các router trung gian chỉ thấy một gói IP đích là địa chỉ IP của đại diện ngoài. Nói cách khác, một tunnel IP được thiết lập giữa đại diện nhà và đại diện ngoài, và đại diện nhà chỉ việc thả các gói tin đích là nút di động vào tunnel đó.

Khi một gói tin cuối cùng đến đại diện ngoài, nó sẽ gỡ bỏ header IP phụ và tìm thấy bên trong một gói IP đích là địa chỉ nhà của nút di động. Rõ ràng đại diện ngoài không thể xử lý như một gói IP thông thường vì như vậy nó sẽ gửi ngược về mạng nhà. Thay vào đó, nó phải nhận ra địa chỉ này là của một nút di động đã đăng ký. Nó sẽ chuyển gói tin đến *địa chỉ phần cứng* của nút di động (ví dụ, địa chỉ Ethernet), đã biết trong quá trình đăng ký.

Một nhận xét về các thủ tục này là đại diện ngoài và nút di động có thể nằm cùng một thiết bị; tức là, một nút di động có thể tự thực hiện chức năng đại diện ngoài. Tuy nhiên, để làm được điều này, nút di động phải có khả năng động nhận một địa chỉ IP thuộc không gian địa chỉ của mạng ngoài (ví dụ, dùng DHCP). Địa chỉ này sẽ được dùng làm địa chỉ care-of. Trong ví dụ, địa chỉ này sẽ có số mạng là 12. Cách tiếp cận này có ưu điểm là cho phép nút di động kết nối vào các mạng không có đại diện ngoài; do đó, di động có thể đạt được chỉ với việc bổ sung đại diện nhà và phần mềm mới trên nút di động (giả sử mạng ngoài có DHCP).

Vậy còn lưu lượng theo chiều ngược lại (tức là từ nút di động đến nút cố định)? Điều này hóa ra dễ hơn nhiều. Nút di động chỉ cần đặt địa chỉ IP của nút cố định vào trường đích của các gói IP, đồng thời đặt địa chỉ nhà vào trường nguồn, và các gói tin sẽ được chuyển tiếp đến nút cố định theo cách thông thường. Tất nhiên, nếu cả hai nút trong một phiên liên lạc đều di động, thì các thủ tục mô tả ở trên sẽ được dùng cho cả hai chiều.

Tối ưu hóa định tuyến trong Mobile IP
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Có một nhược điểm lớn trong cách tiếp cận trên: Đường đi từ nút tương ứng đến nút di động có thể rất không tối ưu. Một ví dụ cực đoan là khi nút di động và nút tương ứng ở cùng một mạng, nhưng mạng nhà của nút di động lại ở phía bên kia Internet. Nút tương ứng gửi tất cả các gói đến mạng nhà; chúng đi qua Internet để đến đại diện nhà, rồi lại được tunnel ngược qua Internet để đến đại diện ngoài. Rõ ràng, sẽ tốt hơn nếu nút tương ứng biết rằng nút di động thực sự ở cùng mạng và chuyển gói tin trực tiếp. Trong trường hợp tổng quát hơn, mục tiêu là chuyển gói tin càng trực tiếp càng tốt từ nút tương ứng đến nút di động mà không qua đại diện nhà. Điều này đôi khi được gọi là *vấn đề định tuyến tam giác* vì đường đi từ nút tương ứng đến nút di động qua đại diện nhà đi theo hai cạnh của tam giác, thay vì cạnh thứ ba là đường đi trực tiếp.

Ý tưởng cơ bản để giải quyết định tuyến tam giác là cho nút tương ứng biết địa chỉ care-of của nút di động. Nút tương ứng sau đó có thể tự tạo tunnel đến đại diện ngoài. Đây được coi là một tối ưu hóa của quá trình vừa mô tả. Nếu nút gửi được trang bị phần mềm cần thiết để học địa chỉ care-of và tự tạo tunnel, thì đường đi có thể được tối ưu hóa; nếu không, các gói tin sẽ đi theo đường không tối ưu.

Khi đại diện nhà thấy một gói tin gửi đến một trong các nút di động mà nó hỗ trợ, nó có thể suy ra rằng nút gửi không dùng đường đi tối ưu. Do đó, nó gửi một thông điệp “cập nhật binding” ngược lại cho nguồn gửi, đồng thời chuyển tiếp gói dữ liệu đến đại diện ngoài. Nguồn gửi, nếu có khả năng, sẽ dùng cập nhật binding này để tạo một mục trong *binding cache*, gồm danh sách ánh xạ từ địa chỉ nút di động đến địa chỉ care-of. Lần tới khi nguồn gửi có gói dữ liệu gửi đến nút di động đó, nó sẽ tìm thấy binding trong cache và có thể tunnel gói tin trực tiếp đến đại diện ngoài.

Có một vấn đề rõ ràng với sơ đồ này, đó là binding cache có thể bị lỗi thời nếu host di động chuyển sang mạng mới. Nếu một mục cache lỗi thời được dùng, đại diện ngoài sẽ nhận các gói tunnel cho một nút di động không còn đăng ký trên mạng của nó. Trong trường hợp này, nó gửi một thông điệp *cảnh báo binding* ngược lại cho nguồn gửi để yêu cầu ngừng dùng mục cache này. Sơ đồ này chỉ hoạt động khi đại diện ngoài không phải là chính nút di động. Vì lý do này, các mục cache cần bị xóa sau một khoảng thời gian; thời gian cụ thể được chỉ định trong thông điệp cập nhật binding.

Như đã nói ở trên, định tuyến di động đặt ra một số thách thức bảo mật thú vị, điều này càng rõ ràng hơn khi ta thấy cách Mobile IP hoạt động. Ví dụ, một kẻ tấn công muốn chặn các gói tin gửi đến một nút khác trong liên mạng có thể liên hệ với đại diện nhà của nút đó và tự nhận mình là đại diện ngoài mới cho nút đó. Do đó, rõ ràng là cần có các cơ chế xác thực.

Di động trong IPv6
~~~~~~~~~~~~~~~~~~

Có một số khác biệt đáng kể giữa hỗ trợ di động trong IPv4 và IPv6. Quan trọng nhất, có thể xây dựng hỗ trợ di động vào các tiêu chuẩn của IPv6 ngay từ đầu, do đó giảm bớt nhiều vấn đề triển khai từng bước. (Có thể nói đúng hơn là IPv6 chính là một vấn đề triển khai từng bước lớn, mà khi giải quyết xong sẽ cung cấp hỗ trợ di động như một phần của gói.)

Vì tất cả các host hỗ trợ IPv6 đều có thể nhận địa chỉ bất cứ khi nào chúng kết nối vào mạng ngoài (bằng nhiều cơ chế được định nghĩa trong các đặc tả lõi của v6), Mobile IPv6 loại bỏ đại diện ngoài và tích hợp các khả năng cần thiết để đóng vai trò đại diện ngoài vào mọi host.

Một điểm thú vị khác của IPv6 liên quan đến Mobile IP là việc tích hợp một tập hợp linh hoạt các header mở rộng, như đã mô tả ở phần khác trong chương này. Điều này được sử dụng trong kịch bản định tuyến tối ưu mô tả ở trên. Thay vì *tunnel* một gói tin đến nút di động tại địa chỉ care-of, một nút IPv6 có thể gửi một gói IP đến địa chỉ care-of với địa chỉ nhà nằm trong một *header định tuyến*. Header này bị các nút trung gian bỏ qua, nhưng cho phép nút di động xử lý gói tin như thể nó được gửi đến địa chỉ nhà, từ đó cho phép nó tiếp tục cung cấp cho các giao thức tầng cao hơn ảo tưởng rằng địa chỉ IP của nó là cố định. Việc sử dụng header mở rộng thay vì tunnel hiệu quả hơn cả về băng thông lẫn xử lý.

Cuối cùng, cần lưu ý rằng vẫn còn nhiều vấn đề mở trong mạng di động. Quản lý tiêu thụ năng lượng của thiết bị di động ngày càng quan trọng, để có thể xây dựng các thiết bị nhỏ với nguồn pin hạn chế. Cũng có vấn đề về mạng di động *ad hoc*—cho phép một nhóm nút di động tạo thành một mạng mà không cần nút cố định nào—với nhiều thách thức riêng. Một lớp mạng di động đặc biệt thách thức là *mạng cảm biến*. Cảm biến thường nhỏ, rẻ, và thường dùng pin, nghĩa là các vấn đề về tiêu thụ năng lượng cực thấp và khả năng xử lý hạn chế cũng phải được cân nhắc. Hơn nữa, vì truyền thông không dây và di động thường đi cùng nhau, các tiến bộ liên tục trong công nghệ không dây tiếp tục tạo ra những thách thức và cơ hội mới cho mạng di động.
