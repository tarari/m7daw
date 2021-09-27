---
description: Accés a bases de dades
---

# PHP PDO

L'extensió _Objectes de Dades de PHP_ \( PDO per les seves sigles en anglès\) defineix una interfície i capa d'abstracció lleugera per poder accedir a bases de dades en PHP. Cada controlador de bases de dades que implementi la interfície PDO \(**sqlite, mysql, sqlserver, pgsql\)** pot exposar característiques específiques de la base de dades, com les funcions habituals de l'extensió. 

Cal instal·lar previament l'extensió necessària per a cada cas.

## Crear connexió i comprovar status

Comencem per la creació d'un connector a la base de dades, en aquest exemple exposem dos tipus, sqlite i mysql.

```php
try{
    //Crear connexio
    $db = new PDO('mysql:host=localhost;dbname=DATABASENAME;charset=utf8mb4', 'USERNAME', 'PASSWORD');
    // cas que sigui sqlite
    $db = new PDO('sqlite:database.squlite');
    $db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);//https://www.php.net/manual/en/pdo.setattribute.php
    // com extreiem dades (en array associatiu)
    $db->setAttribute(PDO::ATTR_DEFAULT_FETCH_MODE, PDO::FETCH_ASSOC);
    //Comprovar connection status
    echo $db->getAttribute(PDO::ATTR_CONNECTION_STATUS);
} catch (PDOException $e){
    echo $e->getMessage();
    }

```

## Select en bucle

Com fem la selecció de registres? habitualment preparem la consulta i recuperem resultats amb **fetch**.

**Esquema de funcionament:**

{% hint style="info" %}
_`Preparació de sentència --> execute() --> FETCH (recòrrer resultats)`_
{% endhint %}



Per evitar atacs d'injecció de codi fem servir el mètode **`prepare`** que preprocessa la sentència abans d'executar-la.

```php
//SELECT amb bucle 
$stmt = $db->prepare("SELECT col,col2 FROM table");
$stmt->execute();
while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
    $db_col = $row['col'];
    $db_col2 = $row['col2'];
    echo "$db_col $db_col2<br>";
}
```

## Select amb un valor WHERE

**Esquema de funcionament:**

Preparació de sentència --&gt; execute\(\[array associatiu de paràmetres ":" i valors\]\) --&gt; FETCH o FETCHALL \(recòrrer resultats\).

Col1, col2 i col3 són els camps o columnes que extreiem al resultat.

```php
//SELECT amb  WHERE un sol valor per paràmetre
$uid = 1610;
$stmt = $db->prepare("SELECT col,col2,col3 FROM `table` WHERE `uid` = :uid LIMIT 1;");
$stmt->execute([':uid' => $uid]);
$row = $stmt->fetchAll(PDO::FETCH_ASSOC);
$col = $row[0]['col'];
```

## Insert \(registre\)

Com afegim registres a la nostra base de dades?

**Esquema de funcionament:**

Preparació de sentència --&gt; **bind parameters**-&gt; execute\(\)

L'operació **bind**, permet separar php de la consulta sql, això dona més seguretat ja que evita injecció de codi SQL a la nostra sentència.

```php
// Prepare
$stmt = $db->prepare("INSERT INTO clients (nom, ciutat) VALUES (?, ?)");
// Bind
$nom = "Pete";
$ciutat = "BCN";
$stmt->bindParam(1, $nom);
$stmt->bindParam(2, $ciutat);
// Excecute
$stmt->execute();
// Bind
$nombre = "Marta";
$ciudad = "Paris";
$stmt->bindParam(1, $nom);
$stmt->bindParam(2, $ciutat);
// Execute
$stmt->execute();
```

En aquest exemple, fem bind amb paràmetres per posició segons on es troba el **`?`**.

També podríem haver passat paràmetres directament a través d'un array al execute:

```php
$stmt = $dbh->prepare("INSERT INTO Clients (nom, ciutat) VALUES (:nom, :ciutat)");
$nome = "Toni";
$ciutat = "Barcelona";
if($stmt->execute([':nom'=>$nom, ':ciutat'=>$ciutat]) ){
    echo "Nou registre!";
}
```

## Post PDO

Resulta molt edificant "fabricar-se" una llibreria de funcions més utilitzades a PDO. Segur que ens facilita i molt la feina. Intentem fer abstracció de les funcions que podem necessitar en els nostres projectes i desenvolupem-les:

Us deixo aquí una mostra del que podem fer:

```php
<?php
  /**
  * retorna objecte de connexió
  *
  */
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
    /**
    * retorna objecte de connexió
    * @param: string $dsn,string $userdb,string $passdb
    * @return PDO
    */

    function connectMysql(string $dsn,string $userdb,string $passdb){
        try{
            $db = new PDO($dsn, $userdb, $passdb);
            $db->setAttribute(PDO::ATTR_ERRMODE,PDO::ERRMODE_EXCEPTION);
            $db->setAttribute( PDO::ATTR_EMULATE_PREPARES, false );
            $db->setAttribute(PDO::ATTR_DEFAULT_FETCH_MODE,PDO::FETCH_ASSOC);

        }catch(PDOException $e){
            die( $e->getMessage());
            
        }
        return $db;
    }
    // funció d'autenticació
    // comprova si existeix usuari i password
    // i crea variables de sessió, això podria estar fora
    function auth($db,$email,$pass):bool
    {
        
        try{   
            
            $stmt=$db->prepare('SELECT * FROM users WHERE email=:email LIMIT 1');
            $stmt->execute([':email'=>$email]);
            $count=$stmt->rowCount();
            $row=$stmt->fetchAll(PDO::FETCH_ASSOC);  
            
            if($count==1){       
                $user=$row[0];
                $res=password_verify($pass,$user['passw']);
               
                if ($res){
                $_SESSION['uname']=$user['uname'];
                $_SESSION['email']=$user['email'];
           
                    return true;
                }else{
                    return false;
                }
            }else{
                return false;
            }
        }catch(PDOException $e){
            return false;
        }
    }
    // funció d'inserció de registres en taula
    function insert($db,$table,$data):bool 
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
                    $stmt=$db->prepare($sql);

                    $stmt->execute($values);
                }catch(PDOException $e){
                    echo $e->getMessage();
                    return false;
                }
            
            return true;
            }
            return false;
        }

      // funció de selecció de  tots els registres
      // pots indicar quins camps vols mostrar
        function selectAll($db,$table,array $fields=null):array
        {
            if (is_array($fields)){
                $columns=implode(',',$fields);
                
            }else{
                $columns="*";
            }
            
            $sql="SELECT {$columns} FROM {$table}";
           
            $stmt=$db->prepare($sql);
            $stmt->execute();
            $rows=$stmt->fetchAll(PDO::FETCH_ASSOC);
            return $rows;
        }
    // select amb join sense condició
        function selectAllWithJoin($db,$table1,$table2,array $fields=null,string $join1,string $join2):array
        {
            if (is_array($fields)){
                $columns=implode(',',$fields);
                
            }else{
                $columns="*";
            }
           
            $inners="{$table1}.{$join1} = {$table2}.{$join2}";
            
            $sql="SELECT {$columns} FROM {$table1} INNER JOIN {$table2} ON {$inners}";
            
            $stmt=$db->prepare($sql);
            $stmt->execute();
            $rows=$stmt->fetchAll(PDO::FETCH_ASSOC);
            return $rows;
        }
        // select amb join amb només una condició
        function selectWhereWithJoin($db,$table1,$table2,array $fields=null,string $join1,string $join2,array $conditions):array
        {
            if (is_array($fields)){
                $columns=implode(',',$fields);
                
            }else{
                $columns="*";
            }
           
            $inners="{$table1}.{$join1} = {$table2}.{$join2}";
            $cond="{$conditions[0]}='{$conditions[1]}'";
            
        $sql="SELECT {$columns} FROM {$table1} INNER JOIN {$table2} ON {$inners} WHERE {$cond} ";
        
            
            $stmt=$db->prepare($sql);
            $stmt->execute();
            $rows=$stmt->fetchAll(PDO::FETCH_ASSOC);
            return $rows;   
        }
```



