3.3 Internet (IP)
=================

Trong phần trước, chúng ta đã thấy rằng có thể xây dựng các LAN khá lớn bằng cách sử dụng bridge và switch LAN, nhưng các phương pháp này bị giới hạn về khả năng mở rộng và xử lý dị chủng. Trong phần này, chúng ta sẽ khám phá một số cách để vượt qua các giới hạn của mạng bridge, cho phép xây dựng các mạng lớn, dị chủng cao với khả năng định tuyến hợp lý. Chúng ta gọi các mạng như vậy là *liên mạng* (*internetworks*). Chúng ta sẽ tiếp tục thảo luận về cách xây dựng một liên mạng toàn cầu thực sự ở chương tiếp theo, nhưng trước mắt sẽ tìm hiểu các khái niệm cơ bản. Chúng ta bắt đầu bằng cách xem xét kỹ hơn ý nghĩa của từ *internetwork*.

3.3.1 Liên mạng là gì?
----------------------

Chúng ta sử dụng thuật ngữ *liên mạng* (*internetwork*), hoặc đôi khi chỉ là *internet* với chữ *i* thường, để chỉ một tập hợp bất kỳ các mạng được liên kết với nhau nhằm cung cấp một dịch vụ truyền gói tin từ máy chủ tới máy chủ. Ví dụ, một công ty có nhiều chi nhánh có thể xây dựng một liên mạng riêng bằng cách kết nối các LAN tại các địa điểm khác nhau bằng các liên kết điểm-điểm thuê từ công ty điện thoại. Khi nói về liên mạng toàn cầu được sử dụng rộng rãi mà phần lớn các mạng hiện nay đều kết nối tới, chúng ta gọi nó là *Internet* với chữ *I* hoa. Theo cách tiếp cận nguyên lý đầu tiên của cuốn sách này, chúng tôi chủ yếu muốn bạn học về các nguyên lý của liên mạng “chữ *i* thường”, nhưng sẽ minh họa các ý tưởng này bằng các ví dụ thực tế từ Internet “chữ *I* lớn”.

Một thuật ngữ khác cũng dễ gây nhầm lẫn là sự khác biệt giữa mạng, mạng con (subnetworks), và liên mạng. Chúng ta sẽ tránh đề cập đến mạng con (hoặc subnet) cho đến phần sau. Hiện tại, chúng ta dùng *mạng* để chỉ một mạng kết nối trực tiếp hoặc một mạng chuyển mạch như đã mô tả ở phần trước và chương trước. Một mạng như vậy sử dụng một công nghệ, ví dụ như 802.11 hoặc Ethernet. Một *liên mạng* là một tập hợp các mạng như vậy được liên kết với nhau. Đôi khi, để tránh nhầm lẫn, chúng ta gọi các mạng cơ sở mà chúng ta đang liên kết là *mạng vật lý*. Một internet là một *mạng logic* được xây dựng từ một tập hợp các mạng vật lý. Trong bối cảnh này, một tập hợp các đoạn Ethernet được kết nối bằng bridge hoặc switch vẫn được coi là một mạng duy nhất.

.. _fig-inet:
.. figure:: figures/f03-14-9780123850591.png
   :width: 500px
   :align: center

   Một liên mạng đơn giản. H là máy chủ, R là bộ định tuyến.

:numref:`Hình %s <fig-inet>` minh họa một ví dụ về liên mạng. Một liên mạng thường được gọi là “mạng của các mạng” vì nó được tạo thành từ nhiều mạng nhỏ hơn. Trong hình này, ta thấy các mạng Ethernet, một mạng không dây, và một liên kết điểm-điểm. Mỗi cái là một mạng đơn công nghệ. Các nút kết nối các mạng này được gọi là *bộ định tuyến* (*routers*). Chúng đôi khi cũng được gọi là *gateway*, nhưng vì thuật ngữ này có nhiều nghĩa khác, chúng ta sẽ chỉ dùng từ router.

.. _fig-ip-graph:
.. figure:: figures/f03-15-9780123850591.png
   :width: 600px
   :align: center

   Một liên mạng đơn giản, minh họa các tầng giao thức dùng để kết nối H5 tới H8 trong hình trên. ETH là giao thức chạy trên Ethernet.

*Giao thức Internet* là công cụ chủ chốt được sử dụng ngày nay để xây dựng các liên mạng dị chủng, khả mở. Ban đầu nó được gọi là giao thức Kahn-Cerf theo tên các nhà phát minh. Một cách để hình dung IP là nó chạy trên tất cả các nút (cả máy chủ và bộ định tuyến) trong một tập hợp các mạng và định nghĩa hạ tầng cho phép các nút và mạng này hoạt động như một liên mạng logic duy nhất. Ví dụ, :numref:`Hình %s <fig-ip-graph>` cho thấy cách các máy chủ H5 và H8 được kết nối logic bởi internet trong :numref:`Hình %s <fig-inet>`, bao gồm đồ thị giao thức chạy trên mỗi nút. Lưu ý rằng các giao thức tầng cao hơn, như TCP và UDP, thường chạy trên IP ở các máy chủ.

Phần còn lại của chương này và chương tiếp theo sẽ nói về các khía cạnh khác nhau của IP. Dù chắc chắn có thể xây dựng một liên mạng mà không dùng IP—và thực tế, trong những ngày đầu của Internet đã có các giải pháp thay thế—IP là trường hợp thú vị nhất để nghiên cứu đơn giản vì quy mô của Internet. Nói cách khác, chỉ có Internet dựa trên IP mới thực sự đối mặt với vấn đề về khả mở. Do đó, nó cung cấp ví dụ điển hình nhất về một giao thức liên mạng khả mở.

.. sidebar:: Mạng tầng 2 vs tầng 3 (L2 vs L3)

   Như đã thấy ở phần trước, một Ethernet có thể được coi là một *liên kết* điểm-điểm kết nối một cặp switch, với một mạng lưới các switch kết nối tạo thành một *Ethernet chuyển mạch*. Cấu hình này còn gọi là *mạng tầng 2* (*L2 Network*).

   Nhưng như chúng ta sẽ thấy ở phần này, một Ethernet (ngay cả khi được cấu hình điểm-điểm thay vì mạng chia sẻ CSMA/CD) có thể được coi là một *mạng* kết nối một cặp bộ định tuyến, với một mạng lưới các bộ định tuyến như vậy tạo thành một Internet. Cấu hình này còn gọi là *mạng tầng 3* (*L3 Network*).

   Điều gây nhầm lẫn là vì một Ethernet điểm-điểm vừa là một liên kết vừa là một mạng (dù là mạng hai nút tầm thường trong trường hợp thứ hai), tùy thuộc vào việc nó kết nối một cặp switch tầng 2 chạy thuật toán cây bao phủ, hay một cặp bộ định tuyến tầng 3 chạy IP (cộng với các giao thức định tuyến sẽ được mô tả sau trong chương này). Tại sao chọn cấu hình này thay vì cấu hình kia? Một phần phụ thuộc vào việc bạn muốn mạng là một miền quảng bá duy nhất (nếu có, chọn L2), và liệu bạn muốn các máy chủ kết nối tới mạng thuộc các mạng khác nhau (nếu có, chọn L3).

   Tin tốt là khi bạn hiểu rõ ý nghĩa của sự song hành này, bạn đã vượt qua một rào cản lớn trong việc làm chủ các mạng chuyển mạch gói hiện đại.

3.3.2 Mô hình dịch vụ
---------------------

Một điểm khởi đầu tốt khi xây dựng một liên mạng là xác định *mô hình dịch vụ* của nó, tức là các dịch vụ từ máy chủ tới máy chủ mà bạn muốn cung cấp. Mối quan tâm chính khi xác định mô hình dịch vụ cho một liên mạng là chúng ta chỉ có thể cung cấp dịch vụ máy chủ tới máy chủ nếu dịch vụ này có thể được cung cấp trên mỗi mạng vật lý bên dưới. Ví dụ, sẽ không có ý nghĩa gì nếu quyết định rằng mô hình dịch vụ liên mạng sẽ đảm bảo chuyển phát mọi gói tin trong 1 ms hoặc ít hơn nếu có các công nghệ mạng bên dưới có thể trì hoãn gói tin tùy ý. Triết lý khi xác định mô hình dịch vụ IP, do đó, là làm cho nó đủ đơn giản để hầu như bất kỳ công nghệ mạng nào có thể xuất hiện trong một liên mạng đều có thể cung cấp dịch vụ cần thiết.

Mô hình dịch vụ IP có thể được coi là gồm hai phần: một sơ đồ địa chỉ, cung cấp cách nhận diện tất cả các máy chủ trong liên mạng, và một mô hình truyền dữ liệu kiểu datagram (không kết nối). Mô hình dịch vụ này đôi khi được gọi là *best effort* (nỗ lực tối đa) vì, dù IP cố gắng hết sức để chuyển phát datagram, nó không đưa ra bất kỳ đảm bảo nào. Chúng ta sẽ hoãn thảo luận về sơ đồ địa chỉ và trước tiên xem xét mô hình truyền dữ liệu.

Truyền datagram
~~~~~~~~~~~~~~~

Datagram IP là nền tảng của Giao thức Internet. Nhớ lại từ phần trước rằng một datagram là một gói tin được gửi theo kiểu không kết nối qua mạng. Mỗi datagram mang đủ thông tin để mạng có thể chuyển tiếp gói tin tới đích đúng; không cần bất kỳ cơ chế thiết lập trước nào để báo cho mạng biết phải làm gì khi gói tin đến. Bạn chỉ cần gửi nó đi, và mạng sẽ cố gắng hết sức để chuyển nó tới đích mong muốn. Phần “nỗ lực tối đa” nghĩa là nếu có sự cố và gói tin bị mất, bị lỗi, bị chuyển nhầm, hoặc bằng cách nào đó không đến được đích, mạng sẽ không làm gì cả—nó đã cố gắng hết sức, và đó là tất cả những gì nó phải làm. Nó không cố gắng phục hồi từ lỗi. Điều này đôi khi được gọi là dịch vụ *không tin cậy*.

Dịch vụ không kết nối, nỗ lực tối đa là dịch vụ đơn giản nhất mà bạn có thể yêu cầu từ một liên mạng, và đó cũng là điểm mạnh lớn nhất của nó. Ví dụ, nếu bạn cung cấp dịch vụ nỗ lực tối đa trên một mạng cung cấp dịch vụ tin cậy, thì cũng tốt—bạn sẽ có một dịch vụ nỗ lực tối đa mà tình cờ luôn chuyển phát gói tin. Ngược lại, nếu bạn có một mô hình dịch vụ tin cậy trên một mạng không tin cậy, bạn sẽ phải bổ sung rất nhiều chức năng vào các bộ định tuyến để bù đắp cho các thiếu sót của mạng bên dưới. Giữ cho các bộ định tuyến càng đơn giản càng tốt là một trong những mục tiêu thiết kế ban đầu của IP.

Khả năng “chạy trên bất cứ thứ gì” của IP thường được coi là một trong những đặc điểm quan trọng nhất của nó. Đáng chú ý là nhiều công nghệ mà IP chạy trên ngày nay chưa tồn tại khi IP được phát minh. Cho đến nay, chưa có công nghệ mạng nào được phát minh mà IP không thể chạy được. Về nguyên tắc, IP có thể chạy trên một mạng truyền thông bằng chim bồ câu.

Dịch vụ nỗ lực tối đa không chỉ có nghĩa là gói tin có thể bị mất. Đôi khi chúng có thể được chuyển phát không theo thứ tự, và đôi khi cùng một gói tin có thể được chuyển phát nhiều lần. Các giao thức tầng cao hơn hoặc ứng dụng chạy trên IP cần nhận thức được tất cả các kiểu lỗi có thể xảy ra này.

Định dạng gói tin
~~~~~~~~~~~~~~~~~

Rõ ràng, một phần quan trọng của mô hình dịch vụ IP là kiểu gói tin có thể được mang. Datagram IP, giống như hầu hết các gói tin, gồm một tiêu đề (header) theo sau là một số byte dữ liệu. Định dạng của tiêu đề được minh họa trong :numref:`Hình %s <fig-iphead>`. Lưu ý rằng chúng tôi đã áp dụng một kiểu biểu diễn gói tin khác với các chương trước. Đó là vì các định dạng gói tin ở tầng liên mạng và cao hơn, nơi chúng ta sẽ tập trung trong các chương tới, gần như luôn được thiết kế để căn chỉnh theo biên 32 bit để đơn giản hóa xử lý trong phần mềm. Do đó, cách biểu diễn phổ biến (dùng trong các RFC của Internet, chẳng hạn) là vẽ chúng như một chuỗi các từ 32 bit. Từ trên cùng là từ được truyền trước, và byte ngoài cùng bên trái của mỗi từ là byte được truyền trước. Trong biểu diễn này, bạn có thể dễ dàng nhận ra các trường có độ dài là bội số của 8 bit. Khi có trường không phải là bội số của 8 bit, bạn có thể xác định độ dài trường bằng cách nhìn vào vị trí bit ở đầu gói tin.

.. _fig-iphead:
.. figure:: figures/f03-16-9780123850591.png
   :width: 450px
   :align: center

   Tiêu đề gói tin IPv4.

Khi xem xét từng trường trong tiêu đề IP, ta thấy rằng mô hình đơn giản về truyền datagram nỗ lực tối đa vẫn có một số tính năng tinh vi. Trường ``Version`` chỉ định phiên bản của IP. Phiên bản IP vẫn được giả định hiện nay là 4, thường gọi là *IPv4*. Lưu ý rằng đặt trường này ngay đầu datagram giúp dễ dàng định nghĩa lại mọi thứ khác trong định dạng gói tin ở các phiên bản sau; phần mềm xử lý tiêu đề bắt đầu bằng cách nhìn vào phiên bản rồi xử lý phần còn lại của gói tin theo định dạng phù hợp. Trường tiếp theo, ``HLen``, chỉ độ dài tiêu đề tính theo từ 32 bit. Khi không có tùy chọn, điều này thường là 5 từ (20 byte). Trường ``TOS`` (type of service) 8 bit đã có nhiều định nghĩa khác nhau qua các năm, nhưng chức năng cơ bản là cho phép các gói tin được xử lý khác nhau tùy theo nhu cầu ứng dụng. Ví dụ, giá trị ``TOS`` có thể quyết định liệu một gói tin có nên được đặt vào hàng đợi đặc biệt nhận độ trễ thấp hay không.

16 bit tiếp theo của tiêu đề chứa trường ``Length`` của datagram, bao gồm cả tiêu đề. Không giống trường ``HLen``, trường ``Length`` tính theo byte chứ không phải từ. Do đó, kích thước tối đa của một datagram IP là 65.535 byte. Tuy nhiên, mạng vật lý mà IP chạy trên đó có thể không hỗ trợ các gói tin dài như vậy. Vì lý do này, IP hỗ trợ quá trình phân mảnh và lắp ráp lại. Từ thứ hai của tiêu đề chứa thông tin về phân mảnh, chi tiết sẽ được trình bày ở phần sau “Phân mảnh và lắp ráp lại”.

Tiếp theo, byte đầu tiên của từ thứ ba trong tiêu đề là trường ``TTL`` (time to live). Tên trường này phản ánh ý nghĩa lịch sử hơn là cách sử dụng hiện nay. Ý định của trường này là bắt các gói tin bị lặp trong các vòng lặp định tuyến và loại bỏ chúng, thay vì để chúng tiêu tốn tài nguyên vô hạn. Ban đầu, ``TTL`` được đặt thành một số giây cụ thể mà gói tin được phép tồn tại, và các bộ định tuyến trên đường đi sẽ giảm trường này cho đến khi nó về 0. Tuy nhiên, vì hiếm khi một gói tin phải chờ tới 1 giây trong một bộ định tuyến, và các bộ định tuyến không có đồng hồ chung, hầu hết các bộ định tuyến chỉ giảm ``TTL`` đi 1 mỗi khi chuyển tiếp gói tin. Do đó, nó trở thành bộ đếm số bước nhảy hơn là bộ đếm thời gian, nhưng vẫn là cách tốt để bắt các gói tin bị kẹt trong vòng lặp định tuyến. Một điểm tinh tế là giá trị khởi tạo trường này do máy gửi đặt: Nếu đặt quá cao, gói tin có thể lặp lại nhiều lần trước khi bị loại bỏ; nếu đặt quá thấp, có thể không đến được đích. Giá trị mặc định hiện nay là 64.

Trường ``Protocol`` đơn giản là một khóa phân kênh xác định giao thức tầng cao hơn mà gói tin IP này nên chuyển tới. Có các giá trị xác định cho TCP (Transmission Control Protocol—6), UDP (User Datagram Protocol—17), và nhiều giao thức khác có thể nằm trên IP trong đồ thị giao thức.

Trường ``Checksum`` được tính bằng cách coi toàn bộ tiêu đề IP là một chuỗi các từ 16 bit, cộng chúng lại bằng toán học bù một, và lấy bù một của kết quả. Do đó, nếu bất kỳ bit nào trong tiêu đề bị lỗi khi truyền, checksum sẽ không đúng khi nhận gói tin. Vì một tiêu đề bị lỗi có thể chứa lỗi ở địa chỉ đích—và do đó có thể đã bị chuyển nhầm—nên hợp lý khi loại bỏ bất kỳ gói tin nào không vượt qua kiểm tra checksum. Lưu ý rằng loại checksum này không có khả năng phát hiện lỗi mạnh như CRC, nhưng dễ tính toán hơn nhiều trong phần mềm.

Hai trường bắt buộc cuối cùng trong tiêu đề là ``SourceAddr`` và ``DestinationAddr`` của gói tin. Trường sau là chìa khóa cho truyền datagram: Mỗi gói tin chứa địa chỉ đầy đủ của đích để các quyết định chuyển tiếp có thể được thực hiện tại mỗi bộ định tuyến. Địa chỉ nguồn là cần thiết để người nhận quyết định có nhận gói tin không và để trả lời. Địa chỉ IP sẽ được thảo luận ở phần sau—hiện tại, điều quan trọng là IP định nghĩa không gian địa chỉ toàn cục riêng, độc lập với bất kỳ mạng vật lý nào mà nó chạy trên đó. Như chúng ta sẽ thấy, đây là một trong những chìa khóa để hỗ trợ dị chủng.

Cuối cùng, có thể có một số tùy chọn ở cuối tiêu đề. Có hay không các tùy chọn này có thể xác định bằng cách kiểm tra trường độ dài tiêu đề (``HLen``). Dù các tùy chọn này khá hiếm khi dùng, một triển khai IP đầy đủ phải xử lý được tất cả.

Phân mảnh và lắp ráp lại
~~~~~~~~~~~~~~~~~~~~~~~~

Một trong những vấn đề khi cung cấp mô hình dịch vụ máy chủ tới máy chủ thống nhất trên một tập hợp dị chủng các mạng là mỗi công nghệ mạng thường có ý tưởng riêng về kích thước gói tin tối đa. Ví dụ, Ethernet cổ điển có thể nhận các gói tin dài tới 1500 byte, nhưng các biến thể hiện đại có thể truyền các gói lớn hơn (jumbo) mang tới 9000 byte dữ liệu. Điều này để lại hai lựa chọn cho mô hình dịch vụ IP: Đảm bảo rằng mọi datagram IP đều đủ nhỏ để vừa trong một gói tin trên bất kỳ công nghệ mạng nào, hoặc cung cấp một cơ chế để các gói tin có thể bị phân mảnh và lắp ráp lại khi chúng quá lớn để truyền qua một công nghệ mạng nhất định. Lựa chọn thứ hai hóa ra là tốt, đặc biệt khi bạn cân nhắc rằng các công nghệ mạng mới luôn xuất hiện, và IP cần chạy trên tất cả; điều này sẽ khiến việc chọn một giới hạn nhỏ phù hợp cho kích thước datagram trở nên khó khăn. Điều này cũng có nghĩa là máy chủ sẽ không gửi các gói tin nhỏ không cần thiết, gây lãng phí băng thông và tiêu tốn tài nguyên xử lý do phải có nhiều tiêu đề hơn cho mỗi byte dữ liệu gửi đi.

Ý tưởng trung tâm ở đây là mỗi loại mạng đều có một *đơn vị truyền tối đa* (MTU), là datagram IP lớn nhất mà nó có thể mang trong một frame. Lưu ý rằng giá trị này nhỏ hơn kích thước gói tin lớn nhất trên mạng vì datagram IP cần vừa trong *payload* của frame tầng liên kết.

.. [#] Trong mạng ATM, MTU may mắn là lớn hơn nhiều so với một cell, vì ATM có cơ chế phân mảnh và lắp ráp riêng. Frame tầng liên kết trong ATM gọi là *convergence-sublayer protocol data unit* (CS-PDU).

Khi một máy chủ gửi một datagram IP, nó có thể chọn bất kỳ kích thước nào nó muốn. Một lựa chọn hợp lý là MTU của mạng mà máy chủ kết nối trực tiếp. Khi đó, phân mảnh chỉ cần thiết nếu đường đi tới đích có mạng với MTU nhỏ hơn. Nếu giao thức tầng vận chuyển trên IP đưa cho IP một gói lớn hơn MTU cục bộ, máy chủ nguồn phải phân mảnh nó.

Phân mảnh thường xảy ra ở một bộ định tuyến khi nó nhận một datagram mà nó muốn chuyển tiếp qua một mạng có MTU nhỏ hơn datagram nhận được. Để các mảnh này có thể được lắp ráp lại ở máy chủ nhận, tất cả đều mang cùng một định danh trong trường ``Ident``. Định danh này do máy chủ gửi chọn và phải là duy nhất trong số các datagram có thể đến đích từ nguồn này trong một khoảng thời gian hợp lý. Vì tất cả các mảnh của datagram gốc đều chứa định danh này, máy chủ lắp ráp sẽ nhận ra các mảnh thuộc về nhau. Nếu không phải tất cả các mảnh đều đến được máy chủ nhận, máy chủ sẽ từ bỏ quá trình lắp ráp và loại bỏ các mảnh đã nhận. IP không cố gắng phục hồi các mảnh bị mất.

.. _fig-frag:
.. figure:: figures/f03-17-9780123850591.png
   :width: 600px
   :align: center

   Các datagram IP đi qua chuỗi các mạng vật lý như trong hình trước.

Để hiểu ý nghĩa của điều này, hãy xem điều gì xảy ra khi máy chủ H5 gửi một datagram tới H8 trong ví dụ internet ở :numref:`Hình %s <fig-inet>`. Giả sử MTU là 1500 byte cho hai mạng Ethernet và mạng 802.11, và 532 byte cho mạng điểm-điểm, thì một datagram 1420 byte (20 byte tiêu đề IP cộng 1400 byte dữ liệu) gửi từ H5 sẽ đi qua mạng 802.11 và Ethernet đầu tiên mà không bị phân mảnh nhưng phải bị phân mảnh thành ba datagram tại bộ định tuyến R2. Ba mảnh này sau đó được R3 chuyển tiếp qua Ethernet thứ hai tới máy chủ đích. Tình huống này được minh họa trong :numref:`Hình %s <fig-frag>`. Hình này cũng củng cố hai điểm quan trọng:

1. Mỗi mảnh là một datagram IP độc lập được truyền qua một chuỗi các mạng vật lý, độc lập với các mảnh khác.

2. Mỗi datagram IP được đóng gói lại cho mỗi mạng vật lý mà nó đi qua.

.. _fig-fragment:
.. figure:: figures/f03-18-9780123850591.png
   :align: center
   :width: 350px

   Các trường tiêu đề dùng trong phân mảnh IP: (a) gói chưa phân mảnh; (b) các gói đã phân mảnh.

Quá trình phân mảnh có thể hiểu chi tiết bằng cách xem các trường tiêu đề của từng datagram, như minh họa trong :numref:`Hình %s <fig-fragment>`. Gói chưa phân mảnh ở trên cùng có 1400 byte dữ liệu và 20 byte tiêu đề IP. Khi gói đến bộ định tuyến R2, có MTU là 532 byte, nó phải bị phân mảnh. MTU 532 byte để lại 512 byte cho dữ liệu sau khi trừ 20 byte tiêu đề IP, nên mảnh đầu tiên chứa 512 byte dữ liệu. Bộ định tuyến đặt bit M trong trường ``Flags`` (xem :numref:`Hình %s <fig-iphead>`), nghĩa là còn các mảnh tiếp theo, và đặt ``Offset`` là 0, vì mảnh này chứa phần đầu của datagram gốc. Dữ liệu trong mảnh thứ hai bắt đầu từ byte thứ 513 của dữ liệu gốc, nên trường ``Offset`` trong tiêu đề này được đặt là 64, tức là 512/8. Tại sao chia cho 8? Vì các nhà thiết kế IP quyết định rằng phân mảnh luôn xảy ra trên biên 8 byte, nghĩa là trường ``Offset`` đếm theo đơn vị 8 byte, không phải byte. (Bạn có thể tự tìm hiểu lý do thiết kế này.) Mảnh thứ ba chứa 376 byte dữ liệu cuối cùng, và offset là 2 × 512/8 = 128. Vì đây là mảnh cuối cùng, bit M không được đặt.

Lưu ý rằng quá trình phân mảnh được thực hiện sao cho nó có thể lặp lại nếu một mảnh đến một mạng khác có MTU còn nhỏ hơn. Phân mảnh tạo ra các datagram IP nhỏ hơn, hợp lệ, có thể dễ dàng lắp ráp lại thành datagram gốc khi nhận, bất kể thứ tự các mảnh đến. Việc lắp ráp lại được thực hiện tại máy chủ nhận, không phải tại mỗi bộ định tuyến.

Việc lắp ráp lại IP không hề đơn giản. Ví dụ, nếu một mảnh bị mất, máy nhận vẫn cố gắng lắp ráp datagram, và cuối cùng sẽ từ bỏ và phải giải phóng tài nguyên đã dùng cho quá trình lắp ráp thất bại. Việc một máy chủ giữ tài nguyên vô ích có thể là cơ sở cho một cuộc tấn công từ chối dịch vụ.

Vì lý do này và các lý do khác, phân mảnh IP thường được coi là điều nên tránh. Hiện nay, các máy chủ được khuyến khích mạnh mẽ thực hiện “khám phá MTU đường đi” (path MTU discovery), một quá trình giúp tránh phân mảnh bằng cách gửi các gói đủ nhỏ để đi qua liên kết có MTU nhỏ nhất trên đường từ nguồn tới đích.

3.3.3 Địa chỉ toàn cục
----------------------

Trong phần thảo luận về mô hình dịch vụ IP ở trên, chúng ta đã đề cập rằng một trong những điều mà nó cung cấp là một sơ đồ địa chỉ. Rốt cuộc, nếu bạn muốn gửi dữ liệu tới bất kỳ máy chủ nào trên bất kỳ mạng nào, cần có cách nhận diện tất cả các máy chủ. Do đó, chúng ta cần một sơ đồ địa chỉ toàn cục—trong đó không có hai máy chủ nào có cùng địa chỉ. Tính duy nhất toàn cục là thuộc tính đầu tiên mà một sơ đồ địa chỉ phải cung cấp.

Địa chỉ Ethernet là duy nhất toàn cục, nhưng chỉ điều đó thôi là chưa đủ cho một sơ đồ địa chỉ trong một liên mạng lớn. Địa chỉ Ethernet cũng là *phẳng*, nghĩa là chúng không có cấu trúc và cung cấp rất ít thông tin cho các giao thức định tuyến. (Thực tế, địa chỉ Ethernet có cấu trúc cho mục đích *cấp phát*—24 bit đầu nhận diện nhà sản xuất—nhưng điều này không giúp ích gì cho định tuyến vì cấu trúc này không liên quan tới cấu trúc mạng.) Ngược lại, địa chỉ IP là *phân cấp*, nghĩa là chúng gồm nhiều phần tương ứng với một dạng phân cấp nào đó trong liên mạng. Cụ thể, địa chỉ IP gồm hai phần, thường gọi là phần *mạng* và phần *máy chủ*. Đây là một cấu trúc khá hợp lý cho một liên mạng, vốn được tạo thành từ nhiều mạng liên kết với nhau. Phần mạng của địa chỉ IP nhận diện mạng mà máy chủ kết nối; tất cả các máy chủ kết nối cùng một mạng có cùng phần mạng trong địa chỉ IP. Phần máy chủ sau đó nhận diện từng máy chủ duy nhất trên mạng đó. Do đó, trong liên mạng đơn giản ở :numref:`Hình %s <fig-inet>`, các địa chỉ của các máy chủ trên mạng 1, ví dụ, đều có cùng phần mạng và phần máy chủ khác nhau.

Lưu ý rằng các bộ định tuyến trong :numref:`Hình %s <fig-inet>` kết nối tới hai mạng. Chúng cần có một địa chỉ trên mỗi mạng, một cho mỗi giao diện. Ví dụ, bộ định tuyến R1, nằm giữa mạng không dây và một Ethernet, có một địa chỉ IP trên giao diện tới mạng không dây với phần mạng giống tất cả các máy chủ trên mạng đó. Nó cũng có một địa chỉ IP trên giao diện tới Ethernet có phần mạng giống các máy chủ trên Ethernet đó. Do đó, lưu ý rằng một bộ định tuyến có thể được triển khai như một máy chủ với hai giao diện mạng, nên chính xác hơn là coi địa chỉ IP thuộc về giao diện hơn là thuộc về máy chủ.

Vậy các địa chỉ phân cấp này trông như thế nào? Không giống một số dạng địa chỉ phân cấp khác, kích thước của hai phần không giống nhau cho mọi địa chỉ. Ban đầu, địa chỉ IP được chia thành ba lớp khác nhau, như minh họa trong :numref:`Hình %s <fig-class>`, mỗi lớp định nghĩa kích thước phần mạng và phần máy chủ khác nhau. (Cũng có địa chỉ lớp D dùng cho nhóm multicast và lớp E hiện chưa dùng.) Trong mọi trường hợp, địa chỉ dài 32 bit.

Lớp của một địa chỉ IP được xác định ở một vài bit quan trọng nhất. Nếu bit đầu là 0, đó là địa chỉ lớp A. Nếu bit đầu là 1 và bit thứ hai là 0, đó là lớp B. Nếu hai bit đầu là 1 và bit thứ ba là 0, đó là lớp C. Do đó, trong khoảng 4 tỷ địa chỉ IP có thể có, một nửa là lớp A, một phần tư là lớp B, và một phần tám là lớp C. Mỗi lớp cấp phát một số bit nhất định cho phần mạng và phần còn lại cho phần máy chủ. Mạng lớp A có 7 bit cho phần mạng và 24 bit cho phần máy chủ, nghĩa là chỉ có 126 mạng lớp A (giá trị 0 và 127 được dành riêng), nhưng mỗi mạng có thể chứa tới :math:`2^{24} - 2` (khoảng 16 triệu) máy chủ (lại có hai giá trị dành riêng). Địa chỉ lớp B cấp phát 14 bit cho mạng và 16 bit cho máy chủ, nghĩa là mỗi mạng lớp B chứa tối đa 65.534 máy chủ. Cuối cùng, địa chỉ lớp C chỉ có 8 bit cho máy chủ và 21 bit cho phần mạng. Do đó, một mạng lớp C chỉ có thể có 256 định danh máy chủ duy nhất, tức là chỉ 254 máy chủ kết nối (một định danh máy chủ, 255, dành cho broadcast, và 0 không hợp lệ). Tuy nhiên, sơ đồ địa chỉ này hỗ trợ 2\ :sup:`21` mạng lớp C.

.. _fig-class:
.. figure:: figures/f03-19-9780123850591.png
   :width: 350px
   :align: center

   Địa chỉ IP: (a) lớp A; (b) lớp B; (c) lớp C.

Nhìn bề ngoài, sơ đồ địa chỉ này có nhiều linh hoạt, cho phép các mạng có kích thước rất khác nhau được hỗ trợ khá hiệu quả. Ý tưởng ban đầu là Internet sẽ gồm một số ít mạng diện rộng (là mạng lớp A), một số vừa phải các mạng quy mô site (campus) (là mạng lớp B), và rất nhiều LAN (là mạng lớp C). Tuy nhiên, hóa ra nó không đủ linh hoạt, như chúng ta sẽ thấy ngay sau đây. Ngày nay, địa chỉ IP thường là “không phân lớp”; chi tiết sẽ được giải thích bên dưới.

Trước khi xem cách địa chỉ IP được sử dụng, sẽ hữu ích khi xem một số vấn đề thực tế, như cách ghi lại chúng. Theo quy ước, địa chỉ IP được viết dưới dạng bốn số nguyên *thập phân* cách nhau bằng dấu chấm. Mỗi số nguyên biểu diễn giá trị thập phân chứa trong 1 byte của địa chỉ, bắt đầu từ byte quan trọng nhất. Ví dụ, địa chỉ của máy tính mà câu này được gõ là ``171.69.210.245``.

Điều quan trọng là không nhầm lẫn địa chỉ IP với tên miền Internet, vốn cũng là phân cấp. Tên miền thường là chuỗi ASCII cách nhau bằng dấu chấm, như ``cs.princeton.edu``. Điều quan trọng về địa chỉ IP là chúng được mang trong tiêu đề các gói tin IP, và chính các địa chỉ này được dùng trong các bộ định tuyến IP để quyết định chuyển tiếp.

3.3.4 Chuyển tiếp datagram trong IP
-----------------------------------

Giờ chúng ta đã sẵn sàng xem xét cơ chế cơ bản mà các bộ định tuyến IP dùng để chuyển tiếp datagram trong một liên mạng. Nhớ lại từ phần trước rằng *chuyển tiếp* là quá trình lấy một gói tin từ đầu vào và gửi nó ra đầu ra phù hợp, trong khi *định tuyến* là quá trình xây dựng các bảng cho phép xác định đầu ra đúng cho một gói tin. Phần thảo luận ở đây tập trung vào chuyển tiếp; chúng ta sẽ bàn về định tuyến ở phần sau.

Các điểm chính cần nhớ khi thảo luận về chuyển tiếp datagram IP là:

-  Mỗi datagram IP chứa địa chỉ IP của máy chủ đích.

-  Phần mạng của một địa chỉ IP nhận diện duy nhất một mạng vật lý là một phần của Internet lớn hơn.

-  Tất cả các máy chủ và bộ định tuyến có cùng phần mạng trong địa chỉ đều kết nối tới cùng một mạng vật lý và do đó có thể giao tiếp với nhau bằng cách gửi frame qua mạng đó.

-  Mỗi mạng vật lý là một phần của Internet đều có ít nhất một bộ định tuyến, theo định nghĩa, cũng kết nối tới ít nhất một mạng vật lý khác; bộ định tuyến này có thể trao đổi gói tin với các máy chủ hoặc bộ định tuyến trên cả hai mạng.

Do đó, chuyển tiếp datagram IP có thể được xử lý như sau. Một datagram được gửi từ máy chủ nguồn tới máy chủ đích, có thể đi qua nhiều bộ định tuyến trên đường. Bất kỳ nút nào, dù là máy chủ hay bộ định tuyến, trước tiên cố gắng xác định xem nó có kết nối tới cùng mạng vật lý với đích không. Để làm điều này, nó so sánh phần mạng của địa chỉ đích với phần mạng của địa chỉ trên từng giao diện mạng của nó. (Máy chủ thường chỉ có một giao diện, trong khi bộ định tuyến thường có hai hoặc nhiều hơn, vì chúng thường kết nối tới hai hoặc nhiều mạng.) Nếu có trùng khớp, nghĩa là đích nằm trên cùng mạng vật lý với giao diện đó, và gói tin có thể được chuyển trực tiếp qua mạng đó. Một phần sau sẽ giải thích chi tiết quá trình này.

Nếu nút không kết nối tới cùng mạng vật lý với nút đích, nó cần gửi datagram tới một bộ định tuyến. Thông thường, mỗi nút sẽ có nhiều lựa chọn bộ định tuyến, nên nó cần chọn bộ định tuyến tốt nhất, hoặc ít nhất là một bộ định tuyến có khả năng đưa datagram tới gần đích hơn. Bộ định tuyến mà nó chọn gọi là *bước nhảy tiếp theo* (*next hop*). Bộ định tuyến tìm bước nhảy tiếp theo đúng bằng cách tra cứu bảng chuyển tiếp của nó. Bảng chuyển tiếp về mặt khái niệm chỉ là một danh sách các cặp ``(NetworkNum, NextHop)``. (Như sẽ thấy bên dưới, bảng chuyển tiếp thực tế thường chứa thêm một số thông tin liên quan tới bước nhảy tiếp theo.) Thông thường, cũng có một bộ định tuyến mặc định được dùng nếu không có mục nào trong bảng khớp với số mạng đích. Đối với một máy chủ, hoàn toàn chấp nhận được nếu chỉ có một bộ định tuyến mặc định và không có gì khác—nghĩa là tất cả các datagram gửi tới các máy chủ không nằm trên mạng vật lý mà máy chủ gửi kết nối sẽ được gửi qua bộ định tuyến mặc định.

Chúng ta có thể mô tả thuật toán chuyển tiếp datagram như sau:

::

   if (NetworkNum of destination = NetworkNum of one of my interfaces) then
       deliver packet to destination over that interface
   else
       if (NetworkNum of destination is in my forwarding table) then
           deliver packet to NextHop router
       else
           deliver packet to default router

Đối với một máy chủ chỉ có một giao diện và chỉ có một bộ định tuyến mặc định trong bảng chuyển tiếp, điều này đơn giản thành

::

   if (NetworkNum of destination = my NetworkNum) then
       deliver packet to destination directly
   else
       deliver packet to default router

Hãy xem cách hoạt động này trong ví dụ liên mạng ở :numref:`Hình %s <fig-inet>`. Đầu tiên, giả sử H1 muốn gửi một datagram tới H2. Vì chúng nằm trên cùng mạng vật lý, H1 và H2 có cùng số mạng trong địa chỉ IP. Do đó, H1 suy ra rằng nó có thể chuyển datagram trực tiếp tới H2 qua Ethernet. Vấn đề duy nhất cần giải quyết là làm sao H1 biết địa chỉ Ethernet đúng của H2—cơ chế giải quyết sẽ được mô tả ở phần sau.

Giờ giả sử H5 muốn gửi một datagram tới H8. Vì hai máy chủ này nằm trên các mạng vật lý khác nhau, chúng có số mạng khác nhau, nên H5 suy ra rằng nó cần gửi datagram tới một bộ định tuyến. R1 là lựa chọn duy nhất—bộ định tuyến mặc định—nên H1 gửi datagram qua mạng không dây tới R1. Tương tự, R1 biết rằng nó không thể chuyển datagram trực tiếp tới H8 vì không giao diện nào của R1 nằm trên cùng mạng với H8. Giả sử bộ định tuyến mặc định của R1 là R2; R1 khi đó gửi datagram tới R2 qua Ethernet. Giả sử R2 có bảng chuyển tiếp như trong :numref:`Bảng %s <tab-ipfwdtab>`, nó tra cứu số mạng của H8 (mạng 4) và chuyển tiếp datagram qua mạng điểm-điểm tới R3. Cuối cùng, R3, vì nằm trên cùng mạng với H8, chuyển tiếp datagram trực tiếp tới H8.

.. _tab-ipfwdtab:
.. table:: Bảng chuyển tiếp cho Bộ định tuyến R2.
   :align: center
   :widths: auto

   +------------+---------+
   | NetworkNum | NextHop |
   +============+=========+
   | 1          | R1      |
   +------------+---------+
   | 4          | R3      |
   +------------+---------+

Lưu ý rằng có thể đưa thông tin về các mạng kết nối trực tiếp vào bảng chuyển tiếp. Ví dụ, ta có thể gán nhãn các giao diện mạng của bộ định tuyến R2 là giao diện 0 cho liên kết điểm-điểm (mạng 3) và giao diện 1 cho Ethernet (mạng 2). Khi đó R2 sẽ có bảng chuyển tiếp như trong :numref:`Bảng %s <tab-ipfwdtab2>`.

.. _tab-ipfwdtab2:
.. table:: Bảng chuyển tiếp đầy đủ cho Bộ định tuyến R2.
   :align: center
   :widths: auto

   +------------+-------------+
   | NetworkNum | NextHop     |
   +============+=============+
   | 1          | R1          |
   +------------+-------------+
   | 2          | Interface 1 |
   +------------+-------------+
   | 3          | Interface 0 |
   +------------+-------------+
   | 4          | R3          |
   +------------+-------------+

Như vậy, với bất kỳ số mạng nào mà R2 gặp trong một gói tin, nó đều biết phải làm gì. Hoặc mạng đó kết nối trực tiếp với R2, khi đó gói tin có thể được chuyển trực tiếp tới đích qua mạng đó, hoặc mạng đó có thể tới được qua một bộ định tuyến bước nhảy tiếp theo mà R2 có thể tới qua một mạng mà nó kết nối. Trong cả hai trường hợp, R2 sẽ dùng ARP, mô tả bên dưới, để tìm địa chỉ MAC của nút mà gói tin sẽ được gửi tới tiếp theo.

Bảng chuyển tiếp mà R2 dùng đủ đơn giản để có thể cấu hình thủ công. Tuy nhiên, thường thì các bảng này phức tạp hơn và sẽ được xây dựng bằng cách chạy một giao thức định tuyến như một trong các giao thức sẽ mô tả ở phần sau. Cũng lưu ý rằng, trên thực tế, các số mạng thường dài hơn (ví dụ, 128.96).

Giờ ta có thể thấy cách địa chỉ phân cấp—chia địa chỉ thành phần mạng và phần máy chủ—đã cải thiện khả năng mở rộng của một mạng lớn. Các bộ định tuyến giờ chỉ cần bảng chuyển tiếp liệt kê một tập số mạng thay vì tất cả các nút trong mạng. Trong ví dụ đơn giản của chúng ta, điều đó nghĩa là R2 có thể lưu thông tin cần thiết để tới tất cả các máy chủ trong mạng (có tám máy chủ) chỉ trong một bảng bốn mục. Ngay cả khi có 100 máy chủ trên mỗi mạng vật lý, R2 vẫn chỉ cần bốn mục đó. Đây là một bước đầu tốt (dù chưa phải cuối cùng) để đạt được khả năng mở rộng.

.. _key-aggregation:
.. admonition:: Bài học then chốt

   Điều này minh họa một trong những nguyên lý quan trọng nhất khi xây dựng mạng khả mở: Để đạt được khả năng mở rộng, bạn cần giảm lượng thông tin lưu trữ ở mỗi nút và trao đổi giữa các nút. Cách phổ biến nhất để làm điều đó là *kết tụ phân cấp* (hierarchical aggregation). IP giới thiệu một phân cấp hai tầng, với mạng ở tầng trên và nút ở tầng dưới. Ta đã kết tụ thông tin bằng cách để bộ định tuyến chỉ xử lý việc tới đúng mạng; thông tin mà bộ định tuyến cần để chuyển một datagram tới bất kỳ nút nào trên một mạng nhất định được đại diện bởi một mẩu thông tin kết tụ duy nhất. :ref:`[Tiếp theo] <key-best-effort>`

3.3.5 Địa chỉ con và địa chỉ không phân lớp
-------------------------------------------

Ý định ban đầu của địa chỉ IP là phần mạng sẽ nhận diện duy nhất đúng một mạng vật lý. Hóa ra cách tiếp cận này có một số nhược điểm. Hãy tưởng tượng một campus lớn có nhiều mạng nội bộ và quyết định kết nối Internet. Với mỗi mạng, dù nhỏ đến đâu, site cần ít nhất một địa chỉ mạng lớp C. Tệ hơn, với bất kỳ mạng nào có hơn 255 máy chủ, họ cần một địa chỉ lớp B. Điều này có vẻ không lớn, và thực tế không phải khi Internet mới hình thành, nhưng chỉ có một số lượng hữu hạn số mạng, và số địa chỉ lớp B còn ít hơn lớp C nhiều. Địa chỉ lớp B đặc biệt được ưa chuộng vì bạn không bao giờ biết mạng của mình có thể mở rộng vượt quá 255 nút hay không, nên tốt hơn là dùng địa chỉ lớp B ngay từ đầu hơn là phải đánh lại địa chỉ khi hết chỗ trên mạng lớp C. Vấn đề ở đây là sự kém hiệu quả trong cấp phát địa chỉ: Một mạng chỉ có hai nút dùng hết một địa chỉ mạng lớp C, lãng phí 253 địa chỉ hữu ích; một mạng lớp B có hơn 255 máy chủ một chút lãng phí hơn 64.000 địa chỉ.

Việc cấp phát một số mạng cho mỗi mạng vật lý, do đó, làm tiêu tốn không gian địa chỉ IP nhanh hơn mong muốn. Dù cần kết nối hơn 4 tỷ máy chủ để dùng hết tất cả địa chỉ hợp lệ, chỉ cần kết nối 2\ :sup:`14` (khoảng 16.000) mạng lớp B là hết phần không gian địa chỉ này. Do đó, ta muốn tìm cách sử dụng số mạng hiệu quả hơn.

Việc cấp phát nhiều số mạng còn có một nhược điểm khác khi nghĩ về định tuyến. Nhớ rằng lượng trạng thái lưu trữ ở một nút tham gia giao thức định tuyến tỷ lệ thuận với số nút khác, và định tuyến trong một liên mạng gồm việc xây dựng các bảng chuyển tiếp cho biết bộ định tuyến phải làm gì để tới các mạng khác nhau. Do đó, càng có nhiều số mạng được dùng, bảng chuyển tiếp càng lớn. Bảng lớn làm tăng chi phí cho bộ định tuyến, và có thể chậm hơn khi tìm kiếm, làm giảm hiệu năng bộ định tuyến. Điều này là một động lực khác để cấp phát số mạng cẩn thận.

*Địa chỉ con* (subnetting) là bước đầu tiên để giảm tổng số số mạng được cấp phát. Ý tưởng là lấy một số mạng IP duy nhất và cấp phát các địa chỉ IP với số mạng đó cho nhiều mạng vật lý, giờ gọi là *mạng con* (subnets). Có một số việc cần làm để điều này hoạt động. Đầu tiên, các mạng con nên gần nhau. Vì từ một điểm xa trên Internet, chúng sẽ đều trông như một mạng duy nhất, chỉ có một số mạng giữa chúng. Điều này nghĩa là một bộ định tuyến chỉ có thể chọn một đường để tới bất kỳ mạng con nào, nên tốt nhất là chúng đều cùng hướng. Một tình huống lý tưởng để dùng subnetting là một campus hoặc công ty lớn có nhiều mạng vật lý. Từ ngoài campus, tất cả những gì bạn cần biết để tới bất kỳ mạng con nào bên trong là nơi campus kết nối với phần còn lại của Internet. Điều này thường chỉ ở một điểm, nên một mục trong bảng chuyển tiếp là đủ. Ngay cả khi có nhiều điểm kết nối, biết cách tới một điểm trong mạng campus vẫn là một khởi đầu tốt.

Cơ chế cho phép một số mạng duy nhất được chia sẻ giữa nhiều mạng con là cấu hình tất cả các nút trên mỗi mạng con với một *mặt nạ mạng con* (subnet mask). Với địa chỉ IP đơn giản, tất cả các máy chủ trên cùng một mạng phải có cùng số mạng. Mặt nạ mạng con cho phép ta đưa vào một *số mạng con*; tất cả các máy chủ trên cùng một mạng vật lý sẽ có cùng số mạng con, nghĩa là các máy chủ có thể ở các mạng vật lý khác nhau nhưng chia sẻ một số mạng duy nhất. Khái niệm này được minh họa trong :numref:`Hình %s <fig-subaddr>`.

.. _fig-subaddr:
.. figure:: figures/f03-20-9780123850591.png
   :width: 350px
   :align: center

   Địa chỉ mạng con.

Ý nghĩa của subnetting với một máy chủ là nó giờ được cấu hình với cả địa chỉ IP và mặt nạ mạng con cho mạng con mà nó kết nối. Ví dụ, máy chủ H1 trong :numref:`Hình %s <fig-subnet>` được cấu hình với địa chỉ 128.96.34.15 và mặt nạ mạng con 255.255.255.128. (Tất cả các máy chủ trên một mạng con được cấu hình cùng mặt nạ; tức là mỗi mạng con chỉ có một mặt nạ.) Phép AND bit giữa hai số này xác định số mạng con của máy chủ và tất cả các máy chủ khác trên cùng mạng con. Trong trường hợp này, 128.96.34.15 AND 255.255.255.128 bằng 128.96.34.0, nên đây là số mạng con cho mạng con trên cùng trong hình.

.. _fig-subnet:
.. figure:: figures/f03-21-9780123850591.png
   :width: 500px
   :align: center

   Ví dụ về subnetting.

Khi máy chủ muốn gửi một gói tin tới một địa chỉ IP nào đó, việc đầu tiên nó làm là thực hiện phép AND bit giữa mặt nạ mạng con của nó và địa chỉ IP đích. Nếu kết quả bằng số mạng con của máy chủ gửi, nó biết rằng máy chủ đích nằm trên cùng mạng con và gói tin có thể được chuyển trực tiếp qua mạng con. Nếu kết quả không bằng, gói tin cần được gửi tới một bộ định tuyến để chuyển tiếp tới mạng con khác. Ví dụ, nếu H1 gửi tới H2, H1 AND mặt nạ mạng con của nó (255.255.255.128) với địa chỉ của H2 (128.96.34.139) để được 128.96.34.128. Điều này không khớp với số mạng con của H1 (128.96.34.0) nên H1 biết H2 nằm trên mạng con khác. Vì H1 không thể chuyển gói tin trực tiếp tới H2 qua mạng con, nó gửi gói tin tới bộ định tuyến mặc định R1.

Bảng chuyển tiếp của một bộ định tuyến cũng thay đổi một chút khi ta đưa vào subnetting. Nhớ rằng trước đây ta có bảng chuyển tiếp gồm các mục dạng ``(NetworkNum, NextHop)``. Để hỗ trợ subnetting, bảng giờ phải chứa các mục dạng ``(SubnetNumber, SubnetMask, NextHop)``. Để tìm mục đúng trong bảng, bộ định tuyến AND địa chỉ đích của gói tin với ``SubnetMask`` của từng mục; nếu kết quả khớp với ``SubnetNumber`` của mục, đây là mục đúng để dùng, và nó chuyển tiếp gói tin tới bộ định tuyến bước nhảy tiếp theo chỉ định. Trong mạng ví dụ ở :numref:`Hình %s <fig-subnet>`, bộ định tuyến R1 sẽ có các mục như trong :numref:`Bảng %s <tab-subnettab>`.

.. _tab-subnettab:
.. table:: Ví dụ bảng chuyển tiếp với subnetting.
   :align: center
   :widths: auto

   +---------------+-----------------+-------------+
   | SubnetNumber  | SubnetMask      | NextHop     |
   +===============+=================+=============+
   | 128.96.34.0   | 255.255.255.128 | Interface 0 |
   +---------------+-----------------+-------------+
   | 128.96.34.128 | 255.255.255.128 | Interface 1 |
   +---------------+-----------------+-------------+
   | 128.96.33.0   | 255.255.255.0   | R2          |
   +---------------+-----------------+-------------+

Tiếp tục ví dụ gửi datagram từ H1 tới H2, R1 sẽ AND địa chỉ của H2 (128.96.34.139) với mặt nạ mạng con của mục đầu tiên (255.255.255.128) và so sánh kết quả (128.96.34.128) với số mạng của mục đó (128.96.34.0). Vì không khớp, nó chuyển sang mục tiếp theo. Lần này có khớp, nên R1 chuyển datagram tới H2 qua interface 1, là giao diện kết nối tới cùng mạng với H2.

Giờ ta có thể mô tả thuật toán chuyển tiếp datagram như sau:

::

   D = địa chỉ IP đích
   for each forwarding table entry (SubnetNumber, SubnetMask, NextHop)
       D1 = SubnetMask & D
       if D1 = SubnetNumber
           if NextHop is an interface
               deliver datagram directly to destination
           else
               deliver datagram to NextHop (a router)

Dù không minh họa trong ví dụ này, thường sẽ có một đường mặc định trong bảng và sẽ được dùng nếu không tìm thấy khớp rõ ràng nào. Lưu ý rằng một triển khai ngây thơ của thuật toán này—lặp lại phép AND địa chỉ đích với mặt nạ mạng con có thể không khác nhau mỗi lần, và tìm kiếm tuyến tính bảng—sẽ rất kém hiệu quả.

Một hệ quả quan trọng của subnetting là các phần khác nhau của internet nhìn thế giới khác nhau. Từ ngoài campus giả định của chúng ta, các bộ định tuyến nhìn thấy một mạng duy nhất. Trong ví dụ trên, các bộ định tuyến ngoài campus nhìn tập hợp các mạng trong :numref:`Hình %s <fig-subnet>` chỉ là mạng 128.96, và họ giữ một mục trong bảng chuyển tiếp để biết cách tới đó. Các bộ định tuyến trong campus, tuy nhiên, cần có khả năng định tuyến gói tin tới đúng mạng con. Do đó, không phải tất cả các phần của internet đều nhìn thấy cùng một thông tin định tuyến. Đây là một ví dụ về *kết tụ* thông tin định tuyến, điều cơ bản để mở rộng hệ thống định tuyến. Phần tiếp theo sẽ cho thấy cách kết tụ có thể được nâng lên một mức nữa.

Địa chỉ không phân lớp
~~~~~~~~~~~~~~~~~~~~~~

Subnetting có một đối ứng, đôi khi gọi là *supernetting*, nhưng thường gọi là *Định tuyến liên miền không phân lớp* (Classless Interdomain Routing, CIDR, phát âm là “cider”). CIDR đưa ý tưởng subnetting tới kết luận logic của nó bằng cách loại bỏ hoàn toàn các lớp địa chỉ. Tại sao chỉ subnetting thôi là chưa đủ? Về bản chất, subnetting chỉ cho phép ta chia một địa chỉ phân lớp thành nhiều mạng con, trong khi CIDR cho phép ta gộp nhiều địa chỉ phân lớp thành một “siêu mạng”. Điều này giải quyết thêm vấn đề kém hiệu quả về không gian địa chỉ đã nói ở trên, và làm như vậy mà không làm hệ thống định tuyến bị quá tải.

Để thấy vấn đề hiệu quả không gian địa chỉ và khả năng mở rộng của hệ thống định tuyến liên quan với nhau thế nào, hãy xét trường hợp giả định một công ty có mạng với 256 máy chủ. Đó là hơi nhiều cho một địa chỉ lớp C, nên bạn sẽ muốn cấp một địa chỉ lớp B. Tuy nhiên, dùng một phần không gian địa chỉ có thể chứa 65.535 máy chủ để chỉ chứa 256 máy chủ có hiệu quả chỉ 256/65.535 = 0,39%. Dù subnetting có thể giúp ta cấp phát địa chỉ cẩn thận, nó không giải quyết được thực tế là bất kỳ tổ chức nào có hơn 255 máy chủ, hoặc dự kiến sẽ có, đều muốn một địa chỉ lớp B.

Cách đầu tiên bạn có thể xử lý vấn đề này là từ chối cấp địa chỉ lớp B cho bất kỳ tổ chức nào trừ khi họ chứng minh cần gần 64K địa chỉ, và thay vào đó cấp cho họ số lượng địa chỉ lớp C phù hợp với số máy chủ dự kiến. Vì giờ ta cấp phát không gian địa chỉ theo từng khối 256 địa chỉ một, ta có thể khớp chính xác hơn lượng không gian địa chỉ tiêu thụ với quy mô tổ chức. Với bất kỳ tổ chức nào có ít nhất 256 máy chủ, ta có thể đảm bảo hiệu quả sử dụng địa chỉ ít nhất 50%, và thường cao hơn. (Đáng buồn là, ngay cả khi bạn có thể chứng minh cần một số mạng lớp B, cũng đừng bận tâm, vì chúng đã được cấp hết từ lâu.)

Tuy nhiên, giải pháp này lại gây ra một vấn đề nghiêm trọng không kém: yêu cầu lưu trữ quá lớn ở các bộ định tuyến. Nếu một site có, ví dụ, 16 số mạng lớp C được cấp, nghĩa là mỗi bộ định tuyến xương sống Internet cần 16 mục trong bảng định tuyến để chuyển gói tin tới site đó. Điều này đúng ngay cả khi đường đi tới tất cả các mạng đó là như nhau. Nếu ta cấp một địa chỉ lớp B cho site, cùng thông tin định tuyến đó có thể lưu trong một mục bảng. Tuy nhiên, hiệu quả sử dụng địa chỉ khi đó chỉ là 16 x 255 / 65.536 = 6,2%.

Do đó, CIDR cố gắng cân bằng mong muốn giảm số đường mà bộ định tuyến cần biết với nhu cầu cấp phát địa chỉ hiệu quả. Để làm điều này, CIDR giúp ta *kết tụ* các đường. Tức là, nó cho phép ta dùng một mục duy nhất trong bảng chuyển tiếp để biết cách tới rất nhiều mạng khác nhau. Như đã nói ở trên, nó làm điều này bằng cách phá vỡ ranh giới cứng giữa các lớp địa chỉ. Để hiểu cách hoạt động, hãy xét tổ chức giả định với 16 số mạng lớp C. Thay vì cấp phát 16 địa chỉ ngẫu nhiên, ta có thể cấp một khối các địa chỉ lớp C *liền kề*. Giả sử ta cấp các số mạng lớp C từ 192.4.16 đến 192.4.31. Lưu ý rằng 20 bit đầu của tất cả các địa chỉ trong dải này giống nhau (``11000000 00000100 0001``). Do đó, về thực chất, ta đã tạo ra một số mạng 20 bit—một cái gì đó nằm giữa số mạng lớp B và lớp C về số máy chủ có thể hỗ trợ. Nói cách khác, ta vừa có hiệu quả địa chỉ cao khi cấp phát địa chỉ theo khối nhỏ hơn mạng lớp B, vừa có một tiền tố mạng duy nhất có thể dùng trong bảng chuyển tiếp. Lưu ý rằng, để cách này hoạt động, ta cần cấp phát các khối địa chỉ lớp C có cùng tiền tố, nghĩa là mỗi khối phải chứa số mạng lớp C là lũy thừa của hai.

CIDR yêu cầu một kiểu ký hiệu mới để biểu diễn số mạng, hay còn gọi là *tiền tố* (prefix), vì tiền tố có thể có bất kỳ độ dài nào. Quy ước là đặt ``/X`` sau tiền tố, trong đó ``X`` là độ dài tiền tố tính theo bit. Vậy, trong ví dụ trên, tiền tố 20 bit cho tất cả các mạng 192.4.16 đến 192.4.31 được biểu diễn là 192.4.16/20. Ngược lại, nếu muốn biểu diễn một số mạng lớp C duy nhất, dài 24 bit, ta viết 192.4.16/24. Ngày nay, với CIDR là chuẩn, người ta thường nói về “slash 24” hơn là mạng lớp C. Lưu ý rằng biểu diễn địa chỉ mạng theo cách này giống với cách ``(mask, value)`` dùng trong subnetting, miễn là ``mask`` gồm các bit liên tục từ bit quan trọng nhất (trên thực tế hầu như luôn như vậy).

.. _fig-cidreg:
.. figure:: figures/f03-22-9780123850591.png
   :width: 500px
   :align: center

   Kết tụ đường với CIDR.

Khả năng kết tụ đường ở biên mạng như vừa thấy chỉ là bước đầu. Hãy tưởng tượng một mạng nhà cung cấp dịch vụ Internet, nhiệm vụ chính là cung cấp kết nối Internet cho nhiều công ty và campus (khách hàng). Nếu ta cấp phát tiền tố cho khách hàng sao cho nhiều mạng khách hàng kết nối tới mạng nhà cung cấp chia sẻ một tiền tố địa chỉ ngắn hơn, ta có thể kết tụ đường lớn hơn nữa. Xét ví dụ trong :numref:`Hình %s <fig-cidreg>`. Giả sử tám khách hàng được mạng nhà cung cấp phục vụ, mỗi khách hàng được cấp một tiền tố mạng 24 bit liền kề. Các tiền tố này đều bắt đầu bằng cùng 21 bit. Vì tất cả khách hàng đều tới được qua cùng một mạng nhà cung cấp, nó có thể quảng bá một đường duy nhất tới tất cả bằng cách chỉ quảng bá tiền tố 21 bit chung. Và nó có thể làm điều này ngay cả khi chưa cấp hết các tiền tố 24 bit, miễn là cuối cùng nhà cung cấp sẽ có quyền cấp các tiền tố đó cho khách hàng. Một cách để làm điều này là cấp trước một phần không gian địa chỉ cho nhà cung cấp và để nhà cung cấp cấp phát địa chỉ từ không gian đó cho khách hàng khi cần. Lưu ý rằng, khác với ví dụ đơn giản này, không cần tất cả tiền tố khách hàng phải cùng độ dài.

Xét lại chuyển tiếp IP
~~~~~~~~~~~~~~~~~~~~~

Trong tất cả các thảo luận về chuyển tiếp IP tới giờ, ta đã giả định rằng có thể tìm số mạng trong một gói tin rồi tra cứu số đó trong bảng chuyển tiếp. Tuy nhiên, giờ đã có CIDR, ta cần xem lại giả định này. CIDR nghĩa là tiền tố có thể có bất kỳ độ dài nào, từ 2 đến 32 bit. Hơn nữa, đôi khi có thể có các tiền tố trong bảng chuyển tiếp “chồng lấn”, nghĩa là một số địa chỉ có thể khớp với nhiều tiền tố. Ví dụ, ta có thể thấy cả 171.69 (tiền tố 16 bit) và 171.69.10 (tiền tố 24 bit) trong bảng chuyển tiếp của một bộ định tuyến. Khi đó, một gói tin gửi tới, ví dụ, 171.69.10.5 rõ ràng khớp cả hai tiền tố. Quy tắc trong trường hợp này dựa trên nguyên lý “khớp dài nhất”; tức là, gói tin khớp với tiền tố dài nhất, ở đây là 171.69.10. Ngược lại, một gói gửi tới 171.69.20.5 sẽ khớp với 171.69 và *không* khớp với 171.69.10, và nếu không có mục nào khác khớp trong bảng định tuyến thì 171.69 là khớp dài nhất.

Nhiệm vụ tìm khớp dài nhất giữa một địa chỉ IP và các tiền tố độ dài biến đổi trong bảng chuyển tiếp là một lĩnh vực nghiên cứu phong phú nhiều năm qua. Thuật toán nổi tiếng nhất dùng cách tiếp cận gọi là *cây PATRICIA*, thực ra được phát triển trước cả CIDR.

3.3.6 Biên dịch địa chỉ (ARP)
-----------------------------

Ở phần trước, chúng ta đã nói về cách đưa datagram IP tới đúng mạng vật lý nhưng chưa đề cập tới việc làm sao đưa datagram tới đúng máy chủ hoặc bộ định tuyến trên mạng đó. Vấn đề chính là datagram IP chứa địa chỉ IP, nhưng phần cứng giao diện vật lý trên máy chủ hoặc bộ định tuyến mà bạn muốn gửi datagram chỉ hiểu sơ đồ địa chỉ của mạng cụ thể đó. Do đó, ta cần biên dịch địa chỉ IP sang địa chỉ tầng liên kết phù hợp với mạng này (ví dụ, địa chỉ Ethernet 48 bit). Khi đó, ta có thể đóng gói datagram IP vào trong một frame chứa địa chỉ tầng liên kết đó và gửi tới đích cuối cùng hoặc tới một bộ định tuyến hứa sẽ chuyển tiếp datagram tới đích cuối cùng.

Một cách đơn giản để ánh xạ địa chỉ IP sang địa chỉ mạng vật lý là mã hóa địa chỉ vật lý của máy chủ vào phần máy chủ của địa chỉ IP. Ví dụ, một máy chủ có địa chỉ vật lý ``00100001 01010001`` (giá trị thập phân 33 ở byte cao và 81 ở byte thấp) có thể được cấp địa chỉ IP ``128.96.33.81``. Dù giải pháp này đã được dùng trên một số mạng, nó bị giới hạn ở chỗ địa chỉ vật lý mạng chỉ có thể dài tối đa 16 bit trong ví dụ này; chỉ 8 bit trên mạng lớp C. Rõ ràng điều này không phù hợp với địa chỉ Ethernet 48 bit.

Một giải pháp tổng quát hơn là mỗi máy chủ duy trì một bảng các cặp địa chỉ; tức là, bảng ánh xạ địa chỉ IP sang địa chỉ vật lý. Dù bảng này có thể được quản trị viên hệ thống quản lý tập trung rồi sao chép tới từng máy chủ trên mạng, một cách tốt hơn là mỗi máy chủ tự động học nội dung bảng này qua mạng. Điều này có thể thực hiện bằng Giao thức phân giải địa chỉ (ARP). Mục tiêu của ARP là cho phép mỗi máy chủ trên mạng xây dựng một bảng ánh xạ giữa địa chỉ IP và địa chỉ tầng liên kết. Vì các ánh xạ này có thể thay đổi theo thời gian (ví dụ, vì card Ethernet của một máy bị hỏng và thay bằng card mới có địa chỉ mới), các mục sẽ hết hạn định kỳ và bị loại bỏ. Điều này diễn ra khoảng 15 phút một lần. Tập hợp các ánh xạ hiện có trên một máy chủ gọi là bộ nhớ đệm ARP hoặc bảng ARP.

ARP tận dụng thực tế là nhiều công nghệ mạng tầng liên kết, như Ethernet, hỗ trợ broadcast. Nếu một máy chủ muốn gửi một datagram IP tới một máy chủ (hoặc bộ định tuyến) mà nó biết nằm trên cùng mạng (tức là các nút gửi và nhận có cùng số mạng IP), nó trước tiên kiểm tra ánh xạ trong bộ nhớ đệm. Nếu không tìm thấy, nó cần gọi ARP qua mạng. Nó làm điều này bằng cách broadcast một truy vấn ARP lên mạng. Truy vấn này chứa địa chỉ IP cần tìm (địa chỉ IP đích). Mỗi máy chủ nhận truy vấn và kiểm tra xem nó có khớp với địa chỉ IP của mình không. Nếu có, máy chủ gửi một thông điệp phản hồi chứa địa chỉ tầng liên kết của nó về cho máy gửi truy vấn. Máy gửi thêm thông tin này vào bảng ARP của mình.

Thông điệp truy vấn cũng bao gồm địa chỉ IP và địa chỉ tầng liên kết của máy gửi. Do đó, khi một máy chủ broadcast một truy vấn, mỗi máy chủ trên mạng có thể học địa chỉ tầng liên kết và địa chỉ IP của máy gửi và lưu thông tin đó vào bảng ARP của mình. Tuy nhiên, không phải mọi máy chủ đều thêm thông tin này vào bảng ARP. Nếu máy chủ đã có một mục cho máy gửi trong bảng, nó “làm mới” mục này; tức là, đặt lại thời gian sống của mục. Nếu máy chủ là đích của truy vấn, nó thêm thông tin về máy gửi vào bảng, ngay cả khi chưa có mục cho máy gửi. Điều này vì có khả năng máy chủ nguồn sắp gửi một thông điệp ứng dụng tới nó, và nó có thể cần gửi phản hồi hoặc ACK lại cho nguồn; nó sẽ cần địa chỉ vật lý của nguồn để làm điều này. Nếu một máy chủ không phải là đích và chưa có mục cho nguồn trong bảng ARP, nó không thêm mục cho nguồn. Vì không có lý do gì để tin rằng máy chủ này sẽ cần địa chỉ tầng liên kết của nguồn; không cần làm bảng ARP lộn xộn với thông tin này.

.. _fig-arp:
.. figure:: figures/f03-23-9780123850591.png
   :width: 500px
   :align: center

   Định dạng gói ARP để ánh xạ địa chỉ IP sang địa chỉ Ethernet.

:numref:`Hình %s <fig-arp>` cho thấy định dạng gói ARP cho ánh xạ địa chỉ IP sang Ethernet. Thực tế, ARP có thể dùng cho nhiều loại ánh xạ khác—khác biệt chính là ở kích thước địa chỉ. Ngoài địa chỉ IP và địa chỉ tầng liên kết của cả máy gửi và đích, gói tin còn chứa

-  Trường ``HardwareType``, chỉ loại mạng vật lý (ví dụ, Ethernet)

-  Trường ``ProtocolType``, chỉ giao thức tầng cao hơn (ví dụ, IP)

-  Trường ``HLen`` (độ dài địa chỉ phần cứng) và ``PLen`` (độ dài địa chỉ giao thức), chỉ độ dài địa chỉ tầng liên kết và địa chỉ giao thức tầng cao hơn

-  Trường ``Operation``, chỉ đây là yêu cầu hay phản hồi

-  Địa chỉ phần cứng (Ethernet) và giao thức (IP) của nguồn và đích

Lưu ý rằng kết quả của quá trình ARP có thể được thêm vào như một cột bổ sung trong bảng chuyển tiếp như trong :numref:`Bảng %s <tab-ipfwdtab>`. Do đó, ví dụ, khi R2 cần chuyển tiếp một gói tin tới mạng 2, nó không chỉ tìm thấy bước nhảy tiếp theo là R1, mà còn tìm được địa chỉ MAC để đặt vào gói tin gửi tới R1.

.. _key-best-effort:
.. admonition:: Bài học then chốt

   Chúng ta đã thấy các cơ chế cơ bản mà IP cung cấp để xử lý cả dị chủng và khả mở. Về dị chủng, IP bắt đầu bằng cách định nghĩa một mô hình dịch vụ nỗ lực tối đa với giả định tối thiểu về các mạng bên dưới; đáng chú ý nhất, mô hình này dựa trên datagram không tin cậy. IP sau đó bổ sung hai điểm quan trọng: (1) định dạng gói tin chung (phân mảnh/lắp ráp là cơ chế giúp định dạng này hoạt động trên các mạng có MTU khác nhau) và (2) không gian địa chỉ toàn cục để nhận diện tất cả máy chủ (ARP là cơ chế giúp không gian địa chỉ này hoạt động trên các mạng có sơ đồ địa chỉ vật lý khác nhau). Về khả mở, IP dùng kết tụ phân cấp để giảm lượng thông tin cần thiết để chuyển tiếp gói tin. Cụ thể, địa chỉ IP được chia thành phần mạng và phần máy chủ, với gói tin trước tiên được định tuyến tới mạng đích rồi chuyển tới đúng máy chủ trên mạng đó. :ref:`[Tiếp theo] <key-dhcp>`

3.3.7 Cấu hình máy chủ (DHCP)
-----------------------------

Địa chỉ Ethernet được cấu hình vào card mạng bởi nhà sản xuất, và quá trình này được quản lý để đảm bảo các địa chỉ này là duy nhất toàn cục. Điều này đủ để đảm bảo bất kỳ tập hợp máy chủ nào kết nối tới một Ethernet (kể cả LAN mở rộng) đều có địa chỉ duy nhất. Hơn nữa, điều duy nhất ta yêu cầu ở địa chỉ Ethernet là tính duy nhất.

Địa chỉ IP, ngược lại, không chỉ phải duy nhất trên một liên mạng nhất định mà còn phải phản ánh cấu trúc của liên mạng. Như đã nói ở trên, chúng chứa phần mạng và phần máy chủ, và phần mạng phải giống nhau cho tất cả các máy chủ trên cùng mạng. Do đó, không thể cấu hình địa chỉ IP một lần vào máy chủ khi sản xuất, vì điều đó ngụ ý nhà sản xuất biết máy chủ nào sẽ kết nối tới mạng nào, và máy chủ, một khi đã kết nối tới một mạng, sẽ không bao giờ chuyển sang mạng khác. Vì lý do này, địa chỉ IP cần có khả năng cấu hình lại.

Ngoài địa chỉ IP, còn một số thông tin khác mà máy chủ cần có trước khi bắt đầu gửi gói tin. Đáng chú ý nhất là địa chỉ của bộ định tuyến mặc định—nơi nó có thể gửi các gói tin có địa chỉ đích không nằm trên cùng mạng với máy chủ gửi.

Hầu hết các hệ điều hành máy chủ cung cấp cách để quản trị viên hệ thống, hoặc thậm chí người dùng, cấu hình thủ công thông tin IP cần thiết cho máy chủ; tuy nhiên, có một số nhược điểm rõ ràng với cấu hình thủ công như vậy. Một là rất tốn công để cấu hình tất cả các máy chủ trong một mạng lớn, đặc biệt khi bạn cân nhắc rằng các máy chủ đó không thể truy cập qua mạng cho tới khi được cấu hình. Quan trọng hơn, quá trình cấu hình rất dễ sai, vì cần đảm bảo mọi máy chủ nhận đúng số mạng và không có hai máy chủ nhận cùng địa chỉ IP. Vì những lý do này, các phương pháp cấu hình tự động là cần thiết. Phương pháp chính dùng một giao thức gọi là *Giao thức cấu hình máy chủ động* (DHCP).

DHCP dựa vào sự tồn tại của một máy chủ DHCP chịu trách nhiệm cung cấp thông tin cấu hình cho các máy chủ. Có ít nhất một máy chủ DHCP cho mỗi miền quản trị. Ở mức đơn giản nhất, máy chủ DHCP có thể hoạt động như một kho lưu trữ tập trung thông tin cấu hình máy chủ. Xét ví dụ, vấn đề quản trị địa chỉ trong liên mạng của một công ty lớn. DHCP giúp quản trị viên mạng không phải đi tới từng máy chủ trong công ty với danh sách địa chỉ và sơ đồ mạng để cấu hình từng máy chủ thủ công. Thay vào đó, thông tin cấu hình cho mỗi máy chủ có thể lưu trong máy chủ DHCP và được tự động lấy về khi máy chủ khởi động hoặc kết nối mạng. Tuy nhiên, quản trị viên vẫn chọn địa chỉ mà mỗi máy chủ sẽ nhận; chỉ là lưu nó trong máy chủ. Trong mô hình này, thông tin cấu hình cho mỗi máy chủ được lưu trong một bảng được đánh chỉ mục bằng một định danh khách hàng duy nhất, thường là địa chỉ phần cứng (ví dụ, địa chỉ Ethernet của card mạng).

Một cách dùng DHCP tinh vi hơn giúp quản trị viên mạng không phải gán địa chỉ cho từng máy chủ. Trong mô hình này, máy chủ DHCP duy trì một vùng địa chỉ có sẵn để cấp phát cho các máy chủ khi cần. Điều này giảm đáng kể lượng cấu hình mà quản trị viên phải làm, vì giờ chỉ cần cấp phát một dải địa chỉ IP (tất cả cùng số mạng) cho mỗi mạng.

Vì mục tiêu của DHCP là giảm thiểu cấu hình thủ công cần thiết để máy chủ hoạt động, sẽ vô nghĩa nếu mỗi máy chủ phải được cấu hình với địa chỉ của máy chủ DHCP. Do đó, vấn đề đầu tiên DHCP phải giải quyết là phát hiện máy chủ.

Để liên lạc với máy chủ DHCP, một máy chủ vừa khởi động hoặc vừa kết nối gửi một thông điệp ``DHCPDISCOVER`` tới một địa chỉ IP đặc biệt (255.255.255.255) là địa chỉ broadcast IP. Điều này nghĩa là nó sẽ được tất cả các máy chủ và bộ định tuyến trên mạng nhận. (Bộ định tuyến không chuyển tiếp các gói này sang mạng khác, ngăn broadcast tới toàn Internet.) Trong trường hợp đơn giản nhất, một trong các nút này là máy chủ DHCP cho mạng. Máy chủ sẽ trả lời máy chủ gửi thông điệp khám phá (các nút khác sẽ bỏ qua). Tuy nhiên, không thực tế khi yêu cầu một máy chủ DHCP trên mỗi mạng, vì điều này vẫn tạo ra số lượng lớn máy chủ cần cấu hình đúng và nhất quán. Do đó, DHCP dùng khái niệm *relay agent* (đại lý chuyển tiếp). Có ít nhất một relay agent trên mỗi mạng, và nó chỉ cần cấu hình một thông tin: địa chỉ IP của máy chủ DHCP. Khi một relay agent nhận được thông điệp ``DHCPDISCOVER``, nó gửi đơn lẻ tới máy chủ DHCP và chờ phản hồi, rồi gửi lại cho máy chủ yêu cầu. Quá trình chuyển tiếp thông điệp từ máy chủ tới máy chủ DHCP từ xa được minh họa trong :numref:`Hình %s <fig-dhcp-relay>`.

.. _fig-dhcp-relay:
.. figure:: figures/f03-24-9780123850591.png
   :width: 500px
   :align: center

   Một relay agent DHCP nhận thông điệp broadcast DHCPDISCOVER từ máy chủ và gửi DHCPDISCOVER đơn lẻ tới máy chủ DHCP.

:numref:`Hình %s <fig-dhcp>` dưới đây cho thấy định dạng thông điệp DHCP. Thông điệp thực tế được gửi bằng một giao thức gọi là *User Datagram Protocol* (UDP) chạy trên IP. UDP sẽ được thảo luận chi tiết ở chương sau, nhưng điều duy nhất nó làm ở đây là cung cấp một khóa phân kênh nói rằng, “Đây là một gói DHCP.”

.. _3.3 Internet (IP)
=================

Trong phần trước, chúng ta đã thấy rằng có thể xây dựng các LAN khá lớn bằng cách sử dụng bridge và switch LAN, nhưng các phương pháp này bị giới hạn về khả năng mở rộng và xử lý dị chủng. Trong phần này, chúng ta sẽ khám phá một số cách để vượt qua các giới hạn của mạng bridge, cho phép xây dựng các mạng lớn, dị chủng cao với khả năng định tuyến hợp lý. Chúng ta gọi các mạng như vậy là *liên mạng* (*internetworks*). Chúng ta sẽ tiếp tục thảo luận về cách xây dựng một liên mạng toàn cầu thực sự ở chương tiếp theo, nhưng trước mắt sẽ tìm hiểu các khái niệm cơ bản. Chúng ta bắt đầu bằng cách xem xét kỹ hơn ý nghĩa của từ *internetwork*.

3.3.1 Liên mạng là gì?
----------------------

Chúng ta sử dụng thuật ngữ *liên mạng* (*internetwork*), hoặc đôi khi chỉ là *internet* với chữ *i* thường, để chỉ một tập hợp bất kỳ các mạng được liên kết với nhau nhằm cung cấp một dịch vụ truyền gói tin từ máy chủ tới máy chủ. Ví dụ, một công ty có nhiều chi nhánh có thể xây dựng một liên mạng riêng bằng cách kết nối các LAN tại các địa điểm khác nhau bằng các liên kết điểm-điểm thuê từ công ty điện thoại. Khi nói về liên mạng toàn cầu được sử dụng rộng rãi mà phần lớn các mạng hiện nay đều kết nối tới, chúng ta gọi nó là *Internet* với chữ *I* hoa. Theo cách tiếp cận nguyên lý đầu tiên của cuốn sách này, chúng tôi chủ yếu muốn bạn học về các nguyên lý của liên mạng “chữ *i* thường”, nhưng sẽ minh họa các ý tưởng này bằng các ví dụ thực tế từ Internet “chữ *I* lớn”.

Một thuật ngữ khác cũng dễ gây nhầm lẫn là sự khác biệt giữa mạng, mạng con (subnetworks), và liên mạng. Chúng ta sẽ tránh đề cập đến mạng con (hoặc subnet) cho đến phần sau. Hiện tại, chúng ta dùng *mạng* để chỉ một mạng kết nối trực tiếp hoặc một mạng chuyển mạch như đã mô tả ở phần trước và chương trước. Một mạng như vậy sử dụng một công nghệ, ví dụ như 802.11 hoặc Ethernet. Một *liên mạng* là một tập hợp các mạng như vậy được liên kết với nhau. Đôi khi, để tránh nhầm lẫn, chúng ta gọi các mạng cơ sở mà chúng ta đang liên kết là *mạng vật lý*. Một internet là một *mạng logic* được xây dựng từ một tập hợp các mạng vật lý. Trong bối cảnh này, một tập hợp các đoạn Ethernet được kết nối bằng bridge hoặc switch vẫn được coi là một mạng duy nhất.

.. _fig-inet:
.. figure:: figures/f03-14-9780123850591.png
   :width: 500px
   :align: center

   Một liên mạng đơn giản. H là máy chủ, R là bộ định tuyến.

:numref:`Hình %s <fig-inet>` minh họa một ví dụ về liên mạng. Một liên mạng thường được gọi là “mạng của các mạng” vì nó được tạo thành từ nhiều mạng nhỏ hơn. Trong hình này, ta thấy các mạng Ethernet, một mạng không dây, và một liên kết điểm-điểm. Mỗi cái là một mạng đơn công nghệ. Các nút kết nối các mạng này được gọi là *bộ định tuyến* (*routers*). Chúng đôi khi cũng được gọi là *gateway*, nhưng vì thuật ngữ này có nhiều nghĩa khác, chúng ta sẽ chỉ dùng từ router.

.. _fig-ip-graph:
.. figure:: figures/f03-15-9780123850591.png
   :width: 600px
   :align: center

   Một liên mạng đơn giản, minh họa các tầng giao thức dùng để kết nối H5 tới H8 trong hình trên. ETH là giao thức chạy trên Ethernet.

*Giao thức Internet* là công cụ chủ chốt được sử dụng ngày nay để xây dựng các liên mạng dị chủng, khả mở. Ban đầu nó được gọi là giao thức Kahn-Cerf theo tên các nhà phát minh. Một cách để hình dung IP là nó chạy trên tất cả các nút (cả máy chủ và bộ định tuyến) trong một tập hợp các mạng và định nghĩa hạ tầng cho phép các nút và mạng này hoạt động như một liên mạng logic duy nhất. Ví dụ, :numref:`Hình %s <fig-ip-graph>` cho thấy cách các máy chủ H5 và H8 được kết nối logic bởi internet trong :numref:`Hình %s <fig-inet>`, bao gồm đồ thị giao thức chạy trên mỗi nút. Lưu ý rằng các giao thức tầng cao hơn, như TCP và UDP, thường chạy trên IP ở các máy chủ.

Phần còn lại của chương này và chương tiếp theo sẽ nói về các khía cạnh khác nhau của IP. Dù chắc chắn có thể xây dựng một liên mạng mà không dùng IP—và thực tế, trong những ngày đầu của Internet đã có các giải pháp thay thế—IP là trường hợp thú vị nhất để nghiên cứu đơn giản vì quy mô của Internet. Nói cách khác, chỉ có Internet dựa trên IP mới thực sự đối mặt với vấn đề về khả mở. Do đó, nó cung cấp ví dụ điển hình nhất về một giao thức liên mạng khả mở.

.. sidebar:: Mạng tầng 2 vs tầng 3 (L2 vs L3)

   Như đã thấy ở phần trước, một Ethernet có thể được coi là một *liên kết* điểm-điểm kết nối một cặp switch, với một mạng lưới các switch kết nối tạo thành một *Ethernet chuyển mạch*. Cấu hình này còn gọi là *mạng tầng 2* (*L2 Network*).

   Nhưng như chúng ta sẽ thấy ở phần này, một Ethernet (ngay cả khi được cấu hình điểm-điểm thay vì mạng chia sẻ CSMA/CD) có thể được coi là một *mạng* kết nối một cặp bộ định tuyến, với một mạng lưới các bộ định tuyến như vậy tạo thành một Internet. Cấu hình này còn gọi là *mạng tầng 3* (*L3 Network*).

   Điều gây nhầm lẫn là vì một Ethernet điểm-điểm vừa là một liên kết vừa là một mạng (dù là mạng hai nút tầm thường trong trường hợp thứ hai), tùy thuộc vào việc nó kết nối một cặp switch tầng 2 chạy thuật toán cây bao phủ, hay một cặp bộ định tuyến tầng 3 chạy IP (cộng với các giao thức định tuyến sẽ được mô tả sau trong chương này). Tại sao chọn cấu hình này thay vì cấu hình kia? Một phần phụ thuộc vào việc bạn muốn mạng là một miền quảng bá duy nhất (nếu có, chọn L2), và liệu bạn muốn các máy chủ kết nối tới mạng thuộc các mạng khác nhau (nếu có, chọn L3).

   Tin tốt là khi bạn hiểu rõ ý nghĩa của sự song hành này, bạn đã vượt qua một rào cản lớn trong việc làm chủ các mạng chuyển mạch gói hiện đại.

3.3.2 Mô hình dịch vụ
---------------------

Một điểm khởi đầu tốt khi xây dựng một liên mạng là xác định *mô hình dịch vụ* của nó, tức là các dịch vụ từ máy chủ tới máy chủ mà bạn muốn cung cấp. Mối quan tâm chính khi xác định mô hình dịch vụ cho một liên mạng là chúng ta chỉ có thể cung cấp dịch vụ máy chủ tới máy chủ nếu dịch vụ này có thể được cung cấp trên mỗi mạng vật lý bên dưới. Ví dụ, sẽ không có ý nghĩa gì nếu quyết định rằng mô hình dịch vụ liên mạng sẽ đảm bảo chuyển phát mọi gói tin trong 1 ms hoặc ít hơn nếu có các công nghệ mạng bên dưới có thể trì hoãn gói tin tùy ý. Triết lý khi xác định mô hình dịch vụ IP, do đó, là làm cho nó đủ đơn giản để hầu như bất kỳ công nghệ mạng nào có thể xuất hiện trong một liên mạng đều có thể cung cấp dịch vụ cần thiết.

Mô hình dịch vụ IP có thể được coi là gồm hai phần: một sơ đồ địa chỉ, cung cấp cách nhận diện tất cả các máy chủ trong liên mạng, và một mô hình truyền dữ liệu kiểu datagram (không kết nối). Mô hình dịch vụ này đôi khi được gọi là *best effort* (nỗ lực tối đa) vì, dù IP cố gắng hết sức để chuyển phát datagram, nó không đưa ra bất kỳ đảm bảo nào. Chúng ta sẽ hoãn thảo luận về sơ đồ địa chỉ và trước tiên xem xét mô hình truyền dữ liệu.

Truyền datagram
~~~~~~~~~~~~~~~

Datagram IP là nền tảng của Giao thức Internet. Nhớ lại từ phần trước rằng một datagram là một gói tin được gửi theo kiểu không kết nối qua mạng. Mỗi datagram mang đủ thông tin để mạng có thể chuyển tiếp gói tin tới đích đúng; không cần bất kỳ cơ chế thiết lập trước nào để báo cho mạng biết phải làm gì khi gói tin đến. Bạn chỉ cần gửi nó đi, và mạng sẽ cố gắng hết sức để chuyển nó tới đích mong muốn. Phần “nỗ lực tối đa” nghĩa là nếu có sự cố và gói tin bị mất, bị lỗi, bị chuyển nhầm, hoặc bằng cách nào đó không đến được đích, mạng sẽ không làm gì cả—nó đã cố gắng hết sức, và đó là tất cả những gì nó phải làm. Nó không cố gắng phục hồi từ lỗi. Điều này đôi khi được gọi là dịch vụ *không tin cậy*.

Dịch vụ không kết nối, nỗ lực tối đa là dịch vụ đơn giản nhất mà bạn có thể yêu cầu từ một liên mạng, và đó cũng là điểm mạnh lớn nhất của nó. Ví dụ, nếu bạn cung cấp dịch vụ nỗ lực tối đa trên một mạng cung cấp dịch vụ tin cậy, thì cũng tốt—bạn sẽ có một dịch vụ nỗ lực tối đa mà tình cờ luôn chuyển phát gói tin. Ngược lại, nếu bạn có một mô hình dịch vụ tin cậy trên một mạng không tin cậy, bạn sẽ phải bổ sung rất nhiều chức năng vào các bộ định tuyến để bù đắp cho các thiếu sót của mạng bên dưới. Giữ cho các bộ định tuyến càng đơn giản càng tốt là một trong những mục tiêu thiết kế ban đầu của IP.

Khả năng “chạy trên bất cứ thứ gì” của IP thường được coi là một trong những đặc điểm quan trọng nhất của nó. Đáng chú ý là nhiều công nghệ mà IP chạy trên ngày nay chưa tồn tại khi IP được phát minh. Cho đến nay, chưa có công nghệ mạng nào được phát minh mà IP không thể chạy được. Về nguyên tắc, IP có thể chạy trên một mạng truyền thông bằng chim bồ câu.

Dịch vụ nỗ lực tối đa không chỉ có nghĩa là gói tin có thể bị mất. Đôi khi chúng có thể được chuyển phát không theo thứ tự, và đôi khi cùng một gói tin có thể được chuyển phát nhiều lần. Các giao thức tầng cao hơn hoặc ứng dụng chạy trên IP cần nhận thức được tất cả các kiểu lỗi có thể xảy ra này.

Định dạng gói tin
~~~~~~~~~~~~~~~~~

Rõ ràng, một phần quan trọng của mô hình dịch vụ IP là kiểu gói tin có thể được mang. Datagram IP, giống như hầu hết các gói tin, gồm một tiêu đề (header) theo sau là một số byte dữ liệu. Định dạng của tiêu đề được minh họa trong :numref:`Hình %s <fig-iphead>`. Lưu ý rằng chúng tôi đã áp dụng một kiểu biểu diễn gói tin khác với các chương trước. Đó là vì các định dạng gói tin ở tầng liên mạng và cao hơn, nơi chúng ta sẽ tập trung trong các chương tới, gần như luôn được thiết kế để căn chỉnh theo biên 32 bit để đơn giản hóa xử lý trong phần mềm. Do đó, cách biểu diễn phổ biến (dùng trong các RFC của Internet, chẳng hạn) là vẽ chúng như một chuỗi các từ 32 bit. Từ trên cùng là từ được truyền trước, và byte ngoài cùng bên trái của mỗi từ là byte được truyền trước. Trong biểu diễn này, bạn có thể dễ dàng nhận ra các trường có độ dài là bội số của 8 bit. Khi có trường không phải là bội số của 8 bit, bạn có thể xác định độ dài trường bằng cách nhìn vào vị trí bit ở đầu gói tin.

.. _fig-iphead:
.. figure:: figures/f03-16-9780123850591.png
   :width: 450px
   :align: center

   Tiêu đề gói tin IPv4.

Khi xem xét từng trường trong tiêu đề IP, ta thấy rằng mô hình đơn giản về truyền datagram nỗ lực tối đa vẫn có một số tính năng tinh vi. Trường ``Version`` chỉ định phiên bản của IP. Phiên bản IP vẫn được giả định hiện nay là 4, thường gọi là *IPv4*. Lưu ý rằng đặt trường này ngay đầu datagram giúp dễ dàng định nghĩa lại mọi thứ khác trong định dạng gói tin ở các phiên bản sau; phần mềm xử lý tiêu đề bắt đầu bằng cách nhìn vào phiên bản rồi xử lý phần còn lại của gói tin theo định dạng phù hợp. Trường tiếp theo, ``HLen``, chỉ độ dài tiêu đề tính theo từ 32 bit. Khi không có tùy chọn, điều này thường là 5 từ (20 byte). Trường ``TOS`` (type of service) 8 bit đã có nhiều định nghĩa khác nhau qua các năm, nhưng chức năng cơ bản là cho phép các gói tin được xử lý khác nhau tùy theo nhu cầu ứng dụng. Ví dụ, giá trị ``TOS`` có thể quyết định liệu một gói tin có nên được đặt vào hàng đợi đặc biệt nhận độ trễ thấp hay không.

16 bit tiếp theo của tiêu đề chứa trường ``Length`` của datagram, bao gồm cả tiêu đề. Không giống trường ``HLen``, trường ``Length`` tính theo byte chứ không phải từ. Do đó, kích thước tối đa của một datagram IP là 65.535 byte. Tuy nhiên, mạng vật lý mà IP chạy trên đó có thể không hỗ trợ các gói tin dài như vậy. Vì lý do này, IP hỗ trợ quá trình phân mảnh và lắp ráp lại. Từ thứ hai của tiêu đề chứa thông tin về phân mảnh, chi tiết sẽ được trình bày ở phần sau “Phân mảnh và lắp ráp lại”.

Tiếp theo, byte đầu tiên của từ thứ ba trong tiêu đề là trường ``TTL`` (time to live). Tên trường này phản ánh ý nghĩa lịch sử hơn là cách sử dụng hiện nay. Ý định của trường này là bắt các gói tin bị lặp trong các vòng lặp định tuyến và loại bỏ chúng, thay vì để chúng tiêu tốn tài nguyên vô hạn. Ban đầu, ``TTL`` được đặt thành một số giây cụ thể mà gói tin được phép tồn tại, và các bộ định tuyến trên đường đi sẽ giảm trường này cho đến khi nó về 0. Tuy nhiên, vì hiếm khi một gói tin phải chờ tới 1 giây trong một bộ định tuyến, và các bộ định tuyến không có đồng hồ chung, hầu hết các bộ định tuyến chỉ giảm ``TTL`` đi 1 mỗi khi chuyển tiếp gói tin. Do đó, nó trở thành bộ đếm số bước nhảy hơn là bộ đếm thời gian, nhưng vẫn là cách tốt để bắt các gói tin bị kẹt trong vòng lặp định tuyến. Một điểm tinh tế là giá trị khởi tạo trường này do máy gửi đặt: Nếu đặt quá cao, gói tin có thể lặp lại nhiều lần trước khi bị loại bỏ; nếu đặt quá thấp, có thể không đến được đích. Giá trị mặc định hiện nay là 64.

Trường ``Protocol`` đơn giản là một khóa phân kênh xác định giao thức tầng cao hơn mà gói tin IP này nên chuyển tới. Có các giá trị xác định cho TCP (Transmission Control Protocol—6), UDP (User Datagram Protocol—17), và nhiều giao thức khác có thể nằm trên IP trong đồ thị giao thức.

Trường ``Checksum`` được tính bằng cách coi toàn bộ tiêu đề IP là một chuỗi các từ 16 bit, cộng chúng lại bằng toán học bù một, và lấy bù một của kết quả. Do đó, nếu bất kỳ bit nào trong tiêu đề bị lỗi khi truyền, checksum sẽ không đúng khi nhận gói tin. Vì một tiêu đề bị lỗi có thể chứa lỗi ở địa chỉ đích—và do đó có thể đã bị chuyển nhầm—nên hợp lý khi loại bỏ bất kỳ gói tin nào không vượt qua kiểm tra checksum. Lưu ý rằng loại checksum này không có khả năng phát hiện lỗi mạnh như CRC, nhưng dễ tính toán hơn nhiều trong phần mềm.

Hai trường bắt buộc cuối cùng trong tiêu đề là ``SourceAddr`` và ``DestinationAddr`` của gói tin. Trường sau là chìa khóa cho truyền datagram: Mỗi gói tin chứa địa chỉ đầy đủ của đích để các quyết định chuyển tiếp có thể được thực hiện tại mỗi bộ định tuyến. Địa chỉ nguồn là cần thiết để người nhận quyết định có nhận gói tin không và để trả lời. Địa chỉ IP sẽ được thảo luận ở phần sau—hiện tại, điều quan trọng là IP định nghĩa không gian địa chỉ toàn cục riêng, độc lập với bất kỳ mạng vật lý nào mà nó chạy trên đó. Như chúng ta sẽ thấy, đây là một trong những chìa khóa để hỗ trợ dị chủng.

Cuối cùng, có thể có một số tùy chọn ở cuối tiêu đề. Có hay không các tùy chọn này có thể xác định bằng cách kiểm tra trường độ dài tiêu đề (``HLen``). Dù các tùy chọn này khá hiếm khi dùng, một triển khai IP đầy đủ phải xử lý được tất cả.

Phân mảnh và lắp ráp lại
~~~~~~~~~~~~~~~~~~~~~~~~

Một trong những vấn đề khi cung cấp mô hình dịch vụ máy chủ tới máy chủ thống nhất trên một tập hợp dị chủng các mạng là mỗi công nghệ mạng thường có ý tưởng riêng về kích thước gói tin tối đa. Ví dụ, Ethernet cổ điển có thể nhận các gói tin dài tới 1500 byte, nhưng các biến thể hiện đại có thể truyền các gói lớn hơn (jumbo) mang tới 9000 byte dữ liệu. Điều này để lại hai lựa chọn cho mô hình dịch vụ IP: Đảm bảo rằng mọi datagram IP đều đủ nhỏ để vừa trong một gói tin trên bất kỳ công nghệ mạng nào, hoặc cung cấp một cơ chế để các gói tin có thể bị phân mảnh và lắp ráp lại khi chúng quá lớn để truyền qua một công nghệ mạng nhất định. Lựa chọn thứ hai hóa ra là tốt, đặc biệt khi bạn cân nhắc rằng các công nghệ mạng mới luôn xuất hiện, và IP cần chạy trên tất cả; điều này sẽ khiến việc chọn một giới hạn nhỏ phù hợp cho kích thước datagram trở nên khó khăn. Điều này cũng có nghĩa là máy chủ sẽ không gửi các gói tin nhỏ không cần thiết, gây lãng phí băng thông và tiêu tốn tài nguyên xử lý do phải có nhiều tiêu đề hơn cho mỗi byte dữ liệu gửi đi.

Ý tưởng trung tâm ở đây là mỗi loại mạng đều có một *đơn vị truyền tối đa* (MTU), là datagram IP lớn nhất mà nó có thể mang trong một frame. Lưu ý rằng giá trị này nhỏ hơn kích thước gói tin lớn nhất trên mạng vì datagram IP cần vừa trong *payload* của frame tầng liên kết.

.. [#] Trong mạng ATM, MTU may mắn là lớn hơn nhiều so với một cell, vì ATM có cơ chế phân mảnh và lắp ráp riêng. Frame tầng liên kết trong ATM gọi là *convergence-sublayer protocol data unit* (CS-PDU).

Khi một máy chủ gửi một datagram IP, nó có thể chọn bất kỳ kích thước nào nó muốn. Một lựa chọn hợp lý là MTU của mạng mà máy chủ kết nối trực tiếp. Khi đó, phân mảnh chỉ cần thiết nếu đường đi tới đích có mạng với MTU nhỏ hơn. Nếu giao thức tầng vận chuyển trên IP đưa cho IP một gói lớn hơn MTU cục bộ, máy chủ nguồn phải phân mảnh nó.

Phân mảnh thường xảy ra ở một bộ định tuyến khi nó nhận một datagram mà nó muốn chuyển tiếp qua một mạng có MTU nhỏ hơn datagram nhận được. Để các mảnh này có thể được lắp ráp lại ở máy chủ nhận, tất cả đều mang cùng một định danh trong trường ``Ident``. Định danh này do máy chủ gửi chọn và phải là duy nhất trong số các datagram có thể đến đích từ nguồn này trong một khoảng thời gian hợp lý. Vì tất cả các mảnh của datagram gốc đều chứa định danh này, máy chủ lắp ráp sẽ nhận ra các mảnh thuộc về nhau. Nếu không phải tất cả các mảnh đều đến được máy chủ nhận, máy chủ sẽ từ bỏ quá trình lắp ráp và loại bỏ các mảnh đã nhận. IP không cố gắng phục hồi các mảnh bị mất.

.. _fig-frag:
.. figure:: figures/f03-17-9780123850591.png
   :width: 600px
   :align: center

   Các datagram IP đi qua chuỗi các mạng vật lý như trong hình trước.

Để hiểu ý nghĩa của điều này, hãy xem điều gì xảy ra khi máy chủ H5 gửi một datagram tới H8 trong ví dụ internet ở :numref:`Hình %s <fig-inet>`. Giả sử MTU là 1500 byte cho hai mạng Ethernet và mạng 802.11, và 532 byte cho mạng điểm-điểm, thì một datagram 1420 byte (20 byte tiêu đề IP cộng 1400 byte dữ liệu) gửi từ H5 sẽ đi qua mạng 802.11 và Ethernet đầu tiên mà không bị phân mảnh nhưng phải bị phân mảnh thành ba datagram tại bộ định tuyến R2. Ba mảnh này sau đó được R3 chuyển tiếp qua Ethernet thứ hai tới máy chủ đích. Tình huống này được minh họa trong :numref:`Hình %s <fig-frag>`. Hình này cũng củng cố hai điểm quan trọng:

1. Mỗi mảnh là một datagram IP độc lập được truyền qua một chuỗi các mạng vật lý, độc lập với các mảnh khác.

2. Mỗi datagram IP được đóng gói lại cho mỗi mạng vật lý mà nó đi qua.

.. _fig-fragment:
.. figure:: figures/f03-18-9780123850591.png
   :align: center
   :width: 350px

   Các trường tiêu đề dùng trong phân mảnh IP: (a) gói chưa phân mảnh; (b) các gói đã phân mảnh.

Quá trình phân mảnh có thể hiểu chi tiết bằng cách xem các trường tiêu đề của từng datagram, như minh họa trong :numref:`Hình %s <fig-fragment>`. Gói chưa phân mảnh ở trên cùng có 1400 byte dữ liệu và 20 byte tiêu đề IP. Khi gói đến bộ định tuyến R2, có MTU là 532 byte, nó phải bị phân mảnh. MTU 532 byte để lại 512 byte cho dữ liệu sau khi trừ 20 byte tiêu đề IP, nên mảnh đầu tiên chứa 512 byte dữ liệu. Bộ định tuyến đặt bit M trong trường ``Flags`` (xem :numref:`Hình %s <fig-iphead>`), nghĩa là còn các mảnh tiếp theo, và đặt ``Offset`` là 0, vì mảnh này chứa phần đầu của datagram gốc. Dữ liệu trong mảnh thứ hai bắt đầu từ byte thứ 513 của dữ liệu gốc, nên trường ``Offset`` trong tiêu đề này được đặt là 64, tức là 512/8. Tại sao chia cho 8? Vì các nhà thiết kế IP quyết định rằng phân mảnh luôn xảy ra trên biên 8 byte, nghĩa là trường ``Offset`` đếm theo đơn vị 8 byte, không phải byte. (Bạn có thể tự tìm hiểu lý do thiết kế này.) Mảnh thứ ba chứa 376 byte dữ liệu cuối cùng, và offset là 2 × 512/8 = 128. Vì đây là mảnh cuối cùng, bit M không được đặt.

Lưu ý rằng quá trình phân mảnh được thực hiện sao cho nó có thể lặp lại nếu một mảnh đến một mạng khác có MTU còn nhỏ hơn. Phân mảnh tạo ra các datagram IP nhỏ hơn, hợp lệ, có thể dễ dàng lắp ráp lại thành datagram gốc khi nhận, bất kể thứ tự các mảnh đến. Việc lắp ráp lại được thực hiện tại máy chủ nhận, không phải tại mỗi bộ định tuyến.

Việc lắp ráp lại IP không hề đơn giản. Ví dụ, nếu một mảnh bị mất, máy nhận vẫn cố gắng lắp ráp datagram, và cuối cùng sẽ từ bỏ và phải giải phóng tài nguyên đã dùng cho quá trình lắp ráp thất bại. Việc một máy chủ giữ tài nguyên vô ích có thể là cơ sở cho một cuộc tấn công từ chối dịch vụ.

Vì lý do này và các lý do khác, phân mảnh IP thường được coi là điều nên tránh. Hiện nay, các máy chủ được khuyến khích mạnh mẽ thực hiện “khám phá MTU đường đi” (path MTU discovery), một quá trình giúp tránh phân mảnh bằng cách gửi các gói đủ nhỏ để đi qua liên kết có MTU nhỏ nhất trên đường từ nguồn tới đích.

3.3.3 Địa chỉ toàn cục
----------------------

Trong phần thảo luận về mô hình dịch vụ IP ở trên, chúng ta đã đề cập rằng một trong những điều mà nó cung cấp là một sơ đồ địa chỉ. Rốt cuộc, nếu bạn muốn gửi dữ liệu tới bất kỳ máy chủ nào trên bất kỳ mạng nào, cần có cách nhận diện tất cả các máy chủ. Do đó, chúng ta cần một sơ đồ địa chỉ toàn cục—trong đó không có hai máy chủ nào có cùng địa chỉ. Tính duy nhất toàn cục là thuộc tính đầu tiên mà một sơ đồ địa chỉ phải cung cấp.

Địa chỉ Ethernet là duy nhất toàn cục, nhưng chỉ điều đó thôi là chưa đủ cho một sơ đồ địa chỉ trong một liên mạng lớn. Địa chỉ Ethernet cũng là *phẳng*, nghĩa là chúng không có cấu trúc và cung cấp rất ít thông tin cho các giao thức định tuyến. (Thực tế, địa chỉ Ethernet có cấu trúc cho mục đích *cấp phát*—24 bit đầu nhận diện nhà sản xuất—nhưng điều này không giúp ích gì cho định tuyến vì cấu trúc này không liên quan tới cấu trúc mạng.) Ngược lại, địa chỉ IP là *phân cấp*, nghĩa là chúng gồm nhiều phần tương ứng với một dạng phân cấp nào đó trong liên mạng. Cụ thể, địa chỉ IP gồm hai phần, thường gọi là phần *mạng* và phần *máy chủ*. Đây là một cấu trúc khá hợp lý cho một liên mạng, vốn được tạo thành từ nhiều mạng liên kết với nhau. Phần mạng của địa chỉ IP nhận diện mạng mà máy chủ kết nối; tất cả các máy chủ kết nối cùng một mạng có cùng phần mạng trong địa chỉ IP. Phần máy chủ sau đó nhận diện từng máy chủ duy nhất trên mạng đó. Do đó, trong liên mạng đơn giản ở :numref:`Hình %s <fig-inet>`, các địa chỉ của các máy chủ trên mạng 1, ví dụ, đều có cùng phần mạng và phần máy chủ khác nhau.

Lưu ý rằng các bộ định tuyến trong :numref:`Hình %s <fig-inet>` kết nối tới hai mạng. Chúng cần có một địa chỉ trên mỗi mạng, một cho mỗi giao diện. Ví dụ, bộ định tuyến R1, nằm giữa mạng không dây và một Ethernet, có một địa chỉ IP trên giao diện tới mạng không dây với phần mạng giống tất cả các máy chủ trên mạng đó. Nó cũng có một địa chỉ IP trên giao diện tới Ethernet có phần mạng giống các máy chủ trên Ethernet đó. Do đó, lưu ý rằng một bộ định tuyến có thể được triển khai như một máy chủ với hai giao diện mạng, nên chính xác hơn là coi địa chỉ IP thuộc về giao diện hơn là thuộc về máy chủ.

Vậy các địa chỉ phân cấp này trông như thế nào? Không giống một số dạng địa chỉ phân cấp khác, kích thước của hai phần không giống nhau cho mọi địa chỉ. Ban đầu, địa chỉ IP được chia thành ba lớp khác nhau, như minh họa trong :numref:`Hình %s <fig-class>`, mỗi lớp định nghĩa kích thước phần mạng và phần máy chủ khác nhau. (Cũng có địa chỉ lớp D dùng cho nhóm multicast và lớp E hiện chưa dùng.) Trong mọi trường hợp, địa chỉ dài 32 bit.

Lớp của một địa chỉ IP được xác định ở một vài bit quan trọng nhất. Nếu bit đầu là 0, đó là địa chỉ lớp A. Nếu bit đầu là 1 và bit thứ hai là 0, đó là lớp B. Nếu hai bit đầu là 1 và bit thứ ba là 0, đó là lớp C. Do đó, trong khoảng 4 tỷ địa chỉ IP có thể có, một nửa là lớp A, một phần tư là lớp B, và một phần tám là lớp C. Mỗi lớp cấp phát một số bit nhất định cho phần mạng và phần còn lại cho phần máy chủ. Mạng lớp A có 7 bit cho phần mạng và 24 bit cho phần máy chủ, nghĩa là chỉ có 126 mạng lớp A (giá trị 0 và 127 được dành riêng), nhưng mỗi mạng có thể chứa tới :math:`2^{24} - 2` (khoảng 16 triệu) máy chủ (lại có hai giá trị dành riêng). Địa chỉ lớp B cấp phát 14 bit cho mạng và 16 bit cho máy chủ, nghĩa là mỗi mạng lớp B chứa tối đa 65.534 máy chủ. Cuối cùng, địa chỉ lớp C chỉ có 8 bit cho máy chủ và 21 bit cho phần mạng. Do đó, một mạng lớp C chỉ có thể có 256 định danh máy chủ duy nhất, tức là chỉ 254 máy chủ kết nối (một định danh máy chủ, 255, dành cho broadcast, và 0 không hợp lệ). Tuy nhiên, sơ đồ địa chỉ này hỗ trợ 2\ :sup:`21` mạng lớp C.

.. _fig-class:
.. figure:: figures/f03-19-9780123850591.png
   :width: 350px
   :align: center

   Địa chỉ IP: (a) lớp A; (b) lớp B; (c) lớp C.

Nhìn bề ngoài, sơ đồ địa chỉ này có nhiều linh hoạt, cho phép các mạng có kích thước rất khác nhau được hỗ trợ khá hiệu quả. Ý tưởng ban đầu là Internet sẽ gồm một số ít mạng diện rộng (là mạng lớp A), một số vừa phải các mạng quy mô site (campus) (là mạng lớp B), và rất nhiều LAN (là mạng lớp C). Tuy nhiên, hóa ra nó không đủ linh hoạt, như chúng ta sẽ thấy ngay sau đây. Ngày nay, địa chỉ IP thường là “không phân lớp”; chi tiết sẽ được giải thích bên dưới.

Trước khi xem cách địa chỉ IP được sử dụng, sẽ hữu ích khi xem một số vấn đề thực tế, như cách ghi lại chúng. Theo quy ước, địa chỉ IP được viết dưới dạng bốn số nguyên *thập phân* cách nhau bằng dấu chấm. Mỗi số nguyên biểu diễn giá trị thập phân chứa trong 1 byte của địa chỉ, bắt đầu từ byte quan trọng nhất. Ví dụ, địa chỉ của máy tính mà câu này được gõ là ``171.69.210.245``.

Điều quan trọng là không nhầm lẫn địa chỉ IP với tên miền Internet, vốn cũng là phân cấp. Tên miền thường là chuỗi ASCII cách nhau bằng dấu chấm, như ``cs.princeton.edu``. Điều quan trọng về địa chỉ IP là chúng được mang trong tiêu đề các gói tin IP, và chính các địa chỉ này được dùng trong các bộ định tuyến IP để quyết định chuyển tiếp.

3.3.4 Chuyển tiếp datagram trong IP
-----------------------------------

Giờ chúng ta đã sẵn sàng xem xét cơ chế cơ bản mà các bộ định tuyến IP dùng để chuyển tiếp datagram trong một liên mạng. Nhớ lại từ phần trước rằng *chuyển tiếp* là quá trình lấy một gói tin từ đầu vào và gửi nó ra đầu ra phù hợp, trong khi *định tuyến* là quá trình xây dựng các bảng cho phép xác định đầu ra đúng cho một gói tin. Phần thảo luận ở đây tập trung vào chuyển tiếp; chúng ta sẽ bàn về định tuyến ở phần sau.

Các điểm chính cần nhớ khi thảo luận về chuyển tiếp datagram IP là:

-  Mỗi datagram IP chứa địa chỉ IP của máy chủ đích.

-  Phần mạng của một địa chỉ IP nhận diện duy nhất một mạng vật lý là một phần của Internet lớn hơn.

-  Tất cả các máy chủ và bộ định tuyến có cùng phần mạng trong địa chỉ đều kết nối tới cùng một mạng vật lý và do đó có thể giao tiếp với nhau bằng cách gửi frame qua mạng đó.

-  Mỗi mạng vật lý là một phần của Internet đều có ít nhất một bộ định tuyến, theo định nghĩa, cũng kết nối tới ít nhất một mạng vật lý khác; bộ định tuyến này có thể trao đổi gói tin với các máy chủ hoặc bộ định tuyến trên cả hai mạng.

Do đó, chuyển tiếp datagram IP có thể được xử lý như sau. Một datagram được gửi từ máy chủ nguồn tới máy chủ đích, có thể đi qua nhiều bộ định tuyến trên đường. Bất kỳ nút nào, dù là máy chủ hay bộ định tuyến, trước tiên cố gắng xác định xem nó có kết nối tới cùng mạng vật lý với đích không. Để làm điều này, nó so sánh phần mạng của địa chỉ đích với phần mạng của địa chỉ trên từng giao diện mạng của nó. (Máy chủ thường chỉ có một giao diện, trong khi bộ định tuyến thường có hai hoặc nhiều hơn, vì chúng thường kết nối tới hai hoặc nhiều mạng.) Nếu có trùng khớp, nghĩa là đích nằm trên cùng mạng vật lý với giao diện đó, và gói tin có thể được chuyển trực tiếp qua mạng đó. Một phần sau sẽ giải thích chi tiết quá trình này.

Nếu nút không kết nối tới cùng mạng vật lý với nút đích, nó cần gửi datagram tới một bộ định tuyến. Thông thường, mỗi nút sẽ có nhiều lựa chọn bộ định tuyến, nên nó cần chọn bộ định tuyến tốt nhất, hoặc ít nhất là một bộ định tuyến có khả năng đưa datagram tới gần đích hơn. Bộ định tuyến mà nó chọn gọi là *bước nhảy tiếp theo* (*next hop*). Bộ định tuyến tìm bước nhảy tiếp theo đúng bằng cách tra cứu bảng chuyển tiếp của nó. Bảng chuyển tiếp về mặt khái niệm chỉ là một danh sách các cặp ``(NetworkNum, NextHop)``. (Như sẽ thấy bên dưới, bảng chuyển tiếp thực tế thường chứa thêm một số thông tin liên quan tới bước nhảy tiếp theo.) Thông thường, cũng có một bộ định tuyến mặc định được dùng nếu không có mục nào trong bảng khớp với số mạng đích. Đối với một máy chủ, hoàn toàn chấp nhận được nếu chỉ có một bộ định tuyến mặc định và không có gì khác—nghĩa là tất cả các datagram gửi tới các máy chủ không nằm trên mạng vật lý mà máy chủ gửi kết nối sẽ được gửi qua bộ định tuyến mặc định.

Chúng ta có thể mô tả thuật toán chuyển tiếp datagram như sau:

::

   if (NetworkNum of destination = NetworkNum of one of my interfaces) then
       deliver packet to destination over that interface
   else
       if (NetworkNum of destination is in my forwarding table) then
           deliver packet to NextHop router
       else
           deliver packet to default router

Đối với một máy chủ chỉ có một giao diện và chỉ có một bộ định tuyến mặc định trong bảng chuyển tiếp, điều này đơn giản thành

::

   if (NetworkNum of destination = my NetworkNum) then
       deliver packet to destination directly
   else
       deliver packet to default router

Hãy xem cách hoạt động này trong ví dụ liên mạng ở :numref:`Hình %s <fig-inet>`. Đầu tiên, giả sử H1 muốn gửi một datagram tới H2. Vì chúng nằm trên cùng mạng vật lý, H1 và H2 có cùng số mạng trong địa chỉ IP. Do đó, H1 suy ra rằng nó có thể chuyển datagram trực tiếp tới H2 qua Ethernet. Vấn đề duy nhất cần giải quyết là làm sao H1 biết địa chỉ Ethernet đúng của H2—cơ chế giải quyết sẽ được mô tả ở phần sau.

Giờ giả sử H5 muốn gửi một datagram tới H8. Vì hai máy chủ này nằm trên các mạng vật lý khác nhau, chúng có số mạng khác nhau, nên H5 suy ra rằng nó cần gửi datagram tới một bộ định tuyến. R1 là lựa chọn duy nhất—bộ định tuyến mặc định—nên H1 gửi datagram qua mạng không dây tới R1. Tương tự, R1 biết rằng nó không thể chuyển datagram trực tiếp tới H8 vì không giao diện nào của R1 nằm trên cùng mạng với H8. Giả sử bộ định tuyến mặc định của R1 là R2; R1 khi đó gửi datagram tới R2 qua Ethernet. Giả sử R2 có bảng chuyển tiếp như trong :numref:`Bảng %s <tab-ipfwdtab>`, nó tra cứu số mạng của H8 (mạng 4) và chuyển tiếp datagram qua mạng điểm-điểm tới R3. Cuối cùng, R3, vì nằm trên cùng mạng với H8, chuyển tiếp datagram trực tiếp tới H8.

.. _tab-ipfwdtab:
.. table:: Bảng chuyển tiếp cho Bộ định tuyến R2.
   :align: center
   :widths: auto

   +------------+---------+
   | NetworkNum | NextHop |
   +============+=========+
   | 1          | R1      |
   +------------+---------+
   | 4          | R3      |
   +------------+---------+

Lưu ý rằng có thể đưa thông tin về các mạng kết nối trực tiếp vào bảng chuyển tiếp. Ví dụ, ta có thể gán nhãn các giao diện mạng của bộ định tuyến R2 là giao diện 0 cho liên kết điểm-điểm (mạng 3) và giao diện 1 cho Ethernet (mạng 2). Khi đó R2 sẽ có bảng chuyển tiếp như trong :numref:`Bảng %s <tab-ipfwdtab2>`.

.. _tab-ipfwdtab2:
.. table:: Bảng chuyển tiếp đầy đủ cho Bộ định tuyến R2.
   :align: center
   :widths: auto

   +------------+-------------+
   | NetworkNum | NextHop     |
   +============+=============+
   | 1          | R1          |
   +------------+-------------+
   | 2          | Interface 1 |
   +------------+-------------+
   | 3          | Interface 0 |
   +------------+-------------+
   | 4          | R3          |
   +------------+-------------+

Như vậy, với bất kỳ số mạng nào mà R2 gặp trong một gói tin, nó đều biết phải làm gì. Hoặc mạng đó kết nối trực tiếp với R2, khi đó gói tin có thể được chuyển trực tiếp tới đích qua mạng đó, hoặc mạng đó có thể tới được qua một bộ định tuyến bước nhảy tiếp theo mà R2 có thể tới qua một mạng mà nó kết nối. Trong cả hai trường hợp, R2 sẽ dùng ARP, mô tả bên dưới, để tìm địa chỉ MAC của nút mà gói tin sẽ được gửi tới tiếp theo.

Bảng chuyển tiếp mà R2 dùng đủ đơn giản để có thể cấu hình thủ công. Tuy nhiên, thường thì các bảng này phức tạp hơn và sẽ được xây dựng bằng cách chạy một giao thức định tuyến như một trong các giao thức sẽ mô tả ở phần sau. Cũng lưu ý rằng, trên thực tế, các số mạng thường dài hơn (ví dụ, 128.96).

Giờ ta có thể thấy cách địa chỉ phân cấp—chia địa chỉ thành phần mạng và phần máy chủ—đã cải thiện khả năng mở rộng của một mạng lớn. Các bộ định tuyến giờ chỉ cần bảng chuyển tiếp liệt kê một tập số mạng thay vì tất cả các nút trong mạng. Trong ví dụ đơn giản của chúng ta, điều đó nghĩa là R2 có thể lưu thông tin cần thiết để tới tất cả các máy chủ trong mạng (có tám máy chủ) chỉ trong một bảng bốn mục. Ngay cả khi có 100 máy chủ trên mỗi mạng vật lý, R2 vẫn chỉ cần bốn mục đó. Đây là một bước đầu tốt (dù chưa phải cuối cùng) để đạt được khả năng mở rộng.

.. _key-aggregation:
.. admonition:: Bài học then chốt

   Điều này minh họa một trong những nguyên lý quan trọng nhất khi xây dựng mạng khả mở: Để đạt được khả năng mở rộng, bạn cần giảm lượng thông tin lưu trữ ở mỗi nút và trao đổi giữa các nút. Cách phổ biến nhất để làm điều đó là *kết tụ phân cấp* (hierarchical aggregation). IP giới thiệu một phân cấp hai tầng, với mạng ở tầng trên và nút ở tầng dưới. Ta đã kết tụ thông tin bằng cách để bộ định tuyến chỉ xử lý việc tới đúng mạng; thông tin mà bộ định tuyến cần để chuyển một datagram tới bất kỳ nút nào trên một mạng nhất định được đại diện bởi một mẩu thông tin kết tụ duy nhất. :ref:`[Tiếp theo] <key-best-effort>`

3.3.5 Địa chỉ con và địa chỉ không phân lớp
-------------------------------------------

Ý định ban đầu của địa chỉ IP là phần mạng sẽ nhận diện duy nhất đúng một mạng vật lý. Hóa ra cách tiếp cận này có một số nhược điểm. Hãy tưởng tượng một campus lớn có nhiều mạng nội bộ và quyết định kết nối Internet. Với mỗi mạng, dù nhỏ đến đâu, site cần ít nhất một địa chỉ mạng lớp C. Tệ hơn, với bất kỳ mạng nào có hơn 255 máy chủ, họ cần một địa chỉ lớp B. Điều này có vẻ không lớn, và thực tế không phải khi Internet mới hình thành, nhưng chỉ có một số lượng hữu hạn số mạng, và số địa chỉ lớp B còn ít hơn lớp C nhiều. Địa chỉ lớp B đặc biệt được ưa chuộng vì bạn không bao giờ biết mạng của mình có thể mở rộng vượt quá 255 nút hay không, nên tốt hơn là dùng địa chỉ lớp B ngay từ đầu hơn là phải đánh lại địa chỉ khi hết chỗ trên mạng lớp C. Vấn đề ở đây là sự kém hiệu quả trong cấp phát địa chỉ: Một mạng chỉ có hai nút dùng hết một địa chỉ mạng lớp C, lãng phí 253 địa chỉ hữu ích; một mạng lớp B có hơn 255 máy chủ một chút lãng phí hơn 64.000 địa chỉ.

Việc cấp phát một số mạng cho mỗi mạng vật lý, do đó, làm tiêu tốn không gian địa chỉ IP nhanh hơn mong muốn. Dù cần kết nối hơn 4 tỷ máy chủ để dùng hết tất cả địa chỉ hợp lệ, chỉ cần kết nối 2\ :sup:`14` (khoảng 16.000) mạng lớp B là hết phần không gian địa chỉ này. Do đó, ta muốn tìm cách sử dụng số mạng hiệu quả hơn.

Việc cấp phát nhiều số mạng còn có một nhược điểm khác khi nghĩ về định tuyến. Nhớ rằng lượng trạng thái lưu trữ ở một nút tham gia giao thức định tuyến tỷ lệ thuận với số nút khác, và định tuyến trong một liên mạng gồm việc xây dựng các bảng chuyển tiếp cho biết bộ định tuyến phải làm gì để tới các mạng khác nhau. Do đó, càng có nhiều số mạng được dùng, bảng chuyển tiếp càng lớn. Bảng lớn làm tăng chi phí cho bộ định tuyến, và có thể chậm hơn khi tìm kiếm, làm giảm hiệu năng bộ định tuyến. Điều này là một động lực khác để cấp phát số mạng cẩn thận.

*Địa chỉ con* (subnetting) là bước đầu tiên để giảm tổng số số mạng được cấp phát. Ý tưởng là lấy một số mạng IP duy nhất và cấp phát các địa chỉ IP với số mạng đó cho nhiều mạng vật lý, giờ gọi là *mạng con* (subnets). Có một số việc cần làm để điều này hoạt động. Đầu tiên, các mạng con nên gần nhau. Vì từ một điểm xa trên Internet, chúng sẽ đều trông như một mạng duy nhất, chỉ có một số mạng giữa chúng. Điều này nghĩa là một bộ định tuyến chỉ có thể chọn một đường để tới bất kỳ mạng con nào, nên tốt nhất là chúng đều cùng hướng. Một tình huống lý tưởng để dùng subnetting là một campus hoặc công ty lớn có nhiều mạng vật lý. Từ ngoài campus, tất cả những gì bạn cần biết để tới bất kỳ mạng con nào bên trong là nơi campus kết nối với phần còn lại của Internet. Điều này thường chỉ ở một điểm, nên một mục trong bảng chuyển tiếp là đủ. Ngay cả khi có nhiều điểm kết nối, biết cách tới một điểm trong mạng campus vẫn là một khởi đầu tốt.

Cơ chế cho phép một số mạng duy nhất được chia sẻ giữa nhiều mạng con là cấu hình tất cả các nút trên mỗi mạng con với một *mặt nạ mạng con* (subnet mask). Với địa chỉ IP đơn giản, tất cả các máy chủ trên cùng một mạng phải có cùng số mạng. Mặt nạ mạng con cho phép ta đưa vào một *số mạng con*; tất cả các máy chủ trên cùng một mạng vật lý sẽ có cùng số mạng con, nghĩa là các máy chủ có thể ở các mạng vật lý khác nhau nhưng chia sẻ một số mạng duy nhất. Khái niệm này được minh họa trong :numref:`Hình %s <fig-subaddr>`.

.. _fig-subaddr:
.. figure:: figures/f03-20-9780123850591.png
   :width: 350px
   :align: center

   Địa chỉ mạng con.

Ý nghĩa của subnetting với một máy chủ là nó giờ được cấu hình với cả địa chỉ IP và mặt nạ mạng con cho mạng con mà nó kết nối. Ví dụ, máy chủ H1 trong :numref:`Hình %s <fig-subnet>` được cấu hình với địa chỉ 128.96.34.15 và mặt nạ mạng con 255.255.255.128. (Tất cả các máy chủ trên một mạng con được cấu hình cùng mặt nạ; tức là mỗi mạng con chỉ có một mặt nạ.) Phép AND bit giữa hai số này xác định số mạng con của máy chủ và tất cả các máy chủ khác trên cùng mạng con. Trong trường hợp này, 128.96.34.15 AND 255.255.255.128 bằng 128.96.34.0, nên đây là số mạng con cho mạng con trên cùng trong hình.

.. _fig-subnet:
.. figure:: figures/f03-21-9780123850591.png
   :width: 500px
   :align: center

   Ví dụ về subnetting.

Khi máy chủ muốn gửi một gói tin tới một địa chỉ IP nào đó, việc đầu tiên nó làm là thực hiện phép AND bit giữa mặt nạ mạng con của nó và địa chỉ IP đích. Nếu kết quả bằng số mạng con của máy chủ gửi, nó biết rằng máy chủ đích nằm trên cùng mạng con và gói tin có thể được chuyển trực tiếp qua mạng con. Nếu kết quả không bằng, gói tin cần được gửi tới một bộ định tuyến để chuyển tiếp tới mạng con khác. Ví dụ, nếu H1 gửi tới H2, H1 AND mặt nạ mạng con của nó (255.255.255.128) với địa chỉ của H2 (128.96.34.139) để được 128.96.34.128. Điều này không khớp với số mạng con của H1 (128.96.34.0) nên H1 biết H2 nằm trên mạng con khác. Vì H1 không thể chuyển gói tin trực tiếp tới H2 qua mạng con, nó gửi gói tin tới bộ định tuyến mặc định R1.

Bảng chuyển tiếp của một bộ định tuyến cũng thay đổi một chút khi ta đưa vào subnetting. Nhớ rằng trước đây ta có bảng chuyển tiếp gồm các mục dạng ``(NetworkNum, NextHop)``. Để hỗ trợ subnetting, bảng giờ phải chứa các mục dạng ``(SubnetNumber, SubnetMask, NextHop)``. Để tìm mục đúng trong bảng, bộ định tuyến AND địa chỉ đích của gói tin với ``SubnetMask`` của từng mục; nếu kết quả khớp với ``SubnetNumber`` của mục, đây là mục đúng để dùng, và nó chuyển tiếp gói tin tới bộ định tuyến bước nhảy tiếp theo chỉ định. Trong mạng ví dụ ở :numref:`Hình %s <fig-subnet>`, bộ định tuyến R1 sẽ có các mục như trong :numref:`Bảng %s <tab-subnettab>`.

.. _tab-subnettab:
.. table:: Ví dụ bảng chuyển tiếp với subnetting.
   :align: center
   :widths: auto

   +---------------+-----------------+-------------+
   | SubnetNumber  | SubnetMask      | NextHop     |
   +===============+=================+=============+
   | 128.96.34.0   | 255.255.255.128 | Interface 0 |
   +---------------+-----------------+-------------+
   | 128.96.34.128 | 255.255.255.128 | Interface 1 |
   +---------------+-----------------+-------------+
   | 128.96.33.0   | 255.255.255.0   | R2          |
   +---------------+-----------------+-------------+

Tiếp tục ví dụ gửi datagram từ H1 tới H2, R1 sẽ AND địa chỉ của H2 (128.96.34.139) với mặt nạ mạng con của mục đầu tiên (255.255.255.128) và so sánh kết quả (128.96.34.128) với số mạng của mục đó (128.96.34.0). Vì không khớp, nó chuyển sang mục tiếp theo. Lần này có khớp, nên R1 chuyển datagram tới H2 qua interface 1, là giao diện kết nối tới cùng mạng với H2.

Giờ ta có thể mô tả thuật toán chuyển tiếp datagram như sau:

::

   D = địa chỉ IP đích
   for each forwarding table entry (SubnetNumber, SubnetMask, NextHop)
       D1 = SubnetMask & D
       if D1 = SubnetNumber
           if NextHop is an interface
               deliver datagram directly to destination
           else
               deliver datagram to NextHop (a router)

Dù không minh họa trong ví dụ này, thường sẽ có một đường mặc định trong bảng và sẽ được dùng nếu không tìm thấy khớp rõ ràng nào. Lưu ý rằng một triển khai ngây thơ của thuật toán này—lặp lại phép AND địa chỉ đích với mặt nạ mạng con có thể không khác nhau mỗi lần, và tìm kiếm tuyến tính bảng—sẽ rất kém hiệu quả.

Một hệ quả quan trọng của subnetting là các phần khác nhau của internet nhìn thế giới khác nhau. Từ ngoài campus giả định của chúng ta, các bộ định tuyến nhìn thấy một mạng duy nhất. Trong ví dụ trên, các bộ định tuyến ngoài campus nhìn tập hợp các mạng trong :numref:`Hình %s <fig-subnet>` chỉ là mạng 128.96, và họ giữ một mục trong bảng chuyển tiếp để biết cách tới đó. Các bộ định tuyến trong campus, tuy nhiên, cần có khả năng định tuyến gói tin tới đúng mạng con. Do đó, không phải tất cả các phần của internet đều nhìn thấy cùng một thông tin định tuyến. Đây là một ví dụ về *kết tụ* thông tin định tuyến, điều cơ bản để mở rộng hệ thống định tuyến. Phần tiếp theo sẽ cho thấy cách kết tụ có thể được nâng lên một mức nữa.

Địa chỉ không phân lớp
~~~~~~~~~~~~~~~~~~~~~~

Subnetting có một đối ứng, đôi khi gọi là *supernetting*, nhưng thường gọi là *Định tuyến liên miền không phân lớp* (Classless Interdomain Routing, CIDR, phát âm là “cider”). CIDR đưa ý tưởng subnetting tới kết luận logic của nó bằng cách loại bỏ hoàn toàn các lớp địa chỉ. Tại sao chỉ subnetting thôi là chưa đủ? Về bản chất, subnetting chỉ cho phép ta chia một địa chỉ phân lớp thành nhiều mạng con, trong khi CIDR cho phép ta gộp nhiều địa chỉ phân lớp thành một “siêu mạng”. Điều này giải quyết thêm vấn đề kém hiệu quả về không gian địa chỉ đã nói ở trên, và làm như vậy mà không làm hệ thống định tuyến bị quá tải.

Để thấy vấn đề hiệu quả không gian địa chỉ và khả năng mở rộng của hệ thống định tuyến liên quan với nhau thế nào, hãy xét trường hợp giả định một công ty có mạng với 256 máy chủ. Đó là hơi nhiều cho một địa chỉ lớp C, nên bạn sẽ muốn cấp một địa chỉ lớp B. Tuy nhiên, dùng một phần không gian địa chỉ có thể chứa 65.535 máy chủ để chỉ chứa 256 máy chủ có hiệu quả chỉ 256/65.535 = 0,39%. Dù subnetting có thể giúp ta cấp phát địa chỉ cẩn thận, nó không giải quyết được thực tế là bất kỳ tổ chức nào có hơn 255 máy chủ, hoặc dự kiến sẽ có, đều muốn một địa chỉ lớp B.

Cách đầu tiên bạn có thể xử lý vấn đề này là từ chối cấp địa chỉ lớp B cho bất kỳ tổ chức nào yêu cầu trừ khi họ có thể chứng minh cần gần 64K địa chỉ, và thay vào đó cấp cho họ số lượng địa chỉ lớp C phù hợp để đáp ứng số host dự kiến. Vì bây giờ chúng ta sẽ cấp phát không gian địa chỉ theo từng khối 256 địa chỉ một lần, chúng ta có thể khớp chính xác hơn lượng không gian địa chỉ tiêu thụ với quy mô tổ chức. Với bất kỳ tổ chức nào có ít nhất 256 host, chúng ta có thể đảm bảo mức sử dụng địa chỉ ít nhất là 50%, và thường là nhiều hơn. (Đáng buồn thay, ngay cả khi bạn có thể biện minh cho việc xin một số mạng lớp B, cũng đừng bận tâm, vì chúng đã được cấp hết từ lâu.)

Tuy nhiên, giải pháp này lại nảy sinh một vấn đề cũng nghiêm trọng không kém: yêu cầu lưu trữ quá lớn ở các router. Nếu một site có, ví dụ, 16 số mạng lớp C được cấp, điều đó có nghĩa là mỗi router backbone của Internet cần 16 mục trong bảng định tuyến để chuyển tiếp gói đến site đó. Điều này đúng ngay cả khi đường đi đến tất cả các mạng đó là giống nhau. Nếu chúng ta cấp một địa chỉ lớp B cho site đó, cùng thông tin định tuyến có thể được lưu chỉ trong một mục bảng. Tuy nhiên, hiệu quả sử dụng địa chỉ khi đó chỉ là 16 x 255 / 65.536 = 6,2%.

Do đó, CIDR cố gắng cân bằng mong muốn giảm số lượng tuyến mà một router cần biết với nhu cầu cấp phát địa chỉ hiệu quả. Để làm điều này, CIDR giúp chúng ta *tổng hợp* các tuyến. Tức là, nó cho phép chúng ta sử dụng một mục duy nhất trong bảng chuyển tiếp để chỉ cách đến rất nhiều mạng khác nhau. Như đã nói ở trên, nó làm điều này bằng cách phá vỡ ranh giới cứng nhắc giữa các lớp địa chỉ. Để hiểu cách hoạt động, hãy xem xét tổ chức giả định của chúng ta với 16 số mạng lớp C. Thay vì cấp phát 16 địa chỉ ngẫu nhiên, chúng ta có thể cấp một khối các địa chỉ lớp C *liền kề*. Giả sử chúng ta cấp các số mạng lớp C từ 192.4.16 đến 192.4.31. Hãy chú ý rằng 20 bit đầu tiên của tất cả các địa chỉ trong dải này là giống nhau (``11000000 00000100 0001``). Như vậy, về thực chất, chúng ta đã tạo ra một số mạng 20 bit—một thứ nằm giữa số mạng lớp B và lớp C về số host mà nó có thể hỗ trợ. Nói cách khác, chúng ta vừa đạt được hiệu quả cấp phát địa chỉ cao khi cấp phát các khối nhỏ hơn mạng lớp B, vừa có một tiền tố mạng duy nhất có thể dùng trong bảng chuyển tiếp. Lưu ý rằng, để sơ đồ này hoạt động, chúng ta cần cấp phát các khối địa chỉ lớp C có chung một tiền tố, nghĩa là mỗi khối phải chứa số mạng lớp C là lũy thừa của hai.

CIDR yêu cầu một kiểu ký hiệu mới để biểu diễn số mạng, hay còn gọi là *tiền tố* (prefix), vì các tiền tố có thể có độ dài bất kỳ. Quy ước là đặt ``/X`` sau tiền tố, trong đó ``X`` là độ dài tiền tố tính bằng bit. Vậy, với ví dụ trên, tiền tố 20 bit cho tất cả các mạng từ 192.4.16 đến 192.4.31 được biểu diễn là 192.4.16/20. Ngược lại, nếu muốn biểu diễn một số mạng lớp C đơn lẻ, dài 24 bit, ta sẽ viết là 192.4.16/24. Ngày nay, khi CIDR đã trở thành chuẩn, người ta thường nói về “slash 24” hơn là mạng lớp C. Lưu ý rằng biểu diễn địa chỉ mạng theo cách này tương tự với cách ``(mask, value)`` dùng trong subnetting, miễn là ``mask`` gồm các bit liên tiếp bắt đầu từ bit quan trọng nhất (trên thực tế gần như luôn là như vậy).

.. _fig-cidreg:
.. figure:: figures/f03-22-9780123850591.png
   :width: 500px
   :align: center

   Tổng hợp tuyến với CIDR.

Khả năng tổng hợp tuyến ở rìa mạng như vừa thấy chỉ là bước đầu tiên. Hãy tưởng tượng một mạng nhà cung cấp dịch vụ Internet, với nhiệm vụ chính là cung cấp kết nối Internet cho nhiều công ty và trường đại học (khách hàng). Nếu chúng ta cấp phát các tiền tố cho khách hàng sao cho nhiều mạng khách hàng khác nhau kết nối vào mạng nhà cung cấp cùng chia sẻ một tiền tố địa chỉ ngắn hơn, thì chúng ta có thể tổng hợp tuyến ở mức cao hơn nữa. Xem ví dụ trong :numref:`Hình %s <fig-cidreg>`. Giả sử tám khách hàng do nhà cung cấp phục vụ mỗi người được cấp một tiền tố mạng 24 bit liền kề. Các tiền tố này đều bắt đầu bằng cùng 21 bit. Vì tất cả khách hàng đều có thể truy cập qua cùng một mạng nhà cung cấp, nó có thể quảng bá một tuyến duy nhất cho tất cả bằng cách chỉ quảng bá tiền tố chung 21 bit mà họ chia sẻ. Và nó có thể làm điều này ngay cả khi chưa cấp phát hết tất cả các tiền tố 24 bit, miễn là nhà cung cấp cuối cùng *sẽ* có quyền cấp các tiền tố đó cho khách hàng. Một cách để thực hiện là cấp trước một phần không gian địa chỉ cho nhà cung cấp và sau đó để nhà cung cấp cấp phát địa chỉ từ không gian đó cho khách hàng khi cần. Lưu ý rằng, khác với ví dụ đơn giản này, không cần tất cả các tiền tố khách hàng phải cùng độ dài.

IP Forwarding Revisited
~~~~~~~~~~~~~~~~~~~~~~~

Trong tất cả các thảo luận về chuyển tiếp IP trước đây, chúng ta đã giả định rằng có thể tìm số mạng trong một gói tin rồi tra số đó trong bảng chuyển tiếp. Tuy nhiên, giờ đây khi đã giới thiệu CIDR, chúng ta cần xem xét lại giả định này. CIDR có nghĩa là các tiền tố có thể có độ dài bất kỳ, từ 2 đến 32 bit. Hơn nữa, đôi khi có thể có các tiền tố trong bảng chuyển tiếp “chồng lấn” nhau, theo nghĩa là một số địa chỉ có thể khớp với nhiều tiền tố. Ví dụ, chúng ta có thể thấy cả 171.69 (tiền tố 16 bit) và 171.69.10 (tiền tố 24 bit) trong bảng chuyển tiếp của một router. Trong trường hợp này, một gói tin gửi đến, ví dụ, 171.69.10.5 rõ ràng khớp với cả hai tiền tố. Quy tắc trong trường hợp này dựa trên nguyên tắc “khớp dài nhất”; tức là, gói tin sẽ khớp với tiền tố dài nhất, ở đây là 171.69.10. Ngược lại, một gói tin gửi đến 171.69.20.5 sẽ khớp với 171.69 và *không* khớp với 171.69.10, và nếu không có mục nào khác khớp trong bảng định tuyến thì 171.69 sẽ là khớp dài nhất.

Nhiệm vụ tìm kiếm hiệu quả khớp dài nhất giữa một địa chỉ IP và các tiền tố độ dài biến đổi trong bảng chuyển tiếp là một lĩnh vực nghiên cứu phong phú trong nhiều năm. Thuật toán nổi tiếng nhất sử dụng một phương pháp gọi là *PATRICIA tree*, thực ra đã được phát triển từ trước khi có CIDR.

3.3.6 Address Translation (ARP)
-------------------------------

Trong phần trước chúng ta đã nói về cách đưa datagram IP đến đúng mạng vật lý nhưng chưa bàn kỹ về cách đưa một datagram đến đúng host hoặc router trên mạng đó. Vấn đề chính là các datagram IP chứa địa chỉ IP, nhưng phần cứng giao tiếp vật lý trên host hoặc router mà bạn muốn gửi datagram đến chỉ hiểu sơ đồ địa chỉ của mạng cụ thể đó. Do đó, chúng ta cần chuyển đổi địa chỉ IP sang địa chỉ tầng liên kết phù hợp với mạng này (ví dụ, địa chỉ Ethernet 48 bit). Khi đó, chúng ta có thể đóng gói datagram IP vào trong một frame chứa địa chỉ tầng liên kết đó và gửi nó đến đích cuối cùng hoặc đến một router hứa sẽ chuyển tiếp datagram về phía đích cuối cùng.

Một cách đơn giản để ánh xạ địa chỉ IP thành địa chỉ mạng vật lý là mã hóa địa chỉ vật lý của host vào phần host của địa chỉ IP. Ví dụ, một host có địa chỉ vật lý ``00100001 01010001`` (có giá trị thập phân là 33 ở byte cao và 81 ở byte thấp) có thể được cấp địa chỉ IP ``128.96.33.81``. Dù giải pháp này đã được dùng trên một số mạng, nó bị giới hạn ở chỗ địa chỉ vật lý của mạng không thể dài quá 16 bit trong ví dụ này; trên mạng lớp C chỉ có thể dài 8 bit. Rõ ràng điều này sẽ không phù hợp với địa chỉ Ethernet 48 bit.

Một giải pháp tổng quát hơn là mỗi host duy trì một bảng ánh xạ địa chỉ; tức là, bảng này sẽ ánh xạ địa chỉ IP thành địa chỉ vật lý. Dù bảng này có thể được quản trị viên hệ thống quản lý tập trung rồi sao chép đến từng host trên mạng, một cách tốt hơn là để mỗi host tự động học nội dung bảng này qua mạng. Điều này có thể thực hiện bằng giao thức Address Resolution Protocol (ARP). Mục tiêu của ARP là cho phép mỗi host trên mạng xây dựng một bảng ánh xạ giữa địa chỉ IP và địa chỉ tầng liên kết. Vì các ánh xạ này có thể thay đổi theo thời gian (ví dụ, do card Ethernet trên một host bị hỏng và được thay bằng card mới với địa chỉ mới), các mục sẽ hết hạn định kỳ và bị loại bỏ. Việc này diễn ra khoảng mỗi 15 phút. Tập hợp các ánh xạ hiện có trên một host gọi là ARP cache hoặc ARP table.

ARP tận dụng thực tế là nhiều công nghệ mạng tầng liên kết, như Ethernet, hỗ trợ broadcast. Nếu một host muốn gửi một datagram IP đến một host (hoặc router) mà nó biết là nằm trên cùng mạng (tức là, node gửi và nhận có cùng số mạng IP), nó sẽ kiểm tra ánh xạ trong cache. Nếu không tìm thấy, nó cần kích hoạt giao thức ARP trên mạng. Nó làm điều này bằng cách broadcast một truy vấn ARP lên mạng. Truy vấn này chứa địa chỉ IP cần hỏi (địa chỉ IP đích). Mỗi host nhận được truy vấn và kiểm tra xem nó có khớp với địa chỉ IP của mình không. Nếu khớp, host sẽ gửi một thông điệp phản hồi chứa địa chỉ tầng liên kết của nó về cho host gửi truy vấn. Host gửi truy vấn sẽ thêm thông tin này vào bảng ARP của mình.

Thông điệp truy vấn cũng bao gồm địa chỉ IP và địa chỉ tầng liên kết của host gửi. Do đó, khi một host broadcast một truy vấn, mỗi host trên mạng đều có thể học được địa chỉ tầng liên kết và địa chỉ IP của host gửi và lưu thông tin đó vào bảng ARP của mình. Tuy nhiên, không phải host nào cũng thêm thông tin này vào bảng ARP. Nếu host đã có một mục cho host đó trong bảng, nó sẽ “làm mới” mục này; tức là, đặt lại thời gian hết hạn của mục. Nếu host đó là đích của truy vấn, nó sẽ thêm thông tin về host gửi vào bảng, ngay cả khi chưa có mục cho host đó. Điều này vì có khả năng host nguồn sắp gửi một thông điệp ứng dụng cho nó, và nó có thể cần gửi phản hồi hoặc ACK lại cho nguồn; nó sẽ cần địa chỉ vật lý của nguồn để làm điều này. Nếu một host không phải là đích và cũng chưa có mục cho nguồn trong bảng ARP, nó sẽ không thêm mục cho nguồn. Vì không có lý do gì để tin rằng host này sẽ cần địa chỉ tầng liên kết của nguồn; không cần làm rối bảng ARP với thông tin này.

.. _fig-arp:
.. figure:: figures/f03-23-9780123850591.png
   :width: 500px
   :align: center

   Định dạng gói ARP để ánh xạ địa chỉ IP sang địa chỉ Ethernet.

:numref:`Hình %s <fig-arp>` cho thấy định dạng gói ARP cho ánh xạ địa chỉ IP sang địa chỉ Ethernet. Thực tế, ARP có thể dùng cho nhiều loại ánh xạ khác—khác biệt chính là ở kích thước địa chỉ. Ngoài địa chỉ IP và địa chỉ tầng liên kết của cả nguồn và đích, gói tin còn chứa

-  Trường ``HardwareType``, xác định loại mạng vật lý (ví dụ, Ethernet)

-  Trường ``ProtocolType``, xác định giao thức tầng cao hơn (ví dụ, IP)

-  Trường ``HLen`` (độ dài địa chỉ “hardware”) và ``PLen`` (độ dài địa chỉ “protocol”), xác định độ dài địa chỉ tầng liên kết và địa chỉ giao thức tầng cao hơn tương ứng

-  Trường ``Operation``, xác định đây là yêu cầu hay phản hồi

-  Địa chỉ hardware (Ethernet) và protocol (IP) của nguồn và đích

Lưu ý rằng kết quả của quá trình ARP có thể được thêm vào một cột bổ sung trong bảng chuyển tiếp như trong :numref:`Bảng %s <tab-ipfwdtab>`. Ví dụ, khi R2 cần chuyển tiếp một gói đến mạng 2, nó không chỉ tìm được next hop là R1, mà còn tìm được địa chỉ MAC để gán cho gói tin gửi đến R1.

.. _key-best-effort:
.. admonition:: Key Takeaway

   Chúng ta đã thấy các cơ chế cơ bản mà IP cung cấp để xử lý cả dị chủng và khả năng mở rộng. Về vấn đề dị chủng, IP bắt đầu bằng việc định nghĩa một mô hình dịch vụ best-effort với giả định tối thiểu về các mạng nền tảng; đáng chú ý nhất, mô hình này dựa trên datagram không tin cậy. IP sau đó bổ sung hai điểm quan trọng: (1) một định dạng gói tin chung (cơ chế phân mảnh/lắp ráp lại giúp định dạng này hoạt động trên các mạng có MTU khác nhau) và (2) một không gian địa chỉ toàn cầu để nhận diện tất cả các host (ARP là cơ chế giúp không gian địa chỉ toàn cầu này hoạt động trên các mạng có sơ đồ địa chỉ vật lý khác nhau). Về vấn đề mở rộng, IP sử dụng tổng hợp phân cấp để giảm lượng thông tin cần thiết cho việc chuyển tiếp gói tin. Cụ thể, địa chỉ IP được chia thành phần mạng và phần host, với các gói tin được định tuyến trước tiên đến mạng đích rồi mới chuyển đến đúng host trên mạng đó.
   :ref:`[Next] <key-dhcp>`

3.3.7 Cấu hình Host (DHCP)
--------------------------

Địa chỉ Ethernet được cấu hình vào card mạng bởi nhà sản xuất, và quá trình này được quản lý sao cho đảm bảo các địa chỉ này là duy nhất toàn cầu. Đây rõ ràng là điều kiện đủ để đảm bảo bất kỳ tập hợp host nào kết nối vào một Ethernet (kể cả LAN mở rộng) đều có địa chỉ duy nhất. Hơn nữa, điều duy nhất chúng ta yêu cầu ở địa chỉ Ethernet là tính duy nhất.

Địa chỉ IP, ngược lại, không chỉ phải duy nhất trên một liên mạng nhất định mà còn phải phản ánh cấu trúc của liên mạng. Như đã nói ở trên, chúng chứa phần mạng và phần host, và phần mạng phải giống nhau cho tất cả các host trên cùng một mạng. Do đó, không thể cấu hình địa chỉ IP một lần vào host khi sản xuất, vì điều đó ngụ ý nhà sản xuất biết host nào sẽ kết nối vào mạng nào, và cũng có nghĩa là một host, một khi đã kết nối vào một mạng, sẽ không bao giờ chuyển sang mạng khác. Vì lý do này, địa chỉ IP cần có khả năng cấu hình lại.

Ngoài địa chỉ IP, còn một số thông tin khác mà host cần có trước khi có thể bắt đầu gửi gói tin. Đáng chú ý nhất là địa chỉ của router mặc định—nơi mà nó có thể gửi các gói tin có địa chỉ đích không nằm trên cùng mạng với host gửi.

Hầu hết các hệ điều hành host đều cung cấp cách để quản trị viên hệ thống, hoặc thậm chí người dùng, cấu hình thủ công thông tin IP cần thiết cho host; tuy nhiên, có một số nhược điểm rõ ràng với cấu hình thủ công như vậy. Một là việc cấu hình tất cả các host trong một mạng lớn trực tiếp là rất tốn công, đặc biệt khi bạn xem xét rằng các host đó không thể truy cập qua mạng cho đến khi được cấu hình. Quan trọng hơn, quá trình cấu hình rất dễ xảy ra lỗi, vì cần đảm bảo mỗi host nhận đúng số mạng và không có hai host nhận cùng một địa chỉ IP. Vì những lý do này, các phương pháp cấu hình tự động là cần thiết. Phương pháp chính sử dụng một giao thức gọi là *Dynamic Host Configuration Protocol* (DHCP).

DHCP dựa vào sự tồn tại của một máy chủ DHCP chịu trách nhiệm cung cấp thông tin cấu hình cho các host. Có ít nhất một máy chủ DHCP cho mỗi miền quản trị. Ở mức đơn giản nhất, máy chủ DHCP có thể hoạt động như một kho lưu trữ tập trung cho thông tin cấu hình host. Xét ví dụ về việc quản trị địa chỉ trong liên mạng của một công ty lớn. DHCP giúp quản trị viên mạng không phải đi đến từng host trong công ty với danh sách địa chỉ và sơ đồ mạng để cấu hình từng host thủ công. Thay vào đó, thông tin cấu hình cho mỗi host có thể được lưu trên máy chủ DHCP và tự động truy xuất bởi mỗi host khi nó khởi động hoặc kết nối vào mạng. Tuy nhiên, quản trị viên vẫn sẽ chọn địa chỉ mà mỗi host nhận; chỉ là lưu nó trên máy chủ. Trong mô hình này, thông tin cấu hình cho mỗi host được lưu trong một bảng được đánh chỉ mục bởi một dạng nhận diện client duy nhất, thường là địa chỉ phần cứng (ví dụ, địa chỉ Ethernet của card mạng).

Một cách sử dụng DHCP tinh vi hơn giúp quản trị viên mạng không cần phải gán địa chỉ cho từng host. Trong mô hình này, máy chủ DHCP duy trì một pool các địa chỉ khả dụng để cấp phát cho host khi có yêu cầu. Điều này giảm đáng kể lượng cấu hình mà quản trị viên phải làm, vì giờ chỉ cần cấp phát một dải địa chỉ IP (tất cả cùng số mạng) cho mỗi mạng.

Vì mục tiêu của DHCP là giảm thiểu cấu hình thủ công cần thiết để một host hoạt động, sẽ thật vô nghĩa nếu mỗi host lại phải được cấu hình với địa chỉ của máy chủ DHCP. Do đó, vấn đề đầu tiên mà DHCP phải giải quyết là phát hiện máy chủ.

Để liên hệ với máy chủ DHCP, một host vừa khởi động hoặc vừa kết nối sẽ gửi một thông điệp ``DHCPDISCOVER`` đến một địa chỉ IP đặc biệt (255.255.255.255) là địa chỉ broadcast IP. Điều này có nghĩa là tất cả các host và router trên mạng đó sẽ nhận được. (Router không chuyển tiếp các gói này sang mạng khác, ngăn việc broadcast ra toàn Internet.) Trong trường hợp đơn giản nhất, một trong các node này là máy chủ DHCP cho mạng. Máy chủ sẽ trả lời host đã gửi thông điệp khám phá (các node khác sẽ bỏ qua). Tuy nhiên, không thực tế khi yêu cầu một máy chủ DHCP trên mỗi mạng, vì điều này vẫn tạo ra số lượng lớn máy chủ cần cấu hình đúng và nhất quán. Do đó, DHCP sử dụng khái niệm *relay agent*. Có ít nhất một relay agent trên mỗi mạng, và nó chỉ cần cấu hình một thông tin: địa chỉ IP của máy chủ DHCP. Khi một relay agent nhận được thông điệp ``DHCPDISCOVER``, nó sẽ gửi unicast đến máy chủ DHCP và chờ phản hồi, sau đó gửi lại cho client yêu cầu. Quá trình chuyển tiếp thông điệp từ host đến máy chủ DHCP từ xa được minh họa trong :numref:`Hình %s <fig-dhcp-relay>`.

.. _fig-dhcp-relay:
.. figure:: figures/f03-24-9780123850591.png
   :width: 500px
   :align: center

   Một DHCP relay agent nhận thông điệp broadcast DHCPDISCOVER từ một host và gửi unicast DHCPDISCOVER đến máy chủ DHCP.

:numref:`Hình %s <fig-dhcp>` bên dưới cho thấy định dạng một thông điệp DHCP. Thông điệp này thực ra được gửi bằng một giao thức gọi là *User Datagram Protocol* (UDP) chạy trên IP. UDP sẽ được thảo luận chi tiết ở chương sau, nhưng điều duy nhất đáng chú ý ở đây là nó cung cấp một khóa phân kênh nói rằng, “Đây là một gói DHCP.”

.. _fig-dhcp:
.. figure:: figures/f03-25-9780123850591.png
   :width: 400px
   :align: center

   Định dạng gói DHCP.

DHCP được phát triển từ một giao thức trước đó gọi là BOOTP, nên một số trường trong gói tin thực ra không liên quan trực tiếp đến cấu hình host. Khi cần lấy thông tin cấu hình, client sẽ điền địa chỉ phần cứng của mình (ví dụ, địa chỉ Ethernet) vào trường ``chaddr``. Máy chủ DHCP trả lời bằng cách điền trường ``yiaddr`` (“your” IP address) và gửi lại cho client. Các thông tin khác như router mặc định cho client này có thể được đưa vào trường ``options``.

Trong trường hợp DHCP cấp phát động địa chỉ IP cho host, rõ ràng host không thể giữ địa chỉ mãi mãi, vì điều này cuối cùng sẽ làm máy chủ cạn pool địa chỉ. Đồng thời, không thể trông chờ host sẽ trả lại địa chỉ, vì nó có thể bị treo, rút khỏi mạng, hoặc tắt máy. Do đó, DHCP cho phép địa chỉ được cấp phát theo dạng thuê (lease) trong một khoảng thời gian. Khi lease hết hạn, máy chủ có thể trả lại địa chỉ đó vào pool. Một host với địa chỉ thuê rõ ràng cần gia hạn lease định kỳ nếu nó vẫn còn kết nối và hoạt động bình thường.

.. _key-dhcp:
.. admonition:: Key Takeaway

   DHCP minh họa một khía cạnh quan trọng của mở rộng: mở rộng quản lý mạng. Dù các thảo luận về mở rộng thường tập trung vào việc giữ cho trạng thái trong thiết bị mạng không tăng quá nhanh, cũng cần chú ý đến sự phức tạp của quản lý mạng. Bằng cách cho phép quản trị viên cấu hình một dải địa chỉ IP cho mỗi mạng thay vì từng địa chỉ cho từng host, DHCP cải thiện khả năng quản lý mạng.
   :ref:`[Next] <key-forwarding>`

Lưu ý rằng DHCP cũng có thể làm tăng độ phức tạp trong quản lý mạng, vì nó làm cho việc gán giữa host vật lý và địa chỉ IP trở nên động hơn nhiều. Điều này có thể khiến công việc của quản trị viên mạng khó khăn hơn nếu, ví dụ, cần xác định vị trí một host bị lỗi.

3.3.8 Báo cáo lỗi (ICMP)
------------------------

Vấn đề tiếp theo là Internet xử lý lỗi như thế nào. Dù IP sẵn sàng loại bỏ datagram khi gặp khó khăn—ví dụ, khi một router không biết cách chuyển tiếp datagram hoặc khi một mảnh của datagram không đến được đích—nó không nhất thiết im lặng thất bại. IP luôn được cấu hình với một giao thức đi kèm, gọi là *Internet Control Message Protocol* (ICMP), định nghĩa một tập hợp các thông điệp lỗi được gửi về host nguồn bất cứ khi nào một router hoặc host không thể xử lý thành công một datagram IP. Ví dụ, ICMP định nghĩa các thông điệp lỗi báo rằng host đích không thể truy cập (có thể do lỗi liên kết), quá trình lắp ráp lại thất bại, TTL đã về 0, checksum header IP bị lỗi, v.v.

ICMP cũng định nghĩa một số thông điệp điều khiển mà router có thể gửi về host nguồn. Một trong những thông điệp điều khiển hữu ích nhất, gọi là *ICMP-Redirect*, báo cho host nguồn biết rằng có một tuyến tốt hơn đến đích. ICMP-Redirect được dùng trong tình huống sau. Giả sử một host kết nối vào một mạng có hai router, gọi là *R1* và *R2*, trong đó host dùng R1 làm router mặc định. Nếu R1 nhận được một datagram từ host, mà dựa trên bảng chuyển tiếp nó biết rằng R2 sẽ là lựa chọn tốt hơn cho một địa chỉ đích cụ thể, nó sẽ gửi một ICMP-Redirect về host, hướng dẫn nó dùng R2 cho các datagram sau này gửi đến đích đó. Host sau đó sẽ thêm tuyến mới này vào bảng chuyển tiếp của mình.

ICMP cũng cung cấp nền tảng cho hai công cụ gỡ lỗi phổ biến, ``ping`` và ``traceroute``. ``ping`` dùng thông điệp echo của ICMP để xác định một node có thể truy cập và còn hoạt động không. ``traceroute`` sử dụng một kỹ thuật hơi khó hiểu để xác định tập hợp các router trên đường đi đến đích, là chủ đề của một bài tập ở cuối chương này.

3.3.9 Mạng ảo và Tunnel
-----------------------

Chúng ta kết thúc phần giới thiệu về IP bằng cách xem xét một vấn đề mà bạn có thể không lường trước, nhưng ngày càng quan trọng. Các thảo luận đến giờ tập trung vào việc làm thế nào để các node trên các mạng khác nhau có thể giao tiếp với nhau một cách không hạn chế. Đây thường là mục tiêu trên Internet—mọi người đều muốn gửi email cho mọi người, và người tạo website mới muốn tiếp cận đông đảo người dùng nhất có thể. Tuy nhiên, có nhiều tình huống cần kết nối có kiểm soát hơn. Một ví dụ quan trọng là *mạng riêng ảo* (VPN).

Thuật ngữ *VPN* bị lạm dụng rất nhiều và định nghĩa cũng khác nhau, nhưng trực giác ta có thể định nghĩa VPN bằng cách xét trước ý tưởng về mạng riêng. Các công ty có nhiều site thường xây dựng mạng riêng bằng cách thuê kênh từ công ty điện thoại và dùng các đường này để kết nối các site. Trong mạng như vậy, liên lạc chỉ diễn ra giữa các site của công ty, điều này thường mong muốn vì lý do bảo mật. Để làm mạng riêng thành *ảo*, các đường truyền thuê riêng—không chia sẻ với công ty khác—sẽ được thay thế bằng một loại mạng chia sẻ nào đó. Một mạch ảo (VC) là sự thay thế hợp lý cho đường thuê riêng vì nó vẫn cung cấp kết nối logic điểm-điểm giữa các site của công ty. Ví dụ, nếu công ty X có một VC từ site A đến site B, thì rõ ràng nó có thể gửi gói giữa A và B. Nhưng không có cách nào để công ty Y gửi gói đến site B mà không thiết lập VC riêng đến B, và việc thiết lập VC như vậy có thể bị ngăn chặn về mặt quản trị, do đó ngăn kết nối không mong muốn giữa công ty X và Y.

:numref:`Hình %s(a) <fig-vpn>` cho thấy hai mạng riêng cho hai công ty khác nhau. Trong :numref:`Hình %s(b) <fig-vpn>`, cả hai được chuyển sang một mạng mạch ảo. Tính giới hạn kết nối của mạng riêng thực được giữ nguyên, nhưng vì các mạng riêng giờ chia sẻ cùng đường truyền và switch nên ta nói rằng đã tạo ra hai mạng riêng ảo.

.. _fig-vpn:
.. figure:: figures/f03-26-9780123850591.png
   :width: 500px
   :align: center

   Ví dụ về mạng riêng ảo: (a) hai mạng riêng biệt; (b) hai mạng riêng ảo chia sẻ switch chung.

Trong :numref:`Hình %s <fig-vpn>`, một mạng mạch ảo (ví dụ dùng ATM) được dùng để cung cấp kết nối kiểm soát giữa các site. Cũng có thể cung cấp chức năng tương tự bằng cách dùng mạng IP để kết nối. Tuy nhiên, ta không thể chỉ đơn giản kết nối các site của các công ty vào một liên mạng duy nhất vì điều đó sẽ tạo ra kết nối giữa công ty X và Y, điều ta muốn tránh. Để giải quyết vấn đề này, cần giới thiệu một khái niệm mới, *IP tunnel*.

Ta có thể coi một IP tunnel như một liên kết logic điểm-điểm giữa một cặp node thực ra bị ngăn cách bởi một số lượng mạng bất kỳ. Liên kết logic này được tạo ra trong router ở đầu vào tunnel bằng cách cung cấp cho nó địa chỉ IP của router ở đầu kia tunnel. Bất cứ khi nào router ở đầu vào tunnel muốn gửi một gói qua liên kết logic này, nó sẽ đóng gói gói tin bên trong một datagram IP. Địa chỉ đích trong header IP là địa chỉ của router ở đầu kia tunnel, còn địa chỉ nguồn là của router đóng gói.

.. _fig-tunnel:
.. figure:: figures/f03-27-9780123850591.png
   :width: 600px
   :align: center

   Một tunnel qua liên mạng. 18.5.0.1 là địa chỉ của R2 có thể truy cập từ R1 qua liên mạng.

Trong bảng chuyển tiếp của router ở đầu vào tunnel, liên kết logic này trông giống như một liên kết bình thường. Xem ví dụ mạng trong :numref:`Hình %s <fig-tunnel>`. Một tunnel đã được cấu hình từ R1 đến R2 và gán số hiệu interface ảo là 0. Bảng chuyển tiếp ở R1 có thể như trong :numref:`Bảng %s <tab-tunneltab>`.

.. _tab-tunneltab:
.. table:: Bảng chuyển tiếp cho Router R1.
   :align: center
   :widths: auto

   +------------+---------------------+
   | NetworkNum | NextHop             |
   +============+=====================+
   | 1          | Interface 0         |
   +------------+---------------------+
   | 2          | Virtual interface 0 |
   +------------+---------------------+
   | Default    | Interface 1         |
   +------------+---------------------+

R1 có hai interface vật lý. Interface 0 kết nối với mạng 1; interface 1 kết nối với một liên mạng lớn và do đó là mặc định cho mọi lưu lượng không khớp với mục cụ thể nào trong bảng chuyển tiếp. Ngoài ra, R1 có một interface ảo, là interface đến tunnel. Giả sử R1 nhận được một gói từ mạng 1 chứa địa chỉ thuộc mạng 2. Bảng chuyển tiếp nói rằng gói này nên được gửi qua interface ảo 0. Để gửi gói qua interface này, router sẽ lấy gói tin, thêm header IP địa chỉ đến R2, rồi tiếp tục chuyển tiếp gói như thể nó vừa nhận được. Địa chỉ của R2 là 18.5.0.1; vì số mạng của địa chỉ này là 18, không phải 1 hay 2, nên một gói gửi đến R2 sẽ được chuyển tiếp qua interface mặc định vào liên mạng.

Khi gói rời khỏi R1, nó trông với phần còn lại của thế giới như một gói IP bình thường gửi đến R2, và được chuyển tiếp như vậy. Tất cả các router trong liên mạng sẽ chuyển tiếp nó bằng cách thông thường, cho đến khi nó đến R2. Khi R2 nhận được gói, nó thấy rằng gói mang địa chỉ của chính nó, nên nó sẽ gỡ bỏ header IP và xem payload của gói. Nó sẽ thấy một gói IP bên trong có địa chỉ đích thuộc mạng 2. R2 sẽ xử lý gói này như bất kỳ gói IP nào khác nó nhận được. Vì R2 kết nối trực tiếp với mạng 2, nó sẽ chuyển tiếp gói đó lên mạng. :numref:`Hình %s <fig-tunnel>` cho thấy sự thay đổi đóng gói của gói khi nó di chuyển qua mạng.

Dù R2 đóng vai trò là điểm cuối của tunnel, không có gì ngăn nó thực hiện các chức năng bình thường của một router. Ví dụ, nó có thể nhận một số gói không đi qua tunnel, nhưng có địa chỉ đến các mạng mà nó biết cách truy cập, và nó sẽ chuyển tiếp chúng như bình thường.

Bạn có thể tự hỏi tại sao lại phải tạo tunnel và thay đổi đóng gói của gói khi nó đi qua liên mạng. Một lý do là bảo mật. Khi kết hợp với mã hóa, tunnel có thể trở thành một loại liên kết rất riêng tư qua mạng công cộng. Một lý do khác có thể là R1 và R2 có một số khả năng không phổ biến ở các mạng trung gian, như định tuyến multicast. Bằng cách kết nối các router này bằng tunnel, ta có thể xây dựng một mạng ảo trong đó tất cả các router có khả năng này trông như được kết nối trực tiếp. Lý do thứ ba để xây dựng tunnel là để truyền các gói từ các giao thức khác ngoài IP qua một mạng IP. Miễn là các router ở hai đầu tunnel biết cách xử lý các giao thức khác này, tunnel IP sẽ giống như một liên kết điểm-điểm mà qua đó họ có thể gửi các gói không phải IP. Tunnel cũng cung cấp một cơ chế để ta có thể buộc một gói được chuyển đến một nơi cụ thể ngay cả khi header gốc của nó—cái sẽ được đóng gói bên trong header tunnel—có thể gợi ý rằng nó nên đi nơi khác. Như vậy, ta thấy tunneling là một kỹ thuật mạnh mẽ và khá tổng quát để xây dựng các liên kết ảo qua liên mạng. Thực tế, kỹ thuật này còn đệ quy, với trường hợp phổ biến nhất là tunnel IP qua IP.

Tunneling cũng có nhược điểm. Một là nó làm tăng độ dài gói tin; điều này có thể gây lãng phí băng thông đáng kể với các gói ngắn. Các gói dài hơn có thể bị phân mảnh, vốn có những bất lợi riêng. Ngoài ra, có thể có ảnh hưởng hiệu năng cho các router ở hai đầu tunnel, vì chúng phải làm nhiều việc hơn so với chuyển tiếp thông thường khi thêm và gỡ bỏ header tunnel. Cuối cùng, còn có chi phí quản lý cho đơn vị quản trị chịu trách nhiệm thiết lập tunnel và đảm bảo chúng được xử lý đúng bởi các giao thức định tuyến.