---
title: "Dijital İmzalar"
ana-not: "[[00_Kriptoloji_İndeks]]"
tarih: 2026-11-19
aliases:
  - Dijital İmzalar
tags:
  - siber-guvenlik
  - kriptoloji
  - dijital-imza
  - dsa
  - ecdsa
  - sertifika
  - crl
  - pki
---

# Dijital İmzalar

> ◀ [Hash Fonksiyonlar ve Hash Algoritmaları](05_Hash_Fonksiyonlar_ve_Algoritmalar%C4%B1.md) · 📚 [İndeks](00_Kriptoloji_%C4%B0ndeks.md) · [Kriptoanaliz Teknikleri](07_Kriptoanaliz_Teknikleri.md) ▶

Dijital imza, bir belgenin veya mesajın kimliğini doğrulamak ve içeriğin bütünlüğünü korumak için kullanılan kriptografik bir tekniktir. Dijital imzalar, elektronik belgelerin ve iletişimin güvenliğini sağlamak amacıyla asimetrik şifreleme algoritmalarını kullanır.

## Dijital İmzaların Amaçları
- **Kimlik Doğrulama (Authentication):** İmzalayan kişinin kimliğini doğrulamak için kullanılır.
- **Bütünlük (Integrity):** Belgenin imzalandıktan sonra değiştirilmediğini garanti eder.
- **İnkâr Edilemezlik (Non-repudiation):** İmzalayan kişinin, imzaladığı belgeyi daha sonra inkâr etmesini önler.

## Dijital İmzaların Çalışma Prensibi
Dijital imza oluşturma ve doğrulama süreci genellikle şu adımlardan oluşur:

### Dijital İmza Oluşturma
1. **Özet Değerinin Hesaplanması:** İmzalanacak belgenin hash değeri, güvenli bir hash fonksiyonu kullanılarak hesaplanır.
2. **Hash Değerinin Şifrelenmesi:** Elde edilen hash değeri, imzalayan kişinin gizli anahtarı ile şifrelenir. Bu şifreli hash değeri dijital imzayı oluşturur.
3. **Dijital İmzanın Eklenmesi:** Dijital imza, belgenin sonuna eklenir veya belge ile birlikte gönderilir.

### Dijital İmza Doğrulama
1. **Özet Değerinin Hesaplanması:** Alıcı, belgenin hash değerini, aynı hash fonksiyonu kullanarak yeniden hesaplar.
2. **Dijital İmzanın Çözülmesi:** Alıcı, dijital imzayı imzalayan kişinin açık anahtarını kullanarak çözer ve elde edilen hash değeri ile belgenin hash değerini karşılaştırır.
3. **Karşılaştırma:** İki hash değeri eşleşiyorsa, belge değiştirilmemiş ve imzalayan kişinin kimliği doğrulanmış olur.

## Dijital İmza Algoritmaları
Dijital imzalar, çeşitli asimetrik şifreleme algoritmaları kullanılarak oluşturulur. En yaygın dijital imza algoritmaları şunlardır:

### 1. RSA (Rivest-Shamir-Adleman)
- Hem şifreleme hem de dijital imza işlemleri için kullanılır.
- İmza oluşturma ve doğrulama süreçlerinde güvenilir bir algoritmadır.

### 2. DSA (Digital Signature Algorithm)
- 1991 yılında NIST tarafından dijital imzalar için standart olarak belirlenmiştir.
- Yalnızca dijital imza oluşturma ve doğrulama için kullanılır.
- RSA'ya göre daha hızlı imza oluşturma ancak daha yavaş doğrulama sürecine sahiptir.

### 3. ECDSA (Elliptic Curve Digital Signature Algorithm)
- Eliptik eğri kriptografisi temel alınarak geliştirilmiştir.
- Kısa anahtar uzunlukları ile yüksek güvenlik sağlar.
- Mobil ve IoT cihazları gibi düşük güç tüketimi gerektiren ortamlarda yaygın olarak kullanılır.

## Dijital Sertifikalar ve PKI (Public Key Infrastructure)
Dijital imzaların güvenli ve doğrulanabilir bir şekilde kullanılabilmesi için dijital sertifikalar ve PKI (Public Key Infrastructure) kullanılır.

### 1. Dijital Sertifikalar
- Bir kullanıcının veya cihazın açık anahtarını ve kimlik bilgilerini içeren dijital belgeler.
- Sertifika otoritesi (CA) tarafından imzalanarak doğrulanır ve güvence altına alınır.
- X.509 standardına göre düzenlenir.

### 2. Sertifika Otoriteleri (CA)
- Dijital sertifikaları imzalayan ve doğrulayan güvenilir kuruluşlardır.
- Sertifikaların geçerliliğini sağlamak için sertifika imzalama ve iptal etme işlemleri gerçekleştirirler.

### 3. Sertifika İptal Listeleri (CRL)
- İptal edilen dijital sertifikaların listesidir.
- Sertifika otoriteleri tarafından düzenlenir ve periyodik olarak güncellenir.

## Dijital İmzaların Kullanım Alanları
- **E-Posta Güvenliği:** E-posta mesajlarının kimliğini doğrulamak ve bütünlüğünü sağlamak için kullanılır (PGP, S/MIME).
- **Elektronik Sözleşmeler:** Elektronik belgelerin ve sözleşmelerin yasal olarak bağlayıcı olmasını sağlar.
- **Yazılım ve Dosya Doğrulama:** Yazılım güncellemeleri ve dosyaların bütünlüğünü ve doğruluğunu sağlamak için kullanılır.
- **Finansal İşlemler:** Elektronik bankacılık ve finansal işlemlerde güvenliği ve kimlik doğrulamayı sağlar.
- **E-Devlet ve E-Ticaret:** E-Devlet hizmetleri ve elektronik ticaret işlemlerinde kimlik doğrulama ve veri bütünlüğünü sağlar.

## Dijital İmzaların Güvenliği
Dijital imzaların güvenliği, kullanılan asimetrik şifreleme algoritmasının ve hash fonksiyonunun güvenliğine dayanır. Güvenli bir dijital imza sistemi, aşağıdaki özelliklere sahip olmalıdır:

- **Güçlü Kriptografik Algoritmalar:** RSA, DSA, ECDSA gibi güvenilir asimetrik şifreleme algoritmaları kullanılmalıdır.
- **Güvenli Hash Fonksiyonları:** SHA-256, SHA-3 gibi güvenli hash fonksiyonları kullanılmalıdır.
- **Anahtar Yönetimi:** Gizli anahtarlar güvenli bir şekilde saklanmalı ve yönetilmelidir.
- **Sertifika Yönetimi:** Dijital sertifikalar ve sertifika iptal listeleri (CRL) düzenli olarak güncellenmeli ve yönetilmelidir.

---
> ◀ [Hash Fonksiyonlar ve Hash Algoritmaları](05_Hash_Fonksiyonlar_ve_Algoritmalar%C4%B1.md) · 📚 [İndeks](00_Kriptoloji_%C4%B0ndeks.md) · [Kriptoanaliz Teknikleri](07_Kriptoanaliz_Teknikleri.md) ▶
