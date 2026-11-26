---
title: "Bölüm 10 - TCP Idle Tarama"
ana-not: "[[00_Nmap_ile_Ağ_Tarama_İndeks]]"
tarih: 2026-11-26
aliases:
  - Idle Scan
  - Zombi Tarama
  - nmap -sI
tags:
  - siber-guvenlik
  - nmap
  - ag-tarama
  - idle-scan
  - tcp
---

# Bölüm 10 - TCP Idle Tarama

> 📚 [İndeks](00_Nmap_ile_A%C4%9F_Tarama_%C4%B0ndeks.md) · ◀ [Bölüm 9 - TCP ACK Tarama](09_TCP_ACK_Tarama.md)

# TCP Idle Tarama

TCP Idle Taraması, port tarama teknikleri arasında benzersiz ve farklı bir yöntemdir. Kendi IP adresinizden tek bir paket bile göndermeden hedefteki portları taramanıza olanak tanır. Bu, "zombi" olarak adlandırılan ve taramanın sonuçlarının onun üzerinden yönlendirildiği bir aracı makine kullanarak yapılır. Bu yöntemle, saldırı tespit sistemleri zombi makineyi saldırgan olarak görecektir.

## TCP Idle Taramasının Temelleri

Idle Tarama, Antirez tarafından 1998 yılında geliştirilen ve tamamen kör bir port taraması yapılmasını sağlayan zekice bir tekniktir. Bu tarama türü, özellikle hedef ve zombi arasındaki IP tabanlı güven ilişkilerini keşfetmek için kullanılır.

## Temel Adımlar

Idle Tarama, temelde üç adımdan oluşur ve her port için bu adımlar tekrarlanır:

1. **Zombi'nin IP ID'sini Sorgulama:** Öncelikle, zombi bilgisayarın IP ID numarasını sorgulayarak başlanır.
2. **SYN Paketi Gönderme:** Zombi adına hedefteki belirli bir porta bir SYN paketi gönderilir. Bu paketin hedefteki port durumuna göre zombinin IP ID değerinde bir artışa neden olup olmayacağı kontrol edilir.
3. **IP ID'de Değişiklik Kontrolü:** Zombi'nin IP ID'si tekrar kontrol edilir. Eğer IP ID, ilk adımda kaydedilenden iki artmışsa, bu portun açık olduğu anlamına gelir. Eğer bir artış varsa, port ya kapalıdır ya da filtrelidir.

![Temel Adımlar](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/ea047221-bad0-4fe5-81ae-c3de38656e45/image-6-63432dbbc.webp)

![Temel Adımlar](https://storage.hackviser.com/file/hackviser-prod/trainings/sections/images/ea047221-bad0-4fe5-81ae-c3de38656e45/image-7-d05e92378.webp)

## Idle Taraması Nasıl Yapılır?

Idle Tarama yapabilmek için, uygun bir zombi bilgisayar seçmek kritik öneme sahiptir. İdeal bir zombi, IP ID değerlerini global olarak art arda şekilde atayan ve mümkün olduğunca az trafik üreten (yani "idle" veya boşta olan) bir sistemdir.

## Tarama Yapma

- Uygun bir zombi bulduktan sonra, `-sI` parametresi ile Nmap komutunu kullanarak idle taraması başlatılır. Örneğin: `nmap -sI zombie-ip target-ip`
- Bu tarama, IDS sistemlerini yanıltabilir ve zombi makineyi saldırgan olarak gösterebilir, bu nedenle kullanımında dikkatli olunmalıdır.
- Idle Tarama, güvenlik duvarlarını ve IP adresine dayalı filtrelemeleri aşmak için de kullanılabilir.

```bash
nmap -sI 10.10.10.10 10.10.10.192
```

## Avantajlar ve Dezavantajlar

**Avantajlar:** Idle Tarama, son derece gizliliğin ön planda olduğu bir tarama yöntemidir ve saldırganın IP adresini gizler. Ayrıca, hedef ve zombi arasındaki IP tabanlı güven ilişkilerini ortaya çıkarmak için de kullanılabilir.

**Dezavantajlar:** Uygun bir zombi bulmak zor olabilir ve tarama süreci, diğer yöntemlere göre daha uzun sürebilir.

TCP Idle Tarama, gelişmiş port tarama tekniklerinden biridir ve özellikle ağ güvenliği uzmanları tarafından tercih edilir. Bu yöntem, saldırganın anonim kalmasını sağlar ve zombi makineyi kullanarak hedef sistem hakkında değerli bilgiler toplamayı mümkün kılar. Ancak, etik kullanımı önemlidir ve zombi olarak kullanılan sistemlerin sahiplerinden izin alınması tavsiye edilir.
