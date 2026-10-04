# Predicción de riesgo cardiovascular mediante IA: una perspectiva de género

Trabajo Fin de Máster · Máster en IA Aplicada a Aplicaciones Sanitarias · Edición 2025-2026
**Autora:** Maribel Villagrasa

[![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/maribelvillagrasa-star/tfm-riesgo-cardiovascular-genero/blob/main/notebooks/tfm_riesgo_cardiovascular_genero.ipynb)

## Descripción

Este repositorio contiene el código del TFM. El trabajo desarrolla un modelo de clasificación para predecir la presencia de enfermedad cardiovascular a partir de variables clínicas (dataset UCI Heart Disease, cohorte de Cleveland) y evalúa de forma desagregada por sexo su rendimiento y la solidez estadística de las diferencias observadas.

El dataset no fue diseñado para estudiar diferencias entre hombres y mujeres: solo el 32 % de los registros (97 de 303) son mujeres. El trabajo lo trata como un análisis secundario y exploratorio, y analiza hasta qué punto ese desequilibrio permite sacar conclusiones fiables sobre el subgrupo femenino.

## Contenido del repositorio

```
├── README.md
├── LICENSE                 # MIT (aplica al código)
├── requirements.txt
├── data/
│   └── heart.csv           # UCI Heart Disease (Cleveland), CC BY 4.0
└── notebooks/
    └── tfm_riesgo_cardiovascular_genero.ipynb
```

## Correspondencia con la memoria

| Bloque del notebook | Capítulo de la memoria |
|---|---|
| Análisis exploratorio (EDA) | 5. Análisis Exploratorio de Datos |
| Preparación de los datos | 6. Preparación de los datos |
| Selección de modelos y entrenamiento | 7. Selección de modelos y entrenamiento |
| Validación y evaluación | 8. Validación y evaluación del modelo |
| Optimización (importancia de variables, umbral, robustez) | 9. Optimización del modelo |
| Análisis adicional (Spearman, matrices de confusión, intervalos de Wilson, bootstrap, fairness, SHAP) | 5.2, 8.1 y 9 |

## Cómo ejecutarlo

**En Google Colab:** pulsa el botón «Abrir en Colab» de arriba. El notebook carga `heart.csv` directamente desde este repositorio, sin depender de ningún almacenamiento privado. Antes de ejecutar los análisis adicionales, instala las dependencias que no vienen por defecto:

```python
!pip install statsmodels shap --quiet
```

**En local:**

```bash
git clone https://github.com/maribelvillagrasa-star/tfm-riesgo-cardiovascular-genero.git
cd tfm-riesgo-cardiovascular-genero
pip install -r requirements.txt
jupyter notebook notebooks/tfm_riesgo_cardiovascular_genero.ipynb
```

En ese caso, en la celda donde se define `data_path`, usa la ruta local `../data/heart.csv`, ya que Jupyter ejecuta el notebook desde su propia carpeta (`notebooks/`).

Se fija `random_state=42` en los modelos y en las particiones. Con versiones distintas de las librerías, las cifras pueden variar ligeramente, y el formato de salida de `shap` cambia entre versiones.

## Resultados principales

Resumen de lo que se detalla en la memoria (conjunto de test de 61 pacientes, Random Forest optimizado):

- Con el umbral por defecto (0,50): AUC-ROC 0,960, sensibilidad 0,929 y especificidad 0,909.
- Con ese mismo umbral, sensibilidad en mujeres 0,857 (6 de 7 casos positivos) frente a 0,952 en hombres (20 de 21). La diferencia no es estadísticamente significativa (bootstrap, p ≈ 0,78) y los intervalos de confianza se solapan ampliamente.
- Con validación cruzada repetida (50 particiones), la desviación estándar de la sensibilidad en mujeres es entre 2,7 y 3,6 veces mayor que en hombres (según el umbral): el tamaño del subgrupo femenino limita la fiabilidad de cualquier conclusión sobre él.
- **Umbral de decisión:** además del umbral por defecto (0,50), se evalúa un umbral operativo τ = 0,30, fijado *sin usar el test*: se aplicó un criterio de seguridad definido de antemano (sensibilidad global ≥ 0,90 con la mayor especificidad posible) a probabilidades fuera de muestra obtenidas solo con el conjunto de entrenamiento. En test, la sensibilidad es 1,000 en ambos sexos, con especificidad global 0,667 (11 falsos positivos frente a 3 a umbral 0,50). En validación cruzada repetida, la sensibilidad en mujeres es 0,882 ± 0,167 (hombres 0,939 ± 0,047): el 1,000 del test es probablemente optimista.

## Limitaciones y aviso

- Muestra pequeña y desequilibrada por sexo, de un único centro y recogida hace décadas.
- El dataset no incluye síntomas atípicos, que la literatura asocia a la presentación del síndrome coronario agudo en mujeres.
- **Este código es un trabajo académico y exploratorio. No está diseñado ni validado para su uso clínico.**

## Datos y licencias

- **Código:** licencia MIT (ver `LICENSE`).
- **Datos:** el fichero `data/heart.csv` procede del repositorio UCI Machine Learning (Heart Disease, cohorte de Cleveland), distribuido bajo licencia [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Fuente: https://archive.ics.uci.edu/dataset/45/heart+disease
- Referencia original: Detrano R, Janosi A, Steinbrunn W, et al. International application of a new probability algorithm for the diagnosis of coronary artery disease. *Am J Cardiol.* 1989;64(5):304-310.
