---
title: "Bölüm 12 - Servis Versiyon Tespiti"
ana-not: "[[00_Nmap_ile_Ağ_Tarama_İndeks]]"
tarih: 2026-11-26
aliases:
  - Servis Versiyon Tespiti
  - Service Version Detection
  - nmap -sV
tags:
  - siber-guvenlik
  - nmap
  - ag-tarama
  - versiyon-tespiti
  - sv
---

# Bölüm 12 - Servis Versiyon Tespiti

> 📚 [İndeks](00_Nmap_ile_A%C4%9F_Tarama_%C4%B0ndeks.md) · ◀ [Bölüm 11 - Zamanlama Şablonları](11_Zamanlama_%C5%9Eablonlar%C4%B1.md)

## 1. Servis Versiyon Tespitinin Önemi
Servis versiyon tespiti, ağ güvenliği ve yönetimi açısından önemlidir. Nmap bu ihtiyaç için güçlü bir servis ve versiyon tespit özelliği sunar.

| Neden önemli? | Açıklama |
|---|---|
| **Güvenlik değerlendirmeleri** | Ağdaki servislerin ve versiyonlarının bilinmesi, potansiyel zafiyetleri belirlemek için hayati öneme sahiptir. Belirli bir versiyona yönelik zafiyetleri ve güncelleme ihtiyaçlarını tespit etmek için bu bilgi gerekir. |
| **Ağ envanteri** | Cihazların hangi servisleri ve hangi versiyonlarla çalıştırdığını bilmek, etkili ağ yönetimi ve planlaması için gereklidir. |
| **Gizli servisleri keşfetme** | Standart olmayan portlarda çalışan veya gizlenmeye çalışılan servisleri bulmak, güvenlik açıklarını ve ağdaki gizli faaliyetleri ortaya çıkarabilir. Örneğin 34359 portunda çalışan bir HTTP sunucusu. |

## 2. Nmap ile Servis Versiyon Tespiti
Nmap'in **`-sV`** parametresi, taramaya servis versiyon tespitini ekler. Açık portlara bağlanarak ve servislerle iletişim kurarak çalışır. Sonuçta yalnızca hangi portların açık olduğunu değil, bu portlarda **hangi servislerin** çalıştığını ve **hangi versiyonda** olduklarını da belirler.

> [!NOTE]
> Yardım menüsündeki `SERVICE/VERSION DETECTION` bölümü (`-sV`, `--version-intensity`, `--version-light`, `--version-all`) için: [Bölüm 2 - Genel Bakış](02_Genel_Bak%C4%B1%C5%9F.md). Bölüm 4'teki `-A` taraması da `-sV`'yi içerir: [Bölüm 4 - Port Tarama](04_Port_Tarama.md)

```
root💀hackerbox:~# nmap -sV scanme.nmap.org
Starting Nmap 7.94 ( https://nmap.org ) at 2024-03-30 16:59 +03
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.24s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (Ubuntu Linux; protocol 2.0)
80/tcp    open  http       Apache httpd 2.4.7 ((Ubuntu))
9929/tcp  open  nping-echo Nping echo
31337/tcp open  tcpwrapped
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 147.76 seconds
```

### 2.1 Çıktıyı Okuma

| Port | Servis | Versiyon |
|---|---|---|
| `22/tcp` | ssh | OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (protokol 2.0) |
| `80/tcp` | http | Apache httpd 2.4.7 (Ubuntu) |
| `9929/tcp` | nping-echo | Nping echo |
| `31337/tcp` | tcpwrapped | Versiyon belirlenemedi |

`Service Info` satırı, tespit edilen işletim sistemini (Linux) ve CPE tanımlayıcısını verir.

> [!TIP]
> Bölüm 3'te `-Pn` ile alınan çıktıda 31337 portu `Elite` olarak görünüyordu. `-sV` ile gerçek servis etiketi `tcpwrapped` çıktı; yani servis belirlenememiş, bağlantı kabul edilip hemen kapatılmış. Port numarasına bakarak servis tahmin etmek yanıltıcı olabilir, bu yüzden `-sV` kullanılır. Port durumları için: [Bölüm 3 - Host Keşfi](03_Host_Ke%C5%9Ffi.md)

> [!WARNING]
> `-sV` hedefle gerçekten iletişim kurduğu için SYN taramasından daha fazla iz bırakır. Örnek hedef (`scanme.nmap.org`) yalnızca gösterim içindir. Taramayı yalnızca izinli hedeflerde ve kendi lab ortamında yap.

---
> 📚 [İndeks](00_Nmap_ile_A%C4%9F_Tarama_%C4%B0ndeks.md) · ◀ [Bölüm 11 - Zamanlama Şablonları](11_Zamanlama_%C5%9Eablonlar%C4%B1.md) · [Bölüm 13 - İşletim Sistemi Tespiti](13_%C4%B0%C5%9Fletim_Sistemi_Tespiti.md) ▶
