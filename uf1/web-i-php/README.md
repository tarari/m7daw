---
description: Característiques avançades de PHP per a la web
---

# Web i PHP

Una aplicació web està dissenyada per retornar una resposta per a cada sol·licitud, que condueix a un **cicle de REQUEST - RESPONSE (sol·licitud - resposta)** . En el món de les aplicacions web, aquest cicle es realitza mitjançant **Hypertext Transfer Protocol** ( **HTTP)**), que és un protocol que garanteix que les dues parts es comuniquen amb el mateix llenguatge o estructura.

## Sessions

Les sessions permeten mantenir i utilitzar informació circulant dels usuaris en el servidor a través de l'array **`$_SESSION`**

A cada **`client`** que accedeix a l'aplicació i inicia sessió se li assigna un _**session ID**_\*\* únic\*\* , i és el que li permet identificar la sessió i que estigui disponible per a aquest client en concret. La forma més segura de gestionar sessions és emmagatzemant en el client només aquesta session ID (cookie), i qualsevol informació de la sessió guardar-la al costat de servidor.

Es defineix sessió com l'interval de temps d'una comunicació en la que s'intercanvien dades relacionades a la mateixa. En aquest procés de connexió persistent es comprova la cookie \*\*`php_session_id` \*\*amb la id de sessió emmagatzemada al servidor. El tancament de sessió indica la finalització de la comunicació.

La funció **`session_start()`** inicia la sessió entre l' **usuari** i el **servidor** , i permet als valors guardats en **$\_SESSION** ser accessibles després.\
Posteriorment, podem accedir a qualsevol variable sessió mentre la connexió no estigui tancada (fins i tot des d'un altre script).

```php
// Continuem  la sessió
session_start();
// Utilitzar variables de sessió
echo "Nom d'usuari: " . $_SESSION["username"];
```

Tan important és iniciar una sessió, com tancar-la. Fins i tot quan realment una sessió és només una forma temporal d'emmagatzemar dades, és important **netejar-la després per assegurar la màxima seguretat** especialment quan es tracta amb informació personal.

Per **suprimir un valor de sessió** simplement s'utilitza _unset ()_ :

```php
session_start();
// Eliminar el nom d'usuari:
unset($_SESSION["username"]);
```

Per **desvincular tots els valors de sessió de tant** es pot emprar **`session_unset():`**

```php
session_start();
// Eliminar totes les sessions:
session_unset();
```

Les dues accions anteriors només afecten els valors guardats a la sessió, no a la pròpia sessió. Encara es poden guardar nous valors en **`$_SESSION`**. Si es vol **deixar d'utilitzar completament la sessió**, per exemple quan l'usuari fa **logout** , s'utilitza :

```php
session_start();
// Finalitzar la sessió:
session_destroy();
```

És molt recomanable que **quan ja no es necessiti la sessió es destrueixi** amb **`session_destroy()`**, en comptes de **desvincular el valor dels seus valors** amb **`session_unset()`**. Si només es desvinculen els valors la sessió romandrà activa, el que deixa la porta oberta a intrusos.

## Navegació en la aplicació

Una característica molt agraïda en una aplicació web és la navegació dins l'aplicació.

És una forma de conèixer en cada moment on som i quines accions estan disponibles en qualsevol moment. Implica tenir clar el mapa de l'aplicació. És la nostra tasca saber implementar aquesta eina tan profitosa.

### Variable de servidor $\__SERVER\['REQUEST\_URI'_]

Una manera de configurar correctament l'enrutament i per tant la navegació és accedir i consultar la variable de servidor REQUEST\_URI.

Si la fraccionem des del controlador frontal (index.php), podem extreure la informació de la ruta a la qual es vol anar i per tant de quina acció es pot realitzar a continuació.

Mirem el següent exemple:

<pre class="language-php"><code class="lang-php">// Primer definim les rutes permeses en la nostra app
<strong>// routes.php
</strong><strong>&#x3C;?php
</strong>
    $routes= [
        '/'=>function(){
            echo '&#x3C;h1>Hello!&#x3C;/h1>';
        },
        '/login'=>function(array $params=[]){
            echo '&#x3C;h1>Login&#x3C;/h1>';
            //$params['name']
        }
    ];
</code></pre>

```php
// funció d'enrutament, és una mica complexa
// router.php
function run(string $url,array $routes):void
    {
        $uri=parse_url($url);
        
        $path=$uri['path'];
        
        if(!array_key_exists($path,$routes)){
            return ;
        }
        $callback=$routes[$path];
        if(!empty($uri['query'])){
            $params=[];
            parse_str($uri['query'],$params);
        }
        $callback();
    }
    
```

```php
// el controlador frontal
//index.php
<?php

    require 'functions.php';
    require 'routes.php';

    run($_SERVER['REQUEST_URI'],$routes);
```

## Formularis

Un formulari permet l'intercanvi de dades entre client (navegador) i servidor (backend de l'aplicació).

```php
<html>
<body>
<form action="form.php" method="post">
    Nombre: <input type="text" name="nom"><br>
    Email: <input type="text" name="email"><br>
    <input type="submit" value="Enviar">
</form>
</body>
</html>
```

En aquest exemple, es vol processar les dades **`$_POST['nom']`** i **`$_POST['email']`** a través de l'escript **form.php**, seguint el mètode de http POST. Sent POST permet enviar les dades clau-valor en el cos del missatge HTTP.

Un cop les dades estan en _form.php_ poden mostrar-se, guardar-se en una base de dades o el que es necessiti. En _form.php_ anem a mostrar-los:

```php
<html>
<body>
Hola <?php echo $_POST["nom"]; ?><br>
El teu email es: <?php echo $_POST["email"]; ?>
</body>
</html>
```

Els 10 **tipus d'elements més utilitzats als formularis HTML amb PHP** són ([w3schools](https://www.w3schools.com/html/html\_form\_elements.asp)):

| **element**               | **descripció**                                                       |
| ------------------------- | -------------------------------------------------------------------- |
| `input type = "text"`     | Caixa de text                                                        |
| `input type = "password"` | Caixa de text on es mostren asteriscs en lloc dels caràcters escrits |
| `input type = "checkbox"` | Caixes seleccionables que permet escollir múltiples opcions          |
| `input type = "radio"`    | Caixes seleccionables en grups que només permeten escollir una opció |
| `input type = "submit"`   | Botó per enviar el formulari                                         |
| `input type = "file"`     | Caixes de text i botó que permet pujar arxius                        |
| `input type = "hidden"`   | Element amagat. Especialment útil per tokens de seguretat            |
| `option`                  | Una opció possible dins d'un element element                         |
| `select`                  | Llista d'opcions d'elements option                                   |
| `textarea`                | text multilínia                                                      |

### Validació de formularis

La **validació de formularis** és un aspecte fonamental ja que prevé possibles atacs d'intrusos, a més d'assegurar que les dades que es reben són realment de el tipus desitjat.

Hi **ha dues maneres de validació de formularis** : al costat del client i en el costat de servidor. A la banda de el client la validació sol ser mitjançant **JavaScript** , és més ràpida i evita enviar més feina a servidor.

A la banda de servidor s'utilitza **PHP** per verificar que s'envien valors correctes, és més segur però és més lent i fa treballar una mica al servidor. Aquí vurem aquesta segona forma.

El següent és un senzill **formulari** mica més complex amb les següent dades: Nom, Contrasenya, Nacionalitat, Idiomes, Email i Lloc web:

{% code lineNumbers="true" %}
```php
<h2>Formulari:</h2>
<form action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]); ?>" method="post">
    Nom:
    <input type="text" name="nom" maxlength="50"><br>
    Contraseña:
    <input type="password" name="password"><br>
    Educacion:
    <select name="estudis">
        <option value="sense-estudis">Sense estudis</option>
        <option value="ESO" selected="selected">Educació Obligatòria</option>
        <option value="FP">Formació professional</option>
        <option value="universitat">Universitat</option>
    </select> <br>
    Nacionalitat:
    <input type="radio" name="nacionalitat" value="espanyola">Espanyola</input>
    <input type="radio" name="nacionalitat" value="altra">Altra</input><br>
    Idiomes:
    <input type="checkbox" name="idiomes[]" value="espanyol" checked="checked">Espanyol</input>
    <input type="checkbox" name="idiomes[]" value="anglés">Anglés</input>
    <input type="checkbox" name="idiomes[]" value="francés">Francés</input>
    <input type="checkbox" name="idiomes[]" value="alemany">Alemany</input><br>
    Email:
    <input type="text" name="email"><br>
    Lloc Web:
    <input type="text" name="llocweb"><br>
    // Botó d'enviar
    <input type="submit" name="submit" value="Enviar">
</form>
</body>
</html>
```
{% endcode %}

## Cookies

Al contrari que les sessions, les cookies són una eina controlada des de backend o frontend per desar dades al client (navegador).

Les cookies són com **arxius de text que es guarden a l'ordinador** . A petició d'un **servidor web** , el navegador crea un arxiu d'aquest tipus. Una vegada que passa això, el servidor pot llegir i escriure contingut en aquest arxiu.

Pot semblar perillós, però hi ha una sèrie de restriccions per fer-ho el més segur possible:

* **Els servidors web només poden accedir a cookies creades al seu propi domini** . Aquest domini és establert pel navegador quan el servidor crea un nou cookie, i només pot ser un domini o subdomini de servidor.
* Segons el **protocol HTTP** , les cookies no poden ser més grans de **4096 Bytes (4KB)** .
* Hi ha un **límit de cookies per domini** . Depèn del navegador, però solen ser 20 cookies.
* També hi ha un **límit en el nombre total de cookies al disc dur de el client** . Solen ser 300 cookies. Quan s'arriba a aquest número, una cookie antiga s'elimina abans de crear la nova.

Les cookies tenen una **data d'expiració** . Aquesta data permet el navegador eliminar cookies antigues quan ja no són requerides pel servidor web. Si la data d'expiració està buida, la cookies s'eliminarà quan finalitzi la connexió amb el servidor. Això passarà quan:

* l'usuari **tanqui la finestra** o pestanya del lloc web,
* o directament tanqui el navegador.

Aquestes cookies, normalment denominades _**session\_cookies**_ , són usades principalment per guardar ajustos temporals.

#### Sintaxi

```php
setcookie(
    string $name,
    string $value = "",
    int $expires = 0,
    string $path = "",
    string $domain = "",
    bool $secure = false,
    bool $httponly = false
): bool
```

**setcookie ()** defineix una cookie per ser enviada juntament amb la resta de capçaleres HTTP. Com altres capçaleres, cookies han de ser enviades _abans_ de qualsevol sortida (echo) en l'script (aquest és un protocol de restricció).

{% hint style="danger" %}
Això requereix es cridi a aquesta funció abans de qualsevol sortida, incloent etiquetes`<html>`i `<head>`així com qualsevol espai en blanc
{% endhint %}

.**`name`**

El nom de la cookies.

**`value`**

El valor de la cookie. Aquest valor s'emmagatzema a l'ordinador d'el client; no emmagatzemar informació sensible. Per recuperar -lo :

```php
$cookie_name=$_COOKIE['cookie_name'];
```

**`expires`**

El temps en què la cookie expira. Aquesta és una marca de temps Unix en nombre de segons des del momment actual. En altres paraules, el més probable és que es faci amb la funció [time ()](https://www.php.net/manual/es/function.time.php) més el nombre de segons abans que sigui que expiri. O es podria usar [mktime ()](https://www.php.net/manual/es/function.mktime.php) . `time()+60*60*24*30`farà que la cookie establerta expiri en 30 dies. Si s'estableix a 0, o és omès, la galeta expirarà a la **fi de la sessió** (quan el navegador és tancat).

> **Nota** :
>
> Observem que el paràmetre `expires`pren una marca de temps Unix, en lloc de format de data `Wdy, DD-Mon-YYYY HH:MM:SS GMT`, això és perquè PHP fa la conversió internament.

**`path`**

La ruta dins el servidor en la qual la cookie estarà disponible. Si s'utilitza `'/'`, la cookie estarà disponible a la totalitat de l' `domain`. Si es configura com `'/foo/'`, la cookie només estarà disponible dins de directori `/foo/`i tots els seus sub-directoris en el `domain`, com ara `/foo/bar/`. El valor per defecte és el directori actual a on s'està configurant la galeta.`domain`

El (sub) domini a què la cookie està disponible. Establint això a un subdomini (com `'www.example.com'`) farà que la cookie estigui disponible per a aquest subdomini i tots els altres subdominis d'ell mateix (pe w2.www.example.com). Perquè la cookie estigui disponible per a tot el domini (incloent tots els seus subdominis), simplement estableixi el nom de domonio ( `'example.com'`, en aquest cas).

Els navegadors més antics encara implementen l'obsoleta [»RFC 2109](http://www.faqs.org/rfcs/rfc2109) poden necessitar un `.`per comparar tots els subdominis.

**`secure`**

Indica que la cookie només hauria de transmetre per una connexió segura HTTPS des del client. Quan es configura com **`true`**, la cookie només es crearà si és que hi ha una connexió segura. De la banda de servidor, depèn de l'programador el enviar aquest tipus de cookies solament a través de connexions segures (per exemple, amb [$ \_SERVER \[ "HTTPS"\]](https://www.php.net/manual/es/reserved.variables.server.php) ).

**`httponly`**

Quan és `true` la cookie serà accessible només a través de l'protocol HTTP. Això vol dir que la cookie no serà accessible per llenguatges de scripting, com JavaScript. S'ha indicat que aquesta configuració ajuda efectivament a reduir el robatori d'identitat a través d'atacs **XSS** (encara que no és suportada per tots els navegadors). Afegit a PHP 5.2.0. Pot ser **`true`o`false`**

**`options`**

Un array associatiu que pot tenir qualsevol de les claus `expires`, `path`, `domain`, `secure`, `httponly`i `samesite`. Els valors tenen el mateix significat que es descriu per als paràmetres amb el mateix nom. El valor de l'element `samesite`hauria de ser `None`, `Lax`o `Strict`. Si no es dóna cap de les opcions permeses, els seus valors per defecte són els mateixos que els valors per defecte dels paràmetres explícits. Si l'element `samesite`és omès, no s'estableix cap atribut de la `cookie` SameSite.



## Capçaleres HTTP

Donat que la comunicació es realitza a través dels protocols HTTP, són les capçaleres HTTP les que determinen el què, el com, i el quan, s'està comunicant. En PHP faem servir la funció `header()`.

```php
header(header, replace, http_response_code)
```

### Exemples:

Header per evitar cache de pàgina:

```php
<?php
    // Date in the past
    header("Expires: Mon, 26 Jul 1997 05:00:00 GMT");
    header("Cache-Control: no-cache");
    header("Pragma: no-cache");
?>

<html>
<body>
...
...
```

Avís a un usuari per baixar un document pdf

```php
<?php
    header("Content-type:application/pdf");
    
    // It will be called downloaded.pdf
    header("Content-Disposition:attachment;filename='downloaded.pdf'");
    
    // The PDF source is in original.pdf
    readfile("original.pdf");
?>

<html>
<body>

...
...
```

Altres exemples:

```php
<?php
# Source: http://www.jonasjohn.de/snippets/php/headers.htm

// Use this header instruction to fix 404 headers
// produced by url rewriting...
header('HTTP/1.1 200 OK');
 
// Page was not found:
header('HTTP/1.1 404 Not Found');
 
// Access forbidden:
header('HTTP/1.1 403 Forbidden');
 
// The page moved permanently should be used for
// all redrictions, because search engines know
// what's going on and can easily update their urls.
header('HTTP/1.1 301 Moved Permanently');
 
// Server error
header('HTTP/1.1 500 Internal Server Error');
 
// Redirect to a new location:
header('Location: http://www.example.org/');
 
// Redriect with a delay:
header('Refresh: 10; url=http://www.example.org/');
print 'You will be redirected in 10 seconds';
 
// you can also use the HTML syntax:
// <meta http-equiv="refresh" content="10;http://www.example.org/ />
 
// override X-Powered-By value
header('X-Powered-By: PHP/4.4.0');
header('X-Powered-By: Brain/0.6b');
 
// content language (en = English)
header('Content-language: en');
 
// last modified (good for caching)
$time = time() - 60; // or filemtime($fn), etc
header('Last-Modified: '.gmdate('D, d M Y H:i:s', $time).' GMT');
 
// header for telling the browser that the content
// did not get changed
header('HTTP/1.1 304 Not Modified');
 
// set content length (good for caching):
header('Content-Length: 1234');
 
// Headers for an download:
header('Content-Type: application/octet-stream');
header('Content-Disposition: attachment; filename="example.zip"'); 
header('Content-Transfer-Encoding: binary');
// load the file to send:
readfile('example.zip');

// Headers for CSV File:
header('Content-Type: application/octet-stream');
header('Content-Disposition: attachment; filename="filename.csv"');
header('Content-Transfer-Encoding: binary');

echo($csv_content);

// Disable caching of the current document:
header('Cache-Control: no-cache, no-store, max-age=0, must-revalidate');
header('Expires: Mon, 26 Jul 1997 05:00:00 GMT'); // Date in the past
header('Pragma: no-cache');
 
// set content type:
header('Content-Type: text/html; charset=iso-8859-1');
header('Content-Type: text/html; charset=utf-8');
header('Content-Type: text/plain'); // plain text file
header('Content-Type: image/jpeg'); // JPG picture
header('Content-Type: application/zip'); // ZIP file
header('Content-Type: application/pdf'); // PDF file
header('Content-Type: audio/mpeg'); // Audio MPEG (MP3,...) file
header('Content-Type: application/x-shockwave-flash'); // Flash animation
 
// show sign in box
header('HTTP/1.1 401 Unauthorized');
header('WWW-Authenticate: Basic realm="Top Secret"');
print 'Text that will be displayed if the user hits cancel or ';
print 'enters wrong login data';
?>
```

## Autenticació HTTP

Exemple d'autenticació bàsica:

```php
<?php
if (!isset($_SERVER['PHP_AUTH_USER'])) {
    header('WWW-Authenticate: Basic realm="My domain"');
    header('HTTP/1.0 401 Unauthorized');
    echo 'Text per a enviar si l'usuari prem el botó Cancelar';
    exit;
} else {
    echo "<p>Hola {$_SERVER['PHP_AUTH_USER']}.</p>";
    echo "<p>Ha introduït {$_SERVER['PHP_AUTH_PW']} com a password.</p>";
}
?>
```

## Codis estat HTTP

Com a conseqüència de les operacions realitzades en la comunicació i en el cicle de vida d'una **sol·licitud-resposta (REQ - RESP)**, es va informant contínuament als protocols HTTP de l'estat de la comunicació, es tracta d'uns codis numèrics amb una significació especial.

#### 1. 1XX Respostes informatives

Aquest tipus de codi d'estat indica una **resposta provisional** . HTTP / 1.0 no va definir cap estat 1XX, de manera que els servidors no haurien d'enviar una resposta 1XX a un client HTTP / 1.0 excepte per a experiments.

#### 1. 1xx Respostes informatives

* **100 Continue** . El servidor ha rebut els **headers de l'request** i el client hauria de procedir a enviar el cos de la resposta.
* **101 Switching Protocols** . El **requester** ha sol·licitat a l'servidor commutar protocols.
* **102 Processing (WebDAV; RFC 2518)** . Usat en **requests** per reprendre peticions PUT o POST avortades.

#### 2. 2XX Peticions correctes

Aquest codi d'estat indica que l'acció sol·licitada pel **client** ha estat rebuda, entesa, acceptada i processada correctament.

* **200 OK** . El request és correcte. Aquesta és la resposta estàndar per respostes correctes.
* **201 Created** . El request s'ha completat i s'ha creat un nou recurs.
* **202 Accepted** . El request s'ha acceptat per processar-lo, però el procés encara no ha acabat.
* **203 Non-authoritative Information** . El request s'ha processat correctament, però retorna informació que podria venir d'una altra font.
* **204 No Content** . El request s'ha processat correctament, però no torna cap contingut.
* **205 Reset Content** . El request s'ha processat correctament, però no torna cap contingut i es requereix que el requester recarregui el contingut.
* **206 Partial Content** . El servidor retorna només part de el recurs a causa d'una limitació que ha configurat el client (s'usa en eines de descàrrega com [wget](http://es.wikipedia.org/wiki/Wget) ).
* **207 Multi-Status (WebDAV; RFC 4918)** . El cos de l'missatge és XML i pot contenir un nombre de codis d'estat diferents depenent de el nombre de sub-requests.

#### 3. Redireccions 3XX

El client ha de prendre una acció addicional per completar el **request** . Molts d'aquests estats es fan servir per **redireccions** . L' **agent d'** pot dur a terme l'acció addicional sense necessitat que actuï el **usuari** només si el **mètode** utilitzat en la segona petició és **GET** o **HEAD** . Un agent d'no hauria de redireccionar automàticament un request més de cinc vegades, ja que aquestes redireccions solen indicar un **infinite loop** .

* **300 Multiple Choices** . És una llista d'enllaços. L'usuari pot seleccionar un enllaç i anar a aquesta direcció. Hi ha un màxim de cinc direccions.
* **301 Moved Permanently** . La pàgina sol·licitada s'ha mogut permanentment a una nova URI.
* **302 Found** . La pàgina sol·licitada s'ha mogut temporalment a una nova URI.
* **303 See Other** . La pàgina sol·licitada es pot trobar en un URI diferent.
* **304 Not Modified** . Indica que la pàgina sol·licitada no s'ha modificat des de l'última petició.
* **305 Utilitza Proxy (des HTTP / 1.1)** . El recurs sol·licitat només està disponible a través d'intermediari, la direcció es proporciona en la resposta. Molts clients HTTP com Mozilla o Internet Explorer no manegen bé aquestes respostes amb aquests codis d'estat, sobretot per seguretat.
* **307 Temporary Redirect (des HTTP / 1.1)** . La pàgina sol·licitada s'ha mogut temporalment a un altre URL. En aquest cas el recurs hauria de repetir-se amb una altra URI, però, futurs requests hauran d'usar la URI original. A l'contrari que amb la 302, el mètode request no pot canviar quan es reubique el request original.
* **308 Permanent Redirect (RFC 7538)** . El request i futurs requests s'haurien de repetir usant un altre URI. Aquest també és similar a l'301, però no permet a l'mètode HTTP que canviï.

#### 4. 4XX Errors de el client

Excepte quan es respon a un **HEAD request** , el servidor ha d'incloure una entitat que conté una explicació de l'error, i si és temporal o permanent. Són aplicables a qualsevol mètode de sol·licitud (GET, POST ...). Els **user agents** han de mostrar qualsevol entitat a l'usuari:

* **400 Bad Request** . El servidor no pot o no va a processar el request per un error de sintaxi de client.
* **401 Unauthorized (RFC 7235)** . Similar a l'error 403, però s'usa quan es requereix una autenticació i ha fallat o encara no a facilitat.
* **402 Payment Required** . Reservat per a futur ús. La intenció original va ser per a **pagament amb targeta** o **micropagament** , però això no ha passat, i aquest codi amb prou feines s'usa.
* **403 Forbidden** . El request va ser vàlid però **el servidor es nega a respondre** .
* **404 Not Found** . El recurs de l'request no s'ha pogut trobar però podria estar disponible en el futur. Es permeten **requests subsecuentes** per part de client.
* **405 Method Not Allowed** . S'ha fet un request amb un recurs utilitzant un **mètode request no suportat** per aquest recurs (per exemple usant GET en un formulari que requereix POST).
* **406 Not Acceptable** . El recurs sol·licitat només genera **contingut no acceptat** d'acord amb els headers Accept enviats al request.
* **407 Proxy Authentication Required (RFC 7235)** . El client s'ha d'identificar primer amb el **proxy** .
* **408 Request Timeout** . El client no ha enviat un **request amb el temps necessari** amb el qual el servidor estava preparat per esperar. El client podria repetir el request sense modificacions més tard.
* **409 Conflict** . Conflicte al request, com quan s'actualitzen a el mateix temps dos recursos.
* **410 Gone** . El recurs sol·licitat no està disponible ni ho estarà en el futur. **Un cercador eliminarà abans una pàgina 410 que una 404** .
* **411 Length Required** . El request no va especificar la **longitud de l'contingut** , la qual és requerida pel recurs sol·licitat.
* **412 precondition Failed (RFC 7232)** . El servidor no compleix una de les precondicions que el requester afegeix al request.
* **413 Request Entity Too Large** . El request és més llarg que el que està disposat a acceptar el servidor.
* **414 Request-URI Too Long** . L' **URI** és molt llarg perquè el servidor ho processi.
* **415 Unsupported Mitjana Type** . L'entitat request té un **mitjana type** que el servidor o recurs no suporten.
* **416 Requested Range Not satisfiable (RFC 7233)** . El client ha sol·licitat una porció d'arxiu, però el servidor no pot oferir aquesta porció.
* **417 Expectation Failed** . El servidor no pot complir els requeriments de l'header de l'request Expect.
* **418 I'm a teapot (RFC 2324)** . Va ser part d'un [April Fool 's day](http://en.wikipedia.org/wiki/April\_Fools'\_Day\_Request\_for\_Comments) , i no s'espera que s'implementi en servidors HTTP. La RFC especifica que aquest codi hauria de ser retornat per teteres per servir te.

#### 5. 5XX Errors de servidor

El servidor ha fallat a l'completar una sol·licitud aparentment vàlida. Quan els codis d'estat comencen per 5 indica casos en què **el servidor sap que té un error** o realment és **incapaç de processar el request** . Excepte quan es tracta d'un request **HEAD** , el servidor ha d'incloure una entitat contenint 1 **explicació de l'error** , i de si és temporal o permanent. Igualment els utilitzar-agents hauran de mostrar qualsevol entitat a l'usuari. Aquests codis de resposta s'apliquen a qualsevol mètode request.

* **500 Internal Server Error** . Error genèric, quan s'ha donat una condició no esperada i no es pot concretar el missatge.
* **501 Not Implemented** . El servidor o no reconeix el **mètode de l'request** o no té la capacitat per completar-lo. Normalment és una cosa que s'oferirà en el futur, com un **nou servei** d'una **API** .
* **502 Bad Gateway** . El server actuava com a porta d'entrada o **servidor intermediari** i va rebre una resposta no vàlida de servidor upstream.
* **503 Service Unavailable** . El servidor està actualment no disponible, ja sigui per **manteniment** o per **sobrecàrrega** .
* **504 Gateway Timeout** . El servidor estava actuant com **a porta d'entrada** o **servidor intermediari** i no va rebre una resposta oportuna per part de servidor upstream.
* **505 HTTP Version Not Supported** . El servidor no suporta la **versió de el protocol HTTP** utilitzada en el request.
* **511 Network Authentication Required (RFC 6585)** . El client necessita autenticar-vos per poder accedir a la xarxa.

## Error Handlers - gestors

Els errors i els gestors d’errors de la programació de programari són un concepte impagable que ajuda als desenvolupadors a identificar punts de fallada en temps de compilació de l’aplicació o en temps d’execució. Poden assenyalar diferents nivells de gravetat.

A PHP, es gestionen i registren els errors mitjançant una sèrie de funcions integrades. Faciliten l’adaptació de la gestió i el registre d’errors per adaptar-se a les necessitats d’una aplicació registrant gestors d’errors personalitzats o configurant informes d’errors per a un rang específic de nivells.

#### die(Missatge)

La forma més simple de controlar un error és aturar-lo, die(), proporciona aquest control.

#### error\_log()

Podem gestionar el registre d'error, enviant un missatge via mail o bé desant-lo al servidor (al directori especificat al php.ini)

#### Excepcions

Tractament d'errors amb l'objecte Exception.

Si una excepció no és tractada, sempre ens apareix com a "uncaught exception".

Ens podem trobar estructures com:

```php
try{
   .....
   } catch (Exception $e){
   ....
   }
```

o bé&#x20;

```php
if (condició) 
{
    throw new Exception();
    //llença una excepció si passa la condició
    }
```
