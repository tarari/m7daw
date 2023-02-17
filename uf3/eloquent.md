# Eloquent

## Introducció

L'ORM Eloquent inclòs amb Laravel, proporciona una implementació **d'ActiveRecord** bonica i senzilla per treballar amb la vostra base de dades. Cada taula de base de dades té un "Model" corresponent que s'utilitza per interactuar amb aquesta taula. Els models ens permeten consultar dades a les  taules, així com inserir nous registres a la taula.

Abans de començar, ens hem d'assegurar de configurar una connexió de base de dades a `config/database.php`. Repasseu l'apartat de fitxer .[`env`](broken-reference)``

### &#x20;Els models

\
Per començar, creem un model Eloqüent. Els models normalment viuen al directori `app`, però podem col·locar-los a qualsevol lloc que es pugui carregar automàticament segons el vostre `composer.json`. Tots els models Eloquent estenen la classe `Illuminate\Database\Eloquent\Model.`

La manera més senzilla de crear una instància de model és utilitzar  `make:model` :

```php
php artisan make:model Post
```

```php
php artisan make:model Flight --migration

php artisan make:model Flight -m
```

Si  a més volem generar una [migració de base](https://laravel.com/docs/7.x/migrations) de dades quan generem el model, podem utilitzar l' opció `--migration`o `-m`:



```php
php artisan make:model Post --migration

php artisan make:model Post -m
```

El model generat, inclou quina taula està associada 'posts', si volem canviar fem servir la propietat `$table`:



```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    /**
     * The table associated with the model.
     *
     * @var string
     */
    protected $table = 'posts';
    /**
     * The primary key associated with the table.
     *
     * @var string
     */
    protected $primaryKey = 'post_id';
}
```

S'associa de forma automàtica una clau primària `'id'`, si volem modificar fem servir `$primaryKey`.

### Relacions en eloquent

Les relacions entre entitats s'estableixen directament en els models a través dels mètodes següents
