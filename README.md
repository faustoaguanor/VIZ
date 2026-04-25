# Visualización del Territorio · Quito

Análisis geoespacial del catastro predial del Distrito Metropolitano de Quito (DMQ). El proyecto examina cómo varía el valor del suelo, el uso del suelo y la desigualdad territorial entre las 10 administraciones zonales del DMQ, a partir de los datos catastrales del Municipio.

**Sitio publicado:** https://faustoaguanor.github.io/VIZ/

---

## Preguntas analíticas

1. ¿Cómo varía el valor del suelo por m² entre zonas?
2. ¿Qué zonas presentan mayor desigualdad interna en el valor catastral?
3. ¿Existe relación entre el tamaño del predio y su avalúo según destino económico?
4. ¿Cómo se distribuye el uso del suelo por zona?
5. ¿Dónde se concentran espacialmente los predios de mayor valor?

## Datos

| Fuente | Descripción |
|--------|-------------|
| `CATASTRO_PREDIAL.gdb` | Catastro predial del DMQ — ~1 M predios, 36 variables |
| `organizacion_territorial_zonal_a.shp` | Límites de las 10 administraciones zonales (EPSG:4326) |

Fuente: Municipio del Distrito Metropolitano de Quito · Sistema de Información Geográfica Municipal.

Los datos brutos no están incluidos en este repositorio. Solo se incluye el sitio renderizado en `docs/`.

## Stack

- **Quarto** — publicación del sitio
- **Python** — procesamiento y análisis
- **GeoPandas** — datos geoespaciales
- **Altair** — visualizaciones estadísticas
- **Folium / Leaflet** — mapas interactivos

## Reproducir el análisis

```bash
# 1. Clonar el repositorio
git clone https://github.com/faustoaguanor/VIZ.git
cd VIZ

# 2. Crear entorno virtual e instalar dependencias
python -m venv .venv
.venv\Scripts\activate      # Windows
pip install -r requirements.txt

# 3. Colocar los datos brutos en datos/
#    (CATASTRO_PREDIAL.gdb y Administraciones_Zonales/)

# 4. Renderizar el sitio
quarto render
```

## Estructura

```
├── index.qmd                  # Página de inicio
├── data_cleaning.qmd          # Preparación de datos
├── visualizations.qmd         # Análisis y visualizaciones
├── docs/                      # Sitio renderizado (GitHub Pages)
│   ├── zonas_admin.json       # Límites zonales para los mapas
│   └── ...
├── _quarto.yml
├── requirements.txt
└── styles.css
```

## Licencia

MIT — ver [LICENSE](LICENSE).
