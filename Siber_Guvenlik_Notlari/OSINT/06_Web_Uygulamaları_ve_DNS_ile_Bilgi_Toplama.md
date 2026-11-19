---
title: "Web Uygulamaları ve DNS ile Bilgi Toplama"
ana-not: "[[00_OSINT_İndeks]]"
tarih: 2026-11-19
aliases:
  - Web Uygulamaları ve DNS ile Bilgi Toplama
tags:
  - siber-guvenlik
  - osint
  - dns
  - whois
  - wayback
  - dig
  - robots-txt
---

# Web Uygulamaları ve DNS ile Bilgi Toplama

> ◀ [Sosyal Medya ve Görseller ile Bilgi Toplama](05_Sosyal_Medya_ve_G%C3%B6rseller_ile_Bilgi_Toplama.md) · 📚 [İndeks](00_OSINT_%C4%B0ndeks.md)

Web uygulamaları ve DNS (Domain Name System) üzerinden bilgi toplama, siber güvenlik ve sızma testlerinde kritik öneme sahip bir tekniktir. Bu süreç, hedef sistemler hakkında ayrıntılı bilgi edinmek ve potansiyel zafiyetleri belirlemek için kullanılır. Web uygulamaları üzerinde yapılan bilgi toplama çalışmaları, uygulama yapısı, kullanılan teknolojiler ve açık portlar hakkında bilgi sağlar. DNS üzerinden yapılan bilgi toplama ise, alan adları, IP adresleri, alt alan adları ve MX (Mail Exchange) kayıtları gibi bilgileri içerir. Bu bilgiler, saldırı yüzeyini genişletmek ve daha etkili bir saldırı planı oluşturmak için kullanılır.

Web uygulamaları ve DNS bilgileri, pasif ve aktif bilgi toplama yöntemleri ile elde edilebilir. Pasif bilgi toplama, hedef sistemle doğrudan etkileşime girmeden bilgi toplama işlemini ifade ederken, aktif bilgi toplama doğrudan hedefe yönelik sorgular yaparak bilgi edinmeyi içerir.

## Temel Kavramlar

**Web (World Wide Web):** Web, internet üzerinde erişilebilen bir bilgi sistemidir. Kullanıcıların web tarayıcıları aracılığıyla metin, resim, video ve diğer multimedya içeriklerine ulaşmasını sağlar. Web, HTML (HyperText Markup Language) dili kullanılarak oluşturulan sayfalardan oluşur ve HTTP (HyperText Transfer Protocol) veya HTTPS (HTTP Secure) protokolleri ile sunucular ve istemciler arasında iletişim kurar.

**DNS (Domain Name System):** DNS, internet üzerindeki cihazların ve hizmetlerin isimlerini IP adreslerine çeviren hiyerarşik ve dağıtık bir veritabanı sistemidir. DNS, kullanıcıların web tarayıcılarına "www.example.com" gibi kolay hatırlanabilir alan adları girmelerini sağlar ve bu alan adlarını ilgili IP adreslerine çevirir, böylece doğru sunucuya bağlanmayı sağlar.

## Whois
Whois, bir web sitesi veya bir IP adresi hakkında önemli bilgileri toplamanıza olanak tanıyan bir sorgulama ve yanıt protokolüdür. Bu eğitimde, Whois sorgulamaları yaparak bir web sitesi hakkında nasıl bilgi toplanabileceğini anlatacağız. Whois sorgulamaları, domain sahibi, iletişim bilgileri, domainin kayıt tarihi ve bitiş tarihi, servis sağlayıcı gibi pek çok önemli bilgiyi ortaya çıkarabilir.

Whois sorgulaması için web tabanlı çevrimiçi araçlar olabileceği gibi, komut satırı araçları da olabilir. Popüler web tabanlı whois sorgulama siteleri arasında ICANN Whois, whois.com ve DomainTools bulunmaktadır.

```text
┌─[root@hackerbox]─[~]
└──╼ # whois google.com

% IANA WHOIS server
% for more information on IANA, visit http://www.iana.org
% This query returned 1 object

refer:        whois.verisign-grs.com

domain:       COM

organisation: VeriSign Global Registry Services
address:      12061 Bluemont Way
address:      Reston VA 20190
address:      United States of America (the)

contact:      administrative
name:         Registry Customer Service
organisation: VeriSign Global Registry Services
address:      12061 Bluemont Way
address:      Reston VA 20190
address:      United States of America (the)
phone:        +1 703 925-6999
fax-no:       +1 703 948 3978
e-mail:       info@verisign-grs.com

contact:      technical
name:         Registry Customer Service
organisation: VeriSign Global Registry Services
address:      12061 Bluemont Way
address:      Reston VA 20190
address:      United States of America (the)
phone:        +1 703 925-6999
fax-no:       +1 703 948 3978
e-mail:       info@verisign-grs.com

nserver:      A.GTLD-SERVERS.NET 192.5.6.30 2001:503:a83e:0:0:0:2:30
nserver:      B.GTLD-SERVERS.NET 192.33.14.30 2001:503:231d:0:0:0:2:30
nserver:      C.GTLD-SERVERS.NET 192.26.92.30 2001:503:83eb:0:0:0:0:30
nserver:      D.GTLD-SERVERS.NET 192.31.80.30 2001:500:856e:0:0:0:0:30
nserver:      E.GTLD-SERVERS.NET 192.12.94.30 2001:502:1ca1:0:0:0:0:30
nserver:      F.GTLD-SERVERS.NET 192.35.51.30 2001:503:d414:0:0:0:0:30
nserver:      G.GTLD-SERVERS.NET 192.42.93.30 2001:503:eea3:0:0:0:0:30
nserver:      H.GTLD-SERVERS.NET 192.54.112.30 2001:502:8cc:0:0:0:0:30
nserver:      I.GTLD-SERVERS.NET 192.43.172.30 2001:503:39c1:0:0:0:0:30
nserver:      J.GTLD-SERVERS.NET 192.48.79.30 2001:502:7094:0:0:0:0:30
nserver:      K.GTLD-SERVERS.NET 192.52.178.30 2001:503:d2d:0:0:0:0:30
nserver:      L.GTLD-SERVERS.NET 192.41.162.30 2001:500:d937:0:0:0:0:30
nserver:      M.GTLD-SERVERS.NET 192.55.83.30 2001:501:b1f9:0:0:0:0:30
ds-rdata:     19718 13 2 8acbb0cd28f41250a80a491389424d341522d946b0da0c0291f2d3d771d7805a

whois:        whois.verisign-grs.com

status:       ACTIVE
remarks:      Registration information: http://www.verisigninc.com

created:      1985-01-01
changed:      2023-12-07
source:       IANA
```

## İnternet Arşivi - Wayback Machine
İnternet Arşivi, yani Wayback Machine, internetin dijital bir zaman kapsülüdür. 1996 yılından bu yana web sayfalarının arşivlenmiş kopyalarını saklar ve kullanıcılara geçmişteki hallerine erişim imkanı sunar.

Wayback Machine, internet sayfalarının zaman içindeki değişikliklerini takip eden bir arşivleme servisidir. Kullanıcılar, bu servis aracılığıyla milyarlarca web sayfasının geçmişteki versiyonlarını görüntüleyebilir.

Bağlantı: <https://web.archive.org/>

## Websitesinde Kullanılan Teknolojiler

### Tarayıcı Eklentileri
Wappalyzer, WhatRuns gibi tarayıcı eklentileri de web sitelerinde kullanılan teknolojileri tespit etme konusunda oldukça kullanışlıdır. Bu eklentiler, ziyaret edilen web sitelerindeki teknolojiler hakkında anında bilgi sağlar.

Örnekler: Wappalyzer, WhatRuns

### Komut Satırı Araçları
Curl gibi komut satırı araçları kullanılarak web sunucularından HTTP başlıkları sorgulanabilir. Bu başlıklar içerisinde sunucu yazılımı, CMS sistemi ve bazen programlama dili hakkında ipuçları bulunabilir.

```text
┌─[root@hackerbox]─[~]
└──╼ # curl --head https://wordpress.org
HTTP/2 200
server: nginx
date: Sat, 18 May 2024 12:47:05 GMT
content-type: text/html; charset=UTF-8
vary: Accept-Encoding
strict-transport-security: max-age=3600
x-olaf: ⛄
link: <https://wordpress.org/wp-json/>; rel="https://api.w.org/"
link: <https://wordpress.org/wp-json/wp/v2/pages/457>; rel="alternate"; type="application/json"
link: <https://w.org/>; rel=shortlink
x-frame-options: SAMEORIGIN
alt-svc: h3=":443"; ma=86400
x-nc: HIT ord 1
```

Yapılan head isteği sonucunda wordpress.org sitesinin nginx web sunucusunda çalıştığı keşfedildi.

## robots.txt
İnternet üzerindeki websiteleri tarayan botlar, arama motorları ve diğer otomatik işlemler, site içeriğine nasıl erişecekleri ve hangi bölümlerden kaçınacakları konusunda yönlendirmelere ihtiyaç duyarlar. Bu yönlendirmeler genellikle robots.txt dosyası aracılığıyla sağlanır. Bu dosya, sitenin kök dizininde yer alır ve botların siteyi nasıl tarayacağını belirleyen kurallar içerir.

```text
┌─[root@hackerbox]─[~]
└──╼ # curl --get https://www.youtube.com/robots.txt
# robots.txt file for YouTube
# Created in the distant future (the year 2000) after
# the robotic uprising of the mid 90's which wiped out all humans.

User-agent: Mediapartners-Google*
Disallow:

User-agent: *
Disallow: /comment
Disallow: /feeds/videos.xml
Disallow: /get_video
Disallow: /get_video_info
Disallow: /get_midroll_info
Disallow: /live_chat
Disallow: /login
Disallow: /qr
Disallow: /results
Disallow: /signup
Disallow: /t/terms
Disallow: /timedtext_video
Disallow: /verify_age
Disallow: /watch_ajax
Disallow: /watch_fragments_ajax
Disallow: /watch_popup
Disallow: /watch_queue_ajax

Sitemap: https://www.youtube.com/sitemaps/sitemap.xml
Sitemap: https://www.youtube.com/product/sitemap.xml
```

## DNS Enumeration
DNS Enumeration, belirli bir hedef sistemin yüzey alanını daha iyi anlamak için yapılan bir keşif (Reconnaissance) tekniğidir. Bu teknik, hedef sistemin yapılandırmasına bağlı olarak DNS kayıt türleri, host isimleri, IP adresleri ve daha fazlası gibi çeşitli önemli bilgileri elde etmeyi amaçlar.

### DNSdumpster
DNSdumpster.com, siber güvenlik uzmanları ve ağ yöneticileri arasında popüler bir çevrimiçi araçtır. Bu web tabanlı araç, belirli bir domain ile ilgili kapsamlı DNS bilgileri ve domain haritalama yetenekleri sunar. Amaç, bir domain'in DNS yapılandırmasını derinlemesine analiz etmek ve bu bilgileri güvenlik incelemeleri, ağ yönetimi için kullanmaktır.

Bağlantı: <https://dnsdumpster.com/>

### host
host aracı, DNS sorguları yapmak için kullanılan basit ve güçlü bir komut satırı aracıdır. Domain isimlerini IP adreslerine çevirebilir, mail sunucularını (MX kayıtları) sorgulayabilir ve domainlerin NS (Name Server) kayıtlarını bulabilir. Linux ve Unix benzeri işletim sistemlerinde yaygın olarak bulunur.

**IP Adresini Sorgulama**

```text
┌─[root@hackerbox]─[~]
└──╼ # host youtube.com
youtube.com has address 142.250.187.110
youtube.com has IPv6 address 2a00:1450:4017:810::200e
youtube.com mail is handled by 0 smtp.google.com.
```

### dig
dig (Domain Information Groper), DNS sorgulamak için kullanılan daha gelişmiş bir araçtır. Gelişmiş sorgu seçenekleri ve çıktı formatlama özellikleri sunar. Sistem yöneticileri ve ağ profesyonelleri tarafından tercih edilir.

**Basit DNS Sorgusu**

```text
┌─[root@hackerbox]─[~]
└──╼ # dig youtube.com

; <<>> DiG 9.10.6 <<>> youtube.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 42632
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;youtube.com.			IN	A

;; ANSWER SECTION:
youtube.com.		300	IN	A	172.217.17.110

;; Query time: 66 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Sat May 18 15:54:50 +03 2024
;; MSG SIZE  rcvd: 56
```

---
> ◀ [Sosyal Medya ve Görseller ile Bilgi Toplama](05_Sosyal_Medya_ve_G%C3%B6rseller_ile_Bilgi_Toplama.md) · 📚 [İndeks](00_OSINT_%C4%B0ndeks.md)
