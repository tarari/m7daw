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

```php
//SELECT amb  WHERE un sol valor per paràmetre
$uid = 1610;
$stmt = $db->prepare("SELECT `col`, `col2`, `col3` FROM `table` WHERE `uid` = :uid LIMIT 1;");
$stmt->execute([':uid' => $uid]);
$row = $stmt->fetchAll(PDO::FETCH_ASSOC);
$col = $row[0]['col'];
```

## Select retallat

```php
//SELECT amb WHERE retallat
$stmt = $db->prepare("SELECT `col`, `col2` FROM `table` WHERE `id` = :id;");
$row = $stmt->fetch($stmt->execute(array(':id' => $id)));
```

