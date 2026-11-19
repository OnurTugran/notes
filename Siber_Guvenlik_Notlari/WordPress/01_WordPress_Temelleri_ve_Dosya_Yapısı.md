---
title: "WordPress Temelleri ve Dosya Yapısı"
ana-not: "[[00_WordPress_İndeks]]"
tarih: 2026-11-19
aliases:
  - WordPress Temelleri ve Dosya Yapısı
tags:
  - siber-guvenlik
  - wordpress
  - cms
  - wp-config
  - dosya-yapisi
---

# WordPress Temelleri ve Dosya Yapısı

> 📚 [İndeks](00_WordPress_%C4%B0ndeks.md) · [WordPress Keşif ve Numaralandırma](02_WordPress_Ke%C5%9Fif_ve_Numaraland%C4%B1rma.md) ▶

## WordPress ve CMS Temelleri

## 📌 CMS (İçerik Yönetim Sistemi) Nedir?
CMS (Content Management System), sıfırdan kod yazmaya veya derin kodlama bilgisine ihtiyaç duymadan web sitesi oluşturmayı sağlayan güçlü bir araçtır. Altyapı tarafındaki "ağır işleri" üstlenerek kullanıcının tasarıma ve sunuma odaklanmasını sağlar.

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

## 🌐 WordPress'e Genel Bakış
WordPress, dünyadaki web sitelerinin yaklaşık üçte birine güç veren en popüler açık kaynaklı CMS'tir.

- **Çok Amaçlı Kullanım:** Bloglar, forumlar, e-ticaret siteleri, belge ve proje yönetimi platformları olarak kullanılabilir.
- **SEO ve Özelleştirme:** Kurumlar arasında popüler olmasının temel sebebi yüksek oranda özelleştirilebilir ve SEO uyumlu olmasıdır.
- **Tema ve Eklenti (Plugin) Ekosistemi:** Siteyi geliştirmek için devasa bir kütüphanesi vardır.
	- *Örnek Eklentiler:* WPForms (iletişim formu), MonsterInsights (Google Analytics entegrasyonu), Constant Contact (E-posta pazarlama).
- **Teknoloji Yığını:** PHP dili ile yazılmıştır. Genellikle web sunucusu olarak Apache ve veritabanı olarak MySQL üzerinde çalışır.

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

## WordPress Dosya ve Dizin Yapısı

## 🖥️ Varsayılan Kurulum ve Ortam
WordPress; Windows, Linux veya Mac OSX üzerinde çalışabilir. Bu modülde **Ubuntu Linux** web sunucusu üzerindeki varsayılan bir WordPress kurulumuna odaklanacağız.

- **Gereksinimler:** Kurulumdan önce tam olarak kurulmuş ve yapılandırılmış bir **LAMP** yığınına (Linux, Apache, MySQL ve PHP) ihtiyaç vardır.
- **Web Kök Dizini (Webroot):** Kurulumdan sonra tüm WordPress destek dosyaları ve dizinleri `/var/www/html` yolunda erişilebilir olacaktır.

---

## 🗂️ Dosya Yapısı (File Structure)
Varsayılan bir WordPress kurulumunun kök dizin yapısı ve sitenin düzgün çalışması için gereken temel dosyalar/alt dizinler aşağıdaki gibidir:

```shell
$ tree -L 1 /var/www/html
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

> [!TIP]
> **Pentest Notu**
> Güvenliği artırmak ve giriş sayfasını bulmayı zorlaştırmak için bu dosyanın adı sıkça değiştirilebilir. Yönetici panelini bulmak her zaman varsayılan yollarla mümkün olmayabilir.

---

## ⚙️ Yapılandırma Dosyası: `wp-config.php`
WordPress'in veritabanına bağlanması için gereken tüm bilgileri içerir. Veritabanı adı, sunucusu, kullanıcı adı, parolası, kimlik doğrulama anahtarları (salts) ve veritabanı tablo ön eki burada yer alır. Ayrıca sorun giderme aşamasında faydalı olabilecek **DEBUG** (hata ayıklama) modu bu yapılandırma dosyasından aktif edilebilir.

> [!WARNING]
> **Kritik Dosya**
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

> [!WARNING]
> **Numaralandırma (Enumeration) Önemi**
> Bu dizinler ve dosyalar dikkatlice numaralandırılmalıdır (enumerated). Uzaktan kod çalıştırmaya (RCE) veya diğer zafiyetlerin ve yanlış yapılandırmaların sömürülmesine yol açabilecek hassas veriler içerebilirler.

```shell
$ tree -L 1 /var/www/html/wp-content
.
├── index.php
├── plugins
└── themes
```

### 2. `wp-includes`
Yönetim bileşenleri ve web sitesine ait temalar **haricindeki** her şeyi barındırır. Sertifikalar, yazı tipleri (fontlar), JavaScript dosyaları ve widget'lar gibi çekirdek (core) dosyaların depolandığı dizindir.

```shell
$ tree -L 1 /var/www/html/wp-includes
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

> [!TIP]
> **CTF ve Pentest İpucu**
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

> [!CAUTION]
> **Pentest Notu: Rollerin Önemi**
> Sunucu üzerinde **kod çalıştırma (Code Execution / RCE)** yetkisi elde etmek için genellikle sisteme bir **Administrator (Yönetici)** olarak erişim sağlamak gerekir. Ancak, **Editörler** ve **Yazarlar** da normal kullanıcıların erişemediği, zafiyet barındıran bazı eklentilere (vulnerable plugins) erişim sağlayabilir ve bu durum sistemin ele geçirilmesine kapı aralayabilir.

## 🔍 WordPress Çekirdek Sürümü Bilgi Toplama (Core Version Enumeration)

Çalıştığımız uygulamanın türünü ve sürüm numarasını bilmek, bilgi toplama (enumeration) aşamasının en kritik parçalarından biridir. Bu bilgi; belirli sürümlere ait varsayılan parolaları denemek veya o sürüme ait **bilinen zafiyetleri (known vulnerabilities)** araştırmak için kullanılır.

Sürüm numarasını manuel olarak tespit etmek için çeşitli yöntemler vardır:

### 1. Kaynak Kod İncelemesi (Meta Generator Etiketi)
İlk ve en kolay adım sayfanın kaynak kodunu incelemektir. Sayfada herhangi bir yere sağ tıklayıp "Sayfa kaynağını görüntüle" diyerek veya **[CTRL + U]** kısayolunu kullanarak kaynak koda ulaşabilirsiniz. Ardından tarayıcıda **[CTRL + F]** ile `<meta name="generator"` etiketini arayabilirsiniz.

**HTML Kaynak Kodunda Örnek Görünüm:**
```html
...SNIP...
<link rel='https://api.w.org/' href='http://blog.inlanefreight.com/index.php/wp-json/' />
<link rel="EditURI" type="application/rsd+xml" title="RSD" href="http://blog.inlanefreight.com/xmlrpc.php?rsd" />
<link rel="wlwmanifest" type="application/wlwmanifest+xml" href="http://blog.inlanefreight.com/wp-includes/wlwmanifest.xml" />
<meta name="generator" content="WordPress 5.3.3" />
...SNIP...
```

### 2. Komut Satırı ile Tespit (cURL & Grep)
Tarayıcı yerine komut satırını (CLI) kullanarak aynı bilgiyi filtrelemek için `cURL` ve `grep` araçları kullanılabilir:

```shell
$ curl -s -X GET http://blog.inlanefreight.com | grep '<meta name="generator"'

<meta name="generator" content="WordPress 5.3.3" />
```

### 3. CSS ve JS Bağlantılarından Sürüm Tespiti
Kaynak koddaki `meta generator` etiketi gizlenmiş olsa bile, CSS (stil şablonları) ve JS (JavaScript) dosyalarına verilen bağlantılar genellikle sürüm numarasını `?ver=` parametresi olarak açığa çıkarır.

**CSS Dosyalarında Sürüm İfşası:**
```html
...SNIP...
<link rel='stylesheet' id='bootstrap-css'  href='http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/bootstrap.css?ver=5.3.3' type='text/css' media='all' />
<link rel='stylesheet' id='transportex-style-css'  href='http://blog.inlanefreight.com/wp-content/themes/ben_theme/style.css?ver=5.3.3' type='text/css' media='all' />
<link rel='stylesheet' id='transportex_color-css'  href='http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/colors/default.css?ver=5.3.3' type='text/css' media='all' />
<link rel='stylesheet' id='smartmenus-css'  href='http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/jquery.smartmenus.bootstrap.css?ver=5.3.3' type='text/css' media='all' />
...SNIP...
```

**JS Dosyalarında Sürüm İfşası:**
```html
...SNIP...
<script type='text/javascript' src='http://blog.inlanefreight.com/wp-includes/js/jquery/jquery.js?ver=1.12.4-wp'></script>
<script type='text/javascript' src='http://blog.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=1.4.1'></script>
<script type='text/javascript' src='http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/subscriber.js?ver=5.3.3'></script>
<script type='text/javascript' src='http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/jquery.validationEngine-en.js?ver=5.3.3'></script>
<script type='text/javascript' src='http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/jquery.validationEngine.js?ver=5.3.3'></script>
...SNIP...
```

> [!NOTE]
> **Ekstra Bilgi: Eski Sürümler**
> Eski WordPress sürümlerinde, sürüm bilgisini açığa çıkaran bir diğer önemli kaynak, doğrudan WordPress kök dizininde (root directory) bulunan `readme.html` dosyasıdır.

---

---
> 📚 [İndeks](00_WordPress_%C4%B0ndeks.md) · [WordPress Keşif ve Numaralandırma](02_WordPress_Ke%C5%9Fif_ve_Numaraland%C4%B1rma.md) ▶
