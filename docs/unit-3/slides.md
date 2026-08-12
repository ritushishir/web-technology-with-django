---
title: Unit 3 · Beyond Basics
---

# Unit 3 — Views, URLConfs, Template Engine and Non-HTML Content

---

## 1. Where We Are Going

Unit 1 got a page on screen; Unit 2 gave it data. This unit is about **writing less code**
and **serving more than HTML**.

- URL schemes that survive a large project
- Class-based generic views instead of repetitive functions
- The template engine, extended on your terms
- CSV, PDF, JSON, ZIP — and feeds and sitemaps so machines can find you

---

## 2. Learning Objectives

- **Architect sophisticated URL schemes** — modular URLconfs with inclusion and namespaces
  that prevent naming collisions in large projects
- **Implement Class-Based Views** — move from functions to objects, using generic views for
  list and detail patterns with minimal code
- **Extend the template engine** — context processors for global data, custom loaders for
  specialized file handling
- **Generate dynamic non-HTML content** — CSV files and PDF documents
- **Optimize for SEO and syndication** — sitemap and RSS/Atom frameworks

---

## 3. Including Other URLconfs

Modularize with `include()`, and give each app a **namespace**:

```python title="geetshala/urls.py"
urlpatterns = [
    path('song/', include('song.urls', namespace='song')),
    path('feedback/', include('feedback.urls', namespace='feedback')),
]
```

Namespaces stop two apps that both define `detail` from colliding —
you refer to `'song:detail'` and `'feedback:detail'`.

→ [Advanced URL dispatching](advanced-url-dispatching.md#urlconf-tricks)

---

## 4. Naming Patterns for Reverse Lookups

Name a pattern once, then never hardcode that URL again:

```python
path('song/<int:id>/', views.song_detail, name='song-detail'),
```

```python title="in Python"
from django.urls import reverse
url = reverse('song-detail', args=[song.id])
```

```html title="in a template"
<a href="{% url 'song-detail' song.id %}">View</a>
```

??? tip "Ask the class: why is `reverse()` better than typing `/song/5/`?"

    Change the URL pattern once and every link follows. Hardcoded paths rot silently.

---

## 5. Passing Extra Options to Views

The URLconf can hand a view **extra keyword arguments** that are not in the URL:

```python
urlpatterns = [
    path('archive/<int:year>/', views.archive, {'month': None}, name='archive'),
    path('archive/<int:year>/<int:month>/', views.archive, name='archive-month'),
]
```

One view, two entry points — the dictionary supplies the default for the shorter URL.

---

## 6. Checkpoint — URL Design

??? question "Two apps each name a pattern `detail`. What breaks, and what fixes it?"

    `reverse('detail')` becomes ambiguous. Namespacing via
    `include('app.urls', namespace='app')` makes it `'app:detail'`.

---

## 7. FBVs vs CBVs

Both do exactly the same job: take an `HttpRequest`, return an `HttpResponse`.

| | Function-Based Views | Class-Based Views |
|---|---|---|
| **Shape** | A Python function | A Python class |
| **Dispatch** | `if request.method == 'POST':` | `def get()` / `def post()` |
| **Pros** | Easy to read for simple views; direct control | Reuse via inheritance and mixins; generic views for common tasks |
| **Cons** | Repetitive; spaghetti when logic grows | Steeper learning curve; logic "hidden" in parent classes |

→ [CBVs and FBVs](working-with-generic-views.md#cbvs-and-fbvs)

---

## 8. The Same View, Both Ways

```python title="Function-based"
def upload_song(request):
    if request.method == 'POST':
        form = SongForm(request.POST, request.FILES)
        if form.is_valid():
            form.save()
            return redirect('song-list')
    else:
        form = SongForm()
    return render(request, 'song_form.html', {'form': form})
```

```python title="Class-based"
class UploadSong(View):
    def get(self, request):
        return render(request, 'song_form.html', {'form': SongForm()})

    def post(self, request):
        form = SongForm(request.POST, request.FILES)
        if form.is_valid():
            form.save()
            return redirect('song-list')
        return render(request, 'song_form.html', {'form': form})
```

The `if` on the request method **becomes the method name**.

---

## 9. Generic Views

- Built-in classes that implement **common web development patterns**
- Built on CBVs, they cut the boilerplate for listing, detail pages, and forms
- Live in `django.views.generic`
- **DRY** in its purest framework form: reusable components for repeated tasks

Cleaner codebase, more maintainable, easier to read.

→ [Using generic views](working-with-generic-views.md#using-generic-views)

---

## 10. ListView and DetailView

```python
class SongListView(ListView):
    model = Song
    template_name = 'songs/song_list.html'
    context_object_name = 'songs'
```

```python
class SongDetailView(DetailView):
    model = Song
    template_name = 'songs/song_detail.html'
    context_object_name = 'song'
```

Three attributes replace an entire view function — the query, the template, and the
context name are all Django needs.

---

## 11. CreateView / UpdateView / DeleteView

```python
class SongCreateView(CreateView):
    model = Song
    fields = ['title', 'artist', 'genre']
    template_name = 'songs/song_form.html'
    success_url = '/songs/'          # where to go after a successful save
```

Full CRUD with almost no code — the form, validation, saving, and redirect are inherited.

---

## 12. Extending Generic Views

Override attributes for configuration, methods for behavior:

```python title="Extra context"
def get_context_data(self, **kwargs):
    context = super().get_context_data(**kwargs)
    context['extra_info'] = 'This is some extra information'
    return context
```

```python title="Custom queryset"
def get_queryset(self):
    return Song.objects.filter(is_published=True)
```

```python title="Hook into form handling"
def form_valid(self, form):
    # form.instance.created_by = self.request.user
    return super().form_valid(form)
```

!!! tip "Always call `super()`"

    You are *extending* a working pipeline, not replacing it. Drop the `super()` call and
    the inherited behavior disappears with it.

→ [Extending generic views](working-with-generic-views.md#extending-generic-views)

---

## 13. Checkpoint — Generic Views

??? tip "Mini-challenge: manage the song collection with generic views only"

    `ListView` for all songs · `DetailView` for one · `CreateView` to add ·
    `UpdateView` to edit · `DeleteView` to remove.

??? question "Where does `DetailView` get the object from?"

    From the primary key or slug captured in the URL, applied to `model`'s default manager —
    which is why the URL pattern must supply `<int:pk>`.

---

## 14. Template Language — Quick Review

- Variables, tags, filters — `{{ }}` and `{% %}`
- Inheritance strategies with `{% extends %}` and `{% block %}`

→ [Tags and filters](../unit-1/project-setup.md#basic-template-tags-and-filters) ·
[Inheritance](../unit-1/project-setup.md#template-inheritance)

---

## 15. RequestContext

- A specialized subclass of `Context` that automatically includes variables from
  **context processors**
- Needs the `HttpRequest`, which is how it reaches request-specific data
- `render()` uses it automatically — you get context processors for free
- Using `Template.render()` directly? Then build a `RequestContext` yourself:

```python
request = HttpRequest()
template = Template("Site Name: {{ site_name }}")
rendered = template.render(RequestContext(request))
```

---

## 16. Context Processors

Functions that take the request and return a dictionary merged into **every** template context.

```python title="songs/context_processors.py"
def app_settings(request):
    return {'site_name': 'Geetshala'}
```

```python title="settings.py"
'OPTIONS': {'context_processors': [..., 'songs.context_processors.app_settings']}
```

```html title="base.html — no view has to pass it"
<title>{{ site_name }}</title>
```

Use them for genuinely global data: site settings, user info, the current year.

→ [Request context and context processors](deep-dive-django-templates.md#request-context-and-context-processors)

---

## 17. Inside Template Loading

How Django finds your HTML files:

- **`DIRS`** — an explicit list of directories to search
- **`APP_DIRS`** — when `True`, search a `templates/` subdirectory in each installed app
- **Search order** — `DIRS` first, then each app's `templates/` in `INSTALLED_APPS` order

??? question "Two apps both ship `templates/song_list.html`. Which wins?"

    The one whose app appears **earlier** in `INSTALLED_APPS` — which is exactly why you
    namespace templates as `songs/templates/songs/song_list.html`.

---

## 18. Custom Filters

When built-in tags and filters run out, extend the engine. A filter takes a value,
processes it, and returns something to display:

```python
@register.filter
def bullet_points(values):
    """Converts a list of items into a bullet-pointed list."""
    if not values:
        return ""
    return "<ul>" + "".join(f"<li>{item}</li>" for item in values) + "</ul>"
```

```html
{{ languages|bullet_points }}
```

→ [Custom template filters](deep-dive-django-templates.md#custom-template-filters)

---

## 19. Custom Tags

```python title="simple_tag — returns a value"
@register.simple_tag
def multiply(a, b):
    return a * b
```

```html
The result of multiplication is: {% multiply 3 4 %}
```

```python title="inclusion_tag — renders a template with data"
@register.inclusion_tag('songs/song_list.html')
def show_songs(songs):
    return {'songs': songs}
```

```html
{% show_songs song_list %}
```

**Rule of thumb:** returning text → `simple_tag`; returning **markup** → `inclusion_tag`.

---

## 20. Registering and Loading

Wiring is mechanical, and easy to get wrong:

1. Create a `templatetags/` directory **inside your app**
2. Add `__init__.py` so it is a package
3. Put your tags/filters in a Python module there
4. Register them with `register = template.Library()`
5. Load them in the template:

```html
{% load your_custom_tags %}
```

!!! warning "Forgot `__init__.py`?"

    Django will not find the module and `{% load %}` fails — the most common mistake here.

---

## 21. Custom Template Loaders

For templates that **do not live on the filesystem** — a database, an external API,
cloud storage, encrypted files.

Subclass `django.template.loaders.base.Loader` and override two methods:

```python
class CustomLoader(Loader):
    def get_template_sources(self, template_name):
        yield f"custom_location/{template_name}"

    def get_contents(self, origin):
        try:
            with open(origin, 'r') as f:
                return f.read()
        except FileNotFoundError:
            raise TemplateDoesNotExist(origin)
```

→ [Writing custom template loaders](deep-dive-django-templates.md#writing-custom-template-loaders)

---

## 22. Two Useful Extras

**Built-in template reference** — Django documents its own template system. Add
`django.contrib.admindocs` to `INSTALLED_APPS` and
`path('admin/doc/', include('django.contrib.admindocs.urls'))` to the URLconf.

**Standalone mode** — use the template engine *outside* a Django project, for automated
emails or report generation:

```python
settings.configure(TEMPLATES=[{
    'BACKEND': 'django.template.backends.django.DjangoTemplates',
    'DIRS': ['path/to/your/templates'],
    'APP_DIRS': False,
}])
django.setup()
template = Engine.get_default().get_template('your_template.html')
print(template.render(Context({'key': 'value'})))
```

---

## 23. Views and MIME Types

Django's response system is used mostly for HTML — but it is not limited to it.

Every `HttpResponse` carries a **MIME type**, telling the browser what it received.

- Default: `text/html; charset=utf-8`
- Override with `content_type`:

```python
return HttpResponse("This is a plain text response.", content_type="text/plain")
```

The MIME type plus `Content-Disposition` decides whether the browser **renders** it (HTML,
images, PDFs), **downloads** it (CSV, ZIP), or **shows it as code** (JSON, XML).

→ [The basics: views and MIME types](specialized-content-and-output.md#the-basics-views-and-mime-types)

---

## 24. Producing CSV

Python's `csv` module writes **straight into the response** — no template involved:

```python
def export_songs_csv(request):
    response = HttpResponse(content_type='text/csv')
    response['Content-Disposition'] = 'attachment; filename="songs.csv"'

    writer = csv.writer(response)
    writer.writerow(['Song Title', 'Released Date', 'Author'])
    for song in Song.objects.all():
        writer.writerow([song.title, song.released_date, song.author])
    return response
```

The key insight: **`HttpResponse` behaves like a file object.**

---

## 25. Generating PDFs

Same trick, different library — `ReportLab` (or `WeasyPrint` for HTML→PDF):

```python
def generate_pdf(request):
    response = HttpResponse(content_type='application/pdf')
    response['Content-Disposition'] = 'attachment; filename="song_detail.pdf"'

    p = canvas.Canvas(response)              # the response *is* the file
    p.drawString(100, 750, "Hello, this is a PDF document.")
    p.showPage()
    p.save()
    return response
```

---

## 26. JSON and ZIP

```python title="JSON — for APIs"
def song_list_api(request):
    songs = list(Song.objects.values('id', 'title', 'author'))
    return JsonResponse({'songs': songs})    # sets application/json for you
```

```python title="ZIP — built on the fly"
def download_zip(request):
    response = HttpResponse(content_type='application/zip')
    response['Content-Disposition'] = 'attachment; filename="songs.zip"'
    with zipfile.ZipFile(response, 'w') as zip_file:
        for song in Song.objects.all():
            zip_file.writestr(f"{song.title}.txt", f"Title: {song.title}")
    return response
```

**Dynamic images** — `Pillow (PIL)` for generated graphics.

---

## 27. Checkpoint — Non-HTML Output

??? tip "Mini-challenge: put a QR code on the song detail page encoding the song's URL"

    Use `qrcode` (or `python-barcode`) to generate the image in a view and serve it with the
    right `content_type`.

??? question "One line makes the browser download instead of display. Which?"

    The `Content-Disposition: attachment; filename="..."` header.

---

## 28. Metadata — Data About Data

Tells search engines and social platforms what your content is about **before** they load it.

```python title="The model Meta class — internal metadata"
class Song(models.Model):
    ...
    class Meta:
        ordering = ['title']
        verbose_name = 'Song'
        verbose_name_plural = 'Songs'
        get_latest_by = 'release_date'
```

**SEO metadata** is the outward-facing half:

- **Description tags** — improve click-through rate from Google
- **OpenGraph (OG) tags** — control how links look on WhatsApp, X, LinkedIn

---

## 29. The Syndication Feed Framework

A high-level framework for **RSS and Atom** feeds — really just specialized views that
return XML instead of HTML, for feed readers and aggregators.

```python title="songs/feeds.py"
class LatestSongsFeed(Feed):
    title = "Latest Songs"
    link = "/feeds/latest/"
    description = "Updates on the latest songs added."

    def items(self):
        return Song.objects.order_by('-release_date')[:5]

    def item_title(self, item):
        return item.title

    def item_description(self, item):
        return f"Artist: {item.artist}, Released on: {item.release_date}"
```

```python title="urls.py"
path('feeds/latest/', LatestSongsFeed(), name='latest-songs-feed'),
```

---

## 30. The Sitemap Framework

An XML file listing the important URLs on your site, so search engines crawl it
intelligently — what exists, how often it changes, how important it is.

```python title="songs/sitemaps.py"
class SongSitemap(Sitemap):
    changefreq = "weekly"
    priority = 0.8

    def items(self):
        return Song.objects.all()

    def lastmod(self, obj):
        return obj.updated_at

    def location(self, obj):
        return obj.get_absolute_url()
```

```python title="Integration"
INSTALLED_APPS += ['django.contrib.sitemaps']
path('sitemap.xml', sitemap, {'sitemaps': {'songs': SongSitemap}}, name='sitemap'),
```

!!! quote "The distinction worth memorizing"

    **RSS feeds are for users. Sitemaps are for search engines.**
    Both serve discovery; neither replaces the other.

---

## 31. Best Practices for Discovery

The goal is content that is actually **found**, not merely **online**.

| Strategy | Action |
|---|---|
| Canonical URLs | Define `get_absolute_url()` on models so feeds and sitemaps point at the right link |
| `robots.txt` | Tell bots what to ignore (e.g. `/admin/`) |
| Rich snippets | Schema.org metadata (JSON-LD) for star ratings, prices in search results |
| Validation | Run the W3C Feed Validator so your RSS is not silently broken |

→ [Best practices for metadata and discovery](metadata-and-discovery-frameworks.md#best-practices-for-metadata-and-discovery)

---

## 32. Lab Checklist

- [ ] Split URLs into per-app URLconfs with `include()` and namespaces
- [ ] Replace every hardcoded link with `{% url %}` / `reverse()`
- [ ] Rewrite the song list and detail views as `ListView` and `DetailView`
- [ ] Add create/update/delete with the generic editing views
- [ ] Override `get_queryset()` to hide unpublished songs
- [ ] Add a context processor exposing `site_name` and use it in `base.html`
- [ ] Write one custom filter and one `inclusion_tag`, with the `templatetags/` wiring
- [ ] Add a CSV export view and a PDF detail view
- [ ] Publish an RSS feed and a `sitemap.xml`

---

## 33. Recap

- `include()` + **namespaces** keep URLs modular; **names** keep links refactorable
- CBVs turn `if request.method` into methods; **generic views** delete the boilerplate
- Configure generic views with **attributes**, change behavior by **overriding methods**
- **Context processors** put data in every template; loading order decides who wins
- Custom **tags, filters, and loaders** extend the engine when built-ins fall short
- `HttpResponse` is a file object — that one fact gives you CSV, PDF, JSON, ZIP
- **Feeds for humans, sitemaps for crawlers**

---

## 34. Exit Questions

??? question "Name two things a `ListView` needs before it can render."

    A `model` (or `get_queryset()`) and a template — either `template_name` or the
    conventional `<app>/<model>_list.html`.

??? question "Why must `get_context_data()` call `super()` first?"

    The parent builds the base context (the object list, pagination, the view itself).
    Skip it and the template loses everything it expects.

??? question "You need the logged-in user's plan name in every template. Where does it go?"

    A context processor — not a copy-pasted line in every view.

??? question "How does a CSV view avoid needing a template?"

    It writes directly to the `HttpResponse`, which behaves like a file object, and sets
    `content_type='text/csv'` plus the `Content-Disposition` header.

??? question "A search engine ignores half your pages. Which framework do you reach for?"

    The sitemap framework — and check `robots.txt` and `get_absolute_url()` while you are there.

---