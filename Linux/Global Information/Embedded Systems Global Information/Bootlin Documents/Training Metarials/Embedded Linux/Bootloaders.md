## Bootloaders

### Boot Sequence

- Ön yükleyici şunlardan sorumlu olarak tanımlanan bir kod parçasıdır,
	- Basit Donanım initialization
	- Uyugunala binaryleri yüklenmesi, Genellikle flash depolamadan, network üzerinden, veya değer kalıcı depolama birimlerinden işletim sistemi çekirdeğinin yüklenmesi görevini görmektedir.
	- İkili uygulamanın dekompresyonu
	- Uygulamanın çalıştırılması

- Bu temel işlevlerin yanı sıra, çoğu önyükleyici çeşitli özelliklere sahip bir kabuk sağlar, farklı işlemler uygulayan komutlar.
	- İnternetten veya depolamadan data yükemek, hafıza inceleme, donanım teşhisi ve testi vb.

### Embeeded CPU's Booting
- İşlemci çalıştırıldığında sabit bir adreste başlamaktadır.
- **CPU** tarafından sağlanan başka bir önyükeme mekanizması bulunmamaktadır.
- Donanım tasarımı **NOR Flash** için kablolu olarak bağlanmış olup ve belirli adresten başlayabilmesi için komutları çalıştırabiliyor olması gereklidir.
- **NOR**'da İlk aşama önyükleyici bu adreste programlanmalıdır. 

![[Screenshot 2022-03-29 214622.png]]

- **NOR**' da zorunludur, çünkü CPU dan doğrudan erişme izin veririr (RAM gibi), fakat NAND **da** durum farklıdır orada izin verilmemektedir. Harici depolama yürütülmeden **RAM**'e kopyalanması gerekir.
- Daha çok **NOR** Flash kullanılmaktadır.


- Bazı işlemcilerde ise **CPU** içerisinde programlanamayan **ROM** hazfızada boot kodu bulunmaktadır.
	- **BootROM**, **AT91 CPUs** "ROM Code" OMAP olabilir.
- Boot kodu, firt stage bootloader depolama biriminden internal SRAM içerisine kodu yükleyebilmelidir (**DRAM** henüz başlatılmamıştır)
	- Depolama birimi tipik olarak, **MMC**, **NAND**, **SPI Flash,** **UART** olabilmektedir
- **First Stage Bootloader** is,
	- Donanım kısıtlamaları nedeniyle sınırlı boyutta olmalı
	- Herhanhangi bir **U-Boot** (**SPL** - Secondary Program Loader) desteklemeli veya CPU ürecisi desteklemelidir.
- **First Stage Bootloader** (**U-BOOT** vb.), **DRAM** ve diğer donanum aygıtlarının başlatmalı ve **DRAM** da ikinci bir bootloder bulabilir.


#### Microchip ARM SAMA5D3 Önyüklenmesi

![[Screenshot 2022-03-29 221540.png]]

- **RomBoot**, çeşitli kaynaklardan ön yükeme image bulmaya çalışır ve SRAM içerisinde yükler (DRAM henüz başlatılmamıştır.) 64 KB hafızası bulunmaktadır ve  kullanıcı buna herhangi bir müdahele edememektedir.
- **U-Boot SPL**, **SRAM** üzerinde çalışmaktadır. **DRAM** başlatılır, ve NAND veya SPI kontroller secondoray bootloader başlatılır **DRAM** içerisinde, kullanıcı etkileşimi yoktur.
- **U-Boot**, DRAM üzerimde çalışmaktadır, Diğer donanım cihazlarını başlatır. (network, USB, vb). Çekirdek Image dosyasını depolamadan veya networ üzerinden DRAM e yükler ve başlatırılır. Kullanıcı için shell komutları sağlanmaktadır.
- **Linux Kernel,** DRAM üzerinde çalışır, sistemi tamamen devralmaktadır(Bootloader artık mevcut değildir.)


#### Marvell SoCs Önyüklenmesi

![[Screenshot 2022-03-29 222004.png]]

- **ROM Code**, depolama kaynaklarından yüklenebilir bir image dosyası bulmaya çalışır, DRAM içerisinde yükler. **DRAM** yapılandırma, **CPU** spesicif header kısmında açıklamada bulunmuşlardır.
- **U-Boot**, **DRAM** üzerinde çalışır, değer donanım birimlerini(USB, network etc) gibi birimleri başlatır. Kernel image yi depolama birimlerinden veya network üzerinden DRAM e yükler.
- **Linux Kernel**, **DRAM** üzerinden çalışır, Sistemi tamamen kontrol eder. (artık ön yükleyici çalışmamaktadır.)


### Generic Bootloader for Embbeded CPUs

Birkaç açık kaynaklı bootloader lar bulunmaktadır. En popüler olanları şunlardır.

#### U-Boot
- ARM üzerinde en çok kullanılan, ayrıca PPC, MIPS, x86, RISC-V mimariilernde de çoğunlıkla kullanılmaktadır.

### Barebox
- Mimariden bağımsız olarak çalışan bir bootloaderdir.
	- U-Boot kadar donanım desteği henüz bulunmamaktadır.











