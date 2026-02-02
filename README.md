# 🏛️ US Political Discourse Intelligence: End-to-End Analytics

![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=Power%20BI&logoColor=black)
![SQL](https://img.shields.io/badge/SQL-4479A1?style=for-the-badge&logo=postgresql&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-success)

### 📋 Executive Summary
Este proyecto analiza más de **1.2 millones de tweets** de figuras políticas estadounidenses para entender cómo se traduce la actividad digital en influencia real. Más allá de las métricas simples (número de seguidores), se ha construido un pipeline de datos para medir la **calidad del engagement**, la **efectividad por estado** y los **patrones temporales** de éxito.

---

## ⚙️ 1. Ingeniería de Datos & SQL Avanzado

Para transformar los datos brutos en insights, se utilizaron consultas SQL complejas enfocadas en normalizar métricas de rendimiento.

### 🗝️ Consultas Clave (Metric Engineering)
En lugar de usar totales brutos, se calcularon ratios para comparar justamente cuentas pequeñas vs. grandes.

#### Cálculo de "Impacto Real" (Engagement per 1K Followers) y Ratio de Reciprocidad (relación entre seguidores y seguidos)**
```sql
CREATE OR REPLACE TABLE users_advanced_metrics AS
SELECT 
    u.id_str,
    u.screen_name,
    -- Ratio de Reciprocidad 
    CAST(u.friends_count AS FLOAT) / NULLIF(u.followers_count, 0) AS reciprocity_ratio,
    
    --Promedio de RTs por cada 1,000 seguidores
    (AVG(t.retweet_count) / NULLIF(u.followers_count, 0)) * 1000 AS engagement_per_1k
FROM users_analysis u
JOIN tweets_analysis t ON u.id_str = t.user_id
GROUP BY u.id_str, u.screen_name;
````
## 🧹 2. Limpieza de Datos: El Desafío "Location"

El dataset original presentaba una columna `location` inconsistente (ej: "Riverside, CA", "Texas", "NYC", "The Sunshine State"). Para habilitar la inteligencia geoespacial en Power BI, se implementó una estrategia de limpieza en Python:

1.  **Extracción con Regex:** Se desarrolló un script para detectar patrones de códigos de estado (CA, TX, NY) dentro de cadenas de texto complejas.
2.  **Estrategia para Nulos:** Se detectó que el 25% de los políticos no declaraban estado.
    * *Solución:* Se imputaron estos valores a **"Washington D.C."** bajo la premisa de muchas de las cuentas operan desde el capitolio. 
3.  **Resultado:** Se recuperó el 100% de la integridad de los datos para el mapa de calor, evitando sesgos de exclusión.

---

## 🧪 3. Validación de Hipótesis

Basado en el análisis estadístico y visual, se concluye:

| Hipótesis | Estado | Hallazgo |
| :--- | :---: | :--- |
| **"La antigüedad garantiza seguidores"** | ✅ **Aceptada** | Existe una correlación positiva fuerte. Las cuentas creadas pre-2012 concentran la mayor base de seguidores, validando la ventaja del pionero. |
| **"Mayor población = Mayor Engagement"** | ❌ **Rechazada** | El mapa de calor reveló que estados menos poblados pueden tener comunidades digitales más activas y reactivas que grandes urbes como NY o CA. |
| **"La verificación (Check Azul) implica consistencia"** | ✅ **Aceptada** | Los usuarios verificados muestran una desviación estándar menor en su frecuencia de publicación diaria, indicando equipos de comunicación profesionales. |

---

## 📊 4. Diseño del Dashboard (Power BI)

El informe final se diseñó siguiendo principios de **visualización modernos** (layout tipo tarjeta, navegación web, paleta corporativa) y storytelling de datos.

### Informe de análisis
* **KPI Cards:** Métricas clave (Alcance, Engagement Rate) que demuestran la consistencia del estudio. 
* **Mapa Coroplético (Filled Map):** Visualización de la intensidad del discurso político por estado, usando la columna `Location_State` limpia y la métrica creada en código SQL.
* **Gráfico de líneas temporales:** Demostración de que los cuentas "pioneras" (previas a 2012) presentan una mayor número de seguidores.
* **Gráfico de barras apiladas + Top N:** Con el objetivo de personificar que cuentas logran una mayor interacción por cada 1k de seguidores, justificando así la calidad de su trabajo. 

![Informe en PowerBI](https://github.com/Nachoide100/Analisis-tweets-politicos/blob/defdf68d1e2cdc908f529c99fcc7ca8d15644937/visualizations/Captura%20de%20pantalla%202026-01-31%20173630.png)
---

## 📥 Instalación y Datos

> ⚠️ **Nota Importante:** Debido a las restricciones de tamaño de GitHub (>100MB), los archivos `tweets.json` (raw data) y `analisis_tweets.pbix` no están alojados directamente en este repositorio.

* **Ver Dashboard:** Puedes ver una versión estática completa en el archivo `Report.pdf`.
* **Ver Proyecto en Power BI:** Puedes acceder al informe interactivo en power BI clicando en el siguiente enlace -> ![Informe_interactivo](https://drive.google.com/file/d/1Cbcp8ux61yaOQlhuk6oYtT_i_5GikoIh/view?usp=drive_link)

---
*Autor: José Ignacio Rubio -> https://www.linkedin.com/in/jos%C3%A9-ignacio-rubio-194471308/*

