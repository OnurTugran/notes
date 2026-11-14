
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


claude --fork-session veya /branch farklı yolları denemek veya riske girince geri alabilmek için

/context ise bağlam miktarı

- **Görünmez Kurallar (Sistem Hatırlatıcıları):** Bazen Claude, senin ona açıkça söylemediğin bir kuralı uygular; örneğin Git commit mesajlarına otomatik olarak `Co-Authored-By` (ortak yazar) satırı ekleyebilir. Bu durum, arka planda çalışan "sistem hatırlatıcılarından" (system reminder) kaynaklanmaktadır.
    
- **Claude'un Sohbete Eklediği Bağlamlar:** Sen çalışırken, Claude senin mesajlarının yanına şu verileri de kendi kendine ekler:
    
    - Projendeki `CLAUDE.md` dosyalarınızın içeriği.
        
    - Çıktı stili (output style) talimatları.
        
    - Claude'un daha önceden okuduğu bir dosya bilgisayarında (diskte) değiştiğinde durumu bildiren bir not.
        
    - Git commit ve pull request işlemleri için atıf (attribution) satırları.
        
- **Bu Otomasyonları Kapatma:** Eğer Claude'un bu işlemleri kendi kendine yapmasını istemiyorsan ayarları değiştirebileceğin belirtilir. Atıf satırlarını değiştirmek veya kaldırmak için `attribution` ayarını kullanabilirsin. Claude'un yerleşik commit ve pull request talimatlarını tamamen kaldırmak istersen de, `includeGitInstructions` ayarını `false` yapman yeterlidir
/config chat ayarları

- **Yardım İste:** Claude'a her zaman "Bunu nasıl yaparım?" diye sorabilirsin. Ayrıca projeni başlatmak için `/init` (otomatik CLAUDE.md oluşturur) ve ayarlarında sorun varsa `/doctor` (teşhis ve tamir) komutlarını kullanabilirsin.
    
- **Sohbet Havasında İlerleyin (Mükemmel Prompt Beklentisini Bırakın):** İlk komutunuz kusursuz olmak zorunda değil. "Giriş yapma hatasını çöz" diye başlarsın, Claude bir şey dener. Hata yaparsa, "Hayır, sorun oturum yönetiminde (session handling)" diye düzeltirsin. En baştan başlamak yerine üst üste koyarak ilerlersiniz.
    
- **Müdahale Et ve Yönlendir (Interrupt and steer):** Claude arka planda harıl harıl kod okurken yanlış yöne gittiğini fark edersen:
    
    - `Esc` tuşuna basıp onu anında durdurabilirsin.
        
    - O çalışırken doğrudan klavyeden yazıp `Enter`a basarsan, mesajın sıraya alınır (queued). Claude elindeki işlemi bitirir bitirmez mesajını okur ve rotasını senin yeni verdiğin bilgiye göre düzeltir.
        
- **Dikte Etme, Devret (Delegate, don’t dictate):** Yapay zekaya "Şu dosyayı aç, 15. satıra in, şu kodu sil" demek yerine usta bir mühendise iş verir gibi davran: _"Süresi dolmuş kredi kartlarında ödeme akışı çöküyor. Kod src/payments/ içinde. Sorunu bulup çözer misin?"_ Gerisini Claude kendisi halledecektir.



# Eğik Çizgi Komutları (Slash Commands)

## Genel Bakış

Eğik çizgi komutları, etkileşimli bir oturum sırasında Claude'un davranışını kontrol eden kısayollardır. Çeşitli türleri bulunur:

- **Yerleşik komutlar (Built-in commands)**: Claude Code tarafından sağlanır (`/help`, `/clear`, `/model`)
    
- **Beceriler (Skills)**: `SKILL.md` dosyaları olarak oluşturulan kullanıcı tanımlı komutlardır (`/optimize`, `/pr`)
    
- **Eklenti komutları (Plugin commands)**: Yüklü eklentilerden gelen komutlardır (`/frontend-design:frontend-design`)
    
- **MCP komutları (MCP prompts)**: MCP sunucularından gelen komutlardır (`/mcp__github__list_prs`)
    

> **Not**: Özel eğik çizgi komutları artık becerilerle (skills) birleştirilmiştir. `.claude/commands/` dizinindeki dosyalar çalışmaya devam etse de, artık önerilen yaklaşım becerilerdir (`.claude/skills/`). Her ikisi de `/komut-adi` şeklinde kısayollar oluşturur. Tam referans için Beceriler Kılavuzuna (Skills Guide) göz atabilirsiniz.

## Yerleşik Komutlar Referansı

Yerleşik komutlar, yaygın işlemler için kullanılan kısayollardır. **60'tan fazla yerleşik komut** ve **yaklaşık 20 adet entegre beceri** (bazıları özellik geçitli) mevcuttur. Tam listeyi görmek için Claude Code'da `/` yazın veya filtrelemek için `/` işaretinden sonra herhangi bir harf yazın.

> **Not**: v2.1.236'dan itibaren, yanlış yazılmış bir eğik çizgi komutunda — veya mevcut oturumda bulunmayan bir komutta — Enter'a basmak, sessizce en yakın eşleşmeyi çalıştırmak yerine bir hata verir. Belirsiz olmayan önekler ve tanımlanmış takma adlar eskisi gibi çalışmaya devam eder.

|Komut|Amacı|
|---|---|
|`/add-dir <path>`|Çalışma dizini ekler|
|`/advisor [model\|off]`|Danışmanı yapılandırır. Etkileşimli bir iletişim kutusu olarak açılır; masaüstü uygulamasında, Uzaktan Kontrolde ve gözetimsiz (`-p` / Agent SDK) oturumlarda metin formu alır (v2.1.260+)|
|`/agents`|Artık çalışmayan bir komuttur: Etkileşimli sihirbaz v2.1.198'de kaldırıldı ve v2.1.281'den beri `/` menüsünde görünmüyor. Claude'dan alt ajanlar oluşturmasını isteyin veya `.claude/agents/` dosyalarını doğrudan düzenleyin|
|`/artifacts`|Sahip olduğunuz veya sizinle paylaşılan eserleri listeler, ardından birini oturuma ekler, tarayıcıda açar veya bağlantısını kopyalar. `d` ile silmek onay ister (v2.1.305)|
|`/auto-mode-setup`|Projenizden ve son oturumlardan `autoMode.environment` girdilerini taslak olarak hazırlar, inceletir ve ayarlarınıza kaydeder|
|`/branch [name]`|Konuşmanın o anki kopyasına geçiş yapar, orijinalini korur (`/resume` ile dönebilirsiniz)|
|`/fork [prompt]`|Mevcut konuşmayı yeni bir **arka plan oturumuna** kopyalar ve siz burada çalışmaya devam edersiniz; kopyalanan oturum bağımsız hale gelir|
|`/subtask <task>`|Tüm konuşmayı devralan ve siz çalışmaya devam ederken görevi yürüten **çatallanmış bir alt ajan (forked subagent)** oluşturur; bittiğinde sonucu bu konuşmaya döner|
|`/btw [question]`|Claude ana görev üzerinde çalışırken ana konuşma bağlamını kirletmeden geçici bir yan soru sorar|
|`/cd <path>`|İstem önbelleğini (prompt cache) bozmadan oturumu yeni bir çalışma dizinine taşır|
|`/chrome`|Chrome tarayıcı entegrasyonunu yapılandırır|
|`/clear`|Konuşmayı temizler (takma adlar: `/reset`, `/new`)|
|`/color [color\|default]`|İstem çubuğu rengini ayarlar. Argümansız kullanım rastgele renk seçer.|
|`/compact [instructions]`|İsteğe bağlı odak talimatlarıyla konuşmayı sıkıştırır. Başarısız sıkıştırma artık sessizce kalmak yerine hata gösterir|
|`/config`|Ayarları açar (takma ad: `/settings`)|
|`/context`|Bağlam kullanımını renkli ızgara olarak görselleştirir. Sınır aşıldığında açık bir uyarı gösterir|
|`/copy [N]`|Asistan yanıtını panoya kopyalar; `w` dosyaya yazar|
|`/cost`|`/usage` için yazım kısayolu — maliyet sekmesini açar|
|`/desktop`|Masaüstü uygulamasında devam eder (takma ad: `/app`)|
|`/diff`|Kaydedilmemiş değişiklikler için etkileşimli fark (diff) görüntüleyici|
|`/doctor`|Kurulum sağlığını teşhis eder. Sistemdeki sorunları bulup onayınızla onarır|
|`/effort [low\|medium\|...\|ultracode [on\|off]]`|Etkileşimli ok tuşlarıyla efor seviyesini ayarlar (`low`, `medium`, `high`, `xhigh`, `max`). **Ultracode** geçişi, efor seviyesinden bağımsız olarak dinamik iş akışlarını koordine eder|
|`/exit`|REPL'den çıkar (takma ad: `/quit`)|
|`/export [filename]`|Mevcut konuşmayı bir dosyaya veya panoya dışa aktarır|
|`/usage-credits`|Hız sınırları için ekstra kullanımı yapılandırır (eski adıyla `/extra-usage`)|
|`/fast [on\|off]`|Hızlı modu değiştirir. Fable 5.2, Opus 5.5, Opus 5 ve Opus 4.8 için geçerlidir|
|`/feedback`|Geri bildirim gönderir. Artık bağlam için son oturumları ekleyebilmektedir|
|`/focus`|Odak görünümünü değiştirir|
|`/goal <statement>`|Oturum düzeyinde bir tamamlama koşulu kaydeder; Claude hedef karşılanana kadar çalışmaya devam eder|
|`/help`|Yardımı gösterir|
|`/hooks`|Kanca (hook) yapılandırmalarını görüntüler|
|`/ide`|IDE entegrasyonlarını yönetir|
|`/init`|`CLAUDE.md` dosyasını başlatır|
|`/insights`|Oturum analiz raporu oluşturur|
|`/install-github-app`|GitHub Actions uygulamasını kurar|
|`/install-slack-app`|Slack uygulamasını kurar|
|`/keybindings`|Tuş atamaları yapılandırmasını açar|
|`/fewer-permission-prompts`|İzin istemlerini azaltmak için Bash/MCP araç çağrılarını analiz edip beyaz liste oluşturur|
|`/login`|Anthropic hesapları arasında geçiş yapar|
|`/logout`|Anthropic hesabınızdan çıkış yapar|
|`/mcp`|MCP sunucularını ve OAuth'u yönetir|
|`/memory`|`CLAUDE.md` dosyasını düzenler, otomatik belleği ayarlar|
|`/mobile`|Mobil uygulama için QR kodu gösterir (takma adlar: `/ios`, `/android`)|
|`/model [model]`|Modeli seçer. Seçim yeni oturumlar için varsayılan olarak kaydedilir|
|`/output-style [name]`|Çıktı stillerini listeler ve değiştirir|
|`/passes`|Ücretsiz bir haftalık Claude Code davetiyesi paylaşır|
|`/permissions`|İzinleri görüntüler/günceller (takma ad: `/allowed-tools`)|
|`/plan [description]`|Plan moduna girer|
|`/plugin`|Eklentileri yönetir|
|`/proactive`|`/loop` için takma ad|
|`/powerup`|Özellikleri animasyonlu interaktif demolarla keşfetmenizi sağlar|
|`/privacy-settings`|Gizlilik ayarları (Pro/Max/Ultra'ya özel)|
|`/release-notes`|Sürüm notlarını görüntüler|
|`/recap`|Oturuma dönerken oturum özetini gösterir|
|`/reload-plugins`|Aktif eklentileri yeniden yükler|
|`/reload-skills`|Oturumu yeniden başlatmadan beceri dizinlerini yeniden tarar|
|`/remote-control`|claude.ai'den uzaktan kontrol (takma ad: `/rc`)|
|`/remote-env`|Varsayılan uzak ortamı yapılandırır|
|`/rename [name]`|Oturumu yeniden adlandırır|
|`/restart`|Bir güncellemeyi uygulamak için Claude Code'u yeniden başlatır|
|`/resume [session]`|Konuşmaya devam eder (takma ad: `/continue`)|
|`/review ...`|`/code-review` komutunun takma adıdır|
|`/rewind`|Konuşmayı ve/veya kodu geri sarar (takma adlar: `/checkpoint`, `/undo`)|
|`/sandbox`|Sandbox (korumalı alan) modunu değiştirir|
|`/schedule [description]`|Bulut tabanlı zamanlanmış görevleri yönetir (takma ad: `/routines`)|
|`/scroll-speed`|Tam ekran modunda fare tekerleği kaydırma hızını ayarlamak için menü açar|
|`/security-review`|Dalı güvenlik açıkları için analiz eder|
|`/skill-doctor`|Hangi yüklü becerilerin kullanılmadığını ve bağlam maliyetlerini gösterir|
|`/skills`|Mevcut becerileri listeler|
|`/stats`|`/usage` için yazım kısayolu — istatistik sekmesini açar|
|`/stickers`|Claude Code çıkartma uygunluğunu kontrol eder|
|`/status`|Sürüm, model, hesap ve oturum türü durumunu gösterir|
|`/statusline`|Durum satırını yapılandırır|
|`/tasks`|Arka plan görevlerini listeler/yönetir|
|`/team-onboarding`|Ekip arkadaşları için son kullanımınıza dayalı bir alışma kılavuzu oluşturur|
|`/teleport`|Web üzerindeki bir oturuma bu terminalde devam eder (takma ad: `/tp`)|
|`/terminal-setup`|Terminal tuş atamalarını yapılandırır|
|`/theme`|Tema seçiciyi açar / özel temaları yönetir|
|`/tui [default\|fullscreen]`|Terminal kullanıcı arayüzü (TUI) oluşturucusunu ayarlar|
|`/ultrareview`|Kapsamlı bulut tabanlı çoklu ajan kod incelemesi|
|`/upgrade`|Yükseltme sayfasını açar|
|`/usage`|Standart kullanım kontrol panelini açar|
|`/voice`|Bas-konuş sesle yazdırmayı açıp kapatır|
|`/workflows`|Çalışan ve tamamlanmış dinamik iş akışlarını görüntüler|

> **Neden `/cd` önemlidir:** Dizin değiştirmek eskiden önbellek sıcaklığını (cache warmth) kaybettirirdi; `/cd`, geçiş boyunca istem önbelleğini korur.

### Entegre Beceriler (Bundled Skills)

Bu beceriler Claude Code ile birlikte gelir ve eğik çizgi komutları gibi çağrılır:

|Beceri|Amacı|
|---|---|
|`/batch <instruction>`|Worktree'ler kullanarak büyük ölçekli paralel değişiklikleri organize eder|
|`/claude-api`|Proje dili için Claude API referansını yükler|
|`/dataviz`|Çalıştırılabilir renk paleti doğrulayıcısı ile grafik ve kontrol paneli tasarımı rehberliği|
|`/debug [description]`|Hata ayıklama günlüklerini etkinleştirir|
|`/design [description]`|Görsel bir **tasarım tuvali (design canvas)** oluşturur — koddan ziyade görsel olarak rafine edilen tasarımlar için|
|`/loop [interval] [prompt]`|İstemi belirtilen aralıklarla tekrarlar|
|`/code-review ...`|Doğruluk hataları için mevcut farkları (diff), bir PR'ı veya dalı inceler. Düzeltmeler için `--fix` kullanılabilir|
|`/simplify`|Sadece temizlik amaçlı (yeniden kullanım / basitleştirme / verimlilik) bir inceleme çalıştırır ve uygular; hata aramaz|


### Yeni Değişiklikler (Recent Changes)

- `/fork` ve `/subtask` rolleri **v2.1.212** sürümünde yer değiştirdi. `/fork` artık arka plan oturumu açarken, çatallanmış alt ajan görevi `/subtask` komutuna geçti.
    
- `/resume` artık görünür listeden kaldırılmış oturumları bile içeren bir seçici açar (v2.1.212).
    
- Arka planda çalışan bir oturumda `/resume` kullanıldığında artık o oturum doğrudan açılır (v2.1.285).
    
- `/output-style` komutu v2.1.269'da **geri eklendi** ve başsız (headless) oturumlarda da çalışmaktadır.
    
- `/review`, tamamen `/code-review` komutunun takma adı haline geldi (v2.1.223).
    
- Efor seviyesi için `/effort` komutu eklendi; Opus 4.6+ üzerinde `max` seviyesi kullanıma sunuldu.
    
- Sesle yazdırma için `/voice` eklendi.
    
- Zamanlanmış görevler için `/schedule` eklendi.
    
- İstem çubuğunu özelleştirmek için `/color` eklendi.
    
- Etkileşimli eğitimler için `/powerup` eklendi.
    
- `/model` seçici artık raw model ID'leri yerine okunabilir isimler (örn. "Sonnet 4.6") gösteriyor.
    
- MCP komutları artık `/mcp__<server>__<prompt>` şeklinde kullanılabiliyor.
    
- Ekip arkadaşları için rehber oluşturan `/team-onboarding` eklendi (v2.1.101).
    
- Titremesiz ekran için `/tui` eklendi (v2.1.110).
    
- Odak görünümü için `/focus` eklendi (v2.1.110).
    
- Bağlam özetini manuel tetikleyen `/recap` eklendi (v2.1.108).
    
- Bulut tabanlı çoklu ajan incelemesi için `/ultrareview` eklendi (v2.1.111).
    
- İzin istemlerini azaltmak için `/fewer-permission-prompts` eklendi (v2.1.111).
    
- Oturum bazlı hedefler belirleyen `/goal` eklendi (v2.1.139).
    
- Tam ekran TUI modunda fare kaydırma hızını ayarlayan `/scroll-speed` eklendi (v2.1.139).
    
- Oturumu yeniden başlatmadan becerileri tarayan `/reload-skills` eklendi (v2.1.152).
    
- Çalışan dinamik iş akışlarını görmek için `/workflows` eklendi (v2.1.154).
    
- Temizlik amaçlı `/simplify` bağımsız bir komut olarak geri döndü (v2.1.154).
    
- Bulut tabanlı kod incelemeleri için `/code-review ultra` birincil komut haline geldi (v2.1.223).
    

### `/goal` — Oturum Düzeyi Tamamlama Koşulu

Geçerli oturum için bir tamamlama koşulu kaydetmek için `/goal` kullanın. Claude, turlar boyunca bu amaca yönelik çalışır ve bir yer paylaşımı (overlay) paneli geçen süreyi, tur sayısını ve kullanılan tokenleri gösterir. `/goal clear` ile temizleyebilirsiniz.

```
User: /goal Payments servisini REST'ten gRPC'ye geçir ve entegrasyon testlerini başarıyla tamamla.
Claude: Hedef kaydedildi. Siz temizleyene kadar bu hedef doğrultusunda çalışacağım.
[Goal panel: ⏱ 0s · turns 0 · tokens 0]

User: REST uç noktalarını listeleyerek başla
Claude: [işi yapar, panel güncellenir]
```

### `/team-onboarding` — Ekip Arkadaşı Alışma Kılavuzu

Projenizin yerel Claude Code kullanımından yola çıkarak ekibe yeni katılanlar için bir alışma kılavuzu (ramp-up guide) oluşturmak üzere `/team-onboarding` kullanın. Claude son 30 güne ait oturumlarınızı, komutlarınızı ve MCP sunucu kullanımınızı analiz ederek yeni geliştiricilerin hızla üretken olmasını sağlayacak bir rehber üretir.

Bash

```
claude /team-onboarding
```

Oluşturulan kılavuz şunlardan yararlanır:

- Son oturumlarınızda tekrarlanan iş akışları
    
- En sık çalıştırdığınız komutlar
    
- Kullandığınız MCP sunucuları
    

## Özel Komutlar (Artık Beceriler/Skills)

Özel eğik çizgi komutları **becerilerle birleştirilmiştir**. Her ikisi de `/komut-adi` ile çağırabileceğiniz komutlar oluşturur:

|Yaklaşım|Konum|Durum|
|---|---|---|
|**Beceriler (Önerilen)**|`.claude/skills/<name>/SKILL.md`|Güncel standart|
|**Eski Komutlar**|`.claude/commands/<name>.md`|Hala çalışıyor|

Aynı ada sahip bir beceri ve bir komut varsa, **beceri önceliklidir**.

### Geçiş Yolu

Mevcut `.claude/commands/` dosyalarınız değişiklik yapılmadan çalışmaya devam eder. Becerilere geçiş yapmak için:

**Önce (Komut):**

```
.claude/commands/optimize.md
```

**Sonra (Beceri):**

```
.claude/skills/optimize/SKILL.md
```

### Neden Beceriler?

Beceriler, eski komutlara göre ek özellikler sunar:

- **Dizin yapısı**: Komut dosyalarını, şablonları ve referans dosyalarını paketler
    
- **Otomatik çağırma**: Claude ilgili durumlarda becerileri otomatik tetikleyebilir
    
- **Çağırma kontrolü**: Kullanıcıların, Claude'un veya her ikisinin çağırıp çağıramayacağını seçebilirsiniz
    
- **Alt ajan yürütme**: `context: fork` ile becerileri izole bağlamlarda çalıştırabilirsiniz
    

### Beceri Olarak Özel Komut Oluşturma

Bir `SKILL.md` dosyasına sahip bir dizin oluşturun:

Bash

```
mkdir -p .claude/skills/my-command
```

**Dosya:** `.claude/skills/my-command/SKILL.md`

YAML

```
---
name: my-command
description: Bu komutun ne işe yaradığı ve ne zaman kullanılacağı
---

# Benim Komutum

Bu komut çağrıldığında Claude'un izleyeceği talimatlar.

1. Birinci adım
2. İkinci adım
3. Üçüncü adım
```

### Frontmatter (Üst Veri) Referansı

|Alan|Amacı|Varsayılan|
|---|---|---|
|`name`|Komut adı (`/name` olur)|Dizin adı|
|`description`|Kısa açıklama (Claude'un ne zaman kullanacağını bilmesine yardımcı olur)|Boş olmayan ilk satır|
|`argument-hint`|Otomatik tamamlama için beklenen argümanlar|Yok|
|`allowed-tools`|Komutun izinsiz kullanabileceği araçlar|Devralınır|
|`model`|Kullanılacak spesifik model|Devralınır|
|`disable-model-invocation`|`true` ise sadece kullanıcı çağırabilir (Claude çağıramaz)|`false`|
|`user-invocable`|`false` ise `/` menüsünden gizlenir|`true`|
|`context`|İzole alt ajanda çalıştırmak için `fork` olarak ayarlanır|Yok|
|`agent`|`context: fork` kullanıldığında ajan türü|`general-purpose`|
|`hooks`|Beceri çalıştığında kaydedilen kancalar|Yok|

### Argümanlar

Komutlar argüman alabilir:

**`$ARGUMENTS` ile tüm argümanlar:**

YAML

```
---
name: fix-issue
description: Numarasına göre bir GitHub issue düzeltir
---

#$ARGUMENTS numaralı issue'yu kodlama standartlarımıza uygun olarak düzelt.
```

Kullanım: `/fix-issue 123` → `$ARGUMENTS` "123" olur.

**`$0`, `$1`, vb. ile bireysel argümanlar:**

YAML

```
---
name: review-pr
description: Bir PR'ı öncelikli olarak inceler
---

$1 önceliğiyle PR #$0'ı incele.
```

Kullanım: `/review-pr 456 high` → `$0`="456", `$1`="high"

### Kabuk Komutları ile Dinamik Bağlam

İstemden önce bash komutlarını yürütmek için `` !`komut` `` kullanın:

YAML

```
---
name: commit
description: Bağlam ile bir git commit oluşturur
allowed-tools: Bash(git *)
---

## Bağlam

- Mevcut git durumu: !`git status`
- Mevcut git farkı: !`git diff HEAD`
- Mevcut dal: !`git branch --show-current`
- Son commit'ler: !`git log --oneline -5`

## Görevin

Yukarıdaki değişikliklere dayanarak tek bir git commit oluştur.
```

### Dosya Referansları

Dosya içeriklerini `@` kullanarak dahil edebilirsiniz:

Markdown

```
@src/utils/helpers.js içindeki uygulamayı incele
@src/old-version.js ile @src/new-version.js dosyalarını karşılaştır
```

## Eklenti Komutları (Plugin Commands)

Eklentiler özel komutlar sağlayabilir:

```
/plugin-name:command-name
```

İsim çakışması olmadığında basitçe `/command-name` olarak da kullanılabilir.

## MCP Komutları (MCP Prompts as Commands)

MCP sunucuları istemlerini eğik çizgi komutları olarak sunabilir:

```
/mcp__<server-name>__<prompt-name> [arguments]
```

**Örnekler:**

Bash

```
/mcp__github__list_prs
/mcp__github__pr_review 456
/mcp__jira__create_issue "Hata başlığı" high
```

## Komut Mimarisi

Kod snippet'i

```
graph TD
    A["Kullanıcı Girişi: /command-name"] --> B{"Komut Türü?"}
    B -->|Yerleşik| C["Yerleşik Komutu Çalıştır"]
    B -->|Beceri| D["SKILL.md Dosyasını Yükle"]
    B -->|Eklenti| E["Eklenti Komutunu Yükle"]
    B -->|MCP| F["MCP İstemini Çalıştır"]

    D --> G["Frontmatter'ı Ayrıştır"]
    G --> H["Değişkenleri Değiştir"]
    H --> I["Kabuk Komutlarını Çalıştır"]
    I --> J["Claude'a Gönder"]
    J --> K["Sonuçları Döndür"]
```

## Komut Yaşam Döngüsü

Kod snippet'i

```
sequenceDiagram
    participant User
    participant Claude as Claude Code
    participant FS as Dosya Sistemi
    participant CLI as Kabuk/Bash

    User->>Claude: /optimize yazar
    Claude->>FS: .claude/skills/ ve .claude/commands/ içinde arar
    FS-->>Claude: optimize/SKILL.md döndürür
    Claude->>Claude: Frontmatter'ı ayrıştırır
    Claude->>CLI: !`komut` değişimlerini yürütür
    CLI-->>Claude: Komut çıktıları
    Claude->>Claude: $ARGUMENTS değerlerini yerleştirir
    Claude->>User: İstemi işler
    Claude->>User: Sonuçları döndürür
```

## Bu Klasördeki Mevcut Komutlar

Bu örnek komutlar beceriler (skills) veya eski tarz komutlar olarak kurulabilir.

### 1. `/optimize` - Kod Optimizasyonu

Kodu performans sorunları, bellek sızıntıları ve optimizasyon fırsatları için analiz eder.

### 2. `/pr` - Pull Request Hazırlığı

Lint, test ve commit biçimlendirmesi dahil PR hazırlık kontrol listesi üzerinden yönlendirir.

### 3. `/generate-api-docs` - API Dokümantasyonu Oluşturucu

Kaynak kodundan kapsamlı API dokümantasyonu üretir.

### 4. `/commit` - Bağlamlı Git Commit

Deponuzdan dinamik bağlam alarak bir git commit oluşturur.

### 5. `/push-all` - Sahnele, Commit Et ve Pushla

Tüm değişiklikleri sahneler, commit oluşturur ve güvenlik kontrolleriyle uzak sunucuya gönderir.

### 6. `/doc-refactor` - Dokümantasyon Yeniden Yapılandırma

Açıklık ve erişilebilirlik için proje dokümantasyonunu yeniden yapılandırır.

### 7. `/setup-ci-cd` - CI/CD Boru Hattı Kurulumu

Kalite güvencesi için pre-commit kancaları ve GitHub Actions uygular.

### 8. `/unit-test-expand` - Test Kapsamını Genişletme

Test edilmemiş dalları ve uç durumları hedefleyerek test kapsamını artırır.

## Kurulum

### Beceriler Olarak (Önerilen)

Beceriler (skills) dizininize kopyalayın:

Bash

```
# Skills dizini oluştur
mkdir -p .claude/skills

# Her komut dosyası için bir skill dizini oluştur
for cmd in optimize pr commit; do
  mkdir -p .claude/skills/$cmd
  cp 01-slash-commands/$cmd.md .claude/skills/$cmd/SKILL.md
done
```

### Eski Tarz Komutlar Olarak

Komutlar dizininize kopyalayın:

Bash

```
# Proje çapında (ekip)
mkdir -p .claude/commands
cp 01-slash-commands/*.md .claude/commands/ && rm .claude/commands/README.md

# Kişisel kullanım
mkdir -p ~/.claude/commands
cp 01-slash-commands/*.md ~/.claude/commands/ && rm ~/.claude/commands/README.md
```

## Kendi Komutlarınızı Oluşturma

### Beceri Şablonu (Önerilen)

`.claude/skills/my-command/SKILL.md` oluşturun:

YAML

```
---
name: my-command
description: Bu komutun ne yaptığı. [Tetikleyici koşullar] sağlandığında kullanın.
argument-hint: [isteğe-bağlı-argümanlar]
allowed-tools: Bash(npm *), Read, Grep
---

# Komut Başlığı

## Bağlam

- Mevcut dal: !`git branch --show-current`
- İlgili dosyalar: @package.json

## Talimatlar

1. İlk adım
2. Argümanlı ikinci adım: $ARGUMENTS
3. Üçüncü adım

## Çıktı Formatı

- Yanıtın nasıl biçimlendirileceği
- Nelerin dahil edileceği
```

### Yalnızca Kullanıcı Komutu (Otomatik Çağrılmayan)

Claude'un otomatik olarak tetiklememesi gereken yan etkilere sahip komutlar için:

YAML

```
---
name: deploy
description: Üretime dağıtım yapar (Deploy to production)
disable-model-invocation: true
allowed-tools: Bash(npm *), Bash(git *)
---

Uygulamayı üretime dağıt:

1. Testleri çalıştır
2. Uygulamayı derle
3. Dağıtım hedefine pushla
4. Dağıtımı doğrula
```

## En İyi Pratikler (Best Practices)

|Yapılacaklar (Do)|Yapılmayacaklar (Don't)|
|---|---|
|Açık, eylem odaklı isimler kullanın|Tek seferlik görevler için komut oluşturmayın|
|Tetikleyici koşulları içeren `description` ekleyin|Komutların içine karmaşık mantıklar kurmayın|
|Komutları tek bir göreve odaklı tutun|Hassas bilgileri (sabit diskte) gömmeyin|
|Yan etkiler için `disable-model-invocation` kullanın|`description` alanını atlamayın|
|Dinamik bağlam için `!` önekini kullanın|Claude'un mevcut durumu bildiğini varsaymayın|
|İlgili dosyaları beceri (skill) dizinlerinde düzenleyin|Her şeyi tek bir dosyaya koymayın|

## Sorun Giderme (Troubleshooting)

### Komut Bulunamadı

- Dosyanın `.claude/skills/<name>/SKILL.md` veya `.claude/commands/<name>.md` içinde olduğunu kontrol edin
    
- Frontmatter'daki `name` alanının beklenen komut adıyla eşleştiğini doğrulayın
    
- Claude Code oturumunu yeniden başlatın
    
- Kullanılabilir komutları görmek için `/help` çalıştırın
    

### Komut Beklendiği Gibi Çalışmıyor

- Daha spesifik talimatlar ekleyin
    
- Skill dosyasına örnekler ekleyin
    
- Bash komutları kullanıyorsanız `allowed-tools` kısmını kontrol edin
    
- Önce basit girdilerle test edin
    

### Beceri ve Komut Çakışması

Eğer aynı isimde hem beceri hem de komut varsa, **beceri önceliklidir**. Birini kaldırın veya yeniden adlandırın.

## İlgili Kılavuzlar

- **Beceriler (Skills)** - Beceriler için tam referans (otomatik çağrılan yetenekler)
    
- **Bellek (Memory)** - CLAUDE.md ile kalıcı bağlam
    
- **Alt ajanlar (Subagents)** - Devredilmiş yapay zeka ajanları
    
- **Eklentiler (Plugins)** - Paketlenmiş komut koleksiyonları
    
- **Kancalar (Hooks)** - Olay odaklı otomasyon