---
title: "WordPress Sertleştirme (Hardening)"
ana-not: "[[00_WordPress_İndeks]]"
tarih: 2026-11-19
aliases:
  - WordPress Sertleştirme (Hardening)
tags:
  - siber-guvenlik
  - wordpress
  - hardening
  - savunma
---

# WordPress Sertleştirme (Hardening)

> ◀ [[03_WordPress_Saldırıları|WordPress Saldırıları]] · 📚 [[00_WordPress_İndeks|İndeks]]

## WordPress Hardening (Sertleştirme)

> [!info] Özet
> WordPress sitelerine yönelik saldırıları önlemek için güncellemeler, eklenti/tema yönetimi, güvenlik eklentileri, kullanıcı yönetimi ve yapılandırma ayarları üzerine temel en iyi uygulamalar.

## Düzenli Güncelleme

Güvenliğin en temel ilkesidir. WordPress çekirdeği, eklentiler ve temalar sürekli güncel tutulmalıdır. Araştırmacılar üçüncü taraf eklentilerdeki açıkları sürekli bulmaktadır. Bazı barındırma sağlayıcıları çekirdeği otomatik günceller.

Otomatik güncellemeyi etkinleştirmek için `wp-config.php` dosyasına şu satırlar eklenebilir:

```php
define( 'WP_AUTO_UPDATE_CORE', true );
add_filter( 'auto_update_plugin', '__return_true' );
add_filter( 'auto_update_theme', '__return_true' );
```

> [!tip] Not
> Yönetim panelinde güncelleme bildirimleri genellikle otomatik olarak görünür. Ancak güncellemeleri ihmal etmek en sık yapılan hatalardan biridir.

## Eklenti ve Tema Yönetimi

- Yalnızca güvenilir kaynaklardan (WordPress.org) eklenti ve tema kurun
- Kurmadan önce şunlara bakın: yorumlar, popülerlik, kurulum sayısı ve son güncelleme tarihi
- Uzun süredir güncellenmemiş eklentiler terk edilmiş ve açık barındırıyor olabilir
- Düzenli denetim yapın, kullanılmayan eklenti ve temaları kaldırın

> [!warning] Dikkat
> Kullanılmayan ama kurulu duran eklentiler, unutulduğu için en çok güvenlik açığı barındıran bileşenlerdir.

## Güvenlik Eklentileri

| Eklenti | Öne Çıkan Özellikler |
|---------|---------------------|
| **Sucuri Security** | Güvenlik denetimi (activity auditing), dosya bütünlük izleme, uzaktan zararlı yazılım taraması, kara liste izleme |
| **iThemes Security** | 30+ koruma yöntemi, 2FA, WordPress tuzları (salts), Google reCAPTCHA, kullanıcı işlem günlüğü |
| **Wordfence Security** | Uç nokta güvenlik duvarı (WAF), zararlı yazılım tarayıcısı; premium sürümde gerçek zamanlı kural ve IP kara listesi |

> [!note] Ayrım
> Bu eklentiler WAF, zararlı yazılım tarama, izleme, brute force önleme ve güçlü parola zorlaması gibi işlevleri sağlar.

## Kullanıcı Yönetimi

Kullanıcılar, kuruluşlardaki en zayıf halka olarak sıkça hedef alınır.

- Varsayılan `admin` kullanıcısını devre dışı bırakın, zor tahmin edilen kullanıcı adları kullanın
- Güçlü parola zorunluluğu getirin
- Tüm kullanıcılar için 2FA etkinleştirin ve zorunlu kılın
- **En az ayrıcalık** (least privilege) ilkesiyle yetki verin
- Kullanıcı haklarını periyodik olarak denetleyin, kullanılmayan hesapları silin

## Yapılandırma Yönetimi

- **Kullanıcı numaralandırmayı (user enumeration)** engelleyen bir eklenti kurun. Böylece saldırgan geçerli kullanıcı adlarını öğrenip parola sprey saldırısında kullanamaz.
- Giriş denemelerini sınırlayarak brute force saldırılarını önleyin
- `wp-login.php` sayfasını yeniden adlandırın, taşıyın veya yalnızca belirli IP'lerden erişilebilir yapın

> [!example] Örnek Saldırı Bağlantısı
> Yukarıdaki [[03_WordPress_Saldırıları|Metasploit ile Exploitation]]te kullanılan `wp_admin_shell_upload` modülü, geçerli yönetici kimlik bilgileri olduğunda çalışır. Bu yüzden güçlü parola, 2FA ve en az ayrıcalık ilkeleri doğrudan bu saldırıyı zorlaştırır.

## İlgili Notlar

- [[04_WordPress_Sertleştirme|WordPress Sertleştirme]]
- [[03_WordPress_Saldırıları|Metasploit ile Exploitation]]
- [[03_WordPress_Saldırıları|WordPress Saldırıları]]
- [[04_WordPress_Sertleştirme|WordPress Sertleştirme]]

---
> ◀ [[03_WordPress_Saldırıları|WordPress Saldırıları]] · 📚 [[00_WordPress_İndeks|İndeks]]
