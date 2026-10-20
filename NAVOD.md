# Koleje – jak dostat hru do telefonu

Složka obsahuje hotovou webovou aplikaci (PWA). Po instalaci má ikonu na ploše, běží přes celou obrazovku a funguje i bez internetu.

## 1. Zveřejni hru na internetu (zdarma, asi 2 minuty)

1. Složka `zeleznicni-inzenyr` je v Dokumentech (C:\Users\filip\Documents).
2. Otevři **app.netlify.com/drop** a přihlas se (jde to i přes Google).
3. Přetáhni celou složku `zeleznicni-inzenyr` z Průzkumníka do okna prohlížeče.
4. Netlify ti dá adresu, třeba `https://zeleznicni-inzenyr.netlify.app`. Název můžeš změnit v nastavení webu (Site configuration → Change site name).

Funguje to i na GitHub Pages nebo jakémkoli jiném hostingu. Důležité je, aby adresa začínala na `https://`.

## 2. Nainstaluj ji do telefonu

- **Android (Chrome):** otevři adresu a klepni na „Instalovat aplikaci“. Pokud se nabídka neukáže, dej menu ⋮ → „Přidat na plochu“ → „Instalovat“.
- **iPhone (Safari):** otevři adresu, klepni na Sdílet → „Přidat na plochu“.

Bez přihlášení se postup ukládá v telefonu.

## 3. Zapni přihlášení přes Google (volitelné)

Přihlášení ukládá postup k Google účtu a skóre posílá do společného žebříčku. Potřebuješ k tomu zdarma Firebase.

1. Otevři **console.firebase.google.com** → „Vytvořit projekt“ (třeba `zeleznicni-inzenyr`). Google Analytics můžeš vypnout.
2. **Authentication** → Začít → Sign-in method → **Google** → Povolit → Uložit.
3. **Authentication → Settings → Authorized domains** → přidej svou adresu z Netlify, třeba `zeleznicni-inzenyr.netlify.app`.
4. **Firestore Database** → Vytvořit databázi → umístění `eur3 (Europe)` → režim „production“.
5. Ve Firestore otevři záložku **Rules**, vlož tohle a dej Publikovat:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /players/{uid} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
    match /scores/{uid} {
      allow read: if true;
      allow write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

6. **Nastavení projektu (ozubené kolo) → Obecné → Vaše aplikace** → ikona `</>` (Web) → zaregistruj aplikaci. Firebase ukáže blok `firebaseConfig`.
7. Otevři v aplikační složce soubor `config.js` a místo `null` vlož ten blok, například:

```js
window.FIREBASE_CONFIG = {
  apiKey: "AIza...",
  authDomain: "zeleznicni-inzenyr.firebaseapp.com",
  projectId: "zeleznicni-inzenyr",
  appId: "1:1234567890:web:abcdef"
};
```

8. Přetáhni složku na Netlify znovu (Deploys → přetáhnout novou verzi).

Ve hře pak klepni nahoře vlevo na „Hráč“ → **Přihlásit přes Google**. Postup se uloží k účtu a načte se i na jiném telefonu.

## 4. Google Play (až bude hra hotová)

Z nainstalovatelné webové aplikace jde udělat balíček pro Google Play:

1. Otevři **pwabuilder.com** a zadej adresu hry.
2. Zvol **Android → Generate package**. Dostaneš soubor `.aab` pro Google Play.
3. Vývojářský účet na Google Play stojí jednorázově 25 USD.

Pro App Store je potřeba Mac a vývojářský účet Apple (99 USD ročně).

## Co je ve složce

| Soubor | K čemu je |
|---|---|
| `index.html` | samotná hra |
| `config.js` | nastavení přihlášení přes Google |
| `three.min.js` | 3D knihovna, je tu kvůli offline hraní |
| `manifest.webmanifest`, `icons/` | název a ikona aplikace |
| `sw.js` | ukládá hru pro offline hraní |
| `_headers` | Netlify díky němu vždy pošle nejnovější verzi hry |
