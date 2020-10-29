---
description: Aplicació amb registres i autenticació d'usuaris.
---

# Cas pràctic

## 1a Aplicació en PHP i aproximació a MVC

Anem a veure un exemple pràctic del que hem estudiat fins ara.

L'arquitectura web determina que un dels models arquitectònics \( la forma de fer les coses web \) més utilitzats en desenvolupament és el que segueix la separació en tres capes, **client - servidor i dades**. El paradigma de programació MVC \(Model - Vista - Client\), és el que millor s'aproxima a aquest model de tres capes.

Mireu l'esquema:

```php
Client  --------->> Servidor ----------->> Dades
Frontend  -------> Backend  ----------->> Bases de dades
Vistes   --------->  Controlador -------->> Model
HTML/CSS/JS ------>   PHP  --------------->> Mysql

REQUEST  -->-------------+++-------------|
RESPONSE <-----------------+++------- -----|
```

Com es veu, es troben moltes relacions, fins i tot a nivell tecnològic, comproveu la línia 4.

I segons la línia 3

#### **Controlador**

Encarregat de la gestió de la consulta REQUEST.

#### Vista

Encarregat de la visualització i interacció amb el client

#### Model

Encarregat de satisfer les demandes de dades persistents

### Desnvolupament pràctic

Punts a tenir en compte:

* _Separació de codi \(vista, fitxers i funcions \)_
* Connexió a base de dades tant en mysql i/o sqlite, opció configurable.
* Les accions de la nostra aplicació estaran en els scripts de la carpeta _controllers_

Cal preparar l'estructura en carpetes de l'aplicació:

```text
.htaccess
index.php
schemagen.php
--/controllers
   login.php
   logaction.php
   home.php
   register.php
   regaction.php
   dashboard.php
--/src
   render.php
   db.php
   |--/database
         database.sqlite
   |--/templates
          header.tpl.php
          footer.tpl.php
          dashboard.tpl.php
          login.tpl.php
          register.tpl.php
          home.php.tpl.php
       
```

A connect.php hi posarem la connexió PDO, ja sigui mysql o bé sqlite \(depenent de les extensions\)

**`index.php`**, arrenca l'aplicació i carrega directament el controlador **home,** així com l'activació de la sessió.

```php
 <?php   
    //configuracio entorn
    session_start();
    //constant APP
    define('APP',__DIR__);
   // carreguem gestor de rutes    
    require APP.'/src/route.php';
    //enrutament
    $controller=getRoute();
    //redirigir a ruta capturada
    require APP.'/controllers/'.$controller.'.php';

```

La **navegació** interna de l'app és la següent:

```php
.htaccess ==> index.php >>> (enrutament) >>> controller 
request ------------------------------------> response
```

**En alguns casos necessitem reescriure i adaptar la REQUEST , .htaccess se n'ocupa d'això. Index.php**  localitza el _controller_ en funció de la ruta indicada a través del REQUEST, concretament podem agafar un paràmetre de la QUERY\_STRING. Ara cal cridar al controller i aquest farà les feines o accions corresponents.

L'estructura de la REQUEST és similar a la figura:

```php
QUERY_STRING
http://app/index.php?url=controlador
```

El sistema d'enrutament es basa en capturar el paràmetre url de la query\_string i el processa per tal d'obtenir el controlador que s'encarregarà de gestionar la REQUEST.

### Entorn

Al fitxer de configuració podem afegir els valors d'entorn de la nostra app.

```php
<?php
   
    // valors d'accés a la base de dades en Mysql
    $dbhost="127.0.0.1";
    $dbname='prova';
    $dbuser='prova';
    $dbpass='++++++++';
    $dsn='mysql:host='.$dbhost.';dbname='.$dbname.';';
    $driver='mysql';
```

### La connexió a la base de dades

Volem que la nostra activitat revisi la connexió a la base de dades amb dos drivers o controladors, amb sqlite i amb mysql. Òbviament les connexions seran diferents:

En el cas de **SQLITE**, es genera un fitxer en la ubicació indicada en el argument del PDO.

En el cas de **Msyql**, requerim un objecte de connexió PDO.

```php
//db.php
<?php

    function connectSqlite(string $dbname){
        try{
            $db=new PDO('sqlite:'.__DIR__.'/database/'.$dbname.'.sqlite');
            $db->setAttribute(PDO::ATTR_ERRMODE,PDO::ERRMODE_EXCEPTION);
            $db->setAttribute(PDO::ATTR_DEFAULT_FETCH_MODE,PDO::FETCH_ASSOC);

        }catch(PDOException $e){
            die($e->getMessage());
    
        }
        return $db;

    }

    function connectMysql(string $dsn,string $userdb,string $passdb){
        try{
            $db = new PDO($dsn, $userdb, $passdb);
            $db->setAttribute(PDO::ATTR_ERRMODE,PDO::ERRMODE_EXCEPTION);
            $db->setAttribute(PDO::ATTR_DEFAULT_FETCH_MODE,PDO::FETCH_ASSOC);

        }catch(PDOException $e){
            die( $e->getMessage());
            
        }
        return $db;
    }
```

{% hint style="info" %}
Per  gestionar la creació de la taula que utilitza la nostra app, podem utilitzar una funció **schemaGen\(\)**, que en esència , executa un comando de creació de taula, distingim també entre sqlite i mysql.
{% endhint %}

### **Controladors**

Estem seguint el paradigma MVC, Model Vista i Controlador. La gestió de la consulta la fa sempre el controlador, el qual actua a través de les accions \(funcions\), que  en temes posteriors tractarem com a _objectes_  i **mètodes**.

{% hint style="info" %}
Els controladors són: encarregats de les accions de les pantalles o vistes, per exemple  si tenim vista **home**, també tindrem controlador **home**.
{% endhint %}

A més el controlador passa dades a vista renderitzada com es pot comprovar a l'exemple:

```php
//controlador home.php
<?php
    //render vista

   require APP.'/src/render.php';
   // si està definida la sessió
   $uname=$_SESSION['uname'] ?? '';
   echo render('home',['title'=>'Home '.$uname]);
```

### Renderitzat de plantilles

La forma més elegant de treballar les vistes en PHP és separar codi PHP de codi HTML. Amb aquest mètode que presentem, es pot separar perfectament, ubicant una carpeta  **`templates`** amb plantilles renderitzables.

```php
<?php

    function render(string $tpl,?array $data=[]):string 
    {
        if($data){
            extract($data,EXTR_OVERWRITE);
        }
        ob_start();
        require 'src/templates/'.$tpl.'.tpl.php';
        $rendered=ob_get_clean();
        return (string)$rendered;
    }
```

El responsable del renderitzat és el controlador.  En el renderitzat, fem servir els ob \(**output-buffer**\). La seva missió és desar les dades que arriben a aquest buffer \( el fitxer de plantilla barrejat amb les dades extretes de $data\) i per últim retornar l'string que el controlador s'encarrega de mostrar :

```php
echo render('home',['title'=>'Home '.$uname]);
```

### Entrutament

I si volem més complexitat en el sentit de poder tenir més opcions d'accions i vistes? Ens queda crear un controlador frontal que discrimini segons les rutes capturades...Una cosa similar a això:

```php
//Fitxer src/route.php
// rutes http://app?url=controlador
<?php
    
    function getRoute():string
    {
        if(isset($_REQUEST['url'])){
             $url=$_REQUEST['url'];
        }else{
            $url="home";
        }
           
        switch ($url){
            case 'login': 
                return 'login';
            case 'register': 
                return 'register';
            case 'regaction': 
                return "regaction";
            case 'logaction': 
                return "logaction";
            case 'logout': 
                return "logout";
            default: 
                return 'home';
        }
            
    }
```

I per què sigui efectiva recurrirem a que sempre s'ha de consultar al index.php per saber quina ruta cal interpretart \(**front controller**\). Necessitem un .htaccess que reescrigui les URL:

```php
Options +FollowSymLinks

RewriteEngine On

RewriteCond %{REQUEST_FILENAME} -f [OR]
RewriteCond %{REQUEST_FILENAME} -d 

RewriteRule ^(.*)$ index.php [NC,L]
```

Si es demana per "algo" que no és ni fitxer ni directori, reescrivim la **REQUEST\_URI** per reenviar-la al index.php.

### Gestió de dades persistents

La gestió de dades persistents la podem fer a través de l'objecte PDO de dades en PHP.

En el nostre exemple, l'aplicació només realitzarà autenticació i registres d'usuaris.

Comencem per la taula d'usuaris, connectada i relacionada amb la taula roles si volem afegir característiques de privilegis d'ús dins l'aplicació.

```php
CREATE TABLE `roles` (
	`id` INT NOT NULL AUTO_INCREMENT,
	`role` varchar(100) NOT NULL UNIQUE,
	PRIMARY KEY (`id`)
);

CREATE TABLE `users` (
	`id` INT NOT NULL AUTO_INCREMENT,
	`email` varchar(100) NOT NULL UNIQUE,
	`uname` varchar(100) NOT NULL UNIQUE,
	`passw` varchar(120) NOT NULL,
	`role` INT NOT NULL,
	PRIMARY KEY (`id`)
);

ALTER TABLE `users` ADD CONSTRAINT `users_fk0` FOREIGN KEY (`role`) REFERENCES `roles`(`id`);

```



