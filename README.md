# Clasificación de flores con redes neuronales convolucionales

Proyecto final de la **Electiva en Ingeniería de Software (redes neuronales)**, Universidad de San Buenaventura Cali.
**Autor:** Juan Miguel López Rojas · **Docente:** Carlos Mario Paredes Valencia

Se clasifican cinco especies de flores (diente de león, margarita, tulipán, girasol y rosa) del conjunto público
[`tf_flowers`](https://www.tensorflow.org/datasets/catalog/tf_flowers) (3.670 imágenes). Se compara una CNN entrenada
desde cero con MobileNetV2 preentrenada en ImageNet (aprendizaje por transferencia), y se reduce el sobreajuste
(overfitting) del modelo del avance anterior mediante aumento de datos, Dropout y regularización L2.

## Contenido

| Archivo o carpeta | Qué contiene |
|---|---|
| `Informe_Final_Clasificacion_Flores.pdf` | Informe técnico |
| `01_datos_y_entrenamiento.ipynb` | Descarga de datos, partición 70/15/15 y entrenamiento de los modelos (ejecutado) |
| `02_evaluacion_y_resultados.ipynb` | Análisis de sobreajuste, métricas en prueba, matrices de confusión, análisis de errores, prueba de McNemar entre modelos, Grad-CAM y comparación con la literatura (ejecutado) |
| `modelos/` | Modelos entrenados en formato `.keras` |
| `resultados/` | Archivos JSON con todas las métricas, `predicciones_prueba.csv` (predicción de cada modelo para cada imagen de prueba) y carpeta `figuras/` |
| `requirements.txt` | Versiones de las librerías usadas |

## Resultados

Exactitud medida sin aumento de datos ni Dropout. La brecha es la exactitud de entrenamiento menos la de validación, en puntos porcentuales.

| Modelo | Entrenamiento | Validación | Prueba | Brecha | F1 macro (prueba) |
|---|---|---|---|---|---|
| CNN base (desde cero) | 85,01 % | 82,00 % | 82,40 % | 3,01 | 81,91 % |
| MobileNetV2 del avance 2 (sin corregir) | 98,01 % | 91,27 % | 92,01 % | 6,74 | 91,81 % |
| MobileNetV2 regularizada, base congelada | 91,32 % | 88,55 % | 88,75 % | 2,77 | 88,41 % |
| **MobileNetV2 regularizada, con ajuste fino (modelo final)** | 97,28 % | 91,82 % | 91,83 % | 5,46 | 91,66 % |

El modelo final es la MobileNetV2 regularizada con ajuste fino: se eligió con el conjunto de validación (mayor exactitud y menor pérdida de validación) y, frente al modelo del avance 2, reduce la brecha de 6,74 a 5,46 puntos sin un cambio detectable en la exactitud de prueba. La versión con la base congelada tiene la menor brecha (2,77 puntos), pero acierta 3,1 puntos menos en prueba (prueba de McNemar, p = 0,003); se conserva como comparación porque muestra que reducir la brecha no equivale a mejorar el desempeño.

## Cómo reproducirlo

1. Instalar Python 3.10 o superior y las librerías: `pip install -r requirements.txt` (en Google Colab ya vienen instaladas).
2. Ejecutar completo `01_datos_y_entrenamiento.ipynb`. Descarga el conjunto de datos (233 MB) en `datos/`, entrena los
   modelos y los guarda en `modelos/`. En CPU tarda cerca de hora y media; con GPU, unos minutos.
3. Ejecutar completo `02_evaluacion_y_resultados.ipynb`. Genera las tablas, las figuras y `resultados/resultados.json`.
   Como los modelos entrenados ya están en `modelos/`, este notebook se puede ejecutar sin volver a entrenar. Necesita tres
   cosas: la carpeta `modelos/` con los archivos `.keras`, el archivo `resultados/entrenamiento.json` (ambos incluidos en
   este repositorio) y la carpeta `datos/`, que se obtiene ejecutando las dos primeras celdas de código del notebook 1
   (configuración y descarga).

La partición usa semilla fija (42). Los resultados pueden variar ligeramente entre máquinas por el orden de las
operaciones en punto flotante.

## Modelos

| Modelo | Archivo |
|---|---|
| CNN base entrenada desde cero | `modelos/CNN_base.keras` |
| MobileNetV2 con la configuración del avance 2 (sin corregir) | `modelos/MobileNetV2_avance2.keras` |
| MobileNetV2 regularizada, base congelada | `modelos/MobileNetV2_regularizada_fase1.keras` |
| MobileNetV2 regularizada, con ajuste fino | `modelos/MobileNetV2_regularizada.keras` |
| MobileNetV2 del avance 2 al final de la fase 1 (base congelada). El notebook 1 la guarda, pero el análisis no la usa | `modelos/MobileNetV2_avance2_fase1.keras` |

Las imágenes de `tf_flowers` tienen licencia Creative Commons Attribution 2.0.
