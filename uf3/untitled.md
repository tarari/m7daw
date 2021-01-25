# Laravel \(2a part\)

## Configuració de base de dades

Un cop creada la base de dades, editem el fitxer **`.env`**:

```php
....
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=*****
-----
```

## Enrutament

### Enrutament bàsic:

Dins l'arxiu **routes/web.php.  routes/api.php,** etc..

```php
Route::get('welcome', function () { 
      return "Hello World!"; }); 
Route::post('welcome', function () { 
       return "Hello World!"; });
```

Com es pot observar, la funció estàtica get, post indiquen el mètode HTTP.

```text
Route::match(['get', 'post'], 'match', function() { 
    return "Matches GET and POST";
}); 
Route::any('any', function () { 
    return "Matches any method"; 
});
```

Una ruta es pot definir per un conjunt de mètodes, **match.** O per qualsevol mètode, **any**.

Nota: En comptes de definir tota la lògica en **routes**, és possible organitzar-la en diferents controladors, els quals es poden definir en el directori  se definen en el directorio _**app/Http/Controllers**_.

### Rutes que criden a un controlador

Habitualment, en comptes de definir un closure, es crida a una acció d'un controlador:

```text
Route::get('post/{id}', [
 'uses' => 'PostController@show'
]);
```

## Controladors

Podem crear controladors amb artisan d'una manera molt simple:

```text
php artisan make:controller PostController
```

Això cea un controlador buit, però si volem crear un controlador per a recursos, podem utilitzar -r que afegeix mètodes típics de control de recursos \(index, show, store, destroy, etc\).

```text
php artisan make:controller PostController -r
```

### Middlewares

Els middleware proporcionen un mecanisme de filtrat als REQUESTs de l'aplicació

Laravel inclou per defecte el middleware d'autenticació que detecta si un usuari està autenticat. Si ho està, el middleware deixarà que segueixi l'execució del programa. Si no, portarà a l'usuari a la pantalla de login.

```text
Route::post('post/store', ['middleware' => 'auth',
    function () {
    //
}]);
```

## Frontend

Frontend indica sempre la part que es desenvolupa al costat client, inclou l'HTML, el CSS i JS fonamentalment.

### Blade

Representa el motor de plantilles que utilitza de forma predeterminada el Laravel. Els fitxers de blade tenen l'extensió `.blade.php`, i entre d'altres característiques, permeten l'ús de php dins seu, ja que el que fa és compilar en PHP pla.

## Eloquent

Crear models:

```text
php artisan make:model Post -m
```

Crea el model Post amb la migració preparada.

### Migracions

Consisteix en passar dades de model cap al sistema gestor de base de dades que s'hagi implementat en  en fitxer de variables d'entorn **`.env`**



