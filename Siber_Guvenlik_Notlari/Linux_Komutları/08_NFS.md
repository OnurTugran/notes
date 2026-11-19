---
title: "NFS (Network File System)"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - NFS (Network File System)
tags:
  - siber-guvenlik
  - linux
  - nfs
---

# NFS (Network File System)

> ◀ [Iptables ve Güvenlik Duvarı](07_Iptables_G%C3%BCvenlik_Duvar%C4%B1.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Web Sunucusu ve Dosya Transferi](09_Web_Sunucusu_ve_Dosya_Transferi.md) ▶

NFS, uzak sistemlerdeki (sunuculardaki) dosyaları sanki kendi yerel bilgisayarımızda (lokalimizde) depolanıyormuş gibi kullanmamızı ve yönetmemizi sağlayan bir ağ protokolüdür.

## Temel Özellikleri ve Kullanım Amaçları
*   **Merkezi Yönetim ve İşbirliği:** Sistem yöneticileri, dosyaları tek bir merkezde (sunucuda) toplamak için NFS kullanır. Böylece hem Linux hem de Windows kullanıcıları aynı verilere ağ üzerinden eşzamanlı olarak erişip ortak çalışabilir.
*   **Yetki ve Güvenlik:** Kullanıcılara özel erişim kontrolleri (okuma/yazma izinleri) atanabilir.
*   **Gerçek Zamanlı Aktarım:** Sunucular arası dosya sistemlerini kopyalamak (replikasyon) ve verileri anlık aktarmak için çok verimlidir.
*   **Yaygın NFS Sunucuları:** `nfs-utils` (Ubuntu), `NFS-Ganesha` (Solaris) ve `OpenNFS` (Redhat Linux) en çok bilinen sunucu yazılımlarıdır.

## Siber Güvenlik ve Sızma Testindeki Önemi
Eğer sızma testi yaptığın hedef sistemde dosya transferi yapmak için **FTP istemcisi (client) yoksa**, ancak sistemde NFS servisi çalışıyorsa, NFS'i tıpkı FTP gibi kullanabilirsin. Hedef makinedeki paylaşıma açık bir NFS klasörünü kendi makinenize bağlayarak (mount ederek) hedefe zararlı dosya yükleyebilir (upload) veya sistemden hassas verileri çekebilirsin (exfiltration).

---
> ◀ [Iptables ve Güvenlik Duvarı](07_Iptables_G%C3%BCvenlik_Duvar%C4%B1.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Web Sunucusu ve Dosya Transferi](09_Web_Sunucusu_ve_Dosya_Transferi.md) ▶
