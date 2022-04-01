# Flash Storage And Filesystems


#### Block devices vs raw flash devices: reminder

- **Block Devices**:
	- Sabit boyutlu bloklar kullanarak rastgele veri erişimine izin verin
	- Medyaya yazarken özel dikkat gerektirmez
	- Blok boyutu nispeten küçüktür (en az 512 bayt, performans için artırılabilir sebepler)
	- Güvenilir olarak kabul edilir (depolama ortamı değilse, bazı donanım veya yazılım parçaları güvenilir hale getirmesi gerekiyordu)
- **Raw Flash Devices**:
	- Rastgele veri erişimine de izin ver
	- Medyaya yazmadan önce özel dikkat gösterin (yapmak üzere olduğunuz bölgeyi silmek üzerine yaz)
	- Silme, yazma ve okuma işlemleri aynı blok boyutunu kullanmayabilir
	- Güvenilirlik Flash teknolojisine bağlıdır

#### NAND Flash Chips: How They Work

- Bitler voltaj seviyelerine göre encodelanır
	- **SLC: Single Level Cell** - 1 bit per memory cell
	- **MLC: Multi Level Cell** - Multiple bits per cell
- Tüm bitler 1 ile başlar
- Yazma, bazı bitlerin 1'den 0'a değiştirilmesi anlamına gelir (hücre başına 1 bit varsayılarak) 
- Bitlerin 1'e geri yüklenmesi **ERASE** işlemi ile yapılır
- Yazma ve silme işlemi bit veya bayt bazında yapılmaz.
- Orhanizasyon,
	- **Page**: minimum unit for **PROGRAM** (write) operation. Example size: 4 K.
	- **Block**: minimum unit for **ERASE** operation. Example size: 128 K.

#### Organizasyon

![[Screenshot 2022-03-31 100854.png]]

#### NAND Flash Storage: Constraints

- Güvenilebilirlik(**Reliability**),
	- NOR Flash dan çok az daha güvenilirdir,
	- Güvenirliği NAND Flash teknolojisine bağlıdır (**SLC**, **MLC**)
	- Bit çevirmelerinden kurtarmak için mekanizmalar gerektir, **ECC** (Error Correcting Code)
- Ömür(**Lifetime**),
	- Diğer depolama ortamlarına kıyasla kısa kullanım ömrü
	- NAND Flash tenolojisine bağlı olarak (SLC, MLC) 1.000.000 ile 1.000 her blok için silme
	- Blokları eşit şekilde silmek için aşınma dengeleme mekanizmaları gereklidir
	- Kötü blok algılama/işleme de gerekli (**detection/handling**)
- **NAND** tarafından getirilen kısıtlamaların sayısına rağmen, çeşitli nedenlerle gömülü sistemlerde bunlar yaygın olarak kullanılmaktadır.
	- Diğer Flash teknolojilerinden daha ucuz
	- Yüksek kapasiteli depolama sağlayın
	- İyi performans sağlayın (hem okuma hem de yazma erişiminde)


#### NAND flash: ECC
- **ECC** (Error Correcting Code)
- **ECC**, **NAND** Flash'taki güvenilirlik sorununu kısmen ele alıyor
- Genellikle 512 veya 1024 baytlık parçalar üzerinde çalışır
- **ECC** verileri OOB **alanında** saklanır
- Algoritma,
	- **Hamming**: yığın başına tek bir bit düzeltebilir
	- **Reed-Solomon**: yığın başına birkaç bit düzeltebilir 
	- **BCH**: yığın başına birkaç bit düzeltebilir
- **** gücü, hangi sırayla OOB boyutuna bağlıdır, ECC verileri için ayrılan boyuta bağlıdır,
- **NAND** üreticileri, veri sayfalarında gerekli ECC gücünü belirtir:
	- Bu gereksinimlerin göz ardı edilmesi veri bütünlüğünü tehlikeye atabilir


## The MTD subsystem
- **MTD** standardı (**Memory Technology Devices**) olarak bilinir.
- Linux'ta olmayan her tür depolama ortamıyla ilgilenen genel alt sistemdir.
- Desteklenen media tipleri, **RAM**, **ROM**, **NOR Flash**, **NAND Flash**, **Dataflash**
- İletişişim arayüzünede bağımlıdır (**Parallel**, **SPI**, **Direct Memory Mapping** vs)
- Soyut depolama ortam özellikleri ve MDT ye erişmek için bisit bir API sağlar.
-  Kullanıcılara maruz kalan MTD cihaz özellikleri,
	- **erasesize**: minimum silme limiti
	- **writesize**: minimım yazma limiti
	- **oobsize**: meta verileri veya ECC verilerini depolamak için ekstra boyut
	- **size**: device size
	- **flags**: cihaz türü ve yetenekleri hakkında bilgi
- **MTD** Çeşitli "Kullanıcıları" vardır, 
	- Dosya türleri
	- Block cihaz ön yüklenmesi
	- Katmanlar
	- Kullanıcı alanı arayüzleri
	
	
	![[Screenshot 2022-03-31 103930.png]]


#### MTD partitioning

- **MTD** cihazları genellikle bölümlenir
	- Flashın farklı alanlarının farklı amaçlar için kullanılmasına izin verir.
		- read-only filesystem, read-write filesystem, backup areas, bootloader area, kernel area
- Kendi bölüm tablosunu içeren blok aygıtların aksine, MTD cihazları harici olarak tanımlanır
	- Karta olarak özelleştirilmiş Device Tree
	- Kernel koduna sabitlenmiş
	- Kernel Command ile özelleştirilmiş
- Her bölüm ayrı bir **MTD** cihazı olur
	- Blok cihaz etiketlemesinden farklı (**sda3**, **mmcblk0p2**)
	- **/dev/mtd1**, sistemdeki ikinci numaralandırılmış bölümdür
	- Note edilmelidirki Master MTD Cihazı **/dev** de gösterilmez.

####  Linux: Definition of MTD Partitions

- Device Tree, varsayılan MTD bölümlerini ve MTD aygıt özelliklerini tanımlamak için standard bir yerdir.

![[Screenshot 2022-03-31 110203.png]]


#### U-Boot: defining MTD partitions

- U-Boot, bunları bildirmek için Linux ile aynı sözdizimini kullanarak flash cihazlarda MTD bölümlerini tanımlamaya izin verir.
- Tanımların paylaşılması, Linux ve U-Boot arasındaki uyumsuzluk riskini ortadan kaldırmayı sağlar.
- Adlandırılmış bölümlerin kullanımı da daha kolaydır ve ofsetleri kullanmaktan çok daha az hataya açıktır.
- Flash'a özel komutları kullanın ve sayısal ofsetler yerine bölüm adlarını iletin
	- nand erase.part <"partname">

Örnek olarak,

![[Screenshot 2022-03-31 110602.png]]

- omap2-nand.o Cihazı 5 Bölümden oluşur,
	- **1st stage Bootloader** (512KiB, read-only)
	- **U-Boot** (1536 KiB, read-only)
	- **U-Boot Environment** (512 KiB)
	- **Kernel** (4 MiB)
	- **Root Filesystem** (Geriye Kalan Alan)

- Bölümlerin boyutları silme bloğu boyutunun katı olmalıdır. Boyutlar hexadecimal olarak da kullanılabilir.
- **ro**, bölümü salt okunur olarak listeler.
- **-** , kalan tüm alanı kullanmak için kullanılır.
- Şu boyutlar unutulmamalı,
	- **0x100 = 256**a
	- **0x1000 = 4 KiB**
	- **0x10000 = 64 KiB**
	- **0x100000 = 1 MiB**
	- **0x1000000 = 16 MiB**


- **mtdids** Bir U-Boot Flash Device adını Linux Device Name olarak ayarlanmaktadır.
	- **setenv mtdids <"devid">=<"mtdid">(,<"devid">=<"mtdid">)**
	
Bu yapılandırma Linux bölümlendirmesi gereklidir.
- *devid*: **U-Boot** cihaz tanımlayısı (form nand info)
- *mtdid*: Linux MTD tanımlayıcısı, Linux Kernel önyüklenirken ekranda gözükür

![[Screenshot 2022-03-31 114218.png]]

- **mtdparts**, farklı cihazlar için bölümler tanımlanabilmektedir.
	- **setenv mtdparts mtdparts=<"mtdid">:<"partition">(,partition)**
- U-Boot tarafından bölüm tanımlarınızın doğru anlaşıldığını kontrol etmek için **mtdparts** komutunu kullanabilirsiniz.

- İşte bölüm tanımlarını **U-Boot**'tan **Linux**'a geçirmenin önerilen bir yolu:
	- **bootargs_base**, bir ortam değişkeni oluşturulur,

		>setenv bootargs_base console=ttyS0 root=....
				
	- Ardından bootcmd ortam değişkeni arayıcıyla Linux Kernel Command line üzerinde tanımlanır,
	
> setenv bootcmd 'setenv bootargs ${bootargs_base} ${mtdparts}; <"rest of bootcmd">'



#### U-Boot: managing NAND devices

![[Screenshot 2022-03-31 114835.png]]

- Komut satırı üzerinde  nand cihazı kontrol edilebilir.

- **nand info**
	- NAND Cihazının karkteristik özelliklerini göstermektedir.

![[Screenshot 2022-03-31 114937.png]]

- **nand device [dev]**
	- NAND cihazının görüntülemek için kullanılır

![[Screenshot 2022-03-31 115054.png]]

- **nand read[.option] <"addr"> <"offset|partname"> <"size">**
	- NAND dan veri okunur

![[Screenshot 2022-03-31 115204.png]]

- **nand erase <"offset"> <"size">**
	- NAND Bölgesini siler

![[Screenshot 2022-03-31 115210.png]]

- **nand erase.part <"partname">**
	- NAND bölümünü siler

![[Screenshot 2022-03-31 115340.png]]

 - **nand write[.option] <"addr"> <"offset|partname"> <"size">**
	 - NAND yazmak için Boş sayfalara yazmaktan kaçınılmalıdır.

![[Screenshot 2022-03-31 115743.png]]

- **nand bad**
	- Kötü blockları gösterir

![[Screenshot 2022-03-31 115842.png]]


#### Linux: MTD Devices Interface With User Space
- **MTD** Cihazları **/proc/mtd** da görünmektedir.
- Kullanıcı alanı, bu bölümlerin altındaki flash aygıtı değil, yalnızca MTD bölümlerini görür (CONFIG_MTD_PARTITIONED_MASTER taki Kernel Tarafından seçilmedikçe)
- **mtdchar**, sürücüsü, sistemin her MTD aygıtı/bölümü için bir karakter aygıtı oluşturur.
	- **** olarak adlandırılır
	- **ioctl()** ,Flash ı silmeye ve yönetmeyi sağlar
	- **mtd-utils**, araçları kullanılır.


#### Linux: User Space Fash Management Tools
- **mtd-utils**, MTD cihazlarını manipüle etmek için bir dizi yardımcı programdır
	- **mtdinfo**, MTD cihazı hakkında bilgileri getirmektedir.
	- **flash_erase,** **flash_eraseall**, belirli bir MTD cihazını kısmen veya tamamen silmek için kullanılır.
	- **nandwrite**, NAND Flash a yazmak için
	- Flash dosya sistemi görüntü oluşturma araçları, **mkfs.jffs2**, **mkfs.ubifs**, **ubinize**, vs.
- İş istasyonunuzda: genellikle dağıtımınızda **mtd-utils** paketi olarak bulunur.
- Embedded targetta: çoğu komut artık BusyBox'ta da mevcuttur.


#### Flash Wear Leveling
- Aşınma dengeleme, gerçekten sık yazılan bloklarda maksimum silme döngüsü sayısına hızla ulaşmaktan kaçınmak için silmelerin tüm Flash aygıtına dağıtılmasından oluşur.
- Şunlar içinde yapılabilir,
	- Filesystem layer (**JFFS2**, **YAFFS2**, gibi)
	- Bir intermediate katmanı adanmış bir ara katmanı (**UBI**)
- Flaş ömrünü iyi veya kötü yapan şey aşınma dengeleme (Wear Levelling) uygulamasıdır.
- Flash kullanıcıları, ek önlemler alarak flash cihazlarının sınırlı kullanım ömrünü de dikkate almalıdır.
	- Flaş depolamanızı takas alanı olarak kullanmayın (zaten gömülü sistemlerde nadirdir)
	- Dosya sistemlerinizi mümkün olduğunda sadece okunur olarak bağlayın.
	- Geçici dosyaları RAM'de tutun (**tmpfs**)
	- Senkronizasyon bağlama seçeneğini kullanmayın (yazmayı hemen taahhüt eder). Dosya başına eşitleme için **fsync()** sistem çağrısını kullanın.
	
- 'Standart' dosya sistemleri, blok cihazlarda çalışmak içindir
- Flash kısıtlamalarıyla başa çıkmak için özel dosya sistemleri geliştirilmiştir
- Bu dosya sistemleri, flash çiplere erişmek için **MTD** katmanına güveniyor
- Belirli kullanımlar için faydalı olabilecek birkaç eski flash dosya sistemi vardır:
	- **JFFS2**, **YAFFS2**.
- Bu günlerde, **UBI/UBIFS** orta büyük kapasiteli **NAND** Flashlar için standartır.


### Legacy Flash Filesystems: JFFS2

- **Journaling Flash File System version 2 (JFFS2)** olark bilinmektedir.
- Anında sıkıştırmayı destekler
- Aşınma dengeleme, elektrik kesintisine dayanıklı
- Resmi Linux Kernelde mevcuttur
- Önyükleme süresi dosya sistemi boyutuna bağlıdır: önyükleme sırasında tüm depolama alanını taramak gerektiğinden büyük bölümler için iyi ölçeklenmez.
	- **CONFIG_JFFS2_SUMMARY**, bu sorunu çözülmesi için bunun etkinleştirilmesi gerekmektedir.
- Detaylı olarak incelemek için,
	- http://www.linux-mtd.infradead.org/doc/jffs2.html

![[Screenshot 2022-03-31 132106.png]]


### Legacy Flash Filesystems: YAFFS2

- **Yet Another Flash File System version 2 (YAFFS2)** olarak tanımlanır.
- Temel olarak NAND Flashlar desteklenmektedir.
- Sıkıştırmayı desteklemez.
- Aşınma dengeleme, elektrik kesintisine dayanıklı
- Hızlı ön yükleme süresi
- Resmi Linux çekirdeğinin bir parçası değil: kod yalnızca ayrı olarak mevcuttur (GPL olmayan işletim sistemleri için Çift GPLv2 / Ticari lisans)
- Detaylı bilgi için
- https://yaffs.net/

![[Pasted image 20220331132345.png]]


### UBI / UBIFS

- **Unsorted Block Images (UBI)**
- **JFFS2**' yi sınırlanmalarına değinerek değiştirmeyi amaçlamaktadır.
- Tasarım seçenekleri
	- Aşınma dengeleme ve dosya sistemi katmanlarını ayırın
	- Biraz esneklik ekleyin
	- Ölçeklenebilirlik, performans ve güvenilirliğe odaklanın
- Dezavantajı: dikkat çekici alan yükü sağlar, özellikle küçük cihazlarda veya bölmelerde kullanıldığında. **JFFS2**, küçük **MTD** bölümlerinde hala mantıklı.

![[Screenshot 2022-03-31 132629.png]]


- Detaylı incelemek için
	- http://www.linux-mtd.infradead.org/doc/ubi.html
- MTD cihazlarının üzerinde hacim yönetim sistemi (LVM'nin blok cihazlar için sağladığına benzer)
- Birden çok mantıksal birim oluşturmaya ve yazma işlemlerini tüm fiziksel bloklara yaymaya izin verir
- Silme bloklarını ve aşınma seviyelendirmeyi yönetmeye özen gösterir. Dosya sistemlerinin uygulanmasını kolaylaştırır
- Aşınma dengeleme, yalnızca tek tek bölmelerde değil, tüm depoda çalışabilir (güçlü avantaj)
- Bölümler dinamik olarak yeniden boyutlanabilir.


![[Screenshot 2022-03-31 133109.png]]


- Bir LEB'de çok fazla aktivite olduğunda, UBI onu daha düşük bir silme sayısına sahip başka bir PEB'ye taşımaya karar verebilir. Salt okunur ciltler bile aşınma dengelemeye katılır!

#### UBI: Intervals
- UBI, meta verilerini bant içinde saklıyor
- Her MTD silme bloğunda,
	- Silme döngülerinin sayısını saymak için bir sayfa ayrılmıştır
	- Silme bloğunu bir UBI birimine eklemek için başka bir sayfa ayrılmıştır
	- Kalan sayfalar, yük verilerini depolamak için kullanılır
- Cihaz alt sayfa yazmayı destekliyorsa, **EC** ve **VID** başlıkları aynı sayfada saklanabilir![[Screenshot 2022-03-31 133542.png]]


#### UBI : Good Practice

- UBI, flash aygıtın her yerine silmeleri dağıtmaktan sorumludur: UBI katmanına bağlı bir bölüme ne kadar fazla alan atarsanız, aşınma dengeleme o kadar verimli olur
- Bölümlemeye ihyiyaç varsa , **UBI** birimleri (Volumes)kullanılır , **MTD** kullanılmaz.
- Bazı bölümlerin yinede MTD bölümlerinden oluşması gerekiyor (bootloader vs)
- **U-Boot** artık ortamını bir **UBI** biriminde depolamayı bile destekliyor !
- Fazladan **MTD** bölümlerine ihtiyacınız varsa, bunları flash aygıtın sonunda veya başında gruplandırmayı deneyin (genellikle daha güvenilir alanlar).

#### UBI layout: Bad Example

![[Screenshot 2022-03-31 134033.png]]

#### UBI layout: Good Example

![[Screenshot 2022-03-31 134121.png]]


### UBIFS
- **Unsorted Block Images File System (UBIFS)**
- Bu dosya sistemi UBI/UBIFS birer çiftir.
- UBI Volume kısımlarında çalışır
- JFFS2'den daha iyi performans sağlayan ve ölçeklenebilirlik sorunlarını ele alan günlük kaydı dosya sistemi
- http://www.linux-mtd.infradead.org/doc/ubifs.html
- http://www.linux-mtd.infradead.org/doc/ubifs_whitepaper.pdf

#### Linux : UBI Host Tools
- **ubinize**, UBI katmanı için tek ana bilgisayar aracıdır
- Birimlerinin içeriğinin bir belirtiminden, bir MTD bölümünde flaşlanacak bir UBI görüntüsü oluşturur:

![[Screenshot 2022-03-31 140105.png]]

- Her bir sektör UBI Volume (Birim) leri olarak tanımlanır
- **static** volumes, salt okunur veri bloblarını depolamak ve karşılık gelen minimum boyutu elde etmek içindir. Üzerlerinde CRC kontrolleri yapılır.
- read-only UBIFS dosya sistemi **statik** bir birime gidebilir, ancak bu durumda **dinamik** birimler performans için en iyisidir (CRC denetimi UBIFS düzeyinde de yapılır).
- autoresize, kalan tüm **UBI** alanını doldurmaya izin verir

![[Screenshot 2022-03-31 140752.png]]


#### ubinize command line

- **ubinize**, komutunu şu argumanlar takip eder,
	- **-o <"output-file-path">**, Path to the output image file
	- **-p <"peb-size">**, The PEB size (MTD erase block size)
	- **-m <"min-io-size">**, The minimum write unit size (MTD write size)
	- **-s <"subpage-size">**, Alt sayfa boyutu, yalnızca hem flash'ınız hem de flash denetleyiciniz alt sayfa yazma işlemlerini destekliyorsa gereklidir
	- Son argüman, **ubinize** yapılandırma dosyasının yoludur.
- Örneğin, 
- **ubinize -o ubi.img -p 256KiB -m 4096 -s 2048 ubinize.cfg**


#### U-Boot : UBI Tools

- UBI komutlarnının altında konumlandırılmış,
	- **ubi part <"part-name">**, **UBI** katmanına bir **MTD** bölümü ekleyin,
		- Örneğin, **ubi part UBI**
	- **ubi info [layout]**, Display UBI device information, (veya volume bilgisi gözükür, layout bilgisi pas geçilebilir.)
	- **ubi check <"vol-name">**, Berlirli bir bölümün olup olmadığını kontrol eder.
	- **ubi readvol <"dest-addr"> <"vol-name"> [<"size">]**, Volume içeriği okunur
			- Örneğin, **ubi readvol 0x21000000 kernel**

- U-Boot Komutları içerisinde UBI Volumeleri düzenlenebilir
	- **ubi createvol <"vol-name"> <"size"> <"type">**, Yeni bir volume oluşturulur.
		- Örneğin, **ubi createvol initramfs 0x100000 static**
	- **ubi removevol <"vol-name">**, Var olan bir volume kaldırılır
	- **ubi writevol <"src-addr"> <"vol-name"> <"size">**, Write to volume
		Örneğin,  
			**tftp 0x21000000 data.ubifs** 
			**ubi writevol 0x21000000 data ${filesize}**

- U-Boot ayrıca UBIFS partitions dar erişebilir:
	- **ubifsmount <"volume-name">**, Belirtilen bir volume bağlanır
		- Örneğin, **ubifsmount root**
	- **ubifsload <"addr"> <"filename"> [bytes]**, Berlirli bir adrese içeriği yükler
		- Örneğin, **ubifsload 0x21000000 boot/zImage**
	- Diğer komutlar, **ubifsls** ve **ubifsumount**

Bu, alanı optimize etmek için kernelin ve DTB'nin bir UBIFS bölümünde de saklanabileceğini gösterir. Aksi takdirde, DTB birimi bir LEB'nin tamamını tüketir.

#### Linux : UBI Target Tools

- UBI öğelerini dinamik olarak oluşturmak ve değiştirmek için hedefte kullanılan araçlardır

- UBI Device Managment,
	- **ubiformat /dev/mtdx**,  MTD partition a formatlanır ve eğer varsa Erase Counter Information korunur
		- Örneğin, **ubiformat /dev/mtd1**
	- **ubiattach -m <"MTD-device-id"> /dev/ubi_ctrl**, UBI katmanına bir MTD bölümü/cihazı ekleyin ve bir UBI cihazı oluşturun,
		- Örneğin, **ubiattach -m 1 /dev/ubi_ctrl**
	- **ubidetach -m <"MTD-device-id"> /dev/ubi_ctrl**, UBI katmanından bir MTD bölümü/cihazını ayırın ve ilgili UBI cihazını kaldırın
		- Örneğin, **ubidetach -m 1 /dev/ubi_ctrl**
	- **ubinfo -a**, UBI devices ve volumes hakkındaki tüm bilgileri yazdırır.

- UBI Volume managment,
	- **ubimkvol /dev/ubi<"UBI-device-id"> -N <"name"> -s <"size">**, Create new volume. Kalan tüm alanı bu birime atamak istiyorsanız **-s <"size">** yerine **-m** kullanın.
		- Örneğin, **ubimkvol /dev/ubi0 -N varlog -s 16MiB**
	- **ubirmvol /dev/ubi<"UBI-device-id> -N <"name">**, Bir UBI volume siler
		- Örneğin, **ubirmvol /dev/ubi0 -N varlog**
	- **ubiupdatevol /dev/ubi<"UBI-device-id">_<"UBI-vol-id"> (-s <"size">) <"img-file">**, Volume içeriğini günceller
		- Örneğin, **ubiupdatevol /dev/ubi0_1 rootfs.img**
	- **ubirsvol /dev/ubi<"UBI-device-id"> -N <"name"> -s <"size">**, UBI Volume yeniden boyutlandırır
		- Örneğin, **ubirsvol /dev/ubi0 -N varlog -s 32MiB**
	- **ubirename /dev/ubi<"UBI-device-id"> <"old-name"> <"new-name">**, UBI Volume yeniden isimlendirir
		- Örneğin, **ubirename /dev/ubi0 varlog var**
	
#### Linux tools: BusyBox UBI limitations

- Bazı UBI Commands BusyBox üzerinde desteklenmemekte olabilir.
	- **ubiformat**, **ubinfo**, henüz implemente edilmedi
	- vs...


#### Linux : UBIFS Host Tools

Detaylı araştırma için https://elinux.org/UBIFS

- **mkfs.ubifs**, UBIFS dosya sistemi imagesi kullanılarak oluşturulabilir.
	- **mkfs.ubifs -m 4096 -e 258048 -c 1000 -r rootfs/ ubifs.img**
		- **-m 4096**, minimal I/O size
		- **-e 258048**, lojik block silme boyutu (PEB boyutundan daha küçük, ubiattach çalıştırıldıktan sonra çekirdek günlüğünde bulunabilir)
		- **-c 1000,** Mantıksal silme bloklarının sayısı olarak, görüntünün flaşlanacağı UBI hacminin maksimum boyutu.Bu sayıyı gereksiz yere büyütmeyin, aksi takdirde UBIFS veri yapıları gerekenden daha büyük olacak ve performans düşecektir.

- Bir kez oluşturulduktan sonra, Linux'ta **ubiupdatevol** veya** U-Boot**'ta **ubi writevol** kullanılarak hedeften bir **UBI** birimine yazılabilir.
- Veya bir **UBI** görüntüsüne dahil edilebilir (ana bilgisayarda **ubinize** kullanılarak)


### Linux : UBIFS Target Host Tools

- UBIFS dosya sistemi oluşturmak için özel araçlar gerekmez. İlk kez takıldığında boş bir dosya sistemi oluşturulur. Aynısı JFFS2 için de geçerlidir.
- Bir **UBIFS** dosya sisteminin montajı **mount** ile yapılır:
	- **mount -t ubifs <"ubi-device-id">:<"volume-name"> <"mount-point">**
	- Örneğin, **mount -t ubifs ubi0:data /data**


### Linux: UBI image creation workflow

![[Screenshot 2022-03-31 151315.png]]


#### Linux: Using a UBIFS Filesystem as Root Filesystem

- Çekirdek komut satırına aşağıdaki bilgileri iletmeniz yeterlidir:
	- **ubi.mtd=UBI**,
		-  **UBI** adlı **MTD** bölümünü **UBI** katmanına ekleyin ve **ubi0** oluşturun.
		-  **MTD** bölüm numarasını da kullanabilirsiniz (hataya daha yatkın): **ubi.mtd=1**
	- **rootfstype=ubifs root=ubi0:rootfs**, Rootfs birimini ubi0'a bir UBIFS dosya sistemi olarak bağlayın
	- **rootfstype=**,  çekirdeğe hangi dosya sisteminin kök dosya sistemi olarak bağlanacağını bilmesini sağlar. UBIFS için zorunludur, ancak blok dosya sistemleri için de kullanılabilir. Bu şekilde, çekirdeğin desteklediği tüm dosya sistemlerini denemesi gerekmez. Bu, önyükleme süresini azaltır.
	- Örneğin,
		- **rootfstype=ubifs ubi.mtd=UBI root=ubi0:rootfs**


## Summary: How To Boot on a UBIFS Filesystem

### In U-Boot
- Bölümleri tanımlayın
	- **setenv mtdids ...
	- **setenv mtdparts ....**
- UBIFS'de önyüklemeyi, kök dosya sistemi olarak kullanılan UBI birimini ve UBI'ye bağlı MTD bölümünü belirterek temel Linux çekirdeği önyüklemelerini tanımlayın.
	- Örneğin,

>  setenv bootargs_base console=ttyS0 rootfstype=ubifs root=ubi0:rootfs ubi.mtd=UBI

- Önyükleme komut sırasını tanımlayın, UBI'yi başlatın, UBI bölümlerinden çekirdek ve DTB görüntüleri yükleyin ve çekirdek komut satırına **mtdparts** ekleyin.
	- Örneğin

> setenv bootcmd 'ubi part UBI; ubi readvol 0x81000000 kernel; ubi
   readvol 0x82000000 dtb; setenv bootargs ${bootargs_base}
   ${mtdparts}; bootz 0x81000000 - 0x82000000'

##### Linux: Block emulation layers
- Bazen mevcut blok dosya sistemlerini, özellikle SquashF'ler gibi salt okunur olanları yeniden kullanmak için blok cihazlarına ihtiyacımız var.
- Linux iki blok öykünme katmanı sağlar:
	- **mtdblock:**, MTD cihazlarının üzerinde taklit edilen cihazları engelle
	- **ubiblock:**, UBI birimlerinin üzerine öykünülmüş salt okunur (**read-only**) blok cihazları


#### Linux: mtdblock

- **mtdblock** katmanı, sistemin her MTD cihazı için bir blok cihazı oluşturur.
- Genellikle adı /dev/mtdblockX dir
- Okuma/yazma blok düzeyinde erişime izin verir. Ancak hatalı bloklar işlenmez ve yazma işlemleri için aşınma dengelemesi yapılmaz.
- Tarihsel nedenlerle, **JFFS2** ve **YAFFS2** dosya sistemleri, mount komutu için bir blok aygıtı gerektirir.
- **mtdblock cihazlarda yazmayın**

#### Linux: ubiblock
- UBI mevcutsa **mtdblock** yerine tercih edilir (UBI, veri saklama ve aşınma seviyelendirme sorunlarını hesaba katar, ancak MTD yapmaz)
- **ubiblock** katmanı, isteğe bağlı olarak salt okunur blok cihazları oluşturur
- Kullanıcı, **ubiblock**'a hangi statik birimleri eklemek istediğini belirtir.
- Çekirdek komut satırı aracılığıyla: geçerek
- **ubi.block=<"ubi-dev-id">,<"volume-name">**
- Örneğin
	- **ubi.block=0,rootfs**
- Linux'ta, **mtd-utils** tarafından sağlanan **ubiblock** yardımcı programını kullanarak:
	- **ubiblock --create <"ubi-volume-dev-file">**
	- Genellikle **/dev/ubiblockX_Y** olarak adlandırılır; burada **X**, **UBI** aygıt kimliğidir ve Y, UBI birim kimliğidir (örnek: **/dev/ubiblock0_3**)


Son olarak detaylı olarak incelemek gerekebilir.

- http://www.linux-mtd.infradead.org/
-  https://en.wikipedia.org/wiki/Error_correction_code
