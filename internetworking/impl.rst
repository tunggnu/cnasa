3.5 Hiện thực hóa (Implementation)
==================================

Cho đến nay, chúng ta đã nói về những gì switch và router phải làm mà chưa mô tả cách chúng thực hiện điều đó. Có một cách đơn giản để xây dựng một switch hoặc router: Mua một bộ xử lý đa dụng và trang bị cho nó nhiều giao diện mạng. Một thiết bị như vậy, chạy phần mềm phù hợp, có thể nhận các gói tin trên một trong các giao diện của nó, thực hiện bất kỳ chức năng chuyển mạch hoặc chuyển tiếp nào được mô tả trong chương này, và gửi các gói tin ra một giao diện khác. Cái gọi là *switch phần mềm* này không quá khác biệt so với kiến trúc của nhiều thiết bị mạng thương mại tầm trung đến thấp hiện nay.\ [#]_ Các hiện thực cung cấp hiệu năng cao thường tận dụng thêm phần tăng tốc phần cứng. Chúng tôi gọi chúng là *switch phần cứng*, mặc dù cả hai cách tiếp cận rõ ràng đều bao gồm sự kết hợp giữa phần cứng và phần mềm.

.. [#] Đây cũng là cách các router Internet đầu tiên, thường được gọi là *gateway* vào thời điểm đó, được hiện thực trong những ngày đầu của Internet.

Phần này cung cấp cái nhìn tổng quan về cả thiết kế thiên về phần mềm và thiên về phần cứng, nhưng đáng chú ý là về câu hỏi phân biệt giữa switch và router, sự khác biệt không quá lớn. Hóa ra, hiện thực của switch và router có quá nhiều điểm chung đến mức một quản trị viên mạng thường chỉ mua một hộp chuyển tiếp duy nhất rồi cấu hình nó thành switch tầng 2 (L2), router tầng 3 (L3), hoặc kết hợp cả hai. Vì thiết kế bên trong của chúng rất giống nhau, chúng tôi sẽ dùng từ *switch* để chỉ cả hai biến thể trong suốt phần này, tránh sự nhàm chán khi phải nói “switch hoặc router” mọi lúc. Chúng tôi sẽ chỉ ra sự khác biệt giữa hai loại khi thích hợp.

3.5.1 Switch phần mềm (Software Switch)
---------------------------------------

:numref:`Hình %s <fig-softswitch>` minh họa một switch phần mềm được xây dựng bằng bộ xử lý đa dụng với bốn card giao diện mạng (NIC). Đường đi của một gói tin điển hình đến, ví dụ, NIC 1 và được chuyển tiếp ra NIC 2 là khá đơn giản: khi NIC 1 nhận gói tin, nó sao chép các byte trực tiếp vào bộ nhớ chính qua bus I/O (PCIe trong ví dụ này) bằng một kỹ thuật gọi là *truy cập bộ nhớ trực tiếp* (DMA). Khi gói tin đã nằm trong bộ nhớ, CPU kiểm tra header của nó để xác định gói nên được gửi ra giao diện nào, và chỉ thị cho NIC 2 truyền gói tin, một lần nữa trực tiếp từ bộ nhớ chính bằng DMA. Điều quan trọng cần lưu ý là gói tin được lưu trữ trong bộ nhớ chính (đây là phần “store” trong store-and-forward), với CPU chỉ đọc các trường header cần thiết vào các thanh ghi nội bộ để xử lý.

.. _fig-softswitch:
.. figure:: figures/impl/Slide1.png
   :width: 300px
   :align: center

   Một bộ xử lý đa dụng được dùng làm switch phần mềm.

Có hai điểm nghẽn tiềm năng với cách tiếp cận này, một hoặc cả hai có thể giới hạn tổng năng lực chuyển tiếp gói tin của switch phần mềm.

Vấn đề đầu tiên là hiệu năng bị giới hạn bởi thực tế là tất cả các gói tin phải đi vào và ra khỏi bộ nhớ chính. Hiệu quả thực tế sẽ thay đổi tùy vào bạn sẵn sàng chi bao nhiêu cho phần cứng, nhưng ví dụ, một máy bị giới hạn bởi bus bộ nhớ 1333-MHz, 64-bit có thể truyền dữ liệu ở tốc độ đỉnh chỉ hơn 100 Gbps—đủ để xây dựng một switch với vài cổng Ethernet 10-Gbps, nhưng khó có thể đủ cho một router cao cấp ở lõi Internet.

Hơn nữa, giới hạn trên này giả định rằng việc di chuyển dữ liệu là vấn đề duy nhất. Điều này khá đúng với các gói dài nhưng lại không đúng với các gói ngắn, vốn là trường hợp xấu nhất mà các nhà thiết kế switch phải tính đến. Với các gói kích thước tối thiểu, chi phí xử lý mỗi gói—phân tích header và quyết định cổng ra—có thể chiếm ưu thế và trở thành điểm nghẽn. Giả sử, ví dụ, một bộ xử lý có thể thực hiện tất cả xử lý cần thiết để chuyển tiếp 40 triệu gói mỗi giây. Điều này đôi khi được gọi là tốc độ packet per second (pps). Nếu gói trung bình là 64 byte, điều này sẽ dẫn đến

.. centered:: Throughput = pps x BitsPerPacket

.. centered:: = 40 × 10\ :sup:`6` × 64 × 8

.. centered:: = 2048 × 10\ :sup:`7`

tức là, thông lượng khoảng 20 Gbps—nhanh, nhưng vẫn thấp hơn đáng kể so với nhu cầu hiện nay. Lưu ý rằng 20 Gbps này sẽ được chia sẻ cho tất cả người dùng kết nối vào switch, giống như băng thông của một đoạn Ethernet (không switch) được chia sẻ cho tất cả người dùng trên môi trường chung. Ví dụ, một switch 16 cổng với tổng thông lượng này chỉ có thể đáp ứng tốc độ trung bình khoảng 1 Gbps trên mỗi cổng.\ [#]_

.. [#] Các con số hiệu năng ví dụ này không đại diện cho tốc độ thông lượng tối đa tuyệt đối mà phần mềm được tối ưu hóa cao trên một máy chủ cao cấp có thể đạt được, nhưng chúng phản ánh giới hạn cuối cùng mà bạn sẽ gặp phải với cách tiếp cận này.

Một điểm cần lưu ý cuối cùng khi đánh giá các hiện thực switch. Các thuật toán không tầm thường được thảo luận trong chương này—thuật toán cây bao phủ dùng cho bridge học, thuật toán vector khoảng cách dùng cho RIP, và thuật toán trạng thái liên kết dùng cho OSPF—*không* trực tiếp là một phần của quyết định chuyển tiếp từng gói. Chúng chạy định kỳ ở chế độ nền, nhưng switch không phải thực thi, ví dụ, mã OSPF cho mỗi gói nó chuyển tiếp. Thao tác tốn kém nhất mà CPU có thể phải thực hiện trên mỗi gói là tra cứu bảng, ví dụ, tra số VCI trong bảng VC, địa chỉ IP trong bảng chuyển tiếp L3, hoặc địa chỉ Ethernet trong bảng chuyển tiếp L2.

.. _key-control-data:
.. admonition:: Ý chính

   Sự phân biệt giữa hai loại xử lý này đủ quan trọng để đặt tên: *mặt phẳng điều khiển* (control plane) tương ứng với xử lý nền cần thiết để “điều khiển” mạng (ví dụ, chạy OSPF, RIP, hoặc BGP sẽ được mô tả ở chương sau) và *mặt phẳng dữ liệu* (data plane) tương ứng với xử lý từng gói để di chuyển gói từ cổng vào đến cổng ra. Vì lý do lịch sử, sự phân biệt này được gọi là *control plane* và *user plane* trong các mạng truy nhập di động, nhưng ý tưởng là như nhau, và thực tế, chuẩn 3GPP định nghĩa CUPS (Control/User Plane Separation) như một nguyên lý kiến trúc.

   Hai loại xử lý này rất dễ bị nhầm lẫn khi cả hai cùng chạy trên một CPU, như trong switch phần mềm ở :numref:`Hình %s <fig-softswitch>`, nhưng hiệu năng có thể được cải thiện đáng kể bằng cách tối ưu hóa cách hiện thực mặt phẳng dữ liệu, và tương ứng, xác định rõ ràng giao diện giữa mặt phẳng điều khiển và mặt phẳng dữ liệu. :ref:`[Tiếp theo] <key-sdn>`

3.5.2 Switch phần cứng (Hardware Switch)
----------------------------------------

Trong phần lớn lịch sử Internet, các switch và router hiệu năng cao là các thiết bị chuyên dụng, được xây dựng bằng các mạch tích hợp chuyên dụng (ASIC). Dù có thể xây dựng các router và switch giá rẻ bằng máy chủ phổ thông chạy chương trình C, ASIC là cần thiết để đạt tốc độ chuyển tiếp yêu cầu.

Vấn đề với ASIC là phần cứng mất nhiều thời gian để thiết kế và chế tạo, nghĩa là thời gian thêm tính năng mới cho switch thường được tính bằng năm, chứ không phải ngày hay tuần như ngành phần mềm hiện nay. Lý tưởng nhất, chúng ta muốn hưởng lợi từ hiệu năng của ASIC và sự linh hoạt của phần mềm.

May mắn thay, những tiến bộ gần đây trong bộ xử lý chuyên biệt theo miền (và các linh kiện phổ thông khác) đã làm điều này trở nên khả thi. Quan trọng không kém, toàn bộ đặc tả kiến trúc cho các switch tận dụng các bộ xử lý mới này hiện đã có sẵn trực tuyến—tương đương phần cứng của *phần mềm mã nguồn mở*. Điều này nghĩa là bất kỳ ai cũng có thể xây dựng một switch hiệu năng cao bằng cách tải bản thiết kế từ web (xem dự án Open Compute Project, OCP, làm ví dụ) giống như bạn có thể tự lắp ráp PC. Trong cả hai trường hợp, bạn vẫn cần phần mềm để chạy trên phần cứng, nhưng cũng như Linux có sẵn để chạy trên PC tự lắp ráp, hiện đã có các stack L2 và L3 mã nguồn mở trên GitHub để chạy trên switch tự lắp ráp. Ngoài ra, bạn có thể đơn giản mua một switch dựng sẵn từ nhà sản xuất switch phổ thông rồi nạp phần mềm của riêng mình lên đó. Phần sau mô tả các *switch bare-metal* mở này, gọi như vậy để phân biệt với các thiết bị đóng, trong đó phần cứng và phần mềm được gắn chặt với nhau, vốn từng thống trị ngành công nghiệp.

.. _fig-baremetal:
.. figure:: figures/impl/Slide2.png
   :width: 500px
   :align: center

   Switch bare-metal sử dụng Network Processing Unit.

:numref:`Hình %s <fig-baremetal>` là mô tả đơn giản hóa của một switch bare-metal. Khác biệt chính so với hiện thực trước đó trên bộ xử lý đa dụng là bổ sung Network Processor Unit (NPU), một bộ xử lý chuyên biệt theo miền với kiến trúc và tập lệnh được tối ưu hóa cho xử lý header gói tin (tức là, để hiện thực mặt phẳng dữ liệu). NPU tương tự về ý tưởng với GPU có kiến trúc tối ưu cho đồ họa máy tính, nhưng trong trường hợp này, NPU được tối ưu cho việc phân tích header gói và quyết định chuyển tiếp. NPU có thể xử lý gói tin (nhận, quyết định chuyển tiếp, và xuất ra) ở tốc độ đo bằng Terabit mỗi giây (Tbps), dễ dàng đáp ứng 32 cổng 100-Gbps, hoặc 48 cổng 40-Gbps như trong hình.

.. sidebar:: Network Processing Units

          Việc chúng tôi dùng thuật ngữ NPU là hơi không chuẩn. Lịch sử, NPU là tên gọi các chip xử lý mạng được định nghĩa hẹp hơn, ví dụ, dùng để hiện thực firewall thông minh hoặc kiểm tra gói sâu. Chúng không đa dụng như các NPU mà chúng tôi đang nói đến ở đây; cũng không hiệu năng cao như vậy. Có vẻ như cách tiếp cận hiện tại sẽ khiến các bộ xử lý mạng chuyên dụng trở nên lỗi thời, nhưng dù sao, chúng tôi thích dùng thuật ngữ NPU vì nó phù hợp với xu hướng xây dựng bộ xử lý chuyên biệt có thể lập trình, bao gồm GPU cho đồ họa và TPU (Tensor Processing Unit) cho AI.

Điểm hay của thiết kế switch mới này là một switch bare-metal bất kỳ giờ đây có thể được lập trình thành switch L2, router L3, hoặc kết hợp cả hai, chỉ bằng phần mềm. Stack phần mềm mặt phẳng điều khiển dùng trong switch phần mềm vẫn chạy trên CPU điều khiển, nhưng ngoài ra, các “chương trình” mặt phẳng dữ liệu được nạp lên NPU để phản ánh các quyết định chuyển tiếp do phần mềm mặt phẳng điều khiển đưa ra. Cách “lập trình” NPU phụ thuộc vào nhà sản xuất chip, hiện có nhiều hãng khác nhau. Trong một số trường hợp, pipeline chuyển tiếp là cố định và bộ xử lý điều khiển chỉ cần nạp bảng chuyển tiếp vào NPU (ý là NPU chỉ biết xử lý một số header nhất định, như Ethernet và IP), nhưng trong các trường hợp khác, pipeline chuyển tiếp cũng có thể lập trình được. P4 là một ngôn ngữ lập trình mới có thể dùng để lập trình các pipeline chuyển tiếp dựa trên NPU như vậy. Ngoài ra, P4 cố gắng che giấu nhiều khác biệt trong tập lệnh NPU bên dưới.

Bên trong, một NPU tận dụng ba công nghệ. Đầu tiên, bộ nhớ SRAM tốc độ cao dùng để buffer các gói khi đang xử lý. SRAM (Static Random Access Memory) nhanh hơn khoảng một bậc so với DRAM (Dynamic Random Access Memory) dùng cho bộ nhớ chính. Thứ hai, bộ nhớ dựa trên TCAM lưu trữ các mẫu bit để so khớp trong các gói đang xử lý. “CAM” trong TCAM là viết tắt của “Content Addressable Memory”, nghĩa là khóa bạn muốn tra cứu trong bảng có thể được dùng như địa chỉ vào bộ nhớ hiện thực bảng đó. “T” là “Ternary”, nghĩa là khóa bạn muốn tra cứu có thể có ký tự đại diện (wildcard), ví dụ, khóa ``10*1`` khớp cả ``1001`` và ``1011``. Cuối cùng, quá trình xử lý để chuyển tiếp mỗi gói được hiện thực bằng một pipeline chuyển tiếp. Pipeline này được hiện thực bằng ASIC, nhưng khi thiết kế tốt, hành vi chuyển tiếp của pipeline có thể thay đổi bằng cách thay đổi chương trình nó chạy. Ở mức cao, chương trình này được biểu diễn như một tập hợp các cặp *(Match, Action)*: nếu bạn khớp trường nào đó trong header, thì thực hiện hành động tương ứng.

Ý nghĩa của việc xử lý gói được hiện thực bằng pipeline nhiều tầng thay vì bộ xử lý một tầng là chuyển tiếp một gói thường liên quan đến việc xem nhiều trường header. Mỗi tầng có thể được lập trình để xem một tổ hợp trường khác nhau. Pipeline nhiều tầng làm tăng một chút độ trễ đầu-cuối cho mỗi gói (tính bằng nano giây), nhưng cũng nghĩa là nhiều gói có thể được xử lý cùng lúc. Ví dụ, Tầng 2 có thể thực hiện tra cứu thứ hai trên gói A trong khi Tầng 1 đang tra cứu ban đầu trên gói B, v.v. Điều này giúp NPU có thể đáp ứng tốc độ đường truyền. Tính đến thời điểm viết sách, mức cao nhất là 25,6 Tbps.

Cuối cùng, :numref:`Hình %s <fig-baremetal>` còn có các linh kiện phổ thông khác giúp mọi thứ trở nên thực tế. Đặc biệt, hiện đã có thể mua các module *transceiver* cắm rời xử lý tất cả chi tiết truy nhập môi trường—dù là Gigabit Ethernet, 10-Gigabit Ethernet, hay SONET—cũng như quang học. Các transceiver này đều tuân theo các chuẩn hình thức như SFP+, có thể kết nối với các linh kiện khác qua bus chuẩn hóa (ví dụ, SFI). Một lần nữa, điều quan trọng là ngành mạng hiện đang bước vào thế giới phổ thông hóa giống như ngành máy tính đã tận hưởng hai thập kỷ qua.

3.5.3 Mạng Định nghĩa Bằng Phần Mềm (Software Defined Networks)
---------------------------------------------------------------

Khi switch ngày càng trở thành hàng hóa phổ thông, sự chú ý hợp lý chuyển sang phần mềm điều khiển chúng. Điều này đặt chúng ta vào trung tâm của xu hướng xây dựng *Mạng Định nghĩa Bằng Phần Mềm* (SDN), một ý tưởng bắt đầu nảy mầm khoảng mười năm trước. Thực tế, chính giai đoạn đầu của SDN đã thúc đẩy ngành mạng chuyển sang các switch bare-metal.

Ý tưởng cơ bản của SDN là điều mà chúng ta đã thảo luận: tách biệt mặt phẳng điều khiển mạng (tức là nơi các thuật toán định tuyến như RIP, OSPF, và BGP chạy) khỏi mặt phẳng dữ liệu mạng (tức là nơi các quyết định chuyển tiếp gói được thực hiện), với phần trước được chuyển thành phần mềm chạy trên máy chủ phổ thông và phần sau được hiện thực bởi các switch bare-metal. Ý tưởng then chốt giúp SDN khả thi là đưa sự tách biệt này đi xa hơn, và định nghĩa một giao diện chuẩn giữa mặt phẳng điều khiển và mặt phẳng dữ liệu. Làm như vậy cho phép bất kỳ hiện thực nào của mặt phẳng điều khiển có thể nói chuyện với bất kỳ hiện thực nào của mặt phẳng dữ liệu; điều này phá vỡ sự phụ thuộc vào giải pháp đóng gói của một nhà cung cấp. Giao diện ban đầu gọi là *OpenFlow*, và ý tưởng tách biệt mặt phẳng điều khiển và dữ liệu này được gọi là disaggregation (phân rã). (Ngôn ngữ P4 đề cập ở phần trước là nỗ lực thế hệ thứ hai nhằm định nghĩa giao diện này bằng cách tổng quát hóa OpenFlow.)

Một khía cạnh quan trọng khác của disaggregation là mặt phẳng điều khiển tập trung về mặt logic có thể được dùng để điều khiển một mặt phẳng dữ liệu mạng phân tán. Chúng ta nói là tập trung về mặt logic vì trạng thái do mặt phẳng điều khiển thu thập được duy trì trong một cấu trúc dữ liệu toàn cục, như Network Map, nhưng hiện thực của cấu trúc này vẫn có thể được phân tán trên nhiều máy chủ. Ví dụ, nó có thể chạy trên đám mây. Điều này quan trọng cho cả khả năng mở rộng và sẵn sàng cao, trong đó điểm mấu chốt là hai mặt phẳng này được cấu hình và mở rộng độc lập với nhau. Ý tưởng này nhanh chóng được áp dụng trong môi trường đám mây, nơi các nhà cung cấp đám mây hiện nay vận hành các giải pháp dựa trên SDN cả trong các trung tâm dữ liệu và trên các mạng backbone kết nối các trung tâm dữ liệu.

Một hệ quả của thiết kế này mà không phải ai cũng nhận ra ngay là mặt phẳng điều khiển tập trung về mặt logic không chỉ quản lý một mạng các switch vật lý (phần cứng) kết nối các máy chủ vật lý, mà còn quản lý một mạng các switch ảo (phần mềm) kết nối các máy chủ ảo (ví dụ, Máy ảo và container). Nếu bạn đếm “cổng switch” (một thước đo tốt cho tất cả thiết bị kết nối vào mạng), thì số lượng cổng ảo trên Internet đã vượt xa số cổng vật lý từ năm 2012.

.. _fig-sdn:
.. figure:: figures/impl/Slide3.png
   :width: 500px
   :align: center

   Hệ điều hành mạng (Network Operating System - NOS) lưu trữ một tập các ứng dụng điều khiển và cung cấp một điểm điều khiển tập trung về mặt logic cho mặt phẳng dữ liệu mạng bên dưới.

Một trong những yếu tố then chốt giúp SDN thành công, như minh họa trong :numref:`Hình %s <fig-sdn>`, là Hệ điều hành mạng (Network Operating System - NOS). Giống như hệ điều hành máy chủ (ví dụ, Linux, iOS, Android, Windows) cung cấp một tập các trừu tượng cấp cao giúp hiện thực ứng dụng dễ dàng hơn (ví dụ, bạn có thể đọc và ghi file thay vì truy cập trực tiếp ổ đĩa), một NOS giúp hiện thực chức năng điều khiển mạng dễ dàng hơn, còn gọi là *Ứng dụng điều khiển* (Control Apps). Một NOS tốt trừu tượng hóa chi tiết của các switch mạng và cung cấp trừu tượng *Network Map* cho lập trình viên ứng dụng. NOS phát hiện các thay đổi trong mạng bên dưới (ví dụ, switch, cổng, và liên kết lên/xuống) và ứng dụng điều khiển chỉ cần hiện thực hành vi mong muốn trên đồ thị trừu tượng này. Điều này nghĩa là NOS đảm nhận việc thu thập trạng thái mạng (phần khó của các thuật toán phân tán như Link-State và Distance-Vector), còn ứng dụng chỉ cần hiện thực thuật toán đường đi ngắn nhất và nạp các luật chuyển tiếp vào các switch bên dưới. Bằng cách tập trung hóa logic này, mục tiêu là tìm ra giải pháp tối ưu toàn cục. Các bằng chứng công bố từ các nhà cung cấp đám mây đã áp dụng cách tiếp cận này xác nhận lợi thế này.

.. _key-sdn:
.. admonition:: Ý chính

   Điều quan trọng cần hiểu là SDN là một chiến lược hiện thực. Nó không thần kỳ làm biến mất các vấn đề cơ bản như việc cần tính toán bảng chuyển tiếp. Nhưng thay vì bắt các switch phải trao đổi thông điệp với nhau như một phần của thuật toán định tuyến phân tán, bộ điều khiển SDN tập trung về mặt logic sẽ thu thập thông tin trạng thái liên kết và cổng từ từng switch, xây dựng một cái nhìn toàn cục về đồ thị mạng, và cung cấp đồ thị đó cho các ứng dụng điều khiển. Từ góc nhìn của ứng dụng điều khiển, tất cả thông tin cần thiết để tính bảng chuyển tiếp đều có sẵn cục bộ. Hãy nhớ rằng bộ điều khiển SDN tập trung về mặt logic nhưng được nhân bản vật lý trên nhiều máy chủ—để đạt hiệu năng mở rộng và sẵn sàng cao—và vẫn còn là câu hỏi gây tranh cãi liệu cách tiếp cận tập trung hay phân tán là tốt nhất. :ref:`[Tiếp theo] <key-tradeoffs>`

Dù các nhà cung cấp đám mây đã tận dụng SDN rất hiệu quả, việc áp dụng nó trong doanh nghiệp và nhà mạng viễn thông vẫn chậm hơn nhiều. Một phần là do khả năng của các thị trường khác nhau trong việc quản lý mạng của họ. Các công ty như Google, Microsoft, Amazon có đội ngũ kỹ sư và DevOps đủ năng lực để tận dụng công nghệ này, trong khi các tổ chức khác vẫn thích các giải pháp đóng gói sẵn và tích hợp hỗ trợ các giao diện quản lý và dòng lệnh mà họ quen thuộc.
