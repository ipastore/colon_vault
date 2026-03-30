revisar arquitectura del build para dgx. Quizás se puede parametrizar. Como?
Hacer un push con el script para testear la instalación
Hay que agregar el PYTHONPATH=build para que funcione
  
```bash
  PYTHONPATH=$PWD/ThirdParty/CudaSift-py-wrapper/build:$PYTHONPATH \
  python -c "import cudasift_py; print(cudasift_py.__file__)"
```

  
En la imagen de docker hay que incluir pybind11. Quizas lo podemos agregar desde el toml