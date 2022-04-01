# Managing the build and the configuration

## Default build organization

- **output/**, top level directory içerisinde derleme çıktıları saklanmaktadır.
	- **O = output,** anlamına gelir ve düzenlenebilir
- **.config**, Konfigurasyon dosyaları top level directory de saklanır
	- **CONFIG_DIR = $(TOPDIR)**
	- **TOPDIR = $(shell pwd)**

## Out of tree build
### Introduction
- Ağaç dışı yapı, **output/** klasöründen farklı olarak farklı konumlara izin verir
- Aynı kaynak ağaçtan farklı Buildroot konfigürasyonları oluşturmak için kullanışlıdır.
- Komut satırında O=/path/to/directory iletilerek çıktı dizininin özelleştirilmesi.


