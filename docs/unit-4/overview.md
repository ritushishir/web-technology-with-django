# Django Security, State, and Performance

---

## 1. Sessions and State Management

### [Django’s Session Framework](./session-and-state-management.md#session-framework)

* Configuring session engines (Database, Cache, File-based, or Cookie-based).
* How to store and retrieve persistent data for anonymous and logged-in users.

---

## 2. Authentication and Authorization

### [Users and Authentication](./authentication-and-authorization.md#users-and-authentication)

* Managing user logins, logouts, and password hashing.
* Using the built-in `User` model.

### [Permissions](./authentication-and-authorization.md#permissions)

Restricting access to views based on user rights

### [Groups](./authentication-and-authorization.md#groups)

Organizing users for bulk permission management

### [Messages](./authentication-and-authorization.md#messages)

Using the Messages Framework for one-time notifications (e.g., "Login Successful").

### [Profiles](./authentication-and-authorization.md#profiles)

Extending user data with Custom User Models or Profile linked models.

---

## 3. The Caching Framework

### [Setting Up the Cache](./setting-up-caching.md#setting-up-the-cache)

* Choosing a backend: Memcached, Redis, Database, or Local-memory.

### [The Per-Site Cache](./setting-up-caching.md#per-site-cache)

* Enabling the "fetch" and "update" middleware to cache the entire website automatically.

### [The Per-View Cache](./setting-up-caching.md#per-view-cache)

* Using the `@cache_page` decorator to cache specific, expensive views.

### [The Low-Level Cache API](./setting-up-caching.md#low-level-cache-api)

* Manually setting, getting, and deleting keys in the cache for granular control.

### [Upstream Caches](./setting-up-caching.md#upstream-caches)

* Working with Nginx/Varnish and the `Vary` header for proxy caching.

### [Other Optimizations](./setting-up-caching.md#other-optimizations)

* Database indexing, `select_related`, and `prefetch_related` to reduce query overhead.

---

## 4. Middleware and Security

### [Order of Middleware Classes](./middleware-and-security.md#order-of-middleware-classes)

* Why order matters (e.g., Session before Authentication).
* How request and response objects flow through the stack.

### [Security Best Practices](./middleware-and-security.md#security-best-practices)

### [CSRF Protection](./middleware-and-security.md#csrf-protection)

* How Django prevents Cross-Site Request Forgery.
* Using the `{% csrf_token %}` in forms and the `@csrf_exempt` decorator.

---

## 5. The Django Standard Library (Contrib Apps)

### [Sites](./django-standard-library.md#sites)

* Managing multiple websites from a single Django installation.

### [Flatpages](./django-standard-library.md#flatpages)

* Creating simple, static-like pages (e.g., "About Us", "Privacy Policy") via the Admin interface.

### [Redirects](./django-standard-library.md#redirects)

* Managing 301 and 302 redirects within the database.

### [Humanizing Data](./django-standard-library.md#humanizing-data)

* Using `django.contrib.humanize` to turn numbers/dates into readable strings (e.g., "3 days ago", "1.2 million").

### [Markup Filters](./django-standard-library.md#markup-filters)

* Processing text as Markdown, ReStructuredText, or other formats within templates.
