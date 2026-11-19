---
title: "Solaris ve Linux Farkları"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Solaris ve Linux Farkları
tags:
  - siber-guvenlik
  - linux
  - solaris
---

# Solaris ve Linux Farkları

> ◀ [Sistem Logları ve Güvenlik Analizi](13_Sistem_Loglar%C4%B1.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md)

Solaris, 1990'larda Sun Microsystems (daha sonra Oracle tarafından satın alındı) tarafından geliştirilen Unix tabanlı, kapalı kaynak (proprietary) bir işletim sistemidir. Özellikle bankacılık, finans ve büyük veri merkezleri gibi yüksek güvenlik, kararlılık ve performans gerektiren kurumsal (mission-critical) ortamlarda kullanılır.

## 1. Linux ve Solaris Arasındaki Temel Farklar

Linux dağıtımları (Ubuntu, Debian vb.) açık kaynaklıdır; Solaris ise kapalı kaynaklıdır ve kaynak kodları herkese açık değildir. Sızma testleri veya sistem yönetimi sırasında hedefin Solaris olduğunu fark edersen, komut cephaneni (toolkit) buna göre güncellemen gerekir.

Aşağıdaki tablo en sık kullanılan komutların Linux ve Solaris karşılıklarını özetler:

| İşlem / Özellik | Linux (Örn: Ubuntu) | Solaris |
| :--- | :--- | :--- |
| **Sistem/Donanım Bilgisi Alma** | `uname -a` | `showrev -a` |
| **Paket Yükleme** | `apt-get install [paket]` | `pkgadd -d [paket]` |
| **SUID İzinli Dosyaları Bulma** | `find / -perm -4000 2>/dev/null` | `find / -perm -4000` |
| **Sürecin Açtığı Dosyaları İzleme** | `lsof -c apache2` | `pfiles \`pgrep httpd\`` |
| **Sistem Çağrılarını İzleme (Debug)** | `strace -p \`pgrep apache2\`` | `truss ls` |
| **NFS Paylaşım Ayar Dosyası** | `/etc/exports` | `/etc/dfs/dfstab` |
| **NFS Paylaşımı Başlatma** | `exportfs` vb. | `share -F nfs -o rw /export/home` |

## 2. Solaris'e Özgü Önemli Mekanizmalar

*   **SMF (Service Management Facility):** Solaris, servislerin güvenilirliğini ve yüksek erişilebilirliğini (high availability) sağlamak için son derece gelişmiş bir servis yönetim altyapısı kullanır.
*   **İzinler ve RBAC:** Linux'ta `sudo` komutu standartken, Solaris geleneksel olarak **RBAC (Role-Based Access Control - Rol Tabanlı Erişim Kontrolü)** kullanır (Sudo desteği Solaris 11 ile gelmiştir).
*   **NFS (Ağ Dosya Paylaşımı) Bağlama:** Sızma testinde bulduğun bir Solaris NFS paylaşımını kendi makinene bağlamak (mount) için şu sözdizimini (syntax) kullanırsın:
    ```bash
    mount -F nfs <HEDEF_IP>:/nfs_share /mnt/local
    ```

## 3. Süreç Analizi ve Hata Ayıklama (Truss & Pfiles)

Hedef sistemde çalışan bir servisin veya uygulamanın ne yaptığını (hangi dosyalara dokunduğunu, işletim sistemine ne istekler yolladığını) analiz etmek için Solaris iki güçlü araç sunar:

*   **`pfiles`:** Linux'taki `lsof` aracının karşılığıdır. Çalışan bir sürecin ağ soketleri ve dosya tanımlayıcıları (file descriptors) dahil olmak üzere açtığı her şeyi gösterir.
*   **`truss`:** Linux'taki `strace` aracının karşılığıdır. Süreçlerin işletim sistemine yaptığı çağrıları (system calls) canlı olarak izler. `strace`'ten en büyük farkı ve avantajı; sadece hedef süreci değil, o sürecin yarattığı **alt süreçleri (child processes)** ve sürece gönderilen **sinyalleri (signals)** de izleyebilmesidir.

> [!NOTE]
> **`-perm 4000` ile `-perm -4000`**
> `-perm 4000` **yalnızca** izni tam olarak `4000` olan dosyaları bulur. `-perm -4000` ise SUID biti **set olan tüm** dosyaları bulur (örn. `4755`). SUID taramasında hep `-4000` kullan.

---
> ◀ [Sistem Logları ve Güvenlik Analizi](13_Sistem_Loglar%C4%B1.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md)
