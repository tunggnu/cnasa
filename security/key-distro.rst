8.3 Phân Phối Khóa Trước
========================

Để sử dụng các thuật toán mã hóa và bộ xác thực, các thành viên giao tiếp cần biết nên sử dụng khóa nào. Trong trường hợp mã hóa khóa bí mật, làm thế nào một cặp thành viên có được khóa mà họ chia sẻ? Trong trường hợp mã hóa khóa công khai, làm thế nào các thành viên biết khóa công khai nào thuộc về thành viên nào? Câu trả lời sẽ khác nhau tùy thuộc vào việc các khóa đó là *khóa phiên* (session key) tồn tại ngắn hạn hay *khóa phân phối trước* (predistributed key) tồn tại lâu hơn.

Khóa phiên là một khóa được sử dụng để bảo vệ một phiên giao tiếp đơn lẻ, tương đối ngắn: một phiên. Mỗi phiên riêng biệt giữa một cặp thành viên sử dụng một khóa phiên mới, và khóa này luôn là khóa bí mật để đảm bảo tốc độ. Các thành viên xác định khóa phiên sẽ sử dụng thông qua một giao thức—giao thức thiết lập khóa phiên. Một giao thức thiết lập khóa phiên cần có cơ chế bảo mật riêng (ví dụ, để kẻ tấn công không thể biết được khóa phiên mới); cơ chế bảo mật này dựa trên các khóa phân phối trước tồn tại lâu hơn.

Có hai động lực chính cho việc phân chia vai trò giữa khóa phiên và khóa phân phối trước:

-  Giới hạn thời gian sử dụng một khóa sẽ giảm thời gian cho các cuộc tấn công đòi hỏi tính toán cao, giảm lượng ciphertext cho phân tích mật mã, và giảm lượng thông tin bị lộ nếu khóa bị phá.

-  Mã hóa khóa công khai thường vượt trội hơn cho xác thực và thiết lập khóa phiên nhưng quá chậm để dùng cho mã hóa toàn bộ thông điệp nhằm đảm bảo tính bảo mật.

Phần này giải thích cách các khóa phân phối trước được phân phối, và phần tiếp theo sẽ giải thích cách các khóa phiên được thiết lập. Từ đây, chúng tôi sẽ sử dụng “Alice” và “Bob” để chỉ các thành viên, như thường thấy trong tài liệu mật mã học. Lưu ý rằng mặc dù chúng tôi thường gọi các thành viên bằng các thuật ngữ nhân hóa, nhưng thực tế chúng tôi quan tâm nhiều hơn đến giao tiếp giữa các thực thể phần mềm hoặc phần cứng như client và server, vốn thường không liên quan trực tiếp đến một cá nhân cụ thể nào.

8.3.1 Phân Phối Trước Khóa Công Khai
-------------------------------------

Các thuật toán để sinh ra một cặp khóa công khai và khóa riêng phù hợp là kiến thức công khai, và phần mềm thực hiện điều này rất phổ biến. Vì vậy, nếu Alice muốn sử dụng mã hóa khóa công khai, cô ấy có thể tự tạo ra cặp khóa công khai và khóa riêng, giữ bí mật khóa riêng, và công khai khóa công khai. Nhưng làm thế nào cô ấy có thể công khai khóa công khai của mình—khẳng định rằng nó thuộc về cô ấy—một cách mà các thành viên khác có thể chắc chắn rằng nó thực sự thuộc về cô ấy? Không thể làm điều này qua email hoặc Web, vì kẻ tấn công có thể giả mạo một tuyên bố tương tự rằng khóa *x* thuộc về Alice trong khi thực tế *x* thuộc về kẻ tấn công.

Một sơ đồ hoàn chỉnh để chứng thực sự liên kết giữa khóa công khai và danh tính—khóa nào thuộc về ai—được gọi là *Hạ tầng Khóa Công khai* (Public Key Infrastructure, PKI). Một PKI bắt đầu bằng khả năng xác minh danh tính và liên kết chúng với các khóa bằng một kênh ngoài băng tần (out of band). “Ngoài băng tần” ở đây nghĩa là một cái gì đó bên ngoài mạng và các máy tính tạo nên nó, ví dụ như sau: Nếu Alice và Bob là hai cá nhân biết nhau, họ có thể gặp nhau trực tiếp và Alice có thể đưa khóa công khai của mình cho Bob, có thể là trên một tấm danh thiếp. Nếu Bob là một tổ chức, Alice có thể xuất trình giấy tờ tùy thân thông thường, có thể bao gồm ảnh hoặc dấu vân tay. Nếu Alice và Bob là các máy tính thuộc sở hữu của cùng một công ty, quản trị viên hệ thống có thể cấu hình Bob với khóa công khai của Alice.

Việc thiết lập khóa ngoài băng tần nghe có vẻ không mở rộng tốt, nhưng nó đủ để khởi tạo một PKI. Việc Bob biết rằng khóa của Alice là *x* có thể được phổ biến rộng rãi và mở rộng bằng cách kết hợp chữ ký số và khái niệm về niềm tin. Ví dụ, giả sử bạn đã nhận được khóa công khai của Bob qua kênh ngoài băng tần và bạn biết đủ về Bob để tin tưởng anh ấy trong các vấn đề về khóa và danh tính. Khi đó Bob có thể gửi cho bạn một thông điệp khẳng định rằng khóa của Alice là *x* và—vì bạn đã biết khóa công khai của Bob—bạn có thể xác thực thông điệp là do Bob gửi. (Hãy nhớ rằng để ký số một phát biểu, Bob sẽ đính kèm một hàm băm mật mã của nó đã được mã hóa bằng khóa riêng của mình.) Vì bạn tin tưởng Bob nói thật, bạn sẽ biết rằng khóa của Alice là *x*, ngay cả khi bạn chưa từng gặp cô ấy hoặc trao đổi bất kỳ thông điệp nào với cô ấy. Sử dụng chữ ký số, Bob thậm chí không cần gửi thông điệp cho bạn; anh ấy chỉ cần tạo và công bố một phát biểu đã ký số rằng khóa của Alice là *x*. Một phát biểu đã ký số như vậy về sự liên kết khóa công khai được gọi là *chứng chỉ khóa công khai* (public key certificate), hoặc đơn giản là chứng chỉ. Bob có thể gửi cho Alice một bản sao chứng chỉ, hoặc đăng nó lên một trang web. Nếu và khi ai đó cần xác minh khóa công khai của Alice, họ có thể làm điều đó bằng cách lấy một bản sao chứng chỉ, có thể trực tiếp từ Alice—miễn là họ tin tưởng Bob và biết khóa công khai của anh ấy. Bạn có thể thấy rằng bắt đầu từ một số lượng rất nhỏ các khóa (trong trường hợp này chỉ có khóa của Bob) bạn có thể xây dựng một tập hợp lớn các khóa tin cậy theo thời gian. Bob trong trường hợp này đóng vai trò thường được gọi là *tổ chức chứng thực* (certification authority, CA), và phần lớn bảo mật Internet ngày nay phụ thuộc vào các CA. VeriSign là một CA thương mại nổi tiếng. Chúng ta sẽ quay lại chủ đề này bên dưới.

Một trong những tiêu chuẩn quan trọng cho chứng chỉ là X.509. Tiêu chuẩn này để ngỏ nhiều chi tiết, nhưng quy định một cấu trúc cơ bản. Một chứng chỉ rõ ràng phải bao gồm:

-  Danh tính của thực thể được chứng thực

-  Khóa công khai của thực thể được chứng thực

-  Danh tính của người ký

-  Chữ ký số

-  Một định danh thuật toán chữ ký số (hàm băm mật mã nào và mã hóa nào)

Một thành phần tùy chọn là thời gian hết hạn của chứng chỉ. Chúng ta sẽ thấy một ứng dụng cụ thể của tính năng này bên dưới.

Vì một chứng chỉ tạo ra sự liên kết giữa một danh tính và một khóa công khai, chúng ta nên xem xét kỹ hơn ý nghĩa của “danh tính”. Ví dụ, một chứng chỉ nói rằng “Khóa công khai này thuộc về John Smith” có thể không hữu ích lắm nếu bạn không thể xác định được John Smith nào trong hàng ngàn người cùng tên. Do đó, chứng chỉ phải sử dụng một không gian tên được định nghĩa rõ ràng cho các danh tính được chứng thực; ví dụ, chứng chỉ thường được cấp cho địa chỉ email và tên miền DNS.

Có nhiều cách khác nhau để một PKI có thể chính thức hóa khái niệm niềm tin. Chúng tôi sẽ thảo luận hai cách tiếp cận chính.

Tổ Chức Chứng Thực (Certification Authorities)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Trong mô hình niềm tin này, niềm tin là nhị phân; bạn hoặc hoàn toàn tin tưởng ai đó hoặc không tin chút nào. Kết hợp với chứng chỉ, điều này cho phép xây dựng *chuỗi niềm tin* (chains of trust). Nếu X chứng thực rằng một khóa công khai nhất định thuộc về Y, và sau đó Y lại chứng thực rằng một khóa công khai khác thuộc về Z, thì tồn tại một chuỗi chứng chỉ từ X đến Z, ngay cả khi X và Z chưa từng gặp nhau. Nếu bạn biết khóa của X—và bạn tin tưởng X và Y—thì bạn có thể tin vào chứng chỉ cung cấp khóa của Z. Nói cách khác, tất cả những gì bạn cần là một chuỗi chứng chỉ, tất cả đều được ký bởi các thực thể mà bạn tin tưởng, miễn là nó dẫn về một thực thể mà bạn đã biết khóa.

Một *tổ chức chứng thực* (certification authority, CA) là một thực thể được cho là đáng tin cậy trong việc xác minh danh tính và cấp chứng chỉ khóa công khai. Có các CA thương mại, CA chính phủ, và thậm chí cả CA miễn phí. Để sử dụng một CA, bạn phải biết khóa của nó. Tuy nhiên, bạn có thể biết khóa của CA đó nếu bạn có thể lấy được một chuỗi chứng chỉ do CA ký bắt đầu từ một CA mà bạn đã biết khóa. Khi đó bạn có thể tin vào bất kỳ chứng chỉ nào do CA mới này ký.

Một cách phổ biến để xây dựng các chuỗi như vậy là sắp xếp chúng thành một cấu trúc cây, như minh họa trong :numref:`Figure %s <fig-pem-tree>`. Nếu mọi người đều có khóa công khai của CA gốc, thì bất kỳ thành viên nào cũng có thể cung cấp một chuỗi chứng chỉ cho thành viên khác và biết rằng nó sẽ đủ để xây dựng một chuỗi niềm tin cho thành viên đó.

.. _fig-pem-tree:
.. figure:: figures/f08-06-9780123850591.png
   :width: 600px
   :align: center

   Cấu trúc cây phân cấp tổ chức chứng thực.

Có một số vấn đề quan trọng với việc xây dựng chuỗi niềm tin. Quan trọng nhất, ngay cả khi bạn chắc chắn rằng mình có khóa công khai của CA gốc, bạn cần đảm bảo rằng mọi CA từ gốc trở xuống đều thực hiện đúng trách nhiệm của mình. Nếu chỉ một CA trong chuỗi sẵn sàng cấp chứng chỉ cho các thực thể mà không xác minh danh tính, thì một chuỗi chứng chỉ tưởng như hợp lệ sẽ trở nên vô nghĩa. Ví dụ, một CA gốc có thể cấp chứng chỉ cho một CA cấp hai và xác minh kỹ lưỡng rằng tên trên chứng chỉ khớp với tên doanh nghiệp của CA, nhưng CA cấp hai đó có thể sẵn sàng bán chứng chỉ cho bất kỳ ai mà không xác minh danh tính. Vấn đề này càng nghiêm trọng khi chuỗi niềm tin càng dài. Chứng chỉ X.509 cung cấp tùy chọn giới hạn tập hợp các thực thể mà chủ thể của chứng chỉ được phép chứng thực tiếp.

Có thể có nhiều hơn một gốc cho một cây chứng thực, và điều này khá phổ biến trong việc bảo mật các giao dịch Web ngày nay, ví dụ. Các trình duyệt Web như Firefox và Internet Explorer được cài sẵn các chứng chỉ cho một tập hợp các CA; thực chất, nhà sản xuất trình duyệt đã quyết định rằng các CA này và các khóa của họ có thể được tin tưởng. Người dùng cũng có thể thêm các CA vào danh sách mà trình duyệt của họ nhận diện là đáng tin cậy. Các chứng chỉ này được chấp nhận bởi Secure Socket Layer (SSL)/Transport Layer Security (TLS), giao thức thường được sử dụng nhất để bảo mật các giao dịch Web, mà chúng ta sẽ thảo luận ở phần sau. (Nếu bạn tò mò, bạn có thể vào phần cài đặt của trình duyệt và tìm tùy chọn “xem chứng chỉ” để xem trình duyệt của bạn đang tin tưởng bao nhiêu CA.)

Mạng Lưới Niềm Tin (Web of Trust)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một mô hình niềm tin thay thế là *mạng lưới niềm tin* (web of trust) được minh họa bởi Pretty Good Privacy (PGP), sẽ được thảo luận thêm ở phần sau. PGP là một hệ thống bảo mật cho email, vì vậy các địa chỉ email là danh tính mà các khóa được liên kết và chứng chỉ được ký. Theo đúng tinh thần của PGP là bảo vệ chống lại sự xâm nhập của chính phủ, không có CA nào cả. Thay vào đó, mỗi cá nhân tự quyết định mình tin ai và tin ở mức độ nào—trong mô hình này, niềm tin là vấn đề mức độ. Ngoài ra, một chứng chỉ khóa công khai có thể bao gồm một mức độ tin cậy cho biết người ký tin tưởng vào sự liên kết khóa trong chứng chỉ đến mức nào, vì vậy một người dùng có thể cần có nhiều chứng chỉ xác nhận cùng một liên kết khóa trước khi sẵn sàng tin tưởng nó.

Ví dụ, giả sử bạn có một chứng chỉ cho Bob do Alice cung cấp; bạn có thể gán cho chứng chỉ đó một mức độ tin cậy vừa phải. Tuy nhiên, nếu bạn có thêm các chứng chỉ cho Bob do C và D cung cấp, mỗi người cũng khá đáng tin cậy, điều đó có thể làm tăng đáng kể mức độ tin tưởng của bạn rằng khóa công khai bạn có cho Bob là hợp lệ. Tóm lại, PGP nhận ra rằng vấn đề thiết lập niềm tin là một vấn đề cá nhân và cung cấp cho người dùng các công cụ để tự quyết định, thay vì giả định rằng tất cả đều sẵn sàng tin tưởng vào một cấu trúc phân cấp duy nhất của các CA. Trích lời Phil Zimmerman, người phát triển PGP, “PGP dành cho những người thích tự gói dù nhảy của mình.”

PGP đã trở nên khá phổ biến trong cộng đồng mạng, và các buổi ký khóa PGP là một hoạt động thường xuyên tại các sự kiện mạng như các cuộc họp IETF. Tại các buổi này, một cá nhân có thể

-  Thu thập khóa công khai từ những người mà anh ta biết danh tính.

-  Cung cấp khóa công khai của mình cho người khác.

-  Nhận chữ ký cho khóa công khai của mình từ người khác, từ đó thu thập các chứng chỉ có thể thuyết phục được ngày càng nhiều người.

-  Ký khóa công khai của người khác, giúp họ xây dựng bộ chứng chỉ để phân phối khóa công khai của mình.

-  Thu thập chứng chỉ từ những người mà anh ta đủ tin tưởng để ký khóa.

Như vậy, theo thời gian, một người dùng sẽ thu thập được một tập hợp các chứng chỉ với các mức độ tin cậy khác nhau.

Thu Hồi Chứng Chỉ (Certificate Revocation)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một vấn đề phát sinh với chứng chỉ là làm thế nào để thu hồi, hoặc hủy bỏ, một chứng chỉ. Tại sao điều này lại quan trọng? Giả sử bạn nghi ngờ ai đó đã biết được khóa riêng của bạn. Có thể có bất kỳ số lượng chứng chỉ nào trên thế giới xác nhận rằng bạn là chủ sở hữu của khóa công khai tương ứng với khóa riêng đó. Người biết được khóa riêng của bạn do đó có mọi thứ cần thiết để giả mạo bạn: các chứng chỉ hợp lệ và khóa riêng của bạn. Để giải quyết vấn đề này, sẽ rất tốt nếu có thể thu hồi các chứng chỉ liên kết khóa cũ, đã bị lộ với danh tính của bạn, để kẻ giả mạo không còn có thể thuyết phục người khác rằng anh ta là bạn.

Giải pháp cơ bản cho vấn đề này khá đơn giản. Mỗi CA có thể phát hành một *danh sách thu hồi chứng chỉ* (certificate revocation list, CRL), là một danh sách các chứng chỉ đã bị thu hồi được ký số. CRL được cập nhật định kỳ và công khai rộng rãi. Vì nó được ký số, nó có thể chỉ cần được đăng lên một trang web. Bây giờ, khi Alice nhận được một chứng chỉ cho Bob mà cô ấy muốn xác minh, cô ấy sẽ kiểm tra CRL mới nhất do CA phát hành. Miễn là chứng chỉ chưa bị thu hồi, nó vẫn hợp lệ. Lưu ý rằng, nếu tất cả các chứng chỉ đều có thời hạn sử dụng không giới hạn, CRL sẽ ngày càng dài, vì bạn không bao giờ có thể xóa một chứng chỉ khỏi CRL do lo ngại rằng một bản sao của chứng chỉ bị thu hồi có thể được sử dụng. Vì lý do này, thông thường sẽ gắn thêm ngày hết hạn cho chứng chỉ khi nó được phát hành. Nhờ đó, chúng ta có thể giới hạn thời gian mà một chứng chỉ bị thu hồi cần phải nằm trong CRL. Ngay khi ngày hết hạn gốc của nó đã qua, nó có thể được xóa khỏi CRL.

8.3.2 Phân Phối Trước Khóa Bí Mật
----------------------------------

Nếu Alice muốn sử dụng mã hóa khóa bí mật để giao tiếp với Bob, cô ấy không thể chỉ chọn một khóa và gửi cho Bob vì, nếu chưa có khóa, họ không thể mã hóa khóa này để giữ bí mật và cũng không thể xác thực nhau. Cũng như với khóa công khai, cần có một sơ đồ phân phối trước. Việc phân phối trước khó hơn đối với khóa bí mật so với khóa công khai vì hai lý do rõ ràng:

-  Trong khi chỉ cần một khóa công khai cho mỗi thực thể là đủ cho xác thực và bảo mật, thì phải có một khóa bí mật cho mỗi cặp thực thể muốn giao tiếp. Nếu có N thực thể, điều đó nghĩa là có N(N-1)/2 khóa.

-  Không giống như khóa công khai, khóa bí mật phải được giữ bí mật.

Tóm lại, có rất nhiều khóa cần phân phối hơn, và bạn không thể sử dụng các chứng chỉ mà ai cũng có thể đọc.

Giải pháp phổ biến nhất là sử dụng một *Trung tâm Phân phối Khóa* (Key Distribution Center, KDC). Một KDC là một thực thể đáng tin cậy chia sẻ một khóa bí mật với mỗi thực thể khác. Điều này giảm số lượng khóa xuống còn N-1, đủ ít để có thể thiết lập ngoài băng tần cho một số ứng dụng. Khi Alice muốn giao tiếp với Bob, giao tiếp đó không đi qua KDC. Thay vào đó, KDC tham gia vào một giao thức xác thực Alice và Bob—sử dụng các khóa mà KDC đã chia sẻ với từng người—và sinh ra một khóa phiên mới cho họ sử dụng. Sau đó Alice và Bob giao tiếp trực tiếp bằng khóa phiên của họ. Kerberos là một hệ thống phổ biến dựa trên cách tiếp cận này. Chúng tôi sẽ mô tả Kerberos (cũng cung cấp xác thực) ở phần tiếp theo. Phần tiếp theo sẽ mô tả một phương án thay thế mạnh mẽ.

8.3.3 Trao Đổi Khóa Diffie-Hellman
----------------------------------

Một cách tiếp cận khác để thiết lập một khóa bí mật chung là sử dụng giao thức trao đổi khóa Diffie-Hellman, hoạt động mà không cần bất kỳ khóa phân phối trước nào. Các thông điệp trao đổi giữa Alice và Bob có thể bị bất kỳ ai nghe lén đọc được, nhưng kẻ nghe lén vẫn không biết được khóa bí mật mà Alice và Bob cuối cùng có được.

Diffie-Hellman không xác thực các thành viên. Vì hiếm khi hữu ích khi giao tiếp an toàn mà không biết chắc mình đang giao tiếp với ai, Diffie-Hellman thường được bổ sung thêm một số cơ chế để cung cấp xác thực. Một trong những ứng dụng chính của Diffie-Hellman là trong giao thức Internet Key Exchange (IKE), một phần trung tâm của kiến trúc IP Security (IPsec).

Giao thức Diffie-Hellman có hai tham số, *p* và *g*, cả hai đều là công khai và có thể được tất cả người dùng trong một hệ thống sử dụng. Tham số *p* phải là một số nguyên tố. Các số nguyên :math:`\bmod p` (viết tắt của modulo *p*) là :math:`0` đến *p-1*, vì :math:`x \bmod p` là phần dư sau khi *x* chia cho *p*, và tạo thành một *nhóm* (group) dưới phép nhân. Tham số *g* (thường gọi là generator) phải là một *căn nguyên thủy* (primitive root) của *p*: Với mọi số *n* từ 1 đến *p-1* phải tồn tại một giá trị *k* sao cho :math:`n = g^k \bmod p`. Ví dụ, nếu *p* là số nguyên tố 5 (một hệ thống thực tế sẽ dùng số lớn hơn nhiều), thì ta có thể chọn 2 làm generator *g* vì:

.. math::

   1 = 2^0 \bmod p

.. math::

   2 = 2^1 \bmod p

.. math::

   3 = 2^3 \bmod p

.. math::

   4 = 2^2 \bmod p

Giả sử Alice và Bob muốn đồng ý về một khóa bí mật chung. Alice và Bob, cũng như mọi người khác, đều đã biết giá trị của *p* và *g*. Alice sinh ra một giá trị riêng tư ngẫu nhiên *a* và Bob sinh ra một giá trị riêng tư ngẫu nhiên *b*. Cả *a* và *b* đều được chọn từ tập các số nguyên :math:`\{1,\dots{}, p-1\}`. Alice và Bob tính toán các giá trị công khai tương ứng—các giá trị mà họ sẽ gửi cho nhau không mã hóa—như sau. Giá trị công khai của Alice là

.. math::

   g^a \bmod p

và giá trị công khai của Bob là

.. math::

   g^b \bmod p

Sau đó họ trao đổi các giá trị công khai này. Cuối cùng, Alice tính

.. math::

   g^{ab} \bmod p = (g^b \bmod p)^a \bmod p

và Bob tính

.. math::

   g^{ba} \bmod p = (g^a \bmod p)^b \bmod p.

Alice và Bob giờ đều có :math:`g^{ab} \bmod p` (bằng với :math:`g^{ba} \bmod p`) làm khóa bí mật chung.

Bất kỳ kẻ nghe lén nào cũng biết *p, g*, và hai giá trị công khai :math:`g^a \bmod p` và :math:`g^b \bmod p`.
Nếu kẻ nghe lén có thể xác định được *a* hoặc *b*, họ có thể dễ dàng tính được khóa kết quả. Tuy nhiên, việc xác định *a* hoặc *b* từ thông tin đó là bất khả thi về mặt tính toán nếu *p, a,* và *b* đủ lớn; điều này được gọi là *bài toán logarit rời rạc* (discrete logarithm problem).

Ví dụ, với *p = 5* và *g = 2* như trên, giả sử Alice chọn số ngẫu nhiên *a = 3* và Bob chọn số ngẫu nhiên *b = 4*.
Khi đó Alice gửi cho Bob giá trị công khai

.. math::

   2^3 \bmod 5 = 3

và Bob gửi cho Alice giá trị công khai

.. math::

   2^4 \bmod 5 = 1

Alice sau đó có thể tính

.. math::

   g^{ab} \bmod p = (2^b \bmod 5)^3 \bmod 5 = (1)^3 \bmod 5 = 1

bằng cách thay giá trị công khai của Bob vào :math:`(2^b \bmod 5)`. Tương tự, Bob có thể tính

.. math::

   g^{ba} \bmod p = (g^a \bmod 5)^4 \bmod 5 = (3)^4 \bmod 5 = 1.

bằng cách thay giá trị công khai của Alice vào :math:`(2^a \bmod 5)`.
Cả Alice và Bob đều đồng ý rằng khóa bí mật là :math:`1`.

Có một vấn đề là Diffie-Hellman không cung cấp xác thực. Một kiểu tấn công có thể lợi dụng điều này là *tấn công người trung gian* (man-in-the-middle attack). Giả sử Mallory là một kẻ tấn công có khả năng chặn các thông điệp. Mallory đã biết *p* và *g* vì chúng là công khai, và cô ấy sinh ra các giá trị riêng tư ngẫu nhiên :math:`c` và :math:`d` để sử dụng với Alice và Bob, tương ứng. Khi Alice và Bob gửi các giá trị công khai cho nhau, Mallory chặn chúng và gửi các giá trị công khai của mình, như minh họa trong :numref:`Figure %s <fig-manInTheMiddle>`. Kết quả là Alice và Bob mỗi người đều vô tình chia sẻ một khóa với Mallory thay vì với nhau.

.. _fig-manInTheMIddle:
.. figure:: figures/f08-12-9780123850591.png
   :width: 300px
   :align: center

   Một cuộc tấn công người trung gian.

Một biến thể của Diffie-Hellman đôi khi được gọi là *Diffie-Hellman cố định* (fixed Diffie-Hellman) hỗ trợ xác thực một hoặc cả hai thành viên. Nó dựa vào các chứng chỉ tương tự như chứng chỉ khóa công khai nhưng thay vào đó chứng thực các tham số Diffie-Hellman công khai của một thực thể. Ví dụ, một chứng chỉ như vậy sẽ nêu rằng các tham số Diffie-Hellman của Alice là *p, g*, và :math:`g^a \bmod p`
(lưu ý rằng giá trị *a* vẫn chỉ mình Alice biết). Một chứng chỉ như vậy sẽ đảm bảo với Bob rằng thành viên còn lại trong Diffie-Hellman là Alice—nếu không, thành viên đó sẽ không thể tính được khóa bí mật vì không biết *a*. Nếu cả hai thành viên đều có chứng chỉ cho các tham số Diffie-Hellman của mình, họ có thể xác thực lẫn nhau. Nếu chỉ một người có chứng chỉ, thì chỉ người đó được xác thực. Điều này hữu ích trong một số tình huống; ví dụ, khi một thành viên là máy chủ web và thành viên còn lại là client bất kỳ, client có thể xác thực máy chủ web và thiết lập một khóa bí mật để đảm bảo bảo mật trước khi gửi số thẻ tín dụng cho máy chủ web.
