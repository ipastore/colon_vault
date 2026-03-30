Estoy estudiando la diffs de la branch WP-11-38--SP-Integration, además la comparo con la main branch

## CmakeLists.txt

- No hace falta WP
```Cmake
set(CMAKE_PREFIX_PATH "/home/jmorlana/libtorch")
```
- Agrega include_directories
 ```Cmake
 ${PROJECT_SOURCE_DIR}/Thirdparty/SuperPoint
```

- Agrega eb add_library
```Cmake
${PROJECT_SOURCE_DIR}/Thirdparty/SuperPoint/SuperPoint.cc
${PROJECT_SOURCE_DIR}/Thirdparty/SuperPoint/SuperPoint.h
```

- Se comenta cuda_add_executable de mono_endo_c3vd_depth
En vez de mono_endo_hculb_depth hay que utilizar mono_endo_hculb_depth_sp

## Examples/Monocular/HCULB_Endoscope_07_seq027_rt_depth_sp.yaml

Se agrega el yaml para el mono:endo_hculb_depth_sp: CREO
En el primer commit no agrega ningun parametro de SP


## Examples/Monocular/mono_endo_hculb_depth_sp.cc

15 \#include <Thirdparty/SuperPoint/SuperPoint.h>

Inicia model
25     SuperPoint superPoint; 

Forward pass
322      std::tuple<Eigen::MatrixX2d, Eigen::MatrixXd> kpts_and_descs = superPoint.predict(frame); 

Pasa los kpts y descs
328         SLAM.TrackRGBD(frame, predMM, kpts_and_descs, vid_seconds, name);



## Thirdparty/SuperPoint/SuperPoint.cc

Archivo que aloja modelo SP. Acá se hace el heay-lifting. Hay que preguntarle a morlana que modelo cargó. Fijarse en Slack. 



## Thirdparty/SuperPoint/SuperPoint.h
Corresponding .h to .cc

Forward
75     const auto out = module.forward({input}).toTuple();

Y luego hay que hacer el post-prcessing: nodust, nms, depth2space 

## src/Frame.cc y .h
60 Modifica frame
```bash
Frame::Frame(const cv::Mat &imGray, const cv::Mat &imDepth, const std::tuple<Eigen::MatrixX2d, Eigen::MatrixXd> &kpts_and_descs, const double &timeStamp,  VocContainer* pVocCont, const float &b, const float &thDepth, GeometricCamera* pCamera, const cv::Mat &borderMask,cv::Mat *pImgColor, Frame* pPrevF,CudaSiftHandler* pCudaHandler)
```

80 Borra las líneas de extracCUDASIFT2BevtorKPs y lo adapta a lo que se hizo en Superpoint.cc

## src/System.cc y .h

Agrega a TrackRGBD los kpts_and_descs
Lo mismo para GrabImageRGBD

## src/Tracking.cc y .h
Lo mismo que en System.cc, agrega a GRABImageRGBD los kpts_and_descs

