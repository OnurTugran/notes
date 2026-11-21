---
title: "Kriptografik Protokoller"
ana-not: "[[00_Kriptoloji_İndeks]]"
tarih: 2026-11-19
aliases:
  - Kriptografik Protokoller
tags:
  - siber-guvenlik
  - kriptoloji
  - protokol
  - tls
  - ipsec
  - ssh
  - pgp
  - kerberos
  - oauth
---

# Kriptografik Protokoller

> ◀ [Kriptoanaliz Teknikleri](07_Kriptoanaliz_Teknikleri.md) · 📚 [İndeks](00_Kriptoloji_%C4%B0ndeks.md)

Kriptografik protokol, iki veya daha fazla taraf arasında güvenli iletişimi sağlamak, kimlik doğrulamak, veri bütünlüğünü korumak ve gizliliği sağlamak amacıyla belirlenmiş kurallar ve algoritmalar dizisidir. Bu protokoller, kriptografik algoritmaların ve tekniklerin doğru ve güvenli bir şekilde uygulanmasını sağlar.

## Önemli Kriptografik Protokoller

### 1. SSL/TLS (Secure Sockets Layer / Transport Layer Security)
- **Amaç:** İnternet üzerindeki iletişimlerin güvenliğini sağlamak.
- **Kullanım Alanları:** Web tarayıcıları ve sunucular arasındaki güvenli iletişim (HTTPS).
- **Çalışma Prensibi:**
  - **El Sıkışma (Handshake):** Taraflar arasında oturum anahtarı oluşturulur ve kimlik doğrulaması yapılır.
  - **Şifreli İletişim:** Oturum anahtarı kullanılarak veri şifrelenir ve iletilir.
- **Anahtar Bileşenler:** Asimetrik şifreleme, simetrik şifreleme, dijital sertifikalar ve hash fonksiyonları.

### 2. IPsec (Internet Protocol Security)
- **Amaç:** IP paketlerinin güvenliğini sağlamak.
- **Kullanım Alanları:** VPN'ler, güvenli ağlar arası iletişim.
- **Çalışma Prensibi:**
  - **Encapsulating Security Payload (ESP):** Veri gizliliği, bütünlüğü ve kimlik doğrulaması sağlar.
  - **Authentication Header (AH):** Veri bütünlüğü ve kimlik doğrulaması sağlar.
  - **IKE (Internet Key Exchange):** Anahtar değişimini ve güvenlik ilişkilendirmelerini yönetir.
- **Anahtar Bileşenler:** Simetrik şifreleme, asimetrik şifreleme, dijital sertifikalar ve hash fonksiyonları.

### 3. SSH (Secure Shell)
- **Amaç:** Güvenli uzak erişim ve yönetim sağlamak.
- **Kullanım Alanları:** Sunuculara, yönlendiricilere ve diğer ağ cihazlarına güvenli erişim.
- **Çalışma Prensibi:**
  - **El Sıkışma:** Taraflar arasında güvenli bir oturum başlatılır ve anahtar değişimi yapılır.
  - **Kimlik Doğrulama:** Kullanıcı kimlik doğrulaması gerçekleştirilir (parola, anahtar tabanlı).
  - **Şifreli İletişim:** Oturum anahtarı kullanılarak veri şifrelenir ve iletilir.
- **Anahtar Bileşenler:** Asimetrik şifreleme, simetrik şifreleme, hash fonksiyonları.

### 4. PGP (Pretty Good Privacy)
- **Amaç:** E-posta iletişiminin güvenliğini sağlamak.
- **Kullanım Alanları:** E-posta mesajlarının şifrelenmesi ve dijital imzalanması.
- **Çalışma Prensibi:**
  - **Anahtar Yönetimi:** Kullanıcılar arasında açık anahtarların paylaşımı.
  - **Şifreleme:** Mesajın simetrik şifreleme ile şifrelenmesi ve oturum anahtarının alıcının açık anahtarı ile şifrelenmesi.
  - **Dijital İmza:** Mesajın hash değerinin göndericinin gizli anahtarı ile imzalanması.
- **Anahtar Bileşenler:** Simetrik şifreleme, asimetrik şifreleme, dijital imzalar ve hash fonksiyonları.

### 5. Kerberos
- **Amaç:** Ağ üzerinde güvenli kimlik doğrulaması sağlamak.
- **Kullanım Alanları:** Kurumsal ağlarda kullanıcı ve hizmet kimlik doğrulaması.
- **Çalışma Prensibi:**
  - **Bilet Veren Sunucu (Ticket Granting Server):** Kullanıcıya oturum anahtarı ve bilet sağlar.
  - **Hizmet Bileti:** Kullanıcı, hizmetlere erişim sağlamak için hizmet biletini kullanır.
- **Anahtar Bileşenler:** Simetrik şifreleme, oturum anahtarları.

### 6. OAuth
- **Amaç:** Üçüncü taraf uygulamalara erişim sağlamak için yetkilendirme.
- **Kullanım Alanları:** Web uygulamaları, mobil uygulamalar.
- **Çalışma Prensibi:**
  - **Yetkilendirme Kodu:** Kullanıcıdan alınan yetkilendirme kodu ile erişim belirteci (access token) alınır.
  - **Erişim Belirteci:** Üçüncü taraf uygulamalar, belirli kaynaklara erişmek için erişim belirtecini kullanır.
- **Anahtar Bileşenler:** HTTPS, token tabanlı yetkilendirme.

## Kriptografik Protokollerin Güvenliği
Kriptografik protokollerin güvenliği, aşağıdaki faktörlere bağlıdır:

- **Güçlü Kriptografik Algoritmalar:** Güvenli ve güncel şifreleme algoritmaları kullanılmalıdır (AES, RSA, SHA-256).
- **Anahtar Yönetimi:** Anahtarların güvenli bir şekilde oluşturulması, saklanması ve değiştirilmesi sağlanmalıdır.
- **Güncellemeler ve Yamalar:** Protokol yazılımları düzenli olarak güncellenmeli ve güvenlik açıkları giderilmelidir.
- **Doğru Uygulama:** Protokoller, standartlara uygun şekilde ve doğru olarak uygulanmalıdır.

## Kriptografik Protokollerin Uygulama Alanları
- **Web Güvenliği:** HTTPS, SSL/TLS ile web tarayıcıları ve sunucular arasındaki iletişim güvence altına alınır.
- **Ağ Güvenliği:** IPsec ve SSH ile ağlar arası ve uzak erişim güvenliği sağlanır.
- **E-posta Güvenliği:** PGP ile e-posta mesajları şifrelenir ve imzalanır.
- **Kimlik Doğrulama ve Yetkilendirme:** Kerberos ve OAuth ile kullanıcı ve hizmet kimlik doğrulaması ve yetkilendirme yapılır.
- **Veri Güvenliği:** Kriptografik protokoller, veri saklama ve iletme süreçlerinde veri bütünlüğü ve gizliliği sağlar.

---
> ◀ [Kriptoanaliz Teknikleri](07_Kriptoanaliz_Teknikleri.md) · 📚 [İndeks](00_Kriptoloji_%C4%B0ndeks.md)
