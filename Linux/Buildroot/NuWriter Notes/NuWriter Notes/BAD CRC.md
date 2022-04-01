12.2 Checksum Calculation (SHA-1 or crc32) 

mkimage tool calculates the checksum of Linux kernel image and puts it in the header. 

The command “bootm” also calculates the checksum of Linux kernel image loaded to DDR and compares the checksum with the value in header of Linux kernel image. If they are different, it shows the error message “Verifying Checksum ... Bad Data CRC” and stop booting Linux kernel. If they are the same, it shows the message “Verifying Checksum ... OK” and continues to boot Linux kernel. The default option of mkimage tool is using software crc32 to calculate the checksum of Linux kernel image. It’s time-consuming. If you want to speed up booting Linux kernel, you can use hardware SHA1 to calculate the checksum of Linux kernel. 

NUC980 adds a new parameter “-S” to calculate Linux kernel checksum


The original checksum calculation method of mkimage tool is crc32, NUC980 provides another option, SHA-1, below is an example uses SHA-1 to calculate Linux kernel checksum. Add option “-S sha1”. Remember to enable CONFIG_NUC980_HW_CHECKSUM in nuc980_evb.h

>u-boot/tools# ./mkimage -A arm -O linux -T kernel -S sha1 -a 0x7fc0 –e 0x8000 -d 980image 980image.ub 
>
>Image Name: 
>
>Created: Fri Aug 8 14:39:47 2014 
>
>Image Type: ARM 
>
>Linux Kernel Image (uncompressed) 
>
>Data Size: 1639744 Bytes = 1601.31 kB = 1.56 MB 
>
>Load Address: 00007FC0
>
 Entry Point: 00008000

-A: set CPU architecture to arm 
-O: set operating system to linux 
-T: set image type to kernel 
-a: set load address to 0x7fc0
-e: set entry point to 0x8000 
-S: set checksum calculation to sha1 
-d: set image data from 980image

If you select crc32 to calculate Linux kernel checksum,below is an example : Added an option “-S crc32”. Remember to disable CONFIG_NUC980_HW_CHECKSUM in nuc980_evb.h

>u-boot/tools# ./mkimage -A arm -O linux -T kernel -S crc32 -a 0x7fc0 –e 0x8000 -d 980image 980image.ub 
>
>Image Name: 
>
>Created: Fri Aug 8 14:39:47 2014 
>
>Image Type: ARM Linux Kernel Image (uncompressed) 
>
>Data Size: 1639744 Bytes = 1601.31 kB = 1.56 MB 
>
>Load Address: 00007FC0 
>
>Entry Point: 00008000


A: set CPU architecture to arm 
-O: set operating system to linux 
-T: set image type to kernel 
-a: set load address to 0x7fc0 
-e: set entry point to 0x8000
-S: set checksum calculation to crc32 
-d: set image data from 980image



./mkimage -A arm -O linux -T kernel -S crc32 -a 0x7fc0 -e 0x8000 -d ~/Projects/Nuvoton/NUC970_Buildroot/output/images/Image ~/Projects/Nuvoton/NUC970_Buildroot/output/images/Image.ub