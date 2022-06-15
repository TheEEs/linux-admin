# Chương 6. Giám sát hệ thống.

> **"That's Hall Monitor to you!"**


Một trong những trách nhiệm quan trọng nhất của người quản trị hệ thống , đó chính là theo dõi hệ thống của anh/cô ta. Với tư cách một sysadmin, bạn sẽ cần phải biết được những gì đang xảy ra trên hệ thống của mình tại một thời điểm cho trước. Bao nhiêu phần trăm tài nguyên hệ thống đang được sử dụng, lệnh nào đang được chạy , ai đã và đang login ? Chương này sẽ bao quát cho bạn thấy làm thế nào bạn có thể giám sát hệ thống của mình, và trong một vài trường hợp, làm thế nào bạn có thể giải quyết những vấn đề có nguy cơ phát sinh.

Khi một vấn đề về hiệu suất phát sinh, đây thường là 4 nguyên nhân chính mà bạn cần xem xét: 
* **CPU**
* **Memory**
* **Disk I/O**
* **Mạng**

Khả năng xác định được đâu  là nơi gây ra hiện tượng nghẽn cổ chai, sẽ giúp bạn tiết kiệm được rất nhiều thời gian.

## 6.1. Tài nguyên hệ thống.
Việc giám sát hiệu suất hệ thống của bạn là cần thiết. Nếu như tài nguyên hệ thống trở nên khan hiếm, nó có thể sinh ra rất nhiều vấn đề. Tài nguyên hệ thống có thể bị chiếm dụng bởi những người dùng độc lập, hoặc bởi các dịch vụ được host trên hệ thống của bạn (*các email, các trang web...*).  Nếu như bạn có khả năng biết được điều gì đang xảy ra, nó sẽ giúp bạn quyết định xem hệ thống có cần được nâng cấp hay không, hoặc có nên di chuyển các dịch vụ khác sang các máy khác hay không.
### 6.1.1. Lệnh top.
Lệnh phổ biến nhất dùng để giám sát hệ thống, chắc hẳn phải nhắc tới **top**. Lệnh này sẽ hiển thị một danh sách các báo cáo được cập nhật thường xuyên về việc sử dụng tài nguyên hệ thống.
```bash
$ top
top - 09:21:02 up 27 min,  1 user,  load average: 0,32, 0,38, 0,41
Tasks: 205 total,   1 running, 204 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0,9 us,  0,3 sy,  0,0 ni, 98,7 id,  0,2 wa,  0,0 hi,  0,0 si,  0,0 
KiB Mem :  8065332 total,  6145064 free,   517888 used,  1402380 buff/cache
KiB Swap:  8000508 total,  8000508 free,        0 used.  7376284 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND  
 1222 root      20   0  201196  55260  31724 S   4,7  0,7   0:25.53 Xorg     
 3599 trandat   20   0  448956  35352  26344 S   3,3  0,4   0:00.36 gnome-t+ 
 2827 trandat   20   0  973656 200424 130696 S   2,3  2,5   2:59.93 chrome   
 2323 trandat   20   0  865072 162856  97004 S   1,0  2,0   1:21.02 chrome   
 1724 trandat   20   0  113268  11396   9980 S   0,3  0,1   0:00.61 i3       
 2297 trandat   20   0  206992   8744   6024 S   0,3  0,1   0:00.32 at-spi2+ 
 3619 trandat   20   0   43464   4100   3392 R   0,3  0,1   0:00.03 top      
    1 root      20   0  119796   5900   3920 S   0,0  0,1   0:01.25 systemd  
    2 root      20   0       0      0      0 S   0,0  0,0   0:00.00 kthreadd 
    3 root      20   0       0      0      0 S   0,0  0,0   0:00.00 ksoftir+ 
    5 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 kworker+ 
    7 root      20   0       0      0      0 S   0,0  0,0   0:01.86 rcu_sch+ 
    8 root      20   0       0      0      0 S   0,0  0,0   0:00.00 rcu_bh   
    9 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 migrati+ 
   10 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 watchdo+ 
   11 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 watchdo+ 
   12 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 migrati+ 
   13 root      20   0       0      0      0 S   0,0  0,0   0:00.00 ksoftir+ 
   15 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 kworker+ 
   16 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 watchdo+ 
   17 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 migrati+ 
   18 root      20   0       0      0      0 S   0,0  0,0   0:00.00 ksoftir+ 
   20 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 kworker+ 
   21 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 watchdo+ 
   22 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 migrati+ 
   23 root      20   0       0      0      0 S   0,0  0,0   0:00.02 ksoftir+ 
   25 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 kworker+ 
   26 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 watchdo+ 
   27 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 migrati+ 
   28 root      20   0       0      0      0 S   0,0  0,0   0:00.00 ksoftir+ 
   30 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 kworker+ 
   31 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 watchdo+ 
   32 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 migrati+ 
   33 root      20   0       0      0      0 S   0,0  0,0   0:00.00 ksoftir+ 
   35 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 kworker+ 
   36 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 watchdo+ 
   37 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 migrati+ 
   38 root      20   0       0      0      0 S   0,0  0,0   0:00.00 ksoftir+ 
   40 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 kworker+ 
   41 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 watchdo+ 
   42 root      rt   0       0      0      0 S   0,0  0,0   0:00.00 migrati+ 
   43 root      20   0       0      0      0 S   0,0  0,0   0:00.00 ksoftir+ 
   45 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 kworker+ 
   46 root      20   0       0      0      0 S   0,0  0,0   0:00.00 kdevtmp+ 
   47 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 netns    
   48 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 perf     
   49 root      20   0       0      0      0 S   0,0  0,0   0:00.00 khungta+ 
   50 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 writeba+ 
   51 root      25   5       0      0      0 S   0,0  0,0   0:00.00 ksmd     
   52 root      39  19       0      0      0 S   0,0  0,0   0:00.18 khugepa+ 
   53 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 crypto   
   54 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 kintegr+ 
   55 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 bioset   
   56 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 kblockd  
   57 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 ata_sff  
   58 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 md       
   59 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 devfreq+ 
   62 root      20   0       0      0      0 S   0,0  0,0   0:00.01 kworker+ 
   64 root      20   0       0      0      0 S   0,0  0,0   0:00.00 kswapd0  
   65 root       0 -20       0      0      0 S   0,0  0,0   0:00.00 vmstat  
```

Các phần trong báo cáo của **top** , như thời gian hệ thống, thời lượng hệ thống đã chạy, tài nguyên CPU, tài nguyên bộ nhớ vật lý và bộ nhớ swap, số lượng các tiến trình. Phía dưới  là một danh sách các tiến trình được sắp theo lượng tài nguyên CPU mà chúng chiếm dụng.

Bạn có thể chỉnh sửa output của **top** trong khi nó đang chạy. Nếu bạn nhấn phím *i*, **top** sẽ không hiển thị các tiến trình đang nghỉ ngơi nữa. Để thấy chúng , bạn nhấn *i* một lần nữa. Nhấn phím *M* sẽ sắp xếp danh sách theo lượng memory bị chiếm dụng, *S* sẽ sắp xếp theo thời lượng mà các tiến trình đã chạy, *P* sẽ sắp xếp theo lượng tài nguyên CPU mà các tiến trình chiếm dụng.

Ngoài các tùy chọn trên, bạn cũng có thể sửa đổi các tiến trình bằng **top**. Bạn có thể nhấn *u* để theo dõi các tiến trình thuộc sở hữu của một người dùng cụ thể, nhấn *k* để giết, *r* để renice một tiến trình cho trước.

Để biết những thông tin sâu và chi tiết hơn về các tiến trình, bạn có thể nhìn vào trong */proc*.  Bạn sẽ tìm thấy trong filesystem này một loạt các thư mục con có tên là những con số. Những thư mục này được liên kết với những tiến trình đang chạy, và tên thư mục chính là process id của tiến trình tương ứng. Trong mỗi một thư mục, bạn có thể tìm thấy một chuỗi các file chứa các thông tin về tiến trình mà bạn cần tìm.

> **BẠN PHẢI CỰC KỲ THẬN TRỌNG ĐỂ KHÔNG LÀM THAY ĐỔI NHỮNG FILE NÀY, NẾU KHÔNG SẼ GÂY RA NHỮNG VẤN ĐỀ KHÁ NGUY HIỂM CHO HỆ THỐNG**

### 6.1.2. Lệnh iostat.
Lệnh này hiển thị tải trung bình của CPU và thông tin về lưu lượng , tình trạng nhập/xuất của đĩa. Đây là một lệnh tuyệt vời để giám sát sự sử dụng đĩa trên hệ thống của bạn.
```bash
Linux 4.4.0-21-generic (trandat-GL552VX) 	12/13/2016 	_x86_64_	(8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           4,18    0,10    0,55    0,57    0,00   94,60

Device:            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
sda              19,77       332,02       118,81    1085957     388593
dm-0              3,80        66,59         0,25     217797        812
```
### 6.1.3. Lệnh ps.
Lệnh **ps** sẽ cung cấp cho bạn một danh sách các tiến trình đang chạy. Có một danh sách rộng các tùy chọn cho bạn lựa chọn.

**ps** thường được dùng với mục đích liệt kê tất cả các tiến trình đang chạy, để làm điều này, bạn gõ lệnh **ps -ef**.
```bash
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 08:53 ?        00:00:01 /sbin/init splash
root         2     0  0 08:53 ?        00:00:00 [kthreadd]
root         3     2  0 08:53 ?        00:00:00 [ksoftirqd/0]
root         5     2  0 08:53 ?        00:00:00 [kworker/0:0H]
root         7     2  0 08:53 ?        00:00:04 [rcu_sched]
root         8     2  0 08:53 ?        00:00:00 [rcu_bh]
root         9     2  0 08:53 ?        00:00:00 [migration/0]
root        10     2  0 08:53 ?        00:00:00 [watchdog/0]
root        11     2  0 08:53 ?        00:00:00 [watchdog/1]
root        12     2  0 08:53 ?        00:00:00 [migration/1]
root        13     2  0 08:53 ?        00:00:00 [ksoftirqd/1]
root        15     2  0 08:53 ?        00:00:00 [kworker/1:0H]
root        16     2  0 08:53 ?        00:00:00 [watchdog/2]
root        17     2  0 08:53 ?        00:00:00 [migration/2]
root        18     2  0 08:53 ?        00:00:00 [ksoftirqd/2]
root        20     2  0 08:53 ?        00:00:00 [kworker/2:0H]
root        21     2  0 08:53 ?        00:00:00 [watchdog/3]
root        22     2  0 08:53 ?        00:00:00 [migration/3]
root        23     2  0 08:53 ?        00:00:00 [ksoftirqd/3]
root        25     2  0 08:53 ?        00:00:00 [kworker/3:0H]
root        26     2  0 08:53 ?        00:00:00 [watchdog/4]
root        27     2  0 08:53 ?        00:00:00 [migration/4]
root        28     2  0 08:53 ?        00:00:00 [ksoftirqd/4]
root        30     2  0 08:53 ?        00:00:00 [kworker/4:0H]
root        31     2  0 08:53 ?        00:00:00 [watchdog/5]
root        32     2  0 08:53 ?        00:00:00 [migration/5]
root        33     2  0 08:53 ?        00:00:00 [ksoftirqd/5]
root        35     2  0 08:53 ?        00:00:00 [kworker/5:0H]
root        36     2  0 08:53 ?        00:00:00 [watchdog/6]
root        37     2  0 08:53 ?        00:00:00 [migration/6]
root        38     2  0 08:53 ?        00:00:00 [ksoftirqd/6]
root        40     2  0 08:53 ?        00:00:00 [kworker/6:0H]
root        41     2  0 08:53 ?        00:00:00 [watchdog/7]
root        42     2  0 08:53 ?        00:00:00 [migration/7]
root        43     2  0 08:53 ?        00:00:00 [ksoftirqd/7]
root        45     2  0 08:53 ?        00:00:00 [kworker/7:0H]
root        46     2  0 08:53 ?        00:00:00 [kdevtmpfs]
root        47     2  0 08:53 ?        00:00:00 [netns]
root        48     2  0 08:53 ?        00:00:00 [perf]
root        49     2  0 08:53 ?        00:00:00 [khungtaskd]
root        50     2  0 08:53 ?        00:00:00 [writeback]
root        51     2  0 08:53 ?        00:00:00 [ksmd]
root        52     2  0 08:53 ?        00:00:00 [khugepaged]
root        53     2  0 08:53 ?        00:00:00 [crypto]
root        54     2  0 08:53 ?        00:00:00 [kintegrityd]
root        55     2  0 08:53 ?        00:00:00 [bioset]
root        56     2  0 08:53 ?        00:00:00 [kblockd]
root        57     2  0 08:53 ?        00:00:00 [ata_sff]
root        58     2  0 08:53 ?        00:00:00 [md]
root        59     2  0 08:53 ?        00:00:00 [devfreq_wq]
root        62     2  0 08:53 ?        00:00:00 [kworker/1:1]
root        64     2  0 08:53 ?        00:00:00 [kswapd0]
root        65     2  0 08:53 ?        00:00:00 [vmstat]
root        66     2  0 08:53 ?        00:00:00 [fsnotify_mark]
root        67     2  0 08:53 ?        00:00:00 [ecryptfs-kthrea]
root        82     2  0 08:53 ?        00:00:00 [kthrotld]
root        83     2  0 08:53 ?        00:00:00 [acpi_thermal_pm]
root        84     2  0 08:53 ?        00:00:00 [bioset]
root        85     2  0 08:53 ?        00:00:00 [bioset]
root        86     2  0 08:53 ?        00:00:00 [bioset]
root        87     2  0 08:53 ?        00:00:00 [bioset]
root        88     2  0 08:53 ?        00:00:00 [bioset]
root        89     2  0 08:53 ?        00:00:00 [bioset]
root        90     2  0 08:53 ?        00:00:00 [bioset]
root        91     2  0 08:53 ?        00:00:00 [bioset]
root        92     2  0 08:53 ?        00:00:00 [bioset]
root        93     2  0 08:53 ?        00:00:00 [bioset]
root        94     2  0 08:53 ?        00:00:00 [bioset]
root        95     2  0 08:53 ?        00:00:00 [bioset]
root        96     2  0 08:53 ?        00:00:00 [bioset]
root        97     2  0 08:53 ?        00:00:00 [bioset]
root        98     2  0 08:53 ?        00:00:00 [bioset]
root        99     2  0 08:53 ?        00:00:00 [bioset]
root       100     2  0 08:53 ?        00:00:00 [bioset]
root       101     2  0 08:53 ?        00:00:00 [bioset]
root       102     2  0 08:53 ?        00:00:00 [bioset]
root       103     2  0 08:53 ?        00:00:00 [bioset]
root       105     2  0 08:53 ?        00:00:00 [bioset]
root       106     2  0 08:53 ?        00:00:00 [bioset]
root       107     2  0 08:53 ?        00:00:00 [bioset]
root       108     2  0 08:53 ?        00:00:00 [bioset]
root       113     2  0 08:53 ?        00:00:00 [ipv6_addrconf]
root       126     2  0 08:53 ?        00:00:00 [deferwq]
root       127     2  0 08:53 ?        00:00:00 [charger_manager]
root       180     2  0 08:53 ?        00:00:00 [scsi_eh_0]
root       181     2  0 08:53 ?        00:00:00 [scsi_tmf_0]
root       182     2  0 08:53 ?        00:00:00 [scsi_eh_1]
root       183     2  0 08:53 ?        00:00:00 [scsi_tmf_1]
root       184     2  0 08:53 ?        00:00:00 [scsi_eh_2]
root       185     2  0 08:53 ?        00:00:00 [scsi_tmf_2]
root       189     2  0 08:53 ?        00:00:00 [rtsx_pci_sdmmc_]
root       191     2  0 08:53 ?        00:00:00 [kworker/5:2]
root       193     2  0 08:53 ?        00:00:00 [bioset]
root       195     2  0 08:53 ?        00:00:00 [bioset]
root       201     2  0 08:53 ?        00:00:00 [kworker/7:1]
root       289     2  0 08:53 ?        00:00:00 [bioset]
root       313     2  0 08:53 ?        00:00:00 [kworker/0:1H]
root       315     2  0 08:53 ?        00:00:00 [jbd2/sda2-8]
root       316     2  0 08:53 ?        00:00:00 [ext4-rsv-conver]
root       331     2  0 08:53 ?        00:00:00 [jbd2/sda5-8]
root       332     2  0 08:53 ?        00:00:00 [ext4-rsv-conver]
root       382     1  0 08:53 ?        00:00:00 /lib/systemd/systemd-journald
root       388     2  0 08:53 ?        00:00:00 [kauditd]
root       403     1  0 08:53 ?        00:00:00 /sbin/lvmetad -f
root       407     1  0 08:53 ?        00:00:00 /lib/systemd/systemd-udevd
root       423     2  0 08:53 ?        00:00:00 [kworker/7:1H]
root       424     2  0 08:53 ?        00:00:00 [kworker/4:1H]
root       442     2  0 08:53 ?        00:00:00 [kworker/4:2]
root       501     2  0 08:53 ?        00:00:00 [kworker/5:1H]
root       508     2  0 08:53 ?        00:00:00 [irq/128-mei_me]
root       515     2  0 08:53 ?        00:00:00 [kmemstick]
root       521     2  0 08:53 ?        00:00:00 [cfg80211]
root       559     2  0 08:53 ?        00:00:00 [kworker/u17:0]
root       562     2  0 08:53 ?        00:00:00 [kworker/u17:1]
root       588     2  0 08:53 ?        00:00:06 [irq/129-iwlwifi]
root       630     2  0 08:53 ?        00:00:00 [led_workqueue]
root       631     2  0 08:53 ?        00:00:00 [kworker/4:3]
root       639     2  0 08:53 ?        00:00:00 [kvm-irqfd-clean]
root       640     2  0 08:53 ?        00:00:00 [irq/95-ELAN1000]
root       645     2  0 08:53 ?        00:00:00 [kworker/6:1H]
root       696     2  0 08:53 ?        00:00:00 [kworker/2:1H]
root       697     2  0 08:53 ?        00:00:00 [kworker/1:1H]
root       701     2  0 08:53 ?        00:00:00 [UVM global queu]
root       702     2  0 08:53 ?        00:00:00 [UVM Tools Event]
root       714     2  0 08:53 ?        00:00:00 [kdmflush]
root       716     2  0 08:53 ?        00:00:00 [bioset]
root       720     2  0 08:53 ?        00:00:00 [jbd2/sda3-8]
root       721     2  0 08:53 ?        00:00:00 [ext4-rsv-conver]
root       753     2  0 08:53 ?        00:00:00 [jbd2/sda4-8]
root       754     2  0 08:53 ?        00:00:00 [ext4-rsv-conver]
root       773     2  0 08:53 ?        00:00:00 [jbd2/dm-0-8]
root       774     2  0 08:53 ?        00:00:00 [ext4-rsv-conver]
root       954     1  0 08:53 ?        00:00:00 /usr/sbin/thermald --no-daemo
avahi      957     1  0 08:53 ?        00:00:00 avahi-daemon: running [tranda
root       964     1  0 08:53 ?        00:00:00 /usr/sbin/cron -f
syslog     970     1  0 08:53 ?        00:00:00 /usr/sbin/rsyslogd -n
message+   982     1  0 08:53 ?        00:00:00 /usr/bin/dbus-daemon --system
avahi      988   957  0 08:53 ?        00:00:00 avahi-daemon: chroot helper
root       995     1  0 08:53 ?        00:00:01 /usr/sbin/acpid
root       999     1  0 08:53 ?        00:00:00 /usr/sbin/cups-browsed
root      1002     1  0 08:53 ?        00:00:00 /lib/systemd/systemd-logind
root      1007     1  0 08:53 ?        00:00:00 /usr/sbin/ModemManager
root      1011     1  0 08:53 ?        00:00:00 /sbin/cgmanager -m name=syste
root      1016     1  0 08:53 ?        00:00:00 /usr/lib/accountsservice/acco
root      1023     1  0 08:53 ?        00:00:00 /usr/sbin/NetworkManager --no
root      1077     1  0 08:53 ?        00:00:00 /usr/sbin/irqbalance --pid=/v
root      1130     1  0 08:53 ?        00:00:00 /usr/lib/policykit-1/polkitd 
root      1188     1  0 08:53 ?        00:00:00 /usr/sbin/mdm --nodaemon
root      1194     1  0 08:53 tty1     00:00:00 /sbin/agetty --noclear tty1 l
root      1198     1  0 08:53 ?        00:00:00 /sbin/wpa_supplicant -u -s -O
root      1217  1188  0 08:53 ?        00:00:00 /usr/sbin/mdm --nodaemon
root      1222  1217  1 08:53 tty8     00:01:23 /usr/lib/xorg/Xorg :0 -audit 
root      1252     1  0 08:53 ?        00:00:00 /usr/lib/bluetooth/bluetoothd
nvidia-+  1267     1  0 08:53 ?        00:00:00 /usr/bin/nvidia-persistenced 
root      1268  1023  0 08:53 ?        00:00:00 /sbin/dhclient -d -q -sf /usr
nobody    1280  1023  0 08:53 ?        00:00:00 /usr/sbin/dnsmasq --no-resolv
ntp       1548     1  0 08:53 ?        00:00:00 /usr/sbin/ntpd -p /var/run/nt
trandat   1634     1  0 08:53 ?        00:00:00 /lib/systemd/systemd --user
trandat   1636  1634  0 08:53 ?        00:00:00 (sd-pam)
root      1650     1  0 08:53 ?        00:00:00 /usr/sbin/console-kit-daemon 
trandat   1724  1217  0 08:53 ?        00:00:01 i3
trandat   1797  1724  0 08:53 ?        00:00:00 /usr/bin/ssh-agent /usr/bin/d
trandat   1800     1  0 08:53 ?        00:00:00 /usr/bin/dbus-launch --exit-w
trandat   1801     1  0 08:53 ?        00:00:00 /usr/bin/dbus-daemon --fork -
trandat   1811     1  0 08:53 ?        00:00:00 /bin/sh -c i3bar --bar_id=bar
trandat   1812  1811  0 08:53 ?        00:00:00 i3bar --bar_id=bar-0 --socket
root      2182     2  0 08:58 ?        00:00:00 [kworker/3:1H]
trandat   2276     1  0 09:00 ?        00:00:00 /usr/lib/at-spi2-core/at-spi-
trandat   2280     1  0 09:00 ?        00:00:00 /usr/lib/gvfs/gvfsd
trandat   2285     1  0 09:00 ?        00:00:00 /usr/lib/gvfs/gvfsd-fuse /run
trandat   2288  2276  0 09:00 ?        00:00:00 /usr/bin/dbus-daemon --config
trandat   2297     1  0 09:00 ?        00:00:01 /usr/lib/at-spi2-core/at-spi2
trandat   2323     1  6 09:00 ?        00:04:25 /opt/google/chrome/chrome
trandat   2328  2323  0 09:00 ?        00:00:00 cat
trandat   2329  2323  0 09:00 ?        00:00:00 [cat] <defunct>
trandat   2332  2323  0 09:00 ?        00:00:00 /opt/google/chrome/chrome --t
trandat   2333  2332  0 09:00 ?        00:00:00 /opt/google/chrome/nacl_helpe
trandat   2336  2332  0 09:00 ?        00:00:00 /opt/google/chrome/chrome --t
trandat   2418  2323  5 09:00 ?        00:03:53 /opt/google/chrome/chrome --t
trandat   2421  2418  0 09:00 ?        00:00:00 /opt/google/chrome/chrome --t
trandat   2428     1  0 09:00 ?        00:00:00 /usr/lib/x86_64-linux-gnu/gco
trandat   2501  2336  0 09:00 ?        00:00:00 /opt/google/chrome/chrome --t
root      2719     2  0 09:01 ?        00:00:00 [kworker/6:0]
trandat   2776  2336  0 09:01 ?        00:00:05 /opt/google/chrome/chrome --t
trandat   2786  2336  0 09:01 ?        00:00:23 /opt/google/chrome/chrome --t
trandat   2810  2336  0 09:01 ?        00:00:05 /opt/google/chrome/chrome --t
trandat   2827  2336 13 09:01 ?        00:08:32 /opt/google/chrome/chrome --t
root      3095     2  0 09:02 ?        00:00:00 [kworker/2:0]
root      3289     2  0 09:08 ?        00:00:00 [kworker/0:0]
trandat   3304  2336  0 09:08 ?        00:00:06 /opt/google/chrome/chrome --t
root      3335     2  0 09:08 ?        00:00:00 [kworker/5:0]
root      3574     2  0 09:19 ?        00:00:01 [kworker/3:0]
root      4018     2  0 09:46 ?        00:00:00 [kworker/u16:0]
root      5109     2  0 09:49 ?        00:00:00 [kworker/0:1]
root      5187     2  0 09:51 ?        00:00:00 [kworker/3:2]
root      5188     2  0 09:51 ?        00:00:00 [kworker/1:2]
trandat   5212     1  2 09:51 ?        00:00:25 /usr/bin/pulseaudio --start -
rtkit     5213     1  0 09:51 ?        00:00:00 /usr/lib/rtkit/rtkit-daemon
trandat   5229     1  0 09:51 ?        00:00:00 /usr/bin/gnome-keyring-daemon
trandat   5286  2336 24 09:51 ?        00:03:32 /opt/google/chrome/chrome --t
root      5318     1  0 09:51 ?        00:00:00 /usr/lib/upower/upowerd
root      5404     2  0 09:52 ?        00:00:00 [kworker/7:0]
root      5466     2  0 09:55 ?        00:00:00 [kworker/u16:1]
trandat   5502     1  0 09:56 ?        00:00:00 kmix
trandat   5507     1  0 09:56 ?        00:00:00 kdeinit4: kdeinit4 Running...
trandat   5510  5507  0 09:56 ?        00:00:00 kdeinit4: klauncher [kdeinit]
trandat   5512     1  0 09:56 ?        00:00:00 kdeinit4: kded4 [kdeinit]
trandat   5516     1  0 09:56 ?        00:00:00 /usr/bin/kglobalaccel
root      5518     1  0 09:56 ?        00:00:00 /usr/lib/udisks2/udisksd --no
trandat   5528     1  0 09:56 ?        00:00:00 /usr/bin/knotify4
root      5684     2  0 09:58 ?        00:00:00 [kworker/2:2]
root      5691     2  0 09:58 ?        00:00:00 [kworker/6:2]
root      5744     2  0 10:01 ?        00:00:00 [kworker/u16:2]
trandat   5810  2336  1 10:05 ?        00:00:00 /opt/google/chrome/chrome --t
root      5860     2  0 10:05 ?        00:00:00 [kworker/6:1]
trandat   5891     1  4 10:05 ?        00:00:00 /usr/lib/gnome-terminal/gnome
trandat   5895  5891  0 10:05 pts/2    00:00:00 bash
trandat   5911  5895  0 10:05 pts/2    00:00:00 ps -fe
```

Cột đầu tiên cho thấy ai đang sở hữu tiến trình. Cột thứ 2 là PID của tiến trình đó. Cột thứ 3 là parent PID của tiến trình đó (*parent process : là tiến trình mà khởi động , hoặc tạo ra một tiến trình khác*). Cột thứ tư là số phần trăm tài nguyên CPU mà tiến trình đó chiếm dụng. Cột thứ 5 là thời gian mà tiến trình đó được khởi chạy. Cột thứ 6 chính là tty có liên quan tới tiến trình đó (*nếu có*). Cột thứ 7 là tổng thời lượng mà CPU dùng để chạy chúng. Cột thứ 8 là lệnh mà tiến trình đó chạy.

Với những thông tin trên bạn có thể thấy chính xác những gì đang chạy trên hệ thống, từ đó đưa ra các phương án thích hợp.

### 6.1.4. Lệnh vmstat.
Lệnh này chung cấp một báo cáo với những số liệu thống kê về các tiến trình hệ thống, bộ nhớ, swap , I/O , CPU. Các số liệu thống kê này được tạo ra sử dụng những dữ liệu từ lần cuối cùng lệnh này được chạy. 
```bash
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 3  0      0 5213272 133028 1719736    0    0    24    16   90  425  5  1 94  0  0
```

Những thông tin dưới đây được tìm trong man page của **vmstat** :
* procs:

 > r : Số lượng các process đang chờ tới luợt chạy.
 > b: Số luợng các process đang nằm trong chế độ *uninterrputable sleep*.
 > w: Số lượng các process được swap vào swap space những vẫn có khả năng chạy.

* memory:

> swpd: Tổng lượng bộ nhớ ảo được sử dụng (kB).
> free: Tổng lượng bộ nhớ đang rảnh rỗi (kB).
> buff: Tổng lượng bộ nhớ được sử dụng làm những bộ đệm (kB).

* swap:

> si: Tổng luợng bộ nhớ được swap từ đĩa vào RAM (kB/s).
> so: Tổng lượng bộ nhớ được swap từ RAM vào đĩa (kB/s).

* IO:

> bi: Các block được gửi tới một block device (blocks/s).
> bo: Các block được nhận từ một block device(block/s).

* system:

> in: Số lượng các ngắt trong một giây.
> cs: Số lượng các tiến trình được scheduler active trong một giây.

* CPU: Phần trăm thời lượng sử dụng CPU

> us: thời lượng các tiến trình người dùng sử dụng CPU.
> sy: thời lượng mà kernel và các chương trình hệ thống sử dụng CPU.
> id: thởi gian rảnh (idle).

### 6.1.5. Lệnh lsof.
Lệnh này sẽ in ra một danh sách mọi file đang được sử dụng. Do trong Linux mọi thứ đều là file, nên danh sách này có thể sẽ rất dài. Tuy nhiên, lệnh này có thể hữu dụng trong việc phân tích chẩn đoán các vấn đề. Một ví dụ đó là nếu bạn muốn unmount một filesystem, nhưng nó lại đang được sử dụng. Bạn có thể sử dụng lệnh này cùng với **grep** để tìm ra ai đang sử dụng filesystem đó. Hoặc giả sử rằng bạn muốn thấy tất cả các file được sử dụng bởi một process cụ thể. Để làm điều này bạn cần dùng lệnh **lsof -p \<processid>**.

## 6.2. Filesystem usage.
### 6.2.1. Lệnh df.
Lệnh này là công cụ đơn giản nhất để xem lượng sử dụng đĩa. Chỉ cần gõ **df** và bạn sẽ thấy được bao nhiêu không gian đã đĩa đã bị chiếm dụng trên các filesystem mà bạn đã mount (*với kích thước blocks là 1Kilobyte*).
```bash
Filesystem              1K-blocks    Used Available Use% Mounted on
udev                      4012384       0   4012384   0% /dev
tmpfs                      806536    9716    796820   2% /run
/dev/sda2                24476036  763392  22446264   4% /
/dev/sda5                 9712160 5219324   3976436  57% /usr
tmpfs                     4032664   65224   3967440   2% /dev/shm
tmpfs                        5120       4      5116   1% /run/lock
tmpfs                     4032664       0   4032664   0% /sys/fs/cgroup
/dev/sda1                   35272       1     35272   1% /boot/efi
/dev/sda3                29398100 1477512  26404200   6% /var
/dev/sda4                 9712160 2717580   6478180  30% /home
/dev/mapper/develop-Cpp   5029504  686860   4080500  15% /home/trandat/develop
cgmfs                         100       0       100   0% /run/cgmanager/fs
tmpfs                      806536       8    806528   1% /run/user/1000
```
Bạn có thể sủ dụng tùy chọn *-h* để yêu cầu **d** cho ra output với định dạng dễ đọc hơn. Output này có thể dưới dạng Kilobytes, Megabytes, Gigabytes phụ thuộc vào kích thước của filesystem. Bạn cũng có thể sử dụng tùy chọn **B** để chỉ rõ kích thước của block mà bạn muốn df đọc.  Ví dụ:
```
$ df -h
Filesystem               Size  Used Avail Use% Mounted on
udev                     3,9G     0  3,9G   0% /dev
tmpfs                    788M  9,5M  779M   2% /run
/dev/sda2                 24G  746M   22G   4% /
/dev/sda5                9,3G  5,0G  3,8G  57% /usr
tmpfs                    3,9G   67M  3,8G   2% /dev/shm
tmpfs                    5,0M  4,0K  5,0M   1% /run/lock
tmpfs                    3,9G     0  3,9G   0% /sys/fs/cgroup
/dev/sda1                 35M   512   35M   1% /boot/efi
/dev/sda3                 29G  1,5G   26G   6% /var
/dev/sda4                9,3G  2,6G  6,2G  30% /home
/dev/mapper/develop-Cpp  4,8G  671M  3,9G  15% /home/trandat/develop
cgmfs                    100K     0  100K   0% /run/cgmanager/fs
tmpfs                    788M  8,0K  788M   1% /run/user/1000
```
Bạn cũng có thể sử dụng **-i** để xem số lượng các inodes đã được sử dụng hoặc đang dư thừa. Ví dụ :
```bash
$ df -i
Filesystem               Inodes  IUsed   IFree IUse% Mounted on
udev                    1003096    614 1002482    1% /dev
tmpfs                   1008166    914 1007252    1% /run
/dev/sda2               1564672  12767 1551905    1% /
/dev/sda5                625856 233140  392716   38% /usr
tmpfs                   1008166     95 1008071    1% /dev/shm
tmpfs                   1008166      5 1008161    1% /run/lock
tmpfs                   1008166     18 1008148    1% /sys/fs/cgroup
/dev/sda1                     0      0       0     - /boot/efi
/dev/sda3               1875968  37498 1838470    2% /var
/dev/sda4                625856  13552  612304    3% /home
/dev/mapper/develop-Cpp  327680   5392  322288    2% /home/trandat/develop
cgmfs                   1008166     14 1008152    1% /run/cgmanager/fs
tmpfs                   1008166     18 1008148    1% /run/user/1000
```

### 6.2.2. Lệnh du.
Bạn sử dụng lệnh này để xem một thư mục hoặc một file chiếm dụng bao nhiêu không gian đĩa. Nếu bạn không chỉ rõ một tệp tin, **du** sẽ chạy theo kiểu đệ quy, nó in ra không gian chiếm dụng của tất cả các thư mục con nằm trong thư mục đó. Ví dụ:
```bash
$ du -h myfile
4,0K	myfile
```

```bash
$ du -h /usr/local
4,0K	/usr/local/src
4,0K	/usr/local/sbin
4,0K	/usr/local/etc
4,0K	/usr/local/lib/python2.7/dist-packages
4,0K	/usr/local/lib/python2.7/site-packages
12K	/usr/local/lib/python2.7
4,0K	/usr/local/lib/python3.5/dist-packages
8,0K	/usr/local/lib/python3.5
24K	/usr/local/lib
4,0K	/usr/local/games
124K	/usr/local/bin
4,0K	/usr/local/share/man
4,0K	/usr/local/share/emacs/24.5/site-lisp
8,0K	/usr/local/share/emacs/24.5
4,0K	/usr/local/share/emacs/site-lisp
16K	/usr/local/share/emacs
4,0K	/usr/local/share/fonts
4,0K	/usr/local/share/xml/misc
4,0K	/usr/local/share/xml/schema
4,0K	/usr/local/share/xml/declaration
4,0K	/usr/local/share/xml/entities
20K	/usr/local/share/xml
4,0K	/usr/local/share/sgml/stylesheet
4,0K	/usr/local/share/sgml/misc
4,0K	/usr/local/share/sgml/dtd
4,0K	/usr/local/share/sgml/declaration
4,0K	/usr/local/share/sgml/entities
24K	/usr/local/share/sgml
4,0K	/usr/local/share/ca-certificates
76K	/usr/local/share
4,0K	/usr/local/include
248K	/usr/local
```

Nếu bạn chỉ muốn xem không gian chiếm dụng của một thư mục duy nhất, sử dụng tùy chọn **-s**:
```bash
$ du -h -s /usr/local
248K	/usr/local
```

## 6.3. Giám sát người dùng.
Chắc hẳn rất nhiều lần bạn muốn được biết mọi người đang làm gì trên hệ thống của bạn. Là khi bạn nhận ra rằng có sự chiếm dụng RAM quá nhiều, hoặc tải của CPU tăng đột ngột. Bạn sẽ muốn thấy ai đang log-in vào hệ thống, họ đang làm cái gì, và họ đang sử dụng kiểu tài nguyên nào của hệ thống.

### 6.3.1. Lệnh who.
Cách dễ nhất để thấy ai đang ở trên hệ thống, đó là sử dụng lệnh *who* hoặc *w*. Lệnh này sẽ in ra tất cả những người dùng đang logged-in vào hệ thống, và port hoặc terminal mà họ log vào. Ví dụ:
```bash
$ who
trandat  tty8         2016-12-13 08:53 (:0)
```

### 6.3.2. Lại là lệnh ps.
Trong section trước, chúng ta đã biết rằng người dùng có tên *trandat* đã loggin vào hệ thống. Nhưng nếu chúng ta muốn thấy những thứ người đó đang làm , chúng ta sẽ làm gì? Lúc này lệnh **ps** phát huy tác dụng. Bạn gõ **ps -u trandat**, output sẽ như sau:
```bash
$ ps -u trandat
11833 pts/2    00:00:00 bash
12603 pts/3    00:00:00 bash
12662 pts/2    00:00:00 ps
```
### 6.3.3. Lệnh w.
Lệnh này dễ hơn , tiện hơn việc sử dụng đồng thời **ps -u** và **who**. **w** sẽ in ra không chỉ những người đang log vào hệ thống, mà còn in ra câu lệnh mà họ đang sử dụng. Ví dụ :
```bash
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
trandat  tty8     :0               08:53    4:27m  5:52   6.69s i3
```
Từ đây ta nhìn thấy đang có một session của i3wm đang chạy.

> Link bài viết sau : [\[Series\] Linux System Administrator's guide. \[Phần 7\]](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-7).
> Nguồn [The Linux Documentation Project](http://tldp.org).
> Link Bài viết trước [\[Series\] Linux  System Administrator'guide. \[Phần 5\]](https://kipalog.com/posts/Series--Linux-System-Administrator-s-guide---Phan-5).