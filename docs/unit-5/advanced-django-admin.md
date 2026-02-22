# Advanced Django Admin

## 🧘 The Zen of Admin {#the-zen-of-admin}

The Django Admin is a powerful internal operations tool:

* For internal staff, not end-users.
* Meant for data management, not complex workflows.
* Best for CRUD operations, not custom dashboards.

It is NOT:

* A public-facing interface.
* A replacement for a custom dashboard.
* Designed for heavy customization or complex UI.

Use Admin when:

* You need a quick internal tool for data management.
* Your staff is comfortable with a basic interface.
* You want to leverage Django's built-in features for admin tasks.

Build a custom dashboard when:

* UX matters
* Workflows are complex
* Role-specific interfaces are required

## 🎨 Customizing Admin Templates {#customizing-admin-templates}

Django allows template overrides.

Override `admin/base_site.html` to change the overall layout and branding.

**Steps**:

1. Create `templates/admin/base_site.html` in your app.
   
2. Extend the default template:
   
```html
{% extends "admin/base_site.html" %}

{% block title %}My Custom Admin{% endblock %}

{% block branding %}
<h1>My Company Admin</h1>
{% endblock %}
```

## ➕ Creating Custom Admin Views {#creating-custom-admin-views}

Extend AdminSite:

```python
from django.contrib.admin import AdminSite
from django.urls import path
from . import views
class MyAdminSite(AdminSite):

    site_header = "Custom Admin"

    def get_urls(self):
        urls = super().get_urls()
        custom_urls = [
            path('custom-view/', self.admin_view(views.custom_view), name='custom-view'),
        ]
        return custom_urls + urls
    
    def reports_view(self, request):
        # Custom view logic
        return HttpResponse("This is a custom admin view.")
```

## 🔄 Overriding Built-in Views {#overriding-built-in-views}

You can override:

* `add_view()`
* `change_view()`
* `changelist_view()`

Example: Customizing the change view

```python
from django.contrib import admin
from .models import MyModel
class MyModelAdmin(admin.ModelAdmin):
    def change_view(self, request, object_id, form_url='', extra_context=None):
        extra_context = extra_context or {}
        extra_context['custom_data'] = 'This is some custom data for the change view.'
        return super().change_view(request, object_id, form_url, extra_context=extra_context)
```
**Use Cases**

* Injecting additional context into the template.
* Altering form behavior based on the object being edited.
* Adding custom validation or processing logic before saving an object.
* Customizing the change list view to display additional information or actions.
* Modifying the add view to pre-populate fields or change the form layout.
