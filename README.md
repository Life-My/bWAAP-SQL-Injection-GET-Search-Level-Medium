## bWAAP SQL GET Search Level Medium

Movies kısmına `'` karakteri koyuyoruz ve hata alamadığımız görüyoruz.
Bundan anlıyoruz ki `'` işaretini escapliyor. SQL sorgumuzun oluştuğu son hal ise aşağıdaki gibi olmaktadır;
```SQL
SELECT * FROM movies WHERE mname LIKE '%\'%'
```
`SQL
SELECT * FROM movies WHERE mname LIKE '%`**\'**`%'`
