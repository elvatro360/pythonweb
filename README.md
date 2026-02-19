# 🌐 PythonWeb: Tu Primer Sitio Web

> Aprende a crear sitios web con Python y Flask

---

## 📑 Índice Rápido

- [¿Qué es Flask?](#qué-es-flask)
- [Cómo Funciona](#cómo-funciona)
- [Instala y Juega](#instala-y-juega)
- [Aprende Haciendo](#aprende-haciendo)
- [Desafíos](#desafíos)
- [Próximos Pasos](#próximos-pasos)

---

## ❓ ¿Qué es Flask?

### La Idea Sencilla

```
Flask es como LEGO para crear sitios web:

LEGO:
├─ Tienes piezas (bloques)
├─ Las juntas como quieres
└─ Construyes algo único

Flask:
├─ Tienes componentes (rutas, plantillas)
├─ Los juntas en tu código
└─ Construyes un sitio web
```

### ¿Por Qué Aprender Flask?

```
✅ Simple: Código limpio y fácil de entender
✅ Flexible: Haces lo que quieras
✅ Python: Lenguaje popular y poderoso
✅ Divertido: Ves resultados rápido
✅ Profesional: Usado en empresas reales
```

---

## 🔧 Cómo Funciona

### Flujo Básico

```
Usuario → Browser → Solicitud → Flask → Respuesta → Browser → Pantalla

Ejemplo:
1. Escribes: http://localhost:5000
2. Browser envía solicitud
3. Flask recibe: "¿Home?"
4. Flask procesa
5. Flask devuelve: página HTML
6. Browser muestra la página
```

### Componentes Principales

```
Flask tiene 3 partes principales:

1. RUTAS (@app.route)
   └─ "Si alguien va a /hola, muestra..."

2. FUNCIONES
   └─ "Aquí va el código que hace algo"

3. PLANTILLAS (HTML)
   └─ "Aquí va lo que ves en el navegador"
```

---

## 🚀 Instala y Juega

### Paso 1: Instalar Python
```bash
Descarga desde: python.org
Verifica: python --version
```

### Paso 2: Instalar Flask
```bash
pip install flask
```

### Paso 3: Tu Primera App

Crea archivo `app.py`:
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hola():
    return "<h1>¡Hola, Mundo! 👋</h1>"

if __name__ == '__main__':
    app.run(debug=True)
```

### Paso 4: Ejecutar
```bash
python app.py

# Verás:
# Running on http://127.0.0.1:5000
# Abre en tu navegador y ¡voilà!
```

---

## 🎨 Aprende Haciendo

### Proyecto 1: Página Personal (30 minutos)

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def inicio():
    return '''
    <h1>Mi Página Personal</h1>
    <p>Hola, soy un estudiante aprendiendo Flask</p>
    <a href="/sobre-mi">Sobre mí</a>
    '''

@app.route('/sobre-mi')
def sobre_mi():
    return '''
    <h1>Sobre Mí</h1>
    <p>Me encanta programar 💻</p>
    <a href="/">Volver</a>
    '''

if __name__ == '__main__':
    app.run(debug=True)
```

**Qué aprendiste:**
- Crear rutas múltiples
- Devolver HTML desde funciones
- Navegar entre páginas

---

### Proyecto 2: Formulario Simple (45 minutos)

```python
from flask import Flask, request

app = Flask(__name__)

@app.route('/')
def formulario():
    return '''
    <h1>¿Cuál es tu nombre?</h1>
    <form method="POST" action="/saludar">
        <input type="text" name="nombre" placeholder="Tu nombre">
        <button type="submit">Enviar</button>
    </form>
    '''

@app.route('/saludar', methods=['POST'])
def saludar():
    nombre = request.form['nombre']
    return f'''
    <h1>¡Hola, {nombre}! 👋</h1>
    <a href="/">Volver</a>
    '''

if __name__ == '__main__':
    app.run(debug=True)
```

**Qué aprendiste:**
- Procesar formularios
- Recibir datos del usuario
- Usar esos datos en respuestas

---

### Proyecto 3: Contador Dinámico (1 hora)

```python
from flask import Flask, session
from datetime import timedelta

app = Flask(__name__)
app.secret_key = 'mi-clave-secreta'
app.permanent_session_lifetime = timedelta(minutes=5)

@app.route('/')
def inicio():
    if 'contador' not in session:
        session['contador'] = 0
    
    return f'''
    <h1>Mi Contador</h1>
    <p>Visitaste {session['contador']} veces</p>
    <a href="/incrementar">Incrementar</a>
    '''

@app.route('/incrementar')
def incrementar():
    session['contador'] = session.get('contador', 0) + 1
    return f'''
    <p>Ahora: {session['contador']}</p>
    <a href="/">Volver</a>
    '''

if __name__ == '__main__':
    app.run(debug=True)
```

**Qué aprendiste:**
- Usar sesiones (memoria del usuario)
- Números que cambian
- Persistencia de datos

---

## 🎯 Desafíos

### Fácil ⭐
```
1. Crea una página con 3 rutas
2. Agrega CSS con <style>
3. Usa colores y fuentes diferentes
```

### Medio ⭐⭐
```
1. Crea formulario de registro
2. Guarda nombre en sesión
3. Muestra "Bienvenido, {nombre}"
```

### Difícil ⭐⭐⭐
```
1. Crea lista de tareas
2. Agregar tareas con formulario
3. Marcar como completadas
4. Guarda en diccionario (pseudo-BD)
```

---

## 📚 Conceptos Importantes

### Ruta
```python
@app.route('/ruta')
# La URL en tu navegador será: http://localhost:5000/ruta
```

### Método GET vs POST
```python
# GET: Para obtener información (default)
# POST: Para enviar información (formularios)

@app.route('/formulario', methods=['POST'])
```

### Sesión
```python
# Guarda información del usuario (mientras está en el sitio)
session['nombre'] = 'Juan'
# Recupera
nombre = session.get('nombre')
```

### Request
```python
# Obtiene datos del formulario
nombre = request.form['nombre']

# O de la URL
@app.route('/usuario/<nombre>')
def usuario(nombre):
    return f"Hola {nombre}"
```

---

## 🏁 Siguientes Pasos

### Mejoras que Puedes Hacer
```
1. Estilo CSS profesional
   → Crea archivo static/style.css
   
2. Guardad datos permanentemente
   → Aprende SQLite (como en CRUD)
   
3. Plantillas reutilizables
   → Crea carpeta templates/
   → Usa {% %} para variables
   
4. Despliegue en línea
   → Heroku, Vercel, PythonAnywhere
```

### Próximos Frameworks
```
Si quieres algo más potente:

Django (más grande):
  ✓ Más características
  ✓ Más documentación
  ✓ Proyectos grandes

FastAPI (más moderno):
  ✓ Super rápido
  ✓ APIs profesionales
```

---

## 📖 Aprende Más

### Documentación Oficial
- 📖 [Flask Oficial](https://flask.palletsprojects.com/)
- 📖 [Tutoriales Oficiales](https://flask.palletsprojects.com/tutorial/)

### Cursos Online
- 🎥 "Flask por Miguel Grinberg"
- 🎥 "Web Development with Flask"
- 🎥 "Build Web Apps with Flask"

### Practica
- 🎮 Haz 5 proyectos propios
- 🎮 Comparte con amigos
- 🎮 Pide retroalimentación

---

## 🎓 Checklist de Aprendizaje

- [ ] Instalé Flask correctamente
- [ ] Ejecuté "Hola Mundo"
- [ ] Creé múltiples rutas
- [ ] Usé formularios
- [ ] Procesé datos del usuario
- [ ] Usé sesiones
- [ ] Hice un proyecto original

**Si marcaste todo ✓ → ¡Ya sabes Flask! 🎉**

---

## ❓ Preguntas Frecuentes

### ¿Flask vs Django?
```
Flask:  Pequeño, flexible, fácil de aprender
Django: Grande, "todo incluido", para proyectos enormes

Para empezar: Flask ✓
Para empresa: Django ✓
```

### ¿Cómo guardo datos permanentemente?
```
Con Flask solo: sesiones (desaparece al cerrar)
Para permanente: necesitas base de datos (SQLite, MySQL, etc)
Mira el proyecto CRUD →
```

### ¿Cómo pongo mi sitio en internet?
```
Opciones:
1. Heroku (fue gratis, ahora es pago)
2. PythonAnywhere (easy y gratis)
3. AWS (profesional)
4. Vercel (moderno)
```

### ¿Flask es seguro?
```
Flask es seguro si lo usas bien:
✓ Valida siempre la entrada
✓ Usa contraseñas en secretos
✓ No expongas tokens
✓ Mantén dependencias actualizadas
```

---

<div align="center">

## ✅ ¡Felicidades!

### Ya aprendiste Flask

**Siguiente proyecto:**

[🏠 Landing Page](../landingPage/README.md) - Agrega formularios

---

*Tutorial amigable | Sin códigos complicados | Práctico*

</div>
