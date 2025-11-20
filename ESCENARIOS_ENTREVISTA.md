# 🎭 ESCENARIOS DE ENTREVISTA SIMULADOS

Estos son escenarios reales que podrían darte en la entrevista. Practica con cronómetro.

---

## 📝 ESCENARIO 1: Sistema de Comentarios (30 min)

### Enunciado
"Queremos que los usuarios puedan comentar en los perfiles de otros usuarios. Implementa un sistema de comentarios donde:
- Cualquier usuario autenticado puede dejar un comentario en un perfil
- Los comentarios muestran el autor y la fecha
- Los comentarios aparecen en el perfil del usuario, ordenados por más reciente primero"

### Checklist de implementación:
- [ ] Modelo `Comment` con campos: content, author (FK a User), profile_user (FK a User), created_at
- [ ] Migración creada y aplicada
- [ ] Form `CommentForm` con solo campo `content`
- [ ] Vista en `profile` que maneje GET (mostrar form + comments) y POST (crear comment)
- [ ] Template actualizado mostrando form y lista de comentarios
- [ ] Solo usuarios autenticados pueden comentar

### Solución rápida:
```python
# models.py
class Comment(models.Model):
    content = models.TextField()
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='comments_made')
    profile_user = models.ForeignKey(User, on_delete=models.CASCADE, related_name='comments_received')
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        ordering = ['-created_at']
    
    def __str__(self):
        return f"Comment by {self.author.username} on {self.profile_user.username}"

# forms.py
from django import forms
from .models import Comment

class CommentForm(forms.ModelForm):
    class Meta:
        model = Comment
        fields = ['content']
        widgets = {
            'content': forms.Textarea(attrs={'rows': 3, 'placeholder': 'Write a comment...'})
        }

# views.py (modificar vista profile existente)
from .forms import CommentForm
from .models import Comment

@login_required
def profile(request, username):
    user = get_object_or_404(User, username=username)
    comments = user.comments_received.all()
    
    if request.method == 'POST':
        form = CommentForm(request.POST)
        if form.is_valid():
            comment = form.save(commit=False)
            comment.author = request.user
            comment.profile_user = user
            comment.save()
            return redirect('core:profile', username=username)
    else:
        form = CommentForm()
    
    return render(request, 'core/profile.html', {
        'user': user,
        'is_own_profile': request.user.username == username,
        'comments': comments,
        'form': form
    })

# template profile.html (agregar)
{% if user.is_authenticated %}
<div class="comments-section">
    <h3>Leave a comment</h3>
    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">Post Comment</button>
    </form>
</div>
{% endif %}

<div class="comments-list">
    <h3>Comments ({{ comments.count }})</h3>
    {% for comment in comments %}
    <div class="comment">
        <p><strong>{{ comment.author.username }}</strong> - {{ comment.created_at|date:"d/m/Y H:i" }}</p>
        <p>{{ comment.content }}</p>
    </div>
    {% empty %}
    <p>No comments yet.</p>
    {% endfor %}
</div>
```

---

## 📝 ESCENARIO 2: Sistema de Seguimiento (30 min)

### Enunciado
"Implementa un sistema donde usuarios puedan seguirse entre sí:
- Un usuario puede seguir a otro
- Un usuario puede dejar de seguir
- En el perfil debe mostrarse un botón 'Follow/Unfollow'
- Mostrar contador de seguidores y seguidos"

### Checklist de implementación:
- [ ] Agregar campo `following` (ManyToManyField) al modelo User
- [ ] Migración
- [ ] Vista `toggle_follow` que agregue/quite el follow
- [ ] URL para `/profile/<username>/follow/`
- [ ] Botón en template de profile
- [ ] Métodos en modelo para contar followers/following

### Solución rápida:
```python
# models.py (modificar User)
class User(AbstractUser):
    following = models.ManyToManyField(
        'self',
        symmetrical=False,
        related_name='followers',
        blank=True
    )
    
    # ... resto del código existente ...
    
    def get_followers_count(self):
        return self.followers.count()
    
    def get_following_count(self):
        return self.following.count()
    
    def is_following(self, user):
        return self.following.filter(id=user.id).exists()

# views.py
@login_required
def toggle_follow(request, username):
    user_to_follow = get_object_or_404(User, username=username)
    
    if request.user == user_to_follow:
        # No puedes seguirte a ti mismo
        return redirect('core:profile', username=username)
    
    if request.user.following.filter(id=user_to_follow.id).exists():
        request.user.following.remove(user_to_follow)
    else:
        request.user.following.add(user_to_follow)
    
    return redirect('core:profile', username=username)

# urls.py
path('profile/<str:username>/follow/', views.toggle_follow, name='toggle_follow'),

# template profile.html
<div class="profile-stats">
    <p>Followers: {{ user.get_followers_count }}</p>
    <p>Following: {{ user.get_following_count }}</p>
</div>

{% if request.user.is_authenticated and request.user != user %}
<form method="post" action="{% url 'core:toggle_follow' user.username %}">
    {% csrf_token %}
    {% if request.user.is_following user %}
        <button type="submit">Unfollow</button>
    {% else %}
        <button type="submit">Follow</button>
    {% endif %}
</form>
{% endif %}
```

---

## 📝 ESCENARIO 3: Blog con Categorías (30 min)

### Enunciado
"Crea un blog simple donde:
- Los posts tienen título, contenido, autor y categoría
- Hay una página que lista todos los posts
- Se puede filtrar posts por categoría
- Solo usuarios autenticados pueden crear posts"

### Checklist:
- [ ] Modelo `Category` (name, slug)
- [ ] Modelo `Post` (title, content, author, category, created_at)
- [ ] Migraciones
- [ ] Form `PostForm`
- [ ] Vista `post_list`, `post_create`, `posts_by_category`
- [ ] Templates para listar y crear
- [ ] URLs configuradas
- [ ] Registro en admin

### Tiempo estimado:
- Models: 5 min
- Migrations: 1 min
- Forms: 2 min
- Views: 10 min
- Templates: 10 min
- URLs: 2 min

---

## 📝 ESCENARIO 4: Sistema de Likes (25 min)

### Enunciado
"Agrega la funcionalidad de dar 'like' a perfiles:
- Un usuario puede dar like a un perfil
- Un usuario puede quitar su like
- Mostrar cuántos likes tiene cada perfil
- Mostrar si el usuario actual ya dio like"

### Pistas:
- ManyToManyField en User para likes recibidos
- Vista toggle_like
- POST form con CSRF
- Método en modelo para contar likes

---

## 📝 ESCENARIO 5: Posts con Búsqueda (25 min)

### Enunciado
"Implementa un sistema de posts con búsqueda:
- Modelo Post con título y contenido
- Lista de todos los posts
- Barra de búsqueda que filtre por título o contenido
- Los resultados se muestran en la misma página"

### Pistas:
- `request.GET.get('q')`
- `filter(Q(title__icontains=query) | Q(content__icontains=query))`
- Form GET, no POST

---

## 📝 ESCENARIO 6: Perfil Extendido (20 min)

### Enunciado
"Extiende el modelo de usuario para incluir:
- Bio (texto largo, opcional)
- Ubicación (texto corto, opcional)
- Fecha de nacimiento (fecha, opcional)
- Crea un formulario para que usuarios editen su perfil"

### Pistas:
- Modificar modelo User existente
- Makemigrations con valores por defecto
- Form con campos específicos
- Vista solo para editar perfil propio

---

## 📝 ESCENARIO 7: Sistema de Tags (30 min)

### Enunciado
"Implementa un sistema de tags/etiquetas para posts:
- Los posts pueden tener múltiples tags
- Crear página que muestre posts por tag
- Mostrar todos los tags disponibles
- Los tags se pueden crear desde el admin"

### Checklist:
- [ ] Modelo `Tag` (name)
- [ ] ManyToManyField en Post
- [ ] Vista `posts_by_tag`
- [ ] Vista `tag_list` (opcional)
- [ ] Templates
- [ ] URLs

---

## 📝 ESCENARIO 8: Actividad Reciente (20 min)

### Enunciado
"Crea una sección de 'Actividad Reciente' en el home que muestre:
- Los últimos 5 usuarios registrados
- Los últimos 10 comentarios (si implementaste comentarios)
- Todo ordenado por fecha"

### Pistas:
- `.order_by('-date_joined')[:5]`
- Queries en la vista home
- Actualizar template home.html

---

## 🎯 ESCENARIO BONUS: API REST Simple (30 min) - AVANZADO

### Enunciado
"Crea un endpoint API que devuelva la lista de usuarios en formato JSON"

### Pistas:
```python
from django.http import JsonResponse

def api_users(request):
    users = User.objects.all().values('id', 'username', 'first_name', 'email')
    return JsonResponse(list(users), safe=False)
```

---

## ⏱️ PRÁCTICA CON CRONÓMETRO

### Esta noche (20-30 min):
Haz el **Escenario 1** completo con cronómetro. Es el más probable.

### Mañana antes de salir (15 min):
Revisa mentalmente cómo harías el **Escenario 2**.

---

## 💡 ESTRATEGIA DURANTE LA ENTREVISTA

### Primeros 3 minutos:
1. Lee el enunciado completo
2. Haz preguntas si algo no está claro
3. Escribe en papel/comentarios:
   - Modelos necesarios
   - Vistas necesarias
   - Templates necesarios

### Siguientes 25 minutos:
1. **Modelo primero** (5 min)
   - Define campos
   - Relaciones
   - Meta class y __str__
   
2. **Migración** (1 min)
   - makemigrations
   - migrate
   - Verificar en admin si es necesario

3. **Vista** (8-10 min)
   - Lógica básica primero
   - Refinamientos después

4. **Template** (8-10 min)
   - HTML básico funcional primero
   - Mejoras estéticas después

5. **URL** (1-2 min)
   - Conectar todo

6. **Testing manual** (2-3 min)
   - Probar en navegador
   - Verificar casos edge

### Últimos 2 minutos:
- Quick review del código
- Asegurar que no hay errores obvios
- Preparar explicación mental

---

## 🗣️ QUÉ DECIR DURANTE EL CÓDIGO

**BIEN:**
- "Voy a empezar creando el modelo porque..."
- "Necesito hacer una migración para actualizar la base de datos"
- "Esto podría mejorarse con..., pero por tiempo voy a..."
- "Déjenme verificar que esto funciona antes de continuar"

**EVITAR:**
- Silencio total
- "No sé qué hacer"
- "Esto está mal pero no sé por qué"

---

## ✅ DESPUÉS DEL CÓDIGO (explicación)

Prepárate para explicar:

1. **Decisiones de diseño:**
   - "Usé ForeignKey porque la relación es uno a muchos"
   - "Agregué blank=True porque este campo es opcional"

2. **Qué mejorarías con más tiempo:**
   - "Agregaría validaciones en el form"
   - "Implementaría paginación"
   - "Mejoraría el diseño del template"

3. **Trade-offs:**
   - "Elegí simplicidad sobre features adicionales"
   - "Prioricé que funcione sobre que se vea perfecto"

4. **Próximos pasos:**
   - "Agregaría tests"
   - "Implementaría manejo de errores más robusto"
   - "Optimizaría las queries"

---

## 🎓 RECUERDA

- No buscan perfección
- Buscan ver cómo piensas
- Comunicación > Código perfecto
- Es OK no terminar
- Es OK tener bugs menores
- Es OK usar IA y consultar

---

¡Practica al menos un escenario completo y estarás listo! 💪
