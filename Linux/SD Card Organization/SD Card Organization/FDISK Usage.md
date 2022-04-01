
Düzenleme kısmına girmek için;

>sudo fdisk /dev/sdb

Bölümleri silmek için;

>d

çalıştırılır

Yeni bir gpt partion table oluşturulması için

>g

komutu girililr

Yeni bir bölüm oluşturmak için

>n

Bölüm numaralı standar ve sectorleri de standar olabilir

Sadece boyut belirlerken düzenleme yapılmalıdır.

Örneğin;

> +256KiB
> +64MiB
> +2MiB

gibi girilmelidir.

Son kalan kısım sd kart boyunun tamamaı girilmeli ki sistemde kullanılabilir olsun.

Son olarak bootlamaya uygun olarak düzenlenmesi için tiplerinin düzenlenmesi gerekmektedir.

Son olarak disk de bulunan son bootfs kısmının bootable şekilde düzenlenmesi gerekebilir.

