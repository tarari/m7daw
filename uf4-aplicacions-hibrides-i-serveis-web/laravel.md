---
description: Un dels frameworks PHP més utilitzats i amb més projecció.
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

Des que el client envia un REQUEST fins que el sistema envia un RESPONSE, Laravel té com a punt d'entrada del tractament del REQUEST el fitxer **public/index.php,** on hi ha la preparació de l'entorn del sistema. Posteriorment s'envia cap a **bootstrap/app.php**, que actua com a contenidor de serveis \( _service container_ \). Aquest actua com a controlador frontal i reenvia cap als kernels o nuclis, bàsicamen HTTP Kernel API kernel i Console Kernel, que acuten com a proveïdors de serveis \(service providers\). Els kernels fan servir el _**middlewares**_ com a filtres específics de les REQUESTS, per exemple un MW verifica si l'usuari s'ha autenticat i actúa en conseqüència, redirigint cap a la pantalla de login o una altra. El tractament final el fa el servei \(_service_\) que permet obtenir una resposta \(RESPONSE\)



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

### Comandos Artisan

Artisan és el nom de la interfície de comandos en línia incosa en Laravel. Cobreix moltes tasques com ara; treball amb migracions de bases de dades, eliminar catxé, crear els fitxers necessaris per l'autenticació, creació de models, controladors, classes d'events i molt més...

```text
php artisan list
```

Llista de comandos artisan més comuns:



| Command | Description |
| :--- | :--- |
| clear-compiled | Remove the compiled class file |
| down | Put the application into maintenance mode |
| env | Display the current framework environment |
| help | Displays help for a command |
| list | Lists commands |
| migrate | Run the database migrations |
| optimize | Optimize the framework for better performance |
| serve | Serve the application on the PHP development server |
| tinker | Interact with your application |
| up | Bring the application out of maintenance mode |
| app:name | Set the application namespace |
| auth:clear-resets | Flush expired password reset tokens |
| cache:clear | Flush the application cache |
| cache:table | Create a migration for the cache database table |
| config:cache | Create a cache file for faster configuration loading |
| config:clear | Remove the configuration cache file |
| db:seed | Seed the database with records |
| event:generate | Generate the missing events and listeners based on registration |
| key:generate | Set the application key |

| Command | Description |
| :--- | :--- |
| make:auth | Scaffold basic login and registration views and routes |
| make:console | Create a new Artisan command |
| make:controller | Create a new controller class |
| make:event | Create a new event class |
| make:job | Create a new job class |
| make:listener | Create a new event listener class |
| make:middleware | Create a new middleware class |
| make:migration | Create a new migration file |
| make:model | Create a new Eloquent model class |
| make:policy | Create a new policy class |
| make:provider | Create a new service provider class |
| make:request | Create a new form request class |
| make:seeder | Create a new seeder class |
| make:test | Create a new test class |
| migrate:install | Create the migration repository |
| migrate:refresh | Reset and re-run all migrations |
| migrate:reset | Rollback all database migrations |
| migrate:rollback | Rollback the last database migration |
| migrate:status | Show the status of each migration |
| queue:failed | List all of the failed queue jobs |
| queue:failed-table | Create a migration for the failed queue jobs database table |
| queue:flush | Flush all of the failed queue jobs |
| queue:forget | Delete a failed queue job |
| queue:listen | Listen to a given queue |
| queue:restart | Restart queue worker daemons after their current job |

| Command | Description |
| :--- | :--- |
| queue:retry | Retry a failed queue job |
| queue:table | Create a migration for the queue jobs database table |
| queue:work | Process the next job on a queue |
| route:cache | Create a route cache file for faster route registration |
| route:clear | Remove the route cache file |
| route:list | List all registered routes |
| schedule:run | Run the scheduled commands |
| session:table | Create a migration for the session database table |
| vendor:publish | Publish any publishable assets from vendor packages |
| view:clear | Clear all compiled view files |

