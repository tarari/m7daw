---
description: Aprofundim i comencem a explicar Programació orientada a l'objecte en PHP.
---

# UF2. Generació dinàmica de pàgines web

### Projecte de creació i adaptació d’un marc de treball propi adaptant app <a id="yui_3_17_2_1_1582493701824_114"></a>

Aprofundim el coneixement de la programació orientada a objecte a través de la creació d'un marc de treball propi adaptant una aplicació.

En les aplicacions web és important resoldre els següents problemes :

* Captura del REQUEST del client
* Adaptar resposta al tipus de client \(HTML, JSON...\)

En el cas de seguir un paradigma MVC \(Model View Controller\), es poden adoptar els següents elements:

* Reescriptura del REQUEST per fer-lo més amigable \(.htaccess\)
* Captura a través del Request del controlador \(objecte\) i l'acció \(mètode\)
* Llençament de la instància de l'objecte controlador i posteriorment cridar l'acció corresponent

**Funcionament bàsic de l'app \( paradigma MVC a nivell web\)**

**Cicle REQUEST - RESPONSE**

Si seguim el cicle entre client i servidor, podem analitzar com es desenvolupa la resposta, aquesta es dona sempre a nivell de Controlador, ja que estem fent servir paradigmes MVC.

![](https://agora.xtec.cat/escolesnuria/moodle/pluginfile.php/4412/mod_assign/intro/Cicle%20request.png)

### Reescriptura de la URI

**.htaccess**

```text
Options +FollowSymLinks

RewriteEngine On

RewriteCond %{REQUEST_FILENAME} -f [OR]
RewriteCond %{REQUEST_FILENAME} -d 

RewriteRule ^.*$ index.php [NC,L]
```

Com s'observa, es tracta de redirigir tota consulta \(_query_ \) cap al controlador frontal de l'aplicació \(**index.php**\).

Per una bona càrrega de classes fem servir l'estandar PSR- 4 \(autocàrrega de classes\)

**Adaptació a estàndat PSR-4 \(autoload\) Composer.json**

```text
{
    "autoload": {
        "psr-4":{
            "App\\": "src/"
        }
    }
```



