---
description: Carret de compra
---

# Activitat dirigida 1

Es tracta de simular l'acció d'afegir elements dins d'un carret de compra. Ens valdrem de variables de sessió i arrays.

### Estructura:

L'estructura de l'aplicació és simple, un controlador frontal (`index.php`) i una carpeta de codi (`src`)

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

