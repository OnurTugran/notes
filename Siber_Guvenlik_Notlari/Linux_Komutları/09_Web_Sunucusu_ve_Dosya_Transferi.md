---
title: "Web Sunucusu ve Dosya Transferi"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Web Sunucusu ve Dosya Transferi
tags:
  - siber-guvenlik
  - linux
  - apache
  - curl
  - wget
  - http-server
---

# Web Sunucusu ve Dosya Transferi

> ◀ [[08_NFS|NFS (Network File System)]] · 📚 [[00_Linux_Komutları|İndeks]] · [[10_Yedekleme_ve_Rsync|Yedekleme ve Rsync]] ▶

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

---
> ◀ [[08_NFS|NFS (Network File System)]] · 📚 [[00_Linux_Komutları|İndeks]] · [[10_Yedekleme_ve_Rsync|Yedekleme ve Rsync]] ▶
