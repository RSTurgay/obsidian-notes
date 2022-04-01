# Block Filesystems

## Block Devices

### Block vs RAW Flash
- Depolama cihazları 2 ana tipte sınıflandırırılmıştır. **Block Devices** ve **Raw Flash Devices**
	- Bunlar farklı altsistemlere ve dosya sistemlerenine göre değişir.
- **Block Devices**, verileri silmeden rasgele sırayla block bazında okunabilir ve yazılabilir.
	- **Harddisk**, **RAM Disk**
	- **USB Key**, **SSD**, **SD Card**, **eMMC** : bunlar Flash depolamaya dayalıdır, ancak bir block cihazını kontrol eden bir kontroller olması gerekir. Belirli bir yol üzerinden
- **Raw Flash Devices**, SoC tarafından kontrol edilmektedir. Bunlar okunabilir, fakat yazma, önceden silmeyi gerektiri ve genellikle "block" dan daha büyük boyutlarda gerçekleşir.
	- **NOR Flash**, **NAND Flash**


- Bulunabilir block devices sistemde şu şekilde bulunabilinmektedir.
	- /proc/partitions
	
	![[Screenshot 2022-03-31 082442.png]]

#### Partitioning

- Block Devices, sistemin farklı bölümlerini depolamak için bölümlere ayrılabilinmektedir.
- Partition Table (Bölüm Tablosu), cihazın kendi içinde saklanır ve Linux Kernel tarafından otomatik olarak okunar ve analiz edilir.
	- **mmcblk0** cihazın bütünüdür.
	- **mmcblk0p2**, **mmcblk0** cihazının ikinci bölümüdür.
- İki tür bölümleme formatı bulunmaktadır,
	- MBR, lagacy format.
	- GTP, 2 TB den daha büyük yeni tür işletim sistemleri için kullanılmaktadır.
- Bir block cihazında bölümler oluşturmak için ve değiştirmek için sayısız araç bulunmaktadır,
	- **fdisk**, **cfdisk**, **sfdisk**, **parted** vs.

#### Transfering data to a block device

- Verileri bir block cihazına ham şekilde aktarmak genellikle gereklidir.
	- Özellikle bir filesystem image block cıhazına yazılırken
- Bu, herhangi bir dosya sistemi katmanını atlayarak doğrudan blok aygıtının kendisine yazar.
- **dd** (disk duplicate) komutunu kullanarak dosyalar trasnfer edilebilir.

> dd if=/dev/mmcblk0p1 of=testfile bs=1M count=16

- Buradaki komuta göre **/dev/mmcblk0p1** bölümüne **testfile** dosyasını 1 MB'lik 16 block aktarmaktadır.

> dd if=testfile of=/dev/sda2 bs=1M seek=4

- Buradaki komuta göre de , test dosyasının tüm içeriğini 1 MB'lik bloklarla **/dev/sda2**'ye aktarır, ancak **/dev/sda2**'de ofset 4 MB'den başlar

- TIPICAL MISTAKE
	- Bir dosyayı kopyalarken (filesystem image yokken) filesystem bağlanmadan yapılması
		- **dd if=zImage of=/dev/sde1**
		- Bunun yerine,
			- **sudo mount /dev/sde1 /boot**
			- **cp zImage /boot/**


### Available Block Filesystems

#### Standard Linux Filesystem Format : ext2, ext3, ext4

- Standart olarak Linux sistemlerinde **ext{2, 3, 4}** kullanılmaktadir.
	- ext2, CONFIG_EXT2_FS olarak konfigure edilir.
	- ext3, Journaled file systemdir.
	- ext4, ONFIG_EXT4_FS olarak konfigure edilir, temek olarak çok büyük bölümleri destekler ve performans iyleştirmeleri sağladı.
- Bir root filesystem Linux un ihtiyaç duyduğu tüm özellikleri desteklemekler, permission, sahiplik, device files, symbolic links vs.

#### Journaled filesystems
- Daha basit dosya sistemlerinden farklıdır (**ext2**, **vfat**....)
- Sistemin çökmesi veya ani bir güç kesintisine karşı tutarlı şekilde kalacak şekilde tasarlanmıştır.
- Yazmalar önce journoal da belirtilir ve buna bağlı olarak tüm yazmalar veya meta veriler buna bağlı olarak konfigure edilir.

![[Screenshot 2022-03-31 084643.png]]


#### Filesystem Recovery Afrer Crashed


![[Screenshot 2022-03-31 084737.png]]

- Journal (Anı defteri, Dergi, Kayıt defteri, Günlük) sayesinde, açılışta kurtarma İşlemler devam ettiği için zaman hızlı kirli kapatma anında açıkça tespit edilmiştir. Burada tamamen dosya sistemi kontrolüne gerek kalmaz.
- Bu son yazılanların yapıldığı anlamına gelmez, depolama sisteminde yapılan tüm değişiklikler kaydedilir.
- Daha detaylı incelemek içi,
	- https://en.wikipedia.org/wiki/Journaling_file_system


#### Other journaled Linux/UNIX filesystems

- **btrfs (CONFIG_BTRFS_FS)**, Linux için en aktif olarak geliştirimiş dosya sistemidir. Sayısız özellikler entegre edilmiştir. Data checksuming, valume managment, snopshot
- **XFS (CONFIG_XFS_FS)**, **SGI IRIX** den devralılan yüksek performanslı dosya sistemidir ve hala geliştirlimeye devam edilmektedir.
- **JFS (CONFIG_JFS_FS)**, **IBM AIX** devralınmıştır, Artık aktif olarak geliştirilmiyor, esas olarak uyumluluk için sağlanmıştır.
- **reiserFS(CONFIG_REISERFS_FS)**, eskiden popüler bir dosya sistemiydi, ancak en son **Reiser4** sürümü hiçbir zaman yukarı akışta birleştirilmedi.

- Tüm bu dosya sistemleri, Linux sistemleri için gerekli işlevleri sağlar: sembolik bağlantılar, izinler, sahiplik, cihaz dosyaları vb.


#### F2FS: Flash-Friendly Filesystem

- **CONFIG_F2FS_FS** ile konfigure edilir.
	- Flash tabanlı depolamanın özelliklerini dikkate alan dosya sistemidir.
		- **eMMC**, **SD Card**, **SSD**
	- Samsung tarafından geliştirilmiştir.
	- Artık şeffaf sıkıştırmayı (**LZO**, **LZ4**, **zstd**) ve şifrelemeyi destekliyor.
	- Optimum sonuçlar için, dahili depolama davranışı hakkında bir dizi ayrıntıya ihtiyacınız var hangisini elde etmek kolay olmayabilir
	- Flash tabanlı depolamada bile **ext4** ve **btrfs** kadar yaygın olarak kullanılmaz.

#### SquashFS: read-only filesystem

- **CONFIG_SQUASHFS** ile konfigure edilir.
	- Block cihazları için sadece okunabilir sıkıştırılmış dosya sistemleridir. Dosya sisteminin iyi parçalarını sadece okunabilir (Kernel, Binaries,....)
	- Genellikle gelişmiş okuma performansı sağlayan mükemmel sıkıştırma oranı
	- Çoğu canlı CD'de ve canlı USB dağıtımında kullanılır
	- Birkaç sıkıştırma algoritmasını destekler (**LZO**, **XZ**, vb.)
	- Karşılaştırmalar: ext3'ten kabaca 3 kat daha küçük ve 2-4 kat daha hızlı
		- https://elinux.org/Squash_Fs_Comparisons

#### Filesystem Seçimi Önemlidir 
- Bazı dosya sistemleri, onları nasıl kullandığınıza bağlı olarak diğerlerinden daha iyi çalışır.
- Örneğin, **reiserFS** birçok küçük dosyayı işleme konusunda en iyi üne sahipti.
- **ext2**, küçük bölümlerde ve az RAM'li sistemlerde harikadır.
- Neyse ki, uygulamalara karşı şeffaf olan dosya sistemlerini kıyaslamak kolaydır:
	- Depolamanızı her dosya sistemiyle biçimlendirin
	- Dosyaları oraya kopyalayın
	- Sistemi performans testine koyun
	- En iyi sistemi sizin için seçin
- Okuma/yazma bölümleri için iyi bir varsayılan seçim **ext4** olacaktır ve ardından deneyin Ekstra performansa ihtiyacınız varsa **btrfs** ve **f2fs**.

- Linux ayrıca, esas olarak aşağıdakilerle birlikte çalışabilir olmas ve diğer işletim sistemleri için birkaç başka dosya sistemi biçimini de destekler.
	- **vfat** (**CONFIG_VFAT_FS**), bu dosya sistemi Windows da çok sayıda dosyalar tarafından tanınmakta ve desteklemektedir,
		- Bootloader ikili dosyalarını depolamak için de uygundur (**ROM** kodu için **FAT**'ın anlaşılması kolaydır)
		- Bu dosya sistemi izinler, sahiplik, sembolik bağlantılar gibi özellikleri desteklemez. vb. Bir Linux root dosya sistemi için kullanılamaz.
		- Linux artık **exFAT** dosya sistemini de destekliyor (**CONFIG_EXFAT_FS**)
	- **nrfts**(**CONFIG_NTFS_FS**), Windows da kullanılan NFTS dosya sistemini destekler.
	- **hfs** (**CONFIG_HFS_FS**), MacOS da kullanılan HFS dosya sistemini destekler.


#### tmpfs: filesystem in RAM

- **CONFIG_TMPFS** olarak ayarlanabilir.
	- Bu bir blcok dosya sistemi değildir.
	- Geçici verileri RAM'de saklamak için mükemmel: sistem günlük dosyaları, bağlantı verileri, geçici dosyalar...
	- Ramdisklerden daha verimli alan: dosyalar doğrudan dosya önbelleğindedir, büyür ve saklanan dosyaları barındırmak için küçülür


### Using Block Filesystems

#### Creating ext2 / ext4 Filesystems

- Bir block cihazının içerisine veya var olan bir dosya sisteminin içerisinde oluşturmak için
	- **mkfs.ext2 /dev/sdb1**
	- **mkfs.ext4 /dev/sda3**
	- **mkfs.ext2 disk.img**
- Tüm dosyalarınızı içeren bir dizinden bir dosya sistemi görüntüsü oluşturmak için,
	- genext2fs, paketleme araçı kullanılabilir aynı isimde,
		- Bu sadece ext2 dosya sistemini destekler
		- **genext2fs -d rootfs/ rootfs.img**
		- İmage dosyası daha sonra blok cihazınıza aktarılmaya hazırdır.

#### Mounting filesystem images

- Bir dosya sistem döngü(**loop**) mekanizmasını kullanarak geliştirme iş istasyonundan içeriki imajı oluşturulduktan sonra, bir kişi imajına erişebilir ve imajını değiştirebilir.
- Örneğin,

>genext2fs -d rootfs/ rootfs.img
>mkdir /tmp/tst
>mount -t ext2 -o loop rootfs.img /tmp/tst

- /temp/tst klasöründen rootfs.img klasörüne erişim sağlanabilinmektedir.
- Bu bir dosyanın içeriğine sahip cihaz, bir bloğu taklit eden bir çekirdek sürücüsü olan döngü(**loop**) sayesinde mümkündür.
- Dosya sistemi görüntüsünü kullanmadan önce **umount**'u çalıştırmayı unutmayın!

	
#### Creating squashfs filesystems
![[Screenshot 2022-03-31 095236.png]]

#### Mixing read-only and read-write filesystems

![[Screenshot 2022-03-31 095253.png]]


#### Issues with flash-based block storage

- Flash depolama, yalnızca bir blok arabirimi aracılığıyla kullanılabilir hale getirildi.
- Bu nedenle, düşük seviyeli bir flash arayüzüne erişmenin ve Linux dosya sistemlerini kullanmanın hiçbir yolu yoktur.
- Kullandıkları katman (Flash Translation Layer) hakkında ayrıntı yok. Ayrıntılar şu şekilde tutulur: ticari sırlar ve kötü uygulamaları gizleyebilir.
- Bu cihazlara yazma sayısı , aşınma dengeleme algoritması hakkında bilgi sahibi olmadığınızdan, sınırlandırılması şiddetle tavsiye edilir.
- Endüstriyel sınıf depolama cihazlarının (**MMC/SD**, **USB**) kullanılması da önerilir.
- Ayrıca bakılabilir.
	- https://lwn.net/Articles/428584/
