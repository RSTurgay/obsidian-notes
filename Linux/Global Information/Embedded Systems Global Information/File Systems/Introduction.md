# Intoduction 

Çoğu gömülü cihazlarda flash memory kullanılmaktadır.

Önemli faktörlerden bazıları,

- Size
- Bootup time

Bu nedenle, özel dosya sistemleri genellikle gelişmiş sıkıştırma veya dosyaları doğrudan flash'tan yürütme yeteneği gibi farklı özelliklerle kullanılır.


## MTD
Flash Memory bazem Linux Sistemlerinede **Memory Technology Device(MDT)** tarafından yönetilebilinmektedir.


## UBI

Linux kernelde bununan **Unsorted Block Images(UBI)** sistemi tek bir flash aygıtından birden çok mantıksal birimi yönetmektedir. **MDT** katmanı aracılıyla mantıksal bloklardan fiziksel silme bloklarına bir eşleme sağlar.

**UBI**, tüm flaş aygıtında aşınma dengelemeye izin veren esnek bir bölümleme konsepti sağlar.

## Partitioning

Linux Kernel sistemi en az bir tane **"root"** file system içermesi gerekmektedir. Bunun içerisinden birden fazla farklı sistemler içerebilir.

Gömülü olmayan sistemlerde, sık sık sadece tekli bir dosya sistemi bulunmaktadır. Ancak, sınırlı kaynakların optimizasyon sıralamaları (Flash, RAM, Processor Speed, boot up time) düşünüydüğünde,  birçok gömülü sistem, dosya sistemini ayrı parçalara böler ve her parçayı kendi bölümüne (genellikle farklı depolama türlerinde) koyar.

Örneğin, bir geliştirici sistemin tüm salt okunur dosyalarını almak ve bunları flash'ta sıkıştırılmış, salt okunur bir dosya sistemine koymak isteyebilir. Bu, bir miktar okuma zamanı performansı pahasına (açma için) flaşta en az alanı tüketecektir.

Başka bir yapılandırma, flash üzerinde sıkıştırılmamış olarak depolanan yürütülebilir dosyalara sahip olabilir, böylece yerinde yürütülebilirler, bu da RAM'den ve başlatma süresinden tasarruf sağlar (potansiyel küçük bir performans kaybıyla).

Yazılabilir veriler için, verilerin kalıcı olması gerekmiyorsa bazen bir ramdisk kullanılır. Performans gereksinimlerine ve RAM sınırlarına bağlı olarak dosya verileri sıkıştırılabilir veya sıkıştırılamaz.

Dosya sisteminin salt okunur ve okunur-yazılır kısımlarını serpiştirmek için tek bir standart yoktur. Bu, büyük ölçüde proje için kullanılan gömülü uygulamalar grubuna bağlıdır.

## eMMC and UFS

Flash bellekler büyüdükçe, çeşitli faktörler, ham NAND kullanımından gömülü aygıtlar için paketlenmiş, blok adreslenebilir NAND flash belleğe geçişe neden oldu.

Bunlar, dönen depolama ortamına (eski sabit disk sürücüleri) benzer şekilde blok G/Ç isteklerini kabul etmek ve bunları yerine getirmek için yerleşik bellenim içeren yongalardır.

Bu, okuma ve yazma isteklerini çipteki NAND flash alanlarına eşlemeyi ve flash belleğin doğruluğunu ve uzun ömürlülüğünü optimize etmeye çalışmak için NAND flash'ı yönetmeyi içerir.

NAND flash, tek tek dosya sistemi bloklarının birçok katı büyüklüğünde büyük bloklar (silme blokları) halinde yeniden yazılmalıdır.

Bu nedenle, sistemdeki blokların tahsisini haritalama, yeniden düzenleme ve çöp toplama yöntemi oldukça önemlidir.

Bu çipler, flash tabanlı dosya sistemi (örn. ext4) yerine blok tabanlı olarak çalıştırılır. 2012 itibariyle, ext4 dosya sistemini bu sistemlerle kullanım için optimize etmek, dosya sistemi araştırmalarının sıcak bir konu alanıdır.