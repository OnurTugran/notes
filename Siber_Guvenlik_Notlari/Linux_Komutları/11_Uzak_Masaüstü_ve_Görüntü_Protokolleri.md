---
title: "Uzak Masaüstü ve Görüntü Protokolleri"
ana-not: "[[00_Linux_Komutları]]"
tarih: 2026-11-19
aliases:
  - Uzak Masaüstü ve Görüntü Protokolleri
tags:
  - siber-guvenlik
  - linux
  - rdp
  - vnc
  - x11
  - ssh-tunnel
---

# Uzak Masaüstü ve Görüntü Protokolleri

> ◀ [Yedekleme ve Rsync](10_Yedekleme_ve_Rsync.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Konteynerler: Docker ve LXC](12_Konteynerler_Docker_ve_LXC.md) ▶

Sızma testlerinde hedef sistemde grafiksel bir arayüze (GUI) erişmek veya sistemi uzaktan yönetmek için kullanılan temel protokoller şunlardır:

## 1. RDP vs VNC (Temel Fark)
* **RDP (Remote Desktop Protocol):** Genellikle Windows ortamları için tasarlanmıştır. Yöneticilerin Windows masaüstlerine sanki bilgisayarın başındaymış gibi bağlanmasını sağlar.
* **VNC (Virtual Network Computing):** Çapraz platformdur (cross-platform) ancak çoğunlukla Linux sistemlerde grafiksel erişim sağlamak için tercih edilir. RFB (Remote Frame Buffer) protokolünü kullanır.

## 2. X11 / XServer
Unix/Linux sistemlerde grafiksel kullanıcı arayüzünün (GUI) işletim sistemiyle iletişim kurmasını sağlayan protokoldür. En büyük avantajı "ağ şeffaflığı"na (network transparency) sahip olmasıdır; yani uzak bir sunucuda çalışan uygulamanın grafikleri, hedef sunucuyu yormadan doğrudan kendi (lokal) bilgisayarınızda işlenebilir (render edilebilir).

* **Portlar:** Genellikle `TCP/6000 - 6009` aralığını kullanır (İlk ekran olan `:0` için `6000` portu açılır).
* **Güvenlik Zafiyeti:** Varsayılan olarak **şifresiz (unencrypted)** veri iletimi yapar. Ağdaki bir saldırgan `xwd` (X pencerelerinin ekran görüntüsünü alan bir araç) gibi standart araçlar kullanarak ağ dinlemesi (sniffing) yapabilir ve ekrandaki şifreleri veya hassas verileri çalabilir. Ayrıca zayıf oturum anahtarları nedeniyle kod çalıştırmaya izin veren tarihi zafiyetleri (CVE-2017-2624 vb.) mevcuttur.
* **Güvenli Kullanım (X11 Forwarding):** X11'i güvenle kullanmak için trafik SSH tüneli içinden geçirilmelidir. Hedef sunucunun `/etc/ssh/sshd_config` dosyasında `X11Forwarding yes` ayarının açık olması gerekir.

```bash
# İstemci makineden (kendi bilgisayarından) X11 yönlendirmesi ile güvenli bir şekilde Firefox'u başlatma:
ssh -X <kullanıcı>@<HEDEF_IP> /usr/bin/firefox
```

## 3. XDMCP (X Display Manager Control Protocol)
Uzak X Window oturumlarını yönetmek için kullanılır ve tüm grafiksel arayüzü (KDE, Gnome vb.) uzak istemciye yönlendirir.
* **Port:** `UDP/177`
* **Güvenlik:** Son derece güvensizdir. Ortadaki Adam (MITM - Man-in-the-Middle) saldırılarına çok açıktır. Saldırgan trafiği kesip kendi komutlarını çalıştırabileceği için yüksek güvenlik gerektiren ortamlarda kesinlikle kullanılmamalıdır.

## 4. VNC Kurulumu ve Kullanımı
VNC varsayılan olarak `TCP/5900` portunu kullanır (Ekran 0). Eklenen her yeni sanal ekran için port artar (Ekran 1 için `5901`, Ekran 2 için `5902` vb.). Güvenlik ve şifreleme özellikleri nedeniyle `UltraVNC` ve `RealVNC` popülerdir. GNOME masaüstü ortamı VNC ile kararsız çalışabildiği için genellikle **XFCE4** masaüstü yöneticisi ile birlikte **TigerVNC** tercih edilir.

### TigerVNC Kurulum ve Yapılandırması

```bash
# VNC Sunucusu ve XFCE4 masaüstü paketlerinin kurulumu:
sudo apt install xfce4 xfce4-goodies tigervnc-standalone-server -y

# VNC bağlantısı için parola oluşturma (Salt okunur -view only- parolasına 'n' diyebilirsiniz):
vncpasswd
```

```bash
# VNC başlatma yapılandırma dosyalarını (xstartup ve config) oluşturma:
touch ~/.vnc/xstartup ~/.vnc/config

# xstartup dosyasını VNC bağlandığında XFCE4 masaüstünü başlatacak şekilde yapılandırma:
cat <<EOT>> ~/.vnc/xstartup
#!/bin/bash
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
/usr/bin/startxfce4
[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r \$HOME/.Xresources ] && xrdb \$HOME/.Xresources
x-window-manager &
EOT

# Çözünürlük ayarlarını (geometry) config dosyasına yazma:
cat <<EOT>> ~/.vnc/config
geometry=1920x1080
dpi=96
EOT

# Başlatma (xstartup) betiğine çalıştırma (execute) izni verme:
chmod +x ~/.vnc/xstartup

# VNC Sunucusunu Başlatma:
vncserver

# Çalışan VNC oturumlarını (ve kullandıkları RFB portlarını) listeleme:
vncserver -list
```

### VNC Bağlantısını SSH ile Şifreleme (SSH Tunneling)
VNC trafiğini güvenli hale getirmek için port doğrudan dışarı açılmaz. Bunun yerine **SSH tüneli** (SSH Tunneling) kurularak VNC trafiği güvenli SSH kanalının içinden akıtılır.

```bash
# Adım 1: Kendi 5901 portumuzu, hedef makinenin (<HEDEF_IP>) lokalindeki 5901 portuna bağlayarak tünel açma:
ssh -L 5901:127.0.0.1:5901 -N -f -l <kullanıcı> <HEDEF_IP>

# Adım 2: VNC istemcisi (xtightvncviewer) ile doğrudan kendi "localhost" adresimize bağlanarak tüneli kullanma:
xtightvncviewer localhost:5901
```

---
> ◀ [Yedekleme ve Rsync](10_Yedekleme_ve_Rsync.md) · 📚 [İndeks](00_Linux_Komutlar%C4%B1.md) · [Konteynerler: Docker ve LXC](12_Konteynerler_Docker_ve_LXC.md) ▶
