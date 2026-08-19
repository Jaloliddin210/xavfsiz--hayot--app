# "Xavfsiz Hayot" ilovasini Play Market'ga chiqarish — to'liq yo'l xaritasi

Veb-ilova endi PWA (Progressive Web App) qilib tayyorlandi (`manifest.json`, `service-worker.js`, ikonkalar qo'shildi). Endi uni **TWA (Trusted Web Activity)** texnologiyasi orqali haqiqiy Android ilovasiga aylantiramiz. Bu — Twitter, Starbucks, Spotify kabi ko'plab kompaniyalar veb-ilovalarini Play Market'ga chiqarish uchun ishlatgan rasmiy Google usuli. Kod deyarli qayta yozilmaydi.

---

## 1-bosqich: Ilovani internetga joylashtirish (hosting)

TWA ishlashi uchun ilova **haqiqiy HTTPS domenda** turishi shart (Google bu orqali ilovani tekshiradi). Eng oson va bepul variantlar:

- **Firebase Hosting** (tavsiya etiladi, Google'ning o'zi) — `firebase.google.com`
- **Netlify** — `netlify.com` (papkani shunchaki sudrab tashlash kifoya)
- **Vercel** — `vercel.com`

**Netlify orqali eng tez yo'l:**
1. netlify.com'da ro'yxatdan o'ting
2. "Add new site" → "Deploy manually"
3. `xavfsiz-hayot-app` papkasini sudrab tashlang
4. Sizga `https://xavfsiz-hayot.netlify.app` kabi manzil beriladi

Bu manzilni saqlab qo'ying — keyingi bosqichlarda kerak bo'ladi.

> Kelajakda haqiqiy domen olsangiz (masalan `xavfsizhayot.uz`), shu manzilga o'tkazishingiz mumkin.

---

## 2-bosqich: Bubblewrap bilan Android loyihasini yaratish

Kompyuteringizda quyidagilar kerak: **Node.js** (nodejs.org) va **Java JDK 17**.

```bash
npm install -g @bubblewrap/cli

bubblewrap init --manifest=https://SIZNING-MANZIL.netlify.app/manifest.json
```

Bubblewrap sizdan bir nechta savol so'raydi (paket nomi, ilova nomi va h.k.) — deyarli hammasini standart holicha qoldirsangiz bo'ladi. Paket nomi uchun tavsiya:

```
uz.gov.fvv.xavsizhayot
```

Jarayon oxirida u avtomatik ravishda **Android Studio loyihasi** va imzo kaliti (keystore) yaratadi. Keystore faylini (`android.keystore`) juda ehtiyotlik bilan saqlang — uni yo'qotsangiz, ilovani keyinchalik yangilay olmaysiz.

---

## 3-bosqich: Digital Asset Links — saytni ilova bilan "bog'lash"

Bubblewrap sizga `assetlinks.json` faylini beradi (namunasi quyida). Buni saytingizning aynan shu manzilida joylashtirishingiz kerak:

```
https://SIZNING-MANZIL.netlify.app/.well-known/assetlinks.json
```

Namuna tuzilma (ilova papkasida ham qoldirildi — `assetlinks-namuna.json`):

```json
[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "uz.gov.fvv.xavsizhayot",
    "sha256_cert_fingerprints": ["BUBBLEWRAP_SIZGA_BERGAN_FINGERPRINT"]
  }
}]
```

Bu fayl bo'lmasa, ilova brauzer manzil satrini ko'rsatib qo'yadi (TWA "to'liq ekran" rejimida ishlamaydi).

---

## 4-bosqich: APK/AAB yig'ish va sinash

```bash
bubblewrap build
```

Bu sizga `app-release-signed.apk` (telefonga o'rnatib sinash uchun) va `app-release-bundle.aab` (Play Market uchun) fayllarini beradi. APK faylni telefoningizga yuborib, o'rnatib ko'ring — SOS tugmasi, xarita, GPS hammasi xuddi veb-versiyadagidek ishlashi kerak.

---

## 5-bosqich: Google Play Console'da chop etish

1. **play.google.com/console** — ro'yxatdan o'tish, bir martalik $25 to'lov
2. "Create app" — ilova nomi, til (o'zbek), kategoriya (Ma'lumotnoma yoki Turmush tarzi)
3. Kerakli materiallar:
   - Ilova ikonkasi (512×512) — `icons/icon-512.png` tayyor
   - Skrinshotlar (kamida 2 ta, telefon o'lchamida)
   - Qisqa va to'liq tavsif (konsepsiya hujjatidagi matndan foydalanish mumkin)
   - Maxfiylik siyosati havolasi (shart — alohida sahifa kerak bo'ladi)
4. `.aab` faylni yuklash
5. Kontent reytingi so'rovnomasini to'ldirish
6. "Favqulodda xizmatlar" toifasidagi davlat ilovasi bo'lgani uchun, agar rasman FVV nomidan chiqarilsa, **tashkilot tasdiqlash (organization verification)** talab qilinishi mumkin — FVV yuridik hujjatlari kerak bo'ladi

Tekshiruvdan o'tish odatda 1–3 kun davom etadi.

---

## Muhim eslatma: hozirgi test ma'lumotlari

Hozirgi versiyada ogohlantirishlar, hodisalar ro'yxati va oila holati **sun'iy (mock)** ma'lumotlar. Play Market'ga rasman chiqarishdan oldin bularni haqiqiy backend bilan almashtirish kerak bo'ladi:

- Hodisa xabarlari → haqiqiy serverga saqlanishi (baza)
- Ogohlantirishlar → Gidrometeorologiya/seysmologik xizmatlar API'siga ulanish
- SOS chaqiruvi → FVV dispetcherlik tizimiga real integratsiya
- Push-bildirishnomalar → Firebase Cloud Messaging orqali haqiqiy xabarlar

Bularni xohlasangiz, keyingi bosqichda backend qismini birga tayyorlab chiqamiz.
