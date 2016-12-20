# Giới thiệu

## Nền tảng Serenity là gì?

Serenity là một nền tảng ứng dụng ASP.NET MVC / Javascript được xây dựng trên công nghệ mã nguồn mở.

Mục đích của Serenity là giúp việc phát triển ứng dụng dễ dàng hơn trong khi giảm chi phí bảo trì bằng cách tránh phải viết lại mã nguồn đã có, giảm thời gian dành cho công việc lặp đi lặp lại và áp dụng các thực hành thiết kế phần mềm tốt nhất.


## Nền tảng này dùng làm gì/dành cho ai

Serenity thích hợp nhất với các ứng dụng kinh doanh với rất nhiều biểu mẫu nhập dữ liệu hoặc giao diện quản trị của trang web. Các tính năng của nó cũng có thể hữu ích với nhiều loại ứng dụng web khác.


## Tìm thông tin ở đâu

Sau khi đọc tài liệu này và các bài hướng dẫn, hãy theo dõi các nguồn tài nguyên dưới đây để biết thêm thông tin về Serenity.

<dl>

  <dt>Kho lưu trữ trên Github:</dt>
  <dd><a href='https://github.com/volkanceylan/Serenity'>https://github.com/volkanceylan/Serenity</a></dd>

  <dt>Các vấn đề / câu hỏi</dt>
  <dd><a href='https://github.com/volkanceylan/Serenity/issues'>https://github.com/volkanceylan/Serenity/issues</a></dd>
  
  <dt>Nhật ký thay đổi:</dt>
  <dd><a href='https://github.com/volkanceylan/Serenity/blob/master/CHANGELOG.md'>https://github.com/volkanceylan/Serenity/blob/master/CHANGELOG.md</a></dd>

  <dt>Mẫu ứng dụng Serene:</dt>
  <dd><a href='https://visualstudiogallery.msdn.microsoft.com/559ec6fc-feef-4077-b6d5-5a99408a6681'>https://visualstudiogallery.msdn.microsoft.com/559ec6fc-feef-4077-b6d5-5a99408a6681</a></dd>

  <dt>Mã nguồn Bài hướng dẫn / Ví dụ:</dt>
  <dd><a href='https://github.com/volkanceylan/Serenity-Tutorials'>https://github.com/volkanceylan/Serenity-Tutorials</a></dd>


</dl>


## Ý nghĩa của tên

Serenity theo từ điển có nghĩa là *bình yên*, *thoải mái* và *bình tĩnh*.

Đây là những gì chúng tôi đang cố gắng để đạt được với Serenity. Chúng tôi hy vọng rằng sau khi cài đặt và sử dụng nó, bạn sẽ cũng sẽ cảm thấy như vậy...

## Danh sách tính năng

* A modular, service based web application model
* Code generator to produce initial services / user interface code for an SQL table
* T4 based code generation on server to reference script widgets with intellisense / compile time validation
* T4 based code generation to provide compile time type safety and intellisense while calling AJAX services from script side.
* An attribute based form definition system (prepare UI in server side with a simple C# class)
* Automatic seamless data-binding through form definitions (form <-> entity <-> service).
* Caching Helpers (Local / Distributed)
* Automatic cache validation
* Configuration System (storage medium independent. store settings in database, file, whatever...)
* Simple Logging
* Reporting (reports just provide data, has no dependency on rendering, similar to MVC)
* Script bundling, minification (making use of Node / UglifyJS / CleanCSS) and content versioning (no more F5 / clear browser cache)
* Fluent SQL Builder (SELECT/INSERT/UPDATE/DELETE)
* Micro ORM (also Dapper is integrated)
* Customizable handlers for REST like services that work by reusing information in entity classes and do automatic validation.
* Attribute based navigation menu
* UI Localization (store localized texts in json files, embedded resource, database, in memory class, anywhere)
* Data Localization (using an extension table mechanism helps to localize even data entered by users,  like lookup tables)
* Script widget system (inspired by jQueryUI but more suitable for C# code)
* Client side and server side validation (based on jQuery validate plugin, but abstracts dependency)
* Audit logging (where CDC is not available)
* System for data based integration tests
* Dynamic scripts
* Script side templates

## Lai lịch

> Phần này ban đầu được viết như một giới thiệu Serenity trên CodeProject. Bài viết này đã bị từ chối với lý do là nó không chứa mã nguồn mà là quảng cáo. Họ đã đúng, vì tôi đã đặt một liên kết đến bài hướng dẫn ứng dụng Phim trong tài liệu này chứ không sao chép và dán mã nguồn vào bài viết.

> Bạn có thể bỏ qua chương tiếp theo, nếu bạn không thích đọc lịch sử :)

We, developers, are all solving the same sets of problems everyday. Just like college students working on their problem books.

Even though we know that they are already solved and have answers somewhere, it doesn't stop us from working on them. Actually, it helps us improve our skills, and hey you can't learn without making some mistakes, can you? But we should learn where to draw a line between training and wasting time.

When you start a new project, you have several decisions to make on platform, architecture and set of libraries. Today you have so many choices for every single topic. Yes, having some options is good, as long as they are limited, as our time is not infinite. 

Here is a short history about *Serenity*, which aims to handle common tasks you deal with business applications, and let you spare your precious time focusing on features specific to your application domain.

My first real job in web technologies was in a web agency designing country-specific web sites of some of big names in industry, e.g. automative companies (btw, we are talking about 10+ years past, time flows fast).

As I had a software architect career in desktop applications before I signed there, I was asked to design a ASP.NET WebForms platform for them. They explained that they have many shared modules, like news, galleries, navigation at each site, but as requirements are different, they had to copy/paste then customize code specific to every customer. When they wanted to add a common feature, they had to repeat it for every site.

At that time, there weren't so many CMS systems in market, and I designed one for them, without even knowing it was called a CMS. For me, it wasn't perfect, not even good enough as I just had a few weeks to design it. But they were very pleased with the result, as it took development of new sites down to days/weeks from months. Also resulting code was more manageable than before.

Learning from that experience, and mistakes, that poor-mans CMS became something better. Later, that platform is evolved to be used by applications in varying domains, like a help-desk system, a CRM, ERP, personnel management, electronic document management, university student information system and more.

To be compatible with different kinds of applications, systems and even legacy databases, it had to be flexible and went through many architectural changes.

Now it takes us to Serenity. Even though it is an open source project for about 2 years, it has a much older background. But it is also young, energetic, and is not afraid of change. It can adapt to new technologies as they became popular and stable. This might mean breaking changes from time to time, but we strive to keep them to a minimum without being paranoid about backwards compability.

