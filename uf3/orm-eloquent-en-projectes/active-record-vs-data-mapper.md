# Active Record vs Data Mapper

Tant l'Active Record com el Data Mapper són patrons de disseny que es poden utilitzar en el desenvolupament d'aplicacions amb bases de dades.

### Active Record

És un patró de disseny que vincula les classes del model a les taules de la base de dades, de manera que una instància d'una classe correspon a una fila de la taula de la base de dades. Aquest patró permet accedir als elements de la base de dades directament des de l'objecte.

* **Avantatges:**
  * És senzill d'utilitzar i comprendre, ja que segueix una estructura molt semblant a la base de dades.
  * Redueix la quantitat de codi que cal escriure per a la gestió de la base de dades, ja que les operacions CRUD (Create, Read, Update, Delete) es realitzen directament a través de l'objecte.
* **Desavantatges:**
  * **Acoblament fort** entre el model i la base de dades, la qual cosa pot ser un problema en aplicacions complexes o amb esquemes de bases de dades molt dinàmics.
  * No és tan flexible en termes d'adaptar-se a canvis en l'esquema de la base de dades.

Exemple d'Active Record en PHP:



```php
class User extends ActiveRecord {
    protected $table = 'users';
    // Propietats i mètodes per interactuar amb la taula 'users'
}

// Fer una consulta i recuperar un usuari específic
$user = User::find(1);
$user->name = 'Pep Lòpez';
$user->save(); // Actualitzar la informació a la base de dades

```
