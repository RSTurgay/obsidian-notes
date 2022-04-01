##### Note
Herşeyi normal bir kullanıcı gibi oluşturabilir ve düzenleyebiliriz. Buildroot yapılandırmak için root olmasına gerek yoktur.

Tüm komutlar normal kullanıcı olarak çalıştırabiliriz.

**Buildroot Create Configuration**

- For ncurses interface

>make menuconfig

- For orginal curces-base configurator

>make nconfig

**Start Build Process**

Basitce derleme işlemini başlatmak için kullanılmaktadır.

>make

**make** komutu genel olarak şu adımları gerçekleştirir: 

- Gerekli olan kaynak kodunun indirilmesi
- Konfigurasyon, derleme ve indirme çapraz-derleyici için gerçekletirir veya sadece basitce harici derleyiciye gönderiri
- Seçilen hedef paketlerini indirir, konfigure eder ve derler.
- Eğer seçili ise kernel image derlenir.
- Eğer seçilirse bootloader image derlenir.
- Seçilen formatta root filesystem oluşturulur.

Buildroot çıktı dosyaları **output/.** klasöründe bulunmaktadır.

##### Output Klasörleri 

**images/**
- Tüm image dosyalarının bulunduğu yerdir. (kernel image, bootloader image, ve root filesystem image)
- Bunlar hedef sistemimize koymak için ihtiyacımız olan dosyalardır.

**build/**
- Tüm bileşenlerin, derlemede kullanılacak bileşenlerin derlendiği klasördür.
- Bu ana bilgisayarda Buildroot tarafından hedef için derlenen paketleri içermektedir.
- Bu dizin içerisinde bulunan paketlerin her birisi için alt dizinler bulunmaktadır.

**host/**
- Hem ana bilgisayar için oluşturulmuş derleme araçlarını hemde hedef toolchain için sysroot dosyalarını içermektedir.

**staging/**
- Geriye dönük uyumluluk için var olan **host/** içindeki hedef araç zinciri sysroot'una bir sembolik bağlantıdır.

**target/**
- Hedef için neredeyse tam kök sistemini içeren:
	- Cihaz hariç herşey mevcut /dev/ içindeki dosyalar (Buildroot onları oluşturamaz çünkü Buildroot root olarak çalışmaz ve root olarak çalışmak istemez).
	- **Bu nedenle bu dizin hedef için kullanılmalıdır.**
	- Bunun yerine **images/** dizininde bulunan image dosyaları kullanılmalıdır.
	- Eğer root filesystem NFS üzerinden başlatılacaksa bir extracted image dostasına ihtiyac duyulmaktadır. Bunun içinde tarball image derlenmesi için **images/** dosyasında derlenmiş olması gerekmektedir.

