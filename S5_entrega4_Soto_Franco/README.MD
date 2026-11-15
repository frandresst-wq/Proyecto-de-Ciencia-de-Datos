# Descomposición de la Inflación en Chile: Incidencia de las Divisiones del IPC (Base 2023 = 100)

Proyecto analítico desarrollado en el marco de la asignatura **Ciencia de Datos · Advance UCSC** por **Franco Soto Soto**.

---

## 1. Pregunta Analítica y Contexto de Decisión

* **Pregunta analítica:** ¿Qué divisiones del IPC explican la variación acumulada del último año?
* **Tomador de decisiones:** Gerencia de Administración y Finanzas / Planificación y Control de Gestión.
* **Decisión respaldada:** Sustituir la indexación lineal de presupuestos operacionales y contratos con proveedores al IPC General por un esquema de reajuste diferenciado por rubro, evitando sobrecostos cuando el alza inflacionaria proviene de sectores concentrados y no de un encarecimiento transversal de la economía.

---

## 2. Fuente de Datos

* **Conjunto de datos:** `ipc_base_20237baa955a44fe4eada201c196338fb3be.csv` (IPC Base Anual 2023 = 100).
* **Institución:** Instituto Nacional de Estadísticas (INE) de Chile.
* **Cobertura:** 20.262 registros a lo largo de 33 meses continuos (enero de 2024 a septiembre de 2026).
* **Licencia:** Creative Commons Reconocimiento-CompartirIgual 4.0 Internacional (CC BY-SA 4.0).
* **URL oficial:** [INE Chile - Índice de Precios al Consumidor](https://www.ine.gob.cl/estadisticas-por-tema/precios-e-inflacion/indice-de-precios-al-consumidor)

---

## 3. Calidad de Datos y Transformaciones Aplicadas

Durante el diagnóstico y limpieza de los datos se ejecutaron las siguientes transformaciones técnicas y decisiones de preservación:

1. **Estandarización numérica:** Reemplazo de comas decimales (`,`) por puntos (`.`) y conversión de tipo (`object` a `float64`) en las 8 métricas cuantitativas (*Ponderación, Índice, Variaciones e Incidencias*) sin generar valores nulos adicionales.
2. **Formalización de jerarquía COICOP:** Imputación con `0` y conversión a enteros (`int64`) de los valores estructuralmente nulos en los códigos jerárquicos (*División, Grupo, Clase, Subclase y Producto*).
3. **Desambiguación de glosas y niveles:** Normalización de texto en `Glosa` y creación de las variables `nivel_jerarquico` y `codigo_jerarquico`. Al filtrar por `nivel_jerarquico == '1_Division'`, la suma mensual de ponderaciones se corrigió de `500,0 %` (suma simultánea de 5 niveles) a exactamente `100,0 %` (429 registros válidos de división).
4. **Serie temporal:** Unificación de `Año` y `Mes` en el atributo `fecha` (`datetime64[ns]`).
5. **Preservación de valores reales y nulos estructurales:** Se conservaron los valores `0` y `-1` (movimientos reales de precios) y se declararon como limitación los nulos interanuales de 2024 (inicio de la base) y el truncamiento de febrero de 2025 en la fuente original.

---

## 4. Metodología

* **Descomposición aditiva por incidencia jerárquica:** Evaluación del aporte en puntos porcentuales (pp) y aporte relativo porcentual por división COICOP, ponderando la variación de precios por su peso real en la canasta familiar.
* **Análisis longitudinal de series temporales:** Comparación de trayectorias acumuladas entre el IPC General y las divisiones principales.
* **Estadística descriptiva y de dispersión:** Cálculo de media, desviación estándar y rango intercuartílico ($1,5 \times \text{IQR}$) para evaluar la concentración y estabilidad de los aportes divisionales.

---

## 5. Principales Resultados

1. **Concentración en Vivienda y servicios básicos (Hallazgo principal):**
   * Al cierre anual de diciembre de 2024, **Vivienda y servicios básicos** concentró el **39,1 %** del aporte relativo a la inflación acumulada —**2,23 veces** el aporte de la segunda división líder, *Alimentos y bebidas no alcohólicas* (**17,5 %**)—.
   * Las tres principales divisiones (*Vivienda*, *Alimentos* y *Restaurantes y alojamiento* con 11,3 %) explican contablemente el **67,9 %** de la inflación acumulada, mientras las 10 divisiones restantes aportan $\le 8,0\text{ \%}$ cada una.
   * **Robustez:** La distribución del aporte relativo entre las 13 divisiones registra una **media de 7,69 %** y una **desviación estándar de 10,60 %** ($\text{DE poblacional} = 10,18\text{ \%}$), confirmando una fuerte asimetría positiva traccionada por el rubro líder.

2. **Desacoplamiento longitudinal del índice de Vivienda (Hallazgo secundario):**
   * Entre enero de 2024 y el cierre de la serie analizada (excluyendo febrero de 2025 por truncamiento de origen), el índice de **Vivienda y servicios básicos** subió un **+19,9 %** (de `103,29` a `123,82`), casi duplicando el alza del **IPC General** (**+11,7 %**, de `101,72` a `113,59`) y superando en 8,9 pp a **Alimentos** (**+11,0 %**).

---

## 6. Limitaciones y Alcance Ético

* **Ausencia de series 12M en 2024 y truncamiento en febrero de 2025:** No es posible calcular variaciones interanuales desagregadas durante 2024 (7.356 registros nulos) ni evaluar el mes completo de febrero de 2025 (contiene solo registros hasta la División 3).
* **Sesgo de canasta urbana fija y plutocrática:** El IPC refleja el gasto promedio de hogares urbanos según la Encuesta de Presupuestos Familiares (EPF), por lo que no representa el impacto real sobre zonas rurales, adultos mayores o quintiles de menores ingresos.
* **Asociación vs. Causalidad:** El análisis establece atribución contable a nivel nacional, sin modelar causalidad macroeconómica.
* **Privacidad:** El conjunto del IPC contiene estadísticas macroeconómicas agregadas sin datos personales; adicionalmente, en el laboratorio de ética se aplicaron técnicas de $k$-anonimato ($k = 23$) y seudonimización (`SHA-256`) sobre datos sintéticos de participantes.

---

## 7. Estructura del Repositorio

```text
├── README.md                                      # Documentación principal del proyecto
├── S5_Entrega_4_Soto_Franco.ipynb                 # Cuaderno reproducible (visualizaciones, anonimización y sesgo)
├── 9-S5_Plantilla_Informe_Analitico_CDD.docx      # Informe final del proyecto analítico (Secciones A y B)
├── S5_Presentacion_Hallazgos_CDD_SOTO_FRANCO.pptx # Presentación ejecutiva de hallazgos (Sección B)
├── S1_Ficha_Riesgo_Etico_CDD.docx                 # Ficha ético-legal cerrada y firmada (Sección C)
├── figura_1.png                                   # Gráfico explicativo 1: Aporte relativo acumulado por división
└── figura_2.png                                   # Gráfico explicativo 2: Evolución temporal del índice
```

---

## 8. Requisitos para Reproducir el Análisis

* **Lenguaje:** Python 3.11+
* **Librerías principales:** `pandas`, `numpy`, `matplotlib`

Para ejecutar el cuaderno localmente o en Google Colab, asegúrate de mantener el archivo procesado `mi_conjunto_minado.csv` en el directorio raíz del proyecto antes de correr las celdas de generación de gráficos (`figura_1.png` y `figura_2.png`).
