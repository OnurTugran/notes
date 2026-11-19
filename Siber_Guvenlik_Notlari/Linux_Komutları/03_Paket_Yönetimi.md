---
title: "Paket Yönetimi"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Paket Yönetimi
tags:
  - siber-guvenlik
  - linux
  - apt
  - dpkg
  - git
---

# Paket Yönetimi

> ◀ [Metin İşleme ve Regex](02_Metin_%C4%B0%C5%9Fleme_ve_Regex.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Süreçler ve Görev Zamanlama](04_S%C3%BCre%C3%A7ler_ve_G%C3%B6rev_Zamanlama.md) ▶

| Komut | Açıklama |
| :--- | :--- |
| **`dpkg`** | Debian paketlerini kurmak, derlemek, kaldırmak ve yönetmek için kullanılan temel araçtır. Daha kullanıcı dostu ana ön yüzü `aptitude` aracıdır. |
| **`apt`** | Paket yönetim sistemi için üst düzey (high-level) komut satırı arayüzü sağlar. En yaygın kullanılan paket yöneticisidir. |
| **`aptitude`** | `apt` aracına bir alternatiftir ve paket yöneticisi için daha gelişmiş, üst düzey bir arayüz sunar. |
| **`snap`** | Snap paketlerini kurar, yapılandırır, günceller ve kaldırır. Bulut, sunucular, masaüstü ve IoT cihazları için güncel uygulamaların güvenli bir şekilde dağıtılmasını sağlar. |
| **`gem`** | Ruby programlama dili için standart paket yöneticisi olan RubyGems'in ön yüzüdür (front-end). |
| **`pip`** | Debian arşivinde bulunmayan Python paketlerini kurmak için önerilen Python paket yükleyicisidir. Kuruluma başlamadan önce tüm gereksinimleri indirerek yarım kalan/hatalı (partial) kurulumları önler. |
| **`git`** | Hızlı, ölçeklenebilir, dağıtık bir versiyon (sürüm) kontrol sistemidir. Hem üst düzey işlemler hem de iç mekanizmalara tam erişim sağlayan zengin bir komut setine sahiptir. |

Linux'ta programlar Windows'taki gibi `.exe` dosyalarıyla değil, genellikle `.deb` (Debian tabanlı sistemler için) paketleriyle veya kaynak koddan derlenerek (Git ile) kurulur.

### 1. APT (Advanced Package Tool)
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

### 2. Git ile Araç İndirme
Siber güvenlik dünyasındaki araçların (scriptler, exploitler) büyük bir kısmı GitHub üzerinde barındırılır. `git clone` komutu, bir projenin tam kopyasını bilgisayarına indirir.

```bash
# Github'daki bir depoyu (repository) kopyalayıp belirtilen klasöre indirme:
mkdir ~/nishang/
git clone https://github.com/samratashok/nishang.git ~/nishang
```

### 3. DPKG (Debian Package Manager) ve Wget
Eğer kurmak istediğin araç APT depolarında yoksa, internetten `.deb` uzantılı dosyasını manuel olarak indirip `dpkg` ile kurabilirsin.
**Önemli Fark:** `dpkg` bağımlılıkları *otomatik çözmez*. Eksik bir paket varsa hata verir.

```bash
# wget ile internetten bir .deb dosyasını indirme:
wget http://archive.ubuntu.com/ubuntu/pool/main/s/strace/strace_4.21-1ubuntu1_amd64.deb

# İndirilen .deb paketini dpkg ile sisteme kurma (-i = install):
sudo dpkg -i strace_4.21-1ubuntu1_amd64.deb
```

---
> ◀ [Metin İşleme ve Regex](02_Metin_%C4%B0%C5%9Fleme_ve_Regex.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Süreçler ve Görev Zamanlama](04_S%C3%BCre%C3%A7ler_ve_G%C3%B6rev_Zamanlama.md) ▶
