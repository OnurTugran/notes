---
title: "Linux Komutları (İndeks)"
ana-not: "[[Siber_Güvenlik_Ana_Sayfa]]"
tarih: 2026-11-19
aliases:
  - Linux Komutları
  - Linux Cheatsheet
tags:
  - siber-guvenlik
  - linux
  - moc
---

# Linux Komutları

> 📚 [Ana Sayfa](../Siber_G%C3%BCvenlik_Ana_Sayfa.md)

> [!NOTE]
> **Bu klasör**
> Sızma testi ve sistem yönetimi için Linux notları. Eskiden tek dosyadaydı, konuya göre bölündü.

| Not | İçerik |
|---|---|
| [Temel Komutlar](01_Temel_Komutlar.md) | Dizin/dosya, kullanıcı ve yetki, grep/sed/awk özet |
| [Metin İşleme ve Regex](02_Metin_%C4%B0%C5%9Fleme_ve_Regex.md) | cut, tr, column, awk, sed, wc, regex operatörleri, sshd_config analizi |
| [Paket Yönetimi](03_Paket_Y%C3%B6netimi.md) | apt, dpkg, git ile araç kurulumu, pip/snap/gem |
| [Süreçler ve Görev Zamanlama](04_S%C3%BCre%C3%A7ler_ve_G%C3%B6rev_Zamanlama.md) | systemd, PID/PPID, /proc, systemd timer ve cron |
| [Dosya Sistemi ve Disk Yönetimi](05_Dosya_Sistemi_ve_Disk.md) | ext4/XFS/Btrfs, inode, mount, fstab, swap |
| [Ağ Yapılandırması ve Güvenliği](06_A%C4%9F_Yap%C4%B1land%C4%B1rmas%C4%B1_ve_G%C3%BCvenli%C4%9Fi.md) | ip/ifconfig, DNS, DAC/MAC/RBAC, ping/traceroute/ss, SELinux/AppArmor |
| [Iptables ve Güvenlik Duvarı](07_Iptables_G%C3%BCvenlik_Duvar%C4%B1.md) | Tablolar, zincirler, hedefler, temel kurallar |
| [NFS (Network File System)](08_NFS.md) | NFS nedir, sızma testindeki önemi |
| [Web Sunucusu ve Dosya Transferi](09_Web_Sunucusu_ve_Dosya_Transferi.md) | Apache, cURL vs wget, python3 http.server |
| [Yedekleme ve Rsync](10_Yedekleme_ve_Rsync.md) | rsync, SSH ile şifreli transfer, cron ile otomatik yedek |
| [Uzak Masaüstü ve Görüntü Protokolleri](11_Uzak_Masa%C3%BCst%C3%BC_ve_G%C3%B6r%C3%BCnt%C3%BC_Protokolleri.md) | RDP/VNC, X11, XDMCP, TigerVNC, SSH tüneli |
| [Konteynerler: Docker ve LXC](12_Konteynerler_Docker_ve_LXC.md) | Docker mimarisi ve komutları, LXC, cgroups |
| [Sistem Logları ve Güvenlik Analizi](13_Sistem_Loglar%C4%B1.md) | auth.log, syslog, kern.log, uygulama logları, tail/grep |
| [Solaris ve Linux Farkları](14_Solaris_ile_Farklar.md) | Komut karşılıkları, SMF, RBAC, truss/pfiles |

## Hızlı hatırlatmalar
- **SUID dosyaları bul:** `find / -perm -4000 2>/dev/null` ([14_Solaris_ile_Farklar](14_Solaris_ile_Farklar.md) altındaki nota bak)
- **Dinleyen portlar:** `ss -tulpn` ([06_Ağ_Yapılandırması_ve_Güvenliği](06_A%C4%9F_Yap%C4%B1land%C4%B1rmas%C4%B1_ve_G%C3%BCvenli%C4%9Fi.md))
- **Yorumsuz config oku:** `grep -vE "^\s*(#|$)" dosya` ([02_Metin_İşleme_ve_Regex](02_Metin_%C4%B0%C5%9Fleme_ve_Regex.md))
- **Hızlı dosya sunucusu:** `python3 -m http.server` ([09_Web_Sunucusu_ve_Dosya_Transferi](09_Web_Sunucusu_ve_Dosya_Transferi.md))
- Yetki yükseltme notları: [00_Yetki_Yükseltme](../Yetki%20Y%C3%BCkseltme/00_Yetki_Y%C3%BCkseltme.md)
