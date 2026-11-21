---
title: "Asimetrik Şifreleme"
ana-not: "[[00_Kriptoloji_İndeks]]"
tarih: 2026-11-19
aliases:
  - Asimetrik Şifreleme
tags:
  - siber-guvenlik
  - kriptoloji
  - asimetrik
  - rsa
  - ecc
  - pki
  - dijital-imza
---

# Asimetrik Şifreleme

> ◀ [Simetrik Şifreleme](03_Simetrik_%C5%9Eifreleme.md) · 📚 [İndeks](00_Kriptoloji_%C4%B0ndeks.md) · [Hash Fonksiyonlar ve Hash Algoritmaları](05_Hash_Fonksiyonlar_ve_Algoritmalar%C4%B1.md) ▶

Asimetrik şifreleme, iki farklı anahtar kullanarak veri şifreleme ve şifre çözme işlemlerinin yapıldığı bir şifreleme yöntemidir. Bu anahtarlar açık anahtar (public key) ve gizli anahtar (private key) olarak adlandırılır. Açık anahtar, herkesin erişimine açıkken, gizli anahtar yalnızca sahibi tarafından bilinir ve korunur. Asimetrik şifreleme, simetrik şifrelemeye göre daha güvenlidir ancak daha yavaştır.

## Asimetrik Şifreleme Algoritmaları

### 1. RSA (Rivest-Shamir-Adleman)
- 1977 yılında Ron Rivest, Adi Shamir ve Leonard Adleman tarafından geliştirilmiştir.
- Hem şifreleme hem de dijital imza işlemleri için kullanılır.
- 1024, 2048, 4096 bit gibi çeşitli anahtar uzunluklarına sahiptir.
- Güvenliği, büyük asal sayıların çarpanlarına ayrılmasının zorluğuna dayanır.

### 2. ECC (Elliptic Curve Cryptography)
- Eliptik eğri teorisine dayanan bir şifreleme yöntemidir.
- RSA'ya göre daha kısa anahtar uzunlukları ile benzer güvenlik sağlar (örneğin, 256 bit ECC anahtarı, 2048 bit RSA anahtarı ile eşdeğer güvenlik sağlar).
- Daha az hesaplama gücü gerektirir, bu yüzden mobil ve IoT cihazlarında tercih edilir.

### 3. ElGamal
- 1985 yılında Taher Elgamal tarafından geliştirilmiştir.
- Hem şifreleme hem de dijital imza için kullanılabilir.
- Güvenliği, ayrık logaritma probleminin zorluğuna dayanır.

### 4. DSA (Digital Signature Algorithm)
- 1991 yılında NIST tarafından dijital imzalar için standart olarak belirlenmiştir.
- Yalnızca dijital imza oluşturma ve doğrulama işlemleri için kullanılır.
- Güvenliği, ayrık logaritma probleminin zorluğuna dayanır.

## Asimetrik Şifrelemenin Avantajları ve Dezavantajları

### Avantajlar
- **Anahtar Dağıtımı:** Simetrik şifrelemede yaşanan anahtar dağıtım sorunları asimetrik şifreleme ile çözülür. Açık anahtar herkesle paylaşılabilir, gizli anahtar ise yalnızca sahibinde kalır.
- **Güvenlik:** İki anahtar kullanılması, sistemin güvenliğini artırır.
- **Kimlik Doğrulama:** Asimetrik şifreleme, dijital imzalar ve sertifikalar aracılığıyla kimlik doğrulama sağlar.

### Dezavantajlar
- **Performans:** Asimetrik şifreleme algoritmaları, simetrik şifreleme algoritmalarına göre daha yavaştır.
- **Hesaplama Gücü:** Daha fazla işlem gücü ve kaynak gerektirir.

## Açık Anahtar Altyapısı (Public Key Infrastructure - PKI)
Açık anahtar altyapısı, dijital sertifikalar ve sertifika otoriteleri (CA) kullanarak açık anahtarların güvenli bir şekilde dağıtılmasını ve yönetilmesini sağlar. PKI'nın temel bileşenleri şunlardır:

### 1. Sertifika Otoriteleri (CA)
- Açık anahtarları dijital sertifikalar ile doğrulayan ve güvence altına alan kuruluşlardır.
- Sertifikaların geçerliliğini sağlamak için sertifika imzalama ve iptal etme işlemleri gerçekleştirir.

### 2. Kayıt Otoriteleri (RA)
- Sertifika otoriteleri adına kimlik doğrulama ve kayıt işlemlerini yürüten kuruluşlardır.

### 3. Dijital Sertifikalar
- Bir kullanıcının veya cihazın açık anahtarını ve kimlik bilgilerini içeren dijital belgeler.
- Sertifika otoritesi tarafından imzalanarak doğrulanır.

### 4. Sertifika Depoları
- Dijital sertifikaların saklandığı ve yönetildiği veri tabanları.

## Dijital İmzalar
Dijital imzalar, bir belgenin veya mesajın kimliğini doğrulamak ve bütünlüğünü sağlamak için kullanılır. Dijital imzalar, genellikle asimetrik şifreleme algoritmaları kullanılarak oluşturulur.

### 1. Dijital İmza Oluşturma
- Belgenin özet değeri (hash) alınır.
- Hash değeri, göndericinin gizli anahtarı ile şifrelenir ve bu şifreli hash dijital imzayı oluşturur.

### 2. Dijital İmza Doğrulama
- Alıcı, belgenin hash değerini yeniden hesaplar.
- Göndericinin açık anahtarını kullanarak dijital imzayı çözer ve elde edilen özet değeri ile karşılaştırır.
- İki özet değeri eşleşiyorsa, belgenin bütünlüğü ve kimliği doğrulanmış olur.

## Asimetrik Şifreleme Uygulama Alanları

### 1. SSL/TLS
- Web tarayıcıları ve sunucular arasındaki güvenli iletişimi sağlamak için kullanılır.
- Web sitelerinin kimlik doğrulaması ve veri bütünlüğü sağlar.

### 2. E-posta Güvenliği
- PGP (Pretty Good Privacy) ve S/MIME (Secure/Multipurpose Internet Mail Extensions) protokolleri ile e-postaların şifrelenmesi ve dijital olarak imzalanması sağlanır.

### 3. VPN (Virtual Private Network)
- Uzak ağlar arasında güvenli bir iletişim sağlamak için kullanılır.

### 4. Dijital Sertifikalar ve Kimlik Doğrulama
- Elektronik ticaret, banka işlemleri ve diğer dijital hizmetlerde kullanıcı kimlik doğrulaması sağlanır.

### 5. Dosya ve Veri Şifreleme
- Hassas verilerin güvenli bir şekilde saklanması ve iletilmesi sağlanır.

---
> ◀ [Simetrik Şifreleme](03_Simetrik_%C5%9Eifreleme.md) · 📚 [İndeks](00_Kriptoloji_%C4%B0ndeks.md) · [Hash Fonksiyonlar ve Hash Algoritmaları](05_Hash_Fonksiyonlar_ve_Algoritmalar%C4%B1.md) ▶
