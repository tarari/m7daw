# Seguretat en PHP

Un aspecte fonamental en qualsevol aplicació és l'aspecte de la seguretat. 

En la comunicació \(REQUEST i RESPONSE\), en el traspàs d'informació \(formularis\), en la navegació \(pas de pantalles\).

## TOKENS

Els tokens representen una comprovació fonamental en els formularis, que determinen si hi ha continuitat en la comunicació, més enllà de l'ús de PHPSESSID. 

La idea és la següent:  Passar una dada al formulari \(token \) i comprovar en destinació si aquesta dada és exactament igual que l'original, si no ho fos, tenim un problema de seguretat.





