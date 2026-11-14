
İlk olarak bir kaç skil indirdim özellikle matt pocock un mühendisler için yaptığı skili bunu kullanmam gereken bir skil kullanmam gerekçeği zaman claude terminaline 

```
claude
/setup-matt-pocock-skills
```

o proje içindeki terminalde yazmamız gerekli.

Matt Pocock'un **"Skills for Real Engineers"** deposundaki beceriler, yapay zekayı rastgele kod üreten bir araçtan çıkarıp disiplinli, test odaklı ve mimari kararlar alan bir mühendislik asistanına dönüştürmek için tasarlanmıştır.

Bu beceriler iki ana gruba ayrılır: Sadece senin komutunla çalışan **User-invoked** (Kullanıcı komutlu) araçlar ve hem senin çağırabileceğin hem de ajanın ihtiyaç duyduğunda arkada otomatik devreye sokabileceği **Model-invoked** (Model komutlu) disiplinler.

Depodaki tüm becerilerin eksiksiz listesi ve ne işe yaradıkları:

### 1. Engineering (Kod ve Yazılım Geliştirme Becerileri)

Günlük kod yazma, mimari tasarlama ve hata ayıklama süreçlerinde kullanılan temel araçlardır.

#### User-invoked (Komutla Çalışanlar)

- **`/setup-matt-pocock-skills`**: Proje bazlı ilk kurulum komutudur. Projede kullanılacak issue tracker tercihini (GitHub, GitLab, yerel dosyalar) ve doküman yollarını yapılandırır. Her yeni projede bir kez çalıştırılır.
    
- **`/ask-matt`**: Mevcut durumun veya sorunun için hangi becerinin veya akışın uygun olduğunu sana söyleyen bir yönlendiricidir (router).
    
- **`/grill-with-docs`**: Kod yazmadan veya büyük bir değişiklik yapmadan önce seni sorguya çeken ("grill") detaylı bir mülakat seansıdır. Sürecin sonunda projenin terimler sözlüğünü (`GLOSSARY.md`) ve mimari kararlarını (`ADR`) günceller.
    
- **`/to-spec`**: O anki sohbeti ve fikirleri resmi bir teknik şartnameye (spec) dönüştürür ve issue tracker'a gönderir.
    
- **`/to-tickets`**: Bir planı veya şartnameyi küçük, yönetilebilir biletlere (görevlere) böler.
    
- **`/implement`**: Belirlenen bir şartnameyi veya biletleri, test kurallarına (`/tdd`) ve kod incelemesine (`/code-review`) sadık kalarak inşa eder.
    
- **`/implement-spec`**: Bütün bir şartnameyi entegrasyon dalında (branch) paralel alt ajanlar kullanarak en yüksek verimle hayata geçirir.
    
- **`/wayfinder`**: Tek bir ajan oturumuna sığmayacak kadar büyük ve karmaşık işleri, karar biletlerinden oluşan haritalara bölerek adım adım çözer.
    
- **`/triage`**: Projedeki görevleri / issue'ları belirli roller ve kurallar çerçevesinde bir durum makinesinden geçirerek sınıflandırır.
    
- **`/improve-codebase-architecture`**: Kod tabanını düzenli aralıklarla tarar, modülleri derinleştirme fırsatlarını raporlar ve seçtiğin mimari iyileştirme üzerinde seninle çalışır.
    
- **`/retro`**: Bir oturum bittiğinde ajanın çevresini (navigasyon, otomatik kontroller, standartlar) daha iyi hale getirmek için iyileştirme önerilerinde bulunur.
    

#### Model-invoked (Arka Planda / Gerektikçe Çalışanlar)

- **`/tdd`**: Test-driven development (Test odaklı geliştirme) disiplinidir. Ajan önce hata veren bir test yazar, ardından kodu yazarak testi geçer ve refaktör eder.
    
- **`/diagnosing-bugs`**: Zorlu hatalarda ve performans düşüşlerinde kullanılan, adım adım hipotez kurma ve izleme döngüsü içeren hata ayıklama disiplinidir.
    
- **`/code-review`**: Yazılan kodun diff çıktılarını hem standartlara uyum (temizlik, kirlilik analizi) hem de şartnameye uygunluk açısından paralel alt ajanlarla inceler.
    
- **`/domain-modeling`**: Projenin alan modelini (domain model) aktif olarak geliştirir; terimleri sözlükte test eder ve edge-case senaryolarla güçlendirir.
    
- **`/codebase-design`**: Derin modüller tasarlamak (küçük bir arayüzün arkasında çok fazla işlevsellik barındırmak) için ortak bir disiplin ve kelime dağarcığı sağlar.
    
- **`/prototype`**: Tasarım sorularını çözmek için geçici prototipler (örneğin tek dosyalık interaktif HTML arayüzleri) inşa eder.
    
- **`/research`**: Güvenilir birincil kaynaklardan araştırma yapar ve bulguları repoya markdown dosyası olarak kaydeder.
    
- **`/pr`**: Pull Request metnini en sade görsel özet, kanıt ve birleştirme tehlikesi (blast radius) içerecek şekilde biçimlendirir.
    
- **`/wizard`**: Altyapı kurma, credential ayarlama veya manuel üçüncü taraf panellerini yönetme gibi adımlarda seni yönlendiren interaktif bir bash sihirbazı üretir.
    

### 2. Productivity (Genel Verimlilik ve İş Akışı Araçları)

Kod odaklı olmayan, genel iletişim ve belge yönetimini düzenleyen araçlardır.

#### User-invoked (Komutla Çalışanlar)

- **`/grill-me`**: Herhangi bir plan veya tasarım fikrini, tüm açık uçlar çözülene kadar seni aralıksız sorguya çekerek olgunlaştıran popüler mülakat aracıdır.
    
- **`/handoff`**: Oturum bittiğinde veya token sınırı dolduğunda, o ana kadar yapılanları kompakt bir devir teslim belgesine sıkıştırarak sonraki ajana aktarır.
    
- **`/wait-what`**: Ajanın cevabı kafa karıştırıcı geldiğinde çalıştırılır; ajanı durdurup eksik bağlamı projenin ortak diliyle sade bir şekilde yeniden anlatmasını sağlar.
    
- **`/teach`**: Mevcut dizini durum bilgisi tutan bir çalışma alanı olarak kullanarak sana yeni bir beceri veya konsepti çoklu oturumlarda öğretir.
    
- **`/to-questionnaire`**: Yanıtlayamadığın kararları, projeye dışarıdan destek veren kişilere iletmek üzere yapılandırılmış bir Markdown anketine dönüştürür.
    

#### Model-invoked (Arka Planda / Gerektikçe Çalışanlar)

- **`/grilling`**: `/grill-me`, `/grill-with-docs` gibi araçların arkasında çalışan temel mülakat yapma motorudur.
    
- **`/writing-for-agents`**: Ajancıkların, `AGENTS.md` / `CLAUDE.md` gibi dosyaların veya ajanların okuyacağı belgelerin en anlaşılır ve hatasız şekilde yazılmasını sağlar.

MCP olayına bakmam gerek sanırım büyük bir olay.


- **`CLAUDE.md` Dosyası (Proje Hafızası ve Kuralları):**
    
    - Projenin ana dizinine koyduğun bu markdown dosyası, Claude Code her çalıştığında **ilk okuduğu kılavuzdur**.
        
    - Projede hangi kod standartlarını kullanacağını (örneğin Python/C++ kodlama kuralları), mimari kararlarını, hangi kütüphaneleri tercih ettiğini buraya yazarsın. Böylece Claude her seferinde kuralları baştan unutmaz. Eğer projede başka ajanlar için yazılmış bir `AGENTS.md` varsa, Claude doğrudan onu da okuyabilir.
        
    - _Not:_ Ayrıca Claude, seninle çalıştıkça arka planda kendi otomatik hafızasını (`auto memory`) oluşturarak oturumlar arası öğrendiği şeyleri kendisi kaydeder.
        
- **Custom Skills (Özel Beceriler):**
    
    - Sık tekrar ettiğin iş akışlarını (`/review-pr` veya `/deploy-staging` gibi özel komutlar haline getirerek) paketleyip hem kendinin hem de ekibinin tek komutla kullanabilmesini sağlayan özel yetenek dosyalarıdır. (Az önce incelediğimiz Matt Pocock'un becerileri de bu mantıkla çalışır).
        
- **Hooks (Kancalar / Otomasyon Tetikleyicileri):**
    
    - Claude Code belirli bir eylemi gerçekleştirmeden _önce_ veya _sonra_ otomatik olarak arkada shell komutları çalıştırmanı sağlar.
        
    - Örneğin: Claude her dosya düzenlediğinde arka planda otomatik format atması (black/prettier vb.) ya da bir commit atmadan önce otomatik olarak linter (kod kontrolü) çalıştırması gibi otomasyonlar bu kancalarla ayarlanır.


- **Background Agents (Arka Plan Ajanları & Lider-Alt Ajan Mimarisi):**
    
    - Büyük bir görevi (örneğin devasa bir refaktör veya birden fazla modülün yazılması) tek bir ajana yükleyip beklemek yerine, sistem bir **lider ajan (lead agent)** atar.
        
    - Bu lider ajan işi parçalara böler, alt ajanlara (subagents) dağıtır, her biri arka planda eş zamanlı çalışır ve sonuçları en sonda lider ajan birleştirip senin önüne tek bir temiz PR olarak getirir. Sen de tek ekrandan bu süreci izlersin.
        
- **Agent SDK (Özel Orkestrasyon):**
    
    - Eğer hazır komutlar yetmezse, Anthropic'in sağladığı **Agent SDK**'yı kullanarak kendi özel ajan ağını kurabilirsin.
        
    - Hangi ajanın hangi araca erişebileceğini, izinleri, görev dağılım zincirini tamamen kendi kodladığın senaryolara göre programlayabilirsin.


- **Remote Control & Mobil Devamlılık (`claude --cloud`, mobil uygulama vb.):**
    
    - Bilgisayarda lokal olarak başlattığın bir Claude Code oturumunu dilediğinde telefonundan veya başka bir cihazdan uzaktan kontrol etmeye devam etmeni sağlar.
        
    - Telegram, Discord, iMessage veya özel webhooks üzerinden Claude oturumlarına dışarıdan komut/etkinlik (push event) tetikleyebilirsin.
        
- **Zamanlanmış Bulut Görevleri (Cloud / Desktop Scheduled Tasks):**
    
    - Claude'u sadece sen komut verdiğinde değil; örneğin her gece belirli bir saatte arka planda çalışacak şekilde otomatik periyodik görevlere bağlamanı sağlar.
        
- **CI/CD Entegrasyonları ve Otomatik Kod İnceleme (GitHub Actions / GitLab CI):**
    
    - Projeye açılan her bir Pull Request'e (PR) otomatik olarak kod incelemesi yaptırabilir, issue'ları triaj edebilir (sınıflandırabilir) veya Slack'ten gelen hata raporlarını doğrudan otomatik bir PR'a dönüştürebilirsin.
        
- **Tarayıcı ve Canlı Web Uygulama Hata Ayıklama (Chrome):**
    
    - Claude'un doğrudan tarayıcıyla etkileşime girerek canlı web uygulamalarındaki hataları test etmesini ve debug etmesini sağlar.
        
- **Agent SDK ile Özel İş Akışları:**
    
    - Hazır araçlar yetmediğinde, kendi özel otomasyon ajanlarını kodlayarak Claude'un tüm yeteneklerini kendi sistemlerine entegre etmene olanak tanır.

#### Shell komutları (Vault kök dizinindeki terminalinizden çalıştırın)

- `claude` $\rightarrow$ Kasanızda etkileşimli modu başlatır
    
- `claude "organize my notes"` $\rightarrow$ Etkileşimli modu ilk komutla birlikte başlatır
    
- `claude -p "summarize today's note"` $\rightarrow$ Tek seferlik bir sorgu çalıştırır, ardından çıkar
    
- `claude -c` $\rightarrow$ Kasa dizinindeki en son sohbete devam eder
    

#### Oturum komutları (Claude Code içinde çalıştırın)

- `/clear` $\rightarrow$ Yeni bir sohbet başlatır
    
- `/help` $\rightarrow$ Kullanılabilir komutları gösterir
    
- `/exit` $\rightarrow$ Claude Code'dan çıkar
    

### Obsidian kullanıcıları için profesyonel ipuçları

- **İsteklerinizde net olun:**
    
    - _Şunun yerine:_ "clean up my notes"
        
    - _Şunu deneyin:_ "clean up the markdown formatting in the 2026/06 folder, fixing broken links"
        
- **Adım adım talimatlar kullanın:**
    
    - Karmaşık görevleri adımlara bölün:
        
        - `scan all markdown files for missing tags`
            
        - `add standard frontmatter YAML to notes that lack it`
            
        - `generate an index note linking them all together`
            
- **Önce Claude'un keşfetmesine izin verin:**
    
    - Değişiklik yapmadan önce Claude'un kasanızı okumasına izin verin:
        
        - `analyze the folder structure of my vault`
            
        - `find all notes related to my university courses`
            
- **Kısayollarla zaman kazanın:**
    
    - Kullanabileceğiniz komutları ve becerileri (skills) görmek için `/` yazın
        
    - Dosya adı tamamlama için `Tab` tuşunu kullanın
        
    - Komut geçmişi için `↑` (Yukarı Ok) tuşuna basın
        
    - İzin modları (sorarak yap/otomatik yap) arasında geçiş yapmak için `Shift+Tab` tuşlarına basın