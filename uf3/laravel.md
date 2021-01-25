---
description: Un dels frameworks PHP més utilitzats i amb més projecció.
---

# Laravel \(1a part\)

## Què és?

Laravel és una eina que ens ajuda a desenvolupar sistemes web , pot ser un sistema, pàgina web o un API. A aquest tipus d'eines li diem Framework i és un entorn de treball o eina estandaritzada en quant a concepte, bones pràctiques, funcionalitats comunes  i estil per resoldre algun tipus de problema.

## Introducció

Com tots el frameworks, el seu gran avantatge és que ja té moltes coses resoltes, l'inici de sessió i registre, paginació de les dades, la connexió i consulta a la base de dades, el sistema de cache, les rutes, el sistema de plantilles, la seguretat i moltes coses més.

Hem d'entendre'l com un sistema de capes i depenent del problema has de saber quina capa tractar.

* Podem crear una ruta  i llençar vistes de d'allà \(estàtic\) 
* O crear una ruta , aquesta apunti al controlador i des d'allà crear una vista en pantalla.
* O seguint el cas anterior pots agregar una capa de seguretat intermitja i si estàs loguejat aleshores el sistema et deixa anar al controlador i llençar la vista pertinent.

### Requeriments

Què és el que necessitem per treballar am Laravel:

* PHP v.7.2 o més gran
* Apache o Nginx
* Sistema gestor de base de dades: MySql o MariadB
* Composer
* Git
* Editor de codi
* Navegador



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

Laravel disposa, a través del seu comando **artisan**, un servidor local per a desenvolupar.

```text
cd projecte
php artisan serve
Laravel development server started: http://127.0.0.1:8000
[Mon Mar  2 20:59:15 2020] PHP 7.4.3 Development Server (http://127.0.0.1:8000) started

```

![Quan accedim des del navegador](../.gitbook/assets/captura-de-pantalla-2020-03-02-a-les-21.01.51.png)

## Estructura de carpetes



**Arrel del projecte**: app, bootstrap, config, database, public, resources, routes, storage, tests, vendor.

**Dins d'app**: Broadcasting, Console, Events, Exceptions, Http, Jobs, Listeners, Mail, Notifications, Policies, Providers, Rules.



{% hint style="info" %}
Amb el temps adquirirem l'habilitat de crear qualsevol estructura de carpetes, la que millor s'adapti al projecte que implementem. 
{% endhint %}

### App

És la carpeta on desenvolupem l'apicació, és on escriurem el nostre codi PHP. 

Aquí dins trobem una varietat important de carpetes com  **Console, Http, Exceptions y Providers**. En **Console** tenim els nostres comandos personalitzats  de **Artisan** i en **Http** creem els nostres controladors, middlewares i processaments de Requests, tot el relacionat amb el HTTP. 

### Arxiu .env

Totes les dades configurables de l'entorn i ecosistema de Laravel es poden configurar a través d'aquest fitxer. Es tracta d'un fitxer que es pot repetir per cada ecosistema on es vulgui establir, ja sigui en producció `.env` en proves **`.env.testing`**, etc.

Entre d'altres coses que podem emmagatzemar, tenim la configuració de la base de dades, usuari, host, base de dades i password \(no és visible, no ens preocupem\). 

També es pot desar la KEY \(encriptada\) del programa, generada en el moment d'instal·lació i modificable en qualsevol instant, que s'utilitza per per exemple, generar el token per evitar atacs CSRF.

### Configuració

En la carpeta config, establim tots els arxius de configuració que necessitem en l'aplicació. Exemple d'arxiu que retorna element de configuració:

```php
<?php
    return ['color'=>'#04a23e'];
```

## RECORDATORI: Cicle de vida del REQUEST

Des que el client envia un REQUEST fins que el sistema envia un RESPONSE, Laravel té com a punt d'entrada del tractament del REQUEST el fitxer **public/index.php,** on hi ha la preparació de l'entorn del sistema. Posteriorment s'envia cap a **bootstrap/app.php**, que actua com a contenidor de serveis \( _service container_ \). Aquest actua com a controlador frontal i reenvia cap als kernels o nuclis, bàsicament HTTP Kernel API kernel i Console Kernel, que acuten com a proveïdors de serveis \(**service providers**\). 

Els kernels fan servir el _**middlewares**_ com a filtres específics de les REQUESTS, per exemple un MW verifica si l'usuari s'ha autenticat i actúa en conseqüència, redirigint cap a la pantalla de login o una altra. El tractament final el fa el servei \(_service_\) que permet obtenir una resposta \(RESPONSE\)



![Cicle de vida, des del REQUEST fins a RESPONSE](../.gitbook/assets/lifecycle.png)

### Routes \(rutes\)

Defineixen a qui/quins serveis cridem quan se solicita una determinada URI.

Totes les rutes es troben definides en un fitxer  _`routes/web.php,`_ ``totes, es troben aquí definides.

A través de les rutes, podem donar servei als mètodes REQUEST **get, post, put i delete**.​ Podem dir que respongui a un mètode determinat, un conjunt de mètode o bé a qualsevol.

{% tabs %}
{% tab title="PHP" %}
```php
Route::get('/', function () {
    return view('welcome');
});
```
{% endtab %}
{% endtabs %}

Laravel busca en la carpeta ****_**resources/views**_ ****la plantilla _welcome.blade.php_

{% tabs %}
{% tab title="XML/HTML/SVG" %}
```markup
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
......
```
{% endtab %}
{% endtabs %}

Podem veure quines **altres opcions** tenim com a sortida, en comptes de view...

```php
Route::get('/', function () {
    return ['foo'=>'cosa'];
});
```

La sortida serà en JSON.

#### Rutes que miren el REQUEST

A vegades és necessari **passar paràmetres REQUEST,** mirem la forma més simple:

```text
Route::get('/test',function (){
    $name=request('name');
    return $name;
});
```

Podem fer la prova, al navegador: `http://localhost:8000/test?name=Pepe`

També podem **passar la variable a una vista**:

```text
Route::get('/test',function (){
    return view('test',['name'=>request('name')]);
});
```

I al fitxer **views/test.blade.php** podem utilitzar la variable `$name` de la següent manera, format php o format blade, però millor de la forma blade \(1\), ja que filtra la request i ens evitem atacs. \(Per filtrar, podem fer servir _**htmlspecialchars\(\)**_\)

```text
{{!! $name !!}} equival a  <?= $name; ?>
{{ $name }} equival a <?= htmlentities($name); ?>
```

### Response

Retorn d'una  resposta ben formada, amb **capçaleres head,** status i contingut. \(response\)

```php
Route::get('home', function () {
    return response('Hello World', 200)
                  ->header('Content-Type', 'text/plain');
});
```

### Comandos Artisan

Artisan és el nom de la interfície de comandos en línia inclosa en Laravel. Cobreix moltes tasques com ara; treball amb migracions de bases de dades, eliminar catxé, crear els fitxers necessaris per l'autenticació, creació de models, controladors, classes d'events i molt més...

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

## Autenticació

Per la via ràpida, instal·lem _**laravel/ui**_ aquest paquet de composer i executem `php artisan ui vue --auth` en una aplicació Laravel **nova, sense estrenar,** si fem servir **vue.** Però podria ser **bootstrap** o **react** Després de migrar la bas de dades, comprovem al navegador _**http://app.test/register**_

Laravel instal·la de forma automàtica un sistema d'autenticació, per tant trobareu creat ja el model **App\User.** També trobarem  en **resources** una carpeta anomenada **auth,** amb totes les plantilles blade que s'utilitzaran en els formularis d'autenticació i registre.



