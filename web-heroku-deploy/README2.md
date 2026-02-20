El servicio de Heroku dejo de dar soporte Gratuito en nov 2022
se queda proyecto como muestra de trabar con flask 

PythonWeb - Micro servicio Flask (Landing)

Resumen
-------
Proyecto minimalista en Flask que sirve una landing page con rutas `/` (home) y `/about`. Se conserva como ejemplo didáctico (Heroku dejó el plan gratuito en 2022).

Requisitos
----------
- Python 3.8+
- Dependencias: ver `requirements.txt`.

Ejecución (desarrollo)
----------------------
1. Crear entorno virtual e instalar dependencias:

```bash
python -m venv .venv
.\.venv\Scripts\activate    # Windows
pip install -r requirements.txt
```

2. Ejecutar la aplicación:

```bash
python index.py
```

Descripción técnica (memoria)
----------------------------
Arquitectura y stack
- Micro-app basada en Flask (WSGI). La app es de una sola instancia `Flask(__name__)` y usa plantillas Jinja2 en `templates/`.
- Está pensada para despliegue sencillo detrás de un servidor WSGI (Gunicorn/uwsgi) y un proxy inverso (NGINX) en producción.

`index.py` - explicación
- Archivo principal que define la aplicación y las rutas:
	- `/` → renderiza `home.html`.
	- `/about` → renderiza `about.html`.
- En desarrollo `app.run(debug=True)` arranca el servidor local con recarga automática. En producción `debug` debe quedar desactivado y se debe usar un WSGI server.

Estructura relevante
- `index.py` - entrypoint de la app.
- `requirements.txt` - dependencias.
- `templates/` - `home.html`, `about.html`, `layout.html`.
- `static/css/main.css` - estilos.

Recomendaciones de seguridad (por feature)
---------------------------------------

General
- No usar `debug=True` en producción; establecer `FLASK_ENV=production` y ejecutar con WSGI.
- Mantener dependencias actualizadas y auditar con `pip-audit` o `safety`.
- Configurar logging y monitorización (Sentry, Papertrail).

Feature: Rutas públicas (`/`, `/about`)
- Riesgos:
	- Exposición de información sensible si las plantillas contienen datos no filtrados.
- Mitigaciones:
	- No incluir credenciales ni secretos en las plantillas o variables de contexto.
	- Validar y sanear cualquier dato que provenga del usuario antes de renderizar.

Feature: Plantillas Jinja2
- Riesgos:
	- XSS si se insertan datos sin escape o se usa `|safe` imprudentemente.
- Mitigaciones:
	- Dejar el autoescape habilitado (comportamiento por defecto).
	- Evitar `|safe` o usar una librería de sanitización (`bleach`) si es necesario permitir HTML.
	- Aplicar Content Security Policy (CSP) que limite `script-src` y `style-src`.

Feature: Archivos estáticos (CSS)
- Riesgos:
	- Entrega de assets desde orígenes no confiables o con cachés mal configurados.
- Mitigaciones:
	- Servir estáticos desde un servidor seguro/proxy (NGINX, CDN) y establecer `Cache-Control` apropiado.
	- No exponer archivos sensibles en la carpeta `static`.

Feature: Ejecución y despliegue
- Riesgos:
	- `index.py` con `debug=True` puede permitir la ejecución remota de código si está expuesto.
- Mitigaciones:
	- Ejecutar con Gunicorn/uwsgi y un usuario de sistema con mínimos privilegios.
	- Usar HTTPS (TLS) y redirigir HTTP a HTTPS en el proxy.
	- Configurar `ALLOWED_HOSTS` equivalente en el proxy y firewall.

Checklist rápida antes de producción
-----------------------------------
- [ ] Quitar `debug=True` y usar WSGI.
- [ ] Revisar `requirements.txt` y auditoría de dependencias.
- [ ] Configurar TLS y forzar HTTPS.
- [ ] Añadir CSP y cabeceras de seguridad (`X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, `Referrer-Policy`).

Próximos pasos sugeridos
-----------------------
- Añadir `gunicorn` en `requirements.txt` y ejemplo de `Procfile`/systemd service.
- Preparar `Dockerfile` y `docker-compose.yml` para despliegue reproducible.
- Añadir pruebas básicas (pytest + flask test client) y pipeline de CI.

Contacto
--------
Para dudas o añadir despliegue/CI, abre un issue o solicita los siguientes artefactos.

