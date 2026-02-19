# 🌐 PythonWeb - Micro Landing Page Flask

> **Estado:** 🟡 Educativo | **Framework:** Flask 3.11 | **Heroku:** ❌ Deprecado (Nov 2022)

---

## 📋 Índice

- [¿Qué es?](#-qué-es)
- [Quick Start](#-quick-start)
- [Documentación](#-documentación)
- [Seguridad](#-seguridad--crítico)
- [Despliegue](#-despliegue)
- [Diferencias con Django](#-diferencias-con-django)

---

## 🎯 ¿Qué es?

Aplicación **Flask minimalista** que sirve una landing page con 2 rutas.

**Características:**
- ✅ Micro-app (50 líneas)
- ✅ 2 rutas: `/` (home) y `/about`
- ✅ Plantillas Jinja2
- ✅ Estáticos CSS
- ✅ Gunicorn incluido
- ✅ Heroku-ready (ahora en Render/Railway)

**Uso:** Referencia educativa, punto de partida

**Nota:** Heroku dejó soporte gratuito en Nov 2022 → Ahora necesita migración

---

## 🚀 Quick Start

### 1. Requisitos
```bash
python --version  # 3.11
pip --version
```

### 2. Setup
```bash
# Entorno virtual
python -m venv venv
.\venv\Scripts\activate        # Windows
source venv/bin/activate       # Linux/Mac

# Instalar (ACTUALIZADO)
pip install -r requirements.txt

# Nota: El requirements.txt actual está VIEJO
# Actualizar primero:
pip install Flask==3.0.0 Werkzeug==3.0.1 Jinja2==3.1.2 gunicorn
pip freeze > requirements.txt
```

### 3. Ejecutar
```bash
python index.py

# Acceder a:
# http://localhost:5000/
# http://localhost:5000/about
```

---

## 📚 Documentación

### Documentos Técnicos

| Documento | Contenido | Para |
|-----------|----------|------|
| **[MEMORIA_TECNICA.md](MEMORIA_TECNICA.md)** | Arquitectura Flask, flujos | Desarrolladores |
| **[SEGURIDAD.md](SEGURIDAD.md)** | 10 vulnerabilidades + fixes | DevOps/Seguridad |
| **[INDICE_SEGURIDAD.md](INDICE_SEGURIDAD.md)** | Checklist ejecutivo | Gerentes |

### Guía de Lectura

**👨‍💻 Desarrollador:**
1. Quick Start (arriba)
2. MEMORIA_TECNICA.md
3. SEGURIDAD.md

**🔒 DevOps:**
1. INDICE_SEGURIDAD.md (checklist)
2. SEGURIDAD.md (soluciones)
3. Sección Despliegue

---

## 📁 Estructura

```
pythonweb/
├── index.py                    ← Aplicación (50 líneas)
├── requirements.txt            ← Dependencias (⚠️ MUY VIEJAS)
├── runtime.txt                 ← Python 3.11
├── Procfile                    ← Heroku (deprecado)
│
├── templates/
│   ├── layout.html            ← Base común
│   ├── home.html              ← Homepage
│   └── about.html             ← About
│
├── static/
│   └── css/
│       └── main.css           ← Estilos
│
└── README.md                   ← Original
```

**Tamaño total:** ~5 KB (minimalista)

---

## 🔐 SEGURIDAD ⚠️ CRÍTICO

### ⛔ Problemas Principales

1. **Flask 1.1.2** - 5 AÑOS VIEJA (2019)
2. **Werkzeug 1.0.1** - 4 AÑOS VIEJA (2020)
3. **DEBUG=True** - Expone debugger
4. **Heroku deprecado** - No se puede desplegar
5. **Sin HTTPS** - Tráfico sin cifrar

### ✅ Lo Crítico (1 hora)

```bash
# 1. Actualizar dependencias
pip install Flask==3.0.0 Werkzeug==3.0.1 Jinja2==3.1.2
pip freeze > requirements.txt

# 2. Quitar DEBUG=True
# En index.py: app.run(debug=False)

# 3. Agregar headers de seguridad
# Ver SEGURIDAD.md

# 4. HTTPS (certificado)
# Ver SEGURIDAD.md - Sección NGINX
```

### Vulnerabilidades Encontradas: 10

🔴 **Críticas (3):**
1. Flask obsoleta
2. DEBUG=True
3. Sin HTTPS

🔴 **Altas (4):**
4. Sin CSP headers
5. Heroku deprecado
6. Sin logging
7. Sin rate limiting

🟡 **Medias (3):**
8. Sin validación
9. Sin cache headers
10. Sin compresión

**Ver completo:** [`INDICE_SEGURIDAD.md`](INDICE_SEGURIDAD.md)

---

## 🚀 Despliegue

### Desarrollo ✅
```bash
python index.py
# http://localhost:5000
```

### Producción ❌ (Requiere cambios)

**Estado actual:**
```
Heroku → ❌ Deprecado (Nov 2022)
```

**Alternativas:**
```
✅ Render.com    (más fácil - recomendado)
✅ Railway       (minimalista)
✅ PythonAnywhere (Python-focused)
✅ VPS (DigitalOcean, AWS, etc.)
```

**Migrar a Render.com (5 minutos):**
1. Push a GitHub
2. Conectar repo en Render.com
3. Crear Web Service
4. Deploy automático ✅

**Despliegue Manual (VPS):**

```bash
# 1. SSH a servidor
ssh user@your-server.com

# 2. Clonar repo
git clone https://github.com/user/pythonweb.git
cd pythonweb

# 3. Setup
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# 4. Ejecutar con Gunicorn
gunicorn index:app --bind 0.0.0.0:8000 --workers 4

# 5. NGINX como proxy (ver SEGURIDAD.md)
```

---

## 🔧 Comparación: Flask vs Django

| Aspecto | Flask | Django |
|--------|-------|--------|
| **Líneas código** | 50 | 200+ |
| **Complejidad** | ✅ Muy baja | 🟡 Media |
| **Base datos** | ❌ No | ✅ ORM incluido |
| **Admin panel** | ❌ No | ✅ Sí |
| **Seguridad built-in** | ⚠️ Básica | ✅ Completa |
| **Learning curve** | ✅ Fácil | 🟡 Media |
| **Production ready** | ❌ No (por defecto) | ✅ Sí |
| **Vulnerabilidades** | 10 | 11-13 |

**Conclusión:** Flask es más simple pero requiere más configuración manual.

---

## 🧩 Componentes

### index.py (Aplicación Principal)

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
    app.run(debug=True)  # ⚠️ Cambiar a False
```

### Plantillas

```
layout.html  ← Base común
├── home.html  (hereda de layout)
└── about.html (hereda de layout)
```

### Estáticos

```
static/css/main.css  ← Estilos
```

---

## 🎓 Conceptos Clave

### Flask vs Django

```
Flask:        Microframework
              - Minimal
              - Flexible
              - Aprende rápido

Django:       Full-stack framework
              - Batteries included
              - Convención sobre config
              - Production-ready
```

### WSGI

```
Web Server Gateway Interface

Cliente ↔ NGINX ↔ Gunicorn ↔ Flask App
         (proxy)   (WSGI)
```

### Rutas en Flask

```python
@app.route('/')              # GET /
def home():
    pass

@app.route('/about')         # GET /about
def about():
    pass

@app.route('/api', methods=['POST'])  # POST /api
def api():
    pass
```

---

## ✅ Checklist: Antes de Producción

```
[ ] Actualizar Flask a 3.0+
[ ] Actualizar Werkzeug y Jinja2
[ ] DEBUG=False
[ ] HTTPS/TLS habilitado
[ ] CSP headers
[ ] Logging configurado
[ ] Rate limiting
[ ] Validación de entrada
[ ] Migrado de Heroku
[ ] Testing en staging
```

---

## 📊 Estadísticas

| Métrica | Valor |
|---------|-------|
| **Líneas código** | ~50 |
| **Rutas** | 2 |
| **Plantillas** | 3 |
| **Dependencias** | 7 |
| **Complejidad** | Muy baja |
| **Setup time** | 5 min |
| **Vulnerabilidades** | 10 |
| **Críticas** | 3 |
| **Esfuerzo fix** | 1-2 horas |

---

## 🎯 Recomendaciones

### Corto plazo (HOY)
1. Actualizar Flask a 3.0+
2. Quitar DEBUG=True
3. Agregar HTTPS

### Mediano plazo (PRÓXIMOS DÍAS)
4. Migrar de Heroku a Render/Railway
5. Logging + Rate limiting
6. CSP headers

### Largo plazo (PRODUCCIÓN)
7. Monitoreo (Sentry)
8. Backup/CI/CD
9. Scaling

---

## ⚖️ Licencia

[Especificar licencia - MIT, Apache, etc.]

---

## 👨‍💼 Autor

**Fernando Pérez**  
GitHub: [@tuusuario]  
Email: tu@email.com

---

## 🎯 Resumen

```
✅ Funciona en desarrollo
🔴 Flask MUY VIEJA (5 años)
❌ Heroku ya no funciona
🟡 10 vulnerabilidades
⏱️ 1-2 horas para arreglarlo
🚀 Listo para producción después
```

**Próximo paso:** Leer [`INDICE_SEGURIDAD.md`](INDICE_SEGURIDAD.md)

---

<div align="center">

**[📖 MEMORIA_TECNICA.md](MEMORIA_TECNICA.md)** · **[🔐 SEGURIDAD.md](SEGURIDAD.md)** · **[⚡ INDICE_SEGURIDAD.md](INDICE_SEGURIDAD.md)**

🟡 Educativo | ❌ No producción (aún) | 🔴 Heroku deprecado

</div>
