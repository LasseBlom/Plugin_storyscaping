# Image Hover Reveal Plugin (Storyscaping Eksamen)

Plugin’et viser et billede med en illustration ovenpå, som afsløres med en x-ray cirkel ved hover.  
PHP binder det ind i WordPress via en shortcode. CSS og JavaScript skaber hele den visuelle og interaktive effekt.

---

## Opbygning (idé)
- To lag: **foto** (base) + **illustration** (overlay).
- Ved hover dæmpes fotoet; overlay afsløres i en cirkel der følger musen.

---

## PHP — del 1 (setup og registrering)

**Sikkerhed**
```php
if (!defined('ABSPATH')) { exit; }
```
Forhindrer direkte adgang til filen udenfor WordPress.

**Plugin-klasse**
```php
class ImageHoverReveal {
    private $plugin_url;
    public function __construct() {
        $this->plugin_url = plugin_dir_url(__FILE__);
        add_action('wp_enqueue_scripts', array($this, 'enqueue_assets'));
        add_shortcode('image_hover_reveal', array($this, 'shortcode'));
    }
```
- Gemmer plugin-URL til assets.
- Indlæser CSS/JS på frontend.
- Registrerer shortcode: `[image_hover_reveal]`.

**Assets**
```php
public function enqueue_assets() {
    wp_enqueue_style('image-hover-reveal-css', $this->plugin_url.'assets/css/image-hover-reveal.css', array(), '1.0.0');
    wp_enqueue_script('image-hover-reveal-js', $this->plugin_url.'assets/js/image-hover-reveal.js', array(), '1.0.0', true);
}
```
Loader styles og script med versionsnummer; script i footer (`true`).

**Shortcode defaults**
```php
public function shortcode($atts) {
    $atts = shortcode_atts(array(
        'photo' => 'photo.jpg',
        'illustration' => 'illustration.png',
        'width' => '100%',
        'max_width' => '950px',
        'id' => ''
    ), $atts);

    $photo_url = $this->plugin_url.'assets/images/'.$atts['photo'];
    $illustration_url = $this->plugin_url.'assets/images/'.$atts['illustration'];
    $unique_id = !empty($atts['id']) ? $atts['id'] : 'hover-reveal-'.uniqid();
```
Sætter standardattributter, danner billed-URLs og genererer unikt ID pr. instans.

---

## PHP — del 2 (output og `ob_start()`)

```php
ob_start();
// HTML output her...
return ob_get_clean();
```
`ob_start()` starter **output buffering**.
HTML markup skrives i buffer og returneres samlet når shortcoden kører. 
Markup består af container + foto + illustration + lille init script.

---

## CSS — vigtigste punkter

**Container**
```css
.image-hover-container {
  position: relative; display: inline-block;
  width: 100%; max-width: 950px; aspect-ratio: 1.599;
  overflow: hidden; cursor: pointer; background: #f0f0f0;
}
```
Container styrer dimensioner og clipping.

**Lagplacering**
```css
.photo-base, .illustration-overlay {
  position: absolute; top:0; left:0; width:100%; height:100%;
}
```

**Hover-dæmpning**
```css
.image-hover-container:hover .photo-base { opacity: .25; }
```

**Responsivitet**
Container bliver full width og cursor default på mobil.

---

## JavaScript — funktioner (kort)

| Funktion | Forklaring |
|----------|-------------|
| `'use strict';` | Aktiverer strict mode og forebygger fejl |
| init(containerId) | Finder containeren og sætter event listeners |
| attemptInit() | Retry hvis DOM ikke er klar |
| handleMouseMove() | Læser musens position i procent |
| handleMouseEnter() | Starter animation og hover state |
| handleMouseLeave() | Lukker cirkel igen |
| updateClipPath() | Opdaterer `clip-path` direkte |
| animate() | Animation loop med easing |
| destroy() | Fjerner instans |
| destroyAll() | Fjerner alle instanser |
| auto-init | Finder alle containers automatisk på DOM ready |

---

## Shortcode (brug)
```php
[image_hover_reveal photo="foto.jpg" illustration="illustration.png" width="100%" max_width="950px"]
```

---

## AI Note
AI blev brugt som sparring og strukturhjælp — designvalg og implementering er manuelt udført.
