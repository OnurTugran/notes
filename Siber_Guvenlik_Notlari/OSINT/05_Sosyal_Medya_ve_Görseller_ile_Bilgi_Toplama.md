---
title: "Sosyal Medya ve Görseller ile Bilgi Toplama"
ana-not: "[[00_OSINT_İndeks]]"
tarih: 2026-11-19
aliases:
  - Sosyal Medya ve Görseller ile Bilgi Toplama
tags:
  - siber-guvenlik
  - osint
  - sosyal-medya
  - sherlock
  - reverse-image
  - exiftool
---

# Sosyal Medya ve Görseller ile Bilgi Toplama

> ◀ [Google Dork ile Bilgi Toplama](04_Google_Dork_ile_Bilgi_Toplama.md) · 📚 [İndeks](00_OSINT_%C4%B0ndeks.md) · [Web Uygulamaları ve DNS ile Bilgi Toplama](06_Web_Uygulamalar%C4%B1_ve_DNS_ile_Bilgi_Toplama.md) ▶

Sosyal medya platformları ve görsel analiz araçları, OSINT (Açık Kaynak İstihbarat) süreçlerinde önemli rol oynar. Bu bölümde, sosyal medya platformlarından ve görsellerden nasıl bilgi toplanacağını incelenecektir.

## Sosyal Medya Analizi

### Kullanıcı Adı ile Bilgi Toplama
Bir kullanıcı adının kullanıldığı uygulamalar çeşitli araçlar ile tespit edilebilmektedir. Burada örnek olarak instantusername.com adresi kullanılacaktır. Web sitenin amacı bir kullanıcı adının uygulamalardaki kullanılıp kullanılmama durumu hakkında bilgi vermek. Bir kişiyi araştırırken ya da CTF yarışmalarında kullanılan popüler araçlardan biridir.

Örnek sorgu: <https://instantusername.com/?q=hacker>

### Sherlock Aracı ile Bilgi Toplama
Sherlock, bir kullanıcının çeşitli sosyal medya ve diğer platformlardaki kullanıcı adlarını bulmak için kullanılan popüler bir OSINT aracıdır. Kullanıcı adlarını tespit ederek, hedef kişinin çevrimiçi varlığı hakkında geniş bir perspektif sağlanabilir.

GitHub Adresi: [github.com/sherlock-project/sherlock](https://github.com/sherlock-project/sherlock)

### Kişiler Hakkında Bilgi Toplama
RocketReach ([rocketreach.co](https://rocketreach.co)), kişilerin profesyonel ve iletişim bilgilerini bulmak için kullanılan bir araçtır. Genellikle e-posta adresleri, telefon numaraları ve sosyal medya profilleri gibi bilgileri sağlar.

- Bir şirketin veya bireyin iletişim bilgilerini RocketReach kullanarak bulmak, bu kişilere ulaşmayı kolaylaştırır.
- LinkedIn profilleri üzerinden, çalışanların e-posta adresleri ve diğer iletişim bilgilerini tespit edilebilir.

## Görsel Analizi
Görseller, OSINT çalışmaları için önemli bir bilgi kaynağıdır. Görsel analizinde kullanılabilecek araç ve yöntemleri aşağıda bulunmaktadır:

### Reverse Image Search (Tersine Görsel Arama)

#### Google Images
Google Images ([images.google.com](https://images.google.com)): Bir görselin kaynağını bulmak, aynı görselin farklı versiyonlarını ve nerelerde kullanıldığını tespit etmek için kullanılabilir.

- İnternette bulunan bir görsel Google Reverse Image Search ile aratılarak, görselin daha önce hangi sitelerde kullanıldığı ve ne zaman yüklendiği öğrenilebilir.
- Bir profil fotoğrafının internette başka hangi platformlarda kullanıldığı tespit edilerek, kullanıcı hakkında daha fazla bilgi edinilebilir.

#### TinEye
TinEye ([tineye.com](https://tineye.com)): Görselin internet üzerindeki geçmişini takip etmek ve benzer görselleri bulmak için kullanılabilecek bir araçtır.

- Bir görsel TinEye'a yüklenerek, görselin hangi web sitelerinde bulunduğu ve farklı versiyonlarının olup olmadığı incelenebilir.
- Bir reklam veya haber görselinin orijinal kaynağı bulunarak, doğruluğu ve güvenilirliği teyit edilebilir.

#### PimEyes
Benzer İnsan Yüzlerini Bulma ([pimeyes.com](https://pimeyes.com)): Yüz tanıma teknolojisini kullanarak internet üzerindeki görsellerdeki yüzleri tanımlamak ve aynı yüzün bulunduğu diğer görselleri bulmak için kullanılan bir araçtır.

- Bir kişinin fotoğrafı PimEyes'a yüklenerek, aynı kişinin bulunduğu diğer görseller internet üzerinde tespit edilebilir.
- Bir profil fotoğrafının farklı web sitelerinde veya sosyal medya hesaplarında kullanılıp kullanılmadığı belirlenebilir.

### Metadata Analizi
Exiftool: Görsellerin ve videoların metadata bilgilerini çıkarmak ve analiz etmek için kullanılan güçlü bir araçtır.

- Bir fotoğrafın Exif bilgileri incelenerek, fotoğrafın çekildiği cihaz, çekim tarihi ve konumu (GPS Position) gibi detaylar öğrenilebilir.
- Bir videonun metadata bilgileri incelenerek, videonun oluşturulduğu yazılım, çekim detayları ve düzenleme bilgileri tespit edilebilir.

Exiftool kurulumu:

```bash
sudo apt-get install exiftool
```

Bir görselin metadata bilgilerini çıkarma:

```text
┌─[root@hackerbox]─[~]
└──╼ # exiftool IMG_8153.JPG
ExifTool Version Number         : 12.42
File Name                       : IMG_8153.JPG
File Size                       : 4.2 MB
File Modification Date/Time     : 2024:05:18 14:43:23+03:00
File Access Date/Time           : 2024:05:18 14:43:29+03:00
File Inode Change Date/Time     : 2024:05:18 14:43:28+03:00
File Permissions                : -rw-------
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
Make                            : Apple
Camera Model Name               : iPhone 14 Pro
Orientation                     : Rotate 90 CW
Software                        : 16.3.1
Modify Date                     : 2023:04:12 17:15:58
Host Computer                   : iPhone 14 Pro
ISO                             : 80
Date/Time Original              : 2023:04:12 17:15:58
Create Date                     : 2023:04:12 17:15:58
Offset Time                     : +02:00
Focal Length In 35mm Format     : 24 mm
Scene Capture Type              : Standard
Lens Info                       : 2.220000029-9mm f/1.779999971-2.8
Lens Make                       : Apple
Lens Model                      : iPhone 14 Pro back triple camera 6.86mm f/1.78
Profile CMM Type                : Apple Computer Inc.
Profile Version                 : 4.0.0
Profile Class                   : Display Device Profile
Color Space Data                : RGB
Image Size                      : 4032x3024
Create Date                     : 2023:04:12 17:15:58.950+02:00
Date/Time Original              : 2023:04:12 17:15:58.950+02:00
Modify Date                     : 2023:04:12 17:15:58+02:00
Thumbnail Image                 : (Binary data 13465 bytes, use -b option to extract)
GPS Altitude                    : 47.2 m Above Sea Level
GPS Latitude                    : 50 deg 50' 30.71" N
GPS Longitude                   : 4 deg 21' 14.96" E
MP Image 2                      : (Binary data 211126 bytes, use -b option to extract)
Circle Of Confusion             : 0.009 mm
Field Of View                   : 73.7 deg
Focal Length                    : 6.9 mm (35 mm equivalent: 24.0 mm)
GPS Position                    : 50 deg 50' 30.71" N, 4 deg 21' 14.96" E
Hyperfocal Distance             : 3.08 m
Lens ID                         : iPhone 14 Pro back triple camera 6.86mm f/1.78
```

---
> ◀ [Google Dork ile Bilgi Toplama](04_Google_Dork_ile_Bilgi_Toplama.md) · 📚 [İndeks](00_OSINT_%C4%B0ndeks.md) · [Web Uygulamaları ve DNS ile Bilgi Toplama](06_Web_Uygulamalar%C4%B1_ve_DNS_ile_Bilgi_Toplama.md) ▶
