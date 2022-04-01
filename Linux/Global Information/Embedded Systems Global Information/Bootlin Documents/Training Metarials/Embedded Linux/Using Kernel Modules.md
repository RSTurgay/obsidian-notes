
# Using Kernel Modules

![[Screenshot 2022-03-30 142655.png]]

- Kernel Modulleri çoğu farklı cihazlar ve kurulumlarda kullanılmaktadır.

- Modules, driver geliştirmede yeniden başlatma olmadan kullanımı kolaylaştırır,
	- load, test, unload, rebuild, ...
- **Kernel Image** boyununu minimumda tutmak her zaman kullanışlıdır.
- Ön yükmele süresini azaltmak için yararlıdır. Cihazları başlatmada yani ön yükleme süresince zaman harcanmaz sadece kullanıldığı yerde başlatılması gereklidir.

#### DIKKAT
	Kernek yüklendiksen sonra sadece root kök sistemi bunlara erişimi vardır, kaldurabilir düzenleyebilir. Normal kullanıcı bunları gerçekleştiremez.

- Güvenliği arttırmak için, sadece imzalı modullere izin verileblir veya modül desteği tamamen devre dışı bırakılabilir.


### Kernel LOG

- Yeni bir modül eklendiğinde, ilişkili bilgi kernel log tarafında saklanmaktadır.
- Kernel, mesajları dariresl bir arabellekte turat (Böylece fazla memory tüketimi bulunmaz.)
- Kernel Log mesajlarına **dmesg** ile ulaşılabilinmektedir. (**d**iagnostic **mes**sa**g**e)
- Kernel Log mesajları ayrıca konsol tarafında da görüntülenebilinmektedir.
	- Console mesajları **loglevel** kernel komutu kullanılarak filtrelenebilinmektedir.
	- Veya **quiet** ile de mesajları devre dışı bırakılabilmektedir.
	- Örnek olarak,
	
	>console=ttyS0 root=/dev/mmcblk0p2 loglevel=5


## Module Utilities

- **<"module-mane">.ko**, mudülleri olarak derlenmektir.
- **modinfo <"module-name">**, /lib/modules
- **modinfo <"module-name">.ko**, yüklenen bir modul hakkında bilgi alır.
- **sudo insmod <"module_path">.ko**, ile modulü yüklemek için kullanılır.
- Dikkat etmekde fayda vardır, bazen modulleri yükerken bazı hatalar ile karşılaşılabilnibmektedir.