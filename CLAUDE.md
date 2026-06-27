# Engineering-Standards & Lessons Learned (projektübergreifend)

Diese Regeln stammen aus dem Eurowings/AAA-PM-Cockpit-Projekt und gelten als Default
für **alle zukünftigen Web-/Client-Apps** (besonders Single-File-Apps mit Cloud-Sync).

## 1. Datenpersistenz & Cloud-Sync (höchste Priorität — hier ging Nutzerdaten verloren)
- **Niemals lokale Daten mit leerem/frischem Seed in die Cloud schreiben.** Beim Boot
  lokalen Stand OHNE Seeding lesen; nur seeden, wenn lokal UND Cloud leer sind.
- **Cloud nur übernehmen, wenn sie wirklich Daten hat UND neuer ist** (oder lokal leer).
  Sonst lokal behalten und hochschieben. Nie einen Blank-Stand pushen.
- **Recency NICHT über die lokale `Date.now()`-Uhr** mehrerer Geräte vergleichen → Uhren-Drift.
  Server-Zeitstempel (`updated_at`) verwenden.
- **Mehrbenutzer = kein Last-Write-Wins auf einem ganzen JSON-Blob.** Pro Projekt/Task
  speichern und feldweise mergen oder optimistic-locking (compare-and-set). Aktives
  Projekt/Eingabe-Fokus über Remote-Updates hinweg erhalten.
- **Speicher-Fehler nie verschlucken.** `localStorage.setItem` in try/catch → Erfolg
  zurückgeben; bei Fehler sichtbar warnen (Badge/Toast) + automatische JSON-Sicherung.
  localStorage-Limit ~5 MB: große Importe sprengen es → IndexedDB oder Server.
- **Browser-Daten löschen (Cookies/Site-Data) wipt localStorage** → Cloud-Login muss
  zuverlässig restaurieren. Nutzer dazu auf „Backup (JSON)" + Login hinweisen.

## 2. Sicherheit
- **HTML-Escaper MUSS auch `"` und `'` escapen** (nicht nur `& < >`), sonst Attribut-Ausbruch.
  **Jede** Interpolation von Nutzerdaten in `innerHTML` escapen (auch `title=`, `value=`).
  Bei High-Churn-Renderern lieber `textContent`/DOM statt String-`innerHTML`.
- **Untrusted JSON** (Restore/Import/Cloud-Payload) gegen ein **Schema validieren**
  (Felder whitelisten, Typen coercen, Längen kappen, Größe begrenzen) — nie blind übernehmen.
- **CDN-Skripte pinnen + SRI** (`integrity`, `crossorigin`); CSP per `<meta>` (GitHub Pages
  kann keine Header). Lib/Fonts für Enterprise selbst hosten.
- **Passwort-Policy ≥12 + Komplexität**; Supabase „leaked password protection" serverseitig.
- **Anon-Key im Client ist nur OK, wenn RLS strikt `auth.uid() = user_id`** erzwingt
  (select/insert/update/delete) + DB-`CHECK` auf Wertgröße. Client-`user_id`-Filter ≠ Isolation.

## 3. Korrektheits-Anti-Patterns (immer prüfen)
- **Kein hartkodiertes „Heute"/Datum.** `TODAY` aus `new Date()` ableiten; alle „As of"-Strings
  aus einer Quelle rendern. (Frozen date = Überfälligkeit/Reports dauerhaft falsch.)
- **Divide-by-zero guarden** (`/length`, `/total`) → sonst `NaN%` bei leeren Listen.
- **Enum-Dropdowns ohne Leeroption** (Status/Priorität/Ja-Nein) → keine ungültigen Werte,
  die Sortierung/Klassen-Lookups vergiften.
- **Regex verankern** (z. B. ID-Normalisierung am End-Token `/-0*(\d+)$/`, global wo nötig).
- **Defaults aus dem aktiven Kontext** nehmen (z. B. Workstream des aktiven Projekts),
  nicht aus globalen Listen.
- **Datum validieren** (`start ≤ due`); negative Dauern/leere Datumsfelder abfangen (kein `NaN`).
- **IDs eindeutig halten**; dangling Dependencies sichtbar markieren.

## 4. UX & Robustheit
- **Keine nativen `prompt()`/`confirm()`/`alert()` für (destruktive) Aktionen** — Browser
  blockieren sie nach mehreren Dialogen → Buttons „tun nichts mehr". Eigene In-App-Modals.
- **Destruktive Aktionen**: Auto-Backup davor, getippte Bestätigung, Papierkorb, **Undo**.
- **Speichern ehrlich rückmelden** (✓/⚠), nicht pauschal „gespeichert".
- **Print/PDF**: globale `table{min-width:…}` sticht Druck-Layout → am Druck-Table per
  Inline-Style `width:100%;table-layout:fixed;min-width:0` erzwingen, sonst Spalten abgeschnitten.
- Genau **ein** Print-Button pro Seite.

## 5. Accessibility, i18n, Performance (Enterprise)
- Modals: `role="dialog" aria-modal`, Focus-Trap, Esc, Fokus zurückgeben. Echte
  `input`/`textarea` + Labels statt `contenteditable`. Status nicht nur über Farbe (Icon+Text).
  Tabs/Karten tastaturbedienbar. Kontrast ≥4.5:1. `aria-live` für Sync/Save-Status.
- **i18n-Layer** (DE/EN) statt hartkodierter Strings; `Intl.DateTimeFormat`; in Excel echte
  Datumszellen statt Text.
- **Nicht bei jedem Edit alles neu rendern**: nur betroffene Zeile aktualisieren, teure
  Berechnungen (z. B. Critical Path) memoisieren, DOM-Reads/Writes batchen, Tabelle virtualisieren.

## 6. Fehlende Enterprise-Features (Default-Backlog)
Rollen/Berechtigungen · Audit-Trail/History (wer/wann/was) · Kommentare/Anhänge/Notifications ·
projektübergreifende Suche · gespeicherte Ansichten + Bulk-Edit + persistente Filter ·
echte Nutzer-Referenzen statt Freitext-Owner · GDPR (Konto/Daten löschen, Retention) ·
Schema-Versioning + Migrations-Framework.

## 7. Prozess
- Vor jedem Push: **JS-Syntaxcheck** (Inline-Script extrahieren → `node --check`).
- Single-File-Prototyp ist OK für MVP, aber: keine Tests/Build/Lint/Monitoring = nicht
  enterprise-ready. Leere `catch(e){}` vermeiden (verstecken Datenverlust).
- Persönliche Copyrights/Branding nicht hartkodieren — firmen-/config-abhängig.
