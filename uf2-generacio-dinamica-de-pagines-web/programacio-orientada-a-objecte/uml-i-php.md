# UML i PHP

## UML i PHP

## Agregació i Composició

Aquestes relacions són part-tot, en la qual tenim una classe que es una partícula d’una altra classe.

La frase per comprovar una relación d’aquest tipus és  A és una part de B.

### Agregació

###

![agregacion](https://lh7-us.googleusercontent.com/E6bpjhwSgYQ0VUUxNQGzsC\_Zq7rQH7q9evz898W2bAwy3motE-DHz\_U9FoGfG4p3mM\_ncgZg29GwhrmNPbNfco5ZO2YWSGGcc9oY-BtkIbcwlgqcmDIfi1eXs3leQB3IW1PrZbdQcgCzsOkuRu6Wx6Y)Agregació: A és part de B



```php
<?php
class A
{
    // Definició de classe A
}

class B
{
    private $coleccioA;
    public function __construct()
    {
    }
  
    public agregar(A $part)
    {
        $this->coleccioA[] = clone $part;
    }
}

```

En aquest cas l’objecte no és creat dins de l’objecte compost, és adherit mitjançant algun mètode que ho permeti.\
\


### Composició



![Composició: A és part de B](https://lh7-us.googleusercontent.com/6CYL4nWZtWb\_qrb9UJghfWk7ycvcfgHVqzXGHe2No3H6c6uz4oT123TuMgayZJxPCuufkZkayF5u7eLejh\_YgQ05qbRnK2SGO5WXbq7PJNAf5lp\_k-r2z2RSPFEDx\_Qz8Jaia1IXAiQlbqLfZpY1Eso)

_Composició: A és part de B_\




```php
<?php

class A
{
    // Definició de classe A
}

class B
{
    private $partA;
    
    public function __construct()
    {
        $this->partA = new A();
    }
}

```

En el cas de la composició, és necessari crear dins de l’objecte compost els  objectes que seran part del mateix.

#### Diferència entre Agregació i Composició

* Les relacions en una composició són requerides, en l’agregació són opcionals.
* En la composició una classe partícula no pot ser compartida per altres classes compostes, en l’agregació això no és  possible.
* La relació de vida de la classe partícula i la classe contenidora, és molt  forta, de fet és la relació més forta; tant així que si un objecte de la classe contenidora és destruït la classe partícula també ho  serà. Això no passa en l’agregació.

## Dependència

La dependència entre dos classes declara que cadascuna d’elles necessita conèixer sobre la classe  que utilitzarà.

En general, aquesta és la relació més feble.&#x20;

El coneixement entre las classe té una sola via, això vol dir que només la classe que utilitza l’altra té  coneixement d’ella.

La frase que determina aquesta relació és “**classe A utilitza  la classe B**”.



<figure><img src="https://lh7-us.googleusercontent.com/cwvvm6kMsmWsKP4xu2DFUWg3JGjx2R_MirdNZPsMKJH1gWgmJBqthgvkzZHFhvSvIvCN1VBmZomLqGg6AAp5du_8R3Fu-lwhfRJPVAt_HfH5OG4_l5Iv5vRoMLtxtXDkAdO7o57pY80Q4ECnPnsU3tA" alt=""><figcaption><p><em>Dependència: A utilitza o depén de B</em></p></figcaption></figure>

**Exemple**

![impresora-papel](https://lh7-us.googleusercontent.com/6sVIQiWafVgYWzxvlgwmFZrHmRmRZLo9HXzinOFoPHswTfIfTgDcQnumfziTvey9tv1h9rw0wbGOr5XRXumkIR5\_gDcdYY19XYpEzWXATvyd-Ise7BpYxmPqiR-26RTUhFbE30l44cW6YjwNK7wl63E)Dependència de Impressora cap a Paper

&#x20;En aquest cas “Impresora usa  papel” en un mètode anomenat imprimir.

En l'exemple es mostra la classe Paper, la qual serà l’eina utilitzada per imprimir un text.

\


```php
<?php

class Paper
{
    private $mida;
    private $text;
    
    public function __construct(string $mida)
    {
        $this->mida = $mida;
    }
    
    public function setText(string $text)
    {
        $this->text = $text;
    }
}

```



També es té la classe  Impresora la qual utilitza al Papel per imprimir. Cal notar que Impressora «coneix» al Paper, és dir, a través d’un mètode és possible fer  ús d’un objecte tipus Paper.



```php
<?php

class Impresora
{ 
    private $model;
    public function __construct(string $model)
   {
       $this->model = $model;
  }
   public function imprimir(Paper $paper, string $informacio)
 {
    $papel->setText($informacio);
}
}
```

### &#x20;Associació

Quan les classes es connecten de forma conceptual, se’n diu  **Associació**

A diferència  de las anteriors relacions aquesta no transmet una frase identificadora sino que sorgeix de la propia relació, amb un verb que ho identifiqui.

<figure><img src="https://lh7-us.googleusercontent.com/dwtUPymqbaZuuwHqXO29AGGH5TLFVx_xAP_Npn7dbp1vjLfQVO-eff6IWJl0AufF9MIxceWWvG_kmi-oUBnvTb6UFHdIaFNYsj40SfqAcyqAUyPhgzTwpjRU6e_2-q1IjgPAvOsmjHQaYPAWGhgDU0U" alt=""><figcaption><p>Verb que associa una relació</p></figcaption></figure>

Dins d’una associació, a més, s’estableixen rols per cadascuna de les classes.



<figure><img src="https://lh7-us.googleusercontent.com/TgaYFov4Tl8QAA4TEIZuL2ReHT7EI6IkiZFVcUbXfaeUIy9OOMZ2hBvmNIx_HNjsB1Qa2YqT4gDnEcHoyjtHNKzihJipKxe869N7KUd5qihZHr_J4zxz1ufoNCEpBACTQ8zmxT_5wx1VP_GTzdXeCMA" alt=""><figcaption><p>Rols de les classes en l’associació</p></figcaption></figure>

És possible establir que l’associació flueixi de forma inversa, és a dir,  que l’equip emplea al jugador.



<figure><img src="https://lh7-us.googleusercontent.com/CiHVrUDIHHKGnFmbNorUvMW_Y52s3APOYIg2Jcno8uxSVf5zTLVubud83Z0vVTgKKfOOWcN7V6qs4jjbYysw67cZKikHUnOPTkXiiqxCLq2X901OXBhl5iIr3o1YmwnKRrXGpoEMqicv4RaTdbfR1t8" alt=""><figcaption><p>Bidireccionalitat de la associació</p></figcaption></figure>

#### Associació Reflexiva

Existeixen casos en els que una classe es pot relacionar amb sí mateixa, i  ocorre quan la classe pot funcionar amb diferents rols. D’això se’n diu  associació reflexiva.

<figure><img src="https://lh7-us.googleusercontent.com/5owSdujR8ZFOL7_FDRpcRZPjTTGBg9_Ntp-Oa0BT7ae9nVfchTtymYl5HQpvnPoLcM6OStLX5KG3eQml7X3uy8Du_Jgr9KNXirQu6_q20BA3mjGfMBh2-o5PVd95Jmch64xRUGSHq1iTD7mY-fE3t6k" alt=""><figcaption><p>Associació reflexiva</p></figcaption></figure>



#### Classes d’Associació

Sovint, ens trobem amb associacions que necessiten atributs i mètodes específics, per a manejar de forma idónea aquesta relació.

<figure><img src="https://lh7-us.googleusercontent.com/99rpHwYEnpkwBhP-qbwtg6q2uMOiOD9A7XbppftovAQJfBODEKmwa5AWDxLQYUbHtvZ4NC0VuPXeYnAqNmm503QG8qqfsMvB64YfDHjT5IVBqV9lwX6d_SXiJHhwiAXJG7BacPo1gRwvuqTzq5_kdIc" alt=""><figcaption><p>Classe Associativa</p></figcaption></figure>



En el cas anterior, la nova classe **Contracte** conté atributs i mètodes específics de la relació entre el jugador i l’equip.

#### Diferències entre Associació i Agregació

És possible que puguin  presentar-se dubtes per  determinar si entre dos classes existeix una asociació o una agregació.

Per comprendre aquestes  situacions és necessari verificar:

1. Determinar els **orígens** de cada tipus de relació. L’**agregació** determina **relacions de tot i part**, per tant sempre tindrem elements que són ingredients o partícules d’altre.
2. En  general, les relacions **d'associació** permeten el coneixement d’ambdues classes, és a  dir, que **dins dels atributs d’una classe estará l’altra i viceversa**. En tant, l’agregació solament permet que el tot conegui les seves partícules i  no a l’inrevés.
3. En la cardinalitat d’una associació pot ser variada, però en l’agregació es té una cardinalitat d'un a molts.

<figure><img src="https://lh7-us.googleusercontent.com/FP74SQ26yE90BifdC5iMdiiC2vCCXgIDczraqCmZAzlE0P1PDrmTSq6A8N23h80x-FrI05nJaO9TKwViCS8foqVyNv3fCeoVs_EyvKhIxY4Le1GObUnLdBFTQNOtLPVrbpPij0254wzk8xVRku4ZArQ" alt=""><figcaption><p>Associació</p></figcaption></figure>

### Referències

\[1] Aprendiendo UML en 24 Horas. Joseph Schmuller.

\
