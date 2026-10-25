# ¿Qué pedido va a fallar? Anticipando insatisfacción y retraso en Olist

Trabajo Fin de Máster — Máster en Data Science, Big Data & Business Analytics (Universidad Complutense de Madrid, 2026)

## Resumen

Este proyecto analiza el [Brazilian E-Commerce Public Dataset de Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) (~100.000 pedidos reales, 2016–2018, 9 tablas relacionales) para **predecir la insatisfacción del cliente y el retraso en la entrega**, con el objetivo de anticipar problemas operativos antes de que impacten en la experiencia de compra.

Más allá de la predicción, el trabajo extiende el análisis a tres frentes de aplicación directa para un equipo de negocio:

- **Scoring de riesgo de vendedores**, para priorizar la gestión de proveedores con mayor probabilidad de generar incidencias.
- **Segmentación de clientes**, para diferenciar estrategias comerciales según el valor y la recurrencia del comprador.
- **Diagnóstico geográfico y por categoría**, para identificar dónde concentrar los esfuerzos de mejora operativa.

## Dataset

Los datos provienen de Olist, una plataforma de e-commerce brasileña, y están disponibles públicamente en Kaggle. No se incluyen en este repositorio por su tamaño; deben descargarse directamente desde el enlace anterior y colocarse en una carpeta `data/` local.

## Metodología

**1. Integración y limpieza de datos**
Consolidación de las 9 tablas en dos conjuntos analíticos (`orders_analytical` a nivel de pedido y `order_items_integrada` a nivel de ítem), con tratamiento explícito de valores ausentes como informativos (no imputados) cuando corresponden a eventos que no llegaron a ocurrir.

**2. Selección de variables**
Cramér's V para variables categóricas y correlación de Pearson para numéricas, usando variables de ruido aleatorio como umbral empírico de relevancia.

**3. Modelización predictiva**
Comparación de Regresión Logística, Random Forest, XGBoost y LightGBM para dos targets: insatisfacción (`review_score ≤ 3`, ~23% de los pedidos) y retraso en la entrega (~8% de los pedidos entregados). Rebalanceo de clases y validación cruzada estratificada.

**4. Interpretabilidad**
Análisis SHAP para identificar los principales drivers de cada modelo (número de vendedores distintos, ticket medio, porcentaje de flete, región Nordeste, entre otros).

**5. Scoring de vendedores**
Metodología de scorecard (credit scoring) con `optbinning`, escalado pdo/odds y selección de umbral óptimo mediante el índice de Youden.

**6. Segmentación de clientes**
Clustering k-means (k=3), diferenciando compradores puntuales, clientes de alto ticket y compradores recurrentes.

**7. Productivización**
Prototipo de inferencia en tiempo real para el modelo de retraso, mediante serialización con `pickle` y arquitectura cliente-servidor con `threading` y `requests`.

## Resultados clave

- Mejora sensible del **recall** en ambos targets tras el rebalanceo de clases, manteniendo un **ROC-AUC ~0,57–0,58** estable.
- **Segmentación de ~99.000 clientes** en 3 perfiles: 94,5% compradores puntuales, 3,7% clientes de alto ticket, 1,8% compradores recurrentes.
- Scorecard de riesgo de vendedores con umbral óptimo (**índice de Youden = 0,184**) como herramienta de priorización, no de penalización automática.
- Identificación de una **dualidad geográfica**: la región Nordeste presenta la peor tasa proporcional de incidencias, mientras que Sudeste (São Paulo) concentra el mayor volumen absoluto en riesgo — dos palancas de negocio distintas.

## Estructura del repositorio

```
├── notebooks/
│   ├── 01_integracion_datos.ipynb
│   ├── 02_eda_seleccion_variables.ipynb
│   ├── 03_modelizacion_predictiva.ipynb
│   ├── 04_interpretabilidad_shap.ipynb
│   ├── 05_scoring_vendedores.ipynb
│   ├── 06_segmentacion_clientes.ipynb
│   └── 07_productivizacion.ipynb
├── Funciones.py
└── README.md
```

## Tecnologías

Python · scikit-learn · XGBoost · LightGBM · SHAP · optbinning · patsy/statsmodels · Plotly · pandas · NumPy

## Autor

Francisco Morales Reina

## Fuente de datos y atribución

Este proyecto utiliza el **Brazilian E-Commerce Public Dataset by Olist**, publicado por Olist y alojado en Kaggle.

- **Fuente:** [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)
- **Proveedor de los datos:** Olist.
- **Periodo de los datos:** 2016–2018.
- **Licencia del dataset:** [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)](https://creativecommons.org/licenses/by-nc-sa/4.0/).

Los datos originales contienen información anonimizada sobre pedidos, clientes, vendedores, productos, pagos, entregas y valoraciones de clientes.

El tratamiento de datos, el análisis exploratorio, la ingeniería y selección de variables, la modelización predictiva, la interpretabilidad, la puntuación de vendedores y la segmentación de clientes desarrollados en este repositorio son elaboración propia, a partir del dataset original.

**Atribución:** Olist. (2018). *Brazilian E-Commerce Public Dataset by Olist* [Conjunto de datos]. Kaggle. https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

**Nota sobre la licencia:** el dataset original está sujeto a sus propias condiciones de licencia. Consulta la licencia antes de redistribuir los datos originales o derivados, o de utilizar el proyecto con fines comerciales.
