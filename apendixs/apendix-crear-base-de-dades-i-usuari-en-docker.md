---
description: Suposem que treballem amb un contenidor docker de Mysql.
---

# Crear base de dades i usuari en docker

## Creació de contenidor MySQL docker amb volum compartit

Crearem un contenidor basat en  mysql/mysql-server:5.7 amba port 3306 exposat i volum compartit en màquina real.

```text
docker run --name mysql1 -p 3306:3306 -v /Users/user/Documents/mysql-data:/var/lib/mysql  -d mysql/mysql-server:5.7

```

Comprovem i copiem el password de root del mysql que acabem d'instal·lar

```text
docker logs mysql1
```

Executem interactiu per poder canviar el password de root@localhost i de root@%.

```text
docker exec -it mysql1 /bin/bash
```

Executem dins del contenidor :

```text
mysql -u root -p
```

Ens demana el password que vam compiar abans. Dins, introduim el següent comando per canviar passwor de root

```text
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password';
CREATE USER 'root'@'%' IDENTIFIED BY 'altrepassword';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
```

Ara podem afegir els usuaris d'accés a la nostra base de dades, o bé fer-ho des de fora. Fem _exit_ per sortir del contenidor. Ara podem, des de la nostra màquina accedir al contenidor \(servei\) i crear usuari i base de dades del projecte.

```text
CREATE USER 'dbase_user'@'%' IDENTIFIED BY 'passwordbase';
GRANT ALL PRIVILEGES ON base.* TO 'base_user'@'%'
```



