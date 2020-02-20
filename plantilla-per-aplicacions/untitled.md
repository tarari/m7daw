# Esquema tipus d'aplicació

Habitualment quan es crea una aplicació, sempre s'utilitza gairebé el mateix esquema, anem a veure ara i com a fruit de la meva experiència el que jo crec que ha d'incorporar qualsevol aplicació:

  


**Pantalles  webAPP**

**&gt;Inici - Home**

 Presentació de dades: Entitat que es maneja - Posts \(per exemple

**&gt;Login**

 Exclussivament **login** o **registre** \(recordatori de si es vol crear un compte\)

**&gt;Dashboard - Escriptori**

 Pantalla de treball de l’usuari

*  **CLIENT**: Pot crear les seves pròpies entitats i administrar el seu compte. 
*  **ADMIN**: Pot  administrar el seu compte i propietats així com de la resta d'entitats. 

  
p.p1 {margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px 'Helvetica Neue'}  
p.p2 {margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px 'Helvetica Neue'; min-height: 14.0px}  
p.p3 {margin: 0.0px 0.0px 0.0px 0.0px; font: 17.0px 'Helvetica Neue'}  


**Subpantalles - associades a les anteriors**

* Registre
* Registre de entitats \(en general\) - requereix token de formulari \(\*\)
* Edició, modificació d'entitats \(en general\) - requereix token  de formularis

_\(\*\)Per evitar atacs CSRF_

**Navegació per pantalles**

Home —&gt; Login

Login —&gt;dashboard

Login —&gt;registre

Dashboard—&gt;Mostrar id

