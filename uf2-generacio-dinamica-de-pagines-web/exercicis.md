# Exercicis



## 1. Construir una relació d'objectes per a estudiants i professors

En aquesta activitat, implementarem conceptes OOP per crear `Student`i `Professor`classes amb constructors, atributs i mètodes de paràmetres. Instantarem les dues classes i establirem una relació entre els objectes. Un professor pot tenir un nombre determinat d’estudiants matriculats a la seva classe. La llista d’estudiants s’ha d’imprimir mitjançant un mètode membre de l’ `Professor`objecte.

Els passos a realitzar són els següents:

1. Creeu un directori anomenat `activity1`per incloure-hi tot el contingut de l’activitat. Aquest ha de ser el nostre directori de treball \(pot ser `cd`al directori\).
2. Creeu un fitxer de script anomenat `activity-classes.php`.
3. Crear `Professor`i `Student`classificar en directoris separats amb les funcions següents.

   Ambdues utilitzen el seu propi espai de nom per carregar les classes automàticament.

   Tots dos prenen el nom com a primer argument del constructor; la `Professor`classe accepta el segon argument com a llista d’estudiants: la llista es filtrarà `Student`només per a instàncies .

   Tots dos tindran la propietat del títol, que per defecte per a la `Professor`classe és `Prof.`i per a la `Student`classe és `student`.

4. Creeu una funció que imprimirà el títol del professor, el nom, el recompte d’estudiants i la llista d’estudiants.
5. Creeu una `Professor`instància, que proporcioni un nom i una llista d’estudiants: casos d’investigador `Student`amb un nom.
6. Afegiu una quantitat aleatòria de `Student`casos a la `Professor`instància.
7. Canvieu el títol del professor per `Dr.`.
8. Imprimeixi la sortida si invoqueu la funció amb la `Professor`instància.

La sortida hauria de tenir el següent:

```text
Alumnes del Dr. Charles Kingsfield (4): 
  1. Elwin Ransom 
  2. Maurice Phipps 
  3. James Dunworthy 
  4. Alecto Carrow
```

