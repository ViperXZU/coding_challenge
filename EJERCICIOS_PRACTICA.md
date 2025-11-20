# 🏋️ EJERCICIOS DE PRÁCTICA - DJANGO

Practica estos ejercicios para estar preparado. Cada uno simula posibles desafíos de la entrevista.

---

## 📝 EJERCICIO 1: Modelo de Blog Posts (15 min)

### Objetivo
Crear un sistema básico de blog posts donde usuarios pueden publicar artículos.

### Tareas:
1. **Crear modelo Post** con:
   - `title` (CharField, max 200)
   - `content` (TextField)
   - `author` (ForeignKey a User)
   - `created_at` (DateTimeField, auto_now_add)
   - `updated_at` (DateTimeField, auto_now)

2. **Registrar en admin**

3. **Crear vista** que muestre todos los posts ordenados por fecha (más recientes primero)

4. **Crear template** que liste los posts mostrando:
   - Título
   - Autor
   - Fecha de creación
   - Primeros 100 caracteres del contenido

5. **Agregar URL** en `/posts/`

### Código de ayuda:

```python
# models.py
from django.db import models
from django.contrib.auth import get_user_model

User = get_user_model()

class Post(models.Model):
    # Tu código aquí
    pass
```

```python
# views.py
from django.shortcuts import render
from .models import Post

def post_list(request):
    # Tu código aquí
    pass
```

```python
# urls.py
from django.urls import path
from . import views

urlpatterns = [
    # Tu código aquí
]
```

### Verificación:
- [ ] Migrations creadas y aplicadas
- [ ] Modelo visible en admin
- [ ] Puedes crear posts desde admin
- [ ] URL `/posts/` muestra la lista
- [ ] Los posts se ven ordenados correctamente

---

## 📝 EJERCICIO 2: Sistema de Categorías (20 min)

### Objetivo
Extender el ejercicio anterior agregando categorías a los posts.

### Tareas:
1. **Crear modelo Category** con:
   - `name` (CharField, max 50, unique)
   - `description` (TextField, blank=True)

2. **Agregar relación en Post:**
   - `category` (ForeignKey a Category, on_delete=models.SET_NULL, null=True, blank=True)

3. **Vista para filtrar posts por categoría:**
   - URL: `/posts/category/<str:category_name>/`
   - Muestra solo posts de esa categoría

4. **Actualizar template:**
   - Mostrar categoría de cada post
   - Links a la vista de filtro por categoría

### Código de ayuda:

```python
# models.py
class Category(models.Model):
    # Tu código aquí
    
    def __str__(self):
        return self.name
    
    class Meta:
        verbose_name_plural = 'categories'
```

```python
# views.py
def posts_by_category(request, category_name):
    # Tu código aquí
    # Tip: Category.objects.get(name=category_name)
    # Tip: category.post_set.all()
    pass
```

### Verificación:
- [ ] Puedes crear categorías en admin
- [ ] Asignar categoría a un post
- [ ] Filtro por categoría funciona
- [ ] Links a categorías funcionan

---

## 📝 EJERCICIO 3: Crear y Editar Posts (25 min)

### Objetivo
Permitir a usuarios autenticados crear y editar sus propios posts desde el frontend.

### Tareas:
1. **Crear PostForm** usando ModelForm

2. **Vista para crear post** (`@login_required`):
   - GET: Muestra form vacío
   - POST: Guarda post con user actual como author

3. **Vista para editar post** (`@login_required`):
   - Solo el autor puede editar su post
   - Mostrar form con datos actuales
   - Actualizar al hacer POST

4. **Templates:**
   - Form para crear/editar
   - Botón "Editar" en cada post (solo visible para el autor)

5. **URLs:**
   - `/posts/create/`
   - `/posts/<int:pk>/edit/`

### Código de ayuda:

```python
# forms.py
from django import forms
from .models import Post

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'content', 'category']
```

```python
# views.py
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib.auth.decorators import login_required
from .models import Post
from .forms import PostForm

@login_required
def create_post(request):
    if request.method == 'POST':
        # Tu código aquí
        pass
    else:
        # Tu código aquí
        pass
    return render(request, 'core/post_form.html', {'form': form})

@login_required
def edit_post(request, pk):
    post = get_object_or_404(Post, pk=pk)
    
    # Verificar que el usuario es el autor
    if post.author != request.user:
        # Redirigir o error 403
        pass
    
    if request.method == 'POST':
        # Tu código aquí
        pass
    else:
        # Tu código aquí
        pass
    return render(request, 'core/post_form.html', {'form': form, 'post': post})
```

```html
<!-- post_form.html -->
{% extends 'base.html' %}

{% block content %}
<div class="card">
    <h1>{% if post %}Edit Post{% else %}Create Post{% endif %}</h1>
    
    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">Save</button>
    </form>
</div>
{% endblock %}
```

### Verificación:
- [ ] Usuario puede crear post
- [ ] Post se asigna al usuario correcto
- [ ] Solo el autor ve botón "Editar"
- [ ] Edición funciona correctamente
- [ ] Usuarios no autenticados son redirigidos a login

---

## 📝 EJERCICIO 4: Sistema de Likes (20 min)

### Objetivo
Permitir que usuarios den "like" a posts.

### Tareas:
1. **Actualizar modelo Post:**
   - `liked_by = models.ManyToManyField(User, related_name='liked_posts', blank=True)`

2. **Crear vista toggle_like:**
   - Agregar/quitar like al post
   - Redirigir de vuelta a la página anterior

3. **Actualizar template:**
   - Mostrar contador de likes
   - Botón para dar/quitar like
   - Indicar si el usuario actual ya dio like

4. **URL:**
   - `/posts/<int:pk>/like/`

### Código de ayuda:

```python
# models.py
class Post(models.Model):
    # ... campos existentes ...
    liked_by = models.ManyToManyField(
        User, 
        related_name='liked_posts', 
        blank=True
    )
    
    def total_likes(self):
        return self.liked_by.count()
    
    def is_liked_by(self, user):
        return self.liked_by.filter(id=user.id).exists()
```

```python
# views.py
@login_required
def toggle_like(request, pk):
    post = get_object_or_404(Post, pk=pk)
    
    if post.liked_by.filter(id=request.user.id).exists():
        # Quitar like
        post.liked_by.remove(request.user)
    else:
        # Agregar like
        post.liked_by.add(request.user)
    
    # Redirigir de vuelta
    return redirect(request.META.get('HTTP_REFERER', 'core:post_list'))
```

```html
<!-- En template de lista de posts -->
<div>
    <p>❤️ {{ post.total_likes }} likes</p>
    
    {% if user.is_authenticated %}
        <form method="post" action="{% url 'core:toggle_like' post.pk %}" style="display: inline;">
            {% csrf_token %}
            {% if post.is_liked_by user %}
                <button type="submit">Unlike</button>
            {% else %}
                <button type="submit">Like</button>
            {% endif %}
        </form>
    {% endif %}
</div>
```

### Verificación:
- [ ] Usuario puede dar like
- [ ] Usuario puede quitar like
- [ ] Contador se actualiza correctamente
- [ ] Se muestra si el usuario ya dio like

---

## 📝 EJERCICIO 5: Búsqueda de Posts (15 min)

### Objetivo
Implementar búsqueda de posts por título o contenido.

### Tareas:
1. **Actualizar vista post_list:**
   - Aceptar parámetro `q` de búsqueda
   - Filtrar posts que contengan `q` en título o contenido

2. **Agregar formulario de búsqueda en template**

3. **Mostrar mensaje si no hay resultados**

### Código de ayuda:

```python
# views.py
from django.db.models import Q

def post_list(request):
    posts = Post.objects.all()
    query = request.GET.get('q')
    
    if query:
        posts = posts.filter(
            Q(title__icontains=query) | 
            Q(content__icontains=query)
        )
    
    posts = posts.order_by('-created_at')
    
    return render(request, 'core/post_list.html', {
        'posts': posts,
        'query': query
    })
```

```html
<!-- post_list.html -->
<form method="get">
    <input 
        type="text" 
        name="q" 
        value="{{ query }}" 
        placeholder="Search posts..."
    >
    <button type="submit">Search</button>
</form>

{% if query %}
    <p>Results for "{{ query }}"</p>
{% endif %}

{% if posts %}
    <!-- Lista de posts -->
{% else %}
    <p>No posts found.</p>
{% endif %}
```

### Verificación:
- [ ] Búsqueda funciona
- [ ] Encuentra posts por título
- [ ] Encuentra posts por contenido
- [ ] Mensaje cuando no hay resultados

---

## 📝 EJERCICIO 6: Perfil Extendido (15 min)

### Objetivo
Agregar más información al perfil de usuario.

### Tareas:
1. **Extender modelo User con campos:**
   - `bio` (TextField, blank=True)
   - `avatar` (CharField para URL o ImageField si conoces)
   - `website` (URLField, blank=True)
   - `location` (CharField, max 100, blank=True)

2. **Actualizar template de perfil para mostrar nueva info**

3. **Crear vista para editar perfil**

### Código de ayuda:

```python
# models.py
class User(AbstractUser):
    bio = models.TextField(blank=True)
    website = models.URLField(blank=True)
    location = models.CharField(max_length=100, blank=True)
    
    # ... resto del código existente ...
```

```python
# forms.py
class UserProfileForm(forms.ModelForm):
    class Meta:
        model = User
        fields = ['first_name', 'last_name', 'email', 'bio', 'website', 'location']
```

```python
# views.py
@login_required
def edit_profile(request):
    if request.method == 'POST':
        form = UserProfileForm(request.POST, instance=request.user)
        if form.is_valid():
            form.save()
            return redirect('core:profile', username=request.user.username)
    else:
        form = UserProfileForm(instance=request.user)
    
    return render(request, 'core/edit_profile.html', {'form': form})
```

### Verificación:
- [ ] Nuevos campos en modelo
- [ ] Migrations aplicadas
- [ ] Perfil muestra nueva info
- [ ] Usuario puede editar su perfil

---

## 🎯 MINI-CHALLENGES (5-10 min cada uno)

### Challenge A: Contador de Posts por Usuario
Mostrar cuántos posts tiene cada usuario en la lista de perfiles.

**Tip:** Usa anotaciones con `Count` o simplemente `user.post_set.count()`

### Challenge B: Posts Destacados
Agregar campo `is_featured` (BooleanField) a Post y mostrarlos primero.

**Tip:** `.order_by('-is_featured', '-created_at')`

### Challenge C: Últimos 5 Posts en Home
Mostrar los últimos 5 posts en la página principal.

**Tip:** `Post.objects.all()[:5]`

### Challenge D: Paginación
Paginar la lista de posts (10 por página).

**Tip:** Usa `Paginator` de Django

```python
from django.core.paginator import Paginator

def post_list(request):
    posts = Post.objects.all().order_by('-created_at')
    paginator = Paginator(posts, 10)
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    return render(request, 'template.html', {'page_obj': page_obj})
```

### Challenge E: Comentarios
Crear modelo Comment relacionado a Post y User.

---

## 🔄 FLOW DE TRABAJO RECOMENDADO

Para cada ejercicio:

1. **Leer completo** (1 min)
2. **Planificar** - escribir pasos en papel (1 min)
3. **Codear:**
   - Model (3-5 min)
   - Migration (1 min)
   - View (5-7 min)
   - Template (5-7 min)
   - URL (1 min)
4. **Probar** (2-3 min)
5. **Ajustar** si es necesario

---

## ✅ CHECKLIST DESPUÉS DE CADA EJERCICIO

- [ ] Código sin errores de sintaxis
- [ ] Migrations creadas y aplicadas
- [ ] App en INSTALLED_APPS (si es nueva)
- [ ] URLs conectadas correctamente
- [ ] Templates en la carpeta correcta
- [ ] Probado en navegador
- [ ] Commit del cambio

---

## 🚀 SUGERENCIA DE PRÁCTICA

**Esta noche (19 de noviembre):**
- Ejercicio 1 (obligatorio)
- Ejercicio 2 (obligatorio)
- Ejercicio 3 (recomendado)

**Mañana en la mañana (20 de noviembre):**
- Repasar la guía principal
- Hacer Ejercicio 4 o 5 (opcional, si tienes tiempo)
- Revisar los mini-challenges

**No te sobre-exijas:** Mejor entender bien 2-3 ejercicios que hacer todos a medias.

---

## 💡 TIPS FINALES

1. **No memorices código** - entiende la lógica
2. **Usa la guía principal como referencia** durante los ejercicios
3. **Si te atascas:** consulta documentación o usa IA
4. **Commitea cada ejercicio completado**
5. **Práctica el flujo completo:** Model → Migration → View → Template → URL

---

¡Buena suerte con la práctica! 💪
