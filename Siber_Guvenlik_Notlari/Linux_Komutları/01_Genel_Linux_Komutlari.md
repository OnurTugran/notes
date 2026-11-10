

# Linux Sistem Yönetimi Komutları
#linux #htb #cheatsheet #terminal

## 📁 Dizin ve Dosya İşlemleri (Hafta 2 & 3)
- `pwd` : Kullanıcının o an bulunduğu mevcut dizini (çalışma alanını) ekrana yazdırır.
- `cd /yol/` : Belirtilen dizine geçiş yapar. (`cd ..` bir üst dizine, `cd ~` ana ev dizinine gider).
- `ls` : Dizin içeriğini listeler. 
  - `ls -l` : Dosyaları ayrıntılı formatta (izinler, sahiplik, boyut, tarih) listeler.
  - `ls -a` : Gizli dosyalar (başında nokta olanlar) dahil tüm öğeleri gösterir.
- `mkdir klasor_adi` : Yeni bir dizin oluşturur. (`mkdir -p` iç içe klasörleri hata almadan oluşturur).
- `touch dosya.txt` : Belirtilen adla yeni ve boş bir dosya oluşturur.
- `cat dosya.txt` : Dosya içeriğini ekrana yazdırır. 
- Birden fazla dosyayı `cat dosya1.txt dosya2.txt > birlesik.txt` komutuyla birleştirebilirsin.
- `echo "metin"` : Belirtilen metni ekrana yazdırır. (`>` ile dosya üzerine yazar, `>>` ile dosyanın sonuna ekleme yapar).
- `cp kaynak hedef` : Dosyaları kopyalar. 
  - `cp -r` : Dizinleri (klasörleri) kopyalamak için kullanılır.
  - `cp -p` : Dosyanın izinlerini, sahipliğini ve zaman damgasını koruyarak kopyalar.
- `mv eski_dosya yeni_yer` : Dosya veya dizin taşımak ya da yeniden adlandırmak için kullanılır.
- `rm dosya.txt` : Dosya siler. 
  - `rm -r` : Dizin ve içindekileri silmek için kullanılır.
  - `rm -f` : Silme işlemini zorla (onay sormadan) yapar.

## 👤 Kullanıcı ve Yetki Yönetimi (Hafta 4)
- `whoami` : Sistemde hangi kullanıcı olarak çalıştığını gösterir.
- `su kullanici_adi` : Başka bir kullanıcı hesabına geçiş (switch user) yapar. (Sadece `su` yazılırsa root kullanıcısına geçmeye çalışır).
- `sudo komut` : Normal kullanıcının tek seferlik "root" (patron) yetkisiyle komut çalıştırmasını sağlar.
- `sudo -i` : Tamamen Root kullanıcısının oturum ortamına geçiş yapar. (Çıkmak için `exit` yazılır).
- `adduser kullanici_adi` : Yeni bir kullanıcı oluşturur (şifre, ev dizini, UID/GID otomatik ayarlanır).
- `deluser kullanici_adi` : Kullanıcıyı sistemden siler ancak dosyaları kalır. (`--remove-home` parametresi ev dizinini de siler).
- `usermod -aG grup_adı kullanici_adi` : Kullanıcıyı mevcut gruplarından çıkarmadan (append) yeni bir gruba ekler.
- `chown sahip:grup dosya` : Bir dosyanın veya dizinin sahibini ve grubunu değiştirir.
- `chmod izinler dosya` : Dosya ve dizinlerin okuma (r=4), yazma (w=2) ve çalıştırma (x=1) izinlerini değiştirir. 
  - *Örnek:* `chmod 755 dosya.txt` (User: rwx, Group: rx, Other: rx).

## 🔍 Metin Arama, Filtreleme ve Düzenleme (Hafta 3)
- `|` (Pipe) : Bir komutun çıktısını kopyalamadan doğrudan diğer bir komuta girdi olarak aktarır.
- **grep** : Dosya içinde belirli kelime veya düzenli ifade arar.
  - `grep -i` : Büyük/küçük harf ayrımını yoksayar.
  - `grep -n` : Eşleşen satırların numarasını gösterir.
  - `grep -c` : Eşleşen satır sayısını sayar.
  - `grep -e 'kelime1' -e 'kelime2'` : Birden fazla farklı kelimeyi aynı anda arar.
- **sed (Stream Editor)** : Metinleri düzenlemek, silmek veya değiştirmek için kullanılır.
  - `sed 's/eskikelime/yenikelime/g' dosya.txt` : Dosyadaki tüm "eskikelime" değerlerini "yenikelime" ile değiştirir.
  - `sed -i` : Yapılan değişikliği sadece ekranda göstermez, doğrudan dosyanın içine kalıcı olarak kaydeder.
  - `sed '3d' dosya.txt` : Dosyanın 3. satırını siler.
- **awk** : Sütunlu veri yapılarında (örneğin CSV) satır satır veri işler.
  - `awk '{print $1}' dosya.txt` : Dosyadaki her satırın 1. sütununu ekrana yazdırır.
  - `awk -F ','` : Virgülle ayrılmış dosyalarda işlem yapmak için alan ayırıcı (Field Separator) belirtir.



# Linux Metin İşleme ve Filtreleme Araçları (Text Processing)

## 1. cut
Belirli bir ayırıcıya (delimiter) göre satırları böler ve sadece istenen alanı (sütunu) ekrana yazdırır.
*   **`-d`** (delimiter): Metni bölecek karakteri belirler.
*   **`-f`** (field): Hangi bölümün (sütunun) gösterileceğini seçer.

```bash
# /etc/passwd dosyasında ":" işaretini ayırıcı olarak kabul et ve 1. alanı (kullanıcı adlarını) getir:
cat /etc/passwd | grep -v "false\|nologin" | cut -d":" -f1
```

## 2. tr (Translate)
Bir karakteri başka bir karakterle değiştirmek veya silmek için kullanılır.

```bash
# /etc/passwd dosyasındaki tüm ":" karakterlerini boşluk (" ") karakteri ile değiştir:
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " "
```

## 3. column
Karmaşık çıktıları okunabilir bir tablo (sütun) formatına dönüştürür.
*   **`-t`** (table): Girdiyi tablo şeklinde hizalar.

```bash
# Sütunları düzgün hizalanmış bir tablo olarak gösterir:
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | column -t
```

## 4. awk
Gelişmiş metin ve veri işleme aracıdır. Özellikle boşluklarla ayrılmış metinlerde belirli sütunları seçmek için çok güçlüdür.
*   **`$1`** : Birinci sütunu temsil eder.
*   **`$NF`** : Satırdaki en son sütunu (Number of Fields) temsil eder.

```bash
# Yalnızca 1. sütunu ve en son sütunu ekrana yazdır:
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}'
```

## 5. sed (Stream Editor)
Metin akışı üzerinde düzenlemeler (bul-değiştir) yapmak için kullanılır. Düzenli ifadeler (Regex) destekler.
*   **`s`** (substitute): Değiştirme komutudur.
*   **`g`** (global): Satırdaki tüm eşleşmeleri değiştirir (sadece ilkini değil).
*   **Sözdizimi:** `sed 's/ESKİ_KELİME/YENİ_KELİME/g'`

```bash
# Çıktıdaki tüm "bin" kelimelerini "HTB" kelimesiyle değiştir:
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}' | sed 's/bin/HTB/g'
```

## 6. wc (Word Count)
Çıktıdaki satır, kelime veya karakter sayısını hesaplar.
*   **`-l`** (lines): Sadece satır sayısını sayar (Bulunan sonuç sayısını hesaplamak için idealdir).

```bash
# Filtrelenen listenin toplam kaç satırdan (sonuçtan) oluştuğunu say:
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1,$NF}' | wc -l
```



# Temel Regex (Düzenli İfadeler) Operatörleri

## 1. `( )` - Gruplama (Grouping)
Regex içindeki belirli kısımları bir arada tutmak (gruplamak) için kullanılır. Tıpkı matematikteki işlem önceliği parantezleri gibidir.
*   **Örnek:** `(jpg|png)` -> Dosya uzantısının jpg veya png olduğu kısmı tek bir grup olarak değerlendirir.

## 2. `[ ]` - Karakter Sınıfları (Character Classes)
Köşeli parantez içindeki karakterlerden **sadece bir tanesiyle** eşleşir. Bir harf veya rakam aralığı belirlemek için çok sık kullanılır.
*   **Örnek:** `[a-z]` -> Sadece küçük harfleri bulur.
*   **Örnek:** `[0-9]` -> Sadece rakamları bulur.

## 3. `{ }` - Miktar Belirleyiciler (Quantifiers)
Kendisinden hemen önce gelen karakterin veya grubun **kaç kez tekrar etmesi** gerektiğini belirler. Süslü parantezler sayı veya aralık alır.
*   **Örnek:** `[0-9]{3}` -> Yan yana tam 3 tane rakam arar (Örn: 123, 987).
*   **Örnek:** `[a-z]{1,10}` -> 1 ile 10 karakter uzunluğunda küçük harflerden oluşan kelimeleri arar.

## 4. `|` - VEYA Operatörü (OR)
Belirtilen seçeneklerden birinin eşleşmesi durumunda sonucu gösterir.
*   **Örnek:** `admin|root` -> Metnin içinde "admin" **VEYA** "root" geçen yerleri bulur.

## 5. `.*` - Her Şey (Joker / AND Mantığı)
En sık kullanılan kalıptır. `.` (nokta) herhangi tek bir karakter anlamına gelir. `*` (yıldız) ise sıfır veya daha fazla tekrar demektir. İkisi birleştiğinde **"arada ne olursa olsun"** anlamına gelir.
*   **Örnek:** `password.*admin` -> Satırda önce "password", sonra arada ne yazarsa yazsın, en son da "admin" geçen satırları bulur. İki kelimenin de aynı satırda, belirtilen sırayla olmasını sağlar.


# Regex Pratikleri (sshd_config Analizi)

## 1. Yorum Satırlarını (#) Gizleme
Konfigürasyon dosyalarındaki (#) ile başlayan yüzlerce satır açıklamayı gizleyip sadece sistemde aktif olan ayarları görmek için kullanılır. Sızma testlerinde en sık başvurulan yöntemlerden biridir.

```bash
# İçinde # işareti geçmeyen satırları göster (-v tersine işlem yapar):
grep -v "#" /etc/ssh/sshd_config
```

## 2. Belirli Bir İfadeyle Başlayan Kelimeleri Bulma
`Permit` kelimesiyle başlayan ayarları bulur (Örn: `PermitRootLogin`, `PermitEmptyPasswords`).

```bash
# \b (boundary) kelime sınırını belirtir. 
grep -E "\bPermit" /etc/ssh/sshd_config
```

## 3. Belirli Bir İfadeyle Biten Kelimeleri Bulma
`Authentication` kelimesiyle biten ayarları bulur (Örn: `PasswordAuthentication`).

```bash
grep -E "Authentication\b" /etc/ssh/sshd_config
```

## 4. İçinde Belirli Bir Kelime (Key) Geçenleri Bulma
Standart arama işlemi.

```bash
grep -E "Key" /etc/ssh/sshd_config
```

## 5. Satır Başı (^) ve Joker Karakter (.*) Birlikte Kullanımı
**Çok Önemli:** Satır "Password" kelimesiyle başlasın (`^`), arada ne olursa olsun (`.*`), satırın bir yerinde "yes" geçsin mantığıdır.

```bash
grep -E "^Password.*yes" /etc/ssh/sshd_config
```

## 6. Satır Sonu ($) Kullanımı
`$` işareti satırın bittiği yeri ifade eder. Sadece en son kelimesi "yes" olan ayarları filtreler.

```bash
grep -E "yes$" /etc/ssh/sshd_config
```


# Linux Paket Yöneticileri ve Araçları

| Komut | Açıklama |
| :--- | :--- |
| **`dpkg`** | Debian paketlerini kurmak, derlemek, kaldırmak ve yönetmek için kullanılan temel araçtır. Daha kullanıcı dostu ana ön yüzü `aptitude` aracıdır. |
| **`apt`** | Paket yönetim sistemi için üst düzey (high-level) komut satırı arayüzü sağlar. En yaygın kullanılan paket yöneticisidir. |
| **`aptitude`** | `apt` aracına bir alternatiftir ve paket yöneticisi için daha gelişmiş, üst düzey bir arayüz sunar. |
| **`snap`** | Snap paketlerini kurar, yapılandırır, günceller ve kaldırır. Bulut, sunucular, masaüstü ve IoT cihazları için güncel uygulamaların güvenli bir şekilde dağıtılmasını sağlar. |
| **`gem`** | Ruby programlama dili için standart paket yöneticisi olan RubyGems'in ön yüzüdür (front-end). |
| **`pip`** | Debian arşivinde bulunmayan Python paketlerini kurmak için önerilen Python paket yükleyicisidir. Kuruluma başlamadan önce tüm gereksinimleri indirerek yarım kalan/hatalı (partial) kurulumları önler. |
| **`git`** | Hızlı, ölçeklenebilir, dağıtık bir versiyon (sürüm) kontrol sistemidir. Hem üst düzey işlemler hem de iç mekanizmalara tam erişim sağlayan zengin bir komut setine sahiptir. |
# Linux Paket Yönetimi ve Araç Kurulumu

Linux'ta programlar Windows'taki gibi `.exe` dosyalarıyla değil, genellikle `.deb` (Debian tabanlı sistemler için) paketleriyle veya kaynak koddan derlenerek (Git ile) kurulur.

## 1. APT (Advanced Package Tool)
Debian tabanlı sistemlerde en çok kullanılan paket yöneticisidir. En büyük avantajı: Bir programı kurarken ihtiyaç duyduğu diğer yan programları (bağımlılıkları / dependencies) **otomatik olarak bulup yüklemesidir**.

Paketlerin indirildiği kaynak (repository) listeleri genellikle `/etc/apt/sources.list` dosyasında tutulur.

```bash
# Çevrimdışı önbellekte (cache) bir aracı arama (Örn: impacket):
apt-cache search impacket

# Bir paket hakkında detaylı bilgi alma (sürüm, bağımlılıklar vb.):
apt-cache show impacket-scripts

# Sistemde kurulu olan tüm paketleri listeleme:
apt list --installed

# Yeni bir paket kurma (-y parametresi sorulan sorulara otomatik "yes" der):
sudo apt install impacket-scripts -y
```

## 2. Git ile Araç İndirme
Siber güvenlik dünyasındaki araçların (scriptler, exploitler) büyük bir kısmı GitHub üzerinde barındırılır. `git clone` komutu, bir projenin tam kopyasını bilgisayarına indirir.

```bash
# Github'daki bir depoyu (repository) kopyalayıp belirtilen klasöre indirme:
mkdir ~/nishang/
git clone [https://github.com/samratashok/nishang.git](https://github.com/samratashok/nishang.git) ~/nishang
```

## 3. DPKG (Debian Package Manager) ve Wget
Eğer kurmak istediğin araç APT depolarında yoksa, internetten `.deb` uzantılı dosyasını manuel olarak indirip `dpkg` ile kurabilirsin. 
**Önemli Fark:** `dpkg` bağımlılıkları *otomatik çözmez*. Eksik bir paket varsa hata verir.

```bash
# wget ile internetten bir .deb dosyasını indirme:
wget [http://archive.ubuntu.com/ubuntu/pool/main/s/strace/strace_4.21-1ubuntu1_amd64.deb](http://archive.ubuntu.com/ubuntu/pool/main/s/strace/strace_4.21-1ubuntu1_amd64.deb)

# İndirilen .deb paketini dpkg ile sisteme kurma (-i = install):
sudo dpkg -i strace_4.21-1ubuntu1_amd64.deb
```


# Linux Süreç Yönetimi (Process Management) Temelleri

## 1. systemd (Init Sistemi)
Linux işletim sistemi başlatıldığında (boot edildiğinde) uyanan ve çalışmaya başlayan **ilk** programdır. Sistemin geri kalan tüm servislerini, ağ bağlantılarını ve arka plan uygulamalarını bu başlatır. Eskiden `init` veya `SysVinit` kullanılırdı, modern Linux dağıtımlarının neredeyse hepsi artık `systemd` kullanmaktadır.

## 2. PID (Process ID - Süreç Kimliği)
Linux'ta çalışan her programa, servise veya komuta sistem tarafından benzersiz bir numara atanır. Buna PID denir (Uygulamaların TC Kimlik Numarası gibi düşünebilirsin). 
* `systemd` sistemde çalışan ilk süreç olduğu için PID numarası her zaman **1**'dir.

## 3. /proc/ Dizini
Çalışan süreçler aslında RAM'de (bellekte) yer tutar. Ancak Linux'ta "her şey bir dosyadır" (everything is a file) mantığı olduğu için, sistem çalışan her sürecin anlık bilgilerini, kullandığı bellek miktarını ve parametrelerini `/proc/` dizini altında canlı olarak tutar. (Örneğin; `/proc/1234/` klasörüne girersen, PID'si 1234 olan uygulamanın tüm sırlarını görebilirsin).

## 4. PPID (Parent Process ID - Üst Süreç Kimliği)
Programlar durduk yere kendi kendine açılmazlar, onları her zaman başka bir program (veya kullanıcı) başlatır. 
* Başlatan sürece **Parent (Ebeveyn)**, başlatılan yeni sürece ise **Child (Çocuk)** denir.
* **PPID**, bir programı hangi uygulamanın başlattığını gösterir. 
* *Siber Güvenlikteki Önemi:* Eğer sistemde zararlı bir yazılım (malware) bulursan, PPID'sine bakarak bu virüsü sisteme kimin soktuğunu veya hangi uygulamanın içinden tetiklendiğini bulabilirsin.

# Linux Görev Zamanlama (Task Scheduling)

Linux'ta belirli görevleri (yedekleme, veritabanı temizliği, güncelleme) otomatik olarak çalıştırmak (bir kahve makinesini sabah otomatik çalışacak şekilde kurmak gibi) için "Görev Zamanlama" kullanılır. Siber güvenlik açısından bu mekanizmalar, yetki yükseltmek (privilege escalation) veya sistemde kalıcılık (persistence) sağlamak amacıyla kötüye kullanılabilir (örn: zararlı bir scripti her gece otomatik çalıştıran bir Cron job).

Bu işlem temelde iki farklı araçla yapılır: **Systemd** ve **Cron**.

## 1. Systemd ile Zamanlama
Systemd ile bir görevi otomatikleştirmek üç aşamadan oluşur:

*   **Adım 1: Timer (Zamanlayıcı) Oluşturma:** Görevin *ne zaman* çalışacağını belirler. `/etc/systemd/system/mytimer.timer` dosyası oluşturulur.
    *   `OnBootSec=3min` (Sistem açıldıktan 3 dakika sonra çalıştır)
    *   `OnUnitActiveSec=1hour` (Her saat başı tekrarla)
*   **Adım 2: Service (Servis) Oluşturma:** Görevin *neyi* çalıştıracağını belirler. `/etc/systemd/system/mytimer.service` dosyası oluşturulup `ExecStart` parametresine scriptin tam yolu (Örn: `/full/path/to/my/script.sh`) yazılır.
*   **Adım 3: Aktifleştirme:** 
    *   `sudo systemctl daemon-reload` (Değişiklikleri sisteme tanıt)
    *   `sudo systemctl start mytimer.timer` (Timer'ı başlat)
    *   `sudo systemctl enable mytimer.timer` (Kalıcı hale getir)

## 2. Cron (Crontab) ile Zamanlama
Cron, görevleri zamanlamak için kullanılan daha eski ve yaygın bir araçtır. Systemd'den farklı olarak, görevler `crontab` adı verilen tek bir dosyaya satır satır yazılır.

Bir Crontab satırı **5 zaman parametresi** ve **1 komut/script yolundan** oluşur:
1.  **Dakika** (0-59)
2.  **Saat** (0-23)
3.  **Ayın Günü** (1-31)
4.  **Ay** (1-12)
5.  **Haftanın Günü** (0-7) *(Not: 0 ve 7 Pazar gününü temsil eder)*

**Crontab Örnekleri:**
*   `0 */6 * * * /path/update.sh` -> Her 6 saatte bir çalıştırır.
*   `0 0 1 * * /path/script.sh` -> Her ayın 1'inde gece yarısı (00:00) çalıştırır.
*   `0 0 * * 0 /path/backup.sh` -> Her Pazar günü gece yarısı (00:00) çalıştırır.

## Systemd vs. Cron (Özet Fark)
*   **Systemd:** İşletim sistemine görevlerin ne zaman çalıştırılacağını bildirmek için iki ayrı dosya ("timer" ve "service" betikleri) gerektirir.
*   **Cron:** Görevlerin ne zaman çalıştırılacağını cron daemon'ına (arka plan servisine) bildirmek için yapılandırılmış tek bir "crontab" dosyası kullanır.

# NFS (Network File System)

NFS, uzak sistemlerdeki (sunuculardaki) dosyaları sanki kendi yerel bilgisayarımızda (lokalimizde) depolanıyormuş gibi kullanmamızı ve yönetmemizi sağlayan bir ağ protokolüdür.

## Temel Özellikleri ve Kullanım Amaçları
*   **Merkezi Yönetim ve İşbirliği:** Sistem yöneticileri, dosyaları tek bir merkezde (sunucuda) toplamak için NFS kullanır. Böylece hem Linux hem de Windows kullanıcıları aynı verilere ağ üzerinden eşzamanlı olarak erişip ortak çalışabilir.
*   **Yetki ve Güvenlik:** Kullanıcılara özel erişim kontrolleri (okuma/yazma izinleri) atanabilir.
*   **Gerçek Zamanlı Aktarım:** Sunucular arası dosya sistemlerini kopyalamak (replikasyon) ve verileri anlık aktarmak için çok verimlidir.
*   **Yaygın NFS Sunucuları:** `nfs-utils` (Ubuntu), `NFS-Ganesha` (Solaris) ve `OpenNFS` (Redhat Linux) en çok bilinen sunucu yazılımlarıdır.

## Siber Güvenlik ve Sızma Testindeki Önemi
Eğer sızma testi yaptığın hedef sistemde dosya transferi yapmak için **FTP istemcisi (client) yoksa**, ancak sistemde NFS servisi çalışıyorsa, NFS'i tıpkı FTP gibi kullanabilirsin. Hedef makinedeki paylaşıma açık bir NFS klasörünü kendi makinenize bağlayarak (mount ederek) hedefe zararlı dosya yükleyebilir (upload) veya sistemden hassas verileri çekebilirsin (exfiltration).


# Web Servisleri ve Terminal Araçları

## 1. Apache Web Sunucusu
Apache, dünyadaki en popüler web sunucularından biridir. Bir evin iskeleti gibidir; farklı odalar (modüller) ekleyerek özelleştirebilirsin. Örneğin, `mod_ssl` modülünü ekleyerek trafiği şifreleyebilir veya PHP/Python gibi dillerle dinamik web siteleri çalıştırabilirsin.

```bash
# Apache kurulumu:
sudo apt install apache2 -y

# Apache servisini başlatma:
sudo systemctl start apache2
```

### Port (Kapı) Değiştirme
Web sunucuları varsayılan olarak **Port 80** üzerinden yayın yapar. Eğer Pwnbox gibi sistemlerde Port 80 zaten başka bir uygulama tarafından kullanılıyorsa, Apache hata verir. Bunu çözmek için dinlediği portu değiştirebilirsin:
1. `/etc/apache2/ports.conf` dosyasını `nano` veya `vim` ile aç.
2. `Listen 80` satırını bulup `Listen 8080` olarak değiştir ve servisi yeniden başlat.

---

## 2. Terminalden Web'e Bağlanma: cURL vs Wget
Sızma testlerinde hedefteki dosyaları indirmek veya hedefe kendi dosyalarımızı yüklemek için tarayıcı yerine bu iki komut satırı aracını kullanırız. Aralarındaki fark çok önemlidir:

### cURL (Ekrana Basar)
Gittiği web sayfasının veya dosyanın tüm kaynak kodunu (HTML) bilgisayarına kaydetmeden **doğrudan terminal ekranına** (STDOUT) basar. HTTP başlıklarını (headers) incelemek ve sayfanın arka planında ne döndüğünü hızlıca analiz etmek için mükemmeldir.

```bash
curl http://localhost:8080
```

### Wget (Diske Kaydeder)
Hedefteki dosyayı veya web sayfasını alır ve **doğrudan bilgisayarına dosya olarak kaydeder**. Terminalde bir indirme yöneticisi (download manager) gibi çalışır. Hedef sunucuya sızdığında dışarıdan kendi zararlı yazılımını hedefe indirtmek için en çok kullanılan araçtır.

```bash
wget http://localhost:8080
```

---

## 3. Python 3 Web Sunucusu (Hızlı Çözüm)
Apache kurmak, ayarlamak ve yapılandırmak zaman alır. Eğer sızma testi sırasında kendi bilgisayarındaki bir klasörü (içindeki zararlı dosyalar veya scriptlerle birlikte) sadece birkaç dakikalığına hedefin erişimine açmak istiyorsan Python kullanırsın.

```bash
# Bulunduğun klasörü anında web üzerinden yayına açar (Varsayılan Port: 8000)
python3 -m http.server
```

**Neden Çok Kullanılır?** 
Bu komutu çalıştırdığında terminal açık kalır. Hedef makine (veya başka biri) senin dosyalarına tıkladığında veya indirdiğinde, terminal ekranında anında canlı log kayıtları düşer. (Örn: `127.0.0.1 - "GET /script.sh HTTP/1.1" 200`). Böylece hedefin dosyayı başarıyla çekip çekmediğini canlı izleyebilirsin.



# Linux Yedekleme (Backup) ve Geri Yükleme (Restore)

Linux'ta (özellikle Ubuntu) verileri yedeklemek ve korumak için üç temel araç öne çıkar:
1. **Rsync:** Sadece değişen veya yeni eklenen dosyaları aktardığı için inanılmaz hızlı ve verimlidir. Ağ üzerinden dosya kopyalamak için endüstri standardıdır.
2. **Duplicity:** Rsync'in üzerine inşa edilmiştir ancak verileri şifreleme (encryption) özelliği ekler. Verileri buluta (örn: Amazon S3) atarken güvenliği sağlar.
3. **Deja Dup:** Komut satırı sevmeyenler için Rsync ve Duplicity'i arka planda kullanan grafik arayüzlü (GUI) basit bir araçtır.

## 1. Rsync Kurulumu ve Temel Kullanımı

```bash
# Rsync'i kurma:
sudo apt install rsync -y

# Yerel klasörü uzak bir sunucuya yedekleme (-a: dosya izinlerini korur, -v: detayı ekrana basar):
rsync -av /path/to/mydirectory user@backup_server:/path/to/backup/directory

# Gelişmiş Yedekleme (-z: sıkıştırır, --delete: kaynakta silineni hedefte de siler):
rsync -avz --backup --backup-dir=/path/to/backup/folder --delete /path/to/mydirectory user@backup_server:/path/to/backup/directory

# Yedekten Geri Yükleme (Restore - Kaynak ve hedefin yerini değiştiriyoruz):
rsync -av user@backup_server:/path/to/backup/directory /path/to/mydirectory
```

## 2. SSH ile Güvenli Veri Transferi (Encrypted Rsync)
Ağ üzerinden giden verinin (özellikle sızma testlerinde hassas verilerin) araya girenler tarafından okunmasını engellemek için rsync'e trafiği **SSH tüneli** içinden geçirmesini söyleyebiliriz.

```bash
# -e ssh parametresi ile transferi şifreleme:
rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

## 3. Otomatik Senkronizasyon (Rsync + Cron)
Yedekleme işlemini her saat başı otomatik yapmak için `cron` ve `rsync` birlikte kullanılır. Ancak işlemin şifre sorulmadan otomatik yapılabilmesi için SSH anahtarı ayarlanmalıdır.

**Adım 1: Parolasız SSH için Anahtar (Key) Üretimi**

```bash
# 2048 bit RSA anahtarı oluştur (soruları Enter ile geç):
ssh-keygen -t rsa -b 2048

# Açık anahtarı (Public Key) yedek sunucusuna kopyala:
ssh-copy-id user@backup_server
```

**Adım 2: Yedekleme Betiği (Script) Oluşturma**
`RSYNC_Backup.sh` adında bir dosya oluşturup içine şu komutu yazarız:

```bash
#!/bin/bash
rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

**Adım 3: Çalıştırma İzni ve Cron Ayarı**

```bash
# Betiğe çalıştırma yetkisi (execute) ver:
chmod +x RSYNC_Backup.sh

# Zamanlanmış görevleri (crontab) düzenle:
crontab -e
```

Crontab dosyasının en altına şu satırı ekleyerek her saat başı (0. dakika) çalışmasını sağlarız:
`0 * * * * /path/to/RSYNC_Backup.sh`



# Linux Dosya Sistemi ve Disk Yönetimi

## 1. Dosya Sistemleri (File Systems)
Linux, diski formatlarken kullanabileceğin farklı dosya sistemlerini destekler. Her birinin kullanım amacı farklıdır:
*   **ext2:** Eski bir sistemdir, çökme anında kurtarma (journaling) özelliği yoktur. Sadece USB bellekler gibi basit işlerde kullanılır.
*   **ext3 / ext4:** Modern Linux sistemlerinin varsayılan (default) ve en dengeli dosya sistemidir. Çökmelere karşı veri kurtarma (journaling) özelliğine sahiptir.
*   **Btrfs:** Anlık görüntü alma (snapshot) ve veri bütünlüğü kontrolleri gibi gelişmiş özellikleri vardır.
*   **XFS:** Büyük dosyaları işlemek ve yüksek performans gerektiren (yüksek I/O) sunucular için idealdir.
*   **NTFS:** Aslen Windows'a aittir. Çift işletim sistemli (Dual-boot) veya Windows ile ortak kullanılacak diskler için uyumluluk sağlar.

## 2. Inode (İndeks Düğümü) Kavramı
Linux'ta dosyalar isimleriyle değil, **Inode** adı verilen kimliklerle tutulur. 
*   **Kütüphane Analojisi:** Inode tablosunu bir kütüphane kataloğu gibi düşünebilirsin. Katalog kartında (Inode) kitabın yazarı, sayfa sayısı ve nerede durduğu yazar ama kitabın içeriği veya kapağındaki tam adı yazmaz. 
*   Inode'lar dosyanın **metadatasını** (izinler, sahiplik, boyut, diskteki fiziksel konumu) tutar. Bazen diskte yer olsa bile Inode limiti dolduğu için yeni dosya oluşturulamayan durumlar yaşanabilir.

## 3. Dosya Türleri
*   **Düzenli Dosyalar (Regular Files):** Metin (ASCII) veya binary (görsel, çalıştırılabilir program) verileridir.
*   **Dizinler (Directories):** İçinde diğer dosyaları ve klasörleri tutan "konteyner" dosyalardır. 
*   **Sembolik Linkler (Symbolic Links / Symlinks):** Windows'taki "Kısayol" mantığıdır. Dosyayı kopyalamadan başka bir konumdan erişmeyi sağlar.

## 4. Disk Yönetimi ve Bağlama (Mounting)
Linux'ta diski bilgisayara takmak yetmez; diski işletim sistemindeki bir klasörle eşleştirmen (bağlaman) gerekir. Bu işleme **Mounting** denir.

*   **`fdisk -l`**: Sistemdeki diskleri ve bölümlerini (partition) listeler.
*   **`mount`**: Bir diski manuel olarak bir klasöre bağlar (Örn: `sudo mount /dev/sdb1 /mnt/usb`).
*   **`/etc/fstab` Dosyası:** Bilgisayar her yeniden başladığında disklerin otomatik olarak bağlanmasını sağlayan ayar dosyasıdır. Sızma testlerinde yanlış yapılandırılmış bir fstab dosyası yetki yükseltme aracı olabilir.
*   **`umount`**: Diski güvenle çıkartır (Örn: `sudo umount /mnt/usb`). 
    *   *Not:* Eğer bir dosya o an çalışıyorsa disk çıkartılamaz. Açık dosyaları ve kullanan süreçleri görmek için `lsof` komutu kullanılır.

## 5. SWAP (Takas Alanı)
Fiziksel RAM dolduğunda sistemin çökmemesi için sabit diskte (HDD/SSD) ayrılan sanal bellek alanıdır.
*   Sistem RAM'de o an kullanılmayan verileri SWAP alanına atarak yer açar. 
*   **Hazırda Beklet (Hibernation):** Bilgisayar uykuya daldığında RAM'deki tüm açık programlar SWAP alanına yazılır, bilgisayar açıldığında buradan geri okunarak kalınan yerden devam edilir.
*   İlgili komutlar: Alan oluşturmak için `mkswap`, aktif etmek için `swapon` kullanılır.


# Konteynerleştirme (Containerization) - Docker ve LXC

## 1. Konteyner Nedir?
Konteynerler, bir uygulamanın çalışması için gereken her şeyi (kod, kütüphaneler, ayarlar) tek bir pakete koyup, o uygulamanın her bilgisayarda aynı şekilde (izole bir şekilde) çalışmasını sağlayan yapılardır.
* **Sanal Makineden (VM) Farkı:** Sanal makineler (VMware, VirtualBox) işletim sisteminin çekirdeğini (kernel) baştan kurar ve çok ağırdır. Konteynerler ise host (ana) makinenin çekirdeğini paylaşır. Çok hızlı açılırlar ve çok az kaynak tüketirler.
* **Siber Güvenlikteki Yeri:** Zafiyetli bir sistemi simüle etmek, malware analiz etmek veya hedefte yetki yükseltme (privilege escalation - özellikle Docker ve LXC yanlış yapılandırılmışsa) denemek için kullanılır.

---

## 2. DOCKER
Docker, uygulamaları "imajlar" (images) halinde paketleyip dağıtmayı sağlayan en popüler konteyner platformudur.

**Docker Mimarisi:**
* **Dockerfile:** İmajın nasıl oluşturulacağını anlatan tarif (script) dosyasıdır.
* **Image (İmaj):** Dockerfile'dan oluşturulan, salt okunur (read-only) kalıplardır.
* **Container (Konteyner):** İmajın çalıştırılmış, canlı halidir.

### Temel Docker Komutları
```bash
# Dockerfile'ın bulunduğu klasörde imaj oluşturma (tag name: FS_docker):
docker build -t FS_docker .

# İmajı çalıştırarak konteyner oluşturma (Host'un 8080 portunu konteynerin 80 portuna bağlar. -d arka planda çalıştırır):
docker run -p 8080:80 -p 8022:22 -d FS_docker

# Çalışan konteynerleri listeleme:
docker ps

# Konteyneri durdurma ve silme:
docker stop <container_id>
docker rm <container_id>
```

---

## 3. LXC (Linux Containers)
LXC, Docker'dan farklı olarak tek bir uygulamaya değil, **tüm işletim sistemine odaklanan** daha geleneksel bir konteyner sistemidir. Hafif bir sanal makine gibi davranır. Docker'dan daha az taşınabilir ancak sistem yöneticilerine daha fazla derinlik sunar.

### Temel LXC Komutları
```bash
# LXC Kurulumu:
sudo apt install lxc -y

# Yeni bir LXC Konteyneri yaratma (Adı: linuxcontainer, Şablonu: ubuntu):
sudo lxc-create -n linuxcontainer -t ubuntu

# Konteyneri başlatma ve durdurma:
sudo lxc-start -n linuxcontainer
sudo lxc-stop -n linuxcontainer

# Çalışan konteynerin içine (terminaline) bağlanma:
sudo lxc-attach -n linuxcontainer

# Konteynerlerin durumunu ve IP adreslerini listeleme:
sudo lxc-ls -f
sudo lxc-info -n linuxcontainer
```

### LXC Güvenliği ve Kaynak Sınırlandırma (Cgroups)
LXC konteynerlerinin işlemci (CPU) ve RAM (Memory) kullanımını sınırlandırmak için `cgroups` kullanılır. Bu ayarlar konteynerin kendi konfigürasyon dosyasında (örn: `/var/lib/lxc/linuxcontainer/config`) yapılır.

```text
# Konfigürasyon dosyasının içine eklenen limitasyon örnekleri:
lxc.cgroup.cpu.shares = 512            # CPU kullanım hakkını yarıya düşürür.
lxc.cgroup.memory.limit_in_bytes = 512M  # RAM kullanımını maksimum 512 MB yapar.
```

Canlı çalışırken geçici olarak değiştirmek için:
```bash
sudo lxc-cgroup -n linuxcontainer memory.limit_in_bytes 512M
```



# Ağ Yapılandırması ve Güvenliği (Network Configuration & Security)

Sızma testlerinde (penetration testing) Linux ağ ayarlarını yapılandırmak, ağ trafiğini yönlendirmek ve güvenliği sağlamak kritik bir beceridir. Bu bölüm ağ arayüzlerini, erişim modellerini, sorun giderme araçlarını ve sistemi sıkılaştırma (hardening) yöntemlerini kapsar.

## 1. Ağ Arayüzlerini Yapılandırma
Linux'ta ağ kartlarını ve bağlantıları yönetmek için geleneksel olarak `ifconfig`, güncel sistemlerde ise `ip` komutu kullanılır.

```bash
# Ağ arayüzlerini ve mevcut IP adreslerini listeleme:
ifconfig    # veya: ip addr

# Bir ağ arayüzünü (Örn: eth0) aktif (up) veya pasif (down) duruma getirme:
sudo ifconfig eth0 up
sudo ip link set eth0 up

# Arayüze geçici IP adresi ve Alt Ağ Maskesi (Netmask) atama:
sudo ifconfig eth0 192.168.1.2
sudo ifconfig eth0 netmask 255.255.255.0

# Varsayılan Ağ Geçidi (Default Gateway) ayarlama:
sudo route add default gw 192.168.1.1 eth0
```

### DNS ve Kalıcı Ayarlar
Alan adlarını (Örn: google.com) IP adreslerine çeviren DNS sunucuları `/etc/resolv.conf` dosyasından ayarlanır.
```bash
# Geçici DNS ayarı (Yeniden başlatılınca silinebilir):
sudo nano /etc/resolv.conf
# İçine eklenecekler: nameserver 8.8.8.8
```

Ayarların kalıcı olması için `/etc/network/interfaces` dosyası düzenlenmeli ve ağ servisi yeniden başlatılmalıdır:
```bash
# Servisi yeniden başlatarak kalıcı ayarları uygulama:
sudo systemctl restart networking
```

---

## 2. Ağ Erişim Kontrolü (NAC - Network Access Control)
Ağa ve sistem kaynaklarına kimin erişebileceğini belirleyen üç temel güvenlik modeli vardır:
* **DAC (Discretionary Access Control - İsteğe Bağlı):** Bir dosyanın veya kaynağın sahibi, o kaynağa kimin erişeceğine kendisi karar verir.
* **MAC (Mandatory Access Control - Zorunlu):** Erişim izinleri kullanıcıya değil, işletim sisteminin katı kurallarına bağlıdır. Daha güvenlidir ancak esnek değildir (Örn: SELinux, AppArmor).
* **RBAC (Role-Based Access Control - Rol Tabanlı):** İzinler kişilere değil, organizasyondaki "görev ve rollere" göre atanır. Yönetimi en kolay olanıdır.

---

## 3. Sorun Giderme ve Ağ İzleme Araçları
Ağdaki kopmaları, yavaşlamaları veya sızma testi sırasındaki bağlantı sorunlarını teşhis etmek için kullanılırlar:

```bash
# 1. PING: Hedef makinenin ayakta olup olmadığını ICMP paketleriyle test eder.
ping 8.8.8.8

# 2. TRACEROUTE: Hedefe giden yoldaki tüm durakları (yönlendiricileri/hop) listeler.
traceroute [www.inlanefreight.com](https://www.inlanefreight.com)

# 3. NETSTAT: Sistemdeki açık portları ve aktif bağlantıları gösterir.
netstat -a
```
*Sızma Testi Notu:* `netstat -a` komutu, sistemde gereksiz yere açık kalmış ve zafiyet barındırabilecek servisleri (SSH, FTP, VNC vb.) bulmak için harikadır.

---

## 4. Sistem Sıkılaştırma (Hardening)
Sistemi yetkisiz erişimlere ve sömürülere karşı korumak için üç temel mekanizma kullanılır:

1. **SELinux (Security-Enhanced Linux):** Linux çekirdeğine (kernel) entegre edilmiş çok katı bir **MAC** sistemidir. İzinleri son derece detaylı yönetir ancak yapılandırması oldukça karmaşıktır.
2. **AppArmor:** SELinux gibi bir **MAC** sistemidir ancak uygulamalara atanan "profiller" üzerinden çalışır. SELinux'a göre kullanımı ve yapılandırması çok daha kolaydır.
3. **TCP Wrappers:** Gelen bağlantıları IP adresine göre engelleyen (deny) veya izin veren (allow) host-tabanlı basit bir araçtır. Ağ seviyesinde temel bir koruma sağlar.


# Linux Uzak Masaüstü ve Görüntü Protokolleri (Remote Desktop Protocols)

Sızma testlerinde hedef sistemde grafiksel bir arayüze (GUI) erişmek veya sistemi uzaktan yönetmek için kullanılan temel protokoller şunlardır:

## 1. RDP vs VNC (Temel Fark)
* **RDP (Remote Desktop Protocol):** Genellikle Windows ortamları için tasarlanmıştır. Yöneticilerin Windows masaüstlerine sanki bilgisayarın başındaymış gibi bağlanmasını sağlar.
* **VNC (Virtual Network Computing):** Çapraz platformdur (cross-platform) ancak çoğunlukla Linux sistemlerde grafiksel erişim sağlamak için tercih edilir. RFB (Remote Frame Buffer) protokolünü kullanır.

## 2. X11 / XServer
Unix/Linux sistemlerde grafiksel kullanıcı arayüzünün (GUI) işletim sistemiyle iletişim kurmasını sağlayan protokoldür. En büyük avantajı "ağ şeffaflığı"na (network transparency) sahip olmasıdır; yani uzak bir sunucuda çalışan uygulamanın grafikleri, hedef sunucuyu yormadan doğrudan kendi (lokal) bilgisayarınızda işlenebilir (render edilebilir).

* **Portlar:** Genellikle `TCP/6000 - 6009` aralığını kullanır (İlk ekran olan `:0` için `6000` portu açılır).
* **Güvenlik Zafiyeti:** Varsayılan olarak **şifresiz (unencrypted)** veri iletimi yapar. Ağdaki bir saldırgan `xwd` (X pencerelerinin ekran görüntüsünü alan bir araç) gibi standart araçlar kullanarak ağ dinlemesi (sniffing) yapabilir ve ekrandaki şifreleri veya hassas verileri çalabilir. Ayrıca zayıf oturum anahtarları nedeniyle kod çalıştırmaya izin veren tarihi zafiyetleri (CVE-2017-2624 vb.) mevcuttur.
* **Güvenli Kullanım (X11 Forwarding):** X11'i güvenle kullanmak için trafik SSH tüneli içinden geçirilmelidir. Hedef sunucunun `/etc/ssh/sshd_config` dosyasında `X11Forwarding yes` ayarının açık olması gerekir.

```bash
# İstemci makineden (kendi bilgisayarından) X11 yönlendirmesi ile güvenli bir şekilde Firefox'u başlatma:
ssh -X htb-student@10.129.23.11 /usr/bin/firefox
```

## 3. XDMCP (X Display Manager Control Protocol)
Uzak X Window oturumlarını yönetmek için kullanılır ve tüm grafiksel arayüzü (KDE, Gnome vb.) uzak istemciye yönlendirir.
* **Port:** `UDP/177`
* **Güvenlik:** Son derece güvensizdir. Ortadaki Adam (MITM - Man-in-the-Middle) saldırılarına çok açıktır. Saldırgan trafiği kesip kendi komutlarını çalıştırabileceği için yüksek güvenlik gerektiren ortamlarda kesinlikle kullanılmamalıdır.

## 4. VNC Kurulumu ve Kullanımı
VNC varsayılan olarak `TCP/5900` portunu kullanır (Ekran 0). Eklenen her yeni sanal ekran için port artar (Ekran 1 için `5901`, Ekran 2 için `5902` vb.). Güvenlik ve şifreleme özellikleri nedeniyle `UltraVNC` ve `RealVNC` popülerdir. GNOME masaüstü ortamı VNC ile kararsız çalışabildiği için genellikle **XFCE4** masaüstü yöneticisi ile birlikte **TigerVNC** tercih edilir.

### TigerVNC Kurulum ve Yapılandırması

```bash
# VNC Sunucusu ve XFCE4 masaüstü paketlerinin kurulumu:
sudo apt install xfce4 xfce4-goodies tigervnc-standalone-server -y

# VNC bağlantısı için parola oluşturma (Salt okunur -view only- parolasına 'n' diyebilirsiniz):
vncpasswd
```

```bash
# VNC başlatma yapılandırma dosyalarını (xstartup ve config) oluşturma:
touch ~/.vnc/xstartup ~/.vnc/config

# xstartup dosyasını VNC bağlandığında XFCE4 masaüstünü başlatacak şekilde yapılandırma:
cat <<EOT>> ~/.vnc/xstartup
#!/bin/bash
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
/usr/bin/startxfce4
[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r \$HOME/.Xresources ] && xrdb \$HOME/.Xresources
x-window-manager &
EOT

# Çözünürlük ayarlarını (geometry) config dosyasına yazma:
cat <<EOT>> ~/.vnc/config
geometry=1920x1080
dpi=96
EOT

# Başlatma (xstartup) betiğine çalıştırma (execute) izni verme:
chmod +x ~/.vnc/xstartup

# VNC Sunucusunu Başlatma:
vncserver

# Çalışan VNC oturumlarını (ve kullandıkları RFB portlarını) listeleme:
vncserver -list
```

### VNC Bağlantısını SSH ile Şifreleme (SSH Tunneling)
VNC trafiğini güvenli hale getirmek için port doğrudan dışarı açılmaz. Bunun yerine **SSH tüneli** (SSH Tunneling) kurularak VNC trafiği güvenli SSH kanalının içinden akıtılır.

```bash
# Adım 1: Kendi 5901 portumuzu, hedef makinenin (10.129.14.130) lokalindeki 5901 portuna bağlayarak tünel açma:
ssh -L 5901:127.0.0.1:5901 -N -f -l htb-student 10.129.14.130

# Adım 2: VNC istemcisi (xtightvncviewer) ile doğrudan kendi "localhost" adresimize bağlanarak tüneli kullanma:
xtightvncviewer localhost:5901
```


# Linux Güvenlik Duvarı (Firewall) ve Iptables

Linux sistemlerinde ağ trafiğini izlemek, filtrelemek ve yetkisiz erişimleri engellemek için güvenlik duvarları (firewalls) kullanılır. Geçmişte `ipchains` kullanılırken, günümüzde standart olarak **`iptables`** (veya onun daha modern, basitleştirilmiş versiyonları olan `nftables`, `ufw`, `firewalld`) yaygındır.

Sızma testlerinde (penetration testing) hedef sistemdeki trafiği yönlendirmek, kendi arka kapı (backdoor) portlarımıza izin vermek veya mevcut engelleri atlatmak için `iptables` mimarisini bilmek zorunludur.

## 1. Iptables Mimarisi (Nasıl Çalışır?)
`iptables` trafiği yönetmek için kuralları belirli kategorilere ayırır.

### A. Tablolar (Tables) ve Zincirler (Chains)
Tablolar kuralların amacını, zincirler ise trafiğin yönünü belirler.
*   **Filter Tablosu (Varsayılan):** Trafiği engellemek veya izin vermek için kullanılan ana tablodur.
    *   `INPUT`: Sisteme *gelen* trafik.
    *   `OUTPUT`: Sistemden *çıkan* trafik.
    *   `FORWARD`: Sistem üzerinden *yönlendirilen* (routing) trafik.
*   **NAT Tablosu:** Ağ adresi çevirisi (IP/Port yönlendirme vb.) için kullanılır (`PREROUTING`, `POSTROUTING`).
*   **Mangle Tablosu:** Paket başlıklarını (header) değiştirmek için kullanılır.

### B. Hedefler (Targets / Aksiyonlar)
Kural eşleştiğinde pakete ne olacağını belirler (`-j` parametresi ile uygulanır):
*   `ACCEPT`: Paketin geçişine izin ver.
*   `DROP`: Paketi sessizce düşür (Karşı tarafa hiçbir şey söyleme). Sızma testlerinde Nmap gibi port tarayıcılarını yavaşlatmak veya atlatmak için idealdir.
*   `REJECT`: Paketi engelle ancak karşı tarafa "engellendiğine" dair hata mesajı gönder.
*   `LOG`: Paketi sistem loglarına kaydet.

---

## 2. Temel Iptables Komutları (Cheat Sheet)

Aşağıdaki komutlar sızma testleri veya sunucu sıkılaştırma (hardening) sırasında en çok ihtiyaç duyacağın temel kural yapılarıdır:

```bash
# Sistemdeki mevcut bütün güvenlik duvarı kurallarını listeleme:
sudo iptables -L

# TCP 22 (SSH) portuna dışarıdan gelen bağlantılara İZİN VERME (Accept):
# (-A: Append/Ekle, -p: Protocol, --dport: Destination Port)
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# TCP 8080 portuna gelen trafiği ENGELLEME (Drop):
sudo iptables -A INPUT -p tcp --dport 8080 -j DROP

# Belirli bir IP adresinden (Örn: 192.168.1.50) gelen TÜM trafiği engelleme:
# (-s: Source/Kaynak IP)
sudo iptables -A INPUT -s 192.168.1.50 -j DROP

# Yalnızca belirli bir IP adresinden gelen trafiğe İZİN VERME:
sudo iptables -A INPUT -s 192.168.1.100 -j ACCEPT

# Sadece ICMP (Ping) protokolünü engelleme (Sunucuyu ping taramalarından gizler):
sudo iptables -A INPUT -p icmp -j DROP

# Yeni bir özel zincir (Chain) oluşturma:
sudo iptables -N KENDI_ZINCIRIM

# Mevcut bir kuralı silme (Örn: INPUT zincirindeki 1 numaralı kuralı silmek için):
sudo iptables -D INPUT 1
```


# Sistem Logları (System Logs) ve Güvenlik Analizi

Linux sistem logları, arka planda gerçekleşen her türlü sistem, ağ, uygulama ve kullanıcı hareketini kaydeder. Bir sızma testi uzmanı (veya sistem yöneticisi) için bu loglar; yetkisiz girişleri (brute-force saldırıları vb.), zafiyetli servisleri, düz metin (clear-text) unutulan parolaları ve sistemdeki anormal aktiviteleri tespit etmenin anahtarıdır.

Siber güvenlik perspektifinden incelemeniz gereken en kritik log dosyaları ve konumları şunlardır:

## 1. Kritik Log Dosyaları ve Konumları

*   **Kimlik Doğrulama Logları (`/var/log/auth.log`)**
    *   **Önemi:** Sızma testlerinde ilk bakılacak dosyalardan biridir. Başarılı ve başarısız tüm kullanıcı girişleri (SSH vb.), `sudo` (yetki yükseltme) komutu kullanımları ve SSH anahtar (public key) kabulleri burada tutulur.
    *   *(Not: CentOS / RHEL tabanlı sistemlerde bu dosya `/var/log/secure` olarak geçer).*
*   **Sistem Logları (`/var/log/syslog`)**
    *   **Önemi:** Sistemin genel günlüğüdür. Hangi servislerin başlatılıp durdurulduğunu, sistem hatalarını ve yeniden başlatma (reboot) geçmişini tutar.
*   **Kernel (Çekirdek) Logları (`/var/log/kern.log`)**
    *   **Önemi:** Çekirdek olaylarını, donanım sürücülerini ve system call (sistem çağrısı) işlemlerini kaydeder. Hedefte istismar edilebilecek (vulnerable) eski bir sürücü ararken veya sisteme sızmış bir rootkit/malware'in bıraktığı şüpheli donanım çağrılarını tespit ederken kullanılır.
*   **Uygulama Logları (Application Logs)**
    *   Belirli servislere yönelik saldırı (Örn: Web sunucusuna SQL Injection denemesi) yaparken veya izleri temizlerken bakılması gereken dosyalardır:
    *   **Apache:** `/var/log/apache2/access.log` ve `/var/log/apache2/error.log`
    *   **Nginx:** `/var/log/nginx/access.log`
    *   **MySQL:** `/var/log/mysql/mysql.log`
    *   **Systemd:** `/var/log/journal/`
*   **Güvenlik Yazılımı Logları (Security Logs)**
    *   Sistemde bir savunma aracı çalışıyorsa, yakalanıp yakalanmadığınızı (veya hedefin kimleri engellediğini) buradan görebilirsiniz:
    *   **Fail2ban:** `/var/log/fail2ban.log` (Hatalı şifre denemelerini engelleyen araç)
    *   **UFW (Güvenlik Duvarı):** `/var/log/ufw.log`

## 2. Logları Okumak İçin Kullanılan Temel Araçlar
Hedef sistemde (veya kendi sisteminizde) logları analiz ederken grafik arayüzünüz olmayacağı için şu terminal komutlarını aktif olarak kullanmalısınız:

*   **`tail`**: Log dosyasının sadece en son (en yeni) satırlarını okumak için kullanılır (Örn: `tail -f /var/log/auth.log` komutu logları canlı olarak akıtır).
*   **`grep`**: Binlerce satır log içinde belirli bir kelimeyi (Örn: "Failed password" veya belirli bir IP adresini) filtreleyip bulmak için kullanılır.
*   **`sed`**: Metin manipülasyonu ve ileri seviye filtreleme yapmak için kullanılır.



# Solaris İşletim Sistemi ve Linux ile Farkları

Solaris, 1990'larda Sun Microsystems (daha sonra Oracle tarafından satın alındı) tarafından geliştirilen Unix tabanlı, kapalı kaynak (proprietary) bir işletim sistemidir. Özellikle bankacılık, finans ve büyük veri merkezleri gibi yüksek güvenlik, kararlılık ve performans gerektiren kurumsal (mission-critical) ortamlarda kullanılır.

## 1. Linux ve Solaris Arasındaki Temel Farklar

Linux dağıtımları (Ubuntu, Debian vb.) açık kaynaklıdır; Solaris ise kapalı kaynaklıdır ve kaynak kodları herkese açık değildir. Sızma testleri veya sistem yönetimi sırasında hedefin Solaris olduğunu fark edersen, komut cephaneni (toolkit) buna göre güncellemen gerekir. 

Aşağıdaki tablo en sık kullanılan komutların Linux ve Solaris karşılıklarını özetler:

| İşlem / Özellik | Linux (Örn: Ubuntu) | Solaris |
| :--- | :--- | :--- |
| **Sistem/Donanım Bilgisi Alma** | `uname -a` | `showrev -a` |
| **Paket Yükleme** | `apt-get install [paket]` | `pkgadd -d [paket]` |
| **SUID İzinli Dosyaları Bulma** | `find / -perm 4000` | `find / -perm -4000` (Tire işaretine dikkat) |
| **Sürecin Açtığı Dosyaları İzleme** | `lsof -c apache2` | `pfiles \`pgrep httpd\`` |
| **Sistem Çağrılarını İzleme (Debug)** | `strace -p \`pgrep apache2\`` | `truss ls` |
| **NFS Paylaşım Ayar Dosyası** | `/etc/exports` | `/etc/dfs/dfstab` |
| **NFS Paylaşımı Başlatma** | `exportfs` vb. | `share -F nfs -o rw /export/home` |

## 2. Solaris'e Özgü Önemli Mekanizmalar

*   **SMF (Service Management Facility):** Solaris, servislerin güvenilirliğini ve yüksek erişilebilirliğini (high availability) sağlamak için son derece gelişmiş bir servis yönetim altyapısı kullanır.
*   **İzinler ve RBAC:** Linux'ta `sudo` komutu standartken, Solaris geleneksel olarak **RBAC (Role-Based Access Control - Rol Tabanlı Erişim Kontrolü)** kullanır (Sudo desteği Solaris 11 ile gelmiştir).
*   **NFS (Ağ Dosya Paylaşımı) Bağlama:** Sızma testinde bulduğun bir Solaris NFS paylaşımını kendi makinene bağlamak (mount) için şu sözdizimini (syntax) kullanırsın:
    ```bash
    mount -F nfs 10.129.15.122:/nfs_share /mnt/local
    ```

## 3. Süreç Analizi ve Hata Ayıklama (Truss & Pfiles)

Hedef sistemde çalışan bir servisin veya uygulamanın ne yaptığını (hangi dosyalara dokunduğunu, işletim sistemine ne istekler yolladığını) analiz etmek için Solaris iki güçlü araç sunar:

*   **`pfiles`:** Linux'taki `lsof` aracının karşılığıdır. Çalışan bir sürecin ağ soketleri ve dosya tanımlayıcıları (file descriptors) dahil olmak üzere açtığı her şeyi gösterir.
*   **`truss`:** Linux'taki `strace` aracının karşılığıdır. Süreçlerin işletim sistemine yaptığı çağrıları (system calls) canlı olarak izler. `strace`'ten en büyük farkı ve avantajı; sadece hedef süreci değil, o sürecin yarattığı **alt süreçleri (child processes)** ve sürece gönderilen **sinyalleri (signals)** de izleyebilmesidir.