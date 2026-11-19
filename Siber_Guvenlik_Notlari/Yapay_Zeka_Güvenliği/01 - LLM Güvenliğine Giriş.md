---
title: LLM Güvenliğine Giriş
bolum: 1
tarih: 2026-11-18
ana-not: "[[00 - Yapay zeka]]"
aliases:
  - LLM Güvenliğine Giriş
  - LLM Giriş
tags:
  - siber-guvenlik
  - yapay-zeka
  - llm-guvenligi
  - transformer
  - tokenization
  - attention
  - llm-egitimi
---

# LLM Güvenliğine Giriş

> 📚 [Ana Sayfa (MOC)](00%20-%20Yapay%20zeka.md) · [Yapay Zeka Güvenliği Neden Farklı?](02%20-%20Yapay%20Zeka%20G%C3%BCvenli%C4%9Fi%20Neden%20Farkl%C4%B1.md) ▶

> [!NOTE]
> **Özet**
> Büyük Dil Modellerine (LLM) nasıl saldıracağımızı veya onları nasıl savunacağımızı anlamak için önce içeriden nasıl çalıştıklarını bilmek gerekir. Bu not; mimariyi, mekanizmaları ve temel kavramları **güvenlik odaklı** ele alır.

## İçindekiler
- [[#LLM (Large Language Model) Nedir?]]
- [[#Transformer Mimarisi]]
- [[#Tokenizasyon (Tokenization)]]
- [[#Attention Mekanizması]]
- [[#LLM Eğitiminin Aşamaları]]
- [[#LLM Davranışını Etkileyen Temel Parametreler]]
- [[#Sistem Prompt ve Kullanıcı Prompt'u]]
- [[#LLM Güvenliği Neden Önemlidir?]]

---

## LLM (Large Language Model) Nedir?

**Büyük Dil Modeli (LLM)**, insan benzeri dili tahmin etmek ve üretmek için büyük miktarda metin verisiyle eğitilmiş bir yapay zeka sistemidir. ChatGPT, Claude veya Gemini'ye soru yazıldığında model insan gibi "düşünmez" veya "anlamaz". Çok karmaşık bir istatistiksel hesap yapar:

> [!NOTE]
> **Temel soru**
> Eğitim sırasında gördüğü tüm metinlere göre, üretilecek **bir sonraki token** (kelime veya alt kelime) hangisidir?

### Güvenlik profesyonelleri için kritik noktalar
- Modelin **doğruluk kavramı** yok.
- **Gizlilik bilinci** yok.
- **Yerleşik bir etik pusulası** yok.
- "Biliyor" veya "inanıyor" gibi görünen her şey, milyarlarca parametre boyunca örüntü tanımanın ortaya çıkan bir özelliğidir.
- Model tehlikeli bir soruyu reddettiğinde ahlaki yargı uygulamaz; **uyum eğitimi (alignment)** aşamasında öğrendiği kalıpları takip eder.

> [!WARNING]
> **Sonuç**
> Reddetme davranışı öğrenilmiş bir kalıp olduğu için, prensipte **hizalama atlanabilir**. Bkz. [[Jailbreak]], [[Prompt Injection]].

### Model ailelerinin manzarası
Parametre sayısı, context window, multimodal yetenekler ve araç entegrasyonları sağlayıcıya ve sürüme göre değişir. Bu yüzden güvenlik değerlendirmesi belirli bir model adına değil, **modelin gerçek yeteneklerine, entegrasyonlarına ve yetki sınırlarına** dayanmalıdır.

| Sağlayıcı | Öne çıkan modeller |
|---|---|
| OpenAI | GPT-5.5 (geniş kullanım, flagship), GPT-5.6 (sınırlı preview) |
| Anthropic | Claude Fable 5, Claude Opus 4.8, Claude Sonnet 5, Claude Haiku 4.5 |
| Google | Gemini 3.x / 3.5 ailesi |
| Meta | Llama 4 Scout, Llama 4 Maverick (open-weight) |

> [!NOTE]
> **Not**
> Bu liste bir eğitim snapshot'ıdır. Gerçek bir değerlendirmede model adı, context window, cutoff, tool desteği ve veri kullanım kuralları **sağlayıcının resmi dokümantasyonundan** ve test edilen dağıtımdan doğrulanmalıdır.

---

## Transformer Mimarisi

Her modern LLM, Vaswani ve arkadaşlarının 2017 tarihli **"Attention Is All You Need"** makalesindeki Transformer mimarisi üzerine kuruludur. [[Prompt Injection]]'dan eğitim verisi çıkarımına kadar birçok açık, bu mimarinin bilgiyi işleme biçimiyle yakından ilişkilidir.

### Transformer öncesindeki sorun
Önceden baskın mimariler **RNN** ve **LSTM**'di. Metni soldan sağa, her defasında bir kelime olarak sırayla işliyorlardı. İki büyük sorun vardı:

1. **Vanishing gradients (sönümlenen gradyanlar):** Uzun cümlenin sonunda başında ne olduğunu büyük ölçüde "unutuyordu".
2. **Paralelleştirme yok:** Her kelime bir öncekine bağlı olduğu için eğitim çok yavaştı, GPU paralelliğinden yararlanılamıyordu.

Transformer, **self-attention (öz-dikkat)** ile ikisini de çözdü: model dizideki tüm tokenlara aynı anda bakar ve hangilerinin birbiriyle alakalı olduğunu belirler.

### Transformer nasıl çalışır?

```mermaid
flowchart TD
    A[Ham metin] --> B[1. Tokenization]
    B --> C[2. Embedding]
    C --> D[3. Positional Encoding]
    D --> E[4. Self-Attention]
    E --> F[5. Multi-Head Attention]
    F --> G[6. Feed-Forward Network]
    G --> H{7. Katman yığınlama<br/>4-6 adımları tekrar}
    H -->|tekrar| E
    H --> I[8. Output Projection]
    I --> J[Sonraki token]
```

#### 1. Tokenization
Ham metin tokenlere bölünür. Detayı: [[#Tokenizasyon (Tokenization)]].

#### 2. Embedding (Vektör Temsili)
Her token yüksek boyutlu sayısal bir vektöre dönüştürülür (tipik **768 ila 12.288 boyut**). Benzer kelimeler benzer vektörlere sahip olur.
- Örnek: "kral" ve "kraliçe" vektörleri birbirine, "kral" ve "bisiklet"ten daha yakındır.

#### 3. Positional Encoding (Konumsal Kodlama)
Transformer tüm tokenları aynı anda işlediği için doğal bir sıra anlayışı yoktur. Konumsal kodlama her token'ın dizideki konumunu ekler. Olmasaydı model şu iki cümleyi aynı sayardı:
- "Paspasın üzerine oturan kedi"
- "Paspas kedinin üzerine oturdu"

#### 4. Self-Attention (Öz-Dikkat)
Temel mekanizma. Her token için üç vektör hesaplanır:

| Vektör | Anlamı |
|---|---|
| **Query (Q)** | "Neyi arıyorum?" |
| **Key (K)** | "İçimde ne var?" |
| **Value (V)** | "Hangi bilgileri sağlıyorum?" |

İki token arasındaki **attention score**, birinin Query'si ile diğerinin Key'inin **dot product** değeriyle hesaplanır. Yüksek skor = yüksek alaka. Her token'ın nihai çıktısı, ağırlıkları attention score olan tüm Value vektörlerinin **ağırlıklı toplamıdır**.

> [!NOTE]
> **Bağlamsal anlam örnekleri**
> **"Nehir kenarındaki kıyı sular altında kaldı"**
> "kıyı" işlenirken "Nehir" ve "sular altında kaldı" ifadelerine yüksek attention → *riverbank* (coğrafi bağlam)
>
> **"Banka kredi başvurumu onayladı"**
> "Banka" işlenirken "kredi" ve "onayladı" ifadelerine yüksek attention → *financial institution*
>
> Aynı kelime bağlama göre farklı temsil alır. Buna **bağlamsal** anlayış denir.

#### 5. Multi-Head Attention (Çok Başlıklı Dikkat)
Attention paralel olarak birden çok kez hesaplanır (genelde **12 ila 96 head**). Her head farklı ilişkiye odaklanabilir:
- Sözdizimsel (özne-fiil uyumu)
- Anlamsal (konu tutarlılığı)
- Konumsal (yakındaki kelimeler)

#### 6. Feed-Forward Network (İleri Beslemeli Ağ)
Attention sonrası her token temsili bir feed-forward sinir ağı katmanından geçer. Doğrusal olmayan dönüşümlerle attention'ın tek başına yakalayamadığı karmaşık örüntüleri yakalar.

#### 7. Layer Stacking (Katman Yığınlama)
4-6. adımlar tipik olarak **32 ila 96 kez** tekrarlanır. Her katman daha fazla soyutlama ekler:
- İlk katmanlar: gramer, sözdizimi gibi yüzey kalıpları
- Sonraki katmanlar: anlam bilimi, akıl yürütme, dünya bilgisi

#### 8. Output Projection (Çıktı Projeksiyonu)
Son katmanın çıktısı tüm sözcük dağarcığı üzerinde bir **olasılık dağılımına** yansıtılır. Model bir sonraki tokenı bu olasılıklara göre seçer.

### Bu güvenlik açısından neden önemli?

> [!CAUTION]
> **Güvenlik sonuçları**
> 1. **Attention bağlama bağlıdır.** Model her tokenı context window'daki diğer tüm tokenlara göre yorumlar. Saldırgan, attention weight'lerini değiştiren girdiler hazırlayarak modelin system prompt'u yorumlama biçimini etkileyebilir.
> 2. **Ayrıcalıklı bellek bölgesi yoktur.** CPU'daki çekirdek/kullanıcı alanı ayrımının aksine Transformer, context window'daki tüm tokenları eşit ele alır. System prompt, kullanıcı mesajları ve getirilen belgeler **aynı dikkat alanındadır**. [[Prompt Injection]]'ın işe yaramasının temel nedeni budur.
> 3. **Katman katman işleme fırsatlar yaratır.** Her katman temsili dönüştürür; adversarial girdiler belirli katmanları hedefleyebilir, farklı saldırılar pipeline'ın farklı düzeylerini vurur.

---

## Tokenizasyon (Tokenization)

Ham metni modelin fiilen işlediği **sayısal tokenlere** dönüştürme işlemidir. Token kaçakçılığı ve token limiti istismarı gibi tekniklerin anlaşılması için kritiktir.

### Nasıl çalışır?
Modern LLM'ler metni karakter veya kelime kelime işlemez. **BPE (Byte Pair Encoding)**, **WordPiece** veya **SentencePiece** gibi alt kelime algoritmaları metni sık görülen alt kelime birimlerine böler.

**Örnek:**
- Giriş: `Siber güvenlik, yapay zeka güvenliği için önemlidir`
- Tokenlar: `["Siber", "güvenlik", "önemlidir", "için", "Yapay Zeka", "güvenlik"]`
- Token ID'leri: `[34424, 22706, 374, 3062, 369, 15592, 7296]`

**Diğer örnekler:**
| Metin | Tokenlar |
|---|---|
| `unhappiness` | `["un", "happiness"]` (iki ayrı token) |
| `ChatGPT` | `["Chat", "GPT"]` |
| `sk-proj-abc123` | `["sk", "-", "proj", "-", "abc", "123"]` |

### Tokenizasyonun güvenlik etkileri

> [!CAUTION]
> **Token Sınırı İstismarı**
> Her modelin karakterle değil **tokenla** ölçülen maksimum context window'u vardır. Tokenization'ı anlayan saldırgan, minimum görünür karakterle maksimum token tüketen girdiler üretebilir (veya tersi).

> [!CAUTION]
> **Token Kaçakçılığı (Token Smuggling)**
> Tokenizasyon karakterleri beklenmedik şekillerde bölüp birleştirebildiği için anahtar kelimeye dayalı filtreler atlatılabilir. Örneğin "bomba" arayan bir filtre, saldırgan `b-o-m-b` yazarsa tokenizer farklı tokenlar ürettiği için bunu yakalayamayabilir.

> [!CAUTION]
> **Dil Farklılıkları**
> İngilizce genelde verimlidir (kelime başına ~1 token). Çince, Japonca, Korece gibi diller kavram başına daha fazla token kullanabilir. Bu hem context window kullanımını hem de çoğunlukla İngilizce veriyle eğitilmiş **güvenlik filtrelerinin** etkinliğini etkiler.

**Dil bazında örnekler:**
- Türkçe `Merhaba` → yaklaşık **1 token**
- Çince `你好` → çoğu tokenizer'da **2 token**
- Arapça `مرحبا` → tokenizer'a bağlı olarak yaklaşık **3-5 token**

> [!TIP]
> **Çıkarım**
> Ağırlıklı olarak İngilizce örüntülerle eğitilmiş güvenlik filtreleri, diğer dillerdeki zararlı içeriği tespit etmede daha az etkili olabilir.

---

## Attention Mekanizması

LLM güvenlik açıklarını anlamak için en önemli kavramlardan biridir.

### Scaled Dot-Product Attention

$$
\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right) V
$$

| Sembol | Anlamı |
|---|---|
| `Q` | Query matrisi, her token'ın ne aradığı |
| `K` | Key matrisi, her token'ın ne sunduğu |
| `V` | Value matrisi, taşınan asıl bilgi |
| `d_k` | Key vektör boyutu (ölçekleme faktörü) |
| `softmax` | Ham skorları toplamı 1 olan olasılıklara çevirir |

> [!NOTE]
> **Neden `sqrt(d_k)`'ya bölünür?**
> Nokta çarpımlarının aşırı büyümesini önler. Aksi halde softmax çok keskinleşir: bir token neredeyse tüm attention'ı alır, diğerleri neredeyse hiç katkı vermez.

### Context Window (Bağlam Penceresi)
Modelin aynı anda işleyebileceği **toplam token sayısı**. Şunların hepsini kapsar:
- System prompt
- Konuşma geçmişi
- Getirilen belgeler
- Kullanıcı girişi
- Modelin kendi ürettiği çıktı

| Model / mimari tipi | Tipik context yaklaşımı |
|---|---|
| Küçük veya yerel instruction modelleri | Binlerce ila on binlerce token |
| Yaygın hosted chat modelleri | On binlerce ila yüz binlerce token |
| Uzun bağlamlı hosted modeller | Yüz binlerce ila bir milyondan fazla token |
| Özel retrieval mimarileri | Uzun bağlamı harici bellek veya [[RAG]] ile birleştirebilir |

> [!NOTE]
> **Doğrulama**
> Kesin limitler sağlayıcıya, model sürümüne, hesap katmanına ve API moduna göre sık değişir. Statik eğitim materyaline güvenmek yerine test edilen dağıtımın dokümante edilmiş limitini doğrula.

> [!CAUTION]
> **Güvenlik açısından**
> Context window, **erişim kontrolü olmayan paylaşılan bir alandır**. Model, güvenilir system prompt tokenları ile güvenilmeyen kullanıcı girdisi tokenları arasında mimari olarak ayrım yapamaz; hepsi aynı attention hesaplamalarına katılır. Bu, [[Prompt Injection]]'ın temel nedenidir.

### Causal (Autoregressive) Attention
Metin üreten LLM'ler causal attention kullanır: **N. token üretilirken model yalnızca 1'den N-1'e kadar olan tokenlara bakabilir**. Geleceğe bakamaz. Bu, gelecekteki pozisyonları engelleyen bir **attention mask** ile uygulanır.

> [!NOTE]
> **Örnek**
> "Kedi ___ üzerine oturdu" cümlesinde boşluktaki token tahmin edilirken yalnızca boşluktan öncekiler görülür. Sonrası henüz üretilmediği için attention'a katılmaz.

LLM'lerin soldan sağa, her seferinde bir token üretmesinin nedeni budur.

> [!WARNING]
> **Güvenlik etkisi**
> Modelin davranışı **aşamalı olarak etkilenebilir**. Saldırgan, asıl kötü amaçlı isteği yapmadan önce birden fazla aşamada kademeli bağlam oluşturarak modelin "dikkat ortamını" yönlendirebilir (bkz. [[Multi-turn Jailbreak]]).

---

## LLM Eğitiminin Aşamaları

Her eğitim aşaması belirli güvenlik hususları ortaya koyar.

```mermaid
flowchart LR
    A[Pre-training] --> B[SFT]
    B --> C[RLHF / DPO]
    C --> D[Constitutional AI]
```

### Aşama 1: Pre-training (Eğitim Öncesi)
Model, terabaytlarca web sayfası, kitap, kod ve akademik makaleden oluşan veriyle eğitilir. Amaç basit: **bir sonraki tokenı tahmin etmek**.

**Güvenlik etkileri:**
- Model, hassas bilgileri (e-posta adresleri, telefon numaraları, API anahtarları, kod parçaları) **ezberleyebilir**.
- Eğitim verisindeki sapmalar (bias) parametrelere gömülür.
- Eğitim verisinde talimat metni bulunduğu için model talimatları izlemeyi öğrenir.

### Aşama 2: Supervised Fine-Tuning (SFT)
Model, soru-cevap çiftleri ve talimat takip örneklerinden oluşan seçilmiş veri kümeleriyle fine-tune edilir. İnsan değerlendiriciler yüksek kaliteli örnekler hazırlar.

**Güvenlik etkileri:**
- Fine-tuning verisinin kalitesi güvenliği doğrudan etkiler. **Zehirli (poisoned) veri backdoor davranışlarına** yol açabilir.
- Model istismar edilebilecek ölçüde "yardımcı" olmayı öğrenebilir, yani yanıtlamaması gerekenleri yanıtlayabilir.

### Aşama 3: RLHF (İnsan Geri Bildiriminden Takviyeli Öğrenme)
Model aynı prompt için birden fazla yanıt üretir, insanlar bunları sıralar. Sıralamalardan bir **reward model** eğitilir, sonra LLM reward model'den yüksek puan alacak şekilde optimize edilir.

**Güvenlik etkileri:**
- RLHF, zararlı istekleri reddetmeyi öğreten birincil mekanizmadır. Ancak bu **davranışsal bir değişiklik**tir, temel bir yetenek kısıtlaması değildir.
- Model "yüzey düzeyinde" reddetme kalıpları öğrenir; RLHF'in kapsamadığı şekilde yeniden ifade edilen istekler bunu atlatabilir.
- **Reward Hacking:** Model, gerçekte güvenli/yararlı olmasa da reward modelden yüksek puan alan yanıtlar üretmeyi öğrenebilir.

### Aşama 4: Constitutional AI / DPO
- **Constitutional AI:** Modeli kendi çıktılarını bir dizi **ilkeye** göre değerlendirecek şekilde eğitir.
- **DPO (Direct Preference Optimization):** Ayrı reward model olmadan tercih verisinden doğrudan optimizasyon yapan, RLHF'e daha basit bir alternatiftir.

**Güvenlik etkileri:**
- Ek güvenlik katmanı ekler ama temel mimari açıkları ortadan kaldırmaz.
- Kullanılan hizalama tekniğini anlayan saldırgan, onun zayıf yönlerini hedefleyen promptlar kurabilir.

### Training pipeline özeti
| Aşama | Kazandırdığı şey |
|---|---|
| Pre-training | Ham bilgi + next-token prediction |
| Supervised Fine-Tuning | Talimat takip etme davranışı |
| RLHF / DPO | Neyi reddedeceğini öğrenme, safety alignment |
| Constitutional AI | İlkelere göre öz değerlendirme |

> [!CAUTION]
> **Kritik çıkarım**
> Her katman güvenliği artırır ama **hiçbiri mimari olarak zorunlu değildir**. Tüm güvenlik önlemleri modelin parametre alanında öğrenilmiş davranışlardır; ayrı bir mimari yetki sınırı oluşturmazlar.

---

## LLM Davranışını Etkileyen Temel Parametreler

API üzerinden etkileşimde çıktıyı birkaç parametre kontrol eder. Hem saldırganlar (manipüle edebilir) hem savunanlar (güvenli yapılandırmalı) için önemlidir.

### Temperature (Sıcaklık)
Çıktının rastgeleliğini kontrol eder; token seçiminden önce olasılık dağılımını değiştirir.

| Değer | Davranış | Kullanım |
|---|---|---|
| `0.0` | Deterministik, hep en olası token | Gerçek görevler, kod üretimi, güvenlik açısından kritik uygulamalar |
| `0.7` | Dengeli, biraz rastgelelik | Yaratıcı yazma, sohbet uygulamaları |
| `1.5+` | Yüksek rastgelelik, tutarsız/beklenmedik | Üretim sistemleri için riskli |

> [!WARNING]
> **Güvenlik etkisi**
> Yüksek temperature, modelin güvenlik eğitimini atlayan çıktılar üretmesine yol açabilir. Güvenlik davranışları öğrenilmiş pattern'lerdir; yüksek temperature'da düşük olasılıklı ve potansiyel olarak güvensiz token dizileri daha olası hale gelir.

### Top-p (Nucleus Sampling)
Token seçimini, kümülatif olasılığı `p` eşiğini aşan **en küçük token setiyle** sınırlar.

- `top_p = 0.1` → yalnızca ilk %10 olasılık kütlesi; odaklı, öngörülebilir
- `top_p = 0.9` → ilk %90; daha çeşitli
- `top_p = 1.0` → tüm tokenlar; maksimum çeşitlilik

### Top-k
Token seçimini **en olası k token** ile sınırlar.

- `top_k = 1` → tek token; neredeyse tam deterministik
- `top_k = 40` → en olası 40 token arasından seçim
- `top_k = 100` → en olası 100 token arasından seçim

### Max Tokens
Yanıtta üretilecek maksimum token sayısı. **Sert bir kesintidir.**

> [!WARNING]
> **Güvenlik etkisi**
> - **Çok yüksek:** Saldırgan çok uzun çıktı ürettirip API kredilerini ve işlem kaynaklarını tüketebilir (bir tür **DoS**).
> - **Çok düşük:** Güvenlikle ilgili sorumluluk reddi beyanları veya talimatlar kesilebilir.

### Stop Sequences (Durdurma Dizileri)
Üretildiğinde modelin üretimini durduran metin dizileri.

> [!WARNING]
> **Güvenlik etkisi**
> Stop sequence'leri bilen saldırgan, modeli bu diziyi erken üretmeye kandırıp önemli **güvenlik uyarılarını kısaltabilir**.

---

## Sistem Prompt ve Kullanıcı Prompt'u

Çoğu modern LLM API'si system prompt ile user prompt'u ayırır:

```python
messages = [
    {"role": "system", "content": "Siz BankCorp için yardımcı bir müşteri hizmetleri temsilcisisiniz. Hesap numaralarını veya şifrelerini asla açıklamayın. Hesap ayrıntılarını konuşmadan önce daima kimliği doğrulayın."},
    {"role": "user", "content": "Hesap bakiyem nedir?"},
    {"role": "assistant", "content": "Bakiyenizi kontrol etmenize yardımcı olmaktan memnuniyet duyarım. Güvenlik nedeniyle lütfen önce kimliğinizi doğrulayabilir misiniz?"}
]
```

> [!CAUTION]
> **Güvenlik etkisi**
> Rol ayrımına rağmen system prompt ve user prompt, modelin context window'unda **aynı token dizisi** içindedir. Model system prompt'lara daha yüksek öncelik vermeyi öğrenmiştir, ancak bu **mimari bir zorunluluk değil, davranışsal bir pattern**'dir. Bu yüzden **system prompt çıkarma** ve **override** saldırıları mümkündür.

---

## LLM Güvenliği Neden Önemlidir?

LLM'lerin kritik uygulamalara hızla konuşlandırılması yeni ve benzeri görülmemiş bir saldırı yüzeyi yarattı.

### LLM'lerin kullanıldığı alanlar
| Alan | Kullanım |
|---|---|
| Sağlık hizmetleri | Hasta önceliklendirme, tıbbi kayıt özetleme, sağlık soruları |
| Finansal hizmetler | Kredi başvurusu işleme, dolandırıcılık tespiti, müşteri danışmanlığı |
| Hukuk teknolojisi | Sözleşme analizi, hukuki özet, araştırma |
| Müşteri hizmetleri | Şikayet, iade, hesap yönetimi |
| Yazılım geliştirme | Kod yazma/inceleme, altyapı yönetimi, hata ayıklama |
| Devlet ve savunma | İstihbarat analizi, belge işleme, karar desteği |

### Örnek riskler
- Müşteri hizmetleri botuna yönelik [[Prompt Injection]] → **müşteri verisi sızıntısı**
- Sağlık yapay zekasına saldırı → **tehlikeli tıbbi tavsiye**
- Kod asistanına saldırı → **üretim yazılımında güvenlik açıkları**

### Gerçek dünya olayları
> [!NOTE]
> **Teorik değil, gerçek**
> - **2023:** Bir Chevrolet bayisi sohbet robotu, hedef kaçırma (goal hijacking) saldırısıyla bir arabayı **1 dolara** satmayı kabul edecek şekilde kandırıldı.
> - **Samsung:** Çalışanlar özel kaynak kodunu ChatGPT'ye yapıştırarak yanlışlıkla sızdırdı.
> - **Araştırmacılar:** ChatGPT'nin kişisel olarak tanımlanabilir bilgiler dahil **eğitim verilerini aynen yeniden üretecek** şekilde kullanılabileceğini gösterdi.
> - **Microsoft "Sydney"** (Copilot öncesi sohbet deneyimi): Lansmandan kısa süre sonra **jailbreak** ve **system prompt sızıntısı** örnekleriyle gündeme geldi.

> [!TIP]
> **Sonuç**
> LLM güvenliği alanı hızla gelişiyor. Temel kavramları doğru anlamak; saldırı yüzeylerini tanımlamak, güvenlik risklerini değerlendirmek ve uygulanabilir azaltma stratejileri geliştirmek için kritiktir.

---

## İlgili Notlar
- [[Prompt Injection]]
- [[Jailbreak]]
- [[Multi-turn Jailbreak]]
- [[RAG]]
- [[Siber Güvenlik]]

#siber-guvenlik #llm #yapay-zeka

---
> 📚 [Ana Sayfa (MOC)](00%20-%20Yapay%20zeka.md) · [Yapay Zeka Güvenliği Neden Farklı?](02%20-%20Yapay%20Zeka%20G%C3%BCvenli%C4%9Fi%20Neden%20Farkl%C4%B1.md) ▶
