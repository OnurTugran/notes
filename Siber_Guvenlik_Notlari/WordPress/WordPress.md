
---
tags:
  - wordpress
  - cms
  - web-security
  - pentesting
  - siber-güvenlik
date: 2026-11-10
---

# 📝 WordPress ve CMS Temelleri

## 📌 CMS (İçerik Yönetim Sistemi) Nedir?
[[CMS]] (Content Management System), sıfırdan kod yazmaya veya derin kodlama bilgisine ihtiyaç duymadan web sitesi oluşturmayı sağlayan güçlü bir araçtır. Altyapı tarafındaki "ağır işleri" üstlenerek kullanıcının tasarıma ve sunuma odaklanmasını sağlar.

Genellikle Microsoft Word'e benzeyen **WYSIWYG** (Ne Görüyorsan Onu Alırsın) editörleri ve medya kütüphaneleri sunarlar.

### CMS'in İki Ana Bileşeni:
1. **CMA (Content Management Application):** Kullanıcının içerik eklemek ve yönetmek için etkileşime girdiği ön yüz (arayüz).
2. **CDA (Content Delivery Application):** CMA'ya girilen veriyi alıp, çalışan ve görsel olarak düzgün bir web sitesi koduna dönüştüren arka plan (backend).

### İyi Bir CMS'te Olması Gerekenler:
- **Genişletilebilirlik:** Koda dokunmadan yeni işlevler ve tasarım öğeleri ekleyebilme.
- **Kullanıcı Yönetimi:** Rol ve erişim izinlerinin ince ayarı.
- **Medya Yönetimi:** Kolay fotoğraf/video yükleme ve yerleştirme.
- **Sürüm Kontrolü ve Güvenlik:** Düzenli güncellemeler ve saldırganlara karşı yerleşik güvenlik ayarları (Hardening).

---

## 🌐 [[WordPress]]'e Genel Bakış
WordPress, dünyadaki web sitelerinin yaklaşık üçte birine güç veren en popüler açık kaynaklı CMS'tir. 

- **Çok Amaçlı Kullanım:** Bloglar, forumlar, e-ticaret siteleri, belge ve proje yönetimi platformları olarak kullanılabilir.
- **SEO ve Özelleştirme:** Kurumlar arasında popüler olmasının temel sebebi yüksek oranda özelleştirilebilir ve SEO uyumlu olmasıdır.
- **Tema ve Eklenti (Plugin) Ekosistemi:** Siteyi geliştirmek için devasa bir kütüphanesi vardır. 
	- *Örnek Eklentiler:* WPForms (iletişim formu), MonsterInsights (Google Analytics entegrasyonu), Constant Contact (E-posta pazarlama).
- **Teknoloji Yığını:** [[PHP]] dili ile yazılmıştır. Genellikle web sunucusu olarak [[Apache]] ve veritabanı olarak [[MySQL]] üzerinde çalışır.

---

## 🛡️ Güvenlik ve Hacking Perspektifi
WordPress'in en büyük avantajı olan **genişletilebilirlik (üçüncü taraf tema ve eklentiler)**, aynı zamanda onun en büyük zayıflığıdır. Bu yapı, sistemi dışarıdan gelen güvenlik açıklarına (vulnerability) son derece yatkın hale getirir.

### Güvenlik Modülü Hedefleri (Pentesting):
Bu notun ait olduğu siber güvenlik modülünde hedeflenen yetenekler şunlardır:
- WordPress çekirdek yapısını anlamak.
- Yanlış yapılandırmaları (misconfiguration) ve zafiyetleri bulmak için **manuel ve otomatik numaralandırma (enumeration)** yapmak.
- Yaygın saldırı vektörlerini uygulamak.
- Bir WordPress sitesini ve altındaki web sunucusunu tamamen ele geçirmek (compromise) için pratik beceriler geliştirmek.


---
tags:
  - wordpress
  - file-structure
  - pentesting
  - web-security
  - linux
date: 2026-11-10
---

# 📂 WordPress Dosya ve Dizin Yapısı

## 🖥️ Varsayılan Kurulum ve Ortam
WordPress; Windows, Linux veya Mac OSX üzerinde çalışabilir. Bu modülde **Ubuntu Linux** web sunucusu üzerindeki varsayılan bir WordPress kurulumuna odaklanacağız. 

- **Gereksinimler:** Kurulumdan önce tam olarak kurulmuş ve yapılandırılmış bir **[[LAMP]]** yığınına (Linux, Apache, MySQL ve PHP) ihtiyaç vardır.
- **Web Kök Dizini (Webroot):** Kurulumdan sonra tüm WordPress destek dosyaları ve dizinleri `/var/www/html` yolunda erişilebilir olacaktır.

---

## 🗂️ Dosya Yapısı (File Structure)
Varsayılan bir WordPress kurulumunun kök dizin yapısı ve sitenin düzgün çalışması için gereken temel dosyalar/alt dizinler aşağıdaki gibidir:

```shell
Disgusted@htb[/htb]$ tree -L 1 /var/www/html
.
├── index.php
├── license.txt
├── readme.html
├── wp-activate.php
├── wp-admin
├── wp-blog-header.php
├── wp-comments-post.php
├── wp-config.php
├── wp-config-sample.php
├── wp-content
├── wp-cron.php
├── wp-includes
├── wp-links-opml.php
├── wp-load.php
├── wp-login.php
├── wp-mail.php
├── wp-settings.php
├── wp-signup.php
├── wp-trackback.php
└── xmlrpc.php
```

---

## 📄 Önemli WordPress Dosyaları
Kök dizin, WordPress'in doğru çalışacak şekilde yapılandırılması için gereken hayati dosyaları içerir.

- **`index.php`**: WordPress'in ana sayfasıdır.
- **`license.txt`**: Kurulan WordPress sürümü gibi bilgi toplama (enumeration) aşamasında faydalı olabilecek bilgiler içerir.
- **`wp-activate.php`**: Yeni bir site kurulurken e-posta aktivasyon süreci için kullanılır.
- **`xmlrpc.php`**: HTTP'yi taşıma, XML'i ise kodlama mekanizması olarak kullanarak veri iletimini sağlayan eski bir özelliktir. Bu iletişim türünün yerini büyük oranda WordPress REST API almıştır, ancak hala Brute Force (Kaba Kuvvet) ve DDoS saldırıları için sıkça hedeflenir.

### 🔐 Giriş ve Yönetim (`wp-admin`)
`wp-admin` klasörü, yönetici erişimi için giriş sayfasını ve arka uç (backend) kontrol panelini barındırır. Oturum açan bir kullanıcı, atanan izinlerine göre sitede değişiklik yapabilir. Giriş sayfasına genellikle şu yollardan ulaşılır:
- `/wp-admin/login.php`
- `/wp-admin/wp-login.php`
- `/login.php`
- `/wp-login.php`

> [!tip] Pentest Notu
> Güvenliği artırmak ve giriş sayfasını bulmayı zorlaştırmak için bu dosyanın adı sıkça değiştirilebilir. Yönetici panelini bulmak her zaman varsayılan yollarla mümkün olmayabilir.

---

## ⚙️ Yapılandırma Dosyası: `[[wp-config.php]]`
WordPress'in veritabanına bağlanması için gereken tüm bilgileri içerir. Veritabanı adı, sunucusu, kullanıcı adı, parolası, kimlik doğrulama anahtarları (salts) ve veritabanı tablo ön eki burada yer alır. Ayrıca sorun giderme aşamasında faydalı olabilecek **DEBUG** (hata ayıklama) modu bu yapılandırma dosyasından aktif edilebilir.

> [!warning] Kritik Dosya
> Sızma testlerinde (LFI/RFI veya dizin gezinme zafiyetleri aracılığıyla) bu dosyanın okunması, doğrudan veritabanı ele geçirilmesine yol açar.

```php
<?php
/** <SNIP> */
/** The name of the database for WordPress */
define( 'DB_NAME', 'database_name_here' );
/** MySQL database username */
define( 'DB_USER', 'username_here' );
/** MySQL database password */
define( 'DB_PASSWORD', 'password_here' );
/** MySQL hostname */
define( 'DB_HOST', 'localhost' );
/** Authentication Unique Keys and Salts */
/* <SNIP> */
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );
/** WordPress Database Table prefix */
$table_prefix = 'wp_';
/** For developers: WordPress debugging mode. */
/** <SNIP> */
define( 'WP_DEBUG', false );
/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
    define( 'ABSPATH', __DIR__ . '/' );
}
/** Sets up WordPress vars and included files. */
require_once ABSPATH . 'wp-settings.php';
```

---

## 📁 Önemli WordPress Dizinleri

### 1. `wp-content`
Eklentilerin (plugins) ve temaların (themes) depolandığı ana dizindir. İçerisindeki `uploads/` alt dizini, genellikle platforma yüklenen tüm dosyaların saklandığı yerdir. 

> [!warning] Numaralandırma (Enumeration) Önemi
> Bu dizinler ve dosyalar dikkatlice numaralandırılmalıdır (enumerated). Uzaktan kod çalıştırmaya (RCE) veya diğer zafiyetlerin ve yanlış yapılandırmaların sömürülmesine yol açabilecek hassas veriler içerebilirler.

```shell
Disgusted@htb[/htb]$ tree -L 1 /var/www/html/wp-content
.
├── index.php
├── plugins
└── themes
```

### 2. `wp-includes`
Yönetim bileşenleri ve web sitesine ait temalar **haricindeki** her şeyi barındırır. Sertifikalar, yazı tipleri (fontlar), JavaScript dosyaları ve widget'lar gibi çekirdek (core) dosyaların depolandığı dizindir.

```shell
Disgusted@htb[/htb]$ tree -L 1 /var/www/html/wp-includes
.
├── <SNIP>
├── theme.php
├── update.php
├── user.php
├── vars.php
├── version.php
├── widgets
├── widgets.php
├── wlwmanifest.xml
├── wp-db.php
└── wp-diff.php
```

### 🎯 Hedef Dizinler (Manuel Kontrol)

Dizin listeleme (Directory Indexing) zafiyetini manuel olarak test ederken, tarayıcının adres çubuğundan sırayla kontrol edilmesi gereken temel WordPress dizinleri şunlardır:

- `/wp-admin/`
- `/wp-content/`
- `/wp-includes/`
- `/wp-content/plugins/`
- `/wp-content/themes/`

> [!tip] CTF ve Pentest İpucu
> Hedefte özellikle **`/wp-content/uploads/`** dizinine dikkat edin. CTF (Capture The Flag) yarışmalarında aranan bayrak dosyaları (`flag.txt`) veya gerçek sızma testlerinde hassas kullanıcı dosyaları genellikle buraya veya bu dizinin alt klasörlerine saklanır.


## 👥 WordPress Kullanıcı Rolleri

Standart bir WordPress kurulumunda beş temel kullanıcı tipi (rolü) bulunur:

| Rol | Açıklama |
| :--- | :--- |
| **Administrator (Yönetici)** | Web sitesindeki tüm yönetim özelliklerine erişimi vardır. Kullanıcı ve gönderi (post) ekleme/silme işlemlerinin yanı sıra **kaynak kodunu düzenleme** yetkilerini de içerir. |
| **Editor (Editör)** | Diğer kullanıcıların gönderileri de dahil olmak üzere, tüm gönderileri yayınlayabilir ve yönetebilir. |
| **Author (Yazar)** | Sadece kendi gönderilerini yayınlayabilir ve yönetebilir. |
| **Contributor (Katkıda Bulunan)** | Kendi gönderilerini yazabilir ve yönetebilir, ancak bunları **yayınlayamaz**. |
| **Subscriber (Abone)** | Gönderilere göz atabilen ve sadece kendi profillerini düzenleyebilen normal kullanıcılardır. |

> [!danger] Pentest Notu: Rollerin Önemi
> Sunucu üzerinde **kod çalıştırma (Code Execution / RCE)** yetkisi elde etmek için genellikle sisteme bir **Administrator (Yönetici)** olarak erişim sağlamak gerekir. Ancak, **Editörler** ve **Yazarlar** da normal kullanıcıların erişemediği, zafiyet barındıran bazı eklentilere (vulnerable plugins) erişim sağlayabilir ve bu durum sistemin ele geçirilmesine kapı aralayabilir.


## 🔍 WordPress Çekirdek Sürümü Bilgi Toplama (Core Version Enumeration)

Çalıştığımız uygulamanın türünü ve sürüm numarasını bilmek, bilgi toplama (enumeration) aşamasının en kritik parçalarından biridir. Bu bilgi; belirli sürümlere ait varsayılan parolaları denemek veya o sürüme ait **bilinen zafiyetleri (known vulnerabilities)** araştırmak için kullanılır.

Sürüm numarasını manuel olarak tespit etmek için çeşitli yöntemler vardır:

### 1. Kaynak Kod İncelemesi (Meta Generator Etiketi)
İlk ve en kolay adım sayfanın kaynak kodunu incelemektir. Sayfada herhangi bir yere sağ tıklayıp "Sayfa kaynağını görüntüle" diyerek veya **[CTRL + U]** kısayolunu kullanarak kaynak koda ulaşabilirsiniz. Ardından tarayıcıda **[CTRL + F]** ile `<meta name="generator"` etiketini arayabilirsiniz.

**HTML Kaynak Kodunda Örnek Görünüm:**
```html
...SNIP...
<link rel='[https://api.w.org/](https://api.w.org/)' href='[http://blog.inlanefreight.com/index.php/wp-json/](http://blog.inlanefreight.com/index.php/wp-json/)' />
<link rel="EditURI" type="application/rsd+xml" title="RSD" href="[http://blog.inlanefreight.com/xmlrpc.php?rsd](http://blog.inlanefreight.com/xmlrpc.php?rsd)" />
<link rel="wlwmanifest" type="application/wlwmanifest+xml" href="[http://blog.inlanefreight.com/wp-includes/wlwmanifest.xml](http://blog.inlanefreight.com/wp-includes/wlwmanifest.xml)" /> 
<meta name="generator" content="WordPress 5.3.3" />
...SNIP...
```

### 2. Komut Satırı ile Tespit (cURL & Grep)
Tarayıcı yerine komut satırını (CLI) kullanarak aynı bilgiyi filtrelemek için `cURL` ve `grep` araçları kullanılabilir:

```shell
Disgusted@htb[/htb]$ curl -s -X GET [http://blog.inlanefreight.com](http://blog.inlanefreight.com) | grep '<meta name="generator"'

<meta name="generator" content="WordPress 5.3.3" />
```

### 3. CSS ve JS Bağlantılarından Sürüm Tespiti
Kaynak koddaki `meta generator` etiketi gizlenmiş olsa bile, CSS (stil şablonları) ve JS (JavaScript) dosyalarına verilen bağlantılar genellikle sürüm numarasını `?ver=` parametresi olarak açığa çıkarır.

**CSS Dosyalarında Sürüm İfşası:**
```html
...SNIP...
<link rel='stylesheet' id='bootstrap-css'  href='[http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/bootstrap.css?ver=5.3.3](http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/bootstrap.css?ver=5.3.3)' type='text/css' media='all' />
<link rel='stylesheet' id='transportex-style-css'  href='[http://blog.inlanefreight.com/wp-content/themes/ben_theme/style.css?ver=5.3.3](http://blog.inlanefreight.com/wp-content/themes/ben_theme/style.css?ver=5.3.3)' type='text/css' media='all' />
<link rel='stylesheet' id='transportex_color-css'  href='[http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/colors/default.css?ver=5.3.3](http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/colors/default.css?ver=5.3.3)' type='text/css' media='all' />
<link rel='stylesheet' id='smartmenus-css'  href='[http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/jquery.smartmenus.bootstrap.css?ver=5.3.3](http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/jquery.smartmenus.bootstrap.css?ver=5.3.3)' type='text/css' media='all' />
...SNIP...
```

**JS Dosyalarında Sürüm İfşası:**
```html
...SNIP...
<script type='text/javascript' src='[http://blog.inlanefreight.com/wp-includes/js/jquery/jquery.js?ver=1.12.4-wp](http://blog.inlanefreight.com/wp-includes/js/jquery/jquery.js?ver=1.12.4-wp)'></script>
<script type='text/javascript' src='[http://blog.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=1.4.1](http://blog.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=1.4.1)'></script>
<script type='text/javascript' src='[http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/subscriber.js?ver=5.3.3](http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/subscriber.js?ver=5.3.3)'></script>
<script type='text/javascript' src='[http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/jquery.validationEngine-en.js?ver=5.3.3](http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/jquery.validationEngine-en.js?ver=5.3.3)'></script>
<script type='text/javascript' src='[http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/jquery.validationEngine.js?ver=5.3.3](http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/jquery.validationEngine.js?ver=5.3.3)'></script>
...SNIP...
```

> [!info] Ekstra Bilgi: Eski Sürümler
> Eski WordPress sürümlerinde, sürüm bilgisini açığa çıkaran bir diğer önemli kaynak, doğrudan WordPress kök dizininde (root directory) bulunan `readme.html` dosyasıdır.


---

# 🧩 Eklenti (Plugin) ve Tema Bilgi Toplama

WordPress sitelerinde yüklü olan eklentiler ve temalar, zafiyet barındırma ihtimalleri en yüksek bileşenlerdir. Bu bileşenler hakkında bilgi toplamak (enumeration) için **pasif** ve **aktif** olmak üzere iki temel yöntem kullanılır.

## 🕵️‍♂️ Pasif Bilgi Toplama (Kaynak Kodu İncelemesi)

Sayfa kaynağını manuel olarak inceleyerek veya komut satırı araçlarını (`cURL`, `grep`, `sed`, `cut`) kullanarak HTML kodu içindeki eklenti ve tema yollarını filtreleyebiliriz. Ayrıca HTTP yanıt başlıkları (response headers) da bazen belirli eklentilerin sürüm numaralarını barındırabilir.

### Eklentileri (Plugins) Filtreleme Komutu
Sayfa kaynağındaki `wp-content/plugins/` dizininden çağrılan dosyaları listelemek için:

```shell
Disgusted@htb[/htb]$ curl -s -X GET http://blog.inlanefreight.com | sed 's/href=/\n/g' | sed 's/src=/\n/g' | grep 'wp-content/plugins/*' | cut -d"'" -f2
```

### Temaları (Themes) Filtreleme Komutu
Sayfa kaynağında `themes` kelimesini geçen CSS, JS ve görsel bağlantılarını listelemek için:

```shell
Disgusted@htb[/htb]$ curl -s -X GET http://blog.inlanefreight.com | sed 's/href=/\n/g' | sed 's/src=/\n/g' | grep 'themes' | cut -d"'" -f2
```

---

## ⚡ Aktif Bilgi Toplama (Dizin / Dosya Keşfi)

Tüm yüklü eklenti ve temalar sayfa kaynağında (pasif olarak) görünmeyebilir. Bu durumda, sunucuda var olabileceğini düşündüğümüz dizin veya dosyalara aktif olarak `GET` istekleri göndermemiz gerekir.

- **Dizin/Dosya varsa:** Sunucu genellikle ya doğrudan erişim verir ya da içeriğin var olduğunu ancak erişim iznimiz olmadığını gösteren bir yönlendirme (**HTTP 301 Moved Permanently**) yanıtı döndürür.
- **Dizin/Dosya yoksa:** Sunucu **HTTP 404 Not Found** hatası döndürür.

### Yüklü Olan Bir Eklentiyi Doğrulama Komutu
Var olan bir eklenti dizinine istek atıldığında dönen HTTP başlıklarını (örneğin 301 yönlendirmesini) görmek için:

```shell
Disgusted@htb[/htb]$ curl -I -X GET http://blog.inlanefreight.com/wp-content/plugins/mail-masta
```

### Yüklü Olmayan Bir Eklentiyi Test Etme Komutu
Var olmayan bir eklenti dizinine istek atıldığında sunucunun verdiği tepkiyi (404 hatasını) görmek için:

```shell
Disgusted@htb[/htb]$ curl -I -X GET http://blog.inlanefreight.com/wp-content/plugins/someplugin
```

*(Not: Aynı aktif tespit mantığı `wp-content/themes/` dizini altındaki temalar için de geçerlidir.)*

> [!tip] Otomasyon (Pentest Notu)
> Bilgi toplama sürecini hızlandırmak için bu denemeleri tek tek elle yapmak yerine basit bir **Bash betiği (script)** yazılabilir veya süreci otomatize eden **`wfuzz`** ya da **`WPScan`** gibi güçlü araçlar kullanılabilir.


# 📂 Dizin Listeleme (Directory Indexing)

Bir WordPress web sitesini değerlendirirken (pentest aşamasında) odak noktamız sadece **aktif** eklentiler olmamalıdır. 

> [!danger] Kritik Hata: Devre Dışı Bırakılan Eklentiler
> Zafiyet barındıran bir eklentiyi sadece **devre dışı bırakmak (deactivate)**, WordPress sitesinin güvenliğini artırmaz. Eklenti devre dışı bırakılsa bile dosyalarına, script'lerine ve fonksiyonlarına dışarıdan erişilmeye devam edilebilir. En iyi güvenlik uygulaması (best practice), kullanılmayan eklentileri tamamen **kaldırmak** veya güncel tutmaktır.

Örneğin, devre dışı bırakılmış olan "Mail Masta" eklentisine tarayıcı üzerinden şu şekilde gidildiğinde dizin içeriği görüntülenebilir:
`http://<target>/wp-content/plugins/mail-masta/`

---

## 🖥️ Terminal Üzerinden Dizin Listeleme (cURL & html2text)

Tarayıcı yerine komut satırını kullanarak bir dizin listesini (directory listing) çekebilir ve HTML çıktısını `html2text` aracı ile terminalde okunabilir, temiz bir formata dönüştürebiliriz:

```shell
Disgusted@htb[/htb]$ curl -s -X GET [http://blog.inlanefreight.com/wp-content/plugins/mail-masta/](http://blog.inlanefreight.com/wp-content/plugins/mail-masta/) | html2text

****** Index of /wp-content/plugins/mail-masta ******
[[ICO]]       Name                 Last_modified    Size Description
===========================================================================
[[PARENTDIR]] Parent_Directory                         -  
[[DIR]]       amazon_api/          2020-05-13 18:01    -  
[[DIR]]       inc/                 2020-05-13 18:01    -  
[[DIR]]       lib/                 2020-05-13 18:01    -  
[[   ]]       plugin-interface.php 2020-05-13 18:01  88K  
[[TXT]]       readme.txt           2020-05-13 18:01 2.2K  
===========================================================================
     Apache/2.4.29 (Ubuntu) Server at blog.inlanefreight.com Port 80
```

### 🛡️ Dizin Listeleme Zafiyeti Nedir?
Yukarıdaki örnekte görülen erişim türüne **Dizin Listeleme (Directory Indexing)** adı verilir. Bu durum; saldırganların klasörler arasında gezinmesine, hassas bilgiler veya zafiyetli kodlar barındıran dosyalara doğrudan erişmesine olanak tanır. 

**Çözüm:** Web sunucularında dizin listeleme özelliğinin kapatılması (disable directory indexing) standart bir güvenlik prosedürüdür. Böylece saldırganlar, sitenin çalışması için gereken dosyalar haricindeki içeriklere doğrudan göz atamaz.


# 👤 Kullanıcı Bilgi Toplama (User Enumeration)

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
Disgusted@htb[/htb]$ curl -s -I [http://blog.inlanefreight.com/?author=1](http://blog.inlanefreight.com/?author=1)

HTTP/1.1 301 Moved Permanently
Date: Wed, 13 May 2020 20:47:08 GMT
Server: Apache/2.4.29 (Ubuntu)
X-Redirect-By: WordPress
Location: [http://blog.inlanefreight.com/index.php/author/admin/](http://blog.inlanefreight.com/index.php/author/admin/)
Content-Length: 0
Content-Type: text/html; charset=UTF-8
```
*(Bu istek bizi kullanıcının profil sayfasına veya ana giriş sayfasına yönlendirir.)*

**Mevcut Olmayan Bir Kullanıcıyı Sorgulama (Örn: ID 100):**
```shell
Disgusted@htb[/htb]$ curl -s -I [http://blog.inlanefreight.com/?author=100](http://blog.inlanefreight.com/?author=100)

HTTP/1.1 404 Not Found
Date: Wed, 13 May 2020 20:47:14 GMT
Server: Apache/2.4.29 (Ubuntu)
Expires: Wed, 11 Jan 1984 05:00:00 GMT
Cache-Control: no-cache, must-revalidate, max-age=0
Link: [http://blog.inlanefreight.com/index.php/wp-json/](http://blog.inlanefreight.com/index.php/wp-json/); rel="[https://api.w.org/](https://api.w.org/)"
Transfer-Encoding: chunked
Content-Type: text/html; charset=UTF-8
```

---

## Yöntem 2: WordPress JSON Uç Noktası (REST API)

İkinci yöntem, kullanıcı listesini elde etmemizi sağlayan JSON uç noktasıyla (endpoint) etkileşime girmektir. 

> [!info] WordPress Sürüm Farklılıkları
> Bu özellik WordPress çekirdeğinin **4.7.1 sürümünden sonra değiştirildi**. Sonraki sürümler yalnızca bir kullanıcının yapılandırılıp yapılandırılmadığını gösterir. Bu sürümden **önceki** sürümlerde ise, gönderi (post) yayınlamış olan tüm kullanıcılar varsayılan olarak açıkça listeleniyordu.

### JSON Uç Noktasından Kullanıcı Çekme (cURL & jq)
`wp-json/wp/v2/users` yoluna istek atarak (ve çıktıyı güzelleştirmek için `jq` aracı kullanarak) sistemdeki kullanıcıları listeleyebiliriz:

```shell
Disgusted@htb[/htb]$ curl -s [http://blog.inlanefreight.com/wp-json/wp/v2/users](http://blog.inlanefreight.com/wp-json/wp/v2/users) | jq

[
  {
    "id": 1,
    "name": "admin",
    "url": "",
    "description": "",
    "link": "[http://blog.inlanefreight.com/index.php/author/admin/](http://blog.inlanefreight.com/index.php/author/admin/)",
    <SNIP>
  },
  {
    "id": 2,
    "name": "ch4p",
    "url": "",
    "description": "",
    "link": "[http://blog.inlanefreight.com/index.php/author/ch4p/](http://blog.inlanefreight.com/index.php/author/ch4p/)",
    <SNIP>
  },
<SNIP>
]
```


# 🔐 Giriş ve Kaba Kuvvet (Login & Brute Force) Saldırıları

Geçerli kullanıcıların bir listesini (user enumeration) elde ettikten sonra, WordPress arka ucuna (backend) erişim sağlamak için bir parola kaba kuvvet (brute-forcing) saldırısı başlatabiliriz. 

Bu saldırı iki temel yol üzerinden gerçekleştirilebilir:
1. Standart giriş sayfası (`wp-login.php`) üzerinden.
2. **`xmlrpc.php`** sayfası üzerinden (Genellikle otomatik araçlar ve botnet'ler tarafından daha hızlı olduğu için tercih edilir).

---

## 🚀 `xmlrpc.php` Üzerinden Brute Force Testi (cURL)

Aşağıdaki örneklerde, `xmlrpc.php` dosyasına `wp.getUsersBlogs` metodunu kullanarak nasıl bir XML formatında POST isteği atılabileceği gösterilmiştir.

### 1. Geçerli Kimlik Bilgileri (Başarılı Giriş)
Eğer gönderdiğimiz POST isteği **doğru** kimlik bilgilerini (kullanıcı adı: `admin`, parola: `CORRECT-PASSWORD`) içeriyorsa, sunucu girişin başarılı olduğunu gösteren ve site bilgilerini içeren bir XML yanıtı döndürür:

```shell
Disgusted@htb[/htb]$ curl -X POST -d "<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>CORRECT-PASSWORD</value></param></params></methodCall>" [http://blog.inlanefreight.com/xmlrpc.php](http://blog.inlanefreight.com/xmlrpc.php)

<?xml version="1.0" encoding="UTF-8"?>
<methodResponse>
  <params>
    <param>
      <value>
      <array><data>
  <value><struct>
  <member><name>isAdmin</name><value><boolean>1</boolean></value></member>
  <member><name>url</name><value><string>[http://blog.inlanefreight.com/](http://blog.inlanefreight.com/)</string></value></member>
  <member><name>blogid</name><value><string>1</string></value></member>
  <member><name>blogName</name><value><string>Inlanefreight</string></value></member>
  <member><name>xmlrpc</name><value><string>[http://blog.inlanefreight.com/xmlrpc.php](http://blog.inlanefreight.com/xmlrpc.php)</string></value></member>
</struct></value>
</data></array>
      </value>
    </param>
  </params>
</methodResponse>
```

### 2. Geçersiz Kimlik Bilgileri (Başarısız Giriş - 403 Forbidden)
Eğer kimlik bilgileri **yanlışsa** (kullanıcı adı: `admin`, parola: `asdasd`), sunucu hatalı kullanıcı adı veya parola uyarısı barındıran bir `403 faultCode` yanıtı döndürür:

```shell
Disgusted@htb[/htb]$ curl -X POST -d "<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>asdasd</value></param></params></methodCall>" [http://blog.inlanefreight.com/xmlrpc.php](http://blog.inlanefreight.com/xmlrpc.php)

<?xml version="1.0" encoding="UTF-8"?>
<methodResponse>
  <fault>
    <value>
      <struct>
        <member>
          <name>faultCode</name>
          <value><int>403</int></value>
        </member>
        <member>
          <name>faultString</name>
          <value><string>Incorrect username or password.</string></value>
        </member>
      </struct>
    </value>
  </fault>
</methodResponse>
```

---

> [!info] Sonuç: Manuel Bilgi Toplamanın (Enumeration) Önemi
> Otomatik araçlar (WPScan, wfuzz vb.) sızma testi sürecini büyük ölçüde hızlandırsa da, değerlendirdiğimiz sistemler üzerindeki etkilerini anlamak bizim sorumluluğumuzdadır. Otomatik araçları kullanmaya çalışmadan önce **manuel yöntemleri** anlamak çok önemlidir. Manuel bilgi toplama yöntemlerini iyi kavramak, otomatik araçların düzgün çalışmadığı veya beklenmedik çıktılar verdiği durumlarda sorun gidermenize (troubleshooting) büyük ölçüde yardımcı olacaktır.



# 🔍 WPScan Enumeration (Numaralandırma)

`--enumerate` bayrağı (flag), WordPress uygulamasının eklentiler, temalar ve kullanıcılar gibi çeşitli bileşenlerini numaralandırmak için kullanılır. Varsayılan olarak WPScan; zafiyetli eklentileri, temaları, kullanıcıları, medya dosyalarını ve yedeklemeleri tarar. Ancak belirli argümanlar verilerek tarama sınırlandırılabilir (Örneğin; tüm eklentileri taramak için `--enumerate ap` argümanı kullanılır).

> [!info] Thread (İş Parçacığı) Notu
> WPScan taramalarında varsayılan iş parçacığı (thread) sayısı 5'tir. Bu değer `-t` bayrağı yardımıyla değiştirilebilir.

---

## 💻 WPScan Tarama Komutu ve Çıktısı

Hedef bir WordPress sitesine API token kullanarak yapılan standart bir numaralandırma taraması ve çıktısı şu şekildedir:

```shell
Disgusted@htb[/htb]$ wpscan --url [http://blog.inlanefreight.com](http://blog.inlanefreight.com) --enumerate --api-token Kffr4fdJzy9qVcTk
```

wpscan --url http://blog.inlanefreight.com --enumerate

nuclei -u  http://154.57.164.78:38680 -t wp-content/plugins/

nuclei -u  http://154.57.164.78:38680 -t http/vulnerabilities/wordpress/
