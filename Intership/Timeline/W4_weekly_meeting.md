Date: 20/02/25
Attendees: Ruben, Morlana, Rodirguez

## Aplicar máscaras
Lo que hice la semana pasada fue aplicar las máscaras a posteriori. Eso afecta a los matchers, tanto los sparse como los dense. 

Entonces tengo que aplicar la máscara antes: 
- Para los sparse con detectors:  **filtrar keypoints**

Por ejemplo: En lightglue.py

/Users/ignaciopastorebenaim/Documents/MGRCV/COLON/colon_matching/utils/image-matching_models/matching/third_party/LightGlue/lightglue/utils.py

line 150: 
```python
def match_pair(extractor,matcher,image0: torch.Tensor,image1: torch.Tensor,device: str = "cpu",**preprocess,):

"""Match a pair of images (image0, image1) with an extractor and matcher"""

feats0 = extractor.extract(image0, **preprocess)
feats1 = extractor.extract(image1, **preprocess)
matches01 = matcher({"image0": feats0, "image1": feats1})
data = [feats0, feats1, matches01]

# remove batch dim and move to target device
feats0, feats1, matches01 = [batch_to_device(rbd(x), device) for x in data]

return feats0, feats1, matches01
```

- Para los dense com ROMA o LOFTR: **ver como se puede aplicar la máscara** 
NO preoucparse con ser muy generalista porque vamos a utilizar algunos modelos nomás.

## Métricas
En este momento no se puede sacar una buena conclusión de qué modelo funciona mejor. Para esto vamos a utilizar una secuencia real que utilizaron COLMAP para armar la trayectoria. La idea es agarrar algún submapa con la lógica de easy, medium, hard. Y comparar esos casos con la E matrix, sacando las poses de las cámaras. En algún punto se parece mucho al paper de la review que armaba una groundtruth con COLMAP y luego para cada par de imagenes sacaba las poses e iba modificando el threshold. Si no superaba el treshold la rotacion y la traslación lo ponía como la identidad (creo que sin rotacion y traslación). La diferencia es que no se va a hacer sfm sino trayectoria. 

En este enlace se ve las posibles secuencias que se podrían utilizar que tienen COLMAP 3D reconstructions: [Dataset Summary](https://docs.google.com/spreadsheets/d/1b9FULhh13dPhbqCXLcFTbAlwhKRGFReW/edit?usp=drive_link&ouid=104532659855887590320&rtpof=true&sd=true)

En este caso son las [Seq_001](https://drive.google.com/drive/folders/15gNjjvChngzfs97YOb17_lk_jubLrB3j?usp=drive_link) y las [Seq_002](https://drive.google.com/drive/u/4/folders/15hjA1wC2XqB1SMrFGkN1mnwpNxaIpl3k)

Cada una consiste de varios submapas, con los cuales podría pensar algo parecido a los casos easy, medium, hard. 

**Tengo que instalar COLMAP en ubuntu** 