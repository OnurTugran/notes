---
title: "Bölüm 8 - Wireshark İstatistikler"
ana-not: "[[00_Ağ_Trafiği_Analizi_İndeks]]"
tarih: 2026-11-25
aliases:
  - Wireshark İstatistikler
  - Statistics
  - Protocol Hierarchy
  - Conversations
  - Endpoints
tags:
  - siber-guvenlik
  - ag-trafigi-analizi
  - wireshark
---

# Bölüm 8 - Wireshark İstatistikler

> 📚 [İndeks](00_A%C4%9F_Trafi%C4%9Fi_Analizi_%C4%B0ndeks.md) · ◀ [Bölüm 7 - Wireshark Filtreleme](07_Wireshark_Filtreleme.md)

Wireshark'ın **Statistics** menüsü, yakalanan trafiğin **özetini** verir. Binlerce paketi tek tek okumak yerine önce buradan büyük resme bakarsın: hangi protokoller var, kim kiminle konuşuyor, kim en çok trafik üretiyor.

> [!TIP]
> Analize genelde istatistiklerle başla: **Protocol Hierarchy** ile trafiğin bileşimine, **Conversations/Endpoints** ile en aktif cihazlara bak, şüpheli bir şey görürsen o konuşmaya filtre uygulayıp paketlere in. Bu, [Bölüm 4'teki](04_A%C4%9F_Analizi_S%C3%BCreci.md) "basit analizden ayrıntıya" yaklaşımıyla aynıdır.

| Seçenek | Menü yolu | Ne gösterir |
|---|---|---|
| Resolved Addresses | Statistics → Resolved Addresses | Adreslerin host isimleri |
| Protocol Hierarchy | Statistics → Protocol Hierarchy | Protokol dağılımı ve yüzdeleri |
| Conversations | Statistics → Conversations | İki uç arasındaki akışlar |
| Endpoints | Statistics → Endpoints | Tek tek cihazların trafiği |
| DNS | Statistics → DNS | DNS sorgu/yanıt istatistikleri |
| HTTP | Statistics → HTTP | HTTP istek, yanıt ve URL istatistikleri |
| IPv4 / IPv6 Statistics | Statistics → IPv4 Statistics / IPv6 Statistics | IP paket istatistikleri |

---

## 1. Resolved Addresses (Çözümlenen Adresler)

Yakalanan trafikteki **MAC ve IP adreslerine karşılık gelen host isimlerini** gösterir (DNS çözümlemesi ve Wireshark'ın kendi adres/üretici tablosu ile). Ağdaki cihazları sayısal adres yerine okunabilir isimle tanımanı sağlar.

> [!NOTE]
> MAC adresinin ilk yarısı üretici (OUI) bilgisini verir. Örneğin `SamsungElect_75:b3:2e`, MAC'in Samsung'a ait olduğunu gösterir. Ağda tanımadığın bir cihazı bulmanın ilk yoludur. Bkz. [Bölüm 6](06_Wireshark_ile_A%C4%9F_Analizi.md).

---

## 2. Protocol Hierarchy (Protokol Hiyerarşisi)

Yakalamada **hangi protokoller var** ve her birinin **yüzdesi** nedir, ağaç yapısında gösterir (Ethernet → IP → TCP → HTTP gibi).

- Trafiğin genel bileşimini hızlıca anlarsın. Örn: "trafiğin %60'ı HTTP".
- Beklenmeyen bir protokol bu ekranda göze çarpar (örn: normalde görmediğin bir uzaktan erişim ya da tünel protokolü).

> [!WARNING]
> Güvenlik açısından: Şifresiz protokollerin (HTTP, FTP, Telnet) yüksek oranı içeriğin okunabilir olduğu anlamına gelir. Olağan dışı yüksek bir DNS oranı ise DNS tüneli ya da veri sızdırma belirtisi olabilir.

---

## 3. Conversations (Konuşmalar)

**İki nokta arasındaki trafik akışlarının** listesidir. Hangi cihazların birbiriyle iletişim kurduğunu ve her konuşmanın ne kadar veri taşıdığını gösterir.

Pencerede dört sekme vardır:

| Sekme | Konuşmayı neye göre gruplar |
|---|---|
| **Ethernet** | MAC adresi çiftleri |
| **IP** | IP adresi çiftleri |
| **TCP** | IP + TCP port çiftleri |
| **UDP** | IP + UDP port çiftleri |

Bir konuşmaya sağ tıklayıp **Apply as Filter** dersen paket listesi sadece o konuşmaya daralır.

---

## 4. Endpoints (Uç Noktalar)

**En çok trafik gönderen veya alan** cihazları hızlıca bulmak için kullanılır. Conversations ile aynı dört sekmeye sahiptir (Ethernet, IP, TCP, UDP), ama konuşma çiftleri yerine **tek tek cihazları** listeler.

> [!NOTE]
> **Conversations ile Endpoints farkı**
> - **Endpoints:** "Kim var, kim ne kadar trafik üretiyor?" (tek cihaz)
> - **Conversations:** "Kim kiminle konuşuyor?" (cihaz çifti)

---

## 5. DNS

Yakalanan trafikteki **DNS sorgu ve yanıtlarının** istatistiklerini verir. Şunlar için işe yarar:
- Yavaş çözümlemeler ve başarısız sorgular gibi DNS sorunlarını gidermek
- Ağda **hangi alan adlarının sorgulandığını** görmek

> [!TIP]
> Zararlı yazılım (botnet/C2) tespitinde sorgulanan alan adları önemli ipucudur. Tanımadığın, rastgele görünen ya da çok sık sorgulanan alan adlarına dikkat et. Bkz. [Bölüm 1 - Kullanım alanları](01_Giri%C5%9F.md).

---

## 6. HTTP

**HTTP trafiğinin derin analizini** verir:
- İstek yöntemleri (GET, POST...)
- Durum kodları (200, 404, 500...)
- İstek URL'leri ve bunları içeren paketler

Web uygulaması sorunlarını gidermek ve HTTP ile ilgili garip davranışları ya da saldırıları belirlemek için kullanılır (örn: çok sayıda 404 ya da tek bir adrese yoğun POST).

---

## 7. IPv4 ve IPv6 İstatistikleri

IP paketleri için ayrıntılı istatistik verir:
- Toplam IP paketi sayısı
- Kaynak ve hedef adreslere göre trafik hacmi
- Paketlerin boyuta göre dağılımı

---

## Hızlı Özet

| Soru | Nereye bakmalı |
|---|---|
| Trafikte hangi protokoller var? | Protocol Hierarchy |
| Kim kiminle konuşuyor? | Conversations |
| En çok trafik üreten cihaz kim? | Endpoints |
| Bu MAC/IP kime ait? | Resolved Addresses |
| Hangi alan adları sorgulanmış? | DNS |
| Hangi web istekleri ve durum kodları? | HTTP |
| IP paket hacmi ve boyut dağılımı? | IPv4 / IPv6 Statistics |

---
> ◀ [Bölüm 7 - Wireshark Filtreleme](07_Wireshark_Filtreleme.md) · 📚 [İndeks](00_A%C4%9F_Trafi%C4%9Fi_Analizi_%C4%B0ndeks.md) · [Bölüm 9 - Analiz](09_Wireshark_Analiz.md) ▶
