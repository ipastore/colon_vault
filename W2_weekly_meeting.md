		TL;DR:
- Trabajar de manera iterativa con resultados rápidos.
- Probar casos faciles, intermedios y avanzados. Los datasets me los va a pasar Morlana.
- Implementar primero LG+SP+GIM(encontraré el repo con GIM?). Luego implementar Roma y SIFT (o CudaSift).
- Entender SP, LG, ROMA, SIFT y CudaSIFT.
- Preocuparse por las métricas luego.
- Más adelante se podría entrenar LG con un dataset del colon
- Aplicar las máscaras especulares en el preprocesamiento (tengo que hacer algo con fish-eye y la resolucón?).
- Recibir la data y aplicar las implementaciones existentes. Earthmatcher creo que tiene varias. Buscar más.
- Buscar si hay algo de LG + CudaSift.

Attendees: JMM, Ruben, Morlana, Rodriguez

Gantt, de manera iterativa. Leer bibliografía: iterativo. Más de research. 

Centrarme en SuperGlue. 
Luego en el TFM 
reconocimiento de lugares: Parecido al assignment del colon. 

11, 28 y 29
No vamos a probar detectores que no tengan puntos porqu enuestro sustema e s de todos puntos. Rechazamos Loftr y ROma(al final quieren Roma)

Slam basdo en puntos: ORB, dentro del cuerp no funciona muy bien. Cuda sift funcionaría mejor pero no suficiente. 

Conversación:
Detector free no sirven tanto
- teiendo mapa de cudasift, te pierdes. Puedes coger el keyframe y haces matches densos e interpolas el punto más cercano. 
- No tienes por que coger el point de ROMA. 
- Lo que me atrae del Superpoint es que la geometria es demasiado burta. O esta bien a pixel o no vale. CUando hay algo de deformación te los cargas todos. Sin embargo con superpoint lo entreno con rigido pero es más laxo
Otro: la idea era evaluar los más prometedores
(pero no descarten el los detector free)

Elección
Loftr Morlana y Puigvert lo han hecho. 
Ruben: 

Conclusion: probar con Superpoint + LightGlue
Dejanos pensar las métricas. 
Preaparar dataset para loop closing y perderse (esto me lo pasa Morlana)
Tienes esos COLMAPS? Tnego los mapas separados porkeyframes y etiquetados masomenos. 
Pueso ahora podemos coger unas pocas imagenes. 5 pares o 10
Puedes pasarle las imágenes a Nacho.
Nos da un problema representativo el phatom? Las secuencias son muy pequeñas: Aquí los tiene que encontrar.  No quieren usar phantom porque es mucho más fácil que el matchin verdader.
En el screening tenemos 

Otra idea usar el dataset (invarianza al giro) del assignment. Seq_027 y Seq_035.. Elegir 10 pares de fotos que los elige moralna interesnates y probar con 2 o 3 métodos. 
Nivel fácil, nivel intermedio, nivezl avanzado (mismo paciente con dos secuencias).
Tiene sentido ir al phantom o nos complicamos la vida?
Puigvert: yo soy un phatom-lover: por qué tenemos que cambiar en este caso?

Secuencia fácil dentro del mapa (?)
Secuencia intermedio: no entendí
Secuencia difícil: 2 secuencias del mismo paciente separados por el tiempo (Seq_027) and (Seq_035)

Acceso al dataset

Son covisibles: data - labeling a ojo
Matches: data labeling imposible.

Se puede partir en 2 el problema: 
1) Las fotos ven lo mismo? Son covisibles y se puede hacer data - labeling a ojo
2) Matching que ya no se puede etiquetar

JMM: Benchmarking ni idea pero ahora para empezar hacemos otra solucion y despues vemos. A día de hoy sabemos que las imagenes covisibles fallan en el matching

Esto es un problema de dominio. Por ejemplo Roma ha sacado dos modelos: uno indorr y otro outdoors. 

El grupo a intentado de entreanar Superpoint. León lo ha hecho. Pero no hay modelos 3D densos del colon para entrenar LightGlue. 

El superpOint de León si qe está entrenado. Light glue: agarra los descroptores y los compara con los de la zona circundante. 

Conclusión preliminear: SIFT ( o Cuda SIFT como vemos luego). SP + LG + GIM, ROMA (backbone Dino; cuanto cuesta el Fast Forward?). En cudaSift tarda 5000s? Sin embargo para place recognition tienes más tiempo.
Roma tardaría 200-300s? Se puede hacer una destilación
El ROMA está entrenado con el Megadepth?

Roma tiene el backbone de DIno y esta con fine-tuning con Megadepth

Idea para más adelante: Hacer secuencias del Endomapper para entrenar.

Usar phantmo? Puigvert Si. Moralan y Montiel NO.

Revisar la diferencia de los modelos con y sin detector. No entendí bien. Pero se ve que los que no tienen detector los puntos para hcaer el mapa no son los mismos que te da para hacer los matches. Sin embargo esto podría serviar para una buena aproximación y luego utilizar super-glue.

Use SIFT (or CudaSIFT) as dthe baseline. 

Para todo esto tengo que aplicar la máscara de especularidad y luego hacer el matching.

Hay máscaras buenas en el artículo de CUdasift que las hizo Richard. Me las va a entregar Moralana. Está en C++ pero hago 20 fotos con 20 máscaras y las guardo en el disco. La idea no es integrar nada por ahora. Utilizar lo que ya está hecho y pegarlo con moco. 

CudaSift no está en Python. Se puede hacer un ejecutable y sacar el .json con los descriptores y los matches. 

JMM dice que LightGLue fue entrenado con SIFT, no CUDASIFT. entonces deberías hacerse el baseline con SIFT y no CUDA. Sin emabrgo como dice Ruben, buscar como se haría en google. 

Conclusión final: 
Morlana me va a pasar los 3 datasets con las imagenes. 
Moralana me va a pasar las máscaras.
Yo tengo que leer:
	- Super Point
	- Light Glue
	- SIFT
	- CudaSIFT
	- ROMA
Buscar repos que implementen esto:
- Empezar con earthmatcher pero buscar más
























