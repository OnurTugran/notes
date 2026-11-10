
flowchart TD
    A([Wi-Fi Güvenliği]) --> B([Wi-Fi Kimlik Doğrulama Türleri])
    
    B --> WEP([WEP])
    B --> WPA([WPA])
    B --> WPA2([WPA2])
    B --> WPA3([WPA3])
    
    WPA2 --> WPA2_PSK([WPA2-PSK / WPA2-Kişisel])
    WPA2 --> WPA2_ENT([WPA2-Kurumsal])
    
    WPA3 --> WPA3_ENT([WPA3-Kurumsal])
    WPA3 --> WPA3_SAE([WPA3-SAE / WPA3-Kişisel])
    
    WPA2_ENT --> EAP_TTLS([EAP-TTLS/PAP])
    WPA2_ENT --> PEAP([PEAP-MSCHAPv2])
    WPA2_ENT --> EAP_TLS([EAP-TLS])
    
    WPA3_ENT --> EAP_TLS



### Wi-Fi Güvenlik Protokolleri

*   **WEP (Wired Equivalent Privacy):** Orijinal Wi-Fi güvenlik protokolüdür. Temel düzeyde bir şifreleme sağlasa da, kırılmasını çok kolaylaştıran zafiyetleri nedeniyle günümüzde tamamen **eski ve güvensiz** olarak kabul edilmektedir.
*   **WPA (WiFi Protected Access):** WEP'in zayıflıklarına karşı geçici bir iyileştirme olarak sunulmuştur. TKIP (Temporal Key Integrity Protocol) aracılığıyla daha iyi bir şifreleme sunar, ancak daha yeni standartlara kıyasla güvenlik açısından yetersizdir.
*   **WPA2 (WiFi Protected Access II):** WPA üzerinden yapılan çok önemli bir sıçramadır. Sağlam bir güvenlik için **AES (Advanced Encryption Standard)** şifrelemesini kullanır. Uzun yıllardır sektör standardı olarak kalmış ve çoğu ağ için güçlü bir koruma sağlamıştır.
*   **WPA3 (WiFi Protected Access III):** En güncel standarttır. Bireyselleştirilmiş veri şifreleme ve parola tahminlerine/kaba kuvvet saldırılarına karşı çok daha dirençli kimlik doğrulama mekanizmaları ile güvenliği artırır. Şu an uygulanabilen en güvenli seçenektir.

---

### Wi-Fi Sızma Testinin Temelleri (Penetration Testing Fundamentals)

Kablosuz ağlar (Wi-Fi), kurumların dış güvenlik çevresinin (perimeter security) fiziksel olarak en erişilebilir alanlarından biridir. Ancak farklı kimlik doğrulama mekanizmaları ve korumalar, en tecrübeli sızma testi uzmanları için bile zorluklar barındırır. Kapsamlı bir Wi-Fi sızma testi dört temel bileşenden oluşur:

1.  **Parolaların Değerlendirilmesi (Evaluating Passphrases):**
    *   Wi-Fi parolalarının gücünü ve güvenliğini ölçmeyi içerir. 
    *   Sızma testi uzmanları, parolaların yetkisiz erişime karşı direncini kırmak için sözlük saldırıları (dictionary attacks), kaba kuvvet (brute force) saldırıları ve özel parola kırma (password cracking) araçları kullanır.

2.  **Yapılandırmanın Değerlendirilmesi (Evaluating Configuration):**
    *   Güvenlik zafiyetlerini belirlemek için Wi-Fi yönlendiricilerinin (router) ve erişim noktalarının (Access Point - AP) ayarları analiz edilir.
    *   Şifreleme protokolleri, kimlik doğrulama yöntemleri, ağ segmentasyonu (ağ bölümlendirme) ve diğer cihaz parametrelerinin en iyi güvenlik standartlarına (best practices) uyup uymadığı incelenir.

3.  **Altyapının Test Edilmesi (Testing the Infrastructure):**
    *   Wi-Fi ağ altyapısının genel mimari sağlamlığını araştırmaya odaklanır. 
    *   Saldırganlar tarafından ağı tehlikeye atmak için istismar edilebilecek cihaz yapılandırmaları, eski/zayıf donanım yazılımı (firmware) sürümleri ve ağ tasarımındaki mimari zayıflıklar tespit edilir.

4.  **İstemcilerin Test Edilmesi (Testing the Clients):**
    *   Ağa bağlanan dizüstü bilgisayarlar, akıllı telefonlar ve IoT (Nesnelerin İnterneti) cihazları gibi kullanıcı (istemci) cihazlarının güvenliği değerlendirilir.
    *   Saldırganlar için potansiyel arka kapılar yaratabilecek istemci yazılımları, işletim sistemleri, kablosuz ağ sürücüleri (wireless drivers) ve ağ yığını (network stack) uygulamalarındaki zafiyetler test edilir.