# 🛡️ Clasificación de solicitudes de ciberseguridad defensiva mediante Fine-Tuning de BERT

Proyecto académico de Inteligencia Artificial y Procesamiento de Lenguaje Natural (NLP), cuyo objetivo es desarrollar un modelo de clasificación multiclase capaz de identificar el dominio de ciberseguridad al que pertenece una solicitud escrita por un usuario, utilizando Fine-Tuning del modelo BERT.

## 📌 1. Descripción del proyecto

El proyecto utiliza técnicas de aprendizaje automático y procesamiento de lenguaje natural para clasificar solicitudes relacionadas con ciberseguridad defensiva en **17 dominios temáticos**.

Se implementa un modelo basado en BERT (`bert-base-uncased`), adaptado mediante Fine-Tuning con el conjunto de datos HolyCyber.

El sistema recibe una solicitud escrita en lenguaje natural y predice el dominio al que pertenece.

**Objetivo principal:** entrenar, evaluar y comparar un modelo BERT especializado en clasificación de solicitudes de ciberseguridad defensiva, utilizando métricas de rendimiento y un modelo tradicional como referencia.

## 🎯 2. Objetivos específicos

- Descargar y analizar el conjunto de datos HolyCyber.
- Realizar limpieza, transformación y auditoría de los datos.
- Identificar la distribución de las 17 categorías.
- Preparar los textos mediante tokenización.
- Adaptar el modelo preentrenado BERT para clasificación multiclase.
- Experimentar con diferentes hiperparámetros.
- Seleccionar la mejor configuración mediante validación.
- Evaluar el modelo utilizando datos de prueba independientes.
- Comparar BERT con un modelo tradicional basado en TF-IDF y regresión logística.
- Analizar errores y permitir la clasificación de nuevas solicitudes.

## 📚 3. Dataset utilizado

Se utiliza **HolyCyber**, disponible en Hugging Face:

- Dataset: [Reizxn/HolyCyber](https://huggingface.co/datasets/Reizxn/HolyCyber)
- Versión del dataset: 2.3.0.
- Revisión: `main`.
- Tarea: clasificación multiclase de texto.
- Etiqueta objetivo: `domain`.
- Número de categorías: 17.

El conjunto de datos se encuentra dividido en tres particiones:

| Partición | Uso |
|---|---|
| Train | Entrenamiento del modelo. |
| Validation | Selección de hiperparámetros y evaluación durante los experimentos. |
| Test | Evaluación final independiente. |

El proyecto extrae únicamente el contenido de los mensajes cuyo rol es `user`, evitando utilizar las respuestas del asistente como información de entrada.

Los metadatos adicionales, como `subdomain`, `task_type` y `difficulty`, se utilizan durante el análisis exploratorio, pero no como características del clasificador.

## 🧰 4. Tecnologías y herramientas

| Tecnología | Uso |
|---|---|
| Python | Lenguaje de programación. |
| Google Colab | Entorno de ejecución. |
| PyTorch | Framework de aprendizaje profundo. |
| Hugging Face Transformers | Carga y entrenamiento de BERT. |
| Hugging Face Datasets | Gestión del conjunto de datos. |
| Hugging Face Hub | Descarga de archivos y modelos. |
| Pandas | Manipulación de datos. |
| NumPy | Operaciones numéricas. |
| Scikit-learn | Métricas y modelo baseline. |
| Matplotlib | Visualización de resultados. |
| Seaborn | Gráficas y mapas de calor. |
| Accelerate | Optimización del entrenamiento. |

### Versiones principales

- Transformers: `5.22.1`
- Datasets: `4.8.5`
- Accelerate: `1.15.0`
- Hugging Face Hub: `1.32.0`
- Pandas: `2.2.3`
- PyArrow: `23.0.1`
- Fsspec: `2025.12.0`

PyTorch se conserva en la versión proporcionada por el entorno de ejecución para mantener la compatibilidad con GPU.

## ⚙️ 5. Arquitectura y metodología

El proyecto está organizado en las siguientes etapas:

### 5.1. Preparación de datos

- Descarga de los archivos de entrenamiento, validación y prueba.
- Extracción del mensaje del usuario.
- Conversión de los registros a DataFrames.
- Limpieza de valores nulos y registros incompletos.
- Verificación de las 17 categorías.
- Auditoría de duplicados y separación de grupos para evitar fuga de información.

### 5.2. Análisis exploratorio (EDA)

Se realiza un análisis estadístico y visual del dataset para identificar:

- Distribución de los dominios.
- Frecuencia de los tipos de tareas.
- Distribución de subdominios.
- Niveles de dificultad.
- Diferencias entre las particiones.
- Ejemplos de solicitudes por categoría.

### 5.3. Tokenización

Se utiliza el tokenizador de `bert-base-uncased`, basado en WordPiece.

Parámetros principales:

| Parámetro | Valor |
|---|---|
| Modelo | `bert-base-uncased` |
| Longitud máxima | 256 tokens |
| Semilla | 42 |
| Número de clases | 17 |

Los textos se convierten en identificadores numéricos que pueden ser procesados por BERT. Se utiliza truncamiento para las solicitudes que superan los 256 tokens y padding dinámico para organizar los lotes.

### 5.4. Fine-Tuning de BERT

Se carga `BertForSequenceClassification` utilizando el checkpoint preentrenado `bert-base-uncased`.

Se agrega una nueva capa de clasificación configurada para las 17 categorías del dataset.

Durante el Fine-Tuning, el modelo ajusta sus parámetros para aprender a identificar el dominio correspondiente a cada solicitud.

Se utilizan las siguientes métricas:

- Accuracy.
- Precision Macro.
- Recall Macro.
- F1 Macro.
- F1 Weighted.

La métrica principal para seleccionar la configuración es F1 Macro, debido a que otorga la misma importancia a cada categoría.

### 5.5. Experimentación de hiperparámetros

Se comparan cinco configuraciones con diferentes combinaciones de:

- Learning Rate.
- Batch Size.
- Número de épocas.
- Función de pérdida estándar y ponderada.

Para optimizar el tiempo de entrenamiento, se utiliza una muestra estratificada de hasta 12.000 registros de entrenamiento durante los experimentos.

Se selecciona la configuración con mejor F1 Macro en validación.

### 5.6. Entrenamiento final

Una vez seleccionada la mejor configuración, se entrena nuevamente el modelo BERT utilizando los conjuntos `train` y `validation`.

El conjunto `test` permanece reservado para la evaluación final.

### 5.7. Modelo baseline

Se implementa un modelo tradicional de comparación utilizando:

- TF-IDF.
- Unigramas y bigramas.
- Regresión logística mult
