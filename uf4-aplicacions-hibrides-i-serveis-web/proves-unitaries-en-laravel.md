# Testing en Laravel

Quan s'hereda un sistema, es recomana fer proves funcionals, si el que fem és començar de 0, aleshores ens hem d'inclinar per fer proves unitàries

## Proves funcionals (features)

Busquem les funcions del sistema i el posem a prova per determinar si tal o qual funció es desenvolupa correctament. Per exemple, procés d'autenticació o el de registre.

## Proves unitàries (unit)

Són proves que ataquen a una part espeífica del sistema (un mètode normalment) i són les proves que s'executen més ràpid.

Laravel inclou un entorn de proves pre-configurat basat en **phpunit** i amb alguns tests d'exemple. Es pot configurar a través del fitxer _phpunit.xml_

{% code lineNumbers="true" %}
```xml

<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="./vendor/phpunit/phpunit/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true"
>
<testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>
    </testsuites>
    <source>
        <include>
            <directory suffix=".php">./app</directory>
        </include>
    </source>
    <php>
        <env name="APP_ENV" value="testing"/>
        <env name="BCRYPT_ROUNDS" value="4"/>
        <env name="CACHE_DRIVER" value="array"/>
        <env name="DB_CONNECTION" value="mysql"/>
        <env name="DB_DATABASE" value="test_products"/>
        <env name="MAIL_MAILER" value="array"/>
        <env name="QUEUE_CONNECTION" value="sync"/>
        <env name="SESSION_DRIVER" value="array"/>
        <env name="TELESCOPE_ENABLED" value="false"/>
    </php>
</phpunit>


```
{% endcode %}

El fitxer inclou la testsuite en el carpeta _tests_, incorpora source com a codi del qual treballar en les proves, també incorpora un bootstrap o arrencada, amb el sistema de autoload de testing.

## Testant el sistema d'autenticació

Suposem que tenim una app en la que ja hem preinstal·lat l'autenticació:

```php
php artisan make:auth
```

o bé amb

```
php artisan breeze:install
```

Preparem una prova unitària per al sistema login, concretament el mètode que ens porta cap a la _view_ **`login`**

Els test corren de forma paral·lela a l'aplicació, tenen el seu propi espai de noms Tests\\

Preparem el controlador, aquest l'ubicarem a **tests/Feature**,

```php
php artisan make:test Auth\LoginControllerTest
```

El comando genera una extensió de TestCase com podeu comprovar:

```php
namespace Tests\Feature\Http\Controllers\Auth;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;

class LoginControllerTest extends TestCase
{
    /**
     * A basic feature test example.
     *
     * @return void
     */
    public function test_example()
    {
        $response = $this->get('/');

        $response->assertStatus(200);
    }
}
```

En aquest cas, provem d'accedir a la homepage de l'aplicació.

{% hint style="info" %}
Les proves o mètodes tenen tres parts:

1. Preparació de l'entorn
2. Acció, és a dir generar una response
3. Checking o comprovació amb **assert**
{% endhint %}

**El nom de la nostra prova automatitzada ha de ser el més descriptiu possible i començar per test\_.**&#x20;

En el nostre cas, volem assegurar-nos que quan visitem `/login`, es mostra el formulari d'inici de sessió.

<pre class="language-php"><code class="lang-php"><strong>Prova::
</strong><strong>GET /login ----> view ('auth.login')
</strong></code></pre>

I la funció quedaria de la següent manera, sempre començant per test\_:

```php
/**
     * A Test login
     *
     * @return void
     */
    public function test_login_displays_the_login_form()
    {
        //objectiu
        $response = $this->get(route('login'));
        //comprovació
        $response->assertStatus(200);
        $response->assertViewIs('auth.login');
    }
```

I per provar-la (únicament aquesta):

```php
vendor/bin/phpunit 
tests/Feature/Http/Controllers/Auth/LoginControllerTest.php

Time: 00:00.250, Memory: 20.00 MB

OK (1 test, 2 assertions)
```

Si tot ha anat bé, sortirà OK i en verd.

Una segona prova consisteix en veure si la resposta POST del login s'ajusta a lo esperat:

```php
/** @test */
public function test_login_displays_validation_errors()
{
    $response = $this->post(route('login'), []);
    $response->assertStatus(302);
    $response->assertSessionHasErrors('email');
}
```

Aquí provem que passa si no enviem dades a través de post (array de línia 4). Si dona errors de validació i ens redirigeix (**302**), en una altre línia comprovem si hi ha errors en el camp **'email'.**

**El resultat seria el següent:**

```php
vendor/bin/phpunit tests/Feature/Http/Controllers/Auth/LoginControllerTest.php
PHPUnit 9.5.2 by Sebastian Bergmann and contributors.

..                                                                  2 / 2 (100%)

Time: 00:00.253, Memory: 22.00 MB

OK (2 tests, 5 assertions)
```

Un pas més seria comprovar si amb un usuari de prova (fake) podem accedir i redirigir un cop autenticat:

```php
 /** @test */
    public function test_login_authenticates_and_redirects_user()
    {
        $user = User::factory()->create();
        $response = $this->post(route('login'), [
            'email' => $user->email,
            'password' => 'password'
        ]);

        $response->assertRedirect(route('dashboard'));
        $this->assertAuthenticatedAs($user);
    }
```

Aquesta prova és molt més completa. Hem utilitzat el helper factory (**database/factoriew/UserFactory.php**) que crea (de forma persistent a la base de dades) un usuari.

Podem crear-nos a través del factory() més definicions de models si són necessaris

```php
namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class UserFactory extends Factory
{
    /**
     * The name of the factory's corresponding model.
     *
     * @var string
     */
    protected $model = User::class;

    /**
     * Define the model's default state.
     *
     * @return array
     */
    public function definition()
    {
        return [
            'name' => $this->faker->name,
            'email' => $this->faker->unique()->safeEmail,
            'email_verified_at' => now(),
            'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
            'remember_token' => Str::random(10),
        ];
    }
}
```

Com a exemple, també us deixo com es faria la prova de registre d'usuari, incorpora l'acció de visualitzar el formulari i la creació de l'usuari:

{% code lineNumbers="true" %}
```php
use App\Providers\RouteServiceProvider;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
 
class RegistrationTest extends TestCase
{
    use RefreshDatabase;
 
    public function test_registration_screen_can_be_rendered()
    {
        $response = $this->get('/register');
 
        $response->assertStatus(200);
    }
 
    public function test_new_users_can_register()
    {
        $response = $this->post('/register', [
            'name' => 'Test User',
            'email' => 'test@example.com',
            'password' => 'password',
            'password_confirmation' => 'password',
        ]);
 
        $this->assertAuthenticated();
        $response->assertRedirect(RouteServiceProvider::HOME);
    }
}
```
{% endcode %}

{% hint style="info" %}
[Laravel Assertions](https://laravel.com/docs/9.x/http-tests#available-assertions)
{% endhint %}

Recordem que les assertions en general van sobre l'objecte TestCase ($this), però les que són específiques de les respostes, com ara la línia 26, van sobre l'objecte $response.

En general si fem servir el trait RefreshDatabase, vol dir que s'utilitza una base de dades paral·lela que no afecta a la normal, per tant si volem buidar seria millor amb un **`php artisan migrate:fresh`** sempre i quan sigui una base de dades separada, per tant podem editar el phpunit.xml i canviar la suite per que permeti per exemple una base de dades en sqlite.

#### Bases de dades de testing

{% code lineNumbers="true" %}
```php
<php>
    <env name="APP_ENV" value="testing"/>
    <env name="BCRYPT_ROUNDS" value="4"/>
    <env name="CACHE_DRIVER" value="array"/>
    <!-- <env name="DB_CONNECTION" value="sqlite"/> -->
    <!-- <env name="DB_DATABASE" value="database/testing.sqlite"/> -->
    <env name="MAIL_MAILER" value="array"/>
    <env name="QUEUE_CONNECTION" value="sync"/>
    <env name="SESSION_DRIVER" value="array"/>
    <env name="TELESCOPE_ENABLED" value="false"/>
</php>
```
{% endcode %}

Caldria doncs, descomentar la línia 5 i 6 i crear el fitxer `testing.sqlite` dins el directori **database**, i definir en el fitxer .env que es fa servir una DATABASE de testing, les classes que treballin amb aquesta base de dades, utilitzaran el trait `use RefreshDatabase;`(línia 4)

```sh
.....
DB_DATABASE=testing
DB_USERNAME=root
....
```

{% code lineNumbers="true" %}
```php

class IndexTest extends TestCase
{
    use RefreshDatabase;
    /**
     @test
     */
    public function test_list_products(): void
    {
        $product=Product::factory()->create();
        $this->get(route('products.index'))
            ->assertStatus(200)
            ->assertSee($product->name);
    }
}

```
{% endcode %}

