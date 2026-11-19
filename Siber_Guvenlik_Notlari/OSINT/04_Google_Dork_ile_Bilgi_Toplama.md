---
title: "Google Dork ile Bilgi Toplama"
ana-not: "[[00_OSINT_İndeks]]"
tarih: 2026-11-19
aliases:
  - Google Dork ile Bilgi Toplama
tags:
  - siber-guvenlik
  - osint
  - google-dork
  - ghdb
  - pasif-kesif
---

# Google Dork ile Bilgi Toplama

> ◀ [Temel Kavramlar](03_Temel_Kavramlar.md) · 📚 [İndeks](00_OSINT_%C4%B0ndeks.md) · [Sosyal Medya ve Görseller ile Bilgi Toplama](05_Sosyal_Medya_ve_G%C3%B6rseller_ile_Bilgi_Toplama.md) ▶

Google Dorking, belirli anahtar kelimeler, operatörler ve özel karakterler kullanarak Google'da gelişmiş aramalar yapma yöntemidir. Bu yöntemle, normalde bulunması zor veya gizli olan bilgilere erişim sağlanabilir.

## Google Hacking Database (GHDB)

Google Hacking Database (GHDB), siber güvenlik uzmanları ve etik hackerlar için önemli bir kaynaktır. Johnny Long tarafından 2000'lerin başında oluşturulan GHDB, Google dork'larını ve diğer arama motoru sorgularını kullanarak sistemlerdeki güvenlik açıklarını bulmayı hedefler. Veritabanında, açık dizinler, hatalı yapılandırmalar, gizli dosyalar ve daha birçok potansiyel güvenlik açığı içeren arama sorguları yer alır. GHDB, siber güvenlik testlerinde ve güvenlik denetimlerinde yaygın olarak kullanılır.

Google Hacking Database (GHDB) hakkında daha fazla bilgiye ve dork'ların tam listesine Exploit Database'in web sitesinden ulaşılabilmektedir. Detaylı bilgi ve dork'lar için GHDB'nin resmi sayfasını ziyaret edilebilir: [exploit-db.com/google-hacking-database](https://www.exploit-db.com/google-hacking-database)

## En Çok Kullanılan Google Dork'lar

| # | Dork | Açıklama | Kullanım Örneği |
|---|---|---|---|
| 1 | `site:` | Belirli bir sitede arama yapar. | `site:example.com` |
| 2 | `filetype:` | Belirli bir dosya türünde arama yapar. | `filetype:pdf` |
| 3 | `intitle:` | Başlığında belirli kelimeler bulunan sayfaları arar. | `intitle:"login"` |
| 4 | `inurl:` | URL'sinde belirli kelimeler bulunan sayfaları arar. | `inurl:admin` |
| 5 | `cache:` | Google'ın önbelleğinde saklanan sayfaları gösterir. | `cache:example.com` |
| 6 | `link:` | Belirli bir sayfaya bağlantı veren sayfaları bulur. | `link:example.com` |
| 7 | `related:` | Belirli bir siteye benzer siteleri bulur. | `related:example.com` |
| 8 | `intext:` | Sayfa içeriğinde belirli kelimeleri arar. | `intext:"password"` |
| 9 | `allintitle:` | Başlığında tüm belirtilen kelimeleri içeren sayfaları arar. | `allintitle:login admin` |
| 10 | `allinurl:` | URL'sinde tüm belirtilen kelimeleri içeren sayfaları arar. | `allinurl:admin login` |
| 11 | `allintext:` | Sayfa içeriğinde tüm belirtilen kelimeleri arar. | `allintext:username password` |
| 12 | `define:` | Belirli bir kelimenin tanımını arar. | `define:OSINT` |
| 13 | `"keyword"` | Belirli bir kelime öbeğini tam olarak arar. | `"admin login"` |
| 14 | `-keyword` | Belirli bir kelimeyi içermeyen sayfaları arar. | `password -example` |
| 15 | `OR` | Belirtilen iki kelimeden birini içeren sayfaları arar. | `login OR signup` |
| 16 | `*` | Belirtilen kelimenin yerine herhangi bir kelimeyi koyar. | `intitle:"admin *"` |
| 17 | `..` | Belirli bir aralıkta sayıları arar. | `filetype:pdf 2020..2022` |
| 18 | `info:` | Belirli bir site hakkında bilgi gösterir. | `info:example.com` |
| 19 | `maps:` | Belirli bir yerin haritasını gösterir. | `maps:New York` |
| 20 | `stocks:` | Belirli bir şirketin hisse senedi bilgilerini gösterir. | `stocks:GOOG` |

## Google Dork Kullanım Örnekleri

Aşağıda, Google dork'ların ne işe yaradığını ve nasıl kullanıldığını gösteren örnekler bulunmaktadır. Google dork'lar, arama motoru sorgularını özelleştirerek bilgi toplama ve sızma testlerinde kritik bilgiler elde etmek için kullanılır.

### 1. Belirli Bir Sitede Arama
Bir sitede belirli bilgileri arar. Örneğin, bir sitenin iletişim bilgilerini bulmak için:

```text
site:example.com "contact information"
```

### 2. Belirli Bir Dosya Türünde Arama
Belirli dosya türlerini arar. Örneğin, PDF formatında gizli belgeleri bulmak için:

```text
filetype:pdf "confidential"
```

### 3. Açık Dizinleri ve İçerdikleri Dosyaları Bulma
Açık dizinlerdeki dosyaları bulur. Örneğin:

```text
intitle:"index of /"
```

### 4. Yönetim Panellerini Bulma
URL'de "admin" kelimesi geçen sayfaları arar. Bu, yönetim panellerini bulmak için kullanılır:

```text
inurl:admin
```

### 5. Google Önbelleğinde Saklanan Sayfaları Gösterir
Bir sitenin önbelleğe alınmış eski versiyonlarını gösterir. Örneğin:

```text
cache:example.com
```

### 6. Belirli Bir Sayfaya Bağlantı Veren Sayfaları Bulma
Belirli bir sayfaya hangi sitelerin bağlantı verdiğini bulur:

```text
link:example.com
```

### 7. Benzer Siteleri Bulma
Belirli bir siteye benzer siteleri keşfetmek için kullanılır:

```text
related:example.com
```

### 8. Sayfa İçeriğinde Belirli Kelimeleri Arama
Sayfa içeriğinde belirli kelimeleri arar. Örneğin, şifre gibi kritik bilgileri bulmak için:

```text
intext:"password"
```

### 9. Başlığında Belirtilen Kelimeleri İçeren Sayfaları Arama
Başlığında belirli kelimeleri içeren sayfaları arar. Örneğin, giriş ve yönetici sayfalarını bulmak için:

```text
allintitle:login admin
```

### 10. URL'sinde Belirtilen Kelimeleri İçeren Sayfaları Arama
URL'sinde belirli kelimeleri içeren sayfaları arar. Yönetici ve giriş sayfalarını bulmak için:

```text
allinurl:admin login
```

### 11. Sayfa İçeriğinde Belirtilen Kelimeleri Arama
Sayfa içeriğinde belirli kelimeleri arar. Örneğin, kullanıcı adı ve şifre içeren sayfaları bulmak için:

```text
allintext:username password
```

### 12. Belirli Bir Kelimenin Tanımını Arama
Belirli bir kelimenin tanımını arar. Örneğin, phishing teriminin tanımını öğrenmek için:

```text
define:phishing
```

### 13. Belirli Bir Kelime Öbeğini Tam Olarak Arama
Belirli bir kelime öbeğini tam olarak arar. Örneğin, gizli belgeleri tam olarak bulmak için:

```text
"confidential document"
```

### 14. Belirli Bir Kelimeyi İçermeyen Sayfaları Arama
Belirli bir kelimeyi içermeyen sayfaları arar. Örneğin, "password" içeren ama "example" içermeyen sayfaları bulmak için:

```text
password -example
```

### 15. Belirtilen İki Kelimeden Birini İçeren Sayfaları Arama
İki kelimeden birini içeren sayfaları arar. Örneğin, "admin" veya "user" kelimelerinden birini içeren sayfaları bulmak için:

```text
admin OR user
```

### 16. Belirtilen Kelimenin Yerine Herhangi Bir Kelimeyi Koyma
Belirtilen kelimenin yerine herhangi bir kelimeyi koyar. Örneğin, "admin" ile başlayan ve herhangi bir kelimeyle devam eden başlıkları bulmak için:

```text
intitle:"admin *"
```

### 17. Belirli Bir Aralıkta Sayıları Arama
Belirli bir aralıktaki sayıları içeren sonuçları arar. Örneğin, belirli yıllar arasındaki PDF dosyalarını bulmak için:

```text
filetype:pdf 2020..2023
```

### 18. Belirli Bir Site Hakkında Bilgi Gösterir
Belirli bir site hakkında genel bilgi almak için kullanılır:

```text
info:google.com
```

### 19. Açık Dizinleri ve İçeriklerini Bulma
Açık dizinleri ve içeriklerini bulur. Örneğin:

```text
intitle:"Index of" -inurl:(jsp|pl|php|html|aspx|htm|cf|shtml)
```

### 20. Açık Web Kameralarını Bulma
Canlı yayın yapan web kameralarını bulur:

```text
inurl:"viewerframe?mode=motion"
```

### 21. E-posta Listelerini Bulma
E-posta adresi listelerini bulur:

```text
filetype:txt @gmail.com OR @yahoo.com
```

### 22. Güvenlik Açıkları Olan Web Sunucularını Bulma
Güvenlik açıkları olan eski Windows 2000 sunucularını bulur:

```text
intitle:"Welcome to Windows 2000 Internet Services"
```

### 23. SQL Enjeksiyon Açıklıkları Olan Sayfaları Bulma
SQL enjeksiyon açıklıkları olan sayfaları bulur:

```text
inurl:index.php?id=
```

### 24. Hassas Bilgileri ve Parola İçeren Dosyaları Bulma
Parola içeren dosyaları bulur:

```text
intitle:"Index of" password.txt
```

### 25. Yönetici Giriş Sayfalarını Bulma
Yönetici giriş sayfalarını bulur:

```text
inurl:admin/login
```

### 26. Açık phpMyAdmin Veritabanı Yönetim Panellerini Bulma
Açık phpMyAdmin veritabanı yönetim panellerini bulur:

```text
intext:"phpMyAdmin" "running on" inurl:"main.php"
```

### 27. Açık FTP Sunucularını Bulma
Açık FTP sunucularını bulur:

```text
intitle:"index of" inurl:ftp
```

### 28. Açık Yapılandırma Dosyalarını Bulma
Açık yapılandırma dosyalarını bulur:

```text
intitle:"index of" config.yml
```

### 29. Açık Git Depolarını Bulma
Açık Git depolarını bulur:

```text
intitle:index of .git
```

### 30. Güvenlik Açığı Olan Apache Tomcat Sunucularını Bulma
Güvenlik açığı olan Apache Tomcat sunucularını bulur:

```text
intitle:"Apache Tomcat" intext:"If you're seeing this, you've successfully installed Tomcat"
```

---
> ◀ [Temel Kavramlar](03_Temel_Kavramlar.md) · 📚 [İndeks](00_OSINT_%C4%B0ndeks.md) · [Sosyal Medya ve Görseller ile Bilgi Toplama](05_Sosyal_Medya_ve_G%C3%B6rseller_ile_Bilgi_Toplama.md) ▶
