
# Aircrack-ng'e Giriş (Introduction to Aircrack-ng)

Aircrack-ng, Wi-Fi ağ güvenliğinin değerlendirilmesi için tasarlanmış kapsamlı bir araç setidir (suite). Kablosuz ağları analiz etmeye veya istismar etmeye (exploit) başlamadan önce, bu araçların temel işlevlerini kavramak şarttır.

Aircrack-ng, Wi-Fi güvenliğinin dört farklı temel alanına odaklanır:
*   **İzleme (Monitoring):** Paket yakalama (packet capture) ve elde edilen verilerin üçüncü parti araçlarla (örneğin Wireshark) işlenmek üzere pcap/metin dosyalarına aktarılması.
*   **Saldırı (Attacking):** Paket enjeksiyonu yoluyla tekrar saldırıları (replay attacks), bağlantı koparma (deauthentication), sahte erişim noktaları (fake access points) oluşturma ve ağ trafiği manipülasyonu.
*   **Test Etme (Testing):** Ağ (Wi-Fi) kartlarının ve sürücülerin yeteneklerinin (yakalama ve enjeksiyon yapabilme durumlarının) kontrol edilmesi.
*   **Kırma (Cracking):** WEP ve WPA PSK (WPA 1 ve 2) parolalarının kırılması.

Aircrack-ng içindeki tüm araçlar komut satırı arayüzü (CLI) üzerinden çalışır, bu da otomasyon ve betik (scripting) yetenekleri sağlar. Bu esnek yapı, birçok grafiksel arayüzlü (GUI) sızma testi aracı tarafından altyapı olarak kullanılır. Aircrack-ng ağırlıklı olarak Linux platformlarında çalışır; ancak Windows, macOS, FreeBSD ve çeşitli Unix sistemleriyle de uyumludur.

## Temel Aircrack-ng Araçları
Aircrack-ng seti, Wi-Fi ağlarını denetlemek için 20'den fazla araç içerir. Çeşitli ağ değerlendirmeleri için vazgeçilmez olan ve en sık kullanacağımız **6 temel araç** şunlardır:

| Araç (Tool)       | Açıklama (Description)                                                                                                             |
| :---------------- | :--------------------------------------------------------------------------------------------------------------------------------- |
| **`airmon-ng`**   | Kablosuz ağ kartlarında İzleme Modunu (Monitor Mode) etkinleştirmek ve devre dışı bırakmak için kullanılır.                        |
| **`airodump-ng`** | Havadaki ham (raw) 802.11 çerçevelerini yakalayıp kaydetmeye (sniffing) yarar.                                                     |
| **`airgraph-ng`** | `airodump-ng` tarafından oluşturulan CSV dosyalarını kullanarak, kablosuz ağların görsel grafiklerini ve haritalarını oluşturur.   |
| **`aireplay-ng`** | Havadaki kablosuz ağ trafiğini manipüle etmek ve yapay trafik üretmek için (Paket enjeksiyonu, Deauth vb.) kullanılır.             |
| **`airdecap-ng`** | Yakalanan WEP, WPA PSK veya WPA2 PSK (pcap) trafik dosyalarının şifresini çözerek okunabilir hale getirir.                         |
| **`aircrack-ng`** | Önceden Paylaşılmış Anahtar (PSK) veya PMKID kullanan WEP ve WPA/WPA2 ağlarının parolalarını (hash'lerini) kırmak için kullanılır. |


# Airmon-ng (İzleme Modu Yönetimi)

`airmon-ng`, kablosuz ağ kartlarında İzleme Modunu (Monitor Mode) etkinleştirmek, devre dışı bırakmak veya ağ kartını meşgul edip sorun çıkaran ağ yöneticisi (network manager) süreçlerini sonlandırmak için kullanılır.

İzleme (Monitor) modu, ağ kartının sadece kendisine gelen paketleri değil, menzili içindeki havadaki **tüm Wi-Fi trafiğini** yakalamasını sağlar. Sızma testlerinde (pentest) paket analizi, sorun giderme ve ağ zafiyetlerini tespit etmek için bu özellik vazgeçilmezdir.

## 1. Ağ Kartını İzleme Moduna Alma
Öncelikle sistemdeki ağ kartlarının (interface), sürücülerinin (driver) ve yonga setlerinin (chipset) durumunu görmek için parametresiz komutu çalıştırabiliriz:
```bash
sudo airmon-ng
```

Ağ kartını (`wlan0`) İzleme moduna geçirmek için `start` parametresini kullanırız:
```bash
sudo airmon-ng start wlan0
```
*Not: Bu işlem başarılı olduğunda ağ kartının adı genellikle sonuna 'mon' eki alarak **`wlan0mon`** olarak değişir.*

Ağ kartının başarıyla İzleme moduna geçip geçmediğini doğrulamak için:
```bash
iwconfig
# Çıktıda adın "wlan0mon" ve Mode kısmının "Mode:Monitor" olduğu görülmelidir.
```

## 2. Çakışan Süreçleri Kontrol Etme ve Kapatma (Interfering Processes)
Ağ kartını İzleme moduna alırken arka planda çalışan bazı süreçler (NetworkManager, wpa_supplicant, dhclient vb.) ağın kanalını değiştirebilir veya kartı zorla Yönetilen (Managed) moda geri çekebilir. Bu çakışan süreçleri listelemek için:
```bash
sudo airmon-ng check
```

Eğer sızma testi sırasında paket yakalama araçları (airodump-ng, aireplay-ng vb.) hata veriyor veya duruyorsa, bu sorunlu süreçleri sonlandırmak için şu komut kullanılır:
```bash
sudo airmon-ng check kill
```
*(Uyarı: Bu işlem ana makinenin internet bağlantısını kesebilir, bu nedenle sadece sızma testi aşamasında sorun yaşandığında kullanılmalıdır.)*

## 3. Belirli Bir Kanalda İzleme Modunu Başlatma
Bazen ağ kartımızı tüm kanallarda değil, sadece hedef modemin yayın yaptığı spesifik bir kanalda (örneğin Kanal 11) İzleme moduna almak isteyebiliriz. Bunun için komutun sonuna kanal numarasını eklememiz yeterlidir:
```bash
sudo airmon-ng start wlan0 11
```

## 4. İzleme Modunu Kapatma (Yönetilen Moda Dönüş)
İşimiz bittiğinde, ağ kartını tekrar normal bir son kullanıcı cihazı gibi (Managed Mode) internete bağlanabilir hale getirmek için `stop` parametresi kullanılır:
```bash
sudo airmon-ng stop wlan0mon
```

Eski haline dönüp dönmediğini doğrulamak için tekrar



# Airodump-ng (Paket Yakalama ve Ağ Keşfi)

Airodump-ng, havada uçuşan ham (raw) 802.11 Wi-Fi paketlerini yakalamak için kullanılan temel araçtır. Asıl amacı; daha sonra `aircrack-ng` aracı ile kırmak üzere WEP Başlatma Vektörlerini (IVs) veya WPA/WPA2 el sıkışmalarını (handshake) toplamaktır.

Ayrıca ortamdaki tüm Erişim Noktaları (Access Points - AP) ve bunlara bağlı olan İstemciler (Clients/Stations) hakkında detaylı dosyalar ve tablolar oluşturur.

---

## 1. Airodump-ng Çıktı Tablosunun Anlamı
Airodump-ng çalıştırıldığında terminali ikiye böler. **Üst kısım** ortamdaki ağları (modemleri), **alt kısım** ise bu ağlara bağlı olan veya ağ arayan cihazları (telefon, bilgisayar vb.) gösterir.

| Alan (Field) | Açıklama (Description) |
| :--- | :--- |
| **BSSID** | Erişim noktasının (modemin) MAC adresini gösterir. |
| **PWR** | Ağın veya cihazın sinyal gücünü (Power) gösterir. Sıfıra ne kadar yakınsa (örneğin -30, -80'den daha iyidir) sinyal o kadar güçlüdür. |
| **Beacons** | Ağ tarafından saniyede yaklaşık 10 kez gönderilen "Ben buradayım" (duyuru) paketlerinin sayısıdır. |
| **#Data** | Yakalanan veri paketi sayısıdır. (Şifre kırma için önemlidir). |
| **#/s** | Son 10 saniyede yakalanan saniyelik veri paketi hızıdır. |
| **CH** | Ağın yayın yaptığı Wi-Fi Kanalını (Channel) gösterir. |
| **MB** | Ağın desteklediği maksimum aktarım hızını (Megabit) gösterir. |
| **ENC** | Ağın kullandığı şifreleme türünü gösterir (Örn: WEP, WPA, WPA2, OPN). |
| **CIPHER** | Ağın kullandığı şifreleme algoritmasını gösterir (Örn: TKIP, CCMP). |
| **AUTH** | Kullanılan kimlik doğrulama tipini gösterir (Örn: PSK - Pre-Shared Key). |
| **ESSID** | Ağın görünen adını (Wi-Fi ismi) gösterir. |
| **STATION** | Ağa bağlı olan veya ağ arayan cihazların (istemcilerin) MAC adresini gösterir. |
| **RATE** | İstemci ile erişim noktası arasındaki veri aktarım hızını gösterir. |
| **LOST** | Havadaki parazit vb. yüzünden kaybedilen veri paketi sayısını gösterir. |
| **Packets** | İstemci tarafından gönderilen veri paketi sayısıdır. |
| **Notes** | İstemci hakkında yakalanan EAPOL (Handshake) veya PMKID gibi ekstra bilgileri gösterir. |
| **PROBES** | Cihazın (Station) geçmişte bağlandığı ve şu an havada aradığı ağların isimlerini gösterir. |

---

## 2. Airodump-ng Kullanımı

Airodump-ng'yi kullanabilmek için ağ kartının kesinlikle **İzleme Modunda (Monitor Mode)** olması gerekir (`sudo airmon-ng start wlan0` komutu ile).

### A. Genel Tarama (Channel Hopping)
Ortamdaki tüm kanalları gezerek (Kanal Atlama - Channel Hopping) kapsamlı bir keşif yapmak için sadece İzleme modundaki arayüzün adını belirtmek yeterlidir:
```bash
sudo airodump-ng wlan0mon
```

### B. Belirli Bir Kanalı Tarama (`-c`)
Genel tarama yaparken airodump-ng saniyede birkaç kez kanal değiştirir. Eğer hedef ağ Kanal 11'deyse ve biz o ağın paketlerini (özellikle Handshake) yakalamak istiyorsak, kartımızı hedefle aynı kanala sabitlemeliyiz. Aksi takdirde airodump-ng başka kanalda gezinirken hedef kanaldaki kritik paketleri kaçırabiliriz.

**Tek kanal sabitleme:**
```bash
sudo airodump-ng -c 11 wlan0mon
```
**Çoklu kanal sabitleme:**
```bash
sudo airodump-ng -c 1,6,11 wlan0mon
```

### C. 5 GHz Bantlarını Tarama (`--band`)
Varsayılan olarak airodump-ng sadece 2.4 GHz bandını (b ve g) tarar. Ağ kartınız 5 GHz destekliyorsa, bu frekanstaki ağları görmek için `--band` parametresini kullanmalısınız.
*   **`a`** = 5 GHz
*   **`b`** = 2.4 GHz
*   **`g`** = 2.4 GHz

**Sadece 5 GHz ağları taramak:**
```bash
sudo airodump-ng wlan0mon --band a
```
**Tüm bantları (2.4 GHz ve 5 GHz) aynı aynı anda taramak:**
```bash
sudo airodump-ng wlan0mon --band abg
```

### D. Yakalanan Paketleri Dosyaya Kaydetme (`-w` veya `--write`)
Paketleri daha sonra kırmak (aircrack-ng) veya analiz etmek (Wireshark) için tarama sonuçlarını kaydetmemiz gerekir. `-w` parametresi kullanıldığında Airodump-ng belirtilen ön ek (prefix) ile diskte 5 farklı dosya formatı oluşturur.

```bash
# HTB önekiyle tarama sonuçlarını diske kaydet
sudo airodump-ng wlan0mon -w HTB
```
Bu komut bulunduğunuz dizinde şu dosyaları oluşturur:
1.  `HTB-01.cap` (Yakalanan asıl trafik dosyası - Wireshark ve Aircrack-ng için)
2.  `HTB-01.csv` (Ağların ve cihazların tablo listesi)
3.  `HTB-01.kismet.csv`
4.  `HTB-01.kismet.netxml`
5.  `HTB-01.log.csv`

*(Not: Komutu her durdurup yeniden başlattığınızda dosya numarasını otomatik artırır, örn: HTB-02.cap, HTB-03.cap).*



# Airgraph-ng (Ağ Topolojisi Görselleştirme)

Airgraph-ng, `airodump-ng` tarafından üretilen CSV dosyalarını (örneğin daha önce oluşturduğumuz `HTB-01.csv` dosyasını) kullanarak kablosuz ağların grafiksel temsillerini (haritalarını) oluşturan bir Python betiğidir. Airodump-ng'nin karmaşık metin tabanlı verilerini görselleştirerek ağın yapısını analiz etmeyi çok daha kolay hale getirir.

Airgraph-ng, CSV dosyasını işleyerek iki farklı türde grafik üretir:

## 1. CAPR Grafiği (Clients to AP Relationship / İstemci - AP İlişkisi)
Bu grafik, ağdaki aktif istemciler (bağlı cihazlar) ile Erişim Noktaları (AP) arasındaki mevcut bağlantıları gösterir. Odak noktası istemciler (müşteriler) olduğu için, **kendisine hiçbir cihaz bağlı olmayan boş AP'ler bu grafikte gösterilmez.**

Grafikteki erişim noktaları, kullandıkları şifreleme türüne göre renklerle kodlanır:
*   🟢 **Yeşil:** WPA / WPA2 (Güvenli)
*   🟡 **Sarı:** WEP (Zayıf Şifreleme)
*   🔴 **Kırmızı:** Açık (Open / Şifresiz) Ağlar
*   ⚫ **Siyah:** Bilinmeyen Şifreleme

**Kullanımı (CAPR Grafiği Oluşturma):**
```bash
sudo airgraph-ng -i HTB-01.csv -g CAPR -o HTB_CAPR.png
```

## 2. CPG Grafiği (Common Probe Graph / Ortak Yoklama Grafiği)
Bu grafik, kablosuz istemcilerin havada "aradıkları" (probe request) ağlar ile aralarındaki ilişkiyi görselleştirir. Bir cihaz o an hiçbir ağa bağlı olmasa bile, geçmişte bağlandığı ağları sürekli arar. CPG grafiği, hangi istemcinin havada hangi ağ isimlerini (ESSID) yokladığını göstererek potansiyel hedefleri açığa çıkarır.

**Kullanımı (CPG Grafiği Oluşturma):**
```bash
sudo airgraph-ng -i HTB-01.csv -g CPG -o HTB_CPG.png
```
*(Uyarı: Üretilen PNG görselleri, ağın büyüklüğüne göre çok devasa çözünürlüklerde olabilir.)*

---
> **Pentest Özet Çıkarımı:**
> Airgraph-ng, saldırı zincirini planlamadan önce hedef ağın ve cihazların (düğümlerin) stratejik bir haritasını çıkarmamızı sağlar. Bu görsel analizden sonra, tespit ettiğimiz bu hedeflere **`aireplay-ng`** aracını kullanarak sahte paketler göndermeye ve trafik manipülasyonu yapmaya geçebiliriz.

# Aireplay-ng (Trafik Üretimi ve Deauth Saldırıları)

`aireplay-ng`'nin temel işlevi, daha sonra `aircrack-ng` ile WEP ve WPA-PSK anahtarlarını kırmak için kullanılabilecek **yapay ağ trafiği (traffic generation)** oluşturmaktır. 

WPA el sıkışma (handshake) verilerini yakalamak için istemcileri ağdan düşürme (deauthentication), sahte kimlik doğrulama, ARP isteklerini tekrar oynatma gibi çeşitli saldırı modları içerir.

## 1. Aireplay-ng Saldırı Modları
Terminalde sadece `aireplay-ng` yazarak tüm saldırı modlarını görebiliriz:

| Parametre | Saldırı Numarası | Açıklama |
| :--- | :---: | :--- |
| `--deauth` | **-0** | Deauthentication (İstemciyi ağdan düşürme) |
| `--fakeauth`| **-1** | Sahte kimlik doğrulama (Fake authentication) |
| `--interactive`| **-2** | İnteraktif paket tekrar oynatma |
| `--arpreplay` | **-3** | Standart ARP isteği tekrar oynatma |
| `--chopchop` | **-4** | KoreK chopchop saldırısı (WEP için) |
| `--fragment` | **-5** | Parçalanma (Fragmentation) saldırısı |
| `--caffe-latte`| **-6** | Cafe-latte saldırısı |
| `--cfrag` | **-7** | İstemci odaklı parçalanma saldırısı |
| `--migmode` | **-8** | WPA Migration Mode saldırısı |
| `--test` | **-9** | Paket enjeksiyonu ve kalite testi |

*Not: WPA/WPA2 kırma işlemlerinde en çok **-0 (Deauth)** saldırısı kullanılır.*

---

## 2. Paket Enjeksiyonu Testi (Injection Test)
Deauth paketleri göndermeden önce, kablosuz ağ kartımızın hedef erişim noktasına (AP) başarılı bir şekilde **paket enjekte edip edemediğini** test etmeliyiz.

Öncelikle kartı İzleme (Monitor) moduna alıp hedef ağın bulunduğu kanala (Örn: Kanal 1) sabitliyoruz:
```bash
sudo iw dev wlan0mon set channel 1
```

Ardından test modunu (`-9` veya `--test`) çalıştırıyoruz:
```bash
sudo aireplay-ng --test wlan0mon
```
Eğer her şey yolundaysa, çıktıda **`12:34:56 Injection is working!`** mesajını görmeliyiz. Bu, ağ kartımızın saldırıya hazır olduğunu gösterir.

---

## 3. Deauthentication (Ağdan Düşürme) Saldırısı
Deauth saldırısının amacı, ağa bağlı olan meşru bir cihazın (telefon, PC) bağlantısını zorla kesmektir. Cihaz bağlantısı koptuğu an otomatik olarak ağa yeniden bağlanmaya çalışır. Tam bu yeniden bağlanma anında `airodump-ng` ile **WPA Handshake (4'lü el sıkışma)** verisini yakalarız. Şifreyi kırmak için ihtiyacımız olan şey bu veridir.

**Adım 1:** Hedef AP'nin (BSSID) ve düşürülecek İstemcinin (STATION) MAC adreslerini `airodump-ng` ile tespit edin.

**Adım 2:** Deauth saldırısını başlatın:
```bash
sudo aireplay-ng -0 5 -a 00:14:6C:7A:41:81 -c 00:0F:B5:32:31:31 wlan0mon
```

**Parametrelerin Anlamı:**
*   `-0` : Saldırı tipi (Deauthentication).
*   `5` : Gönderilecek deauth paketi sayısı. (Sürekli göndermek isterseniz `0` yapın).
*   `-a` : Hedef modemin (Access Point) MAC adresi.
*   `-c` : Ağdan düşürülecek hedefin (İstemci/Telefon) MAC adresi. *(Bu parametre yazılmazsa ağdaki HERKES düşürülür - Broadcast Deauth).*
*   `wlan0mon` : Ağ kartımızın arayüz adı.

**Adım 3:** Handshake Yakalama
Saldırı başarılı olduğunda, arka planda çalışan `airodump-ng` ekranının sağ üst köşesinde **`WPA handshake: 00:14:6C:7A:41:81`** ibaresi belirir. Bu mesajı gördüğümüzde şifre kırma işlemi için gerekli veriyi elde etmiş (ve `.cap` dosyasına kaydetmiş) oluruz.

> **Önemli Not:** HTB laboratuvar ortamında istemciler sürekli olarak AP'ye otomatik yeniden bağlandıkları için, bazen Deauth paketi göndermeye bile gerek kalmadan Handshake verisini yakalayabilirsiniz.

# Airdecap-ng (PCAP Şifre Çözme ve Temizleme)

`airdecap-ng`, ağın şifresini (key/passphrase) ele geçirdikten sonra, elimizdeki şifreli ağ trafiği dosyasını (`.cap` / `.pcap`) çözerek okunabilir hale getiren araçtır. 

Şifresi çözülmemiş bir dosyayı Wireshark'ta açarsan sadece 802.11 başlıklarını ve MAC adreslerini görürsün. `airdecap-ng` ile şifreyi çözdükten sonra ise HTTP, TCP, ARP gibi asıl protokolleri ve kaynak/hedef **IP adreslerini** net bir şekilde analiz edebilirsin.

## 1. Temel Parametreler

| Parametre | Açıklama |
| :--- | :--- |
| `-b` | Erişim Noktası (AP) MAC adresi filtresi (BSSID) |
| `-e` | Hedef ağın adı (ESSID) |
| `-p` | WPA/WPA2 parolası (Passphrase) |
| `-w` | WEP anahtarı (Hexadecimal formatta) |
| `-k` | WPA/WPA2 PMK anahtarı (Hexadecimal formatta) |
| `-l` | 802.11 başlığını (header) silme/bırak |

> **Önemli:** İşlem bittiğinde orijinal dosya bozulmaz. Araç, aynı klasöre sonu **`-dec.cap`** ile biten yeni ve şifresi çözülmüş/temizlenmiş bir dosya oluşturur (Örn: `HTB-01-dec.cap`). Analize bu yeni dosyayla devam edilir.

---

## 2. Kullanım Senaryoları (Kopya Kâğıdı)

### Senaryo A: Açık (Şifresiz) Ağda Başlıkları Temizleme
Şifresiz bir ağda paket yakalasan bile 802.11 başlıkları Wireshark'ta kalabalık yapar. Bunları temizleyip sadece asıl veriyi bırakmak için:
```bash
sudo airdecap-ng -b 00:14:6C:7A:41:81 opencapture.cap
```
*(Sadece hedefin MAC adresini (`-b`) ve dosya adını vermek yeterlidir).*

### Senaryo B: WEP Şifreli Trafiği Çözme
WEP şifresini (Hex formatında) bulduktan sonra trafiği açmak için:
```bash
sudo airdecap-ng -w 1234567890ABCDEF HTB-01.cap
```
*(`-w` parametresi ile şifreyi veriyoruz).*

### Senaryo C: WPA/WPA2 Şifreli Trafiği Çözme
WPA parolasını kırmayı başardıktan sonra, içerdeki trafiği okumak için hem ağın adına (`-e`) hem de parolaya (`-p`) ihtiyacımız vardır:
```bash
sudo airdecap-ng -p 'gizlisifre123' -e "Wireless Lab" HTB-01.cap
```
*(İşlem bittikten sonra oluşan `-dec.cap` dosyasını Wireshark ile açarak ağdaki tüm gizli trafiği okuyabilirsiniz).*




# Aircrack-ng (WEP / WPA-WPA2 Şifre Kırma)

`aircrack-ng`, yakalanan paketler (pcap/cap) üzerinden WEP ve WPA/WPA2 ağlarının şifrelerini kırmak için kullanılan **çevrimdışı (offline)** bir saldırı aracıdır. İşlem sırasında ağa veya cihaza doğrudan bağlı olmanıza gerek yoktur.

## 1. Sistem Performans Testi (Benchmark)
Kırma işlemine başlamadan önce CPU'nun saniyede kaç parola deneyebileceğini test etmek için kullanılır:
```bash
aircrack-ng -S
```
*Not: Aircrack-ng işlemciyi (CPU) tam kapasite kullanır. Arka planda ağır işlemler varsa kırma hızı (k/s) düşer.*

---

## 2. WEP Şifresi Kırma
WEP ağlarını kırmak için `airodump-ng` ile yeterli sayıda **IVs (Initialization Vectors)** paketinin yakalanmış olması gerekir. Sadece IVs verilerini kaydettiyseniz, Korek WEP kırma metodunu (`-K`) kullanarak şifreyi çözebilirsiniz.

**Kullanım:**
```bash
aircrack-ng -K HTB.ivs
```
Yeterli veri varsa şifre saniyeler içinde **`KEY FOUND! [ ... ]`** şeklinde ekrana düşer.

---

## 3. WPA / WPA2 Şifresi Kırma
WPA/WPA2 şifrelerini kırmak için iki şeye ihtiyaç vardır:
1. İçinde **4-Way Handshake (4'lü el sıkışma)** verisi bulunan bir `.pcap` veya `.cap` dosyası. *(Tam 4 paket olması şart değildir; EAPOL 2. ve 3. paketler veya 3. ve 4. paketler de yeterlidir).*
2. Şifre denemelerinin yapılacağı bir **Wordlist (Kelime listesi - Sözlük)**.

WPA/WPA2 şifreleri **sadece Sözlük Saldırısı (Dictionary Attack)** ile kırılabilir.

**Kullanım:**
```bash
aircrack-ng HTB.pcap -w /opt/wordlist.txt
```
Komutu çalıştırdıktan sonra dosya içindeki ağların listesi çıkar. Kırmak istediğiniz hedefin **Index numarasını** (örn: `1`) girerek saldırıyı başlatırsınız. Doğru parola wordlist içindeyse **`KEY FOUND! [ parola ]`** şeklinde eşleşmeyi gösterir.