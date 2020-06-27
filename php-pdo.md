# PHP PDO



## Crear connexió i comprovar status

```php
//Create connection
$db = new PDO('mysql:host=localhost;dbname=DATABASENAME;charset=utf8mb4', 'USERNAME', 'PASSWORD');
$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);//https://www.php.net/manual/en/pdo.setattribute.php

//Check connection status
echo $db->getAttribute(PDO::ATTR_CONNECTION_STATUS);


```

## Select en bucle

```php
//SELECT with loop
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
//SELECT with WHERE one value
$uid = 1610;
$stmt = $db->prepare("SELECT `col`, `col2`, `col3` FROM `table` WHERE `uid` = :uid LIMIT 1;");
$stmt->execute(array(':uid' => $uid));
$row = $stmt->fetchAll(PDO::FETCH_ASSOC);
$col = $row[0]['col'];
```

## Select retallat

```php
//SELECT with WHERE shorter
$stmt = $db->prepare("SELECT `col`, `col2` FROM `table` WHERE `id` = :id;");
$row = $stmt->fetch($stmt->execute(array(':id' => $id)));
```

