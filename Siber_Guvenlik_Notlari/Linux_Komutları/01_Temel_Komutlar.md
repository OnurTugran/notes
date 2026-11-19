---
title: "Temel Komutlar"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Temel Komutlar
tags:
  - siber-guvenlik
  - linux
  - temel-komutlar
  - cheatsheet
---

# Temel Komutlar

> 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Metin İşleme ve Regex](02_Metin_%C4%B0%C5%9Fleme_ve_Regex.md) ▶

## 📁 Dizin ve Dosya İşlemleri
- `pwd` : Kullanıcının o an bulunduğu mevcut dizini (çalışma alanını) ekrana yazdırır.
- `cd /yol/` : Belirtilen dizine geçiş yapar. (`cd ..` bir üst dizine, `cd ~` ana ev dizinine gider).
- `ls` : Dizin içeriğini listeler.
  - `ls -l` : Dosyaları ayrıntılı formatta (izinler, sahiplik, boyut, tarih) listeler.
  - `ls -a` : Gizli dosyalar (başında nokta olanlar) dahil tüm öğeleri gösterir.
- `mkdir klasor_adi` : Yeni bir dizin oluşturur. (`mkdir -p` iç içe klasörleri hata almadan oluşturur).
- `touch dosya.txt` : Belirtilen adla yeni ve boş bir dosya oluşturur.
- `cat dosya.txt` : Dosya içeriğini ekrana yazdırır.
- Birden fazla dosyayı `cat dosya1.txt dosya2.txt > birlesik.txt` komutuyla birleştirebilirsin.
- `echo "metin"` : Belirtilen metni ekrana yazdırır. (`>` ile dosya üzerine yazar, `>>` ile dosyanın sonuna ekleme yapar).
- `cp kaynak hedef` : Dosyaları kopyalar.
  - `cp -r` : Dizinleri (klasörleri) kopyalamak için kullanılır.
  - `cp -p` : Dosyanın izinlerini, sahipliğini ve zaman damgasını koruyarak kopyalar.
- `mv eski_dosya yeni_yer` : Dosya veya dizin taşımak ya da yeniden adlandırmak için kullanılır.
- `rm dosya.txt` : Dosya siler.
  - `rm -r` : Dizin ve içindekileri silmek için kullanılır.
  - `rm -f` : Silme işlemini zorla (onay sormadan) yapar.

## 👤 Kullanıcı ve Yetki Yönetimi
- `whoami` : Sistemde hangi kullanıcı olarak çalıştığını gösterir.
- `su kullanici_adi` : Başka bir kullanıcı hesabına geçiş (switch user) yapar. (Sadece `su` yazılırsa root kullanıcısına geçmeye çalışır).
- `sudo komut` : Normal kullanıcının tek seferlik "root" (patron) yetkisiyle komut çalıştırmasını sağlar.
- `sudo -i` : Tamamen Root kullanıcısının oturum ortamına geçiş yapar. (Çıkmak için `exit` yazılır).
- `adduser kullanici_adi` : Yeni bir kullanıcı oluşturur (şifre, ev dizini, UID/GID otomatik ayarlanır).
- `deluser kullanici_adi` : Kullanıcıyı sistemden siler ancak dosyaları kalır. (`--remove-home` parametresi ev dizinini de siler).
- `usermod -aG grup_adı kullanici_adi` : Kullanıcıyı mevcut gruplarından çıkarmadan (append) yeni bir gruba ekler.
- `chown sahip:grup dosya` : Bir dosyanın veya dizinin sahibini ve grubunu değiştirir.
- `chmod izinler dosya` : Dosya ve dizinlerin okuma (r=4), yazma (w=2) ve çalıştırma (x=1) izinlerini değiştirir.
  - *Örnek:* `chmod 755 dosya.txt` (User: rwx, Group: rx, Other: rx).

## 🔍 Metin Arama ve Filtreleme (Özet)
- `|` (Pipe) : Bir komutun çıktısını kopyalamadan doğrudan diğer bir komuta girdi olarak aktarır.
- **grep** : Dosya içinde belirli kelime veya düzenli ifade arar.
  - `grep -i` : Büyük/küçük harf ayrımını yoksayar.
  - `grep -n` : Eşleşen satırların numarasını gösterir.
  - `grep -c` : Eşleşen satır sayısını sayar.
  - `grep -e 'kelime1' -e 'kelime2'` : Birden fazla farklı kelimeyi aynı anda arar.
- **sed (Stream Editor)** : Metinleri düzenlemek, silmek veya değiştirmek için kullanılır.
  - `sed 's/eskikelime/yenikelime/g' dosya.txt` : Dosyadaki tüm "eskikelime" değerlerini "yenikelime" ile değiştirir.
  - `sed -i` : Yapılan değişikliği sadece ekranda göstermez, doğrudan dosyanın içine kalıcı olarak kaydeder.
  - `sed '3d' dosya.txt` : Dosyanın 3. satırını siler.
- **awk** : Sütunlu veri yapılarında (örneğin CSV) satır satır veri işler.
  - `awk '{print $1}' dosya.txt` : Dosyadaki her satırın 1. sütununu ekrana yazdırır.
  - `awk -F ','` : Virgülle ayrılmış dosyalarda işlem yapmak için alan ayırıcı (Field Separator) belirtir.

> [!TIP]
> **Devamı**
> `cut`, `tr`, `awk`, `sed`, regex ve ayrıntılı filtreleme: [02_Metin_İşleme_ve_Regex](02_Metin_%C4%B0%C5%9Fleme_ve_Regex.md).

---
> 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Metin İşleme ve Regex](02_Metin_%C4%B0%C5%9Fleme_ve_Regex.md) ▶
