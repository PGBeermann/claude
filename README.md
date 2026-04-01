# N8N Workflow: Formulario a PDF

Workflow que recibe un formulario con datos personales e imágenes y genera un PDF compilado.

## Flujo del Workflow

```
[Form Trigger] → [Preparar HTML] → [Preparar nombre] → [HTML a PDF] → [Renombrar] → [Responder con PDF]
```

## Campos del Formulario

| Campo | Tipo | Requerido |
|-------|------|-----------|
| Nombre | Texto | Sí |
| Cedula | Texto | Sí |
| Titulo | Archivo (imagen) | Sí |
| Creditos | Archivo (imagen) | Sí |
| Copia de Cedula | Archivo (imagen) | Sí |

## Nodos

### 1. Formulario de Registro (`n8n-nodes-base.formTrigger`)
- Expone una URL pública con el formulario
- Acepta archivos `.jpg`, `.jpeg`, `.png`, `.pdf`

### 2. Preparar HTML (`n8n-nodes-base.code`)
- Extrae texto e imágenes del formulario
- Convierte imágenes a base64 para incrustarlas en el HTML
- Genera un HTML estilizado con todos los datos

### 3. Preparar nombre de archivo (`n8n-nodes-base.code`)
- Prepara el nombre final del PDF con formato `registro_{cedula}_{timestamp}.pdf`

### 4. HTML a PDF — Nodo nativo (`n8n-nodes-base.html`)
- Convierte el HTML a PDF usando Puppeteer integrado de n8n
- **Requiere n8n >= 1.22**
- Formato A4, márgenes estándar, fondo impreso

### 5. Renombrar PDF (`n8n-nodes-base.moveBinaryData`)
- Renombra el archivo binario con el nombre dinámico

### 6. Responder con PDF (`n8n-nodes-base.respondToWebhook`)
- Devuelve el PDF al navegador como descarga directa

## Requisitos

- **n8n versión 1.22+** para usar el nodo HTML con soporte PDF nativo
- Chromium/Puppeteer disponible en el entorno n8n (viene incluido en imágenes Docker oficiales)

## Instalación

1. En n8n, ir a **Workflows → Import**
2. Importar el archivo `workflow_form_to_pdf.json`
3. Activar el workflow
4. La URL del formulario aparecerá en el nodo **Formulario de Registro**

## Alternativa: API externa html2pdf.app

Si la versión de n8n no soporta el nodo HTML nativo para PDF, se incluye el nodo
`Generar PDF (html2pdf.app)` que usa la API de [html2pdf.app](https://html2pdf.app).

Para usarlo:
1. Registrarse en html2pdf.app y obtener una API key
2. Reemplazar `YOUR_HTML2PDF_API_KEY` en el nodo correspondiente
3. Reconectar el nodo **Preparar HTML** → **Generar PDF (html2pdf.app)** → **Responder con PDF**

## Estructura del PDF generado

```
┌─────────────────────────────────┐
│        Registro de Datos        │
│    Generado el [fecha actual]   │
├─────────────────────────────────┤
│ Información Personal            │
│  Nombre: [valor]                │
│  Cédula: [valor]                │
├─────────────────────────────────┤
│ Título                          │
│  [imagen incrustada]            │
├─────────────────────────────────┤
│ Créditos                        │
│  [imagen incrustada]            │
├─────────────────────────────────┤
│ Copia de Cédula                 │
│  [imagen incrustada]            │
├─────────────────────────────────┤
│ Documento generado automát...   │
└─────────────────────────────────┘
```
