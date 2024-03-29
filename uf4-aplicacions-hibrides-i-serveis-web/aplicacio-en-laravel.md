---
description: Unes pautes per a la creació d'aplicacions a Laravel (v.7x i posteriors)
---

# Crear una aplicació en Laravel

En el nostre exemple, crearem una app de publicació d'anuncis d'habitatges de lloguer/venda.

A continuació us indiquem quins passos cal seguir per realitzar una aplicació en Laravel:

El diagrama d'entitats seria el següent:

![](../.gitbook/assets/rentit.png)

## 1.Accions de navegació

Ens permet entendre les diferents accions de navegació del nostre projecte, seguim esquema tradicional d'aplicació web en escriptori.

| EXEMPLE       | Tasca                 |
| ------------- | --------------------- |
| USUARI (ROLE) | Login                 |
|               | Registre              |
|               | Propietat /Registrar  |
|               | Propietat /Editar     |
|               | Propietat / Eliminar  |
|               | Publicació / Crear    |
|               | Publicació / Editar   |
|               | Publicació / Eliminar |
| ADMIN (ROLE)  | Backend Users         |
|               | Backend Properties    |
|               | Backend Publications  |

## 2.Crear i modificar l'entorn .env de l'app

Modificar dades d'accés a la base de dades i usuari d'accés a la base de dades.

```bash
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=inmo
DB_USERNAME=inmo
DB_PASSWORD=xxxxxxxx
```

## 3. Activar l'autenticació i sistema _ui_

### _3.1 Versió laravel/ui_

Si utilitzem sistema d'autenticació d'usuaris, caldria configurar-lo:

```bash
$ composer require laravel/ui
```

I activat amb el frontend que desitgem (bootstrap, vue, react...)

```bash
$ php artisan ui bootstrap --auth
```

Si volguèssim definir-lo més tard, simplement acondicionem l'autenticació, controladors, proveïdors i vistes:

```bash
$ php artisan ui:auth
```

### 3.2 Versió laravel/breeze

Caldria afegir la dependència laravel/breeze

Instal·lar a través de artisan

Fer les migracions corresponents a usuaris, contrasenyes i passwords

Executar vite.js per obtenir les vistes corresponents

## 4. Definir models i relacions associades i fer les migracions

**Model**

Podem fer servir els comandos artisan per crear els models. Un cop creats, cal acabar-los d'implementar.

```
php artisan make:model Property -m
```

**Taula**

Després de definir els models i les seves migracions (-m), podem continuar modificant les migracions.

```
php artisan make:migrations create_table_properties
```

Si migreu ( `php artisan migrate` ), ja tindreu creades les taules a la vostra base de dades.

Ara caldrà comprovar com queda l'script de migració:

Crearem un altre script de migració:

```
php artisan make:migrations change_table_properties
```

El modifiquem afegint les funcions **up**() i **down**() (crear i eliminar taula). Aneu amb compte amb els tipus de dades relacionats **id** de _users_ i **users\_id** de _properties_

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
         // també es pot fer servir  en L8.x
         // $table->id(); 
            $table->bigIncrements('id');
            $table->decimal('price',8,2);
            $table->string('description');
        // també es pot fer servir en L8.x
        //  $table->unsignedBigInteger('user_id')
            $table->bigInteger('user_id')->unsigned();
            $table->timestamps();
            $table->foreign('user_id')->references('id')->on('users');
       // en Laravel 8.x:
       // $table->foreignId('user_id')->constrained()->onDelete('cascade');

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

![Esquema ER](../.gitbook/assets/inmo.png)

En aquest cas veiem també les relacions que es poden crear, són vàries:

user --->properties

property--->user

user --->contacts

contact--->user

........

recordem-ho per més endavant.



Segons l'esquema ER, també hem de poder crear els models necessaris amb les seves corresponents migracions:

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
            $table->foreignId('interested_id')->constrained();
            $table->foreignId('publication_id')->constrained();
            $table->timestamps();
        });
```

Un cop fets els fitxers de migració completarem la migració:

```php
php artisan migrate
```

#### Completar mètodes en models

Ara queda acabar de copiar les relacions i característiques $fillable, etc. en Eloquent, sempre fixant-nos en l'esquema ER anterior, per exemple, entre User i Property (1:M):

```php
class Property extends Model
{
    protected $fillable=['description','price','type','owner_id'];
    
    //relació inversa
    public function user(){
        return $this->belongsTo('App\User','owner_id');
    }
}
```

i User :

```php
  ........
    //relació directa
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

Fixem-nos en el mètode **down()**, efectivament, elimina el camp _photo_. Per últim, només queda que migrar : `php artisan migrate`

## 5. Crear els "_middlewares_" que en siguin necessaris. Middleware de control d'accès.

Podem crear un middleware de _control del rol_ d'usuari, recordem que un middleware actua com un filtre.

![Els middlewares actuen com a filtres dels REQUESTs abans de processar la sol·licitud](../.gitbook/assets/dibujo-sin-titulo.png)

Modificarem el model User, afegirem el camp de 'roles', i l'actualitzarem, Després afegirem els mètodes per manipular els rols al mateix model.

Pas a pas, caldrà crear una migració per afegir el camp roles al model - taula User, in cop migrat, afegim els mètodes al model.

### Rols d'usuari

Sino tenim instal·lat l'auntenticació:

```
$ php artisan ui bootstrap --auth
```

Crear el model **Role** amb la seva respectiva migració (paràmetre -m):

```
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

```
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

```
$ php artisan make:seeder RoleSeeder
$ php artisan make:seeder UserSeeder
```

Editem la classe _**RoleSeeder**_ (es troba dins de la carpeta:\_ database/seeds/\_) afegint el següent codi a mètode _**run**_ :

```php
use Illuminate\Database\Seeder;
use App\Role;
class RoleSeeder extends Seeder
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

Fem el mateix amb la classe _**UserSeeder**_ :

```php
use Illuminate\Database\Seeder;
use App\User;
use App\Role;
class UserSeeder extends Seeder
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

Editem la classe _**DatabaseSeeder**_ (situada a la carpeta: database/seeds/) afegint el següent codi a mètode _**run**_ :

```php
public function run()
{
    // La creación de datos de roles debe ejecutarse primero
    $this->call(RoleSeeder::class);
    // Los usuarios necesitarán los roles previamente generados
    $this->call(UserSeeder::class);
}
```

Ja queda poc ... està gairebé tot a punt. Ara és temps d'executar les migracions i generar el contingut:

> Cal assegurar-se tenir definides les variables d'entorn al nostre arxiu **.env** relatives a la base de dades que utilitzarem

```
php artisan migrate:refresh --seed
o bé
php artisan db:seed
(aquesta no fa migració) 
```

Obrim el model **User.php** i afegim aquests tres mètodes (mireu a veure si interpreteu per a què serveixen ):

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

Obrim l'arxiu app/Http/Controllers/Auth/\_**RegisterController.php**\_i canviem el mètode _**create()**_ per definir per defecte el Role per als nous usuaris:

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

Finalment el pas final. Ara, tot el que es necessita és cridar al mètode _authorizeRoles_() dins de les accions del controlador i passar l'array amb els rols d'usuari i el nivell d'accés que es desitgi.

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

**Opcional**:

Definir paràmetres de selecció per a la vista Home (_resources/views/_):

```php
@if(Auth::user()->hasRole('admin'))
    <div>Acces com administrador</div>
@else
    <div>Acces usuari</div>
@endif
You are logged in!
```

### Creació del Middleware

Laravel inclou un middleware que permet verificar si un usuari està autenticat quan accedeix a l'aplicació. Si l'usuari no ho estigués, el middleware ho redireccionaria a la pantalla de **login**. I per contra, si ho estigués, el middleware permetria l'accés a l'aplicació (dashboard).

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

Enregistrem el middleware al kernel (línia 17 de App\HTTP\Kernel) :

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

En principi, començarem per tots els controladors associats a les entitats (_resource controllers_), amb artisan podem començar a crear:

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

| Verb      | URI                           | Action  | Route Name         |
| --------- | ----------------------------- | ------- | ------------------ |
| GET       | `/propeties`                  | index   | properties.index   |
| GET       | `/properties/create`          | create  | properties.create  |
| POST      | `/properties`                 | store   | properties.store   |
| GET       | `/properties/{property}`      | show    | properties.show    |
| GET       | `/properties/{property}/edit` | edit    | properties.edit    |
| PUT/PATCH | `/properties/{property}`      | update  | properties.update  |
| DELETE    | `/properties/{property}`      | destroy | properties.destroy |

### Tractament dels controladors de recursos

Recordem que aquesta és tasca dels administradors de backend per tant, podem establir un middleware en el controlador (línia 5):

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

Observem que en la ruta **properties.index**, volem mostrar totes les propietats, les quals passem a la vista. En **views** crearem la carpeta **properties** i allà dins crearem el fitxer _index.blade.php_

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

Quan editem un determinat item ($property), es crida a la ruta \_property.edit, \_que al mateix temps s'encarrega de portar a la vista d'edició l'item seleccionat:

```php
public function edit($id)
    {
        $property=Property::find($id);
        $users=User::all();
        return view('properties.edit',compact('property','users'));
    }
```

Veiem ara la vista _properties.edit_. Aquesta té com a característica un formulari que incorpora una **action** que accedeix al mètode _update_ del controlador, passant l'element que volem editar, amb els canvis realitzats es torna al servidor a través del mètode PUT, tot tenit¡nt la protecció CSRF (recordeu! forms segurs )

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

Pràcticament el que fa és modificar a través d'Eloquent **update** utilitzant el Request i tornar a la ruta anterior (_properties.index_).

## 7. Associar i crear les vistes a través de blade

En el punt anterior ja s'intueix com funciona tot, seguim el paradigma MVC.

Suposem l'accés **backend** al nostre exemple de properties, hem vist les accions generades al recurs:

| Acció   | Efecte                                                                         |
| ------- | ------------------------------------------------------------------------------ |
| index   | Llistat de tots els recursos                                                   |
| create  | Renderitza formulari de creació del recurs                                     |
| store   | Acció d'emmagatzemar en la base de dades, cridada des del formulari **create** |
| show    | Mostrar el recurs seleccionat                                                  |
| edit    | Renderitza formulari per modificar recurs                                      |
| update  | Acció d'actualitzar dades del recurs, cridada des del formulari **edit**       |
| destroy | Acció d'eliminar el recurs seleccionat                                         |

Totes les accions finals (store, update, destroy) han de retornar al catàleg del recurs:

```php
 return redirect()->route('properties.index');
```

### Exemple acció edició/update

Sent una acció definida per al rol d'administrador, fixem en el controlador que només si estàs autenticat i tens el rol admin podràs accedir-hi:

```php
namespace App\Http\Controllers;
use App\Property;
use App\User;
use Illuminate\Http\Request;
use Illuminate\Http\Response;

class PropertyController extends Controller
{
    function __construct()
    {
        $this->middleware(['auth','role:admin']);
    }
```

Comprovem el controlador, i agafem l'acció de modificar una propietat, des de la pantalla de catàleg de propietats:

![Catàleg de propietats](../.gitbook/assets/captura-de-pantalla-2020-04-02-a-les-21.10.52.png)

Fixem-nos, que, si premem **edit** provoquem la visualització d'un formulari amb valors precarregats del registre en qüestió. Primer, la crida al controlador@edit:

```php
 public function edit($id)
    {
        $property=Property::find($id);
        $users=User::all();
        return view('properties.edit',compact('property','users'));
    }
```

Se selecciona la propietat (línia 3), agafem la llista de possibles usuaris propietaris (línia 4 ) i retornem la vista de formulari 'properties.edit' (carpeta _resources/views/properties_ i fitxer _edit.blade.php)_

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
```

Aquest formulari envia a través de mètode post però emmascara el mètode PUT, típic mètode HTTP d'actualització de recursos.

Com a anècdota, utilitzem un \<datalist> per visualitzar el llistat de propietaris disponibles (línies 20 a 24).

L'acció del formulari és el mètode\*\* PropertyController@update\*\*, :

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

#### _Característiques_:

* En comptes de passar $id, podem injectar directament l'objecte **Property.**
* Si el model ja existeix, com és el cas, podem utilitzar el mètode **save(). De manera que només actualitza aquells valors modificats. Mireu el mètode `isDirty()`** en la documentació per a més informació.

```php
public function update(Request $request, Property $property)
    {
        $property->description=$request->description;
        $property->price=$request->price;
        $property->owner_id=$request->owner_id;
        //$property->update(['description'=>$request->description,
        //'price'=>$request->price,
        //'owner_id'=>$request->owner_id
        //]);
        $property->save();
        return redirect()->route('properties.index');
    }
```

Aquest sistema d'implementació seria similar en la resta d'accions.

### Formularis i validacions

Des de HTML és possible indicar al sistema si un determinat camp és requerit o no, a l'estil de si a la base de dades no permet una dada nul·la. Estem parlant de l'atribut `required`.

Des de la classe Request també podem aplicar els validadors des dels controladors. Fixem-nos en les línies 3 a 6, es tracta d'una validació prèvia a la creació d'un usuari predeterminat en el backend d'administració.

```php
 public function store(Request $request)
    {
       $request->validate([
           'name'=>'required|unique:users',
           'email'=>'required|unique:users',
       ]);
        $role_user = Role::where('name', 'user')->first();
        $user=User::create([
            'name'=>$request->name,
            'email'=>$request->email,
            'password'=>Hash::make('secret'),
        ]);
        //by default role_user
        $user->roles()->attach($role_user);
        return redirect()->route('users.index');
    }
```

Una altra manera de fer-ho (i recomanable ) és crear una classe específica per poder validar requests als formularis específics:

```php
php artisan make:request StoreUserRequest
```

I reescrivim les regles aquí en App\HTTP\Requests, posant **authorize()** a true i posant a **rules()** les regles validator

```php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StoreUserRequest extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return true;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'name'=>'required|unique:users',
            'email'=>'required|unique:users',
        ];
    }
}
```

Modifiquem ara el mètode **store()** canviant el paràmetre Request per la nova classe.

```php
 public function store(StoreUserRequest $request)
    {

        $role_user = Role::where('name', 'user')->first();
        $user=User::create([
            'name'=>$request->name,
            'email'=>$request->email,
            'password'=>Hash::make('secret'),
        ]);
        //by default role_user
        $user->roles()->attach($role_user);
        return redirect()->route('users.index');
    }
```

Bé, espero que us funcioni!

### Emmgatzematge (storage)

Suposem que volem afegir una foto a cadascuna de les nostres entitats (properties). Laravel ens ofereix la possibilitat d'afegir un espai públic a través del seu _filesystem_.

Des del fitxer _config/filesystems.php_, es determinen tres tipus d'emmagatzematge disk, que són\*\* local, public i s3,\*\* nosaltre utilitzarem a la pràctica el 'public', això permet accessos des de `http://app/arxiu`

Per poder-lo utilitzar és necessari establir un link o accés directe, d'aquesta manera:

```php
php artisan storage:link
```

#### PropertyController@store

```php
public function store(Request $request)
    {
        $path=$request->file('photo')->store('photos','public');
        Property::create(['description'=>$request->description,
                        'price'=>$request->price,
                        'owner_id'=>$request->owner_id,
                        'photo'=>$path
            ]);
        return redirect()->route('properties.index');
    }
```

Extreiem el PATH del fitxer (en **public** ) per poder desar a la base de dades (línia 7). Òbviament això està relacionat amb el formulari de creació de l'entitat:

```php
@extends('app')

@section('content')
    <div class="col-lg-12">

        <h1 class="my-4">New Property</h1>
        <form action="{{route('properties.store')}}" method="POST" enctype="multipart/form-data">
            @csrf
            Description
        <br/>
            <input type="text" name="description" value="" class="form form-control" required>
            Price
            <br/>
            <input type="text" name="price" value="" class="form form-control" required>
            Owner
            <br/>
            <input class="list-group " list="owner_id" name="owner_id">
            <datalist id="owner_id">
            @foreach($users as $user)

                    <option value="{{$user->id}}">{{$user->email}}</option>
            @endforeach
            </datalist>
            <br/>
            <input type="file" name="photo">

            <br/>
            <br/>
            <input type="submit" class="btn btn-primary" value="Save">
            <br/>
            <br/>
        </form>
    </br>
    </div>

    @endsection
```

Observem:

* el formulari té una encriptació per suportar multipart/form-data
* afegim control tipus **file** amb name="photo"

Quedaran els fitxers emmagatzemats a **storage/app/public**

Veiem com queda per exemple, la vista del mètode show d'aquesta entitat:

```php
@extends('app')

@section('content')
    <div class="col-lg-12">

        <h1 class="my-4"> Property: {{$property->id}}</h1>
        <h2 class="my-5">{{$property->description}}</h2>
        <p><strong>Price: </strong>{{$property->price}} €</p>
        <p><strong>Owner email: </strong>{{$email}}</p>
        <br/>
        <br/>
        @if($property->photo!=null)<img src="{{asset('storage/'.$property->photo)}}" width="150px">@endif
        <br/>
        <br/>
    </br>
        <a class="btn btn-info" href="{{route('properties.index')}}">Continue</a>
        <br/>
        <br/>
    </div>

    @endsection
```

Accedim a ella a través d'un asset prefixant el directori storage: `{{asset('storage/'.$property->photo)}}`

## 8. Tests

Laravel està orientat al testing, per tant ja trobem instal·lat **`phpunit`**. A més tota la configuració de l'entorn de proves es troba\*\* phpunit.xm\*\*l. Si bé podem crear un fitxer `.env.testing` amb els paràmetres de prova, per exemple, la base de dades ha de ser diferent, podem treballar pot ser amb **`sqlite`**.

```php
    APP_NAME=Laravel
    APP_ENV=testing
    APP_KEY=base64:5CpEFQ9USR543dbJUsT3araoSSyxuN8NF92gCJJXpk8=
    APP_DEBUG=true
    APP_URL=http://localhost

    LOG_CHANNEL=stack

    DB_CONNECTION=sqlite
    DB_DATABASE=/absolute/path/to/test.sqlite
```

Per crear una prova podem fer servir el comando artisan

Un cop generada la prova podem utilitzar phpunit o bé el comando **`artisan test`**

### Redacció dels casos de prova

Què és el que volem provar?

### Casos de prova finals o endpoints

Es crea la unitat, per exemple provar propietats i es codifiquen tots els casos de prova anteriors p.e. testCreateProperty())

## Apèndix - Mail: configuració i ús amb Gmail

Podem associar un compte de correu electrònic tipus gmail a la nostra aplicació. Per començar hem de treballar al fitxer **.env.**

Aquestes serien les línies que ens interessen:

```php
MAIL_MAILER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PORT=465
MAIL_USERNAME=zzzzzz@gmail.com
MAIL_PASSWORD=lsnfvzbhwglldfefdfpouxffx
MAIL_ENCRYPTION=ssl
MAIL_FROM_ADDRESS=zzzzzzz@gmail.com
MAIL_FROM_NAME="${APP_NAME}"
```

Com es pot observar el recipient mail\_host es defineix com smtp.gmail.com amb una encriptació **SSL** cal habilitar el port 465.

### Configuració del compte de correu

Cada vegada que fem servir el nostre email a través d'una aplicació diferent al propi gmail, és necessària una autorització. Aquesta s'aconsegueix seguint els passos següents:

Habilitar accés a aplicacions no segures: [https://myaccount.google.com/security](https://myaccount.google.com/security).

Accedim a contrasenyes d'aplicació i en generem una de nova

![Contrasenyes d'aplicació](../.gitbook/assets/captura-de-pantalla-2020-05-08-a-les-19.26.09.png)

![Indiquem l'aplicació i dispositu i generem](../.gitbook/assets/captura-de-pantalla-2020-05-08-a-les-19.29.57.png)

Un cop generada la clau, la traslladarem a la nostra aplicació:

![Contrasenya generada](../.gitbook/assets/captura-de-pantalla-2020-05-08-a-les-19.32.41.png)

```php
MAIL_PASSWORD=tszabtgduevapwzg
```

### Configuració en laravel

Ara modifiquem el fitxer **`config/mail.php`**

```php
'mailers' => [
        'smtp' => [
            'transport' => 'smtp',
            'host' => env('MAIL_HOST', 'smtp.gmail.com'),
            'port' => env('MAIL_PORT', 465),
            'encryption' => env('MAIL_ENCRYPTION', 'ssl'),
            'username' => env('MAIL_USERNAME'),
            'password' => env('MAIL_PASSWORD'),
        ],

        .......
    /*
    |--------------------------------------------------------------------------
    | Global "From" Address
    |--------------------------------------------------------------------------
    |
    | You may wish for all e-mails sent by your application to be sent from
    | the same address. Here, you may specify a name and address that is
    | used globally for all e-mails that are sent by your application.
    |
    */

    'from' => [
        'address' => env('MAIL_FROM_ADDRESS', 'zzzzzz@gmail.com'),
        'name' => env('MAIL_FROM_NAME', 'Inmo'),
    ],
```

### Mail

Les API mail de Laravel es basen en les llibreries Guzzle HTTP. Per tant instal·larem la llibreria:

```php
composer require guzzlehttp/guzzle
```

En Laravel cada tipus de mail es representa amb la classe _Mailable_. Aquestes classes s'emmagatzemen a **`app/mail`**. Podem generar el nostre model amb **artisan**.

```php
php artisan make:mail ContactRequired
```

En la classe generada farem una sèrie de canvis. En el **construct** li passem dades de les quals omplir dades al nostre mail. I en el mètode **build()**, construim els elements de nostre mail, que es basarà en una plantilla _blade_

```php
class ContactRequired extends Mailable
{
    use Queueable, SerializesModels;
    public $publication;
    /**
     * Create a new message instance.
     *
     * @return void
     */
    public function __construct(Publication $publication)
    {
        $this->publication=$publication;
    }

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {

        return $this->from('zzzzzz@gmail.com')->view('mailcontact');
    }
}
```

La vista la generem a **resources/views** :

```php
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Contact Request</title>
</head>
<body>

    <p>Hello!</p>
    <p>This is your contact required!:</p>
    <p><strong>{{$publication->publisher->email}}</strong></p>
    <br/>
    <p>See you soon!!</p>

<h3>RentaHouse: Sales & Rents</h3>
<h5>Castelldefels</h5>

</body>
</html>
```

Un cop creada la plantilla, podem usar la classe Mail des del controlador. Fixem-nos en els mètodes **`::to()`** i **`->send()`**

```php
public function reqContact(Publication $publication){

        $contact=Contact::create([
            'interested_id'=>Auth::user()->id,
            'publication_id'=>$publication->id,
        ]);
        Mail::to(Auth::user()->email)
            ->send(new ContactRequired($publication));

        return view('contactRequest',compact('publication'))->with('success','We have sent you a message!');
    }
```
