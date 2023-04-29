# PostgreSQL + Adminer

Varsayılan kullanıcı bilgileri kullanıcı adı: postgres  ve şifre: example olacak.
![image](https://user-images.githubusercontent.com/261946/235289229-1fa58121-880b-4af9-90f0-2158ab019200.png)

Ayaklandırmak için:

```shell
$ docker-compose up
```


## Postgresql Günlükleri
PostgreSQL, çeşitli olayların kaydedilmesi için ayrıntılı günlük tutma seçenekleri sunar. Bunlar aşağıdaki gibidir:

1. `log_destination`: PostgreSQL günlüklerinin nereye yazılacağını belirler (`-c log_destination=stderr`). Varsayılan değer, `stderr`dir, yani hata çıkışına yazılır. Diğer olası seçenekler arasında `csvlog`, `syslog` ve `eventlog` bulunur.
2. `logging_collector`: PostgreSQL'in günlükleri toplamak için bir toplayıcı süreç başlatıp başlatmayacağını belirler. Varsayılan olarak `off` olarak ayarlanmıştır (`-c logging_collector=on`).
3. `log_directory`: PostgreSQL günlüklerinin kaydedileceği dizini belirler (`-c log_directory=/var/log/postgresql`). Varsayılan olarak, günlükler PostgreSQL veri dizinine kaydedilir.
4. `log_filename`: PostgreSQL günlüklerinin dosya adını belirler. Varsayılan olarak, `postgresql-%Y-%m-%d_%H%M%S.log` şeklindedir.
5. `log_rotation_age`: PostgreSQL günlüklerinin kaç gün saklanacağını belirler. Varsayılan olarak, günlükler sınırsız olarak saklanır.
6. `log_rotation_size`: PostgreSQL günlüklerinin dosya boyutunu belirler. Varsayılan olarak, dosya boyutu sınırsızdır.
7. `log_min_duration_statement`: Bu seçenek, PostgreSQL'in ne kadar uzun sorguların günlüğe yazılacağını belirler. Varsayılan olarak, `-1` olarak ayarlanmıştır, yani tüm sorguların günlüğe yazılmasını sağlar.
8. `log_checkpoints`: Bu seçenek, PostgreSQL'in her checkpoint'in günlüğe yazılıp yazılmayacağını belirler. Varsayılan olarak `off` olarak ayarlanmıştır.
9. `log_connections`: Bu seçenek, PostgreSQL'in her bağlantı girişiminin günlüğe yazılıp yazılmayacağını belirler. Varsayılan olarak `off` olarak ayarlanmıştır.
10. `log_disconnections`: Bu seçenek, PostgreSQL'in her bağlantı kesme olayının günlüğe yazılıp yazılmayacağını belirler. Varsayılan olarak `off` olarak ayarlanmıştır.
11. `log_statement` PostgresSQL'deki bir günlükleme parametresidir (`-c log_statement=all`). Bu parametre, herhangi bir SQL ifadesi yürütüldüğünde, günlüğe yazılmadan önce ifadeyi gösteren bir kayıt oluşturulmasını sağlar.
  Bu parametre üç farklı değer alabilir:
    - `none`: Bu seçenek, SQL ifadelerinin günlüğe yazılmamasını sağlar.
    - `ddl`: Bu seçenek, yalnızca veritabanı tanımlama (DDL) ifadelerinin günlüğe yazılmasını sağlar. DDL ifadeleri, veritabanı nesnelerinin oluşturulması, değiştirilmesi veya silinmesiyle ilgilidir.
    - `all`: Bu seçenek, tüm SQL ifadelerinin günlüğe yazılmasını sağlar.
    `log_statement` parametresi, özellikle büyük ve karmaşık bir veritabanında sorunları tespit etmek ve hata ayıklamak için çok yararlıdır. Ancak, çok fazla SQL ifadesinin günlüğe yazılması, performans sorunlarına neden olabilir, bu nedenle bu parametrenin dikkatli bir şekilde ayarlanması önerilir.
  Bu seçenekler, PostgreSQL yapılandırma dosyası (`postgresql.conf`) içinde ayarlanabilir ve çalışma zamanında değiştirilebilir.

Biz konteyner ayaklandığında çalışacak komut olarak `command: postgres -c logging_collector=on -c log_directory=/var/log/postgresql -c log_statement=all` kullanıyoruz.


## Ağ Paketlerini Yakalamak

- `tshark -D` ile hangi arayüzleri dinleyebileceğimizi ve numaralarını görebiliriz.
- `tshark -i <izlenecek arayüz>` komutuyla paketleri yakalamaya başlıyoruz ancak bu paketlerin hareketini ekrana basıyor.
- `tshark -i <izlenecek arayüz> -w <dosya adı.pcap>` komutuyla ağ arayüzünden gelen ağ trafiğini yakalar ve paketleri bir dosyaya yazıyoruz.
- `tshark -i <izlenecek arayüz> -w -` komutuyla ağ arayüzünden gelen ağ trafiğini yakalar ve standart çıktıya `stdout` yazdırır.
- `wireshark -k -i -` komutu `-k` parametresi, Wireshark programının açılır açılmaz trafiği izlemeye başlatır, `-i -` parametresi, Wireshark programının trafiği standart girdiden (`stdin`) okumasını ifade eder.
- `tshark -i <izlenecek arayüz> -w - | wireshark -k -i -` komutuyla ilk kısımda tshark ile yakalanan paketler stdout'a yazdırılır ve wireshark stdin'den okuyarak görüntüler.


![image](https://user-images.githubusercontent.com/261946/235289984-3fe31f3f-e54a-4caf-9531-51e0d3a3fbb9.png)

![image](https://user-images.githubusercontent.com/261946/235292136-a87a1cad-0656-4929-98ee-4be41a2b43be.png)
