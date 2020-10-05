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
          dashboard.php
          login.php
          register.php
    connect.php
    schema.php
    login.php
    
    
```

A connect.php hi posarem la connexió PDO, ja sigui mysql o bé sqlite \(depenent de les extensions\)

**`index.php`**, arrenca l'aplicació i si troba valor en el paràmetre $\_GET\['page'\], el localitzarà i el carregarà \(la vista i el controlador\).

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





