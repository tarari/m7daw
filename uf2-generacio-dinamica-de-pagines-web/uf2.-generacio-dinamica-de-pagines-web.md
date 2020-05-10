---
description: Aprofundim i comencem a explicar Programació orientada a l'objecte en PHP.
---

# UF2. Generació dinàmica de pàgines web

## Principis SOLID

Aquest patró va ser promogut per Robert C. Martin, conegut com Uncle Bob \(Oncle Bob\) el qual és considerat el pare de l'arquitectura de programari ja que gràcies al seu enginy, ens ha posat a disposició tota la seva experiència i bones pràctiques a seguir en pro de desenvolupar sistemes amb arquitectura neta, llegibles, robustos i sobretot, escalables.

**SOLID** es un acrònim per a simbolitzar els 5 principis bàsics de disseny a l'hora de treballar amb el  paradigma de la programació orientada a objectes. Ajustar-nos a aquests principis ens permetrà desenvolupar arquitectures molt més fàcils de mantindre i esacalables , per tant convé familiaritzar-se amb elles de cara a entendre les seves implicacions i saber com aplicar-les. Recordem, tot és **PRÀCTICA**.

###  **S:** Single-responsibility principle

> Una classe només hauria de tenir una única raó per canviar

Principi de responsabilitat única: Una classe ha de tenir una i només una raó per canviar o el que és el mateix: **Una classe** ha de tenir **una única responsabilitat**.

Vist en un exemple real, si necessitem exportar un document en diferents formats com HTML i PDF, no podem delegar totes les funcions i implementacions a una única classe, la idea seria repartir **cada tasca a una classe per separat**. Vegem un exemple de coma quedaria una implementació aplicant aquest principi:

```php
class Document
{
    protected $title;
    protected $content;

    public function __construct(string $title, string $content)
    {
        $this->title = $title;
        $this->content= $content;
    }

    public function getTitle(): string
    {
        return $this->title;
    }

    public function getContent(): string
    {
        return $this->content;
    }
}
```

ya que el método `insertInDB` está fuera del alcance de la misma. Por tanto, la solución correcta sería tener dos clases para evitar ese tipo de acoplamiento:![](https://miro.medium.com/max/60/1*EhfQUavTj36hCOVL06CJVQ.png?q=20)![](https://miro.medium.com/max/2768/1*EhfQUavTj36hCOVL06CJVQ.png)![](https://miro.medium.com/max/60/1*mQn-7um_poL3Jf7wVf6YsA.png?q=20)![](https://miro.medium.com/max/2768/1*mQn-7um_poL3Jf7wVf6YsA.png)

### “O”: Open-Closed principle

> Los objetos deberían estar abiertos para su extensión y cerrados para su modificación

De acuerdo a este principio, una entidad debería ser posible extenderla con nuevas funcionalidades sin necesidad de modificar su código.

Supongamos por ejemplo que tenemos que calcular la suma total de las áreas de una serie de figuras geométricas. Una posible implementación que no repetiría este principio sería la siguiente:![](https://miro.medium.com/max/38/1*jOa-IUsjSoONO9ISF-GyNQ.png?q=20)![](https://miro.medium.com/max/2768/1*jOa-IUsjSoONO9ISF-GyNQ.png)

¿Por qué? Porque cada vez que añadamos un nuevo tipo de figura nos veremos obligados a modificar el método `sum` de la clase `AreaCalculator` de cara a añadir el nuevo tipo de figura.

Para resolver este problema una solución sería crear la interfaz `Shape` que declararía el método `area` que todas las figuras deberían implementar para devolver su área.

De este modo, el método `sum` de `AreaCalculator` tan sólo tendría que llamar al método `area` de cada objeto \(pues todos implementan la interfaz `Shape` \) sin preocuparse del tipo de objeto que sea.![](https://miro.medium.com/max/36/1*wwvQkpwkFA8uc12qwd8TPQ.png?q=20)![](https://miro.medium.com/max/2768/1*wwvQkpwkFA8uc12qwd8TPQ.png)

### “L”: Liskov Substitution principle

> Cada clase que hereda de otra puede usarse como su padre sin necesidad de conocer las diferencias entre ellas.

Aunque formalmente se define como:

> Sea ϕ**\(x\)** una propiedad comprobable acerca de los objetos **x** de tipo T. Entonces ϕ**\(y\)** debe ser verdad para los objetos **y** del tipo **S** donde **S**, es un subtipo de **T**.

Imaginad por ejemplo que tenemos dos tipos de vehículos: `Car` y `Bike.` Según las circunstancias podríamos emplear cualquiera de ellos siendo la única diferencia la velocidad a la que se mueven. El principio de Liskov nos obliga a que nuestra aplicación funcione sin necesidad de comprobar la clase a la que pertenecen los objetos.

Por ejemplo, un código que no cumpliría este principio sería el siguiente:![](https://miro.medium.com/max/60/1*kuGstrch8L3MbG3xGTVPEg.png?q=20)![](https://miro.medium.com/max/2768/1*kuGstrch8L3MbG3xGTVPEg.png)

Ya que en la línea 19 estamos comprobando el tipo de vehículo y nuestra aplicación debería ser capaz de funcionar sin esa comprobación.

Un ejemplo de código que cumple este principio podría ser:![](https://miro.medium.com/max/36/1*SUeCAOjMpWwvKtjc0nmnVQ.png?q=20)![](https://miro.medium.com/max/2768/1*SUeCAOjMpWwvKtjc0nmnVQ.png)

### “I”: Interface Segregation principle

> Un cliente nunca debería verse obligado a implementar una interfaz que no utiliza y debería verse obligado a depender de métodos que no utiliza.

Es decir, una clase no debería implementar una interfaz que no vaya a usar ni las interfaces deberían estar sobrecargadas de métodos que no todas las clases que las implementen los necesitarán.

Por tanto, la solución es plantear interfaces específicas en vez de interfaces generales que cubran excesivos casos de uso.

Por ejemplo, si tenemos máquinas capaces de hacer café y té, una posible implementación que no respetase este principio sería:![](https://miro.medium.com/max/50/1*TMkB63h-Lu_KCzAEZZqbKw.png?q=20)![](https://miro.medium.com/max/2768/1*TMkB63h-Lu_KCzAEZZqbKw.png)

Esto se debe a que la interfaz `BeverageMachine` es demasiado general de modo que las clases que la implementan se ven obligadas a lanzar excepciones en los métodos que no necesitan.

Una implementación correcta de acuerdo a este principio sería:![](https://miro.medium.com/max/60/1*dAU3HGTvkwjLrsdG8uf6gA.png?q=20)![](https://miro.medium.com/max/2768/1*dAU3HGTvkwjLrsdG8uf6gA.png)

### “D”: Dependency Inversion principle

> A. Las clases de alto nivel no deberían depender de las clases de bajo nivel. Ambas deberían depender de las abstracciones.
>
> B. Las abstracciones no deberían depender de los detalles. Los detalles deberían depender de las abstracciones.

Es decir, una clase particular no debería depender directamente de una clase sino de la abstracción de la misma de modo que se favorezca la reusabilidad frente al acoplamiento.

Por ejemplo, una clase que requiera una conexión a una base de datos no seguiría este principio si es declarada así:![](https://miro.medium.com/max/60/1*8SnYziO2-ffbrTC9wM9tfA.png?q=20)![](https://miro.medium.com/max/2768/1*8SnYziO2-ffbrTC9wM9tfA.png)

Pues estaría acoplada al tipo de base de datos que emplee la aplicación por lo que si cambiáramos de sistema nos veríamos obligados a modificarla \(esta implementación también rompe con el principio “Open-Closed”\).

La solución correcta sería declarar una interfaz `DBConnectionInterface` sobre la que trabajaría `UserDB` :![](https://miro.medium.com/max/60/1*7jzXwDYGA29k6g6043e9Mw.png?q=20)![](https://miro.medium.com/max/2836/1*7jzXwDYGA29k6g6043e9Mw.png)

De este modo, si cambiamos de tipo de base de datos `SomeClass` seguirá funcionando pues depende de la abstracción proporcionada por `DatabaseConnectionInterface` .  




## Projecte de creació i adaptació d’un marc de treball propi adaptant app

Aprofundim el coneixement de la programació orientada a objecte a través de la creació d'un marc de treball propi adaptant una aplicació.

En les aplicacions web és important resoldre els següents problemes :

* Captura del REQUEST del client
* Adaptar resposta al tipus de client \(HTML, JSON...\)

En el cas de seguir un paradigma MVC \(Model View Controller\), es poden adoptar els següents elements:

* Reescriptura del REQUEST per fer-lo més amigable \(.htaccess\)
* Captura a través del Request del controlador \(objecte\) i l'acció \(mètode\)
* Llençament de la instància de l'objecte controlador i posteriorment cridar l'acció corresponent

**Funcionament bàsic de l'app \( paradigma MVC a nivell web\)**

**Cicle REQUEST - RESPONSE**

Si seguim el cicle entre client i servidor, podem analitzar com es desenvolupa la resposta, aquesta es dona sempre a nivell de Controlador, ja que estem fent servir paradigmes MVC.

![](https://agora.xtec.cat/escolesnuria/moodle/pluginfile.php/4412/mod_assign/intro/Cicle%20request.png)

### Reescriptura de la URI

**.htaccess**

```text
Options +FollowSymLinks

RewriteEngine On

RewriteCond %{REQUEST_FILENAME} -f [OR]
RewriteCond %{REQUEST_FILENAME} -d 

RewriteRule ^.*$ index.php [NC,L]
```

Com s'observa, es tracta de redirigir tota consulta \(_query_ \) cap al controlador frontal de l'aplicació \(**index.php**\).

Per una bona càrrega de classes fem servir l'estandar PSR- 4 \(autocàrrega de classes\)

**Adaptació a estàndat PSR-4 \(autoload\) Composer.json**

```text
{
    "autoload": {
        "psr-4":{
            "App\\": "src/"
        }
    }
```



