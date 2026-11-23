---
title: "Bölüm 13 - Process Yönetimi"
ana-not: "[[00_Linux_Temelleri_İndeks]]"
tarih: 2026-11-23
aliases:
  - Process Yönetimi
  - Süreç Yönetimi
  - ps top kill
  - systemctl
  - Linux Temelleri Bölüm 13
tags:
  - siber-guvenlik
  - linux
  - linux-temelleri
  - process
  - ps
  - top
  - kill
  - nice
  - systemctl
---

# Bölüm 13 - Process Yönetimi

> ◀ [Bölüm 12 - İzinler](12_%C4%B0zinler.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md)

## 0. Process Nedir ve Nasıl Çalışır?
Linux'ta o an çalışan her programa veya komuta **process (süreç)** denir. Bir programa (örn. Firefox) tıkladığında ya da terminale bir komut (örn. `ls`) yazdığında işletim sistemi bu iş için bellekte yer ayırır ve ona **benzersiz bir kimlik numarası (PID)** verir.

**Temel özellikler:**
- **PID (Process ID):** Her process'in benzersiz numarası.
- **Sahip (User):** Process'i başlatan kullanıcı.
- **Ebeveyn (Parent):** Bir process başka bir process tarafından başlatılabilir (terminalden Firefox açarsan terminal "baba", Firefox "çocuk" olur).

Process yönetimi; sistem performansını kontrol etmek ve kilitlenen programları kapatmak için hayati önem taşır.

> [!NOTE]
> PID, PPID, `systemd`'nin PID 1 olması ve `/proc` dizini [Süreçler ve Görev Zamanlama](../Linux_Komutlar%C4%B1/04_S%C3%BCre%C3%A7ler_ve_G%C3%B6rev_Zamanlama.md) notunda anlatıldı. Bu bölüm onları **komutlarla yönetmeyi** ekler. Açılışta PID 1'in rolü: [Bölüm 1 - Açılış süreci](01_Linux_Giri%C5%9F.md).

## 1. Süreç Türleri

### Ön Plan (Foreground)
Varsayılan olarak tüm process'ler ön planda çalışır. Klavyeden girdi alır, ekrana çıktı verir. İşini bitirene kadar terminali kilitler.
```bash
user@hackerbox:~$ pwd
/home/user
```
`pwd` ön planda çalıştı, çıktısını verdi ve görevini bitirip çıktı.

### Arka Plan (Background)
Terminali **kilitlemez**, arka planda çalışır; aynı anda paralel olarak birçok process çalıştırılabilir. Komutun sonuna `&` eklenir.
```bash
user@hackerbox:~$ ping 127.0.0.1 &
[1] 54017
```
`[1]` iş (job) numarasıdır, `54017` ise process'in **PID**'sidir.

| | Ön plan | Arka plan |
|---|---|---|
| Terminal | Kilitlenir | Serbest kalır |
| Başlatma | Normal komut | Komut sonuna `&` |
| Örnek | `pwd` | `ping 127.0.0.1 &` |

## 2. Mevcut Terminal Oturumundaki Process'ler (`jobs`, `fg`)
Arka planda çalışan işleri listelemek için `jobs`:
```bash
user@hackerbox:~$ jobs
[1]    running    ping 127.0.0.1
```
Arka plandaki komutu tekrar ön plana almak için `fg` (foreground):
```bash
user@hackerbox:~$ fg %1
ping 127.0.0.1
```
`%1` iş numarasıdır.

| Kısayol | Etkisi |
|---|---|
| `CTRL + C` | Process'i **durdurur** (sonlandırır) |
| `CTRL + Z` | Process'i **duraklatır** ve arka plana atar |

> [!TIP]
> `CTRL+Z` ile duraklatılan işi arka planda çalıştırmaya devam ettirmek için `bg %1`, tekrar ön plana almak için `fg %1` kullanılır.

## 3. Sistem Genelindeki Process'ler (`ps`, `top`)
Sadece mevcut terminalde değil, **tüm sistemde** çalışan process'ler de vardır.

### `ps aux`
Tüm process'lerin **anlık fotoğrafını** çeker.
```bash
user@hackerbox:~$ ps aux | head -n 10
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.1 168436 10240 ?        Ss   08:00   0:02 /sbin/init
root           2  0.0  0.0      0     0 ?        S    08:00   0:00 [kthreadd]
root        1234  0.0  0.5 123456 23456 ?        Sl   08:05   0:10 /usr/sbin/apache2
syslog       850  0.0  0.0 220100  5000 ?        Ssl  08:00   0:01 /usr/sbin/rsyslogd -n
message+     855  0.0  0.1   8500  4200 ?        Ss   08:00   0:01 /usr/bin/dbus-daemon --system
root         950  0.0  0.1  15000  6500 ?        Ss   08:00   0:00 /usr/sbin/sshd -D
mehmet      5432  1.5  3.2 567890 65432 pts/0    R+   10:30   0:05 python3 script.py
mehmet      5433  0.0  0.1   9000  4000 pts/0    S+   10:31   0:00 tail -f log.txt
root        6000  0.1  2.0 400000 50000 ?        S    09:00   0:15 /usr/bin/containerd
```

| Sütun | Açıklama |
|---|---|
| `USER` | Process'i başlatan kullanıcı |
| `PID` | Benzersiz kimlik numarası |
| `%CPU` | İşlemci kullanım oranı |
| `%MEM` | Bellek (RAM) kullanım oranı |
| `STAT` | Durum: `R` çalışıyor, `S` uyuyor, `Z` zombi |
| `START` | Başlangıç zamanı |
| `COMMAND` | Çalıştırılan komut |

Çıktıda `PID 1` (`/sbin/init`) ilk süreçtir; `[kthreadd]` gibi köşeli parantezli olanlar çekirdek süreçleridir.

> [!TIP]
> Belirli bir process'i aramak için pipe + grep: `ps aux | grep apache` ([Bölüm 8](08_Filtreler_ve_Y%C3%B6nlendirme.md)). Sızma testinde ve olay müdahalesinde `ps aux` çıktısına bakarak **şüpheli process'leri** (beklenmedik kullanıcı, garip yoldan çalışan komut) tespit edebilirsin.

### `top`
Process'leri **canlı** izlemek için kullanılır (çıkmak için `q`).
```bash
top - 14:30:00 up 10 days,  4:20,  1 user,  load average: 0.05, 0.10, 0.05
Tasks: 123 total,   1 running, 122 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1.0 us,  0.5 sy,  0.0 ni, 98.5 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   3900.0 total,   1500.0 free,   1200.0 used,   1200.0 buff/cache
```
- `load average`: son 1, 5, 15 dakikadaki sistem yükü
- `Tasks`: toplam/çalışan/uyuyan/durmuş/zombi process sayısı
- `%Cpu(s)`: `us` kullanıcı, `sy` sistem, `id` **boşta** (yüksekse sistem rahattır)
- `MiB Mem`: toplam, boş, kullanılan bellek

| | `ps aux` | `top` |
|---|---|---|
| Görünüm | Anlık fotoğraf | Canlı, sürekli güncellenir |
| Kullanım | Script/pipe ile süzme | Kaynak tüketimini izleme |

## 4. Çalışan Process'i Durdurmak (`kill`)
`kill` komutu **PID numarası** ister.
```bash
user@hackerbox:~$ kill 5432
```
Process'e **sinyal** göndererek durdurulur. `kill` varsayılan olarak **15** numaralı sinyali gönderir.

| Sinyal No | Ad | Anlamı |
|---|---|---|
| `15` | **SIGTERM** | **Nazikçe kapan** (varsayılan). Dosyaları kaydetmeye izin verir. |
| `9` | **SIGKILL** | **Derhal öl.** Kaydetmeye izin vermez, zorla kapatır. |
| `1` | **SIGHUP** | Yeniden başla (config dosyasını tekrar oku). |
| `2` | **SIGINT** | Kesme sinyali; klavyeden `CTRL+C` ile gönderilir. |
| `19` | **SIGSTOP** | Process'i duraklatır (Pause). |
| `18` | **SIGCONT** | Duraklatılmış process'i devam ettirir (Continue). |

Normal `kill` ile kapanmayan process için zorla kapatma:
```bash
user@hackerbox:~$ kill -9 5432
```
> [!WARNING]
> `kill -9` son çaredir: process temizlik yapamaz, açık dosyalar bozulabilir. Önce normal `kill` dene.

> [!NOTE]
> `CTRL+Z` aslında **SIGTSTP** sinyali gönderir (`SIGSTOP`'un klavye karşılığı).

**İsmine göre öldürme (`pkill`, `killall`):** PID aramadan, isimle kapatır.
```bash
user@hackerbox:~$ pkill python
user@hackerbox:~$ killall firefox
```
> [!WARNING]
> `pkill python` adında `python` geçen **tüm** process'leri kapatır. Önce `pgrep python` ile kimleri etkileyeceğine bak.

## 5. Öncelik Ayarı (`nice`, `renice`)
Process'lerin önceliği vardır: **-20 en yüksek**, **19 en düşük**, varsayılan **0**. (Sayı büyüdükçe process "daha nazik" davranır, yani önceliği düşer.)

**`nice`:** Programı başlatırken öncelik verir.
```bash
user@hackerbox:~$ nice -n 10 tar -czf yedek.tar.gz /home
```
Yedekleme düşük öncelikle çalışır, bilgisayarı yormaz.

**`renice`:** Çalışan process'in önceliğini değiştirir.
```bash
user@hackerbox:~$ renice -n -5 -p 5432
```
> [!TIP]
> Önceliği **yükseltmek** (negatif değer) için root yetkisi gerekir (`sudo renice ...`). Normal kullanıcı yalnızca önceliği düşürebilir.

## 6. Servis Yönetimi (`systemd`)
Modern Linux sistemlerinde (Ubuntu, CentOS vb.) arka plan servislerini **systemd** yönetir; komut aracı **`systemctl`**'dir. (Systemd timer örneği: [Görev Zamanlama](../Linux_Komutlar%C4%B1/04_S%C3%BCre%C3%A7ler_ve_G%C3%B6rev_Zamanlama.md).)

**Örnek: Nginx servisi**

Durumunu gör:
```bash
user@hackerbox:~$ systemctl status nginx
● nginx.service - A high performance web server
   Active: active (running) since Mon 2023-10-01 ...
```
Başlat / durdur:
```bash
sudo systemctl start nginx
sudo systemctl stop nginx
```
Otomatik başlat (bilgisayar açılınca):
```bash
sudo systemctl enable nginx
```

| Komut | Etkisi |
|---|---|
| `systemctl status <servis>` | Durumu gösterir |
| `sudo systemctl start <servis>` | Başlatır |
| `sudo systemctl stop <servis>` | Durdurur |
| `sudo systemctl restart <servis>` | Yeniden başlatır |
| `sudo systemctl enable <servis>` | Açılışta otomatik başlatır |
| `sudo systemctl disable <servis>` | Otomatik başlatmayı kapatır |

> [!TIP]
> `start/stop` servisi **şimdi** etkiler, `enable/disable` ise **bir sonraki açılışı.** Hem şimdi başlatıp hem kalıcı yapmak için: `sudo systemctl enable --now nginx`. Güvenlik gözüyle: kalıcılık (persistence) için servis/timer eklemek saldırganların sık kullandığı yöntemdir.

## 7. Hızlı Özet

| İş | Komut |
|---|---|
| Arka planda çalıştır | `komut &` |
| Arka plan işleri / ön plana al | `jobs` / `fg %1` |
| Tüm process'leri listele | `ps aux` |
| Canlı izle | `top` (çıkış `q`) |
| Nazikçe kapat | `kill <PID>` |
| Zorla kapat | `kill -9 <PID>` |
| İsimle kapat | `pkill <ad>` / `killall <ad>` |
| Düşük öncelikle başlat | `nice -n 10 komut` |
| Öncelik değiştir | `renice -n <n> -p <PID>` |
| Servis durumu / başlat / durdur | `systemctl status/start/stop <servis>` |
| Açılışta otomatik başlat | `sudo systemctl enable <servis>` |

---
> ◀ [Bölüm 12 - İzinler](12_%C4%B0zinler.md) · 📚 [İndeks](00_Linux_Temelleri_%C4%B0ndeks.md)
