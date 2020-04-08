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

