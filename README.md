# ⚙️ Rebs Automation Suite

> Suite de automatización desarrollada de forma autónoma durante las prácticas en **Rebs Real Estate Business School** (Oct 2025 – Jun 2026).

Dos sistemas independientes en producción activa que eliminaron procesos manuales repetitivos del equipo operativo.

---

## 📦 Sistemas

### 🏠 [Pulsímetro Inmobiliario](pulsimetro/README.md)
Pipeline Python de 3 fases que integra datos del INE y Ministerio de Transportes, actualiza 9 variables para 51 provincias y genera 61 PDFs corporativos con diseño completo.

**De horas de trabajo manual a ~5 minutos.**

### 🤖 [Formularios MBA](formularios-mba/README.md)
11 flujos n8n en producción que generan automáticamente Google Forms desde un Excel de control con deduplicación de estado. Cubre todos los programas presenciales y online.

---

## 🛠️ Stack técnico global

| Capa | Tecnologías |
|------|-------------|
| Lenguaje principal | Python 3.9+ |
| Manipulación Excel | openpyxl · xlrd |
| Generación PDF | reportlab · pypdf |
| Render gráficos | LibreOffice headless · pdf2image · Pillow |
| Automatización flujos | n8n |
| APIs | Google Forms API · Google Sheets API · Google Drive API · INE API |
| Fuentes de datos | INE (JSON) · Ministerio de Transportes (XLS) |

---

## 👤 Autor

**Enrique Benito López** — Full Stack Developer  
Prácticas DAM2 Dual Intensivo · Rebs Real Estate Business School · Madrid  
[linkedin.com/in/enrique-benito-lópez-developer](https://linkedin.com/in/enrique-benito-lópez-developer)

---

> ⚠️ **Confidencialidad:** Este repositorio contiene únicamente documentación técnica de arquitectura. El código fuente, credenciales y datos de la empresa no están incluidos.
