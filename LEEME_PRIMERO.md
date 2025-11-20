# 📚 MATERIALES DE PREPARACIÓN - ENTREVISTA SYZLAB

Estos materiales te prepararán para la entrevista técnica de Django del jueves 20 de noviembre.

---

## 📂 ARCHIVOS INCLUIDOS

### 🎯 Para leer HOY (miércoles 19):

1. **`GUIA_PREPARACION.md`** (30-45 min) ⭐⭐⭐
   - Guía completa de Django desde cero
   - Conceptos fundamentals: Models, Views, Templates, URLs, ORM
   - Comandos esenciales
   - Tips para la entrevista
   - **Léelo completo esta noche**

2. **`EJERCICIOS_PRACTICA.md`** (1-2 horas) ⭐⭐⭐
   - 6 ejercicios prácticos con soluciones
   - Simula los tipos de desafíos de la entrevista
   - **Haz al menos los ejercicios 1 y 2 hoy**

3. **`ESCENARIOS_ENTREVISTA.md`** (30-60 min) ⭐⭐
   - Escenarios reales simulados con cronómetro
   - Estrategias de tiempo
   - Qué decir durante el código
   - **Practica al menos el Escenario 1 completo**

### 🚀 Para leer MAÑANA (jueves 20):

4. **`RESUMEN_ULTIMA_HORA.md`** (5-10 min) ⭐⭐⭐
   - Resumen ejecutivo de todo
   - Checklist pre-entrevista
   - Estrategia de 30 minutos
   - Errores comunes a evitar
   - **Lee esto justo antes de salir**

5. **`CHEAT_SHEET.md`** (referencia) ⭐⭐⭐
   - Referencia rápida de sintaxis
   - Patterns comunes
   - Comandos frecuentes
   - **Ten esto abierto durante la entrevista**

---

## 📅 PLAN DE ESTUDIO RECOMENDADO

### 🌙 MIÉRCOLES 19 NOCHE (2-3 horas total)

**7:00-7:45 PM** (45 min)
- ✅ Leer `GUIA_PREPARACION.md` completo
- ✅ Entender MVT pattern
- ✅ Revisar ejemplos de código

**8:00-9:30 PM** (90 min)
- ✅ Hacer `EJERCICIO 1: Blog Posts` completo
- ✅ Hacer `EJERCICIO 2: Categorías` completo
- ✅ Si te sobra tiempo: `EJERCICIO 3: Crear/Editar`

**9:30-10:00 PM** (30 min)
- ✅ Practicar `ESCENARIO 1: Comentarios` con cronómetro
- ✅ Objetivo: Completar la mayor parte en 30 min

**Opcional antes de dormir:**
- 📖 Repasar `CHEAT_SHEET.md`
- 🧘 Relajarte - ¡ya hiciste suficiente!

### ☀️ JUEVES 20 MAÑANA (30 min)

**9:30-10:00 AM**
- ✅ Leer `RESUMEN_ULTIMA_HORA.md`
- ✅ Verificar checklist de software
- ✅ Repasar patrón universal de código
- ✅ Respirar y confiar

**10:00-10:30 AM**
- Preparar laptop, cerrar apps innecesarias
- Salir hacia la entrevista
- Llegar 10 min antes

---

## 🎯 PRIORIDADES DE ESTUDIO

### ⚡ ABSOLUTAMENTE ESENCIAL:
- [x] Entender MVT (Model-View-Template)
- [x] Saber crear un modelo con campos básicos
- [x] Saber crear una vista simple
- [x] Entender cómo conectar URLs
- [x] Recordar {% csrf_token %} en forms
- [x] Comandos: makemigrations, migrate, runserver

### 🎓 MUY IMPORTANTE:
- [ ] ForeignKey y relaciones
- [ ] Forms con ModelForm
- [ ] Queries básicas del ORM
- [ ] Template inheritance y tags
- [ ] @login_required decorator

### 💡 ÚTIL PERO NO CRÍTICO:
- [ ] ManyToManyField
- [ ] Queries avanzadas (Q objects)
- [ ] Admin customization
- [ ] Paginación

---

## 🧭 NAVEGACIÓN RÁPIDA

### Si tienes 3 horas:
1. `GUIA_PREPARACION.md` (completo)
2. `EJERCICIOS_PRACTICA.md` (Ejercicios 1, 2, 3)
3. `ESCENARIOS_ENTREVISTA.md` (Escenario 1 con cronómetro)

### Si tienes 2 horas:
1. `GUIA_PREPARACION.md` (secciones 1-6)
2. `EJERCICIOS_PRACTICA.md` (Ejercicios 1 y 2)
3. `RESUMEN_ULTIMA_HORA.md` (completo)

### Si tienes 1 hora:
1. `GUIA_PREPARACION.md` (conceptos clave)
2. `CHEAT_SHEET.md` (revisar completo)
3. `RESUMEN_ULTIMA_HORA.md` (patrón universal)

### Si tienes 30 minutos:
1. `RESUMEN_ULTIMA_HORA.md` (completo) ⭐
2. `CHEAT_SHEET.md` (modelos, views, templates)
3. Hacer un ejercicio mental del flujo completo

---

## 💻 SETUP TÉCNICO

### Antes de empezar a estudiar:

```bash
# 1. Verificar que todo funciona
cd c:\Users\viper\OneDrive\Escritorio\Trabajos\coding_challenge
python manage.py check

# 2. Correr servidor
python manage.py runserver

# 3. Abrir en navegador
http://127.0.0.1:8000/

# 4. Probar login
Usuario: syzlab
Password: djangoforlife
```

### Durante la práctica:

Cada vez que hagas un ejercicio, sigue estos pasos:
```bash
# 1. Crear/modificar modelo
# 2. Hacer migrations
python manage.py makemigrations
python manage.py migrate

# 3. Crear vista, template, URL
# 4. Probar en navegador
# 5. Fix errores si hay
# 6. Commit
git add .
git commit -m "Ejercicio X completado"
```

---

## 📊 MÉTRICAS DE ÉXITO

### Al final de esta noche deberías poder:

- ✅ Crear un modelo básico con 3-4 campos
- ✅ Hacer migrations sin ayuda
- ✅ Crear una vista que liste objetos
- ✅ Crear una vista que cree objetos (con form)
- ✅ Conectar una URL a una vista
- ✅ Extender base.html en un template
- ✅ Usar {% for %} para iterar
- ✅ Usar {% url %} para links
- ✅ Recordar {% csrf_token %}

### Bonus (si tienes tiempo):
- ✅ ForeignKey y relaciones
- ✅ ManyToManyField
- ✅ @login_required
- ✅ get_object_or_404

---

## 🎓 CONCEPTOS CORE (Memoriza)

### El flujo de Django:
```
REQUEST → URL → VIEW → MODEL (DB) → TEMPLATE → RESPONSE
```

### El ciclo de desarrollo:
```
1. Model
2. makemigrations
3. migrate
4. View
5. Template
6. URL
7. Test
```

### La estructura MVT:
```
M (Model)    = Datos y lógica de negocio
V (View)     = Controlador (procesa requests)
T (Template) = Presentación (HTML)
```

---

## 🚨 ERRORES COMUNES (Evítalos)

1. ❌ Olvidar `{% csrf_token %}`
2. ❌ No hacer migrations después de cambiar modelos
3. ❌ Imports incorrectos (`from models` en lugar de `from .models`)
4. ❌ App no en `INSTALLED_APPS`
5. ❌ Olvidar `commit=False` cuando seteas campos manualmente
6. ❌ No usar `get_object_or_404`
7. ❌ Template en carpeta incorrecta

---

## 🔗 RECURSOS ADICIONALES

### Durante la entrevista puedes consultar:
- ✅ Estos archivos (especialmente CHEAT_SHEET.md)
- ✅ ChatGPT / GitHub Copilot / Claude
- ✅ Documentación oficial de Django
- ✅ Los entrevistadores (¡pregunta!)

### NO necesitas memorizar:
- ❌ Sintaxis exacta de todo
- ❌ Todos los parámetros de cada campo
- ❌ Nombres exactos de funciones
- ❌ Configuración de settings.py

---

## 📞 INFORMACIÓN DE LA ENTREVISTA

**Fecha:** Jueves 20 de noviembre, 2025  
**Hora:** 11:00 AM  
**Duración:** 1 hora (30 min código + 30 min explicación)  
**Ubicación:** Av. Providencia 1208, oficina 303  
**Mapa:** https://maps.app.goo.gl/cv2f844VNReJeabd8  
**Teléfono emergencia:** +56 9 6871 2108

### Qué llevar:
- Laptop con Python, Git, VSCode
- Cargador
- Este proyecto funcionando
- Actitud positiva

### Qué puedes usar:
- IA (ChatGPT, Copilot, etc.)
- Documentación
- Internet
- Estos materiales de estudio
- Consultar con los entrevistadores

---

## 🎯 OBJETIVO FINAL

**No es:** Escribir código perfecto  
**Es:** Demostrar que puedes:
1. Entender un problema
2. Planificar una solución
3. Implementar usando Django
4. Comunicar tu proceso de pensamiento
5. Usar herramientas eficientemente (incluida IA)

---

## 💪 MENSAJE MOTIVACIONAL

Ya pasaste la entrevista online. Eso significa que SyzLab ve potencial en ti.

Estos materiales cubren todo lo que necesitas para los desafíos típicos de Django. No necesitas ser un experto, solo necesitas entender los fundamentos y mostrar cómo piensas.

**Recuerda:**
- Puedes usar IA libremente
- Puedes consultar con ellos
- No tienes que terminar todo
- El proceso importa más que el resultado
- Ya demostraste que puedes hacerlo

---

## ✅ CHECKLIST FINAL

### Miércoles noche:
- [ ] Leí `GUIA_PREPARACION.md`
- [ ] Hice al menos 2 ejercicios de práctica
- [ ] Practiqué un escenario con cronómetro
- [ ] Entiendo el flujo MVT
- [ ] Sé crear modelo, vista, template, URL

### Jueves mañana:
- [ ] Leí `RESUMEN_ULTIMA_HORA.md`
- [ ] Proyecto corriendo sin errores
- [ ] Laptop lista con todo instalado
- [ ] `CHEAT_SHEET.md` abierto para referencia
- [ ] Mentalidad positiva activada

---

## 🎓 ÚLTIMO CONSEJO

**No trates de memorizar todo.**  
**Entiende el patrón y úsalo.**

Todos los problemas de Django siguen el mismo flujo:
1. Modelo para datos
2. Vista para lógica
3. Template para presentación
4. URL para conectar

Si entiendes esto, puedes resolver cualquier desafío que te den.

---

# 🚀 ¡MUCHA SUERTE!

Tienes todo lo necesario. Ahora solo confía en ti y muestra lo que sabes.

**Nos vemos del otro lado con buenas noticias.** 💪

---

*Creado el 19 de noviembre, 2025*  
*Para la entrevista del 20 de noviembre, 2025*  
*SyzLab - Coding Challenge*
