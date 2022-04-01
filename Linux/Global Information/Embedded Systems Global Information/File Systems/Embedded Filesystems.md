# Embedded Filesystems
Aşağıdaki dosya sistemleri gömülü sistemler için kullanılmaktadır.


## AXFS
[AXFS](https://elinux.org/AXFS "AXFS") - **Advanced XIP File System**

- Bu dosya sistemi özellikle Execute-in-place (Yerinde yürütme )işlemleri için desteklenmesi için tasarlanmıştır.
- İki aşamalı bir yaklaşım kullanmaktadır.
	- İlk aşama, dosya sisteminin flash'ta olması ve hangi sayfaların kullanıldığını belirterek profil verilerini toplamak için çalıştırmasıdır.
	- İkinci aşamada, bu profil verilerini kullanarak bir dosya sistemi kurarsınız.
- Bu dosya sistemi, profil dosyasında belirtilen tüm sayfaları XIP verileri olarak yapar ve bu veriler daha sonra montaj sırasında RAM'e yüklenebilir (ve XIP olarak yürütülür). XIP sayfalarını NOR flash'a koymak ve oradan çalıştırmak da mümkündür.


## CramFS
[CRAMFS](http://en.wikipedia.org/wiki/Cramfs)

Linux için sıkıştırılmış salt okunur bir dosya sistemi.

CRAMFS maksimum boyutu 256MB dir.

- Doğrusal Cramfs", Cramfs dosya sistemi ile doğrusal bir blok düzeninde sıkıştırılmamış dosyayı kullanmak için özel bir özelliğin adıdır.
- Bu, yerinde yürütülebilecek dosyaları depolamak için kullanışlıdır.
- Daha Fazla bilgi için [Application XIP](https://elinux.org/Application_XIP "Application XIP")


## F2FS
-   [F2FS](https://elinux.org/F2FS "F2FS")[(wikipedia entry)](http://en.wikipedia.org/wiki/F2FS) Linux için flash dostu bir dosya sistemidir .,
- Samsung tarafıdnan geliştirimiştir.
-  Dha Fazla bilgi için incelenebiliir.



## InitRAMFS

RAM Diskleri için yeni bir dosya sistemi olarak tanınır.

Teknik makale olarak bu yöntem, root filesystem ve programını başlatılması yerine kernel in bellek ön belleğinde bulunmasını sağlayan bir Linux 2.6 olan **initramfs** tanıtmaktadır.

Yazar, **initrd** ile karşılaştırıldığında **initramfs**'in önyükleme süresi esnekliğini, bellek verimliliğini ve basitliği artırabileceğini söylüyor.

Gömülü Linux geliştiricileri için özellikle ilginç bir özellik, nispeten basit, derinlemesine gömülü sistemlerin **initramfs**'i tek dosya sistemi olarak kullanabilmesidir.


Nasıl oluşturulduğu hakkındaki makale;
-   [http://www.landley.net/writing/rootfs-howto.html](http://www.landley.net/writing/rootfs-howto.html)


## JFFS2

[JFFS2](http://sourceware.org/jffs2/) - The Journalling Flash File System

Bu en yaygın kullanılan flash dosya sistemidir.

- MAX Size 128MB dir.


## LogFS

LogFS, çoğu kullanım için JFFS2'nin yerini almayı amaçlayan ölçeklenebilir bir flash dosya sistemiydi.

Ne yazık ki, şu anda terk edilmiş görünüyor.

See [LogFS](https://elinux.org/LogFS "LogFS") for details.


## NFS

Gömülü aygıtlardaki alan kısıtlamaları nedeniyle, geliştirme sırasında hedef için kök dosya sistemi için bir ağ dosya sistemi kullanmak yaygındır. Bu, hedefin geliştirme sırasında tam boyutlu ikili dosyaların ve birçok geliştirme aracının yerleştirilebileceği çok geniş bir alana sahip olmasını sağlar. Bu yaklaşımın bir dezavantajı, geliştirme döngüsünün herhangi bir döneminde nihai ürün sevkiyatı için sistemin yerel dosya sistemleriyle yeniden yapılandırılması (ve büyük olasılıkla yeniden test edilmesi) gerekmesidir.

- Linux çekirdeğine bir NFS istemcisi yerleştirilebilir ve çekirdek, kök dosya sistemi olarak NFS'yi kullanacak şekilde yapılandırılabilir.
- Bunun için ağ desteği ve hedef için IP adresini belirleme mekanizmaları ve NFS ana bilgisayarındaki dosya sistemine giden yol gerekir. Ayrıca, ana bilgisayar bir NFS sunucusunu çalıştıracak şekilde yapılandırılmalıdır. Çoğu zaman, ana bilgisayar, bir DHCP sunucusu çalıştırarak hedef panoya gerekli adres ve yol bilgilerini de sağlar.

Çekirdekle bir NFS kök dosya sistemi kurma hakkında daha fazla bilgi için Linux çekirdek kaynağındaki **Documentation/nfsroot.txt** dosyasına bakın.

