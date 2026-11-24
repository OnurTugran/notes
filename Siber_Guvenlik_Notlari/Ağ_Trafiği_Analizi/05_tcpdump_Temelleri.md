---
title: "Bölüm 5 - tcpdump Temelleri"
ana-not: "[[00_Ağ_Trafiği_Analizi_İndeks]]"
tarih: 2026-11-24
aliases:
  - tcpdump
  - tcpdump Temelleri
  - paket yakalama
tags:
  - siber-guvenlik
  - ag-trafigi-analizi
  - tcpdump
  - pcap
  - paket-yakalama
---

# Bölüm 5 - tcpdump Temelleri

> ◀ [Bölüm 4 - Ağ Analizi Süreci](04_A%C4%9F_Analizi_S%C3%BCreci.md) · 📚 [İndeks](00_A%C4%9F_Trafi%C4%9Fi_Analizi_%C4%B0ndeks.md)

`tcpdump`, komut satırından paket yakalayan güçlü bir araçtır ([Bölüm 3](03_Temel_Ara%C3%A7lar.md)). Bu bölümde kurulum, yakalama, çıktıyı okuma, filtreleme ve dosyaya kaydetme anlatılır.

## 1. Kurulum
Çoğu Linux dağıtımı `tcpdump`'ı varsayılan olarak içerir. Yüklü değilse paket yöneticisiyle kur ([Paket Yönetimi](../Linux_Temelleri/09_Paket_Y%C3%B6netimi.md)):

| Dağıtım | Komut |
|---|---|
| Debian / Ubuntu | `sudo apt-get install tcpdump` |
| RHEL / Fedora | `sudo dnf install tcpdump` |

### Sürüm doğrulama
```bash
which tcpdump
tcpdump --version
```
`which` kurulu olduğu yolu, `--version` sürümü gösterir ([which](../Linux_Temelleri/06_Dosya_ve_Dizinleri_Bulma.md)).

## 2. Trafik Yakalama
`tcpdump`'ı çalıştırmak için **root veya `sudo`** gerekebilir.

**Mevcut arabirimleri listelemek:**
```bash
tcpdump -D
```
**Belirtilen arabirimde basit yakalama başlatmak** (`eth0` yerine yakalama yapmak istediğin arabirimi yaz):
```bash
tcpdump -i eth0
```
Durdurmak için `CTRL+C`.

> [!NOTE]
> **`wlan0` kendi trafiğini mi, tüm Wi-Fi trafiğini mi gösterir?**
> Normal (managed) modda `tcpdump -i wlan0` **yalnızca sana gelen/senden giden trafiği** (ve ağdaki yayın/broadcast, multicast paketlerini) gösterir. Aynı Wi-Fi'a bağlı diğer cihazların paketlerini görmezsin.
> - Wi-Fi kartı bağlıyken diğer istemcilerin paketlerini sürücü zaten iletmez; ayrıca WPA2/WPA3'te her istemcinin trafiği **ayrı anahtarla şifrelidir.**
> - Başkalarının çerçevelerini görmek için kartın **monitor moduna** alınması gerekir. Monitor modda bile korumalı (WPA2/3) ağların veri içeriği şifreli görünür; çözmek için o ağın parolası ve el sıkışması (handshake) gerekir.
> - Kablolu ağda da benzer şekilde **switch** yalnızca sana ait trafiği sana gönderir.
>
> Monitor mod ve şifre çözme için: [Wi-Fi Çalışma Modları](../Wi-Fi%20Hackleme/05_Wi-Fi_%C3%87al%C4%B1%C5%9Fma_Modlar%C4%B1.md), [Airmon-ng](../Wi-Fi%20Hackleme/06.1_Airmon-ng.md), [Airdecap-ng](../Wi-Fi%20Hackleme/06.5_Airdecap-ng.md). Başkalarının ağ trafiğini yakalamak yalnızca **kendi ağında ya da yazılı izinle** yapılmalıdır; izinsiz dinleme yasa dışıdır.

## 3. tcpdump Çıktısını Anlama
Çıktıda **her satır yakalanan bir pakettir.** Satırda şunlar bulunur:

| Alan | Anlamı |
|---|---|
| **Zaman damgası** | Paketin yakalandığı zaman |
| **Protokol** | Protokol türü (TCP, UDP, ICMP vb.) |
| **Kaynak ve hedef** | IP adresleri ve port numaraları |
| **Paket boyutu** | Paketin bayt cinsinden uzunluğu |
| **Ek bilgiler** | Bayraklar, sıra numaraları ve protokole özel bilgiler |

**Örnek satır:**
```
13:38:45.232106 IP 192.168.122.59.58774 > 93.184.216.34.http: Flags [.], ack 1021, win 501, options [nop,nop,TS val 1034903020 ecr 4089339606], length 0
```

| Bölüm | Değer | Açıklama |
|---|---|---|
| Zaman damgası | `13:38:45.232106` | Paketin yakalandığı zaman (saat:dakika:saniye.mikrosaniye) |
| Protokol | `IP` | Internet Protocol |
| Kaynak | `192.168.122.59.58774` | Kaynak IP adresi ve port numarası |
| Hedef | `93.184.216.34.http` | Hedef IP ve port (`http` = port 80) |
| Bayraklar | `Flags [.]` | TCP bayrakları. `.` = **ACK** set edilmiş; diğerleri (`SYN`, `FIN`, `RST`, `PSH`, `URG`) set edilmemiş |
| ACK numarası | `ack 1021` | Alıcı tarafından onaylanan TCP sıra numarası |
| Pencere boyutu | `win 501` | Alıcının kabul edebileceği veri miktarı |
| Seçenekler | `options [nop,nop,TS val 1034903020 ecr 4089339606]` | TCP seçenekleri |
| Uzunluk | `length 0` | Paketin veri kısmı (burada 0, yani sadece onay paketi) |

TCP seçenekleri:
- `nop` : Boş seçenek (hizalama için)
- `TS val 1034903020` : Gönderenin zaman damgası
- `ecr 4089339606` : Karşı tarafın zaman damgasının yankısı (echo reply)

**Bayrak gösterimleri:**

| Gösterim | Bayrak |
|---|---|
| `[S]` | SYN (bağlantı başlatma) |
| `[S.]` | SYN-ACK |
| `[.]` | ACK |
| `[P.]` | PSH-ACK (veri gönderimi) |
| `[F.]` | FIN-ACK (bağlantıyı kapatma) |
| `[R]` | RST (bağlantıyı sıfırlama) |

> [!TIP]
> Satırı soldan sağa oku: **kim → kime, hangi bayrakla, ne kadar veri.** Port adı yerine numara görmek için `-n` ekle: `tcpdump -n -i eth0` (isim çözümlemesini kapatır, daha hızlıdır). Belirli sayıda paket için `-c 10` (10 paketten sonra durur).

## 4. Trafiği Filtreleme
`tcpdump`, belirli trafiğe odaklanmak için filtre kullanır ([Filter adımı](04_A%C4%9F_Analizi_S%C3%BCreci.md)).

| Filtre | Örnek | Ne gösterir? |
|---|---|---|
| **Protokole göre** | `tcpdump 'tcp'` | Yalnızca TCP trafiği |
| **Ana bilgisayara göre** | `tcpdump 'host 192.168.1.100'` | Belirli bir IP adresine giden/gelen trafik |
| **Bağlantı noktasına göre** | `tcpdump 'port 80'` | 80 numaralı porttaki trafik (HTTP) |
| **Kombinasyon** | `tcpdump 'tcp and port 22'` | SSH VE TCP |

Kombinasyonlarda **`and`, `or`** gibi mantıksal operatörler kullanılır (`not` da çalışır). Bir arabirim ile birlikte: `sudo tcpdump -i eth0 'tcp and port 22'`.

> [!TIP]
> Hangi portun hangi servis olduğunu hatırlamak için [Yaygın Portlar tablosuna](02_Hat%C4%B1rlatmalar.md) bak (örn. 22 SSH, 53 DNS, 443 HTTPS).

## 5. Dosya Okuma / Yazma
Daha sonra analiz edilebilecek bir **`output.pcap`** dosyası oluşturmak ve okumak için:

**Yakalamaları kaydetme:**
```bash
tcpdump -i eth0 -w output.pcap
```
**Dosyadan okuma:**
```bash
tcpdump -r output.pcap
```
`-w` ile kaydedilen dosya ikili (binary) PCAP biçimindedir; ekrana okunur çıktı vermez. Okumak için `-r` kullanılır. Aynı dosyayı **Wireshark'ta** da açabilirsin ([Bölüm 3](03_Temel_Ara%C3%A7lar.md): "tcpdump ile yakala, Wireshark'ta aç"). Okurken de filtre uygulanabilir: `tcpdump -r output.pcap 'port 443'`.

## 6. Hızlı Özet

| İş | Komut |
|---|---|
| Kurulu mu / sürüm | `which tcpdump` / `tcpdump --version` |
| Arabirimleri listele | `tcpdump -D` |
| Arabirimde yakala | `sudo tcpdump -i eth0` |
| Protokole göre | `tcpdump 'tcp'` |
| Hosta göre | `tcpdump 'host 192.168.1.100'` |
| Porta göre | `tcpdump 'port 80'` |
| Birleştir | `tcpdump 'tcp and port 22'` |
| Dosyaya kaydet | `tcpdump -i eth0 -w output.pcap` |
| Dosyadan oku | `tcpdump -r output.pcap` |

---
> ◀ [Bölüm 4 - Ağ Analizi Süreci](04_A%C4%9F_Analizi_S%C3%BCreci.md) · 📚 [İndeks](00_A%C4%9F_Trafi%C4%9Fi_Analizi_%C4%B0ndeks.md) · [Bölüm 6 - Wireshark](06_Wireshark_ile_A%C4%9F_Analizi.md) ▶
