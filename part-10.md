# Chương 10. Thời gian.

> **"Thời gian đơn thuần là một sự ảo tưởng"**

Chương này giải thích làm cách nào mà một hệ thống Linux có thể quản lý thời gian, và những thứ bạn cần làm để tránh gặp phải vấn đề. Thường thì bạn sẽ không cần làm gì với thời gian của hệ thống, nhưng sẽ tốt nếu như bạn hiểu được nó.

## 10.1. Khái niệm về thời gian cục bộ.
Sự đo lường và ước lượng về thời gian chủ yếu dựa trên các hiện tượng tự nhiên, chẳng hặn như sự luôn phiên sáng-tối được gây ra bởi sự quay quanh trục của các hành tinh. Thời gian của một ngày là một hằng số T = tổng thời gian của buổi sáng và buổi tối cộng lai, tuy nhiên độ dài của sáng và tối sẽ khác nhau tùy vào từng thời điểm. Cũng có một vài hằng số thời gian nữa, có thể kể đến như buổi trưa, buổi đêm.

Buổi trưa là thời điểm mà mặt trời ở vị trí cao nhất. Tuy nhiên do trái đất hình cầu, buổi trưa sẽ xảy ra ở những thời điểm khác nhau ở mỗi nơi khác nhau. Điều này dẫn tới một khái niệm gọi là "thời gian cục bộ - *local  time*. Con người đo lường thời gian bằng các đơn vị khác nhau, hầu hết các khái niệm đó được gắn với các hiện tượng tự nhiên như **buổi trưa** , **giờ Tý**(*thời điểm chuột hoạt động mạnh nhất*) ..v...v... Miễn là bạn ở trong cùng một nơi, bạn có thể sinh sống và đo lường cùng thời điểm với người khác thông qua một hệ đơn vị và một mốc thời gian nhất định.

Nhưng rồi cũng sẽ đến một ngày mối quan hệ của bạn đi xa hơn, vượt ra khỏi nơi mà bạn sống. Điều này nảy sinh ra một nhu cầu về một hệ đếm thời gian chung, để mọi người có thể đồng bộ hóa sự đo lường của họ mà không quan tâm về vấn đề địa điểm. Ngày nay hầu hết mọi người sử dụng một tiêu chuẩn toàn cầu được định ra dành cho việc đo lường thời gian. Tiêu chuẩn này được gọi là Thời gian phổ quát - **UTC** hoặc **UT** - *Universal Time* . Trước đây người ta gọi nó là  Greenwich Mean Time hoặc GMT. Khi những người ở những nơi khác nhau thực hiện các hoạt động giao tiếp có liên quan đến thời gian, họ có thể sử dụng UTC để tránh gây ra những sự nhầm lẫn.

Mỗi một *thời gian cục bộ*  được gọi là một khu vực thời gian, hay *time zone*. 

Các *time zones*  được đặt tên thông qua vị trí của chúng hoặc bởi sự khác nhau giữa *local time* của nơi đó với UTC. Tại Mỹ và một vài quốc gia khác, những *local times* đều có tên của chúng và 3 chữ cái viết tắt. Những chữ cái viết tắt có thể không duy nhất , và không nên được sử dụng trừ khi quốc gia ở time zone đó cũng có cùng tên. Sẽ tốt hơn nếu như bạn nói rằng *"Tôi đang ở time zone Hanoi"* thay vì *"Tôi đang ở timezone Đông Nam Á"* , bởi vì không phải nơi nào ở Đông Nam Á cũng có cùng múi giờ hoặc cách đo lường giống nhau.

Linux có một gói gồm tất cả những time zone được biết đến trên thế giới, và có thể dễ dàng được cập nhật nếu cần. Tất cả những người quản trị hệ thống cần làm là lựa chọn time zone thích hợp. Mỗi người dùng cũng có thể thiết đặt timezone của riêng họ, điều này quan trọng do nhiều người trên nhiều nơi khác nhau có thể cùng làm việc trên một máy thông qua Internet.

## 10.2. Đồng hồ phần cứng và đồng hồ phần mềm.
Trên một máy tính cá nhân có một pin nhỏ. Pin này đảm bảo rằng đồng hồ sẽ hoạt động ngay cả khi các bộ phận khác của máy tính đã bị ngắt điện. Đồng hồ phần cứng có thể được thiết đặt trong BIOS hoặc bởi bất kỳ một hệ điều hành nào đang chạy.

Linux kernel theo dõi thời gian một cách độc lập với đồng hồ phần cứng. Trong quá trình khởi động , Linux thiết đặt thời gian của nó giống với thời gian của đồng hồ phần cứng. Sau đó, 2 đồng hồ này chạy riêng biệt. Linux duy trì đồng hồ của nó , do việc liên tục nhìn vào hardware clock rất chậm và phức tạp.

Đồng hồ của hạt nhân Linux luôn trả về thời gian UTC. Mỗi tiến trình sẽ tự mình giải quyết quy ước về timezone (*sử dụng các công cụ chuẩn nằm trong gói timezone*).

Đồng hồ phần cứng có thể được thiết đặt theo UTC hoặc local time. Nhưng thường thì để nó ở mode UTC sẽ tốt hơn. Không may mắn lắm, một vài hệ điều hành máy tính, bao gồm MS-DOS , Windows, và OS/2 mặc định rằng hardware clock hiển thị local time.

## 10.3. Hiện thị và thiết đặt thời gian.

Trong Linux, thời gian cục bộ của hệ thống được quyết định bởi một symbolic link là */etc/localtime*. Link này trỏ tới một timezone data file, file này chứa các mô tả về các timezone. Các timezone data file được lưu ở */usr/lib/zoneinfo* hoặc */usr/share/zoneinfo* , tùy thuộc vào bản phân phối mà bạn đang dùng.

Ví dụ, máy của tôi sử dụng Arch Linux và được đặt ở Việt Nam, thì */etc/localtime* của tôi sẽ trỏ tới */usr/share/zoneinfo/Asia/Ho_Chi_Minh*.

Vậy điều gì xảy ra khi hệ thống của bạn có một người dùng sống ở nơi khác  ? Một người dùng có thể thay đổi timezone của anh ấy bằng cách thiết đặt biến môi trường **TZ**. Nếu biến đó không được thiết lập, timezone của hệ thống sẽ được sử dụng. Cú pháp của biến TZ được mô tả trong *tzset* manual.

Lệnh **date** cho ta thấy thời gian hiện tại bao gồm ngày và giờ. Ví dụ :
```bash
$ date
Thứ ba, 21 Tháng hai năm 2017 19:07:36 DST
```
**date** cũng có thể hiện thị UTC :
```bash
date -u 

```
Bạn cũng có thể sử dụng lệnh này để thiết đặt software clock của hạt nhân Linux : 
```
$ date 07142157
```

Hãy đọc manual của lệnh **date** nếu bạn muốn biết chi tiết hơn, tuy nhiên syntax của lệnh này hơi sida một chút. Chỉ có root mới có quyền thiết đặt đồng hồ, những người dùng khác dùng chung đồng hồ hệ thống, chỉ có điều là họ có thể tự do thiết đặt timezone của mình mà thôi.

Tránh nhầm lẫn với lệnh **time**, lệnh này được dùng để đo lường quãng thời gian từ lúc một chương trình chạy cho đến khi nó bị hủy.

Lệnh **date** chỉ thiết đặt hoặc hiển thị software clock. Lệnh **clock** sẽ đồng bộ đồng hồ phần cứng và đồng hồ phần mềm với nhau. Nó được sử dụng tại thời điểm mà hệ thống boot, nó sẽ đọc hardware clock và thiết đặt software clock. Nếu bạn cần thiết đặt cả hai loại đồng hồ này, đầu tiên hãy chỉnh sửa software clock với **date** , sau đó hãy đồng bộ hardware clock bằng lệnh **clock -w**.

Tùy chọn **-u** nói với lệnh **clock** rằng hardware clock đang sử dụng UTC. Bạn **phải** sử dụng tùy chọn này một cách hợp lý. Nếu không, máy tính của bạn sẽ gặp một chút trục trặc về vấn đề thời gian.

Bạn nên thay đổi đồng hồ (*kể cả phần cứng lẫn phần mềm*) một cách thận trọng. Nhiều phần của một hệ thống UNIX đòi hỏi đồng hồ phải chạy đúng. Ví dụ , tác vụ **cron** chẳng hạn. 

## 10.4. Khi đồng hồ bị sai.

Software clock được duy trì bởi hạt nhân Linux không phải lúc nào cũng đúng. Về bản chất thì nó hoạt động bởi một ngắt theo chu kỳ được gây ra bởi một bộ đếm phần cứng của máy tính. Nếu như hệ thống có quá nhiều tiến trình đang chạy, thì có thể sẽ rất lâu mới xử lý được thông tin của ngắt, và software clock bắt đầu có xu hướng bị lệch về thời gian. Hardware clock chạy một cách độc lập nên nó có xu hướng ít bị  sai lệch hơn. Nếu bạn khởi động lại hệ thống một cách thường xuyên, thì sự sai lệch của software clock sẽ gần như không xảy ra do cả hai đồng hồ này sẽ được sync trong lúc hệ thống boot. Nếu bạn muốn điều chỉnh lại đồng hồ phần cứng, đơn giản là chỉ cần reboot hệ thống và truy cập vào BIOS. Điều này tránh được tất cả các vấn đề mà có thể xảy ra nếu như bạn chỉnh sửa software clock. Nếu chỉnh sửa thời gian bằng BIOS không phải là lựa chọn của bạn, bạn có thể làm nó thông qua **date** và **clock** , nhưng vẫn nên chuẩn bị sẵn sàng để reboot trong trường hợp hệ thống diễn trò sau khi điều chỉnh clock :smile:.

Một phương pháp khác là sử dụng **hwclock -w** hoặc **hwclock --systohc** để đồng bộ hardware clock từ software clock. Nếu bạn muốn đồng bộ software clock từ hardware clock, sử dụng **hwclock -s** hoặc **hwclock --hwtosys**. Bạn có thể biết nhiều thông tin hơn bằng cách đọc manual của lệnh này.

## 10.5. NTP - Giao thức thời gian mạng - Network Time Protocol.
Một máy tính được nối mạng  có thể kiểm tra đồng hồ của nó một cách tự động bằng cách so sánh đồng hồ của nó với đồng hồ của một máy khác nhằm giữ thời gian chạy đúng. Network Time Protocol là giao thức giúp thực hiện  điều này. Nó là một phương pháp giúp giữ đồng hồ của bạn chạy chuẩn bằng cách đồng bộ đồng hồ trên máy với một hệ thống khác. 

Đối với những người dùng Linux thông thường thì đây có thể coi là một sự xa xỉ chấp nhận được. Nhưng đối với  những tổ chức lớn hơn thì sự *"xa xỉ"*  này trở nên cần thiết. Hầu hết các bản phân phối Linux ngày nay đều đi kèm với một gói NTP. Bạn có thể sử dụng nó để cài đặt NTP, hoặc bạn cũng có thể download mã nguồn tại [đây](http://www.ntp.org/downloads.html) sau đó tự mình biên dịch.

Do mỗi bản phân phối Linux đều có những cách sử dụng NTP của riêng nó, nên cách sử dụng cụ thể sẽ không nằm trong khuôn khổ của bài viết này. Bạn có thể tham khảo các tài liệu rõ ràng hơn trên mạng.

> Nguồn : [http://tldp.org](http://tldp.org)
> Link bài viết trước : [\[Series\] Linux System Administrator's guide. \[Phần 9\]](https://kipalog.com/posts/Series--Linux-System-Administrators-s-guide---Phan-9)