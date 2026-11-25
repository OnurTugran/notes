---
title: "Bölüm 9 - Wireshark Analiz"
ana-not: "[[00_Ağ_Trafiği_Analizi_İndeks]]"
tarih: 2026-11-25
aliases:
  - Wireshark Analiz
  - Follow TCP Stream
  - Export Objects
  - TCP akışı takip
tags:
  - siber-guvenlik
  - ag-trafigi-analizi
  - wireshark
---

# Bölüm 9 - Wireshark Analiz

> 📚 [İndeks](00_A%C4%9F_Trafi%C4%9Fi_Analizi_%C4%B0ndeks.md) · ◀ [Bölüm 8 - Wireshark İstatistikler](08_Wireshark_%C4%B0statistikler.md)

Wireshark'ın analiz özellikleri, tek tek paketlere bakmak yerine **bir konuşmayı bütün olarak** incelemeni sağlar. Bu bölümde üç şey var: TCP akışını takip etmek, tek bir akışı filtrelemek ve yakalamadan dosya çıkarmak.

| Özellik | Ne işe yarar | Nereden |
|---|---|---|
| Follow TCP Stream | Bir bağlantının tüm konuşmasını tek pencerede okursun | Paket → sağ tık → Follow → TCP Stream |
| Akışı filtreleme | Sadece o konuşmanın paketlerini listede bırakırsın | Apply as Filter → Selected |
| Export Objects | HTTP/FTP ile aktarılan dosyaları çıkarırsın | File → Export Objects |

---

## 1. TCP Akışlarını Takip Etme (Follow TCP Stream)

Bir TCP bağlantısındaki **tüm paketleri tek pencerede** toplar. Ham paket verisi yerine, ilgili protokolün yorumladığı **konuşmanın içeriğini** görürsün (örneğin bir HTTP isteği ve sunucunun yanıtı art arda okunur).

**Adımlar**
1. TCP akışına ait bir pakete **sağ tıkla**.
2. **Follow → TCP Stream** seç.
3. Açılan pencerede konuşmanın yorumlanmış içeriği görünür.

> [!TIP]
> Pencerede istemci ve sunucu tarafı farklı renkte gösterilir (genelde istemci kırmızı, sunucu mavi). Aşağıdaki açılır listeden tek bir yönü ya da tüm konuşmayı seçebilirsin.

> [!WARNING]
> Şifresiz protokollerde (HTTP, FTP, Telnet) kullanıcı adı, parola ve içerik bu pencerede **açık metin** olarak okunur. Bu, şifreli protokollerin (HTTPS, SSH) neden gerekli olduğunu gösterir. Başkasının trafiğini yalnızca yetkin olduğun ağda incele.

---

## 2. Belirli Bir TCP Akışını Filtreleme

Yoğun trafik içinde tek bir konuşmaya odaklanmayı kolaylaştırır. Bir TCP akışıyla ilgili **tüm paketleri** ayırmak istediğinde kullanılır.

**Adımlar**
1. Akıştaki bir pakete sağ tıkla.
2. **Apply as Filter → Selected** seç.
3. Wireshark yalnızca o filtreye uyan paketleri gösterir.

**Alternatif:** **Prepare as Filter** seçeneği filtreyi filtre çubuğuna yazar ama uygulamaz. Düzenleyip kendin Enter'a basarsın.

> [!NOTE]
> Ders metnindeki iki noktayı netleştireyim:
> - **Apply as Filter → Selected**, sağ tıkladığın **alanı** filtreler (örneğin sadece o kaynak IP ya da o port). Bütün akışı bir seferde izole etmenin en kolay yolu **Follow → TCP Stream** açmaktır. Wireshark bu sırada otomatik olarak `tcp.stream eq N` filtresini uygular. Pencereyi kapatınca liste o akışa daralmış kalır.
> - **Prepare as Filter** filtre **butonu** oluşturmaz, sadece filtreyi çubuğa yazar. Buton için filtre çubuğunun sağındaki **+** simgesini ya da filtre ifadesine sağ tıklayıp kaydetme seçeneğini kullan.

Akışı doğrudan filtreyle de seçebilirsin:

```
tcp.stream eq 5
```

Akış numarası, paket detayında TCP satırının altında `[Stream index: 5]` olarak yazar.

---

## 3. Arayüzden Dosya Çıkarma

Wireshark, **HTTP, FTP** gibi protokollerle aktarılan dosyaları doğrudan yakalama dosyasından geri çıkarabilir.

### Yöntem 1: Export Objects

1. **File → Export Objects → HTTP** seç.
2. Listeden istediğin dosyayı seç.
3. **Save** ile kaydet.

Aynı menüde HTTP dışında başka protokoller de vardır (SMB, TFTP, IMF gibi), hangileri çıkarılabilir diye menüye bakabilirsin.

### Yöntem 2: Follow Stream ile kaydetme

1. Dosya içeren pakete sağ tıkla.
2. **Follow → protokol** seç (örneğin **HTTP Stream**).
3. Açılan pencerede **Save As** butonuna tıkla.
4. Dosyayı kaydetmek için konum seç.

> [!NOTE]
> Follow Stream ile kaydedince akışın **ham verisi** kaydedilir, HTTP başlıkları (header) da dosyanın içinde kalır. Temiz bir dosya istiyorsan **Export Objects** daha uygundur, o başlıkları ayıklayıp sadece dosya gövdesini verir.

> [!WARNING]
> **Çıkardığın dosyalara dikkat.** Yakalamadan çıkan dosya zararlı yazılım olabilir. Açmadan önce:
> - Çift tıklayıp çalıştırma. Analizi izole bir sanal makinede yap.
> - Hash değerini hesaplayıp (`sha256sum dosya`) bilinen zararlı veritabanlarıyla karşılaştır. Bkz. [Hash Fonksiyonları](../Kriptoloji/05_Hash_Fonksiyonlar_ve_Algoritmalar%C4%B1.md).
> - Dosya yalnızca **şifresiz** aktarımdan çıkar. HTTPS ile gelen dosya şifreli olduğu için Export Objects ile görünmez.

---

## Hızlı Özet

| İş | Yol |
|---|---|
| Konuşmayı baştan sona oku | Sağ tık → Follow → TCP Stream |
| Tek akışa filtrele | `tcp.stream eq N` ya da Follow Stream |
| Alanı filtrele | Sağ tık → Apply as Filter → Selected |
| Filtreyi yaz ama uygulama | Prepare as Filter |
| HTTP'den dosya çıkar | File → Export Objects → HTTP |
| Akışı olduğu gibi kaydet | Follow → HTTP Stream → Save As |

---
> ◀ [Bölüm 8 - Wireshark İstatistikler](08_Wireshark_%C4%B0statistikler.md) · 📚 [İndeks](00_A%C4%9F_Trafi%C4%9Fi_Analizi_%C4%B0ndeks.md)
