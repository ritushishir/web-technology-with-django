# The Django Standard Library (Contrib Apps)

Django comes with a rich set of built-in applications, known as "contrib apps," that provide common functionalities needed in web development. These apps can be easily integrated into your Django projects to save time and effort.

## Sites (`django.contrib.sites`) {#sites}

The Sites framework allows you to manage multiple websites using a single Django installation. Each site can have its own domain name and content.

The Sites app allows you to run multiple websites (with different domains) from a single Django installation and a single database.

When to use: You have a "Pro" site and a "Free" site, or a "US" site and a "UK" site that share the same backend logic but need different domain names and settings.

Key Concept: The SITE_ID setting. In your settings.py, you define which site the current instance represents.

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.sites',
    ...
]
SITE_ID = 1  # This should match the ID of the site in the database
```
Using it in a View:
```python
    from django.contrib.sites.shortcuts import get_current_site

    def my_view(request):
        current_site = get_current_site(request)
        if current_site.domain == 'premium-site.com':
            # Show premium content
            pass
```

## Flatpages (`django.contrib.flatpages`) {#flatpages}

Flatpages is a simple way to manage static content on your site without needing a full CMS. It allows you to create and edit static pages through the Django admin interface.

* Creating and managing flatpages via the Admin.
* Linking flatpages to URLs in your project.


**Setup**: Requires the sites app mentioned above.

**Workflow**: 

 1. Add `django.contrib.flatpages` to INSTALLED_APPS. 
 2. Add `django.contrib.flatpages.middleware.FlatpageFallbackMiddleware` to MIDDLEWARE. 
 3. Create a page in the Admin (URL: `/about/`, Title: `About Us`). 
 4. Django will automatically render it using a `flatpages/default.html` template.

## Redirects (`django.contrib.redirects`) {#redirects}

The Redirects framework helps you manage URL redirections within your Django application. This is useful for maintaining SEO when URLs change or for redirecting old links to new ones.

  * **Problem**: You changed a URL from `/old-contact/` to `/contact/`, and you don't want users to see a 404 error.

  * **How it works**: When a user hits a page that doesn't exist (404), the `RedirectFallbackMiddleware` checks the database. If it finds a match for that "Old URL," it automatically redirects the user to the "New URL."

**Example**:

  * Old Path: `/songs/2026/old-post/`

  * New Path: `/songs/modern-post/`

  * Result: The user never sees the 404; they are seamlessly moved to the new page.

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',  # Must come before AuthenticationMiddleware
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.redirects.middleware.RedirectFallbackMiddleware',  # Enables Redirects framework
    # ... other middleware ...
]
```

## Humanizing Data (`django.contrib.humanize`) {#humanizing-data}

Django's `django.contrib.humanize` app provides template filters that help you present data in a more human-readable format. This includes formatting numbers, dates, and times in a way that is easier for users to understand.

* Using humanize filters in templates (e.g., `naturaltime`, `intcomma`).
* Examples of humanized data presentation.

```html
{{ some_date|naturaltime }}  <!-- e.g., "3 days ago" -->
{{ some_number|intcomma }}    <!-- e.g., "1,234,567" -->
```

## Markup Filters {#markup-filters}

Django provides several markup filters that allow you to process text in different formats directly within your templates. These filters can convert plain text into HTML using various markup languages.

* Common markup filters: `markdown`, `restructuredtext`, `linebreaks`, `linebreaksbr`.

```html
{{ some_markdown_text|markdown }}
{{ some_restructuredtext|restructuredtext }}
{{ some_text|linebreaks }}
{{ some_text|linebreaksbr }}
```

* How to enable and use these filters in your templates.
* Examples of using markup filters for content formatting.

```python
INSTALLED_APPS = [
    # ... other installed apps ...
    'django.contrib.sites',
    'django.contrib.flatpages',
    'django.contrib.redirects',
    'django.contrib.humanize',
    # ... other installed apps ...
]
```
