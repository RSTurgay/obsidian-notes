# Linux Root Filesystem

## Principle and Solutions

- Dosya sistemleri, dizinlerdeki ve depolama aygıtlarındaki dosyalardaki verileri düzenlemek için kullanılır veya ağda. Dizinler ve dosyalar bir hiyerarşi olarak düzenlenir.
- **UNIX** sistemlerinde, uygulamalar ve kullanıcılar **tek bir global dosya hiyerarşisi** görür ve birkaç dosya sisteminden oluşabilen dizinler.
- Dosya sistemleri belirli konumlara hiyerarşik klasörlerle yerleştirilmiştir.
	- Bir dosya sistemi bir klasöre yerleştirildiğinde (**mount point** olarak adlandırılır), bu dizin içeriği depolama aygıtının içerini yansıtmaktadır.
	- Depolama aygıtı çıkartıldığında (unmounted), mount point boş olarak gözükür.
- Bu, uygulamaların dosyalara ve dizinlere, konumlarından bağımsız olarak kolayca erişmesini sağlamaktadır.

 - Bir bağlama noktası (mount point) oluşturmak için bir klasör oluşturulur

> sudo mkdir /mnt/usbkey

- Belirli bir depolama aygıtını bir mount point de bağlama

> sudo mount -t vfat /dev/sda1 /mnt/usbkey


- **mount**, dosya sistemlerini bağlamaya izin verir.

	> mount -t type device mountpoint

	- **type**, dosya sisteminin tipi (sanal olmayan dosya sistemi)
	- **device**, depolama cihazı veya network konumu
	- **mountpoint**, depolama cihazının veya network konumunun erişilebilir bir klasör konumudur.
	- **mount**, bağımsız değişken olmadan, şu anda bağlı olan dosya sistemlerini gösterir

- **unmount**, dosya sistemlerini kaldırmak için izin verir,
	- Bu, yeniden başlatmadan önce veya bir USB anahtarını çıkarmadan önce gereklidir, çünkü Linux kernel önbellekleri, performansı artırmak için belleğe yazar.
	- **unmount**, yazma işlemleri depolamaya bağlıdır.


- Belirli bir dosya sistemi, / ile tanımlanan hiyerarşinin köküne monte edilir.
- Bu tip dosya sistemleri **root filesystem** olarak adlandırılır.
- **mount** ve **unmount** progralmarı , bir dosya sistemi içindeki dosyalardır.
	- En az bir dosya sistemi monte edilmeden bunlara erişilemez.
- **Root filesystem**  ilk olarak bağlanan dosya sistemidir, mount komutu ile kullanılmaz.
- Bu direk olarak kernel tarafından bağlanır, **root=** opsiyonu ile belirlenir.
- Dosya sistemi bulunamadığında, kernel paniğe kapılır

>Please append a correct "root=" boot option
  Kernel panic - not syncing: VFS: Unable to mount root fs on unknown block(0,0)


#### Location of the root filesystem

- Root filesystem farklı konumlara monte edilebilir,
	- **Harddisk** bir bölününe (partition)
	- **USB Key** in bir bölümüne
	- **SD Card** bir bölümüne
	- **NAND Flash** veya benzer bir depolama cihazının bir bölümüne
	- **Network** üzerinde, NFS protokolü kullanılarak
	- **Memory** üzerinden, bir ön önyükleyici (pre-bootloader) kullanarak

- Sistem yapılandırmasını seçmek sistem tasarımcısına bağıdır ve kernel davranışını **root=** ile yapılandırır.,

#### Mounting Rootfs From Storage Device
- **Harddisk** veya **USB Key** bir bölününden, 
	- **root=/dev/sdXY**, **X** burada cihazın belirlenen adı, **Y** ise bölüm numarasıdır.
- **SD Card** bir bölümünü yüklemek için,
	- **root=/dev/mmcblkXpY**, X burada cihazın belirkenen adı, Y ise bölüm numarasıdır.
	- Örneğin **/dev/mmcblk0p2** ilk cihazın 2. bölüm numarasıdır.
- Flash Storage nın bir bölümünden yüklemek için,
	- **root=/dev/mtdblockX**, X bölüm numarasından önyüklenmektedir.
	- **/dev/mtdblock3**, sistemindeki dördüncü flash depolama aygıtını belirlenmiştir.

#### Mounting Rootfs Over The Network
	Bakılmadı daha sonra bakılacak (embedded-linux-slides Sayfa numarası 193)


#### Root Fileststem in Memory: initramfs
- Dosya sistemi memory tarafından da yüklenebilme yapısın sahiptir.  **initramfs** olarak adlandırılmaktadır.
	- Ya **Kernel Image** ine entegre edilmiş CPIO arşivinden
	- Yada **bootloader** tarafından yüklenmektedir.
	- Bir önyükleme sırasında,  bu arşiv Linux dosya önbelleğinde ayıklanır.
	- Burada iki kullanışlud durum vardır,
		- Çok küçük root filesystem lerin hızlı önyüklemesi. Dosya sistemi tamamen yüklendiğinden önyükleme süresi, uygulama başlatma çok hızlıdır.
		- Cihazlarda bulunan gerçek bir kök dosya sisteminde geçmeden bir ara adım olarak kernel image nin parçası olmayan sürücüler içindir.
		- Her zaman kernel image sınırlı küçük tutmalıdırlar.
		- Detaylar için,
			- https://www.kernel.org/doc/html/latest/filesystems/ramfs-rootfs-initramfs.html


#### External initramfs

![[Screenshot 2022-03-30 163706.png]]


#### Build-in initramfs

![[Screenshot 2022-03-30 163838.png]]


## Contents

#### Important Directories
- **/bin**, Basit programlar
- **/boot**, Kernel Images, konfigurasyon ve initrams
- **/dev**, Device Files
- **/etc**, System tarafındaki konfigurasyonlar
- **/home**, Kullanıcı dosyaları içeriri
- **/lib**, Basit kütüphaneler
- **/media**, Çıkartılabilir media bağlantı noktası için
- **/mnt**, static media bağlantı noktaları
- **/proc**, Sanal dosya sistemleri bağlantı noktaları
- **/root**, root user için home directories
- **/sbin**, Basit sistem programları
- **/sys**, Sanal dosya sistemleri bağlantı noktası
- **/tmp**, Geçici dosyalar
- **/usr**,
	- **/usr/bin**, Non-basic programlar
	- **/usr/lib**, Non-basic kütüphaneler
	- **/usr/sbin**, Non-basic sistem programları
- **/var**, Değişken veri dosyaları, sistem servisleri. Bu biriktirme dizileri ve dosyalar, yönetim ve günlük verileri gecici olarak depolar


### Device Files

- Bir kernel üzerinde belirli roller bulunmaktadır, bu roller kullanıcının donanım cihazlarına erişimine izin vermektedir.
- Linux Kernel, çoğu cihalara kullanıcı alanı uygulamaları vasıtasıyla iki farklı soyutlama vardır
	- **Character** device
	- **Block** Device
- Dahili olarak kernel tanımlamaları her cihazı bir bilgi üçlüsü ile tanımlarnır,
	- **Type**(**Block** or **Character**)
	- **Major**(Tipik olarak cihazların katogorileri)
	- **Minor**(Tipik olarak cihazların tanımlanması)

#### Block Devices
- Veri depolamak için okunabilen ve yazılabilen sabit boyutlu bloklardan oluşan cihaz
- Harddisk, USB Kets , SD Cards vb

#### Character Device
- Orjinal olarak, sonu ve başlangıcı olmayan sonsuz bir bayt akışı, Saf
	- Örnek olarak Serial Port
- Serial portlar kullanılır, terminaller, ayrıca ses kartları, video kartları, frame buffers 
- Linux Kernel tarafından Block aygıt olmayan aygıtların çoğu, karakter aygıtları olarak temsil edilir.

#### NOTE:
	Linux Kernel çekirdeğinde herşey birer dosyadır. HERŞEY
	Belirli yontemler kullanılarak (open, read, write, close) işlemleri yapılır.

#### Creating Device Files

- **devtmpfs**, tüm kernel e kayıtlı cihazlar /dev üzerinde bu yöntem ile sanal dosya sistemler bağlanabilir.
- **CONFIG_DEVTMPFS_MOUNT**, kernel konfigurasyon kısmında bu belirtilerek sisteme otomatik olarak boot sürecinde monte edilebilir. initramfs sistemine göre yapılamayabilirler.


## Minimal Filesystem

### Basic Application

- Çalışması için, Linux Kernel en az birkaç uygulamaya ihtiyac duyar,
	- **init** application, root filesystemi monte edildikten sonra kernel tarafından başlatılır ilk olarak kulanucu uygulamaları  başlatıldığında.
		- Detaylı olarak incelemek için, https://en.wikipedia.org/wiki/Init
		- Kernel, **init=** eğer var ise komut satırı tarafından belirtilen komutu çalıştırmayı dener.
		- Diğer yandan, **/sbin/init**, **/bin/init**, **/etc/init** ve **/bin/sh** çalıştırmaya çalışmaktadır.
		- Bu durumda **initframfs**, sadece **/init** e bakacaktır.
		- Bunların hiç birisini yapamazsa Kernel panikler ve ön yükleme işlemini durdurur.
		- init application, diğer tüm kullanıcı alanı uygulamalarını başlatmaktan sorumludur ve hizmetler ve ebeveynin sonlandırdığı süreçler için evrensel bir ebeveyn olarak hareket etmek için onlar yapmadan önce.
- Komut dosyalarını uygulamak, görevleri otomatikleştirmek ve bir kullanıcının sistemle etikileşimine girmesini izin veren kapıya shell adı verilir. 
- Basit kullanıcı komutlarının işletilmesi için gerekli olan komutları çalıştırır.
- Bu temel bileşenlerin kullanılabilir hale gelmesi için kök dosya sistemine entegre edilmesi gerekir.


#### Overall Booting Process

![[Screenshot 2022-03-30 225035.png]]


#### Overall Booting Process With Initramfs

![[Screenshot 2022-03-30 225203.png]]

