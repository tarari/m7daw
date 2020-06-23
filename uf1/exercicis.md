# Exercicis

## Visualitzar query strings

En aquesta activitat, aplicarem els coneixements obtinguts dels exercicis anteriors i utilitzarem variables per recuperar cadenes de consulta d’un URL i imprimir la informació rellevant al navegador.

Creareu una aplicació senzilla que permeti als usuaris veure la informació de la pel·lícula al navegador. Un cop realitzada l'activitat, haureu de tenir una sortida similar a la següent:

![](https://s3.amazonaws.com/thinkific/file_uploads/59347/images/99f/9c9/a72/C14196_01_16.png)

Aquests passos us ajudaran a completar l’activitat:

1. Creeu un fitxer anomenat **`movies.php`**.
2. Captura dades de cadena de consulta al fitxer per emmagatzemar els detalls de les pel·lícules, com ara noms de pel·lícules, noms d’actor/actriu i any de llançament.
3. Creeu una estructura HTML bàsica i, a continuació, visualitzeu les cadenes de consulta capturades.
4. Vés al Terminal i executa la comanda per iniciar el servidor web integrat.
5. Després que el servidor web estigui en funcionament, obriu la pàgina PHP i afegiu les vostres cadenes de consulta a l’URL del vostre navegador.

## Creació d'una calculadora

Treballeu en una aplicació web basada en calculadores. Se li dóna tot el codi de la interfície d'usuari, però se li demana que construeixi la funció que realitzarà els càlculs. Has de fer  una única funció que pugui ser reutilitzable per a tots els càlculs necessaris dins de l'aplicació.

Els passos següents us ajudaran a completar l'activitat:

1. Creeu una funció que calculi i retorni el factorial del número d’entrada.
2. Creeu una funció que retorni la suma dels números d’entrada \(un nombre variable de paràmetres\).
3. Creeu una funció que avalui l’ `$number`entrada, que ha de ser un nombre enter i retornarà si el nombre és un nombre prim o no. El tipus de retorn d’aquesta funció és un Boolean \( `bool`\).
4. Creeu una funció base  `performOperation` que gestioni les operacions matemàtiques predefinides. El primer paràmetre de la funció `performOperation` ha de ser una **cadena**, ja sigui ' `factorial`', ' `sum`' o ' `prime`'. Els arguments que queden a la funció matemàtica es diuen com a arguments.



> **Nota:** un factorial és el producte d'un nombre enter i de tots els enters que hi ha a sota.

La sortida hauria de ser similar a la següent. Els valors de sortida depenen dels números que introduïu:

![Simulaci&#xF3;](https://s3.amazonaws.com/thinkific/file_uploads/59347/images/a98/c42/ac8/C14196_04_17.png)

## Exercicis amb funcions

En aquest exercici, utilitzarem un parell de funcions més predefinides per conèixer el processament de dades i escriure els nostres propis  processadors perquè siguin **reutilitzables**. L’objectiu d’aquest exercici és agafar una sèrie de directors i pel·lícules i ordenar-los pel nom del director. A continuació, volem processar aquesta matriu i imprimir el nom del director on la primera lletra del nom  estigui en majúscules i el cognom sigui tot en majúscules. A més, per a les pel·lícules, volem posar en majúscules cada títol, posar-les entre cometes dobles i separar-les mitjançant comes. 

Construirem dues funcions que gestionaran el processament del nom del director i una altra de les pel·lícules. Anem a fer ús de tres noves funcions incorporades en què encara hem de discutir: **`ksort`**, **`explode`i`implode`**. Per obtenir més informació sobre aquestes funcions, reviseu la documentació de [php.net](http://php.net/) :

```php
$directors = [    
    'steven-spielberg' => [
         'ET',
         'Raiders of the lost ark',
         'Saving Private Ryan'
  ],
   'martin-scorsese' => [
        'Ashes and Diamonds',
        'The Leopard',
        'The River'
  ],
   'kathryn-bigelow' => [
        'Detroit',
        'Last Days',
        'The Hurt Locker'
  ],
    'felix-gary-gray' => [
        'Men in Black: International',
        'The Fate of the Furious',
        'Law Abiding Citizen'
  ]
];
```

Feu servir noms de funció indicatius:

```php
function processDirectorName($name){}
function processMovies($movies){}
```

Després de processar l'array, la sortida de l'execució en línia el resultat de : **`php exercici-funcio.php`** seria:

```php
Felix GARY: "MEN IN BLACK: INTERNATIONAL","THE FATE OF THE FURIOUS","LAW ABIDING CITIZEN"
Kathryn BIGELOW: "DETROIT","LAST DAYS","THE HURT LOCKER"
Martin SCORSESE: "ASHES AND DIAMONDS","THE LEOPARD","THE RIVER"
Steven SPIELBERG: "ET","RAIDERS OF THE LOST ARK","SAVING PRIVATE RYAN"
```

Podem com a suplement, crear un layout en html i mostrar el resultat en una plantilla, fes-ho.



