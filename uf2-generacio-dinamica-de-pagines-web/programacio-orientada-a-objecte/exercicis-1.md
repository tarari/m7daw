# Exercicis

## 1. Construir una relació d'objectes per a estudiants i professors

En aquesta activitat, implementarem conceptes OOP per crear `Student`i `Professor`classes amb constructors, atributs i mètodes de paràmetres. 

Es recomana, prèviament realitzar un diagrama UML d'entitats per conèixier els tipus de relació existents. Per conèixer més: [https://diagramasuml.com](https://diagramasuml.com/)



Instanciarem  les dues classes i establirem una relació entre els objectes. Un professor pot tenir un nombre determinat d’estudiants matriculats a la seva classe. La llista d’estudiants s’ha d’imprimir mitjançant un mètode membre de l’objecte `Professor`.

Els passos a realitzar són els següents:

1. Creeu un directori anomenat **`ex1uf2`**per incluoure-hi tot el contingut de l’activitat. Aquest ha de ser el nostre directori de treball \(feu `cd`al directori\).
2. Creeu un fitxer de script anomenat `activity-classes.php`.
3. Crear classes `Professor`i `Student`classificar dins una carpeta amb el mateix nom i dins els  fitxers separats  de  cada classe amb les funcions següents.

   Ambdues utilitzen el seu propi **espai de noms** per carregar les classes automàticament.

**`spl_autoload_register();`**

Tots dos prenen el nom com a primer argument del constructor; la classe **`Professor`** accepta el segon argument com a **llista d’estudiants \(array\)**

Tots dos tindran la propietat del **alies**, que per defecte per a la classe `Professor` és **`Professor`**`.`i per a la  classe `Estudiant` és **`estudiant`**.

1. Creeu una funció que imprimirà el alies del professor, el nom, el recompte d’estudiants i la llista d’estudiants. Quin tipus de relació entre objectes s'estableix?
2. Creeu una instància  **`Professor`**, que proporcioni un nom i una llista d’estudiants: numero d'estudiants amb el nom.
3. Afegiu una quantitat aleatòria d'estudiants a la instància `Professor`.
4. Canvieu el títol del professor per  la de `Doctor`.
5. Imprimeixi la sortida si invoqueu la funció amb la instància  `Professor`

La sortida hauria de tenir el següent:

```text
Alumnes del Dr. Charles Kingsfield (4): 
  1. Edwin Ransom 
  2. Maurici Phipps 
  3. James Dunworthy 
  4. Alecto Carrow
```

