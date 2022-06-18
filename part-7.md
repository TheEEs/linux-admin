# Chương 7. Khởi động và tắt máy.

> Chương này giải thích những thứ xảy ra trong quá trinh mà một hệ thống Linux được khởi động / hoặc bị tắt đi. Làm cách nào chúng ta có thể thực hiện điều đó một cách đúng đắn. Nếu các quy trình không được thực hiện một cách hợp lý, các tệp tin trong máy của bạn có thể sẽ bị hỏng hoặc bị mất.

## 7.1. Tổng quan về quá trình khởi động và tắt máy.
Hành động bật một hệ thống máy tính và khiến cho hệ điều hành trong máy đó được nạp vào , quá trình đó được gọi là **khởi động** - *booting*. 
Trong quá trình này, đầu tiên máy tính sẽ load một phần mã nhỏ gọi là *bootstrap loader*, cái sẽ có trách nhiệm lần lượt tải và bắt đầu hệ điều hành đang có trên máy. *Bootstrap loader* thường được lưu trữ tại một vị trí cố định trong đĩa cứng hoặc một đĩa mềm. Lý do cho điều này đó chính là hệ điều hành thường lớn và phức tạp, trong khi đó *bootstrap loader* - phần mã đầu tiên được máy tính tải lên thường rất nhỏ (chỉ khoảng vài trăm bytes), nhằm tránh việc khiến cho firmware trở nên phức tạp rối rắm không cần thiết thì *bootstrap loader* sẽ được tải lên trước.

Các máy tính khác nhau thực hiện quá trình khởi động theo những cách khác nhau. với PCs, thì BIOS của chúng sẽ đọc sector đầu tiên (*boot sector*) của một đĩa mềm hoặc một đĩa cứng. *Bootstrap loader* nằm trọn trong sector đầu tiên này. Nó sẽ tải hệ điều hành nằm ở đâu đó trên đĩa (*hoặc ở một vài nơi khác*).

Sau khi Linux đã được tải, nó khởi tạo phần cứng và các driver cho các thiết bị trên hệ thống. Sau đó Linux chạy **init**, **init** sẽ bắt đầu những tiến trình khác , những tiến trình mà giúp cho người dùng đăng nhập cũng như làm những thứ khác. Chi tiết về phần này sẽ được nói ở các section dưới.

Trong trường hợp tắt một hệ thống Linux, đầu tiên tất cả các tiến trình sẽ được bảo rằng "Ê, chúng mày biết đường mà tự sát đi". Điều này đa phần luôn khiến cho các tiến trình tự động đóng các file mà nó đã mở, sau đó làm một vài thứ cần thiết để giữ cho hệ thống gọn gàng. Tiếp đến, các filesystem và các không gian swap cũng sẽ bị unmount. Cuối cùng thì một thông điệp được in ra console, nói rằng nguồn điện đã có thể được tắt đi rồi. Nếu các thủ tục bật,tắt máy không hoạt động đúng cách, những thứ rồi tệ có thể sẽ xảy ra, quan trọng nhất là nếu nội dung của buffer cache chưa được flush vào đĩa, đồng nghĩa rằng tất cả các dữ liệu trong buffer cache có nguy cơ mất mát, filesystem tương ứng trên đĩa có thể trở nên không phù hợp, đôi khi là không khả dụng.

## 7.2. Cái nhìn gần hơn về quá trình khởi động.
Khi một PC được khởi động, BIOS sẽ làm một vài bài test khác nhau để đảm bảo rằng mọi thứ trông đều ổn. Sau đó quá trình boot mới thực sự diễn ra. Quá trình kiểm tra này được gọi là *Power on self test*, gọi tắt là POST). Nó sẽ chọn một ổ đĩa (*thường là ổ đĩa mềm nếu có, nếu không thì sẽ là ổ đĩa cứng đầu tiên , mặc dù thứ tự lựa chọn có thể được cấu hình bởi người dùng *) sau đó đọc sector đầu tiên của đĩa đó. Sector này gọi là *boot sector* , trên các ổ đĩa cứng thì nó được gọi là *master boot record* - do một đĩa cứng có thể chứa nhiều phân vùng, mà mỗi phân vùng lại có boot sector của riêng chúng.

Boot sector chứa một chương trình nhỏ (*vừa nhỏ đủ để chứa trong một sector duy nhất*), trách nhiệm của chương trình này là đọc hệ điều hành từ đĩa và khởi động nó. Khi bạn khởi động LInux từ một đĩa mềm, boot sector của đĩa mềm chứa mã , mã này chỉ đọc vài trăm block đầu tiên (*tùy vào kích thước của kernel*) và đặt dữ liệu của những block đó vào trong một vị trí được định trước trong RAM. Trên một đĩa mềm Linux boot thường sẽ không có filesystem nào, Linux kernel sẽ được lưu trữ trong những sector liên tiếp, điều này làm cho quá trình khởi động đơn giản hơn. Tuy nhiên, việc boot từ một đĩa mềm chứa một filesystem là hoàn toàn có thể nếu bạn sử dụng LILO (*Linux Loader*) hoặc GRUB (*GRand Unifying Bootloader*).

Khi khởi động từ một đĩa cứng, mã trong master boot record sẽ đọc bảng phân vùng (*cũng nằm trong master boot record nốt*), nó sẽ xác định các phân vùng active (*phân vùng mà được đánh dấu là bootable*), đọc boot sector của phân vùng đó, sau đó bắt đầu đoạn mã nằm trong boot sector vừa đọc. Các đoạn mã trong các boot sector của các phân vùng hoạt động tương tự như boot record của một đĩa mềm không có filesystem : nó sẽ đọc kernel từ phân vùng tương ứng và khởi động kernel đó. Chi tiết về việc các boot sector này hoạt động có thể sẽ khác nhau, tuy nhiên , người ta thường không tạo một phân vùng riêng chỉ để chứa kernel, nên mã trong bootsector của các phân vùng sẽ không thể đọc đĩa theo kiểu tuần tự, thay vì thế, nó phải tìm những sector chứa kernel ở một nơi nào đó được filesystem đặt trước.Có nhiều cách khác nhau để thực hiện điều này , tuy nhiên cách phổ biến nhất đó chính là sử dụng những bootloader như LILO hoặc GRUB.

Khi khởi động , bootloader thường sẽ bỏ qua các công đoạn trung gian và khởi động kernel mặc định. Bạn cũng hoàn toàn có khả năng cấu hình cho bootloader để nó có thể boot một trong nhiều kernel khác nhau, hoặc boot vào các hệ điều hành khác ngoài Linux, người dùng cũng có khả năng tự chọn kernel hoặc hệ điều hành nào mà họ sẽ muốn boot vào. Ví dụ, LILO có thể được cấu hình sao cho khi một người giữ phím **alt**, **shift** hoặc **ctrl**ở thời điểm khởi động, LILO sẽ hỏi xem nguời đó muốn boot kernel/hệ điều hành nào. Một bootloader cũng có thể đuợc cấu hình để luôn luôn hỏi người dùng về hệ điều hành hoặc kernel mà họ muốn boot, và sau một khoảng thời gian nhất định nếu như người dùng không có sự lựa chọn, nó sẽ boot vào kernel mặc định.

Khởi động từ một đĩa cứng hay một đĩa mềm thì cũng đều có cái lợi của nó, nhưng nhìn chung thì khởi động từ đĩa cứng sẽ ổn hơn, do nó tránh đuợc các phiền phức nếu boot với một đĩa mềm, nó cũng nhanh hơn với việc boot từ một đĩa mềm. Hầu hết các bản phân phối Linux đều cài đặt bootloader cho bạn trong quá trình bạn cài đặt.

Sau khi Linux kernel đã được đọc vào bộ nhớ, bất chấp tất cả, những điều sau đây cũng sẽ được thực hiện:
* Kernel được nén khi cài đặt, nên đương nhiên đầu tiên nó sẽ tự giải nén chính nó. Phần đầu của kernel sẽ chứa một chương trình nhỏ để làm điều này.
* Nếu như bạn có một card đồ hoạ mà Linux nhận ra rằng nó có một vài chế độ chữ (*text mode*) đặc biệt (*100 cột x 40 dòng chẳng hạn*), Linux sẽ hỏi bạn rằng bạn muốn sử dụng chế độ nào. Trong quá trình biên dịch kernel, bạn cũng có khả năng thiết đặt trước một chế độ video, nên Linux sẽ không hỏi bạn về chế độ nào bạn muốn sử dụng nữa. Điều này cũng có thể được hoàn thành bởi LILO, GRUB hoặc **rdev**.
* Sau đó, kernel kiểm tra xem có còn những phần cứng nào đã được cài đặt vào hệ thống nữa hay không (*đĩa cứng, đĩa mềm, network adapter, ..v...v..*), nó cũng tiến hành cấu hình một vài các driver cho các thiết bị được phát hiện một cách hợp lý. Trong khi LInux làm điều này, nó sẽ xuất ra những thông điệp về những thứ mà nó tìm thấy, ví dụ , khi tôi khởi động, những thứ mà tôi thấy trông sẽ như sau:

```bash
LILO boot:
Loading linux.
Console: colour EGA+ 80x25, 8 virtual consoles
Serial driver version 3.94 with no serial options enabled
tty00 at 0x03f8 (irq = 4) is a 16450
tty01 at 0x02f8 (irq = 3) is a 16450
lp_init: lp1 exists (0), using polling driver
Memory: 7332k/8192k available (300k kernel code, 384k reserved, 176k data)
Floppy drive(s): fd0 is 1.44M, fd1 is 1.2M
Loopback device init
Warning WD8013 board not found at i/o = 280.
Math coprocessor using irq13 error reporting.
Partition check: 
	hda: hda1 hda2 hda3
VFS: Mounted root (ext filesystem).
Linux version 0.99.pl9−1 (root@haven) 05/01/93 14:12:20
```
Những thông điệp trên sẽ khác nhau trên những hệ thống khác nhau, phụ thuộc vào phần cứng, phiên bản Linux đang được sử dụng , và cách mà nó được cấu hình.

* Sau đó kernel sẽ thử mount root filesystem. Vị trí của root filesystem được cấu hình trong quá trình biên dịch kernel, với **rdev** hoặc các bootloader , bạn có thể cấu hình nó bất kỳ lúc nào. Loại của filesystem sẽ được tự động phát hiện. Nếu như root filesystem không thể mount được , ví dụ do bạn không có filesystem tương ứng trong kernel, kernel sẽ rơi vào trạng thái hoảng loạn và ngừng hệ thống.
Root filesystem thường được mount dưới dạng một filesystem read-only. Điều này cho phép việc kiểm tra root filesystem trong lúc nó được mount được diễn ra. Không phải là một ý tưởng tốt nếu kiểm tra một filesystem được mount dưới dạng một read-write filesystem.
* Sau đó ,kernel khởi tạo **init** (*/sbin/init*) dưới dạng một tiến trình ngầm (*do đây là tiến trình đầu tiên được khởi chạy, pid của nó luôn là 1*). **init** sẽ làm một vài công việc khởi động khác nhau. Những thứ **init** sẽ làm phụ thuộc vào những cấu hình của nó, nhưng ít nhất **init** sẽ khởi động một vài tiến trình ngầm của các chương trình cần thiết.
* **init** sau đó sẽ chuyển qua chế độ đa người dùng, rồi khởi động **getty** để dùng cho các virtual console và các đường truyền nối tiếp. **getty** là chương trình cho phép người dùng đăng nhập qua những console ảo hoặc các serial terminals. **init** cũng có thể khởi động một vài chương trình khác, tùy vào cấu hình mà nó đang có.
* Sau đó, quá trình boot hoàn thành, hệ thống được bật và chạy một cách bình thường.

## 7.3. Nói thêm về shutdowns.
**Bạn nên tuân theo một quy trình đúng đắn khi bạn tắt một hệ thống Linux** , điều này rất quan trọng, bạn nên nhớ. Nếu bạn không làm điều đó, các filesystem của bạn chắc hẳn sẽ biến thành cái thùng rác và các file trong filesystem đó sẽ rối bung bét hết. Đó là bởi vì Linux có một bộ đệm đĩa , dữ liệu trong bộ đệm đó sẽ được viết vào đĩa theo một chu ký thời gian nhất định. Điều này cải thiện hiệu suất một cách tuyệt vời, nhưng đồng nghĩa rằng nếu bạn tắt nguồn đột ngột, dữ liệu sẽ không được viết vào đĩa, và sẽ biến mất luôn vì không còn điện để giữ data trong RAM nữa.

Một lý do khác để bạn không sập nguồn đột ngột, đó là trong một hệ thống đa nhiệm có thể tồn tại rất nhiều thứ chạy ngầm, và ngắt nguồn có thể gây ra những hậu quả khá tai hại mà bạn không lường trước được. Nhưng nếu tuân theo một quy trình shutdown hợp lý, bạn sẽ đảm bảo rằng tất cả các tiến trình ngầm sẽ lưu trữ lại dữ liệu của nó.

Lệnh tắt nguồn thường được sử dụng trong Linux là **shutdown**. Nó thường được sử dụng theo hai cách.

Nếu như bạn đang chạy một hệ thống mà bạn là người dùng duy nhất, cách mà bạn dùng **shutdown** đó sẽ là thoát tất cả các chương trình đang chạy, logout khỏi tất cả các virtual console, sau đó đăng nhập dưới quyền root, di chuyển tới thư mục root để đề phòng các unmount không mong muốn sẽ xảy ra. Sau đó bạn chạy lệnh **shutdown -h now** (*bạn có thể thay thế \<now> với một dấu cộng đứng trước một số nguyên , đó sẽ là thời gian delay trước khi shutdown*).

Nếu như hệ thống của bạn có nhiều người dùng, bạn nên sử dụng **shutdown -h +\<time> message** , trong đó **\<time>** là thời gian tính bằng phút trước khi hệ thống bước vào trạng thái nghỉ ngơi, **message** là một thông điệp ngắn bạn sẽ dùng để giải thích tại sao hệ thống lại bị tắt.

```bash
$ shutdown -h +10 'We will install a new disk. System should be back on-line in three hours'
$
```
Lệnh này sẽ cảnh báo mọi người rằng hệ thống sẽ tắt trong 10 phút nữa. Những người dùng nên nhanh chóng thực hiện các thao tác để lưu lại dữ liệu của họ. Cảnh báo này sẽ được in lên mọi terminal mà người dùng đã và đang login , bao gồm tất cả các **xterm**.
```bash
Broadcast message from root (ttyp0) Wed Dec 2 01:03:25 2016...
We will install a new disk. System should
be back on−line in three hours.
The system is going DOWN for system halt in 10 minutes !!
```
Lời cảnh báo này sẽ tự động lặp lại một vài lần trước khi shutdown, với giá trị intervals ngày càng ngắn hơn.

Khi quá trình shutdown thật sự diễn ra (*dù có delay hay không*) , mọi filesystem trừ root sẽ bị unmount, các tiến trình người dùng sẽ bị giết nếu còn, các daemons bị tắt , mọi thứ nói chung là sẽ lắng xuống. Sau đó , **init** sẽ in ra một thông điệp là bạn có thể tắt máy. Sau đó, chỉ ngay sau đó, bạn mới nên thò tay vào để nhấn nút nguồn.

Đôi khi , bạn sẽ không thể tắt hệ thống một cách hợp lý (*điều này hiếm khi xảy ra trên các hệ thống tốt*). Ví dụ , nếu kernel rơi vào trạng thái hoảng loạn, crash , nói chung là không hồi đáp, lúc này thì việc đưa thêm một lệnh nào đó khác sẽ là không khả thi nữa , và việc shutdown đúng cách sẽ là thứ gì đó khá xa xỉ , mọi việc bạn có thể làm lúc này là cầu nguyện rằng sẽ không có thứ gì bị hỏng quá nghiêm trọng, rồi thò tay vào tắt nguồn. Nếu như rắc rối có gì đó ít nghiêm trọng hơn (* có ai đó đập tan bàn phím của bạn bằng một cây búa chẳng hạn*), kernel và chương trình **update** vẫn chạy bình thường , bạn nên chờ một vài phút để **update** đẩy tất cả các dữ liệu trong buffer cache vào đĩa, sau đó bạn mới nên tiến hành tắt máy nếu cần.

Một vài người thì thích shutdơwn bằng cách chạy lệnh **sync** 3 lần, chờ cho hoạt động nhập/xuất của đĩa dừng hẳn, sau đó mới tắt nguồn. Tuy nhiên, cách làm này không unmount bất kỳ một filesystem nào , nó có thể dẫn đến một hậu quả với các filesystem ext2, khi mà cờ **clean filesytem** chỉ được set nếu filesytem đó được unmount đúng cách. Phương pháp ba bước như trên thường được dùng vào những ngày đầu của UNIX, khi mà các lệnh được gõ một cách riêng biệt, ngày nay, nó không được khuyến nghị sử dụng.

## 7.4. Tái khởi động - rebooting.
*Reboot* nghĩa là khởi động lại hệ thống một lần nữa. Điều này có thể được thực hiện bằng cách shutdown một cách hợp lý, chờ nguồn tắt rồi bật lại nguồn lên. Một cách đơn giản hơn đó là yêu cầu **shutdown** tái khởi động hệ thống, thay vì chỉ đưa nó vào trạng thái nghỉ. Bạn có thể truyền vào lệnh **shutdown** tham số *-r* để chỉ ra rằng bạn muốn tái khởi động hệ thống : `shutdown -r now`.

Hầu hết các hệ thống Linux chạy lênh *shutdown -r now* khi tổ hợp *ctrl-alt-del* được nhấn. Tổ hợp phím có thể được thay thế thông qua việc cấu hình. Trên các hệ thống đa người dùng, bạn nên chừa ra vài phút trước khi tái khởi động. Các hệ thống có thể được cấu hình để sau cho khi nhấn tổ hợp *ctrl-del-alt* thì sẽ không có gì xảy ra cả.

## 7.5. Chế độ đơn người dùng - single user mode.
Lệnh **shutdown** cũng có thể được sử dụng để đưa hệ thống xuống thành một hệ thống chạy ở chế độ đơn người dùng, trong đó không một ai có thể login, nhưng người dùng root có thể sử dụng console. Điều này hữu dụng trong các tình huống đặc biệt, khi mà hoạt động quản trị không thể hoàn thành trong khi hệ thống chạy bình thường.
## 7.6. Các đĩa mềm khởi động khẩn cấp.
Không phải lúc nào bạn cũng có khả năng boot từ đĩa cứng. Ví dụ , nếu như bạn phạm phải sai lầm nào  đó khi cấu hình LILO, bạn có thể khiến cho hệ thống trở nên không thể boot được. Trong tình huống này , bạn cần một phương pháp booting thay thế mà luôn luôn hoạt động. Với các máy tính cá nhân điển hình, điều đó có nghĩa là bạn nên boot từ một ổ đĩa mềm , đĩa CD hoặc sử dụng một phương tiện boot nào đó khác.
Hầu hết các bản phân phối Linux đều cho phép người dùng tạo ra một đĩa boot khẩn cấp trong quá trình cài đặt. Sẽ là một ý tưởng tốt cho bạn để làm điều đó. Tuy nhiên, một vài đĩa boot chỉ chứa kernel. Giả sử rằng bạn sẽ sử dụng những chương trình trên đĩa cài đặt của bản phân phối đó, để sửa chữa bất kỳ vấn đề nào mà bạn có. Đôi khi những chương trình có sẵn là không đủ để đáp ứng nhu cầu của bạn. Ví dụ đôi khi bạn sẽ cần phải khôi phục một vài file, nhưng những chương trình hỗ trợ để làm điều đó lại không có sẵn trên đĩa cứu hộ mà bạn có. 

Dó đó , đôi khi sẽ cần thiết để bạn có thể tự tạo ra một đĩa cứu hộ cho chính mình với các tùy chọn bổ sung mà bạn muốn. Bạn cũng nên lưu ý rằng bạn cần cập nhật các đĩa cứu hộ của mình với chu kỳ không quá lâu.

> Link bài viết trước : [\[Series\] Linux System Administrator's guide. \[Phần 6\]](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-6).
> Link bài viết sau : [\[Series\] Linux System Administrator's guide. \[Phần 8\]](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-8).
> Nguồn : [The Linux Documentation Project](http://tldp.org).
