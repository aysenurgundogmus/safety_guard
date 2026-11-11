# Safety Guard AI

**Safety Guard AI**, endüstriyel çalışma alanlarında iş güvenliği risklerinin daha erken fark edilmesine yardımcı olmak için geliştirilen bir **bitirme projesidir**. Kamera görüntülerini ve sensör verilerini kullanarak çalışanların koruyucu ekipman durumunu ve çalışma ortamındaki bazı risk göstergelerini takip etmeyi amaçlar.

> Bu proje bir prototiptir; profesyonel iş güvenliği ekipmanlarının, uzman değerlendirmesinin veya sertifikalı sistemlerin yerine geçmez.

## Proje neler yapıyor?

### Kamera ile koruyucu ekipman takibi

ESP32-CAM görüntüleri yerel ağ üzerinden Python sunucusuna iletir. Sunucu, projede kullanılan YOLO modeliyle görüntüde kişi olup olmadığını ve **baret, yelek veya maske eksikliği** olup olmadığını analiz eder. Sonuçlar mobil ve web panellerinde gösterilebilir; eksik ekipman için bilgisayardan sesli uyarı verilebilir.

### Sensör verisi ve risk göstergeleri

Flask API; nabız, vücut ve ortam sıcaklığı, nem, gaz ve alev gibi sensör verilerini alır. Mevcut uygulamada risk puanı, bu verilere uygulanan belirli eşik kurallarıyla hesaplanır ve veriler Supabase'e kaydedilebilir.

Kamera ile ekipman tespiti ve sensör risk hesabı şu an **ayrı işlevlerdir**; kodda bunları birleştiren tek bir risk puanı bulunmamaktadır.

### Mobil ve web panelleri

- **Flutter mobil uygulaması:** kullanıcı kaydı ve girişi, sensör durumu ve kamera/ekipman sonuçlarını gösterir.
- **Web paneli:** kullanıcıları, sensör ölçümlerini ve grafiklerini, risk özetini ve kamera görüntüsünü gösterir.
- **Supabase:** kullanıcı hesapları ve sensör kayıtları için kullanılır.

## Kullanılan teknolojiler

| Alan | Teknoloji |
|---|---|
| Kamera donanımı | ESP32-CAM |
| Görüntü analizi | Python, YOLO, OpenCV |
| API ve sunucu | Flask |
| Mobil uygulama | Flutter |
| Web paneli | HTML, CSS ve JavaScript |
| Hesaplar ve veri saklama | Supabase |

## Proje yapısı

```text
arduino/esp32_ppe_websocket/  ESP32-CAM kamera bağlantı kodu
flutter_application_1/        Flutter mobil uygulaması
web_dashboard/                Web paneli
api_mobil.py                  Sensör verisi API'si ve eşik tabanlı risk hesabı
run_ppe_flask.py              Kamera analizi ve KKD API'si
dashboard_api.py              Web paneli API'si
supabase/                     Veritabanı kurulum ve düzeltme SQL dosyaları
```

## Çalıştırmadan önce

Projeyi çalıştırmak için ilgili bileşenlerin yapılandırılması gerekir:

- Kamera analizi için `run_ppe_flask.py` dosyasının kullandığı `best.pt` YOLO model dosyası gerekir.
- Mobil uygulamadaki Supabase bağlantı bilgileri `flutter_application_1/lib/main.dart` içinde yapılandırılmalıdır.
- ESP32-CAM, sunucu adresi ve Wi-Fi bilgileri kullanılan yerel ağa göre ayarlanmalıdır. Telefon ve bilgisayardan erişim için mobil uygulamadaki API adresleri de kontrol edilmelidir.
- Supabase tabloları ve izinleri için `supabase/` klasöründeki ilgili SQL dosyaları kullanılmalıdır.

Geliştirme sırasında bileşenler ayrı ayrı başlatılabilir:

```bash
python api_mobil.py
python run_ppe_flask.py
python dashboard_api.py
```

Web paneli `dashboard_api.py` tarafından **5003** portunda sunulur. Mobil uygulamayı çalıştırmak için `flutter_application_1/` klasöründe Flutter araçlarıyla `flutter pub get` ve `flutter run` komutları kullanılabilir.

## Kapsam ve sınırlamalar

Bu depo; mobil/web arayüzlerini, API kodlarını ve ESP32-CAM bağlantısını içerir. Sensör donanımının tüm firmware'i veya devrelerinin üretim ve doğrulama süreci bu kodlardan tek başına doğrulanamaz. README'de ölçüm raporu ya da proje içinde doğrulanabilir test sonucu bulunmadığı için belirli bir model doğruluğu veya uçtan uca gecikme değeri verilmemiştir.

Gerçek saha kullanımından önce donanım ve model doğrulaması yapılmalı, eşikler uzmanlarca belirlenmeli, güvenlik ayarları gözden geçirilmeli ve kod içinde bulunan örnek/yerel erişim bilgileri güvenli yapılandırmayla değiştirilmelidir.
