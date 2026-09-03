# Fruit Gang MVP — Kurulum

7 dosya var. Hepsi **aynı klasörde, alt klasör olmadan** durmalı.

```
index.html
manifest.webmanifest
sw.js
icon-192.png
icon-512.png
icon-maskable-512.png
apple-touch-icon.png
```

Toplam süre ~10 dakika. Tek düzenleyeceğin yer `index.html` içindeki `FIREBASE_CONFIG` bloğu.

---

## Adım 1 — Firebase (~5 dk)

**1.1** `console.firebase.google.com` → Google hesabınla gir → **Create a project**
→ isim: `fruit-gang-mvp` → Google Analytics'i **kapat** → Create.

**1.2** Sol menü **Build → Realtime Database → Create database**
→ Location: **europe-west1** (İstanbul'a en yakın)
→ **Start in locked mode** → Enable.

Açılan sayfanın üstündeki adresi not al, şuna benzer:
`https://fruit-gang-mvp-default-rtdb.europe-west1.firebasedatabase.app`

**1.3** Sol menü **Build → Authentication → Get started → Sign-in method**
→ **Anonymous** → Enable → Save.

> Kimseye giriş ekranı çıkmaz. Uygulama arka planda anonim oturum açar.
> Tek amacı, veritabanına rastgele botların yazamaması.

**1.4** Realtime Database → **Rules** sekmesi → içeriği tamamen sil, şunu yapıştır → **Publish**:

```json
{
  "rules": {
    "fg-mvp": {
      ".read": "auth != null",
      ".write": "auth != null"
    }
  }
}
```

**1.5** Sol üstteki dişli → **Project settings** → aşağı in, **Your apps** → **Web `</>`** ikonu
→ App nickname: `fruit-gang` → **Register app**.

Ekranda `const firebaseConfig = { ... }` çıkacak. **Süslü parantezin içindeki satırları** kopyala.

**1.6** `index.html`'i bir metin editöründe aç (Notepad, VS Code, GitHub'ın kendi editörü — fark etmez).
Dosyanın ortalarında şu blok var:

```js
const FIREBASE_CONFIG = {
  // apiKey: "...",
  // authDomain: ...
};
```

Süslü parantezin içini kopyaladığın satırlarla değiştir. Sonuç şuna benzemeli:

```js
const FIREBASE_CONFIG = {
  apiKey: "AIzaSy...",
  authDomain: "fruit-gang-mvp.firebaseapp.com",
  databaseURL: "https://fruit-gang-mvp-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "fruit-gang-mvp",
  storageBucket: "fruit-gang-mvp.appspot.com",
  messagingSenderId: "1234567890",
  appId: "1:1234567890:web:abc123"
};
```

**Önemli:** `databaseURL` satırı Firebase'in verdiği config'de bazen olmuyor.
Yoksa 1.2'de not aldığın adresi elle ekle. O satır olmadan senkron çalışmaz.

---

## Adım 2 — GitHub Pages (~5 dk)

**2.1** GitHub'da **New repository** → isim: `fruit-gang-mvp` → **Public** → Create.

> Pages ücretsiz planda sadece public repo'da çalışır. Repo'nun kapalı kalması şartsa
> Cloudflare Pages'e geçeriz, adımlar neredeyse aynı.

**2.2** **Add file → Upload files** → 7 dosyayı birden sürükle → **Commit changes**.

**2.3** **Settings → Pages** → Source: **Deploy from a branch**
→ Branch: **main**, klasör: **/ (root)** → **Save**.

**2.4** 1–2 dakika bekle. Adresin:

```
https://KULLANICI-ADIN.github.io/fruit-gang-mvp/
```

Masaüstü tarayıcıda aç. Sağ üstte **live** yazıyorsa Firebase bağlandı demektir.

---

## Adım 3 — iPhone'a kurulum

1. Linki **Safari**'de aç. (Chrome'da "Ana Ekrana Ekle" düzgün çalışmaz.)
2. Alttaki **paylaş** ikonu (kutudan çıkan ok).
3. Listeyi aşağı kaydır → **Ana Ekrana Ekle**.
4. İsim `Fruit Gang` → **Ekle**.

Ana ekranda kendi ikonuyla belirir; açınca Safari çubuğu görünmez, tam ekran gelir.
Ekip arkadaşların da aynı linkle aynısını yapar.

**Android:** Chrome'da aç, kendisi **Uygulamayı yükle** teklif eder.

---

## Nasıl çalıştığını bilmen gerekenler

**Senkron.** Biri kutu işaretlediğinde diğerlerinin ekranı bir saniye içinde güncellenir —
yenileme yok. Her task ayrı yazılır, yani aynı anda iki kişi işaretlese biri diğerini ezmez.

**Durum göstergesi** (sağ üst):
- `live` → Firebase bağlı, ortak veri çalışıyor
- `this device only` → config yapıştırılmamış veya hatalı. Uygulama çalışır ama
  işaretler sadece o telefonda kalır. Config'i kontrol et, `databaseURL` var mı bak.

**Güncelleme.** Sana yeni sürüm verdiğimde repo'daki `index.html`'i değiştirip commit'lersin.
Telefonlar bir sonraki açılışta yeni sürümü alır. **Veri Firebase'de olduğu için hiçbir
işaret kaybolmaz** — Claude artifact'indeki "unpublish edersen veri gider" derdi burada yok.

**Offline.** Uçakta da açılır, son hali görünür. İşaretleme internet gelince gider.

**Güvenlik, dürüst hâliyle.** Anonim auth "linki bilen yazabilir"den bir tık iyidir,
gizlilik değildir. İki kişilik, hassas olmayan üretim verisi için makul. Sadece sizin
hesaplarınıza kilitlemek istersen Google girişine çevirebiliriz.

---

## Takılırsan

| Belirti | Sebep |
|---|---|
| `this device only` yazıyor | Config eksik/hatalı, ya da `databaseURL` yok |
| Sayfa boş / beyaz | Tarayıcı konsolunu aç (F12), kırmızı satırı bana at |
| 404 | Pages henüz yayılmadı, 2 dk bekle; veya dosyalar alt klasörde |
| İkon çıkmıyor | png'ler `index.html` ile aynı klasörde değil |
| Ekip farklı sayı görüyor | Birinde config eski; repo'daki son sürümü aldığından emin ol |
