---
title: "Simetrik Şifreleme"
ana-not: "[[00_Kriptoloji_İndeks]]"
tarih: 2026-11-19
aliases:
  - Simetrik Şifreleme
tags:
  - siber-guvenlik
  - kriptoloji
  - simetrik
  - aes
  - des
  - blok-sifre
  - akis-sifre
---

# Simetrik Şifreleme

> ◀ [Kriptografi ve Kriptoanaliz](02_Kriptografi_ve_Kriptoanaliz.md) · 📚 [İndeks](00_Kriptoloji_%C4%B0ndeks.md) · [Asimetrik Şifreleme](04_Asimetrik_%C5%9Eifreleme.md) ▶

Simetrik şifreleme, şifreleme ve şifre çözme işlemleri için aynı anahtarın kullanıldığı bir şifreleme yöntemidir. Bu yöntemde, gönderen ve alıcı, gizli bir anahtarı paylaşır ve bu anahtar kullanılarak veri şifrelenir veya çözümlenir. Simetrik şifreleme, hızlı ve etkilidir, ancak anahtar dağıtımı ve yönetimi konusunda zorluklar yaşanabilir.

## Simetrik Şifreleme Algoritmaları
Simetrik şifreleme algoritmaları iki ana kategoriye ayrılır: blok şifreler ve akış şifreler.

### Blok Şifreler (Block Cipher)
Blok şifreler, veriyi sabit boyutlu bloklar halinde işleyerek şifreleme ve şifre çözme işlemlerini gerçekleştirir. Her blok, belirli bir algoritma ve anahtar kullanılarak şifrelenir.

#### 1. DES (Data Encryption Standard)
- 1970'lerde IBM tarafından geliştirilmiş ve 1977'de ABD hükümeti tarafından standart olarak kabul edilmiştir.
- 56 bitlik anahtar uzunluğuna sahiptir.
- Güvenlik açısından yetersiz olduğu için artık yaygın olarak kullanılmamaktadır.

#### 2. 3DES (Triple DES)
- DES'in güvenlik zayıflıklarını gidermek için geliştirilmiştir.
- Üç kez DES algoritması kullanılarak şifreleme yapılır (şifrele-şifre çöz-şifrele).
- 168 bitlik anahtar uzunluğuna sahiptir.

#### 3. AES (Advanced Encryption Standard)
- 2001 yılında NIST (National Institute of Standards and Technology) tarafından standart olarak kabul edilmiştir.
- 128, 192 ve 256 bitlik anahtar uzunlukları bulunmaktadır.
- Güvenli ve hızlı olduğu için yaygın olarak kullanılmaktadır.

### Akış Şifreler (Stream Cipher)
Akış şifreler, veriyi bit veya bayt akışı halinde işleyerek şifreleme ve şifre çözme işlemlerini gerçekleştirir. Bu yöntem, özellikle veri akışlarının şifrelenmesi için uygundur.

#### 1. RC4 (Rivest Cipher 4)
- 1987 yılında Ron Rivest tarafından geliştirilmiştir.
- Değişken anahtar uzunluğuna sahiptir.
- Hızlı ve basit bir algoritma olmasına rağmen bazı güvenlik zayıflıkları bulunmaktadır.

#### 2. Salsa20 ve ChaCha20
- Güvenli ve hızlı akış şifreleme algoritmalarıdır.
- Özellikle mobil ve IoT cihazlar gibi düşük güç tüketimi gerektiren ortamlarda tercih edilir.

## Simetrik Şifrelemenin Avantajları ve Dezavantajları

### Avantajlar
- **Hız:** Simetrik şifreleme algoritmaları, asimetrik şifreleme algoritmalarına göre çok daha hızlıdır.
- **Verimlilik:** Daha az işlem gücü ve kaynak gerektirir.
- **Basitlik:** Algoritmalar ve uygulamaları genellikle daha basittir.

### Dezavantajlar
- **Anahtar Dağıtımı:** Anahtarın güvenli bir şekilde paylaşılması ve dağıtılması zor olabilir.
- **Anahtar Yönetimi:** Çok sayıda anahtarın yönetilmesi karmaşık olabilir, özellikle büyük ağlarda.
- **Güvenlik:** Anahtar ele geçirilirse tüm şifrelenmiş veriler tehlikeye girebilir.

## Blok Şifreleme Modları
Blok şifreler, farklı modlarda çalıştırılarak güvenlik ve performans artırılabilir.

### 1. ECB (Electronic Codebook) Modu
- Her blok bağımsız olarak şifrelenir.
- Aynı düz metin blokları, aynı şifreli metin bloklarına dönüşür.
- Tekrarlama ve desen ortaya çıkarma riskleri nedeniyle güvenli değildir.

### 2. CBC (Cipher Block Chaining) Modu
- Her blok, bir önceki blokla XOR işlemi yapılarak şifrelenir.
- İlk blok için bir başlangıç vektörü (IV) kullanılır.
- Desenleri ortadan kaldırarak daha güvenli hale gelir.

### 3. CFB (Cipher Feedback) Modu
- Şifreleme algoritması, bir akış şifresi gibi çalışır.
- Düz metin blokları, şifre metni ile XOR işlemi yapılarak şifrelenir.

### 4. OFB (Output Feedback) Modu
- Şifreleme algoritması, bir akış şifresi gibi çalışır.
- Şifreli metin blokları, düz metin blokları ile XOR işlemi yapılarak şifrelenir.

### 5. CTR (Counter) Modu
- Her blok için bir sayaç değeri kullanılarak şifreleme yapılır.
- Bloklar paralel olarak işlenebilir, bu da performansı artırır.

## Uygulama Alanları
Simetrik şifreleme, çeşitli alanlarda güvenli veri iletimi ve saklanması için kullanılır.

- **Veri Saklama:** Dosya ve disk şifreleme
- **İletişim:** VPN, SSL/TLS protokolleri
- **Kimlik Doğrulama:** Anahtar yönetim sistemleri

---
> ◀ [Kriptografi ve Kriptoanaliz](02_Kriptografi_ve_Kriptoanaliz.md) · 📚 [İndeks](00_Kriptoloji_%C4%B0ndeks.md) · [Asimetrik Şifreleme](04_Asimetrik_%C5%9Eifreleme.md) ▶
