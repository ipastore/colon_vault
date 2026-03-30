# Como rotar las features de SIFT

## Keypoint

- Aplicar rotación desde el centro óptico
- Filtrar keypoints que caen fuera de la imagen
- Llevar posición al suelo (floor)
- Posible diferencia de 1 pixel de distancia debido a discretización

## Scales

- No se necesitsa de transofrmación

## Orientations

- Aplicar una traslación de +sigma.
  
  
## Descriptors

- Lo dejo tal cual como está, asumiendo un descriptor totalmente invariante a la rotación. En la práctica cuando se gira una imagen y se interpola, los gradientes cambian ligeramente y además podrían caer en distintos bines del histograma, lo cual también hacen que cambie el descriptor. Nosotros asumiríamos un descriptor perfecto. 