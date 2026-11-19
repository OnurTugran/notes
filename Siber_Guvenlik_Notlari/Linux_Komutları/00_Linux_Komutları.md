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

> 📚 [[Siber_Güvenlik_Ana_Sayfa|Ana Sayfa]]

> [!abstract] Bu klasör
> Sızma testi ve sistem yönetimi için Linux notları. Eskiden tek dosyadaydı, konuya göre bölündü.

| Not | İçerik |
|---|---|
| [[01_Temel_Komutlar\|Temel Komutlar]] | Dizin/dosya, kullanıcı ve yetki, grep/sed/awk özet |
| [[02_Metin_İşleme_ve_Regex\|Metin İşleme ve Regex]] | cut, tr, column, awk, sed, wc, regex operatörleri, sshd_config analizi |
| [[03_Paket_Yönetimi\|Paket Yönetimi]] | apt, dpkg, git ile araç kurulumu, pip/snap/gem |
| [[04_Süreçler_ve_Görev_Zamanlama\|Süreçler ve Görev Zamanlama]] | systemd, PID/PPID, /proc, systemd timer ve cron |
| [[05_Dosya_Sistemi_ve_Disk\|Dosya Sistemi ve Disk Yönetimi]] | ext4/XFS/Btrfs, inode, mount, fstab, swap |
| [[06_Ağ_Yapılandırması_ve_Güvenliği\|Ağ Yapılandırması ve Güvenliği]] | ip/ifconfig, DNS, DAC/MAC/RBAC, ping/traceroute/ss, SELinux/AppArmor |
| [[07_Iptables_Güvenlik_Duvarı\|Iptables ve Güvenlik Duvarı]] | Tablolar, zincirler, hedefler, temel kurallar |
| [[08_NFS\|NFS (Network File System)]] | NFS nedir, sızma testindeki önemi |
| [[09_Web_Sunucusu_ve_Dosya_Transferi\|Web Sunucusu ve Dosya Transferi]] | Apache, cURL vs wget, python3 http.server |
| [[10_Yedekleme_ve_Rsync\|Yedekleme ve Rsync]] | rsync, SSH ile şifreli transfer, cron ile otomatik yedek |
| [[11_Uzak_Masaüstü_ve_Görüntü_Protokolleri\|Uzak Masaüstü ve Görüntü Protokolleri]] | RDP/VNC, X11, XDMCP, TigerVNC, SSH tüneli |
| [[12_Konteynerler_Docker_ve_LXC\|Konteynerler: Docker ve LXC]] | Docker mimarisi ve komutları, LXC, cgroups |
| [[13_Sistem_Logları\|Sistem Logları ve Güvenlik Analizi]] | auth.log, syslog, kern.log, uygulama logları, tail/grep |
| [[14_Solaris_ile_Farklar\|Solaris ve Linux Farkları]] | Komut karşılıkları, SMF, RBAC, truss/pfiles |

## Hızlı hatırlatmalar
- **SUID dosyaları bul:** `find / -perm -4000 2>/dev/null` ([[14_Solaris_ile_Farklar]] altındaki nota bak)
- **Dinleyen portlar:** `ss -tulpn` ([[06_Ağ_Yapılandırması_ve_Güvenliği]])
- **Yorumsuz config oku:** `grep -vE "^\s*(#|$)" dosya` ([[02_Metin_İşleme_ve_Regex]])
- **Hızlı dosya sunucusu:** `python3 -m http.server` ([[09_Web_Sunucusu_ve_Dosya_Transferi]])
- Yetki yükseltme notları: [[00_Yetki_Yükseltme]]
