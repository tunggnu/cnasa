8.2 Các Khối Xây Dựng Mật Mã
============================

Chúng tôi giới thiệu các khái niệm về bảo mật dựa trên mật mã từng bước một.
Bước đầu tiên là các thuật toán mật mã—các mã hóa (cipher) và hàm băm mật mã—
được giới thiệu trong phần này. Chúng không phải là một giải pháp tự thân,
mà là các khối xây dựng từ đó có thể xây dựng nên một giải pháp. Các thuật toán mật mã
được tham số hóa bởi *khóa* (key), và một phần sau sẽ đề cập đến vấn đề phân phối khóa.
Ở bước tiếp theo, chúng tôi mô tả cách tích hợp các khối xây dựng mật mã
vào các giao thức cung cấp liên lạc an toàn giữa các thành viên
có khóa phù hợp. Phần cuối cùng sẽ xem xét một số giao thức và hệ thống bảo mật hoàn chỉnh đang được sử dụng hiện nay.

8.2.1 Nguyên Lý Của Mã Hóa (Cipher)
-----------------------------------

Mã hóa (encryption) biến đổi một thông điệp theo cách mà nó trở nên
không thể hiểu được đối với bất kỳ bên nào không có bí mật về cách đảo ngược phép biến đổi đó.
Người gửi áp dụng một hàm *mã hóa* (encryption) lên thông điệp gốc (*plaintext*),
tạo ra một thông điệp *ciphertext* được gửi qua mạng, như minh họa trong
:numref:`Figure %s <fig-genericCrypto>`. Người nhận áp dụng một hàm *giải mã* (decryption) bí mật—
là phép nghịch đảo của hàm mã hóa—để khôi phục lại bản rõ ban đầu. Ciphertext được truyền qua mạng
là không thể hiểu được đối với bất kỳ kẻ nghe lén nào, với giả định rằng
kẻ nghe lén không biết hàm giải mã. Phép biến đổi được biểu diễn bởi một hàm mã hóa và hàm giải mã tương ứng
được gọi là một *cipher* (mã hóa).

.. _fig-genericCrypto:
.. figure:: figures/f08-01-9780123850591.png
   :width: 500px
   :align: center

   Mã hóa và giải mã khóa bí mật.

Các nhà mật mã học đã rút ra nguyên lý, lần đầu tiên được phát biểu năm 1883,
rằng các hàm mã hóa và giải mã nên được tham số hóa bởi một *khóa* (key),
và hơn nữa các hàm này nên được coi là kiến thức công khai—chỉ có khóa là cần giữ bí mật.
Do đó, ciphertext được tạo ra cho một thông điệp bản rõ nhất định phụ thuộc vào cả hàm mã hóa và khóa.
Một lý do cho nguyên lý này là nếu bạn phụ thuộc vào việc giữ bí mật thuật toán mã hóa,
thì bạn sẽ phải loại bỏ thuật toán đó (không chỉ các khóa) khi bạn tin rằng nó không còn bí mật nữa.
Điều này có nghĩa là phải thay đổi thuật toán thường xuyên, điều này gây khó khăn vì việc phát triển một thuật toán mới rất tốn công sức.
Ngoài ra, một trong những cách tốt nhất để biết một thuật toán mã hóa có an toàn hay không là sử dụng nó trong thời gian dài—
nếu không ai phá được nó, có lẽ nó an toàn. (May mắn thay, có rất nhiều người sẽ cố gắng phá các thuật toán mã hóa
và sẽ thông báo rộng rãi khi họ thành công, vì vậy không có tin tức gì thường là tin tốt.)
Do đó, có chi phí và rủi ro đáng kể khi triển khai một thuật toán mới.
Cuối cùng, việc tham số hóa một thuật toán mã hóa bằng khóa cung cấp cho chúng ta một họ rất lớn các thuật toán mã hóa;
bằng cách thay đổi khóa, về cơ bản chúng ta thay đổi thuật toán, từ đó giới hạn lượng dữ liệu mà một *cryptanalyst*
(kẻ phá mã) có thể sử dụng để cố gắng phá khóa/thuật toán của chúng ta và lượng dữ liệu mà họ có thể đọc nếu thành công.

Yêu cầu cơ bản đối với một thuật toán mã hóa là nó phải biến đổi bản rõ thành ciphertext
sao cho chỉ người nhận dự định—người giữ khóa giải mã—mới có thể khôi phục bản rõ.
Điều này có nghĩa là các thông điệp đã mã hóa không thể bị đọc bởi những người không giữ khóa.

Điều quan trọng là phải nhận ra rằng khi một kẻ tấn công tiềm năng nhận được một đoạn ciphertext,
họ có thể có nhiều thông tin hơn ngoài bản thân ciphertext. Ví dụ, họ có thể biết rằng bản rõ được viết bằng tiếng Anh,
nghĩa là chữ *e* xuất hiện thường xuyên hơn bất kỳ chữ cái nào khác; tần suất của nhiều chữ cái khác và các tổ hợp chữ cái phổ biến cũng có thể dự đoán được.
Thông tin này có thể đơn giản hóa đáng kể việc tìm ra khóa. Tương tự, họ có thể biết điều gì đó về nội dung có khả năng xuất hiện trong thông điệp;
ví dụ, từ “login” có thể xuất hiện ở đầu một phiên đăng nhập từ xa. Điều này có thể cho phép một cuộc tấn công *bản rõ đã biết* (known plaintext),
có xác suất thành công cao hơn nhiều so với một cuộc tấn công *chỉ có ciphertext* (ciphertext only).
Thậm chí tốt hơn là một cuộc tấn công *bản rõ được chọn* (chosen plaintext), có thể thực hiện bằng cách cung cấp một số thông tin cho người gửi
mà bạn biết chắc họ sẽ truyền đi—những điều như vậy đã từng xảy ra trong thời chiến.

Do đó, các thuật toán mật mã tốt nhất có thể ngăn kẻ tấn công suy ra khóa ngay cả khi họ biết cả bản rõ và ciphertext.
Điều này buộc kẻ tấn công chỉ còn cách thử tất cả các khóa có thể—tìm kiếm toàn bộ, “brute force”.
Nếu khóa có *n* bit, thì có 2\ :sup:`n` giá trị khóa có thể (mỗi bit có thể là 0 hoặc 1).
Kẻ tấn công có thể may mắn thử đúng giá trị ngay lập tức, hoặc xui xẻo phải thử tất cả các giá trị sai trước khi thử đúng khóa,
tức là thử tất cả 2\ :sup:`n` giá trị; số lần đoán trung bình để tìm ra giá trị đúng là ở giữa hai cực đoan đó, 2\ :sup:`n-1`.
Điều này có thể trở nên bất khả thi về mặt tính toán bằng cách chọn không gian khóa đủ lớn
và làm cho việc kiểm tra một khóa đủ tốn kém. Điều làm cho việc này khó là tốc độ tính toán ngày càng tăng,
khiến những phép tính trước đây không khả thi trở nên khả thi. Hơn nữa, mặc dù chúng ta tập trung vào bảo mật dữ liệu khi nó di chuyển qua mạng—
tức là dữ liệu đôi khi chỉ dễ bị tấn công trong một thời gian ngắn—nhưng nói chung, người làm bảo mật phải xem xét cả
tính dễ bị tấn công của dữ liệu cần lưu trữ trong kho lưu trữ hàng chục năm. Điều này đòi hỏi phải chọn kích thước khóa đủ lớn.
Mặt khác, khóa lớn hơn làm cho việc mã hóa và giải mã chậm hơn.

Hầu hết các thuật toán mã hóa là *block cipher* (mã hóa khối); chúng được định nghĩa để nhận đầu vào là một khối bản rõ có kích thước cố định,
thường là 64 đến 128 bit. Sử dụng block cipher để mã hóa từng khối độc lập—được gọi là mã hóa *electronic codebook (ECB) mode*—
có điểm yếu là một giá trị khối bản rõ nhất định sẽ luôn tạo ra cùng một khối ciphertext.
Do đó, các giá trị khối lặp lại trong bản rõ sẽ dễ dàng nhận ra trong ciphertext,
khiến cho việc phá mã trở nên dễ dàng hơn.

Để ngăn điều này, block cipher luôn được bổ sung để làm cho ciphertext của một khối thay đổi tùy theo ngữ cảnh.
Các cách bổ sung cho block cipher được gọi là *chế độ hoạt động* (modes of operation).
Một chế độ phổ biến là *cipher block chaining* (CBC), trong đó mỗi khối bản rõ được XOR với ciphertext của khối trước đó trước khi mã hóa.
Kết quả là ciphertext của mỗi khối phụ thuộc một phần vào các khối trước đó (tức là vào ngữ cảnh).
Vì khối bản rõ đầu tiên không có khối trước, nó được XOR với một số ngẫu nhiên.
Số ngẫu nhiên đó, gọi là *vector khởi tạo* (initialization vector, IV), được gửi kèm với chuỗi các khối ciphertext
để có thể giải mã khối ciphertext đầu tiên. Chế độ này được minh họa trong :numref:`Figure %s <fig-cbc>`.
Một chế độ khác là *counter mode*, trong đó các giá trị liên tiếp của một bộ đếm (ví dụ, 1, 2, 3, :math:`\ldots`)
được tích hợp vào việc mã hóa các khối bản rõ liên tiếp.

.. _fig-cbc:
.. figure:: figures/f08-02-9780123850591.png
   :width: 500px
   :align: center

   Chuỗi khối mã hóa (Cipher Block Chaining).

8.2.2 Mã Hóa Khóa Bí Mật
------------------------

Trong một mã hóa khóa bí mật, cả hai bên tham gia liên lạc đều chia sẻ cùng một khóa.\ [#]_
Nói cách khác, nếu một thông điệp được mã hóa bằng một khóa nhất định, thì cùng khóa đó được dùng để giải mã thông điệp.
Nếu thuật toán mã hóa trong :numref:`Figure %s <fig-genericCrypto>` là mã hóa khóa bí mật,
thì khóa mã hóa và giải mã sẽ giống hệt nhau. Mã hóa khóa bí mật còn được gọi là mã hóa khóa đối xứng
vì bí mật được chia sẻ cho cả hai bên. Chúng ta sẽ xem xét lựa chọn thay thế là mã hóa khóa công khai ở phần sau.
(Mã hóa khóa công khai còn gọi là mã hóa khóa bất đối xứng, vì như sẽ thấy, hai bên sử dụng các khóa khác nhau.)

.. [#] Chúng tôi sử dụng thuật ngữ *participant* (thành viên) cho các bên tham gia liên lạc an toàn
       vì đó là thuật ngữ được dùng xuyên suốt cuốn sách để chỉ hai đầu mút của một kênh.
       Trong lĩnh vực bảo mật, họ thường được gọi là *principal* (chủ thể).

Viện Tiêu chuẩn và Công nghệ Quốc gia Hoa Kỳ (NIST) đã ban hành các tiêu chuẩn cho một loạt mã hóa khóa bí mật.
*Tiêu chuẩn Mã hóa Dữ liệu* (DES) là tiêu chuẩn đầu tiên, và nó đã vượt qua thử thách thời gian
khi chưa có cuộc tấn công phân tích mật mã nào tốt hơn brute force được phát hiện.
Tuy nhiên, brute force ngày càng nhanh hơn. Khóa của DES (56 bit độc lập) hiện nay là quá nhỏ với tốc độ xử lý hiện tại.
Khóa DES có 56 bit độc lập (mặc dù tổng cộng có 64 bit; bit cuối của mỗi byte là bit chẵn lẻ).
Như đã nói ở trên, trung bình bạn sẽ phải thử một nửa không gian 2\ :sup:`56` khóa để tìm ra khóa đúng,
tức là 2\ :sup:`55` = 3.6 × 10\ :sup:`16` khóa. Nghe có vẻ nhiều, nhưng việc tìm kiếm này có thể song song hóa rất cao,
nên bạn có thể sử dụng bao nhiêu máy tính tùy thích—và ngày nay rất dễ để có hàng ngàn máy tính.
(Amazon sẽ cho bạn thuê với giá vài xu một giờ.) Đến cuối những năm 1990, đã có thể khôi phục khóa DES chỉ sau vài giờ.
Do đó, NIST đã cập nhật tiêu chuẩn DES năm 1999 để chỉ sử dụng DES cho các hệ thống cũ.

NIST cũng chuẩn hóa thuật toán *Triple DES* (3DES), tận dụng khả năng chống phân tích mật mã của DES
đồng thời tăng kích thước khóa. Khóa 3DES có 168 (= 3 × 56) bit độc lập, và được dùng như ba khóa DES;
gọi là DES-key1, DES-key2, và DES-key3. Mã hóa 3DES một khối được thực hiện bằng cách đầu tiên mã hóa DES với DES-key1,
sau đó *giải mã* DES với DES-key2, và cuối cùng mã hóa DES với DES-key3. Giải mã thực hiện ngược lại:
giải mã với DES-key3, rồi mã hóa với DES-key2, rồi giải mã với DES-key1.

Lý do 3DES sử dụng giải mã DES với DES-key2 là để tương thích với các hệ thống DES cũ.
Nếu một hệ thống DES cũ dùng một khóa duy nhất, thì hệ thống 3DES có thể thực hiện cùng hàm mã hóa
bằng cách dùng khóa đó cho cả DES-key1, DES-key2 và DES-key3; ở hai bước đầu, ta mã hóa rồi giải mã với cùng một khóa,
tạo ra bản rõ gốc, sau đó lại mã hóa lần nữa.

Mặc dù 3DES giải quyết vấn đề độ dài khóa của DES, nó vẫn thừa hưởng một số nhược điểm khác.
Việc triển khai phần mềm DES/3DES chậm vì ban đầu nó được IBM thiết kế để thực hiện bằng phần cứng.
Ngoài ra, DES/3DES dùng kích thước khối 64 bit; kích thước khối lớn hơn sẽ hiệu quả và an toàn hơn.

Hiện nay, 3DES đang dần được thay thế bởi tiêu chuẩn *Advanced Encryption Standard* (AES) do NIST ban hành.
Thuật toán mã hóa nền tảng của AES (với một vài sửa đổi nhỏ) ban đầu có tên là Rijndael (phát âm gần giống “Rhine dahl”)
dựa trên tên của hai tác giả Daemen và Rijmen. AES hỗ trợ độ dài khóa 128, 192 hoặc 256 bit, và độ dài khối là 128 bit.
AES cho phép triển khai nhanh cả trên phần mềm lẫn phần cứng. Nó không yêu cầu nhiều bộ nhớ,
phù hợp với các thiết bị di động nhỏ. AES có một số thuộc tính bảo mật đã được chứng minh toán học
và, tại thời điểm viết sách này, chưa có cuộc tấn công thành công đáng kể nào.

8.2.3 Mã Hóa Khóa Công Khai
---------------------------

Một lựa chọn thay thế cho mã hóa khóa bí mật là mã hóa khóa công khai.
Thay vì một khóa duy nhất được chia sẻ giữa hai bên, mã hóa khóa công khai sử dụng một cặp khóa liên quan,
một để mã hóa và một khác để giải mã. Cặp khóa này “thuộc sở hữu” của chỉ một bên.
Chủ sở hữu giữ bí mật khóa giải mã để chỉ mình có thể giải mã thông điệp; khóa đó gọi là *khóa riêng* (private key).
Chủ sở hữu công khai khóa mã hóa, để bất kỳ ai cũng có thể mã hóa thông điệp gửi cho chủ sở hữu; khóa đó gọi là *khóa công khai* (public key).
Rõ ràng, để sơ đồ này hoạt động, không được phép suy ra khóa riêng từ khóa công khai.
Do đó, bất kỳ bên nào cũng có thể lấy khóa công khai và gửi thông điệp mã hóa cho chủ sở hữu cặp khóa,
và chỉ chủ sở hữu có khóa riêng cần thiết để giải mã. Kịch bản này được minh họa trong :numref:`Figure %s <fig-public>`.

.. _fig-public:
.. figure:: figures/f08-03-9780123850591.png
   :width: 500px
   :align: center

   Mã hóa khóa công khai.

Vì điều này khá khó hiểu, chúng tôi nhấn mạnh rằng khóa mã hóa công khai là vô dụng cho việc giải mã thông điệp—
bạn thậm chí không thể giải mã thông điệp mà chính bạn vừa mã hóa trừ khi bạn có khóa giải mã riêng.
Nếu coi các khóa như xác định một kênh liên lạc giữa các bên, thì một khác biệt nữa giữa mã hóa khóa công khai và khóa bí mật là
hình thái của các kênh. Một khóa cho mã hóa khóa bí mật cung cấp một kênh hai chiều giữa hai bên—
mỗi bên giữ cùng một khóa (đối xứng) mà cả hai đều có thể dùng để mã hóa hoặc giải mã thông điệp theo cả hai chiều.
Một cặp khóa công khai/riêng, ngược lại, cung cấp một kênh một chiều và nhiều-đến-một:
từ tất cả những ai có khóa công khai đến chủ sở hữu duy nhất của khóa riêng, như minh họa trong :numref:`Figure %s <fig-public>`.

Một thuộc tính quan trọng khác của mã hóa khóa công khai là khóa riêng “giải mã” có thể được dùng với thuật toán mã hóa
để mã hóa thông điệp sao cho chỉ có thể giải mã bằng khóa công khai “mã hóa”.
Thuộc tính này rõ ràng không hữu ích cho tính bảo mật vì bất kỳ ai có khóa công khai đều có thể giải mã thông điệp như vậy.
(Thật vậy, để bảo mật hai chiều giữa hai bên, mỗi bên cần có cặp khóa riêng, và mỗi bên mã hóa thông điệp bằng khóa công khai của bên kia.)
Tuy nhiên, thuộc tính này hữu ích cho xác thực vì nó cho người nhận biết rằng thông điệp chỉ có thể được tạo ra bởi chủ sở hữu cặp khóa
(dưới một số giả định sẽ được đề cập sau). Điều này được minh họa trong :numref:`Figure %s <fig-pksign>`.
Có thể thấy từ hình vẽ rằng bất kỳ ai có khóa công khai đều có thể giải mã thông điệp đã mã hóa,
và, giả sử kết quả giải mã khớp với kết quả mong đợi, có thể kết luận rằng khóa riêng đã được dùng để mã hóa.
Cách thức sử dụng thao tác này để cung cấp xác thực sẽ được trình bày ở phần sau.
Như sẽ thấy, mã hóa khóa công khai chủ yếu được dùng cho xác thực và phân phối bí mật các khóa đối xứng,
phần còn lại của bảo mật sẽ do mã hóa khóa bí mật đảm nhiệm.

.. _fig-pksign:
.. figure:: figures/f08-04-9780123850591.png
   :width: 500px
   :align: center

   Xác thực bằng khóa công khai.

Một chút lịch sử thú vị: Khái niệm mã hóa khóa công khai lần đầu tiên được công bố năm 1976 bởi Diffie và Hellman.
Tuy nhiên, sau đó đã xuất hiện các tài liệu chứng minh rằng Nhóm An ninh Truyền thông Điện tử của Anh
đã phát hiện ra mã hóa khóa công khai từ năm 1970, và Cơ quan An ninh Quốc gia Hoa Kỳ (NSA) tuyên bố đã phát hiện ra nó từ giữa những năm 1960.

Thuật toán mã hóa khóa công khai nổi tiếng nhất là RSA, đặt theo tên các tác giả: Rivest, Shamir và Adleman.
RSA dựa vào chi phí tính toán rất lớn của việc phân tích thừa số các số lớn.
Bài toán tìm cách phân tích thừa số hiệu quả là một vấn đề mà các nhà toán học đã nghiên cứu không thành công từ lâu trước khi RSA xuất hiện năm 1978,
và khả năng chống phân tích mật mã của RSA sau đó càng củng cố niềm tin vào tính bảo mật của nó.
Đáng tiếc là RSA cần các khóa tương đối lớn, ít nhất 1024 bit, để an toàn.
Điều này lớn hơn các khóa của mã hóa khóa bí mật vì việc phá khóa riêng RSA bằng cách phân tích thừa số số lớn
dễ hơn so với việc thử toàn bộ không gian khóa.

Một thuật toán mã hóa khóa công khai khác là ElGamal. Giống như RSA, nó dựa vào một bài toán toán học,
bài toán logarit rời rạc, mà chưa có lời giải hiệu quả, và cũng cần các khóa ít nhất 1024 bit.
Có một biến thể của bài toán logarit rời rạc, phát sinh khi đầu vào là một đường cong elliptic,
được cho là còn khó tính toán hơn; các sơ đồ mật mã dựa trên bài toán này được gọi là *mật mã đường cong elliptic* (elliptic curve cryptography).

Đáng tiếc là mã hóa khóa công khai chậm hơn mã hóa khóa bí mật nhiều bậc độ lớn.
Do đó, mã hóa khóa bí mật được dùng cho phần lớn các hoạt động mã hóa,
trong khi mã hóa khóa công khai chỉ dùng cho xác thực và thiết lập khóa phiên.

8.2.4 Bộ Xác Thực (Authenticator)
---------------------------------

Chỉ mã hóa thôi không cung cấp tính toàn vẹn dữ liệu. Ví dụ, chỉ cần sửa đổi ngẫu nhiên một thông điệp ciphertext
có thể biến nó thành một bản rõ trông hợp lệ, khi đó việc giả mạo sẽ không bị người nhận phát hiện.
Mã hóa cũng không cung cấp xác thực. Không có nhiều ý nghĩa khi nói rằng một thông điệp đến từ một bên nào đó
nếu nội dung thông điệp đã bị sửa đổi sau khi bên đó tạo ra. Ở một khía cạnh nào đó, tính toàn vẹn và xác thực là không thể tách rời.

Một *bộ xác thực* (authenticator) là một giá trị được đưa vào thông điệp truyền đi,
có thể dùng để kiểm tra đồng thời tính xác thực và toàn vẹn dữ liệu của thông điệp.
Chúng ta sẽ thấy cách sử dụng bộ xác thực trong các giao thức. Hiện tại, chúng ta tập trung vào các thuật toán tạo ra bộ xác thực.

Bạn có thể nhớ rằng checksum và kiểm tra dư thừa tuần hoàn (CRC) là các thông tin bổ sung vào thông điệp
để người nhận phát hiện khi thông điệp bị sửa đổi do lỗi bit. Một khái niệm tương tự áp dụng cho bộ xác thực,
với thách thức bổ sung là việc làm sai lệch thông điệp có thể được thực hiện có chủ đích bởi ai đó muốn việc đó không bị phát hiện.
Để hỗ trợ xác thực, bộ xác thực bao gồm một bằng chứng rằng người tạo ra bộ xác thực biết một bí mật chỉ người gửi thực sự mới biết;
ví dụ, bí mật đó có thể là một khóa, và bằng chứng có thể là một giá trị được mã hóa bằng khóa đó.
Có sự phụ thuộc lẫn nhau giữa dạng thông tin dư thừa và dạng bằng chứng về kiến thức bí mật.
Chúng tôi sẽ thảo luận một số kết hợp khả thi.

Ban đầu, chúng tôi giả định rằng thông điệp gốc không cần bảo mật—tức là thông điệp truyền đi sẽ gồm bản rõ của thông điệp gốc cộng với bộ xác thực.
Sau này chúng tôi sẽ xem xét trường hợp cần bảo mật.

Một loại bộ xác thực kết hợp mã hóa và *hàm băm mật mã* (cryptographic hash function).
Các thuật toán băm mật mã được coi là kiến thức công khai, giống như các thuật toán mã hóa.
Hàm băm mật mã (còn gọi là *checksum mật mã*) là một hàm xuất ra đủ thông tin dư thừa về một thông điệp để phát hiện bất kỳ sự giả mạo nào.
Cũng như checksum hoặc CRC phát hiện lỗi bit do đường truyền nhiễu, checksum mật mã được thiết kế để phát hiện việc cố ý làm sai lệch thông điệp bởi kẻ tấn công.
Giá trị mà nó xuất ra gọi là *message digest* (bản tóm tắt thông điệp) và, giống như checksum thông thường, được nối vào thông điệp.
Tất cả các bản tóm tắt thông điệp do một hàm băm nhất định tạo ra đều có cùng số bit bất kể độ dài thông điệp gốc.
Vì không gian các thông điệp đầu vào lớn hơn không gian các bản tóm tắt, sẽ có các thông điệp đầu vào khác nhau tạo ra cùng một bản tóm tắt,
giống như va chạm (collision) trong bảng băm.

Một bộ xác thực có thể được tạo ra bằng cách mã hóa bản tóm tắt thông điệp. Người nhận tính toán bản tóm tắt của phần bản rõ của thông điệp
và so sánh với bản tóm tắt đã giải mã. Nếu chúng bằng nhau, người nhận có thể kết luận rằng thông điệp thực sự đến từ người gửi dự kiến
(vì nó phải được mã hóa bằng đúng khóa) và không bị giả mạo. Không kẻ tấn công nào có thể gửi một thông điệp giả với bản tóm tắt giả khớp
vì họ không có khóa để mã hóa bản tóm tắt giả đúng cách. Tuy nhiên, kẻ tấn công có thể lấy được thông điệp gốc bản rõ và bản tóm tắt đã mã hóa
bằng cách nghe lén. Sau đó, vì hàm băm là kiến thức công khai, họ có thể tính bản tóm tắt của thông điệp gốc và tạo ra các thông điệp thay thế
để tìm một thông điệp có cùng bản tóm tắt. Nếu tìm được, họ có thể gửi thông điệp mới với bộ xác thực cũ mà không bị phát hiện.
Do đó, bảo mật đòi hỏi hàm băm phải có tính *một chiều* (one-way): Phải bất khả thi về mặt tính toán để kẻ tấn công tìm được bất kỳ thông điệp bản rõ nào
có cùng bản tóm tắt với thông điệp gốc.

Để hàm băm đáp ứng yêu cầu này, đầu ra của nó phải được phân phối ngẫu nhiên.
Ví dụ, nếu bản tóm tắt dài 128 bit và được phân phối ngẫu nhiên, bạn sẽ phải thử trung bình 2\ :sup:`127` thông điệp
trước khi tìm được thông điệp thứ hai có bản tóm tắt trùng với một thông điệp cho trước.
Nếu đầu ra không phân phối ngẫu nhiên—tức là một số đầu ra có xác suất cao hơn các đầu ra khác—
thì với một số thông điệp, bạn có thể dễ dàng tìm được thông điệp khác có cùng bản tóm tắt hơn, làm giảm tính an toàn của thuật toán.
Nếu bạn chỉ cố tìm bất kỳ *va chạm* nào—bất kỳ hai thông điệp nào cho cùng bản tóm tắt—thì chỉ cần tính bản tóm tắt của 2\ :sup:`64` thông điệp, trung bình.
Sự thật bất ngờ này là cơ sở của “cuộc tấn công ngày sinh nhật”—xem bài tập để biết thêm chi tiết.

Đã có một số thuật toán băm mật mã phổ biến qua các năm, bao gồm Message Digest 5 (MD5) và họ Secure Hash Algorithm (SHA).
Các điểm yếu của MD5 và các phiên bản SHA trước đã được biết đến từ lâu, dẫn đến việc NIST phát triển và khuyến nghị họ thuật toán SHA-3 vào năm 2015.

Khi tạo bản tóm tắt thông điệp đã mã hóa, việc mã hóa bản tóm tắt có thể dùng mã hóa khóa bí mật hoặc mã hóa khóa công khai.
Nếu dùng mã hóa khóa công khai, bản tóm tắt sẽ được mã hóa bằng khóa riêng của người gửi (khóa mà ta thường nghĩ là dùng để giải mã),
và người nhận—hoặc bất kỳ ai khác—có thể giải mã bản tóm tắt bằng khóa công khai của người gửi.

Một bản tóm tắt được mã hóa bằng thuật toán khóa công khai nhưng dùng khóa riêng được gọi là *chữ ký số* (digital signature)
vì nó cung cấp tính không thể chối bỏ giống như chữ ký viết tay. Người nhận một thông điệp có chữ ký số
có thể chứng minh cho bất kỳ bên thứ ba nào rằng người gửi thực sự đã gửi thông điệp đó,
vì bên thứ ba có thể dùng khóa công khai của người gửi để tự kiểm tra. (Mã hóa khóa bí mật bản tóm tắt không có thuộc tính này
vì chỉ hai bên biết khóa; hơn nữa, vì cả hai bên đều biết khóa, người nhận cũng có thể tự tạo ra thông điệp.)
Bất kỳ thuật toán mã hóa khóa công khai nào cũng có thể dùng cho chữ ký số.
*Tiêu chuẩn Chữ ký Số* (DSS) là một định dạng chữ ký số đã được NIST chuẩn hóa.
Chữ ký DSS có thể dùng một trong ba thuật toán mã hóa khóa công khai, một dựa trên RSA, một dựa trên ElGamal,
và một thứ ba gọi là *Thuật toán Chữ ký Số Đường cong Elliptic* (Elliptic Curve Digital Signature Algorithm).

Một cách tiếp cận thay thế phổ biến cho việc mã hóa hàm băm là sử dụng một hàm băm nhận một giá trị bí mật
(một khóa chỉ người gửi và người nhận biết) làm tham số đầu vào ngoài văn bản thông điệp.
Hàm này xuất ra một mã xác thực thông điệp là hàm của cả khóa bí mật và nội dung thông điệp.
Người gửi nối mã xác thực tính được vào thông điệp bản rõ. Người nhận tính lại mã xác thực bằng bản rõ và giá trị bí mật
và so sánh mã vừa tính với mã nhận được trong thông điệp. Các cách phổ biến nhất để tạo mã này gọi là HMAC hoặc
mã xác thực thông điệp băm có khóa (keyed-hash message authentication code).

HMAC có thể dùng bất kỳ hàm băm nào như mô tả ở trên, nhưng nó cũng đưa khóa vào như một phần của dữ liệu cần băm,
nên HMAC là hàm của cả khóa và văn bản đầu vào. Một cách tính HMAC đã được NIST chuẩn hóa và có dạng sau:

HMAC = H((K⊕opad) || H((K⊕ipad) || text))

H là hàm băm, K là khóa, và opad (output pad) và ipad (input pad) là các chuỗi đã biết được XOR (⊕) với khóa. ||
là phép nối chuỗi.

Giải thích sâu về hàm HMAC này vượt quá phạm vi cuốn sách. Tuy nhiên, cách tiếp cận này đã được chứng minh là an toàn
miễn là hàm băm H có các thuộc tính chống va chạm như đã nêu ở trên. Lưu ý rằng HMAC nhận một hàm băm *H* không có khóa,
và biến nó thành hàm băm có khóa bằng cách dùng khóa (XOR với một chuỗi khác, *ipad*) làm khối đầu tiên đưa vào hàm băm.
Đầu ra của hàm băm có khóa sau đó lại được đưa vào một hàm băm có khóa khác (lại XOR khóa với một chuỗi và dùng làm khối đầu tiên).
Hai lần băm có khóa này quan trọng đối với chứng minh tính an toàn của cấu trúc HMAC này.

Cho đến lúc này, chúng tôi giả định rằng thông điệp không cần bảo mật, nên thông điệp gốc có thể truyền dưới dạng bản rõ.
Để thêm bảo mật cho thông điệp có mã xác thực, chỉ cần mã hóa toàn bộ thông điệp bao gồm cả mã xác thực.
Hãy nhớ rằng, trên thực tế, bảo mật được thực hiện bằng mã hóa khóa bí mật vì nó nhanh hơn nhiều so với mã hóa khóa công khai.
Hơn nữa, việc đưa mã xác thực vào mã hóa không tốn kém nhiều, lại tăng tính an toàn.

Những năm gần đây, ý tưởng sử dụng một thuật toán duy nhất để hỗ trợ cả xác thực và mã hóa ngày càng được ủng hộ
vì lý do hiệu năng và đơn giản hóa triển khai. Điều này được gọi là *mã hóa xác thực* (authenticated encryption)
hoặc *mã hóa xác thực với dữ liệu liên kết* (authenticated encryption with associated data).
Thuật ngữ sau cho phép một số trường dữ liệu (ví dụ, tiêu đề gói tin) được truyền dưới dạng bản rõ—đó là dữ liệu liên kết—
trong khi phần còn lại của thông điệp được mã hóa, và toàn bộ, kể cả tiêu đề, đều được xác thực.
Chúng tôi sẽ không đi sâu vào chi tiết ở đây, nhưng hiện đã có một tập hợp các thuật toán tích hợp
tạo ra cả ciphertext và mã xác thực bằng cách kết hợp các thuật toán mã hóa và hàm băm.

Mặc dù bộ xác thực có vẻ như giải quyết được vấn đề xác thực, chúng ta sẽ thấy ở phần sau rằng chúng chỉ là nền tảng của một giải pháp.
Trước hết, chúng ta sẽ giải quyết vấn đề làm thế nào các thành viên có được khóa ngay từ đầu.
