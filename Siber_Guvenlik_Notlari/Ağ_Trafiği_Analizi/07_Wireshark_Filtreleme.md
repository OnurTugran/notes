---
title: "Bölüm 7 - Wireshark Filtreleme"
ana-not: "[[00_Ağ_Trafiği_Analizi_İndeks]]"
tarih: 2026-11-25
aliases:
  - Wireshark Filtreleme
  - Capture Filter
  - Display Filter
  - Yakalama Filtresi
  - Görüntüleme Filtresi
  - BPF
tags:
  - siber-guvenlik
  - ag-trafigi-analizi
  - wireshark
---

# Bölüm 7 - Wireshark Filtreleme

> 📚 [İndeks](00_A%C4%9F_Trafi%C4%9Fi_Analizi_%C4%B0ndeks.md) · ◀ [Bölüm 6 - Wireshark ile Ağ Analizi](06_Wireshark_ile_A%C4%9F_Analizi.md)

Wireshark'ta iki ayrı filtre türü vardır. Hangisinin ne zaman uygulandığı en önemli fark:

| | Yakalama filtresi (Capture Filter) | Görüntüleme filtresi (Display Filter) |
|---|---|---|
| **Ne zaman?** | Yakalamadan **önce** | Yakalamadan **sonra** |
| **Etki** | Filtreye uymayan paket **hiç kaydedilmez** | Paketler durur, sadece ekranda gizlenir/gösterilir |
| **Söz dizimi** | BPF (tcpdump ile aynı), sınırlı | Wireshark'a özel, çok geniş |
| **Geri alma** | Yok, kaçan paket geri gelmez | Filtreyi silince her şey geri gelir |
| **Örnek** | `tcp port 80` | `http.request.method == "POST"` |

> [!TIP]
> Emin olmadığında **filtresiz yakala, görüntüleme filtresiyle ayıkla**. Yakalama filtresi yanlış yazılırsa önemli paketi sonsuza dek kaçırırsın. Yakalama filtresini disk/bellek sınırı olan uzun yakalamalarda kullan.

---

## 1. Yakalama Öncesi Filtreleme (Capture Filters)

Paketler ağ arayüzünden alınır alınmaz, Wireshark belleğe almadan **önce** uygulanır. Yakalama başlatmadan önce arabirim seçiminin yanındaki filtre alanına yazılır.

Söz dizimi **BPF (Berkeley Packet Filter)**'dir. [tcpdump notundaki](05_tcpdump_Temelleri.md) filtrelerle birebir aynıdır. Örneğin `tcp port 80` yalnızca TCP port 80 trafiğini yakalar.

### Yapı taşları

**İlkel öğeler (primitives):** Paketin belirli bir alanını belirtir.

| İfade | Anlamı |
|---|---|
| `host 192.168.1.10` | Bu IP'ye sahip host (kaynak ya da hedef) |
| `ether src 00:11:22:33:44:55` | Kaynak MAC adresi |
| `port 80` | Port 80 |
| `tcp`, `udp`, `icmp` | Protokol türü |

**Operatörler:**

| Operatör | Yazılışı |
|---|---|
| ve | `and` veya `&&` |
| veya | `or` veya `\|\|` |
| değil | `not` veya `!` |

**Parantez:** Karmaşık ifadelerde öncelik belirtmek için kullanılır.

### Örnek yakalama filtreleri

| Amaç | Filtre |
|---|---|
| Sadece HTTP trafiği (port 80) | `tcp port 80` |
| Bir IP'ye gelen ve giden tüm trafik | `host 192.168.1.10` |
| DNS veya HTTP | `port 53 or port 80` |
| 192.168.1.10'a giden TCP **dışındaki** her şey | `not tcp dst host 192.168.1.10` |

> [!NOTE]
> `tcp port 80` "HTTP" ile aynı şey değildir, sadece 80. porttaki TCP trafiğini yakalar. HTTPS (443) ve farklı portlarda çalışan HTTP'yi kaçırırsın.

---

## 2. Yakalama Sonrası Filtreleme (Display Filters)

Veri yakalanıp belleğe yüklendikten **sonra** uygulanır. Daha karmaşık aramalar ve ayrıntılı analiz için kullanılır. Filtre çubuğuna yazılır.

> [!NOTE]
> Ders metni görüntüleme filtresini "genişletilmiş BPF söz dizimi" olarak anlatıyor. Aslında görüntüleme filtresi BPF'ye dayanmaz, Wireshark'ın **kendi filtre dilidir** (alan adı + operatör). Bu yüzden `tcp port 80` yakalama filtresidir ama görüntüleme filtresinde `tcp.port == 80` yazılır.

### Yapı taşları

- **Alan adları:** Wireshark her protokol için alan adları tanımlar. Örn: `ip.addr`, `tcp.port`, `http.request.method`.
- **Karşılaştırma operatörleri:**

| Operatör | Anlamı |
|---|---|
| `==` | Eşittir |
| `!=` | Eşit değildir |
| `>` | Büyüktür |
| `<` | Küçüktür |
| `>=` | Büyük veya eşittir |
| `<=` | Küçük veya eşittir |

- **Mantıksal operatörler:** `and` / `&&`, `or` / `||`, `not` / `!`
- **Parantez:** Öncelik belirtmek için.

### Örnek görüntüleme filtreleri

| Amaç                        | Filtre                                                    |
| --------------------------- | --------------------------------------------------------- |
| Sadece HTTP POST istekleri  | `http.request.method == "POST"`                           |
| 192.168.1.0/24 alt ağı      | `ip.addr >= 192.168.1.0 and ip.addr <= 192.168.1.255`     |
| HTTP hataları (4xx ve 5xx)  | `http.response.code >= 400 and http.response.code <= 599` |
| 1000 bayttan büyük paketler | `frame.len > 1000`                                        |

> [!TIP]
> Alt ağ için daha kısa yol: `ip.addr == 192.168.1.0/24`. CIDR gösterimini Wireshark doğrudan anlar.

---

## 3. Protokol Filtreleri

Protokol adını filtre çubuğuna yazmak, o protokolü kullanan tüm paketleri gösterir.

### TCP / UDP

| Filtre | Gösterir |
|---|---|
| `tcp` | Tüm TCP trafiği |
| `udp` | Tüm UDP trafiği |

### HTTP

| Filtre | Gösterir |
|---|---|
| `http` | HTTP kullanan her paket |
| `http.request` | Sadece HTTP istekleri (GET, POST...) |
| `http.response` | Sadece HTTP yanıtları (200 OK, 404 Not Found...) |
| `http.response.code == 200` | Belirli bir durum kodu |

### DNS

| Filtre | Gösterir |
|---|---|
| `dns` | Tüm DNS sorguları ve yanıtları |
| `dns.qry.name == "example.com"` | Belirli bir alan adı için sorgular |
| `dns.flags.response == 1` | Sadece DNS yanıtları |

> [!NOTE]
> Ders metni "yalnızca yanıtları" için `dns.resp.name` filtresini veriyor. Bu alan yanıt kayıtlarındaki isim alanıdır ve yanıt paketlerini dolaylı olarak getirir. Doğrudan "sadece yanıtlar" demek için `dns.flags.response == 1` daha net bir yoldur.

> [!WARNING]
> `http` filtreleri yalnızca **şifresiz** HTTP'yi görür. HTTPS (TLS) trafiğinin içeriği şifreli olduğu için `http` ile görünmez. Bu trafikte `tls` filtresiyle sadece el sıkışma bilgilerini (sunucu adı vb.) inceleyebilirsin.

---

## 4. Gelişmiş Filtreleme

### `contains`: alt dize içerir

Belirli bir metni/karakter dizisini içeren paketleri bulur. **Büyük/küçük harf duyarlıdır.**

```
http contains "example.com"
```

"example.com" geçen HTTP paketlerini getirir.

### `matches`: düzenli ifade (regex)

Bir düzenli ifadeye uyan paketleri bulur. Metin içinde kalıp eşleştirmek için kullanılır.

```
http.request.uri matches "[0-9]{4}$"
```

4 haneli sayıyla biten URL'leri getirir.

> [!NOTE]
> Wireshark'ta `matches` varsayılan olarak büyük/küçük harfe **duyarsızdır** (`contains` ise duyarlıdır). Duyarlı yapmak için ifadenin başına `(?-i)` eklenir.

### `in`: listede mi?

Bir alanın değeri verilen listede mi diye bakar.

```
tcp.port in {80, 443, 53}
```

80, 443 veya 53 numaralı portu kullanan trafiği getirir.

> [!NOTE]
> Eski Wireshark sürümlerinde liste elemanları virgülsüz, boşlukla yazılırdı: `{80 443 53}`. Filtre çubuğu kırmızıya dönerse bu biçimi dene.

---

## Hızlı Özet

| Konu                 | Not                                                                                                                                         |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| Yakalama filtresi    | Yakalamadan önce, BPF, kaçan paket geri gelmez                                                                                              |
| Görüntüleme filtresi | Yakalamadan sonra, Wireshark dili, geri alınabilir                                                                                          |
| Yakalama örneği      | `tcp port 80`, `host 192.168.1.10`, `port 53 or port 80`                                                                                    |
| Görüntüleme örneği   | `http.request.method == "POST"`, `frame.len > 1000`                                                                                         |
| Metin ara            | `contains` (harf duyarlı)                                                                                                                   |
| Regex ara            | `matches`                                                                                                                                   |
| Liste                | `in {80, 443, 53}`                                                                                                                          |
| Diğer sık filtreler  | [Bölüm 6 - Sık Kullanılan Filtreler](06_Wireshark_ile_A%C4%9F_Analizi.md#10-s%C4%B1k-kullan%C4%B1lan-g%C3%B6r%C3%BCnt%C3%BCleme-filtreleri) |

---
> ◀ [Bölüm 6 - Wireshark ile Ağ Analizi](06_Wireshark_ile_A%C4%9F_Analizi.md) · 📚 [İndeks](00_A%C4%9F_Trafi%C4%9Fi_Analizi_%C4%B0ndeks.md) · [Bölüm 8 - İstatistikler](08_Wireshark_%C4%B0statistikler.md) ▶
