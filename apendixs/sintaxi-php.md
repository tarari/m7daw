# Sintaxi PHP



#### **1. Etiquetes PHP**

Quan PHP analitza un arxiu, cerca etiquetes d'obertura i tancament, que normalment són **`<?php  ?>`**,i indiquen a PHP quan començar i acabar d'interpretar codi . PHP també permet l'etiqueta d'obertura \``<?`, però es desaconsella.

Una altra forma d'incloure codi és amb l'element script:.

Si un arxiu és enterament en PHP, sense HTML, és preferible ometre l'etiqueta de tancament a la fi de l'arxiu **?>**.

#### **2. Escapament amb HTML**

Qualsevol codi fora de les etiquetes d'obertura i tancament és ignorat per l'intèrpret de PHP. Això permet encastar PHP en HTML, cosa que el fa perfecte per crear plantilles HTML:

```markup
<p> Text ignorat per PHP.</p>
<?php echo 'Text que serà interpretat per PHP.'; ?>
<p> Text també ignorat.</P>
```

Quan es fa escapament enmig d'una condició, l'intèrpret tindrà en compte el codi HTML només si aquesta es compleix, en cas contrari saltarà el contingut:

```
<?php if ($expression ==  true):?>
```

** ** Es mostrarà si _$expression_ és true.

```php
<?php  else:?>
```

** ** Si no, es mostrarà aquest contingut.

```php
<?php endif; ?>
```

Per imprimir textos llargs aquest mètode és més eficient que enviar el text a través de `echo` o `print`.

#### 3. Instrucció de separació

Com en C o en Perl, PHP requereix que cada instrucció s'acabi amb un punt i coma ";" a la fi de cada sentència. L'etiqueta de tancament d'un bloc de codi PHP automàticament implica el punt i coma, de manera que no cal incloure-a al final:

```php
<?php echo 'Això és una prova' ?>
```

A vegades és recomanable aquest mètode per evitar espais en blanc a al final, especialment quan es fan servir _include, require_ o _output buffering_.

#### 4. Comentaris

L'estil de comentaris en PHP és com el de C, C ++ o Perl:

```php
<?php echo  'Hola'; // Això és un comentari d'una línia
    /* Això és
    un comentari
    en diverses línies */
    echo 'Més codi'; 
    # Això és un altre comentari d'una línia
?>
```

Els comentaris de diverses línies finalitzen amb el primer _\*/_:

```php
<?php
    /*
    echo 'Això és un comentari'; /* Més comentari * /
    */
?>
```

## OPERADORS

#### 1. Precedència d'operadors

La precedència dels operadors en PHP segueix gairebé les mateixes regles que en matemàtiques,per exemple: multiplicació i divisió precedeixen suma i resta, els parèntesis forcen una precedència, etc.

Quan tenen la mateixa precedència,la seva associativitat decideix com ordenar els operadors. Per exemple l'operador suma **+** té associativitat esquerra, de manera que l'operació **1 + 2 + 3 **sumarà primer 1 i 2, i després 3 => (1 + 2) + 3.&#x20;

Per contra, l'operador **=** té associativitat dreta, per la qual cosa `$x =$i =$z `s'agrupa de manera `$x = ($ i = $z)`.

Els operadors de mateixa precedència que no són associatius no poden usar-se junts, això: 1 < 2 > 1 és incorrecte.

De vegades és aconsellable fer servir parèntesi encara que no sigui necessari per millorar la lectura.

La precedència d'operadors i la seva associativitat només determinen com s'agrupen les expressions.

_Llista d'operadors ordenats per precedència:_

| Associativitat | Operadors                                                             |
| -------------- | --------------------------------------------------------------------- |
| No associatiu  | `clone, new`                                                          |
| Esquerra       | \[                                                                    |
| Dreta          | `**`                                                                  |
| Dreta          | `++, -, ~, (int), (float), (string), (array), (object), (bool ), @`   |
| No associatiu  | `instanceof`                                                          |
| Dreta          | `!`                                                                   |
| Esquerra       | `*, /,%`                                                              |
| Esquerra       | `+, -,.`                                                              |
| Esquerra       | `<<, >>`                                                              |
| No associatiu  | `<, <=,>,> =`                                                         |
| No associatiu  | `==,! =, ===,! ==, <>`                                                |
| Esquerra       | &                                                                     |
| Esquerra       | ^                                                                     |
| Esquerra       |                                                                       |
| Esquerra       | &&                                                                    |
| Esquerra       |                                                                       |
| Esquerra       | ?:                                                                    |
| Dreta          | =, + =, - =, \* =, \*\* =, / =,. =,% =, & =, / =, ^ =, << =, >> =, => |
| Esquerra       | `and`                                                                 |
| Esquerra       | `xor`                                                                 |
| Esquerra       | `or`                                                                  |
| Esquerra       | ,                                                                     |

#### 2. Operadors aritmètics

els operadors aritmètics en PHP són els mateixos que en les matemàtiques:

| Operadors artimètics | Representació |
| -------------------- | ------------- |
| Suma                 | $x + $i       |
| Resta                | $x - $i       |
| Multiplicació        | $x \* $i      |
| Divisió \*           | $x / $i       |
| Mòdul \*\*           | $x% $i        |
| Exponenciació        | $x \*\* $i    |
| Negació              | -$x           |

_\*Divisió retorna un (int) si $x i $i són divisibles, o (float) si no ho són._

_\*\*En Mòdul s'eliminarien primer les parts decimals transformant-se en (int) en cas de ser (float) i després es faria l'operació. El signe (+ o -) de l'resultat dependrà del dividend\*\*, per exemple: -5 % 3 mostraria -2._

#### 3. Operadors d'assignació

Hi ha operadors bàsics i combinats:

L'operador bàsic d'assignació és"=",que actua com a definidor, no com igualador.El valor d'una expressió d'assignació és el valor que se li ha assignat, és a dir: "$x = 3" té un valor de 3.

En el cas d'arrays, s'assigna un valor a una clau nomenada mitjançant l'operador"=>".

Els operadors combinats permeten utilitzar un valor en una expressió i establir el seu nou valor com a resultat d'aquesta expressió:

```php
<?php
  $x = 3;
  $x + = 5;
  // $x val ara 8

  $i = "Hola";
  $i.= ", Què tal?";  //concatenació de cadenes
  // $i val ara "Hola, Què tal?"
```

Hi ha una excepció a l'assignació per valor en PHP, i són els objectes,que s'assignen per referència. Els objectes es copien mitjançant la paraula `clone`.

#### Assignació per referència (Ai! els punters!)

L'assignació per referència significa que les variables apunten als mateixos valors, sense fer cap còpia:

```php
<?php
  $x = 3;
  $i = &$x; //$i és còpia del valor de $x

  print "$x, $i"; // Mostrarà 3, 3

  $x = 5;

  print "$x, $i"; // Mostrarà 5, 5
```

Les referències actuen com quan es crea un accés directe o àlies d'un arxiu o carpeta a l'ordinador.

#### 4. Operadors bit a bit

Els operadors bit a bit permeten l'avaluaciói manipulació de bits específics dins d'un integer.

|             |                                            |    |
| ----------- | ------------------------------------------ | -- |
| **Exemple** | **Nom**                                    |    |
| `$x & $i`   | And                                        |    |
| `$x`        | $ i                                        | Or |
| `$x ^ $i`   | Xor                                        |    |
| `~ $x`      | Not                                        |    |
| `$x << $i`  | Shift left (desplaçament esquerra binari)  |    |
| `$x >> $i`  | Shift right (desplaçament dreta binari)    |    |

#### 5. Operadors de comparació

Els operadors de comparació permeten comparar dos valors. Aquests valors depenen dels [tipus que tinguin](http://php.net/manual/es/language.types.type-juggling.php) assignats.Es poden veure les diferències comparatives en la [taula de comparació de](http://php.net/manual/es/types.comparisons.php) tipus.

|             |                     |                                                         |
| ----------- | ------------------- | ------------------------------------------------------- |
| **Exemple** | **Nom**             | _Resultat_                                              |
| `$x==$i`    | Igual               | true siguin de el mateix tipus o no                     |
| `$x===$i`   | Idèntic             | true només si són de el mateix tipus                    |
| `$x!=$i`    | Diferent            | true si són diferents siguin de el mateix tipus o no    |
| `$x<>$i`    | Diferent            | true si són diferents siguin de el mateix tipus o no    |
| `$x!==$i`   | no idéntico         | true només si no són iguals i tampoc de el mateix tipus |
| `$x<$i`     | menor que           | true si $ x és menor que i                              |
| `$x>$i`     | major que           | true si $ x és més gran que $ i                         |
| `$x <= $i`  | menor o igual que   | true si $ x és menor o igual que $ i                    |
| ` $x>=$a`   | `major o igual `que | true si $ x és més gran o igual que $ i                 |

Si es compara un nombre amb un string o la comparació és entre strings numèrics,cada string es converteix en nombre i la comparació es realitza numèricament (això també s'inclou amb l'ús de switch).Quan es fan comparacions idèntiques com === això no té sentit ja que també es comparen els tipus.

```php
<?php
    var_dump(0 == "x"); // 0 == 0 true
    var_dump ("1" == "01"); // 1 == 1 true
    var_dump ("10" == "1y1"); // 10 == 10 true
    var_dump(100 == "1e2"); // 100 == 100 true
    
    switch ("x"){
        case 0:
            echo "0";
            break;
        case "x": // Mai arribarà perquè "x" ja s'ha trobat amb el cas "0"
            echo "x";
        break;
    }
```

Quan un string s'avalua en un context numèric, el valor i el tipus depenen de la [conversió de string a](http://php.net/manual/es/language.types.string.php#language.types.string.conversion)nombres.

Comparacions entre diversostipus:

|                           |                           |                                                                                                                                                                                                            |
| ------------------------- | ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $ x                       | $ i                       | Resultat                                                                                                                                                                                                   |
| null o string             | string                    | null es converteix en "", comparació numèrica o lèxica                                                                                                                                                     |
| bool o null               | qualsevol                 | Ambdós seran bool,i false  \<true                                                                                                                                                                          |
| object                    | object                    | Dos objectes seran iguals si tenen els mateixos atributs i valors , sent instàncies de la mateixa classe. Dos objectes seran idèntics si a més fan referència a la mateixa instància de la mateixa classe. |
| string, resource o number | string, resource o number | Es tradueixen a nombres i es comparen matemàticament                                                                                                                                                       |
| array                     | array                     | Un array amb menys elements és menor. Si una key no coincideix en els dos arrays,són arraysincomparables.Es compara valor per valor.                                                                       |
| object                    | qualsevol                 | object sempre major                                                                                                                                                                                        |
| array                     | qualsevol                 | array sempre major                                                                                                                                                                                         |

causa de la forma en què són interpretats internament els floats,la seva comparació pot donar resultats inesperats, encara que existeixen [formes de poder](http://php.net/manual/es/language.types.float.php#language.types.float.comparison)comparar-los.

#### L'operador ternari

```php
<?php
    $x = "";
    $accio =(empty($x))?  'valor1': 'valor2'; // Retornarà (string) valor1
    
    // L'anterior serà el mateix que:
    if(empty($x)){
        $accio = 'valor1';
    } else {
        $accio = 'valor2';
    }
```



Suposem l'expressió` $x?$i:$z`. Si $x és true,s'avaluarà $i. Si $x és false, s'avaluarà $z.

Una forma més reduïda de l'operador ternari és mitjançant?:.Suposant $x?: $i, tornarà $x si $x és true, i  $i si $x és false.

#### 6. Operadors de control d'errors

Quan s'anteposa el símbol d'arrova **`@`** davant de qualsevol expressió, qualsevol missatge d'error que pugui generar aquesta expressió serà ignorat.

Si [track\_errors](http://php.net/manual/es/errorfunc.configuration.php#ini.track-errors) està activat, l'últimerror es guardarà a la variable **` `**

**`$php_errormsg`**\_ i podrà emprar-se.

```php
<?php
    $myfile = @file('archivo_que_no_existe') or
    die("No s'ha pogut obrir, l'error ha estat '$php_errormsg'");
```

// Funciona també si per exemple s'intenta accedir a la key d'un array que no existeix:

`$valor = @$array[$key]`

@ Només funciona amb expressions (variables, crides a funcions o includes, constants ...) **no** funciona amb definicions de classes o funcions, condicionals com if o foreach ...

#### 7. Operadors d'execució

l'operador d'execució \`\` fa que el contrenido entre les cometes invertides s'executi com comanda deconsola.Aquest operador és el mateix que fer servir la Function [shell\_exec](http://php.net/manual/es/function.shell-exec.php)().

\<? php

$ var = \`ls -al\`; // Comando Linux per mostrar un llistat d'arxius complet de directori actual

fet "\<pre> $ var \</pre>";

* Si [mode\_segur](http://php.net/manual/es/ini.sect.safe-mode.php#ini.safe-mode) està activat o [shell\_exec ()](http://php.net/manual/es/function.shell-exec.php) està desactivat, les cometes no funcionaran.
* Les cometes invertides no signifiquen res quan van dins de cometes dobles.

#### 8. Operadors d'Increment / decrement

Els operadors d'increment i decrement només afecten números i strings,sense afectar arrays, objects o resources.Disminueix un valor NULL no té efecte, però si s'incrementa s'obté 1. Incrementar o disminuir booleans no té efecte.

|          |                                             |
| -------- | ------------------------------------------- |
| Operador | Efecte                                      |
| ++ $ x   | Incrementa $ x en 1 i després retorna $ x   |
| $ x ++   | Retorna $ x, i després s'incrementa $ x a 1 |
| - $ x    | Decrementa $ x en 1, i després retorna $ x  |
| $ x--    | Retorna $ x, i després decrementa $ x en 1  |

Exemple:

\<? php

$ x = 4;

echo "Això és 4:". $ x++. "\</br>";

echo "I això és 5:". $ x. "\</br>";\


$ x = 4;

echo "Això és 5:".++$ x. "\</br>";

echo "I això és 5:". $ x. "\</br>";\


$ x = 4;

echo "Això és 4:". $ x-. "\</br>";

echo "I això és 3:". $ x. "\</br>";

A l'treballar sobre caràcters, Z passa a AA. Per exemple:

\<? Php

$ x = 'Z';

echo ++$x; // Retornarà AA

echo ++$x; // Retornarà AB\


$ x = 'A9';

echo ++$x; // Devolerá B0

echo ++$x; // Retornarà B1\


$ x = 'A09';

echo ++$x; // Retornarà A10

echo ++$x; // Retornarà A11

#### 9. Operadors lògics

|             |                                         |                                         |
| ----------- | --------------------------------------- | --------------------------------------- |
| Operador    | Resultat                                |                                         |
| $ x and $ i | true si $ xi $ i són true               |                                         |
| $ x or $ i  | true si un dels dos és true (o els dos) |                                         |
| $ x xor $ i | true si només un dels dos és            |                                         |
| true!$ x    | true si $ x no és true                  |                                         |
| $ x && $ i  | true si $ xi $ i són true               |                                         |
| $ x         | $ i                                     | true si un dels dos és true (o els dos) |

* && i || tenen precedència sobre and i or.

Notesimportants:

\<? Php

$ w =(false && foo ()); // false

$ x =(true  || foo ()); // true

$ i =(false and foo ()); // false

$ z =(true  or  foo ()); // true\


// Actua com: ($ m = (false || true))

$ m = false || true;

// Actua com: (($ n = false) or true)

$ n = false or true;\


var\_dump($m, $n);\


// Actua com: ($ a = (true && false))

$ a = true && false;

// Actua com: (($ b = true) and false)

$ b = true and false;\


var\_dump($a, $b);

#### 10. Operadors per strings

ha dos operadors per strings:

* Operador de concatenació '.'. Concatena els arguments dret i esquerre.
* Operador d'assignació sobre concatenació '.=',Afegeix l'argument el costat dret a l'argument la banda esquerra.

\<? php

$ x = "Hola";

$ i = $ x. ", Què tal?"; // $ i = "Hola, Com va?"\


$ x = "Hola";

$ x.= ", Què tal?"; // $ x = "Hola, Com va?"

#### 11. Operadors per arrays

|              |                                                                                     |
| ------------ | ----------------------------------------------------------------------------------- |
| Operador     | Resultat                                                                            |
| $ x + $ i    | Unió de $ xe $ i                                                                    |
| $ x == $ i   | true si $ xe $ i tenen les mateixes parelles key => value                           |
| $ x === $ i  | true si $ xe $ i tenen mateixes parelles key => value,mateix ordre i mateixos tipus |
| $ x! = $ i   | true si $ x no és igual a $ i                                                       |
| $ x <> $ i   | true si $ x no és igual a $ i                                                       |
| $ x! = = $ i | true si $ x no és idèntica a $ i                                                    |

l'operador + retorna la matriu dret($i)afegit a el de l'esquerre($x).Si hi ha claus que hi ha a les dues, s'utilitzaran les keys de l'array esquerra.

Exemple d'unió:

$ a = array("x" => "gos", "i" => "gat");

$ b = array("x" => "ànec", "i" => "llebre", "z" => "conill");

// Unió de $ ai $ b

$ c = $ a + $b;

echo "Unió de \ $ ai \ $ b: \n";

var\_dump($c);

/ \*

\* Array (size = 3)

&#x20; 'x' => string 'gos' (length = 5)

&#x20; 'i' => string 'gat' (length = 4)

&#x20; 'z' => string 'conill' (length = 6 )

\* /

// Unió de $ by $ a

$ c = $ b + $a;

echo "Union de \ $ by \ $ a: \n";

var\_dump($c);

/ \*

\* Array (size = 3)

&#x20; 'x' => string 'ànec' (length = 4)

&#x20; 'i' => string 'llebre' (length = 6)

&#x20; 'z' => string 'conill' (length = 6 )

\* /

Exemple de comparació:

$ x = array("gos", "gat");

$ i = array(1 => "gat", "0" => "gos");\


var\_dump($x == $i); // bool (true)

var\_dump($x === $i); // bool (false)

#### 12. Operadors de tipus

instanceof és utilitzat per saber si una variable és un objecte instanciat d'una classe.

class MiClase\


{}\


class NoEsMiClase\


{}

$ x = new MiClase;\


var\_dump($x instanceof MiClase); // true

var\_dump($i instanceof NoEsMiClase); // false

* Retorna true si la instància és d'una classepare. instanceof PadreClass
* Retorna true amb la interfície d'una classe que laimplementa. instanceof MyInterface
* Retorna true si la variable és un string amb el nom de la classe: $ c = 'MiClase'; instanceof $ c
* Si la variable que està sent comprovada no és un objecte, no torna error, retorna false.
