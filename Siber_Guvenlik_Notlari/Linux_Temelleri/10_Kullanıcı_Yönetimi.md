---
title: "Bölüm 10 - Kullanıcı Yönetimi"
ana-not: "[[00_Linux_Temelleri_İndeks]]"
tarih: 2026-11-23
aliases:
  - Kullanıcı Yönetimi
  - passwd shadow
  - adduser useradd usermod
  - Linux Temelleri Bölüm 10
tags:
  - siber-guvenlik
  - linux
  - linux-temelleri
  - kullanici
  - passwd
  - shadow
  - usermod
  - chage
---

# Bölüm 10 - Kullanıcı Yönetimi

> ◀ [Bölüm 9 - Paket Yönetimi](09_Paket_Y%C3%B6netimi.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md)

Kullanıcı yönetimi, sistem güvenliği ve kaynakların etkin paylaşımı için hayati öneme sahiptir. Bu bölümde kullanıcıların nasıl oluşturulduğu, yönetildiği ve silindiği anlatılır.

> [!NOTE]
> `adduser`, `deluser`, `usermod -aG`, `su`, `sudo`, `chown` ve `chmod` komutlarının kısa özeti [Temel Komutlar](../Linux_Komutlar%C4%B1/01_Temel_Komutlar.md) notunda var. Bu bölüm kullanıcı bilgilerini görme, kritik dosyalar, kilitleme ve parola ömrü gibi konuları ekler.

## 0. Kullanıcı Nedir ve Neden Önemlidir?
Linux doğası gereği **çok kullanıcılı (multi-user)** bir işletim sistemidir; aynı bilgisayarda aynı anda birden fazla kişi çalışabilir ([Bölüm 1](01_Linux_Giri%C5%9F.md)).

- **Kimlik (Identity):** Her kullanıcının bir adı (username) ve benzersiz numarası (**UID**) vardır.
- **İzolasyon:** Her kullanıcının kendi dosyaları, ayarları ve ev dizini (`/home/kullaniciadi`) vardır. İzin verilmedikçe başkasının dosyalarını göremez.
- **Yetki (Privilege):** Her kullanıcının yapabilecekleri sınırlıdır. Her şeyi yalnızca **root** (süper kullanıcı) yapabilir.

Bu yapı güvenliği sağlar: bir kullanıcı hata yapsa veya zararlı yazılım bulaştırsa bile (root yetkisi yoksa) diğer kullanıcılar ve sistem etkilenmez.

### Kullanıcı türleri
- **Sistem kullanıcıları:** Kurulum sırasında sistem tarafından oluşturulur; servisleri ve uygulamaları çalıştırmak için kullanılır.
- **Normal kullanıcılar:** Yönetici tarafından oluşturulur; izinlerine göre sisteme ve kaynaklara erişir.

## 1. Kullanıcı Oluşturma
İki komut vardır: `adduser` (kolay) ve `useradd` (zor, düşük seviyeli).

**Önerilen: `adduser`** - Sorular sorar, ev dizinini otomatik ayarlar.
```bash
user@hackerbox:~$ sudo adduser mehmet
Adding user `mehmet' ...
Adding new group `mehmet' (1001) ...
Adding new user `mehmet' (1001) with group `mehmet' ...
Creating home directory `/home/mehmet' ...
Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Full Name []: Mehmet Yilmaz
Is the information correct? [Y/n] Y
```

**Alternatif: `useradd`**
```bash
user@hackerbox:~$ sudo useradd -m -s /bin/bash ali
```
- `-m` : Ev dizinini oluştur
- `-s` : Kabuğu (shell) belirle ([Bölüm 3](03_Linux_Kabu%C4%9Fu_Shell.md))

> [!WARNING]
> `useradd`'ı `-m` olmadan çalıştırırsan ev dizini oluşmaz; `-s` verilmezse kabuk `/bin/sh` olabilir. Parolayı da ayrıca `sudo passwd ali` ile belirlemek gerekir.

## 2. Kullanıcı Bilgilerini Görme (`id`, `who`, `w`, `last`)
**`id`:** Kullanıcının kimliğini ve gruplarını gösterir.
```bash
user@hackerbox:~$ id mehmet
uid=1001(mehmet) gid=1001(mehmet) groups=1001(mehmet)
```

**`who`:** Kimler bağlı?
```bash
user@hackerbox:~$ who
root     pts/0        2023-10-01 10:00 (192.168.1.5)
mehmet   pts/1        2023-10-01 10:05 (192.168.1.6)
```

**`w`:** Kimler ne yapıyor?
```bash
user@hackerbox:~$ w
 10:10:01 up 1 day,  2 users,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/0    192.168.1.5      10:00    1.00s  0.10s  0.00s w
mehmet   pts/1    192.168.1.6      10:05    2:00   0.05s  0.05s top
```

**`last`:** Kim ne zaman girdi?
```bash
user@hackerbox:~$ last
mehmet   pts/1        192.168.1.6      Sun Oct  1 10:05   still logged in
```

| Komut | Soru |
|---|---|
| `id` | Bu kullanıcı kim, hangi gruplarda? |
| `who` | Şu an kimler bağlı? |
| `w` | Bağlı olanlar ne çalıştırıyor? |
| `last` | Geçmişte kim, ne zaman, nereden girdi? |

> [!TIP]
> `who`, `w` ve `last` olay müdahalesinde (incident response) şüpheli oturumları görmek için ilk bakılan komutlardır. `last` verisi `/var/log/wtmp` dosyasından gelir ([Sistem Logları](../Linux_Komutlar%C4%B1/13_Sistem_Loglar%C4%B1.md)).

## 3. Kritik Dosyalar
Kullanıcı yönetiminde bilinmesi gereken üç temel nokta:

### a) `/etc/passwd`
Sistemdeki tüm kullanıcıların temel bilgilerini saklar; **herkes tarafından okunabilir.** Her satır bir kullanıcıdır ve `:` ile ayrılmış **7 alandan** oluşur.
```bash
root@hackerbox:~$ cat /etc/passwd | tail -n 5
cups-pk-helper:x:111:117:user for cups-pk-helper service,,,:/home/cups-pk-helper:/bin/false
gnome-initial-setup:x:112:118::/run/gnome-initial-setup/:/bin/false
gdm:x:113:119:Gnome Display Manager:/var/lib/gdm3:/bin/false
mehmet:x:1001:1001:Mehmet Yilmaz,,,:/home/mehmet:/bin/bash
ali:x:1002:1002::/home/ali:/bin/sh
```

| Sıra | Alan | Örnek | Açıklama |
|---|---|---|---|
| 1 | Kullanıcı adı | `mehmet` | Giriş adı, 1-32 karakter. |
| 2 | Parola | `x` | Eskiden parola burada yazardı. Şimdi `x`; asıl parola `/etc/shadow`'da. |
| 3 | UID | `1001` | Kullanıcı kimlik numarası. **Root her zaman `0`.** |
| 4 | GID | `1001` | Birincil grubun kimlik numarası. |
| 5 | Açıklama | `Mehmet Yilmaz,,,` | (GECOS) Tam ad, telefon gibi ek bilgiler. |
| 6 | Ev dizini | `/home/mehmet` | Giriş yapınca düşülen dizin. |
| 7 | Kabuk (Shell) | `/bin/bash` | Kullanıcının komut yorumlayıcısı. |

> [!TIP]
> Servis hesaplarında kabuk genelde `/bin/false` veya `/usr/sbin/nologin`'dir; yani bu hesaplarla etkileşimli giriş yapılamaz. [Metin İşleme](../Linux_Komutlar%C4%B1/02_Metin_%C4%B0%C5%9Fleme_ve_Regex.md) notundaki `grep -v "false\|nologin"` örneği gerçek kullanıcıları ayıklamak için bunu kullanır.

### b) `/etc/shadow`
Kullanıcıların parolalarının **hash (kriptolanmış)** halini ve parola geçerlilik sürelerini saklar. Güvenlik nedeniyle **sadece root okuyabilir.**
```
mehmet:$6$aB1...:19450:0:99999:7:::
```

| Alan | Açıklama |
|---|---|
| Kullanıcı adı | Oturum açma adı. |
| Parola hash'i | `$6$` ile başlıyorsa **SHA-512**. `!` veya `*` varsa hesap **kilitli.** |
| Son değişiklik | 1 Ocak 1970'ten (Epoch) bu yana geçen gün sayısı. |
| Min gün | Parola tekrar değiştirilmeden önce geçmesi gereken minimum gün. |
| Max gün | Parolanın geçerli olduğu maksimum gün (dolunca değiştirmek zorunlu). |
| Uyarı | Süre dolmadan kaç gün önce uyarı verileceği. |

(Satırın sonundaki boş alanlar: parola süresi dolduktan sonra hesabın kilitleneceği gün sayısı, hesabın son kullanma tarihi ve ayrılmış alan.)

> [!WARNING]
> **Güvenlik notu**
> `/etc/shadow` okunabilir veya yazılabilir durumdaysa, hash'ler çevrimdışı kırılabilir ya da parola doğrudan değiştirilebilir. Hash'lerin nasıl çalıştığı için: [Hash Fonksiyonlar](../Kriptoloji/05_Hash_Fonksiyonlar_ve_Algoritmalar%C4%B1.md). Yetki yükseltmede bu dosyalar ilk kontrol edilenlerdendir: [Yetki Yükseltme](../Yetki%20Y%C3%BCkseltme/00_Yetki_Y%C3%BCkseltme.md).

### c) `/etc/skel` (Skeleton)
Yeni bir kullanıcı oluşturulduğunda (örn. `useradd -m`) bu dizindeki **her şey** yeni kullanıcının ev dizinine kopyalanır. Örneğin `.bashrc`'yi buraya koyarsan yeni açılan her kullanıcı bu ayarı otomatik alır ([Bölüm 3 - .bashrc](03_Linux_Kabu%C4%9Fu_Shell.md)).

## 4. Kullanıcıyı Değiştirme (`usermod`)
Mevcut bir kullanıcıyı değiştirmek için kullanılır.

| Komut | Etkisi |
|---|---|
| `sudo usermod -aG sudo mehmet` | Gruba ekler (`-a` append, `-G` grup). Mehmet artık yönetici yetkisine sahip. |
| `sudo usermod -L mehmet` | Hesabı **kilitler** (Lock). Mehmet giriş yapamaz. |
| `sudo usermod -U mehmet` | Kilidi **açar** (Unlock). |

> [!WARNING]
> `-a` olmadan `usermod -G` kullanıcıyı **diğer tüm gruplarından çıkarıp** yalnızca verilen gruba koyar. Her zaman `-aG` kullan. Not: `sudo` grubu Debian/Ubuntu içindir; Red Hat ailesinde yönetici grubu `wheel`'dir ([Bölüm 2](02_Linux_Da%C4%9F%C4%B1t%C4%B1mlar%C4%B1.md)).

## 5. Parola Zamanını Düzenleme (`chage`)
Kullanıcının parolasını ne zaman değiştirmesi gerektiğini ayarlar.
```bash
user@hackerbox:~$ sudo chage -l mehmet
Last password change                : Aug 01, 2023
Password expires                    : never
Password inactive                   : never
Account expires                     : never
```
**Örnek:** Parola 90 günde bir değişsin:
```bash
sudo chage -M 90 mehmet
```
`-M` maksimum gün sayısıdır (`/etc/shadow`'daki "Max gün" alanı).

## 6. Kullanıcı Silme
```bash
sudo userdel -r mehmet
```
`-r` ev dizinini (`/home/mehmet`) ve içindeki dosyaları da siler. Kullanılmazsa dosyalar kalır. (Debian'da `deluser` aynı işi yapar: [Temel Komutlar](../Linux_Komutlar%C4%B1/01_Temel_Komutlar.md).)

## 7. Hızlı Özet

| İş | Komut |
|---|---|
| Kullanıcı oluştur | `sudo adduser <ad>` / `sudo useradd -m -s /bin/bash <ad>` |
| Kimlik ve grupları gör | `id <ad>` |
| Kim bağlı / ne yapıyor | `who` / `w` |
| Giriş geçmişi | `last` |
| Gruba ekle | `sudo usermod -aG <grup> <ad>` |
| Kilitle / aç | `sudo usermod -L <ad>` / `-U` |
| Parola süresi | `sudo chage -l <ad>` / `chage -M 90 <ad>` |
| Sil (ev dizini dahil) | `sudo userdel -r <ad>` |

---
> ◀ [Bölüm 9 - Paket Yönetimi](09_Paket_Y%C3%B6netimi.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md)
