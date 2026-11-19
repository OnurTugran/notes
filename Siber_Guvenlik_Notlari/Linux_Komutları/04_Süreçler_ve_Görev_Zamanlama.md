---
title: "Süreçler ve Görev Zamanlama"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Süreçler ve Görev Zamanlama
tags:
  - siber-guvenlik
  - linux
  - process
  - cron
  - systemd
---

# Süreçler ve Görev Zamanlama

> ◀ [Paket Yönetimi](03_Paket_Y%C3%B6netimi.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Dosya Sistemi ve Disk Yönetimi](05_Dosya_Sistemi_ve_Disk.md) ▶

## Süreç yönetimi

### 1. systemd (Init Sistemi)
Linux işletim sistemi başlatıldığında (boot edildiğinde) uyanan ve çalışmaya başlayan **ilk** programdır. Sistemin geri kalan tüm servislerini, ağ bağlantılarını ve arka plan uygulamalarını bu başlatır. Eskiden `init` veya `SysVinit` kullanılırdı, modern Linux dağıtımlarının neredeyse hepsi artık `systemd` kullanmaktadır.

### 2. PID (Process ID - Süreç Kimliği)
Linux'ta çalışan her programa, servise veya komuta sistem tarafından benzersiz bir numara atanır. Buna PID denir (Uygulamaların TC Kimlik Numarası gibi düşünebilirsin).
* `systemd` sistemde çalışan ilk süreç olduğu için PID numarası her zaman **1**'dir.

### 3. /proc/ Dizini
Çalışan süreçler aslında RAM'de (bellekte) yer tutar. Ancak Linux'ta "her şey bir dosyadır" (everything is a file) mantığı olduğu için, sistem çalışan her sürecin anlık bilgilerini, kullandığı bellek miktarını ve parametrelerini `/proc/` dizini altında canlı olarak tutar. (Örneğin; `/proc/1234/` klasörüne girersen, PID'si 1234 olan uygulamanın tüm sırlarını görebilirsin).

### 4. PPID (Parent Process ID - Üst Süreç Kimliği)
Programlar durduk yere kendi kendine açılmazlar, onları her zaman başka bir program (veya kullanıcı) başlatır.
* Başlatan sürece **Parent (Ebeveyn)**, başlatılan yeni sürece ise **Child (Çocuk)** denir.
* **PPID**, bir programı hangi uygulamanın başlattığını gösterir.
* *Siber Güvenlikteki Önemi:* Eğer sistemde zararlı bir yazılım (malware) bulursan, PPID'sine bakarak bu virüsü sisteme kimin soktuğunu veya hangi uygulamanın içinden tetiklendiğini bulabilirsin.

---

## Görev zamanlama (Task Scheduling)

Linux'ta belirli görevleri (yedekleme, veritabanı temizliği, güncelleme) otomatik olarak çalıştırmak (bir kahve makinesini sabah otomatik çalışacak şekilde kurmak gibi) için "Görev Zamanlama" kullanılır. Siber güvenlik açısından bu mekanizmalar, yetki yükseltmek (privilege escalation) veya sistemde kalıcılık (persistence) sağlamak amacıyla kötüye kullanılabilir (örn: zararlı bir scripti her gece otomatik çalıştıran bir Cron job).

Bu işlem temelde iki farklı araçla yapılır: **Systemd** ve **Cron**.

### 1. Systemd ile Zamanlama
Systemd ile bir görevi otomatikleştirmek üç aşamadan oluşur:

*   **Adım 1: Timer (Zamanlayıcı) Oluşturma:** Görevin *ne zaman* çalışacağını belirler. `/etc/systemd/system/mytimer.timer` dosyası oluşturulur.
    *   `OnBootSec=3min` (Sistem açıldıktan 3 dakika sonra çalıştır)
    *   `OnUnitActiveSec=1hour` (Her saat başı tekrarla)
*   **Adım 2: Service (Servis) Oluşturma:** Görevin *neyi* çalıştıracağını belirler. `/etc/systemd/system/mytimer.service` dosyası oluşturulup `ExecStart` parametresine scriptin tam yolu (Örn: `/full/path/to/my/script.sh`) yazılır.
*   **Adım 3: Aktifleştirme:**
    *   `sudo systemctl daemon-reload` (Değişiklikleri sisteme tanıt)
    *   `sudo systemctl start mytimer.timer` (Timer'ı başlat)
    *   `sudo systemctl enable mytimer.timer` (Kalıcı hale getir)

### 2. Cron (Crontab) ile Zamanlama
Cron, görevleri zamanlamak için kullanılan daha eski ve yaygın bir araçtır. Systemd'den farklı olarak, görevler `crontab` adı verilen tek bir dosyaya satır satır yazılır.

Bir Crontab satırı **5 zaman parametresi** ve **1 komut/script yolundan** oluşur:
1.  **Dakika** (0-59)
2.  **Saat** (0-23)
3.  **Ayın Günü** (1-31)
4.  **Ay** (1-12)
5.  **Haftanın Günü** (0-7) *(Not: 0 ve 7 Pazar gününü temsil eder)*

**Crontab Örnekleri:**
*   `0 */6 * * * /path/update.sh` -> Her 6 saatte bir çalıştırır.
*   `0 0 1 * * /path/script.sh` -> Her ayın 1'inde gece yarısı (00:00) çalıştırır.
*   `0 0 * * 0 /path/backup.sh` -> Her Pazar günü gece yarısı (00:00) çalıştırır.

### Systemd vs. Cron (Özet Fark)
*   **Systemd:** İşletim sistemine görevlerin ne zaman çalıştırılacağını bildirmek için iki ayrı dosya ("timer" ve "service" betikleri) gerektirir.
*   **Cron:** Görevlerin ne zaman çalıştırılacağını cron daemon'ına (arka plan servisine) bildirmek için yapılandırılmış tek bir "crontab" dosyası kullanır.

---
> ◀ [Paket Yönetimi](03_Paket_Y%C3%B6netimi.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Dosya Sistemi ve Disk Yönetimi](05_Dosya_Sistemi_ve_Disk.md) ▶
