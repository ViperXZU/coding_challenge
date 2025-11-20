# ⌨️ COMANDOS ÚTILES - REFERENCIA RÁPIDA

Comandos que necesitarás durante la entrevista y práctica.

---

## 🚀 SERVIDOR

```bash
# Iniciar servidor de desarrollo
python manage.py runserver

# Iniciar en otro puerto
python manage.py runserver 8080

# Detener servidor
Ctrl + C
```

**URL local:** http://127.0.0.1:8000/

---

## 🗄️ BASE DE DATOS (MIGRATIONS)

```bash
# Ver estado actual de migrations
python manage.py showmigrations

# Crear nuevas migrations (después de modificar models)
python manage.py makemigrations

# Aplicar migrations a la base de datos
python manage.py migrate

# Ver SQL de una migration específica
python manage.py sqlmigrate core 0001

# Verificar problemas en el proyecto
python manage.py check
```

### Flujo típico después de cambiar un modelo:
```bash
# 1. Modificas models.py
# 2. Creas la migration
python manage.py makemigrations

# 3. Aplicas la migration
python manage.py migrate

# 4. Verificas que todo esté bien
python manage.py check
```

---

## 👤 USUARIOS

```bash
# Crear superusuario
python manage.py createsuperuser
# Te pedirá: username, email (opcional), password

# Cambiar password de un usuario
python manage.py changepassword username
```

**Usuarios de prueba existentes:**
- Usuario: `syzlab` → Password: `djangoforlife` (superuser)
- Usuario: `peter` → Password: `djangoforlife`
- Usuario: `sara` → Password: `djangoforlife`
- ... (todos tienen la misma contraseña)

---

## 📦 FIXTURES (Datos de prueba)

```bash
# Cargar datos de prueba
python manage.py loaddata fixtures/users.json

# Exportar datos actuales
python manage.py dumpdata core.User > users.json

# Exportar todos los datos
python manage.py dumpdata > all_data.json

# Exportar con formato bonito
python manage.py dumpdata --indent 2 core.User > users.json
```

---

## 🐚 SHELL INTERACTIVO

```bash
# Iniciar shell de Django (Python con contexto de Django)
python manage.py shell
```

### Comandos útiles dentro del shell:

```python
# Importar modelo
from core.models import User

# Ver todos los usuarios
User.objects.all()

# Crear usuario
user = User.objects.create(username='test', email='test@test.com')
user.set_password('password123')
user.save()

# Buscar usuario
user = User.objects.get(username='syzlab')
user = User.objects.filter(username='syzlab').first()

# Contar usuarios
User.objects.count()

# Borrar usuario
user.delete()

# Salir del shell
exit()
```

---

## 📱 CREAR APP

```bash
# Crear nueva app Django
python manage.py startapp nombre_app

# No olvides después:
# 1. Agregar 'nombre_app' a INSTALLED_APPS en settings.py
# 2. Crear urls.py en la app
# 3. Incluir urls en el proyecto principal
```

---

## 🔍 INSPECCIÓN Y DEBUG

```bash
# Ver todas las URLs registradas
python manage.py show_urls
# (Necesita django-extensions, no está instalado por defecto)

# O ver URLs de una app específica
# Revisar manualmente el archivo urls.py

# Verificar errores en el proyecto
python manage.py check

# Ver configuración actual
python manage.py diffsettings
```

---

## 📊 BASE DE DATOS SQLITE

```bash
# Abrir base de datos SQLite (si tienes sqlite3 instalado)
sqlite3 db.sqlite3

# Comandos dentro de sqlite3:
.tables                  # Ver todas las tablas
.schema nombre_tabla     # Ver estructura de una tabla
SELECT * FROM core_user; # Query SQL normal
.exit                    # Salir
```

**Nota:** Generalmente no necesitas usar SQLite directamente, usa el ORM de Django.

---

## 🧹 LIMPIAR Y RESETEAR

```bash
# Borrar base de datos (¡CUIDADO!)
# En Windows:
del db.sqlite3

# Borrar todas las migrations de una app (¡CUIDADO!)
# Borra manualmente los archivos en core/migrations/ excepto __init__.py

# Empezar desde cero:
del db.sqlite3
# Borrar archivos de migrations (excepto __init__.py)
python manage.py makemigrations
python manage.py migrate
python manage.py loaddata fixtures/users.json
```

---

## 🔄 GIT (Control de versiones)

```bash
# Ver estado
git status

# Ver cambios
git diff

# Agregar archivos
git add .
git add archivo.py

# Commit
git commit -m "Descripción del cambio"

# Ver historial
git log
git log --oneline

# Ver branches
git branch

# Crear branch
git branch nombre-branch

# Cambiar de branch
git checkout nombre-branch

# Crear y cambiar a nuevo branch
git checkout -b nombre-branch

# Volver a un commit anterior (¡CUIDADO!)
git reset --hard COMMIT_HASH
```

### Durante la entrevista:
```bash
# Commitea frecuentemente
git add .
git commit -m "Add Comment model"

# Si necesitas deshacer el último commit (sin perder cambios)
git reset --soft HEAD~1

# Si necesitas deshacer cambios no commiteados (¡CUIDADO!)
git checkout -- archivo.py
```

---

## 🔧 PYTHON/PIP

```bash
# Ver versión de Python
python --version

# Ver paquetes instalados
pip list

# Instalar paquete
pip install nombre-paquete

# Instalar desde requirements.txt
pip install -r requirements.txt

# Crear requirements.txt
pip freeze > requirements.txt

# Desinstalar paquete
pip uninstall nombre-paquete
```

---

## 🌐 NAVEGADOR

```bash
# Abrir automáticamente el navegador (en algunos sistemas)
start http://127.0.0.1:8000/

# URLs importantes del proyecto:
http://127.0.0.1:8000/              # Home
http://127.0.0.1:8000/admin/        # Admin
http://127.0.0.1:8000/profiles/     # Lista de perfiles (requiere login)
http://127.0.0.1:8000/profile/syzlab/  # Perfil específico (requiere login)
```

---

## 📝 ENTORNO VIRTUAL (si necesitas crearlo)

```bash
# Crear entorno virtual
python -m venv venv

# Activar en Windows
venv\Scripts\activate

# Activar en Mac/Linux
source venv/bin/activate

# Desactivar
deactivate

# Verificar que estás en el entorno virtual
# El prompt debería mostrar (venv) al inicio
```

---

## 🐛 DEBUGGING

### En views.py:
```python
# Print simple
print("Debug:", variable)

# Print más detallado
import pprint
pprint.pprint(variable)

# Ver atributos de un objeto
print(dir(objeto))

# Ver tipo
print(type(variable))
```

### En templates:
```django
{# Ver valor de variable #}
{{ variable }}

{# Ver todas las variables disponibles #}
{{ debug }}

{# En settings.py debe estar DEBUG = True #}
```

### Ver errores detallados:
- Django muestra errores detallados si `DEBUG = True` en settings.py
- Verifica la terminal donde corre el servidor
- Verifica el navegador (página de error de Django)

---

## ⚡ ATAJOS DURANTE LA ENTREVISTA

### Workflow completo rápido:
```bash
# 1. Modificar modelo en models.py
# 2. Crear y aplicar migration
python manage.py makemigrations && python manage.py migrate

# 3. Verificar que no hay errores
python manage.py check

# 4. Si el servidor no está corriendo
python manage.py runserver

# 5. Probar en navegador
# 6. Commit si funciona
git add . && git commit -m "Feature X completada"
```

### Reiniciar rápido si algo se rompe:
```bash
# Detener servidor (Ctrl+C)
# Verificar errores
python manage.py check

# Si es problema de migrations
python manage.py showmigrations
python manage.py migrate

# Reiniciar servidor
python manage.py runserver
```

---

## 🎯 COMANDOS MÁS USADOS EN LA ENTREVISTA

**Top 5:**
1. `python manage.py runserver`
2. `python manage.py makemigrations`
3. `python manage.py migrate`
4. `python manage.py check`
5. `Ctrl+C` (detener servidor)

**Otros importantes:**
- `python manage.py shell` (para probar código rápido)
- `python manage.py createsuperuser` (si necesitas crear un admin)
- `git add . && git commit -m "mensaje"` (commitear cambios)

---

## 📌 TIPS

### Mantén el servidor corriendo:
- Abre 2 terminales: una para el servidor, otra para comandos
- O usa `&` al final (en Mac/Linux) para correr en background
- En Windows, simplemente abre otra terminal

### Verifica errores frecuentemente:
```bash
# Cada vez que hagas cambios importantes:
python manage.py check
```

### Auto-reload:
- Django auto-recarga cuando modificas archivos
- Si no se recarga, detén y reinicia el servidor (Ctrl+C)
- Excepto: cambios en settings.py pueden necesitar reinicio manual

### Migrations:
- Siempre hacer `makemigrations` antes de `migrate`
- Si modificas un modelo, necesitas nueva migration
- Las migrations son secuenciales, no las borres a menos que sepas lo que haces

---

## 🚨 TROUBLESHOOTING RÁPIDO

### "Port already in use"
```bash
# El puerto 8000 está ocupado, usa otro:
python manage.py runserver 8080
```

### "No module named 'core'"
```bash
# La app no está en INSTALLED_APPS
# Agregar 'core' a INSTALLED_APPS en settings.py
```

### "Table doesn't exist"
```bash
python manage.py makemigrations
python manage.py migrate
```

### "CSRF token missing"
```html
<!-- Agregar en el form -->
{% csrf_token %}
```

### "Template does not exist"
```
Verificar:
1. Nombre correcto del template
2. Carpeta correcta (templates/core/archivo.html)
3. TEMPLATES en settings.py tiene 'APP_DIRS': True
```

---

## 💾 BACKUP RÁPIDO (Opcional)

```bash
# Guardar estado actual de la BD
copy db.sqlite3 db.sqlite3.backup

# Restaurar
copy db.sqlite3.backup db.sqlite3
```

---

## 📖 AYUDA

```bash
# Ver ayuda de un comando
python manage.py help
python manage.py help makemigrations

# Ver versión de Django
python -m django --version
```

---

¡Guarda este archivo para referencia rápida! 🚀
