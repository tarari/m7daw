---
description: >-
  Millorar la comprensió del procediments a través de la construcció d'un
  framework
---

# Creació d'un framework

## Introducció

Aprofundim el coneixement de la programació orientada a objecte a través de la creació d'un marc de treball propi adaptant una aplicació.

En les aplicacions web és important resoldre els següents problemes :

* Captura del **REQUEST** del client
* Adaptar **resposta** al tipus de client \(HTML, JSON...\)

En el cas de seguir un paradigma MVC \(Model View Controller\), es poden adoptar els següents elements:

* Reescriptura del REQUEST per fer-lo més amigable \(**`.htaccess`**\)
* Captura a través del Request del controlador \(objecte\) i l'acció \(mètode\)
* Llençament de la instància de l'objecte controlador i posteriorment cridar l'acció corresponent com a mètode del controlador
* Manteniment de la sessió i sistema d'autenticació i autorització
* Sistema de plantilles per al renderitzat i generació de interfície d'usuari.





### **Funcionament bàsic de l'app \( paradigma MVC a nivell web\)**

**Cicle REQUEST - RESPONSE**

Si seguim el cicle entre client i servidor, podem analitzar com es desenvolupa la resposta, aquesta es dona sempre a nivell de Controlador, ja que estem fent servir paradigmes MVC.

![Cicle Request abans d&apos;arribar al controlador](../.gitbook/assets/cicle-request.png)

### **1 Reescriptura de la URI**

**.htaccess**

```text
Options +FollowSymLinks

RewriteEngine On

RewriteCond %{REQUEST_FILENAME} -f [OR]
RewriteCond %{REQUEST_FILENAME} -d 

RewriteRule ^.*$ index.php [NC,L]
```

Com s'observa, es tracta de redirigir tota consulta \(_query_ \) cap al controlador frontal de l'aplicació \(**index.php**\).

Per una bona càrrega de classes fem servir l'estandar **PSR- 4** \(autocàrrega de classes\)

**Adaptació a estàndar PSR-4 \(autoload\) composer.json segons el namespace utlitzat**

```text
{
    "autoload": {
        "psr-4":{
            "App\\": "src/"
        }
    }
```

### 2 Front controller  - index.php

El fitxer index.php del nostre projecte actua com a un frontend controller és a dir, totes les Requests, són dirigides a aquest script.

```php
<?php

    ini_set('display_errors','On');
   

    require __DIR__.'/vendor/autoload.php';
    
    use App\App;
    
    $conf=App::init();
    //constants d'enrutament i BBDD
    define('BASE',$conf['web']);
    define('ROOT',$conf['root']);
    define('DSN',$conf['driver'].':host='.$conf['dbhost'].';dbname='.$conf['dbname']);
    define('USR',$conf['dbuser']);
    define('PWD',$conf['dbpass']);

    App::run();
    
```

Un cop carregat el PSR-4, definim les constants de l'entorn de l'aplicació, aquestes són extretes del fitxer de configuració **config.json**

\*\*\*\*

