# Ontwerp

De app moet lezen als de bewegwijzering in een station: twee seconden kijken en je
weet of je hier kunt eten. Dit is de startrichting; in stap 3 wordt hij naast twee
alternatieven gezet en kiest Idries.

## Vier regels

1. **Kleur is betekenis.** Alleen de vier halal-niveaus hebben kleur. Knoppen,
   tekst en lijnen zijn inkt op papier.
2. **Nooit alleen kleur.** Elk niveau heeft ook een letter in een ring en een naam.
3. **De Japanse naam is het grootste element** op de detailpagina.
4. **Geen versiering.** Geen kleurverloop, geen emoji, geen goud. Schaduw alleen
   onder het ene kaartje dat over de kaart zweeft.

Niet doen (dit maakte de vorige versie generiek): koppen in hoofdletters, labels
boven elke kop, teksten met middenpunten aan elkaar ("Lijst · 0"), dezelfde
pilvorm op elk element, een slogan, pijltjes achter knopteksten.

## Tokens

Alles hieronder komt in `src/styles/tokens.css` als CSS-variabele. Onderdelen
gebruiken alleen die variabelen.

### Kleuren

Contrast gemeten tegen de achtergrond; de norm voor gewone tekst is 4,5.

| Variabele | Licht | Donker | Contrast licht / donker |
| --- | --- | --- | --- |
| `--paper` | `#FFFFFF` | `#15171A` | |
| `--surface` | `#F3F4F1` | `#1F2226` | |
| `--line` | `#D5D8D3` | `#33373C` | |
| `--ink` | `#1A1D21` | `#F2F3F0` | 16,9 / 16,1 |
| `--ink-soft` | `#55595F` | `#A9ADB3` | 7,1 / 8,0 |
| `--tier-a` | `#0B6B4A` | `#5FD0A2` | 6,5 / 9,4 |
| `--tier-b` | `#1F5AA6` | `#86B6F2` | 6,8 / 8,5 |
| `--tier-c` | `#96500A` | `#E8A65A` | 6,1 / 8,6 |
| `--tier-d` | `#5B6068` | `#B3B8BF` | 6,3 / 9,0 |

Donker volgt de instelling van de telefoon (`prefers-color-scheme`). Geen eigen
schakelaar in versie 1. Rood komt niet voor: D betekent "vraag het na", niet gevaar.

### Letter

- Latijns schrift: Atkinson Hyperlegible Next (variabel), via
  `@fontsource-variable/atkinson-hyperlegible-next`. Gekozen omdat I, l, 1 en O, 0
  duidelijk verschillen, wat telt bij adressen en geromaniseerde namen.
- Japans: systeemletter. Stapel:
  `"Atkinson Hyperlegible Next Variable", system-ui, -apple-system, "Hiragino Sans", "Noto Sans JP", "Yu Gothic", sans-serif`.
- Groottes: `--text-sm` 13px, `--text-md` 16px, `--text-lg` 19px, `--text-xl` 23px,
  `--text-2xl` 28px. Diktes: 400 en 600.
- Regelhoogte 1,45 voor lopende tekst, 1,2 voor koppen.
- Invoervelden minstens 16px, anders zoomt iOS in.

### Maten

- Afstanden: `--space-1` 4px, `--space-2` 8px, `--space-3` 12px, `--space-4` 16px,
  `--space-5` 24px, `--space-6` 32px. Geen andere waarden.
- Hoeken: `--radius-sm` 6px (knoppen, velden), `--radius-md` 12px (kaartjes).
  Alleen de niveauring is rond.
- Aanraakvlak minstens 44 × 44px.
- Zijmarge op de telefoon 16px. Geen horizontaal scrollen.

## Schermen

```
Hoofdscherm (telefoon)        Detailpagina                Zinnenkaart
+------------------------+    +----------------------+    +----------------------+
| [Niveau][Alcohol][Eten] (i) | Terug                |    | Terug                |
| +--------------------+ |    | 日本語の店名  (groot)  |    | Laat dit zien aan    |
| |   kaart, 45%       | |    | Engelse naam         |    | het personeel        |
| |   pins A B C D     | |    | [Kopieer Japanse naam]|   | +------------------+ |
| | [voorvertoning]    | |    | +------------------+ |    | | 豚肉は入って     | |
| +--------------------+ |    | | (A) Volledig halal| |    | | いますか？        | |
| Lijst, dichtstbij eerst|    | | ✓ feit ✓ feit     | |    | | Zit hier varkens-| |
| (A) Naam               |    | +------------------+ |    | | vlees in?        | |
|     soort eten, afstand|    | Adres en station     |    | +------------------+ |
| (B) ...                |    | Tijden en prijs      |    | ...                  |
| (D) ...                |    | [Open in Google Maps]|    |                      |
+------------------------+    | [Zinnen voor personeel]   +----------------------+
                              | Bron en peildatum    |
                              +----------------------+
```

- **Hoofdscherm, telefoon:** filters bovenaan, kaart vaste 45% van de hoogte,
  lijst eronder scrolt. Niets is sleepbaar.
- **Hoofdscherm, breder dan 900px:** lijst links (400px), kaart rechts.
- **Detailpagina en zinnenkaart:** gewone pagina's met terugknop; de adresbalk
  onthoudt waar je bent.
- **(i)** opent "Over deze data".

## Onderdelen

- **Niveaubadge:** ring van 2px in de niveaukleur met de letter erin, 24px; met de
  naam ernaast op de detailpagina.
- **Kaart:** MapLibre met OpenFreeMap-stijl `positron` (rustig grijs). Welke stijl
  in donker wordt gekozen, staat open tot stap 5. De bronvermelding van
  OpenStreetMap blijft altijd zichtbaar.
- **Iconen:** alleen `lucide-react`, lijndikte 1,75.
- **Beweging:** alleen als antwoord op een tik (openen, sluiten), 150 tot 200 ms,
  en uit bij `prefers-reduced-motion`.
- **Focus:** zichtbare rand van 2px in `--ink` op alles wat je kunt aantikken.

## Tekst

- Knoppen zeggen wat er gebeurt: "Open in Google Maps", "Kopieer Japanse naam".
- Gewone zinnen, geen hoofdletters, geen uitroeptekens.
- Een leeg scherm geeft richting: "Geen plekken met deze filters. Zet een filter uit."
- Een fout zegt wat er mis is en wat je kunt doen: "De kaart laadt niet. De lijst
  werkt nog."
- Taal volgt de telefoon (Nederlands als die Nederlands is, anders Engels). Wisselen
  kan op "Over deze data".
