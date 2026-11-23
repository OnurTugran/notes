---
title: "Bölüm 5 - Dosya ve Dizin İşlemleri"
ana-not: "[[00_Linux_Temelleri_İndeks]]"
tarih: 2026-11-23
aliases:
  - Dosya ve Dizin İşlemleri
  - touch mkdir cp mv rm
  - Hard Link Soft Link
  - Linux Temelleri Bölüm 5
tags:
  - siber-guvenlik
  - linux
  - linux-temelleri
  - touch
  - mkdir
  - cp
  - mv
  - rm
  - inode
  - link
  - dd
---

# Bölüm 5 - Dosya ve Dizin İşlemleri

> ◀ [Bölüm 4 - Dizinlerde Gezinti](04_Dizinlerde_Gezinti.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md)

## 0. Dosya ve Inode Nedir?
Linux'ta "her şey bir dosyadır" ([Bölüm 1](01_Linux_Giri%C5%9F.md)): resimler, metinler, hatta klavye ve disk gibi donanımlar bile.

**Inode (Index Node)** dosyanın **kimlik kartıdır.** Bir dosya oluşturunca diskte iki şey oluşur:
- **Veri:** Dosyanın içindeki gerçek bilgi.
- **Inode:** Sahibi, boyutu, izinleri, diskteki adresi gibi üst bilgileri tutan kayıt. **Dosya adı sadece bu inode'a işaret eden bir etikettir.**

> [!NOTE]
> Inode kavramı ve inode limiti sorunu için: [Dosya Sistemi ve Disk Yönetimi](../Linux_Komutlar%C4%B1/05_Dosya_Sistemi_ve_Disk.md). Aşağıdaki hard link / soft link konusu bu fikre dayanır.

## 1. Dosya Oluşturma (`touch`)
Dosya varsa **tarihini günceller**, yoksa **boş bir dosya oluşturur.**
```bash
user@hackerbox:~$ touch notlar.txt
user@hackerbox:~$ ls -l notlar.txt
-rw-r--r-- 1 user user 0 Aug 01 12:00 notlar.txt
```
Boyutu 0 olan bir dosya oluştu.

**Toplu oluşturma:** Süslü parantez `{}` ile tek seferde birden fazla dosya.
```bash
user@hackerbox:~$ touch dosya{1..3}.txt
user@hackerbox:~$ ls
dosya1.txt  dosya2.txt  dosya3.txt
```

## 2. Dizin Oluşturma (`mkdir`)
```bash
user@hackerbox:~$ mkdir projeler
```
**İç içe dizin (`-p`, parents):** Normalde `mkdir a/b/c` hata verir çünkü `a` yoktur. `-p` tüm yolu sırayla oluşturur.
```bash
user@hackerbox:~$ mkdir -p projeler/python/uygulama
user@hackerbox:~$ tree projeler
projeler
└── python
    └── uygulama
```

## 3. Kopyalama (`cp`)

| Komut | Açıklama |
|---|---|
| `cp dosya.txt yedek.txt` | Dosyayı kopyalar. |
| `cp -r dizin/ yedek_dizin/` | Dizini ve içindekileri kopyalar (**R**ecursive). |
| `cp -i dosya.txt hedef/` | Hedefte aynısı varsa üzerine yazmadan önce sorar (**I**nteractive). |
| `cp --backup dosya.txt hedef/` | Hedefte aynısı varsa eskisini yedekler (`dosya.txt~`). |

```bash
# Etkileşimli kopyalama (-i)
user@hackerbox:~$ cp -i notlar.txt /tmp/
cp: overwrite '/tmp/notlar.txt'? y

# Yedekleyerek kopyalama (--backup)
user@hackerbox:~$ cp --backup notlar.txt /tmp/
user@hackerbox:~$ ls /tmp/notlar*
/tmp/notlar.txt  /tmp/notlar.txt~
```
`-i`'de aynı isimli dosya olduğu için sordu, `y` ile onayladık. `--backup` eski dosyanın sonuna `~` ekleyip yedeğini aldı, yenisini normal isimle kaydetti.

> [!TIP]
> `cp -p` (izin/sahiplik/zaman damgasını koru) gibi diğer bayraklar: [Temel Komutlar](../Linux_Komutlar%C4%B1/01_Temel_Komutlar.md).

## 4. Taşıma ve Yeniden Adlandırma (`mv`)
Linux'ta ayrı bir "yeniden adlandırma" komutu **yoktur**, bu işi `mv` (Move) yapar.
- **İsim değiştirme:** `mv eski.txt yeni.txt`
- **Taşıma:** `mv dosya.txt /tmp/`

```bash
user@hackerbox:~$ mv notlar.txt Belgeler/
user@hackerbox:~$ ls Belgeler/
notlar.txt
```
Dosya bulunduğu yerden silinip `Belgeler` dizinine taşındı.

## 5. Silme (`rm`)
> [!WARNING]
> **Linux terminalinde "Geri Dönüşüm Kutusu" yoktur. Silinen dosya gider.**

| Komut | Açıklama |
|---|---|
| `rm dosya.txt` | Dosyayı siler. |
| `rm -i dosya.txt` | Silerken "Emin misiniz?" diye sorar (güvenli mod). |
| `rm -r dizin/` | Dizini ve içindekileri siler. |
| `rm -rf dizin/` | Dizini **zorla ve sormadan** siler (**çok tehlikeli!**). |
| `rmdir dizin/` | Sadece **boş** dizini siler (en güvenli yöntem). |

```bash
# Güvenli silme (-i)
user@hackerbox:~$ rm -i önemli_belge.txt
rm: remove regular file 'önemli_belge.txt'? n

# Dizin silme (-r)
user@hackerbox:~$ rm -r projeler/
```
Yanlışlıkla silmeyi önlemek için `-i`'yi alışkanlık yap. İçi dolu dizinler için `-r` şarttır. (İpucu: `alias rm='rm -i'` satırını `~/.bashrc`'ye ekleyebilirsin, bkz. [Bölüm 3](03_Linux_Kabu%C4%9Fu_Shell.md).)

## 6. Dosya Bilgisi: `file` ve `stat`
**`file`** dosyanın türünü (resim, metin, binary) belirler. **Uzantısı ne olursa olsun gerçek içeriği söyler.**
```bash
user@hackerbox:~$ file /bin/bash
/bin/bash: ELF 64-bit LSB shared object, x86-64
user@hackerbox:~$ file notlar.txt
notlar.txt: ASCII text
```

**`stat`** dosya hakkında çok detaylı bilgi verir (inode, zaman damgaları).
```bash
user@hackerbox:~$ stat notlar.txt
  File: notlar.txt
  Size: 0           Blocks: 0          IO Block: 4096   regular empty file
Inode: 123456      Links: 1
Access: 2023-10-20 12:00:00.000000000 +0300
Modify: 2023-10-20 12:00:00.000000000 +0300
Change: 2023-10-20 12:00:00.000000000 +0300
```
- **Access:** Son okunma · **Modify:** İçeriğin son değişimi · **Change:** Inode bilgisinin (izin, sahip vb.) son değişimi.

> [!TIP]
> **Güvenlik / adli bilişim notu**
> `file`, uzantısı değiştirilmiş (örn. `.jpg` görünümlü bir çalıştırılabilir) dosyaları ayırt etmeye yarar. `stat` zaman damgaları ise bir dosyaya ne zaman dokunulduğunu görmede kullanılır.

## 7. Dosya Bağlantıları (Links)
Inode numaralarını görmek için `ls -i` kullanılır:
```bash
user@hackerbox:~$ ls -i notlar.txt
123456 notlar.txt
```

### Hard Link (Sert Bağlantı)
Dosyanın fiziksel verisine (inode) **ikinci bir isim** verir.
- Orijinal dosya silinse bile **veri silinmez**, çünkü hard link üzerinden erişilebilir.
- **Sadece aynı disk bölümünde** çalışır.

```bash
user@hackerbox:~$ ln notlar.txt sert_link.txt
user@hackerbox:~$ ls -li
123456 -rw-r--r-- 2 user user 0 Aug 01 12:00 notlar.txt
123456 -rw-r--r-- 2 user user 0 Aug 01 12:00 sert_link.txt
```
İki dosyanın **inode numarası aynı** (baştaki sayı); yani diskte aynı veriyi işaret ederler. Bağlantı sayısı da `2`'ye çıktı.

### Soft Link (Sembolik Link)
Windows'taki "Kısayol" gibidir; yalnızca orijinal dosyanın **yolunu** gösterir.
- Orijinal silinirse link **bozulur** (kırık link).
- **Farklı disklerde** de çalışır.

```bash
user@hackerbox:~$ ln -s /var/www/html site_kisayolu
user@hackerbox:~$ ls -li site_kisayolu /var/www/html
654321 drwxr-xr-x 2 root root 4096 Aug 01 10:00 /var/www/html
789012 lrwxrwxrwx 1 user user   13 Aug 01 12:05 site_kisayolu -> /var/www/html
```
Inode numaraları **farklı**: `site_kisayolu` sadece `/var/www/html` adresini tutan küçük bir dosyadır (`l` ile başlar).

| | Hard Link | Soft Link |
|---|---|---|
| Komut | `ln hedef link` | `ln -s hedef link` |
| Inode | Aynı | Farklı |
| Orijinal silinirse | Veri durur | Link kırılır |
| Farklı disk | Olmaz | Olur |

## 8. İleri Düzey Kopyalama (`dd`)
`dd` **düşük seviyeli** kopyalama yapar; disk imajı almak veya yazdırmak için kullanılır.

**Örnek: ISO'yu USB'ye yazdırmak**
```bash
sudo dd if=linux.iso of=/dev/sdb bs=4M status=progress
```
| Parametre | Anlamı |
|---|---|
| `if` | Kaynak (Input File - ISO dosyası) |
| `of` | Hedef (Output File - USB bellek) |
| `bs` | Blok boyutu (hız için) |
| `status=progress` | İlerleme göstergesi |

> [!WARNING]
> `of=` yanlış cihaz olursa (örn. ana disk) **tüm veri geri dönüşsüz silinir.** Çalıştırmadan önce `lsblk` veya `fdisk -l` ile hedef cihazı mutlaka doğrula ([Dosya Sistemi ve Disk](../Linux_Komutlar%C4%B1/05_Dosya_Sistemi_ve_Disk.md)).

---
> ◀ [Bölüm 4 - Dizinlerde Gezinti](04_Dizinlerde_Gezinti.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md) · [Bölüm 6 - Dosya ve Dizinleri Bulma](06_Dosya_ve_Dizinleri_Bulma.md) ▶
