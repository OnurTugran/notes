---
title: "Wi-Fi Kimlik Doğrulama Yöntemleri"
ana-not: "[[00_Wi-Fi_Hackleme_İndex]]"
tarih: 2026-11-19
aliases:
  - Wi-Fi Kimlik Doğrulama Yöntemleri
tags:
  - siber-guvenlik
  - wifi
  - kablosuz-ag
  - authentication
---

# Wi-Fi Kimlik Doğrulama Yöntemleri

> ◀ [Wi-Fi Güvenlik Protokolleri ve Sızma Testi Temelleri](01_Wi-Fi_G%C3%BCvenlik_Protokolleri.md) · 📚 [İndeks](00_Wi-Fi_Hackleme_%C4%B0ndex.md) · [802.11 Çerçeveleri ve Bağlantı Döngüsü](03_802.11_%C3%87er%C3%A7eveleri_ve_Ba%C4%9Flant%C4%B1_D%C3%B6ng%C3%BCs%C3%BC.md) ▶

> [!NOTE]
> **Özet**
> Wi-Fi'da iki temel kimlik doğrulama sistemi vardır: **Open System** (parola yok) ve **Shared Key** (ortak parola, challenge-response ile kanıtlanır). WPA/WPA2/WPA3 ağlarında ise parola doğrulaması **handshake / SAE** ile yapılır.

| Yöntem | Parola | Nasıl doğrular? | Saldırı etkisi |
|---|---|---|---|
| Open System | Yok | Auth + Association (2 adım çift) | Trafik şifresizdir, dinlenir |
| WEP Shared Key | WEP anahtarı | Challenge-Response (RC4) | Challenge + response yakalanırsa keystream çıkar |
| WPA/WPA2-PSK | PSK → PMK | **4-way handshake** | Handshake yakalanıp offline sözlükle kırılır |
| WPA3-SAE | Parola | Dragonfly / SAE | Offline kırma çok zor |

Wi-Fi ağlarında temelde iki ana kimlik doğrulama (Authentication) sistemi kullanılır: **Açık Sistem (Open System)** ve **Paylaşılan Anahtar (Shared Key)**.

## 1. Açık Sistem Kimlik Doğrulaması (Open System Authentication)
Hiçbir şifre (parola) veya önceden paylaşılmış bilgi gerektirmeyen, doğrudan bağlantıya izin veren yöntemdir. Genellikle şifresiz kafeler veya misafir ağları gibi halka açık (Open) ağlarda kullanılır.

**Bağlantı Adımları:**
1. **Authentication Request:** İstemci (telefon/bilgisayar) modeme kimlik doğrulama isteği gönderir.
2. **Authentication Response:** Modem, isteği kabul ettiğini belirten bir yanıt döner.
3. **Association Request:** İstemci, ağa bağlanmak için ilişkilendirme (Association) isteği yollar.
4. **Association Response:** Modem onaylar ve cihaz ağa şifresiz bir şekilde bağlanmış olur.

---

## 2. Paylaşılan Anahtar Kimlik Doğrulaması (Shared Key Authentication)
Modem ve cihazın önceden belirlenmiş ortak bir parolayı (Wi-Fi şifresini) bildiği yöntemdir. Cihazlar parolayı bilip bilmediklerini **"Challenge-Response (Meydan Okuma-Yanıt)"** adı verilen bir test mekanizmasıyla birbirlerine kanıtlarlar.

### A. WEP ile Kimlik Doğrulama (Eski ve Zayıf)
WEP, modası geçmiş ve saniyeler içinde kırılabilen ilk nesil korumadır. RC4 şifreleme ve CRC-32 bütünlük kontrolü kullanır.
1. İstemci bağlantı isteği atar.
2. **Challenge (Meydan Okuma):** Modem, istemciye rastgele bir "meydan okuma metni" (challenge text) gönderir.
3. **Challenge Response:** İstemci bu metni bildiği WEP şifresiyle şifreleyip modeme geri yollar.
4. **Verification (Doğrulama):** Modem şifreyi çözer; eğer içinden çıkan metin doğruysa cihazın parolayı bildiğini anlar ve bağlantıya izin verir.

### B. WPA / WPA2 ile Kimlik Doğrulama (Güncel ve Yaygın)
WEP'in zayıflıklarını kapatan ve günümüzde en çok karşılaştığımız ağ türüdür. WPA TKIP şifreleme kullanırken, WPA2 çok daha güvenli olan AES şifrelemesini kullanır.
1. Kimlik doğrulama istek ve cevapları şifresiz yapılır.
2. **Pairwise Key Generation:** Cihaz ve modem, o bildiğimiz Wi-Fi parolasını (PSK) kullanarak ortak bir matematiksel anahtar (PMK) üretir.
3. **4-Way Handshake (4'lü El Sıkışma):** Parolanın gerçekten bilinip bilinmediğini kanıtlamak için 4 aşamalı şifreli bir doğrulama yapılır. *(Pentest Notu: WPA/WPA2 ağlarını hacklemek için havada yakaladığımız hedef tam olarak bu el sıkışma aşamasıdır).*

### C. WPA3 (Yeni Nesil Güvenlik)
Şu anki en güvenli standarttır. Eski WPA2 ağlarındaki PSK (Önceden Paylaşılmış Anahtar) yöntemi yerine **SAE (Simultaneous Authentication of Equals)** adı verilen çok daha gelişmiş bir sistem kullanır.
* **Pentest Etkisi:** SAE sayesinde saldırganların yakaladığı paketleri eve götürüp çevrimdışı (offline) olarak devasa kelime listeleriyle (brute-force) kırma ihtimali çok büyük ölçüde engellenmiştir. Ancak eski cihaz donanımları desteklemediği için WPA2 kadar yaygınlaşmamıştır.

---
> ◀ [Wi-Fi Güvenlik Protokolleri ve Sızma Testi Temelleri](01_Wi-Fi_G%C3%BCvenlik_Protokolleri.md) · 📚 [İndeks](00_Wi-Fi_Hackleme_%C4%B0ndex.md) · [802.11 Çerçeveleri ve Bağlantı Döngüsü](03_802.11_%C3%87er%C3%A7eveleri_ve_Ba%C4%9Flant%C4%B1_D%C3%B6ng%C3%BCs%C3%BC.md) ▶
