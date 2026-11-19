---
title: "İnternet Arama Motorları: Shodan ve Censys"
ana-not: "[[00_OSINT_İndeks]]"
tarih: 2026-11-19
aliases:
  - İnternet Arama Motorları: Shodan ve Censys
tags:
  - siber-guvenlik
  - osint
  - shodan
  - censys
  - arama-motoru
---

# İnternet Arama Motorları: Shodan ve Censys

> ◀ [Web Uygulamaları ve DNS ile Bilgi Toplama](06_Web_Uygulamalar%C4%B1_ve_DNS_ile_Bilgi_Toplama.md) · 📚 [İndeks](00_OSINT_%C4%B0ndeks.md) · [Sızıntı Veri, Dark Web ve Deep Web Araçları](08_S%C4%B1z%C4%B1nt%C4%B1_Veri_Dark_Web_ve_Deep_Web_Ara%C3%A7lar%C4%B1.md) ▶

İnternet arama motorları, siber güvenlik uzmanları ve sızma testi (penetration testing) uzmanları için değerli araçlardır. Bu motorlar, internete bağlı cihazların, sistemlerin ve hizmetlerin durumunu analiz etmeye ve izlemeye yardımcı olur. Bu sayede, güvenlik açıklarını tespit etmek, tehditleri analiz etmek ve ağ güvenliğini güçlendirmek mümkün hale gelir. İnternet arama motorları, geniş veri tabanlarına sahiptir ve çeşitli sorgulama teknikleriyle bu verileri kullanarak bilgi toplar.

## Shodan
Shodan, "internetin arama motoru" olarak bilinen ve siber güvenlik uzmanları için kritik bir araç olan bir başka arama motorudur. Shodan, internete bağlı cihazları ve hizmetleri tarayarak, bu cihazların güvenlik durumunu ve açıklarını tespit etmeye yardımcı olur. Özellikle IoT (Internet of Things) cihazları, endüstriyel kontrol sistemleri ve ağ donanımları gibi internete bağlı tüm cihazları hedefler. Shodan'ın web sitesine [shodan.io](https://www.shodan.io) adresinden ulaşılabilir.

### Shodan Kullanım Örnekleri
[shodan.io/search/filters](https://www.shodan.io/search/filters) adresinde tüm Shodan filtreleri rehber olarak sunulmaktadır. İhtiyaç duyulduğunda buradan kullanılması faydalı olacaktır.

#### Cihazları ve Servisleri Arama
Shodan, belirli bir anahtar kelime veya IP adresi kullanarak cihazları ve servisleri bulmaya olanak tanır.

```text
apache
```

#### Belirli Bir Ülke Koduna Göre Arama
Belirli bir ülke koduna sahip cihazlar aranabilir.

```text
country:DE
```

#### Belirli Bir Şehre Göre Arama
Belirli bir şehirdeki cihazları ve servisler aranabilir.

```text
city:San Francisco
```

#### OS ve Servis Versiyonlarını Belirleme
Shodan, cihazların işletim sistemi ve servis versiyonlarını belirleyerek, zafiyetlerin tespit edilmesine yardımcı olur.

```text
os:Windows
```

#### Belirli Bir Port Numarasına Göre Arama
Belirli bir port numarasına sahip cihazlar aranabilir.

```text
port:22
```

#### Servis Tipine Göre Arama
Belirli bir servis tipine göre cihazlar aranabilir.

```text
service:ssh
```

#### Belirli Bir Ürüne Göre Arama
Belirli bir ürünün kullanıldığı sunucular tespit edilebilir.

```text
product:"Rockwell Automation"
```

#### Belirli Bir Organizasyona Ait Cihazları Arama
Belirli bir organizasyona ait cihazlar tespit edilebilir.

```text
org:Google
```

#### HTTP Başlıklarına Göre Arama
Başlığında belirli bir kelime geçen web sayfalarını bulur.

```text
http.title:"Login"
```

#### Belirli Bir Zafiyete Göre Arama
Belirli bir zafiyete sahip cihazlar tespit edilebilir.

```text
vuln:heartbleed
```

## Censys
Censys, internetin ve bağlı cihazların durumunu analiz etmek ve izlemek için kullanılan güçlü bir arama motorudur. Censys, internet üzerinde aktif olarak hizmet veren cihazların ve sistemlerin ayrıntılı bir envanterini sağlar. Bu sayede, güvenlik açıklarını tespit etmek, tehditleri analiz etmek ve ağ güvenliğini güçlendirmek mümkün hale gelir. Censys'in web sitesine [search.censys.io](https://search.censys.io) adresinden ulaşılabilir.

### Censys Kullanım Örnekleri

#### Açık Portları Bulma
Bir IP adresi veya IP aralığı girerek açık portları ve bu portlara hizmet veren servisleri tespit edilebilir.

```text
ip:192.168.1.1
```

#### SSL/TLS Sertifikalarını İnceleme
Belirli bir domainin SSL/TLS sertifikalarını inceleyerek, sertifika detaylarına ulaşılabilir. Bu, güvenlik açıklarının tespiti ve sertifika yönetimi için önemlidir.

```text
domain:google.com and tags:443/https
```

#### Hizmet Versiyonlarını Belirleme
Censys, belirli IP adreslerindeki servislerin versiyon bilgilerini sağlayarak, potansiyel güvenlik açıklarını tespit edilmesine yardımcı olur.

```text
ip:192.168.1.1 and tags:version
```

#### Belirli Bir Ülkedeki Cihazları Arama
Belirli bir ülkedeki cihazları ve açık portları tespit edilebilir.

```text
location.country_code:US
```

#### Belirli Bir Organizasyona Ait Cihazları Bulma
Belirli bir organizasyona ait IP aralıklarını ve bu aralıklardaki cihazlar bulunabilir.

```text
autonomous_system.asn:AS12345
```

#### Güvenlik Açıklarına Göre Arama
Belirli bir güvenlik açığına sahip cihazlar tespit edilebilir.

```text
443.https.tls.cipher_suite: "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
```

#### Belirli Bir Servis Kullanarak Arama
Belirli bir servis (örneğin, FTP) kullanarak cihazlar bulunabilir.

```text
services.service_name: "ftp"
```

#### Cihazların İşletim Sistemine Göre Arama
Belirli bir işletim sistemine sahip cihazlar bulunabilir.

```text
services.software.product: "Windows"
```

#### Güncelleme Durumuna Göre Arama
Belirli bir yazılımın güncellenmemiş versiyonlar tespit edilebilir.

```text
services.software.version: "OpenSSH_7.2p2"
```

#### Web Uygulamalarının Versiyonunu Belirleme
Belirli bir web uygulamasının versiyonu tespit edilebilir.

```text
services.software.product: "nginx" and services.software.version: "1.14.0"
```

---
> ◀ [Web Uygulamaları ve DNS ile Bilgi Toplama](06_Web_Uygulamalar%C4%B1_ve_DNS_ile_Bilgi_Toplama.md) · 📚 [İndeks](00_OSINT_%C4%B0ndeks.md) · [Sızıntı Veri, Dark Web ve Deep Web Araçları](08_S%C4%B1z%C4%B1nt%C4%B1_Veri_Dark_Web_ve_Deep_Web_Ara%C3%A7lar%C4%B1.md) ▶
