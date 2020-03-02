# API RestFUL en Laravel

Aquest apartat només mostrarà com crear un endpoint i una petita explicació de com fer-lo servir  amb alguns verbs **HTTP**.  
La part de crear un projecte **Laravel**, migracions i models ho veurem molt ràpidament, ja que se suposa que està vist en la UF3, i crearem només una taula, molt senzilla, per no complicar-nos , però que potser vegem en detall més endavant en un altre apartat.  
Si ja tens un projecte funcionant  en Laravel, ens podem   saltar la següent secció.

#### **Requisits:**

* PHP ≥7.1
* Composer: Per instal·lar Laravel.
* Una base de dades creada. En aquest apartat farem servir Mysql i la BD es dirà **PROVESAPI** .

El com manegen la  base de dades ho deixarem com a personal. La majoria de les operacions les farem amb Laravel.

#### **Instal·lant Composer:**

Tenim  [ el lloc oficial.](https://getcomposer.org/)

#### **Instal·lant Laravel:**

```text
composer global require "laravel/installer”
```

\(  triga una mica.\)

Un cop estigui a punt la instal·lació podem crear el nostre projecte en Laravel!

```text
laravel new miApiRest
```

Sense entrar en molt detall, el que fa la comanda és crear una carpeta **miApiRest** , instal·lar les dependències de Laravel dins d'aquesta carpeta, preparar l'arxiu **.env** \(que farem servir per connectar-nos a la nostra BD\), i generar la _**application key**_ per desenvolupar en la nostra màquina **.**

**Connectant a la base de dades:**

Aquesta part és easy peasy. L'únic que hem de fer és modificar el fitxer **.env** que es troba a l'arrel del nostre projecte **miApiRest**.

L'omplirm amb les dades de la  BD:

```text
[...] DB_CONNECTION = mysql 
DB_HOST = 127.0.0.1 
DB_PORT = 3306 
DB_DATABASE = PROVESAPI 
DB_USERNAME = user 
DB_PASSWORD = ********* [...]
```

**Migracions:**

Per no estendre'ns gaire, crearem una taula ultra senzilla anomenada **Pokémon** amb els camps **id** i **nom** .  
La gràcia de les migracions és que podem  tenir la base de dades dins del control de versions com _git_.

Amb el terminal que vulguis entrem  a la carpeta arrel del nostre projecte i executem la següent comanda:

```text
$ php artisan make: migració create_table_pokemons
 Migració creada: 2018_05_08_044418_create_table_pokemons
```

Revisem el que acaba de crear-se:  
La recentment creada migració hauria d'estar en **base de dades / migrations.**

Juntament amb **2020\_02\_08\_044418\_create\_table\_pokemons.php** , si estem en un projecte nou, haurien d'haver 2 migracions més. Aquestes les crea Laravel per defecte per mantenir usuaris. Les esborraré en aquest cas ja que no les faré servir, però depèn de vostès.

Editem la nostra recent creada migració de la següent manera:

Com vaig dir més amunt, mantindrem la BD molt simple per enfocar-nos només en la creació de l'API.

Aquesta migració hauria de crear una taula **pokemons** amb les camps **id** , **name** , **created\_at** i **deleted\_at** . Els 2 últimes els fa anar Laravel de forma automàtica.

**Executem la nostra migració:**

```text
$ php artisan migrate
 Migració: 2018_05_08_044418_create_table_pokemons Migrat 
: 2018_05_08_044418_create_table_pokemons
```

Hauria d'estar la taula ja creada en la nostra BD. A l'revisar que tot va sortir bé, podem començar a crear el nostre model, per poder modificar aquesta taula.

**model:**

```text
$ php artisan make: model Pokemon
 Model creat amb èxit.
```

El model acabat de crear hauria d'estar dins de la carpeta **App /.**

Ja que el camp **name** és l'únic que modificarem en aquest cas, el afegim a l'atribut **$ fillable** de la classe Pokemons.

Nota: Per crear el model + la migració amb un sol comandament pots utilitzar:

> php marca artesanal: model Pokémon -m

A la fi, estem preparats per crear el controller que tindrà els mètodes a cridar per la nostra API. Som-hi!

**Creant el resource controller:**

Un dels talents de Laravel, és que pot crear un controlador amb mètodes ja establerts per a una api, i tenir a punt tot per a l'ús dels verbs HTTP. Tot en 1 senzill pas:

```text
$ php artisan make: controller PokemonController -r
 Controller creat correctament.
```

El **-r** , vol dir que aquest controller es farà servir com a recurs, i laravel deixa els mètodes llestos per ser omplerts. Si no afegir -r, es crearà un controller totalment buit, el que ens donaria més feina, ia ningú li agrada treballar \(?\).

Vegem com ens va quedar:

Ara anem a crear la ruta per accedir al nostre recentment creat controlador.

En Laravel, només hem de definir el nom de l'endpoint, i el controlador.

Hem obrir el fitxer **routes / api.php** , el qual potser ja tingui un middleware configurat per users per defecte, però que ignorarem.

Afegirem la ruta de la següent manera:

**¿Va quedar bé?**

Fem servir la comanda **php artisan route: list** , per veure si les nostres rutes quedar bé configurades:![](https://miro.medium.com/max/60/1*2nUW15qD2V3bWfTaJEXnBQ.png?q=20)

![](https://miro.medium.com/max/819/1*2nUW15qD2V3bWfTaJEXnBQ.png)

Bellesa.

Laravel, en la seva infinita bellesa, va configurar les rutes per als verbs http que vulguem fer servir, com [**GET, POST, PUT, PATCH i DELETE**](https://developer.mozilla.org/es/docs/Web/HTTP/Methods) . Si seguim l'estructura definida en aquesta llista, no hi ha per on perdre.

**Probemium ...**

Crearem uns pokemons amb unes peticions POST, i després demanarem un pokemon en específic usant GET. Això només perquè s'entengui la idea.

Primer, per guardar dades, si ens fixem en la llista de rutes, el verb **POST** està associat amb **api / pokemons** , i amb el mètode **pokemons.store.**

Segon, per mostrar les dades, el mètode **GET** està associat amb **api / pokemons / {Pokémon}** , i amb el mètode **pokemons.show.**

Fem-li cas a la ruta.

En PokemonController, importaré l'arxiu **App \ Pokémon** per poder canviar dades d'aquest, i omplirem els mètodes **store \(\)** i **show \(\)** .

Iniciem el nostre server amb la comanda **php artisan serve.**

Ara podem utilitzar el nostre mètode preferit per provar APIs. En el meu cas faré servir [**Postman**](https://www.getpostman.com/) **.**

Provem el mètode store \(\):![](https://miro.medium.com/max/60/1*65mRKTrKFkIYTM-ial1dSg.png?q=20)

![](https://miro.medium.com/max/582/1*65mRKTrKFkIYTM-ial1dSg.png)

Sent una petició POST, vam enviar un JSON amb la informació que volem inserir en la nostra BD.

Vam provar enviant el meu primer Pokémon **Lugia.**  
La nostra API ens respon amb un **estatus: 200 OK** .

Revisem la nostra BD:![](https://miro.medium.com/max/60/1*UkKu5b1-7ZLkJXruYqsk1g.png?q=20)![](https://miro.medium.com/max/388/1*UkKu5b1-7ZLkJXruYqsk1g.png)Bé!

Ara provem el mètode show \(\):![](https://miro.medium.com/max/60/1*EUxIy1OFctUSExkxHERKug.png?q=20)

![](https://miro.medium.com/max/1047/1*EUxIy1OFctUSExkxHERKug.png)

Postman va sol·licitar amb un mètode GET l'Pokémon amb l'id = 1 a la nostra BD, en aquest cas sent Lugia.
