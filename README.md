# Critter Escape – Aksu Studio

Bu proje, oyunu (www/index.html) Android uygulamasına çevirir ve GitHub üzerinde bulutta derler. Bilgisayarına Android Studio kurman gerekmez.

## Klasörler
- `www/index.html` → oyunun kendisi (Aksu Studio açılış ekranı içinde)
- `assets/` → uygulama ikonu ve açılış görselleri (logodan üretildi)
- `store/` → Play Store için 512x512 ikon ve 1024x500 öne çıkan görsel
- `.github/workflows/android.yml` → otomatik derleme
- `PRIVACY_POLICY.md`, `STORE_LISTING.md` → gizlilik politikası ve mağaza metinleri

## 1) GitHub'a yükle
1. github.com'da yeni bir repo aç (örn. `critter-escape`).
2. Bu klasördeki tüm dosyaları (gizli `.github` klasörü dahil) repoya yükle. Dalın adı `main` olsun.
3. Repoda **Actions** sekmesine gir. Derleme otomatik başlar (veya "Android Build" → "Run workflow").
4. Bittiğinde çalıştırmanın altındaki **Artifacts** bölümünden `critter-escape-debug-apk` dosyasını indirip telefonunda dene.

## 2) Yükleme anahtarı (keystore) oluştur — bir kere
Play Store'a yüklenecek dosya imzalı olmalı. Java kuruluysa bilgisayarında (veya GitHub Codespaces'te) şunu çalıştır:

```
keytool -genkey -v -keystore upload.jks -keyalg RSA -keysize 2048 -validity 10000 -alias upload
```

**upload.jks dosyasını ve şifreni güvenli bir yere yedekle, GitHub'a yükleme!** Kaybedersen güncelleme yapmak zorlaşır.

Dosyayı base64 metnine çevir:
- Mac/Linux: `base64 -i upload.jks | tr -d '\n'`
- Windows PowerShell: `[Convert]::ToBase64String([IO.File]::ReadAllBytes("upload.jks"))`

Repo → **Settings → Secrets and variables → Actions → New repository secret** ile şu 4 secret'ı ekle:
| Ad | Değer |
|---|---|
| `KEYSTORE_BASE64` | yukarıdaki base64 metni |
| `KEYSTORE_PASSWORD` | keystore şifren |
| `KEY_ALIAS` | `upload` |
| `KEY_PASSWORD` | anahtar şifren (genelde aynı) |

Sonra Actions'tan tekrar çalıştır. Bu sefer `critter-escape-release-aab` çıkar: Play Store'a yükleyeceğin dosya (.aab).

## 3) Play Console
1. play.google.com/console → geliştirici hesabı (tek seferlik 25 USD).
2. Yeni uygulama oluştur: ad `Critter Escape`, tür Oyun, ücretsiz.
3. `store/icon-512.png` ve `store/feature-graphic-1024x500.png` dosyalarını yükle. Metinleri `STORE_LISTING.md`'den al.
4. En az 2 telefon ekran görüntüsü gerekir: debug APK'yı telefonuna kurup ekran görüntüsü al.
5. Gizlilik politikası için bir URL gerekir: repo **Settings → Pages**'ten `main` dalını yayınla, `PRIVACY_POLICY.md`'yi bir sayfa olarak göster, e-postanı yaz. Çıkan adresi Play Console'a gir.
6. İçerik derecelendirme, Veri güvenliği ve Reklam beyanı formlarını doldur.
7. `.aab` dosyasını Test → Üretim bölümüne yükle. Yeni kişisel hesaplarda yayından önce kapalı test şartı olabilir, güncel kuralı Play Console'dan kontrol et.

## Güncelleme yaparken
`www/index.html` dosyasını değiştirip commit et. Actions yeni bir `versionCode` ile otomatik yeni .aab üretir (Play her yüklemede artan sürüm ister).

## Notlar
- Oyun açılış ekranı Google Fonts'tan Baloo 2 yazı tipini yükler. İnternet yoksa yedek yazı tipiyle açılır, oyun yine çalışır.
- Oyunda şu an sahte bir banner alanı var. Gerçek reklam (AdMob) için ayrıca bir eklenti ve kod değişikliği gerekir.
- Uygulama kimliği `com.aksustudio.critterescape`. Play Store'a yükledikten sonra değiştirilemez; farklı bir kimlik istiyorsan `capacitor.config.json` içinde yüklemeden önce değiştir.
