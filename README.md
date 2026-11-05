# Optimización de Cross-Selling y Up-Selling en Retail Online

Proyecto de Ciencia de Datos aplicado a un problema de negocio real en retail: 
aumentar el ticket promedio sin incrementar el gasto en adquisición de clientes.

## Objetivo de Negocio
Identificar el segmento de clientes más rentable de una tienda online en el Reino 
Unido y descubrir patrones de compra conjunta para diseñar estrategias de 
cross-selling medibles.

## Datos
- **Fuente:** UCI Machine Learning Repository – Online Retail Dataset
- **Volumen:** 541,909 transacciones - 4,333 clientes únicos tras limpieza
- **Periodo:** Diciembre 2010 – Diciembre 2011
- **Mercado:** Reino Unido (89% de las transacciones)

## Metodología

1. **Limpieza:** separación de devoluciones y ajustes administrativos, winsorización 
   de `quantity` (P99.99 = 1,524) y `unitprice` (£295), transformación Box-Cox / 
   Yeo-Johnson según skew.

2. **Feature Engineering:** RFM + 5 métricas de comportamiento (`pct_mayorista`, 
   `units_per_invoice`, `rango_vida`, `pct_hogar`, `quantity_total`). 8 features 
   finales estandarizadas con `StandardScaler`.

3. **Segmentación (K-Means):** K=4 seleccionado por **Davies-Bouldin mínimo (1.224)** 
   + estabilidad multi-semilla (**ARI > 0.999**). Criterio de negocio: costo de 
   activación por segmento.

4. **Market Basket (Apriori):** aplicado al top 20% por `monetary` del cluster de 
   mayor ingreso (194 clientes - 4,681 canastas). `min_support = 0.02`, 
   `lift > 1.2` - **120 reglas**, de las cuales se seleccionaron las **top 5 por 
   `support × lift`** para priorizar volumen + asociación.



## Hallazgos Principales
- **Concentración extrema de ingresos:** el **22% de los clientes** (cluster 0, 970 
  clientes) genera el **72.6% de los ingresos** (£6.10M de £8.40M). El **35%** de la 
  base (cluster 1, compradores únicos) aporta solo **4.9%**.

- **Cluster 0 es heterogéneo:** coeficiente de variación `monetary` = 2.7 
  (std £16,969 vs media £6,285). Requiere tratamiento por percentil, no como bloque 
  homogéneo.

- **Patrón de compra del top 20%:** compran **la familia completa en múltiples 
  colores** (ej. `REGENCY TEACUP` en green/roses/pink), no productos aislados. 
  Lift de hasta **28.8** con `confidence` de 0.84 - intención de "set", no de ítem.

## Recomendaciones de Negocio
1. **A/B test** en los 194 clientes del top 20% cluster 0: bundle REGENCY 3 colores 
   vs. control. Métricas: ticket promedio y recompra a 60 días.
2. **Redirigir presupuesto** desde el cluster 1 (35% base, 4.9% ingresos) hacia 
   retención del cluster 0 (22% base, 72.6% ingresos).
3. **Diseñar bundles por familia** (no por producto individual): "pack de la misma 
   colección en distintos colores".

## Limitaciones y Siguientes Pasos
- **Reglas frágiles:** `support` bajo (0.02–0.04) - validar con holdout temporal 
  antes de producción.
- **Sesgo de selección:** el análisis de reglas cubre solo el top 20% del cluster 0; 
  no extrapolable al resto del negocio.
- **Features sin explotar:** tasa de devolución por cliente (descartada como nicho, 
  pero es señal de perfil); interacciones `monetary × frequency` como proxy de valor 
  recurrente.
- **Sensibilidad de K:** probar K=5 como validación documentada, no como alternativa 
  (métricas ya favorecen K=4).

## Tecnologías
Python 3.13.5, Pandas, NumPy, Scikit-Learn (K-Means, StandardScaler), 
mlxtend (Apriori, TransactionEncoder), Matplotlib, Seaborn.

## Cómo Ejecutar
Notebook corre en Google Colab o Jupyter. La celda de carga usa `ucimlrepo`, 
por lo que descarga el dataset automáticamente.

---
*Proyecto desarrollado como parte de mi portafolio de Ciencia de Datos.*
