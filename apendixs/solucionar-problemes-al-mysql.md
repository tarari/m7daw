# Solucionar problemes al Mysql

## Access denied for 'root'@'localhost'

Cal en primer lloc aturar el servei mysqld, i iniciar de nou però sense carregar els privilegis:

```text
mysqld --skip-grant-tables
```

A continuació, actualitzem  usuari root entrant al client de mysql, a la base de dades de mysql

```text
$ mysql -u root mysql
$mysql> UPDATE user SET Password=PASSWORD('nou_password') where USER='root';
$mysql> FLUSH PRIVILEGES;
```

Finalment, sortim i reiniciem el servei però aquesta vegada de forma normal, sense l'opció `--skip-grant-tables`

