---
layout: post
title: jQuery UI Autocomplete für WordPress
---

Einer meiner Kunden wollte für die Suche in den Dokumentationen (Custom Post Type) eine Autocomplete-Lösung.
Ich habe vorher nie mit jQuery UI Autocomplete gearbeitet und musste mich erst ein wenig einlesen. Dabei ist mir
aufgefallen das jQuery UI Autocomplete am einfachsten mit dem Dateiformat JSON funktioniert. Hier findest du meine
WordPress Lösung dazu.

Die einfachste und schnellste Möglichkeit die WordPress Beiträge in ein JSON-Format zu packen ist es ein Page-Template
zu erstellen, um dort eigene Header und Queries definieren zu können.

## WordPress Template erstellen

Ein Beispiel Template könnte so aussehen:

```php
<?php
/** Template Name: Blog JSON **/ 
header( 'Content-type: application/json' );

$args = array( 'post_status' => 'publish', 'posts_per_page' => -1);

$the_query = new WP_Query( $args );

if ( $the_query->have_posts() ) : 

  while ( $the_query->have_posts() ) : $the_query->the_post();
	
    $out[] = array( 'value' => html_entity_decode( get_the_title() ), 'url' => get_the_permalink() );
		
  endwhile;

  wp_reset_postdata();

endif;

echo json_encode($out);
```

Weisen wir das Template jetzt einer angelegten WordPress Seite im `wp-admin` zu, werden die Beiträge im JSON ausgegeben.
Zumindest der benötigte Titel und der Permalink.

Durch die Zuweisung an eine WordPress Seite haben wir auch direkt einen Link, den wir dann in unseren AJAX-Request für's
Autocomplete verwenden können.

## Autocomplete Script

Wie das aussehen könnte, zeigt das Beispiel hier:

```javascript	
if(jQuery('#search-form').length > 0) {
		
  var json_url = '<?php bloginfo('url'); ?>/json/'; // DIE URL
		
  var availableTags = (function () {
    var json = null;
    jQuery.ajax({
      'async': false,
      'global': false,
      'url': json_url,
      'dataType': "json",
      'success': function (data) {
        json = data;
      }
    });
    return json;
  })();

  jQuery(document).ready(function() {
				
    jQuery('#search-string').autocomplete({
      source : availableTags, 
      minLength : 0,
      select: function(event, ui) { 
        jQuery(this).val(''); // Leere das INPUT Feld
        jQuery('#search-form').attr("action", ui.item.url); // Weise dem form action die url zu
        jQuery('#search-form').submit();
      }
    });			
				
    jQuery.ui.autocomplete.prototype._resizeMenu = function () {
      var ul = this.menu.element;
      ul.outerWidth(this.element.outerWidth());
    }
  });		
}
```

Vergiss nicht jQuery UI oder jQuery UI Autocomplete einzubinden, da es sonst nicht funktioniert. 
