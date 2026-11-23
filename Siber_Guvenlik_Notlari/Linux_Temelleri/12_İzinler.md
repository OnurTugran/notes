---
title: "Bölüm 12 - İzinler"
ana-not: "[[00_Linux_Temelleri_İndeks]]"
tarih: 2026-11-23
aliases:
  - İzinler
  - Dosya İzinleri
  - chmod
  - SUID SGID Sticky
  - umask
  - chattr
  - Linux Temelleri Bölüm 12
tags:
  - siber-guvenlik
  - linux
  - linux-temelleri
  - izinler
  - chmod
  - suid
  - sgid
  - sticky
  - umask
  - chattr
---

# Bölüm 12 - İzinler

> ◀ [Bölüm 11 - Grup Yönetimi](11_Grup_Y%C3%B6netimi.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md)

## 0. İzin Nedir ve Neden Önemlidir?
Linux çok kullanıcılı bir sistem olduğu için her dosyanın **kime ait olduğu** ve **kimin ne yapabileceği** sıkı kurallara bağlıdır. Bu kurallara **dosya izinleri** denir.

**Neden var?**
- **Gizlilik:** Kişisel dosyalarını (örn. e-postalar) başka kullanıcıların okumasını engellemek.
- **Bütünlük:** Önemli sistem dosyalarının yetkisiz kişilerce değiştirilmesini veya silinmesini önlemek.
- **Güvenlik:** Zararlı yazılımın sistem genelinde çalışmasını ve yayılmasını engellemek.

İzinler olmasaydı herhangi bir kullanıcı kritik bir dosyayı silip sistemi çökertebilirdi. ([Kullanıcılar](10_Kullan%C4%B1c%C4%B1_Y%C3%B6netimi.md) ve [gruplar](11_Grup_Y%C3%B6netimi.md) bu sistemin yapı taşlarıdır.)

## 1. İzinleri Okuma
Her dosya ve dizinin, kimin ona nasıl erişebileceğini belirleyen izinleri vardır. İzin yapısını anlamak için `ls -l` çıktısını ([Bölüm 4](04_Dizinlerde_Gezinti.md)) ayrıntılı incelemek gerekir.
```bash
root@hackerbox:~$ ls -l
-rwxr--r-- 2 john development 4096 Jul 29 12:34 notes.txt
```

| Sütun | Açıklama |
|---|---|
| `-` (ilk karakter) | **Dosya türü.** Dizin ise `d`, dosya ise `-` |
| `rwxr--r--` | Dosya izinleri (9 karakter) |
| `2` | Sabit bağlantı (hard link) sayısı ([Bölüm 5](05_Dosya_ve_Dizin_%C4%B0%C5%9Flemleri.md)) |
| `john` | Sahibi olan kullanıcı |
| `development` | Sahibi olan grup |
| `4096` | Dosyanın boyutu (dizinlerde dizin bilgisini tutan blok boyutu) |
| `Jul 29 12:34` | Son değiştirilme tarihi |
| `notes.txt` | Dosya/dizin adı |

### `r`, `w`, `x` ve `-` karakterleri
İzinler üç yetki türü ve bir yetkisizlik durumuyla ifade edilir:

| Harf | Dosyada | Dizinde |
|---|---|---|
| **r** (Read) | İçeriği **okuma** | İçindekileri **listeleme** (`ls`) |
| **w** (Write) | İçeriğe **yazma**, düzenleme, silme, üzerine yazma | İçine yeni dosya **oluşturma veya silme** |
| **x** (Execute) | **Çalıştırma** (script, derlenmiş program). Bu izin yoksa çalıştırılabilir program bile çalışmaz | İçine **girme** (`cd`) |
| **-** | O pozisyondaki izin **verilmemiş** | aynı |

### Kullanıcı, Grup ve Diğer (User, Group, Others)
İzin sistemi yetkileri **üç kitleye** böler. 10 karakterlik dizide ilk karakter türü belirtir; kalan 9 karakter üçerli gruplardır:

```
 -   rwx   rwx   rwx
 │    │     │     │
tür  sahip grup  diğer
     (u)   (g)   (o)
```

| Kitle | Karakterler | Anlamı |
|---|---|---|
| **Kullanıcı** (User, `u`) | 2-3-4 | Dosyanın **sahibi** (genelde oluşturan kişi). |
| **Grup** (Group, `g`) | 5-6-7 | Dosyanın atandığı gruba dahil **tüm kullanıcılar**. Birden fazla kişinin aynı dosyalarla çalışması için. |
| **Diğer** (Others, `o`) | 8-9-10 | Sahibi olmayan ve gruba dahil olmayan **herkes** ("dünya", world). |

### İzinleri okuyalım
`-rwxr-xr--` örneğini parçalayalım:
- `-` : En baştaki karakter türü gösterir; `-` olduğu için **dosya** (`d` olsaydı dizin).
- `rwx` (sahip): Okur, yazar, çalıştırır. **Tam yetki.**
- `r-x` (grup): Okur ve çalıştırır, **yazamaz.**
- `r--` (diğerleri): **Sadece okur.**

**Örnek çıktı analizi:**
```bash
user@hackerbox:~$ ls -l script.sh
-rwxr-xr-- 1 mehmet yazilim 450 Aug 01 14:00 script.sh
```
- **Sahip (mehmet):** `rwx`: okur, yazar, çalıştırır
- **Grup (yazilim):** `r-x`: okur, çalıştırır
- **Diğerleri:** `r--`: sadece okur

## 2. İzin Değerleri (Oktal)
İzinler sayılarla da ifade edilir:

| Harf | Değer |
|---|---|
| `r` (Read) | **4** |
| `w` (Write) | **2** |
| `x` (Execute) | **1** |

| İzin | Sayısal | Anlamı |
|---|---|---|
| `rwx` | 7 (4+2+1) | Tam yetki |
| `rw-` | 6 (4+2) | Okur/yazar |
| `r-x` | 5 (4+1) | Okur/çalıştırır |
| `r--` | 4 | Sadece okur |
| `---` | 0 | Hiçbir yetki yok |

**Örnek hesap:** `rwxr-xr--`

| Grup | İzinler | Oktal | Anlamı |
|---|---|---|---|
| Sahip | `rwx` | 7 | Oku, yaz, çalıştır |
| Grup | `r-x` | 5 | Oku, çalıştır (yazamaz) |
| Diğer | `r--` | 4 | Sadece oku |
| **Toplam** | `rwxr-xr--` | **754** | Dosyanın tam izni |

## 3. İzinleri Değiştirme (`chmod`)
`chmod` hem **oktal** (`755`) hem **sembolik** (`u+x`) biçimde kullanılır. (Kısa özeti: [Temel Komutlar](../Linux_Komutlar%C4%B1/01_Temel_Komutlar.md).)

**Senaryo: Bir scripti çalıştırılabilir yapma (755)**
```bash
user@hackerbox:~$ ls -l script.sh
-rw-r--r-- 1 user user 0 Aug 01 12:00 script.sh
user@hackerbox:~$ chmod 755 script.sh
user@hackerbox:~$ ls -l script.sh
-rwxr-xr-x 1 user user 0 Aug 01 12:00 script.sh
```

**Herkese tam yetki (777): güvensizdir!**
```bash
user@hackerbox:~$ chmod 777 dosya.txt
user@hackerbox:~$ ls -l dosya.txt
-rwxrwxrwx 1 user user 0 Aug 01 12:00 dosya.txt
```

**Sadece sahip okur/yazar (600):**
```bash
user@hackerbox:~$ chmod 600 ozel.txt
user@hackerbox:~$ ls -l ozel.txt
-rw------- 1 user user 0 Aug 01 12:00 ozel.txt
```

**Sembolik gösterim:** Kitle (`u`, `g`, `o`, `a`) + işlem (`+` ekle, `-` çıkar, `=` ata) + izin (`r`, `w`, `x`).
```bash
# notes.txt dosyasında diğer kullanıcılara (o) yazma izni ekle
root@hackerbox:~$ chmod o+w notes.txt

# Tek seferde tüm kitlelere tüm izinleri ver
root@hackerbox:~$ chmod ugo+rwx notes.txt
root@hackerbox:~$ ls -l notes.txt
-rwxrwxrwx 1 john development 4096 Jul 29 12:34 notes.txt
```

| Sembolik | Etkisi |
|---|---|
| `chmod u+x dosya` | Sahibe çalıştırma ekler |
| `chmod g-w dosya` | Gruptan yazmayı alır |
| `chmod o-rwx dosya` | Diğerlerinden tüm izinleri alır |
| `chmod a+r dosya` | Herkese (a = all) okuma ekler |
| `chmod u=rw,go=r dosya` | Sahip `rw-`, grup ve diğer `r--` yapar (`644`) |

> [!WARNING]
> **`chmod 777` kullanma.** Herkese yazma ve çalıştırma izni vermek, başka bir kullanıcının veya zararlı bir sürecin dosyayı değiştirmesine izin verir. Gerekeni ver, fazlasını verme (en az yetki ilkesi). Özel dosyalarda (SSH anahtarı vb.) `600` kullan.

## 4. Özel İzinler (SUID, SGID, Sticky)
Güvenlik ve işbirliği için kritiktir.

| Ad | Sayısal | Nerede | Ne yapar? | Örnek |
|---|---|---|---|---|
| **SUID** | `4000` | Dosya | Dosyayı çalıştıran kişi, **dosya sahibinin yetkisiyle** çalıştırır. | `passwd` |
| **SGID** | `2000` | Dizin | İçinde oluşturulan dosyalar **dizinin grubunu** alır. | Ortak projeler |
| **Sticky** | `1000` | Dizin | **Sadece dosya sahibi** dosyasını silebilir. | `/tmp` |

**SUID ayarlama:**
```bash
user@hackerbox:~$ chmod 4755 dosya
user@hackerbox:~$ ls -l dosya
-rwsr-xr-x 1 root root 0 Aug 01 12:00 dosya
```
`x` yerine `s` geldi: dosya artık **sahibinin (root) yetkisiyle** çalışır.

**Sticky bit ayarlama (`/tmp`):**
```bash
user@hackerbox:~$ chmod 1777 /tmp
user@hackerbox:~$ ls -ld /tmp
drwxrwxrwt 10 root root 4096 Aug 01 12:00 /tmp
```
`t` harfi sticky bit'i gösterir. `/tmp` herkesin yazabildiği ama başkasının dosyasını silemediği bir dizindir.

> [!NOTE]
> SGID, [Bölüm 11'deki ortak proje dizininde](11_Grup_Y%C3%B6netimi.md) `chmod g+s` ile kullandığımız özelliktir (`2770` gibi).

> [!WARNING]
> **Güvenlik: SUID, yetki yükseltmenin klasik hedefidir.** Sahibi root olan ve yanlış yapılandırılmış bir SUID programı, normal bir kullanıcıya root yetkisi kazandırabilir. SUID dosyalarını bulmak için:
> ```bash
> find / -perm -4000 2>/dev/null
> ```
> ([Bölüm 6 - find](06_Dosya_ve_Dizinleri_Bulma.md) · [Linux Komutları indeksi](../Linux_Komutlar%C4%B1/00_Linux_Komutlar%C4%B1.md) · [Yetki Yükseltme](../Yetki%20Y%C3%BCkseltme/00_Yetki_Y%C3%BCkseltme.md)). `ls -l` çıktısında büyük `S` veya `T` görürsen, altında `x` izni olmadığı anlamına gelir.

## 5. Umask Hesaplama
Yeni oluşturulan dosya ve dizinlerin **varsayılan izinlerini** belirler. Standart değer **`022`**'dir.

| Tür | Hesap | Sonuç |
|---|---|---|
| Dosya | `666 - 022` | `644` (`rw-r--r--`) |
| Dizin | `777 - 022` | `755` (`rwxr-xr-x`) |

Kendi umask değerini görmek için:
```bash
user@hackerbox:~$ umask
0022
```
> [!TIP]
> Dosyalar varsayılan olarak çalıştırma izniyle oluşturulmaz; bu yüzden dosya için `666`, dizin için `777` üzerinden başlanır. (Çıkarma işlemi pratik bir kısayoldur; gerçekte umask'taki bitler izinden *maskelenir*.) Daha kısıtlı varsayılan için umask `077` yapılır: dosyalar `600`, dizinler `700` olur.

## 6. Dosya Öznitelikleri (`chattr` ve `lsattr`)
İzinlerden **daha güçlü** koruma sağlar. Bir dosyayı **değiştirilemez (immutable)** yapmak için kullanılır.
- `chattr +i dosya` : Dosyayı **kilitler.** Root bile silemez.
- `lsattr dosya` : Öznitelikleri gösterir.

```bash
user@hackerbox:~$ sudo chattr +i kritik_dosya.conf
user@hackerbox:~$ rm kritik_dosya.conf
rm: cannot remove 'kritik_dosya.conf': Operation not permitted
```
Kilidi açmak için:
```bash
sudo chattr -i kritik_dosya.conf
```
> [!TIP]
> Savunmada kritik config dosyalarını `chattr +i` ile korumak yaygındır. Saldırgan açısından da önemlidir: bir dosya root ile bile silinemiyorsa, önce `lsattr` ile `i` özniteliği olup olmadığına bakılır.

## 7. Hızlı Özet

| İş | Komut |
|---|---|
| İzinleri gör | `ls -l` / `ls -ld <dizin>` |
| Çalıştırılabilir yap | `chmod +x dosya` veya `chmod 755 dosya` |
| Sadece sahip okur/yazar | `chmod 600 dosya` |
| Sahip/grup tam, diğerleri yok | `chmod 770 dizin` |
| SUID / SGID / Sticky | `chmod 4755` / `2755` / `1777` |
| SUID dosyalarını bul | `find / -perm -4000 2>/dev/null` |
| Varsayılan izin maskesi | `umask` |
| Dosyayı değiştirilemez yap / aç | `sudo chattr +i` / `-i` |
| Öznitelikleri gör | `lsattr dosya` |

---
> ◀ [Bölüm 11 - Grup Yönetimi](11_Grup_Y%C3%B6netimi.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md)
