Acabo de tener una reunión de seguimiento y tengo que modifcar los siguientes puntos: 
1) No computar error con 90, just the porcentage Nimg 

 No quiero penalizar a las imágenes con una rotación de 90 degrees para las imágenes que no tengan los suficientes matches. De hecho, para calcular la E matrix, necsito como mínimo 8 matches o 5? 
 
 2) Escalar individualmente o encontrar la máxima traslación 
 En cuanto a la escala del error de la traslación. He barajado varios opciones, la última fue calcular el bbox del submap de COLMAP y normalizar con esa medida pero no les gustó. Entonces quiero volver unos pasos atrás e implementar 2 opciones: a) escalar indovidualmente t_est con un scale factor (t_norm/t_est). b) Hacer una función aparte para calcular la máxima traslación de un mapa. Como tengo un covisibility graph hay que hacer un pair exhaustiive para cada línea que representa cada cámara y buscar el máximo de la traslación.
 
3) Usar covisibility graph para filtrar covisible images. Hay una función para ver el overlapping de las images vamos a ir uno por uno por importancia y te los reescribo:

En la ultima implementación estaba agarrando el img_train y subsampleandolo. Pero puede pasar el problema de agarrar camaras que nosean covisibles. Entonces puedo hacer uso del gráfico de covisibilidad. Para esto necesito implementar una fucnión para cargar en un diccionario todo el covisibility graph. Por otro lado, me dijeron que hay una función que busca el overlapping de cáda imagen con la otra. Entonces se podría buscar un threshold de covisibilidad y buscar en el image train esa covisibilidad. EN ese sentido se podría dejar como un hiperparámetro ea covisibilidad de las imagenes a matchear y se podróan plantear 3 diferentees niveles como easy, medium y hard (easy, imagenes conitugas, medium, en la mitad y hard son las imágenes con poca covisibilidad.) 

4) Como helpers podría tener funciones paraa graficar las traslaciones y las rotaciones de de cada unas de las cámaras.