
### /dev Managment

Linux Sisteminde , **/dev** klasörü altında belirli özel dosyalar bulunmaktadır. Bu dosyalar, *device files* olarak adlandırılır ve kullanıcı arabirimi uygulamarının Linux Kernel tarafından yönetilen donanım aygıtlarına erişim izni vermektedir.

Bu cihaz dosyaları olmadan, kullanıcı alanı uygulamaları, donanım aygıtları Linux Kernel tarafından düzgün bir şekilde tanınsalar bile çalışmayacaktır.

[[System Configuration-dev-managment.png]] görünebilmektedir.

**System Configuration** altında bulunan , */dev managment*, Buildroot /dev dizinini işlemek için dört farklı seçenek sunmaktadır.

- **Static Using device Table**
	- Bu Linux' da aygıt dosyalarını işlemenın eski klasik yoludur.
	- Bu Method ile aygıt dosyaları kalıcı olarak kök dosya sisteminde saklanır (yeniden başlatmalar boyunca devam eder) ve donanım cihazları eklendiğinde ve sistemden kaldırıldığında bu cihaz dosyaları otomatik olarak eklenir veya kaldırılır yani sonunda hiç bir şey donanum yoksa bulunmayaktır.
	- Bu nedenle Buildroot, bir cihaz tablosu kullanarak standar bir dosya seti oluşturur **system/device_table_dev.txt** içerisinde bulunmaktadır.
	- Bu dosya, Buildroot nihai dosyayı oluşturduğunda işlenir. root dosya sistemi görüntüsü ve bu nedenle aygıt dosyaları **output/target** dizininde görünmez.
	- **BR2_ROOTFS_STATIC_DEVICE_TABLE** seçeneği, Buildroot tarafından kullanılan varsayılan cihaz tablosunu değiştirmeye veya ek bir cihaz tablosu eklemeye izin verir. aygıt dosyaları, derleme sırasında Buildroot tarafından oluşturulur.
	- Bu yöntem kullanılır ise sisteminizde bir dosya eksikse örneğin :
		- **board/yourcompany/yourproject/device_table_dev.txt** şeklinde bir dosya oluşturarak device table oluşturulmalıdır.
		- Ek olarak cihaz dosyası açıklamasını içerir ve ardından **BR2_ROOTFS_STATIC_DEVICE_TABLE** şu şekilde ayarlanabilir
			- system/device_table_dev.txt board/yourcompany/yourproject/device_table_dev.txt.

- **Dynamic using devtmps only**
	- **devtmps** Linux Kernel çekirdeği içerisinde bir sanal dosya sistemi oluşturmaktadır. (2.6.32 versiyonundan sonrasında geçerlidir.)
	- /dev oluşturulduğunda, bu sanal dosya sistemi donanım aygıtları eklendikçe ve sistemden kaldırıldığında aygıt dosyalarının otomatik olarak görünmesini ve kaybolmasını sağlamaktadır.
	- Bu dosyasistemi yeniden başlatmalar arasında kalıcı değildir.
		- Kernel tarafından dinamik olarak sağlanmaktadır.
	- **devtmps** kullanılması için bazı seçeneklerin aktif hale getirilmesi gerekmektedir:
		- CONFIG_DEVTMPFS
		- CONFIG_DEVTMPFS_MOUNT
	- Buildroot ne zaman gömülü cihazımız için linux kernel derlemeye başladığında bu iki opsiyonunun etkin olduğunu kontrol etmektedir.
	- Ancak, Linux çekirdeğinizi Buildroot'un dışında kurarsanız, bu iki seçeneği etkinleştirmek sizin sorumluluğunuzdadır (eğer bunu yapamazsanız, Buildroot sisteminiz önyükleme yapmayacaktır).


- **Dynamic using devtmpfs + eudev** 
	- Bu yöntem ayrıca ayrıntılı olarak verilen devtmpfs sanal dosya sistemine de dayanır. ancak üstüne eudev kullanıcı alanı arka plan programını ekler.
	- **eudev** arka planda çalışan ve çağrılan bir arka plan programıdır. Bir aygıt sisteme eklendiğinde veya sistemden çıkarıldığında çekirdek tarafından bu program çağırılmaktadır.
	- **mdev** 'den daha ağır bir çözümdür, ancak daha yüksek esneklik sağlar. **eudev**, çoğu masaüstü Linux'ta kullanılan orijinal kullanıcı alanı arka plan programı olan **udev**'in bağımsız bir sürümüdür. Artık **Systemd** nin bir parçasıdır.

**Note**

Buildroot geliştiricilerinin önerisi, ihtiyacınız olana kadar **Dynamic using devtmpfs only** çözümünü başlamanızdır. Aygıtlar eklendiğinde/kaldırıldığında veya aygıt yazılımlarına ihtiyaç duyulduğunda kullanıcı alanının bildirilmesi için, bu durumda **Dynamic using devtmpfs** mdev genellikle iyi bir çözümdür.

Şununu unutulmamalıdır, init sistemi **systemd** seçilirse, **/dev** yönetimi **systemd** tarafından sağlanan **udev** programı aracılıyla sağlanmaktadır.