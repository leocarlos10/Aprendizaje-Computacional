# Análisis de Datos - Spotify Dataset

## Descripción del Dataset

El dataframe `spotifyData` contiene información de canciones de Spotify con las siguientes columnas relevantes:

| Columna | Descripción |
|---|---|
| `track_name` | Nombre de la canción |
| `track_popularity` | Popularidad de la canción (0–100) |
| `explicit` | Si la canción tiene contenido explícito (TRUE/FALSE) |
| `artist_name` | Nombre del artista |
| `artist_popularity` | Popularidad del artista (0–100) |
| `artist_followers` | Número de seguidores del artista |
| `track_duration_min` | Duración de la canción en minutos |
| `album_type` | Tipo de álbum (album, single, etc.) |

---

## Pregunta 1: ¿Cuáles son los 10 artistas con mayor popularidad promedio en sus canciones?

### Objetivo
Identificar qué artistas tienen, en promedio, las canciones más populares dentro del dataset. Esto permite descubrir quiénes dominan en popularidad independientemente del número de canciones que tengan.

### Código

```python
top_artists = (
    spotifyData.groupby('artist_name')['track_popularity']
    .mean()
    .sort_values(ascending=False)
    .head(10)
    .reset_index()
)
top_artists.columns = ['Artista', 'Popularidad Promedio']
top_artists['Popularidad Promedio'] = top_artists['Popularidad Promedio'].round(2)
print(top_artists)
```

### Explicación paso a paso

#### Paso 1 — `groupby('artist_name')`
```python
spotifyData.groupby('artist_name')
```
Agrupa todas las filas del dataframe por el nombre del artista. Esto significa que todas las canciones que pertenecen al mismo artista quedan reunidas en un solo grupo.

> Ejemplo: si Diplo tiene 5 canciones en el dataset, esas 5 filas forman un grupo "Diplo".

---

#### Paso 2 — `['track_popularity']`
```python
spotifyData.groupby('artist_name')['track_popularity']
```
De cada grupo, selecciona únicamente la columna `track_popularity`. Así limitamos el cálculo a los valores de popularidad, ignorando el resto de columnas.

---

#### Paso 3 — `.mean()`
```python
.mean()
```
Calcula el **promedio** de `track_popularity` para cada grupo (artista).

> Ejemplo: si Diplo tiene canciones con popularidades `[80, 60, 70, 90, 50]`, el promedio sería `70.0`.

---

#### Paso 4 — `.sort_values(ascending=False)`
```python
.sort_values(ascending=False)
```
Ordena los resultados de **mayor a menor** popularidad promedio. Con `ascending=False` el artista más popular queda en la primera posición.

---

#### Paso 5 — `.head(10)`
```python
.head(10)
```
Toma únicamente los **primeros 10 artistas** del listado ya ordenado, formando así el Top 10.

---

#### Paso 6 — `.reset_index()`
```python
.reset_index()
```
Después de `groupby`, el nombre del artista queda como **índice** del resultado (no como columna normal). Este método lo convierte de nuevo en una columna regular para que el dataframe sea más fácil de leer y manipular.

---

#### Paso 7 — Renombrar columnas
```python
top_artists.columns = ['Artista', 'Popularidad Promedio']
```
Asigna nombres descriptivos en español a las dos columnas del resultado.

---

#### Paso 8 — Redondear decimales
```python
top_artists['Popularidad Promedio'] = top_artists['Popularidad Promedio'].round(2)
```
Redondea los promedios a 2 decimales para una presentación más limpia.

> Ejemplo: `72.6666...` → `72.67`

---

#### Paso 9 — Mostrar resultado
```python
print(top_artists)
```
Imprime la tabla final con los 10 artistas y su popularidad promedio.

---

### Flujo resumido

```
Agrupar por artista → Seleccionar popularidad → Promediar → Ordenar desc. → Top 10 → Limpiar → Mostrar
```

---

## Pregunta 2: ¿Las canciones explícitas son más populares que las no explícitas?

### Objetivo
Comparar la popularidad promedio entre canciones marcadas como explícitas (`explicit = TRUE`) y las que no lo son (`explicit = FALSE`). Esto permite ver si el contenido explícito tiene alguna relación con la popularidad en Spotify.

### Código

```python
explicit_comparison = (
    spotifyData.groupby('explicit')['track_popularity']
    .agg(['mean', 'count'])
    .reset_index()
)
explicit_comparison.columns = ['Explícita', 'Popularidad Promedio', 'Cantidad de canciones']
explicit_comparison['Popularidad Promedio'] = explicit_comparison['Popularidad Promedio'].round(2)
print(explicit_comparison)
```

### Explicación paso a paso

#### Paso 1 — `groupby('explicit')`
```python
spotifyData.groupby('explicit')
```
Agrupa todas las filas del dataframe por el valor de la columna `explicit`. Como esta columna solo tiene dos valores posibles (`TRUE` y `FALSE`), se forman exactamente **dos grupos**: canciones explícitas y no explícitas.

---

#### Paso 2 — `['track_popularity']`
```python
['track_popularity']
```
Selecciona únicamente la columna `track_popularity` de cada grupo para trabajar solo con los valores de popularidad.

---

#### Paso 3 — `.agg(['mean', 'count'])`
```python
.agg(['mean', 'count'])
```
Aplica **dos funciones de agregación** al mismo tiempo sobre cada grupo:
- `mean`: calcula el promedio de popularidad.
- `count`: cuenta cuántas canciones hay en cada grupo.

> Esto es más eficiente que hacer dos operaciones separadas.

---

#### Paso 4 — `.reset_index()`
```python
.reset_index()
```
Igual que en la pregunta 1, convierte el índice (`explicit`) de nuevo en una columna normal del dataframe.

---

#### Paso 5 — Renombrar columnas
```python
explicit_comparison.columns = ['Explícita', 'Popularidad Promedio', 'Cantidad de canciones']
```
Asigna nombres descriptivos en español a las tres columnas: el tipo (explícita o no), el promedio de popularidad y la cantidad de canciones en ese grupo.

---

#### Paso 6 — Redondear decimales
```python
explicit_comparison['Popularidad Promedio'] = explicit_comparison['Popularidad Promedio'].round(2)
```
Redondea los promedios a 2 decimales para mayor legibilidad.

---

#### Paso 7 — Mostrar resultado
```python
print(explicit_comparison)
```
Muestra la tabla comparativa. El resultado tendrá esta forma:

| Explícita | Popularidad Promedio | Cantidad de canciones |
|---|---|---|
| FALSE | X.XX | XXXX |
| TRUE | X.XX | XXXX |

Con esta tabla se puede responder directamente si las canciones explícitas son más o menos populares, y en qué proporción están representadas en el dataset.

---

### Flujo resumido

```
Agrupar por explícita → Seleccionar popularidad → Agregar (promedio + conteo) → Limpiar → Mostrar
```

---

### Diferencia clave entre Pregunta 1 y Pregunta 2

| Aspecto | Pregunta 1 | Pregunta 2 |
|---|---|---|
| Variable de agrupación | `artist_name` (muchos valores) | `explicit` (solo 2 valores) |
| Función de agregación | `.mean()` | `.agg(['mean', 'count'])` |
| Propósito | Ranking / Top N | Comparación entre dos grupos |
| Resultado | Tabla con 10 filas | Tabla con 2 filas |


## pregunta 3: ¿Cuáles son los 10 artistas con mayor número de seguidores en Spotify?


### Objetivo: Identificar cuáles son los artistas con mayor cantidad de seguidores dentro del dataset. Esto permite reconocer qué artistas tienen la base de fans más grande, independientemente de la popularidad específica de cada canción.


### Codigo 
top_followed_artists = (
    spotifyData.groupby('artist_name')['artist_followers']
    .max()
    .sort_values(ascending=False)
    .head(10)
    .reset_index()
)

top_followed_artists.columns = ['Artista', 'Seguidores']
print(top_followed_artists)


### Explicacion paso a paso

### Paso 1 -`groupby('artist_name')`

Agrupa todas las filas del dataframe según el nombre del artista.

### Paso 2 - `['artist_followers']`

Selecciona únicamente la columna artist_followers dentro de cada grupo, que es la que columna contiene el número de seguidores que tiene el artista en Spotify.

### Paso 3 - `.max()`

Obtiene el valor máximo de seguidores para cada artista dentro de su grupo.
Esto se utiliza porque el número de seguidores puede aparecer repetido en varias filas (una por canción). Tomar el máximo asegura que se conserve el valor real más alto registrado para ese artista.

### Paso 4 -`.sort_values(ascending=False)`

Ordena los resultados de mayor a menor según la cantidad de seguidores.
De esta manera, los artistas con más seguidores aparecerán primero en la lista.

### Paso 5 - `.head(10)`

Selecciona únicamente los primeros 10 artistas después de ordenar los datos.

### Paso 6 - `.reset_index()`

Después de usar groupby, el nombre del artista queda como índice del dataframe.
reset_index() convierte ese índice nuevamente en una columna normal, lo que facilita la lectura y manipulación de los datos.

### Paso 7 - `top_followed_artists.columns = ['Artista', 'Seguidores']`

Se asignan nombres más claros y descriptivos en español para las columnas

### Paso 8 - `print(top_followed_artists)`

Imprime la tabla final con los 10 artistas con mayor número de seguidores.