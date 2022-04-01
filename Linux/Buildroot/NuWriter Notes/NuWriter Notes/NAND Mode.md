### NAND Mode

Bu mod, NAND Flash a yeni bir image yazabilir ve image türünü belirleyebilir.

Bu tür bootloader veya Linux tarafından tanımlanabilir türlerdir. 

Bir image türü uBoot, Data, Environment veya PACK olabilir.

#### Image Type

#### 1. Loader Type

- Linux Sistemi için, Loader Type, Linux Kernel için önyükleme yapmak için kullanılmaktadır
- NUC980 U-Boot derlenen bir dosyadır. **U-Boot Main** ve **U-Boot SPL** dosyalarından oluşmaktadır.
- **Main U-Boot** tam özellikli U-Boot yapısını taşımaktadır. 
- **SPL U-Boot** ise daha az koda sahipdir ve **Main U-Boot** dosyasını DDR' da çalıştırmak için kullanılmaktadır.
- **SPL U-Boot** sadece **NAND/SPI NAND** ön yükleme için kullanılmaktadır.
- **SPI** or **eMMC/SD** önyükleme için sadece **Main U-Boot** gereklidir.
- SPL U-Boot default adresi 0x200 olarak tanımlıdır.
- Main U-Boot default adresi ise 0xE00000 olarak tanımlıdır.


#### No-OS System

İşletim Sistemi olmayan sistem için, NUC980 serisi açıldığında, Yükleyici tipinin görüntüsü bir ilk yürütme programıdır. Kullanıcılar, Loader tipi görüntü olarak ikili dosya oluşturmak için Keil derleyicisini kullanabilir. Yükleyici Türü görüntüsü, 32 baytlık başlık, DDR parametreleri ve ikili dosyadan oluşur.

#### 2.Data Type
- Temelde image in data türü NAND Flash adresinde özel olarak belirlenmelidir. Image başlangıç ofseti değerine bağlı olarak (sayfa boyutu sınırına göre hizalanmış, sayfa boyutu NAND belirtimlerine dayalıdır).
-  Image başlangıç offseti 0x10000 olarak eşitlenirse, veriler NAND Flash da 0x10000 adresinden başlar ve kullanıcı NAND Flash düzenlemede yardımcı olabilir.
- Data type YAFFS ve UBIFS olarak desteklenmektedir. Her iki dosya sistemi biçimi de veri türü olarak seçilebilir. Bu dosya sistemi, uboot veya Linux tarafından tanımlanabilir.

#### 3. Data Tpye

- Loader Type bölümündeki uboot environments veriables, image environment formatında olmaıdır ve NAND Flash a özel adrese yazılmalıdır. U-Boot bu ortan değişkenlerini okuyarak sistemi nasıl başlatacağını belirkemektedir.
- Eğer Image start 0x10000 den başlarsa, NAND Flash daki verlerin 0x10000 adresindeki verileri yapılandırmasına yardımcı olur.

### U-Boot Environment Example

env.txt

>baudrate=115200
>bootdelay=3
>ethact=emac 
>ethaddr=00:00:00:11:66:88 
>stderr=serial 
>stdin=serial 
>stdout=serial


#### 4.Pack Type
- Paket türü pack .image formatındaki dosyayı NAND Flash a yüklemek için kullanılmaktadır. Image in paket türü NAND Flashda belirli bir adresde olmalıdır. 
- Görüntü başlangıç ofsetinin değerine bağlı olarak (blok boyutu sınırına göre hizalanmış, blok boyutu NAND belirtimlerine dayalıdır).


### Operation Steps

#### 1. Add New Image

![[Pasted image 20220326213613.png]]
Figure 4-12 NAND – New Image

1. Select the “NAND” type, which will not list the pre-burned images in the NAND Flash ROM.
2. Fill in the image information：
	-  Image Name: Browse the image file
	- Image Type: Select the image type (only one type can be selected)
	- Image execute address: Enter image execute address. Only Loader Type is vaild.
	- Image start offset: Enter image start offset.
	- NAND Flash parameter: Check “User Defined” to pop up the window for configuring NAND parameters.
3. Click “Program” button.
4. Waiting for progress bar shows progress has been completed.
5. After the program is completed, click the “Verify” button to read back the image data to make sure the burning status.
6. 

![[Pasted image 20220326213626.png]]
Figure 4-13 NAND – Configure Parameters

1. Select the “NAND”.
2. Check “User Defined” to pop up the window for setting NAND parameter.
3. Fill the following parameters for NAND Flash using.
	-  Block Per Flash: Block size per NAND Flash.
	- Page Per Block: Page size per Block.
4. Click “OK” button to configure NAND Flash.


NuWriter, parametrelerini belirlemek için NAND Kimliğini otomatik olarak algılar ve kullanıcılar NAND parametrelerini NAND özelliklerine göre ayarlayabilir. Aşağıdaki adımlar, NAND parametrelerinin nasıl yapılandırılacağını açıklar.

![[Pasted image 20220326213816.png]]
Figure 4-14 NAND – Read

Refer to the Figure 4-14 to read data from NAND Flash.
1. Select the “NAND”.
2. Click “Read” button.
3. Click the “Browse” button to save the image file.
4. Set the values of start addres and block count.(Aligned on block size boundary, Block size is based on NAND Flash specifications).
	-  Start: Start addres of blocks
	-  Length: Length of blocks
5. Click “OK” button to read data.