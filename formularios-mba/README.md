# 🤖 Automatización de Formularios MBA (n8n)

Sistema de creación automática de Google Forms para las sesiones de evaluación de los programas MBA de Rebs, implementado con 11 flujos n8n en producción.

---

## Contexto

Cada sesión de los programas MBA requería crear manualmente un Google Form de valoración, configurarlo con las preguntas correctas y registrar el enlace en el sistema. Con varios programas activos en paralelo, el proceso era repetitivo y propenso a errores.

**Resultado:** el equipo solo rellena un Excel de control. El sistema crea los formularios automáticamente, los configura con las preguntas correctas y registra el enlace — sin intervención manual.

---

## Arquitectura del flujo (9 nodos por programa)

```
01 Trigger (Schedule)
    │  Ejecución periódica automática
    │
02 Leer Sheet (Google Sheets)
    │  Lee el Excel de control del programa
    │
03 Filtro pendientes (Filter)
    │  Check Creado ≠ "SI" → solo procesa filas sin formulario creado
    │
03b Loop por formulario (SplitInBatches)
    │  Procesa una fila por iteración hasta completar todas las pendientes
    │
04 Parsear datos (Code — JavaScript)
    │  Extrae título, descripción, preguntas y opciones del texto de la celda
    │
05 Crear Form (HTTP Request → Google Forms API)
    │  POST /v1/forms → devuelve formId
    │
06 Esperar 5s (Wait)
    │  Permite que la API de Google propague el formulario recién creado
    │
07 Construir preguntas (Code — JavaScript)
    │  Arma el payload batchUpdate con preguntas tipo RADIO y campo de comentarios
    │
08 Añadir preguntas (HTTP Request → Google Forms API)
    │  POST /v1/forms/{formId}:batchUpdate
    │
09 Marcar Check Creado (Google Sheets)
       Escribe "SI" en columna Check Creado + guarda form_url
       → el flujo no volverá a procesar esta fila
```

---

## Lógica de deduplicación

La columna `Check Creado` actúa como estado persistente del sistema. En cada ejecución el flujo lee todas las filas y filtra las que ya tienen valor `SI` — nunca duplica un formulario aunque el trigger se ejecute múltiples veces sobre los mismos datos.

El identificador único por fila (`session_id`) se usa como clave de actualización en el paso 09, garantizando que la escritura de vuelta al Sheet sea exacta aunque el orden de las filas cambie.

---

## Lógica de parseo (nodo 04)

El nodo de parseo en JavaScript interpreta el contenido de cada celda del Excel para extraer:

- **Título** y **descripción** del formulario — separados por salto de línea con etiqueta `Descripción:`
- **Enunciado** de cada pregunta — primera línea de la celda
- **Opciones de respuesta** — segunda línea, separadas por comas
- **Fallback de opciones** — si no se especifican, usa `Suspenso · Aprobado · Notable · Sobresaliente`
- **Campo de comentarios** — se añade siempre como última pregunta, tipo texto libre, no obligatorio

Este diseño permite que el equipo configure formularios distintos simplemente editando el Excel, sin tocar el flujo de n8n.

---

## Escalabilidad

Añadir un nuevo programa es copiar el flujo en n8n y apuntar al Sheet de control correspondiente. La lógica de parseo, creación y deduplicación es idéntica en todos los programas — el único parámetro que cambia es el origen de datos.

**Programas en producción:** MBA Presencial Madrid · MBA Presencial Málaga · Master Online · VPI · GCV · MIC · UDI

---

## Stack técnico

| Herramienta | Uso |
|-------------|-----|
| n8n | Orquestación de flujos |
| Google Sheets API | Lectura y escritura del Excel de control |
| Google Forms API | Creación y configuración de formularios vía HTTP |
| JavaScript (nodos Code) | Parseo de datos y construcción de payloads |

---

## Estado

✅ **11 flujos activos en producción**

> ⚠️ Este repositorio contiene únicamente documentación técnica. Los IDs de hojas, credenciales OAuth y datos internos de la empresa no están incluidos.
