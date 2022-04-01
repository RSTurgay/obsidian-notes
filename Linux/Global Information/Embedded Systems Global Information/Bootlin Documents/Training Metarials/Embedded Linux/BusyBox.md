# BusyBox
- Bir Linux Sisteminin çalışması için bir dizi temel programa ihtiyacı vardır.
	- Bir **init** programı
	- Bir **shell**
	- Dosya işleme ve sistem yapılandırması için çeşitli temel yardımcı programlar
- Normal GNU/Linux sistemlerinde bu programlar farklı projeler tarafından sağlanmaktadır.
	- **coreutils**, **bash**, **grep**, **sed**, **tar**, **wget**, **modutils**
	- Bir çok farklı kompenenleri entegre etmektedir.
	- Gömülü sistem kısıtlamaları göz önünde bulundurularak tasarlanmayan bileşenler: çok yapılandırılabilir ve çok çeşitli özelliklere sahiptir.
- BusyBox alternatif bir çözümdür, gömülü sistemlerde son derece yaygın kullanılmaktadır.

### General Purpose Toolbox : BusyBox
- Genel amaçlı kullanılan araç kutusudur.
- Detaylı bilgi için,
	- https://www.busybox.net/
- Birçok kullanılşlı UNIX komut satırınun yardımcı programlarının yeniden yazılması
	- Çalışmayı kolaylaştıran tek bir projeye entegre edilmiştir
	- Tek bir diskete sığmaktadır, (1.44 MB)
	- Gömülü sistemler düşünülerek tasarlandı: yüksek yapılandırılabilir, gereksiz özellikler yoktur.
	- Swiss Army Knife of Embedded Linux olarak anılıyor.


### BusyBox in the Root Filesystem

![[Screenshot 2022-03-30 230643.png]]

- Tüm araçlar tek bir çalıştırılabilir yapıda sağlanmaktadır, **/bin/busybox**
	- **BusyBox**' a entegre her uygulama, **/bin/busybox** için sembolik bağlantılar oluşturulur.
	- Oldukça özellikli bir yapılandırma için 500KB'den az KB (uClibc ile statik olarak derlenmiş) veya 1MB'den az  (glibc ile statik olarak derlenmiştir).

#### Most commands in one binary

![[Screenshot 2022-03-30 230920.png]]


### Configuring BusyBox

- https://busybox.net adresinden kaynak kodu indirilir.
- Configure edilirken (.config) dosyaslı oluşturulur.
	- **make defconfig**
		- BusyBox ile başlamanın iyi yolu
	- **make allnoconfig**
		- Tüm seçenekler kapalı, neye ihtiyac var ise o seçilir.
- **make menuconfig**
	- Linux kernel tarafından kullanılanlarla aynı yapılandırma arayüzüne sahiptir.

### Compiling BusyBox
- **make**
	- komutu ile derleme adımına başlatılır
- **make install**
	- Simgesel bağlantılara sahip UNUX dizin yapısını oluşturur. BusyBox yürütülebilr.


-  BusyBox, bir **init** programının uygulanmasını sağlar.
- Masaüstü / Server sistemlerinde bulunan **init** uygulamalarından daha basittir. (systemd , systemv)
- Tek bir configure dosyası bulunur. **/etc/inittab**
- Başlangıçta hizmetlerin çalıştırılmasına ve belirli hizmetlerin her zaman açık olduğundan emin olunmasına izin verir.



