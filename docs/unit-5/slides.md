---
title: Unit 5 · Middleware
---

# Unit 5 — Middleware, Legacy Databases and Admin Interface

---

## 1. Where We Are Going

Unit 4 *used* middleware and the admin. This unit **opens both up** — plus the reality of
enterprise work: the database was there before you were.

- Middleware as a hook system you can write into
- Django on top of a schema you do not control
- The admin, pushed as far as it should go — and no further

---

## 2. The Request–Response Lifecycle

Without middleware:

```
Client → URL Resolver → View → Template → Response
```

With middleware:

```
Client → Middleware → URL Resolver → View → Template → Middleware → Response
```

Middleware sits at the heart of the pipeline, intercepting and modifying at various stages.

→ [Middleware overview](middleware-architecture.md#middleware-overview)

---

## 3. What Middleware Can Touch

- **Incoming requests** — modify the request object, authenticate, handle sessions
- **View execution** — act before the view runs, or change the response after
- **Exceptions** — catch and handle errors raised during view processing
- **Outgoing responses** — modify the response, add headers, compress content

---

## 4. Middleware as a Hook System

!!! quote "Definition worth writing down"

    A **hook** is a predefined interception point in execution where custom logic can run.

Django's hook points:

| Hook | When it fires |
|---|---|
| `__init__(self, get_response)` | Once at server start — set up state |
| `__call__(self, request)` | The main entry point for every request/response |
| Before the view | Modify the request, or short-circuit with a response |
| After the view | Modify the response before it goes out |
| `process_exception(request, exception)` | The view raised something |
| `process_template_response(request, response)` | The view returned a `TemplateResponse` |

---

## 5. Execution Order — The Onion

```
Client
  ↓
[M1]
  ↓
[M2]
  ↓
 View
  ↑
[M2]
  ↑
[M1]
  ↑
Client
```

**Requests go down the list. Responses come back up.**
The first middleware listed is the outermost layer — first to see the request, last to
touch the response.

---

## 6. Checkpoint — The Pipeline

??? question "M1 logs requests, M2 blocks unauthorized users. Which order?"

    M1 first (outermost) so it logs *everything*, including what M2 rejects. Reverse them
    and blocked requests never appear in the log.

---

## 7. Installation

```python title="settings.py"
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.gzip.GZipMiddleware',
    'myapp.middleware.CustomMiddleware',
]
```

Order is **top-down for requests, bottom-up for responses**:

- Request flow: `Security → Common → Custom → View`
- Response flow: `View → Custom → Common → Security`

→ [Middleware installation](middleware-architecture.md#middleware-installation)

---

## 8. The Basic Structure

Every modern middleware is this shape:

```python
class CustomMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response      # runs once, at startup

    def __call__(self, request):
        # ---- before the view ----
        response = self.get_response(request)  # everything inside runs here
        # ---- after the view ----
        return response
```

`self.get_response(request)` **is** the rest of the stack plus your view. Code above it
sees the request; code below it sees the response.

---

## 9. Advanced Hook: `process_view`

```python
def process_view(self, request, view_func, view_args, view_kwargs):
    ...
```

Runs **before the view executes**, but after URL resolution — so you know *which* view
was chosen.

**Use cases:** logging · access control · modifying view arguments

---

## 10. Advanced Hook: `process_exception`

```python
def process_exception(self, request, exception):
    ...
```

Runs when the view **raises**.

**Use cases:** custom error handling · reporting exceptions to a monitoring service

*This is how Sentry-style tooling plugs into Django.*

---

## 11. Advanced Hook: `process_template_response`

```python
def process_template_response(self, request, response):
    ...
```

Runs when the view returns a **`TemplateResponse`** — before rendering.

**Use cases:** modify context data before rendering · swap the template

??? question "Why must this be a `TemplateResponse`, not a normal one?"

    A plain `HttpResponse` is already rendered — the string is final. `TemplateResponse`
    keeps the template and context separate until later, which is what leaves room to edit.

→ [Middleware methods](middleware-architecture.md#middleware-methods)

---

## 12. `SecurityMiddleware`

**Purpose:** enforces security best practices. Critical in production.

- HTTPS redirect (`SECURE_SSL_REDIRECT`)
- HSTS headers
- Secure cookies
- XSS protection

---

## 13. `CommonMiddleware`

Handles the unglamorous HTTP correctness work:

- **URL normalization** (`APPEND_SLASH`) — `/songs` quietly becomes `/songs/`
- **ETag** handling
- **Conditional GET** support

Improves consistency and HTTP compliance.

??? tip "Who fixed your missing trailing slash in Unit 1?"

    `CommonMiddleware`, via `APPEND_SLASH` — it was working invisibly the whole time.

---

## 14. `GZipMiddleware`

Compresses responses larger than **200 bytes**.

**Benefits:** reduced bandwidth · faster client load times

!!! warning "Caution"

    Do not compress already-compressed data — images, PDFs, ZIPs. You spend CPU and
    gain nothing.

→ [Built-in middleware](middleware-architecture.md#built-in-middleware)

---

## 15. The Enterprise Reality

Enterprise environments frequently require Django to **coexist with pre-existing systems** —
legacy databases, legacy auth, legacy web apps.

You rarely get a green field. You get a schema someone else owns.

→ [Legacy systems and integration](legacy-system-and-integration.md)

---

## 16. `inspectdb`

Generate models **from an existing schema** instead of writing them by hand:

```bash
python manage.py inspectdb > models.py
```

A management command that inspects the database and emits Django model definitions.

**Treat the output as a first draft** — review names, types, and relationships before use.

---

## 17. Non-Standard Schemas

What legacy databases throw at you:

- Non-integer primary keys
- Composite keys
- Missing constraints
- Unconventional naming

```python
class LegacyUser(models.Model):
    user_code = models.CharField(primary_key=True, max_length=20)

    class Meta:
        managed = False
        db_table = 'legacy_songs'
```

---

## 18. `managed = False`

It means: **migrations keep their hands off this table.**

Django will **not** create it, **not** modify it, **not** delete it.

Use when:

- The database is controlled externally
- The schema must not change

??? question "What still works with `managed = False`?"

    Everything about *reading and writing rows* — the ORM, queries, the admin.
    Only schema management is switched off.

---

## 19. Pluggable Authentication Backends

Django's auth is pluggable — **LDAP**, **OAuth2**, **remote header authentication (SSO)**.

```python
class CustomAuthBackend(BaseBackend):
    def authenticate(self, request, username=None, password=None):
        # external verification logic
        return user_object
```

```python title="settings.py"
AUTHENTICATION_BACKENDS = [
    'myapp.backends.CustomAuthBackend',
]
```

Django tries each backend in order until one returns a user.

---

## 20. Coexisting with Legacy Web Apps

**1. Reverse proxy** — Nginx or Apache routes by path:

```
/app/     → Django
/legacy/  → the old application
```

**2. Shared session management** — a common session store (e.g. **Redis**) so authentication
state is visible to both sides.

This is how migrations happen in practice: **path by path**, not big-bang rewrites.

---

## 21. Checkpoint — Integration

??? question "You must not alter a production table but need Django to read it. What do you set?"

    `managed = False` plus an explicit `db_table` in the model's `Meta`.

??? question "Staff already log in through corporate SSO. Where does that plug in?"

    A custom authentication backend listed in `AUTHENTICATION_BACKENDS`.

---

## 22. The Zen of Admin

The admin is a powerful **internal operations tool**:

- For internal **staff**, not end-users
- For **data management**, not complex workflows
- For **CRUD**, not custom dashboards

It is **not** a public-facing interface, not a replacement for a real dashboard, and not
built for heavy UI customization.

→ [Advanced Django admin](advanced-django-admin.md)

---

## 23. Admin or Custom Dashboard?

| Use the Admin when | Build a custom dashboard when |
|---|---|
| You need a quick internal data tool | **UX matters** |
| Staff are comfortable with a basic interface | Workflows are complex |
| You want Django's built-in features for free | Role-specific interfaces are required |

!!! tip "The trap to name out loud"

    Teams keep bending the admin until it is a bad version of the app they should have
    written. Notice the moment you cross that line.

---

## 24. Customizing Admin Templates

Override `admin/base_site.html` for layout and branding:

1. Create `templates/admin/base_site.html` in your app
2. Extend the default and replace only the blocks you care about:

```html
{% extends "admin/base_site.html" %}

{% block title %}My Custom Admin{% endblock %}

{% block branding %}
<h1>My Company Admin</h1>
{% endblock %}
```

Same inheritance mechanics as Unit 1 — applied to Django's own templates.

---

## 25. Creating Custom Admin Views

Add **entirely new pages** by extending `AdminSite`:

```python
class MyAdminSite(AdminSite):
    site_header = "Custom Admin"

    def get_urls(self):
        urls = super().get_urls()
        custom_urls = [
            path('custom-view/', self.admin_view(views.custom_view), name='custom-view'),
        ]
        return custom_urls + urls
```

Two details that matter:

- **`self.admin_view(...)`** wraps the view with the admin's permission checks
- **`custom_urls + urls`** puts yours first, so they are matched before the catch-all patterns

---

## 26. Overriding Built-in Views

You can override `add_view()`, `change_view()`, and `changelist_view()`:

```python
class MyModelAdmin(admin.ModelAdmin):
    def change_view(self, request, object_id, form_url='', extra_context=None):
        extra_context = extra_context or {}
        extra_context['custom_data'] = 'This is some custom data for the change view.'
        return super().change_view(request, object_id, form_url,
                                   extra_context=extra_context)
```

**Use cases:** injecting extra context · altering form behavior for the edited object ·
custom validation before save · richer change lists · pre-populating the add form

---

## 27. Checkpoint — Admin

??? question "Non-technical staff need a guided three-step approval flow. Admin or not?"

    Not the admin. That is a workflow, and the admin models CRUD. Build the dashboard.

??? question "Why wrap a custom admin view in `self.admin_view()`?"

    Without it the page skips the admin's authentication and permission checks — an
    internal tool exposed to anyone who knows the URL.

---

## 28. Lab Checklist

- [ ] Write a `CustomMiddleware` that logs the path and duration of every request
- [ ] Add `process_view` to log which view was selected
- [ ] Add `process_exception` that catches an intentional error
- [ ] Reorder two middleware classes and observe the behavior change
- [ ] Enable `GZipMiddleware` and compare response sizes
- [ ] Point `inspectdb` at an existing database and read the generated models
- [ ] Mark one model `managed = False` with an explicit `db_table`, then query it
- [ ] Write a trivial custom authentication backend and register it
- [ ] Override `admin/base_site.html` with your own branding
- [ ] Add one custom admin view through `AdminSite.get_urls()`

---

## 29. Recap

- Middleware is a **hook system**: requests descend the list, responses climb back up
- `self.get_response(request)` is the dividing line between "before" and "after"
- `process_view`, `process_exception`, `process_template_response` cover the specialized moments
- Built-in middleware is doing real work already — security, `APPEND_SLASH`, compression
- `inspectdb` + `managed = False` let Django live on a schema it does not own
- Authentication is **pluggable**; legacy apps coexist behind a reverse proxy
- The admin is for **staff CRUD** — know when to stop customizing and start building

---

## 30. Exit Questions

??? question "Where does 'before the view' code go in a modern middleware class?"

    In `__call__`, above the `self.get_response(request)` line.

??? question "Which built-in middleware turns `/songs` into `/songs/`?"

    `CommonMiddleware`, via the `APPEND_SLASH` setting.

??? question "Why not gzip a PDF response?"

    It is already compressed — you burn CPU for essentially no size reduction.

??? question "`inspectdb` produced a model with a `CharField` primary key. Is that wrong?"

    No — legacy schemas often use non-integer keys. Keep it, and add `managed = False` if
    Django must not alter the table.

??? question "One sentence: when should you *not* use the Django admin?"

    When the interface is for end-users, or the task is a workflow rather than
    record-by-record data management.

---