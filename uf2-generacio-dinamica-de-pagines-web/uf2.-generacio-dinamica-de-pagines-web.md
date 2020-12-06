---
description: Aprofundim i comencem a explicar Programació orientada a l'objecte en PHP.
---

# Refactorització i bones pràctiques en OOP

## Principis SOLID

En el procés de creació de software de qualitat és molt importat millorar el disseny de les aplicacions, la manera OOP ens indica com fer-ho, però SOLID aporta organització de les classes i de la seva interacció.

Aquest patró va ser promogut per Robert C. Martin, conegut com _Uncle Bob_  el qual és considerat el pare de l'arquitectura de programari ja que gràcies al seu enginy, ens ha posat a disposició tota la seva experiència i bones pràctiques a seguir en pro de desenvolupar sistemes amb arquitectura neta, llegibles, robustos i sobretot, escalables.

**SOLID** es un acrònim per a simbolitzar els 5 principis bàsics de disseny a l'hora de treballar amb el  paradigma de la programació orientada a objectes. Ajustar-nos a aquests principis ens permetrà desenvolupar arquitectures molt més fàcils de mantindre i esacalables , per tant convé familiaritzar-se amb elles de cara a entendre les seves implicacions i saber com aplicar-les. Recordem, tot és **PRÀCTICA**.

###  **S:** Single-responsibility principle

> Una classe només hauria de tenir una única raó per canviar
>
> Una classe &lt;==&gt; una responsabilitat

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
namespace Solid\S;


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



### “I”: Interface Segregation principle

> Un client \(entenen una classe\) mai ha de ser obligat a implementar una interface que no utilitzem o els clients no han de ser forçats a dependre de mètodes que no facin servir.

En aquest punt, estaria bé  esmentar un argot més, els _Fat Interface_. Això és que hem d'evitar fer que les nostres interfaces es tornin grosses quan es manegen gran quantitat de contractes. Una interface grossa viola també el principi de Responsabilitat Única \(_Single Responsability Principle_\) ja que segurament aquesta interface estaria manejant més d'una responsabilitat alhora.

En resum, hem de garantir separar funcionalitats i definir els contractes mitjançant interfícies la responsabilitat sigui única.

Vegem un exemple on es trenca aquest principi:

```php
interface Worker {
 
  public function takeBreak()
 
  public function code()
 
  public function callToClient()
 
  public function attendMeetings()
 
  public function getPaid()
}

class Manager implements Worker {
  public function code() {
    return false;
  }
}

class Developer implements Worker {
  public function callToClient() {
    echo "I'll ask my manager.";
  }
}
```

Com es pot observar, la interface  obliga a ambdues classes a implementar mètodes que no necessita, per exemple un Manager no té perquè implementar el mètode « **code \(..\)»** però si « **attendMeeting \(...\)** » i un Developer no té perquè implementar el mètode « **attendMeeting \(...\)** » però si « **code \(...\)** «. La solució és aplicar SRP i separar responsabilitats \(més interfaces\).



### “D”: Dependency Inversion principle

Aquest és potser el més simple dels principis i estableix que l_es classes han de dependre d'abstraccions, no de concrecions_. Essencialment, no dependre de classes concretes, i si dependre d'interfaces, de manera que s'afavoreix la reusabilitat davant de l'acoblament.

Prenguem el següent exemple:

```php
class MySqlConnection {
    public function connect() {}
}
 
class PageLoader {
    private $connection;
    public function __construct(MySqlConnection $connection) {
        $this->connection = $connection;
    }
}
```

Aquesta estructura significa que essencialment estem estancats amb l'ús de **MySQL** per a la nostra capa de base de dades.

Què passa si volem canviar això per un adaptador de base de dades diferent? Podríem estendre la classe **MySqlConnection** per crear una connexió amb **la Memòria Cau** o [**Redis**](https://redis.io/) per exemple, però això violaria el principi de la substitució de **Liskov** .

El més probable és que els administradors de bases de dades alternatius puguin usar-se per carregar les pàgines, de manera que necessitem trobar una manera de fer-ho.

La solució aquí és crear una interfície anomenada **ConnectionInterface** i després implementar aquesta interfície en la classe **MySqlConnection** i en la classe **RedisConnection** per exemple.

Després, en lloc de confiar en un objecte **MySqlConnection** que es passa a la classe **PageLoader** , confiem en qualsevol classe que implementi la interfície **ConnectionInterface** .

Vegem com quedaria:

```php
interface ConnectionInterface {
    public function connect();
} 
 
class MySqlConnection implements ConnectionInterface {
    public function connect() {}
}

class RedisConnection implements ConnectionInterface {
    public function connect() {}
}
 
class PageLoader {
    private $connection;
    public function __construct(ConnectionInterface $connection) {
        $this->connection = $connection;
    }
}
```

Amb aquesta implementació, va ser possible crear una classe **RedisConnection** i sempre que implementi la interface **ConnectionInterface** , podem usar-lo en la classe **PageLoader** per carregar pàgines.

Aquest enfocament també ens obliga a escriure codi de tal manera que **eviti detalls** **d'implementació** específics en classes que no es preocupen per això. A causa de que hem passat una classe **MySqlConnection** a la nostra classe **PageLoader** , ja no tindríem perquè escriure consultes natives **SQL** a la classe **PageLoader** , o fer sentències IF per determinar quin tipus de connexió és i implementar per exemple una connexió a **Redis** .

Això vol dir que quan passem  un objecte **RedisConnection** es comportarà de la mateixa manera que qualsevol altre tipus de classe de connexió.

## 

