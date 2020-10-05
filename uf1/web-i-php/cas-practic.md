---
description: Aplicació amb registres i autenticació d'usuaris.
---

# Cas pràctic

Anem a veure un exemple pràctic del que hem estudiat fins ara.

Punts a tenir en compte:

* _Separació de codi \(vista, fitxers i funcions \)_
* Connexió a base de dades tant en mysql i/o sqlite, opció configurable.

Cal preparar l'estructura en carpetes de l'aplicació:

```text
index.php
--/src
   |--/database
         database.sqlite
   |--/templates
          dashboard.tpl.php
          login.tpl.php
          register.tpl.php
    connect.php
    schema.php
    login.php
    
    
```

A connect.php hi posarem la connexió PDO, ja sigui mysql o bé sqlite \(depenent de les extensions\)

**`index.php`**, arrenca l'aplicació i si troba valor en el paràmetre `$_GET['page'],` el localitzarà i el carregarà \(la vista i el controlador\).

```php
<?php
    define ('ROOT',__DIR__);
    include 'config.php';
    require __DIR__.'/src/render.php';
    
    
    if (isset($_GET['page'])){
        $page=filter_input(INPUT_GET,'page',FILTER_SANITIZE_URL);
        if(in_array($page,$routes)){
            header('Location:'.$page.'.php');

        }else{
            include 'error.php';
        }
    }
```

Al fitxer de configuració podem afegir l'array de rutes disponibles a la nostra aplicació.

## La connexió a la base de dades

Volem que la nostra activitat revisi la connexió a la base de dades amb dos drivers o controladors, amb sqlite i amb mysql. Òbviament les connexions seran diferents:

```php
//connect.php
<?php

    function connectSqlite(string $dbname){
        try{
            $db=new PDO('sqlite:'.__DIR__.'/database/'.$dbname.'.sqlite');
            $db->setAttribute(PDO::ATTR_ERRMODE,PDO::ERRMODE_EXCEPTION);
            $db->setAttribute(PDO::ATTR_DEFAULT_FETCH_MODE,PDO::FETCH_ASSOC);

        }catch(PDOException $e){
            die($e->getMessage());
    
        }
        return $db;

    }

    function connectMysql(string $dsn,string $userdb,string $passdb){
        try{
            $db = new PDO($dsn, $userdb, $passdb);
            $db->setAttribute(PDO::ATTR_ERRMODE,PDO::ERRMODE_EXCEPTION);
            $db->setAttribute(PDO::ATTR_DEFAULT_FETCH_MODE,PDO::FETCH_ASSOC);

        }catch(PDOException $e){
            die( $e->getMessage());
            
        }
        return $db;
    }
```

En quant al fitxer de configuració, aquest podria tenir aquest aspecte:

```php
<?php

    $routes=[
        'login',
        'dashboard'
    ];
    $dbhost="127.0.0.1";
    $dbname='prova';
    $dsn='mysql:host='.$dbhost.';dbname='.$dbname.';';
    $driver='mysql';
    
```



