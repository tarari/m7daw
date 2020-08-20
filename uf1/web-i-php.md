---
description: Característiques avançades de PHP per a la web
---

# Web i PHP

Una aplicació web  està dissenyada per retornar una resposta per a cada sol·licitud, que condueix a un **cicle de REQUEST - RESPONSE \(sol·licitud -resposta\)** . En el món de les aplicacions web, aquest cicle es realitza mitjançant **Hypertext Transfer Protocol** \( **HTTP\)**\), que és un protocol que garanteix que les dues parts es comuniquen amb el mateix llenguatge o estructura.

## Sessions 

Les sessions permeten mantenir i utilitzar informació circulant dels usuaris en el servidor a través  de l'array **`$_SESSION`**



## Formularis

Un formulari permet l'intercanvi de dades entre client \(navegador\) i servidor \(backend de  l'aplicació\).



## Cookies

Al contrari que les sessions, les cookies són una eina controlada des de backend o frontend per desar dades al client \(navegador\).





## Capçaleres HTTP

Donat que la comunicació es realitza a través dels protocols HTTP,  són les capçaleres HTTP les que determinen el què, el com, i el quan, s'està comunicant.



## Autenticació HTTP



## Codis estat HTTP

Com a conseqüència de les operacions realitzades en la comunicació i en el cicle de vida d'una **sol·licitud-resposta \(REQ - RESP\)**, es va informant contínuament als protocols HTTP de l'estat de la comunicació, es tracta d'uns codis numèrics amb una significació especial.



#### 1. 1XX Respostes informatives

Aquest tipus de codi d'estat indica una **resposta provisional** . HTTP / 1.0 no va definir cap estat 1XX, de manera que els servidors no haurien d'enviar una resposta 1XX a un client HTTP / 1.0 excepte per a experiments.

#### 1. 1xx Respostes informatives

* **100 Continue** . El servidor ha rebut els **headers de l'request** i el client hauria de procedir a enviar el cos de la resposta.
* **101 Switching Protocols** . El **requester** ha sol·licitat a l'servidor commutar protocols.
* **102 Processing \(WebDAV; RFC 2518\)** . Usat en **requests** per reprendre peticions PUT o POST avortades.

#### 2. 2XX Peticions correctes

Aquest codi d'estat indica que l'acció sol·licitada pel **client** ha estat rebuda, entesa, acceptada i processada correctament.

* **200 OK** . El request és correcte. Aquesta és la resposta estàndar per respostes correctes.
* **201 Created** . El request s'ha completat i s'ha creat un nou recurs.
* **202 Accepted** . El request s'ha acceptat per processar-lo, però el procés encara no ha acabat.
* **203 Non-authoritative Information** . El request s'ha processat correctament, però retorna informació que podria venir d'una altra font.
* **204 No Content** . El request s'ha processat correctament, però no torna cap contingut.
* **205 Reset Content** . El request s'ha processat correctament, però no torna cap contingut i es requereix que el requester recarregui el contingut.
* **206 Partial Content** . El servidor retorna només part de el recurs a causa d'una limitació que ha configurat el client \(s'usa en eines de descàrrega com [wget](http://es.wikipedia.org/wiki/Wget) \).
* **207 Multi-Status \(WebDAV; RFC 4918\)** . El cos de l'missatge és XML i pot contenir un nombre de codis d'estat diferents depenent de el nombre de sub-requests.

#### 3. Redireccions 3XX

El client ha de prendre una acció addicional per completar el **request** . Molts d'aquests estats es fan servir per **redireccions** . L' **agent d'** pot dur a terme l'acció addicional sense necessitat que actuï el **usuari** només si el **mètode** utilitzat en la segona petició és **GET** o **HEAD** . Un agent d'no hauria de redireccionar automàticament un request més de cinc vegades, ja que aquestes redireccions solen indicar un **infinite loop** .

* **300 Multiple Choices** . És una llista d'enllaços. L'usuari pot seleccionar un enllaç i anar a aquesta direcció. Hi ha un màxim de cinc direccions.
* **301 Moved Permanently** . La pàgina sol·licitada s'ha mogut permanentment a una nova URI.
* **302 Found** . La pàgina sol·licitada s'ha mogut temporalment a una nova URI.
* **303 See Other** . La pàgina sol·licitada es pot trobar en un URI diferent.
* **304 Not Modified** . Indica que la pàgina sol·licitada no s'ha modificat des de l'última petició.
* **305 Utilitza Proxy \(des HTTP / 1.1\)** . El recurs sol·licitat només està disponible a través d'intermediari, la direcció es proporciona en la resposta. Molts clients HTTP com Mozilla o Internet Explorer no manegen bé aquestes respostes amb aquests codis d'estat, sobretot per seguretat.
* **307 Temporary Redirect \(des HTTP / 1.1\)** . La pàgina sol·licitada s'ha mogut temporalment a un altre URL. En aquest cas el recurs hauria de repetir-se amb una altra URI, però, futurs requests hauran d'usar la URI original. A l'contrari que amb la 302, el mètode request no pot canviar quan es reubique el request original.
* **308 Permanent Redirect \(RFC 7538\)** . El request i futurs requests s'haurien de repetir usant un altre URI. Aquest també és similar a l'301, però no permet a l'mètode HTTP que canviï.

#### 4. 4XX Errors de el client

Excepte quan es respon a un **HEAD request** , el servidor ha d'incloure una entitat que conté una explicació de l'error, i si és temporal o permanent. Són aplicables a qualsevol mètode de sol·licitud \(GET, POST ...\). Els **user agents** han de mostrar qualsevol entitat a l'usuari:

* **400 Bad Request** . El servidor no pot o no va a processar el request per un error de sintaxi de client.
* **401 Unauthorized \(RFC 7235\)** . Similar a l'error 403, però s'usa quan es requereix una autenticació i ha fallat o encara no a facilitat.
* **402 Payment Required** . Reservat per a futur ús. La intenció original va ser per a **pagament amb targeta** o **micropagament** , però això no ha passat, i aquest codi amb prou feines s'usa.
* **403 Forbidden** . El request va ser vàlid però **el servidor es nega a respondre** .
* **404 Not Found** . El recurs de l'request no s'ha pogut trobar però podria estar disponible en el futur. Es permeten **requests subsecuentes** per part de client.
* **405 Method Not Allowed** . S'ha fet un request amb un recurs utilitzant un **mètode request no suportat** per aquest recurs \(per exemple usant GET en un formulari que requereix POST\).
* **406 Not Acceptable** . El recurs sol·licitat només genera **contingut no acceptat** d'acord amb els headers Accept enviats al request.
* **407 Proxy Authentication Required \(RFC 7235\)** . El client s'ha d'identificar primer amb el **proxy** .
* **408 Request Timeout** . El client no ha enviat un **request amb el temps necessari** amb el qual el servidor estava preparat per esperar. El client podria repetir el request sense modificacions més tard.
* **409 Conflict** . Conflicte al request, com quan s'actualitzen a el mateix temps dos recursos.
* **410 Gone** . El recurs sol·licitat no està disponible ni ho estarà en el futur. **Un cercador eliminarà abans una pàgina 410 que una 404** .
* **411 Length Required** . El request no va especificar la **longitud de l'contingut** , la qual és requerida pel recurs sol·licitat.
* **412 precondition Failed \(RFC 7232\)** . El servidor no compleix una de les precondicions que el requester afegeix al request.
* **413 Request Entity Too Large** . El request és més llarg que el que està disposat a acceptar el servidor.
* **414 Request-URI Too Long** . L' **URI** és molt llarg perquè el servidor ho processi.
* **415 Unsupported Mitjana Type** . L'entitat request té un **mitjana type** que el servidor o recurs no suporten.
* **416 Requested Range Not satisfiable \(RFC 7233\)** . El client ha sol·licitat una porció d'arxiu, però el servidor no pot oferir aquesta porció.
* **417 Expectation Failed** . El servidor no pot complir els requeriments de l'header de l'request Expect.
* **418 I'm a teapot \(RFC 2324\)** . Va ser part d'un [April Fool 's day](http://en.wikipedia.org/wiki/April_Fools%27_Day_Request_for_Comments) , i no s'espera que s'implementi en servidors HTTP. La RFC especifica que aquest codi hauria de ser retornat per teteres per servir te.

#### 5. 5XX Errors de servidor

El servidor ha fallat a l'completar una sol·licitud aparentment vàlida. Quan els codis d'estat comencen per 5 indica casos en què **el servidor sap que té un error** o realment és **incapaç de processar el request** . Excepte quan es tracta d'un request **HEAD** , el servidor ha d'incloure una entitat contenint 1 **explicació de l'error** , i de si és temporal o permanent. Igualment els utilitzar-agents hauran de mostrar qualsevol entitat a l'usuari. Aquests codis de resposta s'apliquen a qualsevol mètode request.

* **500 Internal Server Error** . Error genèric, quan s'ha donat una condició no esperada i no es pot concretar el missatge.
* **501 Not Implemented** . El servidor o no reconeix el **mètode de l'request** o no té la capacitat per completar-lo. Normalment és una cosa que s'oferirà en el futur, com un **nou servei** d'una **API** .
* **502 Bad Gateway** . El server actuava com a porta d'entrada o **servidor intermediari** i va rebre una resposta no vàlida de servidor upstream.
* **503 Service Unavailable** . El servidor està actualment no disponible, ja sigui per **manteniment** o per **sobrecàrrega** .
* **504 Gateway Timeout** . El servidor estava actuant com **a porta d'entrada** o **servidor intermediari** i no va rebre una resposta oportuna per part de servidor upstream.
* **505 HTTP Version Not Supported** . El servidor no suporta la **versió de el protocol HTTP** utilitzada en el request.
* **511 Network Authentication Required \(RFC 6585\)** . El client necessita autenticar-vos per poder accedir a la xarxa.

## Error Handlers - gestors

Els errors i els gestors d’errors de la programació de programari són un concepte impagable que ajuda als desenvolupadors a identificar punts de fallada en temps de compilació de l’aplicació o en temps d’execució. Poden assenyalar diferents nivells de gravetat.

A PHP, es gestionen i registren els errors mitjançant una sèrie de funcions integrades. Faciliten l’adaptació de la gestió i el registre d’errors per adaptar-se a les necessitats d’una aplicació registrant gestors d’errors personalitzats o configurant informes d’errors per a un rang específic de nivells.

