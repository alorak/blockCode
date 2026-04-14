# BlockCode

Tarayıcı tabanlı, Bluetooth destekli görsel programlama ortamı. LEGO® uyumlu fiziksel cihazları blok tabanlı kodlarla kontrol etmenin yanı sıra kamera ve mikrofon üzerinden yapay zeka özellikleri sunar.

## Özellikler

### Blok Programlama
- [Blockly](https://developers.google.com/blockly) (v10.4.3) tabanlı sürükle-bırak kod editörü
- Kontrol akışı: başlangıç, koşul, döngü, bekleme, mesaj
- Operatörler: mantıksal, karşılaştırma, rastgele sayı
- Kod çalıştırma ve durdurma
- XML olarak içe/dışa aktarma
- Türkçe / İngilizce dil desteği (anlık geçiş)

### Bluetooth Bağlantısı (Web Bluetooth API)
Aynı anda birden fazla cihaz desteklenir.

| Cihaz | Açıklama |
|-------|----------|
| **Single Motor** | Servo / DC motor — hız ve yön kontrolü |
| **Double Motor** | Çift eksenli motor — her kanal bağımsız |
| **Controller** | Çift kollu joystick — sol/sağ kol açı okuma |
| **Remote Controller** | LEGO® uyumlu uzaktan kumanda — buton okuma |
| **SPIKE Essential Hub** | 6 portlu akıllı hub — LED, motor, sensör |
| **SPIKE Prime Hub** | 6 portlu ileri seviye hub |
| **BOOST Move Hub** | Entegre motorlu hub |
| **City Hub** | 2 portlu kompakt hub |
| **Technic Hub** | 4 portlu güçlü hub |

### Sensörler (Hub Portlarına Takılanlar)
- Kuvvet sensörü (Force Sensor) — basınç değeri ve basılı/değil
- Renk sensörü (Color Sensor) — renk adı ve ham değer
- Mesafe sensörü (Distance Sensor) — cm cinsinden mesafe
- Matrix LED (5×5 piksel) — desen, parlaklık, döndürme
- Kol motoru (Lever) — açı ve konum

### Yapay Zeka — Poz Sınıflandırma
Webcam üzerinden gerçek zamanlı vücut pozu algılama. TensorFlow.js MoveNet SINGLEPOSE_LIGHTNING modeli kullanılır.

| Poz | Açıklama |
|-----|----------|
| İki el aşağıda | Nötr duruş |
| Sağ el yukarıda | Sağ bilek omuzun üzerinde |
| Sol el yukarıda | Sol bilek omuzun üzerinde |
| İki el yukarıda | Her iki bilek omuzun üzerinde |

- Her poz için 0–100 güven yüzdesi
- Model sayfa yüklendiğinde arka planda ön yüklenir
- Bloklar: `poz algılandığında`, `poz algılanıyor mu?`, `güven skoru`

### Yapay Zeka — Vücut Algılama
MoveNet 17 iskelet noktasına dayalı geometrik hesaplamalar:
- İki eklem arası açı
- İki eklem arası mesafe (piksel)
- Eklem koordinatı (x / y)
- Kişi algılandı mı?
- İki eklem birbirine değdi mi?

### Yapay Zeka — Konuşma Komutları
TensorFlow.js Speech Commands modeli ile mikrofon üzerinden kelime tanıma.

Tanınan kelimeler: `yes`, `no`, `up`, `down`, `left`, `right`

- Bloklar: `konuşma algılandığında`, `son N saniyede algılandı mı?`
- Tek mikrofon izni (ikinci prompt engellenmiş)
- Dalga formu görselleştirme

### Ses Kayıtları
- Tarayıcıda ses kaydı ve kırpma
- Kaydedilen sesleri çalma bloğu

## Teknik Gereksinimler

| Gereksinim | Detay |
|------------|-------|
| **Tarayıcı** | Chrome 89+ veya Edge 89+ (Web Bluetooth için) |
| **HTTPS** | Bluetooth ve kamera/mikrofon için zorunlu (`localhost` muaf) |
| **İzinler** | Bluetooth, Kamera (poz), Mikrofon (konuşma) |
| **Bağlantı** | CDN bağımlılıkları için internet (ilk yüklemede) |

> Safari ve Firefox Web Bluetooth API'yi desteklemez.

## Kullanılan Kütüphaneler

| Kütüphane | Sürüm | Amaç |
|-----------|-------|------|
| [Blockly](https://github.com/google/blockly) | 10.4.3 | Blok editörü |
| [TensorFlow.js](https://www.tensorflow.org/js) | CDN latest | Yapay zeka çalışma zamanı |
| [@tensorflow-models/pose-detection](https://github.com/tensorflow/tfjs-models/tree/master/pose-detection) | CDN latest | MoveNet poz tespiti |
| [@tensorflow-models/speech-commands](https://github.com/tensorflow/tfjs-models/tree/master/speech-commands) | CDN latest | Konuşma tanıma |

## Dosya Yapısı

```
blockcode/
├── blockcode.html          # Tek dosya uygulama (tüm HTML/CSS/JS)
├── index.html              # Giriş noktası
├── img/                    # Cihaz görselleri ve ikonlar
│   ├── favicon.png
│   ├── essential-hub.png
│   ├── prime-hub.png
│   ├── single-motor.png
│   └── ...
└── docs/
    ├── BLUETOOTH_README.md # BLE protokol detayları
    ├── LED_REHBERI.md
    └── COCUKLAR_ICIN_REHBER.md
```

## Kurulum ve Çalıştırma

Sunucu gerekmez — `blockcode.html` dosyasını doğrudan tarayıcıda açın.

Bluetooth cihazları için HTTPS zorunludur. Yerel geliştirme için:

```bash
# Python ile basit HTTPS sunucu (sertifika gerekir)
python3 -m http.server 8080

# Veya VS Code Live Server eklentisi
```

`localhost` üzerinden açıldığında HTTP de çalışır.

## Bluetooth Bağlantısı

1. Cihazı açın ve eşleştirme moduna alın
2. **+ Cihaz Ekle** butonuna tıklayın
3. Tarayıcının Bluetooth seçim ekranından cihazı seçin
4. Bağlantı kurulunca sağ panelde cihaz görünür

Birden fazla cihaz aynı anda bağlanabilir.

## Dil Desteği

Sağ üst köşedeki **TR / EN** butonuyla anlık dil geçişi yapılabilir. Tüm blok metinleri, panel başlıkları ve sistem mesajları her iki dilde mevcuttur.

---

Geliştirme notları için [`docs/BLUETOOTH_README.md`](docs/BLUETOOTH_README.md) dosyasına bakın.
