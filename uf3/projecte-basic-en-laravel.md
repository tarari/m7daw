# Projecte bàsic en Laravel

## Instal·lació de projecte

{% code lineNumbers="true" %}
```shell
composer create-project laravel/laravel piopio
cd piopio
php artisan serve
```
{% endcode %}

## Sistema d'autenticació, laravel breeze

Aquest sistema proporciona login, registre, reset de password, verificació d'email i confirmació de password. Té disponibilitat de tres tipus de vistes, en Blade, Vue i React.

{% code lineNumbers="true" %}
```shell
composer require laravel/breeze --dev
php artisan breeze:install blade
```
{% endcode %}

Com s'observa, el nostre sistem funciona amb Blade.

A continuació, per observar si ha funcionat, observem que s'han creat una sèrie de recursos a _resources/views._

Fem ara una instal·lació de paquets JS necessaris per la correcta configuració del sistema. Serà necessari tenir instal·lats el gestor de paquets de NodeJS npm.

```shell-session
npm install
npm run dev
```

La UI (interfaç d'usuari) ja ha estat creada, ara caldrà crear la infraestructura (base de dades), treballarem en sqlite per tant ho indiquen en la variable d'entor DB\_Connection:

```
# .env
DB_CONNECTION='sqlite'
```

Modifiquem el fitxer de configuració de base de dades per indicar la ruta del fitxer sqlite i que habilitem l'ús de FOREIGN\_KEYS. Tan mateix hem de crear manualment el fitxer `sqlite`.

```php
// config/database.php
....
 'connections' => [

        'sqlite' => [
            'driver' => 'sqlite',
            'url' => env('DATABASE_URL'),
            'database' => storage_path().'/piopio.sqlite',
            'prefix' => '',
            'foreign_key_constraints' => env('DB_FOREIGN_KEYS', true),
        ],
```

En storage/ creem el fitxer `piopio.sqlite`.

```shell
touch piopio.sqlite
```

Ara posem en marxa les migracions, per donar suport de dades a l'aplicació:

```bash
 php artisan migrate:fresh
 Dropping all tables .................................. 54ms DONE
 INFO  Preparing database.  
 Creating migration table ............................. 13ms DONE
   INFO  Running migrations.  
  2014_10_12_000000_create_users_table ................. 26ms DONE
  2014_10_12_100000_create_password_resets_table ....... 27ms DONE
  2019_08_19_000000_create_failed_jobs_table ........... 28ms DONE
  2019_12_14_000001_create_personal_access_tokens_table  32ms DONE
```

## Comencem a desenvolupar

La nostra aplicació tracta de crear "pios" una versió descafeïnada de twitter.

Els pios es desen en una taula, també es poden controlar (crear, mostrar, modificar i eliminar), per tal cosa necessitem un model Pio, una migració de model cap a la base de dades i un controlador de recursos (PioController).

El comando que mostrem a continuació pot fer aquestes tres coses, de fet si necessitem més ajuda dels comandos, li afegirem l'opció `--help`.

<pre class="language-php"><code class="lang-php"><strong>php artisan make:model --help
</strong><strong>// aquí l'ajuda
</strong></code></pre>

```php
php artisan make:model -mrc Pio
```

Aquesta instrucció crea:

* `app/Models/Pio.php` - El model en Eloquent
* `database/migrations/<timestamp>_create_pios_table.php` - La database migration que crea la taula a la base de dades.
* `app/Http/Controller/PioController.php` - The HTTP controller que processa les requests i retorna les respostes.

