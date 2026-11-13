# Data

Eén bron van waarheid: `data/places.csv`, één rij per restaurant. `data/build.ts`
controleert elke rij met Zod en schrijft `src/data/places.json`. Een foute rij
stopt de bouw. De app verandert nooit data.

## Bronnen

| Code | Bron | Wat hij levert |
| --- | --- | --- |
| `tcvb-2026` | Tokyo Muslim Travelers' Guide 2026–2027, Tokyo Convention & Visitors Bureau. [Overzicht](https://www.gotokyo.org/book/en/list/1664/), [pdf](https://www.gotokyo.org/book/wp-content/uploads/2026/03/AO2_2603_muslim_low_EN.pdf), [kaart](https://www.google.com/maps/d/viewer?hl=ja&mid=1BYHToW4gnHixodULmLTw2eCBy7jrGAI) | 110 restaurants met adres, telefoon, tijden, station, prijs, iconen. Peildatum: november 2025, door de zaken zelf aangeleverd |
| `osm` | OpenStreetMap, via het uittreksel in [jarokchinavi/jakorchi-site](https://github.com/jarokchinavi/jakorchi-site) (`public/places/`, kolomvolgorde in `docs/data.md`) | 29 plekken in Tokio met een halal-label. Alleen naam, plek en soort eten |

Deze twee zinnen uit de gids komen letterlijk op de pagina "Over deze data":

- "This guidebook is based on the information provided by the establishments as of November 2025."
- "Please check each facility's official website or social media for the latest information."

### Ruwe bestanden

Zet ze in `data/sources/` en verander ze nooit met de hand.

- `tcvb-2026.kml`: het kaartbestand. Download:
  `https://www.google.com/maps/d/kml?mid=1BYHToW4gnHixodULmLTw2eCBy7jrGAI&forcekml=1`
- `tcvb-2026.pdf`: de gids.
- `osm-tokyo.json`: de 29 OSM-plekken, uit het uittreksel van de broer.

De werkomgeving van de AI kan Google en gotokyo.org meestal niet bereiken.
Lukt een download niet, dan download Idries het bestand en zet het in de map.

### Wat wel en niet is gecontroleerd

- Het kaartbestand is KML met per plek `<Point><coordinates>` als
  lengtegraad,breedtegraad. De laag met gebedsruimtes heeft deze velden:
  `Nama(JP)` (zo gespeld), `Address(EN)`, `Address(JP)`, `Phone No.`,
  `Listing No.`, `Map No.`, `Genre`, `HP`, `Hours`, `Closed days`.
- **Niet gecontroleerd:** dat de restaurantlaag dezelfde velden heeft. Dat is de
  eerste controle van stap 1.
- De iconen per restaurant staan niet in de tekst van de pdf, alleen als
  afbeelding. Ze moeten van de pagina's worden afgelezen.

## Datamodel

```ts
type Fact = true | false | null; // null = de bron zegt er niets over

type Place = {
  id: string;                 // "tcvb-023" (volgnummer uit de gids) of "osm-n4629825055"
  name: string;               // Latijns schrift
  nameJa: string | null;
  cuisine: Cuisine;           // vaste lijst, zie hieronder
  lat: number;
  lng: number;
  address: string;
  addressJa: string | null;
  station: string | null;
  phone: string | null;
  hours: string | null;       // vrije tekst, precies zoals de bron
  closed: string | null;      // vrije tekst
  priceLunch: number | null;  // yen
  priceDinner: number | null; // yen
  url: string | null;
  facts: {
    noPork: Fact;
    halalMeat: Fact;          // halal-gecertificeerd vlees
    noAlcohol: Fact;          // er wordt geen alcohol geschonken
    separateKitchen: Fact;
    dedicatedUtensils: Fact;
    muslimOwner: Fact;
    muslimChef: Fact;
    muslimMenu: Fact;         // moslimvriendelijk menu naast het gewone
    prayerSpace: Fact;
  };
  source: "tcvb-2026" | "osm";
  checked: string;            // "2025-11"
};
```

`Cuisine` is een vaste lijst die in stap 1 uit de waarden in `Genre` wordt
gemaakt (bijvoorbeeld ramen, curry, yakiniku, cafe, other). Niet vooraf verzinnen.

### Van icoon naar feit

Icoon in de gids (zo gelabeld in de Icon Guide) naar veld. Een icoon dat er staat
is `true`. Een icoon dat ontbreekt is `null`, niet `false`, behalve waar hieronder
anders staat.

| Icoon in de gids | Veld |
| --- | --- |
| No pork | `noPork` |
| Halal-certified meat | `halalMeat` |
| Alcoholic beverages not available | `noAlcohol` |
| Alcohol served separately | `noAlcohol = false` |
| Separate kitchens for halal food preparation | `separateKitchen` |
| Dedicated cooking utensils for halal food | `dedicatedUtensils` |
| Muslim restaurant owner | `muslimOwner` |
| Muslim chef(s) | `muslimChef` |
| Muslim-friendly menu available | `muslimMenu` |
| Dedicated area for salat | `prayerSpace` |

Andere iconen (Wi-Fi, Engelse menukaart, tax-free en dergelijke) worden niet
opgeslagen. Komt in stap 1 een icoon voor dat niet in deze lijst staat en wel over
halal gaat, dan stopt het werk en vraagt de AI het aan Idries.

### Regels voor `places.csv`

- Kolommen in de volgorde van het model; `facts` als losse kolommen met prefix
  `fact_`; leeg = `null`.
- Coördinaten liggen tussen breedtegraad 35,4 en 35,95 en lengtegraad 139,0 en
  140,0 (Tokio inclusief Hachioji, Hamura en Haneda).
- Geen dubbele `id`. Een OSM-plek binnen 50 meter van een gidsplek met een
  gelijkende naam is een dubbele en gaat eruit.
- OSM-plekken krijgen alle feiten `null`, en dus altijd niveau D.

## Halal-niveaus

Berekend in `src/domain/tier.ts`, alleen uit `facts`. Dit is het voorstel; de
definitieve regels worden in stap 2 vastgesteld, na de telling per icoon.

| Niveau | Naam (en) | Naam (nl) | Regel |
| --- | --- | --- | --- |
| A | Fully halal | Volledig halal | `noPork` en `halalMeat` en `noAlcohol` |
| B | Halal kitchen, alcohol served | Halal keuken, alcohol aanwezig | `noPork` en `halalMeat`, `noAlcohol` is niet `true` |
| C | Halal menu in a mixed kitchen | Halal menu in gemengde zaak | (`halalMeat` of `muslimMenu`) en (`separateKitchen` of `dedicatedUtensils`) |
| D | Ask to confirm | Vraag het na | Al het andere |

De regels worden van boven naar beneden toegepast; de eerste die past, telt.
Op de detailpagina staan de feiten altijd bij het niveau.

## Rechten

De gids is van het Tokyo Convention & Visitors Bureau, de kaartdata van
OpenStreetMap (ODbL, bronvermelding verplicht). Voor een privétest is het
overnemen van feiten met bronvermelding geen probleem. Wordt het ooit openbaar,
dan eerst toestemming vragen aan het bureau.
