---
title: "Metin İşleme ve Regex"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Metin İşleme ve Regex
tags:
  - siber-guvenlik
  - linux
  - grep
  - awk
  - sed
  - regex
---

# Metin İşleme ve Regex

> ◀ [Temel Komutlar](01_Temel_Komutlar.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Paket Yönetimi](03_Paket_Y%C3%B6netimi.md) ▶

## 1. cut
Belirli bir ayırıcıya (delimiter) göre satırları böler ve sadece istenen alanı (sütunu) ekrana yazdırır.
*   **`-d`** (delimiter): Metni bölecek karakteri belirler.
*   **`-f`** (field): Hangi bölümün (sütunun) gösterileceğini seçer.

```bash
# /etc/passwd dosyasında ":" işaretini ayırıcı olarak kabul et ve 1. alanı (kullanıcı adlarını) getir:
cat /etc/passwd | grep -v "false\|nologin" | cut -d":" -f1
```

## 2. tr (Translate)
Bir karakteri başka bir karakterle değiştirmek veya silmek için kullanılır.

```bash
# /etc/passwd dosyasındaki tüm ":" karakterlerini boşluk (" ") karakteri ile değiştir:
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " "
```

## 3. column
Karmaşık çıktıları okunabilir bir tablo (sütun) formatına dönüştürür.
*   **`-t`** (table): Girdiyi tablo şeklinde hizalar.

```bash
# Sütunları düzgün hizalanmış bir tablo olarak gösterir:
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | column -t
```

## 4. awk
Gelişmiş metin ve veri işleme aracıdır. Özellikle boşluklarla ayrılmış metinlerde belirli sütunları seçmek için çok güçlüdür.
*   **`$1`** : Birinci sütunu temsil eder.
*   **`$NF`** : Satırdaki en son sütunu (Number of Fields) temsil eder.

```bash
# Yalnızca 1. sütunu ve en son sütunu ekrana yazdır:
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}'
```

## 5. sed (Stream Editor)
Metin akışı üzerinde düzenlemeler (bul-değiştir) yapmak için kullanılır. Düzenli ifadeler (Regex) destekler.
*   **`s`** (substitute): Değiştirme komutudur.
*   **`g`** (global): Satırdaki tüm eşleşmeleri değiştirir (sadece ilkini değil).
*   **Sözdizimi:** `sed 's/ESKİ_KELİME/YENİ_KELİME/g'`

```bash
# Çıktıdaki tüm "bin" kelimelerini "HTB" kelimesiyle değiştir:
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}' | sed 's/bin/HTB/g'
```

## 6. wc (Word Count)
Çıktıdaki satır, kelime veya karakter sayısını hesaplar.
*   **`-l`** (lines): Sadece satır sayısını sayar (Bulunan sonuç sayısını hesaplamak için idealdir).

```bash
# Filtrelenen listenin toplam kaç satırdan (sonuçtan) oluştuğunu say:
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1,$NF}' | wc -l
```

## Regex

### 1. `( )` - Gruplama (Grouping)
Regex içindeki belirli kısımları bir arada tutmak (gruplamak) için kullanılır. Tıpkı matematikteki işlem önceliği parantezleri gibidir.
*   **Örnek:** `(jpg|png)` -> Dosya uzantısının jpg veya png olduğu kısmı tek bir grup olarak değerlendirir.

### 2. `[ ]` - Karakter Sınıfları (Character Classes)
Köşeli parantez içindeki karakterlerden **sadece bir tanesiyle** eşleşir. Bir harf veya rakam aralığı belirlemek için çok sık kullanılır.
*   **Örnek:** `[a-z]` -> Sadece küçük harfleri bulur.
*   **Örnek:** `[0-9]` -> Sadece rakamları bulur.

### 3. `{ }` - Miktar Belirleyiciler (Quantifiers)
Kendisinden hemen önce gelen karakterin veya grubun **kaç kez tekrar etmesi** gerektiğini belirler. Süslü parantezler sayı veya aralık alır.
*   **Örnek:** `[0-9]{3}` -> Yan yana tam 3 tane rakam arar (Örn: 123, 987).
*   **Örnek:** `[a-z]{1,10}` -> 1 ile 10 karakter uzunluğunda küçük harflerden oluşan kelimeleri arar.

### 4. `|` - VEYA Operatörü (OR)
Belirtilen seçeneklerden birinin eşleşmesi durumunda sonucu gösterir.
*   **Örnek:** `admin|root` -> Metnin içinde "admin" **VEYA** "root" geçen yerleri bulur.

### 5. `.*` - Her Şey (Joker / AND Mantığı)
En sık kullanılan kalıptır. `.` (nokta) herhangi tek bir karakter anlamına gelir. `*` (yıldız) ise sıfır veya daha fazla tekrar demektir. İkisi birleştiğinde **"arada ne olursa olsun"** anlamına gelir.
*   **Örnek:** `password.*admin` -> Satırda önce "password", sonra arada ne yazarsa yazsın, en son da "admin" geçen satırları bulur. İki kelimenin de aynı satırda, belirtilen sırayla olmasını sağlar.

### 1. Yorum Satırlarını (#) Gizleme
Konfigürasyon dosyalarındaki (#) ile başlayan yüzlerce satır açıklamayı gizleyip sadece sistemde aktif olan ayarları görmek için kullanılır. Sızma testlerinde en sık başvurulan yöntemlerden biridir.

```bash
# İçinde # işareti geçmeyen satırları göster (-v tersine işlem yapar).
# Not: satırın ortasında # olan ayarlar da elenir. Daha doğrusu yorum ve boş satırları ele:
# grep -vE "^\s*(#|$)" /etc/ssh/sshd_config
grep -v "#" /etc/ssh/sshd_config
```

### 2. Belirli Bir İfadeyle Başlayan Kelimeleri Bulma
`Permit` kelimesiyle başlayan ayarları bulur (Örn: `PermitRootLogin`, `PermitEmptyPasswords`).

```bash
# \b (boundary) kelime sınırını belirtir.
grep -E "\bPermit" /etc/ssh/sshd_config
```

### 3. Belirli Bir İfadeyle Biten Kelimeleri Bulma
`Authentication` kelimesiyle biten ayarları bulur (Örn: `PasswordAuthentication`).

```bash
grep -E "Authentication\b" /etc/ssh/sshd_config
```

### 4. İçinde Belirli Bir Kelime (Key) Geçenleri Bulma
Standart arama işlemi.

```bash
grep -E "Key" /etc/ssh/sshd_config
```

### 5. Satır Başı (^) ve Joker Karakter (.*) Birlikte Kullanımı
**Çok Önemli:** Satır "Password" kelimesiyle başlasın (`^`), arada ne olursa olsun (`.*`), satırın bir yerinde "yes" geçsin mantığıdır.

```bash
grep -E "^Password.*yes" /etc/ssh/sshd_config
```

### 6. Satır Sonu ($) Kullanımı
`$` işareti satırın bittiği yeri ifade eder. Sadece en son kelimesi "yes" olan ayarları filtreler.

```bash
grep -E "yes$" /etc/ssh/sshd_config
```

---
> ◀ [Temel Komutlar](01_Temel_Komutlar.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Paket Yönetimi](03_Paket_Y%C3%B6netimi.md) ▶
