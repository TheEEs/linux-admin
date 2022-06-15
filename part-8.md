# Chương 8. Đăng nhập và đăng xuất.

> **UNIX đơn giản, nó chỉ cần một thiên tài để hiểu rằng nó đơn giản** - Dennis Ritchie.

Chương này trình bày về những điều xảy ra  khi một người dùng đăng nhập hoặc đăng xuất. Những sự tương tác của các tiến trình nền, các log files, các file cấu hình và những thứ tương tự cũng sẽ đuợc mô tả khá chi tiết.

## 8.1. Đăng nhập từ terminal.
Bài viết đầu tiên trong series này đã mô tả về những gì xảy ra khi bạn đăng nhập qua một terminal. Đầu tiên, **init** sẽ đảm bảo rằng chương trình **getty** đuợc gọi lên để lắng nghe các kết nối từ terminal. **getty** sẽ lắng nghe, chờ đợi và nhận ra nhu cầu đăng nhập của người dùng. Khi nó nhận ra một người dùng, **getty** sẽ xuất ra một thông báo chào mừng (*đuợc lưu trữ tại /etc/issue*), dấu nhắc lệnh cũng xuất hiện để nhập username, sau khi đã nhập username, **getty** sẽ chạy chương trình **login**. Chương trình này nhận username như một tham số, sau đó nó sẽ yêu cầu người dùng nhập password. Nếu username và password đều trùng khớp, **login** sẽ khởi chạy shell đuợc cấu hình cho người dùng đó, nếu không nó sẽ thoát và tiến trình chạy nó sẽ bị hủy. **init** sẽ nhận ra tiến trình bị hủy đó, và khởi chạy lại một instance mới của **getty** để phục vụ cho phiên đăng nhập tiếp theo.

![Hình ảnh không tìm thấy](https://github.com/TheEEs/linux-admin/blob/master/images/8.1.png?raw=true).

Có nhiều các phiên bản khác nhau của **getty** và **init** đuợc sử dụng, chúng đều có những ưu và nhược điểm. Biết về phiên bản hệ thống bạn đang sử dụng là một ý tưởng tốt. Bạn có thể không cần lo lắng về **getty**, nhưng **init** vẫn rất quan trọng.

## 8.2. Đăng nhập từ mạng.
Hai máy tính trong cùng một mạng thường đuợc liên kết qua một cáp vật lý. Khi chúng giao tiếp trên mạng đó, các chương trình tham gia hội thoại trên mỗi máy được liên kết với nhau thông qua một *liên kết ảo*, bạn có thể hiểu nó là một dây cáp tưởng tượng. Các chương trình ở hai đầu của kết nối ảo đó sẽ sở hữu độc quyền cái dây cáp tưởng tượng của chúng. Tuy nhiên, do *"dây cáp"* đó không có thật, nên hệ điều hành trên hai máy tính có thể có nhiều các kết nối ảo , các kết nối đó cùng chia sẻ dây cáp vật lý. Bằng cách này, chỉ sử dụng một dây cáp, các chương trình khác nhau có thể giao tiếp mà không cần phải biết hoặc quan tâm về quá trình giao tiếp của các chương trình khác. Thậm chí việc nhiều máy tính cùng sử dụng một dây cáp cũng hoàn toàn khả thi, kết nối ảo sẽ tồn tại giữa hai máy tính, và các máy tính khác sẽ bỏ qua những kết nối ảo mà chúng không tham gia.

Khá phức tạp để biểu diễn một mô hình mang tính trừu tượng , chung chung như thế này. Tuy nhiên, nếu bạn hiểu đuợc việc đăng nhập từ mạng khác thế nào so với việc đăng nhập bình thường, điều đó đã đủ tốt rồi. Các kết nối ảo đuợc bật lên khi có hai chương trình trên hai máy tính khác nhau muốn giao tiếp. Trên lý thuyết, bạn có thể đăng nhập từ một máy bất kỳ trong mạng đó tới một máy khác trong cùng mạng, nên sẽ có khả năng tồn tại một lượng kết nối ảo rất lớn. Do đó, việc gọi **getty** cho mỗi một kết nối ảo dường như mang lại hiệu suất không cao. Vì vậy, chúng ta cần những giải pháp khác. 

Có những chương trình có thể làm điều này. Khi đuợc gọi lên, nếu trong quá trình chạy nó nhận ra một yêu cầu đăng nhập từ mạng, chương trình đó sẽ khởi chạy một instance của chính mình để xử lý nhu cầu đăng nhập đó, còn nó thì vẫn tiếp tục lắng nghe các kết nối khác.

Có nhiều giao thức đăng nhập từ mạng. Hai trong những giao thức phổ biến và lâu đời nhất phải kể đến **telnet** và **rlogin**. Cũng có thể có nhiều các kết nối ảo dùng cho các dịch vụ như FTP , HTTP , ..v...v.. đuợc dựng lên kèm theo phiên đăng nhập từ mạng đó. Người ta cho rằng việc mỗi một loại kết nối lại có một tiến trình riêng để lắng nghe là không hiệu quả, vậy nên sẽ chỉ có một tiến trình duy nhất, tiến trình này sẽ lắng nghe các kết nối và sẽ khởi chạy các chương trình thích hợp để xử lý giao thức đuợc thực thi trên kết nối đó. Tiến trình đơn lẻ này đuợc gọi là **inetd** , ngày nay có nhiều phiên bản khác của **inetd** mà bạn có thể tham khảo và sử dụng.

## 8.3. Quá trình login sẽ làm gì ?
Chương trình **login** sẽ quan tâm đến việc chứng thực người dùng bằng cách đảm bảo rằng cả username và password đều khớp, thiết đặt một môi trường cho người dùng bằng cách cài đặt các quyền cho đường truyền nối tiếp, sau đó khởi tạo shell đã đuợc cài đặt trước đó.

Một phần của quá trình hậu-login đó sẽ là xuất nội dung của file */etc/motd*, sau đó kiểm tra các thư điện tử. Công đoạn này có thể bị vô hiệu hóa bằng cách tạo ra một file tên là *.hushlogin* trong thư mục home của người dùng.

Nếu như file */etc/nologin* tồn tại, đăng nhập bị vô hiệu hóa. File này thường đuợc tạo ra bởi chương trình shutdown và các chương trình liên quan khác. **login** sẽ check file này, nếu file này tồn tại thì nó sẽ từ chối mọi nhu cầu đăng nhập. Nếu file này tồn tại, **login** sẽ xuất nội dung của nó ra console trước khi thoát.

**login** sẽ log lại tất cả các luợt đăng nhập thất bại vào một file log. Nó cũng log lại tất cả các đăng nhập của root. Những log này có thể sẽ hữu dụng trong quá trình kiểm tra hệ thống.

Những người dùng hiện tại đang đăng nhập đuợc liệt kê tại */var/run/utmp*. File này hợp lệ cho đến khi hệ thống reboot hoặc shutdown. Một khi hệ thống reboot hoặc shutdown , nội dung của file này sẽ đuợc dọn sạch. Nó liệt kê những người dùng đang đăng nhập và terminal hoặc kết nối ảo mà họ đang sử dụng, kèm theo một vài thông tin hữu dụng khác. Lệnh **who**, **w**, và các lệnh có chức năng tương đương khác sẽ nhìn vào *utmp* để xem xem ai đang đăng nhập.

Tất cả các phiên đăng nhập thành công sẽ đuợc ghi lại tại */var/log/wtmp*. File này sẽ lớn lên mà không bị giới hạn, nên bạn cần phải dọn dẹp nó định kỳ. Ví dụ bạn sẽ có *cron job* dọn dẹp file này hàng tuần. Lệnh **last** sẽ duyệt vào file này.

Cả *utmp* và *wtmp* đều thuộc định dạng nhị phân. Bạn cần những chương trình đặc biệt để phân tích dữ liệu có trong file đó.

## 8.4. Điều khiển truy xuất - Access control.
Một cách truyền thống, cơ sở dữ liệu về những người dùng đuợc chứa trong file */etc/passwd*. Một vài hệ thống sử dụng *shadow passwords*, và di chuyển các mật khẩu vào */etc/shadow*. 

Cơ sở dữ liệu này không chỉ lưu trữ mật khẩu, mà còn kèm theo một vài thông tin về người dùng, ví dụ như tên thật của họ, thư mục home, và logins shell. Những thông tin này cần đuợc công khai, nên mọi người có thể đọc nó. Mật khẩu thì đuợc lưu trữ dưới dạng mật mã hóa. Điều này có một điểm yếu đó là mọi người có thể truy xuất đến các hash , dùng các thuật toán khác nhau để đoán biết mật khẩu đó , vậy nên **shadow password** ra đời, nó sẽ lưu mật khẩu vào một chỗ khác mà chỉ root mới có thể đọc đuợc. Tuy nhiên việc cài đặt các shadow password lên một hệ thống không hỗ trợ loại hình mật khẩu này sẽ khá khó khăn.

Dù có hay không có mật khẩu, điều quan trọng bạn cần nhớ đó là phải tạo ra những mật khẩu thật tốt. Chương trình **crack** có thể đuợc sử dụng để bẻ khóa mật khẩu. Tất cả các mật khẩu có thể đuợc tìm thấy bởi chương trình này không phải là một lựa chọn tốt. Trong khi **crack** có thể đuợc sử dụng bởi những người dùng độc hại, nó cũng có thể đuợc người quản trị sử dụng để gia tăng tính bảo mật cho hệ thống của anh/cô ta. Các mật khẩu tốt có thể đuợc thi hành bởi lệnh **passwd**. 

Cơ sở dữ liệu về những nhóm người dùng đuợc lưu trữ tại */etc/group*. Với những hệ thống hỗ trợ shadow password, cơ sở dữ liệu này có thể nằm tại */etc/shadow.group*.

*root* thường không thể đăng nhập từ hầu hết các terminal hoặc qua mạng. *root user* chỉ có thể đăng nhập qua những terminal đuợc liệt kê trong tệp */etc/sercuretty*. Người ta cũng có thể đăng nhập dưới tư cách một người dùng bình thường thông qua bất kỳ một terminal nào , sau đó sử dụng **su** để trở thành root.

## 8.5. Shell startup.

Khi một shell khởi động, nó sẽ tự động thực thi một hoặc nhiều các file đã đuợc định nghĩa trước đó. Các shell khác nhau thực thi các file khác nhau, bạn có thể tìm đọc documentation của mỗi loại shell để biết thêm thông tin.

Hầu hết các shell , đầu tiên sẽ chạy các file toàn cục (*global*), ví dụ , Bourne shell (*/bin/sh*) và các biến thể của nó thực thi file */etc/profile*. Nó cũng thực thi file *.profile* trong thư mục home của người dùng. */etc/profile* cho phép người quản trị thiết đặt một môi trường người dùng, đặc biệt là thiết lập *PATH* nhằm trỏ tới các thư mục chứa các lệnh để người dùng có thể sử dụng. Mặt khác, tệp *.profile* cho phép những người dùng cục bộ có thể tự tùy biến môi trường của riêng họ, bằng cách tạo mới hoặc ghi đè lên các biến môi trường đã có, hoặc thực thi các lệnh họ muốn.

> Phần sau sẽ trình bày về quản lý tài khoản của nguời dùng, các bạn đón đọc nhé.
> Link bài viết trước [\[Series\] Linux System Administrator's guide. \[Phần 7\]](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-7).
> Link bài viết trước [\[Series\] Linux System Administrator's guide. \[Phần 9\]](https://kipalog.com/posts/Series--Linux-System-Administrators-s-guide---Phan-9)
> Nguồn : [The Linux Documentation Project](http://tldp.org)
