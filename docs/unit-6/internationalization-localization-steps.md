
# Step-by-Step Guide: Django Internationalization and Localization

This guide provides a complete implementation roadmap for adding internationalization (i18n) and localization (l10n) to your Django project.

## Step 1: Project Setup and Configuration

### 1.1 Configure Settings
Add these settings to your `settings.py`:

```python
# Enable internationalization
USE_I18N = True
USE_L10N = True
USE_TZ = True

# Default language and locale
LANGUAGE_CODE = 'en-us'
TIME_ZONE = 'UTC'

# Supported languages
LANGUAGES = [
    ('en', 'English'),
    ('ne', 'Nepali'),
]

# Locale paths for translation files
LOCALE_PATHS = [
    BASE_DIR / 'locale/',
]

# Language cookie settings -- optional
LANGUAGE_COOKIE_NAME = 'django_language'
LANGUAGE_COOKIE_AGE = 365 * 24 * 60 * 60  # 1 year
LANGUAGE_COOKIE_DOMAIN = None
LANGUAGE_COOKIE_PATH = '/'
LANGUAGE_COOKIE_SECURE = False  # Set to True for HTTPS
LANGUAGE_COOKIE_HTTPONLY = True
LANGUAGE_COOKIE_SAMESITE = 'Lax'
```

### 1.2 Add Middleware
Update your middleware configuration:

```python
MIDDLEWARE = [
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.locale.LocaleMiddleware',        # After SessionMiddleware
    'django.middleware.common.CommonMiddleware',         # Before CommonMiddleware
    # ... other middleware
]
```

**Important**: `LocaleMiddleware` must be placed after `SessionMiddleware` and before `CommonMiddleware`.


## Step 2: URL Configuration

### 2.1 Configure Internationalized URLs
Update your main `urls.py`:

```python
from django.conf.urls.i18n import i18n_patterns
from django.urls import path, include
from django.conf.urls.static import static
from django.conf import settings

# Include i18n URLs (for language switching and JavaScript catalog)
urlpatterns = [
    path('i18n/', include('django.conf.urls.i18n')),
    # Add other non-internationalized URLs here
]

```

## Step 3: Mark Strings for Translation

### 3.1 Python Code Translation
Update your Python files to mark strings for translation:

```python
# views.py
from django.utils.translation import gettext as _

def song_list(request):

    songs = Song.objects.all()
    message = _("Welcome to our song collection!")
    page_title = _("Song List")
    # Logic to fetch and display a list of songs
    return render(request, 'song_list.html', {'songs': songs, 'message': message, 'page_title': page_title})

```

### 3.2 Template Translation
Update your templates to use translation tags:

```html
{% load i18n %}

<!DOCTYPE html>
<html lang="{% get_current_language as LANGUAGE_CODE %}{{ LANGUAGE_CODE }}">
```

## Step 4: Create Translation Files

### 4.1 Extract Translation Strings
Run these commands to create and update translation files:

```bash
# Create translation files for each language
python manage.py makemessages -l ne

```

### 4.2 Translate the Content
Edit the `.po` files in `locale/[language]/LC_MESSAGES/django.po`:

```po
# locale/ne/LC_MESSAGES/django.po

# This file is distributed under the same license as the geetshala package.
#
msgid ""
msgstr ""
"Project-Id-Version: geetshala\n"
"Report-Msgid-Bugs-To: \n"
"POT-Creation-Date: 2024-01-01 00:00+0000\n"
"PO-Revision-Date: 2024-01-01 00:00+0000\n"
"Last-Translator: \n"
"Language-Team: Nepali\n"
"Language: ne\n"
"MIME-Version: 1.0\n"
"Content-Type: text/plain; charset=UTF-8\n"
"Content-Transfer-Encoding: 8bit\n"

msgid "Welcome to our song collection!"
msgstr "हाम्रो गीत संग्रहमा स्वागत छ!"

msgid "Welcome to Geetshala"
msgstr "गीतशालामा स्वागत छ"

msgid "Home"
msgstr "होम"

msgid "List of Songs"
msgstr "गीतहरूको सूची"

msgid "Add New Song Details"
msgstr "नयाँ गीत विवरण थप्नुहोस्"

```

### 4.3 Compile Translations
```bash
# Compile all translation files
python manage.py compilemessages

# Compile specific language
python manage.py compilemessages -l ne
```

## Step 5: Create Language Switcher

### 5.1 Language Switcher Template
Create `templates/partials/language_switcher.html`:

```html
{% load i18n static %}

<div style="margin: 10px 0;">
    {% get_current_language as LANGUAGE_CODE %}
    {% get_available_languages as LANGUAGES %}
    
    <form action="{% url 'set_language' %}" method="post" style="padding: 5px;border-radius: 4px;border: 1px solid #ddd;">
        {% csrf_token %}
        <input name="next" type="hidden" value="{{ request.path }}" />
        
        <label for="language-select">{% trans "Language:" %}</label>
        <select name="language" id="language-select" onchange="this.form.submit()">
            {% for lang_code, lang_name in LANGUAGES %}
                <option value="{{ lang_code }}" 
                        {% if lang_code == LANGUAGE_CODE %}selected{% endif %}>
                    {{ lang_name }}
                </option>
            {% endfor %}
        </select>
    </form>
</div>
```

### 5.2 Add to Base Template
Include the language switcher in your base template or wherever you want it to appear:

```html
<!-- In your base.html or any template -->
{% include 'partials/language_switcher.html' %}
```


## Step 6: Adding and using new translations

When you need to add new translations:

- Add the new text to your templates using `{% trans %}` or `{% blocktrans %}`
- Run `python manage.py makemessages -l ne` to update the `.po` file
- Translate the new strings in the `.po` file
- Run `python manage.py compilemessages` to compile the translations



