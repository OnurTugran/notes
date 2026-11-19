---
title: "Dosya Sistemi ve Disk Yönetimi"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Dosya Sistemi ve Disk Yönetimi
tags:
  - siber-guvenlik
  - linux
  - filesystem
  - mount
  - inode
---

# Dosya Sistemi ve Disk Yönetimi

> ◀ [Süreçler ve Görev Zamanlama](04_S%C3%BCre%C3%A7ler_ve_G%C3%B6rev_Zamanlama.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Ağ Yapılandırması ve Güvenliği](06_A%C4%9F_Yap%C4%B1land%C4%B1rmas%C4%B1_ve_G%C3%BCvenli%C4%9Fi.md) ▶

## 1. Dosya Sistemleri (File Systems)
Linux, diski formatlarken kullanabileceğin farklı dosya sistemlerini destekler. Her birinin kullanım amacı farklıdır:
*   **ext2:** Eski bir sistemdir, çökme anında kurtarma (journaling) özelliği yoktur. Sadece USB bellekler gibi basit işlerde kullanılır.
*   **ext3 / ext4:** Modern Linux sistemlerinin varsayılan (default) ve en dengeli dosya sistemidir. Çökmelere karşı veri kurtarma (journaling) özelliğine sahiptir.
*   **Btrfs:** Anlık görüntü alma (snapshot) ve veri bütünlüğü kontrolleri gibi gelişmiş özellikleri vardır.
*   **XFS:** Büyük dosyaları işlemek ve yüksek performans gerektiren (yüksek I/O) sunucular için idealdir.
*   **NTFS:** Aslen Windows'a aittir. Çift işletim sistemli (Dual-boot) veya Windows ile ortak kullanılacak diskler için uyumluluk sağlar.

## 2. Inode (İndeks Düğümü) Kavramı
Linux'ta dosyalar isimleriyle değil, **Inode** adı verilen kimliklerle tutulur.
*   **Kütüphane Analojisi:** Inode tablosunu bir kütüphane kataloğu gibi düşünebilirsin. Katalog kartında (Inode) kitabın yazarı, sayfa sayısı ve nerede durduğu yazar ama kitabın içeriği veya kapağındaki tam adı yazmaz.
*   Inode'lar dosyanın **metadatasını** (izinler, sahiplik, boyut, diskteki fiziksel konumu) tutar. Bazen diskte yer olsa bile Inode limiti dolduğu için yeni dosya oluşturulamayan durumlar yaşanabilir.

## 3. Dosya Türleri
*   **Düzenli Dosyalar (Regular Files):** Metin (ASCII) veya binary (görsel, çalıştırılabilir program) verileridir.
*   **Dizinler (Directories):** İçinde diğer dosyaları ve klasörleri tutan "konteyner" dosyalardır.
*   **Sembolik Linkler (Symbolic Links / Symlinks):** Windows'taki "Kısayol" mantığıdır. Dosyayı kopyalamadan başka bir konumdan erişmeyi sağlar.

## 4. Disk Yönetimi ve Bağlama (Mounting)
Linux'ta diski bilgisayara takmak yetmez; diski işletim sistemindeki bir klasörle eşleştirmen (bağlaman) gerekir. Bu işleme **Mounting** denir.

*   **`fdisk -l`**: Sistemdeki diskleri ve bölümlerini (partition) listeler.
*   **`mount`**: Bir diski manuel olarak bir klasöre bağlar (Örn: `sudo mount /dev/sdb1 /mnt/usb`).
*   **`/etc/fstab` Dosyası:** Bilgisayar her yeniden başladığında disklerin otomatik olarak bağlanmasını sağlayan ayar dosyasıdır. Sızma testlerinde yanlış yapılandırılmış bir fstab dosyası yetki yükseltme aracı olabilir.
*   **`umount`**: Diski güvenle çıkartır (Örn: `sudo umount /mnt/usb`).
    *   *Not:* Eğer bir dosya o an çalışıyorsa disk çıkartılamaz. Açık dosyaları ve kullanan süreçleri görmek için `lsof` komutu kullanılır.

## 5. SWAP (Takas Alanı)
Fiziksel RAM dolduğunda sistemin çökmemesi için sabit diskte (HDD/SSD) ayrılan sanal bellek alanıdır.
*   Sistem RAM'de o an kullanılmayan verileri SWAP alanına atarak yer açar.
*   **Hazırda Beklet (Hibernation):** Bilgisayar uykuya daldığında RAM'deki tüm açık programlar SWAP alanına yazılır, bilgisayar açıldığında buradan geri okunarak kalınan yerden devam edilir.
*   İlgili komutlar: Alan oluşturmak için `mkswap`, aktif etmek için `swapon` kullanılır.

---
> ◀ [Süreçler ve Görev Zamanlama](04_S%C3%BCre%C3%A7ler_ve_G%C3%B6rev_Zamanlama.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Ağ Yapılandırması ve Güvenliği](06_A%C4%9F_Yap%C4%B1land%C4%B1rmas%C4%B1_ve_G%C3%BCvenli%C4%9Fi.md) ▶
