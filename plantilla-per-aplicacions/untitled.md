# Esquema tipus d'aplicació

Habitualment quan es crea una aplicació, sempre s'utilitza gairebé el mateix esquema, anem a veure ara i com a fruit de la meva experiència el que jo crec que ha d'incorporar qualsevol aplicació:

  


**Pantalles  webAPP**

{% tabs %}
{% tab title="Inici -Home" %}
Presentació de dades:  Entitat que es maneja en l 'aplicació

 - **Posts** \(per exemple
{% endtab %}

{% tab title="Login" %}
 Exclussivament **login** o **registre** \(recordatori de si es vol crear un compte\) o bé  si has oblidat la contrasenya
{% endtab %}

{% tab title="Escriptori -Dashboard" %}


Pantalla de treball de l’usuari

*  **CLIENT**: Pot crear les seves pròpies entitats i administrar el seu compte. 
*  **ADMIN**: Pot  administrar el seu compte i propietats així com de la resta d'entitats.  
{% endtab %}
{% endtabs %}

 

**Subpantalles - associades a les anteriors**

* Registre
* Registre de entitats \(en general\) - requereix token de formulari \(\*\)
* Edició, modificació d'entitats \(en general\) - requereix token  de formularis

_\(\*\)Per evitar atacs_ [_CSRF_](https://es.wikipedia.org/wiki/Cross-site_request_forgery)\_\_

**Navegació per pantalles**

Home —&gt; Login

Login —&gt;dashboard

Login —&gt;registre

Dashboard—&gt;Mostrar id

