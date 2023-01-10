# API Reflection

Utilitzant l'API de reflexió, podem fer moltes coses al codi real: treure automàticament llibreries dependents a controladors basats en tipus de llibreries, obtenir totes les interfícies que implementen algunes classes, obtenir tots els noms dels camps d'alguna classe (especialment una cosa útil quan estem planejant escriure un ORM o algun tipus de CRUD generats automàticament), obtenir informació sobre totes les funcions de la classe...

Sobretot el que realment ens aporta en la documentació la reflexió: permet analitzar i obtenir tots els comentaris de documents de les vostres classes: document de classe, propietats i mètodes comentaris de document (anotacions).

## Obtenir informació de classe

Ara comprovarem a través d'un exemple com es pot treure informació a partir de la API de reflexió.

```php
class Person {

  const NAME_LENGTH_MAX = 50;

  protected $name;

  public function __construct($name){
    $this->name = $name;
  }

  /**
  * @return string
  */
  public function getName(){
    return $this->getName();
  }

  public static function generateName() {
    $characters = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
    $charactersLength = strlen($characters);
    $name = '';
    for ($i = 0; $i < self::NAME_LENGTH_MAX; $i++) {
        $name .= $characters[rand(0, $charactersLength - 1)];
    }
    return $name;
  }

}
```

Què podem treure com a informació de la classe Person?

```php
function echoInfoOnClass($object) {
    $reflectionClass = new \ReflectionClass($object);

    echo "Nom: " . $reflectionClass->getName() . PHP_EOL; // echo class name (with namespace if any)
    echo "Nom curt: " . $reflectionClass->getShortName() . PHP_EOL; // echo class name without namespace
    // tots els mètodes

    echo PHP_EOL;
    echo "Constants:" . PHP_EOL . PHP_EOL;
    foreach ($reflectionClass->getConstants() as $constantName => $constant) {
        echo $constantName . ' = ' . $constant . PHP_EOL;
    }

    echo PHP_EOL;
    echo "Atributs:" . PHP_EOL . PHP_EOL;
    foreach ($reflectionClass->getProperties() as $property) {

        $propModifiers = \Reflection::getModifierNames($property->getModifiers()); // list of method modifiers (public,static, ..)

        foreach ($propModifiers as $modifier) { // echo all modifiers with space
            echo $modifier . " ";
        }

        $property->setAccessible(true); // this method of ReflectionProperty class make possible to access private or protected data on classes

        echo $property->getName() . ' = ' . json_encode($property->getValue($object)) . PHP_EOL;
    }

    echo PHP_EOL;
    echo "Methods:" . PHP_EOL . PHP_EOL;
    foreach ($reflectionClass->getMethods() as $method) {

        $methodModifiers = \Reflection::getModifierNames($method->getModifiers()); // list of method modifiers (public,static, ..)

        foreach ($methodModifiers as $modifier) { // echo all modifiers with space
            echo $modifier . " ";
        }

        echo $method->getName();

        $params = [];
        foreach ($method->getParameters() as $methodParam) {
            $currentParam = $methodParam->getName();
            if ($methodParam->isDefaultValueAvailable()) {
                $currentParam .= '=' . $methodParam->getDefaultValue();
                if ($methodParam->isDefaultValueConstant()) {
                    $currentParam .= '(' . $methodParam->getDefaultValueConstantName() . ')';
                }
            }
            $params[] = $currentParam;
        }

        // enganxar parelles params -values 
        echo '(' . implode(',', $params) . ')';

        echo PHP_EOL;
    }
}
```
