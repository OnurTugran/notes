
# Wi-Fi Arayüzleri ve Yapılandırma (Wi-Fi Interfaces)

Wi-Fi arayüzleri (ağ kartları/adaptörler) sızma testinin temel taşıdır. Verileri bu arayüzler aracılığıyla iletir ve alırız. Zayıf veya uyumsuz bir ağ kartı seçmek, sızma testi çabalarımızın başarısız olmasına (örneğin paket yakalayamamaya) neden olur.

## 1. Doğru Arayüzü Seçmek (Choosing the Right Interface)
Bir Wi-Fi adaptörü alırken sadece hızına değil, sızma testi yeteneklerine bakmalıyız. Tek bantlı (sadece 2.4GHz) bir kart, iyi bir Linux sürücüsü varsa çift bantlı bir karttan çok daha iyi performans gösterebilir. Aranması gereken temel özellikler şunlardır:
1. **IEEE 802.11ac veya IEEE 802.11ax (Wi-Fi 6) desteği** (Güncel ağları görebilmek için).
2. **Monitor Mode (İzleme Modu)** ve **Packet Injection (Paket Enjeksiyonu)** desteği.
3. Donanımın yonga seti (Chipset) ve Linux işletim sistemindeki sürücü (driver) uyumluluğu. 

---

## 2. Arayüz Gücü ve Bölge Ayarları (Interface Strength & TX Power)
Fiziksel konumlandırma kablosuz ağ saldırılarında çok önemlidir. Kartımızın menzili ve iletim gücü (TX Power) yetersizse saldırılarımız hedefe ulaşmaz.

Kartımızın mevcut gücünü `iwconfig` komutuyla görebiliriz:
```bash
iwconfig
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


# Wi-Fi Ağ Kartı Çalışma Modları (Interface Modes)

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

## 2. İzleme Modu (Monitor Mode / Promiscuous Mode)
Sızma testlerinin kalbidir. Normalde bir ağ kartı sadece "kendisine" gelen paketleri okur. Ancak İzleme modunda kart, hedefin kim olduğuna bakılmaksızın **havadaki tüm Wi-Fi trafiğini (şifreler, el sıkışmalar, aranan ağlar) yakalar ve kaydeder.** WEP, WPA, WPA2 kırmak için bu mod şarttır.

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

## 4. Ad-Hoc Modu
Merkezi bir modem (AP) olmadan, cihazların doğrudan birbirleriyle (Peer-to-Peer / P2P) iletişim kurduğu merkezsiz moddur. Genellikle evlerdeki "Mesh" sistemlerinin kendi aralarındaki iletişim (backhaul) için kullanılır.

**Kullanım:**
```bash
sudo iwconfig wlan0 mode ad-hoc
sudo iwconfig wlan0 essid HTB-Mesh
```

## 5. Mesh Modu
Büyük fiziksel alanlarda (kurumsal şirketler, kampüsler) cihazın kendini yapılandıran devasa bir yönlendirme ağına "Mesh Point" (Düğüm) olarak katıldığı moddur. Bu ağlardaki zafiyetleri (node impersonation) sömürmek için kullanılır.

**Kullanım:**
```bash
sudo iw dev wlan0 set type mesh
```

---
> **Pentest Özet Çıkarımı:**
> *   Parola Kırma ve Trafik Dinleme -> **Monitor Mode** yeterlidir.
> *   Sahte Ağ (Evil Twin) Kurma -> **Master Mode** (hostapd desteği ile) gereklidir.