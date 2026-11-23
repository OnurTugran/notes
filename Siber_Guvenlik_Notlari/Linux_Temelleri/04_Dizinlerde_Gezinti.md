---
title: "Bölüm 4 - Dizinlerde Gezinti"
ana-not: "[[00_Linux_Temelleri_İndeks]]"
tarih: 2026-11-23
aliases:
  - Dizinlerde Gezinti
  - pwd cd ls
  - Linux Temelleri Bölüm 4
tags:
  - siber-guvenlik
  - linux
  - linux-temelleri
  - pwd
  - cd
  - ls
  - tree
---

# Bölüm 4 - Dizinlerde Gezinti

> ◀ [Bölüm 3 - Linux Kabuğu](03_Linux_Kabu%C4%9Fu_Shell.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md)

Grafik arayüzde fareyle gezinilir; Linux terminalinde ise dizinlerde gezinmek ve dosyalarla çalışmak için komutlar kullanılır. Bu bölüm gezinti, listeleme ve verimlilik kısayollarını anlatır.

> [!NOTE]
> Bu bölümün kısa özeti (`pwd`, `cd`, `ls`) [Temel Komutlar](../Linux_Komutlar%C4%B1/01_Temel_Komutlar.md) notunda da var. Burada her komutun çıktılarıyla ayrıntısı verilir.

## 0. Dizin ve Yol (Path) Nedir?
Linux'ta her şey dosyadır ve dosyalar **dizinlerin** (directory) içinde düzenlenir ([Bölüm 1 - FHS](01_Linux_Giri%C5%9F.md)).

- **Kök Dizin (`/`):** Tüm sistemin başlangıç noktası. Windows'taki `C:\` gibi ama Linux'ta **tek bir kök** vardır.
- **Yol (Path):** Bir dosyanın adresi.
- **Mutlak Yol (Absolute Path):** Adresi en baştan (`/`) tarif eder. Örn: `/home/mehmet/resimler/tatil.jpg`
- **Göreceli Yol (Relative Path):** Adresi "şu an bulunduğum yerden" tarif eder. Örn: `resimler/tatil.jpg`

## 1. Neredeyim? (`pwd`)
`pwd` (Print Working Directory) dosya sisteminde nerede olduğunu gösterir.
```bash
user@hackerbox:~$ pwd
/home/user
```
"Şu anda `/home/user` dizinindesin" demektir.

**Diğer faydalı komutlar:**

| Komut | Ne yapar? | Örnek |
|---|---|---|
| `realpath` | Dosyanın tam (mutlak) yolunu verir. | `realpath notlar.txt` → `/home/user/notlar.txt` |
| `basename` | Yolun sadece dosya adını verir. | `basename /home/user/notlar.txt` → `notlar.txt` |
| `dirname` | Yolun sadece dizin kısmını verir. | `dirname /home/user/notlar.txt` → `/home/user` |

## 2. Dizin Değiştirme (`cd`)
`cd` (Change Directory) bir dizinden diğerine geçiş yapar.

| Komut | Açıklama | Etkisi |
|---|---|---|
| `cd [dizin]` | Belirtilen dizine girer. | Kullanıcıyı o dizine taşır |
| `cd /tam/yol` | Mutlak yolla gider. | Tam yoldaki dizine taşır |
| `cd ..` | Bir üst dizine çıkar. | `/home/user/Downloads` → `/home/user` |
| `cd ~` | Ev dizinine döner. | `/var/www` → `/home/user` |
| `cd -` | Bir önceki konuma döner. | `/etc` → `/var/www` |

**Pratik örnekler:**
```bash
# Üst dizine çıkmak (cd ..)
user@hackerbox:/var/www$ cd ..
user@hackerbox:/var$

# Ev dizinine dönmek (cd ~) - nerede olursan ol seni /home/kullanici'ye götürür
user@hackerbox:/var/log$ cd ~
user@hackerbox:~$

# Önceki dizine dönmek (cd -)
user@hackerbox:~$ cd /etc
user@hackerbox:/etc$ cd -
/home/user
user@hackerbox:~$
```

## 3. Listeleme (`ls`)
`ls` (List) bir dizinin içindekileri gösterir. Tek başına az bilgi verir, bu yüzden parametrelerle kullanılır.

| Parametre | Açıklama | Örnek çıktı |
|---|---|---|
| `-l` | **Long:** İzinleri, sahibi, boyutu ve tarihi detaylı gösterir. | `drwxr-xr-x 2 user user 4096 ...` |
| `-a` | **All:** Gizli dosyaları (adı `.` ile başlayan) da gösterir. | `.bashrc .profile .` |
| `-h` | **Human:** Boyutu Byte yerine KB, MB olarak gösterir. | `4.0K`, `23M` |
| `-t` | **Time:** Değiştirilme tarihine göre sıralar (en yeni üstte). | tarihe göre liste |
| `-r` | **Reverse:** Sıralamayı tersine çevirir. | Z-A sıralı liste |
| `-R` | **Recursive:** Alt dizinlerin içini de listeler. | tüm dizin ağacı |
| `-S` | **Size:** Boyuta göre sıralar (büyükten küçüğe). | boyuta göre liste |

Parametreler birleştirilebilir: `ls -la`, `ls -lt`, `ls -lhS` gibi.

### Senaryo 1: Detaylı listeleme (`ls -l`)
```bash
user@hackerbox:~$ ls -l
total 16
drwxr-xr-x 2 user users 4096 Jul 29 08:24 Desktop
drwxr-xr-x 2 user users 4096 Jul 29 08:24 Documents
drwxr-xr-x 2 user users 4096 Jul 29 08:24 Downloads
drwxr-xr-x 2 user users 4096 Jul 29 08:24 Pictures
drwxr-xr-x 2 user users 4096 Jul 29 08:24 Videos
-rw-r--r-- 1 user users 2100 Aug 01 12:00 notlar.txt
-rwxr-xr-x 1 user users  500 Aug 01 11:00 script.sh
```

Sütunların anlamı:

| Sütun | Açıklama |
|---|---|
| `drwxr-xr-x` | Dosya türü ve izinler (`d`: dizin, `-`: dosya, `rwx`: izinler) |
| `2` | Sabit bağlantı (hard link) sayısı |
| `user` | Sahibi olan kullanıcı |
| `users` | Sahibi olan grup |
| `4096` | Boyut (Byte) |
| `Jul 29 08:24` | Son değiştirilme tarihi |
| `Desktop` | Dosya/dizin adı |

`Desktop`, `Documents` gibi satırlar dizin (`d`), `notlar.txt` ve `script.sh` dosyadır (`-`). İzin harfleri (`r`=4, `w`=2, `x`=1) için bkz. [Temel Komutlar - chmod](../Linux_Komutlar%C4%B1/01_Temel_Komutlar.md).

### Senaryo 2: Gizli dosyaları görme (`ls -la`)
```bash
user@hackerbox:~$ ls -la
total 32
drwxr-xr-x 5 user user 4096 Oct 20 10:00 .
drwxr-xr-x 3 root root 4096 Oct 20 09:00 ..
-rw------- 1 user user  220 Oct 20 09:00 .bash_logout
-rw------- 1 user user 3771 Oct 20 09:00 .bashrc
-rw------- 1 user user  807 Oct 20 09:00 .profile
drwxr-xr-x 2 user user 4096 Oct 20 10:00 Documents
drwxr-xr-x 2 user user 4096 Oct 20 10:00 Downloads
```
Normalde görünmeyen `.bashrc` gibi ayar dosyaları ortaya çıktı ([Bölüm 3 - .bashrc](03_Linux_Kabu%C4%9Fu_Shell.md)). Ayrıca `.` (şu anki dizin) ve `..` (üst dizin) de listelendi.

### Senaryo 3: Tarihe göre sıralama (`ls -lt`)
```bash
user@hackerbox:~$ ls -lt
total 16
-rw-r--r-- 1 user user 2100 Oct 20 12:00 notlar.txt
-rwxr-xr-x 1 user user  500 Oct 20 11:00 script.sh
drwxr-xr-x 2 user user 4096 Oct 20 10:00 Documents
drwxr-xr-x 2 user user 4096 Oct 20 10:00 Downloads
```
En son değiştirilen dosya (`notlar.txt`) en üstte.

### Senaryo 4: Tüm alt dizinleri listeleme (`ls -R`)
```bash
user@hackerbox:~$ ls -R
.:
Documents  Downloads  notlar.txt

./Documents:
Rapor.pdf  Resim.jpg

./Downloads:
installer.zip
```
Sadece bulunulan dizini değil, altındaki dizinlerin içeriğini de döker.

### Senaryo 5: Boyuta göre sıralama (`ls -lS`)
```bash
user@hackerbox:~$ ls -lS
-rw-r--r-- 1 user user 10485760 Oct 20 12:00 buyuk_dosya.iso
drwxr-xr-x 2 user user     4096 Oct 20 10:00 Documents
-rw-r--r-- 1 user user     2100 Oct 20 12:00 notlar.txt
```
En büyük dosya (`buyuk_dosya.iso`) en üstte.

> [!TIP]
> **Güvenlik gözüyle `ls -la`**
> Sızma testinde ev dizinlerinde gizli dosyalara (`.bash_history`, `.ssh`, `.bashrc`) bakmak ve boyuta/tarihe göre sıralamak (`-lS`, `-lt`) ilgi çekici dosyaları hızlıca bulmayı sağlar.

## 4. Dizin Yığını (`pushd` ve `popd`)
Bir dizinde çalışırken geçici olarak başka bir yere bakıp geri dönmek gerektiğinde kullanılır.
```bash
user@hackerbox:~/proje$ pushd /var/log
/var/log ~/proje
user@hackerbox:/var/log$ popd
~/proje
user@hackerbox:~/proje$
```
`pushd` ile `/var/log`'a geçtik ama eski yerimiz hafızaya (yığına) alındı. `popd` deyince tam kaldığımız yere (`~/proje`) döndük. (`cd -` yalnızca bir önceki konumu hatırlar; `pushd`/`popd` ise birden fazla konumu istif olarak tutar.)

## 5. Ağaç Görünümü (`tree`)
Dizin yapısını ağaç dalı gibi görselleştirir.
```bash
user@hackerbox:~$ tree -L 2
.
├── Desktop
├── Documents
│   ├── Work
│   │   └── ProjectA
│   └── Personal
│       └── Photos
└── Downloads
    └── installer.zip
```
Dizinlerin iç içe yapısını tek bakışta gösterir. `-L 2` yalnızca 2 seviye derine inmesini söyler.

> [!WARNING]
> `tree` her dağıtımda yüklü gelmeyebilir. Yoksa `sudo apt install tree` ile kurulur ([Paket Yönetimi](../Linux_Komutlar%C4%B1/03_Paket_Y%C3%B6netimi.md)).

---
> ◀ [Bölüm 3 - Linux Kabuğu](03_Linux_Kabu%C4%9Fu_Shell.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md) · [Bölüm 5 - Dosya ve Dizin İşlemleri](05_Dosya_ve_Dizin_%C4%B0%C5%9Flemleri.md) ▶
