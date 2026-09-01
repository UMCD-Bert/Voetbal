# EuroVeld Stats — meerdere landen, competities & seizoenen

Statische site (HTML/CSS/JS) die per land, competitie en seizoen de stand,
onderlinge uitslagen laat zien — en voor een lopend seizoen kan schakelen
tussen "alleen gespeeld" en een voorspelde eindstand.

## Structuur

```
index.html                     de hele site (geen build-stap nodig)
data/competitions.json         register: welke landen/competities/seizoenen bestaan
data/<competitie-id>/<seizoen-id>.json   de wedstrijddata per seizoen
```

Nu gevuld:
- **nl-1 / 2025-26** — Eredivisie 2025/26, volledig afgerond seizoen.
- **demo-1** — een fictieve 5-clubs competitie met een afgerond seizoen
  (`2025-demo`) én een lopend seizoen (`2026-demo`), puur om de
  voorspellingsknop te laten zien. Geen echte data.

Alle andere 16 competities (2e niveau + vrouwencompetitie van Nederland,
Duitsland, Engeland, Spanje, Italië, Frankrijk) staan al in het register,
maar hebben nog geen seizoensdata — de site toont daar netjes een
"nog niet toegevoegd"-melding. Vraag me erom en ik vul ze aan, één voor één.

## Hoe de voorspelling werkt

Voor een seizoen met `"status": "in_progress"` en een `"previousSeason"`-
verwijzing:
- **Alleen gespeeld** telt uitsluitend duels met een score.
- **Voorspelde eindstand** vult elk nog niet gespeeld duel in met de score
  van *dezelfde thuis/uit-combinatie* uit het vorige seizoen. Bestond die
  combinatie niet (bijv. een gepromoveerde club), dan wordt 0-0 gebruikt.
  Voorspelde cijfers krijgen een stippellijn in de matrix.

Zodra een seizoen écht compleet is, zet je `"status"` op `"completed"` en
verdwijnt de voorspellingsknop vanzelf.

## Data verversen

Geen API-key, geen GitHub Actions. Als je nieuwe uitslagen wilt: vraag het
in de chat, dan werk ik het juiste JSON-bestand bij en lever ik het opnieuw,
of je zet het zelf in `data/<competitie-id>/<seizoen-id>.json` volgens
hetzelfde schema:

```json
{
  "competition": "nl-1",
  "season": "2026-27",
  "label": "2026/27",
  "status": "in_progress",
  "previousSeason": "2025-26",
  "teams": ["Ajax", "..."],
  "matches": [
    {"home": "Ajax", "away": "AZ", "score": "2-1"},
    {"home": "Ajax", "away": "PSV", "score": null}
  ]
}
```

En voeg het seizoen toe aan `data/competitions.json` onder
`"seasons"["nl-1"]`.

## Lokaal bekijken

De site gebruikt `fetch()` om de JSON-bestanden te laden — dat werkt niet
als je `index.html` direct opent (`file://`). Start lokaal een simpele
server in deze map:

```bash
python3 -m http.server 8000
```

en open `http://localhost:8000`.

## Live zetten via GitHub Pages

1. Maak een public repository aan en upload deze hele map (`index.html` +
   de `data/`-map, met behoud van de mapstructuur).
2. Settings → Pages → Source: branch `main`, map `/ (root)` → Save.
3. Na ongeveer een minuut staat de site live op
   `https://<jouw-gebruikersnaam>.github.io/<repo-naam>/`.
