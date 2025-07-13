7.1 Định dạng trình bày
=======================

Một trong những phép biến đổi phổ biến nhất của dữ liệu mạng là chuyển đổi từ biểu diễn mà chương trình ứng dụng sử dụng sang một dạng phù hợp để truyền qua mạng và *ngược lại*. Phép biến đổi này thường được gọi là *định dạng trình bày* (presentation formatting). Như minh họa ở :numref:`Hình %s <fig-marshal1>`, chương trình gửi sẽ chuyển đổi dữ liệu mà nó muốn truyền từ biểu diễn nội bộ sang một thông điệp có thể truyền qua mạng; tức là, dữ liệu được *mã hóa* vào thông điệp. Ở phía nhận, ứng dụng sẽ chuyển đổi thông điệp nhận được thành một biểu diễn mà nó có thể xử lý; tức là, thông điệp được *giải mã*. Quá trình này đôi khi còn gọi là *đóng gói đối số* (argument marshalling) hoặc *tuần tự hóa* (serialization). Thuật ngữ này xuất phát từ thế giới Gọi thủ tục từ xa (RPC), nơi phía khách nghĩ rằng nó đang gọi một thủ tục với một tập các đối số, nhưng các đối số này sau đó được “gom lại và sắp xếp theo cách phù hợp và hiệu quả” để tạo thành một thông điệp mạng.

.. _fig-marshal1:
.. figure:: figures/f07-01-9780123850591.png
   :width: 400px
   :align: center

   Định dạng trình bày liên quan đến việc mã hóa và giải mã dữ liệu ứng dụng.

Bạn có thể hỏi điều gì làm cho vấn đề này trở nên thách thức. Một lý do là các máy tính biểu diễn dữ liệu theo những cách khác nhau. Ví dụ, một số máy tính biểu diễn số thực dấu phẩy động theo chuẩn IEEE 754, trong khi một số máy cũ vẫn sử dụng định dạng không chuẩn riêng. Ngay cả với những thứ đơn giản như số nguyên, các kiến trúc khác nhau sử dụng các kích thước khác nhau (ví dụ: 16-bit, 32-bit, 64-bit). Tệ hơn nữa, trên một số máy, số nguyên được biểu diễn theo dạng *big-endian* (bit quan trọng nhất của một từ—“đầu lớn”—nằm ở byte có địa chỉ thấp nhất), trong khi trên các máy khác, số nguyên được biểu diễn theo dạng *little-endian* (bit ít quan trọng nhất—“đầu nhỏ”—nằm ở byte có địa chỉ thấp nhất). Ví dụ, bộ xử lý PowerPC là máy big-endian, còn họ Intel x86 là kiến trúc little-endian. Ngày nay, nhiều kiến trúc (ví dụ: ARM) hỗ trợ cả hai cách biểu diễn (và do đó gọi là *bi-endian*), nhưng vấn đề là bạn không bao giờ chắc chắn máy mà bạn giao tiếp lưu trữ số nguyên như thế nào. Biểu diễn big-endian và little-endian của số nguyên 34.677.374 được minh họa ở :numref:`Hình %s <fig-endian>`.

.. _fig-endian:
.. figure:: figures/f07-02-9780123850591.png
   :width: 500px
   :align: center

   Thứ tự byte big-endian và little-endian cho số nguyên 34.677.374

Một lý do khác khiến đóng gói đối số trở nên khó khăn là các chương trình ứng dụng được viết bằng các ngôn ngữ khác nhau, và ngay cả khi bạn dùng một ngôn ngữ thì có thể có nhiều trình biên dịch khác nhau. Ví dụ, các trình biên dịch có khá nhiều quyền tự do trong cách bố trí các cấu trúc (record) trong bộ nhớ, như việc chèn bao nhiêu padding giữa các trường tạo nên cấu trúc. Do đó, bạn không thể đơn giản truyền một cấu trúc từ máy này sang máy khác, ngay cả khi cả hai máy cùng kiến trúc và chương trình được viết cùng một ngôn ngữ, vì trình biên dịch trên máy đích có thể căn chỉnh các trường trong cấu trúc khác đi.

7.1.1 Phân loại
---------------

Mặc dù đóng gói đối số không phải là khoa học tên lửa—chỉ là vấn đề thao tác bit—nhưng có một số lượng đáng ngạc nhiên các lựa chọn thiết kế mà bạn phải giải quyết. Chúng ta bắt đầu bằng cách đưa ra một phân loại đơn giản cho các hệ thống đóng gói đối số. Đây không phải là phân loại duy nhất khả thi, nhưng đủ để bao quát hầu hết các lựa chọn thú vị.

Kiểu dữ liệu
~~~~~~~~~~~~

Câu hỏi đầu tiên là hệ thống sẽ hỗ trợ những kiểu dữ liệu nào. Nói chung, chúng ta có thể phân loại các kiểu được hỗ trợ bởi một cơ chế đóng gói đối số ở ba cấp độ. Mỗi cấp độ làm phức tạp thêm nhiệm vụ của hệ thống đóng gói.

Ở cấp thấp nhất, một hệ thống đóng gói hoạt động trên một tập các *kiểu cơ bản*. Thông thường, các kiểu cơ bản bao gồm số nguyên, số thực dấu phẩy động và ký tự. Hệ thống cũng có thể hỗ trợ kiểu thứ tự (ordinal) và Boolean. Như đã mô tả ở trên, ý nghĩa của tập kiểu cơ bản là quá trình mã hóa phải có khả năng chuyển đổi mỗi kiểu cơ bản từ một biểu diễn sang biểu diễn khác—ví dụ, chuyển đổi số nguyên từ big-endian sang little-endian.

Ở cấp tiếp theo là các *kiểu phẳng*—cấu trúc và mảng. Mặc dù ban đầu các kiểu phẳng có vẻ không làm phức tạp đóng gói đối số, thực tế là có. Vấn đề là các trình biên dịch dùng để biên dịch chương trình ứng dụng đôi khi chèn padding giữa các trường tạo nên cấu trúc để căn chỉnh các trường này theo biên từ. Hệ thống đóng gói thường sẽ *đóng gói* (pack) các cấu trúc sao cho chúng không có padding.

Ở cấp cao nhất, hệ thống đóng gói có thể phải xử lý các *kiểu phức tạp*—những kiểu được xây dựng bằng con trỏ. Tức là, cấu trúc dữ liệu mà một chương trình muốn gửi cho chương trình khác có thể không nằm gọn trong một cấu trúc, mà có thể liên quan đến các con trỏ từ cấu trúc này sang cấu trúc khác. Một cây là ví dụ điển hình về kiểu phức tạp liên quan đến con trỏ. Rõ ràng, bộ mã hóa dữ liệu phải chuẩn bị cấu trúc dữ liệu để truyền qua mạng vì con trỏ được cài đặt bằng địa chỉ bộ nhớ, và chỉ vì một cấu trúc nằm ở một địa chỉ bộ nhớ nhất định trên một máy không có nghĩa là nó sẽ nằm ở cùng địa chỉ đó trên máy khác. Nói cách khác, hệ thống đóng gói phải *tuần tự hóa* (serialize) các cấu trúc dữ liệu phức tạp.

Tóm lại, tùy vào mức độ phức tạp của hệ kiểu, nhiệm vụ đóng gói đối số thường bao gồm chuyển đổi các kiểu cơ bản, đóng gói các cấu trúc, và tuyến tính hóa các cấu trúc dữ liệu phức tạp, tất cả để tạo thành một thông điệp liên tục có thể truyền qua mạng. :numref:`Hình %s <fig-marshal2>` minh họa nhiệm vụ này.

.. _fig-marshal2:
.. figure:: figures/f07-03-9780123850591.png
   :width: 400px
   :align: center

   Đóng gói đối số: chuyển đổi, đóng gói, và tuyến tính hóa

Chiến lược chuyển đổi
~~~~~~~~~~~~~~~~~~~~~

Khi hệ kiểu đã được xác định, vấn đề tiếp theo là hệ thống đóng gói đối số sẽ sử dụng chiến lược chuyển đổi nào. Có hai lựa chọn chung: *dạng trung gian chuẩn* (canonical intermediate form) và *bên nhận tự xử lý* (receiver-makes-right). Chúng ta sẽ lần lượt xem xét từng cái.

Ý tưởng của dạng trung gian chuẩn là thống nhất một biểu diễn ngoài cho mỗi kiểu; máy gửi chuyển đổi từ biểu diễn nội bộ sang biểu diễn ngoài này trước khi gửi dữ liệu, và máy nhận chuyển đổi từ biểu diễn ngoài này sang biểu diễn cục bộ khi nhận dữ liệu. Để minh họa ý tưởng, hãy xét dữ liệu số nguyên; các kiểu khác cũng được xử lý tương tự. Bạn có thể quy định rằng định dạng big-endian sẽ được dùng làm biểu diễn ngoài cho số nguyên. Máy gửi phải chuyển đổi mỗi số nguyên nó gửi sang dạng big-endian, và máy nhận phải chuyển đổi số nguyên big-endian sang biểu diễn mà nó sử dụng. (Đây là cách làm trong Internet cho các tiêu đề giao thức.) Tất nhiên, một máy có thể đã dùng dạng big-endian, khi đó không cần chuyển đổi.

Lựa chọn thay thế, bên nhận tự xử lý, là máy gửi truyền dữ liệu theo định dạng nội bộ của nó; máy gửi không chuyển đổi các kiểu cơ bản, nhưng thường phải đóng gói và tuyến tính hóa các cấu trúc dữ liệu phức tạp hơn. Máy nhận sau đó chịu trách nhiệm chuyển đổi dữ liệu từ định dạng của máy gửi sang định dạng cục bộ của nó. Vấn đề với chiến lược này là mỗi máy phải sẵn sàng chuyển đổi dữ liệu từ tất cả các kiến trúc máy khác. Trong mạng, điều này gọi là giải pháp *N nhân N*: Mỗi trong N kiến trúc máy phải có khả năng xử lý tất cả N kiến trúc. Ngược lại, trong hệ thống dùng dạng trung gian chuẩn, mỗi máy chỉ cần biết cách chuyển đổi giữa biểu diễn của nó và một biểu diễn khác—biểu diễn ngoài.

Rõ ràng, dùng một định dạng ngoài chung là điều đúng đắn, phải không? Điều này chắc chắn là quan điểm truyền thống trong cộng đồng mạng suốt hơn 30 năm qua. Tuy nhiên, câu trả lời không hoàn toàn rõ ràng. Thực tế là không có quá nhiều biểu diễn khác nhau cho các lớp kiểu cơ bản, hay nói cách khác, N không quá lớn. Ngoài ra, trường hợp phổ biến nhất là hai máy cùng loại giao tiếp với nhau. Trong tình huống này, có vẻ ngớ ngẩn khi chuyển đổi dữ liệu từ biểu diễn của kiến trúc đó sang một biểu diễn ngoài xa lạ, chỉ để rồi lại phải chuyển đổi dữ liệu về biểu diễn của cùng kiến trúc đó ở phía nhận.

Một lựa chọn thứ ba, dù chúng tôi chưa biết hệ thống nào hiện tại sử dụng, là dùng bên nhận tự xử lý nếu máy gửi biết đích có cùng kiến trúc; máy gửi sẽ dùng dạng trung gian chuẩn nếu hai máy dùng kiến trúc khác nhau. Làm sao máy gửi biết kiến trúc của máy nhận? Nó có thể biết thông tin này từ một máy chủ tên hoặc bằng cách thử một trường hợp đơn giản để xem kết quả có đúng không.

Thẻ (Tags)
~~~~~~~~~~

Vấn đề thứ ba trong đóng gói đối số là làm sao phía nhận biết loại dữ liệu nào có trong thông điệp nó nhận được. Có hai cách tiếp cận phổ biến: dữ liệu *có thẻ* (tagged) và *không thẻ* (untagged). Cách có thẻ trực quan hơn, nên chúng ta mô tả trước.

Một thẻ là bất kỳ thông tin bổ sung nào được đưa vào thông điệp—ngoài biểu diễn cụ thể của các kiểu cơ bản—giúp phía nhận giải mã thông điệp. Có một số loại thẻ có thể được đưa vào thông điệp. Ví dụ, mỗi mục dữ liệu có thể được bổ sung một thẻ *kiểu*. Thẻ kiểu cho biết giá trị tiếp theo là số nguyên, số thực dấu phẩy động, hay gì khác. Một ví dụ khác là thẻ *độ dài*. Thẻ này dùng để chỉ số phần tử trong một mảng hoặc kích thước của một số nguyên. Một ví dụ thứ ba là thẻ *kiến trúc*, có thể dùng cùng với chiến lược bên nhận tự xử lý để chỉ rõ kiến trúc mà dữ liệu trong thông điệp được tạo ra. :numref:`Hình %s <fig-tags>` minh họa cách một số nguyên 32-bit có thể được mã hóa trong một thông điệp có thẻ.

.. _fig-tags:
.. figure:: figures/f07-04-9780123850591.png
   :width: 400px
   :align: center

   Một số nguyên 32-bit được mã hóa trong thông điệp có thẻ.

Lựa chọn thay thế, tất nhiên, là không dùng thẻ. Làm sao phía nhận biết cách giải mã dữ liệu trong trường hợp này? Nó biết vì nó được lập trình để biết. Nói cách khác, nếu bạn gọi một thủ tục từ xa nhận hai số nguyên và một số thực dấu phẩy động làm đối số, thì không có lý do gì để thủ tục từ xa phải kiểm tra thẻ để biết nó vừa nhận gì. Nó chỉ đơn giản giả định rằng thông điệp chứa hai số nguyên và một số thực, và giải mã tương ứng. Lưu ý rằng, mặc dù cách này phù hợp với hầu hết trường hợp, nhưng sẽ gặp vấn đề khi gửi mảng có độ dài thay đổi. Trong trường hợp này, thường dùng thẻ độ dài để chỉ mảng dài bao nhiêu.

Cũng cần lưu ý rằng cách không thẻ nghĩa là định dạng trình bày thực sự là đầu-cuối. Không thể có một tác nhân trung gian nào diễn giải thông điệp trừ khi dữ liệu có thẻ. Tại sao một tác nhân trung gian lại cần diễn giải thông điệp, bạn có thể hỏi? Đã từng có những trường hợp như vậy, chủ yếu do các giải pháp *ad hoc* cho các vấn đề bất ngờ mà hệ thống không được thiết kế để xử lý. Thiết kế mạng kém nằm ngoài phạm vi cuốn sách này.

Stub
~~~~

Stub là đoạn mã thực hiện đóng gói đối số. Stub thường được dùng để hỗ trợ RPC. Ở phía khách, stub đóng gói các đối số thủ tục thành một thông điệp có thể truyền bằng giao thức RPC. Ở phía máy chủ, stub chuyển đổi thông điệp trở lại thành một tập biến có thể dùng làm đối số để gọi thủ tục từ xa. Stub có thể là dạng thông dịch hoặc biên dịch.

Với cách tiếp cận dựa trên biên dịch, mỗi thủ tục có một stub khách và stub máy chủ tùy chỉnh. Mặc dù có thể viết stub thủ công, chúng thường được sinh tự động bởi một trình biên dịch stub, dựa trên mô tả giao diện thủ tục. Tình huống này được minh họa ở :numref:`Hình %s <fig-stubs>`. Vì stub được biên dịch, nó thường rất hiệu quả. Với cách tiếp cận dựa trên thông dịch, hệ thống cung cấp các stub khách và máy chủ tổng quát, các tham số của chúng được thiết lập bởi mô tả giao diện thủ tục. Vì dễ thay đổi mô tả này, stub thông dịch có ưu điểm là linh hoạt. Tuy nhiên, stub biên dịch phổ biến hơn trong thực tế.

.. _fig-stubs:
.. figure:: figures/f07-05-9780123850591.png
   :width: 500px
   :align: center

   Trình biên dịch stub nhận mô tả giao diện làm đầu vào và xuất ra stub khách và máy chủ.

7.1.2 Ví dụ (XDR, ASN.1, NDR, ProtoBufs)
----------------------------------------

Chúng ta sẽ mô tả ngắn gọn bốn biểu diễn dữ liệu mạng phổ biến theo phân loại này. Chúng ta dùng kiểu cơ bản số nguyên để minh họa cách mỗi hệ thống hoạt động.

XDR
~~~

External Data Representation (XDR) là định dạng mạng dùng với SunRPC. Theo phân loại vừa giới thiệu, XDR

-  Hỗ trợ toàn bộ hệ kiểu C, trừ con trỏ hàm

-  Định nghĩa một dạng trung gian chuẩn

-  Không dùng thẻ (trừ khi chỉ độ dài mảng)

-  Dùng stub biên dịch

Một số nguyên XDR là một mục dữ liệu 32-bit mã hóa một số nguyên C. Nó được biểu diễn theo dạng bù hai, với byte quan trọng nhất của số nguyên C nằm ở byte đầu tiên của số nguyên XDR và byte ít quan trọng nhất nằm ở byte thứ tư. Tức là, XDR dùng định dạng big-endian cho số nguyên. XDR hỗ trợ cả số nguyên có dấu và không dấu, giống như C.

XDR biểu diễn mảng có độ dài thay đổi bằng cách đầu tiên chỉ định một số nguyên không dấu (4 byte) cho biết số phần tử trong mảng, sau đó là từng phần tử của kiểu phù hợp. XDR mã hóa các thành phần của một cấu trúc theo thứ tự khai báo trong cấu trúc. Với cả mảng và cấu trúc, kích thước mỗi phần tử/thành phần được biểu diễn theo bội số của 4 byte. Các kiểu dữ liệu nhỏ hơn được padding thành 4 byte bằng các số 0. Ngoại lệ cho quy tắc “padding thành 4 byte” là ký tự, được mã hóa một ký tự trên mỗi byte.

.. _fig-xdr:
.. figure:: figures/f07-06-9780123850591.png
   :width: 500px
   :align: center

   Ví dụ mã hóa một cấu trúc trong XDR.

Đoạn mã sau là ví dụ về một cấu trúc C (``item``) và hàm XDR mã hóa/giải mã cấu trúc này (``xdr_item``). :numref:`Hình %s <fig-xdr>` mô tả sơ đồ biểu diễn trên đường truyền của cấu trúc này khi trường ``name`` dài bảy ký tự và mảng ``list`` có ba giá trị.

Trong ví dụ này, ``xdr_array``, ``xdr_int`` và ``xdr_string`` là ba hàm nguyên thủy do XDR cung cấp để mã hóa và giải mã mảng, số nguyên và chuỗi ký tự. Đối số ``xdrs`` là biến ngữ cảnh mà XDR dùng để theo dõi vị trí hiện tại trong thông điệp đang xử lý; nó bao gồm một cờ cho biết hàm này đang được dùng để mã hóa hay giải mã thông điệp. Nói cách khác, các hàm như ``xdr_item`` được dùng cả ở phía khách và phía máy chủ. Lưu ý rằng lập trình viên ứng dụng có thể tự viết hàm ``xdr_item`` hoặc dùng trình biên dịch stub gọi là ``rpcgen`` (không hiển thị ở đây) để sinh hàm mã hóa/giải mã này. Trong trường hợp sau, ``rpcgen`` nhận thủ tục từ xa định nghĩa cấu trúc dữ liệu ``item`` làm đầu vào và xuất ra stub tương ứng.

.. code-block:: c

   #define MAXNAME 256;
   #define MAXLIST 100;

   struct item {
      int     count;
      char    name[MAXNAME];
      int     list[MAXLIST];
   };

   bool_t
   xdr_item(XDR *xdrs, struct item *ptr)
   {
       return(xdr_int(xdrs, &ptr->count) &&
          xdr_string(xdrs, &ptr->name, MAXNAME) &&
          xdr_array(xdrs, &ptr->list, &ptr->count, MAXLIST,
                    sizeof(int), xdr_int));
   }

Chính xác XDR thực hiện như thế nào tất nhiên phụ thuộc vào độ phức tạp của dữ liệu. Trong trường hợp đơn giản là mảng số nguyên, mỗi số nguyên phải chuyển đổi từ một thứ tự byte sang thứ tự khác, trung bình cần ba lệnh cho mỗi byte, nghĩa là chuyển đổi toàn bộ mảng có thể bị giới hạn bởi băng thông bộ nhớ của máy. Các chuyển đổi phức tạp hơn đòi hỏi nhiều lệnh trên mỗi byte sẽ bị giới hạn bởi CPU và do đó tốc độ thấp hơn băng thông bộ nhớ.

ASN.1
~~~~~

Abstract Syntax Notation One (ASN.1) là một chuẩn ISO định nghĩa, trong số những thứ khác, một biểu diễn cho dữ liệu gửi qua mạng. Phần đặc tả biểu diễn của ASN.1 gọi là *Quy tắc mã hóa cơ bản* (Basic Encoding Rules, BER). ASN.1 hỗ trợ hệ kiểu C không có con trỏ hàm, định nghĩa một dạng trung gian chuẩn, và dùng thẻ kiểu. Stub của nó có thể là thông dịch hoặc biên dịch. Một điểm nổi bật của ASN.1 BER là nó được dùng bởi giao thức chuẩn Internet SNMP.

ASN.1 biểu diễn mỗi mục dữ liệu bằng một bộ ba dạng

::

   (tag, length, value)

``tag`` thường là trường 8-bit, mặc dù ASN.1 cho phép định nghĩa thẻ nhiều byte. Trường ``length`` chỉ số byte tạo nên ``value``; chúng ta sẽ bàn thêm về ``length`` bên dưới. Các kiểu dữ liệu phức hợp, như cấu trúc, có thể được xây dựng bằng cách lồng các kiểu nguyên thủy, như minh họa ở :numref:`Hình %s <fig-ber1>`.

.. _fig-ber1:
.. figure:: figures/f07-07-9780123850591.png
   :width: 600px
   :align: center

   Kiểu phức hợp tạo bằng lồng nhau trong ASN.1 BER.

.. _fig-ber2:
.. figure:: figures/f07-08-9780123850591.png
   :width: 400px
   :align: center

   Biểu diễn ASN.1 BER cho một số nguyên 4 byte.

Nếu ``value`` dài 127 byte hoặc ít hơn, thì ``length`` được chỉ định bằng một byte. Ví dụ, một số nguyên 32-bit được mã hóa thành một byte ``type``, một byte ``length``, và 4 byte mã hóa số nguyên, như minh họa ở :numref:`Hình %s <fig-ber2>`. Bản thân ``value``, trong trường hợp số nguyên, được biểu diễn theo dạng bù hai và big-endian, giống như XDR. Lưu ý rằng, mặc dù ``value`` của số nguyên được biểu diễn giống hệt nhau trong XDR và ASN.1, biểu diễn XDR không có thẻ ``type`` hay ``length`` đi kèm số nguyên đó. Hai thẻ này vừa chiếm chỗ trong thông điệp, vừa đòi hỏi xử lý khi đóng gói và giải gói. Đây là một lý do tại sao ASN.1 không hiệu quả bằng XDR. Một lý do khác là việc mỗi giá trị dữ liệu đều có trường ``length`` đi trước nghĩa là giá trị dữ liệu khó có thể rơi vào biên byte tự nhiên (ví dụ, số nguyên bắt đầu ở biên từ). Điều này làm phức tạp quá trình mã hóa/giải mã.

Nếu ``value`` dài từ 128 byte trở lên, thì nhiều byte được dùng để chỉ ``length``. Lúc này bạn có thể hỏi tại sao một byte chỉ độ dài tối đa 127 byte thay vì 256. Lý do là 1 bit của trường ``length`` dùng để chỉ độ dài của trường ``length``. Bit thứ 8 bằng 0 nghĩa là trường ``length`` dài 1 byte. Để chỉ độ dài lớn hơn, bit thứ 8 được đặt là 1, và 7 bit còn lại chỉ số byte bổ sung tạo nên trường ``length``. :numref:`Hình %s <fig-ber3>` minh họa một trường hợp ``length`` 1 byte và một trường hợp nhiều byte.

.. _fig-ber3:
.. figure:: figures/f07-09-9780123850591.png
   :width: 400px
   :align: center

   Biểu diễn ASN.1 BER cho độ dài: (a) 1 byte; (b) nhiều byte.

NDR
~~~

Network Data Representation (NDR) là chuẩn mã hóa dữ liệu dùng trong Môi trường Tính toán Phân tán (DCE). Khác với XDR và ASN.1, NDR dùng chiến lược bên nhận tự xử lý. Nó làm điều này bằng cách chèn một thẻ kiến trúc ở đầu mỗi thông điệp; các mục dữ liệu riêng lẻ không có thẻ. NDR dùng trình biên dịch để sinh stub. Trình biên dịch này nhận mô tả chương trình viết bằng Ngôn ngữ Định nghĩa Giao diện (IDL) và sinh các stub cần thiết. IDL trông khá giống C, nên về cơ bản hỗ trợ hệ kiểu C.

.. _fig-ndr:
.. figure:: figures/f07-10-9780123850591.png
   :width: 600px
   :align: center

   Thẻ kiến trúc của NDR.

:numref:`Hình %s <fig-ndr>` minh họa thẻ định nghĩa kiến trúc 4 byte được chèn ở đầu mỗi thông điệp mã hóa theo NDR. Byte đầu tiên chứa hai trường 4-bit. Trường đầu, ``IntegrRep``, xác định định dạng cho tất cả số nguyên trong thông điệp. 0 nghĩa là số nguyên big-endian, 1 nghĩa là little-endian. Trường ``CharRep`` chỉ định định dạng ký tự: 0 là ASCII, 1 là EBCDIC (một chuẩn cũ của IBM thay thế cho ASCII). Tiếp theo, byte ``FloatRep`` xác định định dạng số thực dấu phẩy động: 0 là IEEE 754, 1 là VAX, 2 là Cray, 3 là IBM. Hai byte cuối dành cho sử dụng trong tương lai. Lưu ý rằng, trong các trường hợp đơn giản như mảng số nguyên, NDR làm việc tương đương XDR, nên đạt hiệu năng tương đương.

ProtoBufs
~~~~~~~~~

Protocol Buffers (ProtoBufs) cung cấp một cách tuần tự hóa dữ liệu có cấu trúc độc lập ngôn ngữ và nền tảng, thường dùng với gRPC. Chúng dùng chiến lược có thẻ với dạng trung gian chuẩn, trong đó stub ở cả hai phía được sinh từ một file ``.proto`` chung. Đặc tả này dùng cú pháp đơn giản giống C, như ví dụ sau:

.. code-block:: c

   message Person {
       required string name = 1;
       required int32 id = 2;
       optional string email = 3;

       enum PhoneType {
           MOBILE = 0;
           HOME = 1;
           WORK = 2;
       }

       message PhoneNumber {
           required string number = 1;
           optional PhoneType type = 2 [default = HOME];
       }

       required PhoneNumber phone = 4;
   }

trong đó ``message`` có thể hiểu tương đương với ``typedef struct`` trong C. Phần còn lại khá trực quan, ngoại trừ việc mỗi trường được gán một định danh số để đảm bảo duy nhất nếu đặc tả thay đổi theo thời gian, và mỗi trường có thể được chú thích là ``required`` hoặc ``optional``.

Cách ProtoBufs mã hóa số nguyên là mới lạ. Chúng dùng kỹ thuật gọi là *varints* (số nguyên độ dài biến đổi), trong đó mỗi byte 8-bit dùng bit quan trọng nhất để chỉ còn byte nào nữa trong số nguyên không, và 7 bit thấp hơn để mã hóa biểu diễn bù hai của nhóm 7 bit tiếp theo trong giá trị. Nhóm ít quan trọng nhất đứng trước trong chuỗi tuần tự hóa.

Điều này nghĩa là một số nguyên nhỏ (dưới 128) có thể được mã hóa trong một byte (ví dụ, số 2 được mã hóa là ``0000 0010``), còn số nguyên lớn hơn 128 cần nhiều byte hơn. Ví dụ, 365 sẽ được mã hóa là

::

   1110 1101 0000 0010

Để thấy điều này, đầu tiên bỏ bit quan trọng nhất ở mỗi byte, vì nó dùng để báo đã hết số nguyên chưa. Trong ví dụ này, ``1`` ở bit quan trọng nhất của byte đầu cho biết còn nhiều hơn một byte trong varint:

::

   1110 1101 0000 0010
   → 110 1101  000 0010

Vì varint lưu số với nhóm ít quan trọng nhất trước, bạn đảo ngược hai nhóm 7 bit. Sau đó nối chúng lại để được giá trị cuối cùng:

::

   000 0010  110 1101
   →  000 0010 || 110 1101
   →  101101101
   →  256 + 64 + 32 + 8 + 4 + 1 = 365

Với đặc tả thông điệp lớn hơn, bạn có thể coi chuỗi byte tuần tự hóa là tập hợp các cặp khóa/giá trị, trong đó khóa (tức là tag) có hai phần: định danh duy nhất cho trường (tức là các số trong file ``.proto``) và *kiểu truyền* (wire type) của giá trị (ví dụ, ``Varint`` là một kiểu truyền đã thấy). Các kiểu truyền khác gồm ``32-bit`` và ``64-bit`` (cho số nguyên độ dài cố định), và ``length-delimited`` (cho chuỗi và thông điệp lồng nhau). Kiểu cuối cùng cho biết thông điệp lồng nhau dài bao nhiêu byte, nhưng chính đặc tả ``message`` trong file ``.proto`` mới cho biết cách diễn giải các byte đó.

7.1.3 Ngôn ngữ đánh dấu (XML)
-----------------------------

Mặc dù chúng ta đã bàn về vấn đề định dạng trình bày từ góc nhìn RPC—tức là, làm sao mã hóa kiểu dữ liệu nguyên thủy và cấu trúc dữ liệu phức hợp để gửi từ chương trình khách đến chương trình máy chủ—vấn đề cơ bản này cũng xuất hiện ở các bối cảnh khác. Ví dụ, làm sao một máy chủ web mô tả một trang web để bất kỳ trình duyệt nào cũng biết hiển thị gì trên màn hình? Trong trường hợp này, câu trả lời là HyperText Markup Language (HTML), chỉ định rằng một số chuỗi ký tự nên được hiển thị đậm hoặc nghiêng, dùng font và cỡ nào, và vị trí hình ảnh ở đâu.

Sự xuất hiện của đủ loại ứng dụng và dữ liệu web cũng tạo ra tình huống trong đó các ứng dụng web khác nhau cần giao tiếp và hiểu dữ liệu của nhau. Ví dụ, một trang thương mại điện tử có thể cần nói chuyện với trang của công ty vận chuyển để khách hàng tra cứu đơn hàng mà không rời khỏi trang thương mại điện tử. Điều này nhanh chóng trở nên giống RPC, và cách tiếp cận hiện nay trên web để cho phép các máy chủ web giao tiếp như vậy dựa trên *Ngôn ngữ đánh dấu mở rộng* (XML)—một cách để mô tả dữ liệu được trao đổi giữa các ứng dụng web.

Các ngôn ngữ đánh dấu, mà HTML và XML đều là ví dụ, đẩy cách tiếp cận dữ liệu có thẻ lên mức cực đoan. Dữ liệu được biểu diễn dưới dạng văn bản, và các thẻ văn bản gọi là *markup* được xen kẽ với dữ liệu để biểu đạt thông tin về dữ liệu. Với HTML, markup chỉ cách hiển thị văn bản; các ngôn ngữ đánh dấu khác như XML có thể biểu đạt kiểu và cấu trúc dữ liệu.

XML thực chất là một khung để định nghĩa các ngôn ngữ đánh dấu khác nhau cho các loại dữ liệu khác nhau. Ví dụ, XML đã được dùng để định nghĩa một ngôn ngữ đánh dấu gần tương đương với HTML gọi là *Extensible HyperText Markup Language* (XHTML). XML định nghĩa cú pháp cơ bản để trộn markup với dữ liệu, nhưng người thiết kế ngôn ngữ đánh dấu cụ thể phải đặt tên và định nghĩa markup của nó. Thông thường, người ta gọi các ngôn ngữ dựa trên XML đơn giản là XML, nhưng ở đây chúng ta sẽ nhấn mạnh sự khác biệt này.

Cú pháp XML trông rất giống HTML. Ví dụ, một bản ghi nhân viên trong một ngôn ngữ dựa trên XML giả định có thể trông như tài liệu XML sau, có thể lưu trong file ``employee.xml``. Dòng đầu chỉ phiên bản XML, các dòng còn lại là bốn trường tạo nên bản ghi nhân viên, trường cuối (``hiredate``) chứa ba trường con. Nói cách khác, cú pháp XML cho phép cấu trúc lồng nhau của các cặp thẻ/giá trị, tương đương với cấu trúc cây cho dữ liệu biểu diễn (với ``employee`` là gốc). Điều này giống khả năng của XDR, ASN.1 và NDR trong việc biểu diễn kiểu phức hợp, nhưng ở định dạng vừa có thể xử lý bằng chương trình vừa đọc được bởi con người. Quan trọng hơn, các chương trình như parser có thể dùng cho nhiều ngôn ngữ dựa trên XML khác nhau, vì định nghĩa các ngôn ngữ đó cũng được biểu diễn dưới dạng dữ liệu máy có thể đọc được để nhập vào chương trình.

.. code:: xml

   <?xml version="1.0"?>
   <employee>
      <name>John Doe</name>
      <title>Head Bottle Washer</title>
      <id>123456789</id>
      <hiredate>
         <day>5</day>
         <month>June</month>
         <year>1986</year>
      </hiredate>
   </employee>

Mặc dù markup và dữ liệu trong tài liệu này rất gợi ý cho người đọc, chính định nghĩa ngôn ngữ bản ghi nhân viên mới quyết định thẻ nào hợp lệ, ý nghĩa của chúng là gì, và chúng ngụ ý kiểu dữ liệu nào. Nếu không có định nghĩa chính thức về các thẻ, người đọc (hoặc máy tính) không thể biết ``1986`` trong trường ``year`` là chuỗi, số nguyên, số nguyên không dấu hay số thực dấu phẩy động.

Định nghĩa một ngôn ngữ dựa trên XML cụ thể được cho bởi một *schema* (lược đồ), là thuật ngữ cơ sở dữ liệu chỉ đặc tả cách diễn giải một tập dữ liệu. Có nhiều ngôn ngữ schema cho XML; ở đây chúng ta tập trung vào chuẩn chính, gọi là *XML Schema*. Một schema cá nhân định nghĩa bằng XML Schema gọi là *Tài liệu XML Schema* (XSD). Sau đây là một đặc tả XSD cho ví dụ trên; tức là, nó định nghĩa ngôn ngữ mà tài liệu ví dụ tuân theo. Nó có thể lưu trong file ``employee.xsd``.

.. code:: xml

   <?xml version="1.0"?>
   <schema xmlns="http://www.w3.org/2001/XMLSchema">
     <element name="employee">
       <complexType>
         <sequence>
           <element name="name" type="string"/>
           <element name="title" type="string"/>
           <element name="id" type="string"/>
           <element name="hiredate">
             <complexType>
               <sequence>
                 <element name="day" type="integer"/>
                 <element name="month" type="string"/>
                 <element name="year" type="integer"/>
               </sequence>
             </complexType>
           </element>
         </sequence>
       </complexType>
     </element>
   </schema>

XSD này trông khá giống tài liệu ví dụ ``employee.xml``, và điều đó có lý do: XML Schema bản thân là một ngôn ngữ dựa trên XML. Có mối liên hệ rõ ràng giữa XSD này và tài liệu định nghĩa ở trên. Ví dụ,

.. code:: xml

   <element name="title" type="string"/>

chỉ ra rằng giá trị nằm giữa thẻ ``title`` sẽ được diễn giải là chuỗi. Thứ tự và lồng nhau của dòng này trong XSD chỉ rằng trường ``title`` phải là mục thứ hai trong bản ghi nhân viên.

Khác với một số ngôn ngữ schema, XML Schema cung cấp các kiểu dữ liệu như string, integer, decimal và Boolean. Nó cho phép kết hợp các kiểu dữ liệu thành chuỗi hoặc lồng nhau, như trong ``employee.xsd``, để tạo kiểu dữ liệu phức hợp. Vậy một XSD định nghĩa nhiều hơn cú pháp; nó định nghĩa mô hình dữ liệu trừu tượng của riêng nó. Một tài liệu tuân theo XSD đại diện cho một tập dữ liệu tuân theo mô hình dữ liệu đó.

Ý nghĩa của việc XSD định nghĩa mô hình dữ liệu trừu tượng chứ không chỉ cú pháp là có thể có các cách khác ngoài XML để biểu diễn dữ liệu tuân theo mô hình đó. Và XML thực ra có một số hạn chế khi dùng làm biểu diễn trên đường truyền: nó không gọn như các biểu diễn dữ liệu khác, và khá chậm để phân tích cú pháp. Có một số biểu diễn thay thế dạng nhị phân đang được sử dụng. Tổ chức Tiêu chuẩn Quốc tế (ISO) đã công bố một chuẩn gọi là *Fast Infoset*, còn World Wide Web Consortium (W3C) đưa ra đề xuất *Efficient XML Interchange* (EXI). Các biểu diễn nhị phân hy sinh khả năng đọc của con người để đổi lấy độ gọn và tốc độ phân tích cú pháp cao hơn.

Không gian tên XML
~~~~~~~~~~~~~~~~~~

XML phải giải quyết một vấn đề phổ biến, đó là xung đột tên. Vấn đề phát sinh vì các ngôn ngữ schema như XML Schema hỗ trợ tính mô-đun ở chỗ một schema có thể được tái sử dụng như một phần của schema khác. Giả sử hai XSD được định nghĩa độc lập, và cả hai đều định nghĩa tên markup *idNumber*. Có thể một XSD dùng tên đó để nhận diện nhân viên công ty, còn XSD kia dùng để nhận diện máy tính xách tay của công ty. Chúng ta có thể muốn tái sử dụng hai XSD đó trong một XSD thứ ba để mô tả tài sản nào gắn với nhân viên nào, nhưng để làm vậy cần một cơ chế phân biệt idNumber của nhân viên với idNumber của laptop.

Giải pháp của XML cho vấn đề này là *không gian tên XML* (XML namespaces). Một không gian tên là một tập hợp các tên. Mỗi không gian tên XML được nhận diện bằng một Uniform Resource Identifier (URI). URI sẽ được mô tả chi tiết ở chương sau; hiện tại, bạn chỉ cần biết URI là một dạng định danh toàn cục duy nhất. (Một HTTP URL là một loại URI cụ thể.) Một tên markup đơn giản như *idNumber* có thể được thêm vào một không gian tên miễn là nó là duy nhất trong không gian tên đó. Vì không gian tên là duy nhất toàn cục và tên đơn giản là duy nhất trong không gian tên, kết hợp hai cái sẽ tạo thành một *tên đủ điều kiện* duy nhất toàn cục, không thể xung đột.

Một XSD thường chỉ định *không gian tên đích* bằng một dòng như sau:

.. code:: xml

   targetNamespace="http://www.example.com/employee"

là một Uniform Resource Identifier, nhận diện một không gian tên giả định. Tất cả markup mới định nghĩa trong XSD đó sẽ thuộc về không gian tên đó.

Bây giờ, nếu một XSD muốn tham chiếu các tên đã được định nghĩa trong XSD khác, nó có thể làm vậy bằng cách gán tiền tố không gian tên cho các tên đó. Tiền tố này là viết tắt ngắn cho URI đầy đủ thực sự nhận diện không gian tên. Ví dụ, dòng sau gán ``emp`` làm tiền tố không gian tên cho không gian tên nhân viên:

.. code:: xml

   xmlns:emp="http://www.example.com/employee"

Bất kỳ markup nào từ không gian tên đó sẽ được định danh bằng cách thêm tiền tố ``emp:`` , như ``title`` trong dòng sau:

.. code:: xml

   <emp:title>Head Bottle Washer</emp:title>

Nói cách khác, ``emp:title`` là một tên đủ điều kiện, sẽ không xung đột với tên ``title`` từ không gian tên khác.

Thật đáng chú ý là XML hiện được sử dụng rộng rãi trong các ứng dụng từ giao tiếp kiểu RPC giữa các dịch vụ web đến công cụ văn phòng và nhắn tin tức thời. Nó chắc chắn là một trong những giao thức cốt lõi mà các tầng trên của Internet hiện nay phụ thuộc vào.
