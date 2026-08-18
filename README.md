# GoalZone — Reservierungsalgorithmus

Interaktiver Echtzeit-Reservierungsalgorithmus als Web-App zur Vorhersage von Kurs-No-Shows und dynamischen Freigabe zusätzlicher Kursplätze, kombiniert mit KI-generierten Handlungsempfehlungen via Anthropic API.

**[➡ Live-Demo ansehen](https://lebensrauminmir-lenka.github.io/Live-Reservierungskonsole/)** <!-- Link aktiviert -->

![Status](https://img.shields.io/badge/status-Proof--of--Concept-yellow)
![License](https://img.shields.io/badge/license-MIT-blue)

---

## Worum geht es?

GoalZone (fiktives Fitnessstudio-Szenario) möchte die Anzahl verfügbarer Kursplätze erhöhen, ohne die Kapazität physisch aufzustocken. Die Idee: Wenn sich vorhersagen lässt, dass ein Mitglied nicht zum gebuchten Kurs erscheint, kann der Platz an ein weiteres Mitglied vergeben werden (Overbooking-Prinzip, ähnlich wie bei Fluggesellschaften).

## Funktionen

- **Tages-Übersicht:** Alle Kursslots des Tages auf einen Blick, mit Live-Auslastung und geschätzter Zusatzkapazität
- **Detailansicht pro Slot:** Kapazität, Buchungen, erwartete No-Shows und geschätzt freie Plätze
- **Sortierte Buchungsliste:** Nach No-Show-Risiko sortiert, mit farblicher Ampel-Kennzeichnung
- **Neue Buchung hinzufügen:** Live-Berechnung der No-Show-Wahrscheinlichkeit beim Eintragen
- **KI-Einschätzung pro Slot:** Auf Knopfdruck erzeugt Claude (via Anthropic API) eine kurze Handlungsempfehlung zur Kapazitätslage des gewählten Slots

## Wie das Modell funktioniert

Das Herzstück ist eine **logistische Regression**, trainiert mit scikit-learn auf 1.500 historischen Buchungen (Datensatz `fitness_class_2212.csv`).

Im Unterschied zu einer aufwändigeren One-Hot-codierten Variante nutzt dieses Modell ein **kompaktes Feature-Schema**: Wochentag und Kurskategorie werden direkt als Zahlen (ordinal) codiert statt in einzelne 0/1-Spalten aufgeteilt. Das ergibt weniger Modellparameter bei nur geringfügig niedrigerer Genauigkeit — ein bewusster Trade-off zwischen Einfachheit/Nachvollziehbarkeit und Präzision.

| Metrik | Wert |
|---|---|
| Genauigkeit (Accuracy) | ≈ 76 % |
| AUC | ≈ 0.82 |

Die trainierten Koeffizienten (`INTERCEPT`, `COEF`) sind direkt im JavaScript-Code (`index.html`) hinterlegt. Es findet **kein Live-Training im Browser statt** — nur die Anwendung des bereits gelernten Modells (Inferenz). Das Trainingsskript liegt unter [`/training`](./training).

## ⚠️ Wichtiger Hinweis zur Anthropic-API-Integration

Der "KI-Einschätzung"-Button ruft `https://api.anthropic.com/v1/messages` auf. **Dieser Aufruf funktioniert nur innerhalb der Claude.ai-Artefakt-Umgebung**, in der die Authentifizierung automatisch im Hintergrund erfolgt.

Bei eigenständigem Hosting (z. B. GitHub Pages, wie in diesem Repo) schlägt der Aufruf fehl — die App fängt das ab und zeigt automatisch einen lokalen Demo-Fallback-Text an. Die Kernfunktion der App (Wahrscheinlichkeitsberechnung, Kapazitätslogik, Buchungsverwaltung) bleibt davon vollständig unberührt.

### Für einen echten Produktivbetrieb wäre zusätzlich nötig:

1. Ein eigener **API-Schlüssel** (Anthropic Console) — niemals im Frontend-Code hinterlegen
2. Ein **Backend** (z. B. eine Node.js-Funktion, ein n8n-Webhook, oder eine Serverless Function auf Vercel/Netlify), das den Schlüssel sicher hält und Anfragen weiterleitet
3. Anpassung von `ANTHROPIC_ENDPOINT` im Code auf die eigene Backend-URL

## Datengrundlage & Datenschutz

Der verwendete Trainingsdatensatz enthält keine personenbezogenen Klardaten. Für den Einsatz mit echten Mitgliederdaten (z. B. via CRM-Anbindung) sind DSGVO-relevante Punkte zu prüfen — insbesondere Rechtsgrundlage der Verarbeitung, Transparenzpflichten gegenüber Mitgliedern und ein gültiger Auftragsverarbeitungsvertrag (AVV) mit Anthropic bei Nutzung der kommerziellen API. Diese README ersetzt keine Rechtsberatung.

## Projektstruktur

```
goalzone-reservierungsalgorithmus/
├── index.html          # Vollständige App (Frontend + eingebettetes Modell)
├── training/
│   ├── train_model.py  # Python-Skript zum Trainieren des Modells (scikit-learn)
│   └── requirements.txt
├── data/
│   └── fitness_class_2212.csv
├── LICENSE
├── .gitignore
└── README.md
```

## Lokal ausführen

Keine Installation nötig — eine einzelne, eigenständige HTML-Datei ohne Build-Prozess:

```bash
git clone https://github.com/<dein-username>/goalzone-reservierungsalgorithmus.git
cd goalzone-reservierungsalgorithmus
open index.html   # oder Datei direkt im Browser öffnen
```

## Tech-Stack

- **Frontend:** Vanilla HTML/CSS/JavaScript (kein Framework, keine Build-Tools)
- **Modelltraining:** Python, pandas, scikit-learn
- **KI-Textgenerierung:** Anthropic API (Claude)

## Datenquelle

Der Trainingsdatensatz basiert auf einem öffentlich verfügbaren Fitnessstudio-Buchungsdatensatz (`fitness_class_2212.csv`), verwendet im Rahmen einer Weiterbildung zu Prozessautomatisierung & KI-Integration.

## Lizenz

Dieses Projekt steht unter der [MIT-Lizenz](./LICENSE).

## Autorin

Helena — KI-Managerin & Prozessautomatisierungs-Spezialistin
