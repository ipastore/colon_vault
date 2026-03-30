desde 0010 no_bin a 0010 con 5 bines hay una marcada diferencia en el overfitting, mejora con el binning
´


![[Screenshot 2026-01-05 at 01.08.19.png]]

![[Screenshot 2026-01-05 at 01.08.45.png]]

![[Screenshot 2026-01-05 at 01.09.20.png]]
En cuanto al overlap cuando se utiliza 0010 se tenían en cuenta las imágenes idénticas, lo cual lo hace más fácil para el recall, pero al ver el rlativo tienen moviemientos parecidos 0010 y 0070. Hay que confirmar esto con las parejas fijas de pairs y el 0099

En cuanto al lr a pesar de que se ve más overfitting con 1e-5 y 5e-06, las curvas de recall en val quedan parecidas. Ya con 1e-05 diverge y cae la recall. Hay que desempatarlo con la rotation a ver si podemos mejorar la recall con lr más altos


![[Screenshot 2026-01-05 at 01.14.46.png]]

![[Screenshot 2026-01-05 at 01.15.36.png]]

![[Screenshot 2026-01-05 at 01.16.08.png]]

![[Screenshot 2026-01-05 at 01.17.00.png]]

![[Screenshot 2026-01-05 at 01.17.37.png]]
