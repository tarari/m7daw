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

Totes les rutes es troben definides en un fitxer  _routes/web.php,_ totes les rutes es troben aquí definides.

