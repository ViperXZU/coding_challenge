# ⚡ DJANGO CHEAT SHEET - REFERENCIA RÁPIDA

Esta es tu hoja de referencia rápida durante la entrevista. ¡Imprimela o tenla en otra ventana!

---

## 🎯 WORKFLOW BÁSICO

```
1. Crear/Modificar Model → 2. makemigrations → 3. migrate → 
4. Crear View → 5. Crear Template → 6. Conectar URL → 7. Probar
```

---

## 📦 MODELS - Campos Comunes

```python
from django.db import models
from django.contrib.auth import get_user_model

User = get_user_model()

class MiModelo(models.Model):
    # Texto
    titulo = models.CharField(max_length=200)
    descripcion = models.TextField(blank=True)
    slug = models.SlugField(unique=True)
    email = models.EmailField()
    url = models.URLField(blank=True)
    
    # Números
    edad = models.IntegerField(default=0)
    precio = models.DecimalField(max_digits=10, decimal_places=2)
    rating = models.FloatField()
    
    # Booleano
    activo = models.BooleanField(default=True)
    
    # Fechas
    creado = models.DateTimeField(auto_now_add=True)
    actualizado = models.DateTimeField(auto_now=True)
    fecha_publicacion = models.DateField(null=True, blank=True)
    
    # Relaciones
    autor = models.ForeignKey(User, on_delete=models.CASCADE)
    tags = models.ManyToManyField('Tag', blank=True)
    
    class Meta:
        ordering = ['-creado']
        verbose_name = 'Mi Modelo'
        verbose_name_plural = 'Mis Modelos'
    
    def __str__(self):
        return self.titulo
```

### on_delete Options:
- `CASCADE` - Elimina en cascada
- `SET_NULL` - Setea a NULL (requiere null=True)
- `SET_DEFAULT` - Setea valor default
- `PROTECT` - Evita eliminación
- `DO_NOTHING` - No hace nada

---

## 👁️ VIEWS - Patterns

### Function-Based View Simple
```python
from django.shortcuts import render

def mi_vista(request):
    context = {'variable': 'valor'}
    return render(request, 'app/template.html', context)
```

### Vista con Query
```python
from django.shortcuts import render
from .models import Post

def lista_posts(request):
    posts = Post.objects.all().order_by('-created_at')
    return render(request, 'posts/lista.html', {'posts': posts})
```

### Vista de Detalle
```python
from django.shortcuts import render, get_object_or_404

def detalle_post(request, pk):
    post = get_object_or_404(Post, pk=pk)
    return render(request, 'posts/detalle.html', {'post': post})
```

### Vista con Login Required
```python
from django.contrib.auth.decorators import login_required

@login_required
def vista_protegida(request):
    return render(request, 'protegida.html')
```

### Vista con Form (GET/POST)
```python
from django.shortcuts import render, redirect
from .forms import MiForm

def crear(request):
    if request.method == 'POST':
        form = MiForm(request.POST)
        if form.is_valid():
            obj = form.save(commit=False)
            obj.autor = request.user
            obj.save()
            return redirect('app:lista')
    else:
        form = MiForm()
    return render(request, 'form.html', {'form': form})
```

### Vista de Edición
```python
def editar(request, pk):
    obj = get_object_or_404(MiModelo, pk=pk)
    
    if request.method == 'POST':
        form = MiForm(request.POST, instance=obj)
        if form.is_valid():
            form.save()
            return redirect('app:detalle', pk=obj.pk)
    else:
        form = MiForm(instance=obj)
    
    return render(request, 'form.html', {'form': form, 'obj': obj})
```

---

## 🔍 ORM - Queries

### Básicas
```python
# Todas
Model.objects.all()

# Filtrar
Model.objects.filter(campo=valor)
Model.objects.filter(campo__contains='texto')
Model.objects.filter(campo__icontains='texto')  # case-insensitive
Model.objects.filter(numero__gt=10)  # greater than
Model.objects.filter(numero__gte=10)  # greater than or equal
Model.objects.filter(numero__lt=10)  # less than
Model.objects.filter(fecha__year=2025)

# Excluir
Model.objects.exclude(campo=valor)

# Obtener uno
Model.objects.get(id=1)
get_object_or_404(Model, id=1)  # Mejor opción

# Primer/último
Model.objects.first()
Model.objects.last()

# Existe
Model.objects.filter(campo=valor).exists()  # True/False

# Contar
Model.objects.count()
Model.objects.filter(activo=True).count()
```

### Ordenar
```python
Model.objects.order_by('campo')  # ascendente
Model.objects.order_by('-campo')  # descendente
Model.objects.order_by('-created_at', 'titulo')  # múltiples
```

### Limitar
```python
Model.objects.all()[:5]  # primeros 5
Model.objects.all()[5:10]  # 6-10
```

### Crear/Actualizar/Eliminar
```python
# Crear
obj = Model.objects.create(campo='valor')

# O
obj = Model(campo='valor')
obj.save()

# Actualizar
obj.campo = 'nuevo valor'
obj.save()

# Eliminar
obj.delete()
```

### Búsqueda con Q (OR)
```python
from django.db.models import Q

Model.objects.filter(
    Q(titulo__icontains='django') | 
    Q(descripcion__icontains='django')
)
```

### Relaciones
```python
# ForeignKey - acceder
post.autor  # User object
post.autor.username

# Reverse - acceder posts de un user
user.post_set.all()
# o si usaste related_name='posts'
user.posts.all()

# ManyToMany
post.tags.all()
post.tags.add(tag)
post.tags.remove(tag)
post.tags.clear()
```

---

## 🛣️ URLS

### urls.py Principal
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('core.urls')),
    path('blog/', include('blog.urls')),
]
```

### urls.py de App
```python
from django.urls import path
from . import views

app_name = 'blog'  # namespace

urlpatterns = [
    path('', views.lista, name='lista'),
    path('<int:pk>/', views.detalle, name='detalle'),
    path('create/', views.crear, name='crear'),
    path('<int:pk>/edit/', views.editar, name='editar'),
    path('<str:slug>/', views.por_slug, name='por_slug'),
]
```

---

## 📄 TEMPLATES

### Herencia
```html
<!-- base.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}Default{% endblock %}</title>
</head>
<body>
    <nav>{% block nav %}{% endblock %}</nav>
    <main>{% block content %}{% endblock %}</main>
</body>
</html>

<!-- hijo.html -->
{% extends 'base.html' %}
{% block title %}Mi Página{% endblock %}
{% block content %}
    <h1>Contenido</h1>
{% endblock %}
```

### Variables
```django
{{ variable }}
{{ objeto.atributo }}
{{ objeto.metodo }}
{{ dict.key }}
{{ lista.0 }}
```

### Filtros
```django
{{ texto|default:"N/A" }}
{{ texto|truncatewords:30 }}
{{ texto|lower }}
{{ texto|upper }}
{{ texto|title }}
{{ numero|floatformat:2 }}
{{ fecha|date:"d/m/Y" }}
{{ lista|length }}
{{ texto|safe }}  # No escapar HTML
```

### Tags - If
```django
{% if user.is_authenticated %}
    Hola {{ user.username }}
{% else %}
    Inicia sesión
{% endif %}

{% if x > 5 and y < 10 %}
    ...
{% endif %}
```

### Tags - For
```django
{% for item in lista %}
    <li>{{ item.nombre }}</li>
{% empty %}
    <li>No hay items</li>
{% endfor %}

{% for item in lista %}
    {{ forloop.counter }}  <!-- 1, 2, 3... -->
    {{ forloop.counter0 }} <!-- 0, 1, 2... -->
    {{ forloop.first }}    <!-- True en primero -->
    {{ forloop.last }}     <!-- True en último -->
{% endfor %}
```

### URLs en Templates
```django
<a href="{% url 'app:vista' %}">Link</a>
<a href="{% url 'app:detalle' pk=objeto.pk %}">Ver</a>
<a href="{% url 'app:editar' objeto.id %}">Editar</a>
```

### Static Files
```django
{% load static %}
<link rel="stylesheet" href="{% static 'css/style.css' %}">
<img src="{% static 'images/logo.png' %}">
```

### Comentarios
```django
{# Comentario de una línea #}

{% comment %}
Comentario
de múltiples
líneas
{% endcomment %}
```

---

## 📝 FORMS

### ModelForm
```python
from django import forms
from .models import Post

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['titulo', 'contenido', 'categoria']
        # o
        exclude = ['autor', 'created_at']
        
        widgets = {
            'contenido': forms.Textarea(attrs={'rows': 4}),
        }
        
        labels = {
            'titulo': 'Título del Post',
        }
```

### Form Regular
```python
class ContactForm(forms.Form):
    nombre = forms.CharField(max_length=100)
    email = forms.EmailField()
    mensaje = forms.CharField(widget=forms.Textarea)
```

### En Template
```html
<form method="post">
    {% csrf_token %}
    
    <!-- Opción 1: Automático -->
    {{ form.as_p }}
    
    <!-- Opción 2: Manual -->
    {% for field in form %}
        <div>
            {{ field.label_tag }}
            {{ field }}
            {% if field.errors %}
                <span>{{ field.errors }}</span>
            {% endif %}
        </div>
    {% endfor %}
    
    <button type="submit">Enviar</button>
</form>
```

---

## 🔧 COMANDOS TERMINAL

```bash
# Servidor
python manage.py runserver
python manage.py runserver 8080

# Migraciones
python manage.py makemigrations
python manage.py migrate
python manage.py showmigrations

# Superuser
python manage.py createsuperuser

# Shell
python manage.py shell

# Fixtures
python manage.py loaddata archivo.json
python manage.py dumpdata app.Model > archivo.json

# App
python manage.py startapp nombre
```

---

## ⚙️ ADMIN

```python
from django.contrib import admin
from .models import Post

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['titulo', 'autor', 'created_at', 'activo']
    list_filter = ['created_at', 'activo', 'categoria']
    search_fields = ['titulo', 'contenido']
    date_hierarchy = 'created_at'
    ordering = ['-created_at']
    
    # Campos de solo lectura
    readonly_fields = ['created_at', 'updated_at']
```

---

## 🚨 ERRORES COMUNES

### "No such table"
```bash
python manage.py makemigrations
python manage.py migrate
```

### "CSRF token missing"
```html
<!-- Agregar en form -->
{% csrf_token %}
```

### "App not in INSTALLED_APPS"
```python
# settings.py
INSTALLED_APPS = [
    ...
    'tu_app',  # ← Agregar
]
```

### "Template does not exist"
```
Verificar:
1. Carpeta templates/ en lugar correcto
2. Nombre correcto del archivo
3. TEMPLATES en settings.py configurado
```

### "ImportError: No module named..."
```python
# Importación correcta
from .models import MiModelo  # desde mismo app
from otra_app.models import Modelo  # desde otro app
```

---

## ✅ CHECKLIST ANTES DE TESTEAR

- [ ] Modelo creado/modificado
- [ ] `makemigrations` ejecutado
- [ ] `migrate` ejecutado
- [ ] Vista creada
- [ ] Template creado en carpeta correcta
- [ ] URL conectada
- [ ] App en INSTALLED_APPS (si es nueva)
- [ ] Imports correctos
- [ ] Sin errores de sintaxis

---

## 🎯 PATRON COMÚN: CRUD COMPLETO

```python
# models.py
class Item(models.Model):
    nombre = models.CharField(max_length=200)
    descripcion = models.TextField()
    creado = models.DateTimeField(auto_now_add=True)

# views.py
def lista(request):
    items = Item.objects.all()
    return render(request, 'app/lista.html', {'items': items})

def detalle(request, pk):
    item = get_object_or_404(Item, pk=pk)
    return render(request, 'app/detalle.html', {'item': item})

@login_required
def crear(request):
    if request.method == 'POST':
        form = ItemForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('app:lista')
    else:
        form = ItemForm()
    return render(request, 'app/form.html', {'form': form})

@login_required
def editar(request, pk):
    item = get_object_or_404(Item, pk=pk)
    if request.method == 'POST':
        form = ItemForm(request.POST, instance=item)
        if form.is_valid():
            form.save()
            return redirect('app:detalle', pk=pk)
    else:
        form = ItemForm(instance=item)
    return render(request, 'app/form.html', {'form': form})

@login_required
def eliminar(request, pk):
    item = get_object_or_404(Item, pk=pk)
    if request.method == 'POST':
        item.delete()
        return redirect('app:lista')
    return render(request, 'app/confirmar.html', {'item': item})

# urls.py
urlpatterns = [
    path('', views.lista, name='lista'),
    path('<int:pk>/', views.detalle, name='detalle'),
    path('create/', views.crear, name='crear'),
    path('<int:pk>/edit/', views.editar, name='editar'),
    path('<int:pk>/delete/', views.eliminar, name='eliminar'),
]
```

---

## 💡 TIPS RÁPIDOS

1. **Usa `get_object_or_404`** en lugar de `.get()` - mejor manejo de errores
2. **Siempre `{% csrf_token %}`** en forms POST
3. **`related_name`** hace queries inversas más claras
4. **`blank=True`** para forms, `null=True` para BD
5. **`auto_now_add`** setea una vez, `auto_now` actualiza siempre
6. **Namespace URLs** con `app_name` para evitar conflictos
7. **`order_by('-campo')`** el `-` es descendente

---

¡Buena suerte! 🍀
