# GoalZone — Live-Reservierungskonsole

Interaktiver Echtzeit-Reservierungsalgorithmus als Web-App zur Vorhersage von Kurs-No-Shows und dynamischen Freigabe zusätzlicher Kursplätze, kombiniert mit KI-generierten Handlungsempfehlungen via Anthropic API.

**[➡ Live-Demo ansehen](#) ** <!-- [[Link einfügen, sobald GitHub Pages aktiviert ist](https://github.com/lebensrauminmir-Lenka/Live-Reservierungskonsole/edit/main/README.md) ]()-->

![Status](https://img.shields.io/badge/status-Proof--of--Concept-yellow)
![License](https://img.shields.io/badge/license-MIT-blue)

---

## Worum geht es?

GoalZone (fiktives Fitnessstudio-Szenario) möchte die Anzahl verfügbarer Kursplätze erhöhen, ohne die Kapazität physisch aufzustocken. Die Idee: Wenn sich vorhersagen lässt, dass ein Mitglied nicht zum gebuchten Kurs erscheint, kann der Platz an ein weiteres Mitglied vergeben werden (Overbooking-Prinzip, ähnlich wie bei Fluggesellschaften).

Diese App demonstriert, wie ein trainiertes Vorhersagemodell und ein Sprachmodell (LLM) zusammenspielen können, um diese Entscheidung in Echtzeit nachvollziehbar zu unterstützen.

## Funktionen

- **Live-Formular:** Mitgliedsdaten eingeben (Mitgliedsdauer, Vorlaufzeit, Kurskategorie, Wochentag, Uhrzeit) → sofortige Wahrscheinlichkeitsvorschau
- **Scoreboard:** Zeigt in Echtzeit gebuchte Plätze, erwartete No-Shows, dadurch freigegebene Zusatzplätze und die tatsächlich verfügbare Anzahl
- **Sortierte Teilnehmerliste:** Nach No-Show-Risiko sortiert, mit farblicher Kennzeichnung
- **KI-Einschätzung pro Buchung:** Ein kurzer, kontextbezogener Satz zur jeweiligen Vorhersage (via Anthropic API, siehe Hinweis unten)

## Wie das Modell funktioniert

Das Herzstück ist ein **logistisches Regressionsmodell**, trainiert mit scikit-learn auf 1.500 historischen Buchungen (Datensatz `fitness_class_2212.csv`, bereinigt von inkonsistenten Wochentags-/Kategorie-Werten und fehlenden Angaben).

| Metrik | Wert |
|---|---|
| Genauigkeit (Accuracy) | ≈ 76 % |
| AUC | ≈ 0.82 |
| Stärkster Prädiktor | Mitgliedsdauer (`months_as_member`) |

Die trainierten Koeffizienten sind direkt im JavaScript-Code (`index.html`) hinterlegt — es findet **kein Live-Training im Browser statt**, sondern nur die Anwendung des bereits gelernten Modells (Inferenz). Das eigentliche Training erfolgte separat in Python; das zugehörige Skript ist unter [`/training`](./training) dokumentiert.

## ⚠️ Wichtiger Hinweis zur Anthropic-API-Integration

Der Code enthält einen Aufruf an `https://api.anthropic.com/v1/messages`, um pro Buchung eine KI-generierte Texteinschätzung zu erzeugen. **Dieser Aufruf funktioniert nur innerhalb der Claude.ai-Artefakt-Umgebung**, in der die Authentifizierung automatisch im Hintergrund erfolgt.

Wird die App eigenständig gehostet (z. B. über GitHub Pages, wie in diesem Repo), **schlägt dieser Aufruf fehl** — die App fängt das ab und zeigt automatisch einen lokalen Platzhaltertext an ("Demo-Fallback"). Die Kernfunktion (Wahrscheinlichkeitsberechnung, Kapazitätslogik) bleibt davon unberührt und funktioniert unabhängig davon vollständig.

### Für einen echten Produktivbetrieb wäre zusätzlich nötig:

1. Ein eigener **API-Schlüssel** (Anthropic Console) — niemals im Frontend-Code hinterlegen
2. Ein **Backend** (z. B. eine Node.js-Funktion, ein n8n-Webhook, oder eine Serverless Function auf Vercel/Netlify), das den Schlüssel sicher hält und Anfragen an die Anthropic API weiterleitet
3. Anpassung von `ANTHROPIC_ENDPOINT` im Code auf die eigene Backend-URL statt der direkten Anthropic-URL

## Datengrundlage & Datenschutz

Der verwendete Trainingsdatensatz enthält **keine personenbezogenen Klardaten** (anonymisierte Buchungs-IDs). Für den Einsatz mit echten Mitgliederdaten (z. B. via CRM-Anbindung) sind DSGVO-relevante Punkte zu prüfen — insbesondere Rechtsgrundlage der Verarbeitung, Transparenzpflichten gegenüber Mitgliedern und ein gültiger Auftragsverarbeitungsvertrag (AVV) mit Anthropic bei Nutzung der kommerziellen API. Diese README ersetzt keine Rechtsberatung.

## Projektstruktur

```
goalzone-live-reservation/
├── index.html          # Vollständige App (Frontend + eingebettetes Modell)
├── training/
│   └── train_model.py  # Python-Skript zum Trainieren des Modells (scikit-learn)
├── data/
│   └── fitness_class_2212.csv  # Trainingsdatensatz (Quelle: siehe unten)
├── LICENSE
├── .gitignore
└── README.md
```

## Lokal ausführen

Keine Installation nötig — es ist eine einzelne, eigenständige HTML-Datei ohne Build-Prozess:

```bash
git clone https://github.com/<dein-username>/goalzone-live-reservation.git
cd goalzone-live-reservation
open index.html   # oder Datei direkt im Browser öffnen
```

Optional mit lokalem Server (z. B. für sauberes CORS-Verhalten):

```bash
python3 -m http.server 8000
# dann im Browser: http://localhost:8000
```

## Tech-Stack

- **Frontend:** Vanilla HTML/CSS/JavaScript (kein Framework, keine Build-Tools)
- **Modelltraining:** Python, pandas, scikit-learn
- **KI-Textgenerierung:** Anthropic API (Claude)

## Datenquelle

Der Trainingsdatensatz basiert auf einem öffentlich verfügbaren Fitnessstudio-Buchungsdatensatz (`fitness_class_2212.csv`), der für Lernzwecke im Rahmen einer Weiterbildung zu Prozessautomatisierung & KI-Integration verwendet wurde.

## Lizenz

Dieses Projekt steht unter der [MIT-Lizenz](./LICENSE).

## Autorin

Helena — KI-Managerin & Prozessautomatisierungs-Spezialistin
