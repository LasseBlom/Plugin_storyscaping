# Forklaring af hvad der sker i mit PHP kode

I dette stykke PHP kode bruger vi `ob_start();` til at starte output buffering.  
Det betyder at alt HTML og script der kommer efter, ikke bliver sendt direkte ud til browseren med det samme — det bliver først "fanget" i en buffer.  
Til sidst bruger vi `return ob_get_clean();` som returnerer hele bufferen som en string.  
Det gør det muligt at returnere HTML direkte fra funktionen — hvilket er smart når man fx laver shortcodes i WordPress.

## Hvad koden gør
- Sætter output buffering i gang med `ob_start()`
- Genererer HTML markup for et image hover element (et billede med illustration overlay)
- Loader foto + illustration
- Indsætter JS der loader animation / hover effekt når DOM er klar
- Til sidst `return ob_get_clean()` returnerer hele outputtet som én string så det kan bruges i WordPress (shortcode output)

```php
ob_start();
// ... HTML + JS + PHP output ...
return ob_get_clean();
