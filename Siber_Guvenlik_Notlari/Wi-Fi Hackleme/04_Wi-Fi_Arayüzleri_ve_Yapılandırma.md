---
title: "Wi-Fi Arayüzleri ve Yapılandırma"
ana-not: "[[00_Wi-Fi_Hackleme_İndex]]"
tarih: 2026-11-19
aliases:
  - Wi-Fi Arayüzleri ve Yapılandırma
tags:
  - siber-guvenlik
  - wifi
  - kablosuz-ag
  - interface
  - iw
---

# Wi-Fi Arayüzleri ve Yapılandırma

> ◀ [[03_802.11_Çerçeveleri_ve_Bağlantı_Döngüsü|802.11 Çerçeveleri ve Bağlantı Döngüsü]] · 📚 [[00_Wi-Fi_Hackleme_İndex|İndeks]] · [[05_Wi-Fi_Çalışma_Modları|Wi-Fi Çalışma Modları]] ▶

> [!abstract] Özet
> Sızma testinin başarısı büyük ölçüde **ağ kartına** bağlıdır. Kartın **monitor mode + packet injection** desteği ve Linux'ta iyi bir **sürücüsü** olmalıdır. Hız ikincil önemdedir.

Wi-Fi arayüzleri (ağ kartları/adaptörler) sızma testinin temel taşıdır. Verileri bu arayüzler aracılığıyla iletir ve alırız. Zayıf veya uyumsuz bir ağ kartı seçmek, sızma testi çabalarımızın başarısız olmasına (örneğin paket yakalayamamaya) neden olur.

## 1. Doğru Arayüzü Seçmek (Choosing the Right Interface)
Bir Wi-Fi adaptörü alırken sadece hızına değil, sızma testi yeteneklerine bakmalıyız. Tek bantlı (sadece 2.4GHz) bir kart, iyi bir Linux sürücüsü varsa çift bantlı bir karttan çok daha iyi performans gösterebilir. Aranması gereken temel özellikler şunlardır:
1. **IEEE 802.11ac veya IEEE 802.11ax (Wi-Fi 6) desteği** (Güncel ağları görebilmek için).
2. **Monitor Mode (İzleme Modu)** ve **Packet Injection (Paket Enjeksiyonu)** desteği.
3. Donanımın yonga seti (Chipset) ve Linux işletim sistemindeki sürücü (driver) uyumluluğu.

---

## 2. Arayüz Gücü ve Bölge Ayarları (Interface Strength & TX Power)
Fiziksel konumlandırma kablosuz ağ saldırılarında çok önemlidir. Kartımızın menzili ve iletim gücü (TX Power) yetersizse saldırılarımız hedefe ulaşmaz.

Kartımızın mevcut gücünü `iwconfig` (veya yeni araç `iw dev`) ile görebiliriz:
```bash
iwconfig
iw dev wlan0 info
```

Varsayılan iletim gücü, işletim sistemindeki bölge/ülke koduna göre yasal sınırlarla kısıtlanmıştır. Türkiye (BTK/ETSI standartları) için bu sınır 2.4 GHz bandında yasal olarak **20 dBm'dir** (100 mW). Mevcut bölge ayarlarını görmek için:
```bash
iw reg get
```

Sızma testi laboratuvarında çalışırken veya ağ kartını yurtdışından getirdiğinde bölge ayarı farklı (örneğin gücü sınırlandırmayan US veya BO) olabilir. Cihazın yayın gücünü Türkiye (TR) yasal sınırlarına ve standartlarına uygun hale getirmek (ve donanımı aşırı ısınmadan korumak) için bölge kodunu `TR` olarak ayarlayıp gücü 20 dBm'e sabitleyebiliriz.

**Türkiye (TR) Standartlarına Göre İletim Gücünü Ayarlama Adımları:**
```bash
# 1. Bölgeyi Türkiye (TR) olarak değiştir
sudo iw reg set TR

# 2. Arayüzü kapat
sudo ifconfig wlan0 down

# 3. Gücü Türkiye yasal sınırına (20 dBm) ayarla
sudo iwconfig wlan0 txpower 20

# 4. Arayüzü tekrar aç
sudo ifconfig wlan0 up
```

> [!note] Modern alternatif
> Yeni sürücülerde `iwconfig txpower` çalışmayabilir. Bu durumda `iw` kullan (birim mBm'dir, 2000 mBm = 20 dBm):
> ```bash
> sudo iw dev wlan0 set txpower fixed 2000
> ```
> Bölge kodunu değiştirmek **yasal sınırları aşmak için değil**, bulunduğun ülkenin kurallarına uymak içindir.

---

## 3. Sürücü Yeteneklerini Kontrol Etme (Checking Driver Capabilities)
Kartımızın WPA3 (SAE) destekleyip desteklemediğini, hangi frekanslarda çalıştığını ve paket enjeksiyonu yapıp yapamadığını `iw list` komutuyla detaylıca görebiliriz.

```bash
iw list
```
*Bu komutun çıktısında Supported Ciphers (Desteklenen Şifrelemeler), Supported interface modes (Desteklenen Modlar - örn: monitor, AP) ve Frequencies (Frekanslar) gibi kritik sızma testi bilgileri yer alır.*

---

## 4. Etraftaki Ağları Tarama (Scanning Available WiFi Networks)
Ağ kartımızla etraftaki ağları (ESSID), sinyal kalitelerini (Quality) ve şifreleme türlerini (IEEE) hızlıca taramak için `iwlist` komutunu `grep` ile filtreleyerek kullanabiliriz:

```bash
iwlist wlan0 scan | grep 'Cell\|Quality\|ESSID\|IEEE'
```

---

## 5. Kanal ve Frekans Değiştirme (Changing Channel & Frequency)
Hedef ağ belirli bir kanalda (örneğin Kanal 64) yayın yapıyorsa, ağ kartımızı o kanala sabitlememiz gerekir. Kartın desteklediği kanalları görmek için:

```bash
iwlist wlan0 channel
```

**Kanalı Değiştirmek İçin:**
```bash
sudo ifconfig wlan0 down
sudo iwconfig wlan0 channel 64
sudo ifconfig wlan0 up
```

**Frekansı Doğrudan Değiştirmek İçin (Alternatif):**
```bash
sudo ifconfig wlan0 down
sudo iwconfig wlan0 freq "5.52G"
sudo ifconfig wlan0 up
```

---
> ◀ [[03_802.11_Çerçeveleri_ve_Bağlantı_Döngüsü|802.11 Çerçeveleri ve Bağlantı Döngüsü]] · 📚 [[00_Wi-Fi_Hackleme_İndex|İndeks]] · [[05_Wi-Fi_Çalışma_Modları|Wi-Fi Çalışma Modları]] ▶
