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
* Adaptar **resposta** al tipus de client (HTML, JSON...)

En el cas de seguir un paradigma MVC (Model View Controller), es poden adoptar els següents elements:

* Reescriptura del REQUEST per fer-lo més amigable (**`.htaccess`**)
* Captura a través del Request del controlador (objecte) i l'acció (mètode)
* Llençament de la instància de l'objecte controlador i posteriorment cridar l'acció corresponent com a mètode del controlador
* Manteniment de la sessió i sistema d'autenticació i autorització
* Sistema de vistes (_views_) per al renderitzat i generació de interfície d'usuari.





## **Funcionament bàsic de l'app ( paradigma MVC i altres ginys)**

#### **Estructura de carpetes i fitxers importants**

```php
/index.php
.env
 config.php
 composer.json
 bootstrap.php
 .htaccess
 vendor/
 src/
     Controllers/
     Database/
     views/
         partials/
 public/
    css/
    img/
    js/
```

La carpeta src/ incorpora les classes bàsiques de funcionament, incloent App que és la classe mestra inicial i l'arrencada del sistema (**bootstrap.php**)

La carpeta _vendor_/ inclou les llibreries externes que utilitzarem, entre elles la característica autoload de PSR-4.

```json
{ 
   "autoload":
   { 
     "psr-4": 
      {
         "App\":"src" }, 
         "files": ["src/helpers.php"] 
      }, 
      "require": { 
           "vlucas/phpdotenv":"^5.3", 
           "psr/container": "^1.0", 
           "ext-pdo": "*" 
        }
  }
```

&#x20;views incorpora totes les vistes php-html que s'utilitzen a les vistes.

public/ incorpora les característiques estàtiques i de front-end de l'aplicació.

### **El Cicle REQUEST - RESPONSE**

Si seguim el cicle entre client i servidor, podem analitzar com es desenvolupa la resposta, aquesta es dona sempre a nivell de Controlador, ja que estem fent servir paradigmes MVC.

![Cicle Request abans d'arribar al controlador](../.gitbook/assets/cicle-request.png)

### **1 Reescriptura de la URI**

**.htaccess**

```
Options +FollowSymLinks

RewriteEngine On

RewriteCond %{REQUEST_FILENAME} -f [OR]
RewriteCond %{REQUEST_FILENAME} -d 

RewriteRule ^.*$ - [NC,L]
RewriteRule ^.*$ index.php [NC,L]
```

Com s'observa, es tracta de redirigir tota consulta (_query_ ) cap al controlador frontal de l'aplicació (**index.php**).

Per una bona càrrega de classes fem servir l'estandar **PSR- 4** (autocàrrega de classes)

**Adaptació a estàndar PSR-4 (autoload) composer.json segons el namespace utlitzat**

```
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

Un cop carregat el PSR-4, definim les constants de l'entorn de l'aplicació, aquestes són extretes del fitxer de configuració **config.json.**

{% hint style="info" %}
**Cal remarcar BASE com a adaptador de rutes en les templates per poder accedir a rutes basades en el DocumentRoot de Apache o el root de nginx.**
{% endhint %}

```php
{
    "conf_dev":{
        "driver":"mysql",
        "dbhost":"127.0.0.1",
        "dbname":"prouf1",
        "dbuser":"prouf1",
        "dbpass":"+++++++++",
        "web":"/"
        
    },
    "conf_pro":{
        "driver":"mysql",
        "dbhost":"toni.cesnuria.com",
        "dbname":"toni_prouf1",
        "dbuser":"toni_prouf1",
        "dbpass":"+++++++++",
        "web":"/m7/todofw/",


    }
}
```

Al fitxer de configuració hi mostrem dos objectes json en funció de si estem en producció conf\__pro o en desenvolupament conf_\_dev.

### 3 App::run()

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
            unset($_SESSION);
            $session=new Session();
            //csrf-token to avoid csrf attacks
             if (!($session->exists('csrf-token'))){
                 $session->set('csrf-token',bin2hex(random_bytes(32)));
             }
            //routes array
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

El mètode init(), proporciona l'array de configuració de l'app. Mentre que run() és en sí el nucli de l'aplicació, ja que determina i activa quin controlador és el responsable de la "request", cal destacar que en la instància del controlador, també injectem els objectes Session i Request, que ens facilita el desenvolupament de l'aplicació com ja s'observarà.

A cada cicle REQ-RESP creem un token aleatori que ens proporcionarà seguretat CSRF als formularis POST.

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

### App

El nucli  o sistema de l'aplicació: que ja vam veure amb anterioritat.

### DB

Accedir a la base de dades és fàcil si fem servir aquesta classe, l'accés a la instància es fa a través del Controller:

**`$this->getDB();`**

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

### FormBuilder

Es tracta de crear de forma semi-automàtica els formularis de la nostra app. Aquests es configuren des del controlador per poder ser renderitzats posteriorment.

```php
namespace App;
    

    class FormBuilder
    {
        private $elements = array();

        public function open($uri,$method=null){
            $method=$method??'POST';
            $this->elements[]="<form method=\"$method\" action=\"$uri\" >";
            return $this;
        }
        public function close(){
            $this->elements[]="</form>";
            return $this;
        }

        public function label($text) {
            $this->elements[] = "<label >$text</label>";
            return $this;
        }   

        public function input($type, $name, $value = null) {
            $value=$value??'';
            $this->elements[] = "<input type=\"$type\" class=\"form-control mb-3\" name=\"$name\" id=\"$name\" value=\"$value\" />";
            return $this;
        }   

        public function textarea($name, $value =null) {
            $value=$value??'';
            $this->elements[] = "<textarea name=\"$name\" id=\"$name\" >$value</textarea>";
            return $this;
        }
        public function submit($value=null){
            $value=$value??'Next';
            $this->elements[] ="<button type=\"submit\" class=\"btn btn-primary m-4\">$value</button>";
            return $this;
        }
        public function csrf($value){
            $this->elements[]="<input type=\"hidden\" name=\"csrf-token\" value=\"$value\">";
           
            return $this;
        }
        /**
         * Returns string when form is completed
         *
         * @return string
         */
        public function __toString() {
            return join("\n", $this->elements);
        }   
    }

```

I quan volem utilitzar el formulari, el configurem abans de renderitzar-lo, mirem l'exemple:

```php
 //UserController
 
  function login(){
            
            $form=$this->createForm();
            $form->open(BASE.'user/log')
                ->label('Email:')
                ->input('email','email')
                ->label('Password:')
                ->input('password','passw')
                ->csrf($this->session->get('csrf-token'))
                ->submit('Sign')
                ->close();

            $this->render([
                'form'=>$form],'login');
        }
```

El procés de configuració consisteix en crear l'objecte **$form**, i després es completa amb tots el items, afegirem també l'atribut ocult csrf-token per protegir-nos d'atacs **CSRF.**



## Procés de resposta

Anem a simular un procés de resposta:

Es tracta de mostrar un "escriptori" un cop s'ha autenticat l'usuari, és a dir provenim d'un procés similar aquest:

```php
'''La navegació en la app:'''

welcome --> login  ----> dashboard ---> add task
        --> register               ---> complete task
                                   ---> remove task
                                   ---> edit task
                                   ---> profile
        USERCONTROLLER          USER/TASKCONTROLLER
```

En l'autenticació el controlador UserController ens porta cap al controlador User i l'acció dashboard:

```php
//class UserController
function log(){
            if (isset($_POST['email'])&&!empty($_POST['email'])
            &&isset($_POST['passw'])&&!empty($_POST['passw']))
            {
                $email=filter_input(INPUT_POST,'email',FILTER_SANITIZE_EMAIL);
                $pass=filter_input(INPUT_POST,'passw',FILTER_SANITIZE_STRING);
            
           
                $user=$this->auth($email,$pass);
                if ($user){
                    $this->session->set('user',$user);
                    //si usuari valid
                    if(isset($_POST['remember-me'])&&($_POST['remember-me']=='on'||$_POST['remember-me']=='1' )&& !isset($_COOKIE['remember'])){
                        $hour = time()+3600 *24 * 30;
                        $path=parse_url($_SERVER['REQUEST_URI'],PHP_URL_PATH);
                        setcookie('uname', $user['uname'], $hour,$path);
                        setcookie('email', $user['email'], $hour,$path);
                        setcookie('active', 1, $hour,$path);          
                    }
                    header('Location:'.BASE.'user/dashboard');
                }
                else{
                    header('Location:'.BASE.'user/login');
                }
            
            }
        }
```

Fixem-nos en la línia 21:

```php
 header('Location:'.BASE.'user/dashboard');
```

Ens crea una REQUEST que demana una cosa similar a **`GET HTTP1.1 /user/dashboard`**

El sistema creat, ens porta a instanciar el controlador UserController i el mètode **dashboard**:

```php
// class UserController
function dashboard(){
            
            $user=$this->session->get('user');
            $data=$this->getDB()->selectAllWithJoin('tasks','users',['tasks.id','tasks.description','tasks.due_date'],'user','id');
            $this->render(['user'=>$user,'data'=>$data],'dashboard');
        }
```

En aquest punt, extreiem de la sessió la clau d'usuari que passarem a la vista a través del mètode render, a l'igual que les dades assignades a aquest usuari.

Mirem la plantilla:

```php
<?php
    include 'header.tpl.php';
    ?>
    <main>
    <section class="container">
        <h3>Todo list <?= $user['uname'];?></h3>
        <div class="row my-auto">
        <table id="mytable" class="table">
            <tr>
            <?php
                if($data){
                $columns=array_keys($data[0]);
                
                foreach ($columns as $field) {
                    echo '<th scope="row">'.$field.'</th>';
                }
                }
                
                ?>
                <th colspan="2"><strong>Actions</strong></th>   
            </tr>
        <?php
            if($data){
                foreach ($data as $row){
                    echo '<tr id="row'.$row["id"].'">';
                    foreach ($row as $column => $value) {
                       echo '<td contenteditable>'.$value.'</td>';
                    }
                    echo '<td><button class="btn btn-primary" id="update'.$row["id"].'" onclick="edit('.$row["id"].')">Update</button></td>';
                    echo '<td><button class="btn btn-danger" id="remove'.$row["id"].'" onclick="remove('.$row["id"].')">Remove</button></td>';
                    echo '</tr>';
                }   
            }
             
        ?>
        </table>
        </div>
        </section>
        <section>
        <a href="/task/new"><button class="btn btn-secondary"><strong>+</strong></button></a>
        </section>
        <section>
            <div id="message"><p></p></div>
        </section>
        
    </main>
    
<?php
    include 'footer.tpl.php';
    ?>

```

El sistema de plantilles creat és una simple composició entre tres scripts que es troben a **templates**. En aquesta plantilla en concret hem passat les dades $user i $data, des del controlador a través d'un array a l'argument de la funció **render** del controlador.

### El controlador

Basat en una classe abstracta:

Proporciona tots els serveis cap als controladors especialitzats, control de request, control de sessió, accés a dades, renderització de vistes i creació de formularis (tema que tractarem més endavant).



```php
<?php

    namespace App;
    use App\View;
    use App\Model;
    use App\DB;
    use App\Session;

    abstract class Controller implements View,Model{
        protected $request;
        protected $session;

        function __construct($request, $session){
            $this->request=$request;
            $this->session=$session;
        }
        
        function createForm(){    
            return new FormBuilder;
        }
        // $this->csrfError() to use in forms processing in Controllers
        function csrfCheck(){
            
            if ($this->request->getMethod() === 'POST') {
                if (!array_key_exists('csrf-token', $_POST)) {
                    throw new Exception();
                  //  echo '<p>ERROR: The CSRF Token was not found in POST payload.</p>';
                } elseif ($_POST['csrf-token'] !== $this->session->get('csrf-token')) {
                    throw new Exception();
                    //echo '<p>ERROR: The CSRF Token is not valid.</p>';
                } else {
                    return true;
                   //echo '<p>OK: The CSRF Token is valid. Will continue with email validation...</p>';
                }
            }
            
        }
        function error($string){
            $this->render(['error'=>$string],'error');
        }
        
        function render(?array $dataview=null,?string $template=null){
            if($dataview){
                extract($dataview,EXTR_OVERWRITE);
            }
            if ($template!=null){
                include 'templates/'.$template.'.tpl.php';
            }else{
                include 'templates/'.$this->request->getController().'.tpl.php';
            }
        }

        function getDB(){
            return DB::singleton();
        }
    }
```

El controlador implementa les interfaces View i Model:

```php
//interface View
<?php

    namespace App;

    interface View{
        public function render(?array $dataview=null,?string $template=null);
       // public function json();
    }
    
 //interface Model
 
    <?php

  namespace App;

   interface Model{
     public function getDB();

  }   
```

Tots els processos segueixen aquest protocol&#x20;

usuari-->-Controlador --->render ---> procés UI --->usuari

