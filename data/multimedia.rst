7.2 Dữ liệu Đa phương tiện
==========================

Dữ liệu đa phương tiện, bao gồm âm thanh, video và hình ảnh tĩnh, hiện chiếm phần lớn lưu lượng trên Internet. Một phần lý do khiến việc truyền tải rộng rãi dữ liệu đa phương tiện qua mạng trở nên khả thi là nhờ những tiến bộ trong công nghệ nén. Bởi vì dữ liệu đa phương tiện chủ yếu được con người tiêu thụ thông qua các giác quan—thị giác và thính giác—và được xử lý bởi não bộ con người, nên có những thách thức đặc biệt trong việc nén loại dữ liệu này. Bạn muốn giữ lại những thông tin quan trọng nhất đối với con người, đồng thời loại bỏ bất cứ thứ gì không cải thiện trải nghiệm thị giác hoặc thính giác. Do đó, cả khoa học máy tính và nghiên cứu về nhận thức của con người đều đóng vai trò quan trọng. Trong phần này, chúng ta sẽ xem xét một số nỗ lực lớn trong việc biểu diễn và nén dữ liệu đa phương tiện.

Tất nhiên, việc sử dụng nén không chỉ giới hạn ở dữ liệu đa phương tiện—ví dụ, bạn có thể đã từng dùng tiện ích như ``zip`` hoặc ``compress`` để nén tệp trước khi gửi qua mạng, hoặc để giải nén tệp dữ liệu sau khi tải về. Hóa ra các kỹ thuật dùng để nén dữ liệu—thường là *không mất dữ liệu* (lossless), vì hầu hết mọi người không muốn mất dữ liệu trong tệp—cũng xuất hiện như một phần của giải pháp cho nén đa phương tiện. Ngược lại, *nén mất dữ liệu* (lossy compression), thường dùng cho dữ liệu đa phương tiện, không đảm bảo rằng dữ liệu nhận được sẽ giống hệt dữ liệu đã gửi. Như đã đề cập ở trên, điều này là do dữ liệu đa phương tiện thường chứa thông tin ít hữu ích đối với người nhận. Các giác quan và não bộ của chúng ta chỉ có thể cảm nhận được một mức độ chi tiết nhất định. Chúng cũng rất giỏi trong việc bù đắp các phần bị thiếu và thậm chí sửa một số lỗi trong những gì chúng ta nhìn hoặc nghe thấy. Ngoài ra, các thuật toán nén mất dữ liệu thường đạt được tỷ lệ nén tốt hơn nhiều so với các thuật toán không mất dữ liệu; chúng có thể tốt hơn một bậc độ lớn hoặc hơn nữa.

Để cảm nhận tầm quan trọng của nén đối với sự phát triển của đa phương tiện mạng, hãy xem xét ví dụ sau. Một màn hình TV độ nét cao có khoảng 1080 × 1920 điểm ảnh, mỗi điểm ảnh có 24 bit thông tin màu, vậy mỗi khung hình là

.. centered:: 1080 × 1920 × 24 = 50 *Mb*

nên nếu bạn muốn gửi 24 khung hình mỗi giây, thì sẽ vượt quá 1 Gbps. Đó là nhiều hơn mức mà hầu hết người dùng Internet có thể truy cập. Ngược lại, các kỹ thuật nén hiện đại có thể giảm một tín hiệu HDTV chất lượng cao xuống khoảng 10 Mbps, tức là giảm hai bậc độ lớn và hoàn toàn nằm trong khả năng của hầu hết người dùng băng thông rộng. Các mức nén tương tự cũng áp dụng cho video chất lượng thấp hơn như các clip YouTube—video trên web sẽ không bao giờ đạt được mức phổ biến hiện nay nếu không có nén để các video giải trí đó phù hợp với băng thông của các mạng hiện đại.

Các kỹ thuật nén áp dụng cho đa phương tiện là một lĩnh vực đổi mới lớn, đặc biệt là nén mất dữ liệu. Tuy nhiên, các kỹ thuật không mất dữ liệu cũng đóng vai trò quan trọng. Thực tế, hầu hết các kỹ thuật nén mất dữ liệu đều bao gồm một số bước không mất dữ liệu, vì vậy chúng ta sẽ bắt đầu bằng tổng quan về nén không mất dữ liệu.

7.2.1 Kỹ thuật nén không mất dữ liệu
------------------------------------

Ở nhiều khía cạnh, nén không thể tách rời với mã hóa dữ liệu. Khi nghĩ về cách mã hóa một phần dữ liệu thành một tập hợp các bit, chúng ta cũng có thể nghĩ về cách mã hóa dữ liệu bằng tập hợp bit nhỏ nhất có thể. Ví dụ, nếu bạn có một khối dữ liệu gồm 26 ký hiệu từ A đến Z, và nếu tất cả các ký hiệu này có xác suất xuất hiện như nhau trong khối dữ liệu bạn đang mã hóa, thì mã hóa mỗi ký hiệu bằng 5 bit là tốt nhất bạn có thể làm (vì 2\ :sup:`5` = 32 là lũy thừa nhỏ nhất của 2 lớn hơn 26). Tuy nhiên, nếu ký hiệu R xuất hiện 50% thời gian, thì sẽ hợp lý nếu dùng ít bit hơn để mã hóa R so với các ký hiệu khác. Nói chung, nếu bạn biết xác suất tương đối của mỗi ký hiệu xuất hiện trong dữ liệu, bạn có thể gán số bit khác nhau cho mỗi ký hiệu sao cho tổng số bit dùng để mã hóa một khối dữ liệu là nhỏ nhất. Đây là ý tưởng cốt lõi của *mã Huffman* (Huffman codes), một trong những phát triển quan trọng đầu tiên trong nén dữ liệu.

Mã hóa theo độ dài chuỗi (Run Length Encoding)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Mã hóa theo độ dài chuỗi (RLE) là một kỹ thuật nén với sự đơn giản thô sơ. Ý tưởng là thay thế các ký hiệu xuất hiện liên tiếp bằng chỉ một bản sao của ký hiệu đó, cộng với số lần ký hiệu xuất hiện—do đó có tên gọi *độ dài chuỗi*. Ví dụ, chuỗi ``AAABBCDDDD`` sẽ được mã hóa thành ``3A2B1C4D``.

RLE tỏ ra hữu ích khi nén một số loại hình ảnh nhất định. Nó có thể được sử dụng trong bối cảnh này bằng cách so sánh các giá trị điểm ảnh liền kề rồi chỉ mã hóa các thay đổi. Đối với các hình ảnh có vùng đồng nhất lớn, kỹ thuật này khá hiệu quả. Ví dụ, không hiếm khi RLE đạt tỷ lệ nén khoảng 8:1 cho hình ảnh văn bản quét. RLE hoạt động tốt với các tệp như vậy vì chúng thường chứa nhiều khoảng trắng có thể loại bỏ. Đối với những ai còn nhớ công nghệ cũ, RLE là thuật toán nén chủ chốt dùng để truyền fax. Tuy nhiên, với hình ảnh có thậm chí một chút biến thiên cục bộ, không hiếm khi nén thực tế lại làm tăng kích thước tệp, vì cần 2 byte để biểu diễn một ký hiệu khi ký hiệu đó không lặp lại.

Mã hóa vi sai (Differential Pulse Code Modulation)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một thuật toán nén không mất dữ liệu đơn giản khác là Mã hóa vi sai (DPCM). Ý tưởng ở đây là đầu tiên xuất ra một ký hiệu tham chiếu, sau đó, với mỗi ký hiệu trong dữ liệu, xuất ra hiệu số giữa ký hiệu đó và ký hiệu tham chiếu. Ví dụ, dùng ký hiệu A làm tham chiếu, chuỗi ``AAABBCDDDD`` sẽ được mã hóa thành ``A0001123333`` vì A giống ký hiệu tham chiếu, B lệch 1 so với ký hiệu tham chiếu, v.v. Lưu ý rằng ví dụ đơn giản này không minh họa lợi ích thực sự của DPCM, đó là khi các hiệu số nhỏ thì có thể mã hóa bằng ít bit hơn ký hiệu gốc. Trong ví dụ này, dải hiệu số 0-3 có thể biểu diễn bằng 2 bit mỗi ký hiệu, thay vì 7 hoặc 8 bit cho ký tự đầy đủ. Khi hiệu số quá lớn, sẽ chọn ký hiệu tham chiếu mới.

DPCM hoạt động tốt hơn RLE với hầu hết hình ảnh số, vì nó tận dụng thực tế là các điểm ảnh liền kề thường giống nhau. Nhờ sự tương quan này, dải động của hiệu số giữa các giá trị điểm ảnh liền kề có thể nhỏ hơn đáng kể so với dải động của hình ảnh gốc, và do đó có thể biểu diễn bằng ít bit hơn. Với DPCM, chúng tôi đo được tỷ lệ nén 1.5:1 trên hình ảnh số. DPCM cũng hoạt động với âm thanh, vì các mẫu liền kề của sóng âm thường có giá trị gần nhau.

Một cách tiếp cận hơi khác, gọi là *mã hóa delta* (delta encoding), chỉ đơn giản mã hóa một ký hiệu là hiệu số so với ký hiệu trước đó. Ví dụ, ``AAABBCDDDD`` sẽ được biểu diễn thành ``A001011000``. Lưu ý rằng mã hóa delta có thể hoạt động tốt khi mã hóa hình ảnh mà các điểm ảnh liền kề giống nhau. Cũng có thể thực hiện RLE sau mã hóa delta, vì có thể xuất hiện các chuỗi dài số 0 nếu có nhiều ký hiệu giống nhau liền kề.

Phương pháp dựa trên từ điển (Dictionary-Based Methods)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Phương pháp nén không mất dữ liệu cuối cùng mà chúng ta xét đến là phương pháp dựa trên từ điển, trong đó thuật toán nén Lempel-Ziv (LZ) là nổi tiếng nhất. Các lệnh ``compress`` và ``gzip`` trên Unix sử dụng các biến thể của thuật toán LZ.

Ý tưởng của thuật toán nén dựa trên từ điển là xây dựng một từ điển (bảng) các chuỗi có độ dài biến đổi (có thể coi như các cụm từ phổ biến) mà bạn kỳ vọng sẽ xuất hiện trong dữ liệu, sau đó thay thế mỗi chuỗi này khi nó xuất hiện trong dữ liệu bằng chỉ số tương ứng trong từ điển. Ví dụ, thay vì làm việc với từng ký tự riêng lẻ trong dữ liệu văn bản, bạn có thể coi mỗi từ là một chuỗi và xuất ra chỉ số trong từ điển cho từ đó. Để minh họa thêm, từ *compression* có chỉ số 4978 trong một từ điển cụ thể; nó là từ thứ 4978 trong ``/usr/share/dict/words``. Để nén một đoạn văn bản, mỗi lần chuỗi “compression” xuất hiện sẽ được thay bằng 4978. Vì từ điển này có hơn 25.000 từ, cần 15 bit để mã hóa chỉ số, nghĩa là từ “compression” có thể biểu diễn bằng 15 bit thay vì 77 bit nếu dùng ASCII 7 bit. Đây là tỷ lệ nén 5:1! Ở một ví dụ khác, chúng tôi đạt tỷ lệ nén 2:1 khi áp dụng lệnh ``compress`` cho mã nguồn các giao thức mô tả trong cuốn sách này.

Tất nhiên, câu hỏi đặt ra là từ điển lấy ở đâu. Một lựa chọn là định nghĩa một từ điển tĩnh, tốt nhất là phù hợp với dữ liệu cần nén. Một giải pháp tổng quát hơn, và là cách mà LZ sử dụng, là định nghĩa từ điển một cách thích nghi dựa trên nội dung dữ liệu cần nén. Tuy nhiên, trong trường hợp này, từ điển xây dựng trong quá trình nén phải được gửi kèm với dữ liệu để thuật toán giải nén có thể hoạt động. Cách xây dựng từ điển thích nghi như thế nào là chủ đề của nhiều nghiên cứu chuyên sâu.

7.2.2 Biểu diễn và nén hình ảnh (GIF, JPEG)
--------------------------------------------

Với việc sử dụng hình ảnh số phổ biến—việc này xuất phát từ sự ra đời của màn hình đồ họa chứ không phải mạng tốc độ cao—nhu cầu về các định dạng biểu diễn chuẩn và thuật toán nén cho dữ liệu hình ảnh số trở nên thiết yếu. Đáp ứng nhu cầu này, ISO đã định nghĩa một định dạng hình ảnh số gọi là *JPEG*, đặt theo tên Nhóm Chuyên gia Ảnh (Joint Photographic Experts Group) đã thiết kế nó. (Chữ “Joint” trong JPEG là sự hợp tác giữa ISO và ITU.) JPEG là định dạng phổ biến nhất cho hình ảnh tĩnh hiện nay. Trọng tâm của định nghĩa định dạng này là một thuật toán nén, sẽ được mô tả dưới đây. Nhiều kỹ thuật dùng trong JPEG cũng xuất hiện trong MPEG, bộ tiêu chuẩn cho nén và truyền video do Nhóm Chuyên gia Hình ảnh Động (Moving Picture Experts Group) xây dựng.

Trước khi đi vào chi tiết JPEG, chúng ta nhận thấy có khá nhiều bước để chuyển từ một hình ảnh số sang một biểu diễn nén có thể truyền, giải nén và hiển thị đúng ở phía nhận. Bạn có thể biết rằng hình ảnh số được tạo thành từ các điểm ảnh (pixel)—do đó có khái niệm megapixel trong quảng cáo máy ảnh điện thoại. Mỗi điểm ảnh đại diện cho một vị trí trong lưới hai chiều tạo nên hình ảnh, và với hình ảnh màu, mỗi điểm ảnh có một giá trị số biểu diễn màu sắc. Có nhiều cách để biểu diễn màu, gọi là *không gian màu* (color spaces); cách quen thuộc nhất là RGB (đỏ, xanh lá, xanh dương). Bạn có thể coi màu sắc là một đại lượng ba chiều—có thể tạo ra bất kỳ màu nào từ các lượng khác nhau của ba màu cơ bản. Trong không gian ba chiều, có nhiều cách hợp lệ để mô tả một điểm (ví dụ, tọa độ Đề-các và tọa độ cực). Tương tự, có nhiều cách để mô tả màu bằng ba đại lượng, và cách thay thế phổ biến nhất cho RGB là YUV. Y là độ sáng (luminance), xấp xỉ độ sáng tổng thể của điểm ảnh, còn U và V chứa thông tin màu (chrominance). Thực tế, có một số biến thể khác nhau của không gian màu YUV. Sẽ nói thêm về điều này ở phần sau.

Ý nghĩa của thảo luận này là việc mã hóa và truyền hình ảnh màu (tĩnh hoặc động) đòi hỏi hai đầu phải thống nhất về không gian màu. Nếu không, phía nhận có thể hiển thị màu khác với màu mà phía gửi ghi nhận. Do đó, việc thống nhất định nghĩa không gian màu (và có thể là cách truyền đạt không gian màu nào đang dùng) là một phần của định nghĩa bất kỳ định dạng hình ảnh hoặc video nào.

Hãy xem ví dụ về Định dạng Trao đổi Đồ họa (GIF). GIF sử dụng không gian màu RGB và bắt đầu với 8 bit cho mỗi trong ba thành phần màu, tổng cộng 24 bit. Tuy nhiên, thay vì gửi 24 bit cho mỗi điểm ảnh, GIF trước tiên giảm hình ảnh màu 24 bit xuống hình ảnh màu 8 bit. Điều này thực hiện bằng cách xác định các màu được sử dụng trong bức ảnh, thường sẽ ít hơn nhiều so với 2\ :sup:`24`, rồi chọn ra 256 màu gần nhất với các màu thực tế trong ảnh. Có thể có nhiều hơn 256 màu, nên thủ thuật là cố gắng không làm biến dạng màu quá nhiều bằng cách chọn 256 màu sao cho không điểm ảnh nào bị thay đổi màu quá lớn.

256 màu này được lưu trong một bảng, có thể đánh chỉ số bằng số 8 bit, và giá trị của mỗi điểm ảnh được thay bằng chỉ số phù hợp. Lưu ý rằng đây là ví dụ về nén mất dữ liệu đối với bất kỳ ảnh nào có hơn 256 màu. Sau đó, GIF chạy một biến thể của LZ trên kết quả, coi các chuỗi điểm ảnh phổ biến là các chuỗi tạo nên từ điển—một thao tác không mất dữ liệu. Với cách tiếp cận này, GIF đôi khi đạt tỷ lệ nén khoảng 10:1, nhưng chỉ khi hình ảnh có số lượng màu rời rạc tương đối nhỏ. Logo đồ họa, chẳng hạn, được GIF xử lý tốt. Hình ảnh cảnh vật tự nhiên, thường có phổ màu liên tục hơn, không thể nén ở tỷ lệ này bằng GIF. Ngoài ra, mắt người cũng dễ nhận ra biến dạng do giảm màu mất dữ liệu của GIF trong một số trường hợp.

Định dạng JPEG phù hợp hơn nhiều với hình ảnh chụp thực tế, như bạn mong đợi từ tên của nhóm tạo ra nó. JPEG không giảm số lượng màu như GIF. Thay vào đó, JPEG bắt đầu bằng cách chuyển đổi màu RGB (thường là đầu ra của máy ảnh số) sang không gian YUV. Lý do là do cách mắt người cảm nhận hình ảnh. Mắt có các thụ thể cho độ sáng và các thụ thể riêng cho màu sắc. Vì chúng ta rất nhạy với biến thiên độ sáng, nên hợp lý khi dành nhiều bit hơn để truyền thông tin độ sáng. Vì thành phần Y của YUV là độ sáng của điểm ảnh, chúng ta có thể nén thành phần này riêng biệt, và ít mạnh tay hơn so với hai thành phần màu (chrominance) còn lại.

Như đã đề cập, YUV và RGB là hai cách thay thế để mô tả một điểm trong không gian ba chiều, và có thể chuyển đổi qua lại bằng các phương trình tuyến tính. Với một không gian YUV thường dùng cho hình ảnh số, các phương trình là:

::

   Y = 0.299R + 0.587G + 0.114B
   U = (B-Y) x 0.565
   V =  (R-Y) x 0.713

Giá trị chính xác của các hằng số này không quan trọng, miễn là bộ mã hóa và giải mã thống nhất với nhau. (Bộ giải mã sẽ phải áp dụng phép biến đổi ngược để thu lại các thành phần RGB cần thiết cho hiển thị.) Tuy nhiên, các hằng số này được chọn cẩn thận dựa trên nhận thức màu của con người. Bạn có thể thấy Y, độ sáng, là tổng của các thành phần đỏ, xanh lá và xanh dương, còn U và V là các thành phần chênh lệch màu. U là hiệu giữa độ sáng và xanh dương, V là hiệu giữa độ sáng và đỏ. Bạn cũng có thể nhận thấy rằng đặt R, G, B ở giá trị tối đa (255 với biểu diễn 8 bit) cũng cho giá trị Y=255, còn U và V bằng 0. Tức là, một điểm ảnh trắng hoàn toàn là (255,255,255) trong RGB và (255,0,0) trong YUV.

.. _fig-yuvsub:
.. figure:: figures/f07-11-9780123850591.png
   :width: 500px
   :align: center

   Lấy mẫu thưa các thành phần U và V của một hình ảnh.

Sau khi chuyển đổi hình ảnh sang không gian YUV, chúng ta có thể nghĩ đến việc nén riêng từng thành phần. Ta muốn nén mạnh tay hơn với các thành phần U và V, mà mắt người ít nhạy cảm hơn. Một cách để nén U và V là *lấy mẫu thưa* (subsample) chúng. Ý tưởng cơ bản của lấy mẫu thưa là lấy một số điểm ảnh liền kề, tính giá trị trung bình U hoặc V cho nhóm đó, rồi truyền giá trị này thay vì truyền giá trị cho từng điểm ảnh. :numref:`Hình %s <fig-yuvsub>` minh họa điều này. Thành phần độ sáng (Y) không được lấy mẫu thưa, nên giá trị Y của tất cả điểm ảnh sẽ được truyền, như lưới 16 × 16 điểm ảnh bên trái. Với U và V, mỗi nhóm bốn điểm ảnh liền kề được coi là một nhóm, tính trung bình U hoặc V cho nhóm đó và truyền đi. Kết quả là ta có lưới 8 × 8 giá trị U và V để truyền. Như vậy, trong ví dụ này, với mỗi bốn điểm ảnh, ta truyền sáu giá trị (bốn Y và một U, một V) thay vì 12 giá trị ban đầu (bốn cho mỗi thành phần), giảm 50% thông tin.

Lưu ý rằng bạn có thể lấy mẫu thưa mạnh tay hơn hoặc nhẹ hơn, với mức nén tăng lên và chất lượng giảm đi tương ứng. Cách lấy mẫu thưa trình bày ở đây, trong đó chrominance được lấy mẫu thưa theo cả chiều ngang và dọc với hệ số hai (ký hiệu 4:2:0), là cách phổ biến nhất dùng cho cả JPEG và MPEG.

.. _fig-jpeg:
.. figure:: figures/f07-12-9780123850591.png
   :width: 550px
   :align: center

   Sơ đồ khối nén JPEG.

Sau khi lấy mẫu thưa, ta có ba lưới điểm ảnh để xử lý, mỗi lưới được xử lý riêng biệt. Nén JPEG cho mỗi thành phần diễn ra qua ba pha, như minh họa ở :numref:`Hình %s <fig-jpeg>`. Ở phía nén, hình ảnh được đưa qua ba pha này từng khối 8 × 8 điểm ảnh một. Pha đầu tiên áp dụng biến đổi cosin rời rạc (DCT) cho khối. Nếu coi hình ảnh là một tín hiệu trong miền không gian, thì DCT biến đổi tín hiệu này thành tín hiệu tương đương trong miền *tần số không gian*. Đây là thao tác không mất dữ liệu nhưng là bước tiền đề cần thiết cho bước tiếp theo, là bước mất dữ liệu. Sau DCT, pha thứ hai áp dụng lượng tử hóa cho tín hiệu kết quả và loại bỏ các thông tin ít quan trọng nhất. Pha thứ ba mã hóa kết quả cuối cùng, đồng thời bổ sung nén không mất dữ liệu vào nén mất dữ liệu đã đạt được ở hai pha đầu. Quá trình giải nén đi theo ba pha này nhưng theo thứ tự ngược lại.

Pha DCT
~~~~~~~

DCT là một phép biến đổi liên quan chặt chẽ với biến đổi Fourier nhanh (FFT). Nó nhận vào một ma trận 8 × 8 giá trị điểm ảnh và xuất ra một ma trận 8 × 8 hệ số tần số. Bạn có thể coi ma trận đầu vào là một tín hiệu 64 điểm xác định trong hai chiều không gian (*x* và *y*); DCT phân tích tín hiệu này thành 64 tần số không gian. Để hình dung trực quan về tần số không gian, hãy tưởng tượng bạn di chuyển qua một bức ảnh theo hướng *x*. Bạn sẽ thấy giá trị mỗi điểm ảnh thay đổi theo một hàm nào đó của *x*. Nếu giá trị này thay đổi chậm khi *x* tăng, thì đó là tần số không gian thấp; nếu thay đổi nhanh, là tần số cao. Vậy các tần số thấp tương ứng với các đặc trưng tổng thể của ảnh, còn tần số cao tương ứng với chi tiết nhỏ. Ý tưởng của DCT là tách biệt các đặc trưng tổng thể, vốn thiết yếu cho việc xem ảnh, khỏi các chi tiết nhỏ, vốn ít thiết yếu hơn và đôi khi mắt người khó nhận ra.

DCT và phép biến đổi ngược của nó, dùng để thu lại điểm ảnh gốc khi giải nén, được định nghĩa bởi các công thức sau:

.. math::

   \begin{aligned}
   DCT(i,j) &=&  \frac{1}{\sqrt{2N}} C(i) C(j) \sum_{x=0}^{N-1}
    \sum_{y=0}^{N-1} pixel(x, y)
    \cos \left[ \frac{(2x+1)i \pi}{2N}\right]
    \cos \left[ \frac{(2y+1)j \pi}{2N}\right]\\
   \mathit{pixel}(x,y) &=&  \frac{1}{\sqrt{2N}} \sum_{i=0}^{N-1}
    \sum_{j=0}^{N-1} C(i) C(j) DCT(i, j)
    \cos \left[ \frac{(2x+1)i \pi}{2N}\right]
    \cos \left[ \frac{(2y+1)j \pi}{2N}\right]
   \end{aligned}

trong đó :math:`C(x) = 1/\sqrt{2}` khi :math:`x=0` và :math:`1` khi
:math:`x>0`, và :math:`pixel(x,y)` là giá trị xám của điểm ảnh tại vị trí *(x,y)* trong khối 8 × 8 đang được nén; N = 8 trong trường hợp này.

Hệ số tần số đầu tiên, tại vị trí (0,0) trong ma trận đầu ra, gọi là *hệ số DC*. Trực giác mà nói, hệ số DC đo giá trị trung bình của 64 điểm ảnh đầu vào. 63 phần tử còn lại của ma trận đầu ra gọi là *hệ số AC*. Chúng bổ sung thông tin tần số không gian cao hơn vào giá trị trung bình này. Như vậy, khi đi từ hệ số đầu tiên đến hệ số thứ 64, bạn đi từ thông tin tần số thấp đến tần số cao, từ các nét lớn của ảnh đến chi tiết nhỏ hơn. Các hệ số tần số cao này ngày càng ít quan trọng đối với chất lượng cảm nhận của ảnh. Chính pha thứ hai của JPEG quyết định phần nào của các hệ số này sẽ bị loại bỏ.

Pha lượng tử hóa (Quantization)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Pha thứ hai của JPEG là nơi nén trở nên mất dữ liệu. DCT tự nó không làm mất thông tin; nó chỉ biến đổi ảnh thành dạng dễ biết phần nào nên loại bỏ. (Dù không mất dữ liệu, nhưng thực tế có thể mất một chút độ chính xác do dùng số học dấu phẩy cố định trong pha DCT.) Lượng tử hóa rất dễ hiểu—chỉ đơn giản là loại bỏ các bit không quan trọng của các hệ số tần số.

Để hiểu pha lượng tử hóa hoạt động thế nào, hãy tưởng tượng bạn muốn nén một số nguyên nhỏ hơn 100, như 45, 98, 23, 66, và 7. Nếu bạn quyết định chỉ cần biết các số này làm tròn đến bội số 10 là đủ, bạn có thể chia mỗi số cho 10 rồi làm tròn xuống, được 4, 9, 2, 6, và 0. Các số này có thể mã hóa bằng 4 bit thay vì 7 bit cho số gốc.

.. _tab-quant:
.. table::  Bảng lượng tử hóa ví dụ của JPEG.
   :widths: auto
   :align: center

   +---------+----+----+----+----+----+----+----+
   | Quantum |    |    |    |    |    |    |    |
   +=========+====+====+====+====+====+====+====+
   | 3       | 5  | 7  | 9  | 11 | 13 | 15 | 17 |
   +---------+----+----+----+----+----+----+----+
   | 5       | 7  | 9  | 11 | 13 | 15 | 17 | 19 |
   +---------+----+----+----+----+----+----+----+
   | 7       | 9  | 11 | 13 | 15 | 17 | 19 | 21 |
   +---------+----+----+----+----+----+----+----+
   | 9       | 11 | 13 | 15 | 17 | 19 | 21 | 23 |
   +---------+----+----+----+----+----+----+----+
   | 11      | 13 | 15 | 17 | 19 | 21 | 23 | 25 |
   +---------+----+----+----+----+----+----+----+
   | 13      | 15 | 17 | 19 | 21 | 23 | 25 | 27 |
   +---------+----+----+----+----+----+----+----+
   | 15      | 17 | 19 | 21 | 23 | 25 | 27 | 29 |
   +---------+----+----+----+----+----+----+----+
   | 17      | 19 | 21 | 23 | 25 | 27 | 29 | 31 |
   +---------+----+----+----+----+----+----+----+

Thay vì dùng cùng một lượng tử cho tất cả 64 hệ số, JPEG dùng một bảng lượng tử hóa cho biết lượng tử dùng cho từng hệ số, như công thức dưới đây. Bạn có thể coi bảng này (``Quantum``) là một tham số có thể điều chỉnh để kiểm soát mức độ thông tin bị mất và mức độ nén đạt được. Trong thực tế, chuẩn JPEG quy định một tập các bảng lượng tử hóa đã chứng minh hiệu quả trong nén hình ảnh số; một ví dụ được cho ở :numref:`Bảng %s <tab-quant>`. Trong các bảng như vậy, các hệ số thấp có lượng tử gần 1 (nghĩa là ít thông tin tần số thấp bị mất), còn các hệ số cao có giá trị lớn hơn (nghĩa là nhiều thông tin tần số cao bị mất hơn). Lưu ý rằng do các bảng lượng tử hóa như vậy, nhiều hệ số tần số cao sẽ bị đặt thành 0 sau lượng tử hóa, tạo điều kiện cho nén tiếp ở pha thứ ba.

Phương trình lượng tử hóa cơ bản là

::

   QuantizedValue(i,j) = IntegerRound(DCT(i,j)/Quantum(i,j))

trong đó

::

   IntegerRound(x) =
       Floor(x + 0.5) if x >= 0
       Floor(x - 0.5) if x < 0

Giải nén đơn giản là

::

   DCT(i,j) = QuantizedValue(i,j) x Quantum(i,j)

Ví dụ, nếu hệ số DC (tức là DCT(0,0)) cho một khối là 25, thì lượng tử hóa giá trị này dùng :numref:`Bảng %s <tab-quant>` sẽ cho

::

   Floor(25/3+0.5) = 8

Khi giải nén, hệ số này sẽ được khôi phục thành 8 × 3 = 24.

Pha mã hóa (Encoding)
~~~~~~~~~~~~~~~~~~~~~

Pha cuối cùng của JPEG là mã hóa các hệ số tần số đã lượng tử hóa thành dạng gọn. Điều này mang lại nén bổ sung, nhưng là nén không mất dữ liệu. Bắt đầu từ hệ số DC ở vị trí (0,0), các hệ số được xử lý theo trình tự zigzag như ở :numref:`Hình %s <fig-zigzag>`. Theo trình tự zigzag này, một dạng mã hóa theo độ dài chuỗi được áp dụng—RLE chỉ áp dụng cho các hệ số 0, điều này quan trọng vì nhiều hệ số về sau là 0. Giá trị từng hệ số sau đó được mã hóa bằng mã Huffman. (Chuẩn JPEG cho phép dùng mã hóa số học thay cho mã Huffman.)

.. _fig-zigzag:
.. figure:: figures/f07-13-9780123850591.png
   :width: 300px
   :align: center

   Trình tự zigzag của các hệ số tần số đã lượng tử hóa.

Ngoài ra, vì hệ số DC chứa phần lớn thông tin về khối 8 × 8 điểm ảnh từ ảnh gốc, và hình ảnh thường thay đổi chậm từ khối này sang khối khác, mỗi hệ số DC được mã hóa là hiệu số so với hệ số DC trước đó. Đây là cách mã hóa delta đã mô tả ở phần trước.

JPEG có nhiều biến thể kiểm soát mức nén đạt được so với độ trung thực của ảnh. Điều này có thể thực hiện, ví dụ, bằng cách dùng các bảng lượng tử hóa khác nhau. Các biến thể này, cộng với việc mỗi ảnh có đặc trưng khác nhau, khiến không thể nói chính xác tỷ lệ nén đạt được với JPEG. Tỷ lệ 30:1 là phổ biến, và tỷ lệ cao hơn chắc chắn có thể, nhưng *hiện tượng giả* (artifacts—biến dạng nhận thấy do nén) sẽ nghiêm trọng hơn ở tỷ lệ cao.

7.2.3 Nén video (MPEG)
----------------------

Bây giờ chúng ta chuyển sang định dạng MPEG, đặt theo tên Nhóm Chuyên gia Hình ảnh Động (Moving Picture Experts Group) đã định nghĩa nó. Ở mức gần đúng đầu tiên, một hình ảnh động (video) chỉ là một chuỗi các hình ảnh tĩnh—còn gọi là *khung hình* (frame) hoặc *ảnh* (picture)—được hiển thị ở một tốc độ video nào đó. Mỗi khung hình này có thể được nén bằng kỹ thuật dựa trên DCT giống như JPEG. Tuy nhiên, dừng lại ở đây sẽ là sai lầm, vì nó không loại bỏ được sự dư thừa giữa các khung hình trong chuỗi video. Ví dụ, hai khung hình liên tiếp sẽ chứa thông tin gần như giống hệt nhau nếu cảnh không có nhiều chuyển động, nên không cần thiết phải gửi cùng một thông tin hai lần. Ngay cả khi có chuyển động, vẫn có thể có nhiều dư thừa vì một vật thể chuyển động có thể không thay đổi từ khung này sang khung khác; đôi khi chỉ vị trí của nó thay đổi. MPEG tính đến sự dư thừa giữa các khung hình này. MPEG cũng định nghĩa cơ chế mã hóa tín hiệu âm thanh cùng với video, nhưng ở đây chúng ta chỉ xét khía cạnh video của MPEG.

Các loại khung hình
~~~~~~~~~~~~~~~~~~

MPEG nhận vào một chuỗi khung hình video và nén chúng thành ba loại khung hình, gọi là *khung I* (intrapicture), *khung P* (predicted picture), và *khung B* (bidirectional predicted picture). Mỗi khung hình đầu vào được nén thành một trong ba loại này. Khung I có thể coi là khung tham chiếu; chúng độc lập, không phụ thuộc vào khung trước hay sau. Gần đúng, một khung I chỉ là phiên bản nén JPEG của khung tương ứng trong nguồn video. Khung P và B không độc lập; chúng chỉ ra sự khác biệt tương đối so với một khung tham chiếu. Cụ thể, khung P chỉ ra sự khác biệt so với khung I trước đó, còn khung B là phép nội suy giữa khung I hoặc P trước và sau.

.. _fig-mpeg:
.. figure:: figures/f07-14-9780123850591.png
   :width: 500px
   :align: center

   Chuỗi các khung I, P, B do MPEG sinh ra.

:numref:`Hình %s <fig-mpeg>` minh họa một chuỗi bảy khung hình video mà sau khi nén bằng MPEG sẽ thành chuỗi các khung I, P, B. Hai khung I độc lập; mỗi khung có thể được giải nén ở phía nhận mà không cần khung nào khác. Khung P phụ thuộc vào khung I trước đó; nó chỉ có thể được giải nén nếu khung I trước đó cũng đến nơi. Mỗi khung B phụ thuộc vào cả khung I hoặc P trước và sau. Cả hai khung tham chiếu này phải đến nơi trước khi MPEG có thể giải nén khung B để tái tạo khung hình gốc.

Lưu ý rằng vì mỗi khung B phụ thuộc vào một khung sau trong chuỗi, các khung nén không được truyền theo thứ tự tuần tự. Thay vào đó, chuỗi I B B P B B I trong :numref:`Hình %s <fig-mpeg>` được truyền thành I P B B I B B. Ngoài ra, MPEG không quy định tỷ lệ giữa khung I với khung P và B; tỷ lệ này có thể thay đổi tùy theo yêu cầu nén và chất lượng hình ảnh. Ví dụ, có thể chỉ truyền các khung I. Điều này tương tự như dùng JPEG để nén video.

Khác với phần mô tả JPEG ở trên, phần sau tập trung vào *giải mã* một luồng MPEG. Việc này dễ mô tả hơn, và là thao tác thường được triển khai trong các hệ thống mạng ngày nay, vì mã hóa MPEG tốn nhiều tài nguyên nên thường được thực hiện ngoại tuyến (không theo thời gian thực). Ví dụ, trong hệ thống video theo yêu cầu, video sẽ được mã hóa và lưu trên đĩa trước. Khi người xem muốn xem, luồng MPEG sẽ được truyền đến máy người xem, nơi sẽ giải mã và hiển thị luồng theo thời gian thực.

Hãy xem kỹ hơn ba loại khung hình. Như đã nói, khung I gần như là phiên bản nén JPEG của khung nguồn. Khác biệt chính là MPEG làm việc với đơn vị macroblock 16 × 16. Với video màu biểu diễn theo YUV, các thành phần U và V trong mỗi macroblock được lấy mẫu thưa thành khối 8 × 8, như đã bàn ở phần JPEG. Mỗi khối con 2 × 2 trong macroblock được cho bởi một giá trị U và một giá trị V—trung bình của bốn điểm ảnh. Khối con vẫn có bốn giá trị Y. Mối quan hệ giữa một khung hình và các macroblock tương ứng được cho ở :numref:`Hình %s <fig-macroblock>`.

.. _fig-macroblock:
.. figure:: figures/f07-15-9780123850591.png
   :width: 500px
   :align: center

   Mỗi khung hình là tập hợp các macroblock.

Khung P và B cũng được xử lý theo đơn vị macroblock. Trực giác mà nói, thông tin chúng mang cho mỗi macroblock thể hiện chuyển động trong video; tức là, cho biết hướng và khoảng cách macroblock di chuyển so với khung tham chiếu. Sau đây mô tả cách một khung B được dùng để tái tạo khung hình khi giải nén; khung P được xử lý tương tự, chỉ khác là phụ thuộc vào một khung tham chiếu thay vì hai.

Trước khi đi vào chi tiết cách giải nén khung B, lưu ý rằng mỗi macroblock trong khung B không nhất thiết phải xác định so với cả khung trước và sau như đã nói, mà có thể chỉ xác định so với một trong hai. Thực tế, một macroblock trong khung B có thể dùng mã hóa nội giống như khung I. Sự linh hoạt này tồn tại vì nếu hình ảnh chuyển động quá nhanh thì đôi khi nên dùng mã hóa nội thay vì mã hóa dự đoán tiến hoặc lùi. Do đó, mỗi macroblock trong khung B có một trường kiểu chỉ ra mã hóa nào được dùng cho macroblock đó. Tuy nhiên, trong phần sau, chúng ta chỉ xét trường hợp tổng quát là macroblock dùng mã hóa dự đoán hai chiều.

Trong trường hợp này, mỗi macroblock trong khung B được biểu diễn bằng một bộ 4 thành phần: (1) tọa độ macroblock trong khung hình, (2) vector chuyển động so với khung tham chiếu trước, (3) vector chuyển động so với khung tham chiếu sau, và (4) delta (:math:`\delta`) cho mỗi điểm ảnh trong macroblock (tức là mức thay đổi của mỗi điểm ảnh so với hai điểm ảnh tham chiếu). Với mỗi điểm ảnh trong macroblock, nhiệm vụ đầu tiên là tìm điểm ảnh tham chiếu tương ứng trong hai khung tham chiếu trước và sau. Việc này thực hiện bằng hai vector chuyển động gắn với macroblock. Sau đó, delta cho điểm ảnh được cộng vào trung bình của hai điểm ảnh tham chiếu này. Cụ thể, nếu F\ :sub:`p` và F\ :sub:`f` là hai khung tham chiếu trước và sau, các vector chuyển động là (x\ :sub:`p`, y\ :sub:`p`) và (x\ :sub:`f`, y\ :sub:`f`), thì điểm ảnh tại *(x,y)* trong khung hiện tại (F\ :sub:`c`) được tính là

.. math::

   F_c(x,y) = (F_p(x+x_p,y+y_p) + F_f(x+x_f,y+y_f))/2 + \delta(x,y)

trong đó :math:`\delta` là delta cho điểm ảnh như xác định trong khung B. Các delta này được mã hóa giống như điểm ảnh trong khung I; tức là, qua DCT rồi lượng tử hóa. Vì các delta thường nhỏ, hầu hết hệ số DCT sẽ là 0 sau lượng tử hóa; do đó, có thể nén hiệu quả.

Từ phần trên, có thể thấy cách mã hóa sẽ được thực hiện, ngoại trừ một điểm. Khi tạo khung B hoặc P trong quá trình nén, MPEG phải quyết định đặt macroblock ở đâu. Nhớ rằng mỗi macroblock trong khung P, chẳng hạn, được xác định so với một macroblock trong khung I, nhưng macroblock trong khung P không nhất thiết phải ở cùng vị trí với macroblock tương ứng trong khung I—sự khác biệt vị trí được cho bởi vector chuyển động. Bạn muốn chọn vector chuyển động sao cho macroblock trong khung P giống nhất với macroblock tương ứng trong khung I, để các delta cho macroblock đó nhỏ nhất có thể. Điều này nghĩa là bạn phải xác định vật thể trong ảnh đã di chuyển từ đâu sang đâu giữa hai khung hình. Đây là bài toán *ước lượng chuyển động* (motion estimation), và có nhiều kỹ thuật (heuristic) để giải quyết. (Chúng tôi bàn về các bài báo nghiên cứu vấn đề này ở cuối chương.) Độ khó của bài toán này là một trong những lý do khiến mã hóa MPEG tốn thời gian hơn giải mã trên cùng phần cứng. MPEG không quy định kỹ thuật cụ thể nào; nó chỉ định dạng mã hóa thông tin này trong các khung B và P và thuật toán tái tạo điểm ảnh khi giải nén như trên.

Hiệu quả và hiệu năng
~~~~~~~~~~~~~~~~~~~~

MPEG thường đạt tỷ lệ nén 90:1, dù tỷ lệ 150:1 cũng không hiếm. Xét riêng từng loại khung hình, có thể kỳ vọng tỷ lệ nén khoảng 30:1 cho khung I (phù hợp với tỷ lệ đạt được với JPEG khi màu 24 bit được giảm xuống 8 bit), còn khung P và B thường nén tốt hơn khung I từ ba đến năm lần. Nếu không giảm màu từ 24 bit xuống 8 bit, tỷ lệ nén đạt được với MPEG thường từ 30:1 đến 50:1.

MPEG là một phép tính tốn tài nguyên. Ở phía nén, thường được thực hiện ngoại tuyến, điều này không thành vấn đề khi chuẩn bị phim cho dịch vụ video theo yêu cầu. Video có thể được nén theo thời gian thực bằng phần cứng hiện nay, nhưng các phần mềm cũng đang nhanh chóng thu hẹp khoảng cách. Ở phía giải nén, các card video MPEG giá rẻ có sẵn, nhưng chúng chủ yếu chỉ làm tra cứu màu YUV, vốn là bước tốn tài nguyên nhất. Phần lớn việc giải mã MPEG thực tế được thực hiện bằng phần mềm. Những năm gần đây, bộ xử lý đã đủ nhanh để theo kịp tốc độ 30 khung hình/giây khi giải mã MPEG hoàn toàn bằng phần mềm—thậm chí các bộ xử lý hiện đại còn có thể giải mã MPEG cho video độ nét cao (HDTV).

Chuẩn mã hóa video
~~~~~~~~~~~~~~~~~~

Cuối cùng, cần lưu ý rằng MPEG là một chuẩn đang phát triển với độ phức tạp đáng kể. Độ phức tạp này xuất phát từ mong muốn cho thuật toán mã hóa mọi mức tự do có thể trong việc mã hóa một luồng video, dẫn đến các tốc độ truyền video khác nhau. Nó cũng đến từ sự phát triển của chuẩn qua thời gian, với Nhóm Chuyên gia Hình ảnh Động nỗ lực duy trì khả năng tương thích ngược (ví dụ, MPEG-1, MPEG-2, MPEG-4). Những gì mô tả trong sách này là các ý tưởng cốt lõi của nén dựa trên MPEG, nhưng chắc chắn không phải tất cả các chi tiết phức tạp của một chuẩn quốc tế.

Ngoài ra, MPEG không phải là chuẩn duy nhất cho mã hóa video. Ví dụ, ITU-T cũng định nghĩa *dòng H* cho mã hóa dữ liệu đa phương tiện thời gian thực. Nhìn chung, dòng H bao gồm các chuẩn cho video, âm thanh, điều khiển và ghép kênh (ví dụ, trộn âm thanh, video và dữ liệu vào một luồng bit duy nhất). Trong dòng này, H.261 và H.263 là các chuẩn mã hóa video thế hệ thứ nhất và thứ hai. Về nguyên tắc, cả H.261 và H.263 đều rất giống MPEG: Chúng dùng DCT, lượng tử hóa và nén giữa các khung hình. Khác biệt giữa H.261/H.263 và MPEG nằm ở chi tiết.

Ngày nay, sự hợp tác giữa ITU-T và nhóm MPEG đã dẫn đến chuẩn chung H.264/MPEG-4, được dùng cho cả đĩa Blu-ray và nhiều nguồn phát trực tuyến phổ biến (ví dụ, YouTube, Vimeo).

7.2.4 Truyền MPEG qua mạng
--------------------------

Như đã đề cập, MPEG và JPEG không chỉ là các chuẩn nén mà còn là định nghĩa định dạng của video và hình ảnh. Tập trung vào MPEG, điều đầu tiên cần nhớ là nó định nghĩa định dạng của một *luồng* video; nó không quy định cách chia luồng này thành các gói mạng. Do đó, MPEG có thể dùng cho video lưu trên đĩa, cũng như video truyền qua kết nối mạng hướng luồng, như TCP cung cấp.

Những gì mô tả dưới đây gọi là *main profile* của một luồng video MPEG được gửi qua mạng. Bạn có thể coi một profile MPEG như một “phiên bản”, ngoại trừ profile không được chỉ rõ trong tiêu đề MPEG; phía nhận phải suy ra profile từ tổ hợp các trường tiêu đề nó thấy.

.. _fig-nested:
.. figure:: figures/f07-16-9780123850591.png
   :width: 500px
   :align: center

   Định dạng của một luồng video nén MPEG.

Một luồng MPEG main profile có cấu trúc lồng nhau, như minh họa ở :numref:`Hình %s <fig-nested>`. (Lưu ý rằng hình này ẩn đi rất nhiều chi tiết phức tạp.) Ở mức ngoài cùng, video chứa một chuỗi các nhóm ảnh (GOP) được phân tách bởi ``SeqHdr``. Chuỗi này kết thúc bằng ``SeqEndCode`` (``0xb7``). ``SeqHdr`` đi trước mỗi GOP chỉ định—trong số các thông tin khác—kích thước mỗi ảnh (khung hình) trong GOP (tính bằng điểm ảnh và macroblock), khoảng thời gian giữa các ảnh (tính bằng μs), và hai ma trận lượng tử hóa cho các macroblock trong GOP này: một cho macroblock mã hóa nội (I block) và một cho macroblock mã hóa giữa các khung (B và P block). Vì thông tin này được cung cấp cho từng GOP—thay vì một lần cho toàn bộ luồng video như bạn có thể nghĩ—nên có thể thay đổi bảng lượng tử hóa và tốc độ khung hình tại các ranh giới GOP trong suốt video. Điều này cho phép điều chỉnh luồng video theo thời gian, như sẽ bàn dưới đây.

Mỗi GOP được xác định bởi một ``GOPHdr``, theo sau là tập các ảnh tạo nên GOP. ``GOPHdr`` chỉ số ảnh trong GOP, cũng như thông tin đồng bộ cho GOP (tức là, khi nào GOP nên phát so với đầu video). Mỗi ảnh, đến lượt nó, được xác định bởi một ``PictureHdr`` và một tập các *slice* tạo nên ảnh. (Slice là một vùng của ảnh, như một dòng ngang.) ``PictureHdr`` xác định loại ảnh (I, B, hoặc P) và định nghĩa bảng lượng tử hóa riêng cho ảnh. ``SliceHdr`` cho biết vị trí dọc của slice, cùng một cơ hội nữa để thay đổi bảng lượng tử hóa—lần này bằng một hệ số tỉ lệ thay vì một bảng mới hoàn toàn. Tiếp theo, ``SliceHdr`` được theo sau bởi một chuỗi macroblock. Cuối cùng, mỗi macroblock có một tiêu đề chỉ địa chỉ khối trong ảnh, cùng dữ liệu cho sáu khối trong macroblock: một cho thành phần U, một cho V, và bốn cho Y. (Nhớ rằng thành phần Y là 16 × 16, còn U và V là 8 × 8.)

Có thể thấy một trong những sức mạnh của định dạng MPEG là nó cho bộ mã hóa cơ hội thay đổi mã hóa theo thời gian. Có thể thay đổi tốc độ khung hình, độ phân giải, tỷ lệ các loại khung hình trong GOP, bảng lượng tử hóa, và mã hóa dùng cho từng macroblock. Do đó, có thể điều chỉnh tốc độ truyền video qua mạng bằng cách đánh đổi chất lượng hình ảnh lấy băng thông mạng. Cách một giao thức mạng có thể tận dụng khả năng thích nghi này hiện là chủ đề nghiên cứu (xem khung bên).

Một khía cạnh thú vị khác của việc gửi luồng MPEG qua mạng là cách chia luồng thành các gói. Nếu gửi qua kết nối TCP, việc chia gói không thành vấn đề; TCP quyết định khi nào đủ byte để gửi một datagram IP. Tuy nhiên, khi dùng video tương tác, hiếm khi truyền qua TCP, vì TCP có nhiều đặc điểm không phù hợp với ứng dụng nhạy cảm độ trễ (như thay đổi tốc độ đột ngột sau khi mất gói và truyền lại gói bị mất). Nếu truyền video qua UDP, hợp lý là chia luồng tại các điểm được chọn cẩn thận, như tại ranh giới macroblock. Lý do là muốn giới hạn ảnh hưởng của mất gói vào một macroblock duy nhất, thay vì làm hỏng nhiều macroblock chỉ vì một gói bị mất. Đây là ví dụ của Application Level Framing, đã bàn ở chương trước.

Chia gói chỉ là vấn đề đầu tiên khi gửi video MPEG nén qua mạng. Phức tạp tiếp theo là xử lý mất gói. Một mặt, nếu một khung B bị mạng làm mất, có thể chỉ cần phát lại khung trước đó mà không ảnh hưởng nghiêm trọng đến video; mất 1 khung trong 30 không phải vấn đề lớn. Mặt khác, mất một khung I có hậu quả nghiêm trọng—không thể xử lý các khung B và P tiếp theo nếu không có nó. Như vậy, mất một khung I sẽ làm mất nhiều khung video. Dù có thể truyền lại khung I bị mất, độ trễ gây ra có thể không chấp nhận được trong hội nghị truyền hình thời gian thực. Một giải pháp là dùng kỹ thuật Dịch vụ Phân biệt (Differentiated Services) đã mô tả ở chương trước để đánh dấu các gói chứa khung I với xác suất bị loại thấp hơn các gói khác.

Một nhận xét cuối cùng là cách bạn chọn mã hóa video phụ thuộc không chỉ vào băng thông mạng sẵn có. Nó còn phụ thuộc vào ràng buộc độ trễ của ứng dụng. Một lần nữa, ứng dụng tương tác như hội nghị truyền hình cần độ trễ nhỏ. Yếu tố then chốt là tổ hợp các khung I, P, B trong GOP. Xét GOP sau:

.. centered:: I B B B B P B B B B I

Vấn đề GOP này gây ra cho ứng dụng hội nghị truyền hình là người gửi phải trì hoãn truyền bốn khung B cho đến khi khung P hoặc I tiếp theo sẵn sàng. Lý do là mỗi khung B phụ thuộc vào khung P hoặc I tiếp theo. Nếu video phát ở 15 khung hình/giây (tức là một khung mỗi 67 ms), điều này nghĩa là khung B đầu tiên bị trì hoãn 4 × 67 ms, tức là hơn một phần tư giây. Độ trễ này cộng thêm bất kỳ độ trễ lan truyền nào do mạng gây ra. Một phần tư giây lớn hơn nhiều so với ngưỡng 100 ms mà con người có thể cảm nhận. Vì lý do này, nhiều ứng dụng hội nghị truyền hình mã hóa video bằng JPEG, thường gọi là motion-JPEG. (Motion-JPEG cũng giải quyết vấn đề mất khung tham chiếu vì mọi khung đều độc lập.) Tuy nhiên, mã hóa giữa các khung chỉ phụ thuộc vào các khung trước chứ không phải khung sau thì không thành vấn đề. Do đó, một GOP như

.. centered:: I P P P P I

sẽ phù hợp cho hội nghị truyền hình tương tác.

Truyền phát thích nghi (Adaptive Streaming)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Vì các phương án mã hóa như MPEG cho phép đánh đổi giữa băng thông tiêu thụ và chất lượng hình ảnh, nên có cơ hội điều chỉnh luồng video cho phù hợp với băng thông mạng sẵn có. Đây chính là điều các dịch vụ truyền phát video như Netflix thực hiện ngày nay.

Đầu tiên, giả sử ta có cách đo dung lượng còn trống và mức độ tắc nghẽn trên đường truyền, ví dụ, bằng cách quan sát tốc độ các gói đến đích thành công. Khi băng thông sẵn có dao động, ta có thể phản hồi thông tin đó về codec để nó điều chỉnh tham số mã hóa, giảm tốc khi tắc nghẽn và gửi mạnh hơn (chất lượng cao hơn) khi mạng rảnh. Điều này tương tự hành vi của TCP, ngoại trừ trong trường hợp video, ta thực sự thay đổi tổng lượng dữ liệu gửi đi thay vì thời gian gửi một lượng dữ liệu cố định, vì không muốn gây thêm độ trễ cho ứng dụng video.

Với dịch vụ video theo yêu cầu như Netflix, ta không điều chỉnh mã hóa theo thời gian thực, mà thay vào đó mã hóa sẵn một số mức chất lượng video, lưu thành các tệp đặt tên tương ứng. Phía nhận chỉ cần đổi tên tệp yêu cầu cho phù hợp với chất lượng mà đo lường cho thấy mạng có thể cung cấp. Phía nhận theo dõi hàng đợi phát lại, và yêu cầu mã hóa chất lượng cao hơn khi hàng đợi đầy, chất lượng thấp hơn khi hàng đợi vơi.

Cách tiếp cận này biết nhảy đến đâu trong phim nếu chất lượng yêu cầu thay đổi như thế nào? Thực tế, phía nhận không bao giờ yêu cầu gửi toàn bộ phim, mà thay vào đó yêu cầu một chuỗi đoạn phim ngắn, thường chỉ vài giây (và luôn ở ranh giới GOP). Mỗi đoạn là một cơ hội để thay đổi mức chất lượng cho phù hợp với khả năng mạng. (Việc yêu cầu từng đoạn phim cũng giúp dễ thực hiện *trick play*, nhảy đến bất kỳ vị trí nào trong phim.) Nói cách khác, một bộ phim thường được lưu thành tập hợp N × M đoạn (tệp): N mức chất lượng cho mỗi M đoạn.

Còn một chi tiết cuối. Vì phía nhận thực chất yêu cầu một chuỗi đoạn video rời rạc theo tên, cách phổ biến nhất để gửi các yêu cầu này là dùng HTTP. Mỗi đoạn là một yêu cầu HTTP GET riêng với URL xác định đoạn mà phía nhận muốn tiếp theo. Khi bạn bắt đầu tải phim, trình phát video sẽ tải trước một tệp *manifest* chỉ đơn giản là danh sách URL cho N × M đoạn phim, rồi gửi chuỗi yêu cầu HTTP với URL phù hợp cho từng tình huống. Cách tiếp cận này gọi là *HTTP adaptive streaming*, dù đã được chuẩn hóa theo nhiều cách khác nhau bởi các tổ chức khác nhau, nổi bật nhất là DASH của MPEG (*Dynamic Adaptive Streaming over HTTP*) và HLS của Apple (*HTTP Live Streaming*).

7.2.5 Nén âm thanh (MP3)
------------------------

Không chỉ MPEG định nghĩa cách nén video, mà còn định nghĩa chuẩn nén âm thanh. Chuẩn này có thể dùng để nén phần âm thanh của một bộ phim (trong trường hợp đó, chuẩn MPEG quy định cách xen kẽ âm thanh nén với video nén trong một luồng MPEG duy nhất) hoặc để nén âm thanh độc lập (ví dụ, đĩa CD âm thanh). Chuẩn nén âm thanh MPEG chỉ là một trong nhiều chuẩn nén âm thanh, nhưng vai trò then chốt của nó khiến MP3 (viết tắt của MPEG Layer III—xem dưới) gần như đồng nghĩa với nén âm thanh.

Để hiểu nén âm thanh, ta cần bắt đầu từ dữ liệu. Âm thanh chất lượng CD, là đại diện số *de facto* cho âm thanh chất lượng cao, được lấy mẫu ở tốc độ 44.1 KHz (tức là, một mẫu được lấy khoảng mỗi 23 μs). Mỗi mẫu là 16 bit, nghĩa là một luồng âm thanh stereo (2 kênh) có tốc độ bit là

.. centered:: 2 × 44.1 × 1000 × 16 = 1.41 *Mbps*

So sánh, thoại chất lượng điện thoại truyền thống được lấy mẫu ở 8 KHz, với mẫu 8 bit, cho tốc độ bit 64 kbps.

Rõ ràng, cần nén để truyền âm thanh chất lượng CD qua mạng băng thông hạn chế. (Hãy nhớ rằng truyền phát MP3 trở nên phổ biến vào thời mà kết nối Internet gia đình 1.5Mbps còn là điều mới mẻ.) Tệ hơn, các chi phí đồng bộ và sửa lỗi làm số bit lưu trên CD tăng gấp ba, nên nếu chỉ đọc dữ liệu từ CD và gửi qua mạng, bạn sẽ cần 4.32 Mbps.

Cũng như video, âm thanh có nhiều dư thừa, và nén tận dụng điều này. Chuẩn MPEG định nghĩa ba mức nén, như liệt kê ở :numref:`Bảng %s <tab-mp3>`. Trong đó, Layer III, còn gọi là MP3, từng là chuẩn phổ biến nhất trong nhiều năm. Gần đây, các codec băng thông cao hơn xuất hiện nhiều hơn khi truyền phát âm thanh trở thành cách nghe nhạc chủ đạo của nhiều người.

.. _tab-mp3:
.. table:: Tốc độ nén MP3.
   :widths: auto
   :align: center

   +-----------+-----------+--------------------+
   | Coding    | Bit Rates | Compression Factor |
   +===========+===========+====================+
   | Layer I   | 384 kbps  | 14                 |
   +-----------+-----------+--------------------+
   | Layer II  | 192 kbps  | 18                 |
   +-----------+-----------+--------------------+
   | Layer III | 128 kbps  | 12                 |
   +-----------+-----------+--------------------+

Để đạt các tỷ lệ nén này, MP3 dùng các kỹ thuật tương tự như MPEG dùng để nén video. Đầu tiên, nó chia luồng âm thanh thành một số dải tần số con, tương tự như cách MPEG xử lý riêng các thành phần Y, U, V của video. Thứ hai, mỗi dải tần số con được chia thành chuỗi các khối, tương tự macroblock của MPEG nhưng có thể thay đổi độ dài từ 64 đến 1024 mẫu. (Thuật toán mã hóa có thể thay đổi kích thước khối tùy theo một số hiệu ứng biến dạng ngoài phạm vi bàn luận ở đây.) Cuối cùng, mỗi khối được biến đổi bằng thuật toán DCT sửa đổi, lượng tử hóa và mã hóa Huffman, giống như video MPEG.

Điểm then chốt của MP3 là số dải tần số con nó chọn dùng và số bit phân bổ cho mỗi dải, với mục tiêu tạo ra âm thanh chất lượng cao nhất cho tốc độ bit mục tiêu. Việc phân bổ này được điều chỉnh bởi các mô hình tâm lý âm học vượt ngoài phạm vi cuốn sách này, nhưng để minh họa, hãy xem xét rằng hợp lý khi phân bổ nhiều bit hơn cho dải tần số thấp khi nén giọng nam và nhiều bit hơn cho dải tần số cao khi nén giọng nữ. Về mặt vận hành, MP3 thay đổi động các bảng lượng tử hóa dùng cho từng dải để đạt hiệu quả mong muốn.

Sau khi nén, các dải tần số con được đóng gói thành các khung cố định, và một tiêu đề được gắn vào. Tiêu đề này chứa thông tin đồng bộ, cũng như thông tin phân bổ bit cần thiết cho bộ giải mã xác định số bit dùng để mã hóa mỗi dải. Như đã nói, các khung âm thanh này có thể được xen kẽ với các khung video để tạo thành một luồng MPEG hoàn chỉnh. Một lưu ý thú vị là, dù có thể bỏ qua các khung B trong mạng khi tắc nghẽn, kinh nghiệm cho thấy không nên bỏ khung âm thanh vì người dùng dễ chịu đựng video xấu hơn là âm thanh xấu.
