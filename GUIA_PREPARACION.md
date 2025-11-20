# 🚀 GUÍA DE PREPARACIÓN - ENTREVISTA SYZLAB
**Fecha:** Jueves 20 de noviembre, 11:00 AM  
**Ubicación:** Av. Providencia 1208, oficina 303  
**Duración:** 1 hora (30 min de código)

---

## 📋 CHECKLIST PRE-ENTREVISTA

### ✅ Antes de salir de casa:
- [ ] Laptop cargado + cargador
- [ ] Python instalado (tienes 3.12 ✓)
- [ ] Git instalado y configurado
- [ ] VSCode funcionando
- [ ] Este proyecto clonado y funcionando
- [ ] Conexión a internet (para usar IA)
- [ ] Teléfono de contacto guardado: +56 9 6871 2108

### ✅ 15 minutos antes de la entrevista:
- [ ] Cerrar aplicaciones innecesarias
- [ ] Abrir VSCode con este proyecto
- [ ] Tener terminal lista
- [ ] Servidor Django funcionando: `python manage.py runserver`
- [ ] Navegador en http://127.0.0.1:8000/

---

## 🎯 ESTRUCTURA DEL PROYECTO ACTUAL

```
coding_challenge/           # Carpeta principal del proyecto Django
├── settings.py            # Configuración (DB, apps instaladas, templates)
├── urls.py               # URLs principales (admin + core)
└── wsgi.py               # Servidor WSGI

core/                      # App principal
├── models.py             # User (modelo personalizado)
├── views.py              # home, profile, profiles (3 vistas)
├── urls.py               # Rutas de core
└── admin.py              # Admin de Django

templates/                 # Templates HTML
├── base.html             # Template base con navbar
└── core/
    ├── home.html         # Página principal
    ├── profile.html      # Perfil de usuario
    └── profiles.html     # Lista de usuarios

static/                    # Archivos estáticos (CSS, imágenes)
└── css/base.css

fixtures/                  # Datos de prueba
└── users.json            # 10 usuarios (password: djangoforlife)

db.sqlite3                # Base de datos SQLite
manage.py                 # Comando principal de Django
requirements.txt          # Django==5.2.8
```

---

## 🔑 CONCEPTOS CLAVE DE DJANGO

### 1. **MVT Pattern (Model-View-Template)**
Django usa MVT en lugar de MVC:
- **Model:** Define la estructura de datos (models.py)
- **View:** Lógica de negocio (views.py) - como un "Controller"
- **Template:** Presentación HTML (archivos .html)

### 2. **Models (Modelos)**
```python
from django.db import models

# Ejemplo básico
class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    
    def __str__(self):
        return self.title
```

**Campos comunes:**
- `CharField(max_length=X)` - Texto corto
- `TextField()` - Texto largo
- `IntegerField()` - Números enteros
- `BooleanField()` - True/False
- `DateTimeField()` - Fecha y hora
- `ForeignKey()` - Relación muchos a uno
- `ManyToManyField()` - Relación muchos a muchos
- `EmailField()` - Email validado
- `URLField()` - URL validada

**Parámetros útiles:**
- `null=True` - Permite NULL en BD
- `blank=True` - Permite vacío en forms
- `default=X` - Valor por defecto
- `unique=True` - Valor único
- `auto_now_add=True` - Se setea al crear
- `auto_now=True` - Se actualiza siempre

### 3. **Views (Vistas)**

**Function-Based Views (lo que usa el proyecto):**
```python
from django.shortcuts import render, get_object_or_404
from django.contrib.auth.decorators import login_required

def mi_vista(request):
    # Lógica aquí
    context = {'variable': 'valor'}
    return render(request, 'template.html', context)

@login_required  # Requiere autenticación
def vista_protegida(request):
    return render(request, 'protegida.html')
```

**Class-Based Views (alternativa):**
```python
from django.views.generic import ListView, DetailView

class PostListView(ListView):
    model = Post
    template_name = 'posts/list.html'
    context_object_name = 'posts'
```

### 4. **URLs (Enrutamiento)**
```python
# coding_challenge/urls.py (principal)
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('core.urls')),  # Incluye urls de core
]

# core/urls.py (app)
from django.urls import path
from . import views

app_name = 'core'  # Namespace

urlpatterns = [
    path('', views.home, name='home'),
    path('posts/', views.post_list, name='post_list'),
    path('post/<int:pk>/', views.post_detail, name='post_detail'),
]
```

**En templates:**
```html
<a href="{% url 'core:home' %}">Home</a>
<a href="{% url 'core:post_detail' pk=post.id %}">Ver</a>
```

### 5. **Templates**

**Herencia:**
```html
<!-- base.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}Default{% endblock %}</title>
</head>
<body>
    {% block content %}{% endblock %}
</body>
</html>

<!-- hijo.html -->
{% extends 'base.html' %}
{% block title %}Mi Página{% endblock %}
{% block content %}
    <h1>Contenido aquí</h1>
{% endblock %}
```

**Tags importantes:**
```django
{% if user.is_authenticated %}
    Bienvenido {{ user.username }}
{% else %}
    Por favor inicia sesión
{% endif %}

{% for item in items %}
    <li>{{ item.name }}</li>
{% empty %}
    <li>No hay items</li>
{% endfor %}

{% url 'app:view_name' arg1=value1 %}

{% load static %}
<img src="{% static 'images/logo.png' %}">
```

**Filtros:**
```django
{{ variable|default:"N/A" }}
{{ text|truncatewords:30 }}
{{ date|date:"d/m/Y" }}
{{ number|floatformat:2 }}
{{ text|lower }}
{{ text|upper }}
```

### 6. **ORM (Consultas a la BD)**

```python
# Obtener todos
posts = Post.objects.all()

# Filtrar
posts = Post.objects.filter(author=user)
posts = Post.objects.filter(title__contains='Django')
posts = Post.objects.filter(created_at__year=2025)

# Obtener uno
post = Post.objects.get(id=1)
post = get_object_or_404(Post, id=1)  # Mejor opción

# Crear
post = Post.objects.create(title='Nuevo', content='...')

# Actualizar
post.title = 'Modificado'
post.save()

# Eliminar
post.delete()

# Ordenar
posts = Post.objects.all().order_by('-created_at')  # - es descendente

# Contar
count = Post.objects.count()

# Existe
exists = Post.objects.filter(title='Test').exists()

# Relaciones
user.post_set.all()  # Todos los posts del usuario
post.author  # Usuario del post
```

### 7. **Forms (Formularios)**

```python
# forms.py
from django import forms
from .models import Post

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'content']
        # o exclude = ['author']

# views.py
def create_post(request):
    if request.method == 'POST':
        form = PostForm(request.POST)
        if form.is_valid():
            post = form.save(commit=False)
            post.author = request.user
            post.save()
            return redirect('core:home')
    else:
        form = PostForm()
    return render(request, 'form.html', {'form': form})
```

```html
<!-- template -->
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Guardar</button>
</form>
```

### 8. **Admin de Django**

```python
# admin.py
from django.contrib import admin
from .models import Post

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'author', 'created_at']
    list_filter = ['created_at', 'author']
    search_fields = ['title', 'content']
```

---

## ⚡ COMANDOS DJANGO ESENCIALES

### Servidor y desarrollo
```bash
python manage.py runserver              # Iniciar servidor
python manage.py runserver 8080         # En otro puerto
```

### Base de datos
```bash
python manage.py makemigrations         # Crear migraciones
python manage.py migrate                # Aplicar migraciones
python manage.py showmigrations         # Ver estado de migraciones
python manage.py sqlmigrate core 0001   # Ver SQL de una migración
```

### Superusuario
```bash
python manage.py createsuperuser        # Crear admin
```

### Shell interactivo
```bash
python manage.py shell                  # Python shell con Django
# Dentro del shell:
from core.models import User
users = User.objects.all()
```

### Datos de prueba
```bash
python manage.py loaddata fixtures/users.json    # Cargar fixtures
python manage.py dumpdata core.User > users.json # Exportar datos
```

### Crear app
```bash
python manage.py startapp nombre_app    # Crear nueva app
```

---

## 🎓 USUARIOS DE PRUEBA

**Todos tienen password:** `djangoforlife`

Usuarios disponibles:
- **syzlab** (superuser)
- peter
- sara
- mary
- zack
- britney
- charlie
- javiera
- shane
- tiffany

---

## 💡 TIPS PARA LA ENTREVISTA

### Durante los 30 minutos de código:

1. **Lee el enunciado completo primero** (2-3 min)
   - Identifica qué te piden
   - Pregunta si algo no está claro

2. **Planifica antes de codear** (2-3 min)
   - Modelo(s) necesario(s)
   - Vista(s) necesaria(s)
   - Template(s) necesario(s)
   - URL(s) necesaria(s)

3. **Estructura sugerida de trabajo:**
   - ✅ Models primero (5-7 min)
   - ✅ Migrations y verificar BD (2 min)
   - ✅ Views (8-10 min)
   - ✅ URLs (2 min)
   - ✅ Templates (8-10 min)
   - ✅ Testing manual (2-3 min)

4. **Buenas prácticas:**
   - Commits pequeños y frecuentes
   - Nombres descriptivos para variables/funciones
   - Comentarios en partes complejas
   - No te estreses por terminar todo
   - Código limpio > código completo

5. **Si te atascas:**
   - Usa IA (GitHub Copilot, ChatGPT, etc.)
   - Consulta con ellos (es permitido)
   - Explica tu razonamiento en voz alta

6. **Errores comunes a evitar:**
   - Olvidar `{% csrf_token %}` en forms
   - No hacer migrations después de cambiar models
   - Olvidar agregar app en `INSTALLED_APPS`
   - No usar `@login_required` cuando se necesita
   - Olvidar el namespace en URLs: `{% url 'core:home' %}`

---

## 🔧 ESCENARIOS PROBABLES

### Escenario 1: CRUD básico
**Ejemplo:** "Crear un blog con posts"

**Pasos:**
1. Modelo Post con campos (title, content, author, created_at)
2. Vista para listar posts
3. Vista para ver detalle de post
4. Vista para crear post (con form)
5. Template para cada vista

### Escenario 2: Relaciones entre modelos
**Ejemplo:** "Agregar categorías a los posts"

**Pasos:**
1. Modelo Category
2. ForeignKey en Post hacia Category
3. Filtrar posts por categoría
4. Mostrar categoría en templates

### Escenario 3: Funcionalidad a usuarios existentes
**Ejemplo:** "Permitir que usuarios tengan bio"

**Pasos:**
1. Agregar campo `bio = models.TextField(blank=True)` a User
2. Migración
3. Actualizar template de profile para mostrar bio
4. (Opcional) Form para editar bio

### Escenario 4: Sistema de likes/favoritos
**Ejemplo:** "Usuarios pueden dar like a posts"

**Pasos:**
1. ManyToManyField en Post: `liked_by = models.ManyToManyField(User)`
2. Vista para toggle like
3. Mostrar contador en template
4. Botón de like/unlike

---

## 🚨 TROUBLESHOOTING RÁPIDO

### Error: "Table doesn't exist"
```bash
python manage.py makemigrations
python manage.py migrate
```

### Error: "CSRF token missing"
```html
<!-- Agregar en form -->
{% csrf_token %}
```

### Error: "App not found"
```python
# settings.py - INSTALLED_APPS
INSTALLED_APPS = [
    ...
    'tu_nueva_app',  # ← Agregar aquí
]
```

### Error al importar modelo
```python
# Importación correcta
from core.models import User, TuModelo
```

### Ver qué usuario está logueado
```python
# En template
{{ user }}
{{ user.username }}
{{ user.is_authenticated }}

# En view
print(request.user)
```

---

## 📚 RECURSOS DE CONSULTA RÁPIDA

Durante la entrevista puedes consultar:
- Documentación oficial: https://docs.djangoproject.com/
- Este documento
- IA (ChatGPT, Copilot, etc.)

**Búsquedas útiles:**
- "django model field types"
- "django queryset methods"
- "django template tags"
- "django form example"

---

## ✨ ESTRUCTURA MENTAL - FLUJO DE DJANGO

```
1. Usuario hace request a URL
   ↓
2. urls.py encuentra la ruta y llama a la view
   ↓
3. View procesa la request:
   - Consulta modelos (DB)
   - Procesa lógica
   - Prepara contexto
   ↓
4. View renderiza template con contexto
   ↓
5. Template genera HTML
   ↓
6. Response se envía al usuario
```

---

## 🎯 RECUERDA

- ✅ **Puedes usar IA** - úsala sin miedo
- ✅ **No tienes que terminar** - enfócate en calidad
- ✅ **Puedes dejar errores** - explícalos después
- ✅ **Consulta con ellos** - están ahí para ayudar
- ✅ **Piensa en voz alta** - muestra tu proceso mental
- ✅ **Clean code** - mejor que código rápido y sucio

---

## 📞 CONTACTO DE EMERGENCIA

**Si tienes problemas para llegar:**  
+56 9 6871 2108

---

## ⏰ TIMELINE DEL DÍA

- **10:30 AM** - Salir con tiempo
- **10:45 AM** - Llegar a Av. Providencia 1208
- **11:00 AM** - Inicio de entrevista
- **11:00-11:30** - Desafío de código (30 min)
- **11:30-12:00** - Explicación y preguntas
- **12:00 PM** - Fin de entrevista

---

## 🎓 ÚLTIMO CONSEJO

**Respira, confía en ti y recuerda:**  
No buscan perfección, buscan ver cómo piensas, cómo resuelves problemas y cómo te comunicas. ¡Ya pasaste la primera fase, lo estás haciendo bien!

---

# ¡MUCHA SUERTE! 🍀

**Te veo del otro lado con buenas noticias.** 💪
