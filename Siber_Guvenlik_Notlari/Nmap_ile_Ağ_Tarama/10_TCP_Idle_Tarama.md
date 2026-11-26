---
title: "Bölüm 10 - TCP Idle Tarama"
ana-not: "[[00_Nmap_ile_Ağ_Tarama_İndeks]]"
tarih: 2026-11-26
aliases:
  - Idle Scan
  - Zombi Tarama
  - nmap -sI
tags:
  - siber-guvenlik
  - nmap
  - ag-tarama
  - idle-scan
  - tcp
---

# Bölüm 10 - TCP Idle Tarama

> 📚 [İndeks](00_Nmap_ile_A%C4%9F_Tarama_%C4%B0ndeks.md) · ◀ [Bölüm 9 - TCP ACK Tarama](09_TCP_ACK_Tarama.md)

## 1. Idle Tarama Nedir?
TCP Idle Taraması, port tarama teknikleri arasında benzersiz bir yöntemdir. **"Zombi"** adı verilen bir aracı makine üzerinden, taramanın sonuçları o makineye yönlendirilerek hedefteki portlar taranır. Böylece hedef, taramanın **gerçek kaynağını** göremez; saldırı tespit sistemleri zombi makineyi saldırgan olarak görür.

> [!NOTE]
> Hedefe giden paketler zombi adına (sahte kaynak adresiyle) gönderilir. Tarayan makine yine de zombiyle konuşur, yani zombiye yoklama paketleri gönderir. Gizlenen şey, **hedefin gördüğü kaynak adresidir**.

## 2. Temelleri
Idle Tarama, **Antirez** tarafından **1998**'de geliştirilen ve tamamen **kör** bir port taraması yapılmasını sağlayan zekice bir tekniktir. Özellikle **hedef ile zombi arasındaki IP tabanlı güven ilişkilerini** keşfetmek için kullanılır.

### 2.1 Temel Adımlar
Tarama üç adımdan oluşur ve her port için tekrarlanır:

1. **Zombi'nin IP ID'sini sorgulama:** Önce zombi bilgisayarın IP ID numarası sorgulanır.
2. **SYN paketi gönderme:** Zombi adına, hedefteki belirli bir porta `SYN` paketi gönderilir. Hedefin bu porttaki durumuna göre zombinin IP ID değerinin artıp artmayacağı izlenir.
3. **IP ID'de değişiklik kontrolü:** Zombi'nin IP ID'si tekrar kontrol edilir.

| IP ID değişimi (ilk değere göre) | Sonuç |
|---|---|
| **+2** | Port **açık** |
| **+1** | Port **kapalı** veya **filtrelenmiş** |

```mermaid
sequenceDiagram
    participant S as Saldırgan
    participant Z as Zombi
    participant H as Hedef
    S->>Z: 1. Yoklama (IP ID okunur)
    S->>H: 2. SYN (kaynak adresi = Zombi, sahte)
    H->>Z: Port açıksa SYN/ACK
    Z->>H: RST (IP ID artar)
    S->>Z: 3. Yoklama (IP ID tekrar okunur)
    Note over
