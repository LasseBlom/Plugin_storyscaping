# Image Hover Reveal Plugin (Storyscaping Eksamen)

Dette plugin viser et billede med en overlay illustration, som afsløres med en x-ray lignende cirkel når brugeren hover. Det bruges på vores About-side for at skabe et professionelt visuelt storytelling element.

Funktionaliteten ligger primært i **CSS** og **JavaScript**, mens PHP delen sørger for integration i WordPress + shortcode output.

---

## Funktion & idé

Pluginet består af 2 lag:

1. Base Photo (originalt billede)
2. Illustration (håndtegnet look)

Når man holder musen henover billedet, dæmpes fotoet og illustrationen afsløres i en cirkel der følger musens bevægelse. Det giver en “x-ray reveal” effekt og understøtter Storyscaping visuelt.

---

## Brug af AI i processen

AI har været brugt som sparringspartner gennem hele udviklingen, hvor jeg beskrev det visuelle mål, designstil og ønsket interaktion. AI blev brugt til optimering af struktur og teknisk kode, men designet og valg af løsning er beslutninger taget bevidst ud fra vores koncept.

---

## PHP Struktur

PHP delen er opdelt i 2 dele:

- Formelle sikkerhedsting (f.eks. `if (!defined('ABSPATH')) exit;` så filen ikke kan tilgås direkte)
- Selve plugin logikken

Alt ligger samlet i en Class (`class ImageHoverReveal`).

### Det vigtigste der sker i PHP:

- Constructoren kører når plugin’et initialiseres og:
  - gemmer plugin URL
  - loader CSS + JS via `wp_enqueue_scripts`
  - registrerer shortcoden `[image_hover_reveal]`

- `enqueue_assets()` sørger for at CSS + JS ligger klar på frontend

- `shortcode()`:
  - sætter standard values
  - danner sti til foto + illustration
  - genererer unikt ID pr instance
  - (senere bygges HTML output via ob_start / ob_get_clean)

---

## CSS Struktur / Visuel Effekt

Containeren `.image-hover-container` definerer rammerne: dimensioner, overflow hidden og cursor pointer. Begge billeder ligger absolut positioneret ovenpå hinanden i samme container.

Base Photo og Illustration Overlay har hver deres z-index, transitions og object-fit setup, så de fylder containeren korrekt.

Når man hover:
- Base Photo fades til 25% opacity
- Overlayet vises via `clip-path: circle(...)` og JavaScript opdaterer denne cirkel live efter musens position

Dette skaber x-ray effekten.

Der er også responsivitet så på mobil ændres cursor til default, og max-width kører fuld width.

---

## Shortcode Eksempel

```php
[image_hover_reveal photo="demo.jpg" illustration="demo-illu.png" width="100%" max_width="950px"]
