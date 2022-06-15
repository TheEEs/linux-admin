> **"Giữa một thằng to mồm và một con mụ dối trá thì thằng to mồm xem ra vẫn có giá hơn. "** - Ai đó - Bầu cử tổng thống Mỹ.

### 4.8.5. Mounting và unmounting.

Trước khi một filesystem có thể đuợc sử dụng, nó cần phải đuợc *mounted*. Sau đó hệ điều hành sẽ tiến hành thống kê kiểm thử lại mọi thứ để chắc rằng rằng chúng hoạt động tốt. Do tất cả các file trong UNIX đều nằm trong một cây thư mục duy nhất , việc mount một filesystem nào đó sẽ khiến cho toàn bộ filesystem đó chỉ trông như một thư mục con của cây thư mục.
Ảnh dưới đây cho thấy 3 filesystem riêng biệt, hai filesystem phía dưới đuợc mount phía dưới */home* và */usr*.

![Hình ảnh không thể tìm thấy](https://github.com/TheEEs/linux-admin/blob/master/images/4.2.1.png?raw=true)

Việc mount những filesystem có thể đuợc thực hiện thông qua lệnh **mount**:
```bash
$ mount /dev/hda2 /home
$ mount /dev/hda3 /usr
```

Lệnh **mount** bao gồm 2 tham số. Tham số thứ nhất là device file đại diện cho đĩa hoặc phân vùng chứa filesystem. Tham số thứ 2 là thư mục mà filesystem sẽ đuợc mount vào. Sau khi chạy hai lệnh này, những nội dung nằm trong hai filesystem này chỉ trông như nội dung của hai thư mục bình thường là */home* và *usr*. Do đó, một người có thể nói rằng : "*/dev/hda2* đã đuợc mount vào */home*" (tương tự với */usr*). Để duyệt những thứ nằm ở một trong hai filesystem trên, người dùng có thể nhìn vào nội dung của thư mục mà filesystem tương ứng đã mount vào , chẳng khác gì nhìn vào những thư mục bình thường. Bạn nên lưu ý sự khác biệt giữa device file , */dev/hda2* và thư mục mà nó mount vào - */home*. Device file thì cung cấp khả năng truy xuất tới những dữ liệu thô trên đĩa, nhưng thư mục thì cung cấp khả năng truy xuất đến từng file. Những thư mục mà các filesystem mount vào , gọi là những *mount-points*.

Linux hỗ trợ rất nhiều các filesystem. **mount** trước khi mount một filesystem nào đó sẽ thử đoán xem filesystem mà nó sẽ mount thuộc loại nào. Bạn cũng có thể chỉ rõ ra loại filesystem đựoc mount thông qua tùy chọn *-t fstype*, điều này đôi khi cần thiết, vì không phải lúc nào khả năng đoán biết của **mount** cũng hiệu quả. Ví dụ, để mount một đĩa mềm MS-DOS, bạn có thể sử dụng câu lệnh sau:
```bash
$ mount -t msdos /dev/fd0 /floopy
```
*Mount-point* không cần thiết phải là một thư mục trống, nhưng nó nhất định phải tồn tại trước trên đĩa. Mọi file trong thư mục đó, trừ các file đã đuợc mở từ trước hoặc các file có các hard-link nằm bên ngoài , sẽ không thể đuợc truy xuất bằng tên trong khi filesystem đã đuợc mount vào. Điều này không nên bị xem là gây hại, thậm chí người ta còn cho rằng nó còn có lợi là đằng khác. Ví dụ , một vài người thích */tmp* và */var/tmp* cùng trỏ về một nơi , họ sẽ biến */tmp* trở thành một liên kết tượng trưng (*symbolic link*) trỏ tới */var/tmp*. Khi hệ thống khởi động, trước khi */var* đuợc mount thì một thư mục */var/tmp* nằm trong root filesystem sẽ đuợc sử dụng. Khi */var* đã mount, nó sẽ làm cho thư mục */var/tmp* nói trên không thể truy cập đuợc. Nếu */var/tmp* không tồn tại trên root filesystem, việc sử dụng các temporary files trước khi mount */var* là không thể.

Nếu bạn không có ý định viết bất kỳ thứ gì vào filesystem, bạn có thể sử dụng tùy chọn *-r* để chỉ ra rằng bạn đang chuẩn bị mount một mount-point *chỉ đọc - read-only mount-point*. Như vậy sẽ khiến cho kernel dừng mọi cố gắng ghi dữ liệu vào filesystem , nó cũng đồng thời ngăn cho kernel không cập nhật access times của file đó trong inode. Read-only filesystem hữu dụng khi bạn muốn mount các phương tiện lưu trữ không có khả năng ghi dữ liệu nhiều lần, CR-ROMs là một ví dụ điển hình.

Các bạn khi đọc đến đây có thể sẽ nhận ra một vấn đề nho nhỏ , đó là làm thế nào mà root filesystem đuợc mount , vì rõ ràng là nó không đuợc mount vào một filesystem nào khác nữa. Câu trả lời có thể là do một loại ma thuật nào đó rồi :smile:. Root filesystem đuợc mount theo một cách thần thánh nào đó tại thời điểm hệ thống khởi động. Nếu như root filesystem không mount đuợc , bạn sẽ không thể boot vào hệ thống.

Root filesystem thường đuợc mount từ ban đầu dưới dạng một filesystem chỉ đọc. Những đoạn kịch bản đuợc chạy trong quá trình khởi động sẽ gọi **fsck** để xác nhận tính hợp lệ của root filesystem. Nếu không có vấn đề gì xảy ra, filesystem này sẽ đuợc mount lại một lần nữa , lúc này thì cả hai hoạt động đọc/ghi đều đuợc cho phép. **fsck** không được phép chạy trên các filesystem đang đuợc mount, bởi nếu một sự thay đổi nào đó xuất hiện ở filesystem trong khi **fsck** đang chạy, điều đó có thể sinh ra lỗi. Do root filesytem đuợc mount dưới dạng chỉ đọc trong khi nó đang đuợc kiểm thử, **fsck** có thể sửa chữa mọi vấn đề với filesystem này mà không cần lo lắng gì bởi hoạt động *remount* root filesystem sẽ tải lại toàn bộ các meta data mà root filesystem lưu trong bộ nhớ.

Trên nhiều hệ thống, ngoài root filesystem, cũng có những filesystem khác đuợc tự động mount vào hệ thống trong quá trình khởi động. Tệp */etc/fstab* sẽ liệt kê một danh sách các filesystem sẽ đuợc tự động mount. 

Khi một filesystem không cần thiết phải mount thêm nữa, chúng sẽ bị unmount bởi lệnh *umount*. Lệnh này có một tham số, tham số đó có thể device files tương ứng với filesystem hoặc là mount-point. Ví dụ, để unmount thư mục ở ví dụ phía trên, bạn có thể sử dụng các câu lệnh sau:
```bash
$ umount /dev/hda2
$ umount /usr
```
Bạn có thể tìm kiếm hướng dẫn cụ thể cho lệnh này thông qua manual có sẵn trong hệ thống. Một điều bắt buộc khác, đó là bạn luôn nhớ phải unmount các đĩa mềm. *Đừng chỉ tháo nó ra khỏi ổ đĩa*. Bởi vì cơ chế caching khiến cho dữ liệu không đuợc viết vào đĩa cho tới khi bạn unmount nó. Nên việc chỉ tháo đĩa ra khỏi ổ mà không unmount có thể khiến dữ liệu ban đầu bị cắt xén. Nếu như bạn chỉ có ý định đọc dữ liệu từ đĩa mềm, thì điều này dường như không phải vấn đề gì to tát, nhưng nếu mục đích của bạn là ghi dữ liệu lên đĩa, đây sẽ đúng là một thảm họa.

Nếu muốn *mount* hoặc *unmount* một filesystem nào đó, bạn cần có quyền *super user*. Ví dụ : chỉ root user mới có thể làm điều hành. Lý do cho việc này đó là: Nếu bất kỳ ai cũng có thể tự do mount và unmount một filesystem bất kỳ, như vậy sẽ rất dễ dàng để lây lan, giả sử một Trojan cải trang thành */bin/sh* hoặc bất kỳ một chương trình người dùng bình thường nào đó khác chẳng hạn. Tuy nhiên , trong thực tế nhiều khi việc cho phép người dùng sử dụng đĩa mềm là việc cần thiết, dưới đây liệt kê một số cách để làm điều này:
* Đưa cho người dùng mật khẩu root. Cái này là cách tệ hại nhất, nhưng là giải pháp dễ dàng nhất. Nó hoạt động tốt nếu bạn không quan tâm hoặc chẳng có gì để quan tâm về vấn đề bảo mật. Thường cách này diễn ra trên các hệ thống không đuợc kết nối mạng, các hệ thống cục bộ cá nhân.
* Sử dụng một vài chương trình như **sudo** để cho phép người dùng mount/unmount filesystem họ muốn. Đây vẫn là một giải pháp có tính bảo mật kém, nhưng nó không trực tiếp trao quyền hạn cao nhất cho người dùng. Bạn có thể quy định để **sudo** chỉ cho phép chạy những câu lệnh nhất định.
* Khiến cho người dùng sử dụng công cụ *mtools*, một gói phần mềm cho việc quản lý điều hành các filesystem MS-DOS nhưng không cần phải mount chúng.
* Liệt kê các thiết bị sử dụng đĩa mềm, và các *mount-points* nhất định mà chúng có thể mount vào.

Chúng ta có một ví dụ như sau, giả sử ta thêm dòng này vào file */etc/fstab*:
```bash
/dev/fd0 /floppy msdos user,noauto 0 0
```
Các cột lần luợt là : Device file để mount,thư mục làm mount-point , loại filesystem, các tùy chọn, tần số sao lưu (*sử dụng bởi lệnh dump*) , và một số đuợc dùng như một tham số cho lệnh **fsck**(*dùng để xác định thứ tự của filesytem này trong danh sách các filesytem sẽ đuợc kiểm tra trong quá trình khởi động, `0` có nghĩa là filesystem đó không cần kiểm tra*).

Tùy chọn *noauto* ngăn cản việc mount filesytem này khi hệ thống bắt đầu. Tùy chọn *user* chỉ ra rằng mọi user đều có thể mount filesystem này, và vì lý do bảo mật, chúng ta cũng không cho phép việc thực thi các chương trình hoặc việc diễn dịch nội dung các device files nằm trong filesystem đó. Sau đó, người dùng nào cũng có thể mount một đĩa mềm chứa một filesytem msdos với lệnh :
```bash
$ mount /floppy
```
Tương tự vậy, bạn cũng có thể (hoặc cần phải) unmount đĩa mềm đó bằng lệnh *umount*.

Nếu như bạn muốn cấp quyền truy cập tới nhiều loại đĩa mềm, bạn phải chỉ ra nhiều mount-points. Các cài đặt có thể khác cho mỗi mount-point. Ví dụ, để cấp quyền truy cập cho cả đĩa mềm MS-DOS và ext2, bạn cần có hai dòng  sau nằm trong file */etc/fstab*:
```bash
/dev/fd0    /mnt/dosfloppy    msdos   user,noauto  0  0
/dev/fd0    /mnt/ext2floppy   ext2    user,noauto  0  0
```
Hoặc , bạn chỉ cần thêm một dòng sau:
```bash
/dev/fd0 /mnt/floppy auto user,noauto 0 0
```
Tùy chọn *auto* trong cột *filesystem type* cho phép lệnh **mount** truy vấn filesystem đang đuợc mount để xác định xem filesystem đó thuộc loại gì. Tùy chọn này không phải lúc nào cũng hoạt động tốt, nhưng nó luôn chạy ổn trên rất nhiều các filesystem phổ biến.

### 4.8.6. Kiểm tra tính hợp lệ của hệ thống tệp tin với lệnh fsck.
Các hệ thống tệp tin là là những tạo vật phức tạp, và đương nhiên, chúng không hoàn hảo. Đôi khi chúng có xu hướng dễ dàng bị lỗi. Tính hợp lệ và đúng đắn của một filesystem có thể đuợc kiểm tra thông qua lệnh **fsck**. Bạn có thể tự thiết đặt để nó sửa chữa bất kỳ lỗi nhỏ nào mà nó tìm thấy, và thông báo cho người dùng nếu như có lỗi nào đó không thể sửa đuợc. May mắn thay, các mã xây dựng nên các filesystem đuợc debug một cách khá hiệu quả, nên hiếm khi có vấn đề nào xảy ra với các filesystem. Chúng chỉ thường xảy ra do các vấn đề về nguồn điện, các phần cứng không đảm bảo chất lượng. Ví dụ là khi bạn không shutdown một cách hợp lý chẳng hạn.

Nhiều hệ thống đuợc cài đặt để chạy **fsck** ở thời điểm khởi động , nên mọi lỗi đều đuợc phát hiện (*hy vọng là đuợc sửa chữa*) trước khi hệ thống đuợc sử dụng. Việc sử dụng một hệ thông tệp tin hỏng sẽ gây ra một vài, thậm chí là nhiều thứ tệ hại khác. Giả sử, nếu như các cấu trúc dữ liệu trong filesystem trở nên hỗn độn, việc sử dụng filesystem đó sẽ càng khiến các cấu trúc dữ liệu bên trong nó rối beng thêm, dẫn tới các dữ liệu bị mất mát càng nhiều. 

**fsck** có thể mất một chút đáng kể thời gian để chạy trên các hệ thống lớn, và do các lỗi hầu như không bao giờ xảy ra nếu bạn tắt máy đúng cách. Nên không phải lúc nào bạn cũng cần chạy **fsck**, một vài thủ thuật đuợc sử dụng để tránh việc hệ thống sẽ tự gọi **fsck** để kiểm tra mỗi khi khởi động , không cần biết filesystem đó có hợp lệ hay không. Đầu tiên là nếu file */etc/fastboot* tồn tại, sẽ không có sự kiểm tra nào đuợc tiến hành. Thứ hai, đó là hệ thống tệp tin **ext2** có một con dấu đặc biệt nằm trong superblock của nó, nói rằng filesystem đó có đuợc unmount đúng cách sau lần mount trước đó hay không. Con dấu này cho phép **e2fsck**(*một phiên bản của fsck dùng cho ext2*) tránh phải kiểm tra filesystem đó nếu như nó đã đuợc unmount hợp lệ trước đó. Trong khi thủ thuật sử dụng */etc/fastboot* có hoạt động hay không lại phụ thuộc  vào các đoạn kịch bản khởi động của hệ thống, thì thủ thuật thứ 2 hoạt động mọi lúc nếu như trên đĩa của bạn tồn tại ít nhất một *ext2* filesystem.

Sự kiểm tra tự động các filesystem chỉ diễn tra tại thời điểm boot, trên các filesystem đuợc tự động mount trong quá trình boot.

Nếu **fsck** tìm đuợc một vấn đề nào đó mà không thể sửa chữa, thì bạn cần phải có kiến thức chuyên sâu về cách thức chung mà các filesystem hoạt động , hoặc bạn cần có một bản backup tốt. Cái thứ 2 có vẻ dễ dàng hơn. Tôi nghĩ rằng chương trình **debugfs** sẽ hữu dụng với bạn vào một ngày nào đó. Một filesystem muốn đuợc kiểm tra bởi **fsck**? Filesystem đó phải unmount từ trước.

### 4.8.7. KIểm tra các lỗi của đĩa với badblocks.
Việc bạn định kỳ kiểm tra các bad-blocks là một thói quen tốt. Công việc này đuợc hoàn thành bởi lệnh **badblocks**. Nó xuất ra một danh sách chứa số thứ tự các badblock mà nó có thể tìm thấy. Danh sách này có thể đuợc sử dụng bởi lệnh **fsck**, nó sẽ ghi danh sách này vào các cấu trúc dữ liệu của filesystem, hệ điều hành sẽ không cố gắng để sử dụng các bad-blocks đuợc liệt kê trong đó nữa. Hãy nhìn vào ví dụ sau:
```bash
$ badblocks /dev/fd0H1440 1440 > bad-blocks
$ fsck -t ext2 -l bad-block /dev/fd0H1440
Parallelizing fsck version 0.5a (5−Apr−94)
        e2fsck 0.5a, 5−Apr−94 for EXT2 FS 0.5, 94/03/10
        Pass 1: Checking inodes, blocks, and sizes
        Pass 2: Checking directory structure
        Pass 3: Checking directory connectivity
        Pass 4: Check reference counts.
        Pass 5: Checking group summary information.
        /dev/fd0H1440: ***** FILE SYSTEM WAS MODIFIED *****
        /dev/fd0H1440: 11/360 files, 63/1440 blocks
```
Nếu **badblocks** báo cáo một block mà đã đuợc sử dụng, **e2fsck** sẽ cố gắng di chuyển block đó đến một chỗ khác. Nếu block đó thực sự là một block xấu, không chỉ phần bên ngoài, mà nội dung của file nằm trên block đó cũng sẽ bị hỏng.

### 4.8.8. Chống phân mảnh.
Khi một tệp tin đuợc viết vào đĩa, nó không thể luôn luôn đuợc viết vào các block liên tiếp nhau. Một tệp không nằm trên các block liên tếp, ta nói tệp đó bị *phân mảnh*. Bạn sẽ mất nhiều thời gian hơn để đọc một *fragmented file*, do đầu đọc/ghi của ổ đĩa phải di chuyển nhiều hơn. Chắc hẳn bạn mong muốn tránh đuợc sự phân mảnh này, mặc dù đối với các ổ đĩa có bộ nhớ đệm tốt thì sự phân mảnh thường chỉ là một vấn đề cỏn con.

Các hệ thống Linux hiện đại cố gắng hạn chế sự phân mảnh ở mức tối đa bằng cách giữ tất cả các block của một file nằm gần nhau, kể cả khi chúng không thể đuợc lưu trữ trên các block liên tục. Một vài các filesystem (*như ext3 chẳng hạn*) cấp phát một freeblock gần nhất với những block khác trong một file một cách rất hiệu quả. Do đó , gần như chúng ta không cần thiết phải lo lắng về sự phân mảnh trong hệ thống Linux.

Trong những ngày đầu của hệ thống tệp tin *ext2*, người ta đã quan tâm đến việc làm thế nào để hạn chế sự phân mảnh ổ cứng. Do đó dẫn đến sự ra đời của một chương trình chống phân mảnh, gọi là **defrag**. Tuy nhiên, người ta **RẤT** không khuyến nghị bạn sử dụng nó. Chương trình này đuợc thiết kế để sử dụng cho những phiên bản đời đầu của *ext2*, và nó đã không đuợc cập nhật từ năm 1998!!! Tôi chỉ đề cập tới nó với mục đích tham khảo mà thôi.

Cũng có nhiều những chương trình chống phân mảnh cho MS-DOS, chúng di chuyển các block xung quanh filesystem để giảm thiểu sự phân mảnh. Với các filesystem khác, chúng ta **chống phân mảnh** bằng cách sao lưu nó , tạo mới lại filesystem đó , và lưu lại các file từ bản backup vừa rồi. Sao lưu một filesystem trước khi chống phân mảnh là một ý tưởng tốt cho tất cả các filesystem, do nhiều thứ có thể bị làm cho sai lệch trong quá trình chống phân mảnh.

### 4.8.9. Các công cụ khác dùng cho tất cả các filesystem.
Một vài công cụ khác cũng hữu dụng trong việc quản lý các hệ thống tệp tin. **df** sẽ hiển thị tất cả các không gian trống trên một hoặc nhiều filesystem. **du** sẽ hiển thị lượng không gian mà một thư mục và tất cả các file bên trong nó chiếm giữ. Hai chương trình này có thể đuợc sử dụng để quản lý , giảm thiểu sự lãng phí không gian đĩa. 

Chương trình **sync** ép buộc tất cả các block nằm trong *buffer cache* phải đuợc viết vào đĩa. Chúng ta sẽ nói về buffer cache sau. Hiếm khi chúng ta phải tự làm điều này. Một tiến trình ngầm tên là **update** sẽ làm việc này một cách tự động. **sync** hữu dụng trong một vài trường hợp , các tai nạn chẳng hạn. Giả sử như khi **update** hoặc một tiến trình hỗ trợ của nó tên là **bdflush** bị chết, hay khi bạn phải tắt nguồn ngay mà không thể chờ tới khi update đuợc chạy. Các lệnh/tiến trình này cũng có sẵn manuals trong hệ thống. Vậy nên lại một lần nữa **man** tỏ ra là một người bạn tốt khi bạn muốn tìm hiểu về lệnh nào đó khác.
### 4.8.10. Các công cụ khác dùng cho hệ thống tệp tin ext3/ext2.
Bên cạnh bộ tạo ext2(*mke2fs*) và bộ kiểm tra ext2(*e2fsck*), hệ thống tệp tin này còn có một vài các chương trình khác hữu dụng.
Chương trình **tune2fs** có thể điều chỉnh các tham số của hệ thống têp tin, một vài tham số hay ho mà bạn có thể tham khảo qua phía dưới.
* Một giá trị quy định số lần mount tối đa. **e2fsck** sẽ thi hành một luợt kiểm tra với filesystem khi filesystem đó được mount quá số lần tối đa. Với một hệ thống đuợc sử dụng cho việc phát triển hoặc kiểm thử, thì bạn nên gia tăng giá trị của tham số này.
* Giời gian tối thiểu giữa mỗi lần kiểm tra. **e2fsck** cũng có thể thi hành một giá trị biểu thị thời gian tối đa giữa hai check. Hai tham số này có thể đuợc bỏ qua, không cần thiết đặt.
* Số lượng các block dành riêng cho root. *Ext2* dành riêng một vài block cho root nên nếu như filesystem bị đầy, nó vẫn có khả năng thực hiện các tác vụ quản trị hệ thống mà không cần xóa bất kỳ thứ gì. Số lượng các block dành riêng mặc định là 5%, một số lượng vừa phải và không nên bị xem là lãng phí. Tuy nhiên, với đĩa mềm thì việc dành riêng là một số lượng các block như thế là không nên.

Bạn có thể ghé qua manual của **e2fsck** để biết thêm thông tin.

Chương trình **dumpe2fs** hiển thị thông tin về một filesystem ext2 hoặc ext3, hầu hết các thông tin đó tới từ superblock. Dưới đây là một output ví dụ. Một số thông tin trong output dưới đây là những thông số kỹ thuật đòi hỏi bạn phải có hiểu biết về cách mà filesystem hoạt động. Nhưng đa phần trong số chúng là những thứ dễ hiểu, ngay cả với những quản trị viên non trẻ.
```bash
$ dumpe2fs

dumpe2fs 1.32 (09−Nov−2002)
Filesystem volume name:   /
Last mounted on:          not available
Filesystem UUID:          51603f82−68f3−4ae7−a755−b777ff9dc739
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal filetype needs_recovery sparse_super
Default mount options:    (none)
Filesystem state:         clean
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              3482976
Block count:              6960153
Reserved block count:     348007
Free blocks:              3873525
Free inodes:              3136573
First block:              0
Block size:               4096
Fragment size:            4096
Blocks per group:         32768
Fragments per group:      32768
Inodes per group:         16352
Inode blocks per group:   511
Filesystem created:       Tue Aug 26 08:11:55 2003
Last mount time:          Mon Dec 22 08:23:12 2003
Last write time:          Mon Dec 22 08:23:12 2003
Mount count:              3
Maximum mount count:      −1
Last checked:             Mon Nov  3 11:27:38 2003
Check interval:           0 (none)
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:               128
Journal UUID:             none
Journal inode:            8
Journal device:           0x0000
First orphan inode:       655612
Group 0: (Blocks 0−32767)
  Primary superblock at 0, Group descriptors at 1−2
  Block bitmap at 3 (+3), Inode bitmap at 4 (+4)
  Block bitmap at 3 (+3), Inode bitmap at 4 (+4)
  Inode table at 5−515 (+5)
  3734 free blocks, 16338 free inodes, 2 directories
```

**debugfs** là một trình debug cho filesystem. Nó cho phép các truy xuất trực tiếp tới các cấu trúc dữ liệu đuợc lưu trữ trên đĩa, do đó nó có thể đuợc sử dụng để sửa một đĩa mà bị hỏng tới nỗi **fsck** không thể tự động khắc phục. Chương trình này cũng đuợc biết tới là một bộ khôi phục những file bị xóa. Tuy nhiên sử dụng **debugfs** đòi hỏi bạn phải hiểu tất cả những gì bạn muốn làm, bởi vì sơ sẩy một chút thôi, mọi thứ sẽ đi tong hết.

Hai chương trình **dump** và **restore** có thể đuợc sử dụng để sao lưu một hệ thống tệp tin ext2.

## 4.9. Đĩa không có filesystem.
Không phải đĩa hoặc phân vùng nào cũng đuợc sử dụng như một filesystem. Một phân vùng swap chẳng hạn, sẽ chẳng có filesystem nào nằm trên nó. Rất nhiều các đĩa mềm cũng đuợc sử dụng theo kiểu *giả lập băng từ*, vậy nên một **tar**(*tape archive*) hoặc các file khác sẽ đuợc viết thẳng vào đĩa thay vì vào một filesystem. Các đĩa mềm boot Linux cũng không chứa bất kỳ một filesystem nào, chỉ có kernel thô mà thôi.

Việc tránh các filesystem có cái lợi đó là làm cho nhiều không gian khác của đĩa có thể đuợc sử dụng, do các filesystem luôn luôn lưu trữ các *book keeping data structures* chứa thông tin về dữ liệu nằm trong đĩa ở phần đầu của filesystem đó. Nó cũng khiến cho đĩa tương thích dễ dàng hơn với các hệ thống khác, ví dụ: định dạng file **tar** là như nhau trên tất cả các hệ thống, trong khi các filesystem trên các hệ thống khác nhau lại khác nhau. Bạn sẽ gặp một vài lúc mà bạn cần sử dụng đĩa hoặc phân vùng mà không cần một filesystem nào cả. Các đĩa mềm boot Linux cũng hầu như không chứa filesystem nào nằm trên chúng, mặc dù đôi khi chúng có thể.

Một lý do khác để sử dụng đĩa thô đó là để tạo ra các bản sao của nó. Giả sử một filesystem bị hỏng, sẽ là một ý tưởng tốt nếu bạn có một bản sao của toàn bộ các filesystem đó(*kể cả những dữ liệu thô*) trước khi cố gắng sửa chữa nó. Với bản sao lưu vừa rồi bạn có thể bắt đầu sửa chữa lại từ đầu nếu chẳng may trong lần sửa trước bạn đã lỡ làm cho filesystem đó hư hỏng nặng hơn. **dd** là một chương trình giúp thực hiện copy toàn bộ filesystem.
```bash
$ dd if=/dev/fd0H1440 of=floppy−image
2880+0 records in 
2880+0 records out
$ dd if=floppy−image of=/dev/fd0H1440
2880+0 records in
2880+0 records out
$
```
Câu lệnh đầu tiên sẽ tạo ra một bản sao hoàn chỉnh, chính xác của toàn bộ đĩa mềm, sau đó đặt bản sao đó vào trong file *floppy-image*. Lệnh thứ hai sẽ ghi lại toàn bộ nội dung cũ của đĩa mềm(*đuợc lưu trong file floppy-image*) vào đĩa mềm đó.
##4.10. Cấp phát không gian đĩa.
###4.10.1. Lược đồ phân vùng.
Khi bạn muốn phân vùng đĩa, sẽ không có một công tụ hay một cách thức toàn năng nào đó giúp bạn phân vùng một cách dễ dàng. Mỗi hệ thống, mỗi loại đĩa khác nhau lại có những thông số kỹ thuật khác nhau, bạn cần phải xem xét những yếu tố kỹ thuật trong những hoàn cảnh cụ thể để đưa ra giải pháp phân vùng đúng.

Với một trạm làm việc có không gian đĩa hữu hạn, laptop chẳng hạn, bạn có thể có tầm 3 phân vùng. Một phân vùng cho root(*/*), một cho */boot* và còn lại là phân vùng swap. Tuy nhiên, cách chia thế này không phải là cách chia tốt, nó không phải là một giải pháp đuợc khuyến nghị.

Một phương pháp truyền thống, đó là bạn sẽ có một phân vùng (*tuơng đối*) nhỏ dùng cho root và các phân vùng riêng biệt dành cho */usr* và */home*. Nếu như root filesystem của bạn không lớn, và tần số bạn sử dụng nó không nhiều, việc tạo ra một phân vùng riêng biệt cho root filesystem sẽ giúp cho root filesystem tránh khỏi bị hư hỏng mỗi khi hệ thống bị treo, nó cũng giúp cho bạn dễ dàng hơn trong việc khôi phục mỗi khi hệ thống bị crash.

Khi bạn tiến hành tạo ra luợc đồ phân vùng, đây là một vài thứ bạn cần phải nhớ. Bạn sẽ không thể tạo ra một phân vùng riêng cho những thư mục sau đây: */bin, /etc, /dev, /initrd, /lib, /sbin*. Nội dung của những thư mục này đuợc yêu cầu tại thời điểm boot, chúng phải luôn là một phần của thư mục root.

Người ta cũng khuyến nghị rằng bạn nên tạo ra nhũng phân vùng riêng dùng cho */var* và */tmp*, do hai filesystem này chứa những dữ liệu có khả năng bị thay đổi liên tục. Nếu như không tạo ra phân vùng riêng biệt cho 2 filesystem này , bạn có thể tự đẩy mình vào nguy cơ làm cho */* bị đầy, hệ thống dễ bị crash và đương nhiên crash xong rất khó bề phục hồi.

Dưới đây là ví dụ cho luợc đồ phân vùng của một máy chủ:
```bash
Filesystem            Size  Used Avail Use% Mounted on
/dev/hda2             9.7G  1.3G  8.0G  14% /
/dev/hda1             128M   44M   82M  34% /boot
/dev/hda3             4.9G  4.0G  670M  86% /usr
/dev/hda5             4.9G  2.1G  2.5G  46% /var
/dev/hda7              31G   24G  5.6G  81% /home
/dev/hda8             4.9G  2.0G  670M  43% /opt
```
Vấn đề xảy ra khi có nhiều phân vùng đó là nó sẽ chia tổng số lượng không gian trống của đĩa thành các phần nhỏ. Có một cách để tránh vấn đề này, đó là tạo ra những *Logical Volumes*.
### 4.10.2.  Logical Volume Manager (LVM).
Sử dụng LVM cho phép người quản trị tạo ra các đĩa logic(*không có thật*) một cách linh hoạt. Các đĩa logic này có thể đuợc mở rộng một cách linh động nếu như nhiều không gian hơn đuợc yêu cầu.

Trước tiên bạn tạo một phân vùng Linux LVM (type *0x8e*). Tiếp đó các phân vùng vật lý sẽ đuợc thêm vào một *Volume Group* và đuợc chia thành các mảnh nhỏ(*chunks*), hoặc *Physical Extents Volume Group*. Các *chunks* hoặc các *extends* này có thể đuợc thêm vào những *Logical Volumes*. Những *Logical Volumes* này cũng có thể đuợc định dạng như những phân vùng vật lý. Điểm khác biệt duy nhất chính là các phân vùng vật lý không có khả năng mở rộng không gian lưu trữ.

Cách sử dụng LVM vuợt ra khỏi khuôn khổ bài viết này, tuy nhiên trên mạng có nhiều hướng dẫn chi tiết về phương pháp này bằng cả tiếng Anh lẫn tiếng Việt, bạn có thể tìm đọc chúng. Một trong số các bài viết tôi khuyến nghị, bạn có thể tìm đọc nó tại [đây](http://www.tldp.org/HOWTO/LVM-HOWTO/).
### 4.10.3. Đòi hỏi về không gian.
Bản phân phối Linux mà bạn cài đặt sẽ đưa ra một vài sự gợi ý về số lượng không gian đĩa bạn sẽ cần đối với các cấu hình khác nhau. Một số các chương trình được cài đặt độc lập cũng có thể xuất ra các gợi ý như vậy. Việc đó sẽ giúp bạn lên kế hoạch sử dụng đĩa khá hiệu quả, nhưng bạn vẫn nên chuẩn bị cho tương lai , dành riêng ra một vài không gian đĩa cho những thứ mà bạn nghĩ rằng sau này mình sẽ để ý/cần tới.

Độ lớn của không gian dành cho các tệp tin người dùng sẽ phụ thuộc vào những thứ mà người dùng của bạn muốn làm. Hầu hết mọi người đều muốn có càng nhiều không gian càng tốt, nhưng thực tế thì lượng không gian mà họ nên có không phải lúc nào cũng giống như cái họ muốn. Một vài người chỉ sử dụng vài MB đĩa cho việc soạn thảo văn bản, nhưng người khác lại cần vài GB cho việc xử lý ảnh chẳng hạn.

Nhân tiện, bạn nên lưu ý rằng đôi khi sự đo lường là không chuẩn xác, một vài nhà sản xuất nói rằng 1MB=1000KB, trong khi đó nhà sản xuất khác lại nói rằng 1MB=1024KB.  Do đó khi nhân viên bán hàng nói với bạn rằng đây là một ổ 345MB , nhưng bạn lại chỉ thấy nó có 330MB thôi, thì cũng đừng quá bất ngờ nhé.
### 4.10.4.Ví dụ về cấp phát không gian đĩa cứng.
Tôi đã từng có một ổ cứng 10GB. Bây giờ tôi sử dụng một ổ 30GB. Tôi sẽ giải thích cách thức và lý do tại sao tôi lại phân vùng những đĩa này.

Đầu tiên, tôi tạo một phân vùng */boot* có dung lượng 128MB. Dung lượng này rõ ràng là lớn hơn những gì tôi cần, nhưng cũng vừa đủ lớn để cho tôi thêm không gian nếu có biến cố trong tương lai. Tôi tạo ra một phân vùng */boot* riêng biệt là để đảm bảo rằng filesystem này sẽ không bao giờ bị làm đầy, do đó nó sẽ có thể boot đuợc. Sau đó tôi tạo một phân vùng */var* 5GB. Do */var* là nơi mà các logfiles và email đuợc lưu , nên tôi muốn nó độc lập với phân vùng root. Tiếp đến, tôi tạo một phân vùng */home* 15GB. Nó rất tiện dụng trong trường hợp hệ thống bị sập. Nếu như tôi phải cài đặt lại Linux từ đầu, tôi sẽ nói cho trình cài đặt rằng *"Ê, không đựoc format cái phân vùng này*, phân vùng */home* cũ sau đó sẽ đuợc mount lại mà không có dữ liệu nào bị mất đi cả. Cuối cùng, do tôi có 512MB RAM, tôi sẽ tạo một phân vùng swap có dung lượng 1GB. Cuối cùng, tôi còn lại đúng 9GB, tôi sẽ dùng nó cho root filesystem. Tôi lại sử dụng ổ đĩa 10GB cũ , tôi sẽ tạo một phân vùng 8GB dùng cho filesystem */usr*  và chừa lại 2GB. Tôi dự định sẽ dùng 2GB trống này trong tương lai mặc dù tôi chưa nghĩ ra cách để dùng nó. Cuối cùng, bạn có thể thấy bảng phân vùng của tôi sẽ trông như sau:
![Hình ảnh không tìm thấy](https://github.com/TheEEs/linux-admin/blob/master/images/4.2.2.png?raw=true)

### 4.10.5. Thêm nhiều không gian đĩa hơn cho Linux.
Việc thêm không gian đĩa cho Linux rất dễ dàng, ít nhất là sau khi phần cứng đã đuợc cài đặt đúng cách. Bạn định dạng lại nó nếu cần thiết, sau đó tạo ra các phân vùng và các filesystem như tôi đã nói ở phần trên và bài viết trước , cuối cùng là thêm những dòng gợi ý hợp lý vào */etc/fstab* để filesystem của bạn có thể tự động đuợc mount.

### 4.10.6. Mẹo cho việc tiết kiệm không gian đĩa.
Cách tốt nhất để tránh lãng phí không gian đĩa, chính là tránh cài các chương trình không cần thiết. Hầu hết các bản phân phối Linux chỉ cài đặt một phần trong kho chương trình của chúng, chúng sẽ phân tích hoặc khảo sát nhu cầu của bạn để cài đặt các chương trình thích hợp, đôi khi chúng sẽ đưa ra các cảnh báo dạng *"Bạn có thật sự muốn cài đặt chương trình này hay không?"*. Điều này sẽ giúp tiết kiệm rất nhiều không gian của đĩa, do nhiều các phần mềm có dung lượng lớn hoặc chúng sản sinh ra một số lượng dữ liệu lớn. Thậm chí đôi khi bạn cần một gói phần mềm nhất định nào đó, không có nghĩa là bạn cần tất cả những chuơng trình có trong gói phần mềm đó. Ví dụ : một vài documentation online có thể không cần thiết, cũng như các Elisp files dùng cho trình soạn thảo GNU Emacs, một vài phông chữ dùng cho X11, hoặc một vài thư viện lập trình.

Nếu như bạn không thể gỡ cài đặt các gói, bạn có thể tìm tới một giải pháp : *Nén*. Các trình nén như **zip** hoặc **gzip** sẽ nén/giải nén các file độc lập hoặc một nhóm các file. Chương trình **gzexe** sẽ nén các chương trình không đuợc nhìn thấy bởi người dùng (*các chương trình không đuợc sử dụng sẽ bị nén, và đuợc giải nén khi chúng đuợc sử dụng*).

Một cách khác giúp tiết kiệm không gian đĩa, đó chính là dành sự chú ý khi bạn định dạng các phân vùng của mình. Hầu hết các hệ thống tệp tin hiện đại cho phép bạn chỉ rõ kích thước block. Kích thước block lớn sẽ giúp cải thiện hiệu suất của đĩa khi sử dụng các file lớn như database chẳng hạn.

> Chương tiếp theo sẽ trình bày về cơ chế bộ nhớ ảo. Các bạn nhớ đón đọc nhé :smile:
> Link bài viết trước : [\[Series\] Linux System Administrator's guide. \[Phần 4.1\]](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-4-1).
> Link bài viết tiếp theo: [\[Series\] Linux System Administrator's guide. \[Phần 5\]](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-5)
> Nguồn : [The Linux Documentation Project](http://tldp.org).