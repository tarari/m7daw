# Esquema tipus d'aplicació

Habitualment quan es crea una aplicació, sempre s'utilitza gairebé el mateix esquema, anem a veure ara i com a fruit de la meva experiència el que jo crec que ha d'incorporar qualsevol aplicació:

\


**Pantalles  webAPP**

{% tabs %}
{% tab title="Inici -Home" %}
Presentació de dades:  Entitat que es maneja en l 'aplicació

&#x20;\- **Posts** (per exemple
{% endtab %}

{% tab title="Login" %}
&#x20;Exclussivament **login** o **registre** (recordatori de si es vol crear un compte) o bé  si has oblidat la contrasenya
{% endtab %}

{% tab title="Escriptori -Dashboard" %}


Pantalla de treball de l’usuari

* &#x20;**CLIENT**: Pot crear les seves pròpies entitats i administrar el seu compte.&#x20;
* &#x20;**ADMIN**: Pot  administrar el seu compte i propietats així com de la resta d'entitats. \

{% endtab %}
{% endtabs %}

&#x20;

**Subpantalles - associades a les anteriors**

* Registre
* Registre de entitats (en general) - requereix token de formulari (\*)
* Edició, modificació d'entitats (en general) - requereix token  de formularis

_(\*)Per evitar atacs _[_CSRF_](https://es.wikipedia.org/wiki/Cross-site\_request\_forgery)__

**Navegació per pantalles**

Home —> Login

Login —> Dashboard

Login —> Registre

Dashboard—> Mostrar / Editar  Entitats



