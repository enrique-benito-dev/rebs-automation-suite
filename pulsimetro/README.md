# 🏠 Suite Pulsímetro Inmobiliario

Pipeline de automatización en tres fases que actualiza y genera el Pulsímetro Inmobiliario de Rebs — informe trimestral de mercado para 51 provincias + España.

**Resultado: de horas de trabajo manual a ~5 minutos.**

---

## Contexto

El Pulsímetro Inmobiliario es un informe trimestral que Rebs publica con datos de mercado inmobiliario por provincia. El proceso de actualización requería descargar manualmente ficheros de varias fuentes oficiales, copiar datos provincia a provincia en Excel y generar los PDFs corporativos con diseño completo.

Esta suite automatiza el proceso completo en tres fases independientes.

---

## Fase 1 — Actualizador de datos ✅ Producción activa

Descarga y procesa datos de fuentes oficiales y actualiza el Excel maestro del Pulsímetro.

**Fuentes:**
- **INE** — API JSON pública: compraventas, hipotecas, precios por provincia
- **Ministerio de Transportes** — descarga automatizada de ficheros XLS por variable

**Stack:** `Python` · `urllib` · `openpyxl` · `xlrd`

**Decisiones técnicas:**
- Normalización de nombres de provincias entre fuentes mediante diccionario de excepciones — cada fuente oficial usa convenciones distintas
- Validación estructural antes de procesar: si el formato de la fuente cambia, el script para con alerta explícita en lugar de escribir datos incorrectos en silencio
- Sistema de backup automático con timestamp antes de cada actualización
- Delays entre peticiones para respetar los límites de las APIs públicas

---

## Fase 2 — Actualizador del Excel de gráficos ✅ Producción activa

Actualiza el archivo maestro de gráficos con los datos procesados en la Fase 1, manteniendo intactos todos los gráficos, fórmulas y estilos del Excel.

**Stack:** `Python` · `openpyxl`

**Variables actualizadas (9 × 51 provincias):**

| Fila | Métrica |
|------|---------|
| 2 | Viviendas Visadas |
| 3 | Viviendas Iniciadas |
| 4 | Viviendas Terminadas |
| 5 | Nº Compraventas Vivienda Nueva |
| 6 | Nº Compraventas Segunda Mano |
| 7 | Nº de Hipotecas |
| 8 | Precio Medio Vivienda Nueva |
| 9 | Precio Medio Segunda Mano |
| 10 | Hipoteca Media (€) |

**Decisión de diseño clave:** escritura no destructiva — solo sobreescribe los valores numéricos en las columnas de año indicadas. La estructura del Excel, los gráficos y los estilos nunca se modifican. El resultado se guarda como archivo nuevo con sufijo `_actualizado`, preservando siempre el original.

---

## Fase 3 — Generador de PDFs corporativos ⚠️ Funcional

Genera 61 PDFs de 12 páginas cada uno con datos actualizados, gráficos renderizados y diseño corporativo completo de Rebs.

**Stack:** `Python` · `openpyxl` · `reportlab` · `pypdf` · `LibreOffice headless` · `pdf2image` · `Pillow` · `numpy`

**Pipeline por provincia:**

```
Excel maestro (61 hojas)
    ├── Metadatos por hoja → mapeo de posición de gráficos
    ├── Datos numéricos → tablas en slides 5, 6 y 7
    └── Gráficos → render LibreOffice headless → PIL Image → autocrop

PDF plantilla corporativa
    └── Extracción de assets (logos, footer) como PNG

Por cada provincia:
    ├── Slide 5: Obra Nueva (gráfico + tabla de datos)
    ├── Slide 6: Nº de Operaciones (gráfico + tabla de datos)
    └── Slide 7: Valores Medios (gráfico + tabla de datos)

pypdf PdfWriter
    └── páginas 1-4 (plantilla) +
