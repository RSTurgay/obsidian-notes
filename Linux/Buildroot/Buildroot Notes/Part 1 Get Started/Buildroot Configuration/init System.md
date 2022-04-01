### init System

init programı, çekirtek tarafından başlatılan ilk kullanıcı alanı programıdır (PID numarası 1'i taşır) ve başlatmadan sorumludur. Kullanıcı alanı servisleri (Örneğin web sunucusu, grafik uygulamaları, diğer ağ suucuları vb).

Buildroot 3 farklı init system yapısına bağlıdır, bu seçim **System configuration**, **Init
system** tarafından yapılmaktadır.

- **BusyBox** 
	- Birçok program arasında **BusyBox**, temel bir init programının bir uygulamasına sahiptir. çoğu gömülü sistem için yeterlidir.
	- **BR2_INIT_BUSYBOX** etkinleştirimesi **BusyBox** derlenmesini ve indirilmesini sağlayacaktır.
	- Bu Buildroot un default yapısında **BusyBox** kullanılmaktadır.
	- **BusyBox** *Init* programı, **/etc/inittab** dosyasından önyükelemede ne yapacağını öğrenmektedir.
	- Dosya sytax http://git.busybox.net/busybox/tree/examples/inittab bu adrese göre yazılmalıdır.
	- Sistem için default **inittab** Buildroot için **system/skeleton/etc/inittab** kısmında bulunmaktadır.
	- Bunların dışında birkaç önemli dosya sistemini monte ederken, varsayılan inittab'ın yaptığı ana iş **/etc/init.d/rcS** shell scrpit i başlatmaktır, ve bir **getty** programı başlatın (bir oturum açma istemi sağlar).

- **systemV**
	- Bu yönten eski geneleksel **sysvinit** programıdır ve Buildroot da **package/sysvinit** içerisindedir.
	- Daha iyi bir alternatife geçene kadar çoğu popüler masaüstü distb. bu yöntem kullanılmaktaydı. (**Systemd** veya **UpStart**)
	- **sysvinit** **inittab** dosyası içierisinde çalışmaktadır. BusyBoxdan faklı bir syntaxı bulunmaktadır.
	- Bu başlatma çözümüyle birlikte kurulan varsayılan **inittab**, **package/sysvinit/inittab** içinde bulunur.

- **systemd**
	- Linux için yeni nesil bir init programıdır.
	- Geleneksel init programlarından çok daha fazlasını yapmaktadır.
		- Agresif paralelleştirme yetetenleri
		- Servisleri başlatmada D-BUS ve Socket aktivasyonu
		- isteğe bağlı olarak deamonsların başlatılması
		- Linux kontrol gruplarını kontrol ederek süreçleri takip eder
		- sistem durumunun anlık görüntüsünü ve geri yüklenmesini destekler
	- **systemd** gerçekçi karmaşık gömülü sistemler için kullanışlı olabilir.
		- Örneğin D-BUS etkinleştirilmesi ve bu servislerin diğer servisler ile konuşturulması gibi.
	- **systemd** oldukça fazla sayıda büyük bağımlılıklar getirdiğini bilmemizde fayda var
		-  dbus, udev ve daha fazlası
	- http://www.freedesktop.org/wiki/Software/systemd. adresinden detaylı olarak incelenmelidir.

**Note :**
Buildroot geliştiricileri tarafından önerilen çözüm, çoğu gömülü sistem için yeterli olduğu için ***BusyBox*** init'i kullanmaktır. ***systemd*** daha karmaşık durumlar için kullanılabilir.

