# PHPUnit

## Prova d’unitat

Una prova d’unitat és un tros petit de codi que utilitza l'aplicació. La idea és desglossar el  codi en unitats petites. Una unitat és el component més petit del codi. Normalment, una unitat es fa a partir de **mètodes de classe** per provar la lògica d’algun codi. L’objectiu d’un test unitari és verificar que la **sortida funciona com s’esperava**.

Podem executar múltiples proves d'una sola vegada. L’avantatge d’aquest plantejament és que si executem les proves i passen totes, podem refactoritzar el  codi i tornar a executar les proves per assegurar-nos que no s’ha trencat res. Atès que cada acció pot ser una prova separada, podem provar contra diversos **casos d’ús** diferents amb la confiança que, si fem un canvi, sabem que el codi segueix estable i funciona com s’esperava.

Alternativament, és possible que vulguem executar la prova abans que s'acabi el codi, per tal de veure que la prova falla i, després, executem-la de nou una vegada finalitzat el codi per veure que passa el test. D’aquesta manera, farem el desenvolupament de **proves dirigides** \( **TDD** \) una pràctica de desenvolupament de programari.

A continuació es mostra un exemple de prova per il·lustrar què és una prova d’unitat. El propòsit és mirar un array `$item` i verificar si conté un índex anomenat `name`. Primer, es defineix l'array; aleshores, les variables s’estableixen per indicar què s’espera i on buscar un valor. Finalment, `assertArrayHasKey`s'executa  i comprova que el valor esperat s'ha trobat a l'array:

```php
/** @test */
public function arrayHasKeyName()
{
    //setup
    $item = [
        'name' => 'toni'
    ];
    //act
    $expected = 'name';
    $actual = $item;
    //assert
    $this->assertArrayHasKey($expected, $actual);
}
```

Si `name`es troba, com a índex a l'array, la prova passarà i mostrarà llum verda `OK`, si no donarà missatge  vermell `FAILURE` 

## Què és PHPUnit?

PHPUnit és un marc de proves de diagnòstic; això vol dir que no depèn de cap marc de tercers. De fet, no es requereix un framework tret de PHPUnit. PHPUnit es pot utilitzar amb un grup de classes i amb un framework ja existent.

PHPUnit és una eina per executar proves. La prova d’unitat és on es divideix el codi en unitats \(petits trossos del codi mínim necessaris per exercir una funció\) i es proven després dels resultats esperats.

## Instal·lació de PHPUnit

Hi ha dues maneres d’instal·lar PHPUnit: utilitzant un **fitxer PHP Archive** \( **PHAR** \) i amb Composer.

### Instal·lació de PHPUnit amb Composer

Donaa que el composer es va cobrir en una lliçó anterior, aquest procés serà familiar. En aquest exercici, instal·lem PHPUnit amb Composer:

1. Per instal·lar PHPUnit mitjançant Composer, afegiu `phpunit/phpunit` al `composer.json` \(situat al directori arrel del projecte\). Afegeix `phpunit/phpunit`a la  secció`require-dev`. Això indica que Composer només ho requereix  en mode desenvolupador:

   ```text
   {     
       "require-dev": 
       {         
           "phpunit/phpunit": "^8"     
       } 
   }
   ```

2. A continuació, a Terminal, executa la següent comanda per instal·lar Composer:

   ```text
   composer install
   ```

3. Si es tracta d'una actualització del projecte:

   ```text
   composer update 
   ```

4. Una altra manera és dir-li al **Composer** que afegeixi PHPUnit com a dependència. A Terminal, escrivim el següent:

   ```text
   composer require --dev phpunit/phpunit ^8
   ```

   Això actualitzarà el fitxer `composer.json` a la versió especificada.

5. A partir d’aquest moment, PHPUnit es podrà executar cridant al seu executable al Terminal:

   ```text
   vendor/bin/phpunit
   ```

> **Nota** : és possible que tinguem la temptació d’instal·lar PHPUnit de forma global; això no es recomana ja que cada projecte ha de gestionar PHPUnit com a dependència específica del projecte.



