# Visualización del Territorio · Quito

[![Sitio](https://img.shields.io/badge/sitio-GitHub%20Pages-2c7a7b)](https://faustoaguanor.github.io/VIZ/)
[![Licencia: MIT](https://img.shields.io/badge/licencia-MIT-blue.svg)](LICENSE)
![Python](https://img.shields.io/badge/python-3.11%2B-3776AB?logo=python&logoColor=white)
![Quarto](https://img.shields.io/badge/quarto-1.5%2B-39729E)

**Análisis geoespacial del catastro predial del Distrito Metropolitano de Quito (DMQ):** cómo varían el valor del suelo, el uso del suelo y la desigualdad territorial entre las administraciones zonales, a partir de ~1 millón de predios del Municipio.

🔗 **Sitio publicado:** <https://faustoaguanor.github.io/VIZ/>

---

## Contenido

- [Resumen del proyecto](#resumen-del-proyecto)
- [Preguntas analíticas y hallazgos](#preguntas-analíticas-y-hallazgos)
- [Datos](#datos)
- [Metodología](#metodología)
- [Stack tecnológico](#stack-tecnológico)
- [Reproducir el análisis](#reproducir-el-análisis)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Limitaciones](#limitaciones)
- [Autoría, fuente y licencia](#autoría-fuente-y-licencia)

## Resumen del proyecto

El catastro es un inventario sistemático de cada predio —su geometría, uso, superficie y avalúo— y una de las fuentes más completas y subutilizadas para entender cómo se distribuye el territorio. Este proyecto lo convierte en un sitio web interactivo que responde cinco preguntas de planificación urbana mediante gráficos estadísticos (Altair) y mapas (Folium/Leaflet).

El diseño sigue el **modelo anidado de Munzner (2009)**: dominio → abstracción de datos y tareas → codificación visual → algoritmo. El sitio se organiza en tres documentos:

| Documento | Contenido |
|-----------|-----------|
| [`index.qmd`](index.qmd) | Problema, motivación, dataset y marco metodológico |
| [`data_cleaning.qmd`](data_cleaning.qmd) | Carga, validación espacial, limpieza, variables derivadas y exportación |
| [`visualizations.qmd`](visualizations.qmd) | Respuesta visual a las cinco preguntas y conclusiones |

## Preguntas analíticas y hallazgos

| # | Pregunta | Visualizaciones | Hallazgo principal |
|---|----------|-----------------|--------------------|
| 1 | ¿Cómo varía el valor del suelo por m² entre zonas? | Ranking con IQR, boxplot logarítmico, mapa coroplético | Brecha norte–sur: Eugenio Espejo y Manuela Sáenz lideran; Quitumbe, Eloy Alfaro y Chocó Andino quedan en el extremo inferior |
| 2 | ¿Qué zonas presentan mayor desigualdad interna? | Coeficiente de variación, valor mediano vs. CV | Mayor valor no implica homogeneidad: las zonas centrales concentran la mayor dispersión |
| 3 | ¿Existe relación entre tamaño del predio y avalúo según destino económico? | Dispersión área–avalúo (log), valor/m² por cuartil de área | El valor unitario decrece con el tamaño del predio; la localización modula la relación |
| 4 | ¿Cómo se distribuye el uso del suelo por zona? | Heatmap de composición, % de predios sin uso, mapa de clusters | El uso habitacional domina, pero comercio y servicios ganan peso en el centro; el suelo vacante aparece en parches |
| 5 | ¿Dónde se concentran los predios de mayor valor? | Mapa de puntos, mapa de calor, Propiedad Horizontal vs. unipropiedad | La Propiedad Horizontal sigue los ejes norte de mayor valor (densificación vertical) |

> Los hallazgos completos, con su interpretación, están en el [sitio publicado](https://faustoaguanor.github.io/VIZ/visualizations.html).

## Datos

| Fuente | Descripción |
|--------|-------------|
| `CATASTRO_PREDIAL.gdb` (capa `PREDIO`) | Catastro predial del DMQ: ~1 M de predios, 36 variables, proyección local SIRES-DMQ |
| `organizacion_territorial_zonal_a.shp` | Límites de las 10 administraciones zonales (EPSG:4326) |

**Variables utilizadas:** `admzonal`, `parroquia`, `estado`, `propiedad`, `desteconomico`, `arterrpredio` (m² de terreno), `arconstruccion` (m² construidos), `valterreno` y `valtotal` (USD).

**Variables derivadas:** `valor_m2` (valterreno / arterrpredio), `ratio_construccion`, `zona_nombre` (nomenclatura normalizada), `uso` (destino económico simplificado), `lon`/`lat` (centroides).

**Fuente:** Municipio del Distrito Metropolitano de Quito · Sistema de Información Geográfica Municipal.

> Los datos brutos y procesados **no se incluyen** en el repositorio (peso y licenciamiento). Solo se versiona el sitio renderizado en `docs/`.

## Metodología

El pipeline completo está documentado y es ejecutable en [`data_cleaning.qmd`](data_cleaning.qmd):

1. **Carga selectiva** de 9 columnas de la capa `PREDIO` con `pyogrio`.
2. **Reproyección** de SIRES-DMQ a EPSG:4326 y **corrección de geometrías inválidas** (`buffer(0)`).
3. **Centroides** y filtro por *bounding box* del DMQ.
4. **Filtros de calidad:** solo predios `ACTIVO`; área > 1 m² y avalúos > 0; recorte de `valor_m2` en el percentil 99 para el análisis distribucional.
5. **Variables derivadas** y normalización de zonas (el campo `admzonal` usa nomenclatura distinta a la del shapefile).
6. **Validación espacial:** *spatial join* de una muestra de 50 000 centroides contra los polígonos zonales para comprobar la consistencia de `admzonal`.
7. **Exportación** a Parquet (dataset completo, muestra estratificada de hasta 3 000 predios por zona y estadísticas por zona) y GeoJSON de zonas.

**Nota sobre La Mariscal:** no tiene registros propios en el catastro; sus predios figuran en Eugenio Espejo, por lo que ambas se tratan como una unidad estadística y se fusionan en los mapas.

## Stack tecnológico

| Capa | Herramientas |
|------|--------------|
| Publicación | [Quarto](https://quarto.org) · GitHub Pages |
| Datos geoespaciales | GeoPandas · pyogrio · Shapely |
| Datos tabulares | pandas · NumPy · PyArrow (Parquet) |
| Visualización estadística | Altair (Vega-Lite) |
| Mapas interactivos | Folium · Leaflet |

## Reproducir el análisis

**Requisitos:** Python 3.11+, [Quarto](https://quarto.org/docs/get-started/) 1.5+ y los datos brutos del Municipio.

```bash
# 1. Clonar el repositorio
git clone https://github.com/faustoaguanor/VIZ.git
cd VIZ

# 2. Crear el entorno virtual e instalar dependencias
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# 3. Registrar el kernel de Jupyter que usa _quarto.yml
python -m ipykernel install --user --name viz-quito --display-name "viz-quito"

# 4. Colocar los datos brutos (ver estructura abajo)

# 5. Renderizar el sitio en docs/
quarto render
# Vista previa con recarga automática: quarto preview
```

**Ubicación esperada de los datos brutos:**

```
datos/
├── CATASTRO_PREDIAL.gdb/
│   └── CATASTRO_PREDIAL.gdb/                      # geodatabase (capa PREDIO)
└── Administraciones_Zonales/
    └── organizacion_territorial_zonal_a.shp       # + .dbf, .shx, .prj
```

Al renderizar, `data_cleaning.qmd` genera `datos/processed/` (ignorado por git) y `visualizations.qmd` lo consume; Quarto procesa los documentos en orden alfabético, por lo que la preparación de datos se ejecuta primero.

## Estructura del repositorio

```
├── index.qmd                  # Inicio: problema, dataset y marco metodológico
├── data_cleaning.qmd          # Preparación y limpieza de datos
├── visualizations.qmd         # Análisis, gráficos, mapas y conclusiones
├── _quarto.yml                # Configuración del sitio (salida en docs/)
├── styles.css                 # Estilos del sitio
├── requirements.txt           # Dependencias de Python
├── docs/                      # Sitio renderizado (GitHub Pages)
│   ├── mapa_*.html            # Mapas Folium incrustados
│   └── zonas_admin.json       # Límites zonales para los mapas
├── datos/                     # (local, no versionado) datos brutos y procesados
└── LICENSE
```

## Limitaciones

- Los avalúos son **valoraciones administrativas**, no precios de mercado; se actualizan periódicamente y pueden rezagarse respecto al mercado. Los valores absolutos en USD/m² no deben leerse como precios de compraventa; las **comparaciones relativas entre zonas** sí son válidas.
- Se excluyen predios con área ≤ 1 m², avalúo nulo o inactivos, y el 1 % superior de `valor_m2` en el análisis distribucional.
- Los mapas de puntos usan muestras estratificadas para mantener el rendimiento en el navegador.
- El análisis es descriptivo; no establece causalidad.

## Autoría, fuente y licencia

**Autor:** Fausto Guano · [@faustoaguanor](https://github.com/faustoaguanor)

Código y textos bajo licencia **MIT** — ver [LICENSE](LICENSE). Los datos pertenecen al Municipio del Distrito Metropolitano de Quito y están sujetos a sus propios términos de uso.
