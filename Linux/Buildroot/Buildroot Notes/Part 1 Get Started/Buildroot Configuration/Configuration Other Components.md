

#### Diğer Kompanentlerin Konfigurasyonu

Belirtilen bişelenşenlerin herhangi birisini konfigure etmeden önce Buildroot un kendisinden zaten yapılandırıldığından emin olmamız gerekmektedir.

Yani belirtilen paketin etkinleştirildiğinden emin olunması gibi.


### BusyBox

Halihazırda bir BusyBox yapılandırma dosyanız varsa,  BR2_PACKAGE_BUSYBOX_CONFIG bu dosyayı kullanarak doğrudan Buildroot yapılandırmasında belirtebilirsiniz. 

* Diğer yandan, Buildroot default BusyBox configuration file üzerinden derlenmeye başlayacaktır.

Konfigurasyon değişikliklerini yapmak için subcommand olan 

>make busybox-menuconfig

komutu kullanılarak düzenlemeler yapılabilmektedir.

* Bu önerilmese de bir ortam değişkeni aracılıylada BusyBox yapılandırma dosyasını belirtmek mümkündür.

BusyBox configurasyon dosyası path veriable şu şekilde olmalıdır :

>BUSYBOX_CONFIG_FILE=<path/to/.config>

BusyBox yapılandırma dosyasının yapılandırma arabiriminden de ayarlanabileceğini unutmayın, böylece Buildroot .config aracılığıyla dosya; bunu ayarlamanın önerilen yolu budur.


### uClibc
**uClibc**'nin konfigürasyonu, BusyBox ile aynı şekilde yapılır.

Mevcut bir konfigürasyonu belirtmek için konfigürasyon değişkeni dosya **BR2_UCLIBC_CONFIG**'dir.

Konfigurasyon değişikliklerini yapmak için subcommand olan 

>make uclibc-menuconfig


### Linux Kernel

Eğer elimizde hali hazırda bulunan bir Linux Kernel konfigurasyon dosyası var ise direk olarak Buildroot sistemine bunu tanıtabiliriz.

**BR2_LINUX_KERNEL_USE_CUSTOM_CONFIG** seçeneği ile harici bir Linux Kernel kullanmış oluruz.

Eğer elimizde hali hazırda bulunan bir Linux Kernel konfigurasyon dosyamız yok ise de **BR2_LINUX_KERNEL_USE_CUSTOM_DEFCONFIG** kullanabiliriz veya **BR2_LINUX_KERNEL_USE_CUSTOM_CONFIG** kullanarak direk olarak yeni bir konfigurasyon dosyası oluşturubiliri.z


### Barebox

**Barebox** LinuxKernel ile aynı yöntem ile konfigusayonları sağlanmaktadır.

BR2_TARGET_BAREBOX_USE_CUSTOM_CONFIG seçeneği ile harici bulunan veya yeni oluşturulan bir **Barebox** Buildroot sistemine dahil edilebilmektedir.

BR2_TARGET_BAREBOX_USE_DEFCONFIG seçeneği ile de Buildroot sisteminde defaul olarak bulundurulan konfigurasyon dosyası seçeneği belirtilebilinmektedir.

Konfigurasyon değişikliklerini yapmak için subcommand olan 

>make barebox-menuconfig


### U-Boot

**U-Boot** LinuxKernel ile aynı yöntem ile konfigusayonları sağlanmaktadır. (version 2015.04 veya daha yeni )

BR2_TARGET_UBOOT_USE_CUSTOM_CONFIG seçeneği ile harici bulunan veya yeni oluşturulan bir **U-Boot** Buildroot sistemine dahil edilebilmektedir.

BR2_TARGET_UBOOT_USE_DEFCONFIG seçeneği ile de Buildroot sisteminde defaul olarak bulundurulan konfigurasyon dosyası seçeneği belirtilebilinmektedir.

Konfigurasyon değişikliklerini yapmak için subcommand olan 

>make uboot-menuconfig
