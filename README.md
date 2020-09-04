## bWAAP SQL GET Search Level Medium
Movies kısmına `'` karakteri koyuyoruz ve hata alamadığımız görüyoruz.

Bundan anlıyoruz ki `'` işaretini escapliyor. SQL sorgumuzun oluştuğu son hal ise aşağıdaki gibi olmaktadır;
```SQL
SELECT * FROM movies WHERE mname LIKE '%\'%'
```
Burada PHP tarafinda addslashes fonksiyonunun kullanıldığını görebiliriz challenge da bu fonksiyonu baypaslayabilmek için mysql char setimizin `character-set-server=GBK` şeklinde olması gereklidir.

####MYSQL CHARSET AYARI
XAMPP yönetim panelinden my.ini konfigürasyon dosyasını açalım ve character-set-server’ı GBK olarak değiştirelim.

####Addslashes 
[Ömer Çıtak](https://omercitak.com/):
>Hex karşılığı 0xbf5c olan “¿\” karakteri, utf 8 charsetinde 2 karakter, GBK charsetinde 1 karakter olarak algılanıyor. İnjection saldırısında sorguya tek tırnak, yani 0x27 gönderdiğiniz vakit mysql_real_escape_string veya addslashes fonksiyonlarımız bizim tırnağımızı “\’” haline çeviriyor. Tırnaktan önce “¿” (0xbf) karakterini gönderdiğimizde yani “¿’” (0xbf27) karakterini gönderdiğimizde, tırnaktan önce \ eklendiğinden gönderdiğimiz karakter “¿\’” (0xbf5c27) halini alıyor. GBK charseti “¿\” karakterini tek karakter olarak aldıladığından sondaki tırnağımız boşa çıkıyor, böylece sorgunun gidişatını değiştirmiş oluyorsunuz. Sonrasında injection ile sorguyu istediğimiz şekle sokabiliriz.
