# 📘 Memoria Técnica - PythonWeb Flask

## 🎯 Resumen Ejecutivo

**Proyecto:** PythonWeb - Micro Landing Page  
**Stack:** Flask 1.1.2 + Python 3.11 + Jinja2  
**Tipo:** Aplicación web minimalista (WSGI)  
**Estado:** Desarrollo (⚠️ Despliegue Heroku deprecado en 2022)  
**Propósito:** Landing page didáctica de dos rutas

---

## 📋 Índice

1. [Arquitectura General](#arquitectura-general)
2. [Componentes Principales](#componentes-principales)
3. [Flujo de Datos](#flujo-de-datos)
4. [Estructura](#estructura-del-proyecto)
5. [Despliegue](#despliegue)
6. [Seguridad Actual](#seguridad-actual)

---

## 🏗️ Arquitectura General

### Stack Tecnológico

```
┌─────────────────────────────────────────┐
│    Cliente (Navegador)                  │
└──────────────────┬──────────────────────┘
                   │ HTTP/HTTPS
        ┌──────────▼──────────────┐
        │  NGINX (Proxy)          │  Producción
        │  :80, :443              │
        └──────────┬──────────────┘
                   │
        ┌──────────▼──────────────┐
        │  Gunicorn (WSGI)        │  Production
        │  :8000 (socket)         │
        └──────────┬──────────────┘
                   │
        ┌──────────▼──────────────┐
        │  Flask App (index.py)   │
        │  - 2 rutas HTML         │
        │  - Plantillas Jinja2    │
        └─────────────────────────┘
```

### Patrón MVC Simplificado

```
Request HTTP
    │
    ▼
URL Router (@app.route)
    │
    ├─ / (home) ────→ render_template('home.html')
    └─ /about ─────→ render_template('about.html')
    
Templates (Jinja2)
    ├─ layout.html (base)
    ├─ home.html
    └─ about.html
    
Static Assets
    └─ /static/css/main.css
```

---

## 🧩 Componentes Principales

### 1. Archivo Principal: `index.py`

```python
from flask import Flask, render_template 

app = Flask(__name__)

@app.route('/')
def home():
    return render_template('home.html')

@app.route('/about')
def about():
    return render_template('about.html')

if __name__ == '__main__':
    app.run(debug=True)  # ⚠️ NO PRODUCCIÓN
```

**Características:**
- Minimalista (16 líneas)
- 2 rutas funcionales
- Uso de plantillas Jinja2
- ⚠️ DEBUG=True en desarrollo

**Problemas:**
- ❌ `debug=True` expone debugger
- ❌ Sin manejo de errores
- ❌ Sin logging
- ❌ Sin validación de entrada

### 2. Plantillas Jinja2 (`templates/`)

```
templates/
├── layout.html       (Base común)
├── home.html         (Homepage)
└── about.html        (Página about)
```

**Estructura:**
```
layout.html
├── DOCTYPE
├── HTML structure
├── CSS imports
└── {% block content %}

home.html
├── {% extends "layout.html" %}
└── {% block content %} ...

about.html
├── {% extends "layout.html" %}
└── {% block content %} ...
```

**Características:**
- ✅ Herencia de plantillas
- ✅ Auto-escape de Jinja2 (previene XSS)
- ⚠️ Sin carga dinámica de datos

### 3. Archivos Estáticos (`static/`)

```
static/
└── css/
    └── main.css      (Estilos)
```

**Características:**
- ✅ CSS centralizado
- ✅ Servido desde `/static/`
- ⚠️ Sin cache headers configurado
- ⚠️ Sin compresión GZIP

### 4. Configuración de Despliegue

#### `Procfile` (Heroku - Deprecado)
```
web: gunicorn index:app
```

**Explicación:**
- `web:` - Tipo de dyno
- `gunicorn` - Servidor WSGI
- `index:app` - Módulo:variable Flask

#### `runtime.txt`
```
Python 3.11
```

**Nota:** Especifica versión de Python

#### `requirements.txt`
```
click==7.1.2
Flask==1.1.2
gunicorn==20.0.4
itsdangerous==1.1.0
Jinja2==2.11.2
MarkupSafe==1.1.1
Werkzeug==1.0.1
```

**Análisis:**
- ✅ Gunicorn incluido
- ❌ Versiones MUY ANTIGUAS (2019-2020)
- ⚠️ Vulnerabilidades conocidas en Flask 1.1.2
- ❌ Sin herramientas de seguridad (bleach, CSP, etc.)

---

## 🔄 Flujo de Datos

### Solicitud GET a `/` (Home)

```
1. Cliente: GET http://localhost:5000/
   
2. Flask Router: Matchea @app.route('/')
   
3. Handler: home()
   └─→ render_template('home.html')
   
4. Jinja2: Renderiza layout.html
   ├─→ Lee layout.html
   ├─→ Reemplaza {% block content %} con home.html
   └─→ Aplica auto-escape
   
5. Response: HTTP 200 + HTML renderizado
   
6. Cliente: Renderiza HTML en navegador
```

### Solicitud GET a `/about` (About)

```
1. Cliente: GET http://localhost:5000/about
   
2. Flask Router: Matchea @app.route('/about')
   
3. Handler: about()
   └─→ render_template('about.html')
   
4. Jinja2: Renderiza plantilla
   ├─→ Layout + contenido
   └─→ Auto-escape
   
5. Response: HTTP 200 + HTML
   
6. Cliente: Renderiza
```

### Solicitud a Ruta No Existente

```
1. Cliente: GET http://localhost:5000/no-existe
   
2. Flask Router: ❌ No matchea ninguna ruta
   
3. Flask Default Handler:
   └─→ Retorna HTTP 404 Not Found
   
4. Response: HTML 404 genérico
```

---

## 📁 Estructura del Proyecto

```
pythonweb/
├── index.py                    ← Aplicación principal
├── requirements.txt            ← Dependencias
├── runtime.txt                 ← Versión Python
├── Procfile                    ← Configuración Heroku
│
├── templates/
│   ├── layout.html            ← Plantilla base
│   ├── home.html              ← Homepage
│   └── about.html             ← Página about
│
├── static/
│   └── css/
│       └── main.css           ← Estilos
│
├── venv/                       ← Entorno virtual
└── README.md                   ← Documentación
```

**Tamaño:**
- Código fuente: ~50 líneas (index.py)
- Plantillas: ~100 líneas
- Estilos: Variable
- Total: Muy pequeño (~1 KB comprimido)

---

## 🌐 Rutas Disponibles

```
GET  /                 → home()         → home.html
GET  /about            → about()        → about.html
GET  /static/<path>    → [automático]   → archivos CSS
GET  /favicon.ico      → [automático]   → favicon (404 si no existe)
```

**Rutas por defecto de Flask:**
```
404  Cualquier otra ruta
500  Error interno
```

---

## 🚀 Despliegue

### Desarrollo Local ✅

```bash
python index.py
```

**Resultado:**
```
* Running on http://localhost:5000/ (Press CTRL+C to quit)
* Restarting with reloader
```

**Características:**
- ✅ Servidor de desarrollo integrado (Werkzeug)
- ✅ Recarga automática
- ⚠️ DEBUG=True (expone debugger)
- ⚠️ Single-threaded

### Producción ❌ (Heroku)

```
# Heroku dejó plan gratuito en Nov 2022
# El proyecto es referencial
```

**Alternativas actuales:**
- ✅ Render.com
- ✅ Railway
- ✅ PythonAnywhere
- ✅ VPS (AWS, DigitalOcean, etc.)

### Despliegue Manual (VPS)

```bash
# 1. Instalar dependencias
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# 2. Con Gunicorn
gunicorn index:app --bind 0.0.0.0:8000

# 3. Configurar NGINX como proxy
# (Ver SEGURIDAD.md)
```

---

## 🔐 Seguridad Actual

### Fortalezas ✅

- ✅ Auto-escape de Jinja2 (previene XSS básico)
- ✅ Flask tiene CSRF protection (cuando se usa)
- ✅ No acceso a BD (no hay BD)
- ✅ Gunicorn incluido para producción
- ✅ Simple = menos superficie de ataque

### Vulnerabilidades ⚠️

| # | Problema | Riesgo | Severidad |
|---|----------|--------|-----------|
| 1 | DEBUG=True en desarrollo | Exposición debugger | 🔴 CRÍTICO |
| 2 | Flask 1.1.2 (Muy vieja) | Vulnerabilidades conocidas | 🔴 CRÍTICO |
| 3 | Sin HTTPS | Man-in-the-middle | 🔴 CRÍTICO |
| 4 | Sin CSP headers | XSS sin restricción | 🔴 ALTO |
| 5 | Sin logging | No trazable | 🟡 MEDIO |
| 6 | Sin rate limiting | DoS | 🟡 MEDIO |
| 7 | Sin validación entrada | Injection | 🟡 MEDIO |
| 8 | Heroku plan deprecado | No despliegue | 🟡 MEDIO |
| 9 | Sin cache headers | Problemas de caché | 🟡 BAJO |
| 10 | Sin compresión | Lento | 🟢 BAJO |

---

## 📊 Comparación con Proyectos Anteriores

| Aspecto | Flask | Django (Landing) | Django (CRUD) |
|--------|-------|-----------------|--------------|
| Framework | Flask (minimalista) | Django (completo) | Django (completo) |
| Líneas código | ~50 | ~200 | ~100 |
| Complejidad | Muy baja | Media | Media |
| BD | ❌ No | ✅ SQLite | ✅ SQLite |
| Admin | ❌ No | ✅ Sí | ✅ Sí |
| ORM | ❌ No | ✅ Sí | ✅ Sí |
| Vulnerabilidades | 10 | 11 | 13 |
| Críticas | 3 | 3 | 4 |
| Esfuerzo fix | 4 horas | 1 día | 1-2 días |

---

## 🎯 Conclusión

**Proyecto Flask:**
- ✅ Muy simple y educativo
- ✅ Código limpio
- ❌ **MUY VIEJO** (2019-2020)
- ❌ **Heroku ya no disponible**
- ❌ **NO apto para producción**

**Recomendación:**
- 🔴 Actualizar Flask a 3.0+
- 🔴 Actualizar dependencias
- 🔴 Quitar debug=True
- ✅ Mantener como referencia educativa

**Ver:** [`SEGURIDAD.md`](SEGURIDAD.md) para detalles.
