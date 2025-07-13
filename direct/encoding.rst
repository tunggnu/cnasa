2.2 Mã hóa
==========

Bước đầu tiên để biến các nút và liên kết thành các khối xây dựng hữu ích là hiểu cách kết nối chúng sao cho các bit có thể được truyền từ một nút này sang nút khác. Như đã đề cập ở phần trước, tín hiệu lan truyền qua các liên kết vật lý. Do đó, nhiệm vụ là mã hóa dữ liệu nhị phân mà nút nguồn muốn gửi thành các tín hiệu mà liên kết có thể mang, sau đó giải mã tín hiệu đó trở lại dữ liệu nhị phân tương ứng tại nút nhận. Chúng ta bỏ qua các chi tiết về điều chế và giả định rằng chúng ta đang làm việc với hai tín hiệu rời rạc: cao và thấp. Trong thực tế, các tín hiệu này có thể tương ứng với hai mức điện áp khác nhau trên một liên kết dùng dây đồng, hai mức công suất khác nhau trên một liên kết quang, hoặc hai biên độ khác nhau trên một truyền dẫn vô tuyến.

Hầu hết các chức năng được thảo luận trong chương này đều do *bộ điều hợp mạng* thực hiện—một phần cứng kết nối một nút với một liên kết. Bộ điều hợp mạng chứa một thành phần tạo tín hiệu, thực sự mã hóa các bit thành tín hiệu ở nút gửi và giải mã tín hiệu thành bit ở nút nhận. Do đó, như minh họa trong :numref:`Hình %s <fig-node-link-1>`, tín hiệu truyền qua liên kết giữa hai thành phần tạo tín hiệu, và các bit truyền giữa các bộ điều hợp mạng.

.. _fig-node-link-1:
.. figure:: figures/f02-03-9780123850591.png
   :width: 500px
   :align: center

   Tín hiệu truyền giữa các thành phần tạo tín hiệu; bit truyền giữa các bộ điều hợp.

Hãy quay lại bài toán mã hóa các bit lên tín hiệu. Điều hiển nhiên là ánh xạ giá trị dữ liệu 1 thành tín hiệu cao và giá trị dữ liệu 0 thành tín hiệu thấp. Đây chính xác là ánh xạ được sử dụng bởi một sơ đồ mã hóa có tên, khá bí ẩn, là *non-return to zero* (NRZ). Ví dụ, :numref:`Hình %s <fig-nrz>` mô tả sơ đồ tín hiệu được mã hóa NRZ (bên dưới) tương ứng với việc truyền một chuỗi bit cụ thể (bên trên).

.. _fig-nrz:
.. figure:: figures/f02-04-9780123850591.png
   :width: 400px
   :align: center

   Mã hóa NRZ của một luồng bit.

Vấn đề với NRZ là một chuỗi nhiều số 1 liên tiếp sẽ khiến tín hiệu duy trì ở mức cao trên liên kết trong một khoảng thời gian dài; tương tự, nhiều số 0 liên tiếp sẽ khiến tín hiệu duy trì ở mức thấp trong thời gian dài. Có hai vấn đề cơ bản phát sinh từ các chuỗi dài các số 1 hoặc 0. Đầu tiên là nó dẫn đến một tình huống gọi là *baseline wander* (trôi đường cơ sở). Cụ thể, bộ thu giữ lại giá trị trung bình của tín hiệu mà nó đã nhận cho đến thời điểm đó và sử dụng giá trị trung bình này để phân biệt giữa tín hiệu thấp và cao. Bất cứ khi nào tín hiệu thấp hơn đáng kể so với giá trị trung bình này, bộ thu kết luận rằng nó vừa nhận được một 0; tương tự, một tín hiệu cao hơn đáng kể so với giá trị trung bình sẽ được hiểu là 1. Vấn đề, tất nhiên, là quá nhiều số 1 hoặc 0 liên tiếp sẽ làm thay đổi giá trị trung bình này, khiến việc phát hiện sự thay đổi đáng kể trong tín hiệu trở nên khó khăn hơn.

Vấn đề thứ hai là các chuyển đổi thường xuyên từ cao xuống thấp và ngược lại là cần thiết để cho phép *khôi phục xung nhịp* (clock recovery). Trực giác mà nói, vấn đề khôi phục xung nhịp là cả quá trình mã hóa và giải mã đều được điều khiển bởi một xung nhịp—mỗi chu kỳ xung nhịp, bộ phát truyền một bit và bộ thu thu nhận một bit. Xung nhịp của bộ phát và bộ thu phải được đồng bộ hóa chính xác để bộ thu có thể thu nhận đúng các bit mà bộ phát truyền đi. Nếu xung nhịp của bộ thu nhanh hơn hoặc chậm hơn một chút so với bộ phát, thì nó sẽ không giải mã đúng tín hiệu. Bạn có thể tưởng tượng gửi xung nhịp đến bộ thu qua một dây riêng, nhưng điều này thường bị tránh vì nó làm tăng gấp đôi chi phí dây dẫn. Thay vào đó, bộ thu lấy xung nhịp từ tín hiệu nhận được—quá trình khôi phục xung nhịp. Bất cứ khi nào tín hiệu thay đổi, chẳng hạn từ 1 sang 0 hoặc từ 0 sang 1, bộ thu biết đó là ranh giới của một chu kỳ xung nhịp, và nó có thể tự đồng bộ lại. Tuy nhiên, một khoảng thời gian dài không có chuyển đổi như vậy sẽ dẫn đến trôi xung nhịp. Do đó, việc khôi phục xung nhịp phụ thuộc vào việc có nhiều chuyển đổi trong tín hiệu, bất kể dữ liệu được gửi là gì.

Một cách tiếp cận giải quyết vấn đề này, gọi là *non-return to zero inverted* (NRZI), là bộ phát sẽ tạo ra một chuyển đổi từ tín hiệu hiện tại để mã hóa một số 1 và giữ nguyên tín hiệu hiện tại để mã hóa một số 0. Điều này giải quyết vấn đề các số 1 liên tiếp, nhưng rõ ràng không làm gì với các số 0 liên tiếp. NRZI được minh họa trong :numref:`Hình %s <fig-encode-all>`. Một phương án thay thế, gọi là *Manchester encoding*, thực hiện việc kết hợp xung nhịp với tín hiệu một cách rõ ràng hơn bằng cách truyền giá trị XOR giữa dữ liệu đã mã hóa NRZ và xung nhịp. (Hãy coi xung nhịp cục bộ như một tín hiệu nội bộ luân phiên giữa thấp và cao; một cặp thấp/cao được coi là một chu kỳ xung nhịp.) Manchester encoding cũng được minh họa trong :numref:`Hình %s <fig-encode-all>`. Lưu ý rằng Manchester encoding dẫn đến việc số 0 được mã hóa thành một chuyển đổi từ thấp lên cao và số 1 được mã hóa thành một chuyển đổi từ cao xuống thấp. Vì cả số 0 và số 1 đều dẫn đến một chuyển đổi trong tín hiệu, xung nhịp có thể được khôi phục hiệu quả tại bộ thu. (Cũng có một biến thể của Manchester encoding, gọi là *Differential Manchester*, trong đó số 1 được mã hóa bằng nửa đầu của tín hiệu giống với nửa sau của tín hiệu bit trước đó và số 0 được mã hóa bằng nửa đầu của tín hiệu đối nghịch với nửa sau của tín hiệu bit trước đó.)

.. _fig-encode-all:
.. figure:: figures/f02-05-9780123850591.png
   :width: 400px
   :align: center

   Các chiến lược mã hóa khác nhau.

Vấn đề với sơ đồ Manchester encoding là nó làm tăng gấp đôi tốc độ chuyển đổi tín hiệu trên liên kết, nghĩa là bộ thu chỉ có một nửa thời gian để phát hiện mỗi xung của tín hiệu. Tốc độ mà tín hiệu thay đổi được gọi là *baud rate* của liên kết. Trong trường hợp Manchester encoding, tốc độ bit là một nửa baud rate, nên mã hóa này chỉ hiệu quả 50%. Hãy nhớ rằng nếu bộ thu có thể theo kịp baud rate nhanh hơn mà Manchester encoding yêu cầu trong :numref:`Hình %s <fig-encode-all>`, thì cả NRZ và NRZI đều có thể truyền gấp đôi số bit trong cùng một khoảng thời gian.

Lưu ý rằng tốc độ bit không nhất thiết nhỏ hơn hoặc bằng baud rate, như Manchester encoding gợi ý. Nếu sơ đồ điều chế có thể sử dụng (và nhận biết) bốn tín hiệu khác nhau, thay vì chỉ hai (ví dụ, “cao” và “thấp”), thì có thể mã hóa hai bit vào mỗi chu kỳ xung nhịp, dẫn đến tốc độ bit gấp đôi baud rate. Tương tự, nếu có thể điều chế giữa tám tín hiệu khác nhau thì có thể truyền ba bit mỗi chu kỳ xung nhịp. Nói chung, cần lưu ý rằng chúng ta đã đơn giản hóa quá mức vấn đề điều chế, vốn phức tạp hơn nhiều so với việc chỉ truyền tín hiệu “cao” và “thấp”. Không hiếm gặp việc thay đổi kết hợp giữa pha và biên độ của tín hiệu, cho phép mã hóa 16 hoặc thậm chí 64 mẫu khác nhau (thường gọi là *symbol*) trong mỗi chu kỳ xung nhịp. *QAM (Quadrature Amplitude Modulation)* là một ví dụ được sử dụng rộng rãi cho sơ đồ điều chế như vậy.

Một phương pháp mã hóa cuối cùng mà chúng ta xét đến, gọi là *4B/5B*, cố gắng giải quyết sự kém hiệu quả của Manchester encoding mà không gặp phải vấn đề về các chuỗi tín hiệu cao hoặc thấp kéo dài. Ý tưởng của 4B/5B là chèn thêm các bit vào luồng bit để phá vỡ các chuỗi dài các số 0 hoặc 1. Cụ thể, cứ mỗi 4 bit dữ liệu thực sẽ được mã hóa thành một mã 5 bit rồi truyền tới bộ thu; do đó có tên gọi 4B/5B. Các mã 5 bit được chọn sao cho mỗi mã không có quá một số 0 ở đầu và không quá hai số 0 ở cuối. Do đó, khi truyền liên tiếp, không cặp mã 5 bit nào dẫn đến việc truyền quá ba số 0 liên tiếp. Các mã 5 bit thu được sau đó sẽ được truyền bằng mã hóa NRZI, giải thích vì sao mã này chỉ quan tâm đến các số 0 liên tiếp—NRZI đã giải quyết vấn đề các số 1 liên tiếp. Lưu ý rằng mã hóa 4B/5B đạt hiệu quả 80%.

.. _tab-4b5b:
.. table:: Mã hóa 4B/5B.
   :align: center
   :widths: auto

   +-----------------------+----------+
   | Ký hiệu Dữ liệu 4-bit | Mã 5-bit |
   +=======================+==========+
   | 0000                  | 11110    |
   +-----------------------+----------+
   | 0001                  | 01001    |
   +-----------------------+----------+
   | 0010                  | 10100    |
   +-----------------------+----------+
   | 0011                  | 10101    |
   +-----------------------+----------+
   | 0100                  | 01010    |
   +-----------------------+----------+
   | 0101                  | 01011    |
   +-----------------------+----------+
   | 0110                  | 01110    |
   +-----------------------+----------+
   | 0111                  | 01111    |
   +-----------------------+----------+
   | 1000                  | 10010    |
   +-----------------------+----------+
   | 1001                  | 10011    |
   +-----------------------+----------+
   | 1010                  | 10110    |
   +-----------------------+----------+
   | 1011                  | 10111    |
   +-----------------------+----------+
   | 1100                  | 11010    |
   +-----------------------+----------+
   | 1101                  | 11011    |
   +-----------------------+----------+
   | 1110                  | 11100    |
   +-----------------------+----------+
   | 1111                  | 11101    |
   +-----------------------+----------+

:numref:`Bảng %s <tab-4b5b>` liệt kê các mã 5 bit tương ứng với mỗi
ký hiệu dữ liệu 4 bit trong số 16 ký hiệu có thể có. Lưu ý rằng vì 5 bit đủ để mã hóa 32 mã khác nhau, và chúng ta chỉ dùng 16 mã này cho dữ liệu, nên còn lại 16 mã có thể dùng cho các mục đích khác. Trong số này, mã ``11111`` được dùng khi đường truyền ở trạng thái rỗi, mã ``00000`` tương ứng với khi đường truyền chết, và ``00100`` được hiểu là dừng. Trong 13 mã còn lại, 7 mã không hợp lệ vì vi phạm quy tắc “một số 0 ở đầu, hai số 0 ở cuối”, và 6 mã còn lại đại diện cho các ký hiệu điều khiển khác nhau. Một số giao thức đóng khung được mô tả ở các phần sau của chương này sử dụng các ký hiệu điều khiển này.
