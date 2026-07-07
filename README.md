# Estudio estadistico del contrarreloj en ciclismo

Trabajo final de la asignatura de Estadistica (Grado en Ingenieria Informatica, 2023/24) consistente en el analisis estadistico en R de los resultados de la contrarreloj individual de La Vuelta a Espana 2023.

## Contenido del repositorio

| Archivo | Descripcion |
|---|---|
| `Codigo.Rmd` | Documento R Markdown con todo el analisis estadistico (codigo R y explicaciones). |
| `Poster.pdf` | Poster resumen del trabajo con los resultados principales. |

## Descripcion del estudio

Los datos utilizados son los resultados de la contrarreloj individual de La Vuelta a Espana 2023 (dataset `Ciclistas.xlsx`, no incluido en este repositorio). La variable principal analizada es `Tiempo(s)`, el tiempo en segundos que tardo cada participante en completar la prueba. Como variables secundarias se emplean la edad del ciclista, la velocidad media (Km/h) y el equipo al que pertenece.

El estudio se estructura en las siguientes partes:

### 1. Estudio descriptivo
Analisis descriptivo de la variable principal (`Tiempo(s)`) mediante `summarytools::descr`, junto con un diagrama de caja y un histograma. Con 160 observaciones, la media es 1875.89 s, la mediana 1890.00 s y la desviacion tipica 76.63 s. Los datos presentan una asimetria negativa (Skewness = -0.49) y no muestran valores atipicos.

### 2. Ajuste de distribuciones
Se comprueba si `Tiempo(s)` se ajusta a una distribucion normal mediante el paquete `fitdistrplus` y los tests de bondad de ajuste del paquete `nortest` (Anderson-Darling, Cramer-von Mises, Kolmogorov-Smirnov-Lilliefors, chi-cuadrado de Pearson y Shapiro-Francia). Al no ajustarse a una normal, se prueba un ajuste a una distribucion Weibull (con el paquete `goftest` para los tests de bondad de ajuste), resultando ser la distribucion que mejor describe los datos.

### 3. Intervalos de confianza y contraste de hipotesis
Contraste de hipotesis sobre la media del tiempo (`H0: mu = 1860` frente a `H1: mu > 1860`) mediante `t.test`, obteniendo un p-valor < 0.05 que lleva a rechazar la hipotesis nula. Tambien se calcula el intervalo de confianza al 95% para la media. No se calcula el intervalo de confianza ni el contraste para la varianza al no distribuirse los datos normalmente.

### 4. Regresion multiple
Se ajustan varios modelos de regresion lineal multiple para explicar `Tiempo(s)`:

- Un primer modelo con `Edad`, `Velocidad Media (Km/h)` y `Equipos(numero)` como variables independientes, del que se elimina `Equipos(numero)` por no ser significativa, quedando:
  `Tiempo(s) = -0.18635 * Edad - 36.81609 * Velocidad Media (Km/h) + 3707.08544`
- Un segundo modelo que sustituye la variable `Equipos(numero)` por variables binarias (dummy) para cada uno de los 23 equipos, eliminando de forma iterativa las variables no significativas hasta quedarse con el modelo final:
  `Tiempo(s) = -0.18253 * Edad - 36.73470 * Velocidad Media (Km/h) + 3.60255 * equipo13 + 3.30582 * equipo20 + 3.78435 * equipo22 + 3702.47453`

Para cada modelo se analizan los graficos de diagnostico (residuos, Q-Q plot, escala-localizacion y apalancamiento), el analisis de varianza (`aov`) y la normalidad de los residuos (test de Pearson).

## Conclusiones principales

- `Tiempo(s)` no se ajusta a una distribucion normal; se ajusta mejor a una distribucion Weibull.
- La media del tiempo es significativamente superior a 1860 segundos.
- La edad afecta positivamente al tiempo: los ciclistas mas jovenes tienden a completar la prueba mas rapido.
- La velocidad media afecta positivamente al tiempo total, en el sentido esperado.
- De los 23 equipos, solo tres resultan significativos en el modelo final (equipo13 - LOTTO DSTNY, equipo20 - ALPECIN-DECEUNINCK y equipo22 - INEOS GRENADIERS), todos con coeficiente positivo.
- Aunque las variables del modelo final son significativas, no se cumplen los supuestos de homocedasticidad, linealidad ni normalidad de los residuos.

## Requisitos para ejecutar el codigo

El fichero `Codigo.Rmd` requiere las siguientes librerias de R:

```r
install.packages(c("readxl", "summarytools", "fitdistrplus", "nortest", "goftest"))
```

Ademas, es necesario disponer del fichero de datos `Ciclistas.xlsx` y ajustar la ruta de lectura (`read_excel(...)`) al lugar donde se encuentre en el sistema.

## Autores

- Arturo Hernanz Carrasco-Munoz
- Alvaro Garcia Piqueras
