# DeFi Portfolio Strategy (Estrategia de gestión en protocolos DeFi)

Este proyecto analiza estrategias de staking en el ecosistema DeFi, con foco en **ETH staking en Lido**, para evaluar hasta qué punto el *machine learning* puede aportar valor en la toma de decisiones de gestión de carteras.

DeFi (Decentralized Finance) es un sistema financiero que opera sin intermediarios tradicionales, utilizando tecnología blockchain. Dentro de este ecosistema existen distintas formas de obtener rendimiento sobre ETH (staking, lending, liquidity providing), cada una con dinámicas y riesgos diferentes.

---

## 🎯 Objetivo del proyecto

El objetivo principal es **ayudar a un inversor a evaluar estrategias de gestión de cartera en DeFi**, buscando maximizar el retorno esperado **más allá de la simple variación del precio de ETH**.

Pregunta central del proyecto:

> ¿Puede el *machine learning* aportar información útil para la toma de decisiones en estrategias DeFi, concretamente en el staking de ETH?

---

## 📊 Datos

Se utiliza un **dataset público**, actualizado diariamente, que recoge el APR del staking de ETH en Lido:

- Fuente: https://github.com/xh3b4sd/eth-staking-rewards

---

## 🧪 Enfoques modelados

A lo largo del proyecto se exploran **distintas formulaciones del problema**, evaluando explícitamente cada una frente a un baseline económico.

### 1️⃣ Predicción del retorno acumulado a 7 días

- **Objetivo**: Predecir el retorno acumulado del staking de ETH en Lido a 7 días.
- **Modelo**: XGBoost (Regresión)
- **Target**: Retorno acumulado a 7 días

**Problema encontrado**:
- R² negativo debido a la **muy baja variabilidad de la serie**.
- El modelo naïve (predecir la media) obtiene un R² ≈ 0.93.

**Hallazgo clave**:
> No existe señal predictiva explotable. Superar al baseline es prácticamente imposible.

---

### 2️⃣ Predicción de retornos diarios (cambio de enfoque)

- **Objetivo**: Predecir cambios diarios en lugar de niveles.
- **Modelo**: XGBoost (Regresión)
- **Target**: Retornos diarios

**Resultados**:
- MAE del modelo inferior al MAE del baseline (~38% de mejora relativa).

**Limitaciones**:
- Predicciones casi constantes.
- Proceso con *drift* positivo muy estable.
- Alto nivel de ruido.

---

### 3️⃣ Clasificación de eventos extremos

- **Objetivo**: Detectar eventos relevantes en lugar de predecir valores exactos.
- **Modelo**: XGBoost (Clasificación)
- **Target**: Cambios extremos del APR (±X%)

**Problemas encontrados**:
- Eventos extremadamente escasos.
- En algunos splits de test no existen eventos positivos.

---

### 4️⃣ Enfoque final: Sistema de alerta (Kill-Switch)

Este es el enfoque que más valor potencial aporta desde un punto de vista operativo.

- **Objetivo**: No predecir el valor del APR, sino **detectar riesgos significativos**.
- **Target**: *Shock* → caída del APR ≥ 20% en los próximos 7 días.
- **Modelo**: Clasificación binaria (shock / no shock)

**Uso operativo**:
- Default: staking activo.
- Solo se desactiva el staking si el modelo detecta riesgo elevado.

**Resultados**:
- Los shocks representan < 1% de las semanas.
- En test: 2 eventos reales.
- El modelo detecta 1 (recall ≈ 50%).
- Muy pocas falsas alarmas.
- Backtest: rendimiento equivalente a “always staking”.

---

## 🧠 Conclusiones finales

### 1️⃣ Sobre el staking de ETH
- El staking de ETH es **extremadamente estable**.
- Las caídas fuertes del APR son **eventos muy raros** (<1%).
- No existen periodos sistemáticamente explotables de forma predictiva.

### 2️⃣ Sobre el uso de Machine Learning
- No existe una estrategia predictiva que supere al baseline económico.
- El *machine learning* **no es útil como optimizador de retornos** en este contexto.
- Su único uso razonable es como **sistema de alerta ante eventos raros**, más que como herramienta de predicción continua.

### 3️⃣ Valor del proyecto
Este proyecto demuestra que:
- Probar múltiples formulaciones es clave antes de sacar conclusiones.
- Comparar explícitamente contra un baseline es imprescindible.
- En algunos problemas financieros, **la ausencia de señal predictiva es el resultado más importante**.

---

## 🛠️ Tecnologías utilizadas

- Python  
- Pandas  
- NumPy
- Scikit-learn 
- XGBoost
- Matplotlib
- Seaborn
- Jupyter Notebook  

---

## 📁 Estructura del proyecto

- **1.Data_Processing.ipynb**  
  Limpieza de datos, preprocesamiento y construcción de variables a partir del APR de staking en Lido.

- **2.Exploratory_Analysis.ipynb**  
  Análisis exploratorio de datos (EDA) para estudiar la estabilidad del staking, la variabilidad del APR y la presencia (o ausencia) de patrones explotables.

- **3.Model_Training.ipynb**  
  Evaluación de distintos enfoques de *machine learning* (regresión y clasificación), siempre comparados contra un baseline económico.
  
- **DATA/**  
  Carpeta con los datasets utilizados en el proyecto:  
  - `rewards.csv` → Dataset original público con información histórica del APR de staking de ETH en Lido.
  - `df_staking.csv` → Dataset derivado, limpio y preparado para el análisis, con variables creadas y calculadas a partir del dataset original.

