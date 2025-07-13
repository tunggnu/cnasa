Lời nói đầu
===========

Đã gần mười năm kể từ khi ấn bản thứ năm của *Computer Networks: A Systems Approach* được xuất bản. Rất nhiều điều đã thay đổi trong thời gian đó, đáng chú ý nhất là sự bùng nổ của điện toán đám mây và các ứng dụng trên điện thoại thông minh. Ở nhiều khía cạnh, điều này gợi nhớ đến tác động mạnh mẽ mà Web đã có đối với Internet khi chúng tôi xuất bản ấn bản đầu tiên của cuốn sách vào năm 1996.

Ấn bản thứ sáu này thích nghi với thời đại, nhưng vẫn giữ *Phương pháp Tiếp cận Hệ thống* làm ngôi sao Bắc Đẩu. Nói một cách tổng quát, chúng tôi cập nhật và cải tiến ấn bản mới này theo bốn cách chính:

-  Chúng tôi làm mới các ví dụ để phản ánh trạng thái hiện tại của thế giới. Điều này bao gồm việc loại bỏ những điều lỗi thời (ví dụ: modem quay số), sử dụng các ứng dụng phổ biến (ví dụ: Netflix, Spotify) để minh họa cho các vấn đề đang được giải quyết, và cập nhật các con số để đại diện cho công nghệ tiên tiến nhất (ví dụ: Ethernet 10-Gbps).

-  Chúng tôi kết nối các điểm giữa nghiên cứu ban đầu dẫn đến sự phát triển của các công nghệ như multicast, truyền video thời gian thực và chất lượng dịch vụ, với các ứng dụng đám mây quen thuộc ngày nay như Zoom, Netflix và Spotify. Điều này phù hợp với nhấn mạnh của chúng tôi về quá trình thiết kế chứ không chỉ là kết quả cuối cùng, điều này đặc biệt quan trọng ngày nay khi rất nhiều chức năng của Internet chủ yếu được cung cấp bởi các dịch vụ thương mại độc quyền.

- Chúng tôi đặt Internet trong bối cảnh rộng lớn hơn của Đám mây, và quan trọng không kém, trong bối cảnh các lực lượng thương mại đang định hình Đám mây. Điều này có tác động tối thiểu đến các chi tiết kỹ thuật được trình bày xuyên suốt cuốn sách, nhưng được thảo luận trong một phần *Góc nhìn* mới ở cuối mỗi chương. Chúng tôi hy vọng một tác dụng phụ của cuộc thảo luận này là giúp độc giả trân trọng sự tiến hóa liên tục của Internet, và cơ hội đổi mới mà điều này mang lại.

- Chúng tôi chắt lọc các nguyên tắc quan trọng của thiết kế mạng trong một loạt các *Điểm then chốt* xuyên suốt cuốn sách. Mỗi điểm then chốt là một phát biểu ngắn gọn về một quy tắc thiết kế hệ thống tổng quát hoặc một khái niệm mạng cơ bản, dựa trên các ví dụ được trình bày trong phần văn bản xung quanh. Về mặt sư phạm, các điểm then chốt này tương ứng với các *mục tiêu học tập* cấp cao cho cuốn sách.

Phương pháp Tiếp cận Hệ thống là gì?
------------------------------------

"Phương pháp Tiếp cận Hệ thống" đề cập đến lĩnh vực thiết kế và triển khai các hệ thống máy tính. Thuật ngữ này thường được các nhà nghiên cứu và thực hành khoa học máy tính sử dụng để nghiên cứu các vấn đề nảy sinh khi xây dựng các hệ thống máy tính phức tạp như hệ điều hành, mạng, ứng dụng phân tán, v.v. Chìa khóa của phương pháp tiếp cận hệ thống là cái nhìn "tổng thể": bạn cần xem xét cách các thành phần của một hệ thống tương tác với nhau để đạt được kết quả chung, thay vì chỉ tối ưu hóa từng thành phần riêng lẻ. Trong bối cảnh mạng, điều đó thường có nghĩa là vượt ra ngoài quan điểm phân lớp truyền thống để xem xét cách một vấn đề nên được giải quyết tốt nhất, có thể liên quan đến nhiều lớp khác nhau. Một ví dụ điển hình là kiểm soát tắc nghẽn, vấn đề này liên quan đến nhiều lớp và ảnh hưởng đến cả hệ thống đầu cuối lẫn các thiết bị mạng. Chúng tôi xem xét tắc nghẽn, một vấn đề toàn hệ thống, trong một chương riêng thay vì cố gắng nhồi nhét nó vào lớp vận chuyển, chẳng hạn. Phương pháp Tiếp cận Hệ thống tập trung mạnh vào việc triển khai thực tế, với Internet là ví dụ rõ ràng nhất về một hệ thống mạng phức tạp được triển khai rộng rãi.

Nội dung mới trong ấn bản thứ sáu
---------------------------------

Cụ thể hơn, ấn bản thứ sáu bao gồm các thay đổi lớn sau:

-  Phần Góc nhìn mới ở Chương 1 giới thiệu chủ đề lặp lại *Đám mây hóa*.
-  Phần 2.8 mới mô tả *Mạng truy cập*, bao gồm Mạng Quang Thụ Động (PON) và Mạng truy cập vô tuyến 5G (RAN).
-  Các chủ đề ở Mục 3.1 (*Kiến thức cơ bản về chuyển mạch*) và 3.2 (*Ethernet chuyển mạch*) được tái cấu trúc, bao gồm mở rộng phạm vi về VLAN.
-  Phần 3.5 được cập nhật để bao gồm mô tả về *Switch Bare-Metal* và *Mạng Định nghĩa bằng Phần mềm* (SDN).
-  Phần Góc nhìn mới ở Chương 3 mô tả các lớp phủ mạng ảo, *VXLAN*, và vai trò của lớp phủ trong Đám mây.
-  Các chủ đề ở Mục 4.1 (*Internet toàn cầu*) và 4.2 (*IP Phiên bản 6*) được tái cấu trúc.
-  Phần Góc nhìn mới ở Chương 4 mô tả cách *Đám mây* ảnh hưởng đến cấu trúc của Internet.
-  Phần 5.2 được mở rộng để bao gồm thảo luận về *QUIC*.
-  Phần 5.3 được mở rộng để bao gồm mô tả về *gRPC*.
-  Các phần 6.3 và 6.4 được cập nhật để bao gồm mô tả về *TCP CUBIC, DCTCP,* và *BBR*.
-  Phần 6.4 được mở rộng để bao gồm mô tả về *Quản lý Hàng đợi Chủ động* (AQM).
-  Phần 7.1 được mở rộng để bao gồm mô tả về *Protocol Buffers*.
-  Phần 7.2 được mở rộng để bao gồm mô tả về *HTTP Adaptive Streaming*.
-  Phần 8.1 mới giới thiệu tính hai mặt của *Mối đe dọa* và *Niềm tin*.
-  Các chủ đề ở Mục 8.3 (*Phân phối khóa trước*) và 8.4 (*Giao thức xác thực*) được tái cấu trúc.
-  Phần Góc nhìn mới ở Chương 8 thảo luận về quỹ đạo của an ninh mạng và đặt câu hỏi liệu an ninh có đang được cải thiện hay xấu đi.
-  Phần 9.1 được cập nhật để bao gồm mô tả về *HTTP/2*, cùng với thảo luận về *REST*, *gRPC*, và *Dịch vụ Đám mây*.
-  Phần 9.3 được mở rộng để bao gồm mô tả về các *Hệ thống Quản lý Mạng* hiện đại bao gồm việc sử dụng *OpenConfig* và *gNMI*.

Tổ chức
-------

Để xây dựng một khóa học về mạng máy tính dựa trên tài liệu trong cuốn sách này, sẽ hữu ích nếu hiểu được tổ chức tổng thể, có thể được đặc trưng bởi ba phần chính:

* Tài liệu khái niệm và nền tảng, tức là những ý tưởng lớn nằm ở trung tâm của mạng máy tính.

* Các giao thức và thuật toán cốt lõi minh họa cách các ý tưởng nền tảng được áp dụng vào thực tiễn.

* Tài liệu nâng cao có thể phù hợp hoặc không phù hợp với bất kỳ khóa học học kỳ nào.

Đặc điểm này có thể được áp dụng ở cấp độ Chương: Chương 1 là nền tảng, các Chương 2, 3, 5 và 9 là cốt lõi, và các Chương 4, 6, 7 và 8 đề cập đến các chủ đề nâng cao hơn.

Đặc điểm này cũng có thể được áp dụng ở cấp độ Mục, nói chung, mỗi Chương tiến triển từ các khái niệm cơ bản đến công nghệ cụ thể rồi đến các kỹ thuật nâng cao. Ví dụ, Chương 3 bắt đầu bằng việc giới thiệu các kiến thức cơ bản về mạng chuyển mạch (3.1), sau đó đề cập đến các chi tiết cụ thể của Ethernet chuyển mạch và Internet IP (3.2-3.4), và kết thúc bằng một thảo luận tùy chọn về SDN (3.5). Tương tự, Chương 6 bắt đầu với các ý tưởng nền tảng (6.1-6.2), sau đó khám phá kiểm soát tắc nghẽn TCP (6.3), và kết thúc bằng tài liệu nâng cao tùy chọn (6.4-6.5).

Tài liệu bổ sung
----------------

Chúng tôi đã tạo ra một bộ tài liệu bổ sung, bao gồm các slide cho giảng viên và sách hướng dẫn giải bài tập xuất hiện trong phiên bản in của cuốn sách này. Sách hướng dẫn giải bài tập được Elsevier kiểm soát nhằm hạn chế quyền truy cập cho các giảng viên được ủy quyền. Nó có thể được tìm thấy tại:
https://educate.elsevier.com/book/details/9780128182000 (ở Hoa Kỳ)
hoặc:
https://inspectioncopy.elsevier.com/book/details/9780128182000 (ngoài Hoa Kỳ). Các slide cũng ở cùng địa chỉ nhưng cũng có thể được tìm thấy trong kho GitHub của chúng tôi:
https://github.com/SystemsApproach/book (xem thư mục 6E-bottomupslides).
Nếu bạn gặp bất kỳ khó khăn nào khi truy cập các tài liệu này, vui lòng liên hệ qua ``discuss@systemsapproach.org``.

Ví dụ mã nguồn
--------------

Kể từ ấn bản đầu tiên của cuốn sách này, chúng tôi đã sử dụng các ví dụ mã nguồn để minh họa cách các giao thức và thuật toán khác nhau có thể được triển khai. Ban đầu, mã nguồn đến từ *x*-kernel, một khung làm việc cho việc triển khai và nghiên cứu giao thức được phát triển vào những năm 1990, và bạn có thể biên dịch và chạy mọi đoạn mã trong cuốn sách. Vì mã nguồn *x*-kernel không còn được duy trì nữa, chúng tôi không còn kiểm tra xem tất cả mã trong sách có biên dịch hoặc chạy được không. Ở thời điểm này, mã nguồn chỉ nên được sử dụng như một hướng dẫn để giúp người đọc hiểu chủ đề đang được đề cập.

Lời cảm ơn
----------

Chúng tôi xin cảm ơn những người sau đây đã giúp đỡ với nội dung mới:

-  Larry Brakmo: Kiểm soát tắc nghẽn TCP
-  Carmelo Cascone: Switch Bare-Metal
-  Charles Chan: Switch Bare-Metal
-  Jude Nelson: Nhận diện phi tập trung
-  Oguz Sunay: Mạng di động
-  Thomas Vachuska: Quản lý mạng

Cùng với các cá nhân sau (người dùng GitHub) vì những đóng góp và sửa lỗi khác nhau:

.. hlist::
   :columns: 3

   -  Mohammed Al-Ameen
   -  Mike Appelman
   -  Andy Bavier
   -  Manuel Berfelde
   -  Brian Bohe
   -  John Craton
   -  Peter DeLong
   -  Aaron Gember-Jacobson
   -  Chris Goldsworthy
   -  John Hartman
   -  Ethan Lam
   -  Diego López León
   -  Matteo Scandolo (teone)
   -  Mike Wawrzoniak
   -  罗泽轩 (spacewander)
   -  Arnaud (arvdrpoo)
   -  Desmond (kingdido999)
   -  Guo (ZJUGuoShuai)
   -  Hellman (eshellman)
   -  Xtao (vertextao)
   -  Joep (joepeding)
   -  Seth (springbov)
   -  miluchen (miluchen)
   -  Takashi Idobe (Takashiidobe)
   -  Beth (Innominata88)
   -  Mohit Mayank (mmynk)
   -  Mark Liffiton (liffiton)
   -  Junhao Zhang (junhaoim)
   -  Cooper Vandiver (CooperVandiver)
   -  Waheed Hafez (WaheedHafez)
   -  Benjamin Levy (BenjaminLevy)
   -  Takashi Idobe (Takashiidobe)
   -  Kirby Linvill (klinvill)
   -  Osman Karaketir (karaketir16)
   -  Anderson Adon (Anderson-A)
   -  李俊康 (lijunkang12)
   -  Yihao Wang (y1hao)
   -  Charles Reiss (charlesreiss)
   -  Karanveer B. (KaranveerB)
   -  Meek Msaki (mmsaki)
   -  Jeroen van der Ham-de Vos (jeroen)

Cuối cùng, chúng tôi xin cảm ơn các nhà phản biện sau đây vì nhiều ý kiến và đề xuất hữu ích. Ảnh hưởng của họ là rất lớn.

- Mark J. Indelicato, Viện Công nghệ Rochester
- Michael Yonshik Choi, Viện Công nghệ Illinois
- Sarvesh Kulkarni, Đại học Villanova
- Alexander L. Wijesinha, Đại học Towson

Mã nguồn mở
-----------

Mã nguồn cho cuốn sách này có tại
https://github.com/SystemsApproach/book, và được phát hành theo các điều khoản của giấy phép Creative Commons (CC BY 4.0). Cộng đồng được mời đóng góp các bản sửa lỗi, cải tiến, cập nhật và nội dung mới theo cùng điều khoản. Giống như nhiều dự án phần mềm mã nguồn mở, dự án này được khởi đầu từ nội dung từng bị hạn chế: ấn bản thứ năm của Peterson và Davie, bản quyền thuộc Elsevier. Chúng tôi hy vọng việc mã nguồn mở tài liệu này sẽ giúp nó được phổ biến rộng rãi và trở thành điểm thu hút cho nội dung mới: cập nhật những gì đã có, mở rộng để bao quát các chủ đề mới, và bổ sung thêm tài liệu giảng dạy.

Nếu bạn sử dụng tài liệu này, phần ghi nhận nên bao gồm các thông tin sau:

|  Tiêu đề: *Computer Networks: A Systems Approach*
|  Tác giả: Larry Peterson và Bruce Davie
|  Bản quyền: Elsevier, 2012
|  Nguồn: https://github.com/SystemsApproach/book
|  Giấy phép: `CC BY  4.0 <https://creativecommons.org/licenses/by/4.0>`__

Cách đóng góp
-------------

Chúng tôi hy vọng rằng, nếu bạn sử dụng tài liệu này, bạn cũng sẵn sàng đóng góp lại cho nó. Nếu bạn mới làm quen với mã nguồn mở, bạn có thể tham khảo hướng dẫn How to Contribute to Open Source (https://opensource.guide/how-to-contribute). Trong đó, bạn sẽ tìm hiểu về việc đăng các Vấn đề (Issues) mà bạn muốn được giải quyết, và gửi các Yêu cầu Kéo (Pull Requests) để hợp nhất các cải tiến của bạn trở lại GitHub. Chúng tôi hy vọng bạn đã nhận được giá trị từ Computer Networks: A Systems Approach trong những năm qua, và chúng tôi rất mong bạn tham gia cùng chúng tôi trong hành trình mới này.

| Larry & Bruce
| Tháng 10 năm 2020
