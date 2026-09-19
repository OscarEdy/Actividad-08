# Actividad 08 (Unidad II) — Clustering Jerárquico

**Curso:** Maestría en Ciencia de Datos — Aprendizaje No Supervisado / Minería de Datos
**Objetivo:** Comprender el uso del agrupamiento por jerarquías (*hierarchical clustering*) aplicándolo a tres dominios: texto, segmentación de clientes y segmentación de imágenes.

## Contenido del repositorio

| Archivo | Descripción |
|---|---|
| `Actividad08_Clustering_Jerarquico.ipynb` | Cuaderno de Google Colab con el código completo, comentado y ejecutable de las tres partes de la actividad. |
| `Resumen_Ejecutivo_Actividad08_APA7.pdf` | Resumen ejecutivo en formato APA 7.ª edición (portada, resumen, cuerpo, tabla comparativa y referencias). |
| `README.md` | Este archivo. |

## Cómo ejecutar el notebook

1. Abrir `Actividad08_Clustering_Jerarquico.ipynb` en [Google Colab](https://colab.research.google.com/) (`Archivo > Subir cuaderno` o arrastrarlo directamente).
2. Ejecutar las celdas en orden (`Entorno de ejecución > Ejecutar todas`).
3. La **Parte 1** (texto) descarga automáticamente el dataset *20 Newsgroups* desde los servidores de scikit-learn (requiere conexión a internet, disponible por defecto en Colab).
4. La **Parte 2** (clientes) intenta descargar un CSV equivalente al *Mall Customer Segmentation Dataset* de Kaggle; si no está disponible en el entorno, genera automáticamente un dataset sintético estadísticamente equivalente. Si se cuenta con el archivo original de Kaggle, puede subirse a Colab como `Mall_Customers.csv` para usarlo directamente.
5. La **Parte 3** (imágenes) usa una imagen incluida en `scikit-image` (no requiere descarga); el código es directamente adaptable a imágenes del dataset **BSDS500** (basta con reemplazar la carga de la imagen por una URL o archivo local de ese dataset).

## Resumen de la metodología

Para cada dominio se siguió el mismo flujo de trabajo:

1. Carga y preprocesamiento de los datos.
2. Construcción de una representación numérica adecuada (TF-IDF + LSA para texto; estandarización para clientes; espacio de color CIE-Lab para imágenes).
3. Cálculo de matrices de enlace (*linkage*) con **al menos dos criterios distintos**: `ward`, `complete`, `average` y (en texto y clientes) `single`.
4. Visualización de dendrogramas.
5. Corte del árbol jerárquico y evaluación cuantitativa (coeficiente de silueta, índice de Rand ajustado, homogeneidad, completitud, varianza intra-clúster según el dominio).
6. Interpretación de los hallazgos.

## Resultados principales

### 1. Clustering jerárquico para texto (20 Newsgroups)

- Subconjunto de 4 categorías (`comp.graphics`, `rec.sport.baseball`, `sci.space`, `soc.religion.christian`), vectorizadas con TF-IDF y reducidas con TruncatedSVD (LSA) antes de calcular los enlaces.
- **`ward`** (sobre el espacio LSA) obtiene típicamente el mejor equilibrio entre silhouette y concordancia con las categorías reales (ARI, V-measure).
- **`single`** es el más débil por el efecto de encadenamiento (*chaining effect*): agrupa casi todo en un único clúster.
- La inspección de los términos TF-IDF más representativos de cada clúster permite etiquetar temáticamente los grupos sin conocer las categorías reales.

### 2. Segmentación de clientes (Mall Customer Dataset / sintético)

- Variables: edad, ingreso anual, puntaje de gasto (estandarizadas antes del clustering).
- **`ward`** produce los segmentos más compactos y con mayor silhouette, generalmente entre 5 y 6 clústeres, coincidiendo con la segmentación clásica reportada para este dataset (p. ej. "ingreso y gasto altos" como segmento objetivo, "ingreso alto/gasto bajo" como oportunidad de venta cruzada).
- **`single`** vuelve a ser el criterio menos útil por el mismo efecto de encadenamiento.

### 3. Segmentación de imágenes

- Representación de píxeles en espacio de color CIE-Lab + grafo de conectividad espacial tipo grilla (`grid_to_graph`), para obtener regiones espacialmente coherentes y no una simple cuantización de color.
- **`ward`** con conectividad produce las regiones más homogéneas (menor varianza de color intra-región).
- **`complete`** tiende a sobre-fragmentar zonas de alta textura.
- Se demuestra experimentalmente que, **sin conectividad espacial**, el algoritmo agrupa píxeles por color sin importar su ubicación, generando máscaras no contiguas e inútiles como segmentación.

## Conclusión general

En los tres dominios, el criterio de enlace **`ward`** resultó ser, de forma consistente, el más exitoso —siempre que los datos se representen en un espacio numérico razonablemente denso y de dimensionalidad controlada—, mientras que **`single`** fue sistemáticamente el más débil por su sensibilidad al efecto de encadenamiento. El detalle completo de la metodología, el código y la discusión de resultados se encuentra en el notebook y en el resumen ejecutivo en PDF (formato APA 7.ª edición) incluidos en este repositorio.

## Referencias

- Hastie, T., Tibshirani, R., & Friedman, J. (2009). *The elements of statistical learning: Data mining, inference, and prediction* (2.ª ed.). Springer.
- Lang, K. (1995). Newsweeder: Learning to filter netnews. En A. Prieditis & S. Russell (Eds.), *Machine learning proceedings 1995* (pp. 331–339). Morgan Kaufmann.
- Martin, D., Fowlkes, C., Tal, D., & Malik, J. (2001). A database of human segmented natural images and its application to evaluating segmentation algorithms and measuring ecological statistics. *Proceedings of the 8th IEEE International Conference on Computer Vision, 2*, 416–423.
- Pedregosa, F. et al. (2011). Scikit-learn: Machine learning in Python. *Journal of Machine Learning Research, 12*, 2825–2830.
- Rousseeuw, P. J. (1987). Silhouettes: A graphical aid to the interpretation and validation of cluster analysis. *Journal of Computational and Applied Mathematics, 20*, 53–65.
- Ward, J. H., Jr. (1963). Hierarchical grouping to optimize an objective function. *Journal of the American Statistical Association, 58*(301), 236–244.
