# EPG4006 — Proyecto Final: Modelos Lineales Generalizados

Modelación y pronóstico de la demanda mensual de **Monarca**, una microempresa de venta de arreglos con plantas y orquídeas, aplicando un **GLM de conteo** (Poisson y Binomial Negativa).

Trabajo final del curso **EPG4006 – Modelos Lineales Generalizados**, Pontificia Universidad Católica.

---

## Objetivo del proyecto

Modelar el **número de ventas por mes** de Monarca como una variable de conteo, con el fin de:

1. Cuantificar la **tendencia** de crecimiento del negocio.
2. Evaluar el efecto de la **estacionalidad** (fechas comerciales como San Valentín, Día de la Madre y Navidad).
3. **Pronosticar** la demanda de los meses siguientes con intervalos de confianza.

Se comparan dos modelos de la familia de conteo —**Poisson** y **Binomial Negativa**— y se selecciona el mejor según su ajuste y su capacidad para manejar la sobredispersión de los datos.

---

## Descripción de la base de datos

La base proviene del registro operativo real de ventas de Monarca. **Los datos han sido anonimizados**: los nombres de clientes, destinatarios, vendedores, domiciliarios y empresas fueron reemplazados por códigos genéricos (`CLI_XXXX`, `VEND_XXXX`, `DOM_XXXX`, `EMP_XXXX`), y se eliminó cualquier dato de contacto o dirección identificable.

### Archivo

`PRODUCTOS_RELANZAMIENTO_Monarca_ANONIMIZADO.xlsx`
Hoja: `Salidas de Inventario 2024`. Los encabezados reales están en la fila 10; las filas anteriores contienen metadatos de la planilla.

### Contenido

| Concepto | Detalle |
|---|---|
| Registros de venta (`Typo == VEN`) | 1.253 transacciones |
| Rango temporal completo | Dic 2023 – Jul 2026 |
| Meses completos usados en el modelo | 31 (Dic 2023 – Jun 2026) |
| Motivos principales | Duelo (720), Cumpleaños (233), Experiencia (131), Día de la Madre, Recuperación |

### Variables relevantes para el análisis

| Columna | Descripción | Uso en el modelo |
|---|---|---|
| `FCompra` | Fecha de la compra | Construcción de la serie mensual y del índice de tendencia |
| `Total` | Valor total de la venta (COP) | Variable auxiliar / análisis de monto |
| `Motivo` | Ocasión de la compra (Duelo, Cumpleaños, etc.) | Segmentación descriptiva |
| `Plataforma` | Canal de venta (WhatsApp, Instagram, Referido) | Descriptivo |
| `Kit` | Familia de arreglo | Descriptivo |

> El resto de las columnas corresponden a detalles operativos de inventario (materas, plantas, cajas, velas, logística de despacho) que no se usan directamente en el modelo de conteo.

### Variable construida (respuesta)

A partir de las transacciones se agregó la **serie mensual**:

| Variable | Descripción |
|---|---|
| `ventas` | Número de ventas del mes (**respuesta**, conteo entero ≥ 0) |
| `t` | Índice de tiempo (0, 1, 2, …) que captura la tendencia |
| `mes_especial` | Factor de estacionalidad: *Normal, San Valentín, Día de la Mujer, Día de la Madre, Navidad* |

---

## Metodología

El conteo mensual de ventas se modela con un GLM de enlace logarítmico:

```
log(E[ventas]) = β₀ + β₁·t + Σ γₖ·mes_especialₖ
```

**Plan de análisis:**

1. Agregación de la serie mensual y análisis exploratorio (tendencia y estacionalidad).
2. Ajuste de un **modelo de Poisson**.
3. Diagnóstico de **sobredispersión** (estadístico de Pearson χ²/gl).
4. Ajuste del modelo alternativo **Binomial Negativa**, que incorpora un parámetro de dispersión.
5. Selección del mejor modelo por **AIC**.
6. Interpretación mediante *Incidence Rate Ratios* (IRR) y **pronóstico** de los meses futuros.

---

## Principales resultados

- Los datos presentan **sobredispersión** (varianza ≫ media), por lo que la Poisson resulta insuficiente y la **Binomial Negativa** se selecciona como modelo final (menor AIC).
- Existe una **tendencia creciente significativa**: el negocio aumenta su volumen de ventas de forma sostenida en el periodo analizado.
- El efecto de las fechas comerciales sobre el conteo mensual total no resulta determinante una vez controlada la tendencia.
- El modelo permite **pronosticar** la demanda de los meses siguientes con intervalos de confianza, un insumo directo para la planeación de inventario y logística.

---

## Estructura del repositorio

```
EPG4006/
├── README.md
├── data/
│   ├── ventasTiendaVirtual.xlsx   # base anonimizada
│   └── monarca_mensual.csv        # serie mensual agregada

```

*(Ajusta las rutas según cómo subas los archivos.)*

---

]



## ⚠️ Nota sobre privacidad

La base incluida en este repositorio está **anonimizada**. No contiene nombres reales de personas ni datos de contacto. Se comparte únicamente con fines académicos, en el marco del curso EPG4006.
