---
description: Integració del component Eloquent i Database en projectes PHP
---

# ORM Eloquent en projectes

L'ús de ORM (Object Relational Mapping) en projectes PHP orientat a objecte, permeten realitzar l'abstracció desitjada, deslligar-nos del llenguatge SQL per a manipulació de dades en la capa de persistència.

## Instal·lació

Per instal·lar el component Eloquent, podem utilitzar _composer_. Aquesta eina ens permet controlar les dependències de tercers al nostre projecte.

Crearem el nostre arxiu de dependències composer.json a l'arrel del nostre projecte:

```
{
  "name": "app-orm",
  "type": "project",
  "autoload": {
    "psr-4": {
      "App\\": "src/"
    }
  },
  "require": {
    "illuminate/database": "^6.12"
  }
}
```

Observem la clau _**require**_, és la que determina les dependències.

Des de la consola, en el directori arrel del projecte, podem iniciar la actualització o instal·lació.

```
composer install
```

### Configuració del component

La configuració prèvia per poder utilitzar les característiquees del ORM, consisteix en crear un arxiu de configuració _**config.php**_ on definirem les constants de la connexió a la base de dades. En el directori arrel,

```
<?php

    define('DBDRIVER','mysql');
    define('DBHOST','127.0.0.1');
    define('DBNAME','dbase');
    define('DBUSER','dbase_user');
    define('DBPASS','*********');
```

Les dades d'accés són, el driver mysql si fem servir mysq o mariadb, el host que conté el servei de base de dades, el nom de la base de dades, l'usuari de la base de dades amb privilegis CRUD (crear, llegir, actualitzar i eliminar), i el password d'aquest usuari.

[Si teniu dubtes, mireu com crear una base de dades i un usuari](../../apendixs/apendix-crear-base-de-dades-i-usuari-en-docker.md)

### Class DB

La class DB, inicia la connexió a la base de dades i estableix els paràmetres inicials.

```
namespace App\Models;

use Illuminate\Database\Capsule\Manager as Capsule;

class DB{
    function __construct()
    {
        $capsule=new Capsule();
        $capsule->addConnection(
            [
                'driver'=>DBDRIVER,
                'host'=>DBHOST,
                'database'=>DBNAME,
                'username'=>DBUSER,
                'password'=>DBPASS,
                'charset'=>'utf8',
                'collation'=>'utf8_unicode_ci',
                'prefix'=>''

            ]
        );
        $capsule->setAsGlobal();
        $capsule->bootEloquent();
    }
}
```

## Introducció a Eloquent

**Eloquent** està inclós a Laravel i proporciona una forma molt simple de treballar amb la base de dades. La idea bàsica és que cada taula té un **MODEL** corresponent i és l'element que utilitzem per interactuar amb una taula.

Suporta una gran varietat de relacions:

* [One To One](https://laravel.com/docs/8.x/eloquent-relationships#one-to-one)
* [One To Many](https://laravel.com/docs/8.x/eloquent-relationships#one-to-many)
* [Many To Many](https://laravel.com/docs/8.x/eloquent-relationships#many-to-many)
* [Has One Through](https://laravel.com/docs/8.x/eloquent-relationships#has-one-through)
* [Has Many Through](https://laravel.com/docs/8.x/eloquent-relationships#has-many-through)
* [One To One (Polymorphic)](https://laravel.com/docs/8.x/eloquent-relationships#one-to-one-polymorphic-relations)
* [One To Many (Polymorphic)](https://laravel.com/docs/8.x/eloquent-relationships#one-to-many-polymorphic-relations)
* [Many To Many (Polymorphic)](https://laravel.com/docs/8.x/eloquent-relationships#many-to-many-polymorphic-relations)

### Relacions One to One

Relació d'associació entre un objecte i un altre, per exemple, un usuari té associat un telèfon, a nivell de Model, seria així:

```php
//MODEL


namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Get the phone associated with the user.
     */
    public function phone()
    {
        return $this->hasOne(Phone::class);
    }
}
/// i la relcació inversa
class Phone extends Model
{
    /**
     * Get the user that owns the phone.
     */
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

A nivell d'esquema, per fer les migracions corresponents, cal afegir els següent camp a la taula _**phones**_

```php
// table
....
$table->unsignedBigInteger('user');
$table->foreign('user')->references('id')->on('users');
```

#### Desar un registre

```php
// entre usuari i telèfon
$user->phone()->save($phone);
// entre telèfon i usuari
$car->user()->associate($user)->save();
```

#### Treure registres

```php
$user->car;
$phone->user
```

### Relacions One to Many

Aquesta relació, estableix una associació de múltiples objectes a un de sol, per exemple, un únic post d'un blog, estar associat a mútliples comentaris:

```php
//MODEL
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    /**
     * Get the comments for the blog post.
     */
    public function comments()
    {
        return $this->hasMany(Comment::class);
    }
}

// i la relació inversa
class Comment extends Model
{
    /**
     * Get the post that owns the comment.
     */
    public function post()
    {
        return $this->belongsTo(Post::class);
    }
}
```

Si la clau forània no termina en `_id,`\_ \_per exemple `post_id`, cal afegir en la funció de relació la clau forània, en aquest cas hi posarem `post`:

```php
//return $this->hasMany(Comment::class, 'foreign_key');
return $this->hasMany(Comment::class, 'post');
```

I a la taula comments, a l'esquema quan fem les migracions, cal afegir:

```php
// table comments
....
$table->unsignedBigInteger('post');
$table->foreign('post')->references('id')->on('posts');
```

#### Desar registres

```php
// desar diversos comentaris sobre un post
$post->comments()->saveMany([
   $comment1, 
   $comment2,
]);

// o d'un en un
$post->comments()->save($comment);
// o associar un comentari a un post
$comment->post()->associate($post)->save();
```

### Relacions Many to Many

És un tipus de relació un pel més complicada, posem el cas d'uns usuaris que poden tenir més d'un rol dins d'un blog, per exemple lector i editor, per tant els rols poden tenir més d'un usuari associat i viceversa.

A nivell de taules, la nostra estructura seria la següent, com s'observa, cal una tercera taula pivot role\_user:

```
users
    id - integer
    name - string

roles
    id - integer
    name - string

role_user
    user_id - integer
    role_id - integer
```

A nivell de models, cal definir la relació a través del mètode `belongsToMany`. Mirem com queda:

```php
// Model User
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * The roles that belong to the user.
     */
    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }
}

//Model Role (relació inversa)

class Role extends Model
{
    /**
     * The users that belong to the role.
     */
    public function users()
    {
        return $this->belongsToMany(User::class);
    }
}
```

Per accedir als diferents rols d'un usuari determinat fem servir la fòrmula:

```php
$roles = User::find(1)->roles()->get();
```

#### Desar registres

```php
$user->roles()->attach([
   $role1->id,
   $role2->id,
]);
// per prevenir duplicats utilitzem sync()
$user->roles()->sync([
   $role1->id,
   $role2->id,
]);
```
