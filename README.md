# Buchhaltung · DRK OV Loppersum

Kassenbuch und Mitgliederliste für Kassenwart, Vorsitz und Kassenprüfer des DRK-Ortsvereins Loppersum.
Die App läuft im Browser auf iPad, Handy und PC und lässt sich auf dem Startbildschirm installieren (Name „DRK Kasse“).

**Adresse:** <https://ajzeytin-beep.github.io/Buchhaltung-Loppersum/>

## Funktionen

**Kassenbuch**

- Zwei getrennte Bereiche:
  - **Getränkekasse:** Getränkepauschale der Mitglieder, Getränkeeinkauf.
  - **Verein:** Rechnungen, Spenden und andere Einnahmen/Ausgaben des Vereins.
- **Konten frei anlegen:** Barkassen und Bankkonten, die IBAN kann später eingetragen werden. Pro Konto gibt es einen Anfangsbestand je Bereich.
- **Belegnummer automatisch und fortlaufend pro Jahr:** `00001-26`, `00002-26` … ab Januar wieder `00001-27`.
- Einnahme, Ausgabe oder **Umbuchung** (z. B. Bargeld zur Bank). Laufender Saldo, Summen und Bestände werden automatisch berechnet.
- **Beleg fotografieren:**
  - Die App liest Betrag, Datum, Händler und Rechnungsnummer aus dem Foto und füllt die Felder vor (gelb markiert).
  - Vor dem Speichern muss bestätigt werden, dass alles mit dem Beleg verglichen wurde.
  - Die Texterkennung läuft komplett auf dem Gerät. Gespeichert wird das Foto verkleinert im Supabase-Speicher.
- **Storno statt Löschen:** Eine Gegenbuchung mit neuer Nummer, damit alles für die Kassenprüfung nachvollziehbar bleibt.
- Filter, Suche, CSV-Export für Excel.
- **Jahresabschluss** mit Beständen, Kategorien, allen Buchungen und Unterschriftenfeldern. Drucken oder als PDF speichern.

**Mitglieder**

- Name, Adresse, Geburtstag, Eintritt, Austritt, Telefon, E-Mail, Bemerkung.
- Getränkepauschale pro Mitglied **pro Monat** oder **pro Jahr**. Die App zeigt, was bezahlt und was offen ist. „Kassieren“ bucht sie direkt.
- Geburtstage und Jubiläen der nächsten 30 Tage in der Übersicht.
- Import aus Excel/CSV mit Vorschau und Vorlage. Export als CSV.

**Zugänge**

| Rolle | Darf |
|---|---|
| Admin | alles, dazu Zugänge freigeben, ändern und entfernen |
| Kassenwart, Vorsitz | buchen, Mitglieder und Konten bearbeiten |
| Kassenprüfer | alles sehen, nichts ändern |

Neue Personen öffnen die Adresse und tippen auf **„Zugang beantragen“**. Der Admin gibt sie unter **Zugänge** frei.

## Datenbank

Die App nutzt dieselbe Supabase-Datenbank wie der [Sani-Trainer](https://github.com/ajzeytin-beep/DRK-Loppersum-). Ihre Tabellen beginnen mit `vw_`, und die Daten sind über eigene Rollen geschützt. Teilnehmende und Ausbilder des Sani-Trainers sehen nichts davon.

Einrichtung (bereits erledigt, nur zur Dokumentation):

1. Im Supabase **SQL Editor** `supabase/update-5.sql` und danach `supabase/update-6.sql` ausführen.
2. Unter der Adresse oben registrieren oder anmelden, dann einmalig zum Admin machen:

   ```sql
   update public.vw_users set role = 'admin', approved_at = now() where email = 'deine@email.de';
   ```

3. In Supabase unter **Authentication → URL Configuration → Redirect URLs** die Adresse `https://ajzeytin-beep.github.io/Buchhaltung-Loppersum/` hinzufügen. Dann funktioniert „Passwort vergessen“ auch hier.

**Datenbank wach halten:** Kostenlose Supabase-Projekte pausieren nach 7 Tagen ohne Nutzung. Der Ablauf `.github/workflows/datenbank-wach-halten.yml` fragt die Datenbank deshalb zweimal pro Woche kurz an (Repository → **Actions** → „Datenbank wach halten“). Schlägt er fehl, ist das Projekt vermutlich pausiert: bei supabase.com anmelden und das Projekt mit „Restore project“ wieder starten.

> Adressen und Geburtstage sind personenbezogene Daten. Ausgetretene Mitglieder sollten nach Ablauf der Aufbewahrungsfristen gelöscht werden.

## Veröffentlichen (GitHub Pages)

Im Repository unter **Settings → Pages** als Quelle den Branch `main` mit dem Ordner `/ (root)` wählen.

## Technik

Reines HTML/CSS/JavaScript ohne Build-Schritt.

| Ordner | Inhalt |
|---|---|
| `js/` | die App |
| `shared/` | Anmeldung, Einstellungen (`config.js`), Symbole |
| `vendor/` | [tesseract.js](https://github.com/naptha/tesseract.js) (Apache-2.0) für die Belegerkennung, [SheetJS](https://sheetjs.com) (Apache-2.0) für den Excel-Import |
