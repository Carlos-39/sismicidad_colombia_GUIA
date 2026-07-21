# Predicción del próximo nodo activo en la red sísmica de Colombia

Semillero GUIA · Universidad del Valle
Autor: Carlos Daniel Corrales Arango (2122878)

## Contexto

Este repositorio retoma una línea de investigación del semillero basada en el
artículo:

> León-Vargas, D., Bucheli-Guerrero, V. A., y Ordoñez-Erazo, H. A. (2023).
> Modelado de la sismicidad colombiana como una red compleja
> espacio-secuencial. *Revista Científica*, 48(3), 67-81.
> https://doi.org/10.14483/23448350.20963

El artículo modela la sismicidad de Colombia como una red compleja
tridimensional (latitud, longitud, profundidad), donde cada celda geográfica
con actividad sísmica es un nodo y los enlaces siguen la secuencia temporal
de ocurrencia de los eventos. Encuentra que esta red tiene estructura de
**pequeño mundo (small-world)** de forma consistente, con un hub dominante
que corresponde al nido sísmico de Bucaramanga.

Este proyecto parte de esa base para responder una pregunta que el artículo
original no aborda: **¿esa estructura de red contiene información
explotable para predecir cuál será el próximo nodo en activarse?**

## Objetivo

Evaluar, con evidencia cuantitativa y comparando tres familias de modelos
de complejidad creciente, si la estructura de red aporta capacidad
predictiva sobre el próximo nodo de actividad sísmica, frente a un modelo
base simple.

## Resultado principal

| Modelo | Top-1 | Top-5 | Top-10 |
|---|---|---|---|
| Markov suavizado (baseline) | 6.6 % | 16.2 % | 19.9 % |
| Atención (secuencial) | 9.1 % | 17.1 % | 18.6 % |
| GNN (estructural) | 9.7 % | 17.0 % | 18.2 % |

**Ningún modelo superó de forma general al baseline probabilístico.** La
mejora de los modelos de atención y de grafos (GNN) está concentrada casi
por completo en los nodos de alta frecuencia (los hubs ya conocidos, como
el nido de Bucaramanga); en los nodos de frecuencia media y baja, los tres
modelos caen a 0% o cerca de 0%. La evidencia apunta a que el cuello de
botella real es el **volumen de datos disponible por nodo** (mediana de 2
observaciones por nodo de origen), no la sofisticación de la arquitectura.

Este es un resultado negativo pero metodológicamente válido, el detalle
completo de la discusión está en el informe de resultados (ver
`docs/informe_resultados.docx`).

## Contenido del repositorio

```
├── sismicidad_GUIA_CarlosCorrales.ipynb      # Pipeline completo: datos, EDA y los 3 modelos
├── docs/
│   └── informe_resultados.docx # Informe final de resultados
└── README.md
```

## El notebook

Un único notebook de Google Colab con seis secciones, pensado para
correrse de principio a fin o retomarse por secciones en sesiones
separadas:

| Sección | Contenido |
|---|---|
| 0 | Preparación de datos: partición temporal 70/15/15 (estrictamente cronológica) y codificación de nodos |
| 1 | Análisis exploratorio (EDA): distribución de nodos, geografía, patrones temporales |
| 2 | Baseline probabilístico: cadena de Markov de primer orden con suavizado |
| 3 | Modelo secuencial con atención |
| 4 | Modelo estructural de grafos (GNN, PyTorch puro) |
| 5 | Comparación final y discusión de resultados |

**Regla de diseño central:** la partición entre entrenamiento, validación y
prueba respeta estrictamente el orden cronológico de los eventos de que nunca se
mezclan al azar, para evitar que un modelo "vea" el futuro durante el
entrenamiento.

## Cómo correrlo

1. Abre `notebook_maestro.ipynb` en Google Colab.
2. Activa GPU (Entorno de ejecución → Cambiar tipo de entorno de ejecución →
   GPU → L4) antes de llegar a las Secciones 3 y 4.
3. Conecta tu Google Drive y ajusta `RUTA_DATOS` en la Sección 0 a la
   carpeta donde tengas los datos de origen (catálogo de eventos y
   ventanas temporales).
4. Corre las celdas en orden. Cada sección guarda sus artefactos en Drive,
   así que puedes retomar por sección en sesiones futuras sin repetir todo
   desde el inicio.

## Datos

Los datos de origen provienen del catálogo de terremotos del USGS-NEIC
para la región de Colombia, siguiendo la misma metodología de construcción
de red descrita en el artículo base (celdas cúbicas de tamaño fijo,
enlaces por secuencia temporal, sin self-loops ni conexiones múltiples).

**Limitación conocida:** los archivos de ventanas temporales usados en este
proyecto reconstruyen ~1056 nodos únicos para el tamaño de celda de 10 km,
mientras que el artículo original reporta ~3837 para el mismo tamaño de
celda. Es probable que estos archivos cubran un subconjunto del catálogo
completo de 46 años (1975-2021) en vez de su totalidad. Esta limitación se
documenta en el informe de resultados y queda como pregunta abierta para
una siguiente iteración del proyecto.

## Referencias

León-Vargas, D., Bucheli-Guerrero, V. A., y Ordoñez-Erazo, H. A. (2023).
Modelado de la sismicidad colombiana como una red compleja
espacio-secuencial. *Revista Científica*, 48(3), 67-81.
https://doi.org/10.14483/23448350.20963
