Buildroot Linux sistemi üzerinde çalıştırılmak için tasarlanmışır.

**Gerekli Olan Kurulumlar**
– git
– buildroot

>sudo apt-get install git

>git clone git://git.buildroot.net/buildroot

**Gerekli Olan Derleme Araçları**:

– which
– sed
– make (version 3.81 or any later)
– binutils
– build-essential (only for Debian based systems)
– gcc (version 4.8 or any later)
– g++ (version 4.8 or any later)
– bash
– patch
– gzip
– bzip2
– perl (version 5.8.7 or any later)
– tar
– cpio
– unzip
– rsync
– file (must be in /usr/bin/file)
– bc
– wget

> sudo apt-get install -y which sed make binutils build-essential gcc g++ bash patch gzip bzip2 perl tar cpio unzip rsync file bc wget

**Önerilen Bağımlılıklar**

– python3 (version 2.7 or any later)

>sudo apt-get install -y python3 python3-dev

**Düzenleme İşlemleri İçin Arayüz Bağımlıkıkları**

– ncurses5 to use the menuconfig interface
– qt5 to use the xconfig interface
– glib2, gtk2 and glade2 to use the gconfig interface


- For ncurses5 interface

> sudo apt-get install -y libncurses5-dev libncursesw5-dev

- For gconfig interface

>sudo apt-get install libglib2.0-dev gtk2.0 python-glade2

