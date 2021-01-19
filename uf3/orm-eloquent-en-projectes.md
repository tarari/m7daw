---
description: Integració del component Eloquent i Database en projectes PHP
---

# ORM Eloquent en projectes

L'ús de ORM \(Object Relational Mapping\) en projectes PHP orientat a objecte, permeten realitzar l'abstracció desitjada, deslligar-nos del llenguatge SQL per a manipulació de dades en la capa de persistència.

## Instal·lació

Per instal·lar el component Eloquent, podem utilitzar _composer_. Aquesta eina ens permet controlar les dependències de tercers al nostre projecte.

Crearem el nostre arxiu de dependències composer.json a l'arrel del nostre projecte:

```text
{
  "name": "app-orm",
  "type": "project",
  "autoload": {
    "psr-4": {
      "App\\": "src/"
    }
  },
  "require": {
    "illuminate/database": "^6.12"
  }
}
```

Observem la clau _**require**_, és la que determina les dependències.

Des de la consola, en el directori arrel del projecte, podem iniciar la actualització o instal·lació.

```text
composer install
```

### Configuració del component 

La configuració prèvia per poder utilitzar les característiquees del ORM, consisteix en crear un arxiu de configuració _**config.php**_ on definirem les constants de la connexió a la base de dades. En el directori arrel,

```text
<?php

    define('DBDRIVER','mysql');
    define('DBHOST','127.0.0.1');
    define('DBNAME','dbase');
    define('DBUSER','dbase_user');
    define('DBPASS','*********');
```

Les dades d'accés són, el driver mysql si fem servir mysq o mariadb, el host que conté el servei de base de dades, el nom de la base de dades, l'usuari de la base de dades amb privilegis CRUD \(crear, llegir, actualitzar i eliminar\), i el password d'aquest usuari.

[Si teniu dubtes, mireu com crear una base de dades i un usuari](../apendixs/apendix-crear-base-de-dades-i-usuari-en-docker.md)

### Class DB

La class DB, inicia la connexió a la base de dades i estableix els paràmetres inicials.

```text
namespace App\Models;

use Illuminate\Database\Capsule\Manager as Capsule;

class DB{
    function __construct()
    {
        $capsule=new Capsule();
        $capsule->addConnection(
            [
                'driver'=>DBDRIVER,
                'host'=>DBHOST,
                'database'=>DBNAME,
                'username'=>DBUSER,
                'password'=>DBPASS,
                'charset'=>'utf8',
                'collation'=>'utf8_unicode_ci',
                'prefix'=>''

            ]
        );
        $capsule->setAsGlobal();
        $capsule->bootEloquent();
    }
}

```

### Introducció a Eloquent

**Eloquent** està inclós  a Laravel i proporciona una forma molt simple de treballar amb la base de dades. La idea bàsica és que cada taula té un **MODEL** corresponent i és l'element que utilitzem per interactuar amb una taula.



