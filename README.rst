Giới thiệu về Cuốn sách này
===========================

.. image:: https://github.com/tunggnu/cnasa/actions/workflows/publish-docs.yml/badge.svg
  :align: left
  :alt: deployment status button
  :target: https://github.com/tunggnu/cnasa/actions/

|

Nguồn của *Computer Networks: A Systems Approach* có sẵn trên GitHub theo các điều khoản của giấy phép `Creative Commons (CC BY 4.0) <https://creativecommons.org/licenses/by/4.0>`__. Cộng đồng được mời đóng góp các chỉnh sửa, cải tiến, cập nhật và nội dung mới theo cùng điều khoản. Mặc dù giấy phép này không tự động cấp quyền tạo ra các tác phẩm phái sinh, chúng tôi rất mong muốn thảo luận về các tác phẩm phái sinh (như bản dịch) với các bên quan tâm. Vui lòng liên hệ qua discuss@systemsapproach.org để trao đổi.

Giống như nhiều dự án phần mềm mã nguồn mở, dự án này được khởi đầu bằng nội dung từng bị giới hạn: ấn bản thứ 5 của *Peterson and Davie*, bản quyền thuộc Elsevier. Chúng tôi hy vọng việc mở mã nguồn tài liệu này sẽ giúp nó được phổ biến rộng rãi và thu hút thêm nội dung mới: cập nhật những gì đã có, mở rộng để bao quát các chủ đề mới, và bổ sung thêm tài liệu giảng dạy.

Nếu bạn sử dụng tài liệu này, phần ghi nhận nên bao gồm các thông tin sau:

|  Tiêu đề: *Computer Networks: A Systems Approach*
|  Tác giả: Larry Peterson và Bruce Davie
|  Bản quyền: Elsevier, 2012
|  Nguồn: https://github.com/SystemsApproach/book
|  Giấy phép: `CC BY  4.0 <https://creativecommons.org/licenses/by/4.0>`__

Đọc Sách
--------

Cuốn sách này là một phần của `Hệ thống Series Systems Approach <https://www.systemsapproach.org>`__, với phiên bản trực tuyến được xuất bản tại https://book.systemsapproach.org.

Để theo dõi tiến độ và nhận thông báo về các phiên bản mới, bạn có thể theo dõi dự án trên `Mastodon <https://discuss.systems/@SystemsAppr>`__. Để đọc các bình luận liên tục về cách Internet đang phát triển, cũng như cập nhật về các dự án viết của chúng tôi, bạn có thể đăng ký nhận bản tin `Systems Approach newsletter <https://systemsapproach.org/newsletter/>`__.

Phát hành và Các Phiên bản
--------------------------

Chúng tôi phát hành nội dung mã nguồn mở thay đổi liên tục thay vì xuất bản các cuốn sách cố định, mặc dù bạn có thể tạm coi v6.0 tương đương với ấn bản thứ 6. Đọc :ref:`lời nói đầu <preface>` để biết những điểm mới trong phiên bản này. Lưu ý rằng Morgan Kaufmann (Elsevier) đã xuất bản ấn bản thứ 6 của giáo trình dựa trên một nhánh của v6.0, nhưng về sau, các bản phát hành mã nguồn mở tại đây sẽ không nhất thiết đồng bộ với bất kỳ ấn bản xuất bản nào trong tương lai.

Thông thường, ``master`` chứa một phiên bản tài liệu nhất quán và hợp lý nội bộ. (Nếu coi như mã nguồn, cuốn sách sẽ có thể biên dịch và chạy được.) Nội dung mới đang phát triển sẽ được kiểm tra vào các nhánh cho đến khi có thể hợp nhất vào ``master`` mà không làm hỏng tính nhất quán. Phiên bản web của cuốn sách tại https://book.systemsapproach.org sau đó được tạo liên tục từ ``master``, tương ứng với một bản phát hành bảo trì điển hình (mặc dù chúng tôi không gắn thẻ nó như vậy).

Các bản phát hành nhỏ (ví dụ, v6.1) sẽ được gắn thẻ bất cứ khi nào có đủ nội dung mới để xứng đáng với công sức. Điều này diễn ra hàng quý, và chủ yếu nhằm tạo ra một bản chụp nhanh để mọi người trong một khóa học có thể biết họ đang sử dụng cùng một phiên bản.

Xây dựng Sách
-------------

Để xây dựng phiên bản có thể xem trên web, trước tiên bạn cần tải về mã nguồn:

.. code:: shell

   $ mkdir ~/systemsapproach
   $ cd ~/systemsapproach
   $ git clone https://github.com/systemsapproach/book.git
   $ cd book

Quy trình xây dựng được lưu trong ``Makefile`` và yêu cầu đã cài đặt Python. ``Makefile`` sẽ tạo một môi trường ảo (``venv-docs``) để cài đặt bộ công cụ tạo tài liệu. Bạn cũng có thể cần cài đặt thư viện C ``enchant`` bằng trình quản lý gói của hệ thống để chức năng kiểm tra chính tả hoạt động đúng.

Để tạo HTML trong ``_build/html``, chạy ``make html``.

Để kiểm tra định dạng của sách, chạy ``make lint``.

Để kiểm tra chính tả, chạy ``make spelling``. Nếu có thêm từ, tên riêng, hoặc từ viết tắt nào được đánh vần đúng nhưng không có trong từ điển, vui lòng thêm chúng vào file ``dict.txt``.

Để xem các định dạng đầu ra khác có sẵn, chạy ``make``.

Cách Đóng góp
-------------

Chúng tôi hy vọng nếu bạn sử dụng tài liệu này, bạn cũng sẵn sàng đóng góp trở lại. Nếu bạn mới làm quen với mã nguồn mở, bạn có thể tham khảo hướng dẫn `How to Contribute to Open Source <https://opensource.guide/how-to-contribute/>`__. Trong đó, bạn sẽ biết về việc đăng *Issues* mà bạn muốn được giải quyết, và gửi *Pull Requests* để hợp nhất các cải tiến của bạn trở lại GitHub.

Nếu bạn muốn đóng góp bản vá hoặc nội dung mới, bạn sẽ cần ký `Thỏa thuận cấp phép cho người đóng góp (CLA) <https://github.com/SystemsApproach/book/blob/master/CLA.rst>`__. Bạn sẽ được nhắc ký CLA lần đầu tiên khi gửi pull request.

CLA khá đơn giản: nó xác nhận rằng (a) bạn có quyền đóng góp những gì bạn đóng góp, và (b) những gì bạn đóng góp sẽ được cung cấp cho mọi người khác theo cùng điều khoản `CC BY <https://creativecommons.org/licenses/by/4.0>`__ như nội dung hiện có. CLA hơi khác thường ở chỗ nó nêu rõ quyền của Elsevier (giống như mọi người khác), nhưng điều này nhằm báo hiệu ý định tiếp tục xuất bản giáo trình dựa trên tài liệu này.

Bạn cũng nên làm quen với `hướng dẫn đóng góp <https://github.com/SystemsApproach/book/blob/master/CONTRIBUTING.rst>`__.

Nếu bạn muốn đóng góp và đang tìm kiếm việc gì đó cần chú ý, hãy xem `Bảng Dự án hiện tại <https://github.com/orgs/SystemsApproach/projects/>`__. Chúng tôi cũng muốn mở rộng tập chủ đề/chương vượt ra ngoài bộ ban đầu kế thừa từ ấn bản thứ 5, nên nếu bạn có ý tưởng, rất mong nhận được phản hồi từ bạn. Gửi email tới ``discuss@systemsapproach.org``, hoặc tốt hơn, `tham gia diễn đàn <https://groups.google.com/a/systemsapproach.org/forum/#!forum/discuss>`__.

Cuối cùng, vì đây là một nỗ lực liên tục, chúng tôi sẽ cố gắng ghi lại và theo dõi `tiến độ <https://github.com/SystemsApproach/book/blob/master/status.rst>`__. Hiện tại, hãy coi đây như ghi chú phát hành tạm thời. Thông tin bổ sung về công việc đang tiến hành có thể được tìm thấy trong `wiki <https://github.com/SystemsApproach/book/wiki>`__.

Tham gia cùng chúng tôi
-----------------------

Chúng tôi hy vọng bạn đã nhận được giá trị từ *Computer Networks: A Systems Approach* trong nhiều năm qua, và chúng tôi rất mong bạn cùng tham gia vào hành trình mới này.

| Larry Peterson & Bruce Davie
| Tháng 11 năm 2019
