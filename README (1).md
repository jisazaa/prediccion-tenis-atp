# Predicción de partidos de tenis ATP con Machine Learning

Proyecto final · Machine Learning · Universidad EIA

**Autores:** [Nombre 1] y [Nombre 2]

## El problema

Antes de que empiece un partido del ATP Tour, ¿podemos predecir quién lo gana?

- **Una fila** = un partido de cuadro principal del ATP Tour (2000 – junio de 2026).
- **Etiqueta** = `gana_A`: 1 si gana el jugador A. Quién es A y quién es B se sortea al azar, para que la etiqueta quede balanceada.
- **Regla a mano (sin ML):** "gana el jugador con mejor ranking".

## Resultado

Evaluación en **10.329 partidos de 2023–2026** que ningún modelo vio al entrenar:

| Modelo | Exactitud | IC 95 % | AUC | Log loss |
|---|---|---|---|---|
| Línea base (siempre A) | 49,0 % | 48,1 – 50,0 | – | – |
| Regla: mejor ranking | 64,0 % | 63,1 – 65,0 | – | – |
| Regla: mayor Elo | 65,1 % | 64,3 – 66,0 | – | – |
| **Regresión logística (elegido)** | **66,6 %** | **65,7 – 67,5** | **0,733** | **0,603** |
| Random forest | 66,4 % | 65,5 – 67,3 | 0,732 | 0,603 |
| XGBoost | 66,4 % | 65,5 – 67,3 | 0,733 | 0,602 |

- El modelo supera a la regla del ranking por **+2,6 puntos** (IC 95 %: +1,7 a +3,4).
- La ganancia se concentra en los **partidos parejos**: con rankings a menos de 1,5× de diferencia, el ranking acierta el 52,9 % y el modelo el 58,3 %.
- Los tres modelos están empatados; se eligió la regresión logística por ser la más simple.
- **Caso real:** Roland Garros 2026 (campeón: Alexander Zverev).

## Estructura

Los notebooks siguen los 6 pasos del curso y se corren **en orden**: cada uno guarda en `datos/` lo que necesita el siguiente.

| Notebook | Paso | Qué hace |
|---|---|---|
| `01_problema_y_regla.ipynb` | 1 · ¿ML? | Define el problema, la etiqueta y mide la regla del ranking |
| `02_datos.ipynb` | 2 · Datos | Descarga y une 4 fuentes: partidos del tour, qualys/challengers, jugadores y rankings semanales |
| `03_limpieza.ipynb` | 3 · Limpieza | Walkovers, retiros, superficie, rankings viejos, imputación de estatura (comparando métodos) |
| `04_columnas.ipynb` | 4 · Columnas | Elo general y por superficie, forma, historial directo, saque, cansancio — todo sin fugas |
| `05_modelos.ipynb` | 5 · Modelos | Logística, random forest y XGBoost con validación cruzada temporal y Pipeline |
| `06_evaluacion.ipynb` | 6 · Evaluación | Prueba 2023–2026, intervalos de confianza, calibración y Roland Garros 2026 |

## Cómo correrlo

**En Google Colab:** subir los notebooks y ejecutarlos en orden (`Entorno de ejecución → Ejecutar todas`).

**En el computador:**

```bash
pip install -r requirements.txt
jupyter notebook
```

- La **primera vez**, `02_datos.ipynb` descarga unos 70 archivos (~300 MB) y tarda unos minutos. Después todo se lee de la carpeta `datos/`, sin internet.
- `05_modelos.ipynb` busca hiperparámetros la primera vez (2–5 minutos). Desde la segunda vez usa los guardados y corre en menos de un minuto.
- Semilla fija (`42`): los resultados son reproducibles.

## Datos

- **Fuente:** Jeff Sackmann / [Tennis Abstract](https://www.tennisabstract.com/).
- **Licencia:** [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) — atribución obligatoria, solo uso no comercial, compartir con la misma licencia.
- **Descarga:** el repositorio original `JeffSackmann/tennis_atp` fue retirado de GitHub en 2026, y la página de Tennis Abstract no permite descargar sus archivos de partidos (`robots.txt`). Se usa el archivo espejo [`Aneeshers/tennis-sackmann-archive`](https://huggingface.co/datasets/Aneeshers/tennis-sackmann-archive) en Hugging Face (mismos archivos y licencia, datos hasta junio de 2026).
- Los datos **no se incluyen en este repositorio**: los notebooks los descargan automáticamente.
