  
> What is the difference between them?

**Image**: the generic Linux kernel binary image file.

**zImage**: a compressed version of the Linux kernel image that is self-extracting.

**uImage**: an image file that has a U-Boot wrapper (installed by the **mkimage** utility) that includes the OS type and loader information.  
A very common practice (e.g. the typical Linux kernel Makefile) is to use a zImage file. Since a zImage file is self-extracting (i.e. needs no external decompressors), the wrapper would indicate that this kernel is "not compressed" even though it actually is.

Note that the author/maintainer of U-Boot considers the (widespread) use of using a zImage inside a uImage questionable:

> Actually it's pretty stupid to use a zImage inside an uImage. It is much better to use normal (uncompressed) kernel image, compress it using just gzip, and use this as poayload for mkimage. This way U-Boot does the uncompresiong instead of including yet another uncompressor with each kernel image.

(quoted from [https://lists.yoctoproject.org/pipermail/yocto/2013-October/016778.html](https://lists.yoctoproject.org/pipermail/yocto/2013-October/016778.html))

---

> Which type of kernel image do I have to use?

You could choose whatever you want to program for.  
For economy of storage, you should probably chose a compressed image over the uncompressed one.  
Beware that executing the kernel (presumably the Linux kernel) involves more than just loading the kernel image into memory. Depending on the architecture (e.g. ARM) and the Linux kernel version (e.g. with or without DTB), there are registers and memory buffers that may have to be prepared for the kernel. In one instance there was also hardware initialization that U-Boot performed that had to be replicated.

**ADDENDUM**

> I know that u-boot needs a kernel in uImage format.

That is accurate for all versions of U-Boot which only have the **bootm** command.  
But more recent versions of U-Boot could also have the **bootz** command that can boot a zImage.