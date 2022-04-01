### Configuration
Bahsedilen konfigurasyon :

>make menuconfig
>make nconfig

Yukarudaki gibi konfigurasyon komutlarını çalıştırarak başlatılan konfigurasyondan bahsedilmektedir. En önemli kısımdır.

### Cross-Compilation Toolchain

Derlemede kullanılacak toolchain konfigürasyon bölümüne [[Toolchain-Menuconfig.png]] kısmından erişebiliriz.

- Bir derleyiciden oluşur (gcc gibi), assembler ve linker gibi ikili araçlardan (binutils gibi) ve birde C standard Library (GNU Libc, uClib-ng gibi).

- Host machinede kurulu olan sistem kesinlikle bir derleme yapmak için kullanabileceğimiz bir araç zincirine sahiptir. Çoğu Linux sisteminde, derleme araç zinciri, C standardı olarak GNU libc yi kullanmaktadır.
- Derleme araç zinciri Buildroot ile bir alakası bulunmamaktadır. (Bir derleme oluşturmak için kullanılması dışında)
- Bu yüzden toolchain kısmında standardart **Buildroot toolchain** seçilebilir.

#### 1. Internal Toolchain Backend
Internal Toolchain backend hedef gömülü sistemi için user alanı uygulamaları ve kitaplıkları derlenmeden önceki çapraz derleme toolchaini Buildroot kendisi derlemektedir.

Backend supports severel C Libraries:
- uClibc-ng
- glibc
- musl

**Toolchain type belirlendikten sonra birkaç seçenek belirir ve bunların seçilmesi önemlidir.**

- **Kernel Headers**:
	- Araç zinciri oluşturmak için kullanılan Linux Çekirdek başlıklarını sürümünü değiştirin. Şöyle açıklanabilir.
	- Bir çapraz derleme araç zinciri oluşturma süreci için C kitaplığı derlenmektedir.
	- Bu kütüphane kullanıcı uygulamalarmı ve Linux Kernel arayüzleri arasında kullanılmaktadır.
	- Diğer bir yandan Linux Kernel ile nasıl konuşacağını bilmek için, arayüz ile kullanıcı dosyaları ve kernel (system calls, data structures, vb.), Linux Kernel Header (.h files) kullanmak için C kütüphanesine sahip olması gerekiyor.
	- Bu ara birim geriye gönük olarak uyumlu olduğundan, Linux Kernel Headers sürümü araç zinciri oluşturmak için kullanılani gömülü sistemde çalıştırmayı düşündüğümüz Linux Kernel sürümüyle tam olarak eşleşmesi gerekmez.
	- Yanlızca çalıştırmayı düşünülen Linux Kernel sürümüne eşit veya daha eski bir sürüme sahip olunması gerekmektedir.
- **C Libray**
	- GCC Derleyici, binutils, C Library verisiyonunu değiştirebiliriz.
- **uClibc Options**
	- Araç zincirinin RPC desteğine sahip olup olmayacağı
	- wide-char desteği
	- konumlandırma desteği
	- C++ iş parcacığı desteği

**Note**
Hangi seçeneğin belirli bir seçeneklere bağlı olduğunu görmek için Buildroot menulerinde görünen kullanıcı alanı uygulamaları ve kitaplıkların sayısı değişecektir.
Bir çok uygulama ve kitaplıklari, belirli araç zinciri seçeneklerini etlkinleştirilmesi gerektirir. Çoğu paket, belirli bir araç zinciri seçeneği olduğunu yorumda göstermektedir.

