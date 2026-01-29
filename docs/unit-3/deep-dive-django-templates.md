# Deep Dive - Django Template System

---

## Template Language Review {#template-language-review}

* Quick refresh on [Tags, Filters, and Variables](/docs/unit-1/project-setup#basic-template-tags-and-filters).
* [Inheritance](/docs/unit-1/project-setup#template-inheritance) strategies (`{% extends %}` and `{% block %}`) .

---

## Request Context and Context Processors {#request-context-and-context-processors}

* Understanding `RequestContext` vs. `Context`.

When rendering a template, Django uses a `Context` object to hold the variables that will be available in the template.

A `RequestContext` is a subclass of `Context` that requires the `HttpRequest` object. It automatically executes Context Processors.

* Configuring built-in and custom context processors to make data available globally across templates.

**Problem**: You want to display app-specific information (like appname) in the navigation bar of your website.

**Solution**: Use the built-in `site_setting` context processor, which adds the `site_name` variable to the template context.

Step 1: Create a file named `context_processors.py` inside your app directory.

```python
# songs/context_processors.py
def app_settings(request):
    return {
        'site_name': 'Geetshala',
    }
```


Step 2: Add the context processor to your `TEMPLATES` setting in `settings.py`.

```python
# settings.py
TEMPLATES = [
    {
        ...,
        'OPTIONS': {
            'context_processors': [
                ...,
                'songs.context_processors.app_settings',
            ],
        },
    },
]
```

Step 3: Use the `site_name` variable in your templates.

```html
<!-- base.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{{ site_name }}</title>
</head>
<body>
.....
</body>
</html>
```

??? tip "Tip"
    Check out the [Django documentation on context processors](https://docs.djangoproject.com/en/stable/ref/templates/api/#django.template.RequestContext) for more built-in options and examples.

---

## Inside Template Loading {#inside-template-loading}

* How Django finds your HTML files?
    * The `DIRS` and `APP_DIRS` configurations.
    * `DIRS`: List of directories to search for templates.
    * `APP_DIRS`: If `True`, Django will look for a `templates` subdirectory in each installed application.
    * **Search Order**: The order of search: `DIRS` first, then each app's `templates` directory in the order listed in `INSTALLED_APPS`.

---

## Extending the Template System {#extending-the-template-system}

When built-in tags and filters are not sufficient, you can extend the template system by creating custom tags and filters.

### Custom Template Filters {#custom-template-filters}

Essentially functions that take a value, process it, and return a new value to be displayed in the template.

```python
@register.filter
def bullet_points(values):
    """Converts a list of items into a bullet-pointed list."""
    if not values:
        return ""
    return "<ul>" + "".join(f"<li>{item}</li>" for item in value) + "</ul>"
```
You can use this filter in your template as follows:

```html
{{ ["Django", "Rails", "Flask"]|bullet_points }}
```

### Custom Tags {#custom-tags}

#### Simple Tags `simple_tag` {#simple-tags}

Processes data and returns a value to be rendered in the template.

```python
from django import template
register = template.Library()
@register.simple_tag

def multiply(a, b):
    return a * b
```
You can use this tag in your template as follows:

```html
The result of multiplication is:
{% multiply 3 4 %}
```

#### Inclusion Tags `inclusion_tag` {#inclusion-tags}
Processes data and renders a specified template with that data.

```python
from django import template
register = template.Library()
@register.inclusion_tag('songs/song_list.html')
def show_songs(songs):
    return {'songs': songs}
```

You can use this tag in your template as follows:

```html
    {% show_songs song_list %}
```

### Registering Custom Tags and Filters {#registering-custom-tags-and-filters}

* Creating a `templatetags` directory inside your app.
* Adding an `__init__.py` file to make it a package.
* Creating Python modules for your custom tags and filters.
* Registering the custom tags and filters in the template system.

### Loading Custom Tags and Filters in Templates {#loading-custom-tags-and-filters-in-templates}
* Using the `{% load %}` tag to load your custom tags and filters in templates.

```html
{% load your_custom_tags %}
```

## Writing Custom Template Loaders {#writing-custom-template-loaders}

* Loading templates from non-standard sources (e.g., Database or API).

## Using the Built-in Template Reference {#using-the-built-in-template-reference}

Django includes a "self-documenting" feature for its template system. If you add the URL pattern for the admin documentation, you can access detailed information about template tags and filters directly from your browser.

1. Add `'django.contrib.admindocs'` to your `INSTALLED_APPS` 
2. Include the following in your `urls.py`:

    ```python
    from django.contrib import admin
    from django.urls import path, include
    urlpatterns = [
        path('admin/doc/', include('django.contrib.admindocs.urls')),
        path('admin/', admin.site.urls),
    ]
    ```


## Configuring the Template System in Standalone Mode {#configuring-the-template-system-in-standalone-mode}

- Standalone Mode: scripts or applications that are not part of a full Django project.
  - automated email generation
  - report generation
  - other templating needs outside of web requests

- power of Django's templating engine without the overhead of a full Django setup.

```python
import django
from django.conf import settings
from django.template import Context, Template, Engine

# Configure settings for standalone usage
# acts as settings.py in a full Django project
settings.configure(
    TEMPLATES=[
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',  #
            'DIRS': ['path/to/your/templates'], # Specify your template directories
            'APP_DIRS': False, # Disable app directories in standalone mode
        },
    ]
)

# Initialize Django
django.setup()

# Create an instance of the template engine
# uses the Loader system
engine = Engine.get_default()

# Load a template
template = engine.get_template('your_template.html')

# Create a context with data
context = Context({'key': 'value'})

# Render the template with the context
rendered_output = template.render(context)
print(rendered_output)
```