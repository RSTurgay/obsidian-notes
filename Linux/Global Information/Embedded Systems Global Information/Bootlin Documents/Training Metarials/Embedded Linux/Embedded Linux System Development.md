# Embedded Linux System Development

## Leveraging open-source components in an Embedded Linux system

### Choosing components

- **Vitality ( Canlılık)**, Geliştirici ve kullanıcı topluluklarının canlılığı. Bu canlılık, bileşenin uzun vadeli bakımını ve nispeten iyi desteği sağlar. Posta listesi trafiğine ve sürüm kontrol sistemi etkinliğine bakılarak ölçülebilir.
- **Quality (Kalite)**, Bileşenin kalitesi. Tipik olarak, bir bileşen gömülü yapı sistemleri aracılığıyla zaten mevcutsa ve dinamik bir kullanıcı topluluğuna sahipse, bu muhtemelen kalitenin nispeten iyi olduğu anlamına gelir.
- **License (Lisans)**, Bileşenin lisansı, lisanslama kısıtlamalarınızla eşleşmelidir. Örneğin, GPL kitaplıkları tescilli uygulamalarda kullanılamaz.
- **Technical requirements (Teknik Gereksinimler)**, Elbette, bileşen sizin teknik gereksinimlerinize uygun olmalıdır. Ancak bir özellik eksikse mevcut bileşenleri iyileştirebileceğinizi unutmayın!

### Keeping changes separate
- Mevcut açık kaynak bileşenlerini projenize entegre ederken bazen bunlarda değişiklik yapmanız gerekebilir.
	- Better integration, reduced footprint, bug fixes, new features, etc.
- Bu değişiklikleri karıştırmak yerine, orijinal bileşen sürümünden ayrı tutmak çok daha iyidir.
	- Bileşenin yükseltilmesi gerekiyorsa, bileşende hangi değişikliklerin yapıldığını bilmek daha kolay
	- Topluluktan destek istenirse, kullandığımız bileşenin yukarı akış sürümünden ne kadar farklı olduğunu bilmek önemlidir.
	- Değişikliklere topluluğa geri katkıda bulunmayı mümkün kılar
- Belirli bir bileşen üzerinde yapılan çeşitli değişiklikleri ayrı tutmak daha da iyidir.
	- İncelemesi ve daha yeni sürümlere güncellemesi daha kolay
- En basit çözüm Quilt kullanmaktır.
	- Quilt, kaynak kod üzerinde bir yığın yama tutmaya izin veren bir araçtır.
	- Bir yamadaki değişiklikleri eklemeyi, kaldırmayı, yığından yama eklemeyi ve kaldırmayı ve bunları güncellemeyi kolaylaştırır
	- Yama yığını, sürüm kontrol sisteminize entegre edilebilir
	- https://savannah.nongnu.org/projects/quilt/
- Başka bir çözüm, bir sürüm kontrol sistemi kullanmaktır. (GIT)
	- Orijinal bileşen sürümünü sürüm kontrol sisteminize aktarın
	- Değişikliklerinizi ayrı bir dalda(Branch) koruyun


## Tools for the target device: Networking

### SSH Server and Client: Dropbear

- https://matt.ucc.asn.au/dropbear/dropbear.html

- Gömülü sistemler için çok küçük bellek ayak izi ssh sunucusu 
- Çoğu ihtiyacı karşılar. Hem istemci hem sunucu!
- Boyut: 204 KB, ARM üzerinde musl ile dinamik olarak derlenir (Bootlin musl toolchain ile Buildroot 2020.11)
- Useful to:
	- Hedef cihazda bir uzak konsol edinin
	- Dosyaları hedef cihaza ve cihazdan kopyalayın (scp veya rsync).
- Masaüstü ve sunucu sistemlerinde kullanılan OpenSSH'ye bir alternatif.


### Web Servers

- **BusyBox** *http server* : https://busybox.net ulaşılabilir
	- **Tiny**: sadece 20KiB dinamik olarak bağlanıtılı tüm özellikler etkin olur.
	- **CGI**, http kimlik doğrulama, komut dosyası desteği (PHP gibi, ayrı bir yorumlayıcı ile), ters proxy gibi web arayüzüne sahip birçok cihaz için yeterli özellikler...
- Diğer kullanlabilir seçenekler, **lightweight servers like Boa**, **thttpd**, **lighttpd**,**nginx**
- Bazı ürünler, kullanılabilecek kadar hafif olan Node.js'yi kullanır. low.js (https://github.com/neonious/lowjs) daha da hafiftir ve Linux'ta ve mikrodenetleyicilerde bulunur.

### Network Utilities
- **avahi**, programların yerel bir ağda hizmetleri yayınlamasına ve keşfetmesine olanak tanıyan bir Multicast DNS Service Discovery uygulamasıdır.
- **bind**, bir DNS serverdir,
- **iptables**, Linux güvenlik duvarı, Netfilter ile ilişkili kullanıcı alanı araçları
- **iw and wireless tools**, Kablosuz cihazlarla ilişkili kullanıcı alanı araçları
- **netsnmp**, SNMP protokolünün uygulanması (cihaz izleme)
- **chrony**, saat senkronizasyonu için Network Time Protocol uygulanması
- **openssl**, SSL ve TLS bağlantıları için bir araç takımı
- **pppd**, çevirmeli bağlantılar için kullanılan Point to Point Protokolün uygulanması
- **samba**,  Windows tarafından dosya ve yazıcıları paylaşmak için kullanılan SMB ve CIFS protokollerini uygular
- **coherence**, a UPnP/DLNA implementation
- **vsftpd**, **proftpd**, FTP servers

## Tools for the target device: System utilities

- dbus, uygulamalar arası nesne yönelimli bir iletişim veri yolu
- gpsd, GPS verilerini yorumlamak ve paylaşmak için bir arka plan programı
- libusb, çekirdek içi sürücü yazmadan USB aygıtlarına erişmek için bir kullanıcı alanı kitaplığı
- Utilities for kernel subsystems: 
	- **i2c-tools** , I2C için araç
	- **input-tool**, inputlar için araç
	- **mtd-utils**, MTD devicesler için arac
	- **usbutils**, USB devicesler için araç

## Tools for the target device: Language interpreters
- En yaygın komut dosyası dilleri için tercümanlar mevcuttur.
	- Application development
	- Web services development
	- Scripting
- Supported languages
	- **Shell** (bash, sh...)
	- **Lua**, easy to embed in C applications
	- **Python**
	- **Perl**
	- **Ruby**
	- **TCL**
	- **PHP**


## System Building

- **Hedef**,
	- Hem üçüncü taraf hem de şirket içi tüm yazılım bileşenlerini çalışan bir kök dosya sistemine entegre edin
	- Tüm bileşenlerin indirilmesini, çıkarılmasını, yapılandırılmasını, derlenmesini ve kurulmasını ve muhtemelen sorunların düzeltilmesini ve yapılandırma dosyalarının uyarlanmasını içerir.
- **Birkaç çözüm**
	- Manually
	- System building tools
	- Dağıtımlar veya hazır dosya sistemleri

### System building: manually
- Bu kısıma hiç bakmadım karışık ve yüksek seviye gerektriyor.
- embedded-linux-side sayfa sayıları 361 - 377


### System building tools

#### Principle
- Kernel ve bazen araç zinciri dahil olmak üzere bir hedef sistem oluşturma sürecini otomatikleştirmek için farklı araçlar mevcuttur.
- Bazen çapraz derleme sorunlarını gidermek için yamaları uyguladıktan sonra, tüm bileşenleri doğru sırayla otomatik olarak indirir, yapılandırır, derler ve kurarlar.
- Halihazırda ana gereksinimlerinize uyması gereken ve kolayca genişletilebilen çok sayıda paketi destekliyorlar.
- Derleme yeniden üretilebilir hale gelir, bu da bazı bileşenlerin yapılandırmasını kolayca değiştirmeye, yükseltmeye, hataları düzeltmeye vb. izin verir.

#### Available system building tools

Geniş araç seçenekleri mevcuttur

- **Buildroot**, topluluk tarafından geliştirilen
	- https://buildroot.org
	- Training Metarials
	- https://bootlin.com/training/buildroot/
- OpenWRT, başlangıçta kablosuz yönlendiriciler için bir Buildroot çatalı, şimdi daha genel bir proje
	- https://openwrt.org
- PTXdist, developed by Pengutronix
	- https://www.ptxdist.org
	- Benzer konfigürasyon arayüzü (menuconfig), ancak ilk başta kavraması biraz zor.
- **OpenEmbedded**, daha esnek ama aynı zamanda çok daha karmaşık
	-  https://www.openembedded.org
	- ve endüstrileşmiş versiyonu **Yocto Project**
	- Training Metarils
	- https://bootlin.com/training/yocto/

### Distributions

#### Debian

![[Screenshot 2022-03-31 220614.png]]

#### Others

![[Screenshot 2022-03-31 220711.png]]

#### Embedded distributions

Bunları nihai ürünler için kullanmasanız bile, hızlı bir şekilde demo yapmak için faydalı olabilirler.

- Alpine Linux: https://www.alpinelinux.org/
- Musl ve BusyBox tabanlı güvenlik odaklı dağıtım
- Supports x86 and arm, both 32 and 64 bit, plus ppc64 and s390
- Multiple types of downloads supported
- Standard version: about 130 MB
- Mini root filesystem: about 4 MB (without kernel)
- Other images: Raspberry Pi, Virtual, Xen, Generic ARM...


#### Application frameworks

- İndirebileceğiniz gerçek dağıtımlar değil. Bunun yerine, Linux çekirdeğinin üzerinde çalışan ve uygulamaların geliştirilmesine izin veren ara katman yazılımları uygularlar.
- Tizen: https://www.tizen.org/
	- Akıllı telefonları, giyilebilir cihazları (saatler), akıllı TV'leri ve Araç İçi Bilgi-Eğlence cihazlarını hedefleme. Büyük telefon üreticileri (çoğunlukla Samsung) ve operatörler HTML5 temel uygulama çerçevesi tarafından desteklenir.
	- https://en.wikipedia.org/wiki/Tizen
- Android: https://www.android.com/
	- Google'ın telefonlar, tabletler, TV'ler, arabalar için dağıtımı... Linux çekirdeği hariç, diğer Linux dağıtımlarından çok farklı bir kullanıcı alanı. Yine de hedef pazarlarında çoğunlukla başarılı.


