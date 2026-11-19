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

> 📚 [Ana Sayfa](../Siber_G%C3%BCvenlik_Ana_Sayfa.md)

> [!NOTE]
> **Bu klasör**
> Web uygulama zafiyetleri. Şu an SQL Injection ağırlıklı; öğrenme sırasına göre dizilmiştir.

> [!WARNING]
> **Yasal uyarı**
> Yalnızca izinli laboratuvar ve hedeflerde uygula.

| Not | İçerik |
|---|---|
| [SQL Injection Temelleri](01_SQL_Injection_Temelleri.md) | Test noktaları, Burp keşfi, temel payload'lar |
| [SQLi UNION Saldırıları](01.1_SQLi_Union.md) | Sütun sayısı, veri tipi, veri çekme |
| [Blind (Kör) SQL Injection](01.2_Blind_SQL.md) | Koşullu yanıtlar, mantıksal sorular |
| [Error-Based SQL Injection](01.3_Error-Based_SQL_Injection.md) | Hata mesajlarından veri sızdırma |
| [Time-Based Blind SQL Injection](01.4_Time_Based_Blind_SQL.md) | Gecikme ile veri çıkarma |
| [Out-of-Band (OAST) SQL Injection](01.5_Out-of-Band_OAST.md) | DNS/HTTP ile veri sızdırma, Burp Collaborator |
| [Farklı Bağlamlarda SQL Injection](01.6_Farkl%C4%B1_Ba%C4%9Flamlarda_SQLi.md) | JSON/XML, ikinci derece SQLi, filtre atlatma |

## Hangi teknik ne zaman?

| Durum | Teknik |
|---|---|
| Sorgu sonucu sayfada görünüyor | [UNION](01.1_SQLi_Union.md) |
| Hata mesajı sayfada görünüyor | [Error-based](01.3_Error-Based_SQL_Injection.md) |
| Sayfa içeriği doğru/yanlışa göre değişiyor | [Blind (boolean)](01.2_Blind_SQL.md) |
| Hiçbir fark yok | [Time-based](01.4_Time_Based_Blind_SQL.md) |
| Sorgu asenkron çalışıyor | [OAST](01.5_Out-of-Band_OAST.md) |
