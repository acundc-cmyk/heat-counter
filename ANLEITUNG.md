# Heat Zähler – eigenes F-Droid-Repo für die Spielrunde

Ziel: Du veröffentlichst die App in einem **eigenen F-Droid-Repository**.
Deine Mitspieler fügen einmal eine Adresse in der F-Droid-App hinzu und bekommen
die App – und alle künftigen Updates – automatisch. GitHub übernimmt dabei den
kompletten Bau; du brauchst keinen eigenen Server.

Der Ablauf ist einmalig etwas Einrichtung, danach genügt für jedes Update ein Klick.

---

## Schritt 1 – Signaturschlüssel erstellen (einmalig)

Der Schlüssel signiert die App. Er muss immer derselbe bleiben, sonst schlagen
spätere Updates fehl. **Bewahre die Datei und die Passwörter gut auf.**

Auf einem PC mit Java im Terminal:

```
keytool -genkey -v -keystore keystore.jks -alias heat \
  -keyalg RSA -keysize 2048 -validity 10000 \
  -storepass MEINPASSWORT -keypass MEINPASSWORT -dname "CN=Heat Zaehler"
```

Ersetze `MEINPASSWORT` durch ein eigenes Passwort. Es entsteht die Datei `keystore.jks`.

Dann die Datei in Text (Base64) umwandeln – den Inhalt brauchst du gleich:

- Linux/macOS: `base64 -w0 keystore.jks > keystore.b64`
  (macOS ohne `-w0`: `base64 -i keystore.jks | tr -d '\n' > keystore.b64`)
- Windows (PowerShell): `[Convert]::ToBase64String([IO.File]::ReadAllBytes("keystore.jks")) > keystore.b64`

---

## Schritt 2 – Projekt zu GitHub hochladen

1. Konto auf https://github.com anlegen (falls nötig).
2. **+ -> New repository**, Namen vergeben (z. B. `heat-zaehler`), **Public** waehlen,
   **Create repository**.
3. **uploading an existing file** waehlen und den **Inhalt** dieser ZIP hochladen
   (die Ordner `.github`, `app`, die Dateien `settings.gradle`, `build.gradle` usw.).
   Wichtig: `settings.gradle` muss oben im Repo liegen, nicht in einem Unterordner.
   **Wichtig:** Lade **niemals** `keystore.jks`, `keystore.b64` oder
   `keystore.properties` hoch – die gehoeren geheim (die `.gitignore` verhindert das
   normalerweise schon).

---

## Schritt 3 – Geheimnisse (Secrets) hinterlegen

Im GitHub-Repo: **Settings -> Secrets and variables -> Actions -> New repository secret**.
Lege diese vier an:

- `KEYSTORE_B64`  = der komplette Inhalt der Datei `keystore.b64`
- `KEYSTORE_PASS` = dein Keystore-Passwort
- `KEY_ALIAS`     = `heat`
- `KEY_PASS`      = dein Schluessel-Passwort

---

## Schritt 4 – GitHub Pages einschalten

**Settings -> Pages -> Build and deployment -> Source: "GitHub Actions"** auswaehlen.

---

## Schritt 5 – Repo bauen lassen

Reiter **Actions -> "F-Droid-Repo veroeffentlichen" -> Run workflow**.
(Alternativ startet er automatisch, sobald du eine Version taggst, z. B. `v1.0`.)
Nach 2–4 Minuten ist alles fertig.

Deine Repo-Adresse lautet dann:

```
https://DEINNAME.github.io/DEINREPO/fdroid/repo
```

(`DEINNAME` = dein GitHub-Benutzername, `DEINREPO` = der Repo-Name, beide klein.)
Es gibt zusaetzlich eine kleine Info-Seite unter
`https://DEINNAME.github.io/DEINREPO/`, die die Adresse anzeigt.

---

## Schritt 6 – In der Spielrunde einrichten

Jeder Mitspieler:

1. Installiert die **F-Droid-App** von https://f-droid.org (einmalig
   "Installation aus unbekannten Quellen" fuer den Browser erlauben).
2. In F-Droid: **Einstellungen -> Repositories -> +** und die Repo-Adresse aus
   Schritt 5 eintragen (oder du schickst ihnen die Info-Seite / einen QR-Code davon).
3. Fertig – "Heat Zaehler" erscheint in F-Droid, laesst sich installieren und
   aktualisiert sich kuenftig von selbst.

---

## Updates veroeffentlichen

Wenn du die App spaeter aenderst:

1. In `app/build.gradle` `versionCode` (Zahl **erhoehen**, z. B. 2) und
   `versionName` (z. B. "1.1") anpassen. Der `versionCode` **muss** bei jedem
   Update groesser sein.
2. Die neue `index.html` unter `app/src/main/assets/` austauschen, falls du die
   App bearbeitet hast.
3. Aenderungen committen und den Workflow erneut laufen lassen (oder neu taggen).

Da immer mit demselben Schluessel signiert wird, kommen die Updates sauber an.

---

## Noch einfacher? (Alternative ohne eigenes Repo)

Wenn dir das zu viel ist: Deine Runde kann auch die App **Obtainium** (in F-Droid
verfuegbar) nutzen. Damit fuegt jeder einmal die Adresse deines GitHub-Repos hinzu
und bekommt die App direkt aus den "Releases" samt Updates – ganz ohne
Repo-Einrichtung. Sag Bescheid, dann richte ich dir stattdessen diesen Weg ein.

---

## Nur schnell testen?

Die Datei `app/src/main/assets/index.html` laesst sich auch direkt in einem
Handy-Browser oeffnen (Menue -> "Zum Startbildschirm hinzufuegen"). Und der zweite
Workflow "APK bauen" erzeugt jederzeit eine einzelne Test-APK zum Sideloaden.
