# Django Interview Boilerplate - AI Agent Instructions

## Project Purpose

This is a **technical interview boilerplate** for Django coding challenges. Candidates fork this repo and have **30 minutes** to implement features during live interviews. The project is designed to be minimal but complete, allowing candidates to demonstrate Django fundamentals quickly.

## Architecture Overview

**Pattern:** Django MVT (Model-View-Template)
- **Single app:** `core` - handles all user-related functionality
- **Custom User model:** `core.User` extends `AbstractUser` (set via `AUTH_USER_MODEL`)
- **Database:** SQLite with 10 pre-loaded test users (password: `djangoforlife`)
- **Templates:** Centralized in `templates/` with `base.html` providing navbar/layout
- **Static files:** CSS in `static/css/`, images under `static/image/`

### URL Structure
```
/                    → core.home (public)
/profiles/           → core.profiles (login required)
/profile/<username>/ → core.profile (login required)
/admin/              → Django admin
```

## Critical Development Workflows

### Standard Development Cycle
```powershell
# 1. Modify/create models in core/models.py
# 2. Generate and apply migrations
python manage.py makemigrations
python manage.py migrate

# 3. Create/update views in core/views.py
# 4. Add URL patterns in core/urls.py (uses app_name='core')
# 5. Create templates in templates/core/
# 6. Test in browser at http://127.0.0.1:8000/
```

### Essential Commands
```powershell
python manage.py runserver              # Start dev server
python manage.py loaddata fixtures/users.json  # Load test users
python manage.py createsuperuser        # Create admin (if needed)
python manage.py shell                  # Interactive Django shell
```

### Test Users (all password: `djangoforlife`)
- `syzlab` (superuser)
- `peter`, `sara`, `mary`, `zack`, `britney`, `charlie`, `javiera`, `shane`, `tiffany`

## Project-Specific Conventions

### Views Pattern
- **Function-based views only** (no CBVs in boilerplate)
- Use `@login_required` decorator for protected views
- Always use `get_object_or_404(Model, ...)` instead of `.get()`
- Pass context as dictionary: `render(request, 'template.html', {'key': value})`

**Example from codebase:**
```python
@login_required
def profile(request, username):
    user = get_object_or_404(User, username=username)
    return render(request, 'core/profile.html', {
        'user': user,
        'is_own_profile': request.user.username == username
    })
```

### URL Patterns
- **Always use app namespacing:** `app_name = 'core'` in `core/urls.py`
- **In templates:** `{% url 'core:view_name' %}`
- **Root URLs** in `coding_challenge/urls.py` include core URLs with `path('', include('core.urls'))`

### Template Inheritance
- **All templates extend `base.html`**
- Base template provides: navbar with login state, static file loading, responsive container
- Override blocks: `{% block title %}` and `{% block content %}`
- **CSRF tokens required:** Always include `{% csrf_token %}` in forms

**Example from codebase:**
```django
{% extends 'base.html' %}
{% block title %}Home{% endblock %}
{% block content %}
    <div class="card">
        <h1>{{ user.username }}</h1>
    </div>
{% endblock %}
```

### Model Patterns
- **Custom User model** is already configured - extend `core.User` for user-related features
- **ForeignKey convention:** `author = models.ForeignKey(User, on_delete=models.CASCADE)`
- **String representation:** Always implement `__str__()` method
- **Common interview additions:** ForeignKey relationships, ManyToManyField, TextFields

### Admin Registration
Simple pattern used throughout:
```python
from django.contrib import admin
from .models import YourModel

admin.site.register(YourModel)
```

For custom User model, inherit from `UserAdmin`:
```python
from django.contrib.auth.admin import UserAdmin
admin.site.register(User, UserAdmin)
```

## Typical Interview Challenges

Candidates are asked to implement features like:

1. **CRUD for new models** (e.g., Blog posts, comments, categories)
   - Create model → migrations → list view → detail view → create form

2. **Add relationships** (e.g., Posts belong to users, tags on posts)
   - ForeignKey for one-to-many
   - ManyToManyField for many-to-many

3. **Extend User model** (e.g., Add bio, avatar, social links)
   - Add fields to `core.User` → migrate → update profile template

4. **User interactions** (e.g., Likes, follows, favorites)
   - ManyToManyField on target model
   - Toggle view with redirect
   - Display count in templates

## Common Pitfalls & Solutions

### ❌ Forgetting CSRF token
```django
<!-- Wrong -->
<form method="post">
    {{ form.as_p }}
</form>

<!-- Correct -->
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
</form>
```

### ❌ Not adding app to INSTALLED_APPS
When creating new apps, add to `settings.py`:
```python
INSTALLED_APPS = [
    # ...
    'core',
    'your_new_app',  # ← Add here
]
```

### ❌ Wrong import paths
```python
# Wrong
from models import User

# Correct
from core.models import User
from .models import User  # Within same app
```

### ❌ Forgetting migrations
After ANY model change:
```powershell
python manage.py makemigrations
python manage.py migrate
```

### ❌ ModelForm without commit=False
When setting fields manually:
```python
# Correct pattern
if request.method == 'POST':
    form = PostForm(request.POST)
    if form.is_valid():
        post = form.save(commit=False)
        post.author = request.user  # Set manually
        post.save()
        return redirect('core:home')
```

## File Structure & Conventions

```
coding_challenge/          # Project config package
├── settings.py           # AUTH_USER_MODEL = 'core.User'
├── urls.py               # Root URLs (admin + includes core)
└── wsgi.py

core/                      # Main app
├── models.py             # Custom User model
├── views.py              # Function-based views
├── urls.py               # App URLs with app_name='core'
└── admin.py              # Simple admin registration

templates/                 # Global templates directory
├── base.html             # Base template with navbar
└── core/                 # App-specific templates
    ├── home.html
    ├── profile.html
    └── profiles.html

static/                    # Static files
└── css/base.css          # Base styles with .card, .heading-primary, etc.

fixtures/users.json        # 10 test users
db.sqlite3                # SQLite database
```

## Quick Reference: ORM Queries

```python
# Common patterns used in interviews
from core.models import User

# List all
users = User.objects.all().order_by('username')

# Get one (use this)
user = get_object_or_404(User, username=username)

# Filter
posts = Post.objects.filter(author=request.user)

# Create
post = Post.objects.create(title='Title', content='...')

# Related objects (reverse FK)
user.post_set.all()  # If Post has ForeignKey to User
```

## Environment & Dependencies

- **Python:** 3.10+ (project uses 3.12)
- **Django:** 5.2.8 (only dependency in requirements.txt)
- **Database:** SQLite (pre-configured, no setup needed)
- **Shell:** PowerShell on Windows

## When Helping Candidates

1. **Encourage MVT thinking:** Model first → View → Template → URL
2. **Remind about migrations:** After model changes, always run makemigrations + migrate
3. **Point to existing patterns:** Views in `core/views.py` show good examples
4. **CSRF tokens:** Most common template error
5. **Use fixtures:** `loaddata fixtures/users.json` provides instant test data
6. **AI is allowed:** Candidates can and should use AI assistance during interviews

## Style & Code Quality

- **Clean, readable code** over complex solutions
- **Descriptive names:** `post_list` not `pl`, `User` not `U`
- **Comments for complex logic** (but code should be self-documenting)
- **Consistent indentation:** 4 spaces (Python standard)
- **Small, focused commits** during development

## Spanish Documentation Note

This project includes extensive Spanish-language study materials (`GUIA_PREPARACION.md`, `RESUMEN_ULTIMA_HORA.md`, etc.) for candidates preparing for interviews. These are reference materials, not part of the codebase itself.
