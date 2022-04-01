# U-Boot
Tipik olarak açık kaynaklı bir yazlımdır.

## U-Boot Configuration
Git sayfası https://github.com/u-boot/u-boot üzeriden yazılım indirilebilir.

- **/cofings** içerisinde bazı konfigurasyon dosyaları bulunmaktadır. Bunlar desteklenen kartlar için özel olarak hazırlanmıştır.
	- CPU type, çevresel birim konfigurasyonu, memory mapping, U-Boot özellikleri tanımlanmalıdır.
- **U-Boot**, **C header files** tanımlamalarını kullanarak yapılandırmalarını kullanmaktadır. Linux Kernel gibi.
	- Yeni sistem içersinde tüm yapılan kartlar dönüştürülmemiş olabilir.


Komutu kullanılarak dermele işlemi başlatılır.

> make menuconfig

- PATH veriable içerisinde cross-compler olduğundan emin olunmalı.
- Derleme işlemini cross-compler belirteler derlemek için,
	- Eğer derleyici **arm-linux-gcc** ise,
	
	 > make CROSS_COMPILE=arm-linux-

- Sonuç olarak U-Boot Image olarak **u-boot.bin** dosyası oluşur. Ama Platforma bağlı olarak hangi depolama aygıtından önyükeme yapıldıysa (**NAND**, **MMC**) farklı olarak da isimlendirilmiş olabilr,
	- **u-boot.img**, **u-boot.kwb** ...
- Ayrıca U-Boot ile birlikte flashlanması SPL dosyası da gereken **u-boot-spl.bin** dosyasıda oluşacaktır. (Gereken mimari ve özellikte.)

## Installing U-Boot

U-Boot donanım tarafından yürütülebilmesi için genellikle flash belleğe yüklenmesi gerekir.

- Donanıma bağlı olarak U-Boot kurulumu farklı şekillerde yapılabilinmektedir.
	- CPU, kullanılabililecek özel bir önyükleme monitörü sağlanabilir.
	- CPU, sabit bir ortamdan (NAND) yükleme yapmadan önce ilk olarak çıkartılabilir ortamdan (MMC) önyüklenir.
	- U-Boot zaten üzerinde yüklüdür. Fakat dikkat edilmesi gereken şey U-Boot yeni sürüm çalışmazsa kart kullanılmaz.
	- Kart, flash belleğe yazmaya izin veren JTAG arayüzü sağlauabilir.

#### Important Commands

Tam komut seti, U-Boot yapılandırmasına bağlıdır.

- **fatload**, FAT dosya sisteminden RAM'e bir dosya yüklenir.
	- Örneğin, **fatload usb 0:1 0x21000000 zImage**
	- Ayrıca bakılabilir, **fatinfo, fatls, fatsize, fatwrite...**
- Benzer dosyasistemi komutları, 
	- **ext2load, ext2ls, ext4load, ext4ls**
- Dosya sistemi bağımız komutlar unutulmamalıdır,
	- **load, ls, size, write**
	- Örneğin
	>load usb 0:1 0x21000000 zImage
	>ls mmc 0:2 boot/
	>size mmc 0:1 dtb
	
- **loadb, loads, loady**, RAM a dosya yüklemek için kullanılır.
- **tftp**, RAM'a network üzerinden dosya yüklenir.
- **ping**, internet testi için kullanılabilir.
- **bootd**, depolanan varsayılan önyükleme komutunu çalıştırır.
- **bootz** 'address', RAM üzerinde, verilen adreste yüknene sıkıştırılmış bir kernel image başlatır (**zImage**) .
- **usb**, USB alt sistemini başlatılması ve kontrol edilmesi çin kullanılır, genel hedef USB depolama birimini başlatmaktır.
- **mmc**, MMC atl sistemini başlatması ve kontrol edilmesi için kullanlır, SD ve microSD kartlar içindir.
- **nand**, NAND için silme, okuma ve yazma işlemlerini gerçekleştirir.
- **md**, hafıdaki imageleri görüntüler. Yüklenen içeriği kontrol etmek yararlı olabilir bellek veya donanım kayıtlarına bakmak için.
- **mm**, bellek içeriğini düzenlemek için kullanılabilr. Direk olarak donanım registerlerini kontrok etmesi ve test etmesi için kullanıllabilir.
- **bdinfo**, Geçerli RAM adresini bulmasına izin verir, SoC datasheet ihtiyac olmadan veya board manuala ihtiyac duyulmadan.



## Environment Veriable

### Prenciple

- U-Boot ortam değişkenleri vasıtasıya konfigure edilebilir.
	- Bazı belirli ortam değişkenleri, farklı komutların davranışını etkiler
	- Özel ortam değişkenleri eklenebilir ve komut dosyalarında kullanılabilir
- Ortam değişkenleri U-Boot da kalıca depolama alanından RAM'a yüklenir çalışıtırılması için. Tanımlanabilir veya değiştirilebilir ve depolamaya tekrar kaydedilebilir değişkenlerdir.

### Implementation
- **U-Boot** yapılandırmasına bağlı olarak, tipik olarakş şuralarda saklanabilir.
	- **NAND Flash** da sabit bir offsette
	- **MMC** veya **USB Depolama** birimnde, başlangıçdan önceki ki ilk bölümde da (Firt Partition)
	- **uboot.env** olarak **FAT** veya **ext4** bölümlerinde
	- **UBI** bölümünde


### Commands
- **printenv**, tüm ortam değişkenlerini göstermektedir.
- **printenv** **"veriable-name"**, değişken adı girilen değeri göstermektedir.
- **setenv** **"veriable-name"** **"veriable value"**, şeklinde düzenleme yapılabilir. Veya yeni bir ortam değişkeni oluşturulabilir. Bu değişklik sadece RAM de saklanır.
- **editenv** **"veriable-name"**, değişkenin değeri değiştirilir. Sadece RAM de saklanır
- **saveenv**, mevcut ortam değişkenlerinin değerlerini depolama kısmına kaydeder.

![[Screenshot 2022-03-30 080234.png]]



### Important U-Boot Env Veriables

- **bootcmd**, bu özel komut U-Boot açılışta otomatik olarak yürüteceği komutları belirtir ve süreç bir kesintiye uğramazsa, yapılandırabilir bir geçikmeden (bootdelay) sonraki sürü başlatılır.
- **bootargs**,** Limux Kernel** için belirli argümanlar alır.
- **serverip**, **U-Boot** un iletişim kuracağı server IP adresini belirlemede kullanılır.
- **ipaddr**, **U-Boot** tarafından kullanılacak IP adresi belirlenir
- **netmask**, server e erişilecek netmask addresi belirtilir.
- **ethadd**, ethernet **MAC** addresini belirlemede kullanılır ve sadece bir kez düzenlenebilir.
- **filesize**, **RAM** e son kopyalanan doyanın boyutunu göstermektedir.


- U-Boot da ortam değişkenleri belirli başlı scriptler alabilir ve bunları yürütebilir,
	- Otomatik önyükleme ve güncelleme işlemlerinide,
	- **;** komutu kullanılarak bir kaç komut zincirlenebilir.
	- Testlerde kullanılması için, **if command ; then ....... ; else ..... ; fi** yapısı bulunmaktadır.
	- **run**, scriptlerin çalıştırılması için kullanılır.
	- ${veriable-name}, kullanılarak diğer referans değişkenlere başvurulabilir.

Örnek olarak, 

![[Screenshot 2022-03-30 081314.png]]

### Transferring Files to The Target

- **U-Boot**, çoğunlukla bir kernel image dosyasını yüklemek için kullanılır , ancak root filesystem flasta depolanır ve kernel imagenin değiştirilmesine izin verir.
- Dosyalar, target ve geliştirme bölümü ile değiştokuş edilmelidir. Bunun birkaç yolu bulunmaktadır.
	- **Netwrok** üzerinden (Eğer network port etkin ise Ethernet, USB device üzerinden Ethernet), eğer **U-Boot** bu sürücüler sahipse gerçekleştirilebilir. Bu en hızlı ve en verimli yöntemdir.
	- **USB** ile gerçekleştirilebilir, eğer **U-Boot** USB Controller destekleniyorsa.
	- **SD** veya **microSD** kart ile gerçekleştirilebilir, eğer **U-BOOT** MMC **kontroller** destekleniyorsa.
	- Seri portlar üzerinden yapılabilir. (**loadb**, **loadx** veya **loady**) komutları ile.


### TFTP

- TFTP, hedefete bulunan U-Boot a geliştirme ortamı bölümünü aracılıyla sağlamaktadır.
	- Trivial File Transver Protocol
	- FTP ile benzerdir, fakat doğrulama olmadan UDP üzerinden yapılmaktadır.
- TFTP, bir geliştirme ortamına ihtiyaç duymaktadır.
	- **sudo apt install tftpd-hpa**
	- **/var/lib/fttpboot** veya **/srv/tftp** etkin olabilir.
- TFTP istemcisi  U-Boot içerine entegre bir sistemdir.
	- **ippaddr**, **serverip**, **athaddr** ortamam değişkenleri ile configure edilebilir.
	- **tftp** **"address"** **"filename"** içeriği RAM a belirli bir adrese yüklenebilir.
		- **tftp 0x21000000 zImage**


