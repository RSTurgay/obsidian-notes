## Gömülü Sistemlere Giriş

Linux Kernel ve çoğu mimari bağımlı kompananetler 32 bit ve 64 bit mimarileri desteklemektedir.
- x86, x86-64
- ARM
- RISC-V
- PowerPC
- MIPS
- ARC, x68k, XTensa...

Ve benzeri birçok mimari desteklenmektedir.

Hem **MMU** hemde **non-MMU** mimarilerini desteklemektedir, sadece non-MMU mimarisinde belirli kısıtlamalar bulunmaktadır.

- Toolchain, bootloader, kernel ve çoğu diğer kompanentler **mimariden bağımsız** olarak çalışabilen bileşenlerdir.

### RAM
Çok basit bir Linux işletim sistemi 8 MB lık bir RAM de çalışabilmektedirr Daha karmaşık ve gerçekci sistemlerde genellikle en az 32 MB RAM kapasitesi bulundurması gerekmektedir. Bu sadece uygulama bağımlık olarak düşünülebilir.

### Depolama
- Çok basit bir Linux işletim sistemi 4 MB kadar bir depolama alanında çalışabilmektedir. Fakat genellikle daha fazlasına ihtiyac bulunmaktadir.
	- **Block Storage (Blok Depolama)** : SD /MMC/ eMMC, USB Mass Storage, SATA vb.
	- Raw Flash Storage (Ham Flash Depolama) : Hem NAND hemde NOR Flash desteklenmektedir. Fakat özel belirli dosya sistemleri kullanılması gerekmektedir.

- RAM / Storage konusunda çok kısıtlayıcı olmak  gerekli değildir. Esnekli kullanabilirliği arttırmaktadır.

### Donanım Seçimindeki Kriterler
- Belirlenen SoC (System on Chip) zaten belirli bir Linux Kernel desteğinin bulunaması be açık kaynaklı bir Bootloader olması gerektiğine dikkat edilmesi gerekiyor.

- **Düzgün bir şekilde desteklenen Donanım, Linux Kernel geliştirmede ve maliyette fark yaratacaktır.**

## Embedded Linux System Arhitecture

![[Screenshot 2022-03-29 205148.png]]

