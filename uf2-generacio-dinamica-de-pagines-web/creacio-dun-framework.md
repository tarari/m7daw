---
description: >-
  Millorar la comprensió del procediments a través de la construcció d'un
  framework
---

# Creació d'un framework

## Introducció

Aprofundim el coneixement de la programació orientada a objecte a través de la creació d'un marc de treball propi adaptant una aplicació.

En les aplicacions web és important resoldre els següents problemes :

* Captura del **REQUEST** del client
* Adaptar **resposta** al tipus de client \(HTML, JSON...\)

En el cas de seguir un paradigma MVC \(Model View Controller\), es poden adoptar els següents elements:

* Reescriptura del REQUEST per fer-lo més amigable \(**`.htaccess`**\)
* Captura a través del Request del controlador \(objecte\) i l'acció \(mètode\)
* Llençament de la instància de l'objecte controlador i posteriorment cridar l'acció corresponent com a mètode del controlador
* Manteniment de la sessió i sistema d'autenticació i autorització
* Sistema de plantilles per al renderitzat i generació de interfície d'usuari.





## **Funcionament bàsic de l'app \( paradigma MVC a nivell web\)**

#### **Estructura de carpetes i fitxers**

```php
/index.php
 config.json
 composer.json
 vendor/
 src/
 templates/
 public/
    css/
    img/
    js/
```

La carpeta src/ incorpora les classes bàsiques de funcionament, incloent App que és la classe mestra inicial.

La carpeta vendor/ inclou únicament la característica autoload de PSR-4.

templates/ incorpora totes les plantilles php-html que s'utilitzen a les vistes.

public/ incorpora les característiques estàtiques i de front-end de l'aplicació.

### **El Cicle REQUEST - RESPONSE**

Si seguim el cicle entre client i servidor, podem analitzar com es desenvolupa la resposta, aquesta es dona sempre a nivell de Controlador, ja que estem fent servir paradigmes MVC.

![Cicle Request abans d&apos;arribar al controlador](../.gitbook/assets/cicle-request.png)

### **1 Reescriptura de la URI**

**.htaccess**

```text
Options +FollowSymLinks

RewriteEngine On

RewriteCond %{REQUEST_FILENAME} -f [OR]
RewriteCond %{REQUEST_FILENAME} -d 

RewriteRule ^.*$ index.php [NC,L]
```

Com s'observa, es tracta de redirigir tota consulta \(_query_ \) cap al controlador frontal de l'aplicació \(**index.php**\).

Per una bona càrrega de classes fem servir l'estandar **PSR- 4** \(autocàrrega de classes\)

**Adaptació a estàndar PSR-4 \(autoload\) composer.json segons el namespace utlitzat**

```text
{
    "autoload": {
        "psr-4":{
            "App\\": "src/"
        }
    }
```

### 2 Front controller  - index.php

El fitxer index.php del nostre projecte actua com a un frontend controller és a dir, totes les Requests, són dirigides a aquest script.

```php
<?php

    ini_set('display_errors','On');
   

    require __DIR__.'/vendor/autoload.php';
    
    use App\App;
    
    $conf=App::init();
    //constants d'enrutament i BBDD
    define('BASE',$conf['web']);
    define('ROOT',$conf['root']);
    define('DSN',$conf['driver'].':host='.$conf['dbhost'].';dbname='.$conf['dbname']);
    define('USR',$conf['dbuser']);
    define('PWD',$conf['dbpass']);

    App::run();
    
```

Un cop carregat el PSR-4, definim les constants de l'entorn de l'aplicació, aquestes són extretes del fitxer de configuració **config.json**

```php
{
    "conf_dev":{
        "driver":"mysql",
        "dbhost":"127.0.0.1",
        "dbname":"prouf1",
        "dbuser":"prouf1",
        "dbpass":"+++++++++",
        "web":"http://localhost:8000/",
        "root":"/"
    },
    "conf_pro":{
        "driver":"mysql",
        "dbhost":"toni.cesnuria.com",
        "dbname":"toni_prouf1",
        "dbuser":"toni_prouf1",
        "dbpass":"+++++++++",
        "web":"/m7/todofw/",
        "root":"/m7/todofw/"

    }
}
```

Al fitxer de configuració hi mostrem dos objectes json en funció de si estem en producció conf\__pro o en desenvolupament conf_\_dev.

### 3 App::run\(\)

Observem la classe src/App.php

```php
src/App.php
<?php

namespace App;

   use App\Request;
   use App\Session;

   final class App{
        static protected $action;
        static protected $req;

        private static function env(){
            $ipAddress=gethostbyname($_SERVER['SERVER_NAME']);
            if($ipAddress=='127.0.0.1'){
                return 'dev';
            }else{
                return 'pro';
            }
        }
        private static function loadConf(){
            $file="config.json";
            $jsonStr=file_get_contents($file);
            $arrayJson=json_decode($jsonStr);
            return $arrayJson;
        }
        static function init(){
            //read configuration
            $config=self::loadConf();
            //determinar env pro o dev
            $strconf='conf_'.self::env();   
            $conf=(array)$config->$strconf;
            return $conf;

        }
        public static function run(){

            $session=new Session();
            $routes=self::getRoutes();
           
            
            // obtenir tres parametres: controlador, accio,[parametres]
            // url friendly :  http://app/controlador/accio/param1/valor1/param2/valor2
            self::$req=new Request;
            $controller=self::$req->getController();
            
        
            self::$action=self::$req->getAction();
            
            self::dispatch($controller,$routes,$session);

        }
        
        private static function dispatch($controller,$routes,$session):void 
        {
            
            try{
                if(in_array($controller,$routes)){
                   
                   $nameController='\\App\Controllers\\'.ucfirst($controller).'Controller';
                   $objContr=new $nameController(self::$req,$session);
                   
                   //comprovar si existeix l'acció com mètode a l'objecte
                   if (is_callable([$objContr,self::$action])){
                       call_user_func([$objContr,self::$action]);
                   }else{
                       call_user_func([$objContr,'error']);
                   }

               }else{
                    throw new \Exception("Ruta no disponible");
                }
           }catch(\Exception $e){
               die($e->getMessage());
           }
        }
        /**
         *  @return array
         *  returns registered route array
         */
        static function getRoutes(){
            $dir=__DIR__.'/Controllers';
            $handle=opendir($dir);
            while(false !=($entry=readdir($handle))){
               if($entry!="." && $entry!=".."){
                   $routes[]=strtolower(substr($entry,0,-14));
               }
               
            } 
            return $routes;
        }
           
    
   }
```

El mètode init\(\), proporciona l'array de configuració de l'app. Mentre que run\(\) és en sí el nucli de l'aplicació, ja que determina i activa quin controlador és el responsable de la "request", cal destacar que en la instància del controlador, també injectem els objectes Session i Request, que ens facilita el desenvolupament de l'aplicació com ja s'observarà.

## Elements de la carpeta src/

### Request

```php
<?php

    namespace App;

    class Request{
        private  $controller;
        private  $action;
        private $method;
        private  $params;

        protected $arrURI;
        
        function __construct(){
            $requestString=\htmlentities($_SERVER['REQUEST_URI']);
            //adaptar el sistema root a domini o carpeta
            $reqStr=$this->get_diff($requestString,ROOT);  
            //extract URI
            $this->arrURI=explode('/',$reqStr);
           
            $this->extractURI();
        }

        private function get_diff($a, $b){
            $c=substr($a,strlen($b));
            return $c;  
        }

        private function extractURI():void{     
            $length=count($this->arrURI);
            //estudi de casos possibles
            switch($length){
                case 1: //only controller
                    if($this->arrURI[0]==""){
                        $this->setController('index');
                    }else{
                        $this->setController($this->arrURI[0]);
                    }
                    $this->setAction('index');
                    break;
                case 2: //controller & action
                    $this->setController($this->arrURI[0]);
                    if($this->arrURI[1]==""){
                        $this->setAction('index');
                    }else{
                        $this->setAction($this->arrURI[1]);
                    }
                break;
                default: // cont. & act & params
                    $this->setController($this->arrURI[0]);
                    $this->setAction($this->arrURI[1]);
                    $this->Params();
                break;
            }
            $this->setMethod(\htmlentities($_SERVER['REQUEST_METHOD']));

        }
        
        private function Params():void{
            if($this->arrURI!=null){
                $arr_length=count($this->arrURI);
                if($arr_length > 2){
                    //quitar contr, y accion
                    array_shift($this->arrURI);
                    array_shift($this->arrURI);
                    $arr_length=count($this->arrURI);
                    if($arr_length % 2 == 0){
                        for($i=0;$i<$arr_length;$i++){  
                            if($i%2 == 0){
                                $arr_k[]=$this->arrURI[$i];
                            }else{
                                $arr_v[]=$this->arrURI[$i];
                            }
                        }
                        $array_res=array_combine($arr_k,$arr_v);
                        $this->setParams($array_res);
                    }

                }

            }
        }

        public function getController(){
            return $this->controller;
        }
        public function setController($controller){
            $this->controller=$controller;
        }
        public function getAction(){
            return $this->action;
        }
        public function setAction($action){
            $this->action=$action;
        }
        public function getMethod(){
            return $this->method;
        }
        public function setMethod($method){
            $this->method=$method;
        }
        public function getParams(){
            return $this->params;
        }
        public function setParams($array){
            $this->params=$array;
        }
    }
```

### Session

```php
<?php

    namespace App;

    final class Session {
        protected $id;
        public function __construct() {
    
            $status = session_status();
    
            if($status == PHP_SESSION_DISABLED) {
                throw new \LogicException('Sessions are disabled.');
            }
                if($status == PHP_SESSION_NONE) {
                session_start();
                $this->id=session_id();
            }
        }
    
        /**
         * Gets a session value associated with the specified key.
         *
         * @param string $key
         *
         * @return mixed|null Returns the value on success. NULL if the key doesn't exist.
         */
        public function get($key) {
            if(array_key_exists($key, $_SESSION)) {
                return $_SESSION[$key];
            }
            return null;
        }
    
        /**
         * Set a new session elements or update an existing one.
         *
         * @param string $key
         * @param mixed  $value
         */
        public function set($key, $value) {
            $_SESSION[$key] = $value;
        }
        public function unset($key){
            if ($this->exists($key)){
                unset($_SESSION[$key]);
            }
        }
    
        /**
         * Deletes a session element.
         *
         * @param string $key
         *
         * @return bool
         */
        public function delete($key) {
            if(array_key_exists($key, $_SESSION)) {
                unset($_SESSION[$key]);
                return true;
            }
            return false;
        }
    
        /**
         * Determines if a session key exists.
         *
         * @param string $key
         *
         * @return bool
         */
        public function exists($key) {
            return array_key_exists($key, $_SESSION);
        }

        public function destroy(){
            session_destroy();
        }
    
    }
```

### DB

```php
<?php

    namespace App;

    class DB extends \PDO{
        static $instance;
        protected  $config;

        static function singleton(){
            if(!(self::$instance instanceof self)){
                self::$instance=new self();
            }
            return self::$instance;
        }

        public function __construct(){
            parent::__construct(DSN,USR,PWD);
        }
        
       
        // Db functions
        function insert($table,$data):bool 
        {
           if (is_array($data)){
              $columns='';$bindv='';$values=null;
                foreach ($data as $column => $value) {
                    $columns.='`'.$column.'`,';
                    $bindv.='?,';
                    $values[]=$value;
                }
                $columns=substr($columns,0,-1);
                $bindv=substr($bindv,0,-1);
                
                
               
                $sql="INSERT INTO {$table}({$columns}) VALUES ({$bindv})";
                
                    try{
                        $stmt=self::$instance->prepare($sql);
    
                        $stmt->execute($values);
                    }catch(\PDOException $e){
                        echo $e->getMessage();
                        return false;
                    }
                
                return true;
                }
                return false;
            }
    
            function selectAll($table,array $fields=null):array
            {
                if (is_array($fields)){
                    $columns=implode(',',$fields);
                    
                }else{
                    $columns="*";
                }
                
                $sql="SELECT {$columns} FROM {$table}";
               
                $stmt=self::$instance->prepare($sql);
                $stmt->execute();
                $rows=$stmt->fetchAll(\PDO::FETCH_ASSOC);
                return $rows;
            }
    
            function selectAllWithJoin($table1,$table2,array $fields=null,string $join1,string $join2):array
            {
                if (is_array($fields)){
                    $columns=implode(',',$fields);
                    
                }else{
                    $columns="*";
                }
               
                $inners="{$table1}.{$join1} = {$table2}.{$join2}";
                
                $sql="SELECT {$columns} FROM {$table1} INNER JOIN {$table2} ON {$inners}";
                
                $stmt=self::$instance->prepare($sql);
                $stmt->execute();
                $rows=$stmt->fetchAll(\PDO::FETCH_ASSOC);
                return $rows;
            }
            // només una condició
            function selectWhereWithJoin($table1,$table2,array $fields=null,string $join1,string $join2,array $conditions):array
            {
                if (is_array($fields)){
                    $columns=implode(',',$fields);
                    
                }else{
                    $columns="*";
                }
               
                $inners="{$table1}.{$join1} = {$table2}.{$join2}";
                $cond="{$conditions[0]}='{$conditions[1]}'";
                
            $sql="SELECT {$columns} FROM {$table1} INNER JOIN {$table2} ON {$inners} WHERE {$cond} ";
            
                
                $stmt=self::$instance->prepare($sql);
                $stmt->execute();
                $rows=$stmt->fetchAll(\PDO::FETCH_ASSOC);
                return $rows;   
            }
    
            function update(string $table, array $data,array $conditions)
            {
                if ($data){
                    $keys=array_keys($data);
                    $values=array_values($data);
                    $changes="";
                    for($i=0;$i<count($keys);$i++){
                        $changes.=$keys[$i].'='.$values[$i].',';
                    }
                    $changes=substr($changes,0,-1);
                    $cond="{$conditions[0]}='{$conditions[1]}'";
                    $sql="UPDATE {$table} SET {$changes} WHERE {$cond}";
                    $stmt=self::$instance->prepare($sql);
                    $res=$stmt->execute();
                    if($res){
                        return true;
                    }    
                }else{
                    return false;
                }
                
    
            }
    
            function remove($tbl,$id){
            
                $sql="DELETE FROM {$tbl} WHERE id=$id";
                $stmt=self::$instance->prepare($sql);
                $res=$stmt->execute();
                if($res){
                    return true;
                }
                else{
                    return false;
                }    
            }
    }
```
