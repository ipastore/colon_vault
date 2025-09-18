Al final matchear imágenes consecutivas es fácil para el matcher pero difícil para calcular la pose por poco paralaje. 

Lo que tengo que hacer es encontrar el baseline para el report. En este caso, en vez de utilizar el covisibility graph, utilizar el paralaje para encontrar un caso que tenga mAA = 1.

Rubén había tenido una idea de matchear 1-2; 1-3; 1-4; 1-5, etcc.. para ver como iba aumentando el mAA o el pose estimation.

Quedamos en que Morlana me tiene que pasar lo que había implementado para calcular el paralaje de las imágenes. 

TODO: testear con 4 casos un paralaje para el cual se pueda alcanzar mAA = 1