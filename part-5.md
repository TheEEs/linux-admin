# Chương 5. Quản lý bộ nhớ.

> **"Bước đi trên thiên đạo, người thống trị tất cả"** - Kamen Rider Kabuto.

Phần này trình bày về tính năng quản lý bộ nhớ trong Linux, bao gồm **bộ nhớ ảo** và **disk buffer cache**. Mục đích, những công việc và những thứ người quản trị cần xem xét cũng sẽ đuợc miêu tả.

## 5.1. Bộ nhớ ảo là gì ?
Linux hỗ trợ *bộ nhớ ảo - virtual memory*. Cơ chế chính của tính năng này đó chính là sử dụng đĩa như một phần mở rộng của RAM, từ đó tăng dung lượng bộ nhớ dư thừa của hệ thống. Kernel sẽ viết nội dung của những block đang không đuợc sử dụng vào đĩa , do đó bộ nhớ có thể đuợc sử dụng cho những mục đích khác. Những nội dung ấy sẽ đuợc viết lại vào bộ nhớ mỗi khi có một chương trình yêu cầu sử dụng chúng. Những việc tráo đổi dữ liệu giữa đĩa và RAM như trên diễn ra ở tầng thấp và không đuợc nhìn thấy bởi người dùng. Các chương trình chạy trên Linux chỉ nhìn thấy một lượng lớn bộ nhớ có sẵn nhưng lại không nhận ra rằng những phần dữ liệu của chúng cũng bị viết vào/lôi ra từ đĩa hết lần này đến lần khác. Tất nhiên là việc đọc và ghi đĩa cứng lâu hơn rất rất nhiều so với việc sử dụng bộ nhớ thật, do đó các chương trình sẽ không chạy nhanh như trên lý thuyết. Phần đĩa cứng đuợc sử dụng như một bộ nhớ ảo , đuợc gọi là *swap space*.

Linux có thể sử dụng một file bình thường hoặc một phân vùng riêng biệt làm *swap space*. Một phân vùng thì sẽ nhanh hơn, nhưng một file thì lại có thể dễ dàng hơn trong việc thay đổi kích thước của *swap space*. Khi bạn biết bạn cần bao nhiêu không gian đĩa cho *swap space*, bạn nên sử dụng một phân vùng. Nhưng nếu bạn không chắc chắn về dung lượng bộ nhớ ảo nên có, hãy sử dụng file. Thường thì người dùng Linux lâu năm sẽ sử dụng file trước, sau một thời gian họ sẽ ước lượng đuợc *"Bây nhiêu dung lượng bộ nhớ ảo thôi là đủ"*, lúc đó họ mới tiến hành tạo một phân vùng riêng.

Bạn cũng nên biết rằng Linux cho phép sử dụng nhiều phân vùng/file swap cùng một lúc. Điều này có nghĩa là nếu đôi khi bạn nghĩ rằng mình cần thêm nhiều không gian swap hơn nữa, bạn có thể thiết lập thêm một file/phân vùng swap mới ngay lúc đó.

Một lưu ý khác về thuật ngữ trong hệ điều hành : Khoa học máy tính thường phân biệt giữa **swapping**(*viết toàn bộ tiến trình vào không gian swap*) với **paging**(*Chỉ viết những phần có kích thuớc cố định, thường là một số ít kilobytes, trong một lần*). **Paging** thuờng sẽ hiệu quả hơn **swapping** , nhưng thuật ngữ Linux truyền thống vẫn gọi quá trình này là **swapping**,  và thuật ngữ này đuợc sử dụng thường xuyên bởi những người dùng Linux , bất chấp sự phân biệt.

## 5.2. Tạo một không gian swap.
Một swap file cũng đơn thuần chỉ là một file bình thường. Bạn cần lưu ý rằng swapfile không nên tồn tại bất kỳ cái lỗ nào. File này sẽ đuợc sử dụng bởi chuơng trình **mkswap**, và nó phải nằm trên đĩa hoặc phân vùng cục bộ (*ngoại trừ những filesystem được chia sẻ thông qua NFS*). 

Bạn cũng cần lưu ý về những cái lỗ trong file. Swap file vốn dĩ đã chiếm dụng riêng một phần không gian trên đĩa nên kernel có thể nhanh chóng swap một page mà không cần lo nghĩ về các công đoạn như *"Làm thế nào để cấp phát thêm một block cho file đó"*. Kernel cũng chỉ sử dụng các sector đã được cấp phát cho file đó mà thôi. Việc tồn tại một (*hoặc nhiều*) cái lỗ trong swap file tương đương với việc không có một block nào được cấp phát cho vị trí đó của file cả, thật không tốt nếu kernel vẫn cố gắng swap lên những vị trí đó.

Một cách khá tốt để tạo ra một file swap mà không gặp *Lỗ* được thực thi bởi câu lệnh sau:
```bash
$ dd if=/dev/zero of=/extra-swap bs=1024 count=1024
1024+0 records in
1024+0 records out
```
Trong đó */extra-swap* là tên của file swap , size của nó được chỉ rõ phía sau *count=*. Tốt nhất kích thước của file nên là một bội số của 4, bởi vì kernel swap các dữ liệu theo từng *page*. Mỗi *page* 4Kilobytes. Nếu như kích thước của swapfile không phải một bội số của 4, sẽ gây ra nguy cơ lãng phí những block cuối cùng.

Một phân vùng swap cũng có chức năng tương tự như một swapfile. Bạn tạo ra nó cũng bằng cách mà bạn dùng để tạo ra các phân vùng khác, điểm khác biệt duy nhất chính là phân vùng này không có filesystem nào bên trong nó, nó được sử dụng dưới dạng thô kệch vốn có ban đầu. Trong quá trình tạo ra phân vùng swap, bạn cũng nên đánh dấu nó là một phân vùng swap với ID 82. Nó sẽ khiến bạn dễ dàng hơn để nhận ra những phân vùng swap khi liệt kê bảng phân vùng với **fdisk** hoặc **cfdisk**. Mặc dù bạn không đánh dấu nó là một swap partition cũng chẳng sao.

Sau khi bạn đã tạo ra một swapfile hoặc một phân vùng swap, bước tiếp theo bạn cần làm đó là ghi một chữ ký lên phần bắt đầu của phân vùng đó. Nó sẽ chứa một vài thông tin có tính quản trị được sử dụng bởi kernel. Công việc này được tiến hành bởi lệnh **mkswap**, như dưới đây:
```bash
$ mkswap /extra-swap 1024
Setting up swapspace, size = 1044480 bytes
$
```

Lưu ý rằng không gian swap vẫn chưa được sử dụng, nhưng nó đã tồn tại. Chỉ là kernel chưa sử dụng nó để cung cấp bộ nhớ ảo mà thôi.

Bạn nên thật sự cẩn thận khi sử dụng mkswap , vì nó không kiểm tra file hoặc partition của bạn đã được sử dụng cho mục đích khác hay chưa. Bạn có nguy cơ khi đè các tệp quan trọng hoặc các phân vùng quan trọng khi sử dụng **mkswap**. May mắn thay, thường thì bạn sẽ chỉ cần tới **mkswap** khi cài đặt hệ thống.

Trình quản lý bộ nhớ của Linux giới hạn dung lượng của mỗi không gian swap là 2GB, tuy nhiên bạn có thể sử dụng đồng thời đến 8 swap spaces , tất cả là 16BG.

## 5.3. Sử dụng một không gian swap.

Bạn đã tạo ra một swap space, tiếp theo bạn cần chuẩn bị nó để nó có thể được đưa vào sử dụng. Công việc này được tiến hành bởi lệnh **swapon**. Lệnh này nói với kernel rằng không gian swap có thể được sử dụng. Đường dẫn tới không gian swap được truyền vào như một tham số, như sau:
```bash
$ swapon /extra-swap
$
```

Các không gian swap cũng có thể được sử dụng một cách tự động bằng cách liệt kê chúng vào trong tệp */etc/fstab*:
```plain
/dev/hda8  none  swap  sw 0 0
/swapfile  none  swap  sw 0 0 
```

Các đoạn kịch bản khởi động sẽ chạy lệnh `swapon -a`, lệnh này sẽ swap-on tất cả các không gian swap được liệt kê trong */etc/fstab*. Lệnh **swapon** thường chỉ được sử dụng khi nào hệ thống thực sự cần thêm không gian swap.

Bạn có thể theo dõi việc sử dụng các không gian swap với lệnh **free**. Nó sẽ nói cho bạn biết tổng số lượng các không gian swap được sử dụng. Ví dụ:
```bash
$ free
              total        used        free      shared  buff/cache   available
Mem:        8065332      928048     5335272      324392     1802012     6731680
Swap:       8000508           0     8000508
$
```

Dòng thứ nhất (*Mem:*) cho bạn thấy được tình trạng của bộ nhớ vật lý.  Cột *shared* cho biết tổng dung luợng bộ nhớ được chia sẻ giữa các tiến trình khác nhau , càng nhiều càng tốt. Cột **buff/cache** cho thấy kích thước hiện tại của bộ nhớ đệm ổ đĩa.

Hàng phía dưới (*Swap:*) cho thấy các thông tin tương tự về các không gian swap. Nếu như bạn không nhìn thấy dòng này, có nghĩa là các không gian swap của bạn chưa được kích hoạt.

Những thông tin tương tự cũng có sẵn bởi lệnh **top**, hoặc trong file */proc/meminfo*.  Khá khó khăn để bạn có thể lấy thông tin về lượng sử dụng của một swap space cụ thể.

Một không gian swap có thể bị tắt đi bởi lệnh **swapoff**. Lệnh này không được dùng thường xuyên, trừ trường hợp bạn vừa mới kích hoạt một không gian swap tạm thời. Khi sử dụng **swapoff** , mọi dữ liệu trong không gian swap đó sẽ được ghi trở lại RAM, nhưng nếu RAM không còn chỗ để chứa chúng, chúng sẽ lại được ghi vào một không gian swap khác. Nếu như không có đủ luợng bộ nhớ ảo để giữ tất cả các pages cần thiết , Linux sẽ lâm vào tình trạng rất khó đỡ, nó sẽ bị đơ. Và sau một khoảng thời gian dài, khả năng cao hệ thống sẽ được khôi phục, nhưng trong lúc chờ cho hệ thống khôi phục, nó sẽ không thể được sử dụng. Bạn nên kiểm tra xem hệ thống có đủ bộ nhớ hay không trước khi quyết định tháo bỏ một không gian swap.

Tất cả các không gian swap được sử dụng tự động bởi lệnh **swapon -a** thì cũng có thể được tháo bỏ bởi lệnh **swapoff -a**. Lệnh này sẽ nhìn vào file */etc/fstab* để tìm ra những không gian swap thích hợp để tiến hành tháo bỏ.

Thi thoảng một luợng lớn các không gian swap có thể được sử dụng ngay cả khi RAM còn rất nhiều không gian trống. Điều này có thể xảy ra , ví dụ như khi một tiến trình lớn , chiếm dụng rất nhiều không gian RAM bỗng nhiên bị phá hủy và để lại rất nhiều bộ nhớ trống. Những page trong swap space sẽ không được tự động ghi trở lại RAM cho tới khi chúng cần phải được sử dụng, nên bộ nhớ vật lý có thể vẫn dư rất nhiều RAM trống trong một khoảng thời gian dài. Bạn không cần phải lo về vấn đề này, nhưng tôi nghĩ đây là một vấn đề xảy ra không hiếm, bạn cũng nên biết đôi chút về nó.

## 5.4. Chia sẻ các không gian swap với các hệ điều hành khác.
*Bộ nhớ ảo* là tính năng được hỗ trợ bởi rất nhiều các hệ điều hành đa nhiệm phổ biến. Do mỗi hệ điều hành chỉ cần bộ nhớ ảo lúc chúng đang chạy, nếu máy cục bộ của bạn có nhiều hệ điều hành, mỗi hệ điều hành chiếm dụng một không gian riêng trên đĩa cho bộ nhớ ảo, như vậy dẫn tới lãng phí khá lớn. Việc chia sẻ những không gian swap với những hệ điều hành sẽ khá hiệu quả trong việc cải thiện hiệu suất hệ thống. Điều này hoàn toàn có thể thực hiện, nhưng sẽ yêu cầu một vài kiến thức nâng cao, nên chúng ta sẽ không đi sâu về chủ đề này.

## 5.5. Cấp phát không gian swap.
Một vài người sẽ nói với bạn rằng bạn nên cấp phát bộ nhớ ảo có dung lượng gấp đôi bộ nhớ RAM , nhưng đó là một gợi ý chủ quan. Dưới đây mô tả làm thế nào bạn có thể cấp phát không gian cho một nhớ ảo một cách hợp lý:
* Ước tính tổng nhu cầu về bộ nhớ của bạn. Đây là lượng bộ nhớ lớn nhất bạn sẽ cần tại một thời điểm, là tổng lượng bộ nhớ đòi hỏi của tất cả các chương trình bạn muốn chạy cùng một thời điểm cộng lại. Sự ước tính này có thể dễ dàng được thực hiện bằng cách chạy tất cả các chương trình bạn muốn trong cùng một thời điểm, như chưa bao giờ được chạy :joy:.

> Ví dụ nếu bạn muốn chạy X , bạn nên cấp phát khoảng 8MB cho nó , bạn tiếp tục chừa ra vài MB cho gcc , tương tự với các chương trình khác. Kernel cũng sử dụng khoảng 1MB virtual memory cho chính nó. Các shell phổ biến và các tiện ích nhỏ khác cũng nên có vài trăm KB dự phòng. Việc tính toán dung lượng để cấp phát không yêu cầu bạn phải tính toán chính xác, nhưng thường người ta sẽ tính theo xu hướng bi quan, có nghĩa là cứ cộng dư thêm một chút không gian swap so với những gì đã tính trước đó, cho chắc.
> Bạn hãy nhớ rằng không một ai sử dụng máy tính mà không chiếm dụng bộ nhớ. Càng nhiều người sử dụng hệ thống cùng lúc, lượng bộ nhớ bị chiếm dụng càng tăng cao. Tuy nhiên, nếu hai người cùng chạy một chương trình giống nhau tại một thời điểm, tổng lượng bộ nhớ tiêu thụ thường sẽ không tăng gấp đôi, do các page chứa mã thực thi và các shared libraries chỉ tồn tại duy nhất một lần.
> Chương trình **free** và **ps** cũng hữu dụng trong việc ước lượng  nhu cầu về bộ nhớ.

* Thêm một vài phương án bảo mật vào sự ước luợng ở bước 1. Đó là bởi vì sự ước lượng chắc hẳn sẽ sai lệch, do bạn có thể sẽ quên chạy một vài chương trình mà bạn muốn. Để đảm bảo rằng bạn có một vài không gian đi kèm phòng hờ những trường hợp cần thiết, giả sử trong trường hợp của tôi, tôi sẽ cấp phát thêm khoảng 2MB không gian swap nữa.
*  Dựa trên sự tính toán phía trên , bạn biết được bạn sẽ cần tất cả bao nhiêu bộ nhớ thông qua việc nhận ra việc bao nhiêu dung lượng RAM bị chiếm dụng cho mỗi chương trình. (*Trên một vài hệ thống sử dụng những phiên bản UNIX cũ, bạn cần phải cấp phát không gian swap với kích thước ngang bằng với kích thước RAM mà bạn có , nên nếu bạn đang dùng những phiên bản UNIX như vậy, sự tính toán như trên là không cần thiết*)
*  Nếu sự tính toán của bạn cho ra một dung lượng quá lớn, bạn nên nghĩ đến sự đầu tư thêm vào bộ nhớ vật lý. Nếu không thì hiệu xuất hệ thống mang lại sẽ rất chậm.

Tốt nhất là bạn nên có vài không gian swap trong máy, kể cả nếu sự tính toán phía trên cho bạn thấy rằng bạn không cần một bộ nhớ ảo nào. Linux sử dụng những không gian swap để chống lại việc bộ nhớ vật lý bị xâm thực quá đà, nên bộ nhớ vật lý sẽ luôn có không gian trống nhiều nhất có thể. Linux sẽ swap các pages đang không được sử dụng trong RAM vào swap space, kể cả khi bộ nhớ vật lý vẫn còn dư thừa rất nhiều không gian , và những không gian dư thừa ấy cũng chẳng để làm gì cả. Điều này nhằm tránh việc các chương trình phải chờ hệ thống swap các pages cũ mỗi khi nó cần đụng tới dữ liệu của mình.

Các không gian swap có thể nằm trên nhiều đĩa khác nhau. Điều này đôi khi cải thiện hiệu suất khá hiệu quả, tùy thuộc vào tốc độ tương đối của đĩa cũng như một vài yếu tố phụ khác. Bạn không nên tuyệt đối tin vào lời khuyên về việc sử dụng swap spaces của bất kỳ ai khác vì không phải lúc nào nó cũng đúng.

## 5.6. Buffer Cache.
Việc đọc từ đĩa rất lâu so với truy xuất vào RAM. Thêm nữa, khá nhiều lần hệ thống đọc đi đọc lại một phần nhất định của đĩa với một chu kỳ thời gian khá ngắn. Ví dụ, đầu tiên, một hệ thống có thể đọc một thông điệp email, sau đó tiếp tục đọc nội dung email đó để đưa vào một editor nào đó, sau đó chương trình mail lại tiếp tục đọc nó khi sao chép nó vào một folder khác. Hoặc chúng ta hãy xem xét tần xuất lệnh **ls** có thể chạy trên một hệ thống nhiều người dùng. Bằng cách đọc thông tin từ đĩa một lần duy nhất , sau đó lưu trữ thông tin đó trong bộ nhớ cho đến khi thông tin đó không cần thiết nữa, lệnh **ls** có thể tự cải thiện hiệu xuất của chính nó. Cách thức hoạt động trên của **ls** được dựa vào một thủ thuật của hệ điều hành , gọi là *disk buffering - bộ đệm đĩa*, và bộ nhớ được sử dụng cho mục đích trên gọi là *buffer cache*.

Không may mắn lắm, RAM tuy nhanh nhưng lại là nguồn tài nguyên khan hiếm có hạn, buffer cache thường không thể đủ lớn (*nó không thể giữ  mọi dữ liệu một cách lâu dài*). Khi buffer cache bị đầy, những dữ liệu không được sử dụng trong một thời gian dài nhất sẽ bị xóa bỏ và bộ nhớ do đó sẽ được giải phóng , dành không gian cho những dữ liệu mới.

Một buffer cache , đương nhiên có thể được viết vào. Mặt khác, dữ liệu được viết vào cũng sẽ sớm được đọc ra. Ví dụ : một file chữa mã nguồn được lưu , sau đó được đọc bởi trình biên dịch, do đó cache lại (*đệm lại*) dữ liệu của file là một ý tưởng tốt. Việc chỉ đặt dữ liệu vào cache , không viết chúng vào đĩa cùng một lúc sẽ làm cho các chương trình có ghi dữ liệu ra file chạy nhanh hơn. Việc viết dữ liệu vào đĩa có thể được thực hiện ngầm mà không làm chậm các chương trình khác.

Hầu hết các hệ điều hành đều có *buffer caches* (*mặc dù đôi khi chúng có thể được gọi bằng một vài cái tên khác*) , nhưng không phải tất cả trong số chúng đều hoạt động theo nguyên tắc trên. Một vài buffer cache thuộc loại *write-through (dữ liệu được viết vào đĩa cùng thời điểm dữ liệu được ghi vào cache)*. Một buffer cache thuộc loại *write-back* nếu như dữ liệu trong cache sẽ được viết vào đĩa trong tương lai. *Write-back* hiệu quả hơn *Write-through*, tuy nhiên cũng tiềm tàng nguy cơ sinh lỗi. Nếu như máy bị crashs , hoặc khi nguồn điện bị cắt bất chợt, hoặc khi đĩa mềm được tháo ra khỏi ổ đĩa trước khi dữ liệu trong cache được ghi lên đĩa, những dữ liệu mà bạn tưởng rằng đã được viết lên đĩa thường sẽ mất toi. Điều này thậm chí có nghĩa rằng filesystem (*nếu có*) sẽ không hoạt động đúng như bạn mong muốn vì dữ liệu chưa được viết có thể chứa những thông tin bookkeeping quan trọng.

Do những lý do trên, bạn không bao giờ nên tắt nguồn mà không thông qua một thủ tục shutdown , hoặc không nên tháo đĩa mềm ra khỏi ổ một khi chúng chưa được unmount. Lệnh **sync** sẽ đẩy (*flush*) toàn bộ dữ liệu trong buffer cache vào đĩa, lệnh này có thể được sử dụng khi một người nào đó muốn chắc chắn rằng mọi thứ được ghi vào đĩa một cách an toàn. Trong các hệ thống UNIX truyền thống, có một chương trình chạy ngầm là **update** , nó sẽ gọi lệnh **sync** mỗi 30 giây, nên thường thì chúng ta không cần thiết phải gọi **sync**. Linux có một daemon bổ sung , **bdflush** , daemon này gọi **sync** với tần số thường xuyên hơn để tránh hiện tượng đóng băng đột ngột, do đôi khi sync phải ghi một khối lượng dữ liệu khá lớn.

Trong Linux, **bdflush** được coi là một helper của **update**, nó được gọi bởi **update**.  Đôi khi **bdflush** sẽ chết vì một vài lý do, lúc này bạn sẽ phải khởi động lại nó bằng tay (**/sbin/update**).

Buffer cache không cache các file, mà là các blocks - đơn vị lưu trữ nhỏ nhất của một đĩa cứng (*trong Linux, kích thước của một block thường là 1KB*). Bằng cách này, các thư mục, các super-blocks, hoặc các cấu trúc dữ liệu bookkeeping của các filesystem cũng có thể được cache lại.

Tính hiệu quả của việc cache chủ yếu được quyết định bởi kích thước của buffer cache. Một cache bé thường vô dụng.
Giới hạn kích thước của buffer cache phụ thuộc vào lượng dữ liệu được đọc/ghi lên nó, và mức độ thường xuyên mà một dữ liệu giống nhau được truy xuất. Cách duy nhất để đo luờng được kích thước của buffer cache đó là thông qua các thí nghiệm. 

Nếu như trên hệ thống của bạn , buffer cache có kích thước cố định thì việc sở hữu một buffer cache quá lớn không phải là một điều tốt , bởi vì nó sẽ làm cho không gian trống trong RAM càng ít đi, và khiến cho hệ thống phải swap dữ liệu của các chương trình càng nhiều(*như chúng ta đã nói trên thì việc swap cũng chẳng nhanh nhẹn gì*). Để sử dụng bộ nhớ thực (RAM) một cách hiệu quả nhất, Linux tự động sử dụng mọi không gian trống trong RAM làm buffer cache, nhưng nó cũng tự động làm cho buffer cache trở nên nhỏ hơn nếu như các chương trình cần thêm bộ nhớ.

Trong Linux thì bạn hầu hết không cần quan tâm tới việc sử dụng tới buffer cache, nó được hoàn thành hoàn toàn tự động. Trừ khi bạn cần phải flush bằng tay toàn bộ dữ liệu từ cache vào đĩa như đã nói trên.

>Link bài viết sau:  [\[Series\] Linux System Administrator's guide. \[Phần 6\]](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-6)
> Link bài viết trước : [\[Series\] Linux System Administrator's guide. \[Phần 4.2\]](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-4-2)
> Nguồn : [The Linux Documentation Project](http://tldp.org)