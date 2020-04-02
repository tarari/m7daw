---
description: Unes pautes per a la creació d'aplicacions a Laravel
---

# Aplicació en Laravel

A continuació us indiquem quins passos cal seguir per realitzar una aplicació en Laravel:

## 1.Casos d´ús

Ens permet entendre les diferents accions del nostre projecte

| EXEMPLE |  |
| :--- | :--- |
| USUARI \(ROLE\) | Login |
|  | Registre |
|  | Propietat /Registrar |
|  | Propietat /Editar |
|  | Propietat / Eliminar |
|  | Publicació / Crear |
|  | Publicació / Editar |
|  | Publicació / Eliminar |
| ADMIN \(ROLE\) | Backend Users |
|  | Backend Properties |
|  | Backend Publications |

## 2.Crear i modificar l'entorn .env de l'app

Modificar dades d'accés a la base de dades i usuari d'accés a la base de dades.

```bash
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=inmo
DB_USERNAME=inmo
DB_PASSWORD=linuxlinux
```

## 3. Activar l'autenticació i sistema ui

Si utilitzem sistema d'autenticació, caldria configurar-lo:

```bash
$ composer require laravel/ui
```

I activat amb el frontend que desitgem:

```bash
$ php artisan ui bootstrap --auth
```

Si volguèssim definir-lo més tard, simplement acondicionem l'autenticació, controladors, proveïdors i vistes:

```bash
$ php artisan make:auth
```

## 4. Definir models i relacions associades i fer les migracions

Podem fer servir els comandos artisan per crear els models. Un cop creats, cal acabar-los d'implementar.



```text
php artisan make:model Property -m
```

Després de definir els models i les seves migracions \(-m\), podem continuar modificant les migracions.

```text
php artisan make:migrations create_table_properties
```

Si migreu \( `php artisan migrate` \), ja tindreu creades les taules a la vostra base de dades.

Ara caldrà comprovar com queda l'script de migració:

Crearem un altre script de migració:

```text
php artisan make:migrations change_table_properties
```

 El modifiquem afegint les funcions **up**\(\) i **down**\(\) \(crear i eliminar taula\). Aneu amb compte amb els tipus de dades relacionats **id** de _users_ i **users\_id** de _properties_

```php
class ChangeTableProperties extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('properties', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->decimal('price',8,2);
            $table->string('description');
            $table->bigInteger('user_id')->unsigned();
            $table->timestamps();
            $table->foreign('user_id')->references('id')->on('users');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('properties');
    }
}

```

Ara ja podríem executar la migració: `php artisan migrate`

Acabem de fer la resta de models i les relacions pertinents.

![](../.gitbook/assets/inmo%20%281%29.png)



Segons l'esquema ER hem de poder crear els models necessaris amb les seves corresponents migracions:

```php
php artisan make:model Property -m
```

Aquesta línia crearà el model App\Property i també la seva migració: `create_property_table`

El mateix farem amb la resta de models, acabarem de crear les migracions, tenint molt en compte les relacions entre taules. Per exemple a la migració de contacts `create_table_contacts` :

```php
class CreateContactsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('contacts', function (Blueprint $table) {
            $table->id();
            $table->bigInteger('interested_id')->unsigned();
            $table->bigInteger('publication_id')->unsigned();
            $table->foreign('interested_id')->references('id')->on('users');
            $table->foreign('publication_id')->references('id')->on('publications');
            $table->timestamps();
        });

```

Un cop fets els fitxers de migració completarem la migració:

```php
php artisan migrate
```

#### Completar mètodes en models

Ara queda acabar de copiar les relacions  i característiques $fillable, etc. en Eloquent, sempre fixant-nos en l'esquema ER anterior, per exemple, entre User i Property \(1:M\):

```php
class Property extends Model
{
    protected $fillable=['description','price','type','owner_id'];
    
    public function user(){
        return $this->belongsTo('App\User','owner_id');
    }
}

```

i User :

```php
  ........
    public function properties(){
        return $this->hasMany('App\Property');
    }
    public function publications(){
        return $this->hasMany('App\Publication');
    }

```

#### Afegir un camp a una taula

Per afegir un camp a una taula hem de crear una migració, per exemple, volem afegir _photo_ a **properties**

```php
php artisam make:migratrion add_photo_to_properties
```

Es crea la següent migració:

```php
class AddPhotoToProperties extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('properties', function (Blueprint $table) {
            $table->string('photo')->nullable();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('properties', function (Blueprint $table) {
            $table->dropColumn(['photo']);
        });
    }
}

```

Fixem-nos en el mètode **down\(\)**, efectivament, elimina el camp _photo_. Per últim, només queda que migrar : `php artisan migrate`





## 5. Crear els "_middlewares_" que en siguin necessaris. Middleware de control d'accès.

Podem crear un middleware de _control del rol_ d'usuari, recordem que un middleware actua com un filtre. 

![Els middlewares actuen com a filtres dels REQUESTs abans de processar la sol&#xB7;licitud](../.gitbook/assets/dibujo-sin-titulo.png)

Modificarem el model User, afegirem el camp de 'roles', i l'actualitzarem, Després afegirem els mètodes per manipular els rols al mateix model.

Pas a pas, caldrà crear una migració per afegir el camp roles al model - taula User, in cop migrat, afegim els mètodes al model.



Sino tenim instal·lat l'auntenticació:

```text
$ php artisan ui bootstrap --auth
```

Crear el model **Role** amb la seva respectiva migració \(paràmetre -m\):

```text
$ php artisan make:model Role -m
```

Edita la classe _**CreateRolesTable**_ a la carpeta de migrations:

```php
public function up()
{
    Schema::create('roles', function (Blueprint $table) {
        $table->increments('id');
        $table->string('name');
        $table->string('description');
        $table->timestamps();
    });
}

public function down()
{
    Schema::dropIfExists('roles');
}
```

Crear una nova migració per a la taula dinàmica _**role\_user**_ :

```text
$ php artisan make:migration create_role_user_table
```

Edita la classe _**CreateRoleUserTable**_ a la carpeta database / migrations:

```php
public function up()
{
    Schema::create('role_user', function (Blueprint $table) {
        $table->increments('id');
        $table->integer('role_id')->unsigned();
        $table->integer('user_id')->unsigned();
        $table->timestamps();  
    });
}
public function down()
{
    Schema::dropIfExists('role_user');
}
```

Ara cal generar una relació _**many-to-many**_ entre entitats _**User**_ i _**Role.**_

Obrim el model _**User.php**_ i afegim el següent mètode:

```php
public function roles()
{
    return $this
        ->belongsToMany('App\Role')
        ->withTimestamps();
}
```

Fem el mateix amb el model _**Role.php**_ :

```php
public function users()
{
    return $this
        ->belongsToMany('App\User')
        ->withTimestamps();
}
```

És moment de crear alguns _seeders_ i afegir rols i usuaris a la base de dades:

```text
$ php artisan make:seeder RoleTableSeeder
$ php artisan make:seeder UserTableSeeder
```

Editem la classe _**RoleTableSeeder**_ \(es troba dins de la carpeta: _database/seeds/_\) afegint el següent codi a mètode _**run**_ :

```php
use Illuminate\Database\Seeder;
use App\Role;
class RoleTableSeeder extends Seeder
{
    public function run()
    {
        $role = new Role();
        $role->name = 'admin';
        $role->description = 'Administrator';
        $role->save();
        $role = new Role();
        $role->name = 'user';
        $role->description = 'User';
        $role->save();
    }
}
```

Fem el mateix amb la classe _**UserTableSeeder**_ :

```php
use Illuminate\Database\Seeder;
use App\User;
use App\Role;
class UserTableSeeder extends Seeder
{
    public function run()
    {
        $role_user = Role::where('name', 'user')->first();
        $role_admin = Role::where('name', 'admin')->first();
        $user = new User();
        $user->name = 'User';
        $user->email = 'user@example.com';
        $user->password = bcrypt('secret');
        $user->save();
        $user->roles()->attach($role_user);
        $user = new User();
        $user->name = 'Admin';
        $user->email = 'admin@example.com';
        $user->password = bcrypt('secret');
        $user->save();
        $user->roles()->attach($role_admin);
     }
}
```

Editem la classe _**DatabaseSeeder**_ \(situada a la carpeta: database/seeds/\) afegint el següent codi a mètode _**run**_ :

```php
public function run()
{
    // La creación de datos de roles debe ejecutarse primero
    $this->call(RoleTableSeeder::class);
    // Los usuarios necesitarán los roles previamente generados
    $this->call(UserTableSeeder::class);
}
```

Ja queda poc ... està gairebé tot a punt. Ara és temps d'executar les migracions i generar el contingut:

> Cal assegurar-se tenir definides les variables d'entorn al nostre arxiu **.env** relatives a la base de dades que utilitzarem

```text
php artisan migrate:refresh --seed
```

Obrim el model **User.php** i afegim aquests tres  mètodes \(mireu a veure si interpreteu per a què serveixen \):

```php
public function authorizeRoles($roles)
{
    if ($this->hasAnyRole($roles)) {
        return true;
    }
    abort(401, 'Non authorized action.');
}
public function hasAnyRole($roles)
{
    if (is_array($roles)) {
        foreach ($roles as $role) {
            if ($this->hasRole($role)) {
                return true;
            }
        }
    } else {
        if ($this->hasRole($roles)) {
            return true;
        }
    }
    return false;
}
public function hasRole($role)
{
    if ($this->roles()->where('name', $role)->first()) {
        return true;
    }
    return false;
}
```

Obrim l'arxiu app/Http/Controllers/Auth/_**RegisterController.php**_i canviem  el mètode _**create\(\)**_ per definir per defecte el Role per als nous usuaris:

```php
use App\Role;
class RegisterController ...
protected function create(array $data)
{
    $user = User::create([
        'name' => $data['name'],
        'email' => $data['email'],
        'password' => Hash::make($data['password'])
    ]);
    $user
        ->roles()
        ->attach(Role::where('name', 'user')->first());
    return $user;
}
```

Finalment el pas final. Ara, tot el que es necessita és cridar al mètode _authorizeRoles_\(\) dins de les accions del controlador i passar l'array amb els rols d'usuari i el nivell d'accés que es desitgi.

```php
class HomeController extends Controller
{
    public function __construct()
    {
        $this->middleware('auth');
    }
    public function index(Request $request)
    {
        $request->user()->authorizeRoles(['user', 'admin']);
        return view('home');
    }
/*
    public function someAdminStuff(Request $request)
    {
        $request->user()->authorizeRoles(‘admin’);
        return view(‘some.view’);
    }
    */
}

```

Opcional:

Definir paràmetres de selecció per a la vista Home \(_resources/views/_\):

```php
@if(Auth::user()->hasRole('admin'))
    <div>Acceso como administrador</div>
@else
    <div>Acceso usuario</div>
@endif
You are logged in!
```

### Creació del Middleware

Laravel inclou un middleware que permet verificar si un usuari està autenticat quan accedeix a l'aplicació. Si l'usuari no ho estigués, el middleware ho redireccionaria a la pantalla de **login**. I per contra, si ho estigués, el middleware permetria l'accés a l'aplicació \(dashboard\).

```php
php artisan make:middleware CheckRole
```

Ara editem el middleware que hem acabat de crear

```php
namespace App\Http\Middleware;

use Closure;

class CheckRole
{
    /**
     * Handle an incoming request.
     *
     * @param \Illuminate\Http\Request $request
     * @param \Closure $next
     * @param $role
     * @return mixed
     */
    public function handle($request, Closure $next,$role)
    {
        if (! $request->user()->hasRole($role)) {
            return redirect('home');
        }
        return $next($request);
    }
}

```

Hem agregat una redirección estàndar cap a 'home' però igualment es podria fer:

```php
// opció 1
abort(403, “Non authorized”);
// Opció 2
return redirect(‘home’);
```

Enregistrem el middleware al kernel \(línia 17 de App\HTTP\Kernel\) :

```php
/**
     * The application's route middleware.
     *
     * These middleware may be assigned to groups or used individually.
     *
     * @var array
     */
    protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
        'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'password.confirm' => \Illuminate\Auth\Middleware\RequirePassword::class,
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
        'role' => \App\Http\Middleware\CheckRole::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
    ];
```

Per últim, podem fer servir el middleware ja directament a les rutes:

```php
Route::put('post/{id}', function ($id) {
    //
})->middleware('auth', 'role:admin');
```

## 6. Crear els controladors i les seves accions en funció de les rutes definides

Si ja tenim clar quina és l'estructura de l'aplicació i per tant les rutes associades, podem passar a aquest punt.

#### Controladors de recursos

En principi, començarem per tots els controladors associats a les entitats \(_resource controllers_\), amb artisan podem començar a crear:

```php
php artisan make:controller PropertyController -r
```

Es genera d'aquesta manera un controlador i els mètodes CRUD corresponents.

Al mateix temps, podem associar les rutes del recurs amb `Route::resource` :

```php
Route::resource([
    'properties'=>'PropertyController',
    'publications'=>'PublicationController'
        ]);
```

Això genera una llista de rutes i noms de ruta associade



| Verb | URI | Action | Route Name |
| :--- | :--- | :--- | :--- |
| GET | `/propeties` | index | properties.index |
| GET | `/properties/create` | create | properties.create |
| POST | `/properties` | store | properties.store |
| GET | `/properties/{property}` | show | properties.show |
| GET | `/properties/{property}/edit` | edit | properties.edit |
| PUT/PATCH | `/properties/{property}` | update | properties.update |
| DELETE | `/properties/{property}` | destroy | properties.destroy |



### Tractament dels controladors de recursos

Recordem que aquesta és tasca dels administradors de backend per tant, podem establir un middleware en el controlador \(línia 5\):

```php
class PropertyController extends Controller
{
    function __construct()
    {
        $this->middleware(['auth','role:admin']);
    }

    /**
     * Display a listing of the resource.
     *
     * @return Response
     */
    public function index()
    {
        $properties= Property::all();
       return view('properties.index',compact('properties'));
    }
```

Observem que en la ruta properties.index, volem mostrar totes les propietats, les quals passem a la vista. En **views** crearem la carpeta **properties** i allà dins crearem el fitxer _index.blade.php_

```php
@extends('app')

@section('content')
    <div class="col-lg-12">

        <h1 class="my-4">Properties</h1>
        <table class="table">
            <thead>
                <tr>
                    <th>Description</th>
                    <th>Price</th>
                    <th>Type</th>
                    <th></th>
                </tr>
            @foreach($properties as $property)
                <tr>
                    <td>{{$property->description}}</td>
                    <td>{{$property->price}} €</td>
                    <td>{{$property->type}}</td>
                    <td><a class="btn btn-primary" href="{{route('properties.edit',$property->id)}}">Edit</a></td>
                </tr>
            @endforeach

            </thead>
        </table>
    </div>

    @endsection
```

Quan editem un determinat item \($property\), es crida a la ruta _property.edit,_ que al mateix temps s'encarrega de portar a la vista d'edició l'item seleccionat:

```php
public function edit($id)
    {
        $property=Property::find($id);
        $users=User::all();
        return view('properties.edit',compact('property','users'));
    }
```

Veiem ara la vista _properties.edit_. Aquesta té com a característica un formulari que incorpora una **action** que accedeix al mètode _update_ del controlador, passant l'element que volem editar, amb els canvis  realitzats es torna al servidor a través del mètode PUT, tot tenit¡nt la protecció CSRF \(recordeu! forms segurs \)

```php
@extends('app')

@section('content')
    <div class="col-lg-12">

        <h1 class="my-4">Property edit</h1>
        <form action="{{route('properties.update',$property->id)}}" method="POST">
            @csrf
            @method('PUT')
            Description
            <br/>
            <input type="text" name="description" value="{{$property->description}}" class="form form-control">

            Price
            <br/>
            <input type="text" name="price" value="{{$property->price}}" class="form form-control">
            Owner
            <br/>
            <input class="list-group " list="owner_id" name="owner_id" value="{{$property->owner_id}}">
            @foreach($users as $user)
                <datalist id="owner_id">
                    <option value="{{$user->id}}">{{$user->email}}</option>
                </datalist>
            @endforeach
            <br/>
            <input type="submit" class="btn btn-primary" value="Save">
            <br/>
            <br/>
        </form>
    <br/>
    </div>

    @endsection

```

Ara caldria observar com actua el mètode _update_ de Properties:

```php
  public function update(Request $request, $id)
    {
        $property=Property::find($id);
        $property->update(['description'=>$request->description,
        'price'=>$request->price,
        'owner_id'=>$request->owner_id
        ]);

        return redirect()->route('properties.index');
    }
```

Pràcticament el que fa és modificar a través d'Eloquent **update** utilitzant el Request i tornar a la ruta anterior \(_properties.index_\).



## 7. Associar i crear les vistes a través de blade

En el punt anterior ja s'intueix com funciona tot, seguim el paradigma MVC

## 8. Tests

## 

