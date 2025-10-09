Repasando el doc de TFM con ruben
regtel: click supervision

Evitar borde de máscara
Hay convoluciones con patrones que parecerían demasiado training
CROP: LEFT 70; RIGHT:20; TOP: 34; DOWN:34 (hay fuc)
```python
self.transform = transforms.Compose([  
    transforms.ToTensor(),  
    Crop(0, 70, 1080, 1350),  
    transforms.CenterCrop((992, 1344))])


class Crop:  
    _"""Crop to size with different center"""_    def __init__(self, top, left, height, width):  
        self.top = top  
        self.height = height  
        self.width = width  
        self.left = left    def __call__(self, x):  
        return transforms.functional.crop(x, self.top, self.left, self.height, self.width)
```
- Debería poder ver que tira igual a las labels en el train set (fijarse como mostrar eso)
- Probar con otra secuencia para entrenar
- Al video hacerle el crop para que no tire en los bordes
- Tengo que poner un condicional para aplicar la máscara especular en training o no
- la máscara de C está miu dilatada
# Charla con Ruben
-  Hacer un "ablation study" de la máscara especular: bajar el th de export (set D) y hacer un train (máximo 30k) para ver si evita las máscaras
- Hacer un "ablation study" con weight decay AdamW optimizer con el weight decay por default: agarrar hasta ahora el mejor modelo y probar con weight decay: Loss + lambda x norma de weights al cuadrado
- El heatmap está normalizado? agarrar el último de la red y fijarse si le aplican una normalización, me imagino que si
- Hacer unas métricas para tracking. Agarrar los histogramas por frame y agregarlos por secuencia.
- Vimos que con 1100 datos deja de aprender a las 30k. Esto da 430 epochs (según el 50 epochs era suficiente, pero los datos dicen otra cosa)
- Para validar, agarrar otro video directamente. Utilizar las 1100 imagenes para training
- Además agregar más datos de training (puedo probar con agregar otra secuencia)