---
title: "Wi-Fi Çalışma Modları"
ana-not: "[[00_Wi-Fi_Hackleme_İndex]]"
tarih: 2026-11-19
aliases:
  - Wi-Fi Çalışma Modları
tags:
  - siber-guvenlik
  - wifi
  - kablosuz-ag
  - interface-modes
  - monitor-mode
---

# Wi-Fi Çalışma Modları

> ◀ [Wi-Fi Arayüzleri ve Yapılandırma](04_Wi-Fi_Aray%C3%BCzleri_ve_Yap%C4%B1land%C4%B1rma.md) · 📚 [İndeks](00_Wi-Fi_Hackleme_%C4%B0ndex.md) · [Aircrack-ng Genel Bakış](06_Aircrack-ng_Genel_Bak%C4%B1%C5%9F.md) ▶

> [!NOTE]
> **Özet**
> Ağ kartı 5 modda çalışabilir. Parola kırma ve trafik dinleme için **Monitor**, sahte AP (Evil Twin) için **Master/AP** modu gerekir.

| Mod | Ne yapar? | Pentest kullanımı |
|---|---|---|
| Managed | Normal istemci | Ağa bağlanmak |
| **Monitor** | Havadaki tüm çerçeveleri yakalar | WEP/WPA/WPA2 kırma, deauth, keşif |
| Master (AP) | Kart AP gibi yayın yapar (`hostapd`) | Evil Twin / Rogue AP |
| Ad-Hoc | Cihazdan cihaza doğrudan bağlantı | Nadiren |
| Mesh | 802.11s mesh düğümü | Mesh ağ testleri |

Sızma testlerinde ağ kartımızın (interface) hangi rolde çalıştığı çok önemlidir. WPA şifrelerini kırmak için farklı, sahte bir ağ (Rogue AP) oluşturmak için farklı bir mod kullanmamız gerekir. Temelde 5 ana çalışma modu vardır:

## 1. Yönetilen Mod (Managed Mode)
Ağ kartının varsayılan, standart çalışma modudur. Cihazın bir son kullanıcı (istemci) gibi davranarak etraftaki ağları (AP) bulmasını ve onlara bağlanmasını sağlar.

**Kullanım:** Kartı bu moda almak ve bir ağa bağlanmak için:
```bash
sudo ifconfig wlan0 down
sudo iwconfig wlan0 mode managed
sudo iwconfig wlan0 essid HTB-Wifi # Bağlanılacak ağın (ESSID) adı
sudo iwconfig # Durumu kontrol et
```

## 2. İzleme Modu (Monitor Mode)
Sızma testlerinin kalbidir. Normalde bir ağ kartı sadece "kendisine" gelen paketleri okur. Monitor mode, *promiscuous* moddan farklıdır: kart **bir ağa bağlı olmadan** havadaki tüm 802.11 çerçevelerini yakalar. Ancak İzleme modunda kart, hedefin kim olduğuna bakılmaksızın **havadaki tüm Wi-Fi trafiğini (şifreler, el sıkışmalar, aranan ağlar) yakalar ve kaydeder.** WEP, WPA, WPA2 kırmak için bu mod şarttır.

**Kullanım:** Kartı İzleme (Monitor) moduna almak için:
```bash
sudo ifconfig wlan0 down
sudo iw wlan0 set monitor control
sudo ifconfig wlan0 up
iwconfig # Mode:Monitor olarak değiştiğini doğrula
```

## 3. Ana Mod / Erişim Noktası Modu (Master Mode)
Ağ kartımızın bir İstemci (bağlanan) değil, **Modem (Access Point)** gibi davranıp etrafa yayın yaptığı moddur. Sızma testlerinde "Evil-Twin" (Sahte Erişim Noktası / Rogue AP) saldırıları yapmak için kartın bu modu desteklemesi gerekir.
*Bu mod doğrudan `iwconfig` ile açılamaz. `hostapd` gibi harici bir yönetim yazılımı (daemon) gerektirir.*

**Kullanım:** `hostapd` ile sahte bir ağ ayağa kaldırmak:
```bash
# Önce bir konfigürasyon dosyası (open.conf) oluşturulur:
# interface=wlan0
# driver=nl80211
# ssid=HTB-Hello-World
# channel=2
# hw_mode=g

# Ardından ağ başlatılır:
sudo hostapd open.conf
```

## 4. Ad-Hoc Modu (IBSS)
Merkezi bir modem (AP) olmadan, cihazların doğrudan birbirleriyle (Peer-to-Peer / P2P) iletişim kurduğu merkezsiz moddur. Geçici ağlar ve eski cihazlarla dosya paylaşımı için kullanılır.

**Kullanım:**
```bash
sudo iwconfig wlan0 mode ad-hoc
sudo iwconfig wlan0 essid HTB-Mesh
```

## 5. Mesh Modu (802.11s)
Büyük alanlarda (kampüs, kurumsal ağlar) cihazın kendini yapılandıran bir yönlendirme ağına **Mesh Point** (düğüm) olarak katıldığı moddur. Bu ağlardaki zafiyetler (örneğin düğüm taklidi / node impersonation) test edilirken kullanılır.

**Kullanım:**
```bash
sudo iw dev wlan0 set type mesh
```

---
> **Pentest Özet Çıkarımı:**
> *   Parola Kırma ve Trafik Dinleme -> **Monitor Mode** yeterlidir.
> *   Sahte Ağ (Evil Twin) Kurma -> **Master Mode** (hostapd desteği ile) gereklidir.

---
> ◀ [Wi-Fi Arayüzleri ve Yapılandırma](04_Wi-Fi_Aray%C3%BCzleri_ve_Yap%C4%B1land%C4%B1rma.md) · 📚 [İndeks](00_Wi-Fi_Hackleme_%C4%B0ndex.md) · [Aircrack-ng Genel Bakış](06_Aircrack-ng_Genel_Bak%C4%B1%C5%9F.md) ▶
