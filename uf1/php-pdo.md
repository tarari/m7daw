---
description: Accés a bases de dades
---

# PHP PDO

L'extensió _Objectes de Dades de PHP_ \( PDO per les seves sigles en anglès\) defineix una interfície i capa d'abstracció lleugera per poder accedir a bases de dades en PHP. Cada controlador de bases de dades que implementi la interfície PDO \(sqlite, mysql, sqlserver, pgsql\) pot exposar característiques específiques de la base de dades, com les funcions habituals de l'extensió. 

Cal instal·lar previament l'extensió necessària per a cada cas.

## Crear connexió i comprovar status

```php
try{
    //Crear connexio
    $db = new PDO('mysql:host=localhost;dbname=DATABASENAME;charset=utf8mb4', 'USERNAME', 'PASSWORD');
    // cas que sigui squlite
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

**Esquema de funcionament:**

Preparació de sentència --&gt; execute\(\) --&gt; FETCH \(recòrrer resultats\)

Per evitar atacs d'injecció de codi fem servir el mètode **`prepare`** que preprocessa la sentència abans d'executar-la.

```php
//SELECT amb bucle 
$stmt = $db->prepare("SELECT `col`, `col2` FROM `table`");
$stmt->execute();
while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
    $db_col = $row['col'];
    $db_col2 = $row['col2'];
    echo "$db_col $db_col2<br>";
}
```

## Select amb un valor WHERE

**Esquema de funcionament:**

Preparació de sentència --&gt; execute\(\[array associatiu de paràmetres ":" i valors\]\) --&gt; FETCH o FETCHALL \(recòrrer resultats\)

```php
//SELECT amb  WHERE un sol valor per paràmetre
$uid = 1610;
$stmt = $db->prepare("SELECT `col`, `col2`, `col3` FROM `table` WHERE `uid` = :uid LIMIT 1;");
$stmt->execute([':uid' => $uid]);
$row = $stmt->fetchAll(PDO::FETCH_ASSOC);
$col = $row[0]['col'];
```

## Insert \(registre\)

**Esquema de funcionament:**

Preparació de sentència --&gt; bind parameters-&gt; execute\(\)

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

