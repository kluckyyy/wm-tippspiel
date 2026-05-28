# ⚽ WM 2026 – Klassen-Tippspiel (Live)

**Live-App:** https://kluckyyy.github.io/wm-tippspiel/
**Repo:** https://github.com/kluckyyy/wm-tippspiel (Quelle: `index.html`)

Schüler tippen auf dem **eigenen Handy** (QR/Link), die **Ergebnisse zieht die App
automatisch** aus offenen WM-Daten, der **Live-Highscore** rechnet sich selbst.
Du musst keine Ergebnisse eintragen.

## So läuft es
1. **Du (Lehrer):** Live-App öffnen → es entsteht ein Spiel mit eigenem **Lehrer-Link**
   (bookmarken!) und einem **Schüler-Link + QR-Code** (Tab „Einladen").
2. **Schüler:** QR scannen → Spitzname eintippen → Spiele tippen. Fertig, keine App-Installation.
3. **Highscore** aktualisiert sich automatisch, sobald Ergebnisse da sind.

## Features
- **WM-2026-Gruppenphase** vorausgefüllt: alle **72 Gruppenspiele** A–L. Die Auslosung ist
  gegen `openfootball/worldcup.json` **verifiziert** (alle 12 Gruppen Team-für-Team identisch).
  *Hinweis: deckt die Gruppenphase ab; K.o.-Runde ist (noch) nicht enthalten.*
- **Ergebnisse automatisch** aus `openfootball/worldcup.json` (kein Eintragen) – End-to-End
  gegen die echten Daten getestet: alle 72 Spiele mappen, Heim/Gast-Orientierung stimmt.
- **Live-Highscore** über alle Geräte (Firebase Realtime Database).
- **Nicht getippte Spiele = 0:0.** Beim Prüfen Hinweis + Sprung-Links zu offenen Spielen.
- **Keine Doppelanmeldung:** jeder Spitzname nur einmal · 1 Eintrag pro Gerät ·
  „Anmeldung sperren"-Schalter · Mitspieler entfernen.
- **Datenschutz:** nur Spitznamen + Tipps, keine echten Namen/Schülerdaten. Sieht eine Eingabe
  nach einem echten Namen aus, muss man **aktiv bestätigen** (Echtname-Schutz).
- **Anpfiffzeit in deutscher Lokalzeit**; Tippen ist nach Anpfiff eines Spiels gesperrt (fair).
- **Offline-Warnbanner:** falls kein Live-Sync (Firebase fehlt / kein Netz), wird deutlich gewarnt,
  dass Tipps nur lokal bleiben – kein stiller Datenverlust.
- Punkte: exakt 4 · Tordifferenz 3 · Tendenz 2.

## ⚠️ Einmal-Setup (Firebase – durch Stefan, ~3 Min)
Damit der Live-Highscore über mehrere Geräte funktioniert, braucht die App eine
Firebase-Realtime-Database (kostenlos, nur Spitznamen):

1. https://console.firebase.google.com → **Projekt hinzufügen** (Google-Login, Name, 2× weiter).
2. Links **Realtime Database** → **Datenbank erstellen** → Region **europe-west1** → **Testmodus**.
3. **WICHTIG – Regeln dauerhaft setzen** (sonst sperrt der Testmodus nach 30 Tagen mitten in der WM):
   Reiter „Regeln" → eine der beiden Varianten einfügen → Veröffentlichen.

   **a) Quick-Start (empfohlen, garantiert lauffähig):** komplett offen –
   ```json
   { "rules": { ".read": true, ".write": true } }
   ```
   Für ein Klassenspiel ohne Schülerdaten (nur Spitznamen) völlig ausreichend.

   **b) Gehärtet (optional, etwas sicherer):** Inhalt von `firebase-rules.json` einfügen.
   Beschränkt Schreibzugriff auf den Spiel-Bereich und prüft Feldgrößen/-typen (ohne Login).
   *Empfehlung: erst mit Variante a starten; b nur, wenn du es testen kannst – ein Tippfehler
   in den Regeln kann sonst das Beitreten blockieren.*

   > Hinweis: Ohne Login lässt sich nicht verhindern, dass ein technisch versierter Schüler
   > per Browser-Konsole fremde Tipps ändert. Für ein Spaß-Klassenspiel ist das vertretbar;
   > eine echte Absicherung bräuchte ein Login (bewusst weggelassen für „einfach").
4. Zahnrad → **Projekteinstellungen** → unten **Web-App** (`</>`) registrieren → **Config kopieren**.
5. Config an Claude/in `index.html` (oben `FIREBASE_CONFIG`) einsetzen → pushen → live.

Ohne diese Config läuft die App im **Vorschau-Modus** (nur auf einem Gerät, kein Sync).

## Dateien
- `index.html` – die Live-Online-App (gehostet auf GitHub Pages).
- `firebase-rules.json` – optionale, gehärtete DB-Regeln (siehe Setup-Schritt 3b).
- `WMTippspiel.html` – ältere reine **Offline**-Variante (eine Datei, ohne Server/Sync), als Fallback.

## Geprüft
- Auslosung & Spielplan gegen `openfootball/worldcup.json` verifiziert (alle 12 Gruppen identisch).
- 122 Logik-Tests (Matching aller 72 echten Spiele, Punktelogik inkl. 0:0-Default) + 18 Tests
  (Syntax, Anpfiff-Sperre, Lokalzeit) – alle grün.
- Adversarialer Code-Audit (Firebase-Sync, Zeitzonen, UX, Robustheit/XSS): bestätigte Befunde behoben.
