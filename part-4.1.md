# Chương 4. Sử dụng đĩa và các thiết bị lưu trữ khác.

> **"On a clear disk you can seek forever. "**

Khi cài đặt hoặc nâng cấp hệ thống, bạn cần làm một số việc với đĩa của bạn. Bạn phải tạo ra các hệ thống tệp tin trên đĩa mà ở đó các tệp tin có thể đuợc lưu trữ , đồng thời dành ra một vài không gian riêng trên đĩa cho những phần khác của hệ thống.

Chương này giải thích tất cả những công việc bạn có thể sẽ cần làm sau khi cài đặt hoặc nâng cấp hệ thống. Thông thường, một khi bạn đã thiết đặt xong hệ thống của mình, bạn sẽ không cần phải lặp lại những công việc này trong tương lai nữa (*ngoại trừ với các ổ đĩa mềm - floppy disks*). Bạn có thể sẽ cần xem lại bài viết này nếu như bạn thêm vào một ổ đĩa mới , hoặc muốn làm cho ổ đĩa hoạt động trơn tru trở lại.

Những tác vụ quản trị ổ đĩa cơ bản, bao gồm:
* Định dạng. Việc định dạng lại ổ đĩa sẽ làm một vài thứ khác nhau để khiến cho ổ đĩa hoặc phân vùng có thể sử dụng đuợc , ví dụ kiểm tra các sector lỗi, các sector xấu chẳng hạn.
* Phân vùng. Nếu bạn muốn sử dụng ổ đĩa cho nhiều hoạt động khác nhau , không can thiệp tới những hoạt động khác, bạn sẽ cần phải phân vùng ổ đĩa.Một lý do phổ biến cho việc phân vùng đĩa nhớ đó chính là để cài đặt nhiều hệ điều hành trên cùng một ổ cứng , một lý do nữa đó là lưu trữ các files người dùng riêng biệt với các files hệ thống , từ đó có thể dễ dàng sao lưu và bảo vệ hệ thống khỏi bị hư hỏng.
* Tạo một hệ thống tệp tin trên mỗi phân vùng hoặc trên toàn bộ ổ đĩa. Ổ đĩa chẳng có nghĩa lý gì đối với Linux, trừ khi bạn tạo ra ít nhất một filesystem trên nó. Các file có thể đuợc truy xuất từ đó.
* Mount các hệ thống tệp tin khác nhau vào một cây thư mục duy nhất.

## 4.1. Hai loại thiết bị.
Unix, và đương nhiên là bao gồm cả Linux, đề ra 2 khai niệm để phân biệt các thiết bị thành 2 loại tương ứng : *random-access block devices* và *character devices*. Mỗi một thiết bị đuợc hỗ trợ bởi hệ thống sẽ đuợc đại diện bởi một thứ gọi là các **device file - device node** mà tôi đã đề cập tới trong bài viết trước. Khi bạn đọc/ghi một *device file* , thì dữ liệu hoặc sẽ đuợc đọc từ thiết bị, hoặc sẽ đuợc gửi tới thiết bị. Bằng cách này , chúng ta chẳng cần một chương trình đặc biệt nào , hoặc không cần đến những phương pháp đặc biệt trong lập trình ứng dụng như sử dụng ngắt hoặc gọi vòng trên cổng nối tiếp (*polling serial ports*) để có thể giao tiếp với thiết bị.
Một ví dụ điển hình, để gửi một tệp tin tới máy in, chỉ cần : `$ cat filename > /dev/lp1` và nội dung của file sẽ đuợc in ra giấy (*tất nhiên là file đuợc gửi phải thuộc một định dạng mà máy in hiểu đuợc*). Tuy nhiên, bạn có thể nhận ra trên đây không phải một cách tốt để sử dụng máy in , bởi vì có thể nhiều người sẽ chạy câu lệnh trên tại cùng một thời điểm. Bạn có thể sử dụng **lpr**, chương trình này đảm bảo rằng chỉ có một file đuợc in tại một thời điểm, và sẽ tự động gửi các file tiếp theo trong hàng đợi tới máy in khi file trước đó đã in xong. Một vài chương trình có chức năng tương tự cũng cần thiết cho hầu hết các thiết bị khác ngoài máy in. Chúng ta hiếm khi phải lo lắng về các **device file** này.

Do các thiết bị trên hệ thống đuợc nhận diện dưới dạng một file (*trong thư mục /dev*), chúng ta sẽ dễ dàng tìm ra đuợc những **device file** nào đang tồn tại bằng cách sử dụng *ls*. Output từ lệnh **ls -l**  , cột thứ nhất chứa thông tin về loại thiết bị và các quyền (*permissions*) đối với **device file** tương ứng. Vi dụ , với một dòng output dưới đây :
```bash
$ ls −l /dev/ttyS0
crw−rw−r−− 1 root dialout 4, 64 Aug 19 18:56 /dev/ttyS0
```
Ký tự đầu tiên ở cột đầu tiên , **c** , có nghĩa là file này đại diện cho một character device. Với những file bình thường , thay vì **c** sẽ là "**-**" , với thư mục thì là **d**, **b** cho các block device. Bạn cũng cần lưu ý rằng thường thì các device file có thể tồn tại ngay cả khi thiết bị tương ứng với nó chưa đuợc cài đặt. Bạn có một file */dev/sda*, điều đó không có nghĩa rằng hệ thống của bạn có một ổ đĩa SCSI tương ứng. Việc có tất sẵn một lượng lớn các device file thế này khiến cho việc cài đặt các chương trình dễ dàng hơn, đồng thời cũng dễ hơn nếu muốn thêm một phần cứng mới vào hệ thống, bạn sẽ không cần phải tìm ra các tham số tương ứng để tạo ra device file cho thiết bị mới vừa đuợc thêm vào.

## 4.2. Đĩa cứng.
Section này giới thiệu các thuật ngữ có liên quan tới đĩa cứng. Nếu bạn đã biết trước, bạn có thể bỏ qua section này.
![Hình ảnh không tìm thấy](https://github.com/TheEEs/linux-admin/blob/master/images/4.1.png?raw=true)
Hình trên mô tả những phần quan trọng trong ổ đĩa cứng. Một ổ cứng bao gồm một hoặc nhiều các đĩa nhôm tròn , gọi là các *platters*. Một hoặc cả hai bề mặt *(surface)* của *platter* đuợc phủ một lớp chất có từ tính dùng để ghi lại thông tin. Mỗi *surface* lại có một đầu (*head*) kiêm luôn hai chức năng đọc/ghi , có thể đọc hoặc thay đổi dữ liệu trên đĩa. Các *platters* xoay trên một trục chung , tốc độ điển hình là 5400 hoặc 7200 rpm (*rotations per minute*) , mặc dù các ổ đĩa hiệu suất cao hoặc các ổ đĩa cũ sẽ có tốc độ xoay khác nhau. *Head* sẽ di chuyển dọc theo bán kính của *platter*, chuyển động của đầu đọc/ghi kết hợp với vòng xoay của đĩa cứng cho phép *Head* truy cập đuợc mọi nơi trên bề mặt.

CPU và ổ đĩa giao tiếp với nhau thông qua một *bộ điều khiển đĩa - disk controller*. Những phần còn lại của máy tính sẽ đỡ mệt hơn mỗi khi chúng muốn giao tiếp với ổ đĩa cứng , do những bộ điều khiển nằm trên mỗi loại ổ đĩa khác nhau có thể đuợc trừu tượng hóa vào một giao diện giao tiếp chung. Chính vì thế , máy tính chỉ cần nói "Ê đĩa, đưa cho tao mấy cái tao yêu cầu. Nhanh !" , thay vì gửi đi hoặc nhận về một chuỗi các tín hiệu điện tử phức tạp để di chuyển *head* đến vị trí thích hợp rồi đợi cho đĩa quay đến đúng vị trí cần đọc , ròi tiếp tục tiến hành các công việc khó nhai khác. (*Trên thực tế thì giao diện giao tiếp với các ổ đĩa vẫn phức tạp, nhưng như vậy vẫn đỡ hơn rất , rất nhiều nếu như không có nó*). Bộ điều khiển ổ đĩa cũng có thể thực hiện một vài hoạt động khác, như làm bộ nhớ đệm, hoặc tự động thay thế các bad sector.

Những điều đuợc nói phía trên thường là tất cả những gì người ta cần biết về phần cứng ổ đĩa. Cũng có những thứ khác, chẳng hạn như motor dùng để xoay các *platters* và di chuyển các *head*, và các thiết bị điện tử khác dùng để điều khiển hoạt động của các bộ phận cơ khí, nhưng đa phần chúng chẳng liên quan mấy khi một người muốn tìm hiểu về nguyên tắc mà ổ đĩa cứng hoạt động.

Các bề mặt (*surfaces*) của một **platters** đuợc chia thành các vòng tròn đồng tâm, gọi là các *tracks* , các *tracks* lại đuợc chia nhỏ hơn thành các *sectors*. Sự chia nhỏ này có mục đích nhằm xác lập một hệ tọa độ để có thể định vị một vị trí trên đĩa cứng, đồng thời phân bổ không gian đĩa cho các tệp tin. Để tìm một vị trí cho trước, người ta nói "surface 3, track 5, sector 7". Thường thì số lượng sector trên các tracks là bằng nhau , bất kể bán kính của tracks đó là gì. Nhưng một vài loại đĩa cứng khác bố trí nhiều sector hơn trên các tracks có bán kính rộng hơn (*các sectors đều giống nhau về kích thước vật lý, nên phần nhiều trong số chúng vừa vặn với các tracks nằm phía ngoài*). Thông thường, một sector chứa đuợc 512bytes dữ liệu. Ổ cứng, tự nó không thể nào xử lý một lượng dữ liệu nhỏ hơn một sector.

Mỗi bề mặt đều đuợc chia thành các tracks, các sector theo cùng một cách. Điều đó có nghĩa là khi *head* của bề mặt này ở trên một *track*, các *heads* của những bề mặt khác cũng ở trên các tracks tương ứng. Những tracks tương ứng ấy có cùng bán kính, tập hợp các tracks có cùng bán kính đuợc gọi là một hình trụ (*cylinder*). Việc di chuyển head từ cylinder này sang cylinder khác đương nhiên là sẽ mất thời gian, nên việc đặt các dữ liệu thường đuợc truy xuất cùng nhau (*như file chẳng hạn*) trên cùng một cylinder sẽ giúp ổ đĩa không cần phải di chuyển head để đọc toàn bộ các dữ liệu đó.  Điều này cải thiện hiệu xuất, nhưng không phải lúc nào chúng ta cũng có khả năng lưu các file trên cùng một cylinder như vậy, các files mà đuợc lưu trữ tại những nơi khác nhau trên đĩa , hiện tượng này gọi là **phân mảnh** - *fragmented*.

Số lượng các bề mặt, các cylinders, các sector trên mỗi ổ đĩa cứng khác nhau rất nhiều. Những đặc tả kỹ thuật về số lượng của từng thành phần (*surfaces, cylinders, sectors*) đuợc gọi là *đặc tả hình học* của một đĩa cứng. Các *đặc tả hình học* này thường đuợc lưu trong một bộ nhớ đặc biệt, sử dụng nguồn pin tên là *CMOS RAM*. Hệ điều hành có thể đọc đặc tả hình học của đĩa cứng từ CMOS RAM trong quá trình khởi động, hoặc khi khởi tạo driver.

Hơi xui một tí, BIOS có một hạn chế trong thiết kế, điều này làm nó không thể xác định đuợc một tracks có số thứ tự lớn hơn 1024 trong CMOS RAM, một số lượng quá ít cho một đĩa cứng. Để khắc phục điều này , ổ đĩa cứng đã thực hành một trò lừa đảo với đặc tả hình học của chính nó, bằng cách tiến hành phiên dịch các địa chỉ đuợc máy tính đưa ra thành một vài thứ gì đó phù hợp với thực tế. Ví dụ : Một ổ đĩa cứng có 8 head, 2048 tracks, và 35 sectors trên mỗi tracks. Bộ điều khiển của đĩa cứng này có thể lừa đảo máy tính rằng đĩa cứng này có 16 head, 1024 track, mỗi track có 35 sectors. Các phép toán mà disk controller dùng để lừa đảo có thể sẽ phức tạp hơn nhiều trong thực tế , bởi vì đương nhiên số lượng các phần của đĩa cứng sẽ không thể đẹp như trong bài viết này. Sự phiên dịch này sẽ bóp méo cái nhìn của máy tính về cách mà ổ cứng đuợc tổ chức. Sự bóp méo đặc tả hình học này cũng khiến cho việc nâng cao hiệu suất bằng cách sử dụng tất cả không gian trên một cylinder dường như không khả thi trong thực tế. Rõ ràng là không gian lưu trữ mà ổ đĩa cứng trình bày trước mắt bạn đôi khi nhỏ hơn không gian lưu trữ mà nó có thể có đuợc.

Duy nhất các ổ đĩa IDE mới gặp phải vấn đề với việc biên dịch nói trên . Các ổ SCSI đánh địa chỉ cho các sector bằng các số tuần tự , bộ điều khiển ổ đĩa SCSI sẽ có trách nhiệm suy diễn cái số đó thành bộ ba *head*,*cylinder*,*sector*. Các ổ đĩa SCSI cũng sử dụng một phương pháp hoàn toàn khác để CPU có thể giao tiếp với bộ điều khiển , nên chúng tránh đuợc vấn đề mà ổ IDE mắc phải. Lưu ý rằng máy tính có thể không biết đuợc đặc tả hình học thật sự của một ổ SCSI cũng như ổ IDE.

Bởi vì Linux thường xuyên không biết đuợc đặc tả hình học thật sự của ổ đĩa cứng , nên những hệ thống tệp tin của nó sẽ không cố gắng thử lưu trữ các tệp bên trong một cylinder duy nhất. Thay vào đó, nó cố gắng để gán những sector được đánh số tuần tự cho các file, cách này hầu hết luôn luôn cho hiệu suất tương tự. 

Mỗi một đĩa cứng đuợc trình bày bởi một device file riêng biệt. Thường thì chỉ có 2 hoặc 4 ổ IDE , như đã nói ở chương trước , bao gồm */dev/hda, /dev/hdb, /dev/hdc, /dev/hdd*. Các ổ SCSI cũng đuợc trình bày dưới dạng các device file bao gồm: */dev/sda, /dev/sdb, /dev/sdc, /dev/sdd* , tiếp tục nếu như có các ổ đĩa khác đuợc thêm vào hệ thống. Các quy ước đặt tên tương tự cũng áp dụng cho các loại đĩa cứng khác. Lưu ý rằng *device file* của một ổ đĩa cứng sẽ cho quyền truy cập tới toàn bộ đĩa , không còn quan tâm tới giới hạn phân vùng nữa. Điều này dễ khiến cho dữ liệu trong đĩa của bạn trở thành một mớ hỗn tạp nếu như bạn không thao tác với các *device files* này cẩn thận. Những *device files* dùng cho ổ đĩa thường chỉ sử dụng để truy xuất tới **Master boot record**

## 4.3. Đĩa mềm.
Một đĩa mềm bao gồm một màng dẻo , đuợc phủ chất có từ tính lên một hoặc cả hai mặt , giống như một đĩa cứng. Đĩa mềm tự nó không sở hữu đầu đọc/ghi, đầu đọc/ghi đuợc bao gồm trong các ổ đĩa. Một đĩa mềm tương ứng với một *platter* trong đĩa cứng, nhưng nó có thể tháo rời , một ổ đĩa có thể đuợc sử dụng để truy xuất tới nhiều đĩa mềm khác nhau. Một đĩa mềm cũng có thể đuợc đọc bởi nhiều ổ đĩa khác nhau, trong khi các đĩa cứng là một đơn vị độc lập của mỗi ổ cứng.

Giống như đĩa cứng, các đĩa mềm cũng đuợc chia thành các tracks và các sectors , hai tracks có cùng bán kính trên hai mặt cũng đuợc gọi là *cylinders*. Đương nhiên số lượng tracks , sector trên đĩa mềm ít hơn trên đĩa cứng.

Một ổ đĩa mềm thường có khả năng sử dụng nhiều loại đĩa khác nhau. Ví dụ: một ổ 3.5 inch có thể sử dụng cả hai loại đĩa 720KB và 1.44MB. Bởi vì ổ đĩa mềm có cách thức hoạt động hơi khác một chút , và do hệ điều hành cần phải biết kích thước của đĩa là bao nhiêu, nên thường trong hệ thống sẽ có nhiều các *device files* cho các ổ đĩa mềm. Một một device file là đại diện cho sự kết hợp giữa ổ đĩa và loại đĩa. Do đó , */dev/fd0H1440* sẽ là ổ đĩa mềm đầu tiên (*fd0*) , chắc hẳn nó sẽ là một ổ 3.5 icnh, sử dụng một đĩa mềm mật độ cao (*High density - H*) 3.5 inch có kích cỡ 1440KB - một đĩa mềm bình thường.

Tên của các ổ đĩa mềm khá phức tạp, tuy nhiên Linux có những *device node* đặc biệt dùng để tự động phát hiện loại đĩa đang đuợc sử dụng trong ổ. Nó hoạt động thông qua cách thử đọc sector đầu tiên của một đĩa vừa đuợc chèn vào ổ bằng nhiều phương pháp khác nhau cho tới khi tìm ra đuợc một phương pháp thích hợp. Điều này đương nhiên sẽ yêu cầu rằng đĩa mềm phải đuợc định dạng truớc tiên. Các thiết bị đặc biệt nói trên là */dev/fd0, /dev/fd1* ...v...v... 

Các tham số mà các device node nói trên dùng để truy xuất đĩa mềm cũng có thể đựoc thiết đặt thông qua chương trình **setfdprm**. Điều này có thể sẽ hữu dụng nếu như bạn muốn sử dụng những đĩa không tuân theo các quy ước về kích thước đĩa mềm. Ví dụ , nếu như chúng ta có một đĩa với số lượng sector không phải là một số lượng phổ biến thường đuợc dùng, hoặc khi quá trình tự động phát hiện bị thất bại vì một vài lý do khiến cho device node thích hợp sẽ không xuất hiện.

Linux có thể xử lý rất nhiều các định dạng đĩa mềm chưa đuợc chuẩn hóa bên cạnh những định dạng chuẩn. Một vài trong số chúng yêu cầu sử dụng các chương trình định dạng đặc biệt. File */etc/fdprm* nó lưu trữ những thiệt đặt mà chương trình **setfdprm** tạo ra.

Hệ điều hành phải nhận ra khi nào một đĩa mềm đuợc chèn vào trong ổ, ví dụ, trong trường hợp muốn tránh các dữ liệu đuợc cache lại từ đĩa trước đó. Tuy nhiên, dòng tín hiệu để hệ điều hành nhận ra đĩa mới đôi khi bị bể gãy, đôi khi lại bị sai lệch. Nếu như bạn gặp vấn đề với những đĩa mềm, đây có thể là một lý do. Cách duy nhất để khắc phục là tháo đĩa ra rồi lắp lại vào ổ.

## 4.4. CD−ROMs.
Một ổ CD-ROM sử dụng đầu đọc quang học, một đĩa tráng nhựa. Thông tin đuợc ghi lại trên bề mặt của đĩa, ở trong những "lỗ" nhỏ đuợc sắp xếp dọc theo một đường xoắn ốc từ tâm đĩa tới viền ngoài. Ổ đĩa điều hướng một chùm tia laser di chuyển dọc theo hình xoắn ốc để đọc đĩa. Khi tia laser chạm đến một lỗ , nó bị phản xạ theo một cách. Nếu laser chạm phải bề mặt phẳng, nó lại phản xạ theo một cách khác. Cơ chế này khiến cho đĩa CD-ROM dễ dàng hơn trong việc xác lập dữ liệu trên đĩa theo từng bit. Phần còn lại của ổ đĩa là các bộ phận cơ học, do cơ chế hoạt động của CD-ROM nói trên, nên những bộ phận cơ học này cũng hoạt động khá nhàn hạ. 

Nếu so ổ CD-ROM với một ổ đĩa cứng, đương nhiên ổ đĩa cứng nhanh hơn. Trong khi một ổ đĩa cứng rẻ tiền sẽ mất trung bình khoảng ít hơn 15 mili-giây để tìm kiếm dữ liệu, thì những ổ CD-ROM nhanh nhất cũng phải mất tới 1/10 giây. Tốc độ chuyển vận dữ liệu với đĩa CD diễn ra ở tỉ lệ khá nhanh, ở mức hàng trăm KB một giây. Sự chậm chạp của ổ CD có nghĩa rằng bạn không thể sử dụng nó một cách êm dịu ngọt ngào như khi xài ổ đĩa cứng (*một vài bản phân phối Linux cung cấp những liveCD , bạn không cần thiết phải sao chép các files vào trong ổ cứng , từ đó sẽ khiến việc cài đặt dễ dàng hơn, tiết kiệm rất nhiều không gian đĩa*), mặc dù việc đó đôi khi cũng khả thi. Khi một nhà phát triển muốn lựa chọn một phương tiện để phân phối bản cài đặt của họ, thì đĩa CD là lựa chọn tốt, do tốc độ tối đa của ổ CD không có nghĩa lý gì đối với quá trình cài đặt phần mềm.

Có một số cách để sắp xếp dữ liệu trên đĩa CD, một trong những cách phổ biến nhất đã đuợc quy định bởi chuẩn quốc tế ISO 9660. Chuẩn này quy định một filesystem rất nhỏ , thậm chí còn thô thiển hơn cả cái mà MS-DOS sử dụng. Nhưng do filesystem này nhỏ ở mức tối thiểu , nên các hệ điều hành sẽ có khả năng điều hướng nó vào trong hệ thống của mình.

Với những hoạt động sử dụng UNIX bình thường, hệ thống tệp tin ISO 9660 duờng như không khả dụng. Nên một bản mở rộng của chuẩn này đã đuợc phát triển, gọi là *Rock Ridge extension*. Bản mở rộng này cho phép tên file dài hơn, các liên kết tượng trưng (*symbolic links*) , và rất nhiều những hay ho khác. Điều này khiến cho một đĩa CD-ROM trông gần giống như một UNIX filesystem. Hơn thế nữa, filesystem Rock Ridge vẫn là một **ISO 9660 filesystem** hợp lệ , nên nó cũng có thể đuợc sử dụng bởi các hệ điều hành không dựa trên UNIX. Linux hỗ trợ cả hai filesystem này, tuy nhiên Rock Ridge đuợc khuyến nghị và sử dụng một cách tự động.

Tuy vậy, các filesystem cũng chỉ là một nửa của trận chiến. Hầu hết các đĩa CD-ROM đều chứa các dữ liệu mà yêu cầu một chương trình đặc biệt mới có thể truy xuất tới chúng. Và đa phần các chương trình đó thì không đuợc thiết kế để chạy trên Linux (tất nhiên, là có thể chạy trên dosemu - một trình giả lập MS-DOS ; hoặc chạy trên Wine một trình giả lập MS-Windows.

Chớ trêu thay , Wine thực tế không đuợc dịch là *rượu* , nó nên đuợc dịch là *Wine Is Not an Emulator* :joy:. Wine,đúng hơn nên đuợc coi là một sự thay thế API cho các ứng dụng Windows. 
Chúng ta cũng có VMWare, một sản phẩm thương mại, nó sẽ giả lập toàn bộ các máy sử dụng kiến trúc x86. Một ổ đĩa CD-ROM thường đuợc truy xuất thông qua một **device file** tương ứng. Cũng có rất nhiều cách để kết nối một ổ đĩa CD tới máy tính : SCSI , từ một card âm thanh , hoặc EIDE (*Enhanced Integrated Drive Electronics*). Kiến thức về hardware hacking là cần thiết nếu bạn muốn thiết đặt những kiểu kết nối nói trên, tuy nhiên chúng sẽ không đuợc bao gồm trong bài viết này. Kiểu kết nối sẽ quyết định xem device file đại diện cho ổ đĩa CD sẽ trông sẽ như thế nào.

## 4.5. Băng từ.
Một ổ bằng từ, đương nhiên sẽ sử dụng băng từ, thứ tương tự với băng cát-xét bạn sử dụng cho âm nhạc. Một băng từ có dạng một dải, điều đó có nghĩa là, để đọc đuợc một phần dữ liệu nào đó của băng, bạn cần lướt qua tất cả các đoạn băng từ nằm giữa đoạn băng từ đầu tiền và đoạn băng từ cần tìm. Một ổ đĩa có thể truy xuất vào một sector một cách ngẫu nhiên tùy ý , nhưng với băng từ, việc lưu trữ dữ liệu dưới dạng một dải như vậy khiến chúng trở nên chậm chạp. Mặt khác, muốn tạo ra một băng từ sẽ mất ít tiền hơn do chúng không yêu cầu phải nhanh. Băng từ càng dài, càng chứa đuợc nhiều dữ liệu. Băng từ thích hợp cho việc lưu trữ và sao lưu không đòi hỏi tốc độ , nhưng yêu cầu chi phí thấp và dung lượng lớn.

## 4.6. Định dạng.
**Định dạng** là công đoạn ghi các mã hiệu lên các phương tiện lưu trữ bằng từ tính, nhằm đánh dấu các tracks và các sectors. Trước khi đĩa đuợc định dạng, bề mặt của đĩa là một đống hỗn độn các tín hiệu từ trường. Một điều quan trọng , đó là đĩa không thể đuợc sử dụng nếu như chúng chưa đuợc định dạng. 
Các thuật ngữ đuợc nhắc tới sau đây có thể sẽ gây cho bạn một chút hỗn loạn: trong MS-DOS và MS-Windows , cụm từ *formatting* hàm ý bao gồm cả công đoạn tạo ra một filesystem và công đoạn ghi mã hiệu như đã nói. Hai quá trình này thường đuợc kết hợp với nhau, đặc biệt là khi áp dụng lên đĩa mềm. Nếu như bạn muốn tạo ra một sự phân biệt, thì công đoạn ghi các mã hiệu cho ổ đĩa đuợc gọi là *low-level formatting* , trong khi công đoạn tạo ra một filesystem mới , đuợc gọi là *high-level formatting*. Trong thế giới UNIX, thì hai công đoạn trên đuợc phân biệt rõ ràng: *"Định dạng"* và "*Tạo một filesystem*". Hai cách gọi này từ giờ đuợc sử dụng xuyên suốt trong các bài viết thuộc series này.

Với các ổ đĩa IDE và một vài ổ đĩa SCSI , thì việc định dạng đĩa đã đuợc thực hiện ở nhà máy sản xuất và không cần đuợc lặp lại. Do đó hiếm khi người dùng cần bận tâm về điều này. Trong thực tế, việc định dạng lại một đĩa cứng có thể khiến cho nó hoạt động kém hơn. Ví dụ một đĩa có thể cần đuợc định dạng theo những cách rất đặc biệt nhằm cho phép chức năng *tự thay thế bad sectors* hoạt động.

Trong khi định dạng đĩa, có thể sẽ gặp phải vài điểm xấu , đựoc gọi là những *bad blocks* hoặc *bad sectors*. Thi thoảng những *bad sectors* này đựoc ổ đĩa tự xử lý, nhưng nếu như số lượng các bad sectors trở nên nhiều hơn, thì chúng ta cần phải làm một vài việc nếu muốn tránh những bad sectors như thế này. Đôi khi filesystem sẽ lưu ý tới các bad sectors và tránh sử dụng chúng. Ngoài ra, chúng ta cũng có thể tạo ra các phân vùng nho nhỏ , mỗi phân vùng này sẽ chỉ bao gồm một phần xấu trên đĩa mà thôi. Cách này ổn nếu như những bad sectors trở nên quá nhiều , do thi thoảng filesystem có thể gặp vấn đề với những vùng xấu quá lớn.

Các đĩa mềm đuợc định dạng bằng chương trình *fdformat*, *device file* của ổ đĩa mềm đuợc truyền vào như một tham số. Ví dụ, câu lệnh dưới đây sẽ định dạng lại một đĩa mềm mật độ cao, 3.5 inch nằm trong ổ đĩa mềm đầu tiên:
```bash
$ fdformat /dev/fd0H1440

Double−sided, 80 tracks, 18 sec/track. Total capacity 
        1440 kB.
Formatting ... done
Verifying ... done
$
```
Lưu ý rằng nếu như bạn muốn sử dụng một thiết bị có khả năng tự động phát hiện loại đĩa như */dev/fd0* chẳng hạn, bạn *phải* thiết đặt tham số của thiết bị với **setfdprm** trước tiên. Với câu lệnh trên, thay vì sử dụng */dev/fd0H1440*, chúng ta có thể chỉ sử dụng */dev/fd0*:
```bash
$ setfdprm /dev/fd0 1440/1440
$ fdformat /dev/fd0
Double−sided, 80 tracks, 18 sec/track. Total capacity 
        1440 KB.
Formatting ... done
Verifying ... done
$
```
Thường thì việc chọn ra đúng một *device file* tương ứng với loại đĩa mềm đuợc sử dụng sẽ thuận tiện hơn việc dùng *setfdprm* như cách thứ 2. Lưu ý rằng nếu như bạn định dạng đĩa mềm để chứa một lượng dữ liệu lớn hơn dung lượng mà nó đuợc thiết kế, điều đó thật không khôn ngoan chút nào.

*fdformat* cũng kiểm thử lại đĩa mềm , ví dụ kiểm tra các bad sectors chẳng hạn. Nó sẽ thử một bad block nhiều lần (*bạn có thể nghe thấy rõ, tiếng ồn của ổ đĩa tăng đáng kể*). Nếu đĩa mềm gặp các vấn đề ở phần ngoài như bụi bẩn bám trên đầu đọc/ghi , những lỗi tín hiệu sai, *fdformat* sẽ không ném ra một thông báo nào, nhưng nếu như đĩa mềm đuợc validated gặp phải một lỗi thật sự, quá trình kiểm thử có thể sẽ bị dừng lại đột ngột. Linux kernel sẽ in ra một thông điệp cho mỗi một lỗi nhập/xuất mà nó tìm thấy. Những thông điệp ấy có thể sẽ đi tới console hoặc nếu như *syslogs* đuợc sử dụng, các thông điệp sẽ đuợc lưu tại file */var/log/messages*. *fdformat* tự nó sẽ không nói cho bạn biết lỗi nằm ở đâu , mà người ta cũng chẳng quan tâm lắm , đĩa mềm đủ rẻ để có thể vứt đi không hối tiếc nếu như chúng gặp lỗi.
```bash
$ fdformat /dev/fd0H1440
Double−sided, 80 tracks, 18 sec/track. Total capacity 
        1440 KB.
Formatting ... done
Verifying ... read: Unknown error
$
```
Bất kỳ đĩa hoặc phân vùng nào cũng có thể sử dụng **badblocks** để kiểm tra các bad block. Nó không định dạng ổ đĩa, vì thế nó có thể đuợc sử dụng để kiểm tra cả những hệ thống tệp tin đã tồn tại trước đó. Ví dụ dưới đây sẽ kiểm tra một đĩa mềm 3.5 inch với hai bad blocks:
```bash
$ badblocks /dev/fd0H1440 1440
718
719
$
```
**badblocks** in ra số thứ tự của những bad block mà nó tìm thấy. Hầu hết các hệ thống tệp tin có thể tránh khỏi những bad blocks kiểu này. Chúng có một danh sách các bad blocks đã đuợc biết , danh sách này đuợc khởi tạo khi hệ thống tệp tin đuợc tạo ra, và có thể đuợc chỉnh sửa ở lần sau. Lệnh **mkfs** trước khi tạo một filesystem cũng sẽ tìm kiếm các badblock , khi filesystem đã đuợc tạo ra thì việc tìm kiếm và kiểm tra các badblock thuộc về lệnh **badblocks**, các blocks mới đuợc thêm vào bởi lệnh **fsck** , chúng ta sẽ nói về **mkfs** và **fsck** ở các section sau.

Nhiều các đĩa hiện đại tự động để ý đến các bad blocks, sau đó cố gắng sửa chữa chúng bằng cách sử dụng một block đặt biệt, đuợc dành riêng cho mục đích thay thế những block xấu. Công đoạn này không đuợc nhìn thấy bởi hệ điều hành.

## 4.7. Các phân vùng.
Một đĩa cứng có thể đuợc chia thành nhiều phân vùng khác nhau. Mỗi phân vùng hoạt động giống như thể chúng là một đĩa độc lập. Ý tưởng ở đây là , nếu như bạn có một đĩa cứng, và muốn cài đặt hai hệ điều hành lên cùng một đĩa đó, vậy bạn có thể chia nhỏ đĩa ra thành hai phân vùng riêng biệt. Mỗi hệ điều hành sở hữu phân vùng của riêng nó , không can thiệp gì vào phân vùng còn lại. Bằng cách này hai hệ điều hành có thể cùng tồn tại hòa bình trên một đĩa cứng , bằng không, bạn sẽ phải chi tiền ra mua một ổ cứng mới nếu muốn tiến hành cài đặt song song nhiều hệ điều hành.
Đĩa mềm thường không đuợc phân vùng. Mặc dù chúng có thể đuợc phân vùng, nhưng vì kích thước quá nhỏ , việc phân vùng trên đĩa mềm tỏ ra thừa thãi không hữu dụng. Đĩa CD-ROM cũng vậy, sẽ dễ dàng hơn khi sử dụng CD dưới dạng một đĩa duy nhất.

### 4.7.1. MBR, boot sectors và Bảng phân vùng.
Thông tin về cách mà đĩa cứng đuợc phân vùng đuợc lưu trong sector đầu tiên , chi tiết hơn thì là "sector đầu tiên của track đầu tiên trên bề mặt đầu tiên". Sector này đuợc gọi là *Master Boot Record* của đĩa. Đây là sector mà BIOS đọc khi máy bắt đầu khởi động. MBR chứa một chương trình nhỏ, chương trình này tiến hành đọc bảng phân vùng , kiểm tra xem phân vùng nào đang *active*  (*đựoc đánh dấu "có thể boot đựoc"*) , sau đó tiếp tục đọc nội dung sector đầu tiên của phân vùng đó. Sector này đựoc gọi là *boot sector của phân vùng* , về bản chất thì MBR cũng chỉ là một boot sector, nhưng do nó  có một trạng thái đặc biệt, nên kèm theo đó cũng phải có một cái tên đặc biệt. Boot sector của các phân vùng cũng chứa một chương trình nhỏ, chương trình này đọc nội dung những phần đầu tiên của hệ điều hành đuợc lưu trữ ở phân vùng đó (*giả sử phân vùng đó là bootable*) , sau đó khởi động hệ điều hành.

Luợc đồ phân vùng (*partitioning scheme*) là một khái niệm để chỉ một quy ước mà rất nhiều hệ điều hành tuân theo. Không phải tất cả đều tuân theo quy ước này, nhưng đó là ngoại lệ. Một vài hệ điều hành hỗ trợ các phân vùng, nhưng thực tế là chúng chiếm đóng một phân vùng duy nhất trên toàn bộ đĩa, sau đó sử dụng phương pháp của riêng mình để chia cái phân vùng đó thành những phân vùng nhỏ hơn. Một cách phân vùng ổ cứng khác thì đuợc hỗ trợ và tồn tại hòa thuận với các hệ điều hành khác (trong đó có Linux) , chúng không yêu cầu những phương pháp đặc biệt. Mặt khác, một hệ điều hành không hỗ trợ phân vùng sẽ không thể cùng tồn tại trên một đĩa với các hệ điều hành khác.

Như một biện pháp phòng ngừa an toàn, sẽ rất tốt nếu bạn có thể ghi sơ đồ bảng phân vùng của mình ra một mảnh giấy. Do đó nếu ngay cả khi phân vùng bị hỏng, bạn sẽ không đánh mất toàn bộ tệp tin. (*Một bảng phân vùng xấu có thể đuợc sửa chữa với chương trình fdisk*). Bạn có thể xem các thông tin liên quan bằng lệnh **fdisk -l**:
```bash
$ fdisk −l /dev/hda
Disk /dev/hda: 15 heads, 57 sectors, 790 cylinders
Units = cylinders of 855 * 512 bytes
 Device Boot    Begin   Start     End  Blocks   Id  System
/dev/hda1           1       1      24   10231+  82  Linux swap
/dev/hda2          25      25      48   10260   83  Linux native
/dev/hda3          49      49     408  153900   83  Linux native
/dev/hda4         409     409     790  163305    5  Extended
/dev/hda5         409     409     744  143611+  83  Linux native
/dev/hda6         745     745     790   19636+  83  Linux native
$
```
### 4.7.2. Phân vùng mở rộng và phân dùng logic.
Luợc đồ phân vùng cho máy tính chỉ cho phép 4 phân vùng. Số lượng này nhanh chóng trở thành quá ít trong cuộc sống thực, có thể là bởi vì nhiều người muốn rằng họ có thể sở hữu nhiều hơn 4 hệ điều hành cùng lúc trên một máy *(Linux, MS−DOS,
OS/2, Minix, FreeBSD, NetBSD, Windows/NT, ...v....v..)*. Nhưng chủ yếu bởi vì một hệ điều hành có nhiều phân vùng sẽ là một ý tưởng tốt. Ví dụ , vì lý do tốc độ, không gian dùng cho swap sẽ sở hữu phân vùng của riêng nó thay vì đuợc đặt vào phân vùng Linux chính.

Số lượng phân vùng là một giới hạn khá khó chịu của ổ cứng. Để vuợt qua giới hạn này, phân vùng mở rộng (*extended partitions*) ra đời. Thủ thuật này cho phép một phân vùng chính thành nhiều phân vùng con. *Phân vùng chính* mà chúng ta đang nói tới chính là phân vùng extended. Những phân vùng con đuợc gọi là những *phân vùng logic*. Chúng hoạt động tương tự như những phân vùng chính, nhưng đuợc tạo ra theo một cách khác. Không có sự khác biệt nào về tốc độ giữa chúng. Bằng cách sử dụng phân vùng mở rộng, bạn có thể sở hữu tới 15 phân vùng trên một đĩa.

Cấu trúc phân vùng của một đĩa cứng có thể trông giống như hình dưới. Đĩa đựoc chia thành 3 phân vùng chính. Một trong ba phân vùng chính lại đuợc chia thành hai phân vùng logic khác. Không phải phần nào của đĩa cũng đuợc phân vùng. Mỗi phân vùng chính đều có một boot sector của riêng nó.
![Hình ảnh không tìm thấy](https://github.com/TheEEs/linux-admin/blob/master/images/4.1.2.png?raw=true)

### 4.7.2. Các loại phân vùng.
Bảng phân vùng (cái thứ nằm trong MBR, một cái khác cũng nằm ở phân bùng mở rộng) , trong đó mỗi phân vùng có một byte trong bảng phân vùng để xác định phân vùng đó thuộc loại gì. Byte này có thể dùng để xác định hệ điều hành nằm trên phân vùng, hoặc mục đích mà phân vùng đuợc tạo ra. Nguyên nhân mà các byte này tồn tại, đó là để tránh khỏi việc có hai hệ điều hành vô tình cùng nằm trên một phân vùng. Tuy nhiên trong thực tế, các hệ điều hành thực sự chẳng mấy quan tâm tới những byte đó. Tệ hơn nữa, một vài hệ điều hành thì còn không sử dụng các byte này đúng cách, một vài phiên bản của DR-DOS bỏ qua các bit tín hiệu quan trọng trong byte này. Không có một sự chuẩn hóa nào giúp xác định các byte sẽ mang ý nghĩa gì, nhưng theo Linux , đây là một danh sách các loại phân vùng đuợc liệt kê bằng chương trình *fdisk*:
```bash
 0  Empty           1c  Hidden Win95 FA 70  DiskSecure Mult bb  Boot Wizard hid
 1  FAT12           1e  Hidden Win95 FA 75  PC/IX           be  Solaris boot
 2  XENIX root      24  NEC DOS         80  Old Minix       c1  DRDOS/sec (FAT−
 3  XENIX usr       39  Plan 9          81  Minix / old Lin c4  DRDOS/sec (FAT−
 4  FAT16 <32M      3c  PartitionMagic  82  Linux swap      c6  DRDOS/sec (FAT−
 5  Extended        40  Venix 80286     83  Linux           c7  Syrinx
 6  FAT16           41  PPC PReP Boot   84  OS/2 hidden C:  da  Non−FS data
 7  HPFS/NTFS       42  SFS             85  Linux extended  db  CP/M / CTOS / .
 8  AIX             4d  QNX4.x          86  NTFS volume set de  Dell Utility
 9  AIX bootable    4e  QNX4.x 2nd part 87  NTFS volume set df  BootIt
 a  OS/2 Boot Manag 4f  QNX4.x 3rd part 8e  Linux LVM       e1  DOS access
 b  Win95 FAT32     50  OnTrack DM      93  Amoeba          e3  DOS R/O
 c  Win95 FAT32 (LB 51  OnTrack DM6 Aux 94  Amoeba BBT      e4  SpeedStor
 e  Win95 FAT16 (LB 52  CP/M            9f  BSD/OS          eb  BeOS fs
 f  Win95 Extd  (LB 53  OnTrack DM6 Aux a0  IBM Thinkpad hi ee  EFI GPT
10  OPUS            54  OnTrackDM6      a5  FreeBSD         ef  EFI (FAT−12/16/
11  Hidden FAT12    55  EZ−Drive        a6  OpenBSD         f0  Linux/PA−RISC b
12  Compaq diagnost 56  Golden Bow      a7  NeXTSTEP        f1  SpeedStor
14  Hidden FAT16 <3 5c  Priam Edisk     a8  Darwin UFS      f4  SpeedStor
16  Hidden FAT16    61  SpeedStor       a9  NetBSD          f2  DOS secondary
17  Hidden HPFS/NTF 63  GNU HURD or Sys ab  Darwin boot     fd  Linux raid auto
18  AST SmartSleep  64  Novell Netware  b7  BSDI fs         fe  LANstep
1b  Hidden Win95 FA 65  Novell Netware  b8  BSDI swap       ff  BBT
```
### 4.7.3. Phân vùng một đĩa cứng.
Có rất nhiều chương trình giúp bạn tạo và hủy bỏ các phân vùng. Hầu hết các hệ điều hành đều có các tiện ích của riêng nó để tiến hành công việc này. Trong Linux chúng ta có **fdisk**, hướng dẫn sử dụng chi tiết của **fdisk** có thể đuợc đọc trên trang manual của nó lưu tại hệ thống. Ngoài ra còn có **cfdisk**, tương tự như **fdisk** , nhưng có một giao diện fullscreen đẹp hơn, dễ dùng hơn.

Khi sử dụng ổ đĩa IDE, phân vùng boot (*phân vùng chứa các bootable kernel images*) hoàn toàn phải nằm trong 1024 cylinders đầu tiên. Đây là bởi vì đĩa đuợc sử dụng từ BIOS trong khi khởi động , và BIOS thì không thể xử lý nhiều hơn 1024 cylinders. Miễn là tất cả các tệp tin đuợc sử dụng bởi BIOS đều nằm trong 1024 cylinders đầu tiên, bằng không sẽ có một vài hậu quá xấu. Do vậy rất khó khăn để bạn có thể xắp xếp lại đĩa cứng, làm sao bạn biết đuợc rằng khi kernel đuợc cập nhật hoặc khi chống phân mảnh ổ cứng cũng sẽ khiến cho hệ thống không boot đuợc ? Hãy đảm bảo rằng các boot images của bạn nằm gọn trong 1024 cylinders đầu tiên. Tuy nhiên , đây dường như không còn là vấn đề với các phiên bản mới hơn của LILO , hỗ trợ LBA (*Logical Block Addressing*). Bạn có thể đọc qua bài viết [này](https://kipalog.com/posts/Data-Storage----Dia-chi-CHS-va-dia-chi-LBA) để hiểu rõ hơn.

Các phiên bản mới của BIOS và các ổ đĩa IDE cũng đã có khả năng xử lý nhiều hơn 1024 cylinders. Nếu như bạn có một BIOS mới và một ổ đĩa IDE cũng mới như trên, bạn không cần phải lo lắng về vấn đề giới hạn 1024 đã nói.

Mỗi phân vùng nên có một số lượng *chẵn* các sector, do LInux sử dụng kích thước block là 1 Kilobyte (2 sector). Một sổ lượng các sector là lẻ sẽ khiến cho sector cuối cùng không đuợc sử dụng. Nó không có vấn đề gì mấy, nhưng để chừa ra một sector cuối thật xấu xí. Một vài phiên bản của **fdisk** sẽ cảnh báo nếu chúng ta đặt một phân vùng có số lượng các sector là lẻ.

Đồng thời , khi bạn muốn thay đổi kích thước của một phân vùng, trước tiên bạn nên sao lưu lại tất cả những gì bạn muốn lưu lại từ phân vùng ấy, xóa phân vùng, tạo một phân vùng mới và di chuyển tất cả những thứ vừa backed up vào phân vùng mới vừa tạo.
Như đã nói trên,  thay đổi kích thước của phân vùng quả là phiền hà và tiềm tàng rủi ro. Người ta ưa thích việc tính toán ra kích thước hợp lý của phân vùng ngay từ đầu, và sẽ sử dụng kích thước đó lâu dài. 

**fips** là một chương trình dành cho MS-DOS, chương trình này thay đổi kích cỡ một phân vùng MS-DOS nhưng không yêu cầu sao lưu hay khôi phục, nhưng đối với các hệ thống tệp tin khác, việc sao lưu hoặc khôi phục là vẫn cần thiết. **fips** đuợc bao gồm trong rất nhiều các bản phân phối Linux.

Làm ơn nhớ rằng việc phân vùng đĩa đôi khi khá nguy hiểm. **Đảm bảo rằng bạn đã sao lưu lại toàn bộ các dữ liệu quan trọng trước khi thử thay đổi kích thước của một phân vùng**.  Chương trình **parted** cũng có thể đuợc sử dụng để thay đổi kích thước của nhiều kiểu phân vùng. Bạn nên đọc kỹ documentation của nó trước khi thực hành.

### 4.7.4. Các file thiết bị và các phân vùng.
Mỗi phân vùng (tính cả những phân vùng logic) đều sở hữu những device file của riêng nó. Quy ước đặt tên cho những device file này đó là : Số thứ tự của phân vùng đuợc ghi ngay sau tên của ổ đĩa , các số từ 1-4 sẽ dùng cho các phân vùng primary, trong khi các số lớn hơn hoặc bằng 5 đuợc dùng cho các phân vùng logic. Ví dụ : */dev/hda1* là phân vùng đầu tiên trên đĩa IDE đầu tiên ; */dev/sdb7* lại là phân vùng logic thứ 3 của đĩa SCSI thứ 2.

## 4.8. Các hệ thống tệp tin.
### 4.8.1. Hệ thống tệp tin là gì ?
Một *hệ thống tệp tin-filesystem* là một phương pháp và một cấu trúc dữ liệu mà một hệ điều hành dùng nhằm để mắt tới các tệp trên đĩa hoặc phân vùng, nói chung là cách thức mà các tệp đuợc tổ chức trên đĩa. Cụm từ *hệ thống tệp tin* cũng đuợc sử dụng để chỉ một 	phân vùng hoặc một đĩa chứa các tệp tin , hoặc loại của filesystem. Do đó, khi một người nói "Tôi có 2 filesystem" , có nghĩa là người đó sở hữu hai phân vùng. Hoặc khi ai đó nói "Tôi có một extended filesystem" , người đó đang đề cập tới kiểu của phân vùng.

Sự khác biệt giữa đĩa/phân vùng và các hệ thống tệp tin mà chúng đang sở hữu khá quan trọng. Một vài chương trình (bao gồm các chương trình hỗ trợ tạo ra các filesystem) hoạt động trực tiếp trên các *raw sectors* của đĩa hoặc phân vùng, cách hoạt động này khá nguy hiểm và chỉ nên dành cho các hoạt động ở tầng thấp. Còn lại hầu hết các chương trình hoạt động trên một *filesystem* , do đó chúng sẽ sinh lỗi hoặc bị ngừng nếu như phân vùng đó không sử dụng filesystem nào (hoặc phân vùng đó chứa một filesystem lỗi thời, sai ...v...v...).

Trước khi một phân vùng hoặc một đĩa có thể đuợc sử dụng như một filesystem, chúng cần đuợc khởi tạo (*initialized*) , một data structure gọi là *bookkeeping* cũng đuợc viết vào đĩa. Quá trình này gọi là *tạo ra một filesystem*.

Hầu hết các UNIX filesystem có một cấu trúc tương đương hoặc gần tương đương nhau, mặc dù những chi tiết đôi khi sẽ khác nhau một chút. Những khái niệm trung tâm mà chúng ta sẽ nhắc tới sau đây bao gồm :*superblock, inode, data block, directory block và indirection block*. *Superblock* chứa thông tin về thứ gì đó liên quan đến toàn bộ filesystem, như kích thước chẳng hạn (thông tin có chính xác hay không sẽ phụ thuộc vào filesystem). Một *inode* chứa tất cả các thông tin về một file, ngoại trừ tên của nó. Tên file đuợc lưu trong thư mục, cùng với chỉ số của inode. Mỗi một entry trong một thư mục chứa tên file và chỉ số của inode đại diện cho file đó. *inode* chứa chỉ số của các *data blocks* , thứ đuợc dùng để lưu trữ nội dung của một file. *inode* dành ra một không gian chứa đuợc một số lượng ít các chỉ số của các data blocks. Nhưng nếu cần thiết , nhiều không gian hơn sẽ đuợc cấp phát động (*dynamically*) , không gian đuợc cấp phát động này đuợc gọi là các *indirect blocks* , chứa các con trỏ trỏ tới các data blocks. Không gian ấy chứa những *indirection blocks*, đúng như cái tên, khi muốn tìm các data block , đầu tiên bạn cần phải tìm chỉ số của data block đó trong indirection block tương ứng. Cấu trúc của một inode được mô tả như dưới hình:

	
![Hình ảnh không tìm thấy](https://github.com/TheEEs/linux-admin/blob/master/images/inode.png?raw=true)

Các UNIX filesystem thường cho phép tạo ra một cái *lỗ - hole* trong một file. Điều đó có nghĩa là filesystem sẽ giả vờ rằng một vị trí cụ thể nào đó trong file chỉ là những byte 0 , nhưng không có sector nào đuợc dành cho vị trí đó trong file cả. Như vậy về cơ bản, file sẽ chiếm ít không gian lưu trữ hơn. Cách này đặc biệt thường đuợc áp dụng với các chương trình nhỏ , các Linux shared libraries , một vài cơ sở dữ liệu, và trong một số trường hợp đặc biệt khác. *Holes* đuợc triển khai bằng cách lưu trữ một giá trị đặc biệt như địa chỉ của một data block, vào một indirection block hoặc inode. Điều đó có nghĩa là không một sectors nào đựoc cấp phát cho phần đó của file , khi đó phần ảo ấy đuợc gọi là một cái *lỗ* .

### 4.8.2. Rất nhiều các filesystem...
Linux hỗ trợ rất nhiều các loại filesystem khác nhau. Bài viết này liệt kê ra những loại filesystem *tối quan trọng* , đa phần đuợc Linux hỗ trợ rất tốt ,bao gồm:
* **minix**: Là một filesystem cổ thụ , lâu đời nhất. Đuợc đánh giá có độ tin cậy cao, nhưng lại bị hạn chế một số tính năng như mốc thời gian của file (created , edited ..v...v..) bị mất , tên tệp tối đa 30 ký tự; ngoài ra filesystem này còn hạn chế về dung lượng - mỗi **minix** filesystem có tối đa 64MB dung lượng.
* **xia**: Một phiên bản đuợc chỉnh sửa từ **minix** , cải thiện độ dài của tên tệp và kích thước của filesystem , ngoài ra thì chẳng còn tính năng gì khác. Filesystem này không phổ biến, nhưng nó đựoc đánh giá rằng chạy rất tốt.
* **ext3**: Filesystem này hội tụ tất cả các tính năng có từ **ext2** . Ở phiên bản này tính năng **journaling** được thêm vào (*journaling - nhật ký: là một tính năng giúp theo dõi các thay đổi diễn ra trong filesystem*). TÍnh năng này giúp cải thiện hiệu xuất và tăng thời gian khôi phục lại filesystem mỗi khi hệ thống gặp vấn đề hoặc khi người dùng tắt máy không đúng cách. **ext3** ngày càng trở nên phổ biến hơn **ext2**.
* **ext2**: Một trong những filesystem thuần Linux đầy tính năng. Nó đựoc thiết kế để dễ dàng trở nên tương thích.
* **ext**: Phiên bản cũ hơn của **ext2** , có độ tương thích kém hơn. Filesystem này khó để sử dụng trong các bản cài đặt Linux mới, mọi người đều có xu hướng ít nhất là sử dụng **ext2**.
* **reiserfs**: Một hệ thống tệp tin mạnh mẽ. **Journaling** đuợc sử dụng để giảm thiểu tốt đa sự mất mát dữ liệu.
* **jfs**: Một filesystem hỗ trợ Journaling, đuợc thiết kế bởi IBM để hoạt động trong các môi trường có hiệu suất cao.

Kèm theo đó, Linux cũng hỗ trợ rất nhiều các filesystem đuợc sử dụng các hệ thống khác. Điều này khiến việc trao đổi files dễ dàng hơn giữa các hệ điều hành. Các tệp tin hệ thống bên ngoài hoạt động tương tự như các hệ thống tệp tin thuần Linux, tuy nhiên chúng có thể không hỗ trợ những tính năng mà một UNIX filesystem cần phải có, đôi khi cũng xuất hiện một vài hạn chế. Các hệ thống tệp tin bên ngoài phổ biến bao gồm :
* **msdos**: Hệ thống tệp tin này có khả năng tương thích với hệ điều hành MS-DOS , Windows NT và FAT filesystem.
* **umsdos**: Một phiên bản của *msdos* đuợc mở rộng cho Linux, cho phép tệp tin dài hơn. Hỗ trợ xác định chủ sở hữu, các quyền hạn, các liên kết và các device files.
* **vfat**: Một bản mở rộng từ FAT, cũng đuợc biết tới dưới tên gọi **FAT32**. Hỗ trợ dung lượng đĩa lớn hơn FAT. Hầu hết các đĩa dưới quyền Microsoft Windows đều sử dụng *vfat*.
* **iso9660**: Hệ thống tệp tin chuẩn đuợc quy định sử dụng cho các CD-ROMs. Một phiên bản mở rộng của filesytem này tên là *Rock Ridge* hỗ trợ tên file dài hơn , ngày nay *Rock Ridge* đuợc tự động hỗ trợ và khuyên dùng hơn là *iso9660*.
* **nfs**: Một hệ thống tệp tin trên mạng , hỗ trợ chia sẻ file giữa nhiều máy tính với nhau.
* **smbfs**: Cũng là một hệ thống tệp tin mạng. Nó tương thích với giao thức chia sẻ file của Microsoft Windows.
* **NTFS**: Hệ thống tệp tin đuợc cho là cao cấp nhất của Windows cho đến hiện tại, nó hỗ trợ *journaling*.

Việc bạn lựa chọn filesystem nào để sử dụng phụ thuộc vào tình huống cụ thể. Nếu vì lý do tương thích, hoặc những lý do khác khiến bạn bắt buộc phải sử dụng các filesystem không thuần Linux, hãy sử dụng nó. Nhưng nếu như đuợc quyền tự do lựa chọn, hãy chọn *ext3*, do nó sử hữu tất cả các tính năng từ *ext2*, và hỗ trợ journaling rất ổn định. 

Cũng có một filesytem tên là *proc*, thường đuợc truy xuất qua thư mục */proc*. Nó thực tế không hẳn là một filesystem, mặc dù nhìn bên ngoài thì nó chẳng khác gì những filesystem đã nói trên. Filesystem này làm cho chúng ta dễ dàng hơn nếu muốn truy xuất đến các cấu trúc dữ liệu nhất định của hạt nhân, ví dụ như danh sách các tiến trình. Nó trình bày các cấu trúc dữ liệu dưới dạng một filesystem "xịn" , và filesystem ấy có thể đuợc truy xuất bình thường bởi các công cụ quản lý , truy xuất file. Ví dụ, để lấy thông tin về tất cả các process hiện có, một người có thể sử dụng lệnh sau:
```bash
$ ls −l /proc
total 0
dr−xr−xr−x   4 root     root            0 Jan 31 20:37 1
dr−xr−xr−x   4 liw      users           0 Jan 31 20:37 63
dr−xr−xr−x   4 liw      users           0 Jan 31 20:37 94
dr−xr−xr−x   4 liw      users           0 Jan 31 20:37 95
dr−xr−xr−x   4 root     users           0 Jan 31 20:37 98
dr−xr−xr−x   4 liw      users           0 Jan 31 20:37 99
−r−−r−−r−−   1 root     root            0 Jan 31 20:37 devices
−r−−r−−r−−   1 root     root            0 Jan 31 20:37 dma
−r−−r−−r−−   1 root     root            0 Jan 31 20:37 filesystems
−r−−r−−r−−   1 root     root            0 Jan 31 20:37 interrupts
−r−−−−−−−−   1 root     root      8654848 Jan 31 20:37 kcore
−r−−r−−r−−   1 root     root            0 Jan 31 11:50 kmsg
−r−−r−−r−−   1 root     root            0 Jan 31 20:37 ksyms
−r−−r−−r−−   1 root     root            0 Jan 31 11:51 loadavg
−r−−r−−r−−   1 root     root            0 Jan 31 20:37 meminfo
−r−−r−−r−−   1 root     root            0 Jan 31 20:37 modules
dr−xr−xr−x   2 root     root            0 Jan 31 20:37 net
dr−xr−xr−x   4 root     root            0 Jan 31 20:37 self
−r−−r−−r−−   1 root     root            0 Jan 31 20:37 stat
−r−−r−−r−−   1 root     root            0 Jan 31 20:37 uptime
−r−−r−−r−−   1 root     root            0 Jan 31 20:37 version
.....
```
Lưu ý rằng mặc dù nó đuợc gọi là một *filesystem* , nhưng nó không hề chiếm dụng một sector nào trên đĩa. Nó chỉ *tồn tại trong sự tưởng tượng của hạt nhân* mà thôi. Mỗi khi ai đó cố gắng truy xuất vào hệ thống tệp tin proc, kernel sẽ khiến cho nó trông như thể là một filesystem đã tồn tại ở đâu đó trên đĩa , mặc dù nó không hề như vậy. Nên, thậm chí ngay cả khi bạn nhìn thấy một file */proc/kcore* lên tới hàng Gigabytes, thì file đó cũng không chiếm dụng tài nguyên nào trên đĩa của bạn hết.

### 4.8.3. Filesystem nào nên đuợc sử dụng ?
Thường có một vài điểm nho nhỏ cần lưu tâm giữa muôn vàn các filesystem khác nhau.Hiện tại với Linux , **ext3** là filesystem đuợc sử dụng phổ biến nhất do nó hỗ trợ journaling. Có lẽ ở thời điểm này nó là sự lựa chọn khôn ngoan nhất. Phụ thuộc vào tốc độ, độ tin cậy, mức tương thích, và những lý do khác, đó sẽ là những lý do hữu ích để giúp bạn lựa chọn filesytem cần thiết. Về cơ bản, việc lựa chọn filesystem đuợc thực hiện theo từng trường hợp cụ thể.
Một filesystem hỗ trợ journaling, cũng đuợc gọi là một *journaled filesystem*. Một *journaled filesystem* truy trì một log hay tạm gọi là một cuốn nhật ký, ghi lại mọi sự thay đổi diễn ra trong filesystem đó. Giả sử một hệ thống bị crash, hay do đứa em 2 tuổi ngây thơ vô số tội của bạn lỡ tay bấm nút power , lúc này tính năng journaling tỏ ra hữu dụng. Filesystem sẽ sử dụng *cuốn nhật ký* của nó để tái tạo lại các dữ liệu chưa đuợc lưu hoặc các dữ liệu bị mất mát. Tính năng này đã trở thành một tiêu chuẩn cho các filesystem chạy trên Linux. Tuy nhiên, bạn không nên quá chủ quan , lỗi vẫn có thể xảy ra. Tính năng *journaling* chỉ nên đuợc coi là một liều thuốc phòng ngừa thay vì một liều thuốc thần. Luôn luôn chắc chắn rằng bạn đã sao lưu lại toàn bộ dữ liệu , để dùng cho trường hợp khẩn cấp.

### 4.8.3. Tạo ra một filesystem.
Các filesystem đuợc tạo, initialize bằng lệnh **mkfs**. Thực tế là mỗi loại filesystem sẽ có một chương trình riêng biệt giúp tạo mới và initialized cái filesystem đó, **mkfs** chỉ là một *front-end* giúp chạy chương trình tương ứng phụ thuộc vào filesystem đuợc chỉ ra trước đó mà thôi. Loại của filesystem đuợc chỉ rõ bằng tùy chọn `-t fstype`.

Chương trình đuợc gọi bởi **mkfs** có một giao diện dòng lệnh hơi khác chút xíu. 3 tùy chọn phổ biến và quan trọng nhất, đuợc liệt kê ở đây:
* **-t**: Chỉ rõ loại filesystem 
* **-c**: Tìm kiếm các bad blocks và khởi tạo danh sách các bad blocks phù hợp.
* **−l filename**: Một danh sách chứa các bad blocks đuợc biết trước.

Cũng có nhiều những chương trình đuợc viết ra để có thể thêm vào các tùy chọn đặc biệt khi tạo một filesystem. Ví dụ **mkfs.ext3** có tùy chọn **-b** để người quản trị hệ thống tự chọn kích thước block.

Để tạo ra một hệ thống tệp tin **ext2** trên một đĩa mềm, chúng ta thực thi lệnh sau:
```bash
$ fdformat −n /dev/fd0H1440
Double−sided, 80 tracks, 18 sec/track. Total capacity 
1440 KB.
Formatting ... done
$ badblocks /dev/fd0H1440 1440 > ./bad−blocks
$ mkfs.ext2 −l ./bad−blocks /dev/fd0H1440
mke2fs 0.5a, 5−Apr−94 for EXT2 FS 0.5, 94/03/10
360 inodes, 1440 blocks
72 blocks (5.00%) reserved for the super user
First data block=1
Block size=1024 (log=0)
Fragment size=1024 (log=0)
1 block group
8192 blocks per group, 8192 fragments per group
360 inodes per group
Writing inode tables: done
Writing superblocks and filesystem accounting information: done
$
```

Như bạn thấy, đầu tiên, chúng ta sẽ định dạng lại đĩa mềm , tùy chọn *-n* sẽ bỏ qua công đoạn kiểm thử, ví dụ kiểm tra các bad sectors chẳng hạn. Sau đó chương trình *badblocks* sẽ tìm kiếm các bad sectors, danh sách các bad sectors đuợc viết vào file *bad-block* trong cùng thư mục hiện hành. Cuối thì thì filesystem mới đuợc tạo ra, cùng với danh sách các bad sectors mà **badblocks** tìm thấy.

Tùy chọn *-c* cũng có thể đuợc sử dụng trong *mkfs* thay cho chương trình *badblocks*. Ví dụ dưới đây sẽ cho bạn thấy điều đó:
```bash
mkfs.ext2 −c  /dev/fd0H1440
mke2fs 0.5a, 5−Apr−94 for EXT2 FS 0.5, 94/03/10
360 inodes, 1440 blocks
72 blocks (5.00%) reserved for the super user
First data block=1
Block size=1024 (log=0)
Fragment size=1024 (log=0)
1 block group
8192 blocks per group, 8192 fragments per group
360 inodes per group
Checking for bad blocks (read−only test): done
Writing inode tables: done
Writing superblocks and filesystem accounting information: done
```
Có vẻ như tùy chọn *-c* thuận tiện hơn, nhưng sử dụng **badblocks** một cách riêng biệt cũng rất cần thiết cho hoạt động kiểm tra sau khi filesystem đã đuợc tạo. Quá trình chuẩn bị các filesystem trên đĩa cứng hoặc các phân vùng cũng tương tự như với ổ đĩa, ngoại trừ việc bạn không cần phải format chúng trước tiên.

### 4.8.4. Kích thước block của một filesystem.
*Block size* là một khái niệm, chỉ ra rằng đó là kích cỡ mà filesystem sẽ sử dụng để đọc/ghi dữ liệu. Các block có kích thước lớn sẽ giúp cải thiện hiệu xuất vào/ra của đĩa khi sử dụng các file lớn , như database chẳng hạn. Điều này xảy ra là do đĩa có thể đọc/ghi dữ liệu theo một chu kỳ lâu hơn trước khi tìm kiếm block kế tiếp. Mặt khác, nếu như filesystem của bạn chỉ sử dụng các file có kích thước bé, ví dụ */etc/* chẳng hạn, điều này gây ra một sự lãng phí tài nguyên đĩa rất đáng kể.

Ví dụ , khi bạn thiết đặt kích thước block là 4096, hoặc 4K , sau đó bạn tạo một file có kích thước 256byte , thì file đó vẫn sẽ chiếm trọn toàn bộ 4K không gian trên đĩa của bạn. Với một tệp tin, thì thế này có vẻ vẫn chưa hề hấn gì, nhưng khi số lượng các tệp lên tới cả chục, cả trăm, cả nghìn file , bạn sẽ nhận thấy rằng sự lãng phí hiện ra rất rõ ràng. 
Kích thước của block cũng có thể ảnh hưởng tới kích thước file tối đa mà một filesystem hỗ trợ. Đó là bởi vì nhiều filesystem hiện đại ngày nay không giới hạn kích thước của block hoặc file, nhưng lại giới hạn số lượng các block. Tôi nghĩ công thức này sẽ hữu dụng với bạn : **block_size \* SốLượngTốiĐaCácBlock = KíchThướcTốiĐaMộtFileCóThểĐạtĐuợc** 

### 4.8.5. So sánh các filesystem.
Bảng dưới đây so sánh các tính năng có trong các filesystem phổ biến/cận phổ biến.
![Hình ảnh không thể tìm thấy](https://github.com/TheEEs/linux-admin/blob/master/images/4.1.3.png?raw=true)
Bảng dưới đây liệt kê ra các đơn vị đo lường thông tin trong máy tính hay đuợc sử dụng
![Hình ảnh không thể tìm thấy](https://github.com/TheEEs/linux-admin/blob/master/images/4.1.4.png?raw=true)
Bạn nên lưu ý rằng kích thước file rất hiếm khi lên tới các đơn vị Exabytes, Zettabytes và Yottabytes, mặc dù trong một vài trường hợp đặc biệt, chúng có thể.

> Link bài viết tiếp theo [\[Series\] Linux System Administrator's guide. \[Phần 4.2\]](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-4-2).
> Link bài viết trước tại [đây](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-3)
> Nguồn : [The Linux Documentation Project](http://tldp.org)