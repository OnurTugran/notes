---
title: "WordPress Keşif ve Numaralandırma"
ana-not: "[[00_WordPress_İndeks]]"
tarih: 2026-11-19
aliases:
  - WordPress Keşif ve Numaralandırma
tags:
  - siber-guvenlik
  - wordpress
  - enumeration
  - wpscan
  - kullanici
---

# WordPress Keşif ve Numaralandırma

> ◀ [WordPress Temelleri ve Dosya Yapısı](01_WordPress_Temelleri_ve_Dosya_Yap%C4%B1s%C4%B1.md) · 📚 [İndeks](00_WordPress_%C4%B0ndeks.md) · [WordPress Saldırıları](03_WordPress_Sald%C4%B1r%C4%B1lar%C4%B1.md) ▶

## Eklenti (Plugin) ve Tema Bilgi Toplama

WordPress sitelerinde yüklü olan eklentiler ve temalar, zafiyet barındırma ihtimalleri en yüksek bileşenlerdir. Bu bileşenler hakkında bilgi toplamak (enumeration) için **pasif** ve **aktif** olmak üzere iki temel yöntem kullanılır.

## 🕵️‍♂️ Pasif Bilgi Toplama (Kaynak Kodu İncelemesi)

Sayfa kaynağını manuel olarak inceleyerek veya komut satırı araçlarını (`cURL`, `grep`, `sed`, `cut`) kullanarak HTML kodu içindeki eklenti ve tema yollarını filtreleyebiliriz. Ayrıca HTTP yanıt başlıkları (response headers) da bazen belirli eklentilerin sürüm numaralarını barındırabilir.

### Eklentileri (Plugins) Filtreleme Komutu
Sayfa kaynağındaki `wp-content/plugins/` dizininden çağrılan dosyaları listelemek için:

```shell
$ curl -s -X GET http://blog.inlanefreight.com | sed 's/href=/\n/g' | sed 's/src=/\n/g' | grep 'wp-content/plugins/*' | cut -d"'" -f2
```

### Temaları (Themes) Filtreleme Komutu
Sayfa kaynağında `themes` kelimesini geçen CSS, JS ve görsel bağlantılarını listelemek için:

```shell
$ curl -s -X GET http://blog.inlanefreight.com | sed 's/href=/\n/g' | sed 's/src=/\n/g' | grep 'themes' | cut -d"'" -f2
```

---

## ⚡ Aktif Bilgi Toplama (Dizin / Dosya Keşfi)

Tüm yüklü eklenti ve temalar sayfa kaynağında (pasif olarak) görünmeyebilir. Bu durumda, sunucuda var olabileceğini düşündüğümüz dizin veya dosyalara aktif olarak `GET` istekleri göndermemiz gerekir.

- **Dizin/Dosya varsa:** Sunucu genellikle ya doğrudan erişim verir ya da içeriğin var olduğunu ancak erişim iznimiz olmadığını gösteren bir yönlendirme (**HTTP 301 Moved Permanently**) yanıtı döndürür.
- **Dizin/Dosya yoksa:** Sunucu **HTTP 404 Not Found** hatası döndürür.

### Yüklü Olan Bir Eklentiyi Doğrulama Komutu
Var olan bir eklenti dizinine istek atıldığında dönen HTTP başlıklarını (örneğin 301 yönlendirmesini) görmek için:

```shell
$ curl -I -X GET http://blog.inlanefreight.com/wp-content/plugins/mail-masta
```

### Yüklü Olmayan Bir Eklentiyi Test Etme Komutu
Var olmayan bir eklenti dizinine istek atıldığında sunucunun verdiği tepkiyi (404 hatasını) görmek için:

```shell
$ curl -I -X GET http://blog.inlanefreight.com/wp-content/plugins/someplugin
```

*(Not: Aynı aktif tespit mantığı `wp-content/themes/` dizini altındaki temalar için de geçerlidir.)*

> [!TIP]
> **Otomasyon (Pentest Notu)**
> Bilgi toplama sürecini hızlandırmak için bu denemeleri tek tek elle yapmak yerine basit bir **Bash betiği (script)** yazılabilir veya süreci otomatize eden **`wfuzz`** ya da **`WPScan`** gibi güçlü araçlar kullanılabilir.

## Dizin Listeleme (Directory Indexing)

Bir WordPress web sitesini değerlendirirken (pentest aşamasında) odak noktamız sadece **aktif** eklentiler olmamalıdır.

> [!CAUTION]
> **Kritik Hata: Devre Dışı Bırakılan Eklentiler**
> Zafiyet barındıran bir eklentiyi sadece **devre dışı bırakmak (deactivate)**, WordPress sitesinin güvenliğini artırmaz. Eklenti devre dışı bırakılsa bile dosyalarına, script'lerine ve fonksiyonlarına dışarıdan erişilmeye devam edilebilir. En iyi güvenlik uygulaması (best practice), kullanılmayan eklentileri tamamen **kaldırmak** veya güncel tutmaktır.

Örneğin, devre dışı bırakılmış olan "Mail Masta" eklentisine tarayıcı üzerinden şu şekilde gidildiğinde dizin içeriği görüntülenebilir:
`http://<target>/wp-content/plugins/mail-masta/`

---

## 🖥️ Terminal Üzerinden Dizin Listeleme (cURL & html2text)

Tarayıcı yerine komut satırını kullanarak bir dizin listesini (directory listing) çekebilir ve HTML çıktısını `html2text` aracı ile terminalde okunabilir, temiz bir formata dönüştürebiliriz:

```shell
$ curl -s -X GET http://blog.inlanefreight.com/wp-content/plugins/mail-masta/ | html2text

****** Index of /wp-content/plugins/mail-masta ******
[ICO]       Name                 Last_modified    Size Description
===========================================================================
[PARENTDIR] Parent_Directory                         -
[DIR]       amazon_api/          2020-05-13 18:01    -
[DIR]       inc/                 2020-05-13 18:01    -
[DIR]       lib/                 2020-05-13 18:01    -
[   ]       plugin-interface.php 2020-05-13 18:01  88K
[TXT]       readme.txt           2020-05-13 18:01 2.2K
===========================================================================
     Apache/2.4.29 (Ubuntu) Server at blog.inlanefreight.com Port 80
```

### 🛡️ Dizin Listeleme Zafiyeti Nedir?
Yukarıdaki örnekte görülen erişim türüne **Dizin Listeleme (Directory Indexing)** adı verilir. Bu durum; saldırganların klasörler arasında gezinmesine, hassas bilgiler veya zafiyetli kodlar barındıran dosyalara doğrudan erişmesine olanak tanır.

**Çözüm:** Web sunucularında dizin listeleme özelliğinin kapatılması (disable directory indexing) standart bir güvenlik prosedürüdür. Böylece saldırganlar, sitenin çalışması için gereken dosyalar haricindeki içeriklere doğrudan göz atamaz.

## Kullanıcı Bilgi Toplama (User Enumeration)

Geçerli kullanıcıların bir listesini çıkarmak (enumerate), WordPress güvenlik değerlendirmesinin kritik bir aşamasıdır. Bu listeyi elde ettiğimizde:
- Varsayılan kimlik bilgilerini (credentials) tahmin edebiliriz.
- **Brute Force (Kaba Kuvvet)** parola saldırıları gerçekleştirebiliriz.

Başarılı olursak, WordPress arka ucuna bir yazar (author) veya **yönetici (administrator)** olarak giriş yapabiliriz. Bu erişim, web sitesini değiştirmek veya altındaki web sunucusunu ele geçirmek (RCE) için kullanılabilir.

Manuel kullanıcı tespiti için yaygın olarak kullanılan iki temel yöntem vardır:

---

## Yöntem 1: Yazar Kimliği (Author ID) Parametresini Kullanmak

İlk yöntem, kullanıcıya atanan kimliği (ID) ve karşılık gelen kullanıcı adını ortaya çıkarmak için yayınlanan gönderileri (post) incelemektir.

Tarayıcıda "admin tarafından (by admin)" gibi bir yazar bağlantısının üzerine fareyle geldiğinizde, sol alt köşede kullanıcının hesap bağlantısı (ve ID'si) görünür. Genellikle **admin kullanıcısına `1` numaralı kullanıcı ID'si atanır**.

Bunu URL'deki `author` parametresine kullanıcı kimliğini belirterek doğrulayabiliriz:
`http://blog.inlanefreight.com/?author=1`

### Komut Satırı (cURL) İle Doğrulama
Bu işlem komut satırından `cURL` ile de yapılabilir. Aşağıdaki HTTP yanıtındaki `Location` başlığı, bu kullanıcı kimliğinin `admin` kullanıcısına ait olduğunu doğrular.

**Mevcut Bir Kullanıcıyı Sorgulama (ID 1):**
```shell
$ curl -s -I http://blog.inlanefreight.com/?author=1

HTTP/1.1 301 Moved Permanently
Date: Wed, 13 May 2020 20:47:08 GMT
Server: Apache/2.4.29 (Ubuntu)
X-Redirect-By: WordPress
Location: http://blog.inlanefreight.com/index.php/author/admin/
Content-Length: 0
Content-Type: text/html; charset=UTF-8
```
*(Bu istek bizi kullanıcının profil sayfasına veya ana giriş sayfasına yönlendirir.)*

**Mevcut Olmayan Bir Kullanıcıyı Sorgulama (Örn: ID 100):**
```shell
$ curl -s -I http://blog.inlanefreight.com/?author=100

HTTP/1.1 404 Not Found
Date: Wed, 13 May 2020 20:47:14 GMT
Server: Apache/2.4.29 (Ubuntu)
Expires: Wed, 11 Jan 1984 05:00:00 GMT
Cache-Control: no-cache, must-revalidate, max-age=0
Link: http://blog.inlanefreight.com/index.php/wp-json/; rel="https://api.w.org/"
Transfer-Encoding: chunked
Content-Type: text/html; charset=UTF-8
```

---

## Yöntem 2: WordPress JSON Uç Noktası (REST API)

İkinci yöntem, kullanıcı listesini elde etmemizi sağlayan JSON uç noktasıyla (endpoint) etkileşime girmektir.

> [!NOTE]
> **WordPress Sürüm Farklılıkları**
> Bu özellik WordPress çekirdeğinin **4.7.1 sürümünden sonra değiştirildi**. Sonraki sürümler yalnızca bir kullanıcının yapılandırılıp yapılandırılmadığını gösterir. Bu sürümden **önceki** sürümlerde ise, gönderi (post) yayınlamış olan tüm kullanıcılar varsayılan olarak açıkça listeleniyordu.

### JSON Uç Noktasından Kullanıcı Çekme (cURL & jq)
`wp-json/wp/v2/users` yoluna istek atarak (ve çıktıyı güzelleştirmek için `jq` aracı kullanarak) sistemdeki kullanıcıları listeleyebiliriz:

```shell
$ curl -s http://blog.inlanefreight.com/wp-json/wp/v2/users | jq

[
  {
    "id": 1,
    "name": "admin",
    "url": "",
    "description": "",
    "link": "http://blog.inlanefreight.com/index.php/author/admin/",
    <SNIP>
  },
  {
    "id": 2,
    "name": "ch4p",
    "url": "",
    "description": "",
    "link": "http://blog.inlanefreight.com/index.php/author/ch4p/",
    <SNIP>
  },
<SNIP>
]
```

## WPScan Enumeration (Numaralandırma)

`--enumerate` bayrağı (flag), WordPress uygulamasının eklentiler, temalar ve kullanıcılar gibi çeşitli bileşenlerini numaralandırmak için kullanılır. Varsayılan olarak WPScan; zafiyetli eklentileri, temaları, kullanıcıları, medya dosyalarını ve yedeklemeleri tarar. Ancak belirli argümanlar verilerek tarama sınırlandırılabilir (Örneğin; tüm eklentileri taramak için `--enumerate ap` argümanı kullanılır).

> [!NOTE]
> **Thread (İş Parçacığı) Notu**
> WPScan taramalarında varsayılan iş parçacığı (thread) sayısı 5'tir. Bu değer `-t` bayrağı yardımıyla değiştirilebilir.

---

## 💻 WPScan Tarama Komutu ve Çıktısı

Hedef bir WordPress sitesine API token kullanarak yapılan standart bir numaralandırma taraması ve çıktısı şu şekildedir:

```shell
$ wpscan --url http://blog.inlanefreight.com --enumerate --api-token <WPSCAN_API_TOKEN>
```

wpscan --url http://blog.inlanefreight.com --enumerate

nuclei -u  http://<HEDEF_IP>:<PORT> -t wp-content/plugins/

nuclei -u  http://<HEDEF_IP>:<PORT> -t http/vulnerabilities/wordpress/

---
> ◀ [WordPress Temelleri ve Dosya Yapısı](01_WordPress_Temelleri_ve_Dosya_Yap%C4%B1s%C4%B1.md) · 📚 [İndeks](00_WordPress_%C4%B0ndeks.md) · [WordPress Saldırıları](03_WordPress_Sald%C4%B1r%C4%B1lar%C4%B1.md) ▶
