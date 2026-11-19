---
title: "Web Hacking (İndeks)"
ana-not: "[[Siber_Güvenlik_Ana_Sayfa]]"
tarih: 2026-11-19
aliases:
  - Web Hacking
tags:
  - siber-guvenlik
  - web-hacking
  - moc
---

# Web Hacking

> 📚 [[Siber_Güvenlik_Ana_Sayfa|Ana Sayfa]]

> [!abstract] Bu klasör
> Web uygulama zafiyetleri. Şu an SQL Injection ağırlıklı; öğrenme sırasına göre dizilmiştir.

> [!warning] Yasal uyarı
> Yalnızca izinli laboratuvar ve hedeflerde uygula.

| Not | İçerik |
|---|---|
| [[01_SQL_Injection_Temelleri\|SQL Injection Temelleri]] | Test noktaları, Burp keşfi, temel payload'lar |
| [[01.1_SQLi_Union\|SQLi UNION Saldırıları]] | Sütun sayısı, veri tipi, veri çekme |
| [[01.2_Blind_SQL\|Blind (Kör) SQL Injection]] | Koşullu yanıtlar, mantıksal sorular |
| [[01.3_Error-Based_SQL_Injection\|Error-Based SQL Injection]] | Hata mesajlarından veri sızdırma |
| [[01.4_Time_Based_Blind_SQL\|Time-Based Blind SQL Injection]] | Gecikme ile veri çıkarma |
| [[01.5_Out-of-Band_OAST\|Out-of-Band (OAST) SQL Injection]] | DNS/HTTP ile veri sızdırma, Burp Collaborator |
| [[01.6_Farklı_Bağlamlarda_SQLi\|Farklı Bağlamlarda SQL Injection]] | JSON/XML, ikinci derece SQLi, filtre atlatma |

## Hangi teknik ne zaman?

| Durum | Teknik |
|---|---|
| Sorgu sonucu sayfada görünüyor | [[01.1_SQLi_Union\|UNION]] |
| Hata mesajı sayfada görünüyor | [[01.3_Error-Based_SQL_Injection\|Error-based]] |
| Sayfa içeriği doğru/yanlışa göre değişiyor | [[01.2_Blind_SQL\|Blind (boolean)]] |
| Hiçbir fark yok | [[01.4_Time_Based_Blind_SQL\|Time-based]] |
| Sorgu asenkron çalışıyor | [[01.5_Out-of-Band_OAST\|OAST]] |
