# Chương 9. Quản lý tài khoản người dùng.

> Chương này giải thích cách tạo ra một tài khoản người dùng mới , làm thế nào để chỉnh sửa thuộc tính cũng như xóa bỏ những tài khoản đó. Các bản phân phối Linux khác nhau , có những công cụ khác nhau dành cho những tác vụ này.

## 9.1. Tài khoản là gì?
Khi một máy tính đuợc sử dụng bởi nhiều người, việc chia riêng biệt những người dùng đó ra là một điều cần thiết. Điều này quan trọng ngay cả khi chỉ có duy nhất một người sử dụng máy tại một thời điểm. Do đó , mỗi người dùng sẽ có một username độc nhất, và username đó đuợc sử dụng để đăng nhập.

Một người dùng có nhiều cái để nhắc đến hơn là username của họ. Một *tài khoản* là tất cả các files, các tài nguyên, và các thông tin thuộc về một người dùng. Bạn có thể liên tưởng tới một ngân hàng, mỗi một tài khoản trong ngân hàng sẽ có một lượng tiền đi kèm ,cùng một vài thông tin phụ thêm khác.

## 9.2. Tạo mới một người dùng.
Hạt nhân Linux xem những người dùng như những con số độc lập. Mỗi người dùng sở hữu một số nguyên độc nhất đuợc gọi là *user id* hoặc *uid*, do các con số nhanh hơn và dễ sử lý hơn cách định danh bằng văn bản. Một cơ sở dữ liệu riêng biệt bên ngoài (*không dính líu tới kernel*) sẽ chứa các thông tin như username, tên thật , ..v...v.. cho mỗi uid. 

Để tạo mới một người dùng, bạn cần phải thêm các thông tin về người dùng đó vào trong cơ sở dữ liệu trên máy, sau đó tạo một thư mục home cho anh/cô ta. Đôi khi người quản trị cũng cần phải thiết lập một môi trường riêng cho người dùng đó.

Hầu hết các bản phân phối Linux đi kèm với một chương trình để tạo ra các tài khoản người dùng. Hai chương trình dòng lệnh thường đuợc sử dụng là *adduser* và *useradd*, cũng có những chương trình đồ họa khác mà bạn có thể tham khảo trong kho phần mềm của bản phân phối tương ứng.

### 9.2.1. */etc/passwd* và các file chứa thông tin liên quan.

Cơ sở dữ liệu người dùng cơ bản trong một hệ thống UNIX là một file text, */etc/passwd* (*chúng ta sẽ gọi nó là password file*), file này liệt hệ các tên người dùng hợp lệ và các thông tin liên quan tới những username đó.Mỗi người dùng có một dòng trong file, mỗi dòng lại chứa các field khác nhau như sau :
* Username.
* Trước đây , field này là nơi mà mật khẩu của người dùng đuợc lưu trữ.
* UID.
* Group Id.
* Tên đầy đủ hoặc những mô tả khác về tài khoản.
* Thư mục home.
* Login shell (*chương trình đuợc chạy tại thời điểm đăng nhập*).

Hầu hết các hệ thống Linux sử dụng *shadow password*. Như đã đề cập , trước đây thì mật khẩu của người dùng đuợc lưu trữ trong */etc/passwd*. Phương pháp mới này lưu trữ mật khẩu theo kiểu : mật khẩu đã mật mã hóa đuợc lưu trữ trong một file riêng biệt mà chỉ root mới có thể đọc đuợc. Tệp */etc/passwd* chỉ chứa một con dấu đặc biệt ở field thứ 2 mà thôi. Những chương trình bình thường mà chỉ sử dụng những fields ngoài encrypted password, sẽ không thể truy xuất đuợc vào field này.

### 9.2.2. Lựa chọn một userid và groupid.

Trên hầu hết các hệ thống, không có vấn đề gì quan trọng đối với uid và groupid, nhưng nếu bạn sử dụng Hệ thống tệp tin mạng (*NFS*), bạn cần phải có uid và gid giống nhau trên tất cả các hệ thống. Điều này là bởi vì NFS cũng định danh người dùng với các số nguyên. Nếu bạn không sử dụng NFS, bạn có thể để cho công cụ tạo tài khoản lựa chọn uid và gid tự động cho bạn.

Tuy nhiên, bạn không nên thử sử dụng các uid và các username đã từng tồn tại, bởi vì chủ sở hữu mới của uid và username đó có thể có quyền truy xuất tới các file đuợc tạo bởi chủ sở hữu cũ của uid hoặc username đó.

### 9.2.3. Khởi tạo môi trường cho người dùng.
Khi một người dùng login, shell của họ sẽ load những thông tin đuợc thiết đặt trong */etc/profile*. File này chứa những thông tin , những biến môi trường sẽ tồn tại trong quá trình người đó sử dụng hệ thống. Các nội dung của */etc/profile* ảnh hưởng tới tất cả những người dùng sử dụng cùng một loại shell. Ta nói */etc/profile* là một file môi trường toàn cục.

Những người dùng cũng có thể tùy biến môi trường làm việc của họ thông qua việc chỉnh sửa nội dung của file *.profile* nằm trong thư mục home.

### 9.2.4. Tạo mới người dùng theo cách thủ công.

Để tạo mới một người dùng, hãy tiến hành theo các bước sau:

* Chỉnh sửa file */etc/password* với **vipw** và thêm một dòng mới cho người dùng mới. Bạn hãy cẩn thận với cú pháp của file này.*Đừng chỉnh sửa file này trực tiếp bằng một editor* , **vipw** khóa file này nên các lệnh khác sẽ không thử cập nhật nó cùng lúc. Bạn nên đánh dấu password field với ký tự *\**.
* Tương tự, hãy chỉnh sửa file */etc/group* với **vigr**, nếu bạn cũng muốn tạo mới một group.
* Tạo thư mục home cho người dùng mới bằng lệnh **mkdir**.
* Chỉnh sửa quyền sở hữu và các quyền hạn bằng **chown** và **chmod**. Tùy chọn *-R* sẽ hữu dụng với bạn trong trường hợp này. Các quyền hạn có thể sẽ khác nhau chút ít đối với mỗi tài khoản.
* Đặt khẩu khẩu với lệnh **passwsd**.

Sau khi bạn đã cài đặt password ở bước cuối, tài khoản này sẽ hoạt động.

## 9.3. Thay đổi các thuộc tính của người dùng.
Dưới đây là một vài các lệnh dùng để thay đổi các thuộc tính khác nhau cho một người dùng:
* chfn : Thay đổi trường fullname.
* chsh: Thay đổi login shell.
* passwd: Thay đổi password.

Root có thể sử dụng những lệnh này để thay đổi thuộc tính của bất kỳ người dùng nào. Những người dùng bình thường chỉ có thể thay đổi những thuộc tính thuộc tài khoản của họ mà thôi. Thỉnh thoảng cũng cần thiết để vô hiệu hóa những lệnh này khỏi tầm tay của những người dùng bình thường.

Những tác vụ khác cũng cần phải thao tác bằng tay. Ví dụ, để thay đổi username, bạn cần chỉnh sửa */etc/passwd* (*tất nhiên là với vipw*). Tương tự, để thêm mới hoặc hủy bỏ một group, chúng ta chỉnh sửa file */etc/group* với **vigr**.

## 9.4. Xóa bỏ một người dùng.
Để xóa bỏ một người dùng, đầu tiên bạn cần xóa bỏ tất cả các file của anh/cô ta , các hộp thư, mail aliases , các print job, cron và at job , cũng như các tham chiếu tới người dùng đó. Sau đó bạn tiến hành xóa dòng hợp lệ trong file */etc/passwd* và */etc/group* (*hãy nhớ rằng bạn cần xóa bỏ username đó từ tất cả những group nó đã add*). Vô hiệu hóa người dùng đó cũng là một ý tưởng tốt (*bạn có thể đọc ở section dưới*). Bạn có thể vô hiệu hóa người dùng đó trước khi tiến hành các công đoạn tháo dỡ , nhằm đề phòng người đó sử dụng tài khoản trong khi tài khoản của anh/cô ta đang bị gỡ bỏ.

Một người dùng cũng có thể có nhiều file nằm bên ngoài thư mục home của họ. Bạn có thể tìm chúng bằng lệnh **find**:
```bash
$ find / -user username
```
Tuy nhiên, lưu ý rằng lệnh trên có thể mất khá nhiều thời gian , nếu như đĩa của bạn có dung lượng lớn.

Một vài bản phân phối Linux đi kèm với các lệnh như *deluser* hoặc *userdel*. Tuy nhiên , việc dỡ bỏ người dùng khá dễ dàng, bạn hoàn toàn có thể làm nó bằng tay, và cũng bởi vì những chương trình ấy đôi khi không làm cho bạn mọi thứ.

## 9.5. Vô hiệu hóa tạm thời một người dùng.
Đôi khi người quản trị cần phải vô hiệu hóa tạn thời một tài khoản nào đó, có thể là do người dùng đó có nguy cơ không tôn trọng hệ thống, hoặc do ai đó có nguy cơ sử dụng trái phép tài khoản đó.

Cách tốt nhất để disable một người dùng, chính là thay đổi shell của người đó thành một chương trình , chương trình này chỉ in ra một thông điệp. Bằng cách này, mọi cố gắng truy cập vào tài khoản này đều thất bại. Thông điệp đuợc in ra có thể chứa những thông tin làm cho người dùng hiểu đuợc tại sao tài khoản của họ lại bị vô hiệu hóa, họ có thể liên hệ với người quản trị để khắc phục.

Người quản trị cũng có thể thay đổi username hoặc password thành những thứ khác, nhưng như vậy sau đó người dùng sẽ không biết chuyện gì đang xảy ra.

Một cách đơn giản đó là tạo ra một chương trình , chúng ta sẽ gọi nó là tail script:

```bash
#!/usr/bin/tail +2
This account has been closed due to a security breach.
Please call xxxxxxxx and wait for the men in black to arrive.
```

Hai ký tự đầu tiên sẽ nói cho kernel rằng phần còn lại của dòng đó là lệnh dùng để thông dịch file này. Lệnh *tail* trong trường hợp này sẽ xuất ra mọi thứ ngoại trừ dòng đầu, nội dung đuợc đi đến standard output.

Bạn sẽ đặt file này làm startup shell cho một người dùng. Khi có một cố gắng đăng nhập tới tài khoản của người dùng đó, lời nhắn sẽ đuợc in ra.


> Phần sau sẽ trình bày về vấn đề thao tác với ngày giờ của hệ thống. Các bạn đón đọc nhé.
> Link bài viết trước [\[Series\] Linux  System Administrator's guide. \[Phần 8\]](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-8).
> Link bài viết sau  [\[Series\] Linux  System Administrator's guide. \[Phần cuối\]](https://kipalog.com/posts/Series----Linux-System-Administrator-s-guide---Phan-cuoi).
> Nguồn : [The Linux Documentation Project](http://tldp.org).