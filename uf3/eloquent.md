# Eloquent

## Introducció

L'ORM Eloquent inclòs amb Laravel, proporciona una implementació **d'ActiveRecord** bonica i senzilla per treballar amb la vostra base de dades. Cada taula de base de dades té un "Model" corresponent que s'utilitza per interactuar amb aquesta taula. Els models ens permeten consultar dades a les  taules, així com inserir nous registres a la taula.

Abans de començar, ens hem d'assegurar de configurar una connexió de base de dades a `config/database.php`. Repasseu l'apartat de fitxer .[`env`](broken-reference)

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

Les relacions entre entitats  i els seus agregats ( DDD ) s'estableixen directament en els models a través dels mètodes següents:

#### One to one

És la relació més bàsica, s'estableix entre dos models o entitats. Exemple: un post i els seus details, un cotxe i el seu  propietari:

S'estableix de la següent manera:

```php
class Post extends Model
{
    use HasFactory;

    /**
     * @return HasOne
     * @description get the detail associated with the post
     */
    public function detail(): HasOne
    {
        return $this->hasOne(Detail::class);
    }
}
```

I la seva inversa:

```php
class Detail extends Model
{
    use HasFactory;

    /**
     * @return BelongsTo
     * @description Get the post that owns the details
     */
    public function post(): BelongsTo
    {
        return $this->belongsTo(Post::class);
    }
}
```

La taula details, contindrà un camp que manté la clau forània:

```php

      // define foreign key
      $table->foreignId('post_id')
            ->constrained()
            ->onUpdate('cascade')
            ->onDelete('cascade');

```

**Store:**

per desar elements:

```php
// Create relation between Post and Detail.
$post->detail()->save($post);
// Create relation between Detail and Post.
$detail->post()->associate($post)->save();
```

**Get:**

per extreure elements:

```php
// Get Post Detail
$post->detail;
// Get Detail Post
$detail->post;
```

#### One to many

En la relació one to many, un element d'una taula es refereix a molts elements en una altra taula:

Podria ser el cas de Category i Post

```php
class Category extends Model
{
    use HasFactory;

    /**
     * @return HasMany
     * @description get all posts for the category
     */
    public function posts(): BelongsTo
    {
        return $this->hasMany(Post::class);
    }
}

class Post extends Model
{
    use HasFactory;

    /**
     * @return BelongsTo
     * @description get the category for the blog post.
     */
    public function category(): HasOne
    {
        return $this->belongsTo(Category::class);
    }
}
```

Per poder desar els registres:

```php
// Create relation between Category and Post.
$category->posts()->saveMany([
   $post1, 
   $post2,
]);
// Or use the save() function for single model.
$thief->cars()->save($car);
// Create relation between Car and Thief.
$car->thief()->associate($thief)->save();

```

I  per poder extreure:

```php
// Get Category Post
$category->posts;
// Get Post Category
$post->category;
```

De vegades és possible que vulguem carregar sempre algunes relacions (de manera ràpida) quan recuperem un model. Per aconseguir-ho, podem definir una propietat `$with` al model:

<pre class="language-php"><code class="lang-php">&#x3C;?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
 
class Book extends Model
{
    /**
     * The relationships that should<a data-footnote-ref href="#user-content-fn-1"> always be loaded</a>.
     *
     * @var array
     */
    protected $with = ['author'];
 
    /**
     * Get the author that wrote the book.
     */
    public function author(): BelongsTo
    {
        return $this->belongsTo(Author::class);
    }
 
    /**
     * Get the genre of the book.
     */
    public function genre(): BelongsTo
    {
        return $this->belongsTo(Genre::class);
    }
}
</code></pre>



#### Many to many

Les relacions de **molts a molts** són una mica més complicades que les relacions `hasOne`i `hasMany`. Un exemple de relació de molts a molts és un usuari que té molts rols i aquests rols també són compartitrs amb altres usuaris de l'aplicació.&#x20;

Per exemple, a un usuari se li pot assignar la funció d'Autor" i "Editor"; no obstant això, aquests rols també es poden assignar a altres usuaris. Per tant, un usuari té molts rols i un rol té molts usuaris.

**Model**

Les relacions de molts a molts es defineixen escrivint un mètode que retorna el resultat del mètode `belongsToMany`. El mètode `belongsToMany` el proporciona la classe `Illuminate\Database\Eloquent\Model,` base que utilitzen tots els models Eloquent de l'aplicació.&#x20;

Per exemple, definim un mètode  `roles` al nostre model`User`. El primer argument passat a aquest mètode és el nom de la classe de model relacionada:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;
 
class User extends Model
{
    /**
     * The roles that belong to the user.
     */
    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class);
    }
}
```

**Inversa de la relació**

Per definir la "inversa" d'una relació de molts a molts, hauríem de definir un mètode en el model relacionat que també retorni el resultat del mètode `belongsToMany`. Per completar el nostre exemple d'usuari-rol, definim el mètode`users`al model `Role`:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;
 
class Role extends Model
{
    /**
     * The users that belong to the role.
     */
    public function users(): BelongsToMany
    {
        return $this->belongsToMany(User::class);
    }
}
```

**Taula intermitja**

Com ja hem après, treballar amb relacions de molts a molts requereix la presència d'una taula intermèdia. Eloquent ofereix algunes maneres molt útils d'interaccionar amb aquesta taula. Per exemple, suposem que el nostre model `User` té molts models`Role` amb els quals està relacionat. Després d'accedir a aquesta relació, podem accedir a la taula intermitja mitjançant l'atribut  **`pivot`**dels models:



```php
use App\Models\User;
 
$user = User::find(1);
 
foreach ($user->roles as $role) {
    echo $role->pivot->created_at;
}
```

Les taules intermitges que utilitzen les marques de temps d'Eloquent que es mantenen automàticament han de tenir les columnes amb les marques de temps `created_at i` `updated_at`.

[^1]: sempre es carreguen
