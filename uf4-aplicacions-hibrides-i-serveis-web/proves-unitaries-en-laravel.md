# Proves unitàries en Laravel

Quan s'hereda un sistema, es recomana fer proves funcionals, si el que fem és començar de 0, aleshores ens hem d'inclinar per fer proves unitàries

## Proves funcionals

Busquem les funcions del sistema i el posem a prova per determinar si tal o qual funció es desenvolupa correctament.

## Proves unitàries

Són proves  que ataquen a una part espeífica del sistema (un mètode normalment) i són les proves que s'executen més ràpid.

Laravel inclou  un entorn de proves pre-configurat basat en **phpunit**  i amb alguns tests d'exemple.

## Testant el sistema d'autenticació

Suposem que tenim una app en la que ja hem preinstal·lat l'autenticació:

```php
php artisan make:auth
```

Preparem una prova unitària per al sistema login, concretament el mètode que ens porta cap a la _view_ **`login`**

Preparem el controlador, aquest l'ubicarem a **tests/Feature**,&#x20;

```php
php artisan make:test Http/Controllers/Auth/LoginControllerTest
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

**El nom de la nostra prova automatitzada ha de ser el més descriptiu possible. **

En el nostre cas, volem assegurar-nos que quan visitem  `/login`, es mostra el formulari d'inici de sessió.

```php
GET /login ----> view ('auth.login')
```

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

I per provar-la:

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

