# Chương 3. Các tiện ích quản lý phần cứng.
> **" Những người hiểu biết thì lên tiếng, những kẻ khôn ngoan sẽ lắng nghe. "** -  Jimi Hendrix.

Chương này khá ngắn , nó trình bày về hai đoạn kịch bản có sẵn trong hệ thống là **MAKEDEV** và **mknod** , kèm theo đó là liệt kê một vài trình tiện ích khá hữu dụng. Bạn có thể tìm thấy manual của các tiện ích đó trên Internet hoặc cac manual có sẵn trong hệ thống. 
### 1. MAKEDEV

Hầu hết các **device files** đã đuợc tạo ra và sẵn sàng đuợc sử dụng kể từ khi bạn cài đặt Linux. Nhưng rủi trong trường hợp nào đó một **device files** lại không được tạo ra trong quá trình cài đặt, bạn sẽ phải sử dụng kịch bản **MAKEDEV**. Đoạn kịch bản này thường đuợc lưu trữ tại */dev/MAKEDEV* , nhưng nó cũng thường có một bản sao hoặc một *symbolic link* nằm tại */sbin/MAKEDEV*.

Kịch bản này hay đuợc sử dụng như dưới đây :
```bash 
$ /dev/MAKEDEV −v ttyS0
create ttyS0 c 4 64 root:dialout 0660
```
Câu lênh này sẽ tạo ra một *device file* **/dev/ttyS0** , mijor 4 và minor 64 , là một character device , quyền truy cập là 0660 , device file này đuợc sở hữu bởi root user và group dialout.
**ttyS0** như đã nói ở bài viết trước, là **device node** đại diện cho cổng Serial đầu tiên trên hệ thống. Mijor và Minor của nó đã đuợc kernel biết trước. Trong khi kernel tham chiếu tới các device node này bằng cặp số *major:minor* , thì đối với người dùng hoặc người quản trị, các cặp  số đó rất khó nhớ. Thay vào đó , chúng ta sử dụng các đường dẫn như trên. Quyền truy cập 0660 có nghĩa là chỉ có chủ sở hữu (trong trường hợp này là *root* và các thành viên của group *dialout*) mới có quyền **đọc và ghi** đối với file này.

### 2. Lệnh mknod
**MAKEDEV** là một kịch bản đuợc ưa thích dùng để tạo ra các device file. Tuy nhiên thỉnh thoảng **MAKEDEV** sẽ không thể biết device file bạn tạo ra thuộc loại gì. Đây chính là lúc để **mknod** ra tay. Trong trường hợp sử dụng **mknod** bạn cần biết về major và minor của *device node* mà bạn sắp tạo ra. Ví dụ sau đây , hãy giả sử rằng phiên bản hiện tại của **MAKEDEV** không biết cách để tạo ra **/dev/ttyS0**. Chúng ta cần *mknod* ngay lúc này. Chúng ta đã biết rằng file này nên có major là 4 , minor là 64 , và đó là tất cả những gì cần biết để tạo */dev/ttyS0* với **mknod** :
```bash
$ mknod /dev/ttyS0 c 4 64
$ chown root.dialout /dev/ttyS0
$ chmod 0644 /dev/ttyS0
$ ls −l /dev/ttyS0
crw−rw−−−− 1 root dialout 4, 64 Oct 23 18:23 /dev/ttyS0
```
Như bạn đã thấy, rất nhiều các lệnh đuợc thực hiện để tạo ra một device file theo cách này. Rất may **ttyS0** đã đuợc MAKEDEV hỗ trợ,  trên đây chỉ là một sự minh họa mà thôi.

### 3. Các tiện ích.
Tôi sẽ liệt kê một vài trình tiện ích dưới đây , hy vọng bạn sẽ thấy chúng hữu dụng cho việc quản lý các phần cứng hệ thống:
* lspci - Liệt kê các thiết bị ngoại vi
* lsdev - Liệt kê các phần cứng đã đuợc cài đặt trên hệ thống
* lsusb - Liệt kê các cổng USB
* hdparm - Đọc/Ghi các tham số cho các drive SATA/IDE

Bài viết sau sẽ nói về **Sử dụng ổ đĩa và các phương tiện lưu trữ khác** , các bạn đón đọc nhé.

>Nguồn : [The Linux Documentation Project](http://tldp.org)
>Link bài viết trước : [\[Series\] Linux System Administrator's guide \[Phần 2\]](https://kipalog.com/posts/Series----Linux-System-Administrator-s-guide---Phan-2)
> Link bài viết sau [\[Series\] Linux System Administrator's guide \[Phần 4.1\]](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-4-1)