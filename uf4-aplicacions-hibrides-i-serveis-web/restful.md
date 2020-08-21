# Restful

## Introducció a REST

REST a l'igual que altres tecnologies, com Git i CSS, requereix una mica de temps per a la seva comprensió. En aquest breu apartat ens centrarem en la filosofia que està darrere de REST \(així com les seves diferències amb SOAP\), i en els aspectes pràctics. Com podem implementar REST avui?

[ ](http://asiermarques.com/2013/conceptos-sobre-apis-rest/)

### Que és és REST?

**REST**  són les sigles de  **rest** . Va ser definit fa una dècada per Roy Fielding en la seva tesi doctoral, i proporciona una manera senzilla d'interacció entre sistemes, la major part de les vegades a través d'un navegador web i HTTP. Aquesta cohesió amb HTTP ve també que Roy és un dels principals autors d'HTTP.

**REST** és un estil arquitectònic, un conjunt de convencions per a aplicacions web i serveis web, que se centra principalment en la manipulació de recursos a través d'especificacions HTTP. Podem dir que REST és una interfície web estàndard i simple que ens permet interactuar amb serveis web d'una manera molt còmoda.

Gràcies a **REST** la web ha gaudit d'escalabilitat com a resultat d'una sèrie de dissenys fonamentals clau:

* Un  **protocol client/servidor sense estat** : cada missatge HTTP conté tota la informació necessària per comprendre la petició. Com a resultat, ni el client ni el servidor necessiten recordar cap estat de les comunicacions entre missatges. No obstant això, en la pràctica, moltes aplicacions basades en HTTP utilitzen cookies i altres mecanismes per mantenir l'estat de la sessió \(algunes d'aquestes pràctiques, com la reescriptura d'URL, no són permeses per REST\).
* Un  **conjunt d'operacions ben definides**  que s'apliquen a tots els recursos d'informació: HTTP en si defineix un conjunt petit d'operacions, les més importants són  **POST** ,  **GET** ,  **PUT**  i  **DELETE** . Sovint aquestes operacions s'equiparen a les operacions CRUD que es requereixen per a la persistència de dades, encara POST no encaixa exactament en aquest esquema.
* Una  **sintaxi universal per identificar els recursos** . En un sistema REST, cada recurs és adreçable únicament a través del seu URI.
* L'  **ús de hipermitjans** , tant per a la informació de l'aplicació com per a les transicions d'estat de l'aplicació: la representació d'aquest estat en un sistema REST són típicament HTML o XML. Com a resultat d'això, és possible navegar d'un recurs REST a molts altres, simplement seguint enllaços sense requerir l'ús de registres o una altra infraestructura addicional.

  
Anem a veure primer el que són les URIs. Una URI és essencialment un identificador d'un recurs. Vegem el següent exemple:

```text
GET /amics
```

Podríem dir a això un recurs. Quan aquesta ruta és cridada, seguint els patrons REST, s'obtindran **tots** els amics \(generalment d'una base de dades\), i es mostraran en pantalla o es retornaran en un format determinat a qui ho sol·liciti.

Però, com farem si volem especificar un amic en particular ?.

```text
GET /amics/marta
```

  
Com es pot veure és fàcilment comprensible. Aquesta és una de les claus de l'arquitectura RESTful. Permet l'ús de URIs que són fàcilment comprensibles pels humans i les màquines.

Pensa en un recurs com un nom en plural. Contactes, estats, usuaris, fotos --- tots aquests serien eleccions perfectes.

Fins ara, hem vist com identificar una col·lecció ia elements individuals en aquesta col·lecció:

```text
GET /amics 
GET /amics/marta
```

De fet, trobaràs que aquests dos segments són tot el que hauries d'haver necessitat sempre. Però podem aprofundir una mica més en la potència d'HTTP per indicar com volem que el servidor respongui a aquestes peticions. Vegem:

Cada petició HTTP especifica un mètode, o un verb, en les seves capçaleres. Generalment et sonaran un parell d'ells com GET i POST.

Per defecte el verb utilitzat quan accedim o veiem una pàgina web és  **GET** .

Per a qualsevol URI donada, podem fer referència a fins a 4 tipus diferents de mètodes:  **GET, POST, PUT, PATCH i DELETE** .

```text
GET /amics 
POST /amics 
PUT /amics 
DELETE /amics
```

Essencialment, aquests verbs HTTP s'indiquen a servidor de fer amb les dades especificades a l'URI. Una manera fàcil d'associar aquests verbs a les accions realitzades, és comparant-ho amb  **CRUD**  \(Create-Read-Update-Delete\).

```text
GET => LLEGIR 
POST => CREATE 
PUT => ACTUALITZAR 
DELETE => ESBORRAR
```

Anteriorment hem dit que GET és el mètode utilitzat per defecte, però també et hauria de sonar POST. Quan vam enviar dades des d'un formulari a servidor, solem utilitzar el mètode POST. Per exemple si volguéssim afegir nous piulades a la nostra base de dades, el formulari hauria de fer un POST dels tweets POST / tweets, en lloc de fer /tweets/añadirNuevoTweet.php.

Exemples d'URIs que són no **RESTful** i que no es recomana utilitzar:

```text
/tweets/afegirNouTweet.php 
/amigcs/esborrarAmicPerNom.php 
/contactes/actualitzarContacte.php
```

Exemples d'URIs que són RESTful i que serien un bon exemple:

```text
GET /tiquets/12/messages - Retorna una llista de missatges per al tiquet # 12 
GET /tiquets/12/messages/5 - Retorna el missatge # 5 per al tiquet # 12 
POST /tiquets/12/messages - Crea un nou missatge en el tiquet # 12 
PUT /tiquets/12/messages/5 - Actualitza el missatge # 5 per al tiquet # 12 
PATCH /tiquets/12/messages/5 - Actualitza parcialment el missatge # 5 per al tiquet # 12 
DELETE /tiquets/12/messages/5 - Esborra el missatge # 5 per al tiquet # 12
```

**Però llavors, quines serien les URIs correctes per presentar un formulari a l'usuari, amb l'objectiu d'afegir o editar un recurs?**

En situacions com aquesta, té més sentit afegir URIs com:

```text
GET /amics/nou 
GET /amics/marta/editar
```

La primera part de la trajectòria / amics / nou, hauria de presentar un formulari a l'usuari per afegir un amic nou. Immediatament després d'enviar el formulari, hauria d'usar una sol·licitud POST, ja que estem afegint un nou amic.

Per al segon cas / amics / marta / editar, aquest formulari hauria editar un usuari existent a la base de dades. Quan actualitzem les dades d'un recurs, s'hauria d'utilitzar una sol·licitud PUT.

**Més informació de com anomenar les URI en una API REST:**

[http://www.restapitutorial.com/lessons/restfulresourcenaming.html](http://www.restapitutorial.com/lessons/restfulresourcenaming.html)

**Un altre llibre recomanable sobre RESTful** :  [http://restcookbook.com/](http://restcookbook.com/)

### Verbs disponibles a REST

Abans de seguir endavant amb exemples concrets, anem a revisar una mica més els verbs utilitzats en les peticions a una API REST.

#### GET \(Recuperar\)

GET és el mètode HTTP utilitzat per defecte en les peticions web. Un advertiment a tenir en compte és que hauríem d'utilitzar GET, per fer peticions només de lectura, i hauríem d'obtenir sempre el mateix tipus de resultat, independentment de les vegades que sigui cridat aquest mètode.

Com a programador pots fer el que vulguis quan es fa una crida a les rutes a la URI, però una bona pràctica és seguir les regles generals per dissenyar una API REST correctament.

#### POST \(crear\)

El segon mètode que et resultarà familiar és POST. S'utilitzarà per indicar que anem a crear un subconjunt de el recurs especificat, o també si estem actualitzant un o més subconjunts de el recurs especificat.

Per exemple crearem un recurs nou per exemple enviant un nou usuari per donar-lo d'alta, llavors ho farem a la URL / amics

```text
  POST /amics
```

#### PUT \(Actualització\)

Utilitza PUT quan vulguis actualitzar un recurs específic a través del seu localitzador a la URL.

Per exemple si un article està a la URL  [http://web.local/api/articles/1333](http://web.local/api/articles/1333) , podem actualitzar aquest recurs enviant tots els camps a actualitzar des d'un formulari \(mètode POST\):

```text
    PUT /api/articles/1333
```

Si no coneixem l'adreça de el recurs actual, for exemple per afegir un nou article, llavors utilitzaríem l'acció POST. Per exemple.

```text
    POST /api/articles
```

#### DELETE \(Esborrar\)

Finalment DELETE hauria es usat quan volem esborrar el recurs especificat a la URI. Per exemple si ja no som més amics de macarena, seguint els principis de REST, podríem esborrar-la fent servir una petició delete a la URI:

```text
  DELETE /amics/carme
```

#### PATCH \(Actualitzacions parcials\)

Una sol·licitud de tipus PATCH s'utilitza per realitzar una actualització parcial d'un recurs és a dir per actualitzar certs camps de el recurs i no el recurs a l'complet. Els camps a actualitzar s'enviarien des d'un formulari per POST i el tipus de petició és PATCH.

```text
    PATCH /api/articles/1333
```

#### HEAD \(Sol·licitud de capçaleres\)

* Una sol·licitud de tipus HEAD és com una sol·licitud GET, amb l'excepció que només es retornen les capçaleres HTTP i el codi de resposta, no el document en si mateix.
* Amb aquest mètode el navegador pot comprovar si un document ha estat modificat, per temes de memòria cau per exemple. També pot comprovar si un document existeix o no.
* Per exemple, si tens un munt d'enllaços a la teva web, periòdicament podries comprovar mitjançant peticions HEAD si els hiperenllaços són correctes o estan trencats. Aquest tipus de comprovació és moltíssim més ràpid que utilitzar GET.

