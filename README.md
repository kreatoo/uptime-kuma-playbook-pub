# Uptime Kuma Ansible Playbook dokümentasyon 

Bu Ansible playbook, AWX APIsini kullanarak bütün host adlarını alır, ve o hostlarda bulunan;

* vhostları (Caddy, NGINX ve Apache destekliyor)
* PostgresSQL/MySQL sunucuları (Tag bulunuyorsa, detayları aşağıda bulabilirsiniz)

Uptime Kuma'ya bildirimleri ile beraber ekler.

Bildirimleri Zulip'ten yollar.

## AWX

AWX API'sini kullandığından dolayı

* `awx_api_url`: AWX API URL'i. AWX'in adresine ayarlayabilirsiniz (örn. `https://awx.test.com`)
* `awx_pass`: AWX'e giriş yapılacak kullanıcı şifresi.
* `awx_user`: AWX'e giriş yapılacak kullanıcı adı.

variablelarını (playbook dosyasının en yukarısında, vars altında ayarlayabilirsiniz) doldurmanız gerekmektedir.

## Bildirim

Bildirimler Zulip'den Webhook yöntemiyle atılacak şekilde ayarlanmıştır. Bundan dolayı:

* `zulip_url`: Zulip Webhook adresi.
* `alarm_stream`: Alarmın ayarlandığı Zulip Streami.

variablelarını doldurmanız gerekmektedir.

Alarmlar ayarladığınız stream altında, hostadı ile açılan topiclerde gönderilir.

## Tagler

Tag adları AWX'de olan hostun gruplarından alınır ve Uptime Kuma'da o hosta eklenir.


## Monitorler

Monitor adları ve URL'leri AWX'de olan hostun adından alınarak koyulur. Varsayılan olarak monitorler https ile ayarlanmıştır.

Örneğin AWX hostunun adı 'test.com' ise Uptime Kuma'da adı `test.com`, URL'si `https://test.com` olur.


## Caddy/NGINX/Apache

Hostlara delegate ederek configde vHost olarak bulunan adresler Uptime Kuma'ya otomatik olarak eklenir.

Örneğin bir AWX hostu içinde Caddy'de;

```
subdomain.test.com {
    reverse_proxy localhost:8080
}
```

Böyle bir vHost bulunuyorsa otomatikman Uptime Kuma'ya `subdomain.test.com` adı ile, `https://subdomain.test.com` adresi ile eklenir.

## Postgres/MySQL

Postgres/MySQL hostta algılanıyorsa Uptime Kuma'ya otomatik eklenebilir. Bunun için:

* `target_group_postgres`: AWX grubu adı. Bu grup hostta bulunuyorsa, hostta çalışan Postgres sunucusu eklenir.
* `target_group_mysql`: AWX grubu adı. Bu grup hostta bulunuyorsa, hostta çalışan MySQL sunucusu eklenir.
* `target_group_postgres_port`: Postgres portu.
* `target_group_postgres_user`: Postgres kullanıcısı.
* `target_group_postgres_password`: Postgres şifresi.
* `target_group_postgres_database`: Postgres database adı.
* `target_group_mysql_port`: MySQL portu.
* `target_group_mysql_user`: MySQL kullanıcısı.
* `target_group_mysql_password`: MySQL şifresi.
* `target_group_mysql_database`: MySQL database adı.

Variablelarının ayarlanması gerekmektedir.

Herhangi bir alan boş olmamalıdır.

Uptime Kuma adında host adının sonuna `-postgres` ya da `-mysql` eklenir.

Örneğin bir `testhost` adlı bir AWX hostunda `postgres` rolü var. `target_group_postgres`i `postgres` olarak ayarlarsanız, playbook `testhost:port` adresine bakan bir Postgres monitorü oluşturacaktır. Adı da `testhost-postgres` olur.

# Detaylar

* Playbook overwrite yapmaz, eğer hali hazırda o adda bir tag/notification/monitor varsa ellemez, ignorelar.
* Delegate fonksiyonunu test etmedim.

