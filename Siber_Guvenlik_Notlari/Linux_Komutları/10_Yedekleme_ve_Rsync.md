---
title: "Yedekleme ve Rsync"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Yedekleme ve Rsync
tags:
  - siber-guvenlik
  - linux
  - backup
  - rsync
  - ssh
---

# Yedekleme ve Rsync

> ◀ [Web Sunucusu ve Dosya Transferi](09_Web_Sunucusu_ve_Dosya_Transferi.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Uzak Masaüstü ve Görüntü Protokolleri](11_Uzak_Masa%C3%BCst%C3%BC_ve_G%C3%B6r%C3%BCnt%C3%BC_Protokolleri.md) ▶

Linux'ta (özellikle Ubuntu) verileri yedeklemek ve korumak için üç temel araç öne çıkar:
1. **Rsync:** Sadece değişen veya yeni eklenen dosyaları aktardığı için inanılmaz hızlı ve verimlidir. Ağ üzerinden dosya kopyalamak için endüstri standardıdır.
2. **Duplicity:** Rsync'in üzerine inşa edilmiştir ancak verileri şifreleme (encryption) özelliği ekler. Verileri buluta (örn: Amazon S3) atarken güvenliği sağlar.
3. **Deja Dup:** Komut satırı sevmeyenler için Rsync ve Duplicity'i arka planda kullanan grafik arayüzlü (GUI) basit bir araçtır.

## 1. Rsync Kurulumu ve Temel Kullanımı

```bash
# Rsync'i kurma:
sudo apt install rsync -y

# Yerel klasörü uzak bir sunucuya yedekleme (-a: dosya izinlerini korur, -v: detayı ekrana basar):
rsync -av /path/to/mydirectory user@backup_server:/path/to/backup/directory

# Gelişmiş Yedekleme (-z: sıkıştırır, --delete: kaynakta silineni hedefte de siler):
rsync -avz --backup --backup-dir=/path/to/backup/folder --delete /path/to/mydirectory user@backup_server:/path/to/backup/directory

# Yedekten Geri Yükleme (Restore - Kaynak ve hedefin yerini değiştiriyoruz):
rsync -av user@backup_server:/path/to/backup/directory /path/to/mydirectory
```

## 2. SSH ile Güvenli Veri Transferi (Encrypted Rsync)
Ağ üzerinden giden verinin (özellikle sızma testlerinde hassas verilerin) araya girenler tarafından okunmasını engellemek için rsync'e trafiği **SSH tüneli** içinden geçirmesini söyleyebiliriz.

```bash
# -e ssh parametresi ile transferi şifreleme:
rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

## 3. Otomatik Senkronizasyon (Rsync + Cron)
Yedekleme işlemini her saat başı otomatik yapmak için `cron` ve `rsync` birlikte kullanılır. Ancak işlemin şifre sorulmadan otomatik yapılabilmesi için SSH anahtarı ayarlanmalıdır.

**Adım 1: Parolasız SSH için Anahtar (Key) Üretimi**

```bash
# 2048 bit RSA anahtarı oluştur (soruları Enter ile geç):
ssh-keygen -t rsa -b 2048

# Açık anahtarı (Public Key) yedek sunucusuna kopyala:
ssh-copy-id user@backup_server
```

**Adım 2: Yedekleme Betiği (Script) Oluşturma**
`RSYNC_Backup.sh` adında bir dosya oluşturup içine şu komutu yazarız:

```bash
#!/bin/bash
rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

**Adım 3: Çalıştırma İzni ve Cron Ayarı**

```bash
# Betiğe çalıştırma yetkisi (execute) ver:
chmod +x RSYNC_Backup.sh

# Zamanlanmış görevleri (crontab) düzenle:
crontab -e
```

Crontab dosyasının en altına şu satırı ekleyerek her saat başı (0. dakika) çalışmasını sağlarız:
`0 * * * * /path/to/RSYNC_Backup.sh`

---
> ◀ [Web Sunucusu ve Dosya Transferi](09_Web_Sunucusu_ve_Dosya_Transferi.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Uzak Masaüstü ve Görüntü Protokolleri](11_Uzak_Masa%C3%BCst%C3%BC_ve_G%C3%B6r%C3%BCnt%C3%BC_Protokolleri.md) ▶
