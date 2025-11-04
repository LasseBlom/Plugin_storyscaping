# Image Hover Reveal - WordPress Plugin

Dette plugin gør det muligt at vise en special visuel image-hover effekt i WordPress.  
Plugin'et tager udgangspunkt i at man har ét foto (base photo) og én illustration (overlay image). Når brugeren holder musen hen over billedet, vil illustrationen blive afsløret som en "clip reveal" cirkel der følger musens bevægelse.

Effekten bruges til at skabe mere visuel storytelling, motion feeling og engagement gennem microinteractions — uden tunge animation libraries.

---

## Teknisk forklaring

Plugin'et består af 3 hovedelementer:

- **PHP (image-hover.php)** genererer markup og shortcodes til WordPress.
- **CSS (image-hover-reveal.css)** styrer layout, transitions og design.
- **JavaScript (image-hover-reveal.js)** håndterer mouse tracking og animating af clipPath.

### PHP output med `ob_start();`
I PHP bruger vi `ob_start();` for at starte output buffering.  
Det betyder, at HTML-output bliver "fanget" i en buffer, og ikke sendt ud med det samme.  
Til sidst returnerer vi alt med `return ob_get_clean();`.

Dette gør det muligt at returnere samlet HTML/JS output som en **string** fra funktionen — hvilket er nødvendigt når man laver WordPress shortcodes.

---

## Hvordan effekten fungerer

1. Plugin'et genererer en container med to lag — et foto og en illustration ovenpå.
2. Billedet bliver mørkere ved hover og overlay bliver synlig i et cirkel-område.
3. JavaScript følger musens position og ændrer dynamisk `clip-path`.
4. Animationen kører ultra smooth via requestAnimationFrame.

---

## Brugsscenarie

Dette plugin er anvendt for at kunne lave reelle kommercielle portfolio hov-over effekter der visuelt viser forskellen mellem det fotograferede motiv og den tegnede/illustrerede stil ovenpå.

Det er relevant i multimediedesign projekter hvor man arbejder med visuel identitet, art direction og storytelling.

---

## Filstruktur

| Fil | Funktion |
|-----|-----------|
| image-hover.php | Shortcode, PHP logic, buffer output |
| image-hover-reveal.css | Styling og transitions |
| image-hover-reveal.js | Mouse movement + reveal animation |

---

## Shortcode eksempel

```php
[image_hover photo="img/photo.jpg" illustration="img/illustration.png" width="100%" max_width="900px"]
