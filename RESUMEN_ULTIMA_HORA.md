# ⚡ RESUMEN ÚLTIMA HORA - LEE ESTO MAÑANA

**Fecha:** Jueves 20 de noviembre  
**Hora:** 11:00 AM  
**Lugar:** Av. Providencia 1208, oficina 303  
**Teléfono emergencia:** +56 9 6871 2108

---

## 🎒 ANTES DE SALIR

### Física:
- [ ] Laptop + cargador
- [ ] Mouse (opcional pero útil)
- [ ] Celular cargado
- [ ] Agua/snack

### Software:
- [ ] Python funcionando (tienes 3.12 ✓)
- [ ] Git configurado
- [ ] VSCode abierto con este proyecto
- [ ] Servidor corriendo: `python manage.py runserver`
- [ ] Navegador en http://127.0.0.1:8000/
- [ ] Este proyecto sin errores: `python manage.py check`

---

## 🧠 CONCEPTOS CLAVE - REPASO 5 MINUTOS

### Django Flow:
```
URL → View → Query Model → Render Template → HTML
```

### Crear algo nuevo:
```
1. Model → 2. makemigrations → 3. migrate → 
4. View → 5. Template → 6. URL → 7. Test
```

### Datos importantes:
- **Password de usuarios:** `djangoforlife`
- **Usuario admin:** `syzlab`
- **Custom User Model:** `AUTH_USER_MODEL = 'core.User'` en settings

---

## 📋 PATRÓN UNIVERSAL - Memoriza esto

```python
# 1. MODEL
class MiModelo(models.Model):
    campo = models.CharField(max_length=200)
    autor = models.ForeignKey(User, on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)
    
    def __str__(self):
        return self.campo

# 2. FORM (si lo necesitas)
class MiForm(forms.ModelForm):
    class Meta:
        model = MiModelo
        fields = ['campo']

# 3. VIEW
@login_required  # Si requiere login
def mi_vista(request):
    if request.method == 'POST':
        form = MiForm(request.POST)
        if form.is_valid():
            obj = form.save(commit=False)
            obj.autor = request.user
            obj.save()
            return redirect('app:otra_vista')
    else:
        form = MiForm()
    
    objetos = MiModelo.objects.all()
    return render(request, 'app/template.html', {
        'objetos': objetos,
        'form': form
    })

# 4. URL
path('ruta/', views.mi_vista, name='mi_vista'),

# 5. TEMPLATE
{% extends 'base.html' %}
{% block content %}
    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <button>Submit</button>
    </form>
    
    {% for obj in objetos %}
        <p>{{ obj.campo }}</p>
    {% endfor %}
{% endblock %}
```

---

## 🎯 ESTRATEGIA 30 MINUTOS

### 0-3 min: PLANIFICAR
- Leer enunciado completo
- Identificar: ¿qué modelos? ¿qué vistas? ¿qué templates?
- Hacer preguntas si algo no está claro
- Escribir plan en comentarios o papel

### 3-8 min: MODELS
```python
# Crear/modificar modelo(s)
# Pensar bien las relaciones
# ForeignKey para uno-a-muchos
# ManyToManyField para muchos-a-muchos
```
```bash
python manage.py makemigrations
python manage.py migrate
```

### 8-18 min: VIEWS
```python
# Empezar con lo básico
# GET primero, POST después
# Usar get_object_or_404
# Importar lo necesario
```

### 18-26 min: TEMPLATES
```html
<!-- Extender base.html -->
<!-- HTML funcional > bonito -->
<!-- No olvidar {% csrf_token %} -->
```

### 26-28 min: URLS
```python
# Conectar vista con URL
# Verificar namespace
```

### 28-30 min: TEST & FIX
- Probar en navegador
- Fix errores obvios
- Respirar

---

## 🚨 ERRORES MÁS COMUNES - EVÍTALOS

### ❌ Olvidar CSRF token
```html
<form method="post">
    {% csrf_token %}  <!-- ← NO OLVIDES ESTO -->
    ...
</form>
```

### ❌ No hacer migrations
```bash
# Después de modificar models:
python manage.py makemigrations
python manage.py migrate
```

### ❌ App no en INSTALLED_APPS
```python
# settings.py
INSTALLED_APPS = [
    ...
    'core',  # ← Tu app debe estar aquí
]
```

### ❌ Import incorrecto
```python
# ✅ Correcto
from .models import User
from django.shortcuts import render, get_object_or_404
from django.contrib.auth.decorators import login_required

# ❌ Incorrecto
from models import User  # No así
```

### ❌ Olvidar commit=False
```python
# Cuando necesitas setear campos antes de guardar:
obj = form.save(commit=False)  # ← No guardes aún
obj.autor = request.user       # ← Setea campo
obj.save()                     # ← Ahora sí guarda
```

---

## 💬 FRASES ÚTILES DURANTE LA ENTREVISTA

**Al empezar:**
- "Déjenme asegurarme de que entendí bien: [repetir enunciado]"
- "¿Puedo usar [herramienta/librería]?"

**Durante el código:**
- "Voy a empezar por el modelo porque..."
- "Necesito crear una migración para..."
- "Estoy pensando en usar [X] porque..."
- "Esto lo voy a simplificar por tiempo, pero idealmente haría..."

**Si te atascas:**
- "Déjenme buscar la sintaxis exacta..."
- "¿Puedo usar Copilot/ChatGPT para esto?"
- "¿Me pueden dar una pista sobre...?"

**Al explicar después:**
- "Empecé por [X] porque..."
- "Esta decisión la tomé considerando..."
- "Con más tiempo, mejoraría [Y] agregando..."
- "Un trade-off que hice fue..."

---

## 🎓 LO MÁS IMPORTANTE

### ✅ PUEDES:
- Usar IA sin restricciones
- Consultar documentación
- Preguntar a los entrevistadores
- Dejar código incompleto
- Tener bugs menores
- Pedir pistas

### ❌ NO TE ESTRESES POR:
- Terminar todo
- Código perfecto
- Diseño bonito
- Optimización prematura
- Casos edge raros

### 🎯 BUSCAN VER:
1. **Cómo piensas** - Habla en voz alta
2. **Cómo resuelves problemas** - Muestra tu proceso
3. **Cómo te comunicas** - Explica decisiones
4. **Estructura del código** - Clean > clever
5. **Uso de herramientas** - Saben usar IA eficientemente

---

## 📊 PRIORIDADES

### PRIORIDAD ALTA (Debe funcionar):
- Modelo con campos correctos
- Migrations aplicadas
- Vista básica que no rompa
- Template que renderice
- URL conectada

### PRIORIDAD MEDIA (Nice to have):
- Form validation
- Error handling
- User feedback (mensajes)
- Edge cases

### PRIORIDAD BAJA (Bonus):
- Diseño CSS
- Optimizaciones
- Features extra
- Tests

---

## 🧘 ÚLTIMO CONSEJO

### 5 minutos antes de entrar:
1. Respira profundo 3 veces
2. Recuerda: **ya pasaste la primera fase**
3. Son 30 minutos, no una vida
4. Puedes usar TODAS las herramientas
5. No buscan perfección, buscan proceso

### Durante los 30 minutos:
- **Piensa en voz alta** - aunque sea para ti mismo
- **Commitea frecuentemente** - cada 5-10 min
- **Prioriza funcionalidad** - que funcione > que sea bonito
- **Usa IA libremente** - está permitido y esperado

### En la explicación:
- **Sé honesto** - "Esto no terminé de pulirlo"
- **Muestra que aprendes** - "Ahora haría X diferente"
- **Explica trade-offs** - "Elegí X sobre Y porque..."

---

## 🍀 MANTRA

```
"No buscan código perfecto.
Buscan ver cómo pienso.
Ya pasé la primera fase.
Puedo hacer esto."
```

---

## 📞 CONTACTOS

**Emergencia para llegar:** +56 9 6871 2108  
**Dirección:** Av. Providencia 1208, oficina 303  
**Maps:** https://maps.app.goo.gl/cv2f844VNReJeabd8

---

## ⏰ TIMELINE

- **10:30** - Salir de casa
- **10:50** - Llegar y ubicarse
- **11:00** - Inicio entrevista
- **11:00-11:30** - Desafío código
- **11:30-12:00** - Explicación
- **12:00** - Fin

---

# 🚀 ¡ÉXITO!

**Respira. Confía. Codea. Comunica.**

Ya tienes todo lo necesario. Ahora solo es cuestión de mostrarlo.

**Nos vemos del otro lado con buenas noticias.** 💪

---

## 📚 ARCHIVOS DE ESTUDIO

- `GUIA_PREPARACION.md` - Guía completa de Django
- `CHEAT_SHEET.md` - Referencia rápida (ten esto abierto)
- `EJERCICIOS_PRACTICA.md` - Ejercicios para practicar
- `ESCENARIOS_ENTREVISTA.md` - Simulacros de entrevista
- Este archivo - Resumen ejecutivo

**Recomendación:** Imprime o ten en otra pantalla `CHEAT_SHEET.md` durante la entrevista.
