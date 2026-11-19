---
title: "WEP Zafiyetleri ve RC4"
ana-not: "[[00_Wi-Fi_Hackleme_İndex]]"
tarih: 2026-11-19
aliases:
  - WEP Zafiyetleri ve RC4
tags:
  - siber-guvenlik
  - wifi
  - kablosuz-ag
  - wep
  - rc4
---

# WEP Zafiyetleri ve RC4

> ◀ [[07.5_MDK4_ile_WPS_Kilidi_DoS|MDK4 ile WPS Kilidi / DoS]] · 📚 [[00_Wi-Fi_Hackleme_İndex|İndeks]] · [[08.1_WEP_ARP_Request_Replay|WEP ARP Request Replay]] ▶

> [!abstract] Özet
> **WEP** artık kırık sayılır: **24-bit IV** çok küçüktür (tekrar eder), IV **şifresiz** gider ve bütünlük için **CRC32** kullanılır (kriptografik değil). Yeterli IV toplanınca anahtar istatistiksel olarak (PTW/FMS/KoreK) bulunur.

## Hangi saldırı, ne zaman?

| Durum | Saldırı | Not |
|---|---|---|
| Ağda aktif istemci var, ARP trafiği üretiyor | [[08.1_WEP_ARP_Request_Replay\|ARP Request Replay]] | En basit ve hızlı |
| Yakalanan paketten keystream lazım | [[08.2_WEP_KoreK_Chop_Chop\|KoreK Chop Chop]] | Anahtarı bilmeden paket çözer |
| İstemci var ama gerçek AP'ye erişim / ARP trafiği yok | [[08.3_WEP_Cafe_Latte\|Cafe Latte]] | Sahte AP + deauth, 4 terminal |
| Hiç istemci yok | [[08.4_WEP_İstemcisiz_Saldırı\|İstemcisiz saldırı]] | Fake auth + chop-chop/fragmentation |
| Çok az paket, 5 karakterlik anahtar | [[08.5_WEP_Ek_Yöntemler_ve_Bruteforce\|Bruteforce]] | Sözlükle |

---

## 1. WEP Nedir ve Temel Zafiyeti Nereden Gelir?
Wired Equivalent Privacy (WEP), kablosuz ağları şifrelemek için kullanılan ancak günümüzde güvensiz kabul edilen eski bir protokoldür.

**24-bit IV (Initialization Vector) Zafiyeti:**
- WEP, şifreleme için `40-bit` veya `104-bit` uzunluğunda asıl ağ anahtarını (parolayı) kullanır.
- Ancak her paket için rastgele üretilen **Başlatma Vektörü (IV)** her zaman **24-bit** boyutundadır.
- 24-bit çok küçük bir havuz olduğu için, ağda veri aktarımı devam ettikçe bu vektörler hızla **tekrar etmeye (collision)** başlar.
- Alıcı cihazın şifreyi çözebilmesi için IV değeri, veri paketinin ucuna **şifresiz (açık metin)** olarak eklenip yollanmak zorundadır.
- **Sonuç:** Ağ trafiğini dinleyen bir saldırgan (sniffer), şifresiz uçuşan bu IV'ler ile şifreli paketler arasındaki matematiksel korelasyonu çözerek asıl WEP anahtarını bulabilir.

---

## 2. RC4 Şifreleme Algoritması Nasıl Çalışır?
WEP, temelinde simetrik bir akış şifrelemesi (stream cipher) olan **RC4 (Rivest Cipher 4)** algoritmasını kullanır.

Süreç 4 adımda gerçekleşir:
1. **Seed (Tohum) Oluşturma:** `24-bit IV` + `40/104-bit WEP Anahtarı` yan yana birleştirilerek (concatenate) şifreleme tohumu oluşturulur.
2. **KSA (Key Scheduling Algorithm):** Bu tohum algoritmanın iç dizisini başlatır ve değerleri karmaşıklaştırır (permütasyon).
3. **PRGA (Pseudo Random Generation Algorithm):** KSA'dan gelen karışık veriyi işleyerek, şifrelenecek orijinal mesajla birebir aynı uzunlukta rastgele bir **Anahtar Akışı (Keystream)** üretir.
4. **Şifreleme:** Düz metin (orijinal mesaj) ile PRGA'nın ürettiği Keystream **XOR** işlemine sokulur ve ağda iletilecek **Cipher Text (Şifreli Metin)** elde edilir.

---

## 3. WEP Kimlik Doğrulama (Authentication) Türleri
WEP ağlarında iki tür kimlik doğrulama bulunur:

- **Open (Açık):** Cihaz ağa bağlanırken şifre sorulmaz, doğrudan bağlanır (ancak veri iletmek/okumak için arka planda doğru şifreye sahip olması gerekir).
- **Shared (Paylaşılan) - *Zafiyetli Olan*:**
	1. AP (Erişim Noktası), cihaza şifresiz bir `Challenge` (Meydan Okuma metni) yollar.
	2. Cihaz, bu metni WEP şifresiyle şifreleyip `Challenge Response` olarak geri yollar.
	3. **Pentester Açısı:** Saldırgan havadan bu düz metni ve şifrelenmiş cevabı aynı anda yakalarsa, RC4'ün zafiyeti sayesinde ağın şifresini bilmese dahi AP'yi kandırarak **sahte kimlik doğrulama (fake authentication)** yapabilir.

---

## 4. Kullanılan Saldırı Metodolojileri
- **FMS ve PTW Saldırıları:** Yeterli sayıda IV paketi toplandığında, istatistiksel olasılık hesaplamaları kullanarak asıl şifreyi (WEP Key) geri döndüren algoritmik saldırılardır.
- **Paket Enjeksiyonu (ARP Replay / Fragmentation):** Normalde ağda IV toplayıp şifreyi kırmak uzun sürer. Saldırgan, ağı manipüle edip sürekli sahte ARP paketleri yollayarak (tekrar oynatma), AP'nin çok hızlı bir şekilde yeni IV'ler üretmesini ve sürecin hızlanmasını sağlar.

---

## 5. Python ile WEP Seed Üretimi Kavramsal Simülasyonu
Aşağıdaki Python kodu, WEP'in şifreleme tohumunu nasıl oluşturduğunu ve RC4'ü nasıl başlattığını gösterir.

> [!note] Kurulum
> `pip install pycryptodome` gerekir.

```python
from Crypto.Random import get_random_bytes
from Crypto.Cipher import ARC4

# 1. Zafiyetli 24-bit (3 byte) IV üretimi (Ağda şifresiz gider)
IV = get_random_bytes(3)

# 2. 40-bit WEP asıl parolası (5 byte)
key = b'\x01\x02\x03\x04\x05'

# 3. Seed (Tohum) oluşturma (IV + Key)
Seed64 = IV + key

# 4. RC4 algoritmasının tohumla başlatılması (KSA aşaması)
keystream = ARC4.new(Seed64)

# 5. Orijinal metnin Keystream ile XOR'lanarak şifrelenmesi (PRGA aşaması)
msg = keystream.encrypt(b'Wired Equivalent Privacy')

print("Rastgele IV:", IV)
print("Şifreli Ağ Paketi (Ciphertext):", msg)

```

---

## WEP bütünlük kontrolü (CRC32 / ICV)
---

### 1. Bütünlük kontrolü (ICV)
WEP, verinin iletim sırasında bozulup bozulmadığını veya değiştirilip değiştirilmediğini kontrol etmek için **CRC32 (Cyclic Redundancy Check)** algoritmasını kullanır.

- Şifrelenecek orijinal düz metnin (plaintext) standart bir CRC32 özeti (sağlama toplamı) hesaplanır.
- Bu matematiksel değer, bütünlük kontrol mesajı (ICV - Integrity Check Value) olarak düz metnin sonuna iliştirilir.
- `[ Düz Metin + CRC32 ]` bloğu birleştirildikten sonra, RC4 algoritmasının ürettiği **Keystream** ile **XOR** işlemine sokularak ağda iletilecek şifreli paket (Ciphertext) oluşturulur.

---

### 2. KoreK Chop Chop
Bu saldırı CRC32/ICV zafiyetini kullanır, ayrıntısı: [[08.2_WEP_KoreK_Chop_Chop]].

### 3. Python ile CRC32 (ICV) Üretimi Simülasyonu
WEP algoritmasının bütünlük doğrulama adımını Python'daki yerleşik `zlib` kütüphanesi ile pratikte şu şekilde hesaplayabiliriz:

```python
import zlib

# 1. Ağda iletilecek orijinal şifresiz düz metin
packetplaintext = b'Something Sensitive'

# 2. zlib kütüphanesi kullanılarak CRC32 özetinin hesaplanması
crc32 = zlib.crc32(packetplaintext)

# Çıktı 32-bit bir tamsayıdır (sağlama toplamı)
print("Hesaplanan CRC32 (ICV) Değeri:", crc32)

```

### 4. Tam WEP Algoritmasının Birleştirilmesi (Mockup)

Seed (Tohum) Üretimi ve CRC32 Üretimi betiklerini RC4 kütüphanesiyle birleştirerek eksiksiz bir WEP algoritması simülasyonu oluşturabiliriz.

Bu birleştirilmiş betikte süreç şu şekilde işler:
1. Başlatma vektörü (IV) ve ağ anahtarı (key) toplanarak **tohum (seed)** oluşturulur.
2. Bu tohum kullanılarak RC4 üzerinden **anahtar akışı (keystream)** üretilir.
3. Düz metnin (packet plaintext) CRC32 sağlama toplamı hesaplanır ve düz metnin sonuna eklenerek şifrelenecek olan mesaj (**ICV Message**) oluşturulur.
4. Elde edilen bu blok, nihai şifreli metni (**Ciphertext**) üretmek üzere RC4'ün şifreleme fonksiyonuna sokulur.
5. Son olarak, başlatma vektörü (IV) bu nihai şifreli metnin en başına eklenir ve ağda iletilecek olan **Son Mesaj (Final Message)** elde edilir.

#### Birleştirilmiş Simülasyon Kodu (Python)

```python
from Crypto.Random import get_random_bytes
from Crypto.Cipher import ARC4
import binascii
import zlib

# Öncelikle ağda iletmemiz gereken şifresiz orijinal mesajımızı belirliyoruz.
packetplaintext = b'Something Sensitive'

# Ardından bu mesajın CRC32 sağlama toplamını (32-bit integer) hesaplıyoruz.
crc32 = zlib.crc32(packetplaintext)

# 24-bit Başlatma Vektörü (IV) üretimi (3 byte)
IV = get_random_bytes(3)

# 40-bit anahtar (5 byte) ve 64-bit tohum (8 byte) tanımı
key = b'\x01\x02\x03\x04\x05'
Seed64 = IV + key

# 104-bit anahtar (13 byte) ve 128-bit tohum tanımı
key104 = b'\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0A\x0B\x0C\x0D'
Seed128 = IV + key104

# Anahtar akışlarının (keystreams) RC4 ile üretilmesi
keystream = ARC4.new(Seed64)
keystreamB = ARC4.new(Seed128)

# ICV Mesajının oluşturulması
crc32byte = crc32.to_bytes(4, 'big')  # Simülasyon için 'big'. Gerçek WEP'te ICV little-endian eklenir.
ICVMessage = packetplaintext + crc32byte # Orijinal metin ile CRC32 değerini birleştiriyoruz.

# ICV Mesajı ile Keystream'in XOR'lanması sonucu oluşan Nihai Şifreli Metin (Ciphertext)
msg = keystream.encrypt(ICVMessage)
msgB = keystreamB.encrypt(ICVMessage)

# Başlatma Vektörü (IV) ile Nihai Şifreli Metnin birleştirilmesiyle oluşan Son Mesaj
finalmsg = IV + msg
finalmsgb = IV + msgB

print('-------------')
print('CRC32 Checksum: ' + str(crc32))
print('Initialization Vector: ' + str(IV))
print('64-bit Seed: ' + str(Seed64))
print('128-bit Seed: ' + str(Seed128))
print('-------------')
print('ICV Message: ' + str(ICVMessage))
print('Cipher Text 64-bit Seed: ' + str(msg))
print('Cipher Text 128-bit Seed: ' + str(msgB))
print('-------------')
print('Final Message 64-bit Seed: ' + str(finalmsg))
print('Final Message 128-bit Seed: ' + str(finalmsgb))
```

---

## Wireshark ile IV okuma
---

### 1. Ağ Dinleme (Sniffing) Hazırlığı
Kablosuz ağ kartını monitör (dinleme) moduna almak ve bağlantıyı bozabilecek süreçleri temizlemek için sırasıyla aşağıdaki komutlar kullanılır:

```bash
# 1. Mevcut kablosuz ağ arayüzlerini listele (örneğin wlan0)
iwconfig
```

```bash
# 2. Dinlemeyi kesintiye uğratabilecek arka plan işlemlerini zorla kapat
sudo airmon-ng check kill
```

```bash
# 3. Ağ kartını monitör moduna geçir (Arayüz ismi genelde wlan0mon olarak değişir)
sudo airmon-ng start wlan0
```

---

### 2. Hedef Tespiti ve Paket Yakalama
Monitör moduna alınan arayüz üzerinden çevredeki ağlar taranır ve hedefe ait veriler .pcap dosyasına kaydedilir:

```bash
# 1. Çevredeki tüm ağları tara (Hedef WEP ağının MAC Adresini ve Kanalını öğrenmek için)
sudo airodump-ng wlan0mon
```

```bash
# 2. Sadece belirlenen hedefe odaklan ve trafiği "WEP_Trafik" isimli bir dosyaya kaydet
# Parametreler:
# -c      : Hedefin yayın yaptığı kanal (CH)
# --bssid : Hedef modemin MAC adresi
# -w      : Çıktı dosyasının ön adı (WEP_Trafik.cap / .pcap olarak kaydeder)
sudo airodump-ng -c 3 --bssid 60:38:E0:71:E9:DC -w WEP_Trafik wlan0mon
```

---

### 3. Wireshark ile IV (Başlatma Vektörü) Okuma
Kaydedilen trafik dosyasındaki açık metin (şifresiz) IV değerlerini analiz etmek için terminalden Wireshark başlatılır:

```bash
# Yakalanan veya lab ortamında verilen .pcap dosyasını doğrudan Wireshark ile aç
wireshark /opt/IV-Wireshark.pcap
```

Wireshark Arayüzünde IV Bulma Adımları:
1. Listeden incelenecek paketi (örneğin 14 numaralı paketi) bul ve seç.
2. Alt bölümdeki hiyerarşik menüden IEEE 802.11 Data sekmesini genişlet.
3. Onun hemen altındaki WEP Parameters sekmesini genişlet.
4. Initialization Vector (IV) alanında yazan değeri (ör. 0x123456) tespit et.

---
> ◀ [[07.5_MDK4_ile_WPS_Kilidi_DoS|MDK4 ile WPS Kilidi / DoS]] · 📚 [[00_Wi-Fi_Hackleme_İndex|İndeks]] · [[08.1_WEP_ARP_Request_Replay|WEP ARP Request Replay]] ▶
