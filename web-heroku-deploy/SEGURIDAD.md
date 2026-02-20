# 🔐 Guía de Seguridad - PythonWeb Flask

## ⚠️ Estado: NO APTO PARA PRODUCCIÓN

Análisis de vulnerabilidades y recomendaciones específicas.

---

## 🚨 Problema Principal

**Las dependencias están obsoletas (2019-2020):**
```
Flask 1.1.2          ← 5 AÑOS VIEJO
Werkzeug 1.0.1       ← 4 AÑOS VIEJO
Jinja2 2.11.2        ← 3 AÑOS VIEJO
```

**Solución urgente:**
```bash
pip install Flask==3.0.0    # Actualizar INMEDIATO
pip install Werkzeug==3.0   # Actualizar INMEDIATO
```

---

## 📋 Índice de Vulnerabilidades

1. [Críticas](#-críticas)
2. [Altas](#-altas)
3. [Medias](#-medias)
4. [Bajas](#-bajas)
5. [Checklist](#checklist-de-implementación)

---

## 🔴 CRÍTICAS

### 1. Flask 1.1.2 Obsoleta

**Problema:** Versión de 2019 con vulnerabilidades conocidas

**Riesgo:** Explotación de vulnerabilidades históricas

**Solución:**

```bash
# requirements.txt - Actualizar INMEDIATO
# ❌ VIEJO
Flask==1.1.2
Werkzeug==1.0.1
Jinja2==2.11.2

# ✅ NUEVO
Flask==3.0.0
Werkzeug==3.0.1
Jinja2==3.1.2
gunicorn==21.2.0
```

**Instalación:**
```bash
pip install --upgrade Flask Werkzeug Jinja2 gunicorn
pip freeze > requirements.txt
```

**Verificar:**
```bash
pip list | grep Flask
# Flask                3.0.0
```

---

### 2. DEBUG=True en Producción

**En `index.py`:**
```python
# ❌ INSEGURO
if __name__ == '__main__':
    app.run(debug=True)
```

**Riesgo:** 
- Expone debugger interactivo
- Permite ejecución remota de código
- Muestra variables de entorno

**Solución:**

```python
# ✅ SEGURO
import os

app = Flask(__name__)

# ... rutas ...

if __name__ == '__main__':
    debug = os.environ.get('FLASK_ENV') == 'development'
    app.run(debug=debug, host='127.0.0.1')
```

**Variables de entorno:**
```bash
# Desarrollo
export FLASK_ENV=development
export FLASK_DEBUG=1

# Producción
export FLASK_ENV=production
export FLASK_DEBUG=0
```

---

### 3. Sin HTTPS / TLS

**Problema:** Tráfico sin cifrar

**Solución - NGINX como proxy:**

```nginx
# /etc/nginx/sites-available/pythonweb

server {
    listen 80;
    server_name tudominio.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name tudominio.com;
    
    ssl_certificate /etc/ssl/certs/cert.pem;
    ssl_certificate_key /etc/ssl/private/key.pem;
    
    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

**Certificado (Let's Encrypt):**
```bash
sudo certbot certonly --standalone -d tudominio.com
```

---

## 🔴 ALTAS

### 4. Sin Content Security Policy

**Solución:**

```python
# index.py - Agregar
from flask import Flask

app = Flask(__name__)

@app.after_request
def set_security_headers(response):
    response.headers['Content-Security-Policy'] = "default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'"
    response.headers['X-Content-Type-Options'] = 'nosniff'
    response.headers['X-Frame-Options'] = 'DENY'
    response.headers['Referrer-Policy'] = 'strict-origin-when-cross-origin'
    return response

@app.route('/')
def home():
    return render_template('home.html')
```

---

### 5. Heroku Deprecado

**Problema:** Heroku dejó plan gratuito en Nov 2022

**Alternativas:**
```
Render.com      ✅ Fácil (similar a Heroku)
Railway         ✅ Minimalista
PythonAnywhere  ✅ Python-focused
DigitalOcean    ✅ VPS completo
AWS             ✅ Escalable
```

**Migrar a Render.com (5 min):**
1. Conectar GitHub
2. Crear Web Service
3. Usar `requirements.txt`
4. Deploy automático

---

### 6. Sin Logging

**Solución:**

```python
# index.py
import logging
from logging.handlers import RotatingFileHandler
import os

app = Flask(__name__)

# Configurar logging
if not app.debug:
    if not os.path.exists('logs'):
        os.mkdir('logs')
    
    file_handler = RotatingFileHandler('logs/pythonweb.log', 
                                       maxBytes=10240000, 
                                       backupCount=10)
    file_handler.setFormatter(logging.Formatter(
        '%(asctime)s %(levelname)s: %(message)s [in %(pathname)s:%(lineno)d]'
    ))
    file_handler.setLevel(logging.INFO)
    app.logger.addHandler(file_handler)
    
    app.logger.setLevel(logging.INFO)
    app.logger.info('PythonWeb startup')

@app.route('/')
def home():
    app.logger.info('Home page accessed')
    return render_template('home.html')

@app.route('/about')
def about():
    app.logger.info('About page accessed')
    return render_template('about.html')
```

---

### 7. Sin Rate Limiting

**Solución:**

```bash
pip install Flask-Limiter
```

```python
# index.py
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

limiter = Limiter(
    app=app,
    key_func=get_remote_address,
    default_limits=["200 per day", "50 per hour"]
)

@app.route('/')
@limiter.limit("100 per hour")
def home():
    return render_template('home.html')

@app.route('/about')
@limiter.limit("100 per hour")
def about():
    return render_template('about.html')
```

---

### 8. Sin Validación de Entrada

**Aunque no hay formularios, agregar para el futuro:**

```python
from flask import escape

@app.route('/search')
def search():
    query = request.args.get('q', '')
    # Sanitizar entrada
    safe_query = escape(query)
    return render_template('search.html', query=safe_query)
```

---

## 🟡 MEDIAS

### 9. Sin Cache Headers

**Solución:**

```python
@app.after_request
def add_cache_headers(response):
    if response.status_code == 200:
        # Caché estáticos por 30 días
        if request.path.startswith('/static/'):
            response.headers['Cache-Control'] = 'public, max-age=2592000'
        # Caché HTML por 1 hora
        else:
            response.headers['Cache-Control'] = 'public, max-age=3600'
    return response
```

---

### 10. Sin Compresión

**En NGINX:**
```nginx
gzip on;
gzip_types text/plain text/css text/xml text/javascript;
gzip_min_length 1000;
gzip_level 6;
```

---

## 🟢 BAJAS

### 11. Sin Monitoreo

**Solución - Sentry:**

```bash
pip install sentry-sdk
```

```python
import sentry_sdk
from sentry_sdk.integrations.flask import FlaskIntegration

sentry_sdk.init(
    dsn="https://your-sentry-dsn@sentry.io/project",
    integrations=[FlaskIntegration()],
    traces_sample_rate=1.0
)
```

---

## ✅ Checklist de Implementación

### Críticos (Haz hoy - 1 hora)
```
[ ] Actualizar Flask a 3.0+
[ ] Actualizar Werkzeug y Jinja2
[ ] Quitar debug=True
[ ] Agregar HTTPS (certificado)
[ ] CSP headers
```

### Altos (Próximos días - 2-3 horas)
```
[ ] Migrar de Heroku a Render/Railway
[ ] Logging
[ ] Rate limiting
[ ] Validación de entrada
```

### Medios (Próxima semana - 1 hora)
```
[ ] Cache headers
[ ] Compresión GZIP
[ ] Monitoreo (Sentry)
```

---

## 📊 Tabla de Vulnerabilidades

| # | Problema | Riesgo | Esfuerzo | Prioridad |
|---|----------|--------|----------|-----------|
| 1 | Flask vieja | Crítico | 5 min | 🔴 |
| 2 | DEBUG=True | Crítico | 5 min | 🔴 |
| 3 | Sin HTTPS | Crítico | 30 min | 🔴 |
| 4 | Sin CSP | Alto | 10 min | 🔴 |
| 5 | Heroku deprecado | Alto | 30 min | 🔴 |
| 6 | Sin logging | Alto | 15 min | 🟡 |
| 7 | Sin rate limit | Alto | 15 min | 🟡 |
| 8 | Sin validación | Medio | 15 min | 🟡 |
| 9 | Sin cache | Medio | 10 min | 🟢 |
| 10 | Sin compresión | Medio | 10 min | 🟢 |

---

## ⏱️ Tiempo Total de Remediación

```
Críticos:  ~1 hora
Altos:     ~1.5 horas
Medios:    ~1 hora
───────────────────
TOTAL:     ~3.5 horas (1/2 día)
```

---

## 🔧 Setup Rápido (Copiar-Pegar)

### requirements.txt Actualizado

```
Flask==3.0.0
Werkzeug==3.0.1
Jinja2==3.1.2
gunicorn==21.2.0
Flask-Limiter==3.5.0
python-dotenv==1.0.0
Sentry-sdk==1.38.0
```

### index.py Mejorado

```python
from flask import Flask, render_template, request
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address
import logging
from logging.handlers import RotatingFileHandler
import os
from dotenv import load_dotenv
import sentry_sdk
from sentry_sdk.integrations.flask import FlaskIntegration

# Cargar variables de entorno
load_dotenv()

# Sentry
sentry_sdk.init(
    dsn=os.getenv('SENTRY_DSN'),
    integrations=[FlaskIntegration()],
    traces_sample_rate=0.1
)

# Flask app
app = Flask(__name__)
limiter = Limiter(
    app=app,
    key_func=get_remote_address,
    default_limits=["200 per day", "50 per hour"]
)

# Logging
if not os.path.exists('logs'):
    os.mkdir('logs')

file_handler = RotatingFileHandler('logs/app.log', maxBytes=10240000, backupCount=10)
file_handler.setFormatter(logging.Formatter(
    '%(asctime)s %(levelname)s: %(message)s'
))
app.logger.addHandler(file_handler)
app.logger.setLevel(logging.INFO)

# Security headers
@app.after_request
def set_security_headers(response):
    response.headers['Content-Security-Policy'] = "default-src 'self'"
    response.headers['X-Content-Type-Options'] = 'nosniff'
    response.headers['X-Frame-Options'] = 'DENY'
    response.headers['Referrer-Policy'] = 'strict-origin-when-cross-origin'
    return response

@app.route('/')
@limiter.limit("100 per hour")
def home():
    app.logger.info('Home accessed')
    return render_template('home.html')

@app.route('/about')
@limiter.limit("100 per hour")
def about():
    app.logger.info('About accessed')
    return render_template('about.html')

if __name__ == '__main__':
    debug = os.getenv('FLASK_ENV') == 'development'
    app.run(debug=debug, host='127.0.0.1', port=5000)
```

### .env (Crear - NO COMMITEAR)

```
FLASK_ENV=development
FLASK_DEBUG=0
SENTRY_DSN=https://your-key@sentry.io/project
```

---

## 🎯 Go/No-Go Decision

### ✅ GO a Producción si:
```
✅ Flask actualizado a 3.0+
✅ DEBUG=False
✅ HTTPS/TLS configurado
✅ CSP headers en respuesta
✅ Logging habilitado
✅ Rate limiting activo
```

### 🔴 NO GO si:
```
❌ Flask 1.1.2 (vieja)
❌ DEBUG=True
❌ Sin HTTPS
❌ Sin CSP headers
```

---

## 📚 Referencias

- [Flask Security](https://flask.palletsprojects.com/en/3.0.x/security/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Let's Encrypt](https://letsencrypt.org/)

---

## 🎓 Conclusión

**Vulnerabilidades:** 10 encontradas  
**Criticidad:** 3 críticas + 4 altas  
**Esfuerzo:** ~3.5 horas (~1/2 día)  

**Recomendación:** ⛔ NO a producción hasta actualizar Flask y agregarseguridad básica.
