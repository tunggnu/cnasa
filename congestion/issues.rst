6.1 Các vấn đề trong phân bổ tài nguyên
=======================================

Phân bổ tài nguyên và kiểm soát tắc nghẽn là những vấn đề phức tạp đã được nghiên cứu rất nhiều kể từ khi mạng đầu tiên được thiết kế. Đây vẫn là những lĩnh vực nghiên cứu sôi động. Một yếu tố khiến các vấn đề này trở nên phức tạp là chúng không chỉ giới hạn ở một tầng duy nhất trong hệ phân cấp giao thức. Phân bổ tài nguyên được thực hiện một phần trong các bộ định tuyến, chuyển mạch và liên kết bên trong mạng, và một phần trong giao thức truyền tải chạy trên các máy chủ đầu cuối. Các hệ thống đầu cuối có thể sử dụng các giao thức báo hiệu để truyền đạt yêu cầu tài nguyên của mình tới các nút mạng, các nút này sẽ phản hồi thông tin về khả năng cung cấp tài nguyên. Một trong những mục tiêu chính của chương này là xác định một khuôn khổ để hiểu các cơ chế này, cũng như cung cấp các chi tiết liên quan về một số cơ chế đại diện.

Chúng ta nên làm rõ thuật ngữ trước khi đi xa hơn. *Phân bổ tài nguyên* là quá trình mà các thành phần mạng cố gắng đáp ứng các nhu cầu cạnh tranh mà các ứng dụng đặt ra đối với tài nguyên mạng—chủ yếu là băng thông liên kết và không gian bộ đệm trong các bộ định tuyến hoặc chuyển mạch. Tất nhiên, sẽ thường xuyên không thể đáp ứng tất cả các nhu cầu, nghĩa là một số người dùng hoặc ứng dụng có thể nhận được ít tài nguyên mạng hơn họ mong muốn. Một phần của vấn đề phân bổ tài nguyên là quyết định khi nào nên từ chối và từ chối ai.

Chúng tôi sử dụng thuật ngữ *kiểm soát tắc nghẽn* để mô tả các nỗ lực của các nút mạng nhằm ngăn chặn hoặc phản ứng với các điều kiện quá tải. Vì tắc nghẽn nhìn chung là điều không tốt cho tất cả mọi người, việc đầu tiên cần làm là làm giảm tắc nghẽn, hoặc ngăn chặn nó ngay từ đầu. Điều này có thể đạt được đơn giản bằng cách thuyết phục một vài máy chủ dừng gửi, từ đó cải thiện tình hình cho những người khác. Tuy nhiên, phổ biến hơn là các cơ chế kiểm soát tắc nghẽn có một số khía cạnh công bằng—tức là, chúng cố gắng chia sẻ khó khăn giữa tất cả người dùng, thay vì gây khó khăn lớn cho một số ít. Do đó, chúng ta thấy rằng nhiều cơ chế kiểm soát tắc nghẽn có một số dạng phân bổ tài nguyên tích hợp trong chúng.

Cũng quan trọng để hiểu sự khác biệt giữa kiểm soát luồng và kiểm soát tắc nghẽn. Kiểm soát luồng liên quan đến việc ngăn một máy gửi nhanh vượt quá khả năng xử lý của máy nhận chậm. Ngược lại, kiểm soát tắc nghẽn nhằm giữ cho một tập hợp các máy gửi không gửi quá nhiều dữ liệu *vào mạng* do thiếu tài nguyên tại một điểm nào đó. Hai khái niệm này thường bị nhầm lẫn; như chúng ta sẽ thấy, chúng cũng chia sẻ một số cơ chế.

6.1.1 Mô hình mạng
------------------

Chúng ta bắt đầu bằng cách xác định ba đặc điểm nổi bật của kiến trúc mạng. Về cơ bản, đây là tóm tắt của những nội dung đã trình bày trong các chương trước có liên quan đến vấn đề phân bổ tài nguyên.

Mạng chuyển mạch gói
~~~~~~~~~~~~~~~~~~~~

Chúng ta xem xét việc phân bổ tài nguyên trong một mạng chuyển mạch gói (hoặc internet) bao gồm nhiều liên kết và chuyển mạch (hoặc bộ định tuyến). Vì hầu hết các cơ chế được mô tả trong chương này được thiết kế để sử dụng trên Internet, và do đó ban đầu được định nghĩa theo các bộ định tuyến thay vì chuyển mạch, chúng tôi sử dụng thuật ngữ *bộ định tuyến* xuyên suốt phần thảo luận. Vấn đề về cơ bản là giống nhau, dù là trên một mạng hay một liên mạng.

Trong môi trường như vậy, một nguồn gửi có thể có đủ dung lượng trên liên kết đầu ra ngay lập tức để gửi một gói, nhưng ở đâu đó giữa mạng, các gói của nó gặp phải một liên kết đang được sử dụng bởi nhiều nguồn lưu lượng khác nhau. :numref:`Hình %s <fig-congestion>` minh họa tình huống này—hai liên kết tốc độ cao đang cấp dữ liệu cho một liên kết tốc độ thấp. Điều này trái ngược với các mạng truy cập chia sẻ như Ethernet và mạng không dây, nơi nguồn gửi có thể trực tiếp quan sát lưu lượng trên mạng và quyết định có nên gửi gói hay không. Chúng ta đã thấy các thuật toán dùng để phân bổ băng thông trên các mạng truy cập chia sẻ (ví dụ, Ethernet và Wi-Fi). Các thuật toán kiểm soát truy cập này, ở một mức độ nào đó, tương tự như các thuật toán kiểm soát tắc nghẽn trong mạng chuyển mạch.

.. _key-congestion:
.. admonition:: Ý chính

   Lưu ý rằng kiểm soát tắc nghẽn là một vấn đề khác với định tuyến.
   Mặc dù đúng là một liên kết bị tắc nghẽn có thể được gán trọng số cạnh lớn bởi giao thức định tuyến, và do đó các bộ định tuyến sẽ định tuyến vòng qua nó, nhưng “định tuyến vòng” một liên kết tắc nghẽn thường không giải quyết được vấn đề tắc nghẽn. Để thấy điều này, chúng ta chỉ cần nhìn vào mạng đơn giản được mô tả trong :numref:`Hình %s <fig-congestion>`, nơi tất cả lưu lượng phải đi qua cùng một bộ định tuyến để đến đích. Mặc dù đây là một ví dụ cực đoan, nhưng việc có một bộ định tuyến mà không thể định tuyến vòng qua là điều phổ biến. Bộ định tuyến này có thể bị tắc nghẽn, và không có gì mà cơ chế định tuyến có thể làm được. Bộ định tuyến bị tắc nghẽn này đôi khi được gọi là *bottleneck* (nút cổ chai). :ref:`[Tiếp theo] <key-policy-mechanism>`

Luồng không kết nối
~~~~~~~~~~~~~~~~~~

Trong phần lớn thảo luận, chúng ta giả định rằng mạng về cơ bản là không kết nối, với bất kỳ dịch vụ hướng kết nối nào được triển khai trong giao thức truyền tải chạy trên các máy chủ đầu cuối. (Chúng tôi sẽ giải thích lý do dùng từ “về cơ bản” trong giây lát.) Đây chính là mô hình của Internet, nơi IP cung cấp dịch vụ chuyển phát datagram không kết nối và TCP triển khai một trừu tượng kết nối đầu-cuối. Lưu ý rằng giả định này không đúng với các mạng mạch ảo như ATM và X.25. Trong các mạng như vậy, một thông điệp thiết lập kết nối sẽ đi qua mạng khi một mạch được thiết lập. Thông điệp này sẽ đặt trước một tập hợp bộ đệm cho kết nối tại mỗi bộ định tuyến, từ đó cung cấp một dạng kiểm soát tắc nghẽn—một kết nối chỉ được thiết lập nếu đủ bộ đệm có thể được cấp phát cho nó tại mỗi bộ định tuyến. Hạn chế lớn nhất của cách tiếp cận này là nó dẫn đến việc sử dụng tài nguyên không hiệu quả—bộ đệm dành riêng cho một mạch cụ thể sẽ không sẵn sàng cho các lưu lượng khác sử dụng ngay cả khi mạch đó không sử dụng đến. Trọng tâm của chương này là các phương pháp phân bổ tài nguyên áp dụng trong một liên mạng, do đó chúng tôi chủ yếu tập trung vào các mạng không kết nối.

.. _fig-congestion:
.. figure:: figures/f06-01-9780123850591.png
   :width: 500px
   :align: center

   Một bộ định tuyến có thể trở thành nút cổ chai.

Chúng ta cần làm rõ thuật ngữ *không kết nối* vì việc phân loại mạng thành hai loại không kết nối hoặc hướng kết nối là hơi quá cứng nhắc; thực tế có một vùng xám ở giữa. Đặc biệt, giả định rằng tất cả các datagram đều hoàn toàn độc lập trong một mạng không kết nối là quá mạnh. Các datagram chắc chắn được chuyển mạch độc lập, nhưng thường thì một luồng datagram giữa một cặp máy chủ cụ thể sẽ đi qua một tập hợp bộ định tuyến nhất định. Ý tưởng về một *luồng*—một chuỗi các gói được gửi giữa một cặp nguồn/đích và đi qua cùng một tuyến đường trong mạng—là một trừu tượng quan trọng trong bối cảnh phân bổ tài nguyên; đây là khái niệm mà chúng ta sẽ sử dụng trong chương này.

Một điểm mạnh của trừu tượng luồng là luồng có thể được định nghĩa ở các mức độ chi tiết khác nhau. Ví dụ, một luồng có thể là giữa hai máy chủ (tức là có cùng địa chỉ nguồn/đích) hoặc giữa hai tiến trình (tức là có cùng cặp địa chỉ nguồn/đích và cổng). Trong trường hợp sau, một luồng về cơ bản giống như một kênh, như chúng ta đã sử dụng thuật ngữ này xuyên suốt cuốn sách. Lý do chúng tôi giới thiệu thuật ngữ mới là vì luồng có thể được các bộ định tuyến bên trong mạng nhìn thấy, trong khi kênh là một trừu tượng đầu-cuối. :numref:`Hình %s <fig-flow>` minh họa một số luồng đi qua một loạt bộ định tuyến.

.. _fig-flow:
.. figure:: figures/f06-02-9780123850591.png
   :width: 500px
   :align: center

   Nhiều luồng đi qua một tập hợp bộ định tuyến.

Vì nhiều gói liên quan đi qua mỗi bộ định tuyến, đôi khi hợp lý để duy trì một số thông tin trạng thái cho mỗi luồng, thông tin này có thể được sử dụng để đưa ra quyết định phân bổ tài nguyên cho các gói thuộc về luồng đó. Trạng thái này đôi khi được gọi là *soft state* (trạng thái mềm). Sự khác biệt chính giữa trạng thái mềm và trạng thái cứng là trạng thái mềm không nhất thiết phải được tạo ra và loại bỏ một cách rõ ràng bằng báo hiệu. Trạng thái mềm đại diện cho một điểm trung gian giữa một mạng hoàn toàn không kết nối mà không duy trì bất kỳ trạng thái nào tại các bộ định tuyến và một mạng hoàn toàn hướng kết nối mà duy trì trạng thái cứng tại các bộ định tuyến. Nói chung, hoạt động đúng của mạng không phụ thuộc vào việc có trạng thái mềm hay không (mỗi gói vẫn được định tuyến đúng mà không cần trạng thái này), nhưng khi một gói thuộc về một luồng mà bộ định tuyến hiện đang duy trì trạng thái mềm, thì bộ định tuyến sẽ xử lý gói đó tốt hơn.

Lưu ý rằng một luồng có thể được định nghĩa ngầm hoặc thiết lập rõ ràng. Trong trường hợp đầu tiên, mỗi bộ định tuyến sẽ quan sát các gói đang đi giữa cùng một cặp nguồn/đích—bộ định tuyến làm điều này bằng cách kiểm tra địa chỉ trong tiêu đề—và coi các gói này thuộc về cùng một luồng cho mục đích kiểm soát tắc nghẽn. Trong trường hợp thứ hai, nguồn gửi một thông điệp thiết lập luồng qua mạng, thông báo rằng một luồng các gói sắp bắt đầu. Mặc dù các luồng được thiết lập rõ ràng về mặt lý thuyết không khác gì một kết nối trong mạng hướng kết nối, chúng tôi nhấn mạnh trường hợp này vì, ngay cả khi được thiết lập rõ ràng, một luồng không ngụ ý bất kỳ ngữ nghĩa đầu-cuối nào và đặc biệt không ngụ ý việc truyền tải tin cậy và theo thứ tự của một mạch ảo. Nó chỉ tồn tại cho mục đích phân bổ tài nguyên. Chúng ta sẽ thấy các ví dụ về cả luồng ngầm và luồng rõ ràng trong chương này.

Mô hình dịch vụ
~~~~~~~~~~~~~~

Ở phần đầu chương này, chúng ta sẽ tập trung vào các cơ chế giả định mô hình dịch vụ best-effort của Internet. Với dịch vụ best-effort, tất cả các gói đều được xử lý gần như như nhau, các máy chủ đầu cuối không có cơ hội yêu cầu mạng cung cấp một số đảm bảo hoặc dịch vụ ưu tiên cho một số gói hoặc luồng nhất định. Việc xác định một mô hình dịch vụ hỗ trợ một số loại dịch vụ ưu tiên hoặc đảm bảo—ví dụ, đảm bảo băng thông cần thiết cho một luồng video—là chủ đề của một phần sau. Một mô hình dịch vụ như vậy được cho là cung cấp nhiều *chất lượng dịch vụ* (QoS). Như chúng ta sẽ thấy, thực tế có một phổ các khả năng, từ mô hình dịch vụ hoàn toàn best-effort đến mô hình trong đó các luồng riêng lẻ nhận được các đảm bảo định lượng về QoS. Một trong những thách thức lớn nhất là xác định một mô hình dịch vụ đáp ứng nhu cầu của nhiều loại ứng dụng khác nhau và thậm chí cho phép các ứng dụng sẽ được phát minh trong tương lai.

6.1.2 Phân loại
---------------

Có vô số cách mà các cơ chế phân bổ tài nguyên khác nhau, vì vậy việc tạo ra một hệ thống phân loại đầy đủ là một đề xuất khó khăn. Hiện tại, chúng tôi mô tả ba chiều mà theo đó các cơ chế phân bổ tài nguyên có thể được đặc trưng; các phân biệt tinh tế hơn sẽ được đề cập trong suốt chương này.

Trung tâm bộ định tuyến so với trung tâm máy chủ
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Các cơ chế phân bổ tài nguyên có thể được phân loại thành hai nhóm lớn: những cơ chế giải quyết vấn đề từ bên trong mạng (tức là tại các bộ định tuyến hoặc chuyển mạch) và những cơ chế giải quyết từ rìa mạng (tức là tại các máy chủ, có thể là bên trong giao thức truyền tải). Vì cả các bộ định tuyến bên trong mạng và các máy chủ ở rìa mạng đều tham gia vào việc phân bổ tài nguyên, vấn đề thực sự là gánh nặng chủ yếu thuộc về đâu.

Trong thiết kế trung tâm bộ định tuyến, mỗi bộ định tuyến chịu trách nhiệm quyết định khi nào các gói được chuyển tiếp và chọn các gói nào sẽ bị loại bỏ, cũng như thông báo cho các máy chủ đang tạo lưu lượng mạng biết số lượng gói mà họ được phép gửi. Trong thiết kế trung tâm máy chủ, các máy chủ đầu cuối quan sát điều kiện mạng (ví dụ, số lượng gói họ gửi thành công qua mạng) và điều chỉnh hành vi của mình cho phù hợp. Lưu ý rằng hai nhóm này không loại trừ lẫn nhau. Ví dụ, một mạng đặt gánh nặng chính cho việc quản lý tắc nghẽn lên các bộ định tuyến vẫn mong đợi các máy chủ đầu cuối tuân thủ bất kỳ thông điệp tư vấn nào mà bộ định tuyến gửi, trong khi các bộ định tuyến trong mạng sử dụng kiểm soát tắc nghẽn đầu-cuối vẫn có một số chính sách, dù đơn giản, để quyết định gói nào sẽ bị loại bỏ khi hàng đợi bị tràn.

Dựa trên đặt trước so với dựa trên phản hồi
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một cách khác để phân loại các cơ chế phân bổ tài nguyên là dựa vào việc chúng sử dụng *đặt trước* hay *phản hồi*. Trong hệ thống dựa trên đặt trước, một thực thể nào đó (ví dụ, máy chủ đầu cuối) yêu cầu mạng cấp phát một lượng dung lượng nhất định cho một luồng. Mỗi bộ định tuyến sau đó sẽ cấp phát đủ tài nguyên (bộ đệm và/hoặc phần trăm băng thông liên kết) để đáp ứng yêu cầu này. Nếu yêu cầu không thể được đáp ứng tại một bộ định tuyến nào đó, vì làm như vậy sẽ vượt quá khả năng của nó, thì bộ định tuyến sẽ từ chối đặt trước. Điều này tương tự như nhận được tín hiệu bận khi cố gọi điện thoại. Trong cách tiếp cận dựa trên phản hồi, các máy chủ đầu cuối bắt đầu gửi dữ liệu mà không đặt trước bất kỳ dung lượng nào và sau đó điều chỉnh tốc độ gửi theo phản hồi mà họ nhận được. Phản hồi này có thể là *rõ ràng* (tức là, một bộ định tuyến bị tắc nghẽn gửi thông điệp “vui lòng giảm tốc độ” tới máy chủ) hoặc *ngầm định* (tức là, máy chủ đầu cuối điều chỉnh tốc độ gửi dựa trên hành vi quan sát được của mạng, chẳng hạn như mất gói).

Lưu ý rằng hệ thống dựa trên đặt trước luôn ngụ ý cơ chế phân bổ tài nguyên trung tâm bộ định tuyến. Điều này là vì mỗi bộ định tuyến chịu trách nhiệm theo dõi lượng tài nguyên hiện có và quyết định liệu có thể chấp nhận các đặt trước mới hay không. Các bộ định tuyến cũng có thể phải đảm bảo mỗi máy chủ tuân thủ đặt trước mà nó đã thực hiện. Nếu một máy chủ gửi dữ liệu nhanh hơn so với cam kết khi đặt trước, thì các gói của máy chủ đó là ứng viên tốt để loại bỏ nếu bộ định tuyến bị tắc nghẽn. Ngược lại, hệ thống dựa trên phản hồi có thể ngụ ý cơ chế trung tâm bộ định tuyến hoặc trung tâm máy chủ. Thông thường, nếu phản hồi là rõ ràng, thì bộ định tuyến tham gia ít nhất ở một mức độ nào đó vào sơ đồ phân bổ tài nguyên. Nếu phản hồi là ngầm định, thì gần như toàn bộ gánh nặng thuộc về máy chủ đầu cuối; các bộ định tuyến âm thầm loại bỏ gói khi bị tắc nghẽn.

Việc đặt trước không nhất thiết phải do máy chủ đầu cuối thực hiện. Quản trị viên mạng cũng có thể cấp phát tài nguyên cho các luồng hoặc cho các nhóm lưu lượng lớn hơn, như chúng ta sẽ thấy ở phần sau.

Dựa trên cửa sổ so với dựa trên tốc độ
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Một cách thứ ba để đặc trưng các cơ chế phân bổ tài nguyên là dựa vào việc chúng là *dựa trên cửa sổ* hay *dựa trên tốc độ*. Đây là một trong những lĩnh vực, như đã đề cập ở trên, nơi các cơ chế và thuật ngữ tương tự được sử dụng cho cả kiểm soát luồng và kiểm soát tắc nghẽn. Cả hai cơ chế kiểm soát luồng và phân bổ tài nguyên đều cần một cách để biểu thị cho máy gửi biết lượng dữ liệu mà nó được phép truyền. Có hai cách tổng quát để làm điều này: bằng *cửa sổ* hoặc bằng *tốc độ*. Chúng ta đã thấy các giao thức truyền tải dựa trên cửa sổ, như TCP, trong đó máy nhận quảng bá một cửa sổ cho máy gửi. Cửa sổ này tương ứng với lượng bộ đệm mà máy nhận có, và nó giới hạn lượng dữ liệu mà máy gửi có thể truyền; tức là, nó hỗ trợ kiểm soát luồng. Một cơ chế tương tự—quảng bá cửa sổ—có thể được sử dụng trong mạng để đặt trước bộ đệm (tức là, hỗ trợ phân bổ tài nguyên). Các cơ chế kiểm soát tắc nghẽn của TCP là dựa trên cửa sổ.

Cũng có thể kiểm soát hành vi của máy gửi bằng tốc độ—tức là, số bit mỗi giây mà máy nhận hoặc mạng có thể tiếp nhận. Kiểm soát dựa trên tốc độ có ý nghĩa đối với nhiều ứng dụng đa phương tiện, vốn thường tạo dữ liệu ở một tốc độ trung bình nào đó và cần ít nhất một mức thông lượng tối thiểu để hữu ích. Ví dụ, một bộ mã hóa video có thể tạo video ở tốc độ trung bình 1 Mbps với tốc độ đỉnh 2 Mbps. Như chúng ta sẽ thấy ở phần sau của chương này, đặc tả luồng dựa trên tốc độ là lựa chọn hợp lý trong hệ thống dựa trên đặt trước hỗ trợ các mức chất lượng dịch vụ khác nhau—máy gửi đặt trước một lượng bit mỗi giây, và mỗi bộ định tuyến trên đường đi xác định xem nó có thể hỗ trợ tốc độ đó hay không, dựa trên các luồng khác mà nó đã cam kết.

Tóm tắt phân loại phân bổ tài nguyên
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Việc phân loại các phương pháp phân bổ tài nguyên ở hai điểm khác nhau trên mỗi trong ba chiều như vừa trình bày dường như gợi ý có tới tám chiến lược riêng biệt. Mặc dù tám cách tiếp cận khác nhau chắc chắn là có thể, chúng tôi nhận thấy rằng trên thực tế hai chiến lược tổng quát là phổ biến nhất; hai chiến lược này gắn liền với mô hình dịch vụ cơ bản của mạng.

Một mặt, mô hình dịch vụ best-effort thường ngụ ý rằng phản hồi đang được sử dụng, vì mô hình này không cho phép người dùng đặt trước dung lượng mạng. Điều này, đến lượt nó, có nghĩa là phần lớn trách nhiệm kiểm soát tắc nghẽn thuộc về các máy chủ đầu cuối, có thể với một số hỗ trợ từ các bộ định tuyến. Trên thực tế, các mạng như vậy sử dụng thông tin dựa trên cửa sổ. Đây là chiến lược tổng quát được áp dụng trong Internet.

Mặt khác, mô hình dịch vụ dựa trên QoS có thể ngụ ý một số hình thức đặt trước. Hỗ trợ cho các đặt trước này có thể đòi hỏi sự tham gia đáng kể của bộ định tuyến, chẳng hạn như xếp hàng các gói khác nhau tùy thuộc vào mức tài nguyên đã đặt trước mà chúng yêu cầu. Hơn nữa, việc biểu diễn các đặt trước này bằng tốc độ là hợp lý, vì cửa sổ chỉ liên quan gián tiếp đến lượng băng thông mà người dùng cần từ mạng. Chúng ta sẽ thảo luận chủ đề này ở phần sau.

6.1.3 Tiêu chí đánh giá
-----------------------

Vấn đề cuối cùng là làm thế nào để biết một cơ chế phân bổ tài nguyên là tốt hay không. Hãy nhớ rằng trong phần phát biểu vấn đề ở đầu chương này, chúng tôi đã đặt ra câu hỏi làm thế nào để một mạng *hiệu quả* và *công bằng* phân bổ tài nguyên của nó. Điều này gợi ý ít nhất hai thước đo rộng để đánh giá một sơ đồ phân bổ tài nguyên. Chúng ta sẽ xem xét từng tiêu chí.

Phân bổ tài nguyên hiệu quả
~~~~~~~~~~~~~~~~~~~~~~~~~~

Một điểm khởi đầu tốt để đánh giá hiệu quả của một sơ đồ phân bổ tài nguyên là xem xét hai chỉ số chính của mạng: thông lượng và độ trễ. Rõ ràng, chúng ta muốn có càng nhiều thông lượng và càng ít độ trễ càng tốt. Thật không may, hai mục tiêu này thường mâu thuẫn với nhau. Một cách chắc chắn để một thuật toán phân bổ tài nguyên tăng thông lượng là cho phép càng nhiều gói vào mạng càng tốt, để đảm bảo tất cả các liên kết đều được sử dụng 100%. Chúng ta làm điều này để tránh khả năng một liên kết bị nhàn rỗi vì một liên kết nhàn rỗi chắc chắn làm giảm thông lượng. Vấn đề với chiến lược này là việc tăng số lượng gói trong mạng cũng làm tăng độ dài hàng đợi tại mỗi bộ định tuyến. Hàng đợi dài hơn đồng nghĩa với việc các gói bị trễ lâu hơn trong mạng.

Để mô tả mối quan hệ này, một số nhà thiết kế mạng đã đề xuất sử dụng tỷ số giữa thông lượng và độ trễ làm chỉ số đánh giá hiệu quả của sơ đồ phân bổ tài nguyên. Tỷ số này đôi khi được gọi là *công suất* của mạng:

::

   Công suất = Thông lượng / Độ trễ

Lưu ý rằng không rõ liệu công suất có phải là chỉ số phù hợp để đánh giá hiệu quả phân bổ tài nguyên hay không. Thứ nhất, lý thuyết về công suất dựa trên mạng hàng đợi M/M/1 giả định hàng đợi vô hạn;[#]_ trong khi mạng thực tế có bộ đệm hữu hạn và đôi khi phải loại bỏ gói. Thứ hai, công suất thường được định nghĩa đối với một kết nối (luồng) đơn lẻ; không rõ nó mở rộng như thế nào cho nhiều kết nối cạnh tranh. Tuy nhiên, bất chấp những hạn chế khá nghiêm trọng này, chưa có chỉ số thay thế nào được chấp nhận rộng rãi, vì vậy công suất vẫn tiếp tục được sử dụng.

.. [#] Vì đây không phải là sách về lý thuyết hàng đợi, chúng tôi chỉ cung cấp mô tả ngắn gọn về hàng đợi M/M/1. Số 1 nghĩa là có một máy phục vụ, và các chữ M nghĩa là phân phối thời gian đến và phục vụ gói là *Markov*, tức là phân phối mũ.

Mục tiêu là tối đa hóa tỷ số này, vốn là hàm của lượng tải đặt lên mạng. Tải, đến lượt nó, được thiết lập bởi cơ chế phân bổ tài nguyên. :numref:`Hình %s <fig-power>` cho thấy một đường cong công suất đại diện, nơi lý tưởng là cơ chế phân bổ tài nguyên sẽ hoạt động tại đỉnh của đường cong này. Bên trái đỉnh, cơ chế quá thận trọng; tức là, nó không cho phép đủ gói được gửi để giữ các liên kết luôn bận. Bên phải đỉnh, quá nhiều gói được cho phép vào mạng đến mức độ trễ tăng lên do xếp hàng bắt đầu lấn át bất kỳ lợi ích nhỏ nào về thông lượng.

Thật thú vị, đường cong công suất này trông rất giống đường cong thông lượng hệ thống trong một hệ thống máy tính chia sẻ thời gian. Thông lượng hệ thống tăng lên khi nhiều công việc được nhận vào hệ thống, cho đến khi đạt đến điểm có quá nhiều công việc đang chạy khiến hệ thống bắt đầu “thrash” (dành hết thời gian để hoán đổi trang bộ nhớ) và thông lượng bắt đầu giảm.

.. _fig-power:
.. figure:: figures/f06-03-9780123850591.png
   :width: 350px
   :align: center

   Tỷ số giữa thông lượng và độ trễ theo hàm của tải.

Như chúng ta sẽ thấy ở các phần sau của chương này, nhiều sơ đồ kiểm soát tắc nghẽn chỉ có thể kiểm soát tải theo những cách rất thô sơ; tức là, đơn giản là không thể điều chỉnh “núm vặn” một chút và chỉ cho phép thêm một số lượng nhỏ gói vào mạng. Do đó, các nhà thiết kế mạng cần quan tâm đến những gì xảy ra ngay cả khi hệ thống hoạt động dưới tải cực lớn—tức là, ở phía ngoài cùng bên phải của đường cong trong :numref:`Hình %s <fig-power>`. Lý tưởng nhất, chúng ta muốn tránh tình huống thông lượng hệ thống giảm về 0 vì hệ thống bị “thrash”. Trong thuật ngữ mạng, chúng ta muốn một hệ thống *ổn định*—nơi các gói vẫn tiếp tục đi qua mạng ngay cả khi mạng hoạt động dưới tải lớn. Nếu một cơ chế không ổn định, mạng có thể gặp phải *sụp đổ do tắc nghẽn*.

Phân bổ tài nguyên công bằng
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Việc sử dụng hiệu quả tài nguyên mạng không phải là tiêu chí duy nhất để đánh giá một sơ đồ phân bổ tài nguyên. Chúng ta cũng phải xem xét vấn đề công bằng. Tuy nhiên, chúng ta nhanh chóng gặp phải những vấn đề phức tạp khi cố gắng định nghĩa chính xác thế nào là phân bổ tài nguyên công bằng. Ví dụ, một sơ đồ phân bổ tài nguyên dựa trên đặt trước cung cấp một cách rõ ràng để tạo ra sự không công bằng có kiểm soát. Với sơ đồ như vậy, chúng ta có thể sử dụng đặt trước để cho phép một luồng video nhận được 1 Mbps trên một liên kết trong khi một truyền tệp chỉ nhận được 10 kbps trên cùng liên kết đó.

Trong trường hợp không có thông tin rõ ràng, khi nhiều luồng chia sẻ một liên kết cụ thể, chúng ta muốn mỗi luồng nhận được một phần băng thông bằng nhau. Định nghĩa này giả định rằng một phần *công bằng* của băng thông nghĩa là một phần *bằng nhau* của băng thông. Tuy nhiên, ngay cả khi không có đặt trước, các phần bằng nhau có thể không đồng nghĩa với các phần công bằng. Chúng ta có nên xem xét cả độ dài đường đi khi so sánh không? Ví dụ, như minh họa trong :numref:`Hình %s <fig-path-len>`, điều gì là công bằng khi một luồng đi qua bốn bước nhảy cạnh tranh với ba luồng đi qua một bước nhảy?

.. _fig-path-len:
.. figure:: figures/f06-04-9780123850591.png
   :width: 600px
   :align: center

   Một luồng đi qua bốn bước nhảy cạnh tranh với ba luồng đi qua một bước nhảy.

Giả sử rằng công bằng đồng nghĩa với bằng nhau và tất cả các đường đi đều có độ dài bằng nhau, nhà nghiên cứu mạng Raj Jain đã đề xuất một chỉ số có thể được sử dụng để định lượng mức độ công bằng của một cơ chế kiểm soát tắc nghẽn. Chỉ số công bằng của Jain được định nghĩa như sau. Cho một tập hợp thông lượng của các luồng

.. math::

   (x_{1}, x_{2}, \ldots , x_{n})

(được đo bằng các đơn vị nhất quán như bit/giây), hàm sau sẽ gán một chỉ số công bằng cho các luồng:

.. math::

   f(x_{1}, x_{2}, \ldots ,x_{n}) = \frac{( \sum_{i=1}^{n} x_{i}
   )^{2}} {n  \sum_{i=1}^{n} x_{i}^{2}}

Chỉ số công bằng luôn cho ra một số nằm trong khoảng từ 0 đến 1, với 1 là mức công bằng lớn nhất. Để hiểu trực giác đằng sau chỉ số này, hãy xem xét trường hợp tất cả *n* luồng đều nhận được thông lượng 1 đơn vị dữ liệu mỗi giây. Ta thấy rằng chỉ số công bằng trong trường hợp này là

.. math::

   \frac{n^2}{n \times n} = 1

Bây giờ, giả sử một luồng nhận được thông lượng :math:`1 + \Delta`.
Khi đó chỉ số công bằng là

.. math::

   \frac{((n - 1) + 1 + \Delta)^2}{n(n - 1 + (1 + \Delta)^2)}
   = \frac{n^2 + 2n\Delta + \Delta^2}{n^2 + 2n\Delta + n\Delta^2}

Lưu ý rằng mẫu số lớn hơn tử số một lượng :math:`(n-1)\Delta^2`.
Do đó, dù luồng lẻ ra nhận được nhiều hay ít hơn các luồng khác (tức là :math:`\Delta` dương hay âm), chỉ số công bằng đều giảm xuống dưới một. Một trường hợp đơn giản khác là chỉ có *k* trong số *n* luồng nhận được thông lượng bằng nhau, và *n-k* người dùng còn lại nhận được thông lượng bằng 0, khi đó chỉ số công bằng giảm xuống còn \ *k/n*.
