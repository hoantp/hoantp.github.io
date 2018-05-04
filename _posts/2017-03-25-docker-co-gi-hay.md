---
layout: post
title: "Docker có gì hay?"
tags: [ docker, devops ]
desc: "Docker hiện nay càng ngày càng nhận được sự quan tâm của giới DevOps, thực ra không chỉ riêng DevOps mà Developer cũng rất chú ý. Vậy chúng ta sẽ tìm hiểu xem Docker là gì, và tại sao nó lại quan tâm nhiều đến vậy."
---

## Docker là gì?

Như các bạn đã biết, vấn đề đầu tiên thường gặp phải đó là việc không đồng bộ môi trường giữa các Developer với nhau; chính vì vậy Docker sinh ra để giải quyết vấn đề "works on my machine" (cái này chạy ngon trên máy tui) bằng cách quản lý các apps song song trong các container được cô lập để đem lại hiệu quả tính toán tốt hơn.

![Docker Architecture](https://docs.docker.com/engine/images/architecture.svg)

<div class="caption">Kiến trúc của Docker</div>

Hiện tại công ty mình có sử dụng Vagrant + Virtualbox để giải quyết vấn đề đó nhưng bản chất là sử dụng máy ảo để cô lập về mặt OS, còn điểm đặc biệt của Docker là cô lập tới từng app.

Mình tiếp cận Docker không sớm cũng không muộn, và đã mất một thời gian rất vất vả để hiểu nó, vì thế mình sẽ tóm tắt những gì mình đã hiểu ở đây để giúp các bạn hình dung về Docker tốt hơn.

## Đặc điểm

Điểm đặc biệt của Docker là nó đóng gói mọi thứ thành Container.

![Docker Container](https://www.docker.com/sites/default/files/Package%20software%40x2.png)

Mọi thứ chúng ta cần sẽ được cô lập trong một container:

* Môi trường để ứng dụng hoạt động.
* Các thiết lập cơ bản cho ứng dụng (tuỳ biến tự do).
* Hệ thống network nội bộ hoặc giữa các container với nhau.

Để biết cách cài đặt Docker mọi người tham khảo [ở đây](https://docs.docker.com/engine/installation/) nhé!

## Image

Nói một cách dễ hiểu, bạn có thể hình dung nó giống như một cái DVD-R, hoặc một file Ghost, hoặc file box (nếu ai biết Vagrant). Tóm lại nó là đóng gói của một... cái gì đó. Về cơ bản nhất là nó sẽ đóng gói một Linux OS nào đó!

Trong Docker có một cái gọi là Dockerfile dùng để build lên một Image mới, nếu bạn nào làm file này sẽ thấy nó kế thừa thành nhiều layer, và layer cao nhất là một OS nào đó! Mỗi lần kế thừa xong, người ta sẽ tiếp tục cài thêm gì đó và đóng gói lại.

Ví dụ nếu bạn xem Dockerfile của [PHP-FPM 7.0](https://github.com/docker-library/php/blob/0792ba42f0ea7435ceb26b42a066274e028b30e3/7.0/fpm/Dockerfile), bạn sẽ thấy nó kế thừa OS Image của Debian (debian:jessie) và tiếp tục cài PHP-FPM lên đó và đóng gói thành Image cho chúng ta tải về và xài.

Một số điểm hay của việc xây dựng Image:

* Có thể kế thừa từ bất kỳ Image nào khác và custom theo ý mình.
* Image có thể upload và chia sẻ cho bất kỳ ai một cách dễ dàng.
* Số lượng Official Image và các Image hữu ích rất nhiều và hầu hết là miễn phí ở trên [Docker Store](https://store.docker.com).

Hầu hết ở các dự án đặc thù thì DevOps sẽ kế thừa các Official Image và custom các thông số theo ý mình, cũng rất dễ kiểm soát và tiện lợi.

Để pull một image về xài ta chỉ cần lên Docker Store, kiếm một cái và chạy lệnh:

```
$ docker pull <tên image>
```

## Container

### Container là gì?

Container là một tên gọi rất thực tế và dễ hình dung tới Container ngoài đời thực. Khi bạn sử dụng Container, những thứ cần thiết để chạy được app của bạn sẽ được đóng gói vào các Container độc lập. Trong đó bao gồm OS, thư viện liên quan, app chính.

Tuy nhiên không giống như VM, Container sẽ không đóng gói một OS đầy đủ mà chỉ gồm các thư viện và các thiết lập cần thiết nhất để app có thể hoạt động. Chính vì vậy các Container không chỉ nhẹ mà còn tối ưu cho app.

![Docker Containers](https://www.docker.com/sites/default/files/group_5622_0.png)

### Tạo một Container mới

Các bạn sẽ tạo ra Container từ Image. Giống như bạn chạy file Ghost như đi cài Windows dạo ngày xưa =)). Bạn có thể tưởng tượng mỗi Container như là một VM (thực tế không phải), được cô lập trong cái hộp nhưng lại xài chung tài nguyên với máy thật của chúng ta.

```
$ docker run <tên image>
```

Thông thường khi chạy lệnh `docker run` thì nếu không có Image ở local thì nó sẽ tìm và tải về cho bạn luôn. Container ngay sau đó sẽ được tạo ra, chủ yếu bạn sẽ tương tác thông qua câu lệnh `docker exec`. Ví dụ

```
$ docker exec -it <tên container> bash
```

* i (interactive): để bạn truy cập vào terminal của OS.
* t (#): hiển thị stdout cho bạn xem.

Khi đó nó sẽ truy cập vào bên trong Container để bạn tương tác, thực chất nó gọi vào `bash` của OS bên trong Container đó, tuỳ vào OS thì có thể là `bash` hoặc `sh` (alpine).

Có một thứ làm mình hết sức đau đầu khi mới xài Docker, đó chính là Container nếu không có một process foreground để giữ stdout hoặc có background in detach mode thì nó sẽ tự động off.

Ban đầu mình khá khó chịu nhưng có thể thấy nó thường dùng để làm một số việc hữu ích như là:

* Monitor app chính trong Container.
* Chắc chắn rằng app của chúng ta đang chạy chứ không tèo.
* Print ra log để có thể theo dõi real-time khi cần thiết mà không cần truy cập vào tận bên trong Container.

Một trick khá thú vị khi viết Dockerfile để sau khi run thành Container mà vẫn giữ nó sống là thêm ở CMD hoặc ENTRYPOINT lệnh:

```
$ tail -f /dev/null
```

Tuy nhiên khi xây dựng Application chạy trên docker thì khuyến khích nên ghi log để monitor dễ dàng hơn chứ null thì... =))

## Một số thành phần khác

Để bổ trợ cho hệ sinh thái Docker, chúng ta còn có các hệ thống network, volume,... để các container có thể dễ dàng tương tác với nhau.

Ví dụ như mình tạo một website sử dụng Laravel, mình sẽ sử dụng các image chính: nginx, php-fpm, mysql. Sau đó mình thiết lập một hệ thống network nội bộ giữa các container được tạo ra rồi cho chúng tương tác với nhau.

Khi đó (ví dụ):

* nginx: expose ra cổng 80 để cho client truy cập.
* php-fpm: mở cổng 9000 để nginx có thể kết nối proxy.
* mysql: mở cổng 3306 để php-fpm khi chạy ứng dụng Laravel có thể truy cập vào DB.

Tuy nhiên việc chỉ dùng đơn thuần Docker để tạo lên một đống Container và cho chúng tương tác với nhau sẽ khá là mệt, tất nhiên ta có thể dùng một số công cụ automation như là Ansible. Nhưng bản thân Docker đã hoàn thiện dần hệ sinh thái và mang đến công cụ như thế.

## Kết luận

Docker đem khái niệm Container trở lại với một cách tiếp cận dễ dàng hiệu quả hơn, nếu quen với Docker bạn có thể nhận ra các lợi thế như:

* Thích hợp với Microservice.
* Giảm thiểu việc tiêu tốn tài nguyên.
* Tối ưu hóa từng ứng dụng nhờ việc thiết lập cũng được cô lập.
* Tốc độ xây dựng môi trường rất nhanh và deploy cũng an toàn hơn.

Với những lợi thế trên, Docker hoàn toàn có thể thay thế các VM truyền thống. Vì vậy, bạn có thể sử dụng Docker trong nhiều trường hợp:

* Xây dựng môi trường phát triển, môi trường chạy thật với khả năng scalable cao.
* Kiểm nghiệm hiệu năng dễ dàng nhờ khả năng tạo Container nhiều và nhanh chóng.

Docker càng ngày càng trưởng thành và còn cung cấp thêm nhiều công cụ để bạn có thể làm việc dễ dàng hơn như Docker Compose, Docker Swarm,... Mình sẽ viết thêm một số bài về 2 tool này để tối ưu hoá khả năng của Docker.

Chúc các bạn một ngày vui vẻ. Hãy cài Docker và thử luôn nhé!
