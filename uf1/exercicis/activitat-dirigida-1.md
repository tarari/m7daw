---
description: Carret de compra
---

# Activitat dirigida 1

Es tracta de simular l'acció d'afegir elements dins d'un carret de compra. Ens valdrem de variables de sessió i arrays.

### Estructura:

L'estructura de l'aplicació és simple, un controlador frontal (`index.php`) i una carpeta de codi (`src`)

```
.
├── index.php
└── src
    ├── add.php
    ├── cart_functions.php
    ├── cart.php
    ├── template
    │   └── cart.tpl.php
    └── view.php
```

Comencem pel controlador frontal, tota l'aplicació passa per aquí. Aquí establim les condicions de treball de l'aplicació:

```php
//index.php
<?php
  session_start();
  require 'src/view.php';
  require 'src/cart_functions.php';
  
  if(isset($_SESSION['cart'])) {
    $cart= $_SESSION['cart'];
  }else{
    $cart=require 'src/cart.php';
  }
  
  
  echo view('cart',['cart'=>$cart]);
```

Destaquem  del codi, es comprova si tenim o no el carret a la sessió, si no, el creem. Hem ficat valors inicials al `src/cart.php` una estructura d'array.

S'afegeix la vista ([mvc](https://ca.wikipedia.org/wiki/Model-Vista-Controlador)), que permet implementar visualment el carret al navegador. És un fitxer de plantilla (template) que barreja HTML i PHP, és l'unic lloc on hauríem de tenir aquesta combinació de llenguatges.

```php
// src/template/cart.tpl.php
<?php
  function view(string $tpl,$data){
    
    if($data){
      extract($data,EXTR_OVERWRITE);
    }
    ob_start();
    require ('src/template/cart.tpl.php');
    $str=ob_get_clean();
    
    return $str;
  }
```

Aquesta funció retorna un string, el qual ve de l'extracció i combinació de les dades `$data`, sobre el fitxer de plantilla `cart.tpl.php`, la sortida es fa sobre buffer de sortida per facilitar la sortida.
