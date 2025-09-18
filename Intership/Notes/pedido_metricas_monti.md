Es muy interesante el análisis que habéis hecho de los diferentes métodos de matching cuando se aplican a pares de imágenes del EndoMapper.

Quería proponer que profundizaseis más en las métricas para determinar la calidad de los matches.

1. Por una parte están las métricas que has reportado. Además del mAA para cada método sería informativo disponer del porcentaje de en cada uno de los 5 thresholds que habéis propuesto, podía reportarse en una única columna como:

/% th1/% th2/ % th3/ % th4/ % th5/ 

2. Quería proponer medir la calidad de cada uno de los matches y después reportar datos poblacionales. Disponemos de la pose relativa de las cámaras por COLMAP que tomamos con GT. Esta pose relativa nos da también una geometría epipolar entre las cámaras y por lo tanto para cada match podemos medir el error epipolar, esto es la distancia de cada match a la línea epipolar. 

- He creado un documento en overleaf [https://es.overleaf.com/3826838913mfcddrnnqfjq#608e18](https://urldefense.com/v3/__https://es.overleaf.com/3826838913mfcddrnnqfjq*608e18__;Iw!!D9dNQwwGXtA!UPwnVYOvYyQR-Iy7Cwo8FUZhJri4gPHZ-KghZZEMzndB3VZ_Z-rbUoAok6-phAAsLTGkq_rHv1tfooSdLigI$) que queden descritas las ecuaciones que empleamos en los cálculos. También es el primer borrador para el documento con los resultados. Incluye una hoja fotocopiada conde indico las ecuaciones del error epipolar simétrico esto es para el punto en la cámara 1, calculamos la epipolar en la cámara 2 y allí la distancia del punto en la imagen 2 a esta epipolar depende de la matriz E_21. Podemos pensar en el simétrico, coger el punto en la cámara 2, calcular la epiploar en la cámara 1 y después la distancia. Propongo que para cada match, el error sea la media de estos dos errores.

- Una vez hecho el cálculo de error de cada match, propongo añadir columnas a la tabla con número total de matches, error mediano y despues indicaría en una columna % matched por debajo de varios threshold, por ejemplo 1 pix, 2 pixels 5 pixels. 
    

3. Habría que añadir SP sin LG en la tabla. SP+SG tiene interés para emparejar imágenes separadas. Pero,  LG tiene un coste elevado y  LG necesita ser reentrenado cuando se reentrena SP y necesitamos supervisión para los matches, algo que hoy por hoy no tenemos. Sí que hay una alternativa: emplear sólo el descriptor de SP como se hace con SIFT. En este caso SP podría emplearse sin reentrenar LG y más importante podría emplearse en tracking con un costo muy bajo.

4. Habría que añadir SP-E en la tabla,  porque está especialmente entrenado para colonoscopia [https://infoscience.epfl.ch/server/api/core/bitstreams/241b4b88-d223-4c96-8804-8ed1803c0d7f/content](https://urldefense.com/v3/__https://infoscience.epfl.ch/server/api/core/bitstreams/241b4b88-d223-4c96-8804-8ed1803c0d7f/content__;!!D9dNQwwGXtA!UPwnVYOvYyQR-Iy7Cwo8FUZhJri4gPHZ-KghZZEMzndB3VZ_Z-rbUoAok6-phAAsLTGkq_rHv1tfoib4tAWz$). Ojo, SP-E sólo proporciona un descriptor y no puede combinarse con LG.
   
5. Guided matching para NN. ENhance in general.