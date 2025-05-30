Góc nhìn: Tốc độ phát triển tính năng
=====================================

Chương này giới thiệu một số bên liên quan trong mạng máy tính—các nhà thiết kế mạng, lập trình viên ứng dụng, người dùng cuối và nhà vận hành mạng—nhằm giúp làm rõ các yêu cầu kỹ thuật định hình cách mạng được thiết kế và xây dựng. Điều này giả định rằng mọi quyết định thiết kế đều hoàn toàn mang tính kỹ thuật, nhưng thực tế, điều đó thường không đúng. Nhiều yếu tố khác, từ lực lượng thị trường, chính sách của chính phủ, đến các cân nhắc về đạo đức, cũng ảnh hưởng đến cách mạng được thiết kế và xây dựng.

Trong số đó, thị trường là yếu tố ảnh hưởng lớn nhất, và nó tương ứng với sự tương tác giữa các nhà vận hành mạng (ví dụ: AT&T, Comcast, Verizon, DT, NTT, China Unicom), các nhà cung cấp thiết bị mạng (ví dụ: Cisco, Juniper, Ericsson, Nokia, Huawei, NEC), các nhà cung cấp ứng dụng và dịch vụ (ví dụ: Facebook, Google, Amazon, Microsoft, Apple, Netflix, Spotify), và tất nhiên, các thuê bao và khách hàng (tức là cá nhân, nhưng cũng bao gồm cả doanh nghiệp và tổ chức). Ranh giới giữa các bên này không phải lúc nào cũng rõ ràng, với nhiều công ty đảm nhận nhiều vai trò khác nhau. Ví dụ điển hình nhất là các nhà cung cấp dịch vụ đám mây lớn, những người (a) tự xây dựng thiết bị mạng của mình bằng các linh kiện phổ thông, (b) triển khai và vận hành mạng của riêng mình, và (c) cung cấp dịch vụ và ứng dụng cho người dùng cuối trên chính mạng của họ.

Khi bạn tính đến các yếu tố khác này trong quá trình thiết kế kỹ thuật, bạn sẽ nhận ra có một vài giả định ngầm trong phiên bản sách giáo khoa của câu chuyện cần được xem xét lại. Một là việc thiết kế mạng là một hoạt động chỉ diễn ra một lần. Xây dựng một lần và sử dụng mãi mãi (ngoại trừ việc nâng cấp phần cứng để người dùng có thể tận hưởng các cải tiến hiệu năng mới nhất). Thứ hai là công việc xây dựng mạng phần lớn tách biệt với công việc vận hành mạng. Cả hai giả định này đều không hoàn toàn đúng.

Thiết kế mạng rõ ràng là đang phát triển, và chúng tôi đã ghi nhận những thay đổi này qua từng lần xuất bản mới của sách trong nhiều năm qua. Làm điều đó theo mốc thời gian tính bằng năm trước đây là đủ, nhưng bất kỳ ai từng tải và sử dụng ứng dụng điện thoại thông minh mới nhất đều biết rằng bất cứ điều gì đo bằng năm đều quá chậm theo tiêu chuẩn ngày nay. Thiết kế hướng tới sự phát triển phải là một phần của quá trình ra quyết định.

Về điểm thứ hai, các công ty xây dựng mạng gần như luôn là những công ty vận hành mạng đó. Họ được gọi chung là *nhà vận hành mạng*, và bao gồm các công ty đã liệt kê ở trên. Nhưng nếu chúng ta lại nhìn vào lĩnh vực đám mây để lấy cảm hứng, chúng ta sẽ thấy rằng phát triển-và-vận-hành không chỉ đúng ở cấp độ công ty, mà còn là cách các công ty đám mây phát triển nhanh nhất tổ chức các nhóm kỹ thuật của mình: xoay quanh mô hình *DevOps*. (Nếu bạn chưa quen với DevOps, chúng tôi khuyên bạn nên đọc *Site Reliability Engineering: How Google Runs Production Systems* để xem cách nó được thực hành.)

Tất cả những điều này có nghĩa là mạng máy tính hiện đang trải qua một cuộc chuyển đổi lớn, với các nhà vận hành mạng cố gắng đồng thời tăng tốc độ đổi mới (đôi khi gọi là tốc độ phát triển tính năng) và vẫn tiếp tục cung cấp dịch vụ ổn định (duy trì độ tin cậy). Và họ ngày càng thực hiện điều này bằng cách áp dụng các thực tiễn tốt nhất của các nhà cung cấp dịch vụ đám mây, có thể tóm tắt thành hai chủ đề lớn: (1) tận dụng phần cứng phổ thông và chuyển toàn bộ trí tuệ vào phần mềm, và (2) áp dụng quy trình kỹ thuật linh hoạt (agile) phá vỡ rào cản giữa phát triển và vận hành.

Sự chuyển đổi này đôi khi được gọi là “đám mây hóa” hoặc “phần mềm hóa” mạng, và mặc dù Internet luôn có một hệ sinh thái phần mềm mạnh mẽ, nhưng trước đây nó chủ yếu giới hạn ở các ứng dụng chạy *trên* mạng (ví dụ, sử dụng Socket API được mô tả ở :ref:`Mục 1.4 <1.4 Software>`). Điều đã thay đổi là ngày nay các thực tiễn kỹ thuật lấy cảm hứng từ đám mây này đang được áp dụng cho *bên trong* mạng. Cách tiếp cận mới này, gọi là *Mạng Định nghĩa bằng Phần mềm* (SDN), là một bước ngoặt, không hẳn về cách chúng ta giải quyết các thách thức kỹ thuật cơ bản như đóng khung, định tuyến, phân mảnh/lắp ráp lại, lập lịch gói tin, kiểm soát tắc nghẽn, bảo mật, v.v., mà là về tốc độ mạng phát triển để hỗ trợ các tính năng mới.

Sự chuyển đổi này quan trọng đến mức chúng tôi sẽ tiếp tục đề cập đến nó trong phần *Góc nhìn* ở cuối mỗi chương. Như các thảo luận này sẽ chỉ ra, những gì xảy ra trong ngành công nghiệp mạng không chỉ là vấn đề công nghệ, mà còn liên quan đến nhiều yếu tố phi kỹ thuật khác, tất cả đều cho thấy Internet đã ăn sâu vào cuộc sống của chúng ta như thế nào.

.. admonition:: Góc nhìn rộng hơn

   Để tiếp tục đọc về quá trình đám mây hóa Internet, xem
   :ref:`Góc nhìn: Cuộc đua ra biên mạng`.

   Để tìm hiểu thêm về DevOps, chúng tôi khuyến nghị: `Site Reliability
   Engineering: How Google Runs Production Systems
   <https://www.amazon.com/Site-Reliability-Engineering-Production-Systems/dp/149192912X/ref=pd_bxgy_14_img_2/131-5109792-2268338?_encoding=UTF8&pd_rd_i=149192912X&pd_rd_r=4b77155f-234d-11e9-944e-278ce23a35b5&pd_rd_w=qIfxg&pd_rd_wg=12dE2&pf_rd_p=6725dbd6-9917-451d-beba-16af7874e407&pf_rd_r=5GN656H9VEG4WEVGB728&psc=1&refRID=5GN656H9VEG4WEVGB728>`__,
   2016.
