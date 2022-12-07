# Solucionar problemes al Mysql

## Access denied for 'root'@'localhost'

Cal en primer lloc aturar el servei mysqld, assegureu-vos que no hi queda cap procés remanent de mysql.

```
// sudo systemctl stop mysql
```

i iniciar de nou però sense carregar els privilegis, mentres aquest terminal estigui obert, tindrem carregat el mysql sense privilegis:

```
mysqld_safe --skip-grant-tables 
```

A continuació, actualitzem usuari root entrant al client de mysql, a la base de dades de mysql

```
$ mysql -u root mysql
$mysql> UPDATE user SET Password=PASSWORD('nou_password') where USER='root';
$mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost';
$mysql> FLUSH PRIVILEGES;
```

Finalment, sortim i reiniciem el servei però aquesta vegada de forma normal, sense l'opció `--skip-grant-tables`
