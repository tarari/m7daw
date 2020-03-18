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

## 4. Definir models i relacions associades

Podem fer servir els comandos artisan per crear els models. Un cop creats, cal acabar-los d'implementar.



```text
php artisan make:model Property -m
```

Després de definir els models i les seves migracions \(-m\), podem continuar modificant les migracions.

```text
php artisan make:migrations create_table_properties
```

Si migreu \( `php artisan migrate` \), ja tindreu creades les taules a la vostra base de dades.

Ara caldrà comprovar com queda l'script de migració: Modifiqueu el de l'última data: I en _**up\(\)**_ definim els camps que ens falten \(línies 12 - 15\):

```text
class CreateTableProperties extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('table_properties', function (Blueprint $table) {
            $table->id();
            $table->decimal('price',8,2);
            $table->string('description');
            $table->integer('user_id')->unsigned();
            $table->foreign('user_id')->references('id')->on('users');
            $table->timestamps()
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



## 5. Realitzar les migracions a la base de dades 

## 6. Crear els "middlewares" que en siguin necessaris

## 7. Crear els controladors i les seves accions en funció de les rutes definides

## 8.Associar i crear les vistes a través de blade

## 9. Tests

## 

