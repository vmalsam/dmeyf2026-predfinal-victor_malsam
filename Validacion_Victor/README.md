# Validación final del modelo gerencial – Víctor Malsam

## Objetivo

Luego del trabajo de undersampling, se realizaron pruebas controladas sobre el notebook gerencial para evaluar si era posible mejorar la configuración de referencia sin basar la decisión únicamente en un pico aislado del Public Leaderboard.

La metodología fue : **modificar una sola cosa por experimento**, mantener fijos el resto de los componentes y comparar la curva completa de cortes de Kaggle (800 a 1300), además del máximo y del promedio de los tres mejores cortes.

> Nota: training_pct se usa para definir qué proporción de la clase CONTINUA entra al Grid Search. No significa que el modelo final se entrene sólo con ese porcentaje de los datos.

## Notebooks incluidos

### 1. Referencia final
`z719_7196_REFERENCIA_100pct_S895651.ipynb`

Configuración principal:

- Semilla: `895651`
- `training_pct = 1.0`
- `popSize = 150`
- `top_features = 20` como máximo
- FE histórica: `lag1`, `lag2`, `lag3`, `delta1`, `delta2`, `delta3`, `ma3`
- `feature_fraction = 0.50`
- `max_bin = 31`
- `learning_rate = 0.03`

Resultado de referencia en Kaggle:

- Mejor Public Score: **25.318**
- Corte: **1100**
- Promedio Top-3: **24.596**

La zona cercana al corte elegido también mostró buenos resultados, por lo que no se trató solamente de un pico aislado.

### 2. Variante experimental FEH01
`z719_719_FEH01_ma6_S895651.ipynb`

Único cambio respecto de la referencia:

- se agrega `ma6`, una media móvil de seis meses.

Fue la única variante que quedó realmente competitiva:

- Mejor Public Score: **24.984**
- Promedio Top-3: **24.373**

No superó globalmente a la referencia, pero mostró que una ventana histórica más larga podía aportar señal útil.

## Resumen de experimentos

| Experimento | Cambio respecto de referencia | Máximo Public | Top-3 medio | Decisión |
|---|---|---:|---:|---|
| **7196 Referencia** | Configuración base | **25.318** | **24.596** | **Conservar** |
| GA01 | `popSize 150 -> 300` | 20.321 | 18.849 | Descartar |
| LGBM01 | `feature_fraction 0.50 -> 0.70` | 23.652 | 23.041 | Descartar |
| FEH01 | agregar `ma6` | 24.984 | 24.373 | Prometedora |
| FEIGA01 | `top_features 20 -> 5` | 19.654 | 18.349 | Descartar |
| LGBM02 | `max_bin 31 -> 63` | 23.319 | 20.904 | Descartar |
| FEH02 | agregar `lag4` | 24.152 | 23.263 | Descartar |

## Qué aportaron las pruebas

Las pruebas permitieron comprobar que:

- aumentar la población del algoritmo genético no mejoró el resultado;
- limitar a cinco las variables generadas por el GA fue demasiado restrictivo;
- aumentar `feature_fraction` o `max_bin` no mejoró la configuración base;
- agregar `lag4` produjo mejoras puntuales, pero empeoró el comportamiento global;
- `ma6` fue la única extensión que quedó cerca de la referencia;
- la configuración de referencia resultó difícil de mejorar en el conjunto de alternativas evaluadas.

No se puede afirmar que sea el mejor modelo posible entre todos los algoritmos o ensembles existentes. Sí se puede afirmar que, **dentro de las alternativas estudiadas y probadas**, fue la configuración más sólida.

## Selección final para Private

Submission seleccionada:

`KA7196_1100.csv`

- Semilla: `895651`
- Corte: `1100`
- Public Score: **25.318**

La selección no se basó solamente en el máximo Public, sino también en el comportamiento de los cortes vecinos y en el promedio de los mejores resultados.

Después de estas pruebas se detuvo la exploración para evitar seguir ajustando decisiones al Public Leaderboard.

## Posible extensión

Una línea futura sería combinar predicciones de varios modelos mediante un ensemble. Esa etapa no se realizó en este cierre: el trabajo se concentró en comparar configuraciones y feature engineering de manera controlada.
