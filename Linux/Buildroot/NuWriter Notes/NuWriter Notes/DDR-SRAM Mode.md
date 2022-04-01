### DDR/SRAM Mode

NUC980 Chip Linux Kernel ve non-OS BSP desteği sağlamaktadır. 
Kullanıcı programı DDR/SRAM yoluylada yazabilmektedir. 

Ek Bu modda kullanılacaksa olarak DTB Dosya Linux Kernek için gereklidir.

**DTB File Format**

Device Tree Blob formatı olarak adlandırılır. 

Bir DTB File yani device tree blob dosyası Linux Kernel Tarafından kullanılmaktadır. İçerisinde binary veriler bulunur ve hardware kısmını açıklamaktadır.

DTB Dosyaları işletim sistemine bilgisayarın hangi donanımın içerdiğini söyleyerek işletim sistemlerinini bir bilgisayarın bileşenlerine yönetmesine izim vermektedir

Linux işletim sistemini kullanan bir bilgisayar önyüklenirken, bilgisayarın önyükleyicisi, işletim sisteminin çekirdeğine bir aygıt ağacı iletir. Aygıt ağacı, bilgisayarın içerdiği donanım bileşenlerini tanımlayarak çekirdeğin bu bileşenlere erişmesine ve bunları yönetmesine izin verir. Bir aygıt ağacının tanımladığı bileşenlerden bazıları bilgisayarın CPU'su, belleği, veri yolları ve çevre birimleridir (bilgisayarın klavyesi gibi).

![[Pasted image 20220326214116.png]]

1. Select “DDR/SRAM”.
2. Click the “Browse” button to load the image file.
3. Input the image execution address. Note: All of DDR types, the last 1MB is not available.
4. Select “Download only” or ”Download and run”.
5. Click the “Download” button to program the DDR or SDRAM.