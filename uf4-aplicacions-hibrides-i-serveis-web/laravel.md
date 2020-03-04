---
description: Un dels frameworks PHP més utilitzats i amb més projecció
---

# Laravel

## Instal·lació

A través de composer com a projecte:

```text
composer create-project --prefer-dist laravel/laravel projecte
```

També  es podria haver fet de forma global i utilitzar després el comando **laravel**.

```text
composer global require laravel/installer
```

I per tant, poder crear projectes a través de comando

```text
laravel new projecte
```

#### Servidor local de desenvolupament

Laravel disposa, a través del seu comando artisan, un servidor local per a desenvolupar.

```text
cd projecte
php artisan serve
Laravel development server started: http://127.0.0.1:8000
[Mon Mar  2 20:59:15 2020] PHP 7.4.3 Development Server (http://127.0.0.1:8000) started

```

![Quan accedim des del navegador](../.gitbook/assets/captura-de-pantalla-2020-03-02-a-les-21.01.51.png)

### Cicle de vida del REQUEST

![Cicle de vida, des del REQUEST fins a RESPONSE](../.gitbook/assets/lifecycle.png)

### Routes

Defineixen a qui / quins serveis cridem quan se solicita una determinada URI.

Totes les rutes es troben definides en un fitxer  _routes/web.php,_ totes les rutes es troben aquí definides.

A través de les rutes, podem donar servei als mètodes REQUEST get, post, put i delete.​ Podem dir que respongui a un mètode determinat, un conjunt de mètode o bé a qualsevol.

```text
Route::get('/', function () {
    return view('welcome');
});
```

Laravel busca en la carpeta _resources/views_ la plantilla _welcome.blade.php_

```text
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
......
```

Podem veure quines altres opcions tenim com a sortida, en comptes de view...

```text
Route::get('/', function () {
    return ['foo'=>'cosa'];
});
```

La sortida serà en JSON.



