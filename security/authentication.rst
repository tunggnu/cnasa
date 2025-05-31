8.4 Các Giao Thức Xác Thực
==========================

Cho đến nay chúng ta đã mô tả cách mã hóa thông điệp, xây dựng bộ xác thực,
phân phối trước các khóa cần thiết. Có vẻ như tất cả những gì chúng ta cần làm
để làm cho một giao thức an toàn là gắn một bộ xác thực vào mỗi thông điệp
và, nếu muốn tính bảo mật, thì mã hóa thông điệp.

Có hai lý do chính khiến mọi việc không đơn giản như vậy. Thứ nhất, có vấn đề về
*một cuộc tấn công phát lại* (*replay attack*): một kẻ tấn công truyền lại một bản sao
của một thông điệp đã được gửi trước đó. Nếu thông điệp đó là một đơn đặt hàng bạn đã
đặt trên một trang web, ví dụ, thì thông điệp bị phát lại sẽ khiến trang web nghĩ rằng
bạn đã đặt thêm nhiều đơn hàng giống như vậy. Mặc dù nó không phải là bản gốc của thông điệp,
bộ xác thực của nó vẫn hợp lệ; dù sao thì thông điệp đó cũng do bạn tạo ra, và nó không bị sửa đổi.
Rõ ràng, chúng ta cần một giải pháp đảm bảo *tính nguyên bản* (*originality*).

Trong một biến thể của cuộc tấn công này gọi là *tấn công trì hoãn-phát lại* (*suppress-replay attack*),
một kẻ tấn công có thể chỉ đơn giản là trì hoãn thông điệp của bạn (bằng cách chặn và phát lại sau),
để nó được nhận vào một thời điểm không còn phù hợp nữa. Ví dụ, một kẻ tấn công có thể trì hoãn
đơn đặt hàng mua cổ phiếu của bạn từ một thời điểm thuận lợi sang một thời điểm mà bạn không còn muốn mua nữa.
Mặc dù thông điệp này theo một nghĩa nào đó là bản gốc, nhưng nó lại không kịp thời.
Vì vậy, chúng ta cũng cần đảm bảo *tính kịp thời* (*timeliness*). Tính nguyên bản và tính kịp thời
có thể được xem là các khía cạnh của tính toàn vẹn. Đảm bảo chúng trong hầu hết các trường hợp
sẽ đòi hỏi một giao thức qua lại không hề đơn giản.

Vấn đề thứ hai mà chúng ta chưa giải quyết là làm thế nào để thiết lập một khóa phiên (*session key*).
Khóa phiên là một khóa mã hóa khóa bí mật được tạo ra ngay tại chỗ và chỉ sử dụng cho một phiên duy nhất.
Điều này cũng liên quan đến một giao thức không hề đơn giản.

Điểm chung của hai vấn đề này là xác thực (*authentication*). Nếu một thông điệp không nguyên bản
và không kịp thời, thì trên thực tế chúng ta muốn coi nó là không xác thực, không đến từ người mà nó tuyên bố.
Và, rõ ràng, khi bạn đang sắp chia sẻ một khóa phiên mới với ai đó, bạn muốn biết mình đang chia sẻ với đúng người.
Thông thường, các giao thức xác thực sẽ thiết lập một khóa phiên cùng lúc, để sau khi giao thức kết thúc,
Alice và Bob đã xác thực lẫn nhau và có một khóa bí mật mới để sử dụng. Nếu không có khóa phiên mới,
giao thức chỉ xác thực Alice và Bob tại một thời điểm; khóa phiên cho phép họ xác thực hiệu quả các thông điệp tiếp theo.
Nói chung, các giao thức thiết lập khóa phiên sẽ thực hiện xác thực. Một ngoại lệ đáng chú ý là Diffie-Hellman,
như mô tả bên dưới, vì vậy các thuật ngữ *giao thức xác thực* (*authentication protocol*) và
*giao thức thiết lập khóa phiên* (*session key establishment protocol*) gần như đồng nghĩa.

Có một tập hợp các kỹ thuật cốt lõi được sử dụng để đảm bảo tính nguyên bản và kịp thời trong các giao thức xác thực.
Chúng tôi sẽ mô tả các kỹ thuật đó trước khi chuyển sang các giao thức cụ thể.

8.4.1 Kỹ Thuật Đảm Bảo Tính Nguyên Bản và Kịp Thời
--------------------------------------------------

Chúng ta đã thấy rằng chỉ dùng bộ xác thực thôi thì không thể phát hiện các thông điệp không nguyên bản hoặc không kịp thời.
Một cách tiếp cận là đưa một dấu thời gian (*timestamp*) vào thông điệp. Rõ ràng, bản thân dấu thời gian phải không thể bị giả mạo,
vì vậy nó phải được bảo vệ bởi bộ xác thực. Nhược điểm chính của dấu thời gian là nó đòi hỏi phải đồng bộ hóa đồng hồ phân tán.
Vì hệ thống của chúng ta sẽ phụ thuộc vào việc đồng bộ hóa, nên chính việc đồng bộ hóa đồng hồ cũng cần được bảo vệ
trước các mối đe dọa bảo mật, ngoài các thách thức thông thường của việc đồng bộ hóa đồng hồ.
Một vấn đề khác là các đồng hồ phân tán chỉ được đồng bộ hóa đến một mức độ nhất định—một biên độ sai số nhất định.
Do đó, tính toàn vẹn về thời gian mà dấu thời gian cung cấp chỉ tốt bằng mức độ đồng bộ hóa.

Một cách tiếp cận khác là đưa vào thông điệp một *nonce*—một số ngẫu nhiên chỉ sử dụng một lần.
Các bên tham gia có thể phát hiện các cuộc tấn công phát lại bằng cách kiểm tra xem một nonce đã được sử dụng trước đó chưa.
Đáng tiếc là điều này đòi hỏi phải theo dõi các nonce đã dùng, mà số lượng có thể tích tụ rất lớn.
Một giải pháp là kết hợp việc sử dụng dấu thời gian và nonce, để các nonce chỉ cần đảm bảo duy nhất trong một khoảng thời gian nhất định.
Điều đó giúp việc đảm bảo tính duy nhất của nonce trở nên khả thi hơn trong khi chỉ cần đồng bộ hóa đồng hồ lỏng lẻo.

Một giải pháp khác cho các hạn chế của dấu thời gian và nonce là sử dụng một hoặc cả hai trong một giao thức *thách thức-đáp ứng* (*challenge-response*).
Giả sử chúng ta sử dụng dấu thời gian. Trong một giao thức thách thức-đáp ứng, Alice gửi cho Bob một dấu thời gian,
thách thức Bob mã hóa nó trong một thông điệp phản hồi (nếu họ chia sẻ một khóa bí mật) hoặc ký số nó trong một thông điệp phản hồi
(nếu Bob có một khóa công khai, như trong :numref:`Figure %s <fig-challenge-response>`).
Dấu thời gian được mã hóa giống như một bộ xác thực bổ sung chứng minh tính kịp thời.
Alice có thể dễ dàng kiểm tra tính kịp thời của dấu thời gian trong phản hồi từ Bob vì dấu thời gian đó xuất phát từ đồng hồ của chính Alice—
không cần đồng bộ hóa đồng hồ phân tán. Giả sử thay vào đó giao thức sử dụng nonce.
Khi đó Alice chỉ cần theo dõi các nonce mà phản hồi đang chờ xử lý và chưa chờ quá lâu;
bất kỳ phản hồi nào với một nonce không nhận ra đều phải là giả mạo.

.. _fig-challenge-response:
.. figure:: figures/f08-07-9780123850591.png
   :width: 450px
   :align: center

   Một giao thức thách thức-đáp ứng.

Điều tuyệt vời của thách thức-đáp ứng, vốn có thể trông quá phức tạp, là nó kết hợp được tính kịp thời và xác thực;
suy cho cùng, chỉ Bob (và có thể cả Alice, nếu là mã hóa khóa bí mật) biết khóa cần thiết để mã hóa dấu thời gian hoặc nonce chưa từng thấy trước đó.
Dấu thời gian hoặc nonce được sử dụng trong hầu hết các giao thức xác thực tiếp theo.

8.4.2 Các Giao Thức Xác Thực Khóa Công Khai
--------------------------------------------

Trong phần thảo luận sau, chúng tôi giả định rằng các khóa công khai của Alice và Bob đã được phân phối trước cho nhau
thông qua một phương tiện nào đó như PKI. Điều này bao gồm cả trường hợp Alice gửi chứng chỉ của mình trong thông điệp đầu tiên cho Bob,
và trường hợp Bob tìm kiếm một chứng chỉ về Alice khi nhận được thông điệp đầu tiên từ cô ấy.

.. _fig-pKAuthSync:
.. figure:: figures/f08-08-9780123850591.png
   :width: 600px
   :align: center

   Một giao thức xác thực khóa công khai phụ thuộc vào đồng bộ hóa.

Giao thức đầu tiên này (:numref:`Figure %s <fig-pKAuthSync>`) dựa vào việc đồng hồ của Alice và Bob được đồng bộ hóa.
Alice gửi cho Bob một thông điệp với dấu thời gian và danh tính của cô ấy ở dạng văn bản thuần cùng với chữ ký số của cô ấy.
Bob sử dụng chữ ký số để xác thực thông điệp và dấu thời gian để kiểm tra tính mới mẻ.
Bob gửi lại một thông điệp với dấu thời gian và danh tính của mình ở dạng văn bản thuần, cũng như một khóa phiên mới được mã hóa
(để đảm bảo tính bảo mật) bằng khóa công khai của Alice, tất cả đều được ký số.
Alice có thể xác minh tính xác thực và tính mới mẻ của thông điệp, do đó cô ấy biết mình có thể tin tưởng vào khóa phiên mới.
Để xử lý việc đồng bộ hóa đồng hồ không hoàn hảo, các dấu thời gian có thể được bổ sung bằng các nonce.

Giao thức thứ hai (:numref:`Figure %s <fig-pKAuthNoSync>`) tương tự nhưng không phụ thuộc vào đồng bộ hóa đồng hồ.
Trong giao thức này, Alice lại gửi cho Bob một thông điệp được ký số với dấu thời gian và danh tính của cô ấy.
Vì đồng hồ của họ không được đồng bộ hóa, Bob không thể chắc chắn rằng thông điệp là mới.
Bob gửi lại một thông điệp được ký số với dấu thời gian gốc của Alice, dấu thời gian mới của chính mình và danh tính của mình.
Alice có thể xác minh tính mới mẻ của phản hồi từ Bob bằng cách so sánh thời gian hiện tại của mình với dấu thời gian bắt nguồn từ cô ấy.
Sau đó, cô ấy gửi cho Bob một thông điệp được ký số với dấu thời gian gốc của Bob và một khóa phiên mới được mã hóa bằng khóa công khai của Bob.
Bob có thể xác minh tính mới mẻ của thông điệp vì dấu thời gian xuất phát từ đồng hồ của mình, do đó anh ấy biết mình có thể tin tưởng vào khóa phiên mới.
Các dấu thời gian về cơ bản đóng vai trò như các nonce tiện lợi, và thực tế giao thức này cũng có thể sử dụng nonce thay thế.

.. _fig-pKAuthNoSync:
.. figure:: figures/f08-09-9780123850591.png
   :width: 500px
   :align: center

   Một giao thức xác thực khóa công khai không phụ thuộc vào đồng bộ hóa.
   Alice kiểm tra dấu thời gian của mình với đồng hồ của chính mình, và Bob cũng vậy.

8.4.3 Các Giao Thức Xác Thực Khóa Bí Mật
----------------------------------------

Chỉ trong các hệ thống khá nhỏ mới thực tế để phân phối trước các khóa bí mật cho từng cặp thực thể.
Chúng tôi tập trung ở đây vào các hệ thống lớn hơn, nơi mỗi thực thể sẽ có *khóa chủ* (*master key*) riêng,
chỉ chia sẻ với Trung tâm Phân phối Khóa (KDC). Trong trường hợp này, các giao thức xác thực dựa trên khóa bí mật
liên quan đến ba bên: Alice, Bob và một KDC. Sản phẩm cuối cùng của giao thức xác thực là một khóa phiên được chia sẻ giữa Alice và Bob
mà họ sẽ sử dụng để giao tiếp trực tiếp, không cần đến KDC nữa.

.. _fig-needhamSchroeder:
.. figure:: figures/f08-10-9780123850591.png
   :width: 500px
   :align: center

   Giao thức xác thực Needham-Schroeder.

Giao thức xác thực Needham-Schroeder được minh họa trong :numref:`Figure %s <fig-needhamSchroeder>`.
Lưu ý rằng KDC thực tế không xác thực thông điệp ban đầu của Alice và không giao tiếp với Bob.
Thay vào đó, KDC sử dụng kiến thức về các khóa chủ của Alice và Bob để tạo ra một phản hồi mà chỉ Alice mới có thể giải mã
(vì chỉ Alice biết khóa đó) và chứa các thành phần cần thiết để Alice và Bob tự thực hiện phần còn lại của giao thức xác thực.

Nonce trong hai thông điệp đầu tiên nhằm đảm bảo với Alice rằng phản hồi của KDC là mới.
Thông điệp thứ hai và thứ ba bao gồm khóa phiên mới và định danh của Alice, được mã hóa cùng nhau bằng khóa chủ của Bob.
Nó giống như một phiên bản khóa bí mật của chứng chỉ khóa công khai; về bản chất, nó là một tuyên bố được ký bởi KDC
(vì KDC là thực thể duy nhất ngoài Bob biết khóa chủ của Bob) rằng khóa phiên kèm theo thuộc về Alice và Bob.
Mặc dù nonce trong hai thông điệp cuối nhằm đảm bảo với Bob rằng thông điệp thứ ba là mới,
nhưng có một lỗ hổng trong lập luận này.

Kerberos
~~~~~~~~

Kerberos là một hệ thống xác thực dựa trên giao thức Needham-Schroeder và được chuyên biệt hóa cho môi trường client/server.
Ban đầu được phát triển tại MIT, nó đã được IETF chuẩn hóa và có sẵn dưới dạng cả mã nguồn mở lẫn sản phẩm thương mại.
Chúng tôi sẽ tập trung vào một số đổi mới thú vị của Kerberos.

Các client Kerberos thường là người dùng, và người dùng xác thực bản thân bằng mật khẩu.
Khóa chủ của Alice, chia sẻ với KDC, được tạo ra từ mật khẩu của cô ấy—nếu bạn biết mật khẩu, bạn có thể tính được khóa.
Kerberos giả định bất kỳ ai cũng có thể truy cập vật lý vào bất kỳ máy client nào; do đó, việc giảm thiểu rủi ro lộ mật khẩu
hoặc khóa chủ của Alice không chỉ trên mạng mà còn trên bất kỳ máy nào cô ấy đăng nhập là rất quan trọng.
Kerberos tận dụng Needham-Schroeder để đạt được điều này. Trong Needham-Schroeder, thời điểm duy nhất Alice cần dùng mật khẩu
là khi giải mã phản hồi từ KDC. Phần mềm client Kerberos sẽ đợi đến khi nhận được phản hồi từ KDC, nhắc Alice nhập mật khẩu,
tính toán khóa chủ và giải mã phản hồi của KDC, sau đó xóa mọi thông tin về mật khẩu và khóa chủ để giảm thiểu rủi ro lộ lọt.
Cũng lưu ý rằng dấu hiệu duy nhất mà người dùng thấy về Kerberos là khi họ được nhắc nhập mật khẩu.

Trong Needham-Schroeder, phản hồi của KDC cho Alice đóng hai vai trò: Nó cung cấp cho cô ấy phương tiện để chứng minh danh tính
(chỉ Alice mới có thể giải mã phản hồi), và nó cung cấp cho cô ấy một dạng chứng chỉ khóa bí mật hoặc “vé” để trình cho Bob—
khóa phiên và định danh của Alice, được mã hóa bằng khóa chủ của Bob. Trong Kerberos, hai chức năng đó—và cả KDC, trên thực tế—
được tách ra (:numref:`Figure %s <fig-kerberos>`). Một máy chủ tin cậy gọi là Máy chủ Xác thực (AS) đóng vai trò KDC đầu tiên,
cung cấp cho Alice thứ mà cô ấy có thể dùng để chứng minh danh tính—lần này không phải với Bob, mà với một máy chủ tin cậy thứ hai
gọi là Máy chủ Cấp vé (TGS). TGS đóng vai trò KDC thứ hai, phản hồi cho Alice bằng một vé mà cô ấy có thể trình cho Bob.
Điểm hấp dẫn của sơ đồ này là nếu Alice cần giao tiếp với nhiều máy chủ, không chỉ Bob, thì cô ấy có thể lấy vé cho từng máy chủ từ TGS
mà không cần quay lại AS.

.. _fig-kerberos:
.. figure:: figures/f08-11-9780123850591.png
   :width: 600px
   :align: center

   Xác thực Kerberos.

Trong miền ứng dụng client/server mà Kerberos hướng tới, việc giả định một mức độ đồng bộ hóa đồng hồ là hợp lý.
Điều này cho phép Kerberos sử dụng dấu thời gian và thời hạn thay vì nonce của Needham-Schroeder,
và nhờ đó loại bỏ điểm yếu bảo mật của Needham-Schroeder. Kerberos hỗ trợ lựa chọn các hàm băm và mã hóa khóa bí mật,
cho phép nó bắt kịp với các thuật toán mật mã hiện đại.
