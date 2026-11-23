---
title: "Bölüm 6 - Dosya ve Dizinleri Bulma"
ana-not: "[[00_Linux_Temelleri_İndeks]]"
tarih: 2026-11-23
aliases:
  - Dosya Bulma
  - find locate which
  - Linux Temelleri Bölüm 6
tags:
  - siber-guvenlik
  - linux
  - linux-temelleri
  - find
  - locate
  - which
  - grep
  - xargs
---

# Bölüm 6 - Dosya ve Dizinleri Bulma

> ◀ [Bölüm 5 - Dosya ve Dizin İşlemleri](05_Dosya_ve_Dizin_%C4%B0%C5%9Flemleri.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md)

Dosya ve dizin arama, dosya sistemi üzerinde etkili gezinmeyi sağlar. Bu bölümde `find`, `locate`, `which` ve dosya **içinde** arama yapan `grep` anlatılır; her biri farklı bir ihtiyaca uygundur.

| Komut | Ne arar? | Hızı / Özelliği |
|---|---|---|
| `find` | Dosya/dizinleri **kritere göre** (ad, tür, boyut, sahip...) | Derin ve kapsamlı, canlı tarar |
| `locate` | Dosya **adına** göre | Çok hızlı, veritabanından okur (güncel olmayabilir) |
| `which` | **Komutun** yolunu | Hangi sürümün çalıştığını gösterir |
| `grep` | Dosyanın **içindeki** metni | Satır bazlı, regex destekler |

## 1. `find` ile Arama
Belirli kriterlere göre dosya ve dizin arar; büyük ve karmaşık dosya sistemlerinde etkilidir.

**Genel kullanım:** `find [NEREDE] [KRİTER] [EYLEM]`

### Temel aramalar

| Kriter | Örnek komut | Açıklama |
|---|---|---|
| İsim | `find / -name "notlar.txt"` | Tam adı `notlar.txt` olanı bulur. |
| İsim (harf duyarsız) | `find . -iname "ReSiM.Jpg"` | Büyük/küçük harfe takılmaz. |
| Tür | `find /home -type d` | Sadece dizinler (`d`). Dosya için `f`. |
| Boyut | `find / -size +100M` | 100 MB'tan büyük dosyalar. |
| Kullanıcı | `find /var -user www-data` | Sahibi `www-data` olanlar. |

**İsimle arama (`-name`):**
```bash
user@hackerbox:~$ find . -name "*.txt"
./Belgeler/notlar.txt
./Masaüstü/yapilacaklar.txt
./İndirilenler/liste.txt
./.cache/gecici.txt
```
Bulunulan dizin ve altındaki tüm dizinlerde `.txt` ile biten dosyalar listelendi.

**Türe göre arama (`-type`):** Sadece `ssh` adındaki dizinler:
```bash
user@hackerbox:~$ find /etc -type d -name "ssh"
/etc/ssh
```
`-type d` demeseydik `ssh` adlı dosya veya linkleri de bulabilirdi.

**Boyuta göre arama (`-size`):** 1 GB'tan büyük dosyalar:
```bash
user@hackerbox:~$ find / -size +1G
/var/log/syslog.1
/home/user/film.mkv
/var/lib/mysql/ibdata1
```
Disk dolduğunda yer açmak için en çok kullanılan yöntemdir.

> [!TIP]
> `find / ...` komutları yetkisiz dizinlerde "Permission denied" hataları basar. Bunları gizlemek için sonuna `2>/dev/null` ekle (SUID örneği: [Linux Komutları indeksi](../Linux_Komutlar%C4%B1/00_Linux_Komutlar%C4%B1.md)).

### Mantıksal operatörler (AND, OR, NOT)

| Operatör | Anlamı | Örnek |
|---|---|---|
| `-a` (AND) | **VE.** Varsayılandır. | `find . -name "*.txt" -size +1M` |
| `-o` (OR) | **VEYA** | `find . -name "*.jpg" -o -name "*.png"` |
| `-not` (NOT) | **DEĞİL** | `find . -not -user root` |

```bash
# VE: hem .txt hem 1MB'tan büyük
user@hackerbox:~$ find . -name "*.txt" -size +1M
./buyuk_notlar.txt

# VEYA: jpg veya png
user@hackerbox:~$ find . -name "*.jpg" -o -name "*.png"
./resim.jpg
./logo.png
./screenshot.png

# DEĞİL: sahibi root olmayanlar
user@hackerbox:~$ find . -not -user root
./home/user/dosya.txt
./tmp/test.tmp
```

### Bulunan dosyaya komut uygulama (`-exec` ve `xargs`)
`find`'ın en güçlü özelliğidir: bulduğu **her dosya için bir komut çalıştırır.**

**Örnek:** `.tmp` uzantılı tüm dosyaları bul ve sil.
```bash
user@hackerbox:~$ find . -name "*.tmp" -exec rm {} \;
```
- `{}` : Bulunan dosya adının yerini tutar.
- `\;` : Komutun bittiğini belirtir.

**`xargs` kullanımı:** Çok fazla dosya bulunduğunda daha performanslıdır (tek tek yerine toplu çalıştırır).
```bash
user@hackerbox:~$ find . -name "*.log" | xargs rm
```

> [!WARNING]
> `rm` içeren `-exec`/`xargs` komutlarını **önce `rm` olmadan** çalıştırıp listeyi kontrol et; silinen geri gelmez ([Bölüm 5 - rm](05_Dosya_ve_Dizin_%C4%B0%C5%9Flemleri.md)). Dosya adlarında boşluk varsa `xargs` bozulabilir; güvenli hali: `find . -name "*.log" -print0 | xargs -0 rm`.

## 2. `locate` ile Arama
Sistemdeki dosyaları `updatedb` adlı bir **veritabanı** üzerinden hızlıca bulur. Bu veritabanı dosyaların dizinini tutar.
```bash
user@hackerbox:~$ locate notlar.txt
/home/user/Belgeler/notlar.txt
/home/user/Yedek/notlar.txt
```
Hızlıdır ama veritabanı **periyodik** güncellendiği için çok yeni dosyaları bulamayabilir. Elle güncellemek için:
```bash
sudo updatedb
```

| | `find` | `locate` |
|---|---|---|
| Çalışma şekli | Diski canlı tarar | Hazır veritabanına bakar |
| Hız | Yavaş | Çok hızlı |
| Güncellik | Her zaman güncel | `updatedb`'ye bağlı |
| Kriter | Ad, boyut, sahip, tür... | Sadece ad/yol |

## 3. `which` ile Komut Yolunu Bulma
Bir komutun **hangi yolda** olduğunu gösterir; birden fazla sürümü kurulu programlardan hangisinin kullanıldığını anlamak için yararlıdır.
```bash
user@hackerbox:~$ which python
/usr/bin/python
```
Varsayılan python sürümünün konumunu öğrenmek için kullanılır. `which`, `$PATH` içindeki dizinlere sırayla bakar ([Bölüm 3 - $PATH](03_Linux_Kabu%C4%9Fu_Shell.md)). Komutun türünü (alias/builtin/dosya) görmek için `type` kullanılır.

## 4. `grep` ile Dosya İÇİNDE Arama
Dosya **adını değil**, dosyanın **içindeki yazıyı** arar.

**Örnek:** `server.log` içinde "Error" geçen satırlar.
```bash
user@hackerbox:~$ grep "Error" server.log
[2023-10-01 10:00] Error: Connection timeout
[2023-10-01 10:05] Error: Database unreachable
[2023-10-01 10:10] Error: Service stopped
```

### Regex (düzenli ifade) tablosu

| Sembol | Anlamı | Örnek komut | Eşleşen |
|---|---|---|---|
| `^` | Satır başı | `grep "^Hata" log.txt` | "Hata oluştu" ✅, "Bir Hata" ❌ |
| `$` | Satır sonu | `grep "tamam$" log.txt` | "İşlem tamam" ✅, "tamamlandı" ❌ |
| `.` | Herhangi bir karakter | `grep "k.t" kelimeler.txt` | kat, küt, kıt |
| `*` | Öncekinin tekrarı | `grep "ab*" kod.txt` | a, ab, abb, abbb |

**Satır başı ile arama:**
```bash
user@hackerbox:~$ grep "^User" config.txt
User: admin
User: guest
```

**Tüm dizinde arama (`-r`):** `/var/log` altındaki tüm dosyalarda "error" (harf duyarsız, `-i`).
```bash
user@hackerbox:~$ grep -r -i "error" /var/log/
/var/log/syslog:Oct 1 12:00:00 Error: Disk full
/var/log/auth.log:Oct 1 12:05:00 Failed password
/var/log/kern.log:Oct 1 12:10:00 I/O Error
```

> [!TIP]
> `grep`'in `-i`, `-n`, `-c`, `-e` bayrakları: [Temel Komutlar](../Linux_Komutlar%C4%B1/01_Temel_Komutlar.md). Gruplama `( )`, karakter sınıfı `[ ]`, `{ }`, `\b` ve `grep -E` gibi gelişmiş regex: [Metin İşleme ve Regex](../Linux_Komutlar%C4%B1/02_Metin_%C4%B0%C5%9Fleme_ve_Regex.md). Log dosyaları için: [Sistem Logları](../Linux_Komutlar%C4%B1/13_Sistem_Loglar%C4%B1.md).

## Özet
- **`find`:** Derinlemesine, kriter bazlı arama; `-exec`/`xargs` ile toplu işlem.
- **`locate`:** Hızlı sonuç; en güncel sonucu garanti etmez (`updatedb`).
- **`which`:** Komutların sistemdeki yerini bulur.
- **`grep`:** Dosya içeriğinde metin/regex arar.

---
> ◀ [Bölüm 5 - Dosya ve Dizin İşlemleri](05_Dosya_ve_Dizin_%C4%B0%C5%9Flemleri.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md) · [Bölüm 7 - Metin Düzenleme](07_Metin_D%C3%BCzenleme.md) ▶
