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

Ara la interface:

```php
interface ExportableDocumentInterface
{
    public function export(Document $document);
}
```

I la resta de classes, cadascuna amb la seva responsabilitat:

```php
namespace Solid\S;

use Solid\S\ExportableDocumentInterface;

class HtmlExportableDocument implements ExportableDocumentInterface
{
    public function export(Document $document)
    {
        echo "DOCUMENT EXPORTED TO HTML".PHP_EOL;
        echo "Title: ".$document->getTitle().PHP_EOL;
        echo "Content: ".$document->getContent().PHP_EOL.PHP_EOL;
    }
}
```

```php
namespace Solid\s;


class PdfExportableDocument
{
    public function export(Document $document)
    {
        echo "DOCUMENT EXPORTED TO PDF".PHP_EOL;
        echo "Title: ".$document->getTitle().PHP_EOL;
        echo "Content: ".$document->getContent().PHP_EOL.PHP_EOL;
    }
}
```

### “O”: Open-Closed principle

> Els objectes haurien d'estart oberts per a la seva extensió i tancats per la seva modificació.

Imaginem que necessitem implementar un sistema de **login**. Inicialment per autenticar al nostre usuari necessitem d'un usuari i una contrasenya, fins aquí tot bé, però què passa si requerim que l'usuari s'autentiqui mitjançant twiter, gmail o facebook ?

Una implementació que trenca amb aquest principi seria:

```php
class LoginService
{
    public function login($user)
    {
        if ($user instanceof User) {
            $this->authenticateUser($user);
        } else if ($user instanceOf ThirdPartyUser) {
            $this->authenticateThirdPartyUser($user);
        }
    }
}
```

Fent un seguiment a aquest principi, ens trobem:

```php
interface LoginInterface
{
    public function authenticateUser($user);
}

class UserAuthentication implements LoginInterface
{
    public function authenticateUser($user)
    {
        // TODO: Implement authenticateUser() method.
    }
}

Class ThirdPartyUserAuthentication implements LoginInterface
{
    public function authenticateUser($user)
    {
        // TODO: Implement authenticateUser() method.
    }
}

class LoginService
{
    public function login(LoginInterface $user)
    {
        $user->authenticateUser($user);
    }
}
```

Com podem  observar, la classe **`LoginService`** és indiferent al  mètode d'autenticació \(per base de dades, via google o twitter, etc.\) que es vagi a utilitzar, tota la lògica aquesta dins de cada Classe dels diferents tipus d'autenticació que puguem tenir.



¿Por qué? Porque cada vez que añadamos un nuevo tipo de figura nos veremos obligados a modificar el método `sum` de la clase `AreaCalculator` de cara a añadir el nuevo tipo de figura.

Para resolver este problema una solución sería crear la interfaz `Shape` que declararía el método `area` que todas las figuras deberían implementar para devolver su área.



### “L”: Liskov Substitution principle

> Si F és una classe filla de P, llavors els objectes de P podrien ser substituïts per objectes de tipus F sense alterar les propietats del problema.

El principi de substitució de Liskov \(conegut com LSP\) és un concepte important quan es tracta de programació orientada a objectes.

**Matemàticament**

> Sigui ϕ**\(x\)** una propiedad comprovable sobre els objectes **x** de tipus T. Llavors ϕ**\(y\)** ha de ser cert per a tots els objectes y  del tipus **S** on **S**, és un subtipus de **T**.

El que vol dir és que qualsevol classe filla hauria de poder ser substituïble per la classe pare sense necessitat de conèixer les diferències entre elles.

Una altra manera de pensar en això és: si tens una classe \(base\), i després tens cinc classes que estenen aquesta classe **base**, qualsevol codi que usi la classe base també hauria de funcionar si reemplaça aquesta classe base amb alguna de les seves classes filles i viceversa.

Vegem un exemple on es requereix calcular el preu total amb impost inclòs segons país de l'usuari.

```php
<?php

interface PriceCalculatorInterface
{
    public static function getTotal(float $price): string;
}

class PriceCalculator implements PriceCalculatorInterface
{
    const DEFAULT_VAT = 7;

    public static function getTotal(float $price): string
    {
        $vat = (($price * self::DEFAULT_VAT) / 100);

        return number_format(round($price + $vat, 2), 1, ',', '.');
    }
}

class SpainPriceCalculator extends PriceCalculator
{
    const DEFAULT_VAT = 21;

    public static function getTotal(float $price): string
    {
        $vat = (($price * self::DEFAULT_VAT) / 100);

        return number_format(round($price + $vat, 2), 2, ',', '.');
    }
}

class ItalyPriceCalculator extends PriceCalculator
{
    const DEFAULT_VAT = 22;

    public static function getTotal(float $price): string
    {
        $vat = (($price * self::DEFAULT_VAT) / 100);

        return number_format(round($price + $vat, 3), 3, '.', ',');
    }
}

class PriceService
{
    public function getTotalPrice(PriceCalculator $priceCalculator): string
    {
        return $priceCalculator::getTotal(1197.45);
    }
}

$priceService = new PriceService();

$defaultTotal = $priceService->getTotalPrice(new PriceCalculator());
$spainTotal   = $priceService->getTotalPrice(new SpainPriceCalculator());
$italyTotal   = $priceService->getTotalPrice(new ItalyPriceCalculator());

echo "Default Total: " . $defaultTotal . PHP_EOL . PHP_EOL;
echo "Spain Total: " . $spainTotal . PHP_EOL . PHP_EOL;
echo "Italy Total: " . $italyTotal;
```

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



