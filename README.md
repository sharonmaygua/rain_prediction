[# Proyecto de Clasificación de Predicción de Luvia en Australia utilizando Azure Machine Learning
Este espacio proporciona una explicación de todo el trabajo que se ha realizado en el proyecto de clasificación para predecir la vida en Australia utilizando el AzureML. 

A continuación, se detallan los pasos seguidos, las herramientas utilizadas y los fragmentos de código más importantes.

## Objetivo del Proyecto
El objetivo del proyecto es predecir si lloverá mañana en diferentes regiones de Australia utilizando distintos modelos de aprendizaje automático (Aprendizaje de máquinas) y eligiendo aquel que tiene mejor rendimiento.
## Herramientas Utilizadas
- Azure ML: Utilizado para el entrenamiento, despliegue y gestión de modelos de aprendizaje automático en la nube.
- MLFlow: Utilizado para el seguimiento y la gestión de los resultados y artefactos generales durante el proceso de entrada.
## Pasos Realizados

### 0) Colección, limpieza y separación de datos
El paso inicial es preparar los datos para el entrenamiento del modelo, con las siguientes tareas :

- Importar los datos: Se importan los datos de entrenamiento y prueba desde un archivo CSV .
- Eliminar los datos faltantes: Se eliminan datos faltantes o inconsistentes.

### 1). Configuración del Entorno

Una continuación, se describe los pasos para configurar el entorno :

- Se busca un recurso de computación (que preferente debe ser creado con anterioridad). En nuestro caso tiene el nombre de `cpu-cluster-prediction` Si no existe, se crea un nuevo recurso con las especificaciones:
  - Tipo: amlcompute
  - Tamaño: Standard_D4_v3
  - Tiempo de inactividad antes de la reducción de escala: 120 minutos
  - Número mínimo de instancias: 0
  - Número máximo de instancias: 6
  - Nivel de servicio: Dedicado

### 2). Entrenamiento del Modelo con AutoML

El siguiente paso es la configuración del trabajo de clasificación de AutoML. El código crea un trabajo con el nombre rain-prediction y un tiempo de espera de 30 minutos. El trabajo utilizará el recurso de cómputo cpu-cluster-prediction y los datos de entrenamiento y validación proporcionados. La columna de destino es RainTomorrow y la métrica principal es precision_score_weighted. Además, se habilita la explicabilidad del modelo. Se establecen los siguientes límites para el trabajo:
 
- Tiempo de espera: 30 minutos
- Número máximo de ensayos: 5
- Número máximo de nodos: 4
Este trabajo de clasificación de AutoML entrenará un modelo para predecir si habrá lluvia en Australia al día siguiente. El trabajo se completará después de 30 minutos o si se han completado 5 ensayos, lo que ocurre primero.

### 3). Ejecución y Seguimiento del Trabajo de Clasificación
El código proporcionado ejecuta el trabajo de clasificación de AutoML y se envía al backend de Azure Machine Learning Studio. El backend ejecutará el trabajo y entrenará varios modelos. El código también imprime el identificador del trabajo creado, que se utiliza para realizar un seguimiento del estado del trabajo y obtener los resultados del entrenamiento.


### 4). Resultados del Entrenamiento

Se entrenan varios modelos, incluido un StackEnsemble, un VotingEnsemble y otros modelos. El mejor modelo, según la métrica de precisión, es el modelo StackEnsemble con una precisión de, por ejemplo, 85.54%. Luego, se propone la URL para acceder a la información del trabajo, incluido el estado actual y los resultados del modelo.

### 5). Acceso a los Resultados a través de MLFlow
El proyecto utiliza MLFlow para acceder a los resultados del trabajo de clasificación. Se inicializa un cliente de MLFlow y se obtiene la URL de seguimiento para MLFlow. Esta URL se utiliza para conectar al servidor de MLFlow y acceder a los resultados del trabajo, como el mejor modelo y sus métricas.


### 6). Implementación del Mejor Modelo en un Recurso de Punto de Conexión en Línea 
Posteriormente, se registra el mejor modelo y lo implementa en un recurso de conexión en línea. El registro del modelo se realiza utilizando el código proporcionado y luego se procesa a la implementación.



### 7). Prueba del Modelo Implementado
Se muestra cómo probar el modelo implementado en el punto de conexión en línea. Se crea un objeto de solicitud JSON con datos de prueba, se guarda en un archivo y se invoca el punto de conexión en línea. Los resultados de la prueba se imprimen en la consola.

### 8). Descarga de Artefactos del Modelo
El proyecto también permite descargar los artefactos del modelo implementado en una carpeta local llamada artifact_downloads. Estos artefactos incluyen el modelo entrenado y otros archivos necesarios para su funcionamiento.

](https://github.com/sharonmaygua/rain_prediction/blob/main/README.md)https://github.com/sharonmaygua/rain_prediction/blob/main/README.md
