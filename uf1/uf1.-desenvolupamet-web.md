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


Suposant que s'ha instal·lat correctament PHP, 

```text
Toni@Toni-mac ~ % php -v
PHP 7.3.9 (cli) (built: Sep 14 2019 18:07:55) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.9, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.3.9, Copyright (c) 1999-2018, by Zend Technologies
```

Creeu un fitxer i_ndex.php_ dins d'un nou  directori  i afegir:  


```text
   <?php
      echo "Hello world";
```

Per executar aquest codi, utilitzeu el servidor incorporat de PHP \(Built-in web server\). Canvieu a l'eina de línia d'ordres preferida \(Terminal, per als usuaris de Mac\), _cd_ a la carpeta d'el projecte i reinicia el servidor amb 

```text
php -S localhost:8888 .
```

Aquest ordre es tradueix en "**Executar un servidor i fer-lo accessible des del meu navegador a localhost, port 8888**".  


Una de les claus per a una programació madura és considerar totes les rutes i opcions  possibles a través del  codi. Per exemple, què passa si no hi ha una clau _persona_  disponible? Potser la cadena de consulta \(_QUERY_ \) s'hagi omès per complet. En aquest cas, es generarà un error, ja que la clau persona no existirà. ¿Quina és la solució? Si bé és cert que això no és més que un simple exemple, és important considerar tots els possibles resultats. Anem a proporcionar un valor per omissió.  


```text
<? php

    if (isset ($_GET['persona']))
    {  
        $persona = $_GET['persona'];
    } else
    {
        $persona = 'Joe';
    }
     echo 'Hola, {$persona}';

```

