---
title: "Bölüm 14 - Nmap Scripting Engine (NSE)"
ana-not: "[[00_Nmap_ile_Ağ_Tarama_İndeks]]"
tarih: 2026-11-26
aliases:
  - NSE
  - Nmap Scripting Engine
  - nmap --script
  - nmap -sC
tags:
  - siber-guvenlik
  - nmap
  - ag-tarama
  - nse
  - scriptler
---

# Bölüm 14 - Nmap Scripting Engine (NSE)

> 📚 [İndeks](00_Nmap_ile_A%C4%9F_Tarama_%C4%B0ndeks.md) · ◀ [Bölüm 13 - İşletim Sistemi Tespiti](13_%C4%B0%C5%9Fletim_Sistemi_Tespiti.md)

## 1. NSE Nedir?
**Nmap Scripting Engine (NSE)**, Nmap'in en güçlü özelliklerinden biridir. Kullanıcıların çeşitli ağ görevlerini otomatikleştirmek için **basit scriptler yazmasına ve paylaşmasına** olanak tanır. Nmap ile dağıtılan geniş script setini kullanabilir ya da özel ihtiyaçlar için kendi scriptini yazabilirsin.

Scriptler **Lua** programlama dilinde yazılır.

## 2. NSE ile Yapılabilecekler

| Alan | Açıklama |
|---|---|
| **Ağ keşfi** | Hedef domain için whois sorgulama, ARIN/RIPE/APNIC'e hedef IP'yi sorgulayarak sahipliği belirleme, açık portlarda identd sorgulama, SNMP sorguları, NFS/SMB/RPC paylaşımlarını ve servislerini listeleme. |
| **Gelişmiş versiyon tespiti** | Bazı servisler tanınmak için iki bağımsız sorgu gerektirir; geleneksel versiyon tespitiyle bu mümkün değildir, NSE ile kolayca yapılır. |
| **Zafiyet tespiti** | Yeni bir zafiyet keşfedildiğinde ağlardaki zafiyete açık sistemleri hızlıca belirlemek için kullanılır. |
| **Backdoor tespiti** | Saldırganların veya otomatik solucanların bıraktığı arka kapıları, daha karmaşık olanlar dahil, güvenilir şekilde tespit etmeye yarar. |
| **Zafiyet sömürüsü** | Genel bir scripting dili olarak NSE, zafiyetleri bulmanın yanında sömürmek için de kullanılabilir. |

## 3. Script Kategorileri

| Kategori | Tanım | Örnekler |
|---|---|---|
| `auth` | Kimlik doğrulama bilgileriyle (veya bunların aşılmasıyla) ilgilenir. | `x11-access`, `ftp-anon`, `oracle-enum-users` |
| `broadcast` | Yerel ağda yayın yaparak komut satırında listelenmeyen hostları keşfeder. | `dns-sd`, `nbns-queries` |
| `brute` | Uzak sunucunun kimlik bilgilerini brute-force ile tahmin etmeye çalışır. | `http-brute`, `oracle-brute`, `snmp-brute` |
| `default` | `-sC` veya `-A` kullanıldığında çalışan varsayılan set. | `identd-owners`, `http-auth`, `ftp-anon` |
| `discovery` | Ağdaki daha fazla bilgiyi aktif olarak keşfetmeye çalışır. | `html-title`, `smb-enum-shares`, `snmp-sysdescr` |
| `dos` | Hizmet reddi (DoS) testleri yapabilir. | `http-slowloris` |
| `exploit` | Bazı zafiyetleri aktif olarak sömürmeye çalışır. | `jdwp-exec`, `http-shellshock` |
| `external` | Verileri üçüncü taraf bir veritabanına veya başka bir ağ kaynağına gönderebilir. | `whois-ip` |
| `fuzzer` | Sunucu yazılımına beklenmedik veya rastgele veri göndererek bulunmamış hataları veya zafiyetleri arar. | `dns-fuzz` |
| `intrusive` | Hedefte çökme riski taşıyan, önemli kaynak kullanabilen veya sistem yöneticilerince kötü niyetli sayılabilecek betikler. | `http-open-proxy`, `snmp-brute` |
| `malware` | Hedef platformun malware veya arka kapılarla enfekte olup olmadığını test eder. | `smtp-strangeport`, `auth-spoof` |
| `safe` | Servisleri çökertmek, çok fazla bant genişliği/kaynak kullanmak veya güvenlik açıklarından yararlanmak için tasarlanmamış betikler. | `ssh-hostkey`, `html-title` |
| `version` | Versiyon tespitinin bir uzantısı olan özel kategori; **açıkça seçilemez**. | `skypev2-version`, `pptp-version` |
| `vuln` | Bilinen zafiyetleri kontrol eder ve sonuçları raporlar. | `realvnc-auth-bypass`, `afp-path-vuln` |

### 3.1 Kategorilerin Kullanım Senaryoları
- **`auth`:** Bir sistemde anonim FTP erişimi olup olmadığını kontrol etmek için `ftp-anon`.
- **`broadcast`:** Yerel ağdaki tüm SMB sunucularını keşfetmek için.
- **`brute`:** Bir web uygulamasının zayıf parolalarla korunup korunmadığını test etmek için `http-brute`.
- **`default`:** Hedefteki servisler hakkında temel bilgileri toplamak için.
- **`discovery`:** Bir web sunucusunun HTML başlığını almak için `html-title`.
- **`dos`:** Bir web sunucusunun Slowloris saldırılarına karşı savunmasız olup olmadığını test etmek için `http-slowloris`.
- **`exploit`:** Shellshock zafiyetini test etmek için `http-shellshock`.
- **`external`:** Hedef IP'nin whois bilgilerini almak için `whois-ip`.
- **`fuzzer`:** Bir DNS sunucusunun dayanıklılığını test etmek için `dns-fuzz`.
- **`intrusive`:** Ağdaki açık HTTP proxy'leri tespit etmek için `http-open-proxy`.
- **`malware`:** Bir sistemin enfekte olup olmadığını kontrol etmek için `auth-spoof`.
- **`safe`:** Bir SSH sunucusunun anahtarını almak için `ssh-hostkey`.
- **`version`:** Belirli servislerin versiyonlarını tespit etmek için bu kategorideki scriptler otomatik çalışır.
- **`vuln`:** Sistemdeki zafiyetleri kontrol etmek için `realvnc-auth-bypass`.

> [!WARNING]
> `brute`, `dos`, `exploit`, `fuzzer` ve `intrusive` kategorileri hedefi bozabilir veya kilitleyebilir, hedefin yöneticileri tarafından saldırı olarak algılanabilir. Bu kategorileri yalnızca **yazılı izin aldığın** hedeflerde ve kendi lab ortamında kullan. Başlangıç için `safe` ve `default` kategorileri daha güvenlidir.

## 4. Uygulama
NSE scriptlerini kullanmak kolaydır:
- Yaygın scriptleri etkinleştirmek için **`-sC`**
- Belirli scriptleri veya kategorileri çalıştırmak için **`--script`**
- Scriptleri özelleştirmek için **`--script-args`** ve **`--script-args-file`**

> [!NOTE]
> Bu seçenekler yardım menüsünün `SCRIPT SCAN` bölümünde de listelenir: [Bölüm 2 - Genel Bakış](02_Genel_Bak%C4%B1%C5%9F.md). `-A` taraması da script taramasını içerir: [Bölüm 4 - Port Tarama](04_Port_Tarama.md)

### 4.1 Yaygın Scriptlerle Tarama (`-sC`)
```
root💀hackerbox:~# nmap -sC scanme.nmap.org
Starting Nmap 7.94 ( https://nmap.org ) at 2024-03-31 00:46 +03
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.24s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT      STATE SERVICE
22/tcp    open  ssh
| ssh-hostkey: 
|   1024 ac:00:a0:1a:82:ff:cc:55:99:dc:67:2b:34:97:6b:75 (DSA)
|   2048 20:3d:2d:44:62:2a:b0:5a:9d:b5:b3:05:14:c2:a6:b2 (RSA)
|   256 96:02:bb:5e:57:54:1c:4e:45:2f:56:4c:4a:24:b2:57 (ECDSA)
|_  256 33:fa:91:0f:e0:e1:7b:1f:6d:05:a2:b0:f1:54:41:56 (ED25519)
80/tcp    open  http
|_http-title: Go ahead and ScanMe!
|_http-favicon: Nmap Project
9929/tcp  open  nping-echo
31337/tcp open  Elite

Nmap done: 1 IP address (1 host up) scanned in 133.66 seconds
```

Bu komut, varsayılan setteki scriptleri kullanarak script taraması yapar. Çıktıda `ssh-hostkey` (SSH anahtarları), `http-title` (sayfa başlığı) ve `http-favicon` scriptlerinin sonuçları görünür.

### 4.2 Belirli Script Kategorisiyle Tarama (`--script`)
```
root💀hackerbox:~# nmap --script=vuln scanme.nmap.org
Starting Nmap 7.94 ( https://nmap.org ) at 2024-03-31 00:49 +03
```

Bu komut, `vuln` kategorisindeki scriptleri çalıştırarak hedefte bilinen zafiyetleri tarar. Kaynak metinde çıktının geri kalanı yoktu; burada yalnızca başlangıç satırları var.

> [!WARNING]
> Örnek hedef (`scanme.nmap.org`) yalnızca gösterim içindir. Taramayı yalnızca izinli hedeflerde ve kendi lab ortamında yap.

---
> 📚 [İndeks](00_Nmap_ile_A%C4%9F_Tarama_%C4%B0ndeks.md) · ◀ [Bölüm 13 - İşletim Sistemi Tespiti](13_%C4%B0%C5%9Fletim_Sistemi_Tespiti.md)
