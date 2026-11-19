---
title: "Iptables ve Güvenlik Duvarı"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Iptables ve Güvenlik Duvarı
tags:
  - siber-guvenlik
  - linux
  - iptables
  - firewall
---

# Iptables ve Güvenlik Duvarı

> ◀ [[06_Ağ_Yapılandırması_ve_Güvenliği|Ağ Yapılandırması ve Güvenliği]] · 📚 [[00_Linux_Komutları|İndeks]] · [[08_NFS|NFS (Network File System)]] ▶

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

> [!warning] Kural sırası ve kalıcılık
> `iptables` kuralları **yukarıdan aşağı** değerlendirilir, ilk eşleşen uygulanır. Kurallar yeniden başlatmada silinir; kalıcı yapmak için `iptables-persistent` veya `netfilter-persistent save` kullan. Uzaktan çalışırken önce SSH'a izin veren kuralı ekle, sonra `DROP` politikası uygula, yoksa kendini dışarıda bırakırsın.

---
> ◀ [[06_Ağ_Yapılandırması_ve_Güvenliği|Ağ Yapılandırması ve Güvenliği]] · 📚 [[00_Linux_Komutları|İndeks]] · [[08_NFS|NFS (Network File System)]] ▶
