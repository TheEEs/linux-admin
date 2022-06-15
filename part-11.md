> "Khi một người đàn ông yêu nhiều hơn 1 cô gái, người duy nhất anh ta yêu chính là bản thân mình" - ở đâu đó quên mất rồi
# Chương 11. init

Chương này mô tả **init** - tiến trình cấp người dùng đầu tiên được kernel khởi động. **init** có nhiều trách nhiệm quan trọng, như là khởi tạo **gettty** (để người dùng có thể login), triển khai các cấp độ chạy - *run levels*, và thu nhận các tiến trình mồ côi. Chương này sẽ giải thích làm thế nào để cấu hình **init** và cách để ứng dụng các cấp độ chạy khác nhau.

## 11.1. init đến trước.
**init**, khỏi phải bàn cãi, là một trong những chương trình thiết yếu nhất để vận hành một hệ thống Linux, nhưng cũng đồng thời là chương trình bị đa phần chúng ta phớt lờ. Một bản phân phối Linux tốt sẽ đi kèm với một cấu hình sẵn có hoạt động ổn định trên các hệ thống, và gần như bạn chẳng cần phải động chạm gì tới nó cả. Thường thì bạn chỉ cần quan tâm đến **init** khi bạn cài đặt các serial terminals, các modem dial-in, hoặc thay đổi các run-level mặc định.

Khi hạt nhân Linux đã tự khởi động nó xong (tải vào bộ nhớ --> khởi động --> khởi động tất cả các driver thiết bị --> thiết lập các cấu trúc dữ liệu cần thiết), nó kết thúc phần việc của mình bằng cách khởi động một chương trình ở cấp người dùng - *user level* tên là **init**. Do đó, **init** luôn là chương trình đầu tiên được chạy (id của nó luôn là 1).

Hạt nhân sẽ tìm kiếm **init** ở vài vị trí khác nhau, thường thì sẽ ở `/sbin/init`. Nếu kernel không thể tìm thấy ở đây, nó sẽ thử chạy `/bin/sh`, nếu vẫn tiếp tục thất bại, hệ thống sẽ không thể khởi động.

Khi **init** đã bắt đầu chạy, nó kết thúc quá trình khởi động của hệ thống bằng cách làm một vài tác vụ quản trị, như là kiểm tra các hệ thống tệp tin, dọn dẹp `/tmp`, khởi động các dịch vụ, và khởi chạy `getty` cho mỗi terminal và các virtual console, nơi mà ở đó người dùng có thể login.

Sau khi hệ thống đã được bật, **init** sẽ khởi động lại `getty` cho mỗi terminal khi có người dùng ở terminal đó logout (để những người dùng sau có thể login). **init** cũng nhận nuôi các tiến trình mồ côi: Khi một process A khởi động một process B và process A chết trước, process B ngay lập tức trở thành child process của **init**. Có nhiều lý do kỹ thuật chứng minh rằng điều này quan trọng, bạn có thể không cần hiểu rõ, nhưng nắm được việc nhận nuôi các tiến trình con của **init** sẽ tốt, vì nó giúp bạn hiểu được danh sách tiến trình và cây tiến trình (process tree graph) dễ dàng hơn. Có nhiều biến thể của **init** tồn tại. Hầu hết các bản phân phối Linux sử dụng **sysvinit** - được dựa trên thiết kế của System V init. Các phiên bản BSD của Unix cũng có một bản init khác. Điểm khác biệt chính giữa chúng chính là các cấp độ chạy - *run levels*: `System V` có chúng, nhưng `BSD` thì không. Sự khác biệt này không cần thiết, chúng ta sẽ chỉ tìm hiểu **sysvinit** mà thôi.

## 9.2. Cấu hình init để khởi chạy getty: tệp tin /etc/inittab

Khi init chạy, nó đọc file cấu hình `/etc/inittab`. Khi hệ thống đang vận hành, nó sẽ đọc lại file này nếu như nó nhận được tín hiệu `HUP` (**kill - HUP 1**). Tính năng này giúp chúng ta không cần phải khởi động lại hệ thống chỉ để khiến các thay đổi trong `/etc/inittab` có tác dụng.

File `/etc/inittab` hơi phức tạp. Chúng ta sẽ bắt đầu với một trường hợp đơn giản để cấu hình `getty`. Các dòng trong `/etc/inittab` được chia thành 4 cột, ngăn cách nhau bởi dấu hai chấm `:`.
```text
id:runlevels:action:process
```
`/etc/inittab` có thể chứa các dòng trống và các dòng bắt đầu bằng `#`, chúng đều bị bỏ qua. Các trường sẽ được mô tả dưới đây:
* **id**: Trường này định danh 1 dòng trong file, với dòng của `getty`, thì con số này sẽ chỉ ra cái terminal nào `getty` sẽ chạy trên đó (ký tự phía sau `/dev/tty`). Với các dòng khác, thì trường này không quan trọng, nhưng các id vẫn nên là độc nhất, không trùng nhau.
* **runlevels**: Các cấp độ chạy. Chúng được viết theo dạng các chữ số liền nhau, không ngăn cách (*các cấp độ chạy sẽ được nói rõ hơn ở phần tiếp theo*).
* **action**: Hành động nào sẽ được thực hiện bởi dòng này, ví dụ: `respawn` để chạy lại câu lệnh ở trường tiếp theo khi nó hoàn tất, hoặc `once` để chạy câu lệnh đó chỉ 1 lần.
* **process**: Câu lệnh để chạy.

Để khởi động `getty` trên terminal ảo đầu tiên (`/dev/tty1`), trong tất cả các cấp độ chạy đa người dùng bình thường (multi-user run levels) từ 2-5, chúng ta có thể viết một dòng như sau:

```text
1:2345:respawn:/sbin/getty 9600 tty1
```

Trường đầu tiên nói rằng đây là dòng dùng cho `dev/tty1`. Trường thứ 2 nói rằng chúng áp dụng cho cấp độ chạy 2,3,4 và 5. Trường thứ 3 nói rằng câu lệnh nên được chạy lại khi chúng hoàn tất. Trường cuối cùng là câu lệnh để chạy `getty` trên terminal ảo đầu tiên.

Các phiên bản khác nhau của `getty` sẽ chạy theo kiểu khác nhau. Bạn phải đọc tờ hướng dẫn, đó là trách nhiệm của bạn.

Nếu bạn định thêm các terminal hoặc các đường truyền dial-in vào hệ thống, bạn sẽ cần thêm nhiều dòng hơn nữa vào `/etc/inittab`, mỗi dòng là 1 terminal hoặc dial-in modem. Để biết chi tiết hơn, hãy đọc manual của **init**, **getty** và **inittab**.

Nếu một dòng lệnh thất bại lúc nó khởi động, và nếu **init** được cấu hình để `restart` nó, điều này sẽ dùng rất nhiều tài nguyên hệ thống: ***init** khởi động A, A thất bại, **init** khởi động A, A thất bại, **init** khởi động A, A thất bại, ...*
liên tiếp như vậy.

Để tránh điều này, **init** sẽ theo dõi mức độ thường xuyên mà nó restart một câu lệnh, nếu tần số restart quá cao, nó sẽ delay 5 phút trước lần khởi động lại tiếp theo.

# 11.3. Các cấp độ chạy - run levels.
Một *cấp độ chạy* là một trạng thái của **init** cũng như toàn bộ hệ thống, trạng thái này định nghĩa những dịch vụ hệ thống nào đang vận hành. Run levels được định danh bởi các số. Một số người quản trị hệ thống sử dụng *run levels* để xác định hệ thống con nào đang hoạt động, ví dụ: liệu `X` có đang chạy, liệu mạng có hoạt động được, ...

Bảng dưới đây mô tả cách hầu hết các bản phân phối Linux định nghĩa các cấp độ chạy của nó. Tuy nhiên, các run levels từ 2-5 có thể được điều chỉnh để phù hợp với nhu cầu riêng của bạn:


| Run levels | Ý nghĩa |
|---|---|
| 0 | Tắt hệ thống |
| 1 | Chế độ đơn người dùng (cho các hoạt động quản trị đặc biệt) |
| 2 | Chế độ đa người dùng cục bộ với mạng, nhưng không kèm các dịch vụ mạng, ví dụ: NFS |
| 3 | Chế độ đa người dùng đầy đủ với kết nối mạng |
| 4 | Không dùng |
| 5 | Chế độ đa người dùng đầy đủ với kết nối mạng và X windows(GUI) |
| 6 | Khởi động lại |

Các dịch vụ khởi động ở một runtime cụ thể được quyết định bởi nội dung của các thư mục `rcN.d` khác nhau. Các bản phân phối thường đặt chúng hoặc là ở `/etc/init.d/rcN.d` hoặc `/etc/rcN.d` (thay chữ `N` bằng số run level tương ứng).

Trong mỗi run-level bạn sẽ tìm thấy một chuỗi các liên kết trỏ tới các đoạn kịch bản khởi động nằm trong `/etc/init.d`. Tên của các liên kết này đều bắt đầu bằng **K** hoặc **S**, theo sau bởi một con số. Nếu bắt đầu bằng **S**, service sẽ bắt đầu khi bạn chuyển vào run-level đó. Nếu là **K**, service sẽ bị kill (nếu đang chạy).

Con số theo sau **K** và **S** chỉ ra thứ tự mà kịch bản sẽ chạy, dưới đây là ví dụ về một `/etc/init.d/rc3.d`:
```sh
$ ls −l /etc/init.d/rc3.d
lrwxrwxrwx 1 root root 10 2004−11−29 22:09 K12nfsboot −> ../nfsboot
lrwxrwxrwx 1 root root 6 2005−03−29 13:42 K15xdm −> ../xdm
lrwxrwxrwx 1 root root 9 2004−11−29 22:08 S01pcmcia −> ../pcmcia
lrwxrwxrwx 1 root root 9 2004−11−29 22:06 S01random −> ../random
lrwxrwxrwx 1 root root 11 2005−03−01 11:56 S02firewall −> ../firewall
lrwxrwxrwx 1 root root 10 2004−11−29 22:34 S05network −> ../network
lrwxrwxrwx 1 root root 9 2004−11−29 22:07 S06syslog −> ../syslog
lrwxrwxrwx 1 root root 10 2004−11−29 22:09 S08portmap −> ../portmap
lrwxrwxrwx 1 root root 9 2004−11−29 22:07 S08resmgr −> ../resmgr
lrwxrwxrwx 1 root root 6 2004−11−29 22:09 S10nfs −> ../nfs
lrwxrwxrwx 1 root root 12 2004−11−29 22:40 S12alsasound −> ../alsasound
lrwxrwxrwx 1 root root 8 2004−11−29 22:09 S12fbset −> ../fbset
lrwxrwxrwx 1 root root 7 2004−11−29 22:10 S12sshd −> ../sshd
lrwxrwxrwx 1 root root 8 2005−02−01 09:24 S12xntpd −> ../xntpd
lrwxrwxrwx 1 root root 7 2004−12−02 20:34 S13cups −> ../cups
lrwxrwxrwx 1 root root 6 2004−11−29 22:09 S13kbd −> ../kbd
lrwxrwxrwx 1 root root 13 2004−11−29 22:10 S13powersaved −> ../powersaved
lrwxrwxrwx 1 root root 9 2004−11−29 22:09 S14hwscan −> ../hwscan
lrwxrwxrwx 1 root root 7 2004−11−29 22:10 S14nscd −> ../nscd
lrwxrwxrwx 1 root root 10 2004−11−29 22:10 S14postfix −> ../postfix
lrwxrwxrwx 1 root root 6 2005−02−04 13:27 S14smb −> ../smb
lrwxrwxrwx 1 root root 7 2004−11−29 22:10 S15cron −> ../cron
lrwxrwxrwx 1 root root 8 2004−12−22 20:35 S15smbfs −> ../smbfs
```

Cách mà run-level bắt đầu được cấu hình trong `/etc/inittab` bằng các dòng, kiểu dạng như:
```text
l2:2:wait:/etc/init.d/rc 2
```
Trường đầu tiên là một nhãn ngẫu nhiên, không quan trọng. Trường thứ 2 chỉ ra rằng đoạn lệnh này sẽ áp dụng cho run level 2. Trường thứ 3 nói rằng: *init nên chạy câu lệnh ở trường thứ 4 một lần, và đợi cho nó hoàn tất*. Câu lênh `/etc/init.d/rc` chạy các lệnh cần thiết để bắt đầu hoặc dừng các dịch vụ nhằm chuyển đến cấp độ chạy tương ứng.

Câu lệnh ở trường thứ 4 làm hết tất cả các công việc khó nhằn nhằm setup một run-level, nó chạy các service chưa được bật, và stop các service không nên có mặt ở run-level tương ứng. 

Khi **init** bắt đầu, nó sẽ tìm một dòng trong `/etc/inittab`, dòng này sẽ chỉ ra run-level mặc định:
```text
id:2:initdefault:
```

Bạn có thể yêu cầu **init** đi tới một run-level không-phải-mặc-định bằng cách truyền một tham số dòng lệnh (`single` hoặc `emergency`) vào kernel khi nó khởi động. Tham số dòng lệnh kiểu này có thể được truyền vào bởi LILO (*Linux Loader*), cho phép bạn truy cập vào chế độ đơn người dùng (run-level 1).

Trong khi hệ thống đang chạy, câu lệnh `telinit` có thể được dùng để thay đổi cấp độ chạy, và khi cấp độ chạy được thay đổi, **init** sẽ chạy câu lệnh tương ứng trong `/etc/inittab`.

## 11.4. Các cấu hình đặc biệt trong `/etc/inittab`.
`/etc/inittab` có một số tính năng đặc biệt cho phép **init** phản ứng với một số tình huống nhất định. Các tính năng này được đánh dấu bởi trường số 3 của mỗi dòng, ví dụ:
* **powerwait**: Cho phép **init** tắt hệ thống khi bị mất điện. Tính năng này giả định rằng bạn đã có bộ cấp nguồn USP và một phần mềm theo dõi USP và thông báo cho **init** khi mất điện.
* **ctrlaltdel**: Cho phép **init** reboot hệ thống khi người dùng nhấn tổ hợp `Ctr+Alt+Del` trên bàn phím.
* **sysinit**: Câu lệnh dùng để chạy khi hệ thống khởi động. Câu lệnh này thường được dùng để dọn dẹp `/tmp`, ví dụ.

Danh sách phía trên không đầy đủ, bạn cần xem manual của **inittab** để nắm rõ hơn.

## 11.5. Chế độ đơn người dùng.
Một cấp độ chạy quan trọng là cấp độ chạy số 1 - **single user mode**. Ở đó chỉ có người quản trị hệ thống sử dụng máy và một số ít nhất có thể các dịch vụ hệ thống khác đang chạy (vd: login chẳng hạn). Chế độ đơn người dùng rất cần thiết cho một vài tác vụ quản trị, như là chạy `fsck` trên phân vùng `/usr` chẳng hạn, bởi vì điều này yêu cầu rằng `/usr` cần phải unmount, và việc unmount `/usr` là không khả thi trừ khi tất cả các dịch vụ hệ thống bị kill.

Một hệ thống đang chạy có thể chuyển chính nó về chế độ đơn người dùng bằng cách dùng `telinit` để yêu cầu chạy ở run-level 1. Còn nếu hệ thống đang khởi động, thì chế độ đơn người dùng có thể được truy cập bằng cách truyền tham số `single` hoặc `emergency` vào dòng lệnh kernel. Kernel sẽ đưa tham số đó cho **init**, và **init** hiểu rằng nó không nên truy cập vào run-level mặc định. 

Khởi động vào single-user mode đôi khi cần thiết bởi vì một người có thể chạy `fsck` thủ công trước khi bất kỳ thứ gì mount, nếu không sẽ gây ra một phân vùng `/usr` hỏng (nó đã hỏng, bạn càng tác động, nó càng hỏng). Do đó fsck nên được chạy càng sớm, càng nhanh càng tốt.

Script **init** sẽ tự động chạy vào chế độ đơn người dùng, nếu như quá trình `fsck` ở lúc khởi động máy thất bại. Đây là cố gắng của Linux nhằm ngăn chặn các truy cập vào những filesystem đã quá hỏng (*`fsck` cũng bó tay*). Những sự hỏng hóc như vậy hiếm khi xảy ra, thường chỉ xuất hiện khi một ổ đĩa cứng bị vỡ, xước, hoặc khi một bản kernel thử nghiệm mới được phát hành, nhưng có chuẩn bị vẫn tốt hơn.

Một đặc tính bảo mật khác, đó là một hệ thống được cấu hình chuẩn mực sẽ hỏi password của root trước khi chạy shell ở chế độ đơn người dùng.
