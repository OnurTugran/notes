---
title: "Ağ Yapılandırması ve Güvenliği"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Ağ Yapılandırması ve Güvenliği
tags:
  - siber-guvenlik
  - linux
  - network
  - hardening
  - selinux
---

# Ağ Yapılandırması ve Güvenliği

> ◀ [[05_Dosya_Sistemi_ve_Disk|Dosya Sistemi ve Disk Yönetimi]] · 📚 [[00_Linux_Komutları|İndeks]] · [[07_Iptables_Güvenlik_Duvarı|Iptables ve Güvenlik Duvarı]] ▶

Sızma testlerinde (penetration testing) Linux ağ ayarlarını yapılandırmak, ağ trafiğini yönlendirmek ve güvenliği sağlamak kritik bir beceridir. Bu bölüm ağ arayüzlerini, erişim modellerini, sorun giderme araçlarını ve sistemi sıkılaştırma (hardening) yöntemlerini kapsar.

## 1. Ağ Arayüzlerini Yapılandırma
Linux'ta ağ kartlarını ve bağlantıları yönetmek için geleneksel olarak `ifconfig`, güncel sistemlerde ise `ip` komutu kullanılır.

> [!note] ifconfig/route eskidi
> `ifconfig`, `route` ve `netstat` eski `net-tools` paketindendir. Modern karşılıkları: `ip addr`, `ip route`, `ss`. Aşağıda ikisi yan yana verilmiştir.

```bash
# Ağ arayüzlerini ve mevcut IP adreslerini listeleme:
ifconfig    # veya: ip addr

# Bir ağ arayüzünü (Örn: eth0) aktif (up) veya pasif (down) duruma getirme:
sudo ifconfig eth0 up
sudo ip link set eth0 up

# Arayüze geçici IP adresi ve Alt Ağ Maskesi (Netmask) atama:
sudo ifconfig eth0 192.168.1.2
sudo ifconfig eth0 netmask 255.255.255.0
# modern karşılığı (IP + maske tek komut):
sudo ip addr add 192.168.1.2/24 dev eth0

# Varsayılan Ağ Geçidi (Default Gateway) ayarlama:
sudo route add default gw 192.168.1.1 eth0
# modern karşılığı:
sudo ip route add default via 192.168.1.1 dev eth0
```

### DNS ve Kalıcı Ayarlar
Alan adlarını (Örn: google.com) IP adreslerine çeviren DNS sunucuları `/etc/resolv.conf` dosyasından ayarlanır.
```bash
# Geçici DNS ayarı (Yeniden başlatılınca silinebilir):
sudo nano /etc/resolv.conf
# İçine eklenecekler: nameserver 8.8.8.8
# Not: systemd-resolved kullanan sistemlerde (Ubuntu) bu dosya otomatik yeniden yazılabilir.
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
traceroute www.inlanefreight.com

# 3. NETSTAT: Sistemdeki açık portları ve aktif bağlantıları gösterir.
netstat -a

# Modern ve daha hızlı karşılığı (dinleyen TCP/UDP portları + süreçler):
ss -tulpn
```
*Sızma Testi Notu:* `netstat -a` / `ss -tulpn` komutu, sistemde gereksiz yere açık kalmış ve zafiyet barındırabilecek servisleri (SSH, FTP, VNC vb.) bulmak için harikadır.

---

## 4. Sistem Sıkılaştırma (Hardening)
Sistemi yetkisiz erişimlere ve sömürülere karşı korumak için üç temel mekanizma kullanılır:

1. **SELinux (Security-Enhanced Linux):** Linux çekirdeğine (kernel) entegre edilmiş çok katı bir **MAC** sistemidir. İzinleri son derece detaylı yönetir ancak yapılandırması oldukça karmaşıktır.
2. **AppArmor:** SELinux gibi bir **MAC** sistemidir ancak uygulamalara atanan "profiller" üzerinden çalışır. SELinux'a göre kullanımı ve yapılandırması çok daha kolaydır.
3. **TCP Wrappers:** Gelen bağlantıları IP adresine göre engelleyen (deny) veya izin veren (allow) host-tabanlı basit bir araçtır. Ağ seviyesinde temel bir koruma sağlar.

---
> ◀ [[05_Dosya_Sistemi_ve_Disk|Dosya Sistemi ve Disk Yönetimi]] · 📚 [[00_Linux_Komutları|İndeks]] · [[07_Iptables_Güvenlik_Duvarı|Iptables ve Güvenlik Duvarı]] ▶
