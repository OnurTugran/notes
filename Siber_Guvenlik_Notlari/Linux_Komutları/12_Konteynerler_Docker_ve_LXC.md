---
title: "Konteynerler: Docker ve LXC"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Konteynerler: Docker ve LXC
tags:
  - siber-guvenlik
  - linux
  - docker
  - lxc
  - container
---

# Konteynerler: Docker ve LXC

> ◀ [[11_Uzak_Masaüstü_ve_Görüntü_Protokolleri|Uzak Masaüstü ve Görüntü Protokolleri]] · 📚 [[00_Linux_Komutları|İndeks]] · [[13_Sistem_Logları|Sistem Logları ve Güvenlik Analizi]] ▶

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
# Dockerfile'ın bulunduğu klasörde imaj oluşturma (etiket: fs_docker, Docker etiketleri küçük harf olmalıdır):
docker build -t fs_docker .

# İmajı çalıştırarak konteyner oluşturma (Host'un 8080 portunu konteynerin 80 portuna bağlar. -d arka planda çalıştırır):
docker run -p 8080:80 -p 8022:22 -d fs_docker

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

---
> ◀ [[11_Uzak_Masaüstü_ve_Görüntü_Protokolleri|Uzak Masaüstü ve Görüntü Protokolleri]] · 📚 [[00_Linux_Komutları|İndeks]] · [[13_Sistem_Logları|Sistem Logları ve Güvenlik Analizi]] ▶
