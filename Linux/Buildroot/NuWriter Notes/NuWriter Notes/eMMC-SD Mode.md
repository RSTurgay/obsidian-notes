### eMMC/SD Mode

Belirtilen kart üzerindeki sokete sd kart üzerinden flashlamak için kullanılır.

### Image Type

![[NAND Mode#1 Loader Type]]

#### 2. Data Type

Genel olarak image data dosyası (Image) eMMC/SD özel adresine yazılır. 
- Image başlangıc ofstitin değerine bağlı olarak (512 bayt sınırına göre hizalanır)
- Eğer image start offset 0x10000 ise veri eMMC/SD içerisindeki 0x10000 adresine yazılır ve kullanıcı bunu düzenleyebilir.

#### 3.Environment Tpye

Tür olarak environment türünde olmalıdır.

#### 4. Pack Type

Paket tipi, paket görüntüsünü eMMC/SD'ye yazabilir. Belirtilen adreste eMMC/SD'ye paket tipinin görüntüsü. Görüntü başlangıç ofsetinin değerine bağlı olarak (512 bayt sınırına göre hizalanır).

#### 5. Format (FAT32)

- eMMC'nin uboot görüntüsünü ve diğer görüntüleri depolaması gerektiğinden, kullanıcının görüntüyü depolamak için ayrılmış alan ayarlaması gerekir (512 bayt sınırında hizalanmış). Kullanıcının ayrılmış alan boyutunu belirlemesi gerekir.

**Not:** Bu parametrenin değiştirilmesi, mevcut görüntü hasarına veya dosya sistemi hasarına neden olabilir.

- FAT32 dosya sistemi birden çok disk bölümünü destekler, ancak bu, ana önyükleme kaydındaki (MBR) dosya ayırma tablosunun sabit yapısı ile sınırlıdır. Bir sabit disk en fazla 4 bölümü destekleyebilir.


### Operation Steps

#### 1. Add New Image

![[Pasted image 20220326222855.png]]
Figure 4-24 eMMC/SD – New Image

1. Select the “eMMC/SD” type, which will not list the pre-burned images in the eMMC/SD.
2. Fill in the image information：
	-  Image Name: Browse the image file.
	-  Image Type Select the image type. (only one type can be selected)
	-  Image execute address: Enter image execute address. Only Loader Type is valid.
	-  Image start offset: Enter image start offset.
1. Click the “Program” button.
2. Waiting for progress bar shows progress has been completed.
3. After the program is completed, click the “Verify” button to read back the image data to make sure the burning status.

![[Pasted image 20220326222935.png]]
Figure 4-25 eMMC/SD – Read

The steps introduce how to read data from eMMC/SD are listed below.
1. Select the “eMMC/SD”.
2. Click “Read” button.
3. Browse save file.
4. Enter the read back of sectors, Aligned on 512 bytes boundary
	-  Start: Start address of sectors
	-  Length: Length of sectors
5. Click the “OK” button.


**Format (FAT32)**

![[Pasted image 20220326223109.png]]
Figure 4-26 eMMC/SD – Format (1)

![[Pasted image 20220326223136.png]]
Figure 4-27 eMMC/SD – Format (2)


![[Pasted image 20220326223141.png]]
Figure 4-28 eMMC/SD – Format (3)


Figure 4-26, Figure 4-27 and Figure 4-28 illustrate how to format eMMC/SD.
1. Select “eMMC/SD”.
2. Click the “Format” button.
3. Enter value of Reserve space size(1 sector is 512bytes). Note : This parameter may cause existing image or file system is being destroyed.
4. Click the “Set” button to apply reserved space size.
5. After setting the reserved space size, there are four sliders to user configure the eMMC/SD partition size. Users operate these slider to determine each partition size.
	- 1st PartitionSize: First partition size.
	-  2ndPartitionSize: Second partition size.
	-  3rd PartitionSize: Third partition size.
	- 4th PartitionSize: Fourth partition size.
6. Click the "Set" button to apply partition size sucessfully.
7. Click the “Add” button to add the next partition. Repeat 5~7 to divide the eMMC/SD space into four partitions.
8. Click the "OK" button to erase complete.
