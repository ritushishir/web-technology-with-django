# Unit 6 — Internationalization, Security and Deployment · Class Slides

### Session Plan (8 Hrs.)

| # | Block | Slides | Approx. |
|---|---|---|---|
| 1 | i18n vs l10n · translation strings in Python and templates | 1–7 | 1.5 hr |
| 2 | `.po`/`.mo` files · language discovery · the switcher | 8–13 | 1.5 hr |
| 3 | Defense in depth · settings layers · security headers | 14–19 | 1.5 hr |
| 4 | The vulnerability tour: SQLi, XSS, CSRF, sessions, email, traversal | 20–27 | 1.5 hr |
| 5 | Shared nothing · settings per environment · WSGI servers | 28–33 | 1.5 hr |
| 6 | Scaling · monitoring · backup and recovery | 34–39 | 0.5 hr |

---

## 1. Scaling for the Real World

Until now the project lived in a **controlled environment**: a local server, one language,
and you as the primary user.

This unit is about the **three pillars of professional deployment**:

- **Empathy (i18n/l10n)** — the app feels local to anyone, anywhere; no language barriers
- **Integrity (Security)** — one vulnerability must not compromise the whole system
- **Resilience (Architecture)** — growth handled gracefully; more users ≠ more headaches

→ [Unit overview](overview.md)

---

## 2. i18n vs l10n

| Term | Meaning |
|---|---|
| **Internationalization (i18n)** | *Designing and preparing* the app to support multiple languages |
| **Localization (l10n)** | *Adapting* the app to a specific language or region |

i18n is the developer's job, done once. l10n happens again for every locale you add.

→ [Internationalization and localization](internationalization-and-localization.md)

---

## 3. Translation Strings in Python

```python
from django.utils.translation import gettext as _, gettext_lazy as _l

title = _('Welcome to our website')          # translated immediately

class Song(models.Model):                     # translated when accessed
    title = models.CharField(max_length=100, verbose_name=_l('Title'))
```

| Function | Behavior |
|---|---|
| `gettext()` | Translates immediately when called |
| `gettext_lazy()` | Returns a proxy that translates **when accessed** |

**Use lazy** in model definitions, form fields, and anywhere the string is evaluated
**before a request exists**.

??? question "Ask the class: why does a model field need the lazy version?"

    Models load at import time — before any request, so before any language is active.
    Eager translation would freeze whichever language happened to be default at startup.

---

## 4. Translation Strings in Templates

```html
{% load i18n %}

<h1>{% trans "Welcome to our site" %}</h1>
<p>{% trans "Welcome" context "greeting" %}</p>

{% blocktrans %}
Hello {{ user.name }}, you have {{ count }} new messages.
{% endblocktrans %}
```

**Rule of thumb:** `{% trans %}` for simple strings, `{% blocktrans %}` for content
containing variables.

---

## 5. Pluralization and Variables

```html title="Plural forms"
{% blocktrans count count=cart.items.count %}
You have {{ count }} item in your cart.
{% plural %}
You have {{ count }} items in your cart.
{% endblocktrans %}
```

```html title="Renaming a variable for translators"
{% blocktrans with product_name=product.name %}
Product: {{ product_name }}
{% endblocktrans %}
```

```html title="Language info and localized dates"
{% get_current_language as LANGUAGE_CODE %}
{% get_available_languages as LANGUAGES %}
{% localdate appointment.date %}
```

!!! tip "Never build a sentence by concatenation"

    Word order differs by language. Hand the translator the **whole sentence** with
    placeholders, which is exactly what `blocktrans` is for.

---

## 6. Enabling i18n

```python title="settings.py"
USE_I18N = True
USE_L10N = True
USE_TZ = True

LANGUAGE_CODE = 'en-us'
LANGUAGES = [('en', 'English'), ('ne', 'Nepali')]
LOCALE_PATHS = [BASE_DIR / 'locale/']
```

```python title="MIDDLEWARE — position is not optional"
'django.contrib.sessions.middleware.SessionMiddleware',
'django.middleware.locale.LocaleMiddleware',      # after Session
'django.middleware.common.CommonMiddleware',      # before Common
```

→ [Step-by-step guide](internationalization-localization-steps.md)

---

## 7. Checkpoint — Marking Strings

??? question "You wrapped every string in `_()` and nothing translated. What is missing?"

    Marking is only step one. You still have to run `makemessages`, translate the `.po`,
    run `compilemessages`, and restart.

---

## 8. `.po` and `.mo` Files

Django uses GNU gettext's format.

**`.po` (Portable Object)** — human-readable, edited by translators:

```po
#: templates/base.html:10
msgid "Welcome"
msgstr "Bienvenue"

#, python-format
msgid "There is %(count)s item"
msgid_plural "There are %(count)s items"
msgstr[0] "Il y a %(count)s élément"
msgstr[1] "Il y a %(count)s éléments"
```

**`.mo` (Machine Object)** — compiled binary, optimized for fast runtime lookup,
generated automatically, **never edited by hand**.

---

## 9. The Translation Workflow

```bash
python manage.py makemessages -l ne        # extract strings → .po
# ... translate the .po file ...
python manage.py compilemessages           # .po → .mo
# ... restart the application ...
```

```
locale/
└── ne/
    └── LC_MESSAGES/
        ├── django.po      # Python + template strings
        ├── django.mo
        ├── djangojs.po    # JavaScript strings
        └── djangojs.mo
```

JavaScript strings are a **separate domain**:
`makemessages -l ne --domain=djangojs`

---

## 10. Language Preference Discovery

Django resolves the language in **priority order**:

1. **URL prefix** — via `i18n_patterns`, giving `/ne/home/`, `/fr/accueil/`
2. **Language cookie** — `django_language`, set by `set_language`; persists across sessions
3. **Session key** — falls back when no cookie is present
4. **`Accept-Language` header** — the browser's preference, parsed by quality values
   (`es-ES,es;q=0.9,en;q=0.8`)
5. **`LANGUAGE_CODE`** — the final fallback

```python
urlpatterns = [path('i18n/', include('django.conf.urls.i18n'))]
urlpatterns += i18n_patterns(
    path('', views.home, name='home'),
    prefix_default_language=False,
)
```

---

## 11. What `LocaleMiddleware` Does

1. **Language detection** — evaluates preferences in priority order, validates them against
   `LANGUAGES`, sets `request.LANGUAGE_CODE`
2. **Translation activation** — activates the language for the current thread so translation
   functions return the right strings; handles timezone and locale formatting
3. **Context processing** — exposes `LANGUAGES`, `LANGUAGE_CODE`, `LANGUAGE_BIDI` to templates

*`LANGUAGE_BIDI` is how right-to-left layouts (Arabic, Hebrew) get handled.*

---

## 12. The `set_language` Redirect View

A built-in view for manual switching — wire it up and build the form:

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

The hidden `next` field is what returns the user to the page they were reading.

---

## 13. Adding Translations Later

Every time you add new text:

1. Wrap it in `{% trans %}` / `{% blocktrans %}` / `_()`
2. `makemessages -l ne` to update the `.po`
3. Translate the new entries
4. `compilemessages`
5. Restart

Skipping step 4 is why "half the page is translated" bugs happen.

---

## 14. Defense in Depth

!!! quote "The rule"

    **Never rely on a single line of defense.**

Layered security means controls at **every layer** of the stack:

1. **Multiple layers** — if one control fails, others still protect
2. **Diverse controls** — authentication, authorization, input validation, headers
3. **Redundancy** — critical functions have backups
4. **Fail-safe defaults** — systems default to the *secure* configuration

→ [Advanced web security](advanced-web-security.md)

---

## 15. Layer 1 — Environment

```python
DEBUG = False
ALLOWED_HOSTS = ['example.com', 'www.example.com']
```

Two lines, and they carry more weight than any library you could add.

---

## 16. Layer 2 — Transport Security

Protecting data **in motion** between browser and server. Without it, data travels the
open internet **like a postcard** — readable, and alterable in transit.

Three goals:

1. **Encryption** — unreadable to anyone intercepting
2. **Authentication** — it really came from your server
3. **Integrity** — it was not tampered with

```python
SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True
```

---

## 17. Layers 3 and 4 — Forms and Sessions

```python title="CSRF & form integrity — identity verification"
CSRF_COOKIE_SECURE = True          # token only over HTTPS
CSRF_COOKIE_HTTPONLY = True        # JavaScript cannot read it
CSRF_TRUSTED_ORIGINS = ['https://example.com']
```

```python title="Session & cookie hardening — key management"
SESSION_COOKIE_SECURE = True
SESSION_COOKIE_HTTPONLY = True
SESSION_COOKIE_SAMESITE = 'Strict'
SESSION_COOKIE_AGE = 3600          # 1 hour
```

---

## 18. Layers 5 and 6 — Auth and Database

```python title="Password policy — the vault door"
AUTH_PASSWORD_VALIDATORS = [
    {'NAME': '...MinimumLengthValidator', 'OPTIONS': {'min_length': 12}},
    {'NAME': '...CommonPasswordValidator'},
    {'NAME': '...NumericPasswordValidator'},
]
```

```python title="Database — secrets from the environment, SSL enforced"
'PASSWORD': os.environ.get('DB_PASSWORD'),   # never hardcode
'OPTIONS': {'sslmode': 'require'},
```

---

## 19. Security Headers

Headers are **front-gate instructions** — response metadata telling the browser how to
behave: *"I am a secure site; here are the rules while you are here."*

```python
class SecurityHeadersMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)
        response['Content-Security-Policy'] = (
            "default-src 'self'; script-src 'self' https://cdn.example.com; "
            "frame-ancestors 'none'; form-action 'self'")
        response['X-Content-Type-Options'] = 'nosniff'
        response['X-Frame-Options'] = 'DENY'
        response['Referrer-Policy'] = 'strict-origin-when-cross-origin'
        response['Permissions-Policy'] = 'geolocation=(), microphone=(), camera=()'
        return response
```

Note the shape — this is exactly the Unit 5 middleware pattern, put to work.

---

## 20. SQL Injection

Malicious SQL inserted into input fields to manipulate queries.

**How the ORM protects you:** query **parameterization** · automatic escaping of input ·
strong **typing**

```python title="✅ Safe"
Song.objects.filter(artist_id=artist_id, status=status)
Song.objects.filter(Q(title__icontains=query) | Q(content__icontains=query))
Song.objects.filter(id=song_id).update(views=F('views') + 1)
Song.objects.raw("SELECT ... WHERE created_at BETWEEN %s AND %s", [start, end])
```

```python title="❌ Dangerous — never do this"
query = f"SELECT * FROM songs WHERE artist_id = {artist_id}"
return Song.objects.raw(query)
```

**The lesson:** `raw()` is not the danger — **string formatting** is.

---

## 21. XSS — Cross-Site Scripting

Malicious scripts injected into pages that **other users** then view.

Django's protections:

1. **Auto-escaping** — every template variable is HTML-escaped by default
2. **Context-aware escaping** — different rules for HTML, CSS, JavaScript
3. **Safe string handling** — safe content must be marked *explicitly*
4. **Content Security Policy** — the browser only trusts sources you name

!!! danger "`|safe` is a promise you are making"

    Every use says "I guarantee this contains no hostile markup." If it came from a user,
    you cannot make that promise.

---

## 22. CSRF — Cross-Site Request Forgery

Forces an **authenticated** user to submit an action they never intended.

Django's mechanisms: **CSRF tokens** per session · **SameSite cookies** ·
**origin validation** · **double-submit cookie**

```html
<form method="post" action="{% url 'create_song' %}">
    {% csrf_token %}
</form>
```

```js title="AJAX — the token rides in a header"
const csrfToken = getCookie('csrftoken');
fetch(url, {method: 'POST', headers: {'X-CSRFToken': csrfToken}, body: ...});
```

```python title="Advanced configuration"
CSRF_USE_SESSIONS = True       # store the token in the session, not a cookie
CSRF_COOKIE_SECURE = True
CSRF_TRUSTED_ORIGINS = ['https://example.com']
CSRF_FAILURE_VIEW = 'myapp.views.csrf_failure'
```

---

## 23. Session Forging and Hijacking

An attacker **steals or forges** a session cookie to impersonate a real user.

```python
SESSION_COOKIE_SECURE = True
SESSION_COOKIE_HTTPONLY = True
SESSION_COOKIE_SAMESITE = 'Strict'
SESSION_COOKIE_AGE = 3600
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
SESSION_SAVE_EVERY_REQUEST = True
```

**Detection and response:** IP / user-agent binding · **session rotation** on sensitive
actions · timeouts and invalidation · invalidation on logout

**Enhanced auth views:** extra checks at login · rate-limited password reset ·
account lockout after failed attempts

---

## 24. Email Header Injection

An attacker adds extra headers — `From:`, `To:`, `Cc:`, `Bcc:` — to your contact form,
turning your server into a **spam bot**.

**Prevention:**

- Validate and sanitize all email headers
- Use Django's `send_mail()`, which sanitizes headers for you
- Reject submissions with suspicious headers

**Rate limiting** matters here too — unthrottled mail forms invite email bombing and DoS.
`django-ratelimit` is the usual tool.

---

## 25. Directory Traversal

Attacks that reach **outside the intended directory** — the classic `../../etc/passwd`.

Where it bites in Django:

- **File uploads** — never trust a user-supplied filename
- **Media serving** — never join user input straight onto a filesystem path
- **URL configuration** — do not let a URL parameter select an arbitrary file

*Rule: validate and normalize the path, then verify the result is still inside the
directory you intended.*

---

## 26. Exposed Error Messages

What `DEBUG = True` in production hands an attacker:

1. **Stack traces** — file paths and code snippets
2. **Configuration** — database credentials, API keys, the secret key
3. **Environment** — server details, Python version, installed packages
4. **SQL queries** — statements and their parameters
5. **Local variables** — internal application state

Custom **404 and 500 pages** give better UX *and* stop the leak.

---

## 27. Error Monitoring and Alerting

**First line: internal logging** — Django's logging framework, configured in `settings.py`
by severity: `DEBUG` · `INFO` · `WARNING` · `ERROR` · `CRITICAL`

**Modern error tracking** (Sentry, Rollbar): real-time error tracking · performance
monitoring · user impact analysis · automated alerts

??? question "Checkpoint: name the single most dangerous production setting."

    `DEBUG = True`. Everything on slide 26 follows from that one line.

---

## 28. Shared Nothing

The shift **from `runserver` to production**.

Each node is completely **independent and self-sufficient**, which is what makes
horizontal scaling possible.

**Core principles:** stateless application servers · **externalized state** ·
loose coupling · fault tolerance

**Benefits:** horizontal scalability · high availability · geographic distribution ·
independent scaling of components · maintenance without downtime

→ [Architecture and deployment](architecture-and-deployment.md)

---

## 29. The Entry Point and the App Tier

**Load balancer** (Nginx, HAProxy, AWS ALB)

- A traffic cop distributing requests across a pool of Django servers
- **Health checks** ensure traffic only reaches servers that are actually working

**Identical web servers**

- Multiple instances of the same project, typically in containers
- **Stateless** — nothing user-specific is written to local disk
- To handle more traffic, spin up more identical copies

---

## 30. Moving State Out

For servers to genuinely "share nothing", **three things must move**:

| State | Where it goes | Why |
|---|---|---|
| **Sessions** | Centralized cache (Redis) or the database | A session started on Server A must be recognized by Server B |
| **Media files** | Cloud storage (S3, GCS) | Uploads on one server would be invisible to the others |
| **Database** | One centralized cluster, often with read replicas | Single source of truth, scaled separately |

**Static assets** — collected and pushed to a **CDN**, so web servers spend their time
running Python, not shipping CSS.

---

## 31. Offloading Heavy Work

A heavy task — generating a PDF, sending 1,000 emails — must not run inside the request.

1. The app server drops a message into a **broker** (RabbitMQ, Redis)
2. Separate **worker** instances (Celery) pick it up and process it

This is what keeps one expensive request from slowing the site for everyone else.

---

## 32. Settings per Environment

Development and production need different settings — and secrets belong in **neither** file.

```bash title=".env.example — the template you commit"
DJANGO_ENV=development
SECRET_KEY=your-secret-key-here
DEBUG=True

DB_NAME=myapp
DB_USER=postgres
DB_PASSWORD=password
DB_HOST=localhost

REDIS_HOST=localhost
AWS_STORAGE_BUCKET_NAME=your-s3-bucket
```

Commit `.env.example`. **Never** commit `.env`.

Also environment-specific: **time zone**, **language**, and **static files** configuration.

---

## 33. Serving Django

**Modern standard:** a WSGI server (**Gunicorn**, uWSGI) or an ASGI server
(**Daphne**, **Uvicorn**) behind Nginx — managed by systemd or run in Docker.

**Legacy methods, for context:**

- **Apache + mod_python** — deprecated and unmaintained
- **FastCGI** — largely deprecated in favor of WSGI/ASGI

!!! note "Why they are still in the syllabus"

    You will meet them in existing systems. Know what they are; do not start new projects
    on them.

---

## 34. Scaling and Performance Tuning

- **Horizontal scaling** — more app servers behind the load balancer
- **Performance tuning** — find bottlenecks with the **Django Debug Toolbar**
- **Caching** — the multi-level strategy from Unit 4, including template fragment caching
- **Database optimization** — indexing and query tuning, **connection pooling**
  (PgBouncer), and **read replicas** via a database router

---

## 35. Why Monitor?

Continuous monitoring keeps a production app **reliable, performant, and secure**.

- **Proactive issue detection** — find problems before users do
- **Performance optimization** — track and improve over time
- **Security monitoring** — spot suspicious activity and potential breaches
- **Capacity planning** — understand usage patterns before you need to scale

→ [Monitoring and maintenance](monitoring-and-maintenance.md)

---

## 36. What to Measure

| Layer | Metrics |
|---|---|
| **Application** | Response time · error rate · throughput · active users |
| **Infrastructure** | CPU · memory and swap · disk I/O · network traffic |
| **Database** | Connection count · slow queries · database size trend |

```python title="A monitoring middleware you can write today"
class MonitoringMiddleware(MiddlewareMixin):
    def process_request(self, request):
        request.start_time = time.time()

    def process_response(self, request, response):
        duration = time.time() - request.start_time
        if duration > 2.0:
            logging.warning(f"Slow request: {request.path} took {duration:.2f}s")
        return response
```

**Tooling:** Prometheus + Grafana for metrics and dashboards · the ELK stack for logs ·
alerting on top of both.

---

## 37. Routine Maintenance

```bash title="A dependency update run"
pip install --upgrade pip
pip-compile requirements.in && pip-sync requirements.txt
pip-audit                              # security vulnerabilities
python manage.py test                  # never skip this step
python manage.py collectstatic --noinput
sudo systemctl restart gunicorn
```

Also on the routine list: automated **database backups** · index management ·
**security patches** · media file cleanup · **log rotation**

---

## 38. Load Testing and Query Optimization

```bash title="Locust"
locust -f locustfile.py --headless --users=100 --spawn-rate=10 \
       --run-time=300s --host=http://localhost:8000
```

```python title="Bad — N+1"
for order in Order.objects.all():
    products = order.products.all()        # one query per order
```

```python title="Good"
Order.objects.select_related('customer').prefetch_related('products', 'products__category')
```

```python title="Best — fetch only the columns you use"
Order.objects.only('id', 'order_number', 'total_amount',
                   'customer__name').select_related('customer')
```

---

## 39. Backup and Disaster Recovery

**Incident severity**

| Level | Meaning |
|---|---|
| **Critical** | Complete outage, data loss, security breach |
| **High** | Major functionality loss, serious performance degradation |
| **Medium** | Partial functionality loss, minor performance issues |
| **Low** | Cosmetic issues, non-critical errors |

**Recovery runbooks** — with expected recovery times

- *Database corruption* (2–4 hrs): assess extent → restore latest clean backup →
  apply transaction logs → verify integrity → update app if the schema changed
- *Server failure* (1–2 hrs): provision → install → restore code → restore database →
  update DNS/load balancer → test
- *Data loss* (4–8 hrs): identify the window → restore → re-import → verify → tell stakeholders

**Communication plan** — dev team immediately, management within 1 hour, staff within 2;
customers as impact warrants.

**Testing** — monthly restore and failover tests; quarterly full disaster drills.

!!! warning "An untested backup is not a backup"

    It is a hope. The monthly restore test is what turns it into a plan.

---

## 40. Beyond the Code

**Documentation and knowledge sharing** — keep deployment and maintenance procedures
current, share knowledge in regular meetings, version-control configuration and scripts.

**Security audits and compliance** — regular audits, compliance with data protection
regulation (e.g. **GDPR**), best practices for user privacy.

**User feedback and continuous improvement** — collect feedback, analyze metrics,
iterate on what the data shows.

---

## 41. Lab Checklist

- [ ] Enable `USE_I18N`, add `LANGUAGES`, insert `LocaleMiddleware` in the right position
- [ ] Mark strings with `_()`, `{% trans %}`, and one `{% blocktrans %}` with a plural
- [ ] Run `makemessages -l ne`, translate the `.po`, run `compilemessages`
- [ ] Add the language switcher and verify the cookie changes
- [ ] Set `DEBUG = False` with `ALLOWED_HOSTS` and add custom 404/500 pages
- [ ] Apply the full secure-cookie and HSTS settings block
- [ ] Write a `SecurityHeadersMiddleware` and inspect the headers in devtools
- [ ] Deliberately write an unsafe f-string query, then fix it — and explain why
- [ ] Move sessions to Redis and media to cloud storage (or a local S3-compatible stand-in)
- [ ] Run the app under Gunicorn behind Nginx
- [ ] Add the slow-request monitoring middleware and trigger it
- [ ] Run a Locust load test and record the response times

---

## 42. Recap

- **i18n** prepares, **l10n** adapts; lazy translation for anything loaded before a request
- The workflow is **mark → makemessages → translate → compilemessages → restart**
- Language is discovered by **URL → cookie → session → header → default**
- **Defense in depth**: no single control is trusted; secure defaults everywhere
- The ORM stops SQL injection unless you **format strings**; templates escape unless you say `|safe`
- **`DEBUG = False`** in production is the highest-value line in the file
- **Shared nothing** means state lives outside the app server: sessions, media, database, static
- Monitoring, tested backups, and runbooks are part of shipping — not afterthoughts

**You have now taken the app from `startproject` to production.**

---

## 43. Exit Questions

??? question "Why `gettext_lazy` in a model but `gettext` in a view?"

    Models are imported before any request, so no language is active yet. A view runs
    inside a request, where the active language is already known.

??? question "A translator edited `django.po` but the site is unchanged. What was skipped?"

    `compilemessages` — Django reads the compiled `.mo`, not the `.po`. And restart after.

??? question "`Song.objects.raw()` — safe or unsafe?"

    Safe with parameters (`%s` and a list). Unsafe the moment you build the string with
    an f-string or `%` formatting.

??? question "Why can't a shared-nothing deployment store uploads in `/media/`?"

    That directory is local to one server. Users hitting a different server would get 404s,
    and the file dies with the container.

??? question "Name three things that must move out of the app server to scale horizontally."

    Sessions (to Redis/DB), media files (to cloud storage), and the database (to a
    centralized cluster) — with static files on a CDN.

??? question "Your backup script has run nightly for a year. Is your data safe?"

    Unknown until you have restored from it. Untested backups are hope, not recovery.
