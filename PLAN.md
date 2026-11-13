# Plan

Het volledige plan met onderbouwing staat in het doc "Halal Tokio – bouwplan".
Dit bestand is de werkversie: status per stap en wat er daarna komt.

## Versie 1 (vaste lijst, niets bij)

1. **Kaart**: opent direct op Tokio, alle plekken zichtbaar. Geen startscherm.
2. **Lijst**: compacte rijen, dichtstbij eerst als locatie aan staat; anders op
   niveau (A eerst) en dan op naam.
3. **Detailpagina**: Japanse naam groot met kopieerknop, Engelse naam, niveau met
   de feiten erachter, adres, station, tijden, prijs, "Open in Google Maps",
   bron en peildatum.
4. **Filters**: halal-niveau, geen alcohol in de zaak, soort eten.
5. **Zinnenkaart**: Japanse zinnen om aan het personeel te laten zien.
6. **Over deze data**: bronnen, peildatum, betekenis van de niveaus, taalkeuze.

Bewust niet: accounts, reviews, foto's, meldingen, zoekbalk, "nu open",
sleepbare lagen, offline kaarten, andere steden, meer talen dan Engels en
Nederlands.

## Stappen

Elke stap is een nieuwe chat. Start met de opdracht onderaan dit bestand.

| Stap | Resultaat | Controle voor je verder gaat | Model (denkniveau) | Status |
| --- | --- | --- | --- | --- |
| 0. Fundament | Vite + React + TS-project, Biome, Vitest, Playwright, CI op GitHub, testlink op Vercel, lege pagina met tokens en i18n | Testlink toont de pagina, CI is groen | Sonnet 5.5 (medium) | open |
| 1. Data | `data/build.ts` en `data/places.csv` uit het kaartbestand; iconen en prijzen uit de pdf; 29 OSM-plekken erbij, zonder dubbelen | Bouwscript accepteert elke rij. Idries vergelijkt 15 willekeurige rijen met de pdf: geen fout in de iconen | Sonnet 5.5 (medium); iconen aflezen onbetrouwbaar → Opus 5.5 (high) | open |
| 2. Niveauregels | Telling per icoon, definitieve regels in DATA.md, `src/domain/tier.ts` met tests | Idries keurt regels en verdeling A–D goed | Opus 5.5 (high) voor de regels, daarna Sonnet 5.5 (medium) voor de code | open |
| 3. Ontwerp kiezen | Drie mockups van hoofdscherm en detailpagina op echte data; DESIGN.md definitief | Idries kiest één richting | Opus 5.5 (high) | open |
| 4. Indeling en lijst | `tokens.css` definitief, beide talen, schermindeling, lijst met niveaubadge, afstand | Lijst toont alle plekken in de juiste volgorde, licht en donker | Sonnet 5.5 (medium) | open |
| 5. Kaart | Pins per niveau, tik toont voorvertoning, kaart en lijst volgen elkaar | Laadt de kaart niet, dan werkt de lijst en staat er een melding | Sonnet 5.5 (medium) | open |
| 6. Detailpagina | Pagina per plek, status in de adresbalk | Terugknop van de telefoon werkt; een gedeelde link opent dezelfde plek | Sonnet 5.5 (medium) | open |
| 7. Filters | Niveau, geen alcohol, soort eten | Aantal klopt met de data; "geen resultaten" geeft richting | Sonnet 5.5 (medium) | open |
| 8. Zinnenkaart en Over | Zinnen uit de gids, bronpagina, alle Nederlandse teksten | Elke Japanse zin is teruggelezen en klopt | Sonnet 5.5 (medium) schermen, Haiku 5.5 (low) Nederlandse teksten, Opus 5.5 (high) controle Japans | open |
| 9. Nalopen | Verslag van een frisse blik op code, toegankelijkheid en sloplijst, plus reparaties | Geen open punten met ernst "hoog" | Opus 5.5 (high) in een nieuwe chat, daarna Sonnet 5.5 (medium) voor reparaties | open |
| 10. Praktijktest | De app gebruikt op straat, op een echte telefoon | Vijf plekken gevonden; wat niet klopte staat hieronder | Idries, geen AI | open |

Vaste regels voor modellen:

- Sonnet lost dezelfde fout twee keer niet op: nieuwe chat met Opus 5.5 (high) en
  de foutmelding. Daarna terug naar Sonnet.
- Fable, xhigh en max zijn voor dit project niet nodig.

## Besloten

- 2026-11-12: alleen halal, alleen Tokio, Engels en Nederlands.
- 2026-11-12: geen backend; data is één bestand dat met de app meegaat.
- 2026-11-12: repo heet Halal Tokio.
- 2026-11-12: Idries doet de steekproef in stap 1 en de praktijktest in stap 10.

## Open punten

- [ ] Stap 2: maakt alcohol in de zaak het verschil tussen A en B?
- [ ] Stap 8: mag de zinnenlijst uit de app van de broer de zinnen uit de gids aanvullen?
- [ ] Gebedsruimtes (18 in de gids): staan nu onder "Later". Toch in versie 1?

## Later

Pas na stap 10 beslissen.

- Gebedsruimtes uit dezelfde gids
- Offline gebruik
- Een tweede stad
- Melden dat een plek gesloten is of niet meer klopt

## Uit de praktijktest

(leeg)

## Startopdracht voor elke stap

```
Lees CLAUDE.md, PLAN.md en het specbestand dat bij deze stap hoort.
Voer alleen stap <nummer> uit.
Geef eerst je aanpak in vijf regels en wacht op mijn akkoord.
Stop na de stap. Meld wat je hebt getest en wat je niet kon controleren.
```

## Stap 0 in detail

- Project met Vite + React + TypeScript (template `react-ts`), daarna de versies
  exact vastzetten in `package.json` (geen `^` of `~`).
- Startversies (npm, 12 nov 2026): react 19.3.1, vite 8.3.8, typescript 7.0.2,
  @vitejs/plugin-react 6.3.0, maplibre-gl 6.18.0, zod 4.6.7, vitest 5.1.0,
  @playwright/test 1.65.0, @biomejs/biome 2.6.7, lucide-react 1.72.0,
  @fontsource-variable/atkinson-hyperlegible-next 5.3.0.
  Werkt een combinatie niet, kies de laatste versie die wel werkt en noteer
  dat hier met de reden.
- `"engines": { "node": ">=22" }`.
- Scripts: `dev`, `build`, `preview`, `data` (bouwt `places.json`), `check`
  (tsc --noEmit, biome check, vitest run, data), `test:e2e`.
- CSS Modules aan; `src/styles/tokens.css` en `base.css` met de waarden uit
  DESIGN.md; lettertype via fontsource.
- `src/i18n/en.ts` en `nl.ts`, met een type dat afdwingt dat `nl` dezelfde
  sleutels heeft als `en`.
- Mappen uit DATA.md en DESIGN.md aanmaken, alleen met wat stap 0 nodig heeft.
  Geen lege bestanden "voor later".
- `.github/workflows/ci.yml`: `npm ci`, `npm run check`, `npm run build`,
  Playwright-test op telefoonformaat.
- Eén Playwright-test: de pagina laadt zonder fouten in de console.
- Vercel: framework Vite, build `npm run build`, map `dist`. Idries koppelt de
  repo in het Vercel-dashboard.
