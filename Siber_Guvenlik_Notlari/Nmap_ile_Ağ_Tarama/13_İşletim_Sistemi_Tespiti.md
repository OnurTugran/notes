---
title: "Bölüm 13 - İşletim Sistemi Tespiti"
ana-not: "[[00_Nmap_ile_Ağ_Tarama_İndeks]]"
tarih: 2026-11-26
aliases:
  - İşletim Sistemi Tespiti
  - OS Detection
  - nmap -O
tags:
  - siber-guvenlik
  - nmap
  - ag-tarama
  - os-tespiti
  - os-detection
---

# Bölüm 13 - İşletim Sistemi Tespiti

> 📚 [İndeks](00_Nmap_ile_A%C4%9F_Tarama_%C4%B0ndeks.md) · ◀ [Bölüm 12 - Servis Versiyon Tespiti](12_Servis_Versiyon_Tespiti.md)

## 1. İşletim Sistemi Tespiti Neden Önemli?
İşletim sistemi tespiti, bir ağdaki cihazların **hangi işletim sistemlerini çalıştırdığını** belirlemek için kullanılır. Bu bilgi şunlar için önemlidir:
- Güvenlik açıklarını tespit etmek
- Ağ envanteri oluşturmak
- Cihazları keşfetmek
- Bulunan açıkları sömürmek için gerekli araç ve tekniklerin belirlenmesine yardımcı olmak

## 2. Nmap ile İşletim Sistemi Tespiti
Özellik **`-O`** seçeneğiyle etkinleştirilir. Hedef sistemlerin verdiği tepkilere dayanan bir dizi **TCP/IP sınama yöntemi** kullanır. Bu sınamalar sistemin ağ yığınının nasıl davrandığını analiz ederek işletim sistemini **tahmin etmeye** çalışır.

> [!NOTE]
> `-O` seçeneği yardım menüsünün `OS DETECTION` bölümünde yer alır (`-O`, `--osscan-limit`, `--osscan-guess`): [Bölüm 2 - Genel Bakış](02_Genel_Bak%C4%B1%C5%9F.md). `-A` taraması da `-O`'yu içerir: [Bölüm 4 - Port Tarama](04_Port_Tarama.md)

### 2.1 Uygulama
```
root💀hackerbox:~# nmap -O scanme.nmap.org
Starting Nmap 7.94 ( https://nmap.org ) at 2024-03-31 00:11 +03
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.25s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
9929/tcp  open  nping-echo
31337/tcp open  Elite
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Linux 3.X (85%)
OS CPE: cpe:/o:linux:linux_kernel:3.10
Aggressive OS guesses: Linux 3.10 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 27 hops

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 31.97 seconds
```

### 2.2 Çıktıyı Okuma

| Satır | Anlamı |
|---|---|
| `Warning: OSScan results may be unreliable...` | Hedefte en az bir açık ve bir **kapalı** port bulunamadığı için sonuç güvenilir olmayabilir. |
| `Device type: general purpose` | Cihaz türü: genel amaçlı. |
| `Running (JUST GUESSING): Linux 3.X (85%)` | Nmap'in tahmini; %85 güvenle Linux 3.X. |
| `OS CPE` | Tespit edilen işletim sisteminin CPE tanımlayıcısı (`linux_kernel:3.10`). |
| `Aggressive OS guesses: Linux 3.10 (85%)` | Daha agresif tahmin: Linux 3.10. |
| `No exact OS matches...` | Kesin eşleşme bulunamadı (koşullar ideal değil). |
| `Network Distance: 27 hops` | Hedefe olan ağ mesafesi: 27 hop. |

> [!TIP]
> Bu örnekte sonuç kesin değil, bir **tahmin**. Nmap, doğru tespit için en az bir açık ve bir kapalı port bulmak ister; burada 996 port filtrelenmiş olduğu için bu şart sağlanamadı. Bölüm 12'de `-sV` çıktısındaki `Service Info: OS: Linux` satırı da aynı yönü gösterir: [Bölüm 12 - Servis Versiyon Tespiti](12_Servis_Versiyon_Tespiti.md)

## 3. İşletim Sistemi Tespiti İçin İpuçları
- **Çoklu denemeler:** Tespit her zaman %100 doğru olmaz. Ağ koşulları veya hedefteki özel yapılandırmalar yüzünden ilk denemede kesin sonuç alınamayabilir. Birden fazla deneme doğruluğu artırabilir.
- **Ağ mesafesi:** Nmap işletim sistemi tespiti sırasında ağ mesafesini de tahmin edebilir. Bu, hedefe olan hop sayısını verir ve ağ topolojisi hakkında fikir verir.
- **Diğer özelliklerle birleştirme:** `-O`'yu servis versiyon tespiti (`-sV`) veya ağ taraması (`-sn`) gibi özelliklerle birleştirmek daha geniş bir ağ analizi sağlar.
- **Sonuçları doğrulama:** Sonuçları mümkünse fiziksel erişim veya başka yöntemlerle doğrula. Bu, yanlış pozitif ve negatif sonuçları azaltır.
- **Güvenlik ve etik:** İşletim sistemi tespiti ağdaki diğer sistemlere müdahale içerebilir. Tarama yapmadan önce ilgili yasal gereklilikleri ve etik standartları göz önünde bulundur.

> [!WARNING]
> Örnek hedef (`scanme.nmap.org`) yalnızca gösterim içindir. Taramayı yalnızca izinli hedeflerde ve kendi lab ortamında yap.

---
> 📚 [İndeks](00_Nmap_ile_A%C4%9F_Tarama_%C4%B0ndeks.md) · ◀ [Bölüm 12 - Servis Versiyon Tespiti](12_Servis_Versiyon_Tespiti.md) · [Bölüm 14 - NSE](14_Nmap_Scripting_Engine.md) ▶
