# Unit 2 — Model, Administration Site and Form Processing · Class Slides

### Session Plan (9 Hrs.)

| # | Block | Slides | Approx. |
|---|---|---|---|
| 1 | MTV recap · what a Model is · first model | 1–5 | 1.5 hr |
| 2 | Database configuration · env vars · migrations | 6–10 | 2 hr |
| 3 | Data access: the ORM and the shell (CRUD) | 11–16 | 2 hr |
| 4 | Queries in views, not templates | 17–18 | 0.5 hr |
| 5 | The admin site: activate, use, customize | 19–24 | 1.5 hr |
| 6 | Forms: standard, ModelForm, validation, styling | 25–32 | 1.5 hr |

---

## 1. Where We Are Going

Unit 1 rendered *hardcoded* content. This unit gives the app a **memory**.

- Describe data once in Python → Django creates the tables
- Read and write it through the **ORM**, never raw SQL
- Get a full admin UI for free
- Accept data *from* users safely with **forms**

→ [Unit overview](overview.md)

---

## 2. Learning Objectives

- Understand the **Model-Template-View (MTV)** architecture of Django
- Define and manage **models**
- Use Django's **admin interface** for database management
- Create and process **forms** in Django applications

---

## 3. The Model — Single Source of Truth

- The **single, definitive source of information** about your data
- Contains the essential **fields** and **behaviors** of what you store
- Each model is a Python class subclassing `django.db.models.Model`
- Each attribute is a **database field**
- You get an automatically generated **database-access API**

!!! quote "One sentence to remember"

    You write the class; Django writes the table *and* the query language.

---

## 4. Our First Model

```python title="songs/models.py"
from django.db import models

class Song(models.Model):
    title = models.CharField(max_length=100)   # maps to a database column
    lyrics = models.TextField()
    release_date = models.DateField(null=True, blank=True)
    is_published = models.BooleanField(default=False)
```

---

## 5. What Django Builds From It

Database table — **`songs_song`**

| Column | Data type | Description |
|---|---|---|
| `id` | Integer | Auto-incrementing primary key |
| `title` | Varchar(100) | Title of the song |
| `lyrics` | Text | Lyrics of the song |
| `release_date` | Date | Release date |
| `is_published` | Boolean | Publication status |

- Table naming convention: **`appname_modelname`** → app `songs` + model `Song`
- An `id` primary key is **added automatically** unless you define one

??? tip "Ask the class: what does `null=True, blank=True` actually control?"

    `null` is a **database** constraint (the column may store NULL);
    `blank` is a **validation** rule (forms may leave it empty). They are not the same thing.

---

## 6. Configuring the Database

```python title="settings.py"
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': '<database-name>',
        'USER': '<djangouser>',
        'PASSWORD': '<djangopassword>',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

Create the database and user first:

```sql
CREATE DATABASE geetshala;
CREATE USER 'djangouser'@'localhost' IDENTIFIED BY 'djangopassword';
GRANT ALL PRIVILEGES ON geetshala.* TO 'djangouser'@'localhost';
```

→ [MySQL server setup, step by step](database-setup.md)

---

## 7. Never Commit Credentials

!!! danger "Industry practice"

    Hardcoded credentials in `settings.py` end up in Git history — and Git history is forever.
    Real projects read secrets from **environment variables**.

```python title="settings.py"
from dotenv import load_dotenv
load_dotenv(BASE_DIR / '.env')

DATABASES = {'default': {
    'ENGINE': 'django.db.backends.mysql',
    'NAME': os.getenv('DB_NAME'),
    'USER': os.getenv('DB_USER'),
    'PASSWORD': os.getenv('DB_PASSWORD'),
    'HOST': os.getenv('DB_HOST', 'localhost'),
    'PORT': os.getenv('DB_PORT', '3306'),
}}
```

`pip install python-dotenv` · put values in `.env` · **add `.env` to `.gitignore`**

→ [Using environment variables](using-environment-variables.md)

---

## 8. Installing the Model — Migrations

Two commands, two very different jobs:

```bash
python manage.py makemigrations    # writes Python files describing the change
python manage.py migrate           # applies those changes to the database
```

![output-makemigrations](assets/output-makemigrations.png)

- `makemigrations` **does not touch the database** — it writes a migration file
- `migrate` needs **database connectivity** and does the real schema work
- Target one app: `python manage.py migrate songs`

---

## 9. Migrations in Practice

![server-with-pending-migration](assets/server-with-pending-migration.png)

Migrations are Django's way of **propagating model changes into the database schema**.

| Task | Command |
|---|---|
| Create a migration | `makemigrations <app>` |
| Apply migrations | `migrate <app>` |
| See what is applied | `showmigrations <app>` |
| Roll back | `migrate <app> <last_good_migration>` |

!!! warning "Migration files are history, not source"

    Once created, **do not edit** a migration file. Make a new migration for the next change.

→ [Making changes to a database schema](database-migrations.md)

---

## 10. Readable Objects — `__str__`

```python
class Song(models.Model):
    ...
    def __str__(self):
        return self.title
```

Why it matters:

- `<Song: Song object (1)>` becomes `<Song: Phoolko Aankhama Phoolai Sansar>`
- Improves readability in the **admin interface**, the shell, and anywhere instances display

---

## 11. The Manager — Database Gatekeeper

- A bridge between your models and the database; a core part of Django's **ORM**
- Every model has at least one Manager, named **`objects`** by default
- The interface through which all query operations are provided
- Methods return a **QuerySet** (a collection of records) or a single instance
- Custom Managers can add methods or change the initial QuerySet

**`Song.objects.filter(...)`** — model, then gatekeeper, then question.

---

## 12. The Django Shell

```bash
python manage.py shell
```

An interactive Python shell that loads the Django environment, so you can talk to
your models and database through the ORM.

Great for:

- Inspecting and debugging data
- Testing ORM queries and custom model methods
- One-off administrative tasks and data clean-up

---

## 13. Create (Insert)

Two common ways:

```python title="Instantiate, then save"
new_song = Song(title="फूलको आँखामा फूलै संन्सार",
                lyrics="...",
                release_date="2004-10-11",
                is_published=True)
new_song.save()
```

```python title="One-step create"
new_song = Song.objects.create(title="Song Title", lyrics="Song Lyrics",
                               release_date="2023-10-01", is_published=True)
```

??? tip "Ask the class: what is the difference?"

    `create()` builds the instance **and** hits the database in one call.
    The two-step form lets you adjust attributes before the `INSERT` happens.

---

## 14. Read (Select, Filter, Order)

```python
Song.objects.all()                          # every row → QuerySet
Song.objects.filter(is_published=True)      # subset → QuerySet
Song.objects.get(id=1)                      # exactly one → instance
Song.objects.order_by('release_date')       # sorted → QuerySet
```

??? tip "Ask the class: when does `get()` blow up?"

    `DoesNotExist` when nothing matches, `MultipleObjectsReturned` when more than one does.
    `filter()` simply returns an empty QuerySet — no exception.

---

## 15. Update

```python title="Instance update — runs save()"
song = Song.objects.get(id=1)
song.title = "Updated Song Title"
song.is_published = False
song.save()
```

```python title="QuerySet update — one SQL statement"
Song.objects.filter(is_published=True).update(is_published=False)
```

!!! note "The catch"

    `update()` modifies the database directly and **does not call `save()`** on instances —
    so custom save logic and signals never run.

---

## 16. Delete

```python title="One object"
song = Song.objects.get(id=1)
song.delete()
```

```python title="Many objects"
Song.objects.filter(is_published=False).delete()
```

---

## 17. The Dumb Way to Query

Querying from inside the template:

```html
{% for song in songs %}
    <p>{{ song.lyrics }}</p>
    <h2>{{ song.album.song_count }}</h2>   <!-- a new query, every iteration -->
{% endfor %}
```

**Why this is dumb**

- Templates should contain **presentation only** — not data fetching or filtering
- **Hard to test** — logic buried in markup
- Causes the **N+1 query problem** — the biggest source of performance issues
- **Violates MTV** — breaks separation of concerns

---

## 18. The Smart Way

Query in the **view**, hand finished data to the template:

```python
def song_list(request):
    songs = Song.objects.all().select_related('album')   # one query, joined
    return render(request, 'songs/song_list.html', {'songs': songs})
```

```html
{% for song in songs %}
    <p>{{ song.lyrics }}</p>
    <h2>{{ song.album.song_count }}</h2>   <!-- no extra query now -->
{% endfor %}
```

Same template. Fast, clean, proper MTV.

---

## 19. The Admin Interface

One of Django's **batteries included** features.

- Reads **metadata from your models** to build a model-centric interface automatically
- Full CRUD on your data without writing management code

**What it is used for:** add/edit/delete records · search and filter · manage users and
permissions · import/export via plugins · quick internal tools for staff · debugging.

→ [Admin interface](admin-interface.md)

---

## 20. How It Works — Three Steps

1. Define models in `models.py`
2. Register them in `admin.py` — `admin.site.register(Song)`
3. Django builds forms, list views, filters, validation, and UI for you

**Requirements:** `django.contrib.admin` (plus `auth`, `contenttypes`, `sessions`) in
`INSTALLED_APPS`, and `path('admin/', admin.site.urls)` in the project URLconf.

```bash
python manage.py createsuperuser
python manage.py runserver          # then visit /admin/
```

---

## 21. First Look

![admin-login-createsuperuser](assets/admin-login-createsuperuser.png)

After logging in:

![admin-interface-default-view](assets/admin-interface-default-view.png)

With the `songs` app registered:

![admin-interface-with-songs-app](assets/admin-interface-with-songs-app.png)

---

## 22. Customizing with `ModelAdmin`

Your admin users are often **non-technical** — shape the interface around their job.

```python title="songs/admin.py"
class SongAdmin(admin.ModelAdmin):
    fields = ('lyrics', 'title', 'release_date')          # order in the add/edit form
    list_display = ('title', 'lyrics', 'release_date')    # columns in the list view
    search_fields = ('title', 'lyrics')                   # what the search box searches
    list_filter = ('release_date',)                       # right sidebar filters
    ordering = ('-release_date',)

admin.site.register(Song, SongAdmin)
```

| Area | Key option | Purpose |
|---|---|---|
| List view | `list_display` | Which fields/methods appear as columns |
| List view | `list_filter` | Sidebar filters based on field values |
| Change form | `fields` / `fieldsets` | Order and grouping of form fields |
| Change form | `inlines` | Edit related (FK) objects on the parent's page |
| Actions | `actions` | Custom bulk actions from the list view |
| Fields | `readonly_fields` | Which fields cannot be edited |

---

## 23. Look, Feel, and the Index Page

**Basic configuration** — set `admin.site.site_header`, `site_title`, `index_title`

**Template overriding** — drop your own `base_site.html` or `index.html` into an
`admin/` subdirectory of your templates directory, e.g. `geetshala/templates/admin/index.html`.
Django finds it instead of the default. Use the `app_list` context variable to lay out
your own dashboard.

**Third-party themes** — drop-in replacements like **Django Jazzmin** or **Django Unfold**
for a modern responsive UI.

*Advanced:* a custom `AdminSite` class overriding `index()`.

---

## 24. Search in the Admin

- Enable it with `search_fields` on the `ModelAdmin` — a tuple of field names
- Django adds a search box to the changelist and filters results against those fields
- Search across relations with **double underscores**: `'foreign_key__related_field'`

??? tip "Mini-challenge: customize the admin for one of your models"

    Add at least three of: `fields` ordering, `search_fields`, `list_filter`, `list_display`.

---

## 25. Forms — Why Bother?

Django's form system handles preparing data for rendering, **validating** submissions,
and converting values to Python types.

- **Security** — automatic CSRF protection, sanitized and validated input
- **Convenience** — renders HTML, shows errors, repopulates after an invalid submit
- **Validation** — built-ins for email, URL, date + custom rules
- **Model integration** — `ModelForm` removes CRUD boilerplate
- **Data cleaning** — a text field arrives in Python as a real `int`, `date`, etc.

→ [Forms in Django](forms-in-django.md)

---

## 26. The Three Building Blocks

| Component | Responsibility |
|---|---|
| **Form class** | Defines the fields and their validation rules |
| **Field objects** | `CharField`, `EmailField`, … — validation and cleaning per data type |
| **Widget objects** | How a field renders as HTML (`<input>`, `<textarea>`, `<select>`) — presentation only |

**Two kinds of forms**

- `forms.Form` — standalone; you declare every field (no model involved)
- `forms.ModelForm` — generated from a model

---

## 27. The Perfect Form

Secure, fully validated, and pleasant to use:

- **HTML structure** — wrapped in `<form method="post">` with `{% csrf_token %}`
- **Validation** — field-level (*is this a valid email?*) **and** form-level (*do the passwords match?*)
- **Clear errors** — shown next to the field they belong to
- **Accessibility** — proper labels and attributes
- **Repopulation** — a failed submit re-renders with the user's data intact

---

## 28. Standard Form — Feedback

```python title="feedback/forms.py"
class FeedbackForm(forms.Form):
    name = forms.CharField(label='Your Name', max_length=100)
    email = forms.EmailField(label='Your Email', required=True)
    message = forms.CharField(
        label='Your Feedback',
        widget=forms.Textarea(attrs={'rows': 5, 'placeholder': 'Enter your feedback here...'}))

    def clean_name(self):
        name = self.cleaned_data.get('name')
        if any(char.isdigit() for char in name):
            raise forms.ValidationError("Name cannot contain numbers.")
        return name
```

```html title="feedback.html"
<form method="post" action="">
  {% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Submit Feedback</button>
</form>
```

---

## 29. Processing the Submission

The single most important pattern in this unit — **one view, two paths**:

```python
def feedback_view(request):
    if request.method == 'POST':
        form = FeedbackForm(request.POST)          # bound form
        if form.is_valid():
            name = form.cleaned_data['name']       # cleaned, typed data
            ...                                    # act on it
            return HttpResponse("Thank you for your feedback!")
        return render(request, 'feedback.html', {'form': form})   # re-render with errors
    form = FeedbackForm()                          # unbound form for GET
    return render(request, 'feedback.html', {'form': form})
```

??? tip "Ask the class: why must the invalid branch pass the *same* form object back?"

    That instance carries both the submitted values and the error messages —
    a fresh `FeedbackForm()` would wipe the user's input.

---

## 30. ModelForm — Song Submission

```python title="songs/forms.py"
class SongForm(forms.ModelForm):
    class Meta:
        model = Song
        fields = ['title', 'lyrics', 'released_date', 'is_published']
        labels = {'title': 'Title of the Song',
                  'is_published': 'Should this song be in the portal?'}
        widgets = {'lyrics': forms.Textarea(attrs={'rows': 5, 'cols': 40}),
                   'released_date': forms.SelectDateWidget()}
```

```python title="songs/views.py"
def create_song(request):
    if request.method == 'POST':
        form = SongForm(request.POST)
        if form.is_valid():
            form.save()                    # writes the Song row
            return redirect('song_list')
    else:
        form = SongForm()
    return render(request, 'song_form.html', {'form': form})
```

`ModelForm` handles **field definition**, **initial data** when editing, and **saving**.

---

## 31. Custom Validation Rules

```python title="Field-level — clean_<fieldname>()"
def clean_title(self):
    title = self.cleaned_data.get('title')
    if title and len(title) < 5:
        raise forms.ValidationError("Title must be at least 5 characters long")
    return title
```

```python title="Form-level — clean(), for rules spanning fields"
def clean(self):
    cleaned_data = super().clean()
    title, lyrics = cleaned_data.get('title'), cleaned_data.get('lyrics')
    if title and lyrics and title in lyrics:
        raise forms.ValidationError("Title do not match with the lyrics content.")
```

Rule of thumb: **one field → `clean_<field>`; two or more fields → `clean()`**.

---

## 32. Custom Look and Feel

**Custom widgets** — keep the validation, change the HTML:

```python
message = forms.CharField(
    widget=forms.Textarea(attrs={'class': 'form-control custom-textarea', 'rows': 5}))
```

**Manual rendering** — maximum control, field by field:

```html
<div class="form-group">
  {{ form.subject.label_tag }} {{ form.subject }}
  {% for error in form.subject.errors %}<p class="error-message">{{ error }}</p>{% endfor %}
</div>
```

**Template packs (recommended)** — override Django's form template fragments, or use
`django-crispy-forms` to apply a CSS framework like Bootstrap.

---

## 33. Lab Checklist

- [ ] Create a MySQL database and user; point `settings.py` at it
- [ ] Move the credentials into `.env` and gitignore it
- [ ] Define the `Song` model with a `__str__` method
- [ ] `makemigrations` → inspect the generated file → `migrate`
- [ ] In `manage.py shell`: create, filter, update, and delete songs
- [ ] Register `Song` with a `SongAdmin` (list_display, search_fields, list_filter)
- [ ] Build the standard `FeedbackForm` end to end, with custom validation
- [ ] Build the `SongForm` ModelForm for create **and** update

---

## 34. Recap

- The **model** is the single source of truth; the table follows from the class
- `makemigrations` writes intent, `migrate` changes the database — never edit a migration
- `objects` is the gatekeeper; **QuerySets** are lazy collections, `get()` returns one object
- Queries belong in **views**, never in templates — N+1 is the price otherwise
- The **admin** is free CRUD; `ModelAdmin` makes it usable by non-technical staff
- **Forms** validate and clean; `ModelForm` ties that straight to a model

**Next up →** Unit 3: Views, URLConfs, the Template Engine, and non-HTML content

---

## 35. Exit Questions

??? question "Model `Album` in app `music` — what is the table called?"

    `music_album`, with an auto-added `id` primary key.

??? question "`makemigrations` printed a file but the table is missing. Why?"

    `makemigrations` never touches the database. You still have to run `migrate`.

??? question "Why is `.update()` faster than looping and calling `.save()`?"

    It issues a single SQL `UPDATE` for the whole QuerySet instead of one round trip per
    row — at the cost of skipping `save()` logic and signals.

??? question "A form fails validation. What must the view return?"

    The same bound form, re-rendered, so errors and the user's input both survive.

??? question "`clean_email()` vs `clean()` — when do you need each?"

    `clean_email()` validates one field in isolation; `clean()` is for rules that compare
    multiple fields.
