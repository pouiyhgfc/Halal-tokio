# Halal Tokio

Testversie van een halal-eetgids voor Tokio: ruim 100 restaurants uit de officiële
Tokyo Muslim Travelers' Guide op een kaart en in een lijst. Statische site, geen
backend, geen accounts. Gemaakt voor eigen gebruik, niet om uit te brengen.

## Lees eerst

- `PLAN.md`: de stappen, hun status, het model per stap en de lijst "later".
- `DATA.md`: bronnen, het datamodel en de halal-niveaus.
- `DESIGN.md`: kleuren, letter, maten en de drie schermen.

Lees alleen het specbestand dat bij je stap hoort, plus dit bestand en PLAN.md.

## Werkwijze

1. Voer alleen de stap uit die gevraagd is. Geef eerst je aanpak in vijf regels en
   wacht op akkoord.
2. Stop na de stap. Meld wat je hebt getest en wat je niet kon controleren.
3. Werk PLAN.md bij: status van de stap, en nieuwe ideeën onder "Later".
4. Twee mislukte pogingen om dezelfde fout op te lossen: stop en meld het.
   Probeer niet een derde keer.

## Regels tegen slop-code

1. Bouw alleen wat de stap vraagt. Geen extra functies, instellingen of "alvast" code.
2. Geen knoppen of teksten voor dingen die niet bestaan. Geen "binnenkort".
3. Eén onderdeel per bestand, hooguit ongeveer 150 regels. Langer: splitsen.
4. Geen stil ingeslikte fouten. Geen lege `catch`. Elke fout heeft een zichtbare
   toestand op het scherm.
5. Kleuren, maten, afstanden en lettergroottes alleen via `src/styles/tokens.css`.
   Geen losse kleurcodes of pixelwaarden in onderdelen.
6. Elke zichtbare tekst staat in `src/i18n/`, in het Engels en het Nederlands.
7. Geen nieuw pakket zonder dat het eerst in PLAN.md staat, met de reden.
8. Restaurantgegevens veranderen alleen via `data/places.csv`. Verzin nooit een
   feit. Onbekend is `null`, niet `false`.
9. Geen `any`, geen `@ts-ignore`, geen uitgeschakelde Biome-regels om een fout weg
   te krijgen.
10. Een scherm rekent nooit zelf. Rekenregels staan in `src/domain/` en zijn getest.

## Klaar betekent

- `npm run check` slaagt (TypeScript, Biome, Vitest, databouw).
- `npm run test:e2e` slaagt.
- Een schermafbeelding op 390 pixels breed is bekeken, in licht en donker.
- Geen regel hierboven is overtreden.
- PLAN.md is bijgewerkt.

## Code

- TypeScript, React, Vite, MapLibre GL JS. Versies staan vast in `package.json`.
- Code, bestandsnamen en commentaar in het Engels. Specbestanden in het Nederlands.
- Commentaar alleen waar de reden niet uit de code blijkt.
- `src/features/map/MapView.tsx` is het enige bestand dat MapLibre aanraakt.
- De status van de app (open plek, filters) staat in de adresbalk, niet in
  losse state. Zie `useUrlState.ts`.
