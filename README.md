# Wildlife Exploration

## Projektübersicht

Das Wildlife Quiz & Exploration kombiniert spielerisches Lernen mit interaktiven Elementen, um Nutzer:innen die Welt der Wildtiere näherzubringen. Das Projekt umfasst eine beeindruckende 3D-animierte Landingpage, eine interaktive Tierkarten-Galerie sowie eine vielseitige Quiz-Seite, die mit einer API verknüpft ist. Ergänzt wird das Erlebnis durch spannende Vergleichsaufgaben, eine Joker-Funktion und dynamische Animationen.

## Features & Highlights
**Landingpage – Interaktiver 3D-Tiger**

- 3D-Tiger-Modell mit Three.js
- Hover-Effekt: Bewegt der/die Nutzer:in den Cursor, wird ein Spotlight aktiviert – verstärkt den Explorations-Vibe
- Scroll-Interaktion: Beim Scrollen dreht sich der Tiger um 90 Grad, wodurch ein immersiver Übergang zur nächsten Sektion entsteht
- One-Pager Integration: Die Landingpage ist mit Fullpage.js für eine nahtlose Navigation umgesetzt

## Card Gallery – Die Tierkarten-Sammlung

- Karten umdrehbar – Auf der Rückseite warten spannende Fun Facts zu jedem Tier
- Filter- & Sortierfunktion: Tiere nach Eigenschaften oder Tierarten gruppieren
- Illustrationen mit KI generiert – Alle Bilder wurden mithilfe von Stable Diffusion erstellt

## Quiz-Seite – Teste dein Wissen!

- API-basiertes Quiz (OpenTDB)
- Flexible Einstellungen
- 50:50-Joker: Reduziert falsche Antworten um 50 %

## Vergleichs-Quiz – Wer ist der Stärkste?
- Eigenentwickelter Fragetyp: Nutzer:innen vergleichen Tierdaten aus der Card Gallery
- Beispiel: Welches dieser Tiere ist am schwersten?
- Besondere Herausforderung: Die Kombination von externen API-Fragen mit den eigenen Daten


## Herausforderungen & Lösungsansätze
**Integration der externen API mit eigenen Daten**
Die Integration externer API-Fragen mit eigenen, selbst entwickelten Quiz-Daten (z.B. Tiervergleichs-Quiz) stellte eine technische Herausforderung dar.

**Optimierung des 3D-Modells & Beleuchtung**
Das Licht im 3D-Modell war anfangs zu dunkel oder falsch positioniert. Zudem lief die Animation ruckelig. Die Lösung bestand darin, manuelles Testen und Anpassen der X-, Y- und Z-Koordinaten durchzuführen, um die beste Lichtwirkung zu erzielen. 

**Optimierung der 3D-Modell-Animation**
Zu Beginn hatte die 3D-Modell-Animation Performance-Probleme und lief ruckelig. Durch den Einsatz der GSAP-Bibliothek konnte die Animation deutlich flüssiger und smoother gestaltet werden.


## Geplante Erweiterungen und Verbesserungen
- Strukturierung des Codes: Eine klarere und strukturiertere Programmierung könnte die Wartbarkeit und Erweiterbarkeit des Projekts verbessern.
- Responsives Design: Die mobile Ansicht könnte noch optimiert werden, um Layouts auf verschiedenen Geräten besser anzupassen.
- Der Footer wurde nicht implementiert, da er in beiden Fullpage-Sektionen nicht gut ausgesehen hätte. Eine angepasste Lösung wäre erforderlich, um den Footer nur auf der letzten Sektion korrekt darzustellen.
- Eine ChatGPT-Integration zur dynamischen Generierung von Quizfragen und zur Erweiterung der Aufgabentypen könnte das Spielerlebnis noch spannender gestalten.
- Interaktive Fortschrittsanzeige: Auf der Seite zur Schwierigkeits- und Fragenauswahl war geplant, ein 3D-Modell eines Weges zu integrieren, bei dem eine vorwärtsbewegende Kamera den Eindruck eines unendlichen Pfades vermittelt. Dies soll die Exploration weiter unterstreichen. Unterhalb des 3D-Modells könnte ein Progress Bar erscheinen mit der Anzeige: "You have explored [number]". Dieser Fortschritt basiert auf der Anzahl der richtig beantworteten Fragen oder der Anzahl der bereits beantworteten Fragen im Quiz.

