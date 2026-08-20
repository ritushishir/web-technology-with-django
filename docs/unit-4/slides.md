---
title: Unit 4 · Users and Session
---

# Unit 4 — Users, Caching and Subframework

---

## 1. Where We Are Going

So far: **handling data and displaying it**. From here: **human elements and infrastructure**.

Content on the modern web is

- **dynamic** and **personalized**
- and must be **protected** and **high-performing**

That is this unit: who the user is, what they may do, and how the site stays fast and safe.

---

## 2. The Web Is Stateless

HTTP is a **stateless** protocol — every request is independent and remembers nothing
about the last one.

So how does a shopping cart work? How does a site know you are logged in?

Django's answers: **cookies**, **sessions**, the **messages framework**, and
**context processors**.

→ [Session and state management](session-and-state-management.md)

---

## 3. How Sessions Work

1. A visitor arrives; Django creates a unique **session ID**
2. That ID is stored in a **cookie** in the browser
3. The actual **data stays on the server**, keyed by that ID
4. Every later request presents the cookie, and Django loads the matching data

!!! quote "The one-line version"

    The cookie holds the **key**; the server holds the **contents**.

---

## 4. Session Engines

`SESSION_ENGINE` decides where session data lives:

| Backend | Setting value | Trade-off |
|---|---|---|
| Database | `sessions.backends.db` | Default; reliable but hits the DB often |
| Cache | `sessions.backends.cache` | Very fast (Redis/Memcached); data lost if the cache clears |
| Cached DB | `sessions.backends.cached_db` | Best of both — reads from cache, writes to DB |
| Cookies | `sessions.backends.signed_cookies` | No server storage; **visible** to the user, though not editable |

```python title="settings.py"
SESSION_ENGINE = 'django.contrib.sessions.backends.db'
```

??? tip "Which backend for a bank? For a game leaderboard?"

    Bank → `cached_db` (durable, still fast). Leaderboard → `cache` (loss is acceptable).
    Never `signed_cookies` for anything the user should not see.

---

## 5. Using Sessions in Views

`request.session` behaves like a dictionary:

```python
request.session['key'] = 'value'                   # store
value = request.session.get('key', 'default')      # retrieve safely
del request.session['key']                         # delete
```

Works for **anonymous** visitors too — that is what makes guest carts possible.

---

## 6. Session Expiry

```python
request.session.set_expiry(300)    # 5 minutes
request.session.set_expiry(0)      # expires when the browser closes
request.session.set_expiry(None)   # use the global default
```

By default a session ends when the browser closes.

---

## 7. Session Security Settings

| Setting | Why it matters |
|---|---|
| `SESSION_COOKIE_AGE` | Lifetime in seconds; default is two weeks |
| `SESSION_EXPIRE_AT_BROWSER_CLOSE` | `True` kills the session when the browser closes |
| `SESSION_COOKIE_HTTPONLY` | Default `True` — hides the cookie from JavaScript, blunting XSS |
| `SESSION_COOKIE_SECURE` | Set `True` in production so the cookie only travels over HTTPS |

Also: **rotate session keys** and **invalidate sessions on logout** to prevent
session fixation attacks.

---

## 8. Other Ways to Carry State

```python title="Cookies — the browser holds the data"
response.set_cookie('cookie_name', 'cookie_value', max_age=3600)
cookie_value = request.COOKIES.get('cookie_name')
```

**URL parameters** — for simple, shareable state like pagination and filters:
`/songs/?page=2&genre=jazz` read via `request.GET.get('page')`

**Context processors** — for state every template needs, like the cart item count

**Messages framework** — one-time notifications that survive exactly one redirect

---

## 9. Authentication vs Authorization

Two different questions, constantly confused:

- **Authentication** — *who are you?*
- **Authorization** — *what are you allowed to do?*

Django ships a robust system for both.

→ [Authentication and authorization](authentication-and-authorization.md)

---

## 10. Users and Authentication

- The default **`User` model**: `username`, `password` (securely **hashed**), `email`,
  `first_name`, `last_name`
- **Login** — `auth.login(request, user)` creates the session
- **Logout** — `auth.logout(request)` ends it

!!! danger "Never store a raw password"

    Django hashes passwords for you. Any design that can show a user their old password
    is a broken design.

---

## 11. Permissions

Every model automatically gets **four** permissions: `add`, `change`, `delete`, `view`.

- Permissions are boolean flags assigned to users or groups
- You can add custom ones, e.g. `can_publish` for publishing song details
- Enforce them two ways:

```python
@permission_required('songs.change_song')
def edit_song(request): ...
```

```python
if request.user.has_perm('songs.change_song'):
    ...
```

---

## 12. Groups

A way to categorize users so permissions are not assigned one person at a time.

**Example:** create an **Editors** group → give it `can_edit_song` → add users to the group.

A user **inherits every permission** of every group they belong to.

??? question "30 new interns need edit rights. What do you touch?"

    The group, once — not forty user records.

---

## 13. The Messages Framework

Temporary, **one-time** notifications: stored on one request, displayed on the next
render — usually right after a redirect.

```python
from django.contrib import messages

def create_song(request):
    messages.success(request, "Song created successfully!")
    return redirect('home')
```

This is how "Login successful" survives a redirect without a query parameter.

---

## 14. Profiles — Extending User Data

Real apps need more than the default fields: a bio, an avatar, a phone number.

**1. Custom User Model (recommended for new projects)**

- Swap the default model out from the start
- Lets you use **email as the login identifier** instead of a username
- `AbstractUser` keeps the default fields; `AbstractBaseUser` gives total control

**2. Profile model (one-to-one link)**

- For projects already in flight where swapping the User model is painful
- `OneToOneField(User, on_delete=models.CASCADE)`
- Use a `post_save` **signal** to create the profile whenever a user registers

!!! warning "Timing matters"

    Switching the User model **after** migrations exist is genuinely painful. Decide on day one.

---

## 15. Why Cache?

Caching stores expensive computations in a fast layer, so thousands of simultaneous
requests do not each pay full price.

The framework offers four levels of granularity — **whole site**, **one view**,
**one fragment of data**, and **upstream proxies**.

→ [Setting up caching](setting-up-caching.md)

---

## 16. Choosing a Backend

```python title="settings.py"
CACHES = {
  'default': {
    'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
    'LOCATION': '127.0.0.1:11211',
  }
}
```

Options: **Memcached**, **Redis**, **database**, **local-memory**.

---

## 17. The Per-Site Cache

Cache the **entire site** by adding two middleware classes:

```python
MIDDLEWARE = [
  'django.middleware.cache.UpdateCacheMiddleware',    # first
  # ... other middleware ...
  'django.middleware.cache.FetchFromCacheMiddleware', # last
]
```

- `Update…` goes **first**, `Fetch…` goes **last** — the response is cached on the way out
- Blunt but effective for read-heavy, mostly-anonymous sites

---

## 18. The Per-View Cache

Cache only the views that actually hurt:

```python
from django.views.decorators.cache import cache_page

@cache_page(60 * 15)          # 15 minutes
def my_expensive_view(request):
    ...
```

Best for expensive pages that change rarely — reports, dashboards, archive listings.

---

## 19. The Low-Level Cache API

Maximum control: cache one computed value, not a whole page.

```python
from django.core.cache import cache

cache.set('my_key', 'my_value', timeout=300)
value = cache.get('my_key')
cache.delete('my_key')
```

Ideal when a page is mostly personal but one piece — a top-10 list, an exchange rate —
is shared and expensive.

---

## 20. Choosing a Caching Level

| Level | Granularity | Use when |
|---|---|---|
| Per-site | Everything | Mostly anonymous, read-heavy site |
| Per-view | One page | A few specific expensive views |
| Low-level API | One value | Page is personalized, one piece is shared |
| Upstream | Before Django runs | A proxy can serve it without touching Python |

??? question "A logged-in dashboard — which level, and why not per-site?"

    Low-level API. Per-site caching would happily serve one user's dashboard to another.

---

## 21. Upstream Caches and `Vary`

Proxies like **Nginx** or **Varnish** sit in front of Django. The `Vary` header tells them
to keep **separate copies** per request header:

```python
from django.views.decorators.vary import vary_on_headers

@vary_on_headers('User-Agent')
def my_view(request):
    ...
```

Common `Vary` keys: `Accept-Language`, `User-Agent`, `Cookie`.

---

## 22. Other Optimizations

Caching is not a substitute for **not making the query**:

```python
books = Book.objects.select_related('author').all()      # FK → one JOIN
authors = Author.objects.prefetch_related('books').all() # M2M/reverse → one extra query
```

Plus **database indexing** on the columns you filter and order by.

!!! tip "Order of operations"

    Fix the queries first, then cache. Caching a bad query only hides it.

---

## 23. Middleware — The Invisible Layer

Middleware processes **every request** before it reaches your view, and
**every response** before it leaves.

```python
MIDDLEWARE = [
  'django.middleware.security.SecurityMiddleware',
  'django.contrib.sessions.middleware.SessionMiddleware',      # before auth!
  'django.contrib.auth.middleware.AuthenticationMiddleware',
  # ... other middleware ...
]
```

→ [Middleware and security](middleware-and-security.md)

---

## 24. Why Order Matters

- Requests travel **down** the list; responses travel back **up**
- `SessionMiddleware` **must** come before `AuthenticationMiddleware` — authentication
  reads the session to identify the user

??? question "What happens if you swap those two lines?"

    Authentication runs with no session available, so `request.user` cannot be resolved —
    every visitor looks anonymous.

---

## 25. CSRF Protection

Django blocks **Cross-Site Request Forgery** through `CsrfViewMiddleware`, on by default.

```html
<form method="post">
  {% csrf_token %}
  <input type="submit" value="Submit">
</form>
```

Exempt a view only when you truly must (certain API endpoints):

```python
@csrf_exempt
def my_view(request):
    ...
```

!!! danger "`@csrf_exempt` is a loaded gun"

    You are removing a protection, not fixing an error. Know why before you type it.

---

## 26. Security Best Practices

- `SecurityMiddleware` for a batch of common hardening headers
- `SECURE_SSL_REDIRECT` to enforce HTTPS
- `SESSION_COOKIE_SECURE` and `CSRF_COOKIE_SECURE` so cookies never cross plain HTTP

*Unit 6 goes deeper into the attacks these settings defend against.*

---

## 27. The Django Standard Library

More **batteries already included** — the contrib apps, so you stop reinventing wheels.

Coverage in this unit: **Sites** · **Flatpages** · **Redirects** · **Humanize** · **Markup filters**

```python
INSTALLED_APPS = [
    'django.contrib.sites',
    'django.contrib.flatpages',
    'django.contrib.redirects',
    'django.contrib.humanize',
]
```

→ [The Django standard library](django-standard-library.md)

---

## 28. Sites

Run **multiple websites** — different domains — from one Django installation and one database.

**When to use:** a Pro site and a Free site, or a US site and a UK site, sharing backend
logic but differing in domain and settings.

```python title="settings.py"
SITE_ID = 1     # which site this instance represents
```

```python
current_site = get_current_site(request)
if current_site.domain == 'premium-site.com':
    ...
```

---

## 29. Flatpages

Static-ish content — *About Us*, *Privacy Policy* — managed from the **admin**, no CMS needed.

1. Add `django.contrib.flatpages` to `INSTALLED_APPS` (requires the **sites** app)
2. Add `FlatpageFallbackMiddleware` to `MIDDLEWARE`
3. Create the page in the admin — URL `/about/`, title *About Us*
4. Django renders it with `flatpages/default.html`

**The point:** content editors stop filing tickets for wording changes.

---

## 30. Redirects

**Problem:** you renamed `/old-contact/` to `/contact/` and do not want users hitting a 404.

**How it works:** on a 404, `RedirectFallbackMiddleware` checks the database for that old
path and, if found, redirects to the new one.

```python
MIDDLEWARE = [
    ...
    'django.contrib.redirects.middleware.RedirectFallbackMiddleware',
]
```

Old `/songs/2026/old-post/` → new `/songs/modern-post/` — the user never sees the 404,
and search rankings survive the rename.

---

## 31. Humanizing Data

Turn machine values into something a person reads comfortably:

```html
{{ some_date|naturaltime }}   <!-- "3 days ago" -->
{{ some_number|intcomma }}    <!-- "1,234,567" -->
```

---

## 32. Markup Filters

Process text formats straight in the template:

```html
{{ some_markdown_text|markdown }}
{{ some_restructuredtext|restructuredtext }}
{{ some_text|linebreaks }}
{{ some_text|linebreaksbr }}
```

Useful when users or editors submit content in Markdown rather than HTML.

---

## 33. Lab Checklist

- [ ] Store and read a value in `request.session`; watch the cookie in devtools
- [ ] Switch `SESSION_ENGINE` and observe where the data goes
- [ ] Wire up login/logout with the built-in auth views
- [ ] Create an **Editors** group, grant a permission, gate a view with `@permission_required`
- [ ] Add a success message that survives a redirect
- [ ] Add a `Profile` model with a `OneToOneField` and a `post_save` signal
- [ ] Cache one expensive view with `@cache_page`, then the same data with the low-level API
- [ ] Add `select_related` to a list view and count the queries before/after
- [ ] Enable flatpages and publish an *About Us* page from the admin
- [ ] Add a database redirect for a URL you renamed

---

## 34. Recap

- HTTP forgets; **sessions remember** — cookie holds the key, server holds the data
- **Authentication** is identity, **authorization** is permission; groups scale the latter
- Choose caching **granularity** to match how personalized the content is
- Fix the **queries** before you reach for the cache
- Middleware **order** is behavior, not style — sessions before authentication
- CSRF protection is on by default; exempting it is a deliberate risk
- Contrib apps — sites, flatpages, redirects, humanize — are free features

---

## 35. Exit Questions

??? question "What is actually stored in the session cookie?"

    Just the session ID (or, with `signed_cookies`, the signed data itself). Normally the
    real data stays server-side.

??? question "`login_required` vs `permission_required` — which is which?"

    `login_required` answers *authentication* (are you someone?);
    `permission_required` answers *authorization* (are you allowed?).

??? question "Why is per-site caching dangerous for logged-in pages?"

    A cached response can be served to a different user — one person's personalized page
    leaks to another.

??? question "You add `select_related('author')`. What changes in the SQL?"

    The author is fetched in the same query via a JOIN instead of one extra query per row.

??? question "A renamed URL is losing traffic. Which contrib app helps, and how?"

    `django.contrib.redirects` — on a 404 the middleware looks up the old path in the
    database and redirects to the new one.

---