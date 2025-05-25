2.3 Đóng khung (Framing)
========================

Bây giờ chúng ta đã biết cách truyền một chuỗi bit qua một liên kết điểm-điểm—từ bộ điều hợp này sang bộ điều hợp khác—hãy xem xét kịch bản trong :numref:`Hình %s <fig-host-link>`. Hãy nhớ lại từ Chương 1 rằng chúng ta đang tập trung vào các mạng chuyển mạch gói, nghĩa là các khối dữ liệu (gọi là *khung* ở mức này), chứ không phải luồng bit, được trao đổi giữa các nút. Chính bộ điều hợp mạng cho phép các nút trao đổi các khung. Khi nút A muốn truyền một khung tới nút B, nó yêu cầu bộ điều hợp của mình truyền một khung từ bộ nhớ của nút. Điều này dẫn đến một chuỗi bit được gửi qua liên kết. Bộ điều hợp trên nút B sau đó tập hợp chuỗi bit nhận được trên liên kết và đặt khung tương ứng vào bộ nhớ của B. Nhận biết chính xác tập hợp bit nào tạo thành một khung—tức là xác định nơi bắt đầu và kết thúc của khung—là thách thức trung tâm mà bộ điều hợp phải đối mặt.

.. _fig-host-link:
.. figure:: figures/f02-06-9780123850591.png
   :width: 500px
   :align: center

   Bit truyền giữa các bộ điều hợp, khung truyền giữa các host.

Có nhiều cách để giải quyết vấn đề đóng khung. Phần này sử dụng ba giao thức khác nhau để minh họa các điểm khác nhau trong không gian thiết kế. Lưu ý rằng mặc dù chúng ta thảo luận về đóng khung trong bối cảnh các liên kết điểm-điểm, nhưng đây là một vấn đề cơ bản cũng phải được giải quyết trong các mạng truy cập đa điểm như Ethernet và Wi-Fi.

2.3.1 Giao thức hướng byte (PPP)
--------------------------------

Một trong những cách tiếp cận lâu đời nhất đối với đóng khung—nó bắt nguồn từ việc kết nối các terminal với máy tính lớn—là xem mỗi khung như một tập hợp các byte (ký tự) thay vì một tập hợp các bit. Các ví dụ ban đầu về các giao thức *hướng byte* như vậy là giao thức Binary Synchronous Communication (BISYNC) do IBM phát triển vào cuối những năm 1960, và giao thức Digital Data Communication Message Protocol (DDCMP) được sử dụng trong DECNET của Digital Equipment Corporation. (Ngày xưa, các công ty máy tính lớn như IBM và DEC cũng xây dựng các mạng riêng cho khách hàng của họ.) Giao thức Point-to-Point Protocol (PPP) được sử dụng rộng rãi là một ví dụ gần đây của cách tiếp cận này.

Ở mức cao, có hai cách tiếp cận đóng khung hướng byte. Cách thứ nhất là sử dụng các ký tự đặc biệt gọi là *ký tự đánh dấu* (sentinel characters) để chỉ ra nơi bắt đầu và kết thúc của khung. Ý tưởng là đánh dấu bắt đầu một khung bằng cách gửi một ký tự SYN (đồng bộ hóa) đặc biệt. Phần dữ liệu của khung đôi khi được đặt giữa hai ký tự đặc biệt khác: STX (bắt đầu văn bản) và ETX (kết thúc văn bản). BISYNC sử dụng cách tiếp cận này. Vấn đề với phương pháp đánh dấu là một trong các ký tự đặc biệt có thể xuất hiện trong phần dữ liệu của khung. Cách tiêu chuẩn để vượt qua vấn đề này là “thoát” ký tự đó bằng cách đặt trước nó một ký tự DLE (data-link-escape) bất cứ khi nào nó xuất hiện trong phần thân khung; ký tự DLE cũng được thoát (bằng cách đặt thêm một DLE nữa phía trước) trong phần thân khung. (Lập trình viên C có thể nhận thấy điều này tương tự như cách dấu ngoặc kép được thoát bằng dấu gạch chéo ngược khi nó xuất hiện trong một chuỗi.) Cách tiếp cận này thường được gọi là *nhồi ký tự* (character stuffing) vì các ký tự bổ sung được chèn vào phần dữ liệu của khung.

Cách thay thế cho việc phát hiện kết thúc khung bằng giá trị đánh dấu là bao gồm số byte trong khung ở đầu khung, trong phần header của khung. DDCMP sử dụng cách tiếp cận này. Một nguy cơ với cách này là lỗi truyền có thể làm hỏng trường đếm, khi đó kết thúc của khung sẽ không được phát hiện đúng. (Một vấn đề tương tự cũng tồn tại với phương pháp đánh dấu nếu trường ETX bị hỏng.) Nếu điều này xảy ra, bộ thu sẽ tích lũy số byte như trường đếm sai chỉ ra và sau đó sử dụng trường phát hiện lỗi để xác định rằng khung bị lỗi. Điều này đôi khi được gọi là *lỗi đóng khung* (framing error). Bộ thu sau đó sẽ chờ cho đến khi thấy ký tự SYN tiếp theo để bắt đầu thu thập các byte tạo thành khung tiếp theo. Do đó, có thể một lỗi đóng khung sẽ khiến hai khung liên tiếp bị nhận sai.

Giao thức Point-to-Point Protocol (PPP), thường được sử dụng để truyền các gói Internet Protocol qua nhiều loại liên kết điểm-điểm khác nhau, sử dụng ký tự đánh dấu và nhồi ký tự. Định dạng cho một khung PPP được đưa ra trong :numref:`Hình %s <fig-ppp>`.

.. _fig-ppp:
.. figure:: figures/f02-08-9780123850591.png
   :width: 500px
   :align: center

   Định dạng khung PPP.

Hình này là hình đầu tiên trong nhiều hình bạn sẽ thấy trong cuốn sách này được dùng để minh họa định dạng khung hoặc gói tin, nên cần giải thích một chút. Chúng tôi thể hiện một gói tin như một chuỗi các trường có nhãn. Phía trên mỗi trường là một số chỉ độ dài của trường đó tính bằng bit. Lưu ý rằng các gói tin được truyền bắt đầu từ trường ngoài cùng bên trái.

Ký tự đặc biệt bắt đầu văn bản, được ký hiệu là trường ``Flag``, có giá trị ``01111110``. Các trường ``Address`` và ``Control`` thường chứa giá trị mặc định nên không có gì đặc biệt. Trường (Protocol) được dùng để tách kênh; nó xác định giao thức tầng cao, như IP. Kích thước payload của khung có thể được thương lượng, nhưng mặc định là 1500 byte. Trường ``Checksum`` có độ dài 2 (mặc định) hoặc 4 byte. Lưu ý rằng mặc dù tên gọi phổ biến là checksum, trường này thực ra là CRC chứ không phải checksum (như sẽ mô tả ở phần sau).

Định dạng khung PPP là bất thường ở chỗ một số trường có độ dài được thương lượng thay vì cố định. Việc thương lượng này được thực hiện bởi một giao thức gọi là Link Control Protocol (LCP). PPP và LCP hoạt động song song: LCP gửi các thông điệp điều khiển được đóng gói trong các khung PPP—các thông điệp này được nhận diện bởi trường Protocol của PPP có giá trị LCP—và sau đó thay đổi định dạng khung PPP dựa trên thông tin chứa trong các thông điệp điều khiển đó. LCP cũng tham gia vào việc thiết lập liên kết giữa hai peer khi cả hai phía phát hiện rằng liên lạc qua liên kết là khả thi (ví dụ, khi mỗi bộ thu quang phát hiện tín hiệu đến từ sợi quang mà nó kết nối).

2.3.2 Giao thức hướng bit (HDLC)
--------------------------------

Không giống như các giao thức hướng byte, một giao thức hướng bit không quan tâm đến ranh giới byte—nó chỉ xem khung như một tập hợp các bit. Các bit này có thể đến từ một bộ ký tự nào đó, như ASCII; có thể là giá trị pixel trong một ảnh; hoặc có thể là các lệnh và toán hạng từ một file thực thi. Giao thức Synchronous Data Link Control (SDLC) do IBM phát triển là một ví dụ về giao thức hướng bit; SDLC sau này được chuẩn hóa bởi ISO thành giao thức High-Level Data Link Control (HDLC). Trong phần thảo luận sau, chúng ta sử dụng HDLC làm ví dụ; định dạng khung của nó được đưa ra trong :numref:`Hình %s <fig-hdlc>`.

HDLC đánh dấu cả bắt đầu và kết thúc của một khung bằng chuỗi bit đặc biệt ``01111110``. Chuỗi này cũng được truyền trong bất kỳ thời điểm nào liên kết ở trạng thái rỗi để bộ phát và bộ thu có thể giữ đồng bộ xung nhịp. Theo cách này, cả hai giao thức về cơ bản đều sử dụng phương pháp đánh dấu. Vì chuỗi này có thể xuất hiện ở bất kỳ đâu trong phần thân của khung—thực tế, các bit ``01111110`` có thể cắt ngang ranh giới byte—các giao thức hướng bit sử dụng tương tự ký tự DLE, một kỹ thuật gọi là *nhồi bit* (bit stuffing).

.. _fig-hdlc:
.. figure:: figures/f02-10-9780123850591.png
   :width: 400px
   :align: center

   Định dạng khung HDLC.

Nhồi bit trong giao thức HDLC hoạt động như sau. Ở phía gửi, bất cứ khi nào năm số 1 liên tiếp được truyền từ phần thân thông điệp (tức là, không tính khi bộ phát cố gắng truyền chuỗi đặc biệt ``01111110``), bộ phát sẽ chèn một số 0 trước khi truyền bit tiếp theo. Ở phía nhận, nếu năm số 1 liên tiếp đến, bộ thu sẽ quyết định dựa trên bit tiếp theo mà nó thấy (tức là, bit sau năm số 1). Nếu bit tiếp theo là 0, nó chắc chắn đã được nhồi vào, nên bộ thu loại bỏ nó. Nếu bit tiếp theo là 1, thì một trong hai điều sau đúng: Hoặc đây là ký hiệu kết thúc khung, hoặc đã có lỗi xuất hiện trong luồng bit. Bằng cách nhìn vào *bit tiếp theo*, bộ thu có thể phân biệt hai trường hợp này. Nếu nó thấy một số 0 (tức là, 8 bit cuối cùng nó vừa xem là ``01111110``), thì đó là ký hiệu kết thúc khung; nếu nó thấy một số 1 (tức là, 8 bit cuối cùng là ``01111111``), thì chắc chắn đã có lỗi và toàn bộ khung bị loại bỏ. Trong trường hợp sau, bộ thu phải chờ đến khi thấy ``01111110`` tiếp theo trước khi có thể bắt đầu nhận lại, và do đó, có khả năng bộ thu sẽ không nhận được hai khung liên tiếp. Rõ ràng, vẫn còn những cách mà lỗi đóng khung có thể không bị phát hiện, chẳng hạn khi toàn bộ mẫu kết thúc khung giả được tạo ra bởi lỗi, nhưng những trường hợp này khá hiếm. Các phương pháp phát hiện lỗi mạnh mẽ hơn sẽ được thảo luận ở phần sau.

Một đặc điểm thú vị của nhồi bit cũng như nhồi ký tự là kích thước của một khung phụ thuộc vào dữ liệu được gửi trong payload của khung. Thực tế là không thể làm cho tất cả các khung có cùng kích thước, vì dữ liệu mang trong mỗi khung có thể tùy ý. (Để tự thuyết phục, hãy xem điều gì xảy ra nếu byte cuối cùng của phần thân khung là ký tự ETX.) Một dạng đóng khung đảm bảo tất cả các khung có cùng kích thước sẽ được mô tả ở phần tiếp theo.

2.3.3 Đóng khung dựa trên xung nhịp (SONET)
-------------------------------------------

Một cách tiếp cận thứ ba đối với đóng khung được thể hiện qua tiêu chuẩn Synchronous Optical Network (SONET). Vì thiếu một thuật ngữ chung được chấp nhận rộng rãi, chúng tôi gọi cách tiếp cận này đơn giản là *đóng khung dựa trên xung nhịp* (clock-based framing). SONET được đề xuất đầu tiên bởi Bell Communications Research (Bellcore), sau đó được phát triển dưới sự bảo trợ của American National Standards Institute (ANSI) cho truyền dẫn số qua cáp quang; nó đã được ITU-T chấp nhận. SONET trong nhiều năm là tiêu chuẩn thống trị cho truyền dẫn dữ liệu đường dài qua các mạng quang.

Một điểm quan trọng về SONET trước khi đi sâu hơn là toàn bộ đặc tả của nó lớn hơn rất nhiều so với cuốn sách này. Do đó, phần thảo luận sau đây chỉ đề cập đến những điểm chính của tiêu chuẩn. Ngoài ra, SONET giải quyết cả vấn đề đóng khung và vấn đề mã hóa. Nó cũng giải quyết một vấn đề rất quan trọng đối với các công ty điện thoại—ghép kênh nhiều liên kết tốc độ thấp lên một liên kết tốc độ cao. (Thực tế, nhiều thiết kế của SONET phản ánh việc các công ty điện thoại phải quan tâm đến việc ghép kênh số lượng lớn các kênh 64 kbps truyền thống dùng cho cuộc gọi điện thoại.) Chúng ta bắt đầu với cách tiếp cận đóng khung của SONET và sẽ thảo luận các vấn đề khác sau đó.

Cũng giống như các sơ đồ đóng khung đã thảo luận trước đó, một khung SONET có một số thông tin đặc biệt cho biết bộ thu nơi bắt đầu và kết thúc của khung; tuy nhiên, điểm giống nhau chỉ dừng lại ở đó. Đáng chú ý, không sử dụng nhồi bit, nên độ dài của một khung không phụ thuộc vào dữ liệu được gửi. Vậy câu hỏi đặt ra là “Làm thế nào bộ thu biết được mỗi khung bắt đầu và kết thúc ở đâu?” Chúng ta sẽ xét câu hỏi này cho liên kết SONET tốc độ thấp nhất, gọi là STS-1 và chạy ở 51,84 Mbps. Một khung STS-1 được minh họa trong :numref:`Hình %s <fig-sonet-frame>`. Nó được sắp xếp thành 9 hàng, mỗi hàng 90 byte, và 3 byte đầu tiên của mỗi hàng là overhead, phần còn lại dành cho dữ liệu được truyền qua liên kết. Hai byte đầu tiên của khung chứa một mẫu bit đặc biệt, và chính các byte này cho phép bộ thu xác định nơi bắt đầu của khung. Tuy nhiên, vì không sử dụng nhồi bit, không có lý do gì mẫu này không thỉnh thoảng xuất hiện trong phần payload của khung. Để phòng ngừa điều này, bộ thu tìm kiếm mẫu bit đặc biệt một cách nhất quán, hy vọng thấy nó xuất hiện mỗi 810 byte, vì mỗi khung dài 9 × 90 = 810 byte. Khi mẫu đặc biệt xuất hiện đúng vị trí đủ số lần, bộ thu kết luận rằng nó đã đồng bộ và có thể diễn giải khung đúng cách.

.. _fig-sonet-frame:
.. figure:: figures/f02-11-9780123850591.png
   :width: 500px
   :align: center

   Một khung SONET STS-1.

Một trong những điều chúng tôi không mô tả do độ phức tạp của SONET là cách sử dụng chi tiết tất cả các byte overhead khác. Một phần của sự phức tạp này là do SONET chạy trên mạng quang của nhà mạng, không chỉ trên một liên kết đơn lẻ. (Hãy nhớ rằng chúng ta đang bỏ qua thực tế là các nhà mạng triển khai một mạng, và thay vào đó tập trung vào việc chúng ta có thể thuê một liên kết SONET từ họ và sử dụng liên kết này để xây dựng mạng chuyển mạch gói của riêng mình.) Sự phức tạp bổ sung còn đến từ việc SONET cung cấp một tập hợp dịch vụ phong phú hơn nhiều so với chỉ truyền dữ liệu. Ví dụ, 64 kbps dung lượng của một liên kết SONET được dành riêng cho một kênh thoại dùng cho bảo trì.

Các byte overhead của một khung SONET được mã hóa bằng NRZ, sơ đồ mã hóa đơn giản đã mô tả ở phần trước, trong đó 1 là mức cao và 0 là mức thấp. Tuy nhiên, để đảm bảo có đủ chuyển đổi cho phép bộ thu khôi phục xung nhịp của bộ phát, các byte payload được *xáo trộn* (scrambled). Điều này được thực hiện bằng cách tính toán XOR giữa dữ liệu cần truyền và một mẫu bit đã biết. Mẫu bit này dài 127 bit, có nhiều chuyển đổi từ 1 sang 0, nên khi XOR với dữ liệu truyền sẽ tạo ra tín hiệu có đủ chuyển đổi để cho phép khôi phục xung nhịp.

SONET hỗ trợ ghép kênh nhiều liên kết tốc độ thấp theo cách sau. Một liên kết SONET nhất định chạy ở một trong số hữu hạn các tốc độ, từ 51,84 Mbps (STS-1) đến 39.813.120 Mbps (STS-768).\ [#]_ Lưu ý rằng tất cả các tốc độ này đều là bội số nguyên của STS-1. Ý nghĩa đối với đóng khung là một khung SONET đơn có thể chứa các khung con cho nhiều kênh tốc độ thấp hơn. Một đặc điểm liên quan thứ hai là mỗi khung dài 125 μs. Điều này có nghĩa là ở tốc độ STS-1, một khung SONET dài 810 byte, trong khi ở tốc độ STS-3, mỗi khung SONET dài 2430 byte. Lưu ý sự cộng hưởng giữa hai đặc điểm này: 3 × 810 = 2430, nghĩa là ba khung STS-1 vừa khít trong một khung STS-3.

.. [#] STS là viết tắt của *Synchronous Transport Signal*, là cách SONET nói về các khung. Có một thuật ngữ song song—*Optical Carrier* (OC)—được dùng để nói về tín hiệu quang mang các khung SONET. Chúng tôi nói hai thuật ngữ này song song vì STS-3 và OC-3, lấy ví dụ cụ thể, đều ngụ ý tốc độ truyền 155,52 Mbps. Vì chúng ta đang tập trung vào đóng khung ở đây, chúng tôi sẽ dùng STS, nhưng thực tế bạn sẽ thường nghe ai đó nói về liên kết quang bằng tên “OC”.

Trực giác mà nói, khung STS-N có thể được coi như gồm N khung STS-1, trong đó các byte từ các khung này được xen kẽ; tức là, một byte từ khung đầu tiên được truyền, sau đó một byte từ khung thứ hai, v.v. Lý do để xen kẽ các byte từ mỗi khung STS-N là để đảm bảo các byte trong mỗi khung STS-1 được phân phối đều; tức là, các byte xuất hiện ở bộ thu với tốc độ đều đặn 51 Mbps, thay vì dồn lại trong một phần :math:`1/N^{th}` của khoảng 125 μs.

.. _fig-sonet1:
.. figure:: figures/f02-12-9780123850591.png
   :width: 350px
   :align: center

   Ba khung STS-1 được ghép kênh vào một khung STS-3c.

Mặc dù có thể coi một tín hiệu STS-N được dùng để ghép kênh N khung STS-1, payload từ các khung STS-1 này có thể được liên kết với nhau để tạo thành một payload STS-N lớn hơn; liên kết như vậy được ký hiệu là STS-Nc (cho *concatenated*). Một trong các trường trong overhead được dùng cho mục đích này. :numref:`Hình %s <fig-sonet1>` mô tả sơ đồ liên kết trong trường hợp ba khung STS-1 được liên kết thành một khung STS-3c duy nhất. Ý nghĩa của một liên kết SONET được ký hiệu là STS-3c thay vì STS-3 là ở trường hợp đầu, người dùng liên kết có thể coi nó như một đường truyền 155,25 Mbps duy nhất, trong khi STS-3 thực ra nên được coi là ba liên kết 51,84 Mbps cùng chia sẻ một sợi quang.

.. _fig-sonet3:
.. figure:: figures/f02-13-9780123850591.png
   :width: 450px
   :align: center

   Các khung SONET lệch pha.

Cuối cùng, mô tả trước đây về SONET là quá đơn giản ở chỗ nó giả định rằng payload của mỗi khung hoàn toàn nằm trong khung đó. (Tại sao lại không như vậy?) Thực tế, chúng ta nên coi khung STS-1 vừa mô tả chỉ là một placeholder cho khung, nơi payload thực tế có thể *trôi nổi* qua các ranh giới khung. Tình huống này được minh họa trong :numref:`Hình %s <fig-sonet3>`. Ở đây chúng ta thấy cả payload STS-1 trôi nổi qua hai khung STS-1 và payload bị dịch sang phải một số byte và do đó bị cuộn lại. Một trong các trường trong overhead của khung chỉ tới điểm bắt đầu của payload. Giá trị của khả năng này là nó đơn giản hóa nhiệm vụ đồng bộ hóa các xung nhịp được sử dụng trong toàn bộ mạng của các nhà mạng, điều mà các nhà mạng dành rất nhiều thời gian để quan tâm.