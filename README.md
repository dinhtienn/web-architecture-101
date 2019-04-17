# Web Architecture 101

Nguồn: [Medium](https://engineering.videoblocks.com/web-architecture-101-a3224e126947)

*Những khái niệm cơ bản về kiến trúc mà tôi ước rằng mình biết khi bắt đầu công việc của một **Web developer***

<div style="display: flex; justify-content: center"><img class="progressiveMedia-image js-progressiveMedia-image" data-src="https://cdn-images-1.medium.com/max/1600/1*K6M-x-6e39jMq_c-2xqZIQ.png" src="https://cdn-images-1.medium.com/max/1600/1*K6M-x-6e39jMq_c-2xqZIQ.png"></div>

Sơ đồ trên là một đại diện khá tốt về kiến trúc của chúng tôi tại Storyblocks. Nếu bạn không phải là một **web developer** có kinh nghiệm, bạn có thể thấy nó phức tạp. Việc dạo qua bên dưới sẽ làm cho mọi thứ dễ tiếp cận hơn trước khi chúng ta đi sâu vào chi tiết của từng thành phần.

*Một người dùng tìm kiếm trên Google rằng "Sương mù và tia nắng mạnh mẽ trong rừng". [Kết quả đầu tiên](https://www.storyblocks.com/stock-image/strong-beautiful-fog-and-sunbeams-in-the-forest-bxxg0dvxdzj6gt9ini) là từ Storyblocks, trang web hình ảnh và vectơ hàng đầu của chúng tôi. Người dùng nhấp vào kết quả chuyển hướng trình duyệt của họ đến trang chi tiết hình ảnh. Bên dưới, trình duyệt của người dùng sẽ gửi yêu cầu đến máy chủ DNS để tra cứu cách liên hệ với Storyblocks, sau đó gửi yêu cầu.*

*Yêu cầu đánh vào bộ cân bằng tải của chúng tôi, chọn ngẫu nhiên một trong số 10 máy chủ web mà chúng tôi đang chạy trang web tại thời điểm đó để xử lý yêu cầu. Máy chủ web tra cứu một số thông tin về hình ảnh từ dịch vụ lưu trữ của chúng tôi và tìm nạp dữ liệu còn lại về nó từ cơ sở dữ liệu. Chúng tôi nhận thấy rằng cấu hình màu cho hình ảnh chưa được tính toán, vì vậy chúng tôi gửi một hồ sơ màu sắc của công việc trực tiếp đến hàng đợi công việc của chúng tôi, máy chủ công việc của chúng tôi sẽ xử lý không đồng bộ, cập nhật cơ sở dữ liệu phù hợp với kết quả.*

*Tiếp đến, chúng tôi cố gắng tìm những bức ảnh tương tự bằng cách gửi yêu cầu đến dịch vụ tìm kiếm của chúng tôi bằng cách sử dụng tiêu đề của ảnh làm đầu vào. Người dùng tình cờ đăng nhập vào Storyblocks với tư cách là thành viên để chúng tôi tra cứu thông tin tài khoản của anh ấy từ dịch vụ tài khoản của chúng tôi. Cuối cùng, chúng tôi khởi động một sự kiện xem trang để dữ liệu của chúng tôi được ghi lại trên hệ thống lưu trữ đám mây của chúng tôi và cuối cùng được tải vào kho dữ liệu, nơi các nhà phân tích sử dụng để giúp trả lời các câu hỏi về doanh nghiệp.*

*Máy chủ bây giờ hiển thị chế độ xem dưới dạng HTML và gửi lại cho trình duyệt của người dùng, trước tiên chuyển qua bộ cân bằng tải. Trang này chứa các tài sản Javascript và CSS mà chúng tôi tải vào hệ thống lưu trữ đám mây của chúng tôi, được kết nối với CDN của chúng tôi, vì vậy trình duyệt của người dùng liên hệ với CDN để lấy nội dung. Cuối cùng, trình duyệt hiển thị rõ ràng trang cho người dùng xem.*

Tiếp theo, tôi sẽ hướng dẫn bạn từng thành phần, đưa ra phần giới thiệu về 101 cho mỗi phần sẽ cung cấp cho bạn một mô hình lạc quan tốt để suy nghĩ về kiến trúc web trong tương lai. Tôi sẽ tiếp tục với một loạt bài viết khác cung cấp các khuyến nghị triển khai cụ thể dựa trên những gì tôi đã học được trong thời gian ở Storyblocks.

## *1. DNS*

DNS là viết tắt của "Domain Name System" và nó là một công nghệ xương sống giúp cho thế giới web trở nên khả thi. Ở cấp độ cơ bản nhất, DNS cung cấp một khóa / giá trị từ một tên miền (ví dụ: google.com) đến địa chỉ IP (ví dụ: 85.129.83.120), được yêu cầu để máy tính của bạn định tuyến yêu cầu đến máy chủ phù hợp. Tương tự với số điện thoại, sự khác biệt giữa tên miền và địa chỉ IP là sự khác biệt giữa cuộc gọi của John Doe, và cuộc gọi 201-867-5309. Giống như bạn cần một cuốn sách điện thoại để tra cứu số của John trong những ngày xưa, bạn cần DNS để tra cứu địa chỉ IP cho một tên miền. Vì vậy, bạn có thể nghĩ DNS là danh bạ điện thoại cho internet.

Có rất nhiều chi tiết khác chúng tôi có thể đi vào đây nhưng chúng tôi sẽ bỏ qua vì nó không quan trọng cho phần giới thiệu 101 cấp của chúng tôi.

## *2. Load Balancer (Cân bằng tải)*

Trước khi đi sâu vào chi tiết về cân bằng tải, chúng ta cần lùi lại một bước để thảo luận về tỷ lệ ngang và dọc trên ứng dụng. Chúng là gì và có gì khác biệt? Rất đơn giản trong bài đăng [StackOverflow](https://stackoverflow.com/questions/11707879/difference-between-scaling-horizontally-and-vertically-for-databases) này, **Mở rộng theo chiều ngang có nghĩa là bạn mở rộng bằng cách thêm nhiều máy hơn vào nhóm tài nguyên của mình trong khi quy mô theo *chiều dọc* có nghĩa là bạn mở rộng bằng cách thêm nhiều năng lượng hơn (ví dụ: CPU, RAM) vào máy hiện có.**

Trong phát triển web, bạn (hầu như) luôn muốn mở rộng quy mô theo chiều ngang bởi vì, để giữ cho nó đơn giản, công cụ sẽ phá vỡ. Máy chủ gặp sự cố ngẫu nhiên. Mạng suy thoái. Toàn bộ trung tâm dữ liệu thỉnh thoảng ngoại tuyến. Có nhiều hơn một máy chủ cho phép bạn lên kế hoạch ngừng hoạt động để ứng dụng của bạn tiếp tục chạy. Nói cách khác, ứng dụng của bạn có "khả năng chịu lỗi của người dùng". Thứ hai, mở rộng ngang cho phép bạn giảm thiểu tối đa các phần khác nhau của backend ứng dụng (máy chủ web, cơ sở dữ liệu, dịch vụ X, v.v.) bằng cách cho mỗi ứng dụng chạy trên các máy chủ khác nhau. Cuối cùng, bạn có thể đạt đến một tỷ lệ mà nó không thể mở rộng theo chiều dọc nữa. Không có máy tính nào trên thế giới đủ lớn để thực hiện tất cả các tính toán ứng dụng của bạn. Hãy nghĩ rằng nền tảng tìm kiếm Google Google là một ví dụ tinh túy mặc dù điều này áp dụng cho các công ty ở quy mô nhỏ hơn nhiều. Ví dụ, Storyblocks chạy 150 đến 400 phiên bản AWS EC2 tại bất kỳ thời điểm nào. Sẽ rất khó khăn để cung cấp toàn bộ sức mạnh tính toán thông qua tỷ lệ dọc.

Ok, trở lại để cân bằng tải. Họ dùng ma thuật để làm cho việc mở rộng theo chiều ngang trở thành có thể. Họ định tuyến các yêu cầu đến tới một trong nhiều máy chủ ứng dụng thường là bản sao / hình ảnh phản chiếu của nhau và gửi phản hồi từ máy chủ ứng dụng trở lại máy khách. Bất kỳ ai trong số họ cũng nên xử lý yêu cầu theo cùng một cách để nó chỉ là vấn đề phân phối các yêu cầu trên toàn bộ máy chủ để không ai trong số họ bị quá tải.

Đó là tất cả. Cân bằng tải về mặt khái niệm là khá thẳng thừng. Bên dưới vỏ bọc chắc chắn có những sự phức tạp nhưng không cần phải đi sâu vào trong phiên bản 101 của chúng tôi.

## *3. Web Application Servers*

Ở cấp độ cao, các máy chủ ứng dụng web tương đối đơn giản để mô tả. Họ thực thi logic nghiệp vụ cốt lõi xử lý yêu cầu của người dùng và gửi lại HTML cho trình duyệt của người dùng. Để thực hiện công việc của mình, họ thường liên lạc với nhiều cơ sở hạ tầng phụ trợ như cơ sở dữ liệu, các lớp bộ đệm, hàng đợi công việc, dịch vụ tìm kiếm, các dịch vụ siêu nhỏ khác, hàng đợi dữ liệu / ghi log, v.v. Như đã đề cập ở trên, bạn thường có ít nhất hai lần và thường xuyên hơn nhiều lần, cắm vào bộ cân bằng tải để xử lý các yêu cầu của người dùng.

Bạn nên biết rằng việc triển khai máy chủ ứng dụng yêu cầu chọn một ngôn ngữ cụ thể (Node.js, Ruby, PHP, Scala, Java, C # .NET, v.v.) và **web MVC framework** cho ngôn ngữ đó (Express for Node.js, Ruby on Rails , Play cho Scala, Laravel cho PHP, v.v.). Tuy nhiên, đi sâu vào chi tiết của các ngôn ngữ và framework này nằm ngoài phạm vi của bài viết này.

## *4. Database Servers*

Mỗi ứng dụng web hiện đại tận dụng một hoặc nhiều cơ sở dữ liệu để lưu trữ thông tin. Cơ sở dữ liệu cung cấp các cách xác định cấu trúc dữ liệu của bạn, chèn dữ liệu mới, tìm dữ liệu hiện có, cập nhật hoặc xóa dữ liệu hiện có, thực hiện tính toán trên dữ liệu và hơn thế nữa. Trong hầu hết các trường hợp, các máy chủ ứng dụng web nói chuyện trực tiếp với một máy chủ, cũng như các máy chủ công việc. Ngoài ra, mỗi backend có thể có cơ sở dữ liệu riêng của mình mà cách ly với phần còn lại của ứng dụng.

Trong khi tôi tránh đi sâu vào các công nghệ cụ thể cho từng thành phần kiến ​​trúc, tôi có thể sẽ làm bạn không hài lòng khi không đề cập chi tiết về cơ sở dữ liệu: SQL và NoSQL.

SQL là viết tắt của “Structured Query Language” và được phát minh vào những năm 1970 để cung cấp một cách truy vấn tiêu chuẩn cho các bộ dữ liệu quan hệ có thể truy cập được đối tượng rộng. Cơ sở dữ liệu SQL lưu trữ dữ liệu trong các bảng được liên kết với nhau thông qua các ID chung, điển hình là số nguyên. Hãy xem qua một ví dụ đơn giản về lưu trữ thông tin địa chỉ lịch sử cho người dùng. Bạn có thể có hai bảng, user và user_addresses, được liên kết với nhau bởi id người dùng. Xem hình ảnh dưới đây cho một phiên bản đơn giản. Các bảng được liên kết vì cột user_id trong user_addresses là một khóa ngoại khóa với cột id trong bảng người dùng.

<div style="display: flex; justify-content: center"><img class="progressiveMedia-image js-progressiveMedia-image" data-src="https://cdn-images-1.medium.com/max/1600/1*Ln39QPggpJVMAScUBsrcCQ.png" src="https://cdn-images-1.medium.com/max/1600/1*Ln39QPggpJVMAScUBsrcCQ.png"></div>

Nếu bạn không biết nhiều về SQL, tôi khuyên bạn nên tìm hiểu hướng dẫn như bạn có thể tìm thấy ở Khan Academy tại [đây](https://www.khanacademy.org/computing/computer-programming/sql). Nó có mặt khắp nơi trong phát triển web, do đó, bạn ít nhất muốn biết những điều cơ bản để thiết kế đúng một ứng dụng.

NoQuery, viết tắt của từ Non-SQL, là một bộ công nghệ cơ sở dữ liệu mới hơn đã xuất hiện để xử lý lượng dữ liệu khổng lồ có thể được tạo ra bởi các ứng dụng web quy mô lớn (hầu hết các biến thể của SQL không có quy mô theo chiều ngang rất tốt và chỉ có thể mở rộng theo chiều dọc đến một điểm nhất định). Nếu bạn không biết gì về NoSQL, tôi khuyên bạn nên bắt đầu với một số giới thiệu đáng giá:

* <https://www.w3resource.com/mongodb/nosql.php>

* <http://www.kdnuggets.com/2016/07/seven-steps-understanding-nosql-databases.html>

* <https://resources.mongodb.com/getting-started-with-mongodb/back-to-basics-1-introduction-to-nosql>

Tôi cũng sẽ ghi nhớ rằng, nhìn chung, [ngành công nghiệp đang liên kết với SQL như một **interface** ngay cả đối với cơ sở dữ liệu NoSQL](https://blog.timescale.com/why-sql-beating-nosql-what-this-means-for-future-of-data-time-series-database-348b777b847a/) vì vậy bạn thực sự nên học SQL, nếu bạn không biết gì về nó. Có rất nhiều cách để tránh nó ngày nay.

## *5. Caching Service*

Dịch vụ lưu trữ đệm cung cấp kho lưu trữ dữ liệu khóa / giá trị đơn giản cho phép lưu và tra cứu thông tin gần nhanh đến O(1) lần. Các ứng dụng thường tận dụng các dịch vụ lưu trữ để lưu kết quả của các tính toán đắt tiền để có thể lấy lại kết quả từ bộ đệm thay vì tính toán lại chúng vào lần tiếp theo khi chúng cần. Một ứng dụng có thể lưu trữ kết quả từ truy vấn cơ sở dữ liệu, gọi đến các dịch vụ bên ngoài, HTML cho một URL nhất định và nhiều hơn nữa. Dưới đây là một số ví dụ từ các ứng dụng trong thế giới thực:

* Google lưu trữ kết quả tìm kiếm cho các truy vấn tìm kiếm phổ biến như chú chó ăn trộm hoặc Taylor Swift, thay vì tính lại chúng mỗi lần

* Facebook lưu trữ nhiều dữ liệu bạn nhìn thấy khi đăng nhập, chẳng hạn như dữ liệu bài đăng, bạn bè, v.v. Đọc bài viết chi tiết về công nghệ lưu trữ bộ nhớ cache của Facebook tại [đây](https://medium.com/@shagun/scaling-memcache-at-facebook-1ba77d71c082).

* Storyblocks lưu trữ đầu ra HTML từ render React phía máy chủ, kết quả tìm kiếm, kết quả đánh máy, v.v.

Hai công nghệ máy chủ bộ nhớ đệm phổ biến nhất là Redis và Memcache. Tôi sẽ đi sâu vào chi tiết hơn ở đây trong một bài viết khác.

## *6. Job Queue & Servers (Hàng đợi và máy chủ)*

Hầu hết các ứng dụng web cần thực hiện một số công việc không đồng bộ phía sau hậu trường mà không liên quan trực tiếp đến việc đáp ứng yêu cầu của người dùng. Chẳng hạn, Google cần thu thập dữ liệu và lập chỉ mục toàn bộ internet để trả về kết quả tìm kiếm. Nó không làm điều này mỗi khi bạn tìm kiếm. Thay vào đó, nó thu thập dữ liệu web không đồng bộ, cập nhật các chỉ mục tìm kiếm trên đường đi.

Mặc dù có các kiến ​​trúc khác nhau cho phép thực hiện công việc không đồng bộ, nhưng phổ biến nhất là thứ mà tôi gọi là “job queue”  của Kiến trúc xếp hàng. Nó bao gồm hai thành phần: một hàng đợi các công việc cần được thực thi, cần được chạy và một hoặc nhiều Sercer (thường được gọi là "workers") chạy các công việc trong hàng đợi.

Hàng đợi công việc lưu trữ một danh sách các công việc cần được chạy không đồng bộ. Đơn giản nhất là hàng đợi nhập trước xuất trước (FIFO) mặc dù hầu hết các ứng dụng cuối cùng đều cần một số hệ thống xếp hàng ưu tiên. Bất cứ khi nào ứng dụng cần một công việc để chạy, theo một lịch trình thông thường hoặc được xác định bởi hành động của người dùng, nó chỉ cần thêm công việc phù hợp vào hàng đợi.

Ví dụ, Storyblocks tận dụng hàng đợi công việc để cung cấp năng lượng cho nhiều công việc hậu trường cần thiết để hỗ trợ thị trường của chúng tôi. Chúng tôi chạy các công việc để mã hóa video và ảnh, xử lý CSV để gắn thẻ siêu dữ liệu, tổng hợp thống kê người dùng, gửi email đặt lại mật khẩu và hơn thế nữa. Chúng tôi đã bắt đầu với một hàng đợi FIFO đơn giản mặc dù chúng tôi đã nâng cấp lên hàng đợi ưu tiên để đảm bảo rằng các hoạt động nhạy cảm với thời gian như gửi email đặt lại mật khẩu đã được hoàn thành càng sớm càng tốt.

**Job servers** xử lý công việc. Nó thăm dò hàng đợi công việc để xác định xem có công việc nào để làm hay không và nếu có, nó sẽ loại bỏ công việc ra khỏi hàng đợi và thực hiện nó. Các lựa chọn ngôn ngữ và framework  cơ bản cũng nhiều như đối với các máy chủ web, vì vậy tôi sẽ không chi tiết trong bài viết này.

## *7. Full-text Search Service*

Rất nhiều nếu không phải hầu hết các ứng dụng web đều hỗ trợ một số tính năng tìm kiếm trong đó người dùng cung cấp kiểu nhập văn bản (thường được gọi là "query") Công nghệ cung cấp năng lượng cho chức năng này thường được gọi là "[Tìm kiếm toàn văn bản](https://en.wikipedia.org/wiki/Full-text_search)", có thể sử dụng một [chỉ mục đảo ngược](https://en.wikipedia.org/wiki/Inverted_index) để tìm kiếm nhanh các tài liệu có chứa các từ khóa truy vấn.

<div style="display: flex; justify-content: center"><img class="progressiveMedia-image js-progressiveMedia-image" data-src="https://cdn-images-1.medium.com/max/1600/1*gun_BpdDH9KrNna1NnaocA.png" src="https://cdn-images-1.medium.com/max/1600/1*gun_BpdDH9KrNna1NnaocA.png"></div>

*Example showing how three document titles are converted into an inverted index to facilitate fast lookup from a specific keyword to the documents with that keyword in the title. Note, common words such as “in”, “the”, “with”, etc. (called stop words), are typically not included in an inverted index.*

*Ví dụ cho thấy cách ba tiêu đề tài liệu được chuyển đổi thành một chỉ mục đảo ngược để tạo điều kiện tra cứu nhanh từ một từ khóa cụ thể đến các tài liệu có từ khóa đó trong tiêu đề. Lưu ý, các từ phổ biến, chẳng hạn như "in", "the", "with", v.v. (được gọi là các **stop words**), thường không được bao gồm trong một chỉ mục đảo ngược*

Mặc dù nó có thể thực hiện tìm kiếm toàn văn bản trực tiếp từ một số cơ sở dữ liệu (ví dụ: [MySQL hỗ trợ tìm kiếm toàn văn bản](https://dev.mysql.com/doc/refman/5.7/en/fulltext-search.html)), nhưng nó có thể chạy một dịch vụ tìm kiếm riêng biệt, tính toán và lưu trữ chỉ mục đảo ngược và cung cấp giao diện truy vấn. Nền tảng tìm kiếm toàn văn bản phổ biến nhất hiện nay là [Elaticsearch](https://www.elastic.co/products/elasticsearch) mặc dù có các tùy chọn khác như [Sphinx](http://sphinxsearch.com/) hoặc [Apache Solr](http://lucene.apache.org/solr/features.html).

## *8. Services*

Khi một ứng dụng đạt đến một quy mô nhất định, có khả năng sẽ có một số **dịch vụ** nhất định được khắc phục để chạy như các ứng dụng riêng biệt. Họ không tiếp xúc với thế giới bên ngoài nhưng ứng dụng và các dịch vụ khác tương tác với họ. Storyblocks, ví dụ, có một số dịch vụ hoạt động và theo kế hoạch:

* **Dịch vụ tài khoản** lưu trữ dữ liệu người dùng trên tất cả các trang web của chúng tôi, cho phép chúng tôi dễ dàng cung cấp các cơ hội bán chéo và tạo trải nghiệm người dùng thống nhất hơn

* **Dịch vụ nội dung** lưu trữ siêu dữ liệu cho tất cả nội dung video, âm thanh và hình ảnh của chúng tôi. Nó cũng cung cấp các giao diện để tải xuống nội dung và xem lịch sử tải xuống.

* **Dịch vụ thanh toán** cung cấp một giao diện để thanh toán thẻ tín dụng của khách hàng.

* **Dịch vụ HTML → PDF** cung cấp giao diện đơn giản chấp nhận HTML và trả về tài liệu PDF tương ứng.

## *9. Data*

Ngày nay, các công ty sống và chết dựa trên mức độ họ khai thác dữ liệu. Hầu như mọi ứng dụng ngày nay, một khi nó đạt đến một quy mô nhất định, tận dụng một **pipeline** dữ liệu để đảm bảo rằng dữ liệu có thể được thu thập, lưu trữ và phân tích. Một **pipeline** điển hình có ba giai đoạn chính:

1. Ứng dụng sẽ gửi dữ liệu, điển hình là các sự kiện về tương tác của người dùng, đến dữ liệu của **firehose**, nơi cung cấp giao diện truyền phát để nhập và xử lý dữ liệu. Thông thường, dữ liệu thô được chuyển đổi hoặc tăng cường và chuyển sang một loại thuốc chữa cháy khác. AWS Kinesis và Kafka là hai công nghệ phổ biến nhất cho mục đích này.

2. Dữ liệu thô cũng như dữ liệu được chuyển đổi / tăng cường cuối cùng được lưu vào bộ nhớ đám mây. AWS Kinesis cung cấp một cài đặt có tên là fire firehose, giúp lưu dữ liệu thô vào bộ lưu trữ đám mây (S3) cực kỳ dễ dàng để cấu hình.

3. Dữ liệu được chuyển đổi / tăng cường thường được tải vào kho dữ liệu để phân tích. Chúng tôi sử dụng AWS Redshift, cũng như một phần lớn và đang phát triển của thế giới khởi nghiệp, mặc dù các công ty lớn hơn sẽ thường sử dụng Oracle hoặc các công nghệ kho độc quyền khác. Nếu các bộ dữ liệu đủ lớn, công nghệ NoSQL MapReduce giống như Hadoop có thể được yêu cầu để phân tích.

Một bước nữa mà không được hình dung trong sơ đồ kiến ​​trúc: tải dữ liệu từ ứng dụng và dịch vụ cơ sở dữ liệu vận hành vào kho dữ liệu. Ví dụ: tại Storyblocks, chúng tôi tải VideoBlocks, AudioBlocks, Storyblocks, dịch vụ tài khoản và cơ sở dữ liệu cổng thông tin đóng góp vào Redshift mỗi đêm. Điều này cung cấp cho các nhà phân tích của chúng tôi một bộ dữ liệu tổng thể bằng cách định vị dữ liệu kinh doanh cốt lõi cùng với dữ liệu sự kiện tương tác người dùng của chúng tôi.

## *10. Cloud storage*

AWS lưu trữ là một cách đơn giản và có thể mở rộng để lưu trữ, truy cập và chia sẻ dữ liệu qua Internet theo [AWS](https://aws.amazon.com/what-is-cloud-storage/). Bạn có thể sử dụng nó để lưu trữ và truy cập ít nhiều bất cứ thứ gì bạn lưu trữ trên hệ thống tệp cục bộ với lợi ích là có thể tương tác với nó thông qua RESTful API qua HTTP. Cho đến nay, Amazon cung cấp S3 là bộ lưu trữ đám mây phổ biến nhất hiện nay và là nơi chúng tôi dựa vào rộng rãi tại Storyblocks để lưu trữ tài sản video, ảnh và âm thanh, CSS và Javascript, dữ liệu sự kiện người dùng của chúng tôi và nhiều hơn nữa.

## *11. CDN*

CDN là viết tắt của “Content Delivery Network” và công nghệ cung cấp cách thức phục vụ các tài sản tĩnh như HTML tĩnh, CSS, Javascript và hình ảnh trên web nhanh hơn nhiều so với phục vụ chúng từ một máy chủ gốc. Nó hoạt động bằng cách phân phối nội dung trên nhiều máy chủ của **edge** trên toàn thế giới để người dùng cuối cùng tải xuống các **assets** từ các máy chủ của Edge edge thay vì máy chủ gốc. Ví dụ trong hình ảnh bên dưới, một người dùng ở Tây Ban Nha yêu cầu một trang web từ một trang web có máy chủ gốc ở NYC, nhưng các tài sản tĩnh cho trang được tải từ máy chủ cạnh CDN, ở Anh, ngăn chặn nhiều HTTP chậm Đại Tây Dương yêu cầu.

<div style="display: flex; justify-content: center"><img class="progressiveMedia-image js-progressiveMedia-image" data-src="https://cdn-images-1.medium.com/max/1600/1*ZkC_5865Hx-Cgph3iPJghw.png" src="https://cdn-images-1.medium.com/max/1600/1*ZkC_5865Hx-Cgph3iPJghw.png"></div>

*[Source](https://www.creative-artworks.eu/why-use-a-content-delivery-network-cdn/)*

## Kết thúc

Và đó là một gói trên Kiến trúc Web 101. Tôi hy vọng bạn thấy điều này hữu ích và hy vọng sẽ đăng một loạt 201 bài viết cung cấp các bài đi sâu vào một số thành phần này trong suốt một hoặc hai năm tới.