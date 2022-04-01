## Linux Kernel in th System

![[Screenshot 2022-03-30 091754.png]]

### Linux Kernel Main Roles

- Tüm donanım kaynaklarının yönetimi, CPU, Memory, I/O
- Taşınabilir, mimari ve donanım bağımlı API, kullanıcı bölümü ve uygulamalarıyla kullacılara erişim sağlamaktadır.
- Eşzamanlı erişimleri ve kullanımı yöntemleri farklı uygulamalarda kullanılmaktadır.
	- Örnek olarak birden çok kullanıcının aynı alanlari belirli zamanlarda kaynak paylaşımıyla kullanması.

### System Calls
- Kernel ile kullanıcı alanı ile arayüz görevi gören sistem çağrıları bulunmaktadır.
- Ana Kernel içerisinde 400 e yakın sistem çağrıları bulunmaktadır
	- Dosya ve cihaz işlemleri, internet işlemleri, inter-process iletişimleri, işlemlerin yönetimi, hafıza haritalandırılması, zamanlayıcılar, iş parçacıkları, senkronizasyon gibi...
- Bu arayüz zamanla sabittir : yalnızca yeni bir sistem çağrıları kernel developers lar tarafından eklenebilir.


### Pseudo Filesystems
- Linux sistem ve kernel bilgilerinini kullanıcı alanında kullanabilir hale getirmektedir. Buna Sözde dosya sistemleri (**pseudo filesystems**) Bazen sanal dosya sistmleri olarak da adlandırılabilir. (**virtual filesystems**)
- İki çok önemli pseudo filesystem bulunmaktadır,
	- proc, genellikle /proc da bulunur ve işletim sistemi ile ilgili bilgileri saklamaktadır (işlemler, hafıza yönetimi vb)
	- sysfs, genellikle /sys de bulunur ve sistemin, veri yolları ile birbirine bağlanan bir aygıt ağacı olarak (**device tree**) temsil edilmesi. Bilgi bu cihazları yöneten çekirdek çerçeveleri (**kernel frameworks**) tarafından toplanır.


## Linux Kernel

![[Screenshot 2022-03-30 093142.png]]

### Supported Hardware Architectures

- Minimum 32 bit işlemciler, MMU olan veya MMU olmayan, gcc veye clang destekleyen
- 32 bit mimari
- **arm**, **arch**, **m68k**, **microblaze**
- 64 bit mimari
- **alpha**, **arm64**, **ia64**
- 32 / 64 bit mimariler
- **mips**, **powerpc**, **riscv**, **sh**, **sparch**, **x86**
- Kernel Kaynakları, 
- **arch/arch/Kconfig, arch/arch/README, veya Documentation/arch/**


## Kernel Configuration

- Kernel Configurasyonu nasıl bir kernel derlenmesi gerektiğine bağlı olarak sürekli olarak değişmeltedir.
	- Target da hangi mimarinin kullanıldığı ve donanımı
	- Kernelin neler yapabilmesi gerektiğine de bağlıdır (network yeterlilikleri, dosya sistemleri, real-time vb) Bunun gibi çoğu opsiyon tüm mimariler için gerçekleştirilebilr.
- Kernel konfigurasyonu ve derlenme sistemi çoklu **Makefiles** ler üzerinden yapılmaktadır.
- Kullanıcı en üst yapıda bulunan Makefile dosyası ile etkileşime girmektedir.
- Derleme işlemlerinin başlatılması için
> **make**
- Komutu çalıştırarak derlemeler başlatılır
- Derleme işlemlerinin başlatmadan önce konfigurasyon dosyalarının tamamlanması gerekmektedir.
- Hedef konfigurasyon dosyalarını görmek için
>**make help**


#### İlk olarak hedef mimari belirlenebilmelidir
- **ARCH** ile **/arch** klasöründe bununan bir mimari belirlenmeli,
>export ARCH=arm
- Default olarak, kernel derleme sistemi host makine için oluşturulduğunu varsaymaktadır.
- Kernel build system şu ayarları kullanacaktır,
	- Hedef mimari için konfigurasyon opsiyonlarını kullanır
	- Hedef mimari için kernel ile kaynak kodunu ve headers dosyalarını derler

#### Derleyici Seçimi

Derleyici Kernel Makefile içerisinde $(CROSS_COMPILER) gcc olarak seçilebilir.

- Derleyicinin belirtilmesi, yapılandırma zamanında zaten gereklidir, çünkü bazı çekirdekler yapılandırma seçenekleri, derleyicinin yeteneklerine bağlıdır.
- Belirlenmesi için
> export CROSS_COMPILE=arm-linux-gnueabi-


#### Konfigurasyon Detayları
- Belirlenen konfigurasyon seçenekleri kernel içirsinde root sistemde **.config** içersisinde bulunmaktadır.
- Konfigure etmek için belirli başlı kullanımlar mevcuttur,
	- make xconfig, make gconfig (Graphical)
	- make menuconflg, make nconfig (text)
- Diğer konfigurasyon dosyalarını düzenleyebilir ve yeniden yükyelebiliriz.

Zor kısımlardan bir tanesi ise kernel konfigurasyonunun kendi donanımımızada dosya sisteminin seçimidir.

- **arch/"arch"/configs/** içerisinde belirli üreticiler tarafından sağlanan konfigurasyon dosyaları bulunmaktadır. Bunlar kullanılarak sistemler geliştirilebilinmektedir.
- ARM 32 Bit de , genellikle CPU ailesine özel olarak tasarlanan configurasyon dosyaları bulunmaktadır.

#### Create Your Own Default Configuration
- Konfigurasyon dosyası seçildikten sonra

>make menuconfig

- Kısmında düzenleme yapıldıktan sonra default olarak **.config** dosyasına yazılmaktadır.
- Bu kısma yazıldıktan sonra kendi default conf dosyasını oluşturmak için,

	>make savedefconfig
	
	Ardından bu dosyayı,

> make defconfig arch/"arch"/configs/myown_defconfig

kaydedilerek daha sonradan kullanılabilinmektedir.

Daha sonradan kullanmak için

>make myown_defconfig

Kullanılarak dosya konfigurasyonu kendi yapımıza uygun olarak düzenlenebilir.


#### Kernel or Module

- **Kernel image** tek bir dosyadan oluşur, tüm nesne dosyalarının bağlanmasından kaynaklanan yapılandırmada etkinleştirilen özelliklere karşılık gelir.
	- Bu dosya **bootloader** tarafından **memory** ye yüklenmektedir.
	- Bu nedenle, dahil edilen tüm özellikler kernel başlar başlamaz dosya sisteminin olmadığı yerlerde her seferinde kullanılabilmektedir.
- Bazı özellikler (device drivers, filesystem vb) moduler olarak derlenebilmektedir.
	- Bunun nedeni özellikleri eklemek :/ kaldırmak gibi dinamik olarak yüklenebilmek / boşaltılabilmek için ekletilerdir.
	- Her bir module ayrı dosyalarda dosya sisteminde saklanmaktadır ve bu nedenle erişim modulleri kullanmak için bir dosya sistemine ihtiyac bulunmaktadır.
	- Kernel daha önceden yüknelebilmesi mümkün değildir, çünkü henüz dosya sistemi yoktur.

--> Kernel arasında bazı modullerin birbirine olarak bağımlıklıkları bulunabilmektedir.

![[Screenshot 2022-03-30 111047.png]]

### make oldconfig

- Çok sık olarak ihtiyac duyulabilinmektedir.
- Bir **.config** dosyasını elle düzenlerseniz, ayarlamak için daha sonra **make oldconfig** komutunu çalıştırmak yararlıdır. 
- Bağımlılık değişiklikleri nedeniyle ortaya çıkmış olabilecek yeni parametrelere değerler.


## Kernel Complitation

>make

Komutu kullanılarak derleme işlemi başlatılmaktadır.

> make -j8

Olarak çalıştırmak parallel olarak çalıştırmaya yaramaktadır. Ve faydası bulunmaktadır.

Kernel çıktıları şu şekilde olabilmektedir,
- **arch/<"arch">/boot/Image**, paketlenmemiş bootlanabilir kernel image çıktsıdır.
- **arch/<"arch">/boot/*Image***, paketlenmiş ve bootlanabilir kernel image çıktısıdır, 
	- **bzImage** 0x86 için,
	- **zImage** ARM için,
	- **Image.RZ** RISC-V için

- **arch/<"arch">/boot/dts/xxx.dtb**, Device Tree Blobs olark derlenir
- Tüm kernel modulleri, kernerl kaynak ağacında ayrı oarak tutulmaldır, **.ko(Kernel Objects)** dosyalarıdır.

### Kernel Instalation : Embbedded Case

> make install 

-  Gömülü geliştirmeden nadiren kullanılır. Kullanımı kolay ve tek bir kernel imagesi olarak tanınır.
- Diğer bir neden ise , kernel deploy etmenin standar bir yolunun olmamsıdır.
- Bu nedenle, çekirdek görüntüsünü hedef için kullanılabilir hale getirmek genellikle manuel veya yapı sistemlerindeki komut dosyaları aracılığıyla yapılır.
- make install davranışı aslında, **arch/<"arch">/boot/install.sh** dosyasını çalıştırmaktır.


#### Kernel Cleanup Targets
- Oluşturulan tüm dosyalar temizlenir(bazen sürüm değiştirilmeden önce kullınışlıdır, İstenmeyen dosyalardan kurtulmak için kaçınmak için yeniden derlemeyi zorlamak için kullanışlıdır.)

> make clean

- Oluşturulan tüm dosyaları kaldırır. Diğer bir mimariye geçiş yapıldığında kullanışlıdır. **.config** dosyasınında kaldırır.

>make mrproper

- Editör yedeğini ve redettme dosyalarını kaldırır. (Esas olarak yamalar.)

>make distclean


![[Screenshot 2022-03-30 132254.png]]



