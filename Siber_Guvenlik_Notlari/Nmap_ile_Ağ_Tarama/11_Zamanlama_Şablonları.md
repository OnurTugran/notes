---
title: "Bölüm 11 - Zamanlama Şablonları"
ana-not: "[[00_Nmap_ile_Ağ_Tarama_İndeks]]"
tarih: 2026-11-26
aliases:
  - Nmap Zamanlama Şablonları
  - Timing Templates
  - nmap -T
tags:
  - siber-guvenlik
  - nmap
  - ag-tarama
  - zamanlama
  - performans
---

# Bölüm 11 - Zamanlama Şablonları

> 📚 [İndeks](00_Nmap_ile_A%C4%9F_Tarama_%C4%B0ndeks.md) · ◀ [Bölüm 10 - TCP Idle Tarama](10_TCP_Idle_Tarama.md)

## 1. Zamanlama Şablonları Nedir?
Nmap, taramaları optimize etmek ve farklı ağ koşullarına uyum sağlamak için **altı zamanlama şablonu** sunar. Şablonlar **`-T`** parametresiyle belirtilir ve her biri tarama hızını ve davranışını büyük ölçüde etkiler. 0'dan 5'e kadar numaralandırılır ya da isimleriyle kullanılabilir.

| Şablon | Parametre | İsim |
|---|---|---|
| 0 | `-T0` | **Paranoid** |
| 1 | `-T1` | **Sneaky** |
| 2 | `-T2` | **Polite** |
| 3 | `-T3` | **Normal** (varsayılan) |
| 4 | `-T4` | **Aggressive** |
| 5 | `-T5` | **Insane** |

> [!NOTE]
> Yardım menüsündeki `-T<0-5>: Set timing template (higher is faster)` seçeneği budur: [Bölüm 2 - Genel Bakış](02_Genel_Bak%C4%B1%C5%9F.md)

```mermaid
flowchart LR
    T0["-T0<br/>Paranoid"] --> T1["-T1<br/>Sneaky"] --> T2["-T2<br/>Polite"] --> T3["-T3<br/>Normal"] --> T4["-T4<br/>Aggressive"] --> T5["-T5<br/>Insane"]
    G["Daha yavaş, daha az iz"] -.-> T0
    T5 -.-> H["Daha hızlı, daha az doğruluk"]
```

## 2. Modlar

### 2.1 Paranoid (`-T0`) ve Sneaky (`-T1`)
**IDS (Saldırı Tespit Sistemleri)** algılamasından kaçınmak için tasarlanmıştır. Ağda çok düşük profille hareket eder ve paketler arasına **uzun gecikmeler** koyar.

### 2.2 Polite (`-T2`)
Tarama hızını düşürerek **bant genişliğini ve hedef makinenin kaynaklarını** korur. Daha yavaş tarama yaparak ağdaki etkini en aza indirir.

### 2.3 Normal (`-T3`)
Nmap'in **varsayılan** modudur. Ek seçenek belirtmeden yapılan taramalar bu modu kullanır.

### 2.4 Aggressive (`-T4`)
Ağın **hızlı ve güvenilir** olduğunu varsayarak taramayı hızlandırır. Daha hızlı sonuç için bazı doğruluk ödünleri verilebilir.

### 2.5 Insane (`-T5`)
Olağanüstü hızlı bir ağın olduğunu ya da hız uğruna doğruluğun düşmesinin göze alındığını varsayar.

## 3. Uygulama

### 3.1 `-T5` Insane
```
root💀hackerbox:~# nmap scanme.nmap.org -T5
Starting Nmap 7.94 ( https://nmap.org ) at 2024-03-30 16:41 +03
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.24s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT      STATE SERVICE
80/tcp    open  http
9929/tcp  open  nping-echo
31337/tcp open  Elite

Nmap done: 1 IP address (1 host up) scanned in 118.60 seconds
```

### 3.2 `-T3` Normal
```
root💀hackerbox:~# nmap scanme.nmap.org -T3
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.24s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
9929/tcp  open  nping-echo
31337/tcp open  Elite

Nmap done: 1 IP address (1 host up) scanned in 151.10 seconds
```

### 3.3 Karşılaştırma
İki tarama arasında hem **sonuç** hem **süre** farkı var:

| | `-T5` | `-T3` |
|---|---|---|
| Süre | 118.60 sn | 151.10 sn |
| Filtrelenmiş port | 997 | 996 |
| Açık port | 3 (80, 9929, 31337) | 4 (22, 80, 9929, 31337) |

> [!TIP]
> `-T5` taramada **22/tcp (ssh) bulunamadı**; `-T3` taramada bulundu. Hız arttıkça doğruluk düşebiliyor. Bu örnekte `-T5` yalnızca yaklaşık 30 saniye kazandırdı ama bir açık portu kaçırdı.

## 4. Tavsiyeler
- **`-T4`:** Makul hızda ve güvenilir bir geniş bant ya da ethernet bağlantın varsa önerilir. Modern ve güvenilir ağlarda tarama yaparken yararlıdır.
- **`-T5`:** Bazı kullanıcılar için çok agresif olabilir; ağın kapasitesine ve istenen gizlilik derecesine bağlıdır.
- **`-T2`:** Dikkat çekmek istemeyen kullanıcılar tercih eder ama taramayı önemli ölçüde yavaşlatır.
- **`-T0` ve `-T1`:** IDS'ten kaçınmak için kullanışlı olabilir ama **binlerce makine veya port** taranırken olağanüstü uzun sürebilir.

## 5. Özet
Zamanlama şablonları, Nmap taramalarını farklı ağ koşullarına ve ihtiyaçlara göre uyarlamak için güçlü ve basit bir yöntemdir. Öncelik gizlilik, hız ya da kaynak kullanımı olabilir; her durumda hedefin ve ağ ortamının özellikleri dikkate alınmalıdır.

> [!WARNING]
> Örnek hedef (`scanme.nmap.org`) yalnızca gösterim içindir. Taramayı yalnızca izinli hedeflerde ve kendi lab ortamında yap.

---
> 📚 [İndeks](00_Nmap_ile_A%C4%9F_Tarama_%C4%B0ndeks.md) · ◀ [Bölüm 10 - TCP Idle Tarama](10_TCP_Idle_Tarama.md) · [Bölüm 12 - Servis Versiyon Tespiti](12_Servis_Versiyon_Tespiti.md) ▶
