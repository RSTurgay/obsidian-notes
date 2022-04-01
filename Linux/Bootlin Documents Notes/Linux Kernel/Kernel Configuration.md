- Kernel içerisinde binlerde device drivers, filesystem drivers, network protcols and birkçok düzenlenebilir elemanlar bulunmaktadır.
- Binlerce opsiyon kernel source code içerisinde bulunmaktadır.
- Kernel Konfigurasyonu kullanıcı tarafından ayarlanması gereken ve ayarlandıktan sonra derlenmesi gereklidir.
- Bazı opsiyonlar şunlara bağlıdır:
	- Hedef Mimari ve donanım
	- Çekirdekten beklenilen kapasite (Network Kapasitesi, dosya sistemi, gerçek zamanlı) gibi özellikler kapasiteyi göstermektedir.
- Kernel konfigırasyonu ver sistemi derlenmesi Makefiles dosyaları üzerinden ilerlemektedir.,
- Kernel kodunun en tepesinde bulunan bir tane makefile dosyası ile etkileşimde bulunulur.
- **make** komutu ile etkileşime girilerek ilerlemektedir.
- Örneğin:
	- cd linux/
	- make **target**


### Specifiying the Target Architecture
- İlk olarak derlenecek kernel mimari belirlenmeli.
	- export ARCH=arm (arm mimarisi)
	- Default olarak, kernel derleme sistemi ana bilgisayar için kernel derleme yapılacağını ve oluşturulacağını varsayar.
	- Kernel derleme sistemi şunları ayarlamak için kullanılabilir:
		- Hedef mimari opsiyonlarını ayarlamak için kullanılabilir.

### Chose Compiler
- Compiler kernel kodunu derlemek için kullanılmaktadır. ($CROSS_COMPILER) gcc 
- Derleyicinin belirtilmesi, yapılandırma zamanında zaten gereklidir, çünkü bazı çekirderkler yapılandırma seçenekleri, derleyicinin yetetenklerine bağlıdır.
- CROSS_COMPILE normalde tanımsız bırakılır ve gcc kullanılılarak çekirdek ana bilgisayar için yerek olarak derlenecektir.
- Cross-Compiler kullanılıldığında:
	-  Yerel bir derleyici le fark yaratmak için, çapraz derleyici yürütülebilir dosyaları hedef sistemim, mimarisinin ve bazende kütüphanesinin adına önüne eklenir.
	- Örneğin:
	- **mips-linux-gcc için -> mips-linux-gnueabi-gcc**
	- **arm-linux-gcc için -> arm-linux-gnueabi-gcc**

### Kernel Configurations Details
- Konfigürasyon dosyaları **.config** dosyasi içerisinde tutulmaktadır. Ve Kernel Soruce root klasöründe bulunmaktadır.
- Bağımlılıklara sahip olunduğunda grapfiksel olarakda da configurasyon kısmını derlemek geçerli olacaktdır.