2.4 Phát hiện lỗi
=================

Như đã thảo luận ở Chương 1, lỗi bit đôi khi xuất hiện trong các khung. Điều này xảy ra, ví dụ, do nhiễu điện hoặc nhiễu nhiệt. Mặc dù lỗi hiếm gặp, đặc biệt trên các liên kết quang, vẫn cần có một cơ chế để phát hiện các lỗi này để có thể thực hiện hành động khắc phục. Nếu không, người dùng cuối sẽ tự hỏi tại sao chương trình C vừa biên dịch thành công lại đột nhiên báo lỗi cú pháp, trong khi tất cả những gì xảy ra là nó được sao chép qua một hệ thống file mạng.

Có một lịch sử lâu dài về các kỹ thuật xử lý lỗi bit trong các hệ thống máy tính, ít nhất từ những năm 1940. Mã Hamming và Reed-Solomon là hai ví dụ nổi bật được phát triển để sử dụng trong các đầu đọc thẻ đục lỗ, khi lưu trữ dữ liệu trên đĩa từ, và trong các bộ nhớ lõi từ đời đầu. Phần này mô tả một số kỹ thuật phát hiện lỗi được sử dụng phổ biến nhất trong mạng máy tính.

Phát hiện lỗi chỉ là một phần của vấn đề. Phần còn lại là sửa lỗi khi đã phát hiện. Có hai cách tiếp cận cơ bản khi người nhận phát hiện ra lỗi trong một thông điệp. Một là thông báo cho người gửi rằng thông điệp đã bị hỏng để người gửi có thể truyền lại một bản sao của thông điệp. Nếu lỗi bit hiếm gặp, thì rất có thể bản sao truyền lại sẽ không có lỗi. Ngoài ra, một số loại thuật toán phát hiện lỗi cho phép người nhận tái tạo lại thông điệp đúng ngay cả khi nó đã bị hỏng; các thuật toán như vậy dựa vào *mã sửa lỗi* (error-correcting codes), sẽ được thảo luận bên dưới.

Một trong những kỹ thuật phổ biến nhất để phát hiện lỗi truyền là một kỹ thuật gọi là *kiểm tra dư thừa tuần hoàn* (cyclic redundancy check - CRC). Nó được sử dụng trong hầu hết các giao thức tầng liên kết được thảo luận trong chương này. Phần này phác thảo thuật toán CRC cơ bản, nhưng trước khi thảo luận về phương pháp này, trước tiên chúng ta sẽ mô tả sơ đồ *checksum* đơn giản được sử dụng bởi một số giao thức Internet.

Ý tưởng cơ bản đằng sau bất kỳ sơ đồ phát hiện lỗi nào là thêm thông tin dư thừa vào một khung để có thể xác định liệu lỗi đã xuất hiện hay chưa. Ở mức cực đoan, ta có thể tưởng tượng truyền hai bản sao hoàn chỉnh của dữ liệu. Nếu hai bản sao giống hệt nhau ở phía nhận, thì có lẽ cả hai đều đúng. Nếu chúng khác nhau, thì đã có lỗi xuất hiện ở một (hoặc cả hai) bản sao, và chúng phải bị loại bỏ. Đây là một sơ đồ phát hiện lỗi khá tệ vì hai lý do. Thứ nhất, nó gửi :math:`n` bit dư thừa cho một thông điệp :math:`n` bit. Thứ hai, nhiều lỗi sẽ không bị phát hiện—bất kỳ lỗi nào làm hỏng cùng vị trí bit trong cả hai bản sao của thông điệp. Nói chung, mục tiêu của các mã phát hiện lỗi là cung cấp xác suất phát hiện lỗi cao với số bit dư thừa tương đối thấp.

May mắn thay, chúng ta có thể làm tốt hơn nhiều so với sơ đồ đơn giản này. Nói chung, chúng ta có thể cung cấp khả năng phát hiện lỗi rất mạnh trong khi chỉ gửi :math:`k` bit dư thừa cho một thông điệp :math:`n` bit, với :math:`k` nhỏ hơn nhiều so với :math:`n`. Ví dụ, trên Ethernet, một khung mang tối đa 12.000 bit (1500 byte) dữ liệu chỉ cần một mã CRC 32 bit, hay thường gọi là CRC-32. Mã này sẽ phát hiện phần lớn các lỗi, như sẽ thấy bên dưới.

Chúng ta nói rằng các bit bổ sung này là dư thừa vì chúng không thêm thông tin mới vào thông điệp. Thay vào đó, chúng được tạo ra trực tiếp từ thông điệp gốc bằng một thuật toán xác định. Cả người gửi và người nhận đều biết chính xác thuật toán đó là gì. Người gửi áp dụng thuật toán lên thông điệp để tạo ra các bit dư thừa. Sau đó, nó truyền cả thông điệp và các bit bổ sung này. Khi người nhận áp dụng cùng thuật toán lên thông điệp nhận được, nó nên (nếu không có lỗi) thu được kết quả giống như người gửi. Nó so sánh kết quả với giá trị do người gửi gửi đến. Nếu chúng khớp nhau, có thể kết luận (với xác suất cao) rằng không có lỗi xuất hiện trong quá trình truyền. Nếu không khớp, chắc chắn hoặc thông điệp hoặc các bit dư thừa đã bị hỏng, và phải thực hiện hành động phù hợp—tức là loại bỏ thông điệp hoặc sửa nếu có thể.

Một lưu ý về thuật ngữ cho các bit bổ sung này. Nói chung, chúng được gọi là *mã phát hiện lỗi* (error-detecting codes). Trong các trường hợp cụ thể, khi thuật toán tạo mã dựa trên phép cộng, chúng có thể được gọi là *checksum*. Chúng ta sẽ thấy rằng Internet checksum được đặt tên đúng: Nó là một kiểm tra lỗi sử dụng thuật toán cộng. Đáng tiếc, từ *checksum* thường bị dùng không chính xác để chỉ bất kỳ dạng mã phát hiện lỗi nào, kể cả CRC. Điều này có thể gây nhầm lẫn, vì vậy chúng tôi khuyên bạn chỉ dùng từ *checksum* cho các mã thực sự sử dụng phép cộng và dùng *mã phát hiện lỗi* cho lớp mã tổng quát được mô tả trong phần này.

2.4.1 Thuật toán Internet Checksum
----------------------------------

Cách tiếp cận đầu tiên với phát hiện lỗi được minh họa bởi Internet checksum. Mặc dù nó không được sử dụng ở tầng liên kết, nó vẫn cung cấp chức năng tương tự như CRC, nên chúng ta sẽ thảo luận ở đây.

Ý tưởng đằng sau Internet checksum rất đơn giản—bạn cộng tất cả các từ được truyền và sau đó truyền kết quả của phép cộng đó. Kết quả là checksum. Bộ nhận thực hiện phép tính tương tự trên dữ liệu nhận được và so sánh kết quả với checksum nhận được. Nếu bất kỳ dữ liệu nào được truyền, bao gồm cả checksum, bị hỏng, thì kết quả sẽ không khớp, nên bộ nhận biết đã có lỗi xảy ra.

Bạn có thể tưởng tượng nhiều biến thể khác nhau của ý tưởng checksum cơ bản. Sơ đồ chính xác được các giao thức Internet sử dụng như sau. Xem dữ liệu cần kiểm tra như một dãy số nguyên 16 bit. Cộng chúng lại với nhau bằng phép toán bù một (ones’ complement) 16 bit (giải thích bên dưới) rồi lấy bù một của kết quả. Số 16 bit đó là checksum.

Trong số học bù một, một số nguyên âm (-x) được biểu diễn bằng bù của x; tức là, mỗi bit của x bị đảo ngược. Khi cộng các số trong số học bù một, một bit nhớ từ bit có trọng số lớn nhất cần được cộng vào kết quả. Ví dụ, cộng -5 và -2 trong số học bù một trên số nguyên 4 bit: +5 là 0101, nên -5 là 1010; +2 là 0010, nên -2 là 1101. Nếu cộng 1010 và 1101, bỏ qua bit nhớ, ta được 0111. Trong số học bù một, việc phép cộng này tạo ra bit nhớ từ bit cao nhất khiến ta phải tăng kết quả lên một, thành 1000, là biểu diễn bù một của -7 (được tạo bằng cách đảo bit của 0111), như mong đợi.

Đoạn mã sau đây là hiện thực đơn giản của thuật toán Internet checksum. Tham số ``count`` cho biết độ dài của ``buf`` tính bằng đơn vị 16 bit. Đoạn mã giả định rằng ``buf`` đã được đệm thêm các số 0 để căn lề 16 bit.

.. code-block:: c

   u_short
   cksum(u_short *buf, int count)
   {
       register u_long sum = 0;

       while (count--)
       {
           sum += *buf++;
           if (sum & 0xFFFF0000)
           {
               /* carry occurred, so wrap around */
               sum &= 0xFFFF;
               sum++;
           }
       }
       return ~(sum & 0xFFFF);
   }

Đoạn mã này đảm bảo phép tính sử dụng số học bù một thay vì bù hai như trên hầu hết các máy tính. Lưu ý câu lệnh ``if`` trong vòng lặp ``while``. Nếu có bit nhớ vào 16 bit cao nhất của ``sum``, thì ta tăng ``sum`` lên một như trong ví dụ trước.

So với mã lặp lại, thuật toán này có ưu điểm là chỉ dùng một số nhỏ bit dư thừa—chỉ 16 bit cho thông điệp bất kỳ độ dài nào—nhưng nó không mạnh về khả năng phát hiện lỗi. Ví dụ, một cặp lỗi một bit, một lỗi tăng một từ và một lỗi giảm một từ khác cùng giá trị, sẽ không bị phát hiện. Lý do sử dụng thuật toán như vậy dù bảo vệ lỗi yếu (so với CRC chẳng hạn) rất đơn giản: Thuật toán này dễ hiện thực bằng phần mềm. Kinh nghiệm cho thấy checksum dạng này là đủ, nhưng một lý do là nó là tuyến phòng thủ cuối cùng trong một giao thức đầu-cuối. Phần lớn lỗi được phát hiện bởi các thuật toán phát hiện lỗi mạnh hơn, như CRC, ở tầng liên kết.

2.4.2 Kiểm tra dư thừa tuần hoàn (CRC)
--------------------------------------

Đến đây, có thể thấy mục tiêu lớn trong thiết kế thuật toán phát hiện lỗi là tối đa hóa xác suất phát hiện lỗi chỉ với một số nhỏ bit dư thừa. CRC sử dụng toán học khá mạnh để đạt mục tiêu này. Ví dụ, một CRC 32 bit cung cấp khả năng bảo vệ mạnh trước các lỗi bit phổ biến trong các thông điệp dài hàng nghìn byte. Nền tảng lý thuyết của CRC bắt nguồn từ một nhánh toán học gọi là *trường hữu hạn* (finite fields). Dù nghe có vẻ khó, các ý tưởng cơ bản có thể hiểu được dễ dàng.

Đầu tiên, hãy nghĩ về một thông điệp (n+1) bit như được biểu diễn bởi một đa thức bậc :math:`n`, tức là một đa thức có hạng tử cao nhất là :math:`x^{n}`. Thông điệp được biểu diễn bằng đa thức bằng cách dùng giá trị của mỗi bit trong thông điệp làm hệ số cho mỗi hạng tử trong đa thức, bắt đầu từ bit có trọng số lớn nhất cho hạng tử cao nhất. Ví dụ, một thông điệp 8 bit gồm các bit 10011010 tương ứng với đa thức

.. math::

   M(x) = (1 \times x^7) + (0 \times x^6) + (0 \times x^5) + (1 \times
   x^4 )+ (1 \times x^3) + (0 \times x^2) + (1 \times x^1) + (0 \times x^0)

.. math::

   M(x) = x^7 + x^4 + x^3 + x^1

Như vậy, ta có thể coi người gửi và người nhận trao đổi các đa thức với nhau.

Để tính CRC, người gửi và người nhận phải thống nhất về một đa thức *chia* :math:`C(x)`. :math:`C(x)` là một đa thức bậc :math:`k`. Ví dụ, giả sử :math:`C(x) = x^3 + x^2 + 1`. Khi đó, :math:`k=3`. Câu trả lời cho câu hỏi “:math:`C(x)` lấy từ đâu?” là, trong hầu hết các trường hợp thực tế, “Bạn tra trong sách.” Thực tế, việc chọn :math:`C(x)` ảnh hưởng lớn đến loại lỗi có thể phát hiện, như sẽ thảo luận bên dưới. Có một số đa thức chia rất tốt cho các môi trường khác nhau, và lựa chọn chính xác thường là một phần của thiết kế giao thức. Ví dụ, chuẩn Ethernet sử dụng một đa thức bậc 32 rất nổi tiếng.

Khi người gửi muốn truyền một thông điệp :math:`M(x)` dài n+1 bit, thực tế sẽ gửi thông điệp (n+1) bit cộng thêm :math:`k` bit. Ta gọi thông điệp truyền hoàn chỉnh, gồm cả bit dư thừa, là :math:`P(x)`. Điều chúng ta sẽ làm là tạo ra đa thức biểu diễn :math:`P(x)` chia hết cho :math:`C(x)`; sẽ giải thích cách thực hiện bên dưới. Nếu :math:`P(x)` được truyền qua liên kết và không có lỗi, thì bộ nhận sẽ chia :math:`P(x)` cho :math:`C(x)` chính xác, dư bằng 0. Ngược lại, nếu có lỗi xuất hiện trong :math:`P(x)` khi truyền, thì rất có thể đa thức nhận được sẽ không còn chia hết cho :math:`C(x)`, và bộ nhận sẽ thu được dư khác 0, báo hiệu có lỗi.

Sẽ dễ hiểu hơn nếu bạn biết một chút về số học đa thức; nó chỉ hơi khác số học số nguyên thông thường. Ở đây, chúng ta làm việc với một lớp đặc biệt của số học đa thức, trong đó các hệ số chỉ có thể là 0 hoặc 1, và các phép toán trên hệ số được thực hiện theo modulo 2. Điều này gọi là “số học đa thức modulo 2.” Vì đây là sách về mạng, không phải toán, nên hãy tập trung vào các tính chất chính cho mục đích của chúng ta (hãy tin vào điều này):

- Bất kỳ đa thức :math:`B(x)` nào cũng có thể chia cho đa thức chia :math:`C(x)` nếu :math:`B(x)` có bậc cao hơn :math:`C(x)`.

- Bất kỳ đa thức :math:`B(x)` nào cũng có thể chia một lần cho đa thức chia :math:`C(x)` nếu :math:`B(x)` cùng bậc với :math:`C(x)`.

- Phần dư khi chia :math:`B(x)` cho :math:`C(x)` được tính bằng phép XOR từng cặp hệ số tương ứng.

Ví dụ, đa thức :math:`x^3 + 1` có thể chia cho :math:`x^3 + x^2 + 1` (vì cùng bậc 3) và dư là :math:`0 \times x^3 + 1 \times x^2 + 0 \times x^1 + 0 \times x^0 = x^2` (bằng cách XOR các hệ số). Về thông điệp, ta có thể nói 1001 chia cho 1101 dư 0100. Bạn có thể thấy phần dư chỉ là phép XOR từng bit của hai thông điệp.

Giờ khi đã biết quy tắc chia đa thức cơ bản, ta có thể thực hiện phép chia dài, cần thiết cho thông điệp dài hơn. Một ví dụ minh họa bên dưới.

Nhớ rằng ta muốn tạo ra một đa thức để truyền, được dẫn xuất từ thông điệp gốc :math:`M(x)`, dài hơn :math:`k` bit so với :math:`M(x)`, và chia hết cho :math:`C(x)`. Ta có thể làm như sau:

1. Nhân :math:`M(x)` với :math:`x^{k}`; tức là thêm :math:`k` số 0 vào cuối thông điệp. Gọi thông điệp đã thêm 0 này là :math:`T(x)`.

2. Chia :math:`T(x)` cho :math:`C(x)` và tìm phần dư.

3. Trừ phần dư khỏi :math:`T(x)`.

Rõ ràng, kết quả còn lại là một thông điệp chia hết cho :math:`C(x)`. Ta cũng lưu ý rằng thông điệp kết quả gồm :math:`M(x)` nối với phần dư thu được ở bước 2, vì khi trừ phần dư (không quá :math:`k` bit), ta chỉ thực hiện phép XOR với :math:`k` số 0 đã thêm ở bước 1. Phần này sẽ rõ hơn với ví dụ.

Xét thông điệp :math:`x^7 + x^4 + x^3 + x^1`, hay 10011010. Ta bắt đầu bằng cách nhân với :math:`x^3`, vì đa thức chia là bậc 3. Kết quả là 10011010000. Ta chia cho :math:`C(x)`, tương ứng với 1101. :numref:`Hình %s <fig-crcalc>` minh họa phép chia dài đa thức. Theo quy tắc số học đa thức ở trên, phép chia dài tiến hành gần giống như chia số nguyên. Ở bước đầu tiên, ta thấy 1101 chia vào bốn bit đầu của thông điệp (1001), vì cùng bậc, và dư là 100 (1101 XOR 1001). Bước tiếp theo là kéo xuống một bit từ thông điệp cho đến khi có đa thức cùng bậc với :math:`C(x)`, ở đây là 1001. Ta lại tính dư (100) và tiếp tục cho đến khi hoàn thành phép chia. Lưu ý rằng “kết quả” của phép chia dài, xuất hiện ở trên cùng, thực ra không quan trọng—chỉ phần dư cuối cùng mới quan trọng.

Bạn có thể thấy ở cuối :numref:`Hình %s <fig-crcalc>` phần dư của phép tính là 101. Vậy ta biết 10011010000 trừ 101 sẽ chia hết cho :math:`C(x)`, và đó là cái ta gửi. Phép trừ trong số học đa thức là phép XOR logic, nên thực tế ta gửi 10011010101. Như đã nói, đây chính là thông điệp gốc nối với phần dư từ phép chia dài. Bộ nhận chia đa thức nhận được cho :math:`C(x)` và nếu kết quả là 0, kết luận không có lỗi. Nếu kết quả khác 0, có thể phải loại bỏ thông điệp bị lỗi; với một số mã, có thể *sửa* lỗi nhỏ (ví dụ, nếu chỉ một bit bị lỗi). Một mã cho phép sửa lỗi gọi là *mã sửa lỗi* (ECC).

.. _fig-crcalc:
.. figure:: figures/f02-15-9780123850591.png
   :width: 400px
   :align: center

   Tính CRC bằng phép chia dài đa thức.

Giờ ta sẽ xét câu hỏi đa thức :math:`C(x)` lấy từ đâu. Trực giác, ý tưởng là chọn đa thức này sao cho rất khó chia hết cho một thông điệp có lỗi. Nếu thông điệp truyền là :math:`P(x)`, ta có thể coi việc xuất hiện lỗi là cộng thêm một đa thức khác :math:`E(x)`, nên bộ nhận thấy :math:`P(x) + E(x)`. Cách duy nhất để lỗi không bị phát hiện là nếu thông điệp nhận được chia hết cho :math:`C(x)`, và vì ta biết :math:`P(x)` chia hết cho :math:`C(x)`, điều này chỉ xảy ra nếu :math:`E(x)` cũng chia hết cho :math:`C(x)`. Bí quyết là chọn :math:`C(x)` sao cho điều này rất khó xảy ra với các loại lỗi phổ biến.

Một loại lỗi phổ biến là lỗi một bit, có thể biểu diễn là :math:`E(x) = x^i` khi nó ảnh hưởng đến vị trí bit *i*. Nếu ta chọn :math:`C(x)` sao cho hạng tử đầu và cuối (tức là :math:`x^k` và :math:`x^0`) khác 0, thì ta đã có một đa thức hai hạng tử không thể chia hết cho đa thức một hạng tử :math:`E(x)`. Như vậy, :math:`C(x)` như vậy có thể phát hiện mọi lỗi một bit. Nói chung, có thể chứng minh rằng các loại lỗi sau có thể được phát hiện bởi :math:`C(x)` với các tính chất sau:

- Mọi lỗi một bit, miễn là các hạng tử :math:`x^{k}` và :math:`x^{0}` có hệ số khác 0

- Mọi lỗi hai bit, miễn là :math:`C(x)` có một thừa số với ít nhất ba hạng tử

- Bất kỳ số lẻ lỗi nào, miễn là :math:`C(x)` chứa thừa số :math:`(x + 1)`

- Bất kỳ lỗi “burst” (tức là chuỗi liên tiếp các bit lỗi) nào có độ dài nhỏ hơn :math:`k` bit (Hầu hết các lỗi burst dài hơn :math:`k` bit cũng có thể phát hiện.)

Sáu phiên bản của :math:`C(x)` được sử dụng rộng rãi trong các giao thức tầng liên kết. Ví dụ, Ethernet sử dụng CRC-32, được định nghĩa như sau:

-  CRC-32 = :math:`x^{32} + x^{26} + x^{23} + x^{22} + x^{16} +
   x^{12} + x^{11} + x^{10} + x^8 + x^7 + x^5 + x^4 + x^2 + x + 1`

Chúng tôi đã đề cập rằng có thể sử dụng các mã không chỉ phát hiện mà còn cho phép sửa lỗi. Vì chi tiết của các mã này đòi hỏi toán học phức tạp hơn cả CRC, nên chúng tôi sẽ không đi sâu ở đây. Tuy nhiên, cũng đáng cân nhắc ưu nhược điểm của sửa lỗi so với phát hiện lỗi.

Thoạt nhìn, có vẻ sửa lỗi luôn tốt hơn, vì với phát hiện lỗi ta buộc phải loại bỏ thông điệp và thường phải yêu cầu truyền lại bản sao khác. Điều này tốn băng thông và có thể gây trễ khi chờ truyền lại. Tuy nhiên, sửa lỗi cũng có nhược điểm, vì thường cần nhiều bit dư thừa hơn để gửi một mã sửa lỗi mạnh bằng mã chỉ phát hiện lỗi. Do đó, trong khi phát hiện lỗi cần gửi thêm bit khi có lỗi, sửa lỗi cần gửi thêm bit *mọi lúc*. Kết quả là, sửa lỗi thường hữu ích nhất khi (1) lỗi khá phổ biến, như trong môi trường không dây, hoặc (2) chi phí truyền lại quá cao, ví dụ do độ trễ khi truyền lại gói qua liên kết vệ tinh.

Việc sử dụng mã sửa lỗi trong mạng đôi khi được gọi là *sửa lỗi tiến* (forward error correction - FEC) vì việc sửa lỗi được xử lý “trước” bằng cách gửi thêm thông tin, thay vì chờ lỗi xảy ra rồi mới xử lý bằng cách truyền lại. FEC thường được sử dụng trong các mạng không dây như 802.11.

Cuối cùng, chúng ta lưu ý rằng thuật toán CRC, dù có vẻ phức tạp, lại dễ dàng được hiện thực bằng phần cứng sử dụng một thanh ghi dịch :math:`k` bit và các cổng XOR. Số bit trong thanh ghi dịch bằng với bậc của đa thức sinh (:math:`k`). :numref:`Hình %s <fig-crc-hard>` minh họa phần cứng sẽ được sử dụng cho đa thức sinh :math:`x^3 + x^2 + 1` từ ví dụ trước của chúng ta. Thông điệp được dịch vào từ bên trái, bắt đầu với bit có trọng số lớn nhất và kết thúc với chuỗi :math:`k` số 0 được gắn vào thông điệp, giống như trong ví dụ chia dài. Khi tất cả các bit đã được dịch vào và XOR thích hợp, thanh ghi sẽ chứa phần dư—chính là CRC (bit có trọng số lớn nhất nằm bên phải). Vị trí của các cổng XOR được xác định như sau: Nếu các bit trong thanh ghi dịch được đánh số từ 0 đến :math:`k-1`, từ trái sang phải, thì đặt một cổng XOR trước bit :math:`n` nếu có một hạng tử :math:`x^n` trong đa thức sinh. Do đó, chúng ta thấy một cổng XOR ở vị trí 0 và 2 cho đa thức sinh :math:`x^3 + x^2 + x^0`.

.. _fig-crc-hard:
.. figure:: figures/f02-16-9780123850591.png
   :width: 350px
   :align: center

   Tính CRC sử dụng thanh ghi dịch.
