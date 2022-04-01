# Booting Kernel

## Device Tree
- Çoğu gömülü mimariler keşfedilmemiş donanımlara sahiptirler (Serial, I2C, SPI, Nand Flash, USB Controller)
- Mimariye bağımlı olarak, **Device Tree** Dilini kullanarak C kodunu direk kernel içerisine aktarmaktadır.
- **Device Tree (DT)** **PowerPC** tarafından oluşturuldu ve ve diğer mimariler için (**ARM**, **ARC**)uyumlu hale getrilen bir sistemdir.
- Buradaki ana amaç, donanım entegrasyonunu tanımlamaktır, kefşedilmemiş donanımların tanımlanması, clock, interrupts, DMA Channels, pin muxing vb. 
- Bir **Device Tree Source (DTS)**, kernel geliştirileri tarafından yazılmış, **Device Tree Blob(DTB)** içerisinde derlenmiş ikili kodda bulunan ve ihtiyac halinde boot içerisinde kullılmaktadır.
- Kernel tarafından desteklenen her bir platform için tasarlanan yapılar bulunmaktadır.
	- **arch/arm/boot/dts/<"board">.dtb**.
- Bootloader, kernel başlatılmadan önce **DTB File** ve **Kernel Image** dosyasını RAM a yüklemesi gerekmektedir.
- Bu yol, bir çekirdek tarafından farklı SoC lar tarafından desteklenmektedir.

### Costimize Your Board Device Tree
Kart geliştiriciler tarafından kullanılacak bit yöntem (YANİ BİZ)

- Bağlı cihazları tanımlamak için keşfedilmeyen veri yollarını yapılandırılması gerekmektedir.(I2C, SPI vs)
- Bazı sistem parametlerini, flash partionlarını, kernel commandlarını configure etmek için kullanılır
- Bu pdf detaylı olarak incelenmesi gerekmektedir.
- ![[petazzoni-device-tree-101.pdf]]

## Booting U-Boot

- U-Boot direk olarak zImage Kermel Image dosyasını önyükleyebilir.
- Kernel Image ek olarak,  U-Boot a DTB dosyasıda iletilmesi gerekmektedir.
- Yani genel olarak yapı şu şekilde oluşturulabilir.
	- **zImage**, RAM'de X adresine yükle
	- **<"board">.dtb**, RAM'de Y adresine yükle
	- **bootz X - Y** komutu ile Kernel başlatılır
		- Buradaki "-" işareti initramfs olmadığını göstermektedir.


### Kernel Command Line 
- Derleme zamanı yapılandırmasına ek olarak Kernel Behavior (davranış) ı da konfigure edilebilinmektedir. Yeniden derlemeye gerek kalmadan **Kernel Command Line** kullanılabilir.
- Kernel Command Line, çeşitli argümanları tanımlayan bir dizedir.
	- Bu sistem konfigurasyonu için çok önemlidir.
	- root=, kök dosya sistemi tanımlanmaktadır.
	- console=, Kernel mesajlarını yayınlayabileceği arayüzü belirlemekte kullanılmaktadır.
	- Örneğin,
	> console=ttyS0 root=/dev/mmcblk0p2 rootwait
	
	Daha detaylı incelemek için,
	https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html


#### Passing The Kernel Command Line

- Daha iyi anlaşılması için incelenmelidir.

![[opdenacker-understanding-u-boot-falcon-mode.pdf]]

- U-Boot, **bootargs** ortam değişkeni değerini **Kernel Command Line** içeriside taşır.
- Kernel başlatılmadan hemen önce, **bootargs** içerisinde belirtilen **Device Tree** section depolanacaktır.
- Kernel, yapılandırma işlevine bağlı olarak farklı davranacaktır, 
	- **CONFIG_CMDLINE_FROM_BOOTLOADER** olarak yapılandırılırsa, kernel sadece önyükleciden gelen dizeyi kullanmaktadır.
	- **CONFIG_CMDLINE_FORCE** olarak yapılandırılırsa, **CONFIG_CMDLINE** gelen yapılandırma süresini kullanmaktadır.
	- **CONFIG_CMDLINE_EXTEND** çekirdek her iki dizeyi de birleştirecektir.