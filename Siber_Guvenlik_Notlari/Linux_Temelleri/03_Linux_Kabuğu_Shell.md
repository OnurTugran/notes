---
title: "Bölüm 3 - Linux Kabuğu (Shell)"
ana-not: "[[00_Linux_Temelleri_İndeks]]"
tarih: 2026-11-23
aliases:
  - Linux Kabuğu
  - Shell
  - Bash
  - Linux Temelleri Bölüm 3
tags:
  - siber-guvenlik
  - linux
  - linux-temelleri
  - shell
  - bash
---

# Bölüm 3 - Linux Kabuğu (Shell)

> ◀ [Bölüm 2 - Linux Dağıtımları](02_Linux_Da%C4%9F%C4%B1t%C4%B1mlar%C4%B1.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md)

## 1. Kabuk Nedir ve Neden İhtiyacımız Var?
Donanım (işlemci, RAM, disk) tek başına işe yaramaz; onu yönetecek bir **çekirdek (kernel)** gerekir. Çekirdek ise yalnızca makine dilinden (0 ve 1) anlar. **Kabuk (Shell)** burada devreye girer.

> [!NOTE]
> **Kabuğun görevi: tercümanlık**
> Yazdığın insan diline yakın komutları (örn. `ls` - listele) çekirdeğin anlayacağı makine diline çevirir. Çekirdek işlemi yapınca sonucu alıp sana okunur şekilde ekrana basar.

Katmanlardaki yeri için bkz. [Bölüm 1 - Mimari](01_Linux_Giri%C5%9F.md). Linux'ta iki ana kabuk tipi vardır: **CLI** ve **GUI**.

### Komut Satırı (CLI)
Metin tabanlı iletişim yöntemidir. **Terminal** adlı bir pencere açılır ve komutlar yazılarak sistem yönetilir.

**Neden CLI kullanmalı?**
- **Hız:** Dosya kopyalamak, program kurmak klavyeyle çok daha hızlıdır.
- **Otomasyon:** Yüzlerce işlem tek bir komut dosyası (script) ile yapılabilir.
- **Uzaktan erişim:** Sunuculara SSH ile bağlanınca grafik arayüz yoktur, yalnızca komut satırı vardır.

### Grafik Arayüzü (GUI)
Windows veya macOS'taki gibi pencereler, ikonlar ve fare ile yönetilen arayüz (GNOME, KDE, XFCE gibi masaüstü ortamları).

## 2. Kabuk Çeşitleri
Linux'ta tek bir kabuk yoktur, ihtiyaca göre değiştirilebilir:

| Kabuk | Özellik |
|---|---|
| **Bash** (Bourne Again Shell) | En yaygın ve standart kabuk; çoğu dağıtımda varsayılan. |
| **Zsh** (Z Shell) | Bash ile uyumlu; daha gelişmiş otomatik tamamlama ve tema desteği. macOS varsayılanı. |
| **Fish** | Kullanıcı dostu; yazarken renklendirir ve öneride bulunur. |

Hangi kabuğu kullandığını öğrenmek için:
```bash
user@hackerbox:~$ echo $SHELL
/bin/bash
```
Çıktı `/bin/bash` ise aktif kabuk Bash'tir.

## 3. Terminal Nedir?
Terminal uygulaması, kullanıcının kabukla etkileşim kurmasını sağlayan **kapıdır**: komutların girildiği ve çıktıların görüldüğü metin tabanlı arayüzdür. (Terminal = pencere, Shell = içinde çalışan komut yorumlayıcı.)

### Faydalı terminal komutları

**`clear`** - Ekranı temizler; önceki çıktılar silinir, imleç en üste taşınır.
```bash
user@hackerbox:~$ clear
```

**`history`** - Daha önce çalıştırılan komutların listesini verir.
```bash
user@hackerbox:~$ history
  1  ls
  2  cd Documents
  3  pwd
  4  ping google.com
  5  cat /etc/passwd
  ...
```
Soldaki numara komutun sırasıdır. `!4` yazıp Enter'a basarsan 4. sıradaki `ping google.com` yeniden çalışır.

**`type`** - Bir komutun gerçekte ne olduğunu (alias, builtin, dosya) gösterir.
```bash
user@hackerbox:~$ type ls
ls is aliased to `ls --color=auto'
user@hackerbox:~$ type cd
cd is a shell builtin
user@hackerbox:~$ type grep
grep is /usr/bin/grep
```
- `ls` → renkli çıktı için bir **takma ad (alias)**
- `cd` → kabuğun içine gömülü komut (**builtin**)
- `grep` → diskte duran gerçek bir **program** (`/usr/bin/grep`)

## 4. Kabuk Konfigürasyon Dosyaları (`.bashrc`, `.zshrc`)
Terminal her açıldığında kabuk, ev dizinindeki (`/home/kullanici`) gizli ayar dosyalarını okur. Kendi kısayollarını buraya ekleyebilirsin.
- Bash için: `~/.bashrc`
- Zsh için: `~/.zshrc`

**Örnek senaryo:** Her seferinde `clear` yazmak yerine sadece `c` yazmak.
1. Dosyayı aç: `nano ~/.bashrc`
2. En alta ekle: `alias c='clear'`
3. Kaydedip çık: `CTRL+O`, `Enter`, `CTRL+X`
4. Ayarları yükle: `source ~/.bashrc`
5. Artık `c` yazınca ekran temizlenir.

Bu yöntemle uzun ve karmaşık komutlar kısaltılabilir.

## 5. Çevre Değişkenleri (Environment Variables)
İşletim sisteminin ve programların çalışma şeklini etkileyen dinamik değerlerdir.

| Değişken | Açıklama |
|---|---|
| `$HOME` | Kullanıcının ev dizini |
| `$USER` | O anki kullanıcı adı |
| `$PWD` | Bulunulan dizin (Present Working Directory) |
| `$SHELL` | Kullanılan kabuk programı |
| `$PATH` | Komutların arandığı dizinler listesi |

```bash
user@hackerbox:~$ echo $HOME
/home/user
user@hackerbox:~$ echo $USER
user
user@hackerbox:~$ echo $PWD
/home/user/Desktop
user@hackerbox:~$ printenv PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```
`echo` değişkenin değerini ekrana basar. `$PATH` çıktısında aralarında `:` olan dizin yolları görünür; bir komut yazdığında Linux bu dizinlere **sırayla** bakar.

> [!WARNING]
> **Güvenlik notu**
> `$PATH` sırası önemlidir. Yazma izni olan bir dizin PATH'in başında yer alırsa, aynı isimli zararlı bir program gerçek komutun yerine çalışabilir (PATH hijacking). Bkz. [Yetki Yükseltme](../Yetki%20Y%C3%BCkseltme/00_Yetki_Y%C3%BCkseltme.md).

## 6. Komut Zincirleme Operatörleri
Birden fazla komutu tek satırda çalıştırmak için kullanılır.

| Operatör | Anlamı | Örnek |
|---|---|---|
| `;` | Sıralı çalıştır. Birincisi hata verse bile ikincisi çalışır. | `echo "Birinci Komut" ; date` |
| `&&` | Birincisi **başarılıysa** ikincisini çalıştır. | `mkdir test_dizini && cd test_dizini` |
| `\|\|` | Birincisi **hata verirse** ikincisini çalıştır (genelde hata mesajı için). | `cd olmayan_dizin \|\| echo "Böyle bir dizin yok!"` |

```bash
user@hackerbox:~$ echo "Birinci Komut" ; date
Birinci Komut
Fri Oct 20 14:30:00 UTC 2023

user@hackerbox:~$ mkdir test_dizini && cd test_dizini
user@hackerbox:~/test_dizini$ pwd
/home/user/test_dizini

user@hackerbox:~$ cd olmayan_dizin || echo "Böyle bir dizin yok!"
-bash: cd: olmayan_dizin: No such file or directory
Böyle bir dizin yok!
```
- `&&` örneğinde `mkdir` başarılı olduğu için `cd` çalıştı ve yeni dizine girildi.
- `||` örneğinde `cd` dizini bulamayıp hata verdi, bunun üzerine `echo` çalıştı.

> [!TIP]
> Pipe (`|`) ile karıştırma: pipe bir komutun **çıktısını** diğerine girdi yapar, `||` ise "hata verirse" demektir. Pipe için: [Temel Komutlar](../Linux_Komutlar%C4%B1/01_Temel_Komutlar.md).

## 7. Betiklerin Başlangıcı (Shebang `#!`)
Bir metin dosyasının **kabuk betiği (script)** olduğunu belirtmek için ilk satırına **Shebang** (`#!`) yazılır.

```bash
#!/bin/bash
echo "Merhaba Dünya"
```
Bu satır sisteme "bu dosyayı `/bin/bash` programıyla çalıştır" der. Çalıştırmak için betiğe `chmod +x` ile execute izni verilir; gerçek kullanım örneği: [Yedekleme ve Rsync](../Linux_Komutlar%C4%B1/10_Yedekleme_ve_Rsync.md) (cron ile otomatik yedek betiği).

---
> ◀ [Bölüm 2 - Linux Dağıtımları](02_Linux_Da%C4%9F%C4%B1t%C4%B1mlar%C4%B1.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md)
