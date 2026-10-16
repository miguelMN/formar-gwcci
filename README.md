# FORMAR 2024–2026 · Mapeo de soja sin datos de entrenamiento (GWCCI)

> **English summary.** Training-free, pre-harvest soybean mapping in the Argentine Pampas
> using the Greenness and Water Content Composite Index (GWCCI; Chen et al., 2023) on
> Sentinel-2 imagery in Google Earth Engine. The workflow selects the optimal acquisition
> date per department, builds independent annual cropland masks (MODIS and Sentinel-2,
> 2019–2025), and evaluates the index against national crop maps (INTA) over 95 departments
> in five provinces.

## Objetivo

Adaptar el índice GWCCI (Chen et al., 2023) para obtener **mapas de soja / no soja antes de
la cosecha (febrero–marzo), sin datos de entrenamiento a campo**, y estimar la superficie
sembrada **por departamento**, que es la unidad en la que se informan las estadísticas
agrícolas oficiales.

- **Área de estudio:** 95 departamentos de Buenos Aires, Córdoba, Entre Ríos, La Pampa y Santa Fe.
- **Campañas:** 2018/19 a 2024/25. Convención: *campaña 2021* = 2020/21, año de la imagen de febrero.
- **Referencia para la evaluación:** Mapa Nacional de Cultivos del INTA (soja = código 11).

## Método (resumen)

1. **Selección de la imagen.** Serie de GCC (B3/(B2+B3+B4)) de Sentinel-2 SR con Cloud Score+
   (cs_cdf ≥ 0,60) sobre el área agrícola de cada departamento. Se elige la fecha–órbita del
   pico de GCC con cobertura suficiente.
2. **Máscara agrícola, independiente del INTA, para cada campaña:**
   - MODIS MOD13Q1, con umbrales de NDVI por franja latitudinal (Nolasco et al., 2023);
   - Sentinel-2 a 60 m: Random Forest (100 árboles) sobre 6 medianas bimestrales, entrenado
     con la máscara MODIS estable (> 14 de 22 campañas).
3. **GWCCI** = NDVI × B11. Se clasifica como soja con el umbral de Chen (0,17) o con un umbral
   de Otsu por departamento, sin entrenamiento.
4. **Evaluación** contra el INTA mediante histogramas agrupados por clase: kappa, exactitud
   global, exactitud del productor y del usuario, y error de área por departamento.

## Estructura

~~~
notebooks/
  00_verificacion_entorno.ipynb     Verificación del entorno y de la conexión con Earth Engine
  01_exploracion_ventana_gcc.ipynb  Exploración de la ventana temporal (GCC)
  02_seleccion_imagen.ipynb         Cobertura por órbita y selección de fecha por departamento
  04_mascaras_modis.ipynb           Máscaras agrícolas MODIS 2001–2025
  05_mascara_s2_60m.ipynb           Máscaras Sentinel-2 60 m y comparación con el INTA
results/
  tables/                           Resultados tabulares (CSV)
  figures/                          Figuras
environment.yml                     Entorno conda reproducible
~~~

La evaluación del GWCCI para 2019–2025 está en preparación.

## Reproducir

~~~
conda env create -f environment.yml
conda activate formar-gwcci
earthengine authenticate
~~~

- Proyecto de Earth Engine: `ee-mnolasco`.
- Los productos intermedios (máscaras e histogramas) se guardan como assets en
  `users/mnolasco/FORMAR_2024_2026/`.
- Los datos crudos (`data/`) **no se incluyen** en el repositorio, porque son pesados o tienen
  restricciones de uso.

## Referencias

- Chen, H. et al. (2023). *A novel Greenness and Water Content Composite Index (GWCCI) for
  soybean mapping from single remotely sensed multispectral images.*
- Nolasco, M. et al. (2023). Máscaras anuales de uso agrícola estival a partir de MODIS
  MOD13Q1. *IEEE*.
- INTA. Mapa Nacional de Cultivos, campañas 2018/19–2024/25.

## Autor

Miguel Nolasco · Facultad de Ciencias Agropecuarias, Universidad Nacional de Córdoba ·
CONAE / Instituto Gulich. Contacto: mnolasco@agro.unc.edu.ar
