# Mail Monti
Te paso una descripción de los algoritmos de BF-NDDR-Bidireccional y del BF-NN-Bidireccional con los correspondientes umbrales: TH_D, y TH_NNDR. Esto pasado a limpio en látex podría ser un apéndice para tu TFM.

Distancia, correlación y coseno.

1. sean d1 y d2 son dos descriptores vectoriales de dimensión n. d1 y d2 están normalizados a norma 1
    
2. c12=d1.d2, \[-1 ,1\] (producto escalar) es el coseno del ángulo que forman, también es la correlación entre los dos vectores.
    
3. d12=sqrt(2(1-c12)) \[2,0\] es la distancia entre los dos vectores
    

4. d1 y d2 ortogonales, no tienen nada que ver, cos=0, correlación = 0 %, distancia=1.41
    
5. d1 y d2 formando cos=0.8, correlación 80 %, distancia = 0.63, ángulo +-37 deg
    
6. d1 y d2 formando cos=0.9, correlación 90 %, distancia = 0.45, ángulo +-26 deg
    
7. d1 y d2 colineales, son el mismo vector, cos=1, correlación 100 %, distancia 0, ángulo 0 deg
    
8. d1 y d2 van en direcciones opuestas, cos=-1, correlación -100 %, distancia 2, ángulo +-180 deg
    

  

Algoritmo BF-NNDR-Bidireccional

1. Se normalizan los descriptores de todos los puntos a norma euclidiana 1.
    
2. para cada punto de la imagen 1, en la imagen 2 el punto con el descriptor más cercano a distancia D1, y el segundo más cercano a Distancia D2
    
3. si D1 < Th_D ( por ejemplo 0.5-0.7 que corresponde a una correlación (coseno) entre 90 %- 80 % (0.9, 0.8) se acepta
    
4. si además  D1/D2 < Th_NDDR (por ejemplo 0.7, ente caso quiere decir que  D1 es un 30 % menor que el D2), se admite el match imagen1-imagen2
    
5. Se calculan los matches entre la imagen 2 y la imagen 1
    
6. Sólo sobreviven los emparejamientos comunes en las dos direcciones.
    

  

Algoritmo BF-NN-Bidireccional

1. Se normalizan los descriptores de todos los puntos a norma euclidiana 1.
    
2. para cada punto de la imagen 1, en la imagen 2 el punto con el descriptor más cercano a distancia D1, y el segundo más cercano a Distancia D2
    
3. si D1 < Th_D ( por ejemplo 0.5-0.7 que corresponde a una correlación (coseno) entre 90 %- 80 % (0.9, 0.8) se acepta
    
4. Se calculan los matches entre la imagen 2 y la imagen 1
    
5. Sólo sobreviven los emparejamientos comunes en las dos direcciones.

Conseguimos que SP vaya mejor bajando NN. Los descriptores son buenos pero no están tan alienados ya que el bidireccional funciona bien (en algubos casos)  
No buscar nada entre retrainy LG
LG, bajand th

Por que en el tensorboard la neg_dist que la pos. 
implementar nndr
Investigar lambdas: normalizaciones
Investigar LG con geometrica
Integrar CUDASIFT al pipeline oficial de LG. Mirar que tan facil es y analizar.

[[ensayos_thresholds_bf_nn_dr]]
