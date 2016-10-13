---
layout: post
title: Webseiten Status anzeigen
---

Für meine [Status Seite][pb-status] habe ich ein kleines PHP script geschrieben, dass mir dabei hilft, den Status einer Website zu ermitteln. 

Dabei wird die Webseite kurz "inkludiert" und die Headerinformationen ausgelesen. Anhand dieser Headerinformationen lässt sich dann sehen, welchen HTTP-Code die Webseite bzw. der aktuelle Aufruf abwirft. 

Wie das Script im Groben aussehen könnte, siehst du hier:

```php
<?php
header('Access-Control-Allow-Origin: *');  

/**
 * Hier kannst du deine Adressen in den Array packen
 */
$urls = array(
  'https://pixelbart.de',
  'http://seozeile.com',
  'http://krzl.net',	
);

sort($urls);

if( is_array($urls) && !empty($urls) ) :
	
foreach($urls as $url) { 

  $handle = curl_init($url);
  curl_setopt($handle,  CURLOPT_RETURNTRANSFER, TRUE);
  $response = curl_exec($handle);
  $httpCode = curl_getinfo($handle, CURLINFO_HTTP_CODE);

  switch($httpCode) {
    case 100: // Statuscode 100
    echo $url . ' - ' . $httpCode . '<br />';
    break;		
    case 101: // Statuscode 101
    echo $url . ' - ' . $httpCode . '<br />';
    break;		
    case 102: // usw...
    echo $url . ' - ' . $httpCode . '<br />';
    break;	
    case 200:
    echo $url . ' - ' . $httpCode . '<br />';
    break;		
    case 301:
    echo $url . ' - ' . $httpCode . '<br />';
    break;		
    case 302:
    echo $url . ' - ' . $httpCode . '<br />';
    break;		
    case 401:
    echo $url . ' - ' . $httpCode . '<br />';
    break;
    case 400:
    echo $url . ' - ' . $httpCode . '<br />';
    break;
    case 404:
    echo $url . ' - ' . $httpCode . '<br />';
    break;
    case 500:
    echo $url . ' - ' . $httpCode . '<br />';
    break;
    case 502:
    echo $url . ' - ' . $httpCode . '<br />';
    break;
    case 503:
    echo $url . ' - ' . $httpCode . '<br />';
    break;
    default:
    echo $url . ' - ' . $httpCode . '<br />';		
    break;
  }

    curl_close($handle);			
  }
}

else :

echo 'Keine URLs vorhanden!';

endif;
```

Die Ausgabe sieht dann in etwa so aus:

**https://pixelbart.de - 404**

Weitere HTTP-Codes findest du unter anderem auch auf Wikipedia. Dort gibt es glücklicherweise auch Erklärungen dazu.

[HTTP-Codes auf Wikipedia][wikipedia-http]

Du kannst jetzt noch `html spans` stylen und diese einfach mit die Echos packen, um bspw. einen "deutlicheren" Effekt für deine Besucher zu erzielen.

Damit du das Ganze auf bspw. Github mit Hilfe von jQuery `.load` einbinden kannst, habe ich dir direkt den `header('Access-Control-Allow-Origin: *');` eingebaut.

[wikipedia-http]: https://de.wikipedia.org/wiki/HTTP-Statuscode
[pb-status]: https://pixelbart.github.io/status
