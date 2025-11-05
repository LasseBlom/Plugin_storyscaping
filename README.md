# Image Hover Reveal Plugin (Storyscaping Eksamen)

###### Lasse Blom Nielsen  
###### Multimediedesigner studerende Erhvervs akadamiet Aarhus 
###### 2025

---
Plugin’et viser et billede med en illustration ovenpå, som afsløres med en x-ray cirkel ved hover.  
PHP binder det ind i WordPress via en shortcode. CSS og JavaScript skaber hele den visuelle og interaktive effekt.

![Hover Effekt](readmemedia/plugin-ezgif.com-video-to-gif-converter.gif)

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

## PHP — del 2 (output og `ob_start()`med Html )

### `ob_start()`

```php
ob_start();
// HTML output her...
return ob_get_clean();
```
`ob_start()` starter **output buffering**.
HTML markup skrives i buffer og returneres samlet når shortcoden kører. 
Markup består af container + foto + illustration + lille init script.

### html

```html
        <div class="image-hover-container" 
             id="<?php echo esc_attr($unique_id); ?>"
             style="width: <?php echo esc_attr($atts['width']); ?>; max-width: <?php echo esc_attr($atts['max_width']); ?>;">
            <div class="photo-base">
                <img src="<?php echo esc_url($photo_url); ?>" 
                     alt=""
                     loading="lazy">
            </div>
            
            <div class="illustration-overlay">
                <img src="<?php echo esc_url($illustration_url); ?>" 
                     alt=""
                     loading="lazy">
            </div>
        </div>
        
        <script>
        (function() {
            function initHover() {
                if (typeof ImageHoverReveal !== 'undefined') {
                    ImageHoverReveal.init('<?php echo esc_js($unique_id); ?>');
                } else {
                    setTimeout(initHover, 100);
                }
            }
            
            if (document.readyState === 'loading') {
                document.addEventListener('DOMContentLoaded', initHover);
            } else {
                initHover();
            }
        })();
```

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

## JavaScript funktioner

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

## Shortcode
```php
[image_hover_reveal photo="foto.jpg" illustration="illustration.png" width="100%" max_width="950px"]
```

---

## AI Note
AI har været brugt som sparringspartner igennem udviklingen af dette plugin. Jeg har anvendt AI til at strukturere min kode bedre, få hjælp til at validere løsningsmuligheder og til at få tekniske forslag til hvordan effekten kunne implementeres mest optimalt. Designbeslutninger, koncept, visuel retning og den endelige kodeimplementering er udført, vurderet og manuelt tilpasset af mig selv. AI har derfor fungeret som et støtteværktøj og ikke som en automatisk generator af løsningen.