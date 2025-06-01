9.3 Ứng dụng hạ tầng
====================

Có một số giao thức là thiết yếu cho việc vận hành trơn tru của Internet nhưng lại không hoàn toàn phù hợp với mô hình phân tầng nghiêm ngặt. Một trong số đó là Hệ thống tên miền (DNS)—không phải là một ứng dụng mà người dùng thường trực tiếp sử dụng, mà là một dịch vụ mà hầu như tất cả các ứng dụng khác đều phụ thuộc vào. Điều này là do dịch vụ tên được dùng để chuyển đổi tên máy chủ thành địa chỉ máy chủ; sự tồn tại của một ứng dụng như vậy cho phép người dùng các ứng dụng khác tham chiếu tới các máy chủ từ xa bằng tên thay vì bằng địa chỉ. Nói cách khác, dịch vụ tên thường được các ứng dụng khác sử dụng, chứ không phải bởi con người.

Một chức năng quan trọng thứ hai là quản lý mạng, mặc dù không quen thuộc với người dùng phổ thông, nhưng lại được thực hiện chủ yếu bởi những người vận hành mạng thay mặt cho người dùng. Quản lý mạng được coi là một trong những vấn đề khó của mạng máy tính và tiếp tục là trọng tâm của nhiều đổi mới. Chúng ta sẽ xem xét một số vấn đề và cách tiếp cận dưới đây.

9.3.1 Dịch vụ tên (DNS)
-----------------------

Trong phần lớn cuốn sách này, chúng ta đã sử dụng địa chỉ để xác định các máy chủ. Mặc dù rất phù hợp để các bộ định tuyến xử lý, địa chỉ lại không thân thiện với người dùng. Vì lý do này, một *tên* duy nhất cũng thường được gán cho mỗi máy chủ trong mạng. Ngay trong phần này, chúng ta đã thấy các giao thức ứng dụng như HTTP sử dụng các tên như ``www.princeton.edu``. Bây giờ chúng ta sẽ mô tả cách một dịch vụ đặt tên có thể được phát triển để ánh xạ các tên thân thiện với người dùng thành các địa chỉ thân thiện với bộ định tuyến. Dịch vụ tên đôi khi được gọi là *middleware* (phần mềm trung gian) vì chúng lấp đầy khoảng trống giữa các ứng dụng và mạng bên dưới.

Tên máy chủ khác với địa chỉ máy chủ ở hai điểm quan trọng. Thứ nhất, chúng thường có độ dài thay đổi và dễ nhớ, do đó giúp con người dễ ghi nhớ hơn. (Ngược lại, các địa chỉ số có độ dài cố định thì dễ xử lý hơn đối với bộ định tuyến.) Thứ hai, tên thường không chứa thông tin nào giúp mạng xác định vị trí (định tuyến gói tin đến) máy chủ. Địa chỉ, ngược lại, đôi khi có thông tin định tuyến được nhúng trong đó; địa chỉ *phẳng* (không thể chia thành các phần thành phần) là ngoại lệ.

Trước khi đi vào chi tiết cách đặt tên cho các máy chủ trong mạng, trước tiên chúng ta giới thiệu một số thuật ngữ cơ bản. Đầu tiên, *không gian tên* (name space) xác định tập hợp các tên có thể có. Một không gian tên có thể là *phẳng* (tên không thể chia thành các thành phần) hoặc *phân cấp* (tên tệp Unix là một ví dụ rõ ràng). Thứ hai, hệ thống đặt tên duy trì một tập hợp các *liên kết* (binding) giữa tên và giá trị. Giá trị có thể là bất cứ thứ gì mà hệ thống đặt tên trả về khi được cung cấp một tên; trong nhiều trường hợp, đó là một địa chỉ. Cuối cùng, một *cơ chế phân giải* (resolution mechanism) là một thủ tục mà khi được gọi với một tên, sẽ trả về giá trị tương ứng. Một *máy chủ tên* (name server) là một hiện thực cụ thể của cơ chế phân giải, có thể truy cập qua mạng và có thể được truy vấn bằng cách gửi thông điệp.

Do quy mô lớn, Internet có một hệ thống đặt tên phát triển đặc biệt—Hệ thống tên miền (DNS). Do đó, chúng ta sử dụng DNS làm khuôn khổ để thảo luận về vấn đề đặt tên máy chủ. Lưu ý rằng Internet không phải lúc nào cũng sử dụng DNS. Trong giai đoạn đầu, khi chỉ có vài trăm máy chủ trên Internet, một cơ quan trung tâm gọi là *Network Information Center* (NIC) duy trì một bảng phẳng các liên kết tên-địa chỉ; bảng này được gọi là ``HOSTS.TXT``.\ [#]_ Bất cứ khi nào một site muốn thêm máy chủ mới vào Internet, quản trị viên site sẽ gửi email cho NIC cung cấp cặp tên/địa chỉ mới. Thông tin này được nhập thủ công vào bảng, bảng đã sửa đổi được gửi qua email tới các site vài ngày một lần, và quản trị viên hệ thống tại mỗi site sẽ cài đặt bảng này lên mọi máy chủ tại site. Việc phân giải tên khi đó chỉ đơn giản là một thủ tục tra cứu tên máy chủ trong bản sao bảng cục bộ và trả về địa chỉ tương ứng.

.. [#] Tin hay không tùy bạn, còn có cả một cuốn sách giấy (giống như danh bạ điện thoại) được xuất bản định kỳ liệt kê tất cả các máy kết nối Internet và tất cả những người có tài khoản email Internet.

Không có gì ngạc nhiên khi cách tiếp cận đặt tên này không hoạt động tốt khi số lượng máy chủ trên Internet bắt đầu tăng lên. Do đó, vào giữa những năm 1980, Hệ thống tên miền (DNS) đã được đưa vào sử dụng. DNS sử dụng không gian tên phân cấp thay vì không gian tên phẳng, và “bảng” các liên kết thực hiện không gian tên này được chia thành các phần rời rạc và phân phối trên toàn Internet. Các bảng con này được cung cấp trong các máy chủ tên có thể được truy vấn qua mạng.

Điều gì xảy ra trên Internet là người dùng cung cấp một tên máy chủ cho một chương trình ứng dụng (có thể được nhúng trong một tên phức hợp như địa chỉ email hoặc URL), và chương trình này sử dụng hệ thống đặt tên để chuyển đổi tên này thành địa chỉ máy chủ. Ứng dụng sau đó mở một kết nối tới máy chủ này bằng cách cung cấp một giao thức truyền tải nào đó (ví dụ, TCP) với địa chỉ IP của máy chủ. Tình huống này được minh họa (trong trường hợp gửi email) ở :numref:`Figure %s <fig-names>`. Mặc dù hình này khiến nhiệm vụ phân giải tên trông có vẻ đơn giản, nhưng thực tế còn phức tạp hơn một chút, như chúng ta sẽ thấy.

.. _fig-names:
.. figure:: figures/f09-14-9780123850591.png
   :width: 400px
   :align: center

   Tên được chuyển thành địa chỉ, các số từ 1 đến 5 cho thấy trình tự các bước trong quá trình.

Phân cấp miền
~~~~~~~~~~~~~

DNS thực hiện không gian tên phân cấp cho các đối tượng Internet. Không giống như tên tệp Unix, được xử lý từ trái sang phải với các thành phần tên được phân tách bằng dấu gạch chéo, tên DNS được xử lý từ phải sang trái và sử dụng dấu chấm làm ký tự phân tách. (Mặc dù được xử lý từ phải sang trái, con người vẫn đọc tên miền từ trái sang phải.) Một ví dụ về tên miền cho một máy chủ là ``cicada.cs.princeton.edu``. Lưu ý rằng chúng ta nói tên miền được dùng để đặt tên cho các “đối tượng” Internet. Ý chúng ta là DNS không chỉ dùng để ánh xạ tên máy chủ thành địa chỉ máy chủ. Chính xác hơn, DNS ánh xạ tên miền thành các giá trị. Tạm thời, chúng ta giả định các giá trị này là địa chỉ IP; chúng ta sẽ quay lại vấn đề này sau trong phần này.

.. _fig-domains:
.. figure:: figures/f09-15-9780123850591.png
   :width: 700px
   :align: center

   Ví dụ về phân cấp miền.

Giống như hệ thống tệp Unix, phân cấp DNS có thể được hình dung như một cây, trong đó mỗi nút trong cây tương ứng với một miền, và các lá trong cây tương ứng với các máy chủ được đặt tên. :numref:`Figure %s <fig-domains>` đưa ra một ví dụ về phân cấp miền. Lưu ý rằng chúng ta không nên gán bất kỳ ý nghĩa nào cho thuật ngữ *miền* ngoài việc nó đơn giản là một ngữ cảnh trong đó có thể định nghĩa thêm các tên khác.\ [#]_

.. [#] Gây nhầm lẫn là từ *domain* cũng được dùng trong định tuyến Internet, nơi nó có nghĩa khác với DNS, gần tương đương với thuật ngữ *autonomous system*.

Thực tế đã có rất nhiều tranh luận khi phân cấp tên miền lần đầu tiên được phát triển về những quy ước nào sẽ điều chỉnh các tên được cấp phát gần đỉnh của phân cấp. Không đi sâu vào chi tiết, hãy chú ý rằng phân cấp này không quá rộng ở cấp đầu tiên. Có các miền cho mỗi quốc gia, cộng với sáu miền lớn: ``.edu``, ``.com``, ``.gov``, ``.mil``, ``.org``, và ``.net``. Sáu miền này ban đầu đều có trụ sở tại Hoa Kỳ (nơi Internet và DNS được phát minh); ví dụ, chỉ các tổ chức giáo dục được công nhận tại Hoa Kỳ mới có thể đăng ký tên miền ``.edu``. Những năm gần đây, số lượng miền cấp cao nhất đã được mở rộng, một phần để đáp ứng nhu cầu cao về tên miền ``.com``. Các miền cấp cao mới bao gồm ``.biz``, ``.coop``, và ``.info``. Hiện nay đã có hơn 1200 miền cấp cao nhất.

Máy chủ tên
~~~~~~~~~~~

Toàn bộ phân cấp tên miền chỉ tồn tại ở dạng trừu tượng. Bây giờ chúng ta chuyển sang câu hỏi làm thế nào phân cấp này thực sự được triển khai. Bước đầu tiên là chia phân cấp thành các cây con gọi là *vùng* (zone). :numref:`Figure %s <fig-zones>` cho thấy cách phân cấp trong :numref:`Figure %s <fig-domains>` có thể được chia thành các vùng. Mỗi vùng có thể được coi là tương ứng với một cơ quan quản trị chịu trách nhiệm cho phần đó của phân cấp. Ví dụ, cấp cao nhất của phân cấp tạo thành một vùng do Tổ chức Internet Corporation for Assigned Names and Numbers (ICANN) quản lý. Bên dưới là một vùng tương ứng với Đại học Princeton. Trong vùng này, một số khoa không muốn chịu trách nhiệm quản lý phân cấp (và do đó vẫn nằm trong vùng cấp trường), trong khi các khoa khác, như Khoa Khoa học Máy tính, tự quản lý vùng cấp khoa của mình.

.. _fig-zones:
.. figure:: figures/f09-16-9780123850591.png
   :width: 700px
   :align: center

   Phân cấp miền được chia thành các vùng.

Ý nghĩa của một vùng là nó tương ứng với đơn vị cơ bản trong DNS—máy chủ tên. Cụ thể, thông tin chứa trong mỗi vùng được triển khai trên hai hoặc nhiều máy chủ tên. Mỗi máy chủ tên, đến lượt nó, là một chương trình có thể truy cập qua Internet. Khách hàng gửi truy vấn tới máy chủ tên, và máy chủ tên trả lời với thông tin được yêu cầu. Đôi khi phản hồi chứa câu trả lời cuối cùng mà khách hàng muốn, đôi khi phản hồi chứa một con trỏ tới máy chủ khác mà khách hàng nên truy vấn tiếp theo. Do đó, từ góc độ triển khai, chính xác hơn là nghĩ về DNS như được đại diện bởi một phân cấp các máy chủ tên thay vì một phân cấp các miền, như minh họa ở :numref:`Figure %s <fig-servers>`.

.. _fig-servers:
.. figure:: figures/f09-17-9780123850591.png
   :width: 500px
   :align: center

   Phân cấp các máy chủ tên.

Lưu ý rằng mỗi vùng được triển khai trên hai hoặc nhiều máy chủ tên để đảm bảo dự phòng; nghĩa là, thông tin vẫn có sẵn ngay cả khi một máy chủ tên bị lỗi. Ngược lại, một máy chủ tên có thể triển khai nhiều vùng.

Mỗi máy chủ tên triển khai thông tin vùng dưới dạng một tập hợp các *bản ghi tài nguyên* (resource record). Về bản chất, một bản ghi tài nguyên là một liên kết tên-giá trị, hoặc cụ thể hơn, là một bộ 5 trường sau:

::

   (Name, Value, Type, Class, TTL)

Trường ``Name`` và ``Value`` đúng như bạn mong đợi, trong khi trường ``Type`` chỉ định cách diễn giải trường ``Value``. Ví dụ, ``Type=A`` chỉ ra rằng ``Value`` là một địa chỉ IP. Như vậy, bản ghi ``A`` thực hiện ánh xạ tên-thành-địa chỉ mà chúng ta đã giả định. Các loại bản ghi khác bao gồm:

-  ``NS``—Trường ``Value`` cung cấp tên miền cho một máy chủ đang chạy dịch vụ tên, biết cách phân giải các tên trong miền được chỉ định.

-  ``CNAME``—Trường ``Value`` cung cấp tên chính thức (canonical name) cho một máy chủ cụ thể; dùng để định nghĩa bí danh.

-  ``MX``—Trường ``Value`` cung cấp tên miền cho một máy chủ đang chạy dịch vụ thư điện tử nhận thư cho miền được chỉ định.

Trường ``Class`` được đưa vào để cho phép các thực thể ngoài NIC định nghĩa các loại bản ghi hữu ích. Cho đến nay, ``Class`` được sử dụng rộng rãi nhất là của Internet; được ký hiệu là ``IN``. Cuối cùng, trường thời gian sống (``TTL``) cho biết bản ghi tài nguyên này hợp lệ trong bao lâu. Nó được các máy chủ lưu vào bộ nhớ đệm các bản ghi tài nguyên từ máy chủ khác sử dụng; khi ``TTL`` hết hạn, máy chủ phải loại bỏ bản ghi khỏi bộ nhớ đệm.

Để hiểu rõ hơn cách các bản ghi tài nguyên đại diện cho thông tin trong phân cấp miền, hãy xem các ví dụ sau được lấy từ phân cấp miền trong :numref:`Figure %s <fig-domains>`. Để đơn giản, chúng ta bỏ qua trường ``TTL`` và chỉ đưa ra thông tin liên quan cho một trong các máy chủ tên triển khai mỗi vùng.

Đầu tiên, một máy chủ tên gốc chứa một bản ghi ``NS`` cho mỗi máy chủ tên miền cấp cao nhất (TLD). Điều này xác định một máy chủ có thể phân giải các truy vấn cho phần này của phân cấp DNS (``.edu`` và ``.com`` trong ví dụ này). Nó cũng có các bản ghi ``A`` chuyển đổi các tên này thành địa chỉ IP tương ứng. Hai bản ghi này kết hợp lại thực hiện một con trỏ từ máy chủ tên gốc tới một trong các máy chủ TLD.

::

   (edu, a3.nstld.com, NS, IN)
   (a3.nstld.com, 192.5.6.32, A, IN)
   (com, a.gtld-servers.net, NS, IN)
   (a.gtld-servers.net, 192.5.6.30, A, IN)
   ...

Đi xuống một cấp trong phân cấp, máy chủ có các bản ghi cho các miền như sau:

::

   (princeton.edu, dns.princeton.edu, NS, IN)
   (dns.princeton.edu, 128.112.129.15, A, IN)
   ...

Trong trường hợp này, chúng ta có một bản ghi ``NS`` và một bản ghi ``A`` cho máy chủ tên chịu trách nhiệm cho phần ``princeton.edu`` của phân cấp. Máy chủ đó có thể trực tiếp phân giải một số truy vấn (ví dụ, cho ``email.princeton.edu``) trong khi sẽ chuyển hướng các truy vấn khác tới máy chủ ở một lớp khác trong phân cấp (ví dụ, cho truy vấn về ``penguins.cs.princeton.edu``).

::

   (email.princeton.edu, 128.112.198.35, A, IN)
   (penguins.cs.princeton.edu, dns1.cs.princeton.edu, NS, IN)
   (dns1.cs.princeton.edu, 128.112.136.10, A, IN)
   ...

Cuối cùng, một máy chủ tên cấp ba, như máy chủ do miền ``cs.princeton.edu`` quản lý, chứa các bản ghi ``A`` cho tất cả các máy chủ của nó. Nó cũng có thể định nghĩa một tập hợp các bí danh (bản ghi ``CNAME``) cho từng máy chủ đó. Bí danh đôi khi chỉ là các tên thuận tiện (ví dụ, ngắn hơn) cho máy, nhưng cũng có thể dùng để cung cấp một mức độ gián tiếp. Ví dụ, ``www.cs.princeton.edu`` là bí danh cho máy chủ có tên ``coreweb.cs.princeton.edu``. Điều này cho phép máy chủ web của site chuyển sang máy khác mà không ảnh hưởng đến người dùng từ xa; họ chỉ cần tiếp tục sử dụng bí danh mà không quan tâm máy nào hiện đang chạy máy chủ web của miền. Các bản ghi trao đổi thư (``MX``) phục vụ mục đích tương tự cho ứng dụng email—chúng cho phép quản trị viên thay đổi máy chủ nhận thư cho miền mà không cần thay đổi địa chỉ email của mọi người.

::

   (penguins.cs.princeton.edu, 128.112.155.166, A, IN)
   (www.cs.princeton.edu, coreweb.cs.princeton.edu, CNAME, IN)
   (coreweb.cs.princeton.edu, 128.112.136.35, A, IN)
   (cs.princeton.edu, mail.cs.princeton.edu, MX, IN)
   (mail.cs.princeton.edu, 128.112.136.72, A, IN)
   ...

Lưu ý rằng, mặc dù các bản ghi tài nguyên có thể được định nghĩa cho hầu như bất kỳ loại đối tượng nào, DNS thường được dùng để đặt tên cho máy chủ (bao gồm cả máy chủ dịch vụ) và site. Nó không dùng để đặt tên cho cá nhân hoặc các đối tượng khác như tệp hoặc thư mục; các hệ thống đặt tên khác thường được sử dụng để nhận diện các đối tượng như vậy. Ví dụ, X.500 là một hệ thống đặt tên của ISO được thiết kế để giúp nhận diện con người dễ dàng hơn. Nó cho phép bạn đặt tên cho một người bằng cách cung cấp một tập hợp thuộc tính: tên, chức danh, số điện thoại, địa chỉ bưu điện, v.v. X.500 tỏ ra quá cồng kềnh—và, ở một mức độ nào đó, đã bị các công cụ tìm kiếm mạnh mẽ trên Web thay thế—nhưng cuối cùng nó đã phát triển thành Giao thức truy cập thư mục nhẹ (LDAP). LDAP là một tập con của X.500, ban đầu được thiết kế làm giao diện cho X.500 trên máy tính cá nhân. Ngày nay, LDAP được sử dụng rộng rãi, chủ yếu ở cấp doanh nghiệp, như một hệ thống để tra cứu thông tin về người dùng.

Phân giải tên
~~~~~~~~~~~~~

Với một phân cấp các máy chủ tên, bây giờ chúng ta xem xét vấn đề làm thế nào một khách hàng sử dụng các máy chủ này để phân giải một tên miền. Để minh họa ý tưởng cơ bản, giả sử khách hàng muốn phân giải tên ``penguins.cs.princeton.edu`` dựa trên tập máy chủ đã nêu ở phần trước. Khách hàng có thể đầu tiên gửi một truy vấn chứa tên này tới một trong các máy chủ gốc (như chúng ta sẽ thấy bên dưới, điều này hiếm khi xảy ra trong thực tế nhưng đủ để minh họa hoạt động cơ bản). Máy chủ gốc, không thể khớp toàn bộ tên, trả về kết quả khớp tốt nhất mà nó có—bản ghi ``NS`` cho ``edu`` trỏ tới máy chủ TLD ``a3.nstld.com``. Máy chủ cũng trả về tất cả các bản ghi liên quan đến bản ghi này, trong trường hợp này là bản ghi ``A`` cho ``a3.nstld.com``. Khách hàng, chưa nhận được câu trả lời mong muốn, tiếp tục gửi cùng truy vấn đó tới máy chủ tên tại địa chỉ IP ``192.5.6.32``. Máy chủ này cũng không thể khớp toàn bộ tên và trả về các bản ghi ``NS`` và ``A`` tương ứng cho miền ``princeton.edu``. Một lần nữa, khách hàng gửi cùng truy vấn như trước tới máy chủ tại địa chỉ IP ``128.112.129.15``, và lần này nhận lại bản ghi ``NS`` và ``A`` cho miền ``cs.princeton.edu``. Lúc này, máy chủ có thể phân giải hoàn toàn truy vấn đã được tiếp cận. Một truy vấn cuối cùng tới máy chủ tại ``128.112.136.10`` trả về bản ghi ``A`` cho ``penguins.cs.princeton.edu``, và khách hàng biết rằng địa chỉ IP tương ứng là ``128.112.155.166``.

Ví dụ này vẫn còn để lại một vài câu hỏi về quá trình phân giải. Câu hỏi đầu tiên là làm thế nào khách hàng xác định được máy chủ gốc ngay từ đầu, hay nói cách khác, làm thế nào để phân giải tên của máy chủ biết cách phân giải tên? Đây là một vấn đề cơ bản trong bất kỳ hệ thống đặt tên nào, và câu trả lời là hệ thống phải được khởi tạo bằng một cách nào đó. Trong trường hợp này, ánh xạ tên-địa chỉ cho một hoặc nhiều máy chủ gốc là đã biết; nghĩa là, nó được công bố thông qua một phương tiện nào đó bên ngoài hệ thống đặt tên.

Tuy nhiên, trong thực tế, không phải tất cả các khách hàng đều biết về các máy chủ gốc. Thay vào đó, chương trình khách chạy trên mỗi máy chủ Internet được khởi tạo với địa chỉ của một máy chủ tên *cục bộ*. Ví dụ, tất cả các máy chủ trong Khoa Khoa học Máy tính tại Princeton đều biết về máy chủ ``dns1.cs.princeton.edu``. Máy chủ tên cục bộ này, đến lượt nó, có các bản ghi tài nguyên cho một hoặc nhiều máy chủ gốc, ví dụ:

::

   ('root', a.root-servers.net, NS, IN)
   (a.root-servers.net, 198.41.0.4, A, IN)

Do đó, việc phân giải tên thực tế liên quan đến việc một khách hàng truy vấn máy chủ cục bộ, máy chủ này lại đóng vai trò là khách hàng truy vấn các máy chủ từ xa thay cho khách hàng gốc. Điều này dẫn đến các tương tác khách/chủ được minh họa ở :numref:`Figure %s <fig-resolution>`. Một lợi thế của mô hình này là tất cả các máy chủ trên Internet không cần phải được cập nhật về vị trí các máy chủ gốc hiện tại; chỉ các máy chủ tên mới cần biết về gốc. Lợi thế thứ hai là máy chủ cục bộ có thể thấy các câu trả lời trả về từ các truy vấn do tất cả các khách hàng cục bộ gửi. Máy chủ cục bộ *lưu vào bộ nhớ đệm* các phản hồi này và đôi khi có thể phân giải các truy vấn trong tương lai mà không cần phải gửi ra mạng. Trường ``TTL`` trong các bản ghi tài nguyên trả về từ máy chủ từ xa cho biết mỗi bản ghi có thể được lưu vào bộ nhớ đệm an toàn trong bao lâu. Cơ chế lưu vào bộ nhớ đệm này cũng có thể được sử dụng ở các cấp cao hơn trong phân cấp, giảm tải cho các máy chủ gốc và TLD.

Câu hỏi thứ hai là hệ thống hoạt động như thế nào khi người dùng gửi một tên không đầy đủ (ví dụ, ``penguins``) thay vì một tên miền đầy đủ (ví dụ, ``penguins.cs.princeton.edu``). Câu trả lời là chương trình khách được cấu hình với miền cục bộ mà máy chủ đang cư trú (ví dụ, ``cs.princeton.edu``), và nó sẽ nối chuỗi này vào bất kỳ tên đơn giản nào trước khi gửi truy vấn.

.. _fig-resolution:
.. figure:: figures/f09-18-9780123850591.png
   :width: 600px
   :align: center

   Phân giải tên trong thực tế, các số từ 1 đến 10 cho thấy trình tự các bước trong quá trình.

.. _key-naming:
.. admonition:: Ý chính

   Để đảm bảo chúng ta rõ ràng, chúng ta đã thấy ba cấp độ định danh khác nhau—tên miền, địa chỉ IP, và địa chỉ mạng vật lý—và việc ánh xạ các định danh ở một cấp sang định danh ở cấp khác diễn ra ở các điểm khác nhau trong kiến trúc mạng. Đầu tiên, người dùng nhập tên miền khi tương tác với ứng dụng. Thứ hai, ứng dụng sử dụng DNS để chuyển đổi tên này thành địa chỉ IP; chính địa chỉ IP được đặt vào mỗi datagram, không phải tên miền. (Ngoài lề, quá trình chuyển đổi này liên quan đến việc gửi các datagram IP qua Internet, nhưng các datagram này được gửi tới máy chủ tên, không phải tới đích cuối cùng.) Thứ ba, IP thực hiện chuyển tiếp tại mỗi bộ định tuyến, thường có nghĩa là nó ánh xạ một địa chỉ IP thành một địa chỉ IP khác; tức là, nó ánh xạ địa chỉ đích cuối cùng thành địa chỉ của bộ định tuyến kế tiếp. Cuối cùng, IP sử dụng Giao thức phân giải địa chỉ (ARP) để chuyển đổi địa chỉ IP kế tiếp thành địa chỉ vật lý cho máy đó; bước kế tiếp có thể là đích cuối cùng hoặc một bộ định tuyến trung gian. Các frame gửi qua mạng vật lý có các địa chỉ vật lý này trong tiêu đề của chúng. :ref:`[Tiếp theo] <key-virtualization>`

9.3.2 Quản lý mạng (SNMP, OpenConfig)
-------------------------------------

Một mạng là một hệ thống phức tạp, cả về số lượng nút tham gia lẫn bộ giao thức có thể chạy trên bất kỳ nút nào. Ngay cả khi bạn chỉ quan tâm đến các nút trong một miền quản trị duy nhất, chẳng hạn như một khuôn viên trường, có thể có hàng chục bộ định tuyến và hàng trăm—thậm chí hàng nghìn—máy chủ cần theo dõi. Nếu bạn nghĩ về tất cả trạng thái được duy trì và thao tác trên bất kỳ nút nào—bảng chuyển đổi địa chỉ, bảng định tuyến, trạng thái kết nối TCP, v.v.—thì thật dễ bị choáng ngợp trước viễn cảnh phải quản lý tất cả thông tin này.

Thật dễ hình dung mong muốn biết về trạng thái của các giao thức khác nhau trên các nút khác nhau. Ví dụ, bạn có thể muốn giám sát số lượng datagram IP bị hủy lắp ráp, để xác định xem thời gian chờ thu gom rác các datagram lắp ráp dở có cần điều chỉnh không. Một ví dụ khác, bạn có thể muốn theo dõi tải trên các nút khác nhau (tức là số lượng gói tin gửi hoặc nhận) để xác định xem có cần bổ sung bộ định tuyến hoặc liên kết mới cho mạng không. Tất nhiên, bạn cũng phải cảnh giác với các phần cứng lỗi và phần mềm hoạt động không đúng.

Những gì chúng ta vừa mô tả là vấn đề quản lý mạng, một vấn đề xuyên suốt toàn bộ kiến trúc mạng. Vì các nút mà chúng ta muốn theo dõi là phân tán, lựa chọn thực tế duy nhất là sử dụng mạng để quản lý mạng. Điều này có nghĩa là chúng ta cần một giao thức cho phép đọc và ghi các thông tin trạng thái khác nhau trên các nút mạng khác nhau. Sau đây là hai cách tiếp cận.

SNMP
~~~~

Một giao thức được sử dụng rộng rãi cho quản lý mạng là SNMP (*Simple Network Management Protocol*). SNMP về cơ bản là một giao thức request/reply chuyên biệt hỗ trợ hai loại thông điệp yêu cầu: ``GET`` và ``SET``. Loại đầu dùng để lấy một thông tin trạng thái từ một nút nào đó, loại sau dùng để lưu một thông tin trạng thái mới vào một nút nào đó. (SNMP cũng hỗ trợ một thao tác thứ ba, ``GET-NEXT``, sẽ được giải thích bên dưới.) Phần thảo luận sau tập trung vào thao tác ``GET``, vì đây là thao tác được sử dụng thường xuyên nhất.

SNMP được sử dụng theo cách hiển nhiên. Người vận hành tương tác với một chương trình khách hiển thị thông tin về mạng. Chương trình khách này thường có giao diện đồ họa. Bạn có thể coi giao diện này giống như một trình duyệt web. Bất cứ khi nào người vận hành chọn một thông tin nào đó muốn xem, chương trình khách sử dụng SNMP để yêu cầu thông tin đó từ nút tương ứng. (SNMP chạy trên UDP.) Một máy chủ SNMP chạy trên nút đó nhận yêu cầu, xác định thông tin phù hợp, và trả về cho chương trình khách, chương trình này sẽ hiển thị cho người dùng.

Chỉ có một điểm phức tạp trong kịch bản đơn giản này: Chính xác thì làm thế nào để chương trình khách chỉ định thông tin nào nó muốn lấy, và, tương tự, làm thế nào để máy chủ biết biến nào trong bộ nhớ cần đọc để đáp ứng yêu cầu? Câu trả lời là SNMP phụ thuộc vào một đặc tả đi kèm gọi là *cơ sở thông tin quản lý* (MIB). MIB định nghĩa các thông tin cụ thể—các biến MIB—mà bạn có thể lấy từ một nút mạng.

Phiên bản hiện tại của MIB, gọi là MIB-II, tổ chức các biến thành các *nhóm* khác nhau. Bạn sẽ nhận ra rằng hầu hết các nhóm này tương ứng với một trong các giao thức được mô tả trong cuốn sách này, và gần như tất cả các biến được định nghĩa cho mỗi nhóm đều quen thuộc. Ví dụ:

-  System—Các tham số chung của hệ thống (nút) nói chung, bao gồm vị trí nút, thời gian hoạt động, và tên hệ thống

-  Interfaces—Thông tin về tất cả các giao diện mạng (bộ điều hợp) gắn với nút này, như địa chỉ vật lý của mỗi giao diện và số lượng gói tin đã gửi và nhận trên mỗi giao diện

-  Address translation—Thông tin về Giao thức phân giải địa chỉ (ARP), đặc biệt là nội dung bảng chuyển đổi địa chỉ

-  IP—Các biến liên quan đến IP, bao gồm bảng định tuyến, số lượng datagram đã chuyển tiếp thành công, và thống kê về lắp ráp datagram; bao gồm số lần IP loại bỏ một datagram vì lý do nào đó

-  TCP—Thông tin về các kết nối TCP, như số lần mở bị động và chủ động, số lần reset, số lần timeout, các thiết lập timeout mặc định, v.v.; thông tin theo kết nối chỉ tồn tại khi kết nối còn tồn tại

-  UDP—Thông tin về lưu lượng UDP, bao gồm tổng số datagram UDP đã gửi và nhận.

Cũng có các nhóm cho Giao thức thông báo điều khiển Internet (ICMP) và chính SNMP.

Quay lại vấn đề chương trình khách chỉ định chính xác thông tin nào muốn lấy từ một nút, có danh sách các biến MIB mới chỉ là một nửa vấn đề. Vẫn còn hai vấn đề. Thứ nhất, chúng ta cần một cú pháp chính xác để chương trình khách sử dụng khi chỉ định biến MIB nào muốn lấy. Thứ hai, chúng ta cần một biểu diễn chính xác cho các giá trị trả về từ máy chủ. Cả hai vấn đề đều được giải quyết bằng Abstract Syntax Notation One (ASN.1).

Xét vấn đề thứ hai trước. Như chúng ta đã thấy ở chương trước, ASN.1/Basic Encoding Rules (BER) định nghĩa một biểu diễn cho các kiểu dữ liệu khác nhau, như số nguyên. MIB định nghĩa kiểu của mỗi biến, sau đó sử dụng ASN.1/BER để mã hóa giá trị chứa trong biến này khi truyền qua mạng. Đối với vấn đề thứ nhất, ASN.1 cũng định nghĩa một hệ thống nhận diện đối tượng. MIB sử dụng hệ thống này để gán một định danh toàn cục duy nhất cho mỗi biến MIB. Các định danh này được viết theo dạng “dấu chấm”, không khác gì tên miền. Ví dụ, 1.3.6.1.2.1.4.3 là định danh ASN.1 duy nhất cho biến MIB liên quan đến IP ``ipInReceives``; biến này đếm số lượng datagram IP đã được nút này nhận. Trong ví dụ này, tiền tố 1.3.6.1.2.1 xác định cơ sở dữ liệu MIB (nhớ rằng, định danh đối tượng ASN.1 là cho tất cả các đối tượng có thể có trên thế giới), số 4 tương ứng với nhóm IP, và số 3 cuối cùng là biến thứ ba trong nhóm này.

Do đó, quản lý mạng hoạt động như sau. Chương trình khách SNMP đặt định danh ASN.1 cho biến MIB muốn lấy vào thông điệp yêu cầu, và gửi thông điệp này tới máy chủ. Máy chủ sau đó ánh xạ định danh này thành một biến cục bộ (tức là một vị trí bộ nhớ nơi giá trị của biến này được lưu), lấy giá trị hiện tại của biến này, và sử dụng ASN.1/BER để mã hóa giá trị gửi lại cho chương trình khách.

Có một chi tiết cuối cùng. Nhiều biến MIB là bảng hoặc cấu trúc. Các biến phức hợp như vậy giải thích lý do cho thao tác ``GET-NEXT`` của SNMP. Thao tác này, khi áp dụng cho một ID biến cụ thể, trả về giá trị của biến đó cộng với ID của biến tiếp theo, ví dụ, mục tiếp theo trong bảng hoặc trường tiếp theo trong cấu trúc. Điều này giúp chương trình khách “duyệt qua” các phần tử của một bảng hoặc cấu trúc.

OpenConfig
~~~~~~~~~~

SNMP vẫn được sử dụng rộng rãi và lịch sử là giao thức quản lý cho switch và router, nhưng gần đây ngày càng có nhiều sự chú ý đến các cách quản lý mạng linh hoạt và mạnh mẽ hơn. Vẫn chưa có sự đồng thuận hoàn toàn về một tiêu chuẩn toàn ngành, nhưng một sự đồng thuận về cách tiếp cận chung đang bắt đầu hình thành. Chúng tôi mô tả một ví dụ, gọi là *OpenConfig*, đang nhận được nhiều sự quan tâm và minh họa nhiều ý tưởng then chốt đang được theo đuổi.

Chiến lược chung là tự động hóa quản lý mạng càng nhiều càng tốt, với mục tiêu loại bỏ con người dễ mắc lỗi khỏi vòng lặp. Điều này đôi khi được gọi là quản lý *zero-touch* (không chạm tay), và nó ngụ ý hai điều phải xảy ra. Thứ nhất, trong khi trước đây các nhà vận hành dùng công cụ như SNMP để *giám sát* mạng, nhưng phải đăng nhập vào từng thiết bị mạng gặp sự cố và sử dụng giao diện dòng lệnh (CLI) để khắc phục, thì quản lý zero-touch ngụ ý rằng chúng ta cũng cần *cấu hình* mạng một cách lập trình. Nói cách khác, quản lý mạng vừa là đọc thông tin trạng thái vừa là ghi thông tin cấu hình. Mục tiêu là xây dựng một vòng điều khiển khép kín, mặc dù sẽ luôn có những trường hợp cần cảnh báo để người vận hành can thiệp thủ công.

Thứ hai, trong khi trước đây người vận hành phải cấu hình từng thiết bị mạng riêng lẻ, thì tất cả các thiết bị phải được cấu hình nhất quán nếu chúng muốn hoạt động đúng như một mạng. Do đó, zero-touch cũng ngụ ý rằng người vận hành nên có thể khai báo *ý định* toàn mạng của mình, với công cụ quản lý đủ thông minh để phát sinh các chỉ thị cấu hình cho từng thiết bị một cách nhất quán trên toàn mạng.

.. _fig-mgmt:
.. figure:: figures/apps/Slide1.png
   :width: 400px
   :align: center

   Người vận hành quản lý mạng thông qua một công cụ cấu hình và quản lý, công cụ này tương tác lập trình với các thiết bị mạng bên dưới (ví dụ, sử dụng gNMI làm giao thức truyền tải và YANG để xác định lược đồ cho dữ liệu được trao đổi).

:numref:`Figure %s <fig-mgmt>` đưa ra một mô tả cấp cao về cách tiếp cận lý tưởng này cho quản lý mạng. Chúng tôi nói “lý tưởng” vì đạt được quản lý zero-touch thực sự vẫn còn là mục tiêu hơn là thực tế. Nhưng tiến bộ đang được thực hiện. Ví dụ, các công cụ quản lý mới bắt đầu tận dụng các giao thức tiêu chuẩn như HTTP để giám sát và cấu hình thiết bị mạng. Đây là một bước tiến tích cực vì nó giúp chúng ta không phải tạo thêm một giao thức request/reply mới nữa và cho phép tập trung vào việc tạo ra các công cụ quản lý thông minh hơn, có thể tận dụng các thuật toán Machine Learning để phát hiện vấn đề.

Cũng giống như HTTP đang bắt đầu thay thế SNMP làm giao thức giao tiếp với thiết bị mạng, có một nỗ lực song song để thay thế MIB bằng một tiêu chuẩn mới cho thông tin trạng thái mà các loại thiết bị khác nhau có thể báo cáo, *cộng với* thông tin cấu hình mà các thiết bị đó có thể phản hồi. Đồng thuận về một tiêu chuẩn cấu hình duy nhất vốn là một thách thức vì mỗi nhà sản xuất đều cho rằng thiết bị của mình là đặc biệt, không giống bất kỳ thiết bị nào của đối thủ. (Nói cách khác, thách thức không hoàn toàn là kỹ thuật.)

Cách tiếp cận chung là cho phép mỗi nhà sản xuất thiết bị công bố một *mô hình dữ liệu* xác định các nút cấu hình (và dữ liệu giám sát có sẵn) cho sản phẩm của mình, và chỉ tiêu chuẩn hóa ngôn ngữ mô hình hóa. Ứng viên hàng đầu là YANG, viết tắt của *Yet Another Next Generation*, một cái tên hài hước về việc phải làm lại mọi thứ nhiều lần. YANG có thể được xem như một phiên bản giới hạn của XSD, mà bạn có thể nhớ là ngôn ngữ xác định lược đồ (mô hình) cho XML. Nghĩa là, YANG xác định cấu trúc của dữ liệu. Nhưng không giống XSD, YANG không chỉ dành cho XML. Nó có thể được sử dụng với các định dạng thông điệp khác nhau, bao gồm XML, Protobufs và JSON.

Điều quan trọng của cách tiếp cận này là mô hình dữ liệu xác định ngữ nghĩa của các biến có thể đọc và ghi dưới dạng lập trình (tức là, không chỉ là văn bản trong đặc tả tiêu chuẩn). Không phải mỗi nhà sản xuất tự do định nghĩa mô hình riêng biệt vì các nhà vận hành mạng mua thiết bị có động lực mạnh để thúc đẩy các mô hình cho các thiết bị tương tự hội tụ lại. YANG giúp quá trình tạo, sử dụng và sửa đổi mô hình trở nên lập trình hơn, và do đó, thích nghi hơn với quá trình này.

Đây là nơi OpenConfig xuất hiện. Nó sử dụng YANG làm ngôn ngữ mô hình hóa, nhưng cũng thiết lập một quy trình thúc đẩy ngành hướng tới các mô hình chung. OpenConfig chính thức không phụ thuộc vào cơ chế RPC dùng để giao tiếp với thiết bị mạng, nhưng một cách tiếp cận mà nó đang theo đuổi là gNMI (*gRPC Network Management Interface*). Như bạn có thể đoán từ tên gọi, gNMI sử dụng gRPC, mà bạn có thể nhớ là chạy trên HTTP. Điều này có nghĩa là gNMI cũng sử dụng Protobufs để xác định dữ liệu thực sự được truyền qua kết nối HTTP. Do đó, như minh họa ở :numref:`Figure %s <fig-mgmt>`, gNMI được thiết kế như một giao diện quản lý tiêu chuẩn cho thiết bị mạng. Điều chưa được tiêu chuẩn hóa là mức độ phong phú của khả năng tự động hóa của công cụ quản lý, hoặc hình thức giao diện hướng tới người vận hành. Như bất kỳ ứng dụng nào đang cố gắng đáp ứng nhu cầu và hỗ trợ nhiều tính năng hơn các lựa chọn thay thế, vẫn còn nhiều không gian đổi mới trong các công cụ quản lý mạng.

Để đầy đủ, chúng tôi lưu ý rằng NETCONF là một trong những giao thức hậu-SNMP để truyền thông tin cấu hình tới thiết bị mạng. OpenConfig hoạt động với NETCONF, nhưng chúng tôi cho rằng gNMI là tương lai.

Chúng tôi kết luận bằng cách nhấn mạnh rằng một sự thay đổi lớn đang diễn ra. Mặc dù liệt kê SNMP và OpenConfig trong tiêu đề phần này có vẻ như chúng tương đương, nhưng chính xác hơn là mỗi cái đại diện cho một cách tiếp cận, và các cách tiếp cận này khá khác nhau. Một mặt, SNMP thực chất chỉ là một giao thức truyền tải, tương tự như gNMI trong thế giới OpenConfig. Lịch sử, nó cho phép giám sát thiết bị, nhưng hầu như không đề cập đến cấu hình thiết bị. (Việc cấu hình vẫn chủ yếu cần can thiệp thủ công.) Mặt khác, OpenConfig chủ yếu là một nỗ lực xác định một tập hợp mô hình dữ liệu chung cho thiết bị mạng, tương tự vai trò của MIB trong thế giới SNMP, ngoại trừ OpenConfig (1) dựa trên mô hình, sử dụng YANG, và (2) tập trung đồng đều vào cả giám sát và cấu hình.
