# Proves unitàries en Laravel

Quan s'hereda un sistema, es recomana fer proves funcionals, si el que fem és començar de 0, aleshores ens hem d'inclinar per fer proves unitàries

## Proves funcionals

Busquem les funcions del sistema i el posem a prova per determinar si tal o qual funció es desenvolupa correctament.

## Proves unitàries

Són proves  que ataquen a una part espeífica del sistema \(un mètode normalment\) i són les proves que s'executen més ràpid.

Laravel inclou  un entorn de proves pre-configurat basat en **phpunit**  i amb alguns tests d'exemple.

## Testant el sistema d'autenticació

Suposem que tenim una app en la que ja hem preinstal·lat l'autenticació:

```php
php artisan make:auth
```

Preparem una prova unitària per al sistema login, concretament el mètode que ens porta cap a la _view_ **`login`**

Preparem el controlador, aquest l'ubicarem a **tests/Feature**, 

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



