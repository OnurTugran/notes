---
title: "Sistem Logları ve Güvenlik Analizi"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Sistem Logları ve Güvenlik Analizi
tags:
  - siber-guvenlik
  - linux
  - logs
  - auth-log
---

# Sistem Logları ve Güvenlik Analizi

> ◀ [[12_Konteynerler_Docker_ve_LXC|Konteynerler: Docker ve LXC]] · 📚 [[00_Linux_Komutları|İndeks]] · [[14_Solaris_ile_Farklar|Solaris ve Linux Farkları]] ▶

Linux sistem logları, arka planda gerçekleşen her türlü sistem, ağ, uygulama ve kullanıcı hareketini kaydeder. Bir sızma testi uzmanı (veya sistem yöneticisi) için bu loglar; yetkisiz girişleri (brute-force saldırıları vb.), zafiyetli servisleri, düz metin (clear-text) unutulan parolaları ve sistemdeki anormal aktiviteleri tespit etmenin anahtarıdır.

Siber güvenlik perspektifinden incelemeniz gereken en kritik log dosyaları ve konumları şunlardır:

## 1. Kritik Log Dosyaları ve Konumları

*   **Kimlik Doğrulama Logları (`/var/log/auth.log`)**
    *   **Önemi:** Sızma testlerinde ilk bakılacak dosyalardan biridir. Başarılı ve başarısız tüm kullanıcı girişleri (SSH vb.), `sudo` (yetki yükseltme) komutu kullanımları ve SSH anahtar (public key) kabulleri burada tutulur.
    *   *(Not: CentOS / RHEL tabanlı sistemlerde bu dosya `/var/log/secure` olarak geçer).*
*   **Sistem Logları (`/var/log/syslog`)**
    *   **Önemi:** Sistemin genel günlüğüdür. Hangi servislerin başlatılıp durdurulduğunu, sistem hatalarını ve yeniden başlatma (reboot) geçmişini tutar.
*   **Kernel (Çekirdek) Logları (`/var/log/kern.log`)**
    *   **Önemi:** Çekirdek olaylarını, donanım sürücülerini ve system call (sistem çağrısı) işlemlerini kaydeder. Hedefte istismar edilebilecek (vulnerable) eski bir sürücü ararken veya sisteme sızmış bir rootkit/malware'in bıraktığı şüpheli donanım çağrılarını tespit ederken kullanılır.
*   **Uygulama Logları (Application Logs)**
    *   Belirli servislere yönelik saldırı (Örn: Web sunucusuna SQL Injection denemesi) yaparken veya izleri temizlerken bakılması gereken dosyalardır:
    *   **Apache:** `/var/log/apache2/access.log` ve `/var/log/apache2/error.log`
    *   **Nginx:** `/var/log/nginx/access.log`
    *   **MySQL:** `/var/log/mysql/mysql.log`
    *   **Systemd:** `/var/log/journal/`
*   **Güvenlik Yazılımı Logları (Security Logs)**
    *   Sistemde bir savunma aracı çalışıyorsa, yakalanıp yakalanmadığınızı (veya hedefin kimleri engellediğini) buradan görebilirsiniz:
    *   **Fail2ban:** `/var/log/fail2ban.log` (Hatalı şifre denemelerini engelleyen araç)
    *   **UFW (Güvenlik Duvarı):** `/var/log/ufw.log`

## 2. Logları Okumak İçin Kullanılan Temel Araçlar
Hedef sistemde (veya kendi sisteminizde) logları analiz ederken grafik arayüzünüz olmayacağı için şu terminal komutlarını aktif olarak kullanmalısınız:

*   **`tail`**: Log dosyasının sadece en son (en yeni) satırlarını okumak için kullanılır (Örn: `tail -f /var/log/auth.log` komutu logları canlı olarak akıtır).
*   **`grep`**: Binlerce satır log içinde belirli bir kelimeyi (Örn: "Failed password" veya belirli bir IP adresini) filtreleyip bulmak için kullanılır.
*   **`sed`**: Metin manipülasyonu ve ileri seviye filtreleme yapmak için kullanılır.

---
> ◀ [[12_Konteynerler_Docker_ve_LXC|Konteynerler: Docker ve LXC]] · 📚 [[00_Linux_Komutları|İndeks]] · [[14_Solaris_ile_Farklar|Solaris ve Linux Farkları]] ▶
