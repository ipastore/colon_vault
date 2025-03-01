MUST: Poner imgenes siempre en las presentaciones

TODO: Tarea para la semana: Enfocarme en lightflue para fine-tunear (puedo plotear los all_kpts) y luego ver como da en COLMAP

NO TANTO TODO pero estaría bueno tenerlo masomenos cerrado el masking: Otras cosas que quiero hacer: aplicar la máscara a ROMA. Revisar (debugear) si todas las implementaciones de las máscaras están bien. Se deberían hacer tests pero no tengo mucho tiempo.

En principio puede haber 3 razones por las cuales con el resizing da más matches:
- Entrenamiento fuera de dominio
- Los parámetros para high quality estan muy altos
- Que en realidad haya que hacer un resizing. Esto se prueba con un ablation test.



