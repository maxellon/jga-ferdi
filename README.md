# Foto-Quiz

Quiz mit Fotos und zwei Antworten (A/B). Die Spieler kommen per QR-Code mit dem Handy dazu, die Antworten laufen über eine Firebase-Datenbank und funktionieren so in jedem Netz (auch im Bus über mobile Daten).

- Host-Seite: `index.html` (Quiz anlegen, Spiel steuern)
- Spieler-Seite: `index.html?join=CODE` (steht im QR-Code) oder `index.html?spieler` (Code tippen)

## Einmalige Einrichtung: Firebase

1. https://console.firebase.google.com öffnen, mit Google-Konto anmelden, **Projekt hinzufügen** (Name z. B. `foto-quiz`). Google Analytics kann aus bleiben.
2. Links im Menü **Build → Realtime Database → Datenbank erstellen**. Standort **Belgium (europe-west1)**. Sicherheitsregeln: **Gesperrter Modus** wählen (wird gleich ersetzt).
3. Reiter **Regeln** öffnen, alles löschen und den Inhalt von `database.rules.json` aus diesem Repository einfügen, **Veröffentlichen**.
4. Zahnrad oben links → **Projekteinstellungen** → unten bei „Meine Apps“ auf das Web-Symbol `</>` → App-Spitzname `foto-quiz`, Hosting nicht ankreuzen, **App registrieren**.
5. Im angezeigten Code steht ein Block `const firebaseConfig = { apiKey: "…", … }`. Diese Werte in `index.html` oben im Block `FIREBASE_CONFIG` eintragen (`apiKey`, `authDomain`, `databaseURL`, `projectId`). Wenn `databaseURL` dort fehlt: die Adresse steht oben in der Realtime-Database-Ansicht (endet auf `firebasedatabase.app`).

Die Werte in `FIREBASE_CONFIG` sind keine Geheimnisse, sie gehören in jede Firebase-Web-App. Der Schutz liegt in den Datenbankregeln.

## Ablauf im Unterricht oder Bus

1. Host-Seite öffnen, **Neues Quiz**, Fotos hinzufügen, Antworten und die richtige Lösung eintragen. Quizze bleiben im Browser gespeichert; **Als Datei sichern** legt zusätzlich eine JSON-Datei an.
2. **Spiel starten**, Zeit pro Frage wählen, **Spiel eröffnen**. Es erscheint ein vierstelliger Code mit QR-Code.
3. Spieler scannen den Code, geben ihren Namen ein und sehen „Du bist dabei“.
4. **Erste Frage**. Frage 1 ist immer eine Aufwärmfrage (Bild und Antworten im Editor oben änderbar, Standard ist eine Sonne); sie läuft wie jede andere, vor Frage 2 werden alle Punkte auf 0 gesetzt. Alle sehen das Foto und tippen A oder B. Nach Ablauf der Zeit (oder per **Auflösen**) erscheint die Verteilung, die richtige Antwort und bei jedem Spieler die Punkte.
5. Nach der letzten Frage: Rangliste. **Spiel löschen** entfernt die Daten aus der Datenbank; Spiele älter als zwei Tage werden beim nächsten Start automatisch gelöscht.

Punkte: 100 pro richtiger Antwort plus bis zu 100 Bonus für Schnelligkeit (der Bonus sinkt mit der Zeit bis zum Ablauf der Fragezeit). Die Auflösung zeigt die drei Schnellsten.

## JGA-App (`jga/`)

Eigene Seite unter `/jga/` mit denselben Firebase-Zugangsdaten: Zeitplan (änderbar), Karte (OpenStreetMap, Orte aus dem Zeitplan per „Auf Karte setzen“), Bierpong-Turnier (Teams tragen sich selbst ein, Teamname mit Vorschlägen, mit Walk-out-Song aus Vorschlägen oder frei – vergebene Songs ausgeblendet; Auslosung per Knopf mit Live-Aufdeckung in 2 Gruppen à 4 Teams, Halbfinale über Kreuz, Platz 3, Finale, live), Quiz-Zugang (zeigt automatisch den Code des gerade laufenden Foto-Quiz) und Hüttenfotos mit Infoblöcken. Alle dürfen alles bearbeiten („Bearbeiten“ oben rechts). Die Datenbankregeln brauchen zusätzlich den Knoten `jga` (siehe `database.rules.json`).

## Videos als Frage und Auflösung

Im Editor nimmt „Fotos oder Clips hinzufügen“ auch MP4-Dateien (max. 1,5 MB je Clip). Pro Frage lässt sich unter „Auflösung“ ein zweites Bild oder ein Auflösungsclip plus Erklärtext hinterlegen; das sehen Host und Spieler nach dem Schließen der Frage. Die Handys laden die nächste Frage samt Medien im Hintergrund vor.

Clips vorbereiten (ffmpeg, ergibt ca. 0,5–1 MB für 6 Sekunden):
`ffmpeg -i clip.mov -ss 0 -t 6 -vf "scale=-2:480,fps=25" -an -c:v libx264 -crf 28 -preset slow -movflags +faststart clip.mp4`

Reiterfolge: Plan, Hütte, Wie lange, Bierpong, Innsbruck, LRG, Börse, Quiz, Fotos, Teamwertung, Musik, Orga, Karte. Reiter **Börse**: Spielerbörse nur für Partnersuchende (Name, Stärke Quiz/Bierpong/LRG), „Team bilden“ füllt das Team-Formular beim Bierpong vor; optional auch über „Ich bin …“ („Habe bereits ein Team“ / „Suche noch einen Mitspieler“ mit Stärke). Reiter **Fotos**: Upload aus der Mediathek mit Namen, Feed Bild für Bild mit anonymem Daumen hoch/runter (eine Stimme pro Handy, änderbar), oben Top 10 des Tages mit Uploader. Reiter **Teamwertung**: Punkte je Bierpong-Team über Quiz, Bierpong, LRG, Sonstiges (manuell, über „Punkte eintragen“). Reiter **Musik**: Playlist-Wünsche und Umfragen mit Live-Ergebnis und **Orga** (Kasse mit automatischer Umlage, Teilnehmer mit Handynummer). Reiter **LRG**: zweites Turnier (Tennis) mit demselben Modul wie Bierpong. Reiter **Wie lange**: GPS-Entfernung und Fahrzeit (OSRM-Route); Richtung Hütte (Ziel per Adresse setzbar), bis jemand „Wir sind da“ drückt oder ein Handy die Hütte erreicht, dann für alle ausgeblendet (im Plan über Bearbeiten wieder einblendbar), ab Sa 06:00 Heimfahrt Richtung Oberesslingen. Reiter **Innsbruck**: Ablauf Innsbruck (Freitagsplan mit Uhrzeiten), Hinweg-Kachel (Bus 580 → S-Bahn mit „Loslaufen um“), Rückweg-Kachel (S-Bahn-Countdown; nach der letzten Bahn automatisch nur Nightliner 560N und Taxinummern); Fahrpläne in der App anpassbar, Links zur ÖBB-Auskunft; Gleis Innsbruck Hbf, Gleis Matrei und Nightliner-Haltestelle unter „Fahrplan ändern“ / „Zeiten ändern“ oder im Admin eintragbar, ohne Eintrag steht „Gleis: s. Anzeige“. Im Plan: „Aktuell“-Kachel (laufender Programmpunkt, nächster Punkt mit Countdown, vor dem Wochenende Tage bis zum Start; aktualisiert sich jede Minute), unter Hütte die Taxi-Liste und Wetter Innsbruck (Open-Meteo, ab ca. 16 Tage vorher). Beim ersten Öffnen ist die Anmeldung Pflicht (Name, Handynummer, Spitzname / Spielername mit deutschen und englischen Vorschlägen – vergebene werden ausgeblendet –, „Habe bereits ein Team“ oder „Suche noch einen Mitspieler“ mit Stärke); danach merkt sich das Handy alles. „Ich bin …“ oben rechts ändert den Namen pro Handy für Fotos, Stimmen und Ausgaben und nimmt die Handynummer für die Teilnehmerliste auf. Eigene Seite `jga/todo.html` (nicht in der Navigation): Orga-Liste zum Abhaken mit Namensfeld, wer was übernimmt.

## Admin (`jga/admin.html`)

PIN-geschützte Seite (Standard 2027, unter Einstellungen änderbar; Schutz gegen Zufallsklicks, keine echte Sicherheit), nicht in der Navigation: Überblick, Teilnehmer (bearbeiten, löschen, Doppelte zusammenführen), Teams beider Turniere (bearbeiten, Auslosung/Ergebnisse zurücksetzen), Börse, Fotos (löschen, Stimmen zurücksetzen, Titelbild), Musik, Kasse, Plan, Einstellungen („Wie lange“, Ziel, Fahrpläne, Quiz-Anzeige), Daten (Export als JSON, gezielte Resets, kompletter Neustart).
