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

```text
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=inmo
DB_USERNAME=inmo
DB_PASSWORD=linuxlinux
```

## 3. Activar l'autenticació i sistema ui

Si utilitzem sistema d'autenticació, caldria configurar-lo:

```text
$ composer require laravel/ui
```

I activat amb el frontend que desitgem:

```text
$ php artisan ui bootstrap --auth
```

Si volguèssim definir-lo més tard, simplement acondicionem l'autenticació, controladors, proveïdors i vistes:

```text
$ pho artisan make:auth
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

```text
class ChangeTableProperties extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('table_properties', function (Blueprint $table) {
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
        Schema::dropIfExists('table_properties');
    }
}

```

Ara ja podríem executar la migració: `php artisan migrate`



## 5. Crear els "middlewares" que en siguin necessaris

Podem crear un middleware de control del rol d'usuari

## 6. Crear els controladors i les seves accions en funció de les rutes definides

## 8.Associar i crear les vistes a través de blade

## 9. Tests

## 

