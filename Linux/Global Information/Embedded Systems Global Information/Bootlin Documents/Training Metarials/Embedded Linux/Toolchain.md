
## Toolchain

- Toolchain oluşturmada tartışılırken üç makine birbirinden ayırt edilmeldiri.
	- **Build Machine**, toolchain build edildiği makine.
	- **Host Machine**, toolchain çalıştıran makine,
	- **Target Machine**, Binary Kodu oluşturulan ve çalıştırılacak makine.


#### Toolchain Farklı Derleme Prosedörleri

![[Screenshot 2022-03-29 210216.png]]

- **Native Build**
	- Normal gcc derlemesinin yapıldığı bölüm
	- Normal gcc kullanılarak ana makineye göre hazırlanır herşey
- **Cross Build**
	- En çok tercih edilen, derlemek için toolchain kullanılır ve host makinede çalışabilecek şekilde fakat binary kodları targete göre hazırlanır
- **Cross-Native Build**
	- Derleme toolchain kullanılılır hedefde çalıştırılır ve binary kodları hedefe göre hazırlanır.
- **Canadian Cross Buid**
	- A mimarisine göre derlenir
	- B mimarisine göre o toolchainde çalıştıırlır
	- C mimarisine göre de binary kodlar üretilir.


#### Cross Derlemede gcc Toolchain Bişenleri
- **Binutils**
	- CPU mimarisi için ikikikodları derlenmesi için (Genellikle .ELF formatı)
- Kernel Headers
- **C/C++ Libaries**
	- Genellikle GCC: GNU Compiler kullanılmaktadır.
- **C/C++ Compiler**
	- glibc, uClibc, musl, klibc, newlib
- **GDB Debugeer (Optional)**


### ABI
- Toolchain oluşturulurken, binary dosyalar oluşturmak için kullanılan ABI tanımlanmalıdır.
- ABI (Application Binary Interface), Uygulama İkili Arayüzü için, çağrı kurallarını tanımlar (nasıl fonksiyon argümanları iletilir, dönüş değeri nasıl iletilir, sistem nasıl çağırılır yapılır) ve yapıların organizasyonu (uyum vb.)
- Tüm binaryler tipik olarak anı ABI ve aynı kernel için derkenir, bu yüzden birbirlernii anlamaları gerekmektedir.
- **ARM** için iki adet **ABIs** bulunmaktadır , **OABI** ve **EABI**
	- Neredeyse herkes **EABI** kullanmaktadır.
- **MIPS** için bazıları,
	- **o32**, **o64**, **n32**, **n64**


### Floating Point Support

- Bazı işlemciler içierisinde floating point unit birimini bulundurur bazıları ise bulundurmaz.
	- Örneğin ARMv4 ve ARMv5 CPU içerisinde bulundurmaz
	- Fakat ARMv7 içeririsnde bulundurmak zorundadır.
- Floating Point Unit birimine sahip olan işleciler için, toolchain float kodlar üretmelidir ki işlemci direk olarak anlayabilsin
- Birimi olmayanlar içinde iki yöntem bulunmaktadır
	- Hard Float Kodu ürtetilerek sabir değerler elde edilir ve işlenciye bıraklılır. Çok yavaştır.
	- Soft Float Kodlar üretilerek, Floatit kodlar üretilmesi yerine bir kulanıcı kitaplığına yapılan çağrılar gerçek sayıyı oluşturur.

- Hangi kayan nokta biriminin kullanılacağını yapılandırmak da mümkündür

### Get Pre-Compiled Toolchain

Genellik tamamen yeniden bir toolchain derlemek yerine önceden derlenmiş toolchain kullanılması tavsiye edilmektedir.

Sadece gerekli koşulları yerine getiriyor olabilmesi gereklidir.

CPU support, endianness, C library, ABI, Soft Float vb

Genellike Crosstool-NG Kullanılmakta olabilir.



