---
description: >-
  Desenvolupament web bàsic, recordatori de sintaxi de PHP, des de l'echo a les
  sessions i cookies.
---

# UF1. Desenvolupamet web

### Introducció

La clau per **aprendre** és sempre anar pas a pas, entendre quines tecnologies impliquen el desenvolupament web.

El primer pas és precisament estudiar el llenguatge **html,** comprendre per exemple, Quina finalitat té el bloc etiqueta _&lt;div&gt;_. Aprendre a estructurar un document que servirà de base a la nostra aplicació. 

Un cop tenim l'arquitectura html, anem a deixar-lo bonic, per això tenim el CSS, i si volem que pugui interactuar am l'usuari, tenim el javascript , per tant HTML + CSS +JS, el que diem _frontend_ de l'aplicació. Més enllà d'això trobem el que hi ha al darrera, el _backend_, que proporciona lògica, funcionament i accés a dades.

### **PHP?**

PHP és l'acrònim de Hypertext Preprocessor. PHP és un llenguatge de script, es va construir específicament per a la web, les seves possibilitats són molt elevades.  
PHP es va crear com a llenguatge de script per permetre un contingut dinàmic ric \(el contingut pot provenir d'altres pàgines PHP o pot ser de naturalesa dinàmica i provenir de fonts externes com una base de dades\). PHP és un **llenguatge interpretat**, el que significa que no cal compilar-lo i crear un fitxer executable. En lloc d'això, els fitxers PHP són interpretats línia per línia pel servidor web que utilitza PHP.

PHP s'utilitza juntament amb **HTML**, **JavaScript** i **CSS** per crear aplicacions web dinàmiques. Com que PHP és fàcil d’aprendre, té una gran comunitat de desenvolupadors a tot el món. Això ha fet que cada vegada més desenvolupadors puguin publicar projectes, frameworks i recursos de codi obert. Per exemple, PHP Framework Interop Group, conegut també com [PHP-FIG](https://www.php-fig.org/) , ha creat una sèrie de recomanacions que la majoria dels desenvolupadors utilitzen per escriure el seu codi. GitHub acull molts projectes de codi obert per als altres, i llocs com [Youtube](http://youtube.com/) tenen molts vídeos sobre desenvolupament web.

### Com funciona?



PHP és un llenguatge script  de servidor. El script de costat del servidor és una manera de com els servidors web poden respondre a les sol·licituds del client mitjançant **HTTP**. 

El seu **funcionament** és que un client \(un navegador\) sol·licita una URL **REQUEST**. Una sol·licitud és enviada per un servidor web a un script. A continuació, l'script llegeix aquesta sol·licitud i, depenent del codi de l'script, retorna el contingut d'una pàgina **RESPONSE**.

Aquest procés es produeix cada vegada que es visita una pàgina web. Quan es treballa amb formularis, les dades s’envien des del client al servidor. Les dades es processen i es retorna una resposta. 

Un exemple habitual és que a Facebook, introduïu una actualització d'estat i premeu _Enter_ . El text s’envia a través d’una `POST`sol·licitud al servidor, comprovat per les seqüències del servidor i després es desa a una base de dades. La pàgina web s'actualitza amb la nova publicació. Els llocs PHP també poden ser serveis d’API, que es poden cridar des de scripts JavaScript \(com  AJAX, per exemple\) o des d’altres serveis. En aquests casos i similars, no hi ha cap sol·licitud de navegador implicada.

Per al desenvolupament de la web es necessiten les següents eines:

* Un navegador com Google Chrome, Firefox o Microsoft Edge.
* Un editor de text com Microsoft Visual Studio Code o un **entorn integrat de desenvolupament** \( **IDE** \), com ara PHPStorm.
* Es pot utilitzar un servidor per executar PHP Apache o NGINX, així com el servidor integrat de PHP.

### Motor de plantilles integrat

PHP es va crear exclussivament per escriure aplicacions web. Es pot escriure juntament amb HTML per crear pàgines **dinàmiques**. Veurem exemples d’això en un moment.

Un motor de plantilla de PHP és: una manera de permetre que el codi PHP pugui notrir  de contingut **HTML**. Això dóna flexibilitat a les pàgines. Qualsevol pàgina destinada a utilitzar el codi PHP té una extensió .**php** en lloc d’una extensió .**html**. Això informa al servidor web que espera el contingut de PHP.

#### **Important**:: 

Un fitxer PHP té una extensió .php i pot contenir HTML, JavaScript i CSS, juntament amb PHP. Com que l'intèrpret de PHP necessita saber on es col·loca el codi en un fitxer PHP, el codi PHP s'escriu entre dues etiquetes especials \( `<?php... ?>`\). Aquestes etiquetes s'anomenen etiquetes d'obertura i tancament. Un fitxer PHP típic s’assembla a aquest:

```php
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>PHP Page</title>
		
</head>
<body>
    <div>
        <h1>Titol</h1>
        <p>
	   <?php
		
          // codi php 
		
           ?>
	</p>        
    </div>
</body>
		
<script>
		
// codi JS
		
</script>
</html>
```

Us adjuntem una [sintaxi més acurada del PHP](../apendixs/sintaxi-php.md).

