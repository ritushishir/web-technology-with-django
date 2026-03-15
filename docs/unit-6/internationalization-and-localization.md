# Internationalization and Localization
- Internationalization (i18n): The process of designing and preparing your application to support multiple languages.
- Localization (l10n): The process of adapting your application to a specific language or region.

## Specifying Translation Strings in Python Code {#specifying-translation-strings-in-python-code}

### Using `gettext` and `gettext_lazy`

Django provides two main functions for marking strings for translation in Python code:

```python
from django.utils.translation import gettext as _, gettext_lazy as _l

# Immediate translation (evaluated at runtime)
title = _('Welcome to our website')

# Lazy translation (evaluated when accessed, ideal for models/forms)
class Song(models.Model):
    title = models.CharField(max_length=100, verbose_name=_l('Title'))
```

**Key Differences:**

- `gettext()`: Translates immediately when called
- `gettext_lazy()`: Returns a proxy object that translates when accessed

Use lazy translation in model definitions, form fields, and other places where the string might be evaluated before the request context is available

## Specifying Translation Strings in Template Code {#specifying-translation-strings-in-template-code}

### Using the `{% trans %}` and `{% blocktrans %}` tags

Django provides template tags for marking translatable content:

```html
{% load i18n %}

<!-- Simple translation -->
<h1>{% trans "Welcome to our site" %}</h1>

<!-- With context -->
<p>{% trans "Welcome" context "greeting" %}</p>

<!-- Blocktrans for complex content -->
{% blocktrans %}
Hello {{ user.name }}, you have {{ count }} new messages.
{% endblocktrans %}

<!-- Blocktrans with pluralization -->
{% blocktrans count count=cart.items.count %}
You have {{ count }} item in your cart.
{% plural %}
You have {{ count }} items in your cart.
{% endblocktrans %}
```

### Translating Variable Content within Templates

For translating variables that contain translatable strings:

```html
{% load i18n %}

<!-- Using with blocktrans -->
{% blocktrans with product_name=product.name %}
Product: {{ product_name }}
{% endblocktrans %}

<!-- Translating template variables -->
{% get_current_language as LANGUAGE_CODE %}
{% get_available_languages as LANGUAGES %}

<!-- Using trans tag with filters -->
<p>{% trans message|upper %}</p>

<!-- Date/time localization -->
{% load locale %}
<p>{% localdate appointment.date %}</p>
<p>{% localtime appointment.time %}</p>
```

**Best Practices:**

- Use `{% trans %}` for simple strings
- Use `{% blocktrans %}` for complex content with variables

## Creating Language Files {#creating-language-files}

### Working with `.po` (Portable Object) and `.mo` (Machine Object) files

Django uses GNU gettext's file format for translations:

**`.po` Files (Portable Object):**

- Human-readable translation files
- Contains original strings and their translations
- Includes metadata like translator information, creation date
- Example structure:

```po
#: templates/base.html:10
msgid "Welcome"
msgstr "Bienvenue"

#: models.py:15
#, python-format
msgid "There is %(count)s item"
msgid_plural "There are %(count)s items"
msgstr[0] "Il y a %(count)s élément"
msgstr[1] "Il y a %(count)s éléments"
```

**`.mo` Files (Machine Object):**

- Compiled binary versions of `.po` files
- Optimized for fast lookup during runtime
- Automatically generated from `.po` files
- Should not be edited manually

### Using `makemessages` and `compilemessages` commands

**Creating Translation Files:**

```bash
# Create translation files for all enabled languages
python manage.py makemessages -l ne

# Create for specific domains
django-admin makemessages -l ne --domain=djangojs  # JavaScript translations
django-admin makemessages -l ne --domain=django     # Python/template translations

# Update existing translations
django-admin makemessages -l ne --no-obsolete
```

**Compiling Translations:**

```bash
# Compile all translation files
python manage.py compilemessages

# Compile for specific language
python manage.py compilemessages -l ne
```

**Directory Structure:**
```
locale/
├── ne/
│   └── LC_MESSAGES/
│       ├── django.po      # Python/template translations
│       ├── django.mo      # Compiled Python/template translations
│       ├── djangojs.po    # JavaScript translations
│       └── djangojs.mo    # Compiled JavaScript translations
├── in/
│   └── LC_MESSAGES/
│       ├── django.po
│       ├── django.mo
│       ├── djangojs.po
│       └── djangojs.mo
```

**Workflow:**

1. Add translatable strings to code using translation functions
2. Run `makemessages` to extract strings and update `.po` files
3. Translate the strings in `.po` files
4. Run `compilemessages` to generate `.mo` files
5. Restart the application to load new translations

## Language Preference Discovery {#language-preference-discovery}

### Determine the user's language via URL, Cookies, or Browser headers

Django uses a sophisticated process to determine the appropriate language for each request:

**Priority Order (from highest to lowest):**

**URL Prefix** (when using `i18n_patterns`)

```python
   # urls.py
   from django.conf.urls.i18n import i18n_patterns
   from django.urls import path, include
   
   urlpatterns = [
       path('i18n/', include('django.conf.urls.i18n')),
   ]
   
   urlpatterns += i18n_patterns(
       path('', views.home, name='home'),
       path('about/', views.about, name='about'),
       prefix_default_language=False  # Optional: don't add default language prefix
   )
```
URLs become: `/ne/home/`, `/fr/accueil/` etc

**Language Cookie** (`django_language`)

   - Set by the `set_language` view or manually
   - Persists user's language preference across sessions
   - Example: `request.COOKIES.get('django_language')`

**Session Key** (`django_language`)

   - Stored in user session when cookie-based sessions are used
   - Falls back if no cookie is present

**HTTP Accept-Language Header**

   - Browser's language preferences
   - Parsed in order of quality values
   - Example: `Accept-Language: es-ES,es;q=0.9,en;q=0.8`

**Default Language** (from `settings.LANGUAGE_CODE`)

   - Final fallback if no other method yields a match

### The role of `LocaleMiddleware`

**Configuration in `settings.py`:**
```python
MIDDLEWARE = [
    # ... other middleware
    'django.middleware.locale.LocaleMiddleware',
    # Must be after SessionMiddleware and before CommonMiddleware
]

# Language settings
LANGUAGE_CODE = 'en-us'
LANGUAGES = [
    ('en', 'English'),
    ('es', 'Español'),
    ('fr', 'Français'),
    ('de', 'Deutsch'),
]
LOCALE_PATHS = [
    BASE_DIR / 'locale/',
]
```

**Middleware Responsibilities:**

1. **Language Detection**

    - Evaluates language preference in priority order
    - Validates against available languages in `LANGUAGES` setting
    - Sets `request.LANGUAGE_CODE`

2. **Translation Activation**

    - Activates the selected language for the current thread
    - Makes translation functions return the correct language
    - Handles timezone and locale formatting

3. **Context Processing**

    - Adds language information to template context
    - Provides `LANGUAGES`, `LANGUAGE_CODE`, and `LANGUAGE_BIDI` variables


## The "set_language" Redirect View {#the-set_language-redirect-view}

### Implementing a language switcher for users to manually toggle between languages

Django provides a built-in view for handling language switching:

**Basic Implementation:**

```python
# urls.py
from django.conf.urls.i18n import i18n_patterns
from django.urls import path, include

urlpatterns = [
    path('i18n/', include('django.conf.urls.i18n')),  # Includes set_language view
]

urlpatterns += i18n_patterns(
    # Your URL patterns here
)
```

**Language Switcher Template:**

```html
{% load i18n %}

<form action="{% url 'set_language' %}" method="post">
    {% csrf_token %}
    <input name="next" type="hidden" value="{{ request.path }}" />
    
    <select name="language" onchange="this.form.submit()">
        {% get_current_language as LANGUAGE_CODE %}
        {% get_available_languages as LANGUAGES %}
        
        {% for lang_code, lang_name in LANGUAGES %}
            <option value="{{ lang_code }}" 
                    {% if lang_code == LANGUAGE_CODE %}selected{% endif %}>
                {{ lang_name }}
            </option>
        {% endfor %}
    </select>
</form>
```
