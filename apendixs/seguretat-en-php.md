# Seguretat en PHP

Un aspecte fonamental en qualsevol aplicació és l'aspecte de la seguretat. En quins aspectes hem de cuidar la seguretat de  l'aplicació? 

En la comunicació \(REQUEST i RESPONSE\), en el traspàs d'informació \(formularis\), en la navegació \(pas de pantalles\).

## TOKENS \(Seguretat CRSF\)

Els tokens representen una comprovació fonamental en els formularis, que determinen si hi ha continuitat en la comunicació, més enllà de l'ús de PHPSESSID. 

La idea és la següent:  Passar una dada al formulari \(token \) i comprovar en destinació si aquesta dada és exactament igual que l'original, si no ho fos, tenim un problema de seguretat.



### Afegir token al formulari

```php
<?php
    session_start();
    $token=md5(uniqid(rand(),true));
    $_SESSION['token']=$token
    $_SESSION['token_time']=time();
```

Resumit, creem un token aleatori i el desem com a variable de sessió juntanment amb el temps.



Per **protegir formularis** , se sol incloure el **identificador dins d'un camp amagat** , sent enviat amb la resta de les dades de l'formulari:

```php
<input type="hidden" name="csrf" value="<?php echo $_SESSION["token"]; ?>">
```



