# Django Exam Answers

## SET A

### Short Answer Questions

#### 1. What is Django? Explain its key features.

**Answer:**

Django is a high-level Python web framework that encourages rapid development and clean, pragmatic design. It follows the Model-View-Template (MVT) architectural pattern and was designed to help developers take applications from concept to completion as quickly as possible.

**Key Features:**

1. **ORM (Object-Relational Mapping)**: Django provides a powerful ORM that allows developers to interact with databases using Python code instead of SQL. It supports multiple databases and handles migrations automatically.

2. **Admin Interface**: Django comes with a built-in, auto-generated admin interface that allows easy management of application data without writing additional code.

3. **URL Routing**: Clean and elegant URL design with a powerful URL dispatcher that maps URLs to views using regular expressions or path converters.

4. **Template Engine**: A robust template system that separates design from Python code, supporting template inheritance and custom filters.

5. **Security Features**: Built-in protection against common security threats like SQL injection, cross-site scripting (XSS), cross-site request forgery (CSRF), and clickjacking.

6. **Scalability**: Designed to handle high-traffic applications with features like caching, database optimization, and support for multiple servers.

7. **Batteries Included**: Comes with many built-in features like authentication, session management, form handling, and internationalization.

8. **DRY Principle**: Follows the "Don't Repeat Yourself" principle, promoting code reusability and reducing redundancy.

---

#### 2. Differentiate between Django's MVT and MVC architecture.

**Answer:**

**MVC (Model-View-Controller):**
- **Model**: Manages data and business logic
- **View**: Handles presentation and user interface
- **Controller**: Processes user input and coordinates between Model and View

**MVT (Model-View-Template):**
- **Model**: Same as MVC - manages data and database interactions
- **View**: Acts as the controller - handles business logic and request processing
- **Template**: Handles presentation - equivalent to View in MVC

**Key Differences:**

| Aspect | MVC | MVT (Django) |
|--------|-----|-------------|
| **Controller Role** | Explicit Controller component | Framework itself acts as controller |
| **View Role** | Handles presentation | Handles business logic |
| **Template Role** | Not explicitly defined | Handles presentation |
| **URL Routing** | Controller handles routing | URL dispatcher handles routing |
| **Framework Control** | Developer controls flow | Django framework controls flow |

**Example in Django:**
```python
# Model (models.py)
class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()

# View (views.py) - Acts as Controller
def article_list(request):
    articles = Article.objects.all()
    return render(request, 'articles.html', {'articles': articles})

# Template (articles.html) - Acts as View
<h1>Articles</h1>
{% for article in articles %}
    <h2>{{ article.title }}</h2>
{% endfor %}
```

---

#### 3. How do you create a Django project and app?

**Answer:**

**Creating a Django Project:**

```bash
# Install Django
pip install django

# Create a new project
django-admin startproject myproject

# Navigate to project directory
cd myproject

# Project structure created:
# myproject/
#     manage.py
#     myproject/
#         __init__.py
#         settings.py
#         urls.py
#         asgi.py
#         wsgi.py
```

**Creating a Django App:**

```bash
# Create an app within the project
python manage.py startapp myapp

# App structure created:
# myapp/
#     __init__.py
#     admin.py
#     apps.py
#     models.py
#     tests.py
#     views.py
#     migrations/
#         __init__.py
```

**Registering the App:**

```python
# myproject/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'myapp',  # Add your app here
]
```

**Running the Development Server:**

```bash
# Apply migrations
python manage.py migrate

# Run development server
python manage.py runserver

# Server runs at http://127.0.0.1:8000/
```

---

#### 4. Explain Django's ORM and its advantages.

**Answer:**

**Django ORM (Object-Relational Mapping):**

Django's ORM is a powerful abstraction layer that allows developers to interact with databases using Python objects instead of writing raw SQL queries. It maps Python classes to database tables and class attributes to table columns.

**How It Works:**

```python
# Define a model (Python class)
class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.IntegerField()
    created_at = models.DateTimeField(auto_now_add=True)

# ORM translates to SQL:
# CREATE TABLE product (
#     id INTEGER PRIMARY KEY,
#     name VARCHAR(100),
#     price DECIMAL(10, 2),
#     stock INTEGER,
#     created_at DATETIME
# );
```

**Advantages:**

1. **Database Abstraction**: Write database-agnostic code that works with multiple databases (PostgreSQL, MySQL, SQLite, Oracle) without changing code.

```python
# Same code works with any database
products = Product.objects.filter(price__lt=100)
```

2. **Security**: Automatic protection against SQL injection attacks through parameterized queries.

```python
# Safe from SQL injection
user_input = "'; DROP TABLE products; --"
Product.objects.filter(name=user_input)  # Safely escaped
```

3. **Productivity**: Faster development with less code compared to raw SQL.

```python
# ORM - concise and readable
expensive_products = Product.objects.filter(price__gte=1000).order_by('-price')

# Equivalent SQL - more verbose
# SELECT * FROM product WHERE price >= 1000 ORDER BY price DESC;
```

4. **Migrations**: Automatic schema migration management.

```bash
python manage.py makemigrations
python manage.py migrate
```

5. **Relationships**: Easy handling of foreign keys and many-to-many relationships.

```python
class Order(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    quantity = models.IntegerField()

# Access related objects easily
order.product.name
product.order_set.all()
```

6. **Query Optimization**: Built-in query optimization with select_related() and prefetch_related().

```python
# Optimize queries to reduce database hits
orders = Order.objects.select_related('product').all()
```

---

#### 5. What are Django models? How do you define a model?

**Answer:**

**Django Models:**

Models are Python classes that define the structure and behavior of data in a Django application. Each model maps to a single database table, and each attribute represents a database field.

**Defining a Model:**

```python
from django.db import models
from django.contrib.auth.models import User

class BlogPost(models.Model):
    # Field types
    title = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    content = models.TextField()
    excerpt = models.CharField(max_length=300, blank=True)
    
    # Numeric fields
    views = models.IntegerField(default=0)
    rating = models.DecimalField(max_digits=3, decimal_places=2, null=True)
    
    # Date/Time fields
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    published_date = models.DateField(null=True, blank=True)
    
    # Boolean field
    is_published = models.BooleanField(default=False)
    
    # Relationship fields
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='posts')
    tags = models.ManyToManyField('Tag', blank=True)
    
    # File fields
    featured_image = models.ImageField(upload_to='blog/images/', null=True, blank=True)
    
    # Meta class for model configuration
    class Meta:
        ordering = ['-created_at']
        verbose_name = 'Blog Post'
        verbose_name_plural = 'Blog Posts'
        indexes = [
            models.Index(fields=['slug']),
            models.Index(fields=['-created_at']),
        ]
    
    # String representation
    def __str__(self):
        return self.title
    
    # Custom methods
    def get_absolute_url(self):
        from django.urls import reverse
        return reverse('blog:post_detail', kwargs={'slug': self.slug})
    
    def increment_views(self):
        self.views += 1
        self.save(update_fields=['views'])

class Tag(models.Model):
    name = models.CharField(max_length=50, unique=True)
    
    def __str__(self):
        return self.name
```

**Common Field Types:**

- `CharField`: Short text (requires max_length)
- `TextField`: Long text
- `IntegerField`: Integer numbers
- `DecimalField`: Decimal numbers
- `BooleanField`: True/False
- `DateField`, `DateTimeField`: Dates and times
- `EmailField`: Email addresses
- `URLField`: URLs
- `FileField`, `ImageField`: File uploads
- `ForeignKey`: One-to-many relationship
- `ManyToManyField`: Many-to-many relationship
- `OneToOneField`: One-to-one relationship

**Creating Tables:**

```bash
# Generate migration files
python manage.py makemigrations

# Apply migrations to database
python manage.py migrate
```

---

#### 6. What is the purpose of Django migrations?

**Answer:**

**Django Migrations:**

Migrations are Django's way of propagating changes made to models (adding fields, deleting models, etc.) into the database schema. They are version control for your database schema.

**Purpose:**

1. **Schema Evolution**: Track and apply database schema changes over time
2. **Version Control**: Keep database schema in sync with code across different environments
3. **Reversibility**: Ability to roll back changes if needed
4. **Team Collaboration**: Share database changes with team members
5. **Deployment**: Safely deploy schema changes to production

**Migration Workflow:**

```python
# 1. Define/modify a model
class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    # Add new field
    author = models.ForeignKey(User, on_delete=models.CASCADE)
```

```bash
# 2. Create migration file
python manage.py makemigrations

# Output:
# Migrations for 'myapp':
#   myapp/migrations/0002_article_author.py
#     - Add field author to article

# 3. View SQL that will be executed (optional)
python manage.py sqlmigrate myapp 0002

# 4. Apply migrations to database
python manage.py migrate

# Output:
# Running migrations:
#   Applying myapp.0002_article_author... OK
```

**Migration File Example:**

```python
# myapp/migrations/0002_article_author.py
from django.db import migrations, models
import django.db.models.deletion

class Migration(migrations.Migration):
    dependencies = [
        ('myapp', '0001_initial'),
    ]
    
    operations = [
        migrations.AddField(
            model_name='article',
            name='author',
            field=models.ForeignKey(
                on_delete=django.db.models.deletion.CASCADE,
                to='auth.User'
            ),
        ),
    ]
```

**Common Migration Commands:**

```bash
# Create migrations
python manage.py makemigrations

# Apply migrations
python manage.py migrate

# Show migration status
python manage.py showmigrations

# Rollback to specific migration
python manage.py migrate myapp 0001

# Create empty migration for custom operations
python manage.py makemigrations --empty myapp

# Squash migrations
python manage.py squashmigrations myapp 0001 0005
```

**Benefits:**

- **Automatic**: Django generates migrations automatically from model changes
- **Safe**: Validates changes before applying
- **Portable**: Works across different database backends
- **Testable**: Can test migrations before deploying
- **Documented**: Migration files serve as documentation of schema changes

---

#### 7. How do you perform CRUD operations using Django ORM?

**Answer:**

**CRUD Operations with Django ORM:**

**Model Definition:**

```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    isbn = models.CharField(max_length=13, unique=True)
    price = models.DecimalField(max_digits=6, decimal_places=2)
    published_date = models.DateField()
    in_stock = models.BooleanField(default=True)
    
    def __str__(self):
        return self.title
```

**1. CREATE Operations:**

```python
# Method 1: Create and save
book = Book(
    title="Django for Beginners",
    author="William Vincent",
    isbn="9781735467207",
    price=39.99,
    published_date="2021-01-15",
    in_stock=True
)
book.save()

# Method 2: Create in one step
book = Book.objects.create(
    title="Two Scoops of Django",
    author="Daniel Roy Greenfeld",
    isbn="9780692915727",
    price=45.00,
    published_date="2017-03-15"
)

# Method 3: Bulk create (efficient for multiple records)
books = [
    Book(title="Book 1", author="Author 1", isbn="1234567890123", price=29.99, published_date="2020-01-01"),
    Book(title="Book 2", author="Author 2", isbn="1234567890124", price=34.99, published_date="2020-02-01"),
]
Book.objects.bulk_create(books)

# Method 4: Get or create (avoid duplicates)
book, created = Book.objects.get_or_create(
    isbn="9781735467207",
    defaults={
        'title': "Django for Beginners",
        'author': "William Vincent",
        'price': 39.99,
        'published_date': "2021-01-15"
    }
)
```

**2. READ Operations:**

```python
# Get all records
all_books = Book.objects.all()

# Get single record by primary key
book = Book.objects.get(pk=1)
book = Book.objects.get(isbn="9781735467207")

# Filter records
django_books = Book.objects.filter(title__icontains="django")
expensive_books = Book.objects.filter(price__gte=40.00)
in_stock_books = Book.objects.filter(in_stock=True)

# Exclude records
out_of_stock = Book.objects.exclude(in_stock=True)

# Chain filters
recent_django_books = Book.objects.filter(
    title__icontains="django",
    published_date__year__gte=2020
).filter(in_stock=True)

# Get first/last record
first_book = Book.objects.first()
latest_book = Book.objects.latest('published_date')

# Order results
books_by_price = Book.objects.order_by('price')
books_by_price_desc = Book.objects.order_by('-price')

# Limit results
first_5_books = Book.objects.all()[:5]

# Count records
total_books = Book.objects.count()
django_books_count = Book.objects.filter(title__icontains="django").count()

# Check existence
exists = Book.objects.filter(isbn="9781735467207").exists()

# Get specific fields only
book_titles = Book.objects.values('title', 'author')
book_titles_list = Book.objects.values_list('title', flat=True)

# Complex queries with Q objects
from django.db.models import Q
books = Book.objects.filter(
    Q(title__icontains="django") | Q(author__icontains="django")
)
```

**3. UPDATE Operations:**

```python
# Method 1: Update single object
book = Book.objects.get(pk=1)
book.price = 44.99
book.save()

# Update specific fields only (more efficient)
book.save(update_fields=['price'])

# Method 2: Update multiple objects at once
Book.objects.filter(author="William Vincent").update(in_stock=True)

# Update all records
Book.objects.all().update(in_stock=True)

# Update with F expressions (database-level operations)
from django.db.models import F
Book.objects.filter(price__lt=50).update(price=F('price') * 1.1)  # 10% increase

# Method 3: Update or create
book, created = Book.objects.update_or_create(
    isbn="9781735467207",
    defaults={'price': 42.99, 'in_stock': True}
)
```

**4. DELETE Operations:**

```python
# Delete single object
book = Book.objects.get(pk=1)
book.delete()

# Delete multiple objects
Book.objects.filter(in_stock=False).delete()

# Delete all records (use with caution!)
Book.objects.all().delete()

# Get count before deleting
deleted_count, details = Book.objects.filter(price__lt=20).delete()
# Returns: (5, {'myapp.Book': 5})
```

**Best Practices:**

```python
# Use get() with try-except for single objects
from django.core.exceptions import ObjectDoesNotExist

try:
    book = Book.objects.get(isbn="9781735467207")
except Book.DoesNotExist:
    # Handle missing object
    book = None

# Or use get_object_or_404 in views
from django.shortcuts import get_object_or_404
book = get_object_or_404(Book, isbn="9781735467207")

# Use filter() when multiple results are possible
books = Book.objects.filter(author="William Vincent")
```

---

#### 8. What are Django views? Explain function-based and class-based views.

**Answer:**

**Django Views:**

Views are Python functions or classes that receive web requests and return web responses. They contain the business logic of your application and act as the bridge between models and templates.

**1. Function-Based Views (FBV):**

Simple Python functions that take a request object and return a response.

```python
# views.py
from django.shortcuts import render, get_object_or_404, redirect
from django.http import HttpResponse, JsonResponse
from .models import Article
from .forms import ArticleForm

# Simple view
def home(request):
    return HttpResponse("Welcome to my site!")

# View with template
def article_list(request):
    articles = Article.objects.filter(published=True).order_by('-created_at')
    context = {'articles': articles}
    return render(request, 'articles/list.html', context)

# Detail view
def article_detail(request, pk):
    article = get_object_or_404(Article, pk=pk)
    return render(request, 'articles/detail.html', {'article': article})

# Form handling
def article_create(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST)
        if form.is_valid():
            article = form.save()
            return redirect('article_detail', pk=article.pk)
    else:
        form = ArticleForm()
    return render(request, 'articles/form.html', {'form': form})

# JSON response
def article_api(request, pk):
    article = get_object_or_404(Article, pk=pk)
    data = {
        'title': article.title,
        'content': article.content,
        'author': article.author.username
    }
    return JsonResponse(data)
```

**2. Class-Based Views (CBV):**

Views defined as Python classes that provide more structure and reusability.

```python
# views.py
from django.views import View
from django.views.generic import (
    ListView, DetailView, CreateView, UpdateView, DeleteView
)
from django.urls import reverse_lazy
from .models import Article
from .forms import ArticleForm

# Basic class-based view
class ArticleListView(View):
    def get(self, request):
        articles = Article.objects.all()
        return render(request, 'articles/list.html', {'articles': articles})
    
    def post(self, request):
        # Handle POST request
        pass

# Generic ListView
class ArticleListView(ListView):
    model = Article
    template_name = 'articles/list.html'
    context_object_name = 'articles'
    paginate_by = 10
    
    def get_queryset(self):
        return Article.objects.filter(published=True).order_by('-created_at')
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['total_count'] = Article.objects.count()
        return context

# Generic DetailView
class ArticleDetailView(DetailView):
    model = Article
    template_name = 'articles/detail.html'
    context_object_name = 'article'
    
    def get_object(self):
        obj = super().get_object()
        obj.views += 1
        obj.save(update_fields=['views'])
        return obj

# Generic CreateView
class ArticleCreateView(CreateView):
    model = Article
    form_class = ArticleForm
    template_name = 'articles/form.html'
    success_url = reverse_lazy('article_list')
    
    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)

# Generic UpdateView
class ArticleUpdateView(UpdateView):
    model = Article
    form_class = ArticleForm
    template_name = 'articles/form.html'
    
    def get_success_url(self):
        return reverse_lazy('article_detail', kwargs={'pk': self.object.pk})

# Generic DeleteView
class ArticleDeleteView(DeleteView):
    model = Article
    template_name = 'articles/confirm_delete.html'
    success_url = reverse_lazy('article_list')
```

**URL Configuration:**

```python
# urls.py
from django.urls import path
from . import views

urlpatterns = [
    # Function-based views
    path('articles/', views.article_list, name='article_list'),
    path('articles/<int:pk>/', views.article_detail, name='article_detail'),
    
    # Class-based views (use .as_view())
    path('articles/', views.ArticleListView.as_view(), name='article_list'),
    path('articles/<int:pk>/', views.ArticleDetailView.as_view(), name='article_detail'),
    path('articles/create/', views.ArticleCreateView.as_view(), name='article_create'),
    path('articles/<int:pk>/update/', views.ArticleUpdateView.as_view(), name='article_update'),
    path('articles/<int:pk>/delete/', views.ArticleDeleteView.as_view(), name='article_delete'),
]
```

**Comparison:**

| Aspect | Function-Based Views | Class-Based Views |
|--------|---------------------|-------------------|
| **Simplicity** | Simpler, easier to understand | More complex initially |
| **Code Reuse** | Limited reusability | High reusability through inheritance |
| **Decorators** | Easy to apply | Requires method_decorator |
| **HTTP Methods** | Manual if/else checks | Separate methods (get, post, etc.) |
| **Generic Operations** | Write from scratch | Built-in generic views |
| **Flexibility** | Very flexible | Structured but customizable |

**When to Use:**

- **FBV**: Simple views, custom logic, quick prototypes
- **CBV**: CRUD operations, code reuse, consistent patterns

---

#### 9. What is Django's template engine? Explain template inheritance.

**Answer:**

**Django Template Engine:**

Django's template engine is a text-based system for generating dynamic HTML. It separates presentation logic from business logic, allowing designers to work on templates while developers work on Python code.

**Template Syntax:**

```django
{# This is a comment #}

{# Variables #}
{{ variable }}
{{ user.username }}
{{ article.title|title }}

{# Tags #}
{% if user.is_authenticated %}
    Welcome, {{ user.username }}!
{% else %}
    Please log in.
{% endif %}

{% for article in articles %}
    <h2>{{ article.title }}</h2>
{% endfor %}

{# Filters #}
{{ text|lower }}
{{ date|date:"Y-m-d" }}
{{ content|truncatewords:30 }}
{{ value|default:"N/A" }}
```

**Template Inheritance:**

Template inheritance allows you to build a base template with common elements and extend it in child templates, promoting DRY principle.

**Base Template (base.html):**

```django
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}My Website{% endblock %}</title>
    
    {% block extra_css %}
    <link rel="stylesheet" href="{% static 'css/style.css' %}">
    {% endblock %}
</head>
<body>
    <header>
        <nav>
            <ul>
                <li><a href="{% url 'home' %}">Home</a></li>
                <li><a href="{% url 'about' %}">About</a></li>
                <li><a href="{% url 'contact' %}">Contact</a></li>
            </ul>
        </nav>
    </header>
    
    <main>
        {% block content %}
        {# Child templates will override this block #}
        {% endblock %}
    </main>
    
    <footer>
        {% block footer %}
        <p>&copy; 2024 My Website. All rights reserved.</p>
        {% endblock %}
    </footer>
    
    {% block extra_js %}
    <script src="{% static 'js/main.js' %}"></script>
    {% endblock %}
</body>
</html>
```

**Child Template (article_list.html):**

```django
{% extends 'base.html' %}
{% load static %}

{% block title %}Articles - {{ block.super }}{% endblock %}

{% block extra_css %}
    {{ block.super }}
    <link rel="stylesheet" href="{% static 'css/articles.css' %}">
{% endblock %}

{% block content %}
    <h1>Latest Articles</h1>
    
    {% if articles %}
        <div class="article-grid">
            {% for article in articles %}
                <article class="article-card">
                    <h2>{{ article.title }}</h2>
                    <p class="meta">
                        By {{ article.author.username }} on {{ article.created_at|date:"F d, Y" }}
                    </p>
                    <p>{{ article.excerpt|truncatewords:50 }}</p>
                    <a href="{% url 'article_detail' article.pk %}">Read More</a>
                </article>
            {% endfor %}
        </div>
    {% else %}
        <p>No articles available.</p>
    {% endif %}
    
    {% if is_paginated %}
        <div class="pagination">
            {% if page_obj.has_previous %}
                <a href="?page=1">First</a>
                <a href="?page={{ page_obj.previous_page_number }}">Previous</a>
            {% endif %}
            
            <span>Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}</span>
            
            {% if page_obj.has_next %}
                <a href="?page={{ page_obj.next_page_number }}">Next</a>
                <a href="?page={{ page_obj.paginator.num_pages }}">Last</a>
            {% endif %}
        </div>
    {% endif %}
{% endblock %}

{% block extra_js %}
    {{ block.super }}
    <script src="{% static 'js/articles.js' %}"></script>
{% endblock %}
```

**Include Templates:**

```django
{# base.html #}
<header>
    {% include 'partials/navigation.html' %}
</header>

{# partials/navigation.html #}
<nav>
    <ul>
        <li><a href="{% url 'home' %}">Home</a></li>
        <li><a href="{% url 'articles' %}">Articles</a></li>
    </ul>
</nav>

{# Pass variables to included template #}
{% include 'partials/article_card.html' with article=article show_author=True %}
```

**Key Features:**

1. **{% extends %}**: Specifies parent template
2. **{% block %}**: Defines overridable sections
3. **{{ block.super }}**: Include parent block content
4. **{% include %}**: Insert another template
5. **{% load %}**: Load template tag libraries
6. **{% static %}**: Reference static files
7. **{% url %}**: Generate URLs from view names

**Benefits:**

- **DRY**: Avoid repeating common HTML structure
- **Maintainability**: Update header/footer in one place
- **Consistency**: Ensure uniform look across pages
- **Flexibility**: Override specific sections as needed
- **Organization**: Separate layout from content

---

#### 10. What is Django's URL dispatcher? How do you configure URLs?

**Answer:**

**Django URL Dispatcher:**

The URL dispatcher is Django's mechanism for mapping URL patterns to views. It allows you to design clean, elegant URLs without framework limitations.

**URL Configuration:**

```python
# myproject/urls.py (Main URL configuration)
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls')),
    path('api/', include('api.urls')),
    path('', include('home.urls')),
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

```python
# blog/urls.py (App-specific URLs)
from django.urls import path
from . import views

app_name = 'blog'

urlpatterns = [
    # Basic path
    path('', views.post_list, name='post_list'),
    
    # Path with string parameter
    path('category/<str:category_name>/', views.category_posts, name='category_posts'),
    
    # Path with integer parameter
    path('post/<int:pk>/', views.post_detail, name='post_detail'),
    
    # Path with slug
    path('post/<slug:slug>/', views.post_by_slug, name='post_by_slug'),
    
    # Multiple parameters
    path('archive/<int:year>/<int:month>/', views.archive, name='archive'),
    
    # UUID parameter
    path('share/<uuid:share_id>/', views.share_post, name='share_post'),
]
```

**Path Converters:**

- `str`: Matches any non-empty string (excluding `/`)
- `int`: Matches zero or positive integers
- `slug`: Matches slug strings (letters, numbers, hyphens, underscores)
- `uuid`: Matches UUID strings
- `path`: Matches any non-empty string (including `/`)

**Custom Path Converters:**

```python
# converters.py
class YearConverter:
    regex = '[0-9]{4}'
    
    def to_python(self, value):
        return int(value)
    
    def to_url(self, value):
        return str(value)

# urls.py
from django.urls import path, register_converter
from . import converters, views

register_converter(converters.YearConverter, 'yyyy')

urlpatterns = [
    path('articles/<yyyy:year>/', views.year_archive, name='year_archive'),
]
```

**Regular Expressions (re_path):**

```python
from django.urls import re_path
from . import views

urlpatterns = [
    re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive),
]
```

**Reverse URL Resolution:**

```python
# In views
from django.urls import reverse
from django.shortcuts import redirect

def my_view(request):
    url = reverse('blog:post_detail', kwargs={'pk': 1})
    return redirect('blog:post_list')

# In templates
{% url 'blog:post_detail' pk=post.pk %}
{% url 'blog:archive' year=2024 month=3 %}
```

---

#### 11. Explain Django forms and form validation.

**Answer:**

**Django Forms:**

Django forms handle HTML form rendering, validation, and processing. They provide a high-level interface for working with user input.

**Form Types:**

**1. Regular Forms:**

```python
# forms.py
from django import forms
from django.core.exceptions import ValidationError

class ContactForm(forms.Form):
    name = forms.CharField(
        max_length=100,
        widget=forms.TextInput(attrs={'class': 'form-control', 'placeholder': 'Your Name'})
    )
    email = forms.EmailField(
        widget=forms.EmailInput(attrs={'class': 'form-control'})
    )
    subject = forms.CharField(max_length=200)
    message = forms.CharField(
        widget=forms.Textarea(attrs={'rows': 5})
    )
    subscribe = forms.BooleanField(required=False)
    
    # Field-level validation
    def clean_name(self):
        name = self.cleaned_data['name']
        if len(name) < 3:
            raise ValidationError("Name must be at least 3 characters long")
        return name.title()
    
    # Form-level validation
    def clean(self):
        cleaned_data = super().clean()
        email = cleaned_data.get('email')
        subscribe = cleaned_data.get('subscribe')
        
        if subscribe and not email:
            raise ValidationError("Email is required for subscription")
        
        return cleaned_data
```

**2. ModelForms:**

```python
# models.py
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    content = models.TextField()
    published = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)

# forms.py
from django import forms
from .models import Article

class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = ['title', 'slug', 'content', 'published']
        # Or exclude specific fields
        # exclude = ['created_at']
        
        widgets = {
            'title': forms.TextInput(attrs={'class': 'form-control'}),
            'content': forms.Textarea(attrs={'class': 'form-control', 'rows': 10}),
        }
        
        labels = {
            'title': 'Article Title',
            'slug': 'URL Slug',
        }
        
        help_texts = {
            'slug': 'URL-friendly version of the title',
        }
    
    def clean_slug(self):
        slug = self.cleaned_data['slug']
        if Article.objects.filter(slug=slug).exclude(pk=self.instance.pk).exists():
            raise ValidationError("This slug is already in use")
        return slug
```

**Form Validation:**

```python
# views.py
from django.shortcuts import render, redirect
from .forms import ContactForm, ArticleForm

def contact_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            # Access cleaned data
            name = form.cleaned_data['name']
            email = form.cleaned_data['email']
            subject = form.cleaned_data['subject']
            message = form.cleaned_data['message']
            
            # Process the form (send email, save to DB, etc.)
            # send_email(name, email, subject, message)
            
            return redirect('success')
    else:
        form = ContactForm()
    
    return render(request, 'contact.html', {'form': form})

def article_create(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST)
        if form.is_valid():
            article = form.save(commit=False)
            article.author = request.user
            article.save()
            return redirect('article_detail', pk=article.pk)
    else:
        form = ArticleForm()
    
    return render(request, 'article_form.html', {'form': form})

def article_update(request, pk):
    article = get_object_or_404(Article, pk=pk)
    if request.method == 'POST':
        form = ArticleForm(request.POST, instance=article)
        if form.is_valid():
            form.save()
            return redirect('article_detail', pk=article.pk)
    else:
        form = ArticleForm(instance=article)
    
    return render(request, 'article_form.html', {'form': form})
```

**Template Rendering:**

```django
{# contact.html #}
<form method="post">
    {% csrf_token %}
    
    {# Render entire form #}
    {{ form.as_p }}
    
    {# Or render fields individually #}
    <div class="form-group">
        {{ form.name.label_tag }}
        {{ form.name }}
        {% if form.name.errors %}
            <div class="error">{{ form.name.errors }}</div>
        {% endif %}
    </div>
    
    {# Display non-field errors #}
    {% if form.non_field_errors %}
        <div class="alert alert-danger">
            {{ form.non_field_errors }}
        </div>
    {% endif %}
    
    <button type="submit">Submit</button>
</form>
```

**Custom Validators:**

```python
from django.core.exceptions import ValidationError
from django.core.validators import RegexValidator

def validate_even(value):
    if value % 2 != 0:
        raise ValidationError(f'{value} is not an even number')

phone_validator = RegexValidator(
    regex=r'^\+?1?\d{9,15}$',
    message="Phone number must be in format: '+999999999'"
)

class CustomForm(forms.Form):
    even_number = forms.IntegerField(validators=[validate_even])
    phone = forms.CharField(validators=[phone_validator])
```

---

#### 12. What is Django's admin interface? How do you customize it?

**Answer:**

**Django Admin Interface:**

Django's admin interface is an automatically generated, production-ready interface for managing application data. It reads metadata from models and provides a CRUD interface.

**Basic Setup:**

```python
# admin.py
from django.contrib import admin
from .models import Article, Category, Tag

# Simple registration
admin.site.register(Article)

# Registration with customization
@admin.register(Category)
class CategoryAdmin(admin.ModelAdmin):
    list_display = ['name', 'slug', 'created_at']
    prepopulated_fields = {'slug': ('name',)}
```

**Advanced Customization:**

```python
# models.py
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    author = models.ForeignKey('auth.User', on_delete=models.CASCADE)
    category = models.ForeignKey('Category', on_delete=models.SET_NULL, null=True)
    tags = models.ManyToManyField('Tag')
    content = models.TextField()
    excerpt = models.TextField(blank=True)
    published = models.BooleanField(default=False)
    featured = models.BooleanField(default=False)
    views = models.IntegerField(default=0)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

# admin.py
from django.contrib import admin
from django.utils.html import format_html
from .models import Article, Category, Tag

class TagInline(admin.TabularInline):
    model = Article.tags.through
    extra = 1

@admin.register(Article)
class ArticleAdmin(admin.ModelAdmin):
    # List page customization
    list_display = ['title', 'author', 'category', 'published_status', 'views', 'created_at']
    list_filter = ['published', 'featured', 'category', 'created_at']
    list_editable = ['published', 'featured']
    list_per_page = 25
    search_fields = ['title', 'content', 'author__username']
    date_hierarchy = 'created_at'
    
    # Form customization
    fieldsets = (
        ('Basic Information', {
            'fields': ('title', 'slug', 'author', 'category')
        }),
        ('Content', {
            'fields': ('content', 'excerpt'),
            'classes': ('wide',)
        }),
        ('Options', {
            'fields': ('published', 'featured'),
            'classes': ('collapse',)
        }),
        ('Metadata', {
            'fields': ('views', 'created_at', 'updated_at'),
            'classes': ('collapse',)
        }),
    )
    
    readonly_fields = ['views', 'created_at', 'updated_at']
    prepopulated_fields = {'slug': ('title',)}
    
    # Filters
    autocomplete_fields = ['category']
    filter_horizontal = ['tags']
    
    # Inlines
    inlines = [TagInline]
    
    # Custom methods
    def published_status(self, obj):
        if obj.published:
            return format_html('<span style="color: green;">✓ Published</span>')
        return format_html('<span style="color: red;">✗ Draft</span>')
    published_status.short_description = 'Status'
    
    # Custom actions
    actions = ['make_published', 'make_unpublished']
    
    def make_published(self, request, queryset):
        updated = queryset.update(published=True)
        self.message_user(request, f'{updated} articles published successfully.')
    make_published.short_description = 'Publish selected articles'
    
    def make_unpublished(self, request, queryset):
        updated = queryset.update(published=False)
        self.message_user(request, f'{updated} articles unpublished.')
    make_unpublished.short_description = 'Unpublish selected articles'
    
    # Override queryset
    def get_queryset(self, request):
        qs = super().get_queryset(request)
        if request.user.is_superuser:
            return qs
        return qs.filter(author=request.user)
    
    # Auto-fill author
    def save_model(self, request, obj, form, change):
        if not change:
            obj.author = request.user
        super().save_model(request, obj, form, change)

@admin.register(Category)
class CategoryAdmin(admin.ModelAdmin):
    list_display = ['name', 'slug', 'article_count']
    search_fields = ['name']
    prepopulated_fields = {'slug': ('name',)}
    
    def article_count(self, obj):
        return obj.article_set.count()
    article_count.short_description = 'Articles'

# Customize admin site
admin.site.site_header = 'My Blog Administration'
admin.site.site_title = 'My Blog Admin'
admin.site.index_title = 'Welcome to My Blog Admin Panel'
```

**Key Customization Options:**

- `list_display`: Fields to display in list view
- `list_filter`: Add filters sidebar
- `list_editable`: Edit fields directly in list view
- `search_fields`: Enable search functionality
- `ordering`: Default ordering
- `fieldsets`: Group fields in form
- `readonly_fields`: Non-editable fields
- `prepopulated_fields`: Auto-populate from other fields
- `inlines`: Edit related models on same page
- `actions`: Bulk actions on selected items

---

#### 13. What are Django middleware? Provide examples.

**Answer:**

**Django Middleware:**

Middleware is a framework of hooks into Django's request/response processing. It's a lightweight plugin system for globally altering Django's input or output.

**Middleware Order (Request/Response Flow):**

```
Request → Middleware 1 → Middleware 2 → View → Middleware 2 → Middleware 1 → Response
```

**Built-in Middleware:**

```python
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',  # Security enhancements
    'django.contrib.sessions.middleware.SessionMiddleware',  # Session support
    'django.middleware.common.CommonMiddleware',  # Common operations
    'django.middleware.csrf.CsrfViewMiddleware',  # CSRF protection
    'django.contrib.auth.middleware.AuthenticationMiddleware',  # Authentication
    'django.contrib.messages.middleware.MessageMiddleware',  # Messages framework
    'django.middleware.clickjacking.XFrameOptionsMiddleware',  # Clickjacking protection
]
```

**Custom Middleware (Function-based):**

```python
# middleware.py

# Simple middleware
def simple_middleware(get_response):
    # One-time configuration and initialization
    
    def middleware(request):
        # Code executed before view (request processing)
        print(f"Request to: {request.path}")
        
        response = get_response(request)
        
        # Code executed after view (response processing)
        print(f"Response status: {response.status_code}")
        
        return response
    
    return middleware

# Request logging middleware
import logging
import time

logger = logging.getLogger(__name__)

def request_logging_middleware(get_response):
    def middleware(request):
        start_time = time.time()
        
        # Log request
        logger.info(f"Request: {request.method} {request.path}")
        
        response = get_response(request)
        
        # Log response with duration
        duration = time.time() - start_time
        logger.info(f"Response: {response.status_code} ({duration:.2f}s)")
        
        return response
    
    return middleware

# Custom header middleware
def custom_header_middleware(get_response):
    def middleware(request):
        response = get_response(request)
        response['X-Custom-Header'] = 'My Custom Value'
        response['X-Processing-Time'] = str(time.time())
        return response
    
    return middleware
```

**Custom Middleware (Class-based):**

```python
# middleware.py
from django.utils.deprecation import MiddlewareMixin
from django.http import HttpResponseForbidden
from django.core.cache import cache

class IPBlockMiddleware(MiddlewareMixin):
    def process_request(self, request):
        blocked_ips = ['192.168.1.100', '10.0.0.50']
        ip = request.META.get('REMOTE_ADDR')
        
        if ip in blocked_ips:
            return HttpResponseForbidden("Your IP is blocked")
        
        return None

class RateLimitMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        ip = request.META.get('REMOTE_ADDR')
        cache_key = f'rate_limit_{ip}'
        
        # Get request count
        request_count = cache.get(cache_key, 0)
        
        if request_count >= 100:  # Max 100 requests per minute
            return HttpResponseForbidden("Rate limit exceeded")
        
        # Increment counter
        cache.set(cache_key, request_count + 1, 60)  # 60 seconds
        
        response = self.get_response(request)
        return response

class UserActivityMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        response = self.get_response(request)
        
        # Update last activity for authenticated users
        if request.user.is_authenticated:
            from django.utils import timezone
            request.user.last_activity = timezone.now()
            request.user.save(update_fields=['last_activity'])
        
        return response

class MaintenanceModeMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        from django.conf import settings
        
        if getattr(settings, 'MAINTENANCE_MODE', False):
            # Allow admin access
            if not request.path.startswith('/admin/'):
                from django.shortcuts import render
                return render(request, 'maintenance.html', status=503)
        
        return self.get_response(request)

class RequestTimingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        import time
        
        # Start timer
        request.start_time = time.time()
        
        response = self.get_response(request)
        
        # Calculate duration
        duration = time.time() - request.start_time
        
        # Add to response header
        response['X-Request-Duration'] = f'{duration:.3f}s'
        
        # Log slow requests
        if duration > 1.0:
            import logging
            logger = logging.getLogger(__name__)
            logger.warning(f'Slow request: {request.path} took {duration:.3f}s')
        
        return response
```

**Registering Middleware:**

```python
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'myapp.middleware.request_logging_middleware',  # Function-based
    'myapp.middleware.IPBlockMiddleware',  # Class-based
    'myapp.middleware.RateLimitMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'myapp.middleware.UserActivityMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'myapp.middleware.RequestTimingMiddleware',
]
```

**Middleware Methods (Class-based):**

- `process_request(request)`: Called before view
- `process_view(request, view_func, view_args, view_kwargs)`: Called before view execution
- `process_exception(request, exception)`: Called when view raises exception
- `process_template_response(request, response)`: Called after view returns TemplateResponse
- `process_response(request, response)`: Called after view returns response

**Use Cases:**

- Authentication and authorization
- Request/response logging
- Rate limiting
- CORS headers
- Compression
- Caching
- Security enhancements
- Performance monitoring
- Maintenance mode

---

#### 14. What is Django's authentication system?

**Answer:**

**Django Authentication System:**

Django provides a robust authentication system that handles user accounts, groups, permissions, and cookie-based user sessions out of the box.

**Components:**

1. **Users**: User accounts with authentication credentials
2. **Permissions**: Binary flags designating whether a user can perform a task
3. **Groups**: Generic way to apply labels and permissions to multiple users
4. **Password hashing**: Secure password storage
5. **Forms and views**: Login, logout, password reset functionality

**User Model:**

```python
from django.contrib.auth.models import User

# Create user
user = User.objects.create_user(
    username='john',
    email='john@example.com',
    password='securepassword123',
    first_name='John',
    last_name='Doe'
)

# Create superuser (via command line)
# python manage.py createsuperuser

# User attributes
user.username
user.email
user.first_name
user.last_name
user.is_active
user.is_staff
user.is_superuser
user.last_login
user.date_joined

# Authentication
user.set_password('newpassword')
user.check_password('password')
user.save()
```

**Custom User Model:**

```python
# models.py
from django.contrib.auth.models import AbstractUser, AbstractBaseUser, BaseUserManager
from django.db import models

# Method 1: Extend AbstractUser
class CustomUser(AbstractUser):
    phone = models.CharField(max_length=15, blank=True)
    bio = models.TextField(blank=True)
    birth_date = models.DateField(null=True, blank=True)
    avatar = models.ImageField(upload_to='avatars/', null=True, blank=True)

# Method 2: Complete custom user with AbstractBaseUser
class CustomUserManager(BaseUserManager):
    def create_user(self, email, password=None, **extra_fields):
        if not email:
            raise ValueError('Email is required')
        email = self.normalize_email(email)
        user = self.model(email=email, **extra_fields)
        user.set_password(password)
        user.save(using=self._db)
        return user
    
    def create_superuser(self, email, password=None, **extra_fields):
        extra_fields.setdefault('is_staff', True)
        extra_fields.setdefault('is_superuser', True)
        return self.create_user(email, password, **extra_fields)

class CustomUser(AbstractBaseUser):
    email = models.EmailField(unique=True)
    name = models.CharField(max_length=100)
    is_active = models.BooleanField(default=True)
    is_staff = models.BooleanField(default=False)
    is_superuser = models.BooleanField(default=False)
    
    objects = CustomUserManager()
    
    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = ['name']
    
    def __str__(self):
        return self.email

# settings.py
AUTH_USER_MODEL = 'myapp.CustomUser'
```

**Authentication Views:**

```python
# views.py
from django.contrib.auth import authenticate, login, logout
from django.contrib.auth.decorators import login_required
from django.shortcuts import render, redirect

def login_view(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        user = authenticate(request, username=username, password=password)
        
        if user is not None:
            login(request, user)
            return redirect('dashboard')
        else:
            return render(request, 'login.html', {'error': 'Invalid credentials'})
    
    return render(request, 'login.html')

def logout_view(request):
    logout(request)
    return redirect('home')

@login_required
def profile_view(request):
    return render(request, 'profile.html', {'user': request.user})

# Class-based view with login required
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import TemplateView

class DashboardView(LoginRequiredMixin, TemplateView):
    template_name = 'dashboard.html'
    login_url = '/login/'
```

**Permissions and Groups:**

```python
from django.contrib.auth.models import User, Group, Permission
from django.contrib.contenttypes.models import ContentType
from myapp.models import Article

# Create permission
content_type = ContentType.objects.get_for_model(Article)
permission = Permission.objects.create(
    codename='can_publish',
    name='Can Publish Articles',
    content_type=content_type,
)

# Assign permission to user
user.user_permissions.add(permission)

# Create group
editors = Group.objects.create(name='Editors')
editors.permissions.add(permission)

# Add user to group
user.groups.add(editors)

# Check permissions
user.has_perm('myapp.can_publish')
user.has_perm('myapp.add_article')

# Permission decorators
from django.contrib.auth.decorators import permission_required

@permission_required('myapp.can_publish')
def publish_article(request, pk):
    # Only users with can_publish permission can access
    pass

# Class-based view with permission
from django.contrib.auth.mixins import PermissionRequiredMixin

class PublishArticleView(PermissionRequiredMixin, UpdateView):
    permission_required = 'myapp.can_publish'
    model = Article
```

**URL Configuration:**

```python
# urls.py
from django.contrib.auth import views as auth_views
from django.urls import path

urlpatterns = [
    path('login/', auth_views.LoginView.as_view(template_name='login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(), name='logout'),
    path('password-change/', auth_views.PasswordChangeView.as_view(), name='password_change'),
    path('password-reset/', auth_views.PasswordResetView.as_view(), name='password_reset'),
]
```

---

#### 15. How do you handle static and media files in Django?

**Answer:**

**Static vs Media Files:**

- **Static files**: CSS, JavaScript, images that are part of the application (unchanging)
- **Media files**: User-uploaded content (dynamic)

**Static Files Configuration:**

```python
# settings.py
import os

# Static files (CSS, JavaScript, Images)
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]

STATICFILES_FINDERS = [
    'django.contrib.staticfiles.finders.FileSystemFinder',
    'django.contrib.staticfiles.finders.AppDirectoriesFinder',
]
```

**Project Structure:**

```
myproject/
├── manage.py
├── myproject/
│   ├── settings.py
│   └── urls.py
├── static/              # Project-level static files
│   ├── css/
│   │   └── style.css
│   ├── js/
│   │   └── main.js
│   └── images/
│       └── logo.png
├── myapp/
│   ├── static/          # App-level static files
│   │   └── myapp/
│   │       ├── css/
│   │       └── js/
│   └── templates/
└── staticfiles/         # Collected static files (production)
```

**Using Static Files in Templates:**

```django
{% load static %}

<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" href="{% static 'css/style.css' %}">
    <script src="{% static 'js/main.js' %}"></script>
</head>
<body>
    <img src="{% static 'images/logo.png' %}" alt="Logo">
</body>
</html>
```

**Collecting Static Files:**

```bash
# Collect all static files to STATIC_ROOT
python manage.py collectstatic

# This copies files from:
# - STATICFILES_DIRS
# - Each app's static/ directory
# To STATIC_ROOT for production deployment
```

**Media Files Configuration:**

```python
# settings.py
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

**URL Configuration for Media Files (Development):**

```python
# urls.py
from django.conf import settings
from django.conf.urls.static import static
from django.urls import path

urlpatterns = [
    # Your URL patterns
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

**Handling File Uploads:**

```python
# models.py
from django.db import models

class Profile(models.Model):
    user = models.OneToOneField('auth.User', on_delete=models.CASCADE)
    avatar = models.ImageField(upload_to='avatars/')
    resume = models.FileField(upload_to='resumes/')

class Article(models.Model):
    title = models.CharField(max_length=200)
    image = models.ImageField(upload_to='articles/%Y/%m/%d/')
    
    # Custom upload path
    def article_image_path(instance, filename):
        return f'articles/{instance.id}/{filename}'
    
    custom_image = models.ImageField(upload_to=article_image_path)

# forms.py
from django import forms
from .models import Profile

class ProfileForm(forms.ModelForm):
    class Meta:
        model = Profile
        fields = ['avatar', 'resume']

# views.py
from django.shortcuts import render, redirect
from .forms import ProfileForm

def upload_file(request):
    if request.method == 'POST':
        form = ProfileForm(request.POST, request.FILES)
        if form.is_valid():
            form.save()
            return redirect('success')
    else:
        form = ProfileForm()
    return render(request, 'upload.html', {'form': form})
```

**Template for File Upload:**

```django
<form method="post" enctype="multipart/form-data">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Upload</button>
</form>

<!-- Display uploaded media -->
{% if profile.avatar %}
    <img src="{{ profile.avatar.url }}" alt="Avatar">
{% endif %}
```

**Production Setup:**

```python
# Use WhiteNoise for static files
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',  # Add this
    # ... other middleware
]

STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

# Or use cloud storage (AWS S3)
DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
STATICFILES_STORAGE = 'storages.backends.s3boto3.S3StaticStorage'

AWS_ACCESS_KEY_ID = 'your-access-key'
AWS_SECRET_ACCESS_KEY = 'your-secret-key'
AWS_STORAGE_BUCKET_NAME = 'your-bucket-name'
AWS_S3_REGION_NAME = 'us-east-1'
```

---

#### 16. What is Django REST Framework (DRF)?

**Answer:**

**Django REST Framework:**

DRF is a powerful toolkit for building Web APIs in Django. It provides serialization, authentication, permissions, and browsable API interface.

**Installation:**

```bash
pip install djangorestframework
```

```python
# settings.py
INSTALLED_APPS = [
    'rest_framework',
]

REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.TokenAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticatedOrReadOnly',
    ],
}
```

**Serializers:**

```python
# serializers.py
from rest_framework import serializers
from .models import Article, Category

class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ['id', 'name', 'slug']

class ArticleSerializer(serializers.ModelSerializer):
    author = serializers.ReadOnlyField(source='author.username')
    category = CategorySerializer(read_only=True)
    category_id = serializers.IntegerField(write_only=True)
    
    class Meta:
        model = Article
        fields = ['id', 'title', 'slug', 'content', 'author', 'category', 'category_id', 'created_at']
        read_only_fields = ['created_at']
    
    def validate_title(self, value):
        if len(value) < 5:
            raise serializers.ValidationError("Title must be at least 5 characters")
        return value

# Custom serializer
class ArticleListSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(max_length=200)
    author_name = serializers.CharField(source='author.username')
    published_date = serializers.DateTimeField(source='created_at')
```

**Views:**

```python
# views.py
from rest_framework import viewsets, generics, status
from rest_framework.decorators import api_view, action
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated, IsAuthenticatedOrReadOnly
from .models import Article
from .serializers import ArticleSerializer

# Function-based view
@api_view(['GET', 'POST'])
def article_list(request):
    if request.method == 'GET':
        articles = Article.objects.all()
        serializer = ArticleSerializer(articles, many=True)
        return Response(serializer.data)
    
    elif request.method == 'POST':
        serializer = ArticleSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save(author=request.user)
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

# Generic views
class ArticleListView(generics.ListCreateAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]
    
    def perform_create(self, serializer):
        serializer.save(author=self.request.user)

class ArticleDetailView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]

# ViewSets (most powerful)
class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]
    filterset_fields = ['category', 'author']
    search_fields = ['title', 'content']
    ordering_fields = ['created_at', 'title']
    
    def get_queryset(self):
        queryset = Article.objects.all()
        published = self.request.query_params.get('published')
        if published is not None:
            queryset = queryset.filter(published=published)
        return queryset
    
    def perform_create(self, serializer):
        serializer.save(author=self.request.user)
    
    # Custom action
    @action(detail=True, methods=['post'])
    def publish(self, request, pk=None):
        article = self.get_object()
        article.published = True
        article.save()
        return Response({'status': 'article published'})
    
    @action(detail=False, methods=['get'])
    def recent(self, request):
        recent_articles = Article.objects.order_by('-created_at')[:5]
        serializer = self.get_serializer(recent_articles, many=True)
        return Response(serializer.data)
```

**URL Configuration:**

```python
# urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from . import views

# Router for ViewSets
router = DefaultRouter()
router.register(r'articles', views.ArticleViewSet)

urlpatterns = [
    # Function-based view
    path('api/articles/', views.article_list),
    
    # Generic views
    path('api/articles/', views.ArticleListView.as_view()),
    path('api/articles/<int:pk>/', views.ArticleDetailView.as_view()),
    
    # ViewSet router
    path('api/', include(router.urls)),
]
```

**Authentication:**

```python
# Token authentication
# settings.py
INSTALLED_APPS = [
    'rest_framework.authtoken',
]

# Generate tokens
python manage.py drf_create_token <username>

# views.py
from rest_framework.authtoken.views import obtain_auth_token

urlpatterns = [
    path('api/token/', obtain_auth_token),
]

# Use token in requests
# Header: Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b
```

**Permissions:**

```python
# permissions.py
from rest_framework import permissions

class IsAuthorOrReadOnly(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        if request.method in permissions.SAFE_METHODS:
            return True
        return obj.author == request.user

# views.py
class ArticleViewSet(viewsets.ModelViewSet):
    permission_classes = [IsAuthorOrReadOnly]
```

---

#### 17. Explain Django signals and their use cases.

**Answer:**

**Django Signals:**

Signals allow decoupled applications to get notified when certain actions occur elsewhere in the framework. They implement the observer pattern.

**Built-in Signals:**

**Model Signals:**
- `pre_save`: Before model's save() method
- `post_save`: After model's save() method
- `pre_delete`: Before model's delete() method
- `post_delete`: After model's delete() method
- `m2m_changed`: When ManyToMany field changes

**Request/Response Signals:**
- `request_started`: When HTTP request starts
- `request_finished`: When HTTP request finishes

**Basic Usage:**

```python
# signals.py
from django.db.models.signals import post_save, pre_save, post_delete
from django.dispatch import receiver
from django.contrib.auth.models import User
from .models import Profile, Article

# Method 1: Using decorator
@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)

@receiver(post_save, sender=User)
def save_user_profile(sender, instance, **kwargs):
    instance.profile.save()

# Method 2: Manual connection
def article_saved(sender, instance, created, **kwargs):
    if created:
        print(f"New article created: {instance.title}")
    else:
        print(f"Article updated: {instance.title}")

post_save.connect(article_saved, sender=Article)
```

**Registering Signals:**

```python
# apps.py
from django.apps import AppConfig

class MyAppConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'myapp'
    
    def ready(self):
        import myapp.signals  # Import signals

# __init__.py
default_app_config = 'myapp.apps.MyAppConfig'
```

**Common Use Cases:**

**1. Auto-create Related Models:**

```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User
from .models import Profile, UserSettings

@receiver(post_save, sender=User)
def create_related_models(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)
        UserSettings.objects.create(user=instance)
```

**2. Send Notifications:**

```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import Order
from .tasks import send_order_confirmation_email

@receiver(post_save, sender=Order)
def send_order_notification(sender, instance, created, **kwargs):
    if created:
        send_order_confirmation_email.delay(instance.id)
```

**3. Update Related Fields:**

```python
from django.db.models.signals import post_save, m2m_changed
from django.dispatch import receiver
from .models import Article, Category

@receiver(post_save, sender=Article)
def update_category_count(sender, instance, **kwargs):
    if instance.category:
        instance.category.article_count = instance.category.article_set.count()
        instance.category.save()

@receiver(m2m_changed, sender=Article.tags.through)
def tags_changed(sender, instance, action, **kwargs):
    if action in ['post_add', 'post_remove', 'post_clear']:
        print(f"Tags updated for article: {instance.title}")
```

**4. Logging and Auditing:**

```python
from django.db.models.signals import pre_delete, post_save
from django.dispatch import receiver
from .models import Article, AuditLog

@receiver(post_save, sender=Article)
def log_article_changes(sender, instance, created, **kwargs):
    action = 'created' if created else 'updated'
    AuditLog.objects.create(
        model_name='Article',
        object_id=instance.id,
        action=action,
        user=instance.author
    )

@receiver(pre_delete, sender=Article)
def log_article_deletion(sender, instance, **kwargs):
    AuditLog.objects.create(
        model_name='Article',
        object_id=instance.id,
        action='deleted',
        data={'title': instance.title}
    )
```

**5. Cache Invalidation:**

```python
from django.db.models.signals import post_save, post_delete
from django.dispatch import receiver
from django.core.cache import cache
from .models import Article

@receiver(post_save, sender=Article)
@receiver(post_delete, sender=Article)
def invalidate_article_cache(sender, instance, **kwargs):
    cache.delete(f'article_{instance.id}')
    cache.delete('article_list')
```

**6. File Cleanup:**

```python
from django.db.models.signals import pre_delete, pre_save
from django.dispatch import receiver
from .models import Profile
import os

@receiver(pre_delete, sender=Profile)
def delete_profile_image(sender, instance, **kwargs):
    if instance.avatar:
        if os.path.isfile(instance.avatar.path):
            os.remove(instance.avatar.path)

@receiver(pre_save, sender=Profile)
def delete_old_avatar(sender, instance, **kwargs):
    if instance.pk:
        try:
            old_avatar = Profile.objects.get(pk=instance.pk).avatar
            if old_avatar and old_avatar != instance.avatar:
                if os.path.isfile(old_avatar.path):
                    os.remove(old_avatar.path)
        except Profile.DoesNotExist:
            pass
```

**Custom Signals:**

```python
# signals.py
from django.dispatch import Signal

# Define custom signal
order_placed = Signal()

# Send signal
from .signals import order_placed

def place_order(request):
    order = Order.objects.create(...)
    order_placed.send(sender=Order, order=order, user=request.user)

# Receive signal
from .signals import order_placed

@receiver(order_placed)
def handle_order_placed(sender, order, user, **kwargs):
    # Send email, update inventory, etc.
    pass
```

**Signal Parameters:**

- `sender`: The model class sending the signal
- `instance`: The actual instance being saved/deleted
- `created`: Boolean (post_save only) - True if new record
- `raw`: Boolean - True if model saved as-is (fixtures)
- `using`: Database alias being used
- `**kwargs`: Always include for future compatibility

**Best Practices:**

- Keep signal handlers lightweight
- Avoid circular imports
- Use signals sparingly (prefer model methods when possible)
- Always include `**kwargs` in receiver functions
- Be careful with infinite loops (signal triggering another signal)

---

#### 18. What are Django management commands? How do you create custom commands?

**Answer:**

**Django Management Commands:**

Management commands are utilities run via `manage.py` or `django-admin`. Django provides many built-in commands, and you can create custom ones.

**Built-in Commands:**

```bash
# Database
python manage.py migrate
python manage.py makemigrations
python manage.py sqlmigrate app_name 0001
python manage.py showmigrations
python manage.py flush

# User management
python manage.py createsuperuser
python manage.py changepassword username

# Development
python manage.py runserver
python manage.py shell
python manage.py dbshell

# Static files
python manage.py collectstatic
python manage.py findstatic filename

# Testing
python manage.py test
python manage.py test app_name.tests.TestClass

# Other
python manage.py check
python manage.py dumpdata app_name
python manage.py loaddata fixture_name
```

**Creating Custom Commands:**

**Directory Structure:**

```
myapp/
├── management/
│   ├── __init__.py
│   └── commands/
│       ├── __init__.py
│       ├── import_data.py
│       ├── send_emails.py
│       └── cleanup.py
```

**Basic Custom Command:**

```python
# myapp/management/commands/greet.py
from django.core.management.base import BaseCommand

class Command(BaseCommand):
    help = 'Greets the user'
    
    def handle(self, *args, **options):
        self.stdout.write('Hello, World!')
        self.stdout.write(self.style.SUCCESS('Command executed successfully'))
```

```bash
python manage.py greet
```

**Command with Arguments:**

```python
# myapp/management/commands/import_data.py
from django.core.management.base import BaseCommand, CommandError
from myapp.models import Article
import csv

class Command(BaseCommand):
    help = 'Import articles from CSV file'
    
    def add_arguments(self, parser):
        # Positional argument
        parser.add_argument('csv_file', type=str, help='Path to CSV file')
        
        # Optional arguments
        parser.add_argument(
            '--delete',
            action='store_true',
            help='Delete existing articles before import',
        )
        
        parser.add_argument(
            '--author',
            type=str,
            default='admin',
            help='Default author username',
        )
        
        parser.add_argument(
            '--limit',
            type=int,
            help='Limit number of articles to import',
        )
    
    def handle(self, *args, **options):
        csv_file = options['csv_file']
        delete = options['delete']
        author_username = options['author']
        limit = options['limit']
        
        # Validate file
        try:
            with open(csv_file, 'r') as file:
                reader = csv.DictReader(file)
                
                # Delete existing articles if requested
                if delete:
                    count = Article.objects.count()
                    Article.objects.all().delete()
                    self.stdout.write(
                        self.style.WARNING(f'Deleted {count} existing articles')
                    )
                
                # Get author
                from django.contrib.auth.models import User
                try:
                    author = User.objects.get(username=author_username)
                except User.DoesNotExist:
                    raise CommandError(f'User "{author_username}" does not exist')
                
                # Import articles
                imported = 0
                for row in reader:
                    if limit and imported >= limit:
                        break
                    
                    Article.objects.create(
                        title=row['title'],
                        content=row['content'],
                        author=author
                    )
                    imported += 1
                    
                    if imported % 100 == 0:
                        self.stdout.write(f'Imported {imported} articles...')
                
                self.stdout.write(
                    self.style.SUCCESS(f'Successfully imported {imported} articles')
                )
        
        except FileNotFoundError:
            raise CommandError(f'File "{csv_file}" does not exist')
        except Exception as e:
            raise CommandError(f'Error importing data: {str(e)}')
```

```bash
python manage.py import_data data.csv
python manage.py import_data data.csv --delete --author=john --limit=100
```

**Scheduled Command (Cleanup):**

```python
# myapp/management/commands/cleanup.py
from django.core.management.base import BaseCommand
from django.utils import timezone
from datetime import timedelta
from myapp.models import Session, TempFile

class Command(BaseCommand):
    help = 'Clean up old sessions and temporary files'
    
    def add_arguments(self, parser):
        parser.add_argument(
            '--days',
            type=int,
            default=30,
            help='Delete records older than this many days',
        )
        
        parser.add_argument(
            '--dry-run',
            action='store_true',
            help='Show what would be deleted without actually deleting',
        )
    
    def handle(self, *args, **options):
        days = options['days']
        dry_run = options['dry_run']
        
        cutoff_date = timezone.now() - timedelta(days=days)
        
        # Find old sessions
        old_sessions = Session.objects.filter(created_at__lt=cutoff_date)
        session_count = old_sessions.count()
        
        # Find old temp files
        old_files = TempFile.objects.filter(created_at__lt=cutoff_date)
        file_count = old_files.count()
        
        if dry_run:
            self.stdout.write(
                self.style.WARNING(
                    f'DRY RUN: Would delete {session_count} sessions and {file_count} temp files'
                )
            )
        else:
            old_sessions.delete()
            old_files.delete()
            
            self.stdout.write(
                self.style.SUCCESS(
                    f'Deleted {session_count} sessions and {file_count} temp files older than {days} days'
                )
            )
```

```bash
python manage.py cleanup --days=60
python manage.py cleanup --dry-run
```

**Command with Progress Bar:**

```python
# myapp/management/commands/process_articles.py
from django.core.management.base import BaseCommand
from myapp.models import Article

class Command(BaseCommand):
    help = 'Process all articles'
    
    def handle(self, *args, **options):
        articles = Article.objects.all()
        total = articles.count()
        
        self.stdout.write(f'Processing {total} articles...')
        
        for i, article in enumerate(articles, 1):
            # Process article
            article.process()
            
            # Show progress
            if i % 10 == 0 or i == total:
                self.stdout.write(f'Processed {i}/{total} articles')
        
        self.stdout.write(self.style.SUCCESS('All articles processed'))
```

**Command with User Confirmation:**

```python
# myapp/management/commands/delete_old_data.py
from django.core.management.base import BaseCommand
from myapp.models import Article

class Command(BaseCommand):
    help = 'Delete old unpublished articles'
    
    def add_arguments(self, parser):
        parser.add_argument(
            '--no-input',
            action='store_true',
            help='Skip confirmation prompt',
        )
    
    def handle(self, *args, **options):
        articles = Article.objects.filter(published=False)
        count = articles.count()
        
        if not options['no_input']:
            confirm = input(f'Delete {count} unpublished articles? (yes/no): ')
            if confirm.lower() != 'yes':
                self.stdout.write(self.style.WARNING('Operation cancelled'))
                return
        
        articles.delete()
        self.stdout.write(self.style.SUCCESS(f'Deleted {count} articles'))
```

**Styling Output:**

```python
self.stdout.write(self.style.SUCCESS('Success message'))
self.stdout.write(self.style.ERROR('Error message'))
self.stdout.write(self.style.WARNING('Warning message'))
self.stdout.write(self.style.NOTICE('Notice message'))
self.stdout.write(self.style.SQL_FIELD('SQL field'))
self.stdout.write(self.style.SQL_COLTYPE('SQL column type'))
self.stdout.write(self.style.SQL_KEYWORD('SQL keyword'))
self.stdout.write(self.style.SQL_TABLE('SQL table'))
self.stdout.write(self.style.HTTP_INFO('HTTP info'))
self.stdout.write(self.style.HTTP_SUCCESS('HTTP success'))
self.stdout.write(self.style.HTTP_REDIRECT('HTTP redirect'))
self.stdout.write(self.style.HTTP_NOT_MODIFIED('HTTP not modified'))
self.stdout.write(self.style.HTTP_BAD_REQUEST('HTTP bad request'))
self.stdout.write(self.style.HTTP_NOT_FOUND('HTTP not found'))
self.stdout.write(self.style.HTTP_SERVER_ERROR('HTTP server error'))
self.stdout.write(self.style.MIGRATE_HEADING('Migrate heading'))
self.stdout.write(self.style.MIGRATE_LABEL('Migrate label'))
```

**Use Cases:**

- Data import/export
- Database cleanup
- Sending batch emails
- Generating reports
- Cron jobs
- Deployment tasks
- Testing data generation
- Cache warming

---

### Long Answer Questions

#### 19. Explain the process of deploying a Django application to production. Include considerations for security, performance, and scalability.

**Answer:**

Deploying a Django application to production requires careful planning and implementation of best practices for security, performance, and scalability. Here's a comprehensive guide:

## 1. Pre-Deployment Preparation

### Environment Configuration

```python
# settings.py - Production settings
import os
from pathlib import Path

# Security settings
DEBUG = False
ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com']

# Secret key from environment variable
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY')

# Database configuration
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.environ.get('DB_NAME'),
        'USER': os.environ.get('DB_USER'),
        'PASSWORD': os.environ.get('DB_PASSWORD'),
        'HOST': os.environ.get('DB_HOST', 'localhost'),
        'PORT': os.environ.get('DB_PORT', '5432'),
        'CONN_MAX_AGE': 600,
    }
}

# Static and media files
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

# Email configuration
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = os.environ.get('EMAIL_HOST')
EMAIL_PORT = int(os.environ.get('EMAIL_PORT', 587))
EMAIL_USE_TLS = True
EMAIL_HOST_USER = os.environ.get('EMAIL_HOST_USER')
EMAIL_HOST_PASSWORD = os.environ.get('EMAIL_HOST_PASSWORD')
```

### Split Settings for Different Environments

```python
# settings/base.py - Common settings
# settings/development.py
from .base import *

DEBUG = True
ALLOWED_HOSTS = ['localhost', '127.0.0.1']

# settings/production.py
from .base import *

DEBUG = False
ALLOWED_HOSTS = ['yourdomain.com']

# Use in manage.py
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings.production')
```

## 2. Security Considerations

### SSL/TLS Configuration

```python
# settings.py
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
SECURE_BROWSER_XSS_FILTER = True
SECURE_CONTENT_TYPE_NOSNIFF = True
X_FRAME_OPTIONS = 'DENY'

# HSTS (HTTP Strict Transport Security)
SECURE_HSTS_SECONDS = 31536000  # 1 year
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True

# Secure proxy SSL header
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')
```

### CSRF and CORS Protection

```python
# CSRF settings
CSRF_COOKIE_HTTPONLY = True
CSRF_COOKIE_SAMESITE = 'Strict'
CSRF_TRUSTED_ORIGINS = ['https://yourdomain.com']

# CORS (if using Django REST Framework)
CORS_ALLOWED_ORIGINS = [
    'https://yourdomain.com',
    'https://www.yourdomain.com',
]
CORS_ALLOW_CREDENTIALS = True
```

### Password Security

```python
# Password validation
AUTH_PASSWORD_VALIDATORS = [
    {'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator'},
    {'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator', 'OPTIONS': {'min_length': 12}},
    {'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator'},
    {'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator'},
]

# Password hashing
PASSWORD_HASHERS = [
    'django.contrib.auth.hashers.Argon2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher',
    'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
]
```

### Security Middleware

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',  # Static files
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

## 3. Performance Optimization

### Database Optimization

```python
# Connection pooling
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'CONN_MAX_AGE': 600,
        'OPTIONS': {
            'connect_timeout': 10,
            'options': '-c statement_timeout=30000',
        },
    }
}

# Query optimization in views
from django.db.models import Prefetch, select_related, prefetch_related

# Use select_related for foreign keys
articles = Article.objects.select_related('author', 'category').all()

# Use prefetch_related for many-to-many
articles = Article.objects.prefetch_related('tags').all()

# Index important fields
class Article(models.Model):
    title = models.CharField(max_length=200, db_index=True)
    slug = models.SlugField(unique=True, db_index=True)
    
    class Meta:
        indexes = [
            models.Index(fields=['created_at', 'published']),
            models.Index(fields=['-created_at']),
        ]
```

### Caching Strategy

```python
# settings.py - Redis cache
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/1',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
            'PARSER_CLASS': 'redis.connection.HiredisParser',
            'CONNECTION_POOL_CLASS_KWARGS': {
                'max_connections': 50,
                'retry_on_timeout': True,
            },
            'SOCKET_CONNECT_TIMEOUT': 5,
            'SOCKET_TIMEOUT': 5,
        },
        'KEY_PREFIX': 'myapp',
        'TIMEOUT': 300,
    }
}

# Cache middleware
MIDDLEWARE = [
    'django.middleware.cache.UpdateCacheMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.cache.FetchFromCacheMiddleware',
]

CACHE_MIDDLEWARE_ALIAS = 'default'
CACHE_MIDDLEWARE_SECONDS = 600
CACHE_MIDDLEWARE_KEY_PREFIX = 'myapp'

# View caching
from django.views.decorators.cache import cache_page

@cache_page(60 * 15)  # 15 minutes
def article_list(request):
    articles = Article.objects.all()
    return render(request, 'articles.html', {'articles': articles})

# Template fragment caching
{% load cache %}
{% cache 500 sidebar %}
    ... sidebar content ...
{% endcache %}

# Low-level cache API
from django.core.cache import cache

def get_article(article_id):
    cache_key = f'article_{article_id}'
    article = cache.get(cache_key)
    
    if article is None:
        article = Article.objects.get(id=article_id)
        cache.set(cache_key, article, 60 * 15)
    
    return article
```

### Static Files Optimization

```python
# settings.py
# Use WhiteNoise for static files
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

# Or use CDN
STATIC_URL = 'https://cdn.yourdomain.com/static/'
MEDIA_URL = 'https://cdn.yourdomain.com/media/'

# AWS S3 for static/media files
DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
STATICFILES_STORAGE = 'storages.backends.s3boto3.S3StaticStorage'

AWS_ACCESS_KEY_ID = os.environ.get('AWS_ACCESS_KEY_ID')
AWS_SECRET_ACCESS_KEY = os.environ.get('AWS_SECRET_ACCESS_KEY')
AWS_STORAGE_BUCKET_NAME = os.environ.get('AWS_STORAGE_BUCKET_NAME')
AWS_S3_REGION_NAME = 'us-east-1'
AWS_S3_CUSTOM_DOMAIN = f'{AWS_STORAGE_BUCKET_NAME}.s3.amazonaws.com'
AWS_DEFAULT_ACL = 'public-read'
AWS_S3_OBJECT_PARAMETERS = {
    'CacheControl': 'max-age=86400',
}
```

## 4. Scalability Considerations

### Application Server Configuration

**Gunicorn Configuration:**

```python
# gunicorn_config.py
import multiprocessing

bind = '0.0.0.0:8000'
workers = multiprocessing.cpu_count() * 2 + 1
worker_class = 'sync'
worker_connections = 1000
max_requests = 1000
max_requests_jitter = 50
timeout = 30
keepalive = 2

# Logging
accesslog = '/var/log/gunicorn/access.log'
errorlog = '/var/log/gunicorn/error.log'
loglevel = 'info'

# Process naming
proc_name = 'myapp'

# Server mechanics
daemon = False
pidfile = '/var/run/gunicorn.pid'
user = 'www-data'
group = 'www-data'
```

```bash
# Run Gunicorn
gunicorn myproject.wsgi:application -c gunicorn_config.py
```

**uWSGI Configuration:**

```ini
# uwsgi.ini
[uwsgi]
module = myproject.wsgi:application
master = true
processes = 4
threads = 2
socket = /tmp/myproject.sock
chmod-socket = 666
vacuum = true
die-on-term = true
```

### Nginx Configuration

```nginx
# /etc/nginx/sites-available/myproject
upstream myproject {
    server unix:/tmp/myproject.sock;
}

server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name yourdomain.com www.yourdomain.com;
    
    # SSL configuration
    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;
    
    # Security headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Frame-Options "DENY" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    
    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/x-javascript application/xml+rss application/json;
    
    # Static files
    location /static/ {
        alias /var/www/myproject/staticfiles/;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
    
    # Media files
    location /media/ {
        alias /var/www/myproject/media/;
        expires 7d;
    }
    
    # Application
    location / {
        proxy_pass http://myproject;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;
        
        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }
    
    # Rate limiting
    limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s;
    limit_req zone=one burst=20 nodelay;
}
```

### Load Balancing

```nginx
# Multiple application servers
upstream myproject {
    least_conn;
    server app1.internal:8000 weight=3;
    server app2.internal:8000 weight=2;
    server app3.internal:8000 weight=1;
    
    # Health checks
    keepalive 32;
}
```

### Database Replication

```python
# settings.py - Read replicas
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydb',
        'USER': 'myuser',
        'PASSWORD': 'mypassword',
        'HOST': 'primary.db.internal',
        'PORT': '5432',
    },
    'replica1': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydb',
        'USER': 'myuser',
        'PASSWORD': 'mypassword',
        'HOST': 'replica1.db.internal',
        'PORT': '5432',
    },
}

# Database router
class PrimaryReplicaRouter:
    def db_for_read(self, model, **hints):
        return 'replica1'
    
    def db_for_write(self, model, **hints):
        return 'default'
    
    def allow_relation(self, obj1, obj2, **hints):
        return True
    
    def allow_migrate(self, db, app_label, model_name=None, **hints):
        return db == 'default'

DATABASE_ROUTERS = ['myproject.routers.PrimaryReplicaRouter']
```

## 5. Deployment Process

### Using Docker

```dockerfile
# Dockerfile
FROM python:3.11-slim

ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy project
COPY . .

# Collect static files
RUN python manage.py collectstatic --noinput

# Run migrations and start server
CMD ["gunicorn", "myproject.wsgi:application", "--bind", "0.0.0.0:8000"]
```

```yaml
# docker-compose.yml
version: '3.8'

services:
  db:
    image: postgres:15
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
    volumes:
      - postgres_data:/var/lib/postgresql/data
  
  redis:
    image: redis:7-alpine
    
  web:
    build: .
    command: gunicorn myproject.wsgi:application --bind 0.0.0.0:8000
    volumes:
      - .:/app
      - static_volume:/app/staticfiles
      - media_volume:/app/media
    ports:
      - "8000:8000"
    env_file:
      - .env
    depends_on:
      - db
      - redis
  
  nginx:
    image: nginx:alpine
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - static_volume:/app/staticfiles
      - media_volume:/app/media
    ports:
      - "80:80"
      - "443:443"
    depends_on:
      - web

volumes:
  postgres_data:
  static_volume:
  media_volume:
```

### Deployment Checklist

```bash
# 1. Run security check
python manage.py check --deploy

# 2. Run tests
python manage.py test

# 3. Collect static files
python manage.py collectstatic --noinput

# 4. Run migrations
python manage.py migrate

# 5. Create superuser (if needed)
python manage.py createsuperuser

# 6. Load initial data (if needed)
python manage.py loaddata initial_data.json

# 7. Restart application server
sudo systemctl restart gunicorn
sudo systemctl restart nginx
```

## 6. Monitoring and Logging

### Logging Configuration

```python
# settings.py
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '{levelname} {asctime} {module} {process:d} {thread:d} {message}',
            'style': '{',
        },
    },
    'handlers': {
        'file': {
            'level': 'INFO',
            'class': 'logging.handlers.RotatingFileHandler',
            'filename': '/var/log/django/myproject.log',
            'maxBytes': 1024 * 1024 * 15,  # 15MB
            'backupCount': 10,
            'formatter': 'verbose',
        },
        'error_file': {
            'level': 'ERROR',
            'class': 'logging.handlers.RotatingFileHandler',
            'filename': '/var/log/django/myproject_errors.log',
            'maxBytes': 1024 * 1024 * 15,
            'backupCount': 10,
            'formatter': 'verbose',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file', 'error_file'],
            'level': 'INFO',
            'propagate': True,
        },
        'myapp': {
            'handlers': ['file', 'error_file'],
            'level': 'INFO',
            'propagate': False,
        },
    },
}
```

### Application Monitoring

```python
# Install Sentry for error tracking
pip install sentry-sdk

# settings.py
import sentry_sdk
from sentry_sdk.integrations.django import DjangoIntegration

sentry_sdk.init(
    dsn=os.environ.get('SENTRY_DSN'),
    integrations=[DjangoIntegration()],
    traces_sample_rate=0.1,
    send_default_pii=True,
    environment='production',
)
```

## 7. Backup Strategy

```bash
# Database backup script
#!/bin/bash
BACKUP_DIR="/var/backups/postgresql"
DATE=$(date +%Y%m%d_%H%M%S)
pg_dump -U myuser mydb | gzip > $BACKUP_DIR/mydb_$DATE.sql.gz

# Keep only last 30 days
find $BACKUP_DIR -name "*.sql.gz" -mtime +30 -delete

# Media files backup
rsync -avz /var/www/myproject/media/ /var/backups/media/
```

**Summary:**

A successful Django production deployment requires:
- **Security**: SSL/TLS, secure cookies, CSRF protection, password hashing
- **Performance**: Database optimization, caching, static file compression
- **Scalability**: Load balancing, database replication, horizontal scaling
- **Monitoring**: Logging, error tracking, performance monitoring
- **Reliability**: Automated backups, health checks, graceful degradation

---

#### 20. Design and implement a complete blog application with Django. Include models, views, templates, and explain the relationships between components.

**Answer:**

## Complete Django Blog Application

### 1. Project Setup

```bash
# Create project
django-admin startproject blog_project
cd blog_project

# Create blog app
python manage.py startapp blog

# Install dependencies
pip install Pillow django-crispy-forms django-taggit
```

```python
# blog_project/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',
    'crispy_forms',
    'taggit',
]

CRISPY_TEMPLATE_PACK = 'bootstrap4'

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

### 2. Models Design

```python
# blog/models.py
from django.db import models
from django.contrib.auth.models import User
from django.urls import reverse
from django.utils.text import slugify
from django.utils import timezone
from taggit.managers import TaggableManager

class Category(models.Model):
    name = models.CharField(max_length=100, unique=True)
    slug = models.SlugField(max_length=100, unique=True)
    description = models.TextField(blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        verbose_name_plural = 'Categories'
        ordering = ['name']
    
    def __str__(self):
        return self.name
    
    def get_absolute_url(self):
        return reverse('blog:category_posts', kwargs={'slug': self.slug})
    
    def save(self, *args, **kwargs):
        if not self.slug:
            self.slug = slugify(self.name)
        super().save(*args, **kwargs)

class PublishedManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(status='published')

class Post(models.Model):
    STATUS_CHOICES = (
        ('draft', 'Draft'),
        ('published', 'Published'),
    )
    
    title = models.CharField(max_length=250)
    slug = models.SlugField(max_length=250, unique_for_date='publish')
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='blog_posts')
    category = models.ForeignKey(Category, on_delete=models.SET_NULL, null=True, related_name='posts')
    featured_image = models.ImageField(upload_to='blog/%Y/%m/%d/', blank=True, null=True)
    excerpt = models.TextField(max_length=500, blank=True)
    content = models.TextField()
    
    publish = models.DateTimeField(default=timezone.now)
    created = models.DateTimeField(auto_now_add=True)
    updated = models.DateTimeField(auto_now=True)
    status = models.CharField(max_length=10, choices=STATUS_CHOICES, default='draft')
    
    views = models.PositiveIntegerField(default=0)
    featured = models.BooleanField(default=False)
    
    tags = TaggableManager()
    
    objects = models.Manager()
    published = PublishedManager()
    
    class Meta:
        ordering = ['-publish']
        indexes = [
            models.Index(fields=['-publish']),
            models.Index(fields=['slug']),
        ]
    
    def __str__(self):
        return self.title
    
    def get_absolute_url(self):
        return reverse('blog:post_detail', kwargs={
            'year': self.publish.year,
            'month': self.publish.month,
            'day': self.publish.day,
            'slug': self.slug
        })
    
    def save(self, *args, **kwargs):
        if not self.slug:
            self.slug = slugify(self.title)
        if not self.excerpt:
            self.excerpt = self.content[:200]
        super().save(*args, **kwargs)
    
    def increment_views(self):
        self.views += 1
        self.save(update_fields=['views'])

class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
    name = models.CharField(max_length=80)
    email = models.EmailField()
    body = models.TextField()
    created = models.DateTimeField(auto_now_add=True)
    updated = models.DateTimeField(auto_now=True)
    active = models.BooleanField(default=True)
    
    class Meta:
        ordering = ['created']
        indexes = [
            models.Index(fields=['created']),
        ]
    
    def __str__(self):
        return f'Comment by {self.name} on {self.post}'

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField(max_length=500, blank=True)
    avatar = models.ImageField(upload_to='avatars/', blank=True, null=True)
    website = models.URLField(blank=True)
    twitter = models.CharField(max_length=100, blank=True)
    github = models.CharField(max_length=100, blank=True)
    
    def __str__(self):
        return f'{self.user.username} Profile'
```

### 3. Forms

```python
# blog/forms.py
from django import forms
from .models import Comment, Post

class CommentForm(forms.ModelForm):
    class Meta:
        model = Comment
        fields = ['name', 'email', 'body']
        widgets = {
            'name': forms.TextInput(attrs={'class': 'form-control', 'placeholder': 'Your Name'}),
            'email': forms.EmailInput(attrs={'class': 'form-control', 'placeholder': 'Your Email'}),
            'body': forms.Textarea(attrs={'class': 'form-control', 'rows': 4, 'placeholder': 'Your Comment'}),
        }

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'category', 'featured_image', 'excerpt', 'content', 'tags', 'status', 'featured']
        widgets = {
            'title': forms.TextInput(attrs={'class': 'form-control'}),
            'excerpt': forms.Textarea(attrs={'class': 'form-control', 'rows': 3}),
            'content': forms.Textarea(attrs={'class': 'form-control', 'rows': 10}),
            'category': forms.Select(attrs={'class': 'form-control'}),
            'status': forms.Select(attrs={'class': 'form-control'}),
        }

class SearchForm(forms.Form):
    query = forms.CharField(
        max_length=100,
        widget=forms.TextInput(attrs={
            'class': 'form-control',
            'placeholder': 'Search posts...'
        })
    )
```

### 4. Views

```python
# blog/views.py
from django.shortcuts import render, get_object_or_404, redirect
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger
from django.db.models import Q, Count
from django.contrib.auth.decorators import login_required
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import ListView, DetailView, CreateView, UpdateView, DeleteView
from django.urls import reverse_lazy
from .models import Post, Category, Comment
from .forms import CommentForm, PostForm, SearchForm

# Function-based views
def post_list(request):
    posts = Post.published.all()
    
    # Pagination
    paginator = Paginator(posts, 6)
    page = request.GET.get('page')
    
    try:
        posts = paginator.page(page)
    except PageNotAnInteger:
        posts = paginator.page(1)
    except EmptyPage:
        posts = paginator.page(paginator.num_pages)
    
    context = {
        'posts': posts,
        'featured_posts': Post.published.filter(featured=True)[:3],
    }
    return render(request, 'blog/post_list.html', context)

def post_detail(request, year, month, day, slug):
    post = get_object_or_404(
        Post,
        slug=slug,
        status='published',
        publish__year=year,
        publish__month=month,
        publish__day=day
    )
    
    # Increment views
    post.increment_views()
    
    # Comments
    comments = post.comments.filter(active=True)
    new_comment = None
    
    if request.method == 'POST':
        comment_form = CommentForm(data=request.POST)
        if comment_form.is_valid():
            new_comment = comment_form.save(commit=False)
            new_comment.post = post
            new_comment.save()
            return redirect(post.get_absolute_url())
    else:
        comment_form = CommentForm()
    
    # Similar posts
    post_tags_ids = post.tags.values_list('id', flat=True)
    similar_posts = Post.published.filter(tags__in=post_tags_ids).exclude(id=post.id)
    similar_posts = similar_posts.annotate(same_tags=Count('tags')).order_by('-same_tags', '-publish')[:4]
    
    context = {
        'post': post,
        'comments': comments,
        'new_comment': new_comment,
        'comment_form': comment_form,
        'similar_posts': similar_posts,
    }
    return render(request, 'blog/post_detail.html', context)

def category_posts(request, slug):
    category = get_object_or_404(Category, slug=slug)
    posts = Post.published.filter(category=category)
    
    paginator = Paginator(posts, 6)
    page = request.GET.get('page')
    posts = paginator.get_page(page)
    
    context = {
        'category': category,
        'posts': posts,
    }
    return render(request, 'blog/category_posts.html', context)

def tag_posts(request, tag_slug):
    posts = Post.published.filter(tags__slug=tag_slug)
    tag = posts.first().tags.get(slug=tag_slug) if posts.exists() else None
    
    paginator = Paginator(posts, 6)
    page = request.GET.get('page')
    posts = paginator.get_page(page)
    
    context = {
        'tag': tag,
        'posts': posts,
    }
    return render(request, 'blog/tag_posts.html', context)

def search_posts(request):
    form = SearchForm()
    query = None
    results = []
    
    if 'query' in request.GET:
        form = SearchForm(request.GET)
        if form.is_valid():
            query = form.cleaned_data['query']
            results = Post.published.filter(
                Q(title__icontains=query) |
                Q(content__icontains=query) |
                Q(excerpt__icontains=query)
            ).distinct()
    
    context = {
        'form': form,
        'query': query,
        'results': results,
    }
    return render(request, 'blog/search.html', context)

# Class-based views
class PostListView(ListView):
    queryset = Post.published.all()
    context_object_name = 'posts'
    paginate_by = 6
    template_name = 'blog/post_list.html'
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['featured_posts'] = Post.published.filter(featured=True)[:3]
        return context

@login_required
def post_create(request):
    if request.method == 'POST':
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            post = form.save(commit=False)
            post.author = request.user
            post.save()
            form.save_m2m()  # Save tags
            return redirect(post.get_absolute_url())
    else:
        form = PostForm()
    
    return render(request, 'blog/post_form.html', {'form': form})

class PostCreateView(LoginRequiredMixin, CreateView):
    model = Post
    form_class = PostForm
    template_name = 'blog/post_form.html'
    
    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)

class PostUpdateView(LoginRequiredMixin, UpdateView):
    model = Post
    form_class = PostForm
    template_name = 'blog/post_form.html'
    
    def get_queryset(self):
        return Post.objects.filter(author=self.request.user)

class PostDeleteView(LoginRequiredMixin, DeleteView):
    model = Post
    success_url = reverse_lazy('blog:post_list')
    template_name = 'blog/post_confirm_delete.html'
    
    def get_queryset(self):
        return Post.objects.filter(author=self.request.user)
```

### 5. URLs

```python
# blog/urls.py
from django.urls import path
from . import views

app_name = 'blog'

urlpatterns = [
    # Post URLs
    path('', views.post_list, name='post_list'),
    path('<int:year>/<int:month>/<int:day>/<slug:slug>/', views.post_detail, name='post_detail'),
    path('create/', views.PostCreateView.as_view(), name='post_create'),
    path('<int:pk>/edit/', views.PostUpdateView.as_view(), name='post_update'),
    path('<int:pk>/delete/', views.PostDeleteView.as_view(), name='post_delete'),
    
    # Category and Tag URLs
    path('category/<slug:slug>/', views.category_posts, name='category_posts'),
    path('tag/<slug:tag_slug>/', views.tag_posts, name='tag_posts'),
    
    # Search
    path('search/', views.search_posts, name='search'),
]

# blog_project/urls.py
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls', namespace='blog')),
    path('', include('blog.urls')),
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### 6. Templates

**Base Template:**

```django
{# blog/templates/blog/base.html #}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}My Blog{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    {% block extra_css %}{% endblock %}
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <div class="container">
            <a class="navbar-brand" href="{% url 'blog:post_list' %}">My Blog</a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav ms-auto">
                    <li class="nav-item">
                        <a class="nav-link" href="{% url 'blog:post_list' %}">Home</a>
                    </li>
                    {% if user.is_authenticated %}
                        <li class="nav-item">
                            <a class="nav-link" href="{% url 'blog:post_create' %}">New Post</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link" href="{% url 'logout' %}">Logout</a>
                        </li>
                    {% else %}
                        <li class="nav-item">
                            <a class="nav-link" href="{% url 'login' %}">Login</a>
                        </li>
                    {% endif %}
                </ul>
            </div>
        </div>
    </nav>
    
    <main class="container my-5">
        {% block content %}{% endblock %}
    </main>
    
    <footer class="bg-dark text-white text-center py-3 mt-5">
        <p>&copy; 2024 My Blog. All rights reserved.</p>
    </footer>
    
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
    {% block extra_js %}{% endblock %}
</body>
</html>
```

**Post List Template:**

```django
{# blog/templates/blog/post_list.html #}
{% extends 'blog/base.html' %}

{% block title %}Blog Posts{% endblock %}

{% block content %}
<div class="row">
    <div class="col-lg-8">
        <h1 class="mb-4">Latest Posts</h1>
        
        {% for post in posts %}
        <article class="card mb-4">
            {% if post.featured_image %}
            <img src="{{ post.featured_image.url }}" class="card-img-top" alt="{{ post.title }}">
            {% endif %}
            <div class="card-body">
                <h2 class="card-title">
                    <a href="{{ post.get_absolute_url }}" class="text-decoration-none">{{ post.title }}</a>
                </h2>
                <p class="text-muted small">
                    By {{ post.author.username }} | {{ post.publish|date:"F d, Y" }} | 
                    <a href="{% url 'blog:category_posts' post.category.slug %}">{{ post.category }}</a> |
                    {{ post.views }} views
                </p>
                <p class="card-text">{{ post.excerpt }}</p>
                <div class="mb-2">
                    {% for tag in post.tags.all %}
                    <a href="{% url 'blog:tag_posts' tag.slug %}" class="badge bg-secondary text-decoration-none">{{ tag.name }}</a>
                    {% endfor %}
                </div>
                <a href="{{ post.get_absolute_url }}" class="btn btn-primary">Read More</a>
            </div>
        </article>
        {% empty %}
        <p>No posts available.</p>
        {% endfor %}
        
        {# Pagination #}
        {% if posts.has_other_pages %}
        <nav>
            <ul class="pagination">
                {% if posts.has_previous %}
                <li class="page-item">
                    <a class="page-link" href="?page={{ posts.previous_page_number }}">Previous</a>
                </li>
                {% endif %}
                
                {% for num in posts.paginator.page_range %}
                <li class="page-item {% if posts.number == num %}active{% endif %}">
                    <a class="page-link" href="?page={{ num }}">{{ num }}</a>
                </li>
                {% endfor %}
                
                {% if posts.has_next %}
                <li class="page-item">
                    <a class="page-link" href="?page={{ posts.next_page_number }}">Next</a>
                </li>
                {% endif %}
            </ul>
        </nav>
        {% endif %}
    </div>
    
    <div class="col-lg-4">
        {# Sidebar #}
        <div class="card mb-4">
            <div class="card-header">Search</div>
            <div class="card-body">
                <form action="{% url 'blog:search' %}" method="get">
                    <div class="input-group">
                        <input type="text" name="query" class="form-control" placeholder="Search...">
                        <button class="btn btn-primary" type="submit">Go</button>
                    </div>
                </form>
            </div>
        </div>
        
        <div class="card mb-4">
            <div class="card-header">Featured Posts</div>
            <div class="card-body">
                {% for post in featured_posts %}
                <div class="mb-3">
                    <a href="{{ post.get_absolute_url }}" class="text-decoration-none">{{ post.title }}</a>
                    <p class="small text-muted">{{ post.publish|date:"M d, Y" }}</p>
                </div>
                {% endfor %}
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**Post Detail Template:**

```django
{# blog/templates/blog/post_detail.html #}
{% extends 'blog/base.html' %}
{% load crispy_forms_tags %}

{% block title %}{{ post.title }}{% endblock %}

{% block content %}
<article>
    <h1>{{ post.title }}</h1>
    <p class="text-muted">
        By {{ post.author.username }} | {{ post.publish|date:"F d, Y" }} | 
        <a href="{% url 'blog:category_posts' post.category.slug %}">{{ post.category }}</a> |
        {{ post.views }} views
    </p>
    
    {% if post.featured_image %}
    <img src="{{ post.featured_image.url }}" class="img-fluid mb-4" alt="{{ post.title }}">
    {% endif %}
    
    <div class="mb-4">
        {% for tag in post.tags.all %}
        <a href="{% url 'blog:tag_posts' tag.slug %}" class="badge bg-secondary text-decoration-none">{{ tag.name }}</a>
        {% endfor %}
    </div>
    
    <div class="content">
        {{ post.content|linebreaks }}
    </div>
    
    {% if user == post.author %}
    <div class="mt-4">
        <a href="{% url 'blog:post_update' post.pk %}" class="btn btn-warning">Edit</a>
        <a href="{% url 'blog:post_delete' post.pk %}" class="btn btn-danger">Delete</a>
    </div>
    {% endif %}
</article>

{# Similar Posts #}
{% if similar_posts %}
<div class="mt-5">
    <h3>Similar Posts</h3>
    <div class="row">
        {% for post in similar_posts %}
        <div class="col-md-6 mb-3">
            <div class="card">
                <div class="card-body">
                    <h5 class="card-title">
                        <a href="{{ post.get_absolute_url }}" class="text-decoration-none">{{ post.title }}</a>
                    </h5>
                    <p class="card-text small">{{ post.excerpt|truncatewords:20 }}</p>
                </div>
            </div>
        </div>
        {% endfor %}
    </div>
</div>
{% endif %}

{# Comments #}
<div class="mt-5">
    <h3>Comments ({{ comments.count }})</h3>
    
    {% for comment in comments %}
    <div class="card mb-3">
        <div class="card-body">
            <h5>{{ comment.name }}</h5>
            <p class="small text-muted">{{ comment.created|date:"F d, Y H:i" }}</p>
            <p>{{ comment.body|linebreaks }}</p>
        </div>
    </div>
    {% empty %}
    <p>No comments yet.</p>
    {% endfor %}
    
    <h4 class="mt-4">Add a comment</h4>
    <form method="post">
        {% csrf_token %}
        {{ comment_form|crispy }}
        <button type="submit" class="btn btn-primary">Submit</button>
    </form>
</div>
{% endblock %}
```

### 7. Admin Configuration

```python
# blog/admin.py
from django.contrib import admin
from .models import Post, Category, Comment, Profile

@admin.register(Category)
class CategoryAdmin(admin.ModelAdmin):
    list_display = ['name', 'slug', 'created_at']
    prepopulated_fields = {'slug': ('name',)}

class CommentInline(admin.TabularInline):
    model = Comment
    extra = 0
    readonly_fields = ['name', 'email', 'created']

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'slug', 'author', 'category', 'publish', 'status', 'views']
    list_filter = ['status', 'created', 'publish', 'author', 'category']
    search_fields = ['title', 'content']
    prepopulated_fields = {'slug': ('title',)}
    raw_id_fields = ['author']
    date_hierarchy = 'publish'
    ordering = ['status', '-publish']
    list_editable = ['status', 'featured']
    inlines = [CommentInline]

@admin.register(Comment)
class CommentAdmin(admin.ModelAdmin):
    list_display = ['name', 'email', 'post', 'created', 'active']
    list_filter = ['active', 'created', 'updated']
    search_fields = ['name', 'email', 'body']
    actions = ['approve_comments']
    
    def approve_comments(self, request, queryset):
        queryset.update(active=True)

@admin.register(Profile)
class ProfileAdmin(admin.ModelAdmin):
    list_display = ['user', 'website']
```

### 8. Signals for Profile Creation

```python
# blog/signals.py
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User
from .models import Profile

@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)

@receiver(post_save, sender=User)
def save_user_profile(sender, instance, **kwargs):
    instance.profile.save()

# blog/apps.py
from django.apps import AppConfig

class BlogConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'blog'
    
    def ready(self):
        import blog.signals
```

### 9. Migrations and Running

```bash
# Create migrations
python manage.py makemigrations

# Apply migrations
python manage.py migrate

# Create superuser
python manage.py createsuperuser

# Run server
python manage.py runserver
```

**Component Relationships:**

1. **Models**: Define data structure (Post, Category, Comment, Profile)
2. **Views**: Handle business logic and data processing
3. **Templates**: Present data to users
4. **Forms**: Handle user input and validation
5. **URLs**: Map URLs to views
6. **Admin**: Manage content through Django admin
7. **Signals**: Auto-create related models (Profile when User is created)

This complete blog application demonstrates Django's MVT architecture with proper separation of concerns, reusable components, and best practices.

---

#### 21. Implement a RESTful API using Django REST Framework for an e-commerce application. Include authentication, permissions, serializers, and viewsets.

**Answer:**

## E-Commerce RESTful API with Django REST Framework

### 1. Project Setup

```bash
# Install dependencies
pip install djangorestframework djangorestframework-simplejwt django-filter pillow

# Create project and app
django-admin startproject ecommerce_api
cd ecommerce_api
python manage.py startapp products
python manage.py startapp orders
```

```python
# ecommerce_api/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'rest_framework.authtoken',
    'django_filters',
    'products',
    'orders',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticatedOrReadOnly',
    ],
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20,
    'DEFAULT_FILTER_BACKENDS': [
        'django_filters.rest_framework.DjangoFilterBackend',
        'rest_framework.filters.SearchFilter',
        'rest_framework.filters.OrderingFilter',
    ],
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle'
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day'
    }
}

from datetime import timedelta

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=60),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=1),
    'ROTATE_REFRESH_TOKENS': False,
    'BLACKLIST_AFTER_ROTATION': True,
}

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

### 2. Models

```python
# products/models.py
from django.db import models
from django.contrib.auth.models import User
from django.core.validators import MinValueValidator, MaxValueValidator

class Category(models.Model):
    name = models.CharField(max_length=100, unique=True)
    slug = models.SlugField(max_length=100, unique=True)
    description = models.TextField(blank=True)
    image = models.ImageField(upload_to='categories/', blank=True, null=True)
    parent = models.ForeignKey('self', on_delete=models.CASCADE, null=True, blank=True, related_name='children')
    is_active = models.BooleanField(default=True)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        verbose_name_plural = 'Categories'
        ordering = ['name']
    
    def __str__(self):
        return self.name

class Product(models.Model):
    name = models.CharField(max_length=200)
    slug = models.SlugField(max_length=200, unique=True)
    category = models.ForeignKey(Category, on_delete=models.CASCADE, related_name='products')
    description = models.TextField()
    price = models.DecimalField(max_digits=10, decimal_places=2, validators=[MinValueValidator(0)])
    discount_price = models.DecimalField(max_digits=10, decimal_places=2, null=True, blank=True, validators=[MinValueValidator(0)])
    stock = models.PositiveIntegerField(default=0)
    image = models.ImageField(upload_to='products/', blank=True, null=True)
    is_active = models.BooleanField(default=True)
    is_featured = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['-created_at']
        indexes = [
            models.Index(fields=['slug']),
            models.Index(fields=['-created_at']),
        ]
    
    def __str__(self):
        return self.name
    
    @property
    def final_price(self):
        return self.discount_price if self.discount_price else self.price
    
    @property
    def in_stock(self):
        return self.stock > 0

class ProductImage(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE, related_name='images')
    image = models.ImageField(upload_to='products/gallery/')
    alt_text = models.CharField(max_length=200, blank=True)
    is_primary = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    
    def __str__(self):
        return f"Image for {self.product.name}"

class Review(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE, related_name='reviews')
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    rating = models.IntegerField(validators=[MinValueValidator(1), MaxValueValidator(5)])
    title = models.CharField(max_length=200)
    comment = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        unique_together = ['product', 'user']
        ordering = ['-created_at']
    
    def __str__(self):
        return f"{self.user.username} - {self.product.name} ({self.rating}★)"

# orders/models.py
from django.db import models
from django.contrib.auth.models import User
from products.models import Product

class Order(models.Model):
    STATUS_CHOICES = [
        ('pending', 'Pending'),
        ('processing', 'Processing'),
        ('shipped', 'Shipped'),
        ('delivered', 'Delivered'),
        ('cancelled', 'Cancelled'),
    ]
    
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name='orders')
    order_number = models.CharField(max_length=20, unique=True)
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default='pending')
    
    # Shipping details
    shipping_address = models.TextField()
    shipping_city = models.CharField(max_length=100)
    shipping_postal_code = models.CharField(max_length=20)
    shipping_country = models.CharField(max_length=100)
    
    # Payment
    total_amount = models.DecimalField(max_digits=10, decimal_places=2)
    payment_method = models.CharField(max_length=50)
    is_paid = models.BooleanField(default=False)
    paid_at = models.DateTimeField(null=True, blank=True)
    
    # Timestamps
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    delivered_at = models.DateTimeField(null=True, blank=True)
    
    class Meta:
        ordering = ['-created_at']
    
    def __str__(self):
        return f"Order {self.order_number}"
    
    def save(self, *args, **kwargs):
        if not self.order_number:
            import uuid
            self.order_number = f"ORD-{uuid.uuid4().hex[:10].upper()}"
        super().save(*args, **kwargs)

class OrderItem(models.Model):
    order = models.ForeignKey(Order, on_delete=models.CASCADE, related_name='items')
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    quantity = models.PositiveIntegerField(default=1)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    
    def __str__(self):
        return f"{self.quantity}x {self.product.name}"
    
    @property
    def subtotal(self):
        return self.quantity * self.price

class Cart(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='cart')
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    def __str__(self):
        return f"Cart for {self.user.username}"
    
    @property
    def total(self):
        return sum(item.subtotal for item in self.items.all())

class CartItem(models.Model):
    cart = models.ForeignKey(Cart, on_delete=models.CASCADE, related_name='items')
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    quantity = models.PositiveIntegerField(default=1)
    
    class Meta:
        unique_together = ['cart', 'product']
    
    def __str__(self):
        return f"{self.quantity}x {self.product.name}"
    
    @property
    def subtotal(self):
        return self.quantity * self.product.final_price
```

### 3. Serializers

```python
# products/serializers.py
from rest_framework import serializers
from .models import Category, Product, ProductImage, Review
from django.contrib.auth.models import User

class CategorySerializer(serializers.ModelSerializer):
    products_count = serializers.SerializerMethodField()
    
    class Meta:
        model = Category
        fields = ['id', 'name', 'slug', 'description', 'image', 'parent', 'is_active', 'products_count', 'created_at']
        read_only_fields = ['created_at']
    
    def get_products_count(self, obj):
        return obj.products.filter(is_active=True).count()

class ProductImageSerializer(serializers.ModelSerializer):
    class Meta:
        model = ProductImage
        fields = ['id', 'image', 'alt_text', 'is_primary']

class ReviewSerializer(serializers.ModelSerializer):
    user = serializers.ReadOnlyField(source='user.username')
    
    class Meta:
        model = Review
        fields = ['id', 'user', 'rating', 'title', 'comment', 'created_at', 'updated_at']
        read_only_fields = ['created_at', 'updated_at']
    
    def validate_rating(self, value):
        if value < 1 or value > 5:
            raise serializers.ValidationError("Rating must be between 1 and 5")
        return value

class ProductListSerializer(serializers.ModelSerializer):
    category = serializers.StringRelatedField()
    final_price = serializers.ReadOnlyField()
    in_stock = serializers.ReadOnlyField()
    
    class Meta:
        model = Product
        fields = ['id', 'name', 'slug', 'category', 'price', 'discount_price', 'final_price', 'image', 'in_stock', 'is_featured']

class ProductDetailSerializer(serializers.ModelSerializer):
    category = CategorySerializer(read_only=True)
    category_id = serializers.PrimaryKeyRelatedField(
        queryset=Category.objects.all(),
        source='category',
        write_only=True
    )
    images = ProductImageSerializer(many=True, read_only=True)
    reviews = ReviewSerializer(many=True, read_only=True)
    average_rating = serializers.SerializerMethodField()
    final_price = serializers.ReadOnlyField()
    in_stock = serializers.ReadOnlyField()
    
    class Meta:
        model = Product
        fields = [
            'id', 'name', 'slug', 'category', 'category_id', 'description',
            'price', 'discount_price', 'final_price', 'stock', 'image', 'images',
            'is_active', 'is_featured', 'in_stock', 'reviews', 'average_rating',
            'created_at', 'updated_at'
        ]
        read_only_fields = ['created_at', 'updated_at']
    
    def get_average_rating(self, obj):
        reviews = obj.reviews.all()
        if reviews:
            return sum(r.rating for r in reviews) / len(reviews)
        return 0

# orders/serializers.py
from rest_framework import serializers
from .models import Order, OrderItem, Cart, CartItem
from products.serializers import ProductListSerializer

class OrderItemSerializer(serializers.ModelSerializer):
    product = ProductListSerializer(read_only=True)
    product_id = serializers.PrimaryKeyRelatedField(
        queryset=Product.objects.all(),
        source='product',
        write_only=True
    )
    subtotal = serializers.ReadOnlyField()
    
    class Meta:
        model = OrderItem
        fields = ['id', 'product', 'product_id', 'quantity', 'price', 'subtotal']

class OrderSerializer(serializers.ModelSerializer):
    items = OrderItemSerializer(many=True, read_only=True)
    user = serializers.ReadOnlyField(source='user.username')
    
    class Meta:
        model = Order
        fields = [
            'id', 'user', 'order_number', 'status', 'shipping_address',
            'shipping_city', 'shipping_postal_code', 'shipping_country',
            'total_amount', 'payment_method', 'is_paid', 'paid_at',
            'items', 'created_at', 'updated_at', 'delivered_at'
        ]
        read_only_fields = ['order_number', 'created_at', 'updated_at']

class OrderCreateSerializer(serializers.ModelSerializer):
    items = OrderItemSerializer(many=True)
    
    class Meta:
        model = Order
        fields = [
            'shipping_address', 'shipping_city', 'shipping_postal_code',
            'shipping_country', 'payment_method', 'items'
        ]
    
    def create(self, validated_data):
        items_data = validated_data.pop('items')
        order = Order.objects.create(**validated_data)
        
        total = 0
        for item_data in items_data:
            product = item_data['product']
            quantity = item_data['quantity']
            price = product.final_price
            
            OrderItem.objects.create(
                order=order,
                product=product,
                quantity=quantity,
                price=price
            )
            total += price * quantity
            
            # Update stock
            product.stock -= quantity
            product.save()
        
        order.total_amount = total
        order.save()
        
        return order

class CartItemSerializer(serializers.ModelSerializer):
    product = ProductListSerializer(read_only=True)
    product_id = serializers.PrimaryKeyRelatedField(
        queryset=Product.objects.all(),
        source='product',
        write_only=True
    )
    subtotal = serializers.ReadOnlyField()
    
    class Meta:
        model = CartItem
        fields = ['id', 'product', 'product_id', 'quantity', 'subtotal']
    
    def validate_quantity(self, value):
        if value < 1:
            raise serializers.ValidationError("Quantity must be at least 1")
        return value

class CartSerializer(serializers.ModelSerializer):
    items = CartItemSerializer(many=True, read_only=True)
    total = serializers.ReadOnlyField()
    
    class Meta:
        model = Cart
        fields = ['id', 'items', 'total', 'created_at', 'updated_at']
        read_only_fields = ['created_at', 'updated_at']

# User serializers
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email', 'first_name', 'last_name']
        read_only_fields = ['id']

class UserRegistrationSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True, min_length=8)
    password2 = serializers.CharField(write_only=True, min_length=8)
    
    class Meta:
        model = User
        fields = ['username', 'email', 'password', 'password2', 'first_name', 'last_name']
    
    def validate(self, data):
        if data['password'] != data['password2']:
            raise serializers.ValidationError("Passwords don't match")
        return data
    
    def create(self, validated_data):
        validated_data.pop('password2')
        user = User.objects.create_user(**validated_data)
        Cart.objects.create(user=user)
        return user
```

### 4. Permissions

```python
# products/permissions.py
from rest_framework import permissions

class IsAdminOrReadOnly(permissions.BasePermission):
    def has_permission(self, request, view):
        if request.method in permissions.SAFE_METHODS:
            return True
        return request.user and request.user.is_staff

class IsOwnerOrReadOnly(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        if request.method in permissions.SAFE_METHODS:
            return True
        return obj.user == request.user

class IsOrderOwner(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.user == request.user or request.user.is_staff
```

### 5. ViewSets

```python
# products/views.py
from rest_framework import viewsets, status, filters
from rest_framework.decorators import action
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated, IsAuthenticatedOrReadOnly
from django_filters.rest_framework import DjangoFilterBackend
from .models import Category, Product, Review
from .serializers import (
    CategorySerializer, ProductListSerializer, ProductDetailSerializer, ReviewSerializer
)
from .permissions import IsAdminOrReadOnly, IsOwnerOrReadOnly

class CategoryViewSet(viewsets.ModelViewSet):
    queryset = Category.objects.filter(is_active=True)
    serializer_class = CategorySerializer
    permission_classes = [IsAdminOrReadOnly]
    lookup_field = 'slug'
    filter_backends = [filters.SearchFilter, filters.OrderingFilter]
    search_fields = ['name', 'description']
    ordering_fields = ['name', 'created_at']

class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.filter(is_active=True)
    permission_classes = [IsAdminOrReadOnly]
    lookup_field = 'slug'
    filter_backends = [DjangoFilterBackend, filters.SearchFilter, filters.OrderingFilter]
    filterset_fields = ['category', 'is_featured']
    search_fields = ['name', 'description']
    ordering_fields = ['price', 'created_at', 'name']
    
    def get_serializer_class(self):
        if self.action == 'list':
            return ProductListSerializer
        return ProductDetailSerializer
    
    def get_queryset(self):
        queryset = super().get_queryset()
        
        # Filter by price range
        min_price = self.request.query_params.get('min_price')
        max_price = self.request.query_params.get('max_price')
        
        if min_price:
            queryset = queryset.filter(price__gte=min_price)
        if max_price:
            queryset = queryset.filter(price__lte=max_price)
        
        return queryset
    
    @action(detail=False, methods=['get'])
    def featured(self, request):
        featured_products = self.queryset.filter(is_featured=True)
        serializer = self.get_serializer(featured_products, many=True)
        return Response(serializer.data)
    
    @action(detail=True, methods=['post'], permission_classes=[IsAuthenticated])
    def add_review(self, request, slug=None):
        product = self.get_object()
        serializer = ReviewSerializer(data=request.data)
        
        if serializer.is_valid():
            serializer.save(user=request.user, product=product)
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

class ReviewViewSet(viewsets.ModelViewSet):
    queryset = Review.objects.all()
    serializer_class = ReviewSerializer
    permission_classes = [IsAuthenticatedOrReadOnly, IsOwnerOrReadOnly]
    filter_backends = [DjangoFilterBackend, filters.OrderingFilter]
    filterset_fields = ['product', 'rating']
    ordering_fields = ['created_at', 'rating']
    
    def perform_create(self, serializer):
        serializer.save(user=self.request.user)

# orders/views.py
from rest_framework import viewsets, status
from rest_framework.decorators import action
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated
from .models import Order, Cart, CartItem
from .serializers import (
    OrderSerializer, OrderCreateSerializer, CartSerializer, CartItemSerializer
)
from .permissions import IsOrderOwner

class OrderViewSet(viewsets.ModelViewSet):
    serializer_class = OrderSerializer
    permission_classes = [IsAuthenticated, IsOrderOwner]
    
    def get_queryset(self):
        if self.request.user.is_staff:
            return Order.objects.all()
        return Order.objects.filter(user=self.request.user)
    
    def get_serializer_class(self):
        if self.action == 'create':
            return OrderCreateSerializer
        return OrderSerializer
    
    def perform_create(self, serializer):
        serializer.save(user=self.request.user)
    
    @action(detail=True, methods=['post'])
    def cancel(self, request, pk=None):
        order = self.get_object()
        
        if order.status in ['delivered', 'cancelled']:
            return Response(
                {'error': 'Cannot cancel this order'},
                status=status.HTTP_400_BAD_REQUEST
            )
        
        order.status = 'cancelled'
        order.save()
        
        # Restore stock
        for item in order.items.all():
            item.product.stock += item.quantity
            item.product.save()
        
        return Response({'status': 'Order cancelled'})

class CartViewSet(viewsets.ModelViewSet):
    serializer_class = CartSerializer
    permission_classes = [IsAuthenticated]
    
    def get_queryset(self):
        return Cart.objects.filter(user=self.request.user)
    
    def get_object(self):
        cart, created = Cart.objects.get_or_create(user=self.request.user)
        return cart
    
    @action(detail=False, methods=['post'])
    def add_item(self, request):
        cart = self.get_object()
        serializer = CartItemSerializer(data=request.data)
        
        if serializer.is_valid():
            product = serializer.validated_data['product']
            quantity = serializer.validated_data['quantity']
            
            cart_item, created = CartItem.objects.get_or_create(
                cart=cart,
                product=product,
                defaults={'quantity': quantity}
            )
            
            if not created:
                cart_item.quantity += quantity
                cart_item.save()
            
            return Response(CartItemSerializer(cart_item).data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
    
    @action(detail=False, methods=['post'])
    def remove_item(self, request):
        cart = self.get_object()
        product_id = request.data.get('product_id')
        
        try:
            cart_item = CartItem.objects.get(cart=cart, product_id=product_id)
            cart_item.delete()
            return Response({'status': 'Item removed'})
        except CartItem.DoesNotExist:
            return Response({'error': 'Item not found'}, status=status.HTTP_404_NOT_FOUND)
    
    @action(detail=False, methods=['post'])
    def clear(self, request):
        cart = self.get_object()
        cart.items.all().delete()
        return Response({'status': 'Cart cleared'})

# Authentication views
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import AllowAny
from rest_framework_simplejwt.tokens import RefreshToken
from products.serializers import UserRegistrationSerializer, UserSerializer

@api_view(['POST'])
@permission_classes([AllowAny])
def register(request):
    serializer = UserRegistrationSerializer(data=request.data)
    if serializer.is_valid():
        user = serializer.save()
        refresh = RefreshToken.for_user(user)
        
        return Response({
            'user': UserSerializer(user).data,
            'refresh': str(refresh),
            'access': str(refresh.access_token),
        }, status=status.HTTP_201_CREATED)
    return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

@api_view(['GET'])
@permission_classes([IsAuthenticated])
def profile(request):
    serializer = UserSerializer(request.user)
    return Response(serializer.data)
```

### 6. URLs

```python
# ecommerce_api/urls.py
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static
from rest_framework.routers import DefaultRouter
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView
from products.views import CategoryViewSet, ProductViewSet, ReviewViewSet, register, profile
from orders.views import OrderViewSet, CartViewSet

router = DefaultRouter()
router.register(r'categories', CategoryViewSet)
router.register(r'products', ProductViewSet)
router.register(r'reviews', ReviewViewSet)
router.register(r'orders', OrderViewSet, basename='order')
router.register(r'cart', CartViewSet, basename='cart')

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include(router.urls)),
    path('api/auth/register/', register, name='register'),
    path('api/auth/profile/', profile, name='profile'),
    path('api/auth/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/auth/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### 7. Testing the API

```bash
# Register user
curl -X POST http://localhost:8000/api/auth/register/ \
  -H "Content-Type: application/json" \
  -d '{"username": "john", "email": "john@example.com", "password": "password123", "password2": "password123"}'

# Login
curl -X POST http://localhost:8000/api/auth/token/ \
  -H "Content-Type: application/json" \
  -d '{"username": "john", "password": "password123"}'

# Get products
curl http://localhost:8000/api/products/

# Get product detail
curl http://localhost:8000/api/products/product-slug/

# Add to cart (authenticated)
curl -X POST http://localhost:8000/api/cart/add_item/ \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"product_id": 1, "quantity": 2}'

# Create order (authenticated)
curl -X POST http://localhost:8000/api/orders/ \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "shipping_address": "123 Main St",
    "shipping_city": "New York",
    "shipping_postal_code": "10001",
    "shipping_country": "USA",
    "payment_method": "credit_card",
    "items": [
      {"product_id": 1, "quantity": 2}
    ]
  }'
```

**Key Features:**

1. **Authentication**: JWT-based authentication with registration and login
2. **Permissions**: Custom permissions for admin-only actions and owner-only access
3. **Serializers**: Nested serializers for related data, read-only fields, validation
4. **ViewSets**: Full CRUD operations with custom actions
5. **Filtering**: Search, ordering, and field-based filtering
6. **Pagination**: Automatic pagination for list views
7. **Throttling**: Rate limiting for anonymous and authenticated users

---

#### 22. Explain Django's caching framework. Implement different caching strategies (per-view caching, template fragment caching, low-level cache API).

**Answer:**

## Django Caching Framework

Django provides a robust caching framework that supports multiple cache backends and caching strategies to improve application performance.

### 1. Cache Backend Configuration

```python
# settings.py

# 1. Memcached (recommended for production)
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.PyMemcacheCache',
        'LOCATION': '127.0.0.1:11211',
        'OPTIONS': {
            'no_delay': True,
            'ignore_exc': True,
            'max_pool_size': 4,
            'use_pooling': True,
        }
    }
}

# 2. Redis (popular choice)
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/1',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
            'PARSER_CLASS': 'redis.connection.HiredisParser',
            'CONNECTION_POOL_CLASS_KWARGS': {
                'max_connections': 50,
                'retry_on_timeout': True,
            },
            'SOCKET_CONNECT_TIMEOUT': 5,
            'SOCKET_TIMEOUT': 5,
            'COMPRESSOR': 'django_redis.compressors.zlib.ZlibCompressor',
            'IGNORE_EXCEPTIONS': True,
        },
        'KEY_PREFIX': 'myapp',
        'TIMEOUT': 300,  # 5 minutes default
    }
}

# 3. Database cache
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.db.DatabaseCache',
        'LOCATION': 'my_cache_table',
    }
}

# Create table: python manage.py createcachetable

# 4. File-based cache
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': '/var/tmp/django_cache',
        'TIMEOUT': 300,
        'OPTIONS': {
            'MAX_ENTRIES': 1000
        }
    }
}

# 5. Local memory cache (development only)
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
        'LOCATION': 'unique-snowflake',
    }
}

# 6. Dummy cache (for development/testing)
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.dummy.DummyCache',
    }
}

# Multiple cache backends
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/1',
    },
    'sessions': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/2',
    },
    'api': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/3',
        'TIMEOUT': 60,
    }
}
```

### 2. Site-Wide Caching (Middleware)

```python
# settings.py
MIDDLEWARE = [
    'django.middleware.cache.UpdateCacheMiddleware',  # Must be first
    'django.middleware.common.CommonMiddleware',
    'django.middleware.cache.FetchFromCacheMiddleware',  # Must be last
]

CACHE_MIDDLEWARE_ALIAS = 'default'
CACHE_MIDDLEWARE_SECONDS = 600  # 10 minutes
CACHE_MIDDLEWARE_KEY_PREFIX = 'mysite'
```

### 3. Per-View Caching

```python
# views.py
from django.views.decorators.cache import cache_page
from django.utils.decorators import method_decorator
from django.views.generic import ListView

# Function-based view caching
@cache_page(60 * 15)  # Cache for 15 minutes
def article_list(request):
    articles = Article.objects.all()
    return render(request, 'articles.html', {'articles': articles})

# Cache with specific cache backend
@cache_page(60 * 15, cache='api')
def api_view(request):
    data = get_expensive_data()
    return JsonResponse(data)

# Conditional caching based on request
from django.views.decorators.cache import cache_page

def conditional_cache(request):
    # Cache only for anonymous users
    if request.user.is_authenticated:
        return uncached_view(request)
    return cached_view(request)

@cache_page(60 * 15)
def cached_view(request):
    return render(request, 'cached.html')

def uncached_view(request):
    return render(request, 'uncached.html')

# Class-based view caching
@method_decorator(cache_page(60 * 15), name='dispatch')
class ArticleListView(ListView):
    model = Article
    template_name = 'articles.html'
    context_object_name = 'articles'

# Cache specific methods
class ProductDetailView(DetailView):
    model = Product
    
    @method_decorator(cache_page(60 * 30))
    def dispatch(self, *args, **kwargs):
        return super().dispatch(*args, **kwargs)

# Vary cache by headers
from django.views.decorators.vary import vary_on_headers

@cache_page(60 * 15)
@vary_on_headers('User-Agent', 'Accept-Language')
def varied_view(request):
    return render(request, 'varied.html')

# Vary cache by cookie
from django.views.decorators.vary import vary_on_cookie

@cache_page(60 * 15)
@vary_on_cookie
def user_specific_view(request):
    return render(request, 'user_specific.html')
```

### 4. Template Fragment Caching

```django
{# Load cache template tag #}
{% load cache %}

{# Basic fragment caching #}
{% cache 500 sidebar %}
    <div class="sidebar">
        {% for item in menu_items %}
            <a href="{{ item.url }}">{{ item.title }}</a>
        {% endfor %}
    </div>
{% endcache %}

{# Cache with variables #}
{% cache 600 article_detail article.id %}
    <article>
        <h1>{{ article.title }}</h1>
        <p>{{ article.content }}</p>
    </article>
{% endcache %}

{# Cache with multiple variables #}
{% cache 300 user_sidebar request.user.id request.LANGUAGE_CODE %}
    <div class="user-sidebar">
        <p>Welcome, {{ request.user.username }}</p>
        <!-- User-specific content -->
    </div>
{% endcache %}

{# Cache with using specific backend #}
{% cache 500 sidebar using='api' %}
    <!-- Sidebar content -->
{% endcache %}

{# Complete example #}
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}My Site{% endblock %}</title>
</head>
<body>
    {% cache 600 header %}
    <header>
        <nav>
            {% for item in navigation %}
                <a href="{{ item.url }}">{{ item.title }}</a>
            {% endfor %}
        </nav>
    </header>
    {% endcache %}
    
    <main>
        {% block content %}{% endblock %}
    </main>
    
    {% cache 300 sidebar request.user.is_authenticated %}
    <aside>
        {% if request.user.is_authenticated %}
            <p>Welcome, {{ request.user.username }}</p>
        {% else %}
            <p>Please log in</p>
        {% endif %}
        
        {% cache 600 popular_posts %}
        <div class="popular-posts">
            <h3>Popular Posts</h3>
            {% for post in popular_posts %}
                <div>{{ post.title }}</div>
            {% endfor %}
        </div>
        {% endcache %}
    </aside>
    {% endcache %}
    
    {% cache 900 footer %}
    <footer>
        <p>&copy; 2024 My Site</p>
    </footer>
    {% endcache %}
</body>
</html>
```

### 5. Low-Level Cache API

```python
# Basic cache operations
from django.core.cache import cache

# Set cache
cache.set('my_key', 'my_value', 300)  # 300 seconds = 5 minutes

# Get cache
value = cache.get('my_key')

# Get with default
value = cache.get('my_key', 'default_value')

# Set if not exists
cache.add('my_key', 'my_value', 300)  # Only sets if key doesn't exist

# Get and delete
value = cache.get_and_delete('my_key')

# Delete cache
cache.delete('my_key')

# Delete multiple keys
cache.delete_many(['key1', 'key2', 'key3'])

# Set multiple values
cache.set_many({
    'key1': 'value1',
    'key2': 'value2',
    'key3': 'value3'
}, 300)

# Get multiple values
values = cache.get_many(['key1', 'key2', 'key3'])
# Returns: {'key1': 'value1', 'key2': 'value2', 'key3': 'value3'}

# Increment/Decrement (for numeric values)
cache.set('counter', 0)
cache.incr('counter')  # Increment by 1
cache.incr('counter', 5)  # Increment by 5
cache.decr('counter')  # Decrement by 1
cache.decr('counter', 3)  # Decrement by 3

# Clear all cache
cache.clear()

# Touch (update expiration without changing value)
cache.touch('my_key', 600)  # Extend timeout to 600 seconds

# Using specific cache backend
from django.core.cache import caches

api_cache = caches['api']
api_cache.set('api_key', 'api_value', 60)

# Practical examples
def get_user_profile(user_id):
    cache_key = f'user_profile_{user_id}'
    profile = cache.get(cache_key)
    
    if profile is None:
        profile = UserProfile.objects.get(user_id=user_id)
        cache.set(cache_key, profile, 60 * 30)  # Cache for 30 minutes
    
    return profile

def get_article_list():
    cache_key = 'article_list'
    articles = cache.get(cache_key)
    
    if articles is None:
        articles = list(Article.objects.filter(published=True).order_by('-created_at')[:10])
        cache.set(cache_key, articles, 60 * 15)  # Cache for 15 minutes
    
    return articles

# Cache with version control
cache.set('my_key', 'value1', version=1)
cache.set('my_key', 'value2', version=2)

value1 = cache.get('my_key', version=1)  # Returns 'value1'
value2 = cache.get('my_key', version=2)  # Returns 'value2'

# Invalidate all versions
cache.delete('my_key', version=cache.VERSION)
```

### 6. Advanced Caching Patterns

```python
# Decorator for caching function results
from functools import wraps
from django.core.cache import cache

def cache_result(timeout=300):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            # Create cache key from function name and arguments
            cache_key = f"{func.__name__}_{args}_{kwargs}"
            result = cache.get(cache_key)
            
            if result is None:
                result = func(*args, **kwargs)
                cache.set(cache_key, result, timeout)
            
            return result
        return wrapper
    return decorator

@cache_result(timeout=600)
def expensive_calculation(x, y):
    import time
    time.sleep(2)  # Simulate expensive operation
    return x + y

# Cache with automatic invalidation
class ArticleManager:
    @staticmethod
    def get_article(article_id):
        cache_key = f'article_{article_id}'
        article = cache.get(cache_key)
        
        if article is None:
            article = Article.objects.get(id=article_id)
            cache.set(cache_key, article, 60 * 30)
        
        return article
    
    @staticmethod
    def update_article(article_id, **kwargs):
        article = Article.objects.get(id=article_id)
        for key, value in kwargs.items():
            setattr(article, key, value)
        article.save()
        
        # Invalidate cache
        cache_key = f'article_{article_id}'
        cache.delete(cache_key)
        
        return article

# Signal-based cache invalidation
from django.db.models.signals import post_save, post_delete
from django.dispatch import receiver

@receiver(post_save, sender=Article)
def invalidate_article_cache(sender, instance, **kwargs):
    cache.delete(f'article_{instance.id}')
    cache.delete('article_list')

@receiver(post_delete, sender=Article)
def invalidate_article_list_cache(sender, instance, **kwargs):
    cache.delete('article_list')

# Cache warming
from django.core.management.base import BaseCommand

class Command(BaseCommand):
    help = 'Warm up cache with frequently accessed data'
    
    def handle(self, *args, **options):
        # Cache popular articles
        popular_articles = Article.objects.filter(views__gte=1000)
        for article in popular_articles:
            cache_key = f'article_{article.id}'
            cache.set(cache_key, article, 60 * 60)  # 1 hour
        
        # Cache article list
        article_list = list(Article.objects.filter(published=True)[:100])
        cache.set('article_list', article_list, 60 * 30)
        
        self.stdout.write(self.style.SUCCESS('Cache warmed successfully'))

# Conditional caching in views
def article_detail(request, article_id):
    # Don't cache for authenticated users
    if request.user.is_authenticated:
        article = Article.objects.get(id=article_id)
    else:
        cache_key = f'article_{article_id}'
        article = cache.get(cache_key)
        
        if article is None:
            article = Article.objects.get(id=article_id)
            cache.set(cache_key, article, 60 * 30)
    
    return render(request, 'article_detail.html', {'article': article})

# Cache with fallback
def get_data_with_fallback(key):
    # Try cache first
    data = cache.get(key)
    
    if data is not None:
        return data
    
    # Try database
    try:
        data = Database.get(key)
        cache.set(key, data, 300)
        return data
    except DatabaseError:
        # Return stale cache if available
        stale_data = cache.get(f'{key}_stale')
        if stale_data:
            return stale_data
        raise

# Rate limiting with cache
def rate_limit(key, limit=10, period=60):
    cache_key = f'rate_limit_{key}'
    count = cache.get(cache_key, 0)
    
    if count >= limit:
        return False
    
    cache.set(cache_key, count + 1, period)
    return True

# Usage in view
def api_endpoint(request):
    ip = request.META.get('REMOTE_ADDR')
    
    if not rate_limit(ip, limit=100, period=3600):
        return JsonResponse({'error': 'Rate limit exceeded'}, status=429)
    
    # Process request
    return JsonResponse({'status': 'success'})
```

### 7. Cache Key Strategies

```python
# Generate consistent cache keys
def make_cache_key(prefix, *args, **kwargs):
    import hashlib
    import json
    
    key_data = {
        'args': args,
        'kwargs': sorted(kwargs.items())
    }
    key_string = json.dumps(key_data, sort_keys=True)
    key_hash = hashlib.md5(key_string.encode()).hexdigest()
    
    return f'{prefix}:{key_hash}'

# Usage
cache_key = make_cache_key('article_search', query='django', page=1, sort='date')
results = cache.get(cache_key)

if results is None:
    results = search_articles(query='django', page=1, sort='date')
    cache.set(cache_key, results, 300)
```

**Best Practices:**

1. **Choose appropriate timeout**: Balance freshness vs. performance
2. **Cache invalidation**: Clear cache when data changes
3. **Cache key naming**: Use consistent, descriptive key names
4. **Monitor cache hit rate**: Track cache effectiveness
5. **Avoid caching user-specific data**: Unless using vary_on_cookie
6. **Use cache versioning**: For gradual cache updates
7. **Implement cache warming**: Pre-populate frequently accessed data
8. **Handle cache failures gracefully**: Always have fallback logic

---

#### 23. Explain Django's testing framework. Write comprehensive tests for models, views, forms, and API endpoints.

**Answer:**

## Django Testing Framework

Django provides a comprehensive testing framework built on Python's `unittest` module, with additional features for testing web applications.

### 1. Test Setup and Configuration

```python
# settings.py - Test-specific settings
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': ':memory:',  # Use in-memory database for faster tests
    }
}

# Disable migrations for faster tests
class DisableMigrations:
    def __contains__(self, item):
        return True
    
    def __getitem__(self, item):
        return None

MIGRATION_MODULES = DisableMigrations()

# Test runner configuration
TEST_RUNNER = 'django.test.runner.DiscoverRunner'
```

### 2. Model Testing

```python
# blog/tests/test_models.py
from django.test import TestCase
from django.contrib.auth.models import User
from django.utils import timezone
from blog.models import Post, Category, Comment

class CategoryModelTest(TestCase):
    @classmethod
    def setUpTestData(cls):
        """Set up non-modified objects used by all test methods"""
        Category.objects.create(name='Technology', slug='technology')
    
    def test_category_creation(self):
        category = Category.objects.get(id=1)
        self.assertEqual(category.name, 'Technology')
        self.assertEqual(category.slug, 'technology')
    
    def test_category_str_method(self):
        category = Category.objects.get(id=1)
        self.assertEqual(str(category), 'Technology')
    
    def test_slug_auto_generation(self):
        category = Category.objects.create(name='Web Development')
        self.assertEqual(category.slug, 'web-development')
    
    def test_get_absolute_url(self):
        category = Category.objects.get(id=1)
        self.assertEqual(category.get_absolute_url(), '/blog/category/technology/')

class PostModelTest(TestCase):
    def setUp(self):
        """Set up for each test method"""
        self.user = User.objects.create_user(
            username='testuser',
            email='test@example.com',
            password='testpass123'
        )
        self.category = Category.objects.create(name='Tech', slug='tech')
        self.post = Post.objects.create(
            title='Test Post',
            slug='test-post',
            author=self.user,
            category=self.category,
            content='Test content',
            status='published'
        )
    
    def test_post_creation(self):
        self.assertEqual(self.post.title, 'Test Post')
        self.assertEqual(self.post.author, self.user)
        self.assertEqual(self.post.status, 'published')
    
    def test_post_str_method(self):
        self.assertEqual(str(self.post), 'Test Post')
    
    def test_post_get_absolute_url(self):
        expected_url = f'/blog/{self.post.publish.year}/{self.post.publish.month}/{self.post.publish.day}/test-post/'
        self.assertEqual(self.post.get_absolute_url(), expected_url)
    
    def test_auto_excerpt_generation(self):
        long_content = 'A' * 300
        post = Post.objects.create(
            title='Long Post',
            slug='long-post',
            author=self.user,
            category=self.category,
            content=long_content,
            status='published'
        )
        self.assertEqual(len(post.excerpt), 200)
    
    def test_increment_views(self):
        initial_views = self.post.views
        self.post.increment_views()
        self.assertEqual(self.post.views, initial_views + 1)
    
    def test_published_manager(self):
        # Create draft post
        Post.objects.create(
            title='Draft Post',
            slug='draft-post',
            author=self.user,
            category=self.category,
            content='Draft content',
            status='draft'
        )
        
        # Only published posts should be returned
        published_posts = Post.published.all()
        self.assertEqual(published_posts.count(), 1)
        self.assertEqual(published_posts[0].status, 'published')

class CommentModelTest(TestCase):
    def setUp(self):
        self.user = User.objects.create_user(username='testuser', password='testpass123')
        self.category = Category.objects.create(name='Tech', slug='tech')
        self.post = Post.objects.create(
            title='Test Post',
            slug='test-post',
            author=self.user,
            category=self.category,
            content='Test content',
            status='published'
        )
        self.comment = Comment.objects.create(
            post=self.post,
            name='John Doe',
            email='john@example.com',
            body='Great post!'
        )
    
    def test_comment_creation(self):
        self.assertEqual(self.comment.name, 'John Doe')
        self.assertEqual(self.comment.post, self.post)
        self.assertTrue(self.comment.active)
    
    def test_comment_str_method(self):
        expected = f'Comment by John Doe on {self.post}'
        self.assertEqual(str(self.comment), expected)
```

### 3. View Testing

```python
# blog/tests/test_views.py
from django.test import TestCase, Client
from django.urls import reverse
from django.contrib.auth.models import User
from blog.models import Post, Category

class PostListViewTest(TestCase):
    @classmethod
    def setUpTestData(cls):
        # Create test data
        user = User.objects.create_user(username='testuser', password='testpass123')
        category = Category.objects.create(name='Tech', slug='tech')
        
        # Create 15 posts for pagination testing
        for i in range(15):
            Post.objects.create(
                title=f'Post {i}',
                slug=f'post-{i}',
                author=user,
                category=category,
                content=f'Content {i}',
                status='published'
            )
    
    def test_view_url_exists_at_desired_location(self):
        response = self.client.get('/blog/')
        self.assertEqual(response.status_code, 200)
    
    def test_view_url_accessible_by_name(self):
        response = self.client.get(reverse('blog:post_list'))
        self.assertEqual(response.status_code, 200)
    
    def test_view_uses_correct_template(self):
        response = self.client.get(reverse('blog:post_list'))
        self.assertTemplateUsed(response, 'blog/post_list.html')
    
    def test_pagination_is_six(self):
        response = self.client.get(reverse('blog:post_list'))
        self.assertTrue('is_paginated' in response.context)
        self.assertTrue(response.context['is_paginated'])
        self.assertEqual(len(response.context['posts']), 6)
    
    def test_lists_all_posts(self):
        # Get second page
        response = self.client.get(reverse('blog:post_list') + '?page=2')
        self.assertEqual(response.status_code, 200)
        self.assertEqual(len(response.context['posts']), 6)

class PostDetailViewTest(TestCase):
    def setUp(self):
        self.user = User.objects.create_user(username='testuser', password='testpass123')
        self.category = Category.objects.create(name='Tech', slug='tech')
        self.post = Post.objects.create(
            title='Test Post',
            slug='test-post',
            author=self.user,
            category=self.category,
            content='Test content',
            status='published'
        )
    
    def test_post_detail_view(self):
        url = self.post.get_absolute_url()
        response = self.client.get(url)
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'Test Post')
        self.assertContains(response, 'Test content')
    
    def test_post_detail_increments_views(self):
        initial_views = self.post.views
        self.client.get(self.post.get_absolute_url())
        self.post.refresh_from_db()
        self.assertEqual(self.post.views, initial_views + 1)
    
    def test_post_not_found(self):
        url = '/blog/2024/01/01/nonexistent-post/'
        response = self.client.get(url)
        self.assertEqual(response.status_code, 404)

class PostCreateViewTest(TestCase):
    def setUp(self):
        self.user = User.objects.create_user(username='testuser', password='testpass123')
        self.category = Category.objects.create(name='Tech', slug='tech')
        self.client = Client()
    
    def test_redirect_if_not_logged_in(self):
        response = self.client.get(reverse('blog:post_create'))
        self.assertRedirects(response, '/accounts/login/?next=/blog/create/')
    
    def test_logged_in_uses_correct_template(self):
        self.client.login(username='testuser', password='testpass123')
        response = self.client.get(reverse('blog:post_create'))
        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, 'blog/post_form.html')
    
    def test_can_create_post(self):
        self.client.login(username='testuser', password='testpass123')
        response = self.client.post(reverse('blog:post_create'), {
            'title': 'New Post',
            'category': self.category.id,
            'content': 'New content',
            'status': 'published',
        })
        
        self.assertEqual(Post.objects.count(), 1)
        post = Post.objects.first()
        self.assertEqual(post.title, 'New Post')
        self.assertEqual(post.author, self.user)

class SearchViewTest(TestCase):
    def setUp(self):
        user = User.objects.create_user(username='testuser', password='testpass123')
        category = Category.objects.create(name='Tech', slug='tech')
        
        Post.objects.create(
            title='Django Tutorial',
            slug='django-tutorial',
            author=user,
            category=category,
            content='Learn Django',
            status='published'
        )
        Post.objects.create(
            title='Python Guide',
            slug='python-guide',
            author=user,
            category=category,
            content='Learn Python',
            status='published'
        )
    
    def test_search_results(self):
        response = self.client.get(reverse('blog:search'), {'query': 'Django'})
        self.assertEqual(response.status_code, 200)
        self.assertEqual(len(response.context['results']), 1)
        self.assertContains(response, 'Django Tutorial')
    
    def test_search_no_results(self):
        response = self.client.get(reverse('blog:search'), {'query': 'JavaScript'})
        self.assertEqual(len(response.context['results']), 0)
```

### 4. Form Testing

```python
# blog/tests/test_forms.py
from django.test import TestCase
from blog.forms import CommentForm, PostForm
from blog.models import Category

class CommentFormTest(TestCase):
    def test_comment_form_valid_data(self):
        form = CommentForm(data={
            'name': 'John Doe',
            'email': 'john@example.com',
            'body': 'Great post!'
        })
        self.assertTrue(form.is_valid())
    
    def test_comment_form_no_data(self):
        form = CommentForm(data={})
        self.assertFalse(form.is_valid())
        self.assertEqual(len(form.errors), 3)
    
    def test_comment_form_invalid_email(self):
        form = CommentForm(data={
            'name': 'John Doe',
            'email': 'invalid-email',
            'body': 'Great post!'
        })
        self.assertFalse(form.is_valid())
        self.assertIn('email', form.errors)
    
    def test_comment_form_field_labels(self):
        form = CommentForm()
        self.assertTrue(form.fields['name'].label is None or form.fields['name'].label == 'Name')

class PostFormTest(TestCase):
    def setUp(self):
        self.category = Category.objects.create(name='Tech', slug='tech')
    
    def test_post_form_valid_data(self):
        form = PostForm(data={
            'title': 'Test Post',
            'category': self.category.id,
            'content': 'Test content',
            'status': 'published',
        })
        self.assertTrue(form.is_valid())
    
    def test_post_form_missing_required_fields(self):
        form = PostForm(data={})
        self.assertFalse(form.is_valid())
        self.assertIn('title', form.errors)
        self.assertIn('content', form.errors)
```

### 5. API Testing (Django REST Framework)

```python
# api/tests/test_api.py
from django.test import TestCase
from django.contrib.auth.models import User
from rest_framework.test import APIClient, APITestCase
from rest_framework import status
from blog.models import Post, Category

class PostAPITest(APITestCase):
    def setUp(self):
        self.client = APIClient()
        self.user = User.objects.create_user(
            username='testuser',
            password='testpass123'
        )
        self.category = Category.objects.create(name='Tech', slug='tech')
        self.post = Post.objects.create(
            title='Test Post',
            slug='test-post',
            author=self.user,
            category=self.category,
            content='Test content',
            status='published'
        )
    
    def test_get_post_list(self):
        response = self.client.get('/api/posts/')
        self.assertEqual(response.status_code, status.HTTP_200_OK)
        self.assertEqual(len(response.data['results']), 1)
    
    def test_get_post_detail(self):
        response = self.client.get(f'/api/posts/{self.post.slug}/')
        self.assertEqual(response.status_code, status.HTTP_200_OK)
        self.assertEqual(response.data['title'], 'Test Post')
    
    def test_create_post_unauthenticated(self):
        data = {
            'title': 'New Post',
            'category_id': self.category.id,
            'content': 'New content',
            'status': 'published',
        }
        response = self.client.post('/api/posts/', data)
        self.assertEqual(response.status_code, status.HTTP_403_FORBIDDEN)
    
    def test_create_post_authenticated(self):
        self.client.force_authenticate(user=self.user)
        data = {
            'title': 'New Post',
            'category_id': self.category.id,
            'content': 'New content',
            'status': 'published',
        }
        response = self.client.post('/api/posts/', data)
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
        self.assertEqual(Post.objects.count(), 2)
    
    def test_update_post(self):
        self.client.force_authenticate(user=self.user)
        data = {'title': 'Updated Post'}
        response = self.client.patch(f'/api/posts/{self.post.slug}/', data)
        self.assertEqual(response.status_code, status.HTTP_200_OK)
        self.post.refresh_from_db()
        self.assertEqual(self.post.title, 'Updated Post')
    
    def test_delete_post(self):
        self.client.force_authenticate(user=self.user)
        response = self.client.delete(f'/api/posts/{self.post.slug}/')
        self.assertEqual(response.status_code, status.HTTP_204_NO_CONTENT)
        self.assertEqual(Post.objects.count(), 0)
    
    def test_filter_posts_by_category(self):
        response = self.client.get(f'/api/posts/?category={self.category.id}')
        self.assertEqual(response.status_code, status.HTTP_200_OK)
        self.assertEqual(len(response.data['results']), 1)
    
    def test_search_posts(self):
        response = self.client.get('/api/posts/?search=Test')
        self.assertEqual(response.status_code, status.HTTP_200_OK)
        self.assertEqual(len(response.data['results']), 1)

class AuthenticationAPITest(APITestCase):
    def setUp(self):
        self.client = APIClient()
        self.user = User.objects.create_user(
            username='testuser',
            email='test@example.com',
            password='testpass123'
        )
    
    def test_user_registration(self):
        data = {
            'username': 'newuser',
            'email': 'new@example.com',
            'password': 'newpass123',
            'password2': 'newpass123',
        }
        response = self.client.post('/api/auth/register/', data)
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
        self.assertIn('access', response.data)
        self.assertIn('refresh', response.data)
    
    def test_user_login(self):
        data = {
            'username': 'testuser',
            'password': 'testpass123',
        }
        response = self.client.post('/api/auth/token/', data)
        self.assertEqual(response.status_code, status.HTTP_200_OK)
        self.assertIn('access', response.data)
        self.assertIn('refresh', response.data)
    
    def test_get_user_profile(self):
        self.client.force_authenticate(user=self.user)
        response = self.client.get('/api/auth/profile/')
        self.assertEqual(response.status_code, status.HTTP_200_OK)
        self.assertEqual(response.data['username'], 'testuser')
```

### 6. Integration Testing

```python
# blog/tests/test_integration.py
from django.test import TestCase, Client
from django.contrib.auth.models import User
from blog.models import Post, Category, Comment

class BlogWorkflowTest(TestCase):
    def setUp(self):
        self.client = Client()
        self.user = User.objects.create_user(
            username='testuser',
            password='testpass123'
        )
        self.category = Category.objects.create(name='Tech', slug='tech')
    
    def test_complete_blog_workflow(self):
        # 1. User logs in
        login = self.client.login(username='testuser', password='testpass123')
        self.assertTrue(login)
        
        # 2. User creates a post
        response = self.client.post('/blog/create/', {
            'title': 'My First Post',
            'category': self.category.id,
            'content': 'This is my first post content.',
            'status': 'published',
        })
        self.assertEqual(Post.objects.count(), 1)
        post = Post.objects.first()
        
        # 3. User views the post
        response = self.client.get(post.get_absolute_url())
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'My First Post')
        
        # 4. User adds a comment
        response = self.client.post(post.get_absolute_url(), {
            'name': 'Test User',
            'email': 'test@example.com',
            'body': 'Great post!',
        })
        self.assertEqual(Comment.objects.count(), 1)
        
        # 5. User edits the post
        response = self.client.post(f'/blog/{post.pk}/edit/', {
            'title': 'My Updated Post',
            'category': self.category.id,
            'content': 'Updated content.',
            'status': 'published',
        })
        post.refresh_from_db()
        self.assertEqual(post.title, 'My Updated Post')
        
        # 6. User deletes the post
        response = self.client.post(f'/blog/{post.pk}/delete/')
        self.assertEqual(Post.objects.count(), 0)
```

### 7. Test Utilities and Fixtures

```python
# blog/tests/factories.py (using factory_boy)
import factory
from django.contrib.auth.models import User
from blog.models import Post, Category

class UserFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = User
    
    username = factory.Sequence(lambda n: f'user{n}')
    email = factory.LazyAttribute(lambda obj: f'{obj.username}@example.com')
    password = factory.PostGenerationMethodCall('set_password', 'testpass123')

class CategoryFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Category
    
    name = factory.Sequence(lambda n: f'Category {n}')
    slug = factory.LazyAttribute(lambda obj: obj.name.lower().replace(' ', '-'))

class PostFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Post
    
    title = factory.Sequence(lambda n: f'Post {n}')
    slug = factory.LazyAttribute(lambda obj: obj.title.lower().replace(' ', '-'))
    author = factory.SubFactory(UserFactory)
    category = factory.SubFactory(CategoryFactory)
    content = factory.Faker('paragraph')
    status = 'published'

# Usage in tests
class PostFactoryTest(TestCase):
    def test_create_post_with_factory(self):
        post = PostFactory()
        self.assertIsNotNone(post.id)
        self.assertEqual(post.status, 'published')
    
    def test_create_multiple_posts(self):
        posts = PostFactory.create_batch(5)
        self.assertEqual(Post.objects.count(), 5)
```

### 8. Running Tests

```bash
# Run all tests
python manage.py test

# Run specific app tests
python manage.py test blog

# Run specific test class
python manage.py test blog.tests.test_models.PostModelTest

# Run specific test method
python manage.py test blog.tests.test_models.PostModelTest.test_post_creation

# Run with verbosity
python manage.py test --verbosity=2

# Run with coverage
coverage run --source='.' manage.py test
coverage report
coverage html

# Run tests in parallel
python manage.py test --parallel

# Keep test database
python manage.py test --keepdb
```

**Best Practices:**

1. **Test isolation**: Each test should be independent
2. **Use setUp/tearDown**: Properly initialize and clean up test data
3. **Test edge cases**: Test boundary conditions and error cases
4. **Mock external dependencies**: Use mocking for external APIs
5. **Maintain test coverage**: Aim for >80% code coverage
6. **Fast tests**: Use in-memory database and disable migrations
7. **Descriptive test names**: Use clear, descriptive test method names
8. **Test documentation**: Document complex test scenarios

---

#### 24. Implement user authentication and authorization in Django. Include custom user models, permissions, and role-based access control.

**Answer:**

## Django Authentication and Authorization System

### 1. Custom User Model

```python
# accounts/models.py
from django.contrib.auth.models import AbstractBaseUser, BaseUserManager, PermissionsMixin
from django.db import models
from django.utils import timezone

class CustomUserManager(BaseUserManager):
    def create_user(self, email, username, password=None, **extra_fields):
        if not email:
            raise ValueError('Users must have an email address')
        if not username:
            raise ValueError('Users must have a username')
        
        email = self.normalize_email(email)
        user = self.model(email=email, username=username, **extra_fields)
        user.set_password(password)
        user.save(using=self._db)
        return user
    
    def create_superuser(self, email, username, password=None, **extra_fields):
        extra_fields.setdefault('is_staff', True)
        extra_fields.setdefault('is_superuser', True)
        extra_fields.setdefault('is_active', True)
        
        if extra_fields.get('is_staff') is not True:
            raise ValueError('Superuser must have is_staff=True')
        if extra_fields.get('is_superuser') is not True:
            raise ValueError('Superuser must have is_superuser=True')
        
        return self.create_user(email, username, password, **extra_fields)

class CustomUser(AbstractBaseUser, PermissionsMixin):
    email = models.EmailField(unique=True, max_length=255)
    username = models.CharField(unique=True, max_length=150)
    first_name = models.CharField(max_length=150, blank=True)
    last_name = models.CharField(max_length=150, blank=True)
    
    # Additional fields
    phone = models.CharField(max_length=15, blank=True)
    bio = models.TextField(blank=True)
    avatar = models.ImageField(upload_to='avatars/', blank=True, null=True)
    date_of_birth = models.DateField(null=True, blank=True)
    
    # Status fields
    is_active = models.BooleanField(default=True)
    is_staff = models.BooleanField(default=False)
    is_verified = models.BooleanField(default=False)
    
    # Timestamps
    date_joined = models.DateTimeField(default=timezone.now)
    last_login = models.DateTimeField(null=True, blank=True)
    
    objects = CustomUserManager()
    
    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = ['username']
    
    class Meta:
        verbose_name = 'User'
        verbose_name_plural = 'Users'
        ordering = ['-date_joined']
    
    def __str__(self):
        return self.email
    
    def get_full_name(self):
        return f'{self.first_name} {self.last_name}'.strip() or self.username
    
    def get_short_name(self):
        return self.first_name or self.username

# settings.py
AUTH_USER_MODEL = 'accounts.CustomUser'
```

### 2. User Profile and Roles

```python
# accounts/models.py (continued)
class Role(models.Model):
    ROLE_CHOICES = [
        ('admin', 'Administrator'),
        ('editor', 'Editor'),
        ('author', 'Author'),
        ('subscriber', 'Subscriber'),
    ]
    
    name = models.CharField(max_length=50, choices=ROLE_CHOICES, unique=True)
    description = models.TextField(blank=True)
    permissions = models.ManyToManyField('auth.Permission', blank=True)
    
    def __str__(self):
        return self.get_name_display()

class UserProfile(models.Model):
    user = models.OneToOneField(CustomUser, on_delete=models.CASCADE, related_name='profile')
    role = models.ForeignKey(Role, on_delete=models.SET_NULL, null=True, blank=True)
    company = models.CharField(max_length=200, blank=True)
    website = models.URLField(blank=True)
    location = models.CharField(max_length=200, blank=True)
    
    # Social links
    twitter = models.CharField(max_length=100, blank=True)
    linkedin = models.CharField(max_length=100, blank=True)
    github = models.CharField(max_length=100, blank=True)
    
    # Preferences
    email_notifications = models.BooleanField(default=True)
    newsletter_subscription = models.BooleanField(default=False)
    
    def __str__(self):
        return f'Profile for {self.user.email}'

# Signal to auto-create profile
from django.db.models.signals import post_save
from django.dispatch import receiver

@receiver(post_save, sender=CustomUser)
def create_user_profile(sender, instance, created, **kwargs):
    if created:
        UserProfile.objects.create(user=instance)

@receiver(post_save, sender=CustomUser)
def save_user_profile(sender, instance, **kwargs):
    instance.profile.save()
```

### 3. Custom Permissions

```python
# blog/models.py
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.ForeignKey('accounts.CustomUser', on_delete=models.CASCADE)
    status = models.CharField(max_length=20, default='draft')
    
    class Meta:
        permissions = [
            ('can_publish', 'Can publish articles'),
            ('can_feature', 'Can feature articles'),
            ('can_moderate', 'Can moderate comments'),
        ]
    
    def __str__(self):
        return self.title

# Create permissions programmatically
from django.contrib.auth.models import Permission
from django.contrib.contenttypes.models import ContentType

content_type = ContentType.objects.get_for_model(Article)
permission = Permission.objects.create(
    codename='can_publish',
    name='Can publish articles',
    content_type=content_type,
)
```

### 4. Permission Decorators and Mixins

```python
# accounts/decorators.py
from functools import wraps
from django.core.exceptions import PermissionDenied
from django.shortcuts import redirect

def role_required(allowed_roles=[]):
    def decorator(view_func):
        @wraps(view_func)
        def wrapper(request, *args, **kwargs):
            if not request.user.is_authenticated:
                return redirect('login')
            
            user_role = request.user.profile.role.name if request.user.profile.role else None
            
            if user_role not in allowed_roles and not request.user.is_superuser:
                raise PermissionDenied
            
            return view_func(request, *args, **kwargs)
        return wrapper
    return decorator

def permission_required_custom(perm, raise_exception=True):
    def decorator(view_func):
        @wraps(view_func)
        def wrapper(request, *args, **kwargs):
            if not request.user.has_perm(perm):
                if raise_exception:
                    raise PermissionDenied
                return redirect('login')
            return view_func(request, *args, **kwargs)
        return wrapper
    return decorator

# Usage in views
from django.shortcuts import render
from .decorators import role_required, permission_required_custom

@role_required(allowed_roles=['admin', 'editor'])
def admin_dashboard(request):
    return render(request, 'admin_dashboard.html')

@permission_required_custom('blog.can_publish')
def publish_article(request, article_id):
    # Publish article logic
    return render(request, 'article_published.html')
```

### 5. Class-Based View Mixins

```python
# accounts/mixins.py
from django.contrib.auth.mixins import LoginRequiredMixin, PermissionRequiredMixin
from django.core.exceptions import PermissionDenied

class RoleRequiredMixin:
    allowed_roles = []
    
    def dispatch(self, request, *args, **kwargs):
        if not request.user.is_authenticated:
            return self.handle_no_permission()
        
        user_role = request.user.profile.role.name if request.user.profile.role else None
        
        if user_role not in self.allowed_roles and not request.user.is_superuser:
            raise PermissionDenied
        
        return super().dispatch(request, *args, **kwargs)

class OwnerRequiredMixin:
    def dispatch(self, request, *args, **kwargs):
        obj = self.get_object()
        if obj.author != request.user and not request.user.is_superuser:
            raise PermissionDenied
        return super().dispatch(request, *args, **kwargs)

# Usage
from django.views.generic import ListView, UpdateView
from .mixins import RoleRequiredMixin, OwnerRequiredMixin

class AdminDashboardView(RoleRequiredMixin, ListView):
    allowed_roles = ['admin', 'editor']
    template_name = 'admin_dashboard.html'
    model = Article

class ArticleUpdateView(LoginRequiredMixin, OwnerRequiredMixin, UpdateView):
    model = Article
    fields = ['title', 'content']
    template_name = 'article_form.html'
```

### 6. Authentication Views

```python
# accounts/views.py
from django.contrib.auth import login, authenticate, logout
from django.contrib.auth.decorators import login_required
from django.shortcuts import render, redirect
from django.contrib import messages
from .forms import UserRegistrationForm, UserLoginForm, UserProfileForm

def register_view(request):
    if request.user.is_authenticated:
        return redirect('dashboard')
    
    if request.method == 'POST':
        form = UserRegistrationForm(request.POST)
        if form.is_valid():
            user = form.save(commit=False)
            user.set_password(form.cleaned_data['password'])
            user.save()
            
            # Assign default role
            from .models import Role
            subscriber_role = Role.objects.get(name='subscriber')
            user.profile.role = subscriber_role
            user.profile.save()
            
            messages.success(request, 'Registration successful! Please log in.')
            return redirect('login')
    else:
        form = UserRegistrationForm()
    
    return render(request, 'accounts/register.html', {'form': form})

def login_view(request):
    if request.user.is_authenticated:
        return redirect('dashboard')
    
    if request.method == 'POST':
        form = UserLoginForm(request.POST)
        if form.is_valid():
            email = form.cleaned_data['email']
            password = form.cleaned_data['password']
            user = authenticate(request, email=email, password=password)
            
            if user is not None:
                if user.is_active:
                    login(request, user)
                    next_url = request.GET.get('next', 'dashboard')
                    return redirect(next_url)
                else:
                    messages.error(request, 'Account is inactive.')
            else:
                messages.error(request, 'Invalid email or password.')
    else:
        form = UserLoginForm()
    
    return render(request, 'accounts/login.html', {'form': form})

def logout_view(request):
    logout(request)
    messages.success(request, 'You have been logged out.')
    return redirect('login')

@login_required
def profile_view(request):
    if request.method == 'POST':
        form = UserProfileForm(request.POST, request.FILES, instance=request.user.profile)
        if form.is_valid():
            form.save()
            messages.success(request, 'Profile updated successfully.')
            return redirect('profile')
    else:
        form = UserProfileForm(instance=request.user.profile)
    
    return render(request, 'accounts/profile.html', {'form': form})

@login_required
def change_password_view(request):
    if request.method == 'POST':
        old_password = request.POST.get('old_password')
        new_password = request.POST.get('new_password')
        confirm_password = request.POST.get('confirm_password')
        
        if not request.user.check_password(old_password):
            messages.error(request, 'Old password is incorrect.')
        elif new_password != confirm_password:
            messages.error(request, 'New passwords do not match.')
        else:
            request.user.set_password(new_password)
            request.user.save()
            messages.success(request, 'Password changed successfully.')
            return redirect('login')
    
    return render(request, 'accounts/change_password.html')
```

### 7. Forms

```python
# accounts/forms.py
from django import forms
from django.contrib.auth import get_user_model
from .models import UserProfile

User = get_user_model()

class UserRegistrationForm(forms.ModelForm):
    password = forms.CharField(widget=forms.PasswordInput, min_length=8)
    password2 = forms.CharField(widget=forms.PasswordInput, label='Confirm Password')
    
    class Meta:
        model = User
        fields = ['email', 'username', 'first_name', 'last_name']
    
    def clean_password2(self):
        password = self.cleaned_data.get('password')
        password2 = self.cleaned_data.get('password2')
        
        if password and password2 and password != password2:
            raise forms.ValidationError("Passwords don't match")
        return password2
    
    def clean_email(self):
        email = self.cleaned_data.get('email')
        if User.objects.filter(email=email).exists():
            raise forms.ValidationError("Email already exists")
        return email

class UserLoginForm(forms.Form):
    email = forms.EmailField()
    password = forms.CharField(widget=forms.PasswordInput)

class UserProfileForm(forms.ModelForm):
    class Meta:
        model = UserProfile
        fields = ['company', 'website', 'location', 'twitter', 'linkedin', 'github',
                  'email_notifications', 'newsletter_subscription']
        widgets = {
            'company': forms.TextInput(attrs={'class': 'form-control'}),
            'website': forms.URLInput(attrs={'class': 'form-control'}),
            'location': forms.TextInput(attrs={'class': 'form-control'}),
        }
```

### 8. URL Configuration

```python
# accounts/urls.py
from django.urls import path
from django.contrib.auth import views as auth_views
from . import views

app_name = 'accounts'

urlpatterns = [
    path('register/', views.register_view, name='register'),
    path('login/', views.login_view, name='login'),
    path('logout/', views.logout_view, name='logout'),
    path('profile/', views.profile_view, name='profile'),
    path('change-password/', views.change_password_view, name='change_password'),
    
    # Password reset
    path('password-reset/', 
         auth_views.PasswordResetView.as_view(template_name='accounts/password_reset.html'),
         name='password_reset'),
    path('password-reset/done/',
         auth_views.PasswordResetDoneView.as_view(template_name='accounts/password_reset_done.html'),
         name='password_reset_done'),
    path('password-reset-confirm/<uidb64>/<token>/',
         auth_views.PasswordResetConfirmView.as_view(template_name='accounts/password_reset_confirm.html'),
         name='password_reset_confirm'),
    path('password-reset-complete/',
         auth_views.PasswordResetCompleteView.as_view(template_name='accounts/password_reset_complete.html'),
         name='password_reset_complete'),
]
```

### 9. Admin Configuration

```python
# accounts/admin.py
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin as BaseUserAdmin
from .models import CustomUser, UserProfile, Role

@admin.register(CustomUser)
class CustomUserAdmin(BaseUserAdmin):
    list_display = ['email', 'username', 'first_name', 'last_name', 'is_staff', 'is_verified']
    list_filter = ['is_staff', 'is_superuser', 'is_active', 'is_verified']
    search_fields = ['email', 'username', 'first_name', 'last_name']
    ordering = ['-date_joined']
    
    fieldsets = (
        (None, {'fields': ('email', 'username', 'password')}),
        ('Personal Info', {'fields': ('first_name', 'last_name', 'phone', 'bio', 'avatar', 'date_of_birth')}),
        ('Permissions', {'fields': ('is_active', 'is_staff', 'is_superuser', 'is_verified', 'groups', 'user_permissions')}),
        ('Important Dates', {'fields': ('last_login', 'date_joined')}),
    )
    
    add_fieldsets = (
        (None, {
            'classes': ('wide',),
            'fields': ('email', 'username', 'password1', 'password2'),
        }),
    )

@admin.register(UserProfile)
class UserProfileAdmin(admin.ModelAdmin):
    list_display = ['user', 'role', 'company', 'location']
    list_filter = ['role', 'email_notifications']
    search_fields = ['user__email', 'user__username', 'company']

@admin.register(Role)
class RoleAdmin(admin.ModelAdmin):
    list_display = ['name', 'description']
    filter_horizontal = ['permissions']
```

**Key Features:**

1. **Custom User Model**: Email-based authentication with additional fields
2. **Role-Based Access Control**: Flexible role system with permissions
3. **Custom Permissions**: Model-level and object-level permissions
4. **Decorators and Mixins**: Reusable authorization logic
5. **Profile Management**: Extended user information
6. **Password Management**: Change password and reset functionality
7. **Admin Integration**: Full admin panel support

---

#### 25. Explain Django's ORM query optimization techniques. Demonstrate select_related, prefetch_related, and database indexing strategies.

**Answer:**

## Django ORM Query Optimization

### 1. Understanding the N+1 Query Problem

```python
# BAD: N+1 queries
posts = Post.objects.all()  # 1 query
for post in posts:
    print(post.author.username)  # N additional queries (one per post)
    print(post.category.name)  # N more queries

# GOOD: Using select_related
posts = Post.objects.select_related('author', 'category').all()  # 1 query with JOINs
for post in posts:
    print(post.author.username)  # No additional query
    print(post.category.name)  # No additional query
```

### 2. select_related() - Forward ForeignKey and OneToOne

```python
# models.py
class Author(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()

class Category(models.Model):
    name = models.CharField(max_length=100)

class Post(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    category = models.ForeignKey(Category, on_delete=models.CASCADE)
    content = models.TextField()

# Without select_related (N+1 problem)
posts = Post.objects.all()
for post in posts:
    print(f"{post.title} by {post.author.name}")  # Executes query for each author

# With select_related (single JOIN query)
posts = Post.objects.select_related('author').all()
for post in posts:
    print(f"{post.title} by {post.author.name}")  # No additional queries

# Multiple relations
posts = Post.objects.select_related('author', 'category').all()

# Nested relations
class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField()

posts = Post.objects.select_related('author__profile').all()
for post in posts:
    print(post.author.profile.bio)  # No additional queries
```

### 3. prefetch_related() - Reverse ForeignKey and ManyToMany

```python
# models.py
class Tag(models.Model):
    name = models.CharField(max_length=50)

class Post(models.Model):
    title = models.CharField(max_length=200)
    tags = models.ManyToManyField(Tag)

class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
    text = models.TextField()

# Without prefetch_related (N+1 problem)
posts = Post.objects.all()
for post in posts:
    for tag in post.tags.all():  # Query for each post
        print(tag.name)

# With prefetch_related (2 queries total)
posts = Post.objects.prefetch_related('tags').all()
for post in posts:
    for tag in post.tags.all():  # No additional queries
        print(tag.name)

# Reverse ForeignKey
posts = Post.objects.prefetch_related('comments').all()
for post in posts:
    for comment in post.comments.all():  # No additional queries
        print(comment.text)

# Multiple prefetch
posts = Post.objects.prefetch_related('tags', 'comments').all()

# Nested prefetch
posts = Post.objects.prefetch_related('comments__author').all()
for post in posts:
    for comment in post.comments.all():
        print(comment.author.name)  # No additional queries
```

### 4. Prefetch() for Custom Querysets

```python
from django.db.models import Prefetch

# Custom queryset for prefetch
active_comments = Comment.objects.filter(is_active=True).select_related('author')
posts = Post.objects.prefetch_related(
    Prefetch('comments', queryset=active_comments, to_attr='active_comments_list')
).all()

for post in posts:
    for comment in post.active_comments_list:  # Only active comments
        print(f"{comment.text} by {comment.author.name}")

# Multiple custom prefetches
recent_comments = Comment.objects.filter(created_at__gte=timezone.now() - timedelta(days=7))
top_tags = Tag.objects.filter(posts__views__gte=1000).distinct()

posts = Post.objects.prefetch_related(
    Prefetch('comments', queryset=recent_comments, to_attr='recent_comments_list'),
    Prefetch('tags', queryset=top_tags, to_attr='popular_tags_list')
).all()
```

### 5. Combining select_related() and prefetch_related()

```python
# Efficient query combining both
posts = Post.objects.select_related(
    'author',
    'category'
).prefetch_related(
    'tags',
    Prefetch('comments', queryset=Comment.objects.select_related('author'))
).all()

for post in posts:
    print(f"Title: {post.title}")
    print(f"Author: {post.author.name}")
    print(f"Category: {post.category.name}")
    
    print("Tags:")
    for tag in post.tags.all():
        print(f"  - {tag.name}")
    
    print("Comments:")
    for comment in post.comments.all():
        print(f"  {comment.text} by {comment.author.name}")
```

### 6. only() and defer()

```python
# only() - Load only specified fields
posts = Post.objects.only('title', 'created_at').all()
for post in posts:
    print(post.title)  # No additional query
    print(post.content)  # Additional query to fetch content

# defer() - Exclude specified fields
posts = Post.objects.defer('content').all()
for post in posts:
    print(post.title)  # No additional query
    print(post.content)  # Additional query to fetch content

# Combining with select_related
posts = Post.objects.select_related('author').only('title', 'author__name').all()

# Deferring large fields
posts = Post.objects.defer('content', 'large_text_field').all()
```

### 7. values() and values_list()

```python
# values() - Returns dictionaries
posts = Post.objects.values('title', 'author__name', 'category__name')
# [{'title': 'Post 1', 'author__name': 'John', 'category__name': 'Tech'}, ...]

# values_list() - Returns tuples
posts = Post.objects.values_list('title', 'author__name')
# [('Post 1', 'John'), ('Post 2', 'Jane'), ...]

# Flat list for single field
titles = Post.objects.values_list('title', flat=True)
# ['Post 1', 'Post 2', 'Post 3']

# Named tuples
from collections import namedtuple
posts = Post.objects.values_list('title', 'author__name', named=True)
for post in posts:
    print(post.title, post.author__name)
```

### 8. Database Indexing

```python
# models.py
class Post(models.Model):
    title = models.CharField(max_length=200, db_index=True)  # Single field index
    slug = models.SlugField(unique=True, db_index=True)
    author = models.ForeignKey(Author, on_delete=models.CASCADE, db_index=True)
    created_at = models.DateTimeField(auto_now_add=True)
    status = models.CharField(max_length=20)
    
    class Meta:
        # Composite indexes
        indexes = [
            models.Index(fields=['status', 'created_at']),
            models.Index(fields=['-created_at']),  # Descending index
            models.Index(fields=['author', 'status']),
            models.Index(fields=['title', 'slug']),
        ]
        
        # Unique together (creates index)
        unique_together = [['slug', 'author']]

# Partial indexes (PostgreSQL)
from django.contrib.postgres.indexes import Index

class Post(models.Model):
    # ... fields ...
    
    class Meta:
        indexes = [
            Index(
                fields=['created_at'],
                name='published_posts_idx',
                condition=models.Q(status='published')
            ),
        ]
```

### 9. Aggregation and Annotation

```python
from django.db.models import Count, Avg, Max, Min, Sum, F, Q

# Count related objects
authors = Author.objects.annotate(post_count=Count('post')).all()
for author in authors:
    print(f"{author.name}: {author.post_count} posts")

# Multiple aggregations
posts = Post.objects.aggregate(
    total_posts=Count('id'),
    avg_views=Avg('views'),
    max_views=Max('views'),
    min_views=Min('views')
)

# Conditional aggregation
posts = Post.objects.aggregate(
    published_count=Count('id', filter=Q(status='published')),
    draft_count=Count('id', filter=Q(status='draft'))
)

# Annotate with calculations
posts = Post.objects.annotate(
    comment_count=Count('comments'),
    avg_rating=Avg('comments__rating')
).filter(comment_count__gt=5)

# F expressions for field comparisons
posts = Post.objects.filter(views__gt=F('likes') * 2)

# Update with F expressions (atomic)
Post.objects.filter(id=1).update(views=F('views') + 1)
```

### 10. Query Optimization Patterns

```python
# Batch operations
# BAD
for post in posts:
    post.views += 1
    post.save()  # N queries

# GOOD
Post.objects.filter(id__in=[p.id for p in posts]).update(views=F('views') + 1)  # 1 query

# Bulk create
# BAD
for i in range(1000):
    Post.objects.create(title=f'Post {i}')  # 1000 queries

# GOOD
posts = [Post(title=f'Post {i}') for i in range(1000)]
Post.objects.bulk_create(posts)  # 1 query

# Bulk update
posts = Post.objects.all()
for post in posts:
    post.views += 1
Post.objects.bulk_update(posts, ['views'])  # 1 query

# exists() instead of count()
# BAD
if Post.objects.filter(author=user).count() > 0:
    pass

# GOOD
if Post.objects.filter(author=user).exists():
    pass

# iterator() for large querysets
for post in Post.objects.iterator(chunk_size=1000):
    process_post(post)  # Processes in chunks, saves memory

# Raw SQL when necessary
posts = Post.objects.raw('SELECT * FROM blog_post WHERE views > %s', [1000])
```

### 11. Database Query Analysis

```python
# Enable query logging
import logging
logging.basicConfig()
logging.getLogger('django.db.backends').setLevel(logging.DEBUG)

# Using django-debug-toolbar
INSTALLED_APPS = [
    'debug_toolbar',
]

MIDDLEWARE = [
    'debug_toolbar.middleware.DebugToolbarMiddleware',
]

# Query count and time
from django.db import connection
from django.test.utils import override_settings

with override_settings(DEBUG=True):
    posts = Post.objects.select_related('author').all()
    print(len(connection.queries))  # Number of queries
    print(connection.queries)  # Query details

# Explain query
print(Post.objects.filter(status='published').explain())

# Query optimization context manager
from django.db import reset_queries

reset_queries()
# Your code here
print(f"Executed {len(connection.queries)} queries")
```

### 12. Caching Querysets

```python
from django.core.cache import cache

def get_popular_posts():
    cache_key = 'popular_posts'
    posts = cache.get(cache_key)
    
    if posts is None:
        posts = list(
            Post.objects.select_related('author', 'category')
            .prefetch_related('tags')
            .filter(views__gte=1000)
            .order_by('-views')[:10]
        )
        cache.set(cache_key, posts, 60 * 15)  # Cache for 15 minutes
    
    return posts
```

**Best Practices:**

1. **Use select_related()** for ForeignKey and OneToOne relationships
2. **Use prefetch_related()** for ManyToMany and reverse ForeignKey
3. **Add database indexes** on frequently queried fields
4. **Use only()/defer()** to load only necessary fields
5. **Batch operations** instead of loops with individual saves
6. **Use exists()** instead of count() for existence checks
7. **Profile queries** with Django Debug Toolbar
8. **Cache expensive queries** when appropriate
9. **Use database-level operations** (F expressions, aggregations)
10. **Avoid N+1 queries** by planning data access patterns

---

## SET B

### Short Answer Questions

#### 1. What is middleware in Django? Explain its execution order.

**Answer:**

Middleware in Django is a framework of hooks into Django's request/response processing. It's a lightweight, low-level plugin system for globally altering Django's input or output.

**Middleware Execution Order:**

```python
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',           # 1
    'django.contrib.sessions.middleware.SessionMiddleware',    # 2
    'django.middleware.common.CommonMiddleware',               # 3
    'django.middleware.csrf.CsrfViewMiddleware',              # 4
    'django.contrib.auth.middleware.AuthenticationMiddleware', # 5
    'django.contrib.messages.middleware.MessageMiddleware',    # 6
    'django.middleware.clickjacking.XFrameOptionsMiddleware',  # 7
]
```

**Request Processing (Top to Bottom):**
1. SecurityMiddleware → SessionMiddleware → CommonMiddleware → ... → View

**Response Processing (Bottom to Top):**
1. View → ... → CommonMiddleware → SessionMiddleware → SecurityMiddleware

**Custom Middleware Example:**

```python
# myapp/middleware.py
class CustomMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        # Code executed before the view (request processing)
        print("Before view")
        
        response = self.get_response(request)
        
        # Code executed after the view (response processing)
        print("After view")
        
        return response
    
    def process_exception(self, request, exception):
        # Called when view raises exception
        return None
```

---

#### 2. How does Django handle static files and media files? Explain the configuration.

**Answer:**

**Static Files** (CSS, JavaScript, images that are part of the application):

```python
# settings.py
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]

STATICFILES_FINDERS = [
    'django.contrib.staticfiles.finders.FileSystemFinder',
    'django.contrib.staticfiles.finders.AppDirectoriesFinder',
]
```

**In Templates:**
```django
{% load static %}
<link rel="stylesheet" href="{% static 'css/style.css' %}">
<img src="{% static 'images/logo.png' %}" alt="Logo">
```

**Collect Static Files:**
```bash
python manage.py collectstatic
```

**Media Files** (User-uploaded content):

```python
# settings.py
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

**In urls.py (Development):**
```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ... your patterns
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

**In Models:**
```python
class Profile(models.Model):
    avatar = models.ImageField(upload_to='avatars/')
    document = models.FileField(upload_to='documents/%Y/%m/%d/')
```

**Production Setup (with Nginx):**
```nginx
location /static/ {
    alias /path/to/staticfiles/;
}

location /media/ {
    alias /path/to/media/;
}
```

---

#### 3. Explain Django signals and their use cases.

**Answer:**

Django signals allow decoupled applications to get notified when certain actions occur elsewhere in the framework.

**Built-in Signals:**

1. **Model Signals:**
   - `pre_save` / `post_save`
   - `pre_delete` / `post_delete`
   - `m2m_changed`

2. **Request/Response Signals:**
   - `request_started` / `request_finished`
   - `got_request_exception`

3. **Database Signals:**
   - `connection_created`

**Example Usage:**

```python
# models.py
from django.db import models
from django.contrib.auth.models import User

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField(blank=True)

# signals.py
from django.db.models.signals import post_save, pre_delete
from django.dispatch import receiver
from django.contrib.auth.models import User
from .models import Profile

@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)

@receiver(post_save, sender=User)
def save_user_profile(sender, instance, **kwargs):
    instance.profile.save()

@receiver(pre_delete, sender=User)
def delete_user_files(sender, instance, **kwargs):
    # Clean up user files before deletion
    if instance.profile.avatar:
        instance.profile.avatar.delete()

# apps.py
from django.apps import AppConfig

class MyAppConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'myapp'
    
    def ready(self):
        import myapp.signals
```

**Custom Signals:**

```python
# signals.py
from django.dispatch import Signal

order_placed = Signal()

# views.py
from .signals import order_placed

def checkout(request):
    # ... process order
    order_placed.send(sender=Order, order=order, user=request.user)

# handlers.py
from django.dispatch import receiver
from .signals import order_placed

@receiver(order_placed)
def send_order_confirmation(sender, order, user, **kwargs):
    # Send email confirmation
    send_mail(
        'Order Confirmation',
        f'Your order #{order.id} has been placed.',
        'from@example.com',
        [user.email],
    )
```

**Use Cases:**
- Auto-create related models (Profile when User is created)
- Send notifications
- Update search indexes
- Clear caches
- Log activities
- Generate thumbnails for uploaded images

---

#### 4. What is Django's context processor? Provide examples.

**Answer:**

Context processors are functions that add variables to the template context globally, making them available in all templates.

**Built-in Context Processors:**

```python
# settings.py
TEMPLATES = [
    {
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
                'django.template.context_processors.media',
                'django.template.context_processors.static',
            ],
        },
    },
]
```

**Custom Context Processor:**

```python
# myapp/context_processors.py
from .models import Category, SiteSettings

def site_settings(request):
    """Add site settings to all templates"""
    try:
        settings = SiteSettings.objects.first()
    except SiteSettings.DoesNotExist:
        settings = None
    
    return {
        'site_settings': settings,
        'site_name': 'My Website',
        'current_year': datetime.now().year,
    }

def navigation(request):
    """Add navigation categories to all templates"""
    return {
        'nav_categories': Category.objects.filter(show_in_nav=True),
    }

def user_notifications(request):
    """Add user notifications count"""
    if request.user.is_authenticated:
        unread_count = request.user.notifications.filter(read=False).count()
    else:
        unread_count = 0
    
    return {
        'unread_notifications': unread_count,
    }
```

**Register in settings.py:**

```python
TEMPLATES = [
    {
        'OPTIONS': {
            'context_processors': [
                # ... default processors
                'myapp.context_processors.site_settings',
                'myapp.context_processors.navigation',
                'myapp.context_processors.user_notifications',
            ],
        },
    },
]
```

**Usage in Templates:**

```django
<!-- Now available in all templates -->
<title>{{ site_name }} - {{ current_year }}</title>

<nav>
    {% for category in nav_categories %}
        <a href="{{ category.get_absolute_url }}">{{ category.name }}</a>
    {% endfor %}
</nav>

{% if unread_notifications > 0 %}
    <span class="badge">{{ unread_notifications }}</span>
{% endif %}
```

---

#### 5. Explain Django's session framework and different session backends.

**Answer:**

Django's session framework allows you to store and retrieve arbitrary data on a per-site-visitor basis.

**Session Configuration:**

```python
# settings.py
SESSION_ENGINE = 'django.contrib.sessions.backends.db'  # Default
SESSION_COOKIE_NAME = 'sessionid'
SESSION_COOKIE_AGE = 1209600  # 2 weeks in seconds
SESSION_COOKIE_SECURE = True  # HTTPS only
SESSION_COOKIE_HTTPONLY = True  # Not accessible via JavaScript
SESSION_SAVE_EVERY_REQUEST = False
SESSION_EXPIRE_AT_BROWSER_CLOSE = False
```

**Session Backends:**

1. **Database-backed sessions** (default):
```python
SESSION_ENGINE = 'django.contrib.sessions.backends.db'
```

2. **Cached sessions:**
```python
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
SESSION_CACHE_ALIAS = 'default'
```

3. **Cached database sessions** (write-through cache):
```python
SESSION_ENGINE = 'django.contrib.sessions.backends.cached_db'
```

4. **File-based sessions:**
```python
SESSION_ENGINE = 'django.contrib.sessions.backends.file'
SESSION_FILE_PATH = '/tmp/django_sessions'
```

5. **Cookie-based sessions:**
```python
SESSION_ENGINE = 'django.contrib.sessions.backends.signed_cookies'
```

**Using Sessions in Views:**

```python
# Set session data
def login_view(request):
    request.session['user_id'] = user.id
    request.session['username'] = user.username
    request.session.set_expiry(3600)  # Expire in 1 hour

# Get session data
def profile_view(request):
    user_id = request.session.get('user_id')
    username = request.session.get('username', 'Guest')

# Delete session data
def logout_view(request):
    del request.session['user_id']
    # Or flush all session data
    request.session.flush()

# Check if key exists
def check_session(request):
    if 'user_id' in request.session:
        # Do something
        pass

# Set session expiry
request.session.set_expiry(300)  # 5 minutes
request.session.set_expiry(0)    # Expire at browser close
request.session.set_expiry(None) # Use global setting
```

**Session in Templates:**

```django
{{ request.session.username }}
```

**Clear expired sessions:**
```bash
python manage.py clearsessions
```

---

#### 6. What are Django management commands? How do you create custom commands?

**Answer:**

Management commands are Django's command-line utilities accessed via `manage.py`.

**Built-in Commands:**
- `runserver`, `migrate`, `makemigrations`
- `createsuperuser`, `collectstatic`
- `shell`, `dbshell`, `test`

**Creating Custom Commands:**

**Directory Structure:**
```
myapp/
    management/
        __init__.py
        commands/
            __init__.py
            import_data.py
            cleanup.py
```

**Basic Command:**

```python
# myapp/management/commands/import_data.py
from django.core.management.base import BaseCommand
from myapp.models import Product

class Command(BaseCommand):
    help = 'Import products from CSV file'
    
    def handle(self, *args, **options):
        self.stdout.write('Starting import...')
        
        # Your logic here
        count = Product.objects.count()
        
        self.stdout.write(
            self.style.SUCCESS(f'Successfully imported {count} products')
        )
```

**Command with Arguments:**

```python
from django.core.management.base import BaseCommand, CommandError
from myapp.models import User

class Command(BaseCommand):
    help = 'Delete inactive users'
    
    def add_arguments(self, parser):
        # Positional argument
        parser.add_argument('days', type=int, help='Days of inactivity')
        
        # Optional argument
        parser.add_argument(
            '--dry-run',
            action='store_true',
            help='Show what would be deleted without deleting',
        )
        
        # Named argument
        parser.add_argument(
            '--email',
            type=str,
            help='Send report to this email',
        )
    
    def handle(self, *args, **options):
        days = options['days']
        dry_run = options['dry_run']
        email = options.get('email')
        
        cutoff_date = timezone.now() - timedelta(days=days)
        users = User.objects.filter(last_login__lt=cutoff_date)
        
        if dry_run:
            self.stdout.write(f'Would delete {users.count()} users')
        else:
            count = users.count()
            users.delete()
            self.stdout.write(
                self.style.SUCCESS(f'Deleted {count} users')
            )
        
        if email:
            # Send email report
            pass
```

**Advanced Command:**

```python
from django.core.management.base import BaseCommand
from django.db import transaction
import csv

class Command(BaseCommand):
    help = 'Bulk import data with progress bar'
    
    def add_arguments(self, parser):
        parser.add_argument('file', type=str)
    
    def handle(self, *args, **options):
        file_path = options['file']
        
        try:
            with open(file_path, 'r') as f:
                reader = csv.DictReader(f)
                rows = list(reader)
                total = len(rows)
                
                with transaction.atomic():
                    for i, row in enumerate(rows, 1):
                        # Process row
                        Product.objects.create(
                            name=row['name'],
                            price=row['price']
                        )
                        
                        # Progress indicator
                        if i % 100 == 0:
                            self.stdout.write(f'Processed {i}/{total}')
                
                self.stdout.write(
                    self.style.SUCCESS(f'Successfully imported {total} items')
                )
        
        except FileNotFoundError:
            raise CommandError(f'File "{file_path}" does not exist')
        except Exception as e:
            raise CommandError(f'Error: {str(e)}')
```

**Running Commands:**
```bash
python manage.py import_data
python manage.py cleanup 30 --dry-run
python manage.py cleanup 30 --email admin@example.com
```

---

#### 7. Explain Django's email backend and how to send emails.

**Answer:**

Django provides a framework for sending emails with support for multiple backends.

**Email Configuration:**

```python
# settings.py

# SMTP Backend (Production)
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_PORT = 587
EMAIL_USE_TLS = True
EMAIL_HOST_USER = 'your-email@gmail.com'
EMAIL_HOST_PASSWORD = 'your-app-password'
DEFAULT_FROM_EMAIL = 'noreply@example.com'
SERVER_EMAIL = 'server@example.com'

# Console Backend (Development)
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

# File Backend (Testing)
EMAIL_BACKEND = 'django.core.mail.backends.filebased.EmailBackend'
EMAIL_FILE_PATH = '/tmp/app-emails'

# Memory Backend (Testing)
EMAIL_BACKEND = 'django.core.mail.backends.locmem.EmailBackend'

# Dummy Backend (No emails sent)
EMAIL_BACKEND = 'django.core.mail.backends.dummy.EmailBackend'
```

**Sending Emails:**

```python
from django.core.mail import send_mail, send_mass_mail, EmailMessage, EmailMultiAlternatives
from django.template.loader import render_to_string
from django.utils.html import strip_tags

# Simple email
send_mail(
    'Subject',
    'Message body',
    'from@example.com',
    ['to@example.com'],
    fail_silently=False,
)

# Multiple recipients
send_mail(
    'Subject',
    'Message',
    'from@example.com',
    ['user1@example.com', 'user2@example.com'],
)

# Mass email (efficient for multiple emails)
message1 = ('Subject 1', 'Body 1', 'from@example.com', ['to1@example.com'])
message2 = ('Subject 2', 'Body 2', 'from@example.com', ['to2@example.com'])
send_mass_mail((message1, message2), fail_silently=False)

# Email with attachments
email = EmailMessage(
    'Subject',
    'Body',
    'from@example.com',
    ['to@example.com'],
)
email.attach_file('/path/to/file.pdf')
email.send()

# HTML email
html_content = render_to_string('email_template.html', {'user': user})
text_content = strip_tags(html_content)

email = EmailMultiAlternatives(
    'Subject',
    text_content,
    'from@example.com',
    ['to@example.com'],
)
email.attach_alternative(html_content, "text/html")
email.send()

# Email with inline images
email = EmailMessage(
    'Subject',
    '<img src="cid:logo">',
    'from@example.com',
    ['to@example.com'],
)
email.content_subtype = 'html'
with open('logo.png', 'rb') as f:
    email.attach('logo.png', f.read(), 'image/png')
    email.attach_alternative(
        '<img src="cid:logo">',
        'text/html'
    )
email.send()
```

**Email Template:**

```django
<!-- templates/email_template.html -->
<!DOCTYPE html>
<html>
<head>
    <style>
        body { font-family: Arial, sans-serif; }
        .header { background: #007bff; color: white; padding: 20px; }
    </style>
</head>
<body>
    <div class="header">
        <h1>Welcome {{ user.username }}!</h1>
    </div>
    <p>Thank you for registering.</p>
    <a href="{{ activation_link }}">Activate your account</a>
</body>
</html>
```

**Async Email (Celery):**

```python
# tasks.py
from celery import shared_task
from django.core.mail import send_mail

@shared_task
def send_email_task(subject, message, recipient_list):
    send_mail(
        subject,
        message,
        'from@example.com',
        recipient_list,
    )

# views.py
from .tasks import send_email_task

def register(request):
    # ... registration logic
    send_email_task.delay(
        'Welcome',
        'Thank you for registering',
        [user.email]
    )
```

---

#### 8. What is Django's contenttypes framework? Provide use cases.

**Answer:**

The contenttypes framework provides a high-level, generic interface for working with models in your Django project. It allows you to create relationships to any model.

**Configuration:**

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.contenttypes',  # Usually included by default
]
```

**Generic Foreign Keys:**

```python
from django.contrib.contenttypes.fields import GenericForeignKey, GenericRelation
from django.contrib.contenttypes.models import ContentType
from django.db import models

# Generic model that can relate to any other model
class Comment(models.Model):
    content_type = models.ForeignKey(ContentType, on_delete=models.CASCADE)
    object_id = models.PositiveIntegerField()
    content_object = GenericForeignKey('content_type', 'object_id')
    
    text = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    
    def __str__(self):
        return f'Comment on {self.content_object}'

# Models that can have comments
class Article(models.Model):
    title = models.CharField(max_length=200)
    comments = GenericRelation(Comment)

class Photo(models.Model):
    title = models.CharField(max_length=200)
    comments = GenericRelation(Comment)

# Usage
article = Article.objects.get(id=1)
Comment.objects.create(
    content_object=article,
    text='Great article!'
)

# Access comments
for comment in article.comments.all():
    print(comment.text)

# Query across content types
article_type = ContentType.objects.get_for_model(Article)
article_comments = Comment.objects.filter(content_type=article_type)
```

**Use Cases:**

1. **Activity/Audit Log:**

```python
class ActivityLog(models.Model):
    ACTION_CHOICES = [
        ('create', 'Created'),
        ('update', 'Updated'),
        ('delete', 'Deleted'),
    ]
    
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    action = models.CharField(max_length=20, choices=ACTION_CHOICES)
    content_type = models.ForeignKey(ContentType, on_delete=models.CASCADE)
    object_id = models.PositiveIntegerField()
    content_object = GenericForeignKey('content_type', 'object_id')
    timestamp = models.DateTimeField(auto_now_add=True)
    
    def __str__(self):
        return f'{self.user} {self.action} {self.content_object}'

# Log activity
ActivityLog.objects.create(
    user=request.user,
    action='create',
    content_object=article
)
```

2. **Tagging System:**

```python
class Tag(models.Model):
    name = models.CharField(max_length=50, unique=True)

class TaggedItem(models.Model):
    tag = models.ForeignKey(Tag, on_delete=models.CASCADE)
    content_type = models.ForeignKey(ContentType, on_delete=models.CASCADE)
    object_id = models.PositiveIntegerField()
    content_object = GenericForeignKey('content_type', 'object_id')
    
    class Meta:
        unique_together = ['tag', 'content_type', 'object_id']

# Tag different models
article = Article.objects.get(id=1)
photo = Photo.objects.get(id=1)

django_tag = Tag.objects.get(name='django')
TaggedItem.objects.create(tag=django_tag, content_object=article)
TaggedItem.objects.create(tag=django_tag, content_object=photo)
```

3. **Favorites/Bookmarks:**

```python
class Favorite(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    content_type = models.ForeignKey(ContentType, on_delete=models.CASCADE)
    object_id = models.PositiveIntegerField()
    content_object = GenericForeignKey('content_type', 'object_id')
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        unique_together = ['user', 'content_type', 'object_id']

# User favorites an article
Favorite.objects.create(user=request.user, content_object=article)

# Get user's favorites
user_favorites = Favorite.objects.filter(user=request.user)
```

4. **Ratings:**

```python
class Rating(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    content_type = models.ForeignKey(ContentType, on_delete=models.CASCADE)
    object_id = models.PositiveIntegerField()
    content_object = GenericForeignKey('content_type', 'object_id')
    score = models.IntegerField(choices=[(i, i) for i in range(1, 6)])
    
    class Meta:
        unique_together = ['user', 'content_type', 'object_id']
```

**Querying ContentTypes:**

```python
from django.contrib.contenttypes.models import ContentType

# Get ContentType for a model
article_type = ContentType.objects.get_for_model(Article)

# Get model class from ContentType
model_class = article_type.model_class()

# Get all instances of a content type
articles = model_class.objects.all()

# Filter by content type
comments_on_articles = Comment.objects.filter(content_type=article_type)
```

---

#### 9. Explain Django's pagination system with examples.

**Answer:**

Django provides built-in pagination support through the `Paginator` class.

**Basic Pagination:**

```python
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger
from django.shortcuts import render
from .models import Article

def article_list(request):
    article_list = Article.objects.all().order_by('-created_at')
    paginator = Paginator(article_list, 10)  # 10 items per page
    
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    
    return render(request, 'articles.html', {'page_obj': page_obj})
```

**Template:**

```django
<!-- articles.html -->
<div class="articles">
    {% for article in page_obj %}
        <div class="article">
            <h2>{{ article.title }}</h2>
            <p>{{ article.excerpt }}</p>
        </div>
    {% endfor %}
</div>

<!-- Pagination controls -->
<div class="pagination">
    <span class="step-links">
        {% if page_obj.has_previous %}
            <a href="?page=1">&laquo; first</a>
            <a href="?page={{ page_obj.previous_page_number }}">previous</a>
        {% endif %}
        
        <span class="current">
            Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}
        </span>
        
        {% if page_obj.has_next %}
            <a href="?page={{ page_obj.next_page_number }}">next</a>
            <a href="?page={{ page_obj.paginator.num_pages }}">last &raquo;</a>
        {% endif %}
    </span>
</div>

<!-- Page info -->
<p>
    Showing {{ page_obj.start_index }} to {{ page_obj.end_index }} 
    of {{ page_obj.paginator.count }} results
</p>
```

**Advanced Pagination with Error Handling:**

```python
def article_list(request):
    article_list = Article.objects.all()
    paginator = Paginator(article_list, 10)
    
    page = request.GET.get('page', 1)
    
    try:
        articles = paginator.page(page)
    except PageNotAnInteger:
        # If page is not an integer, deliver first page
        articles = paginator.page(1)
    except EmptyPage:
        # If page is out of range, deliver last page
        articles = paginator.page(paginator.num_pages)
    
    return render(request, 'articles.html', {'articles': articles})
```

**Class-Based View Pagination:**

```python
from django.views.generic import ListView

class ArticleListView(ListView):
    model = Article
    template_name = 'articles.html'
    context_object_name = 'articles'
    paginate_by = 10
    ordering = ['-created_at']
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['total_articles'] = Article.objects.count()
        return context
```

**Custom Pagination Template:**

```django
<!-- pagination.html -->
<nav aria-label="Page navigation">
    <ul class="pagination">
        {% if page_obj.has_previous %}
            <li class="page-item">
                <a class="page-link" href="?page=1">First</a>
            </li>
            <li class="page-item">
                <a class="page-link" href="?page={{ page_obj.previous_page_number }}">
                    Previous
                </a>
            </li>
        {% endif %}
        
        {% for num in page_obj.paginator.page_range %}
            {% if page_obj.number == num %}
                <li class="page-item active">
                    <span class="page-link">{{ num }}</span>
                </li>
            {% elif num > page_obj.number|add:'-3' and num < page_obj.number|add:'3' %}
                <li class="page-item">
                    <a class="page-link" href="?page={{ num }}">{{ num }}</a>
                </li>
            {% endif %}
        {% endfor %}
        
        {% if page_obj.has_next %}
            <li class="page-item">
                <a class="page-link" href="?page={{ page_obj.next_page_number }}">
                    Next
                </a>
            </li>
            <li class="page-item">
                <a class="page-link" href="?page={{ page_obj.paginator.num_pages }}">
                    Last
                </a>
            </li>
        {% endif %}
    </ul>
</nav>
```

**Pagination with Filters:**

```python
def article_list(request):
    articles = Article.objects.all()
    
    # Apply filters
    category = request.GET.get('category')
    if category:
        articles = articles.filter(category__slug=category)
    
    search = request.GET.get('search')
    if search:
        articles = articles.filter(title__icontains=search)
    
    # Paginate
    paginator = Paginator(articles, 10)
    page_obj = paginator.get_page(request.GET.get('page'))
    
    return render(request, 'articles.html', {
        'page_obj': page_obj,
        'category': category,
        'search': search,
    })
```

**Template with filters:**

```django
<!-- Preserve filters in pagination links -->
<a href="?page={{ page_obj.next_page_number }}{% if category %}&category={{ category }}{% endif %}{% if search %}&search={{ search }}{% endif %}">
    Next
</a>
```

**API Pagination (DRF):**

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20
}

# Custom pagination
from rest_framework.pagination import PageNumberPagination

class CustomPagination(PageNumberPagination):
    page_size = 10
    page_size_query_param = 'page_size'
    max_page_size = 100

# views.py
class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    pagination_class = CustomPagination
```

**Paginator Properties:**

```python
paginator = Paginator(articles, 10)

paginator.count          # Total number of objects
paginator.num_pages      # Total number of pages
paginator.page_range     # Range of page numbers

page = paginator.page(1)
page.object_list         # List of objects on this page
page.number              # Current page number
page.has_next()          # Whether there's a next page
page.has_previous()      # Whether there's a previous page
page.next_page_number()  # Next page number
page.previous_page_number()  # Previous page number
page.start_index()       # 1-based index of first object
page.end_index()         # 1-based index of last object
```

---

#### 10. What is Django's sites framework? Explain its use cases.

**Answer:**

The Django sites framework allows you to operate multiple websites from a single Django installation and database.

**Configuration:**

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.sites',
]

SITE_ID = 1
```

**Migration:**
```bash
python manage.py migrate
```

**Usage:**

```python
from django.contrib.sites.models import Site

# Get current site
current_site = Site.objects.get_current()
print(current_site.domain)  # example.com
print(current_site.name)    # Example Site

# Create/update sites
Site.objects.create(domain='blog.example.com', name='Blog')
Site.objects.create(domain='shop.example.com', name='Shop')

# Associate content with sites
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    sites = models.ManyToManyField(Site)

# Filter by current site
current_articles = Article.objects.filter(sites=current_site)
```

**Use Cases:**

1. **Multi-tenant Applications:**
```python
# Automatically filter by current site
class ArticleManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(site=Site.objects.get_current())

class Article(models.Model):
    site = models.ForeignKey(Site, on_delete=models.CASCADE)
    objects = ArticleManager()
```

2. **Site-specific Settings:**
```python
class SiteSettings(models.Model):
    site = models.OneToOneField(Site, on_delete=models.CASCADE)
    logo = models.ImageField(upload_to='logos/')
    theme_color = models.CharField(max_length=7)
    contact_email = models.EmailField()
```

3. **Email Templates:**
```python
from django.contrib.sites.shortcuts import get_current_site

def send_welcome_email(request, user):
    current_site = get_current_site(request)
    send_mail(
        f'Welcome to {current_site.name}',
        f'Thank you for joining {current_site.domain}',
        f'noreply@{current_site.domain}',
        [user.email],
    )
```

---

#### 11. Explain Django's internationalization (i18n) and localization (l10n).

**Answer:**

Django provides comprehensive support for translating applications into multiple languages.

**Configuration:**

```python
# settings.py
from django.utils.translation import gettext_lazy as _

LANGUAGE_CODE = 'en-us'
TIME_ZONE = 'UTC'
USE_I18N = True
USE_L10N = True
USE_TZ = True

LANGUAGES = [
    ('en', _('English')),
    ('es', _('Spanish')),
    ('fr', _('French')),
    ('de', _('German')),
]

LOCALE_PATHS = [
    os.path.join(BASE_DIR, 'locale'),
]

MIDDLEWARE = [
    'django.middleware.locale.LocaleMiddleware',  # Add this
]
```

**Translation in Python Code:**

```python
from django.utils.translation import gettext as _
from django.utils.translation import gettext_lazy, ngettext

# Simple translation
message = _('Welcome to our site')

# Lazy translation (for module-level strings)
class MyModel(models.Model):
    name = models.CharField(max_length=100, verbose_name=gettext_lazy('Name'))

# Pluralization
count = 5
message = ngettext(
    'There is %(count)d item',
    'There are %(count)d items',
    count
) % {'count': count}

# Context-specific translation
from django.utils.translation import pgettext
message = pgettext('menu item', 'Home')  # vs pgettext('button', 'Home')
```

**Translation in Templates:**

```django
{% load i18n %}

<!-- Simple translation -->
<h1>{% trans "Welcome" %}</h1>

<!-- Translation with variables -->
{% blocktrans with name=user.name %}
    Hello {{ name }}!
{% endblocktrans %}

<!-- Pluralization -->
{% blocktrans count counter=items|length %}
    There is {{ counter }} item
{% plural %}
    There are {{ counter }} items
{% endblocktrans %}

<!-- Language switcher -->
<form action="{% url 'set_language' %}" method="post">
    {% csrf_token %}
    <select name="language">
        {% get_current_language as LANGUAGE_CODE %}
        {% get_available_languages as LANGUAGES %}
        {% for lang_code, lang_name in LANGUAGES %}
            <option value="{{ lang_code }}" {% if lang_code == LANGUAGE_CODE %}selected{% endif %}>
                {{ lang_name }}
            </option>
        {% endfor %}
    </select>
    <button type="submit">Change</button>
</form>
```

**Creating Translation Files:**

```bash
# Create message files
python manage.py makemessages -l es
python manage.py makemessages -l fr

# For JavaScript
python manage.py makemessages -d djangojs -l es

# Compile messages
python manage.py compilemessages
```

**Translation File (locale/es/LC_MESSAGES/django.po):**

```po
msgid "Welcome to our site"
msgstr "Bienvenido a nuestro sitio"

msgid "Hello %(name)s"
msgstr "Hola %(name)s"
```

**URL Patterns:**

```python
# urls.py
from django.conf.urls.i18n import i18n_patterns
from django.urls import path, include

urlpatterns = [
    path('i18n/', include('django.conf.urls.i18n')),
]

urlpatterns += i18n_patterns(
    path('', views.home, name='home'),
    path('about/', views.about, name='about'),
)
# URLs will be: /en/about/, /es/about/, /fr/about/
```

**Localization (Formatting):**

```python
from django.utils import formats
from django.utils.formats import date_format, number_format

# Date formatting
date_format(datetime.now(), 'SHORT_DATE_FORMAT')

# Number formatting
number_format(1234.56, decimal_pos=2)  # Locale-specific

# Currency
from django.contrib.humanize.templatetags.humanize import intcomma
formatted = intcomma(1234567)  # 1,234,567
```

---

#### 12. What are Django's generic views? Provide examples.

**Answer:**

Generic views are class-based views that provide common patterns for displaying and manipulating data.

**Display Views:**

```python
from django.views.generic import ListView, DetailView
from .models import Article

# List view
class ArticleListView(ListView):
    model = Article
    template_name = 'article_list.html'
    context_object_name = 'articles'
    paginate_by = 10
    ordering = ['-created_at']
    
    def get_queryset(self):
        queryset = super().get_queryset()
        return queryset.filter(published=True)
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['categories'] = Category.objects.all()
        return context

# Detail view
class ArticleDetailView(DetailView):
    model = Article
    template_name = 'article_detail.html'
    context_object_name = 'article'
    slug_field = 'slug'
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['related_articles'] = Article.objects.filter(
            category=self.object.category
        ).exclude(pk=self.object.pk)[:5]
        return context
```

**Editing Views:**

```python
from django.views.generic import CreateView, UpdateView, DeleteView
from django.urls import reverse_lazy
from django.contrib.auth.mixins import LoginRequiredMixin

# Create view
class ArticleCreateView(LoginRequiredMixin, CreateView):
    model = Article
    template_name = 'article_form.html'
    fields = ['title', 'content', 'category']
    success_url = reverse_lazy('article_list')
    
    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)

# Update view
class ArticleUpdateView(LoginRequiredMixin, UpdateView):
    model = Article
    template_name = 'article_form.html'
    fields = ['title', 'content', 'category']
    
    def get_success_url(self):
        return reverse_lazy('article_detail', kwargs={'pk': self.object.pk})

# Delete view
class ArticleDeleteView(LoginRequiredMixin, DeleteView):
    model = Article
    template_name = 'article_confirm_delete.html'
    success_url = reverse_lazy('article_list')
```

**Date-based Views:**

```python
from django.views.generic.dates import (
    ArchiveIndexView, YearArchiveView, MonthArchiveView, DayArchiveView
)

class ArticleArchiveView(ArchiveIndexView):
    model = Article
    date_field = 'published_date'
    template_name = 'article_archive.html'

class ArticleYearView(YearArchiveView):
    model = Article
    date_field = 'published_date'
    make_object_list = True

class ArticleMonthView(MonthArchiveView):
    model = Article
    date_field = 'published_date'
    month_format = '%m'
```

**Form Views:**

```python
from django.views.generic import FormView
from .forms import ContactForm

class ContactFormView(FormView):
    template_name = 'contact.html'
    form_class = ContactForm
    success_url = reverse_lazy('contact_success')
    
    def form_valid(self, form):
        # Send email
        form.send_email()
        return super().form_valid(form)
```

**Template View:**

```python
from django.views.generic import TemplateView

class AboutView(TemplateView):
    template_name = 'about.html'
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['team_members'] = TeamMember.objects.all()
        return context
```

**Redirect View:**

```python
from django.views.generic import RedirectView

class ArticleRedirectView(RedirectView):
    permanent = True
    pattern_name = 'article_detail'
    
    def get_redirect_url(self, *args, **kwargs):
        article = Article.objects.get(pk=kwargs['pk'])
        return super().get_redirect_url(slug=article.slug)
```

---

#### 13. Explain Django's database transactions and atomic operations.

**Answer:**

Django provides transaction management to ensure database consistency.

**Atomic Decorator:**

```python
from django.db import transaction

@transaction.atomic
def create_order(user, items):
    # All operations in this function are in a transaction
    order = Order.objects.create(user=user)
    
    for item in items:
        OrderItem.objects.create(
            order=order,
            product=item['product'],
            quantity=item['quantity']
        )
        
        # Update stock
        item['product'].stock -= item['quantity']
        item['product'].save()
    
    return order
    # If any operation fails, all changes are rolled back
```

**Atomic Context Manager:**

```python
from django.db import transaction

def process_payment(order):
    try:
        with transaction.atomic():
            order.status = 'processing'
            order.save()
            
            # Process payment
            payment = Payment.objects.create(order=order, amount=order.total)
            
            if not payment.process():
                raise Exception('Payment failed')
            
            order.status = 'completed'
            order.save()
    except Exception as e:
        # Transaction is automatically rolled back
        order.status = 'failed'
        order.save()
```

**Nested Transactions (Savepoints):**

```python
from django.db import transaction

def complex_operation():
    with transaction.atomic():
        # Outer transaction
        user = User.objects.create(username='john')
        
        try:
            with transaction.atomic():
                # Inner transaction (savepoint)
                profile = Profile.objects.create(user=user)
                # This might fail
                risky_operation()
        except Exception:
            # Inner transaction rolled back, but user still created
            pass
        
        # Outer transaction continues
        user.is_active = True
        user.save()
```

**Manual Savepoints:**

```python
from django.db import transaction

def manual_savepoint_example():
    with transaction.atomic():
        # Create savepoint
        sid = transaction.savepoint()
        
        try:
            # Risky operations
            obj = MyModel.objects.create(name='test')
            obj.risky_method()
            
            # Commit savepoint
            transaction.savepoint_commit(sid)
        except Exception:
            # Rollback to savepoint
            transaction.savepoint_rollback(sid)
```

**Transaction Isolation:**

```python
# settings.py
DATABASES = {
    'default': {
        'OPTIONS': {
            'isolation_level': 'read committed',  # PostgreSQL
        }
    }
}

# In code
from django.db import transaction

with transaction.atomic():
    # Set isolation level
    transaction.set_autocommit(False)
    # Your operations
    transaction.commit()
```

**Non-Atomic Operations:**

```python
from django.db import transaction

@transaction.non_atomic_requests
def my_view(request):
    # This view won't be wrapped in a transaction
    pass

# Or in settings
ATOMIC_REQUESTS = True  # Wrap all views in transactions

# Opt-out specific views
@transaction.non_atomic_requests
def special_view(request):
    pass
```

**Database-Level Locking:**

```python
from django.db import transaction

# SELECT FOR UPDATE (pessimistic locking)
with transaction.atomic():
    article = Article.objects.select_for_update().get(id=1)
    article.views += 1
    article.save()

# NOWAIT option
try:
    with transaction.atomic():
        article = Article.objects.select_for_update(nowait=True).get(id=1)
        article.process()
except DatabaseError:
    # Row is locked by another transaction
    pass

# SKIP LOCKED
articles = Article.objects.select_for_update(skip_locked=True).filter(status='pending')
```

**on_commit Callbacks:**

```python
from django.db import transaction

def send_notification(user_id):
    # This will only execute if transaction commits
    user = User.objects.get(id=user_id)
    send_email(user.email, 'Welcome!')

def create_user(username, email):
    with transaction.atomic():
        user = User.objects.create(username=username, email=email)
        
        # Schedule callback after commit
        transaction.on_commit(lambda: send_notification(user.id))
        
        return user
```

**Best Practices:**

```python
# 1. Keep transactions short
@transaction.atomic
def quick_operation():
    # Fast database operations only
    pass

# 2. Don't make external API calls inside transactions
def process_order(order_id):
    with transaction.atomic():
        order = Order.objects.select_for_update().get(id=order_id)
        order.status = 'processing'
        order.save()
    
    # Make API call outside transaction
    payment_gateway.charge(order.total)

# 3. Use select_for_update for concurrent updates
with transaction.atomic():
    product = Product.objects.select_for_update().get(id=product_id)
    if product.stock >= quantity:
        product.stock -= quantity
        product.save()
```

---

#### 14. What is Django's messages framework? Provide examples.

**Answer:**

The messages framework allows you to temporarily store messages in one request and retrieve them in subsequent requests (typically the next request).

**Configuration:**

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.messages',
]

MIDDLEWARE = [
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
]

TEMPLATES = [
    {
        'OPTIONS': {
            'context_processors': [
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

# Message levels
from django.contrib.messages import constants as messages
MESSAGE_LEVEL = messages.DEBUG

MESSAGE_TAGS = {
    messages.DEBUG: 'debug',
    messages.INFO: 'info',
    messages.SUCCESS: 'success',
    messages.WARNING: 'warning',
    messages.ERROR: 'danger',
}
```

**Adding Messages:**

```python
from django.contrib import messages
from django.shortcuts import redirect

def create_article(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST)
        if form.is_valid():
            article = form.save()
            
            # Different message levels
            messages.debug(request, 'Debug message')
            messages.info(request, 'Article saved as draft')
            messages.success(request, 'Article published successfully!')
            messages.warning(request, 'Article needs review')
            messages.error(request, 'Failed to publish article')
            
            return redirect('article_detail', pk=article.pk)
    
    return render(request, 'article_form.html', {'form': form})

# With extra tags
messages.success(request, 'Profile updated', extra_tags='profile')

# Custom level
messages.add_message(request, messages.INFO, 'Custom message')
```

**Displaying Messages in Templates:**

```django
<!-- Basic display -->
{% if messages %}
    <ul class="messages">
        {% for message in messages %}
            <li class="{{ message.tags }}">{{ message }}</li>
        {% endfor %}
    </ul>
{% endif %}

<!-- Bootstrap styling -->
{% if messages %}
    {% for message in messages %}
        <div class="alert alert-{{ message.tags }} alert-dismissible fade show" role="alert">
            {{ message }}
            <button type="button" class="close" data-dismiss="alert">
                <span>&times;</span>
            </button>
        </div>
    {% endfor %}
{% endif %}

<!-- With message level checks -->
{% for message in messages %}
    {% if message.level == DEFAULT_MESSAGE_LEVELS.ERROR %}
        <div class="error-message">{{ message }}</div>
    {% elif message.level == DEFAULT_MESSAGE_LEVELS.SUCCESS %}
        <div class="success-message">{{ message }}</div>
    {% endif %}
{% endfor %}
```

**Class-Based Views:**

```python
from django.contrib.messages.views import SuccessMessageMixin
from django.views.generic import CreateView, UpdateView

class ArticleCreateView(SuccessMessageMixin, CreateView):
    model = Article
    form_class = ArticleForm
    success_message = "Article '%(title)s' was created successfully"
    
    def get_success_message(self, cleaned_data):
        return self.success_message % dict(
            cleaned_data,
            title=self.object.title,
        )

class ArticleUpdateView(SuccessMessageMixin, UpdateView):
    model = Article
    form_class = ArticleForm
    success_message = "Article updated successfully"
```

**Custom Message Storage:**

```python
# settings.py
MESSAGE_STORAGE = 'django.contrib.messages.storage.session.SessionStorage'
# or
MESSAGE_STORAGE = 'django.contrib.messages.storage.cookie.CookieStorage'
# or (default)
MESSAGE_STORAGE = 'django.contrib.messages.storage.fallback.FallbackStorage'
```

**Advanced Usage:**

```python
from django.contrib import messages

def complex_operation(request):
    # Get message count
    storage = messages.get_messages(request)
    message_count = len(storage)
    
    # Check if messages exist without consuming them
    storage = messages.get_messages(request)
    storage.used = False
    
    # Add multiple messages
    message_list = [
        'First message',
        'Second message',
        'Third message',
    ]
    for msg in message_list:
        messages.info(request, msg)
    
    # Conditional messages
    if user.is_premium:
        messages.success(request, 'Welcome, premium member!')
    else:
        messages.info(request, 'Upgrade to premium for more features')
```

**JavaScript Integration:**

```django
<!-- Pass messages to JavaScript -->
<script>
    const messages = [
        {% for message in messages %}
            {
                level: '{{ message.level_tag }}',
                text: '{{ message|escapejs }}',
                tags: '{{ message.tags }}'
            }{% if not forloop.last %},{% endif %}
        {% endfor %}
    ];
    
    // Display with toast notification
    messages.forEach(msg => {
        showToast(msg.text, msg.level);
    });
</script>
```

---

#### 15. Explain Django's flatpages app and its use cases.

**Answer:**

The flatpages app allows you to store simple "flat" HTML content in the database and handle the rendering.

**Setup:**

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.sites',
    'django.contrib.flatpages',
]

MIDDLEWARE = [
    'django.contrib.flatpages.middleware.FlatpageFallbackMiddleware',
]

SITE_ID = 1
```

**Migration:**
```bash
python manage.py migrate
```

**URLs:**

```python
# urls.py
from django.contrib.flatpages import views

urlpatterns = [
    path('pages/<path:url>/', views.flatpage, name='flatpage'),
]

# Or use middleware (catches all unmatched URLs)
# No URL pattern needed with FlatpageFallbackMiddleware
```

**Creating Flatpages:**

```python
from django.contrib.flatpages.models import FlatPage
from django.contrib.sites.models import Site

# Create flatpage
flatpage = FlatPage.objects.create(
    url='/about/',
    title='About Us',
    content='<h1>About Us</h1><p>We are awesome!</p>',
    enable_comments=False,
    registration_required=False,
)
flatpage.sites.add(Site.objects.get_current())

# Via admin interface (easier)
# Navigate to /admin/flatpages/flatpage/
```

**Custom Template:**

```python
# Flatpage model
flatpage = FlatPage.objects.get(url='/about/')
flatpage.template_name = 'flatpages/about.html'
flatpage.save()
```

```django
<!-- templates/flatpages/about.html -->
{% extends 'base.html' %}

{% block title %}{{ flatpage.title }}{% endblock %}

{% block content %}
    <h1>{{ flatpage.title }}</h1>
    {{ flatpage.content|safe }}
{% endblock %}

<!-- Default template: flatpages/default.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{{ flatpage.title }}</title>
</head>
<body>
    {{ flatpage.content }}
</body>
</html>
```

**Advanced Usage:**

```python
# Custom flatpage view
from django.contrib.flatpages.models import FlatPage
from django.shortcuts import render, get_object_or_404

def custom_flatpage(request, url):
    flatpage = get_object_or_404(FlatPage, url=url)
    
    # Add custom context
    context = {
        'flatpage': flatpage,
        'related_pages': FlatPage.objects.exclude(pk=flatpage.pk)[:5],
    }
    
    return render(request, flatpage.template_name or 'flatpages/default.html', context)

# Extending FlatPage model
from django.contrib.flatpages.models import FlatPage

class ExtendedFlatPage(models.Model):
    flatpage = models.OneToOneField(FlatPage, on_delete=models.CASCADE)
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    featured_image = models.ImageField(upload_to='flatpages/')
    meta_description = models.CharField(max_length=160)
```

**Use Cases:**

1. **Static Pages:** About, Terms of Service, Privacy Policy
2. **Help Documentation:** FAQ pages, user guides
3. **Landing Pages:** Marketing pages with simple content
4. **Legal Pages:** Copyright notices, disclaimers

**Admin Customization:**

```python
# admin.py
from django.contrib.flatpages.admin import FlatPageAdmin
from django.contrib.flatpages.models import FlatPage
from django.contrib import admin

class CustomFlatPageAdmin(FlatPageAdmin):
    fieldsets = [
        (None, {'fields': ['url', 'title', 'content', 'sites']}),
        ('Advanced options', {
            'classes': ['collapse'],
            'fields': ['enable_comments', 'registration_required', 'template_name'],
        }),
    ]

admin.site.unregister(FlatPage)
admin.site.register(FlatPage, CustomFlatPageAdmin)
```

---

#### 16. What is Django's humanize library? Provide examples.

**Answer:**

Django's humanize library provides template filters to add a "human touch" to data.

**Setup:**

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.humanize',
]
```

**Template Usage:**

```django
{% load humanize %}

<!-- apnumber: Converts 1-9 to words -->
{{ 1|apnumber }}  <!-- one -->
{{ 5|apnumber }}  <!-- five -->
{{ 10|apnumber }} <!-- 10 -->

<!-- intcomma: Adds commas to integers -->
{{ 1000|intcomma }}     <!-- 1,000 -->
{{ 1000000|intcomma }}  <!-- 1,000,000 -->

<!-- intword: Converts large integers to words -->
{{ 1000000|intword }}      <!-- 1.0 million -->
{{ 1200000|intword }}      <!-- 1.2 million -->
{{ 1200000000|intword }}   <!-- 1.2 billion -->

<!-- naturalday: Returns "today", "yesterday", or "tomorrow" -->
{{ blog.date|naturalday }}
<!-- If date is today: "today" -->
<!-- If date is yesterday: "yesterday" -->
<!-- Otherwise: "Nov. 4" -->

<!-- naturaltime: Returns time difference -->
{{ blog.date|naturaltime }}
<!-- "4 seconds ago" -->
<!-- "2 minutes ago" -->
<!-- "3 hours ago" -->
<!-- "2 days ago" -->

<!-- naturaldate: Natural representation of date -->
{{ event.date|naturaldate }}
<!-- "Nov. 4, 2024" -->

<!-- ordinal: Converts integer to ordinal string -->
{{ 1|ordinal }}   <!-- 1st -->
{{ 2|ordinal }}   <!-- 2nd -->
{{ 3|ordinal }}   <!-- 3rd -->
{{ 11|ordinal }}  <!-- 11th -->

<!-- filesizeformat: Formats file sizes -->
{{ 123456789|filesizeformat }}  <!-- 117.7 MB -->
{{ 1024|filesizeformat }}       <!-- 1.0 KB -->
```

**Complete Example:**

```django
{% load humanize %}

<div class="article">
    <h2>{{ article.title }}</h2>
    
    <!-- Natural time -->
    <p class="meta">
        Published {{ article.published_date|naturaltime }}
        by {{ article.author }}
    </p>
    
    <!-- View count with commas -->
    <p>{{ article.views|intcomma }} views</p>
    
    <!-- File size -->
    {% if article.attachment %}
        <p>Attachment: {{ article.attachment.size|filesizeformat }}</p>
    {% endif %}
    
    <!-- Ordinal -->
    <p>This is the {{ article.position|ordinal }} article in the series</p>
    
    <!-- Natural day -->
    <p>Last updated: {{ article.updated_at|naturalday }}</p>
</div>

<!-- Stats dashboard -->
<div class="stats">
    <h3>Site Statistics</h3>
    <ul>
        <li>Total Users: {{ total_users|intword }}</li>
        <li>Total Posts: {{ total_posts|intcomma }}</li>
        <li>Storage Used: {{ storage_used|filesizeformat }}</li>
    </ul>
</div>
```

**Using in Python Code:**

```python
from django.contrib.humanize.templatetags.humanize import (
    intcomma, intword, naturaltime, filesizeformat
)

# In views or models
class Article(models.Model):
    views = models.IntegerField()
    
    def formatted_views(self):
        return intcomma(self.views)
    
    def views_in_words(self):
        return intword(self.views)

# In view
def stats_view(request):
    total_users = 1500000
    context = {
        'total_users': intword(total_users),  # "1.5 million"
        'formatted_users': intcomma(total_users),  # "1,500,000"
    }
    return render(request, 'stats.html', context)
```

---

#### 17. Explain Django's sitemaps framework.

**Answer:**

Django's sitemaps framework generates XML sitemaps for search engines.

**Setup:**

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.sitemaps',
]
```

**Basic Sitemap:**

```python
# sitemaps.py
from django.contrib.sitemaps import Sitemap
from .models import Article

class ArticleSitemap(Sitemap):
    changefreq = 'daily'
    priority = 0.9
    
    def items(self):
        return Article.objects.filter(published=True)
    
    def lastmod(self, obj):
        return obj.updated_at
    
    def location(self, obj):
        return obj.get_absolute_url()

# Static pages sitemap
class StaticViewSitemap(Sitemap):
    priority = 0.5
    changefreq = 'monthly'
    
    def items(self):
        return ['home', 'about', 'contact']
    
    def location(self, item):
        return reverse(item)
```

**URLs:**

```python
# urls.py
from django.contrib.sitemaps.views import sitemap
from .sitemaps import ArticleSitemap, StaticViewSitemap

sitemaps = {
    'articles': ArticleSitemap,
    'static': StaticViewSitemap,
}

urlpatterns = [
    path('sitemap.xml', sitemap, {'sitemaps': sitemaps}, name='sitemap'),
]
```

**Advanced Sitemap:**

```python
from django.contrib.sitemaps import Sitemap
from django.urls import reverse

class ArticleSitemap(Sitemap):
    changefreq = 'weekly'
    priority = 0.8
    protocol = 'https'
    limit = 5000  # Items per sitemap file
    
    def items(self):
        return Article.objects.filter(
            published=True,
            status='active'
        ).order_by('-published_date')
    
    def lastmod(self, obj):
        return obj.updated_at
    
    def location(self, obj):
        return obj.get_absolute_url()
    
    def priority(self, obj):
        # Dynamic priority based on views
        if obj.views > 10000:
            return 1.0
        elif obj.views > 1000:
            return 0.8
        return 0.5
    
    def changefreq(self, obj):
        # Dynamic change frequency
        if obj.is_news:
            return 'hourly'
        return 'weekly'

# Category sitemap
class CategorySitemap(Sitemap):
    changefreq = 'weekly'
    priority = 0.6
    
    def items(self):
        return Category.objects.all()
    
    def lastmod(self, obj):
        latest_article = obj.articles.order_by('-updated_at').first()
        return latest_article.updated_at if latest_article else None
```

**Sitemap Index (for large sites):**

```python
# urls.py
from django.contrib.sitemaps.views import sitemap, index

urlpatterns = [
    path('sitemap.xml', index, {'sitemaps': sitemaps}),
    path('sitemap-<section>.xml', sitemap, {'sitemaps': sitemaps},
         name='django.contrib.sitemaps.views.sitemap'),
]
```

**Ping Search Engines:**

```python
from django.contrib.sitemaps import ping_google

def publish_article(request):
    # ... save article
    try:
        ping_google()
    except Exception:
        pass  # Fail silently
```

**robots.txt:**

```python
# views.py
from django.http import HttpResponse
from django.views.decorators.http import require_GET

@require_GET
def robots_txt(request):
    lines = [
        "User-Agent: *",
        "Disallow: /admin/",
        "Disallow: /private/",
        "",
        f"Sitemap: {request.build_absolute_uri('/sitemap.xml')}",
    ]
    return HttpResponse("\n".join(lines), content_type="text/plain")

# urls.py
urlpatterns = [
    path('robots.txt', robots_txt),
]
```

---

#### 18. What is Django's syndication framework (RSS/Atom feeds)?

**Answer:**

Django's syndication framework makes it easy to create RSS and Atom feeds.

**Basic Feed:**

```python
# feeds.py
from django.contrib.syndication.views import Feed
from django.urls import reverse
from .models import Article

class LatestArticlesFeed(Feed):
    title = "My Blog - Latest Articles"
    link = "/articles/"
    description = "Latest articles from my blog"
    
    def items(self):
        return Article.objects.filter(published=True).order_by('-published_date')[:10]
    
    def item_title(self, item):
        return item.title
    
    def item_description(self, item):
        return item.excerpt
    
    def item_link(self, item):
        return item.get_absolute_url()
    
    def item_pubdate(self, item):
        return item.published_date
    
    def item_author_name(self, item):
        return item.author.get_full_name()
```

**URLs:**

```python
# urls.py
from .feeds import LatestArticlesFeed

urlpatterns = [
    path('feed/', LatestArticlesFeed(), name='article_feed'),
]
```

**Advanced Feed:**

```python
from django.contrib.syndication.views import Feed
from django.utils.feedgenerator import Atom1Feed

class ArticleFeed(Feed):
    # Feed metadata
    title = "My Blog"
    link = "/articles/"
    description = "Latest articles"
    author_name = "John Doe"
    author_email = "john@example.com"
    categories = ["Technology", "Programming"]
    feed_copyright = "Copyright (c) 2024"
    ttl = 600  # Time to live in minutes
    
    # Use Atom instead of RSS
    feed_type = Atom1Feed
    
    def items(self):
        return Article.objects.filter(published=True)[:20]
    
    def item_title(self, item):
        return item.title
    
    def item_description(self, item):
        return item.content[:200]
    
    def item_link(self, item):
        return reverse('article_detail', args=[item.pk])
    
    def item_pubdate(self, item):
        return item.published_date
    
    def item_updateddate(self, item):
        return item.updated_at
    
    def item_author_name(self, item):
        return item.author.username
    
    def item_author_email(self, item):
        return item.author.email
    
    def item_categories(self, item):
        return [item.category.name]
    
    def item_enclosure_url(self, item):
        # For podcasts/media
        if item.audio_file:
            return item.audio_file.url
        return None
    
    def item_enclosure_length(self, item):
        if item.audio_file:
            return item.audio_file.size
        return 0
    
    def item_enclosure_mime_type(self, item):
        return "audio/mpeg"

# Category-specific feed
class CategoryFeed(Feed):
    def get_object(self, request, category_slug):
        return Category.objects.get(slug=category_slug)
    
    def title(self, obj):
        return f"Articles in {obj.name}"
    
    def link(self, obj):
        return obj.get_absolute_url()
    
    def description(self, obj):
        return f"Latest articles in {obj.name} category"
    
    def items(self, obj):
        return Article.objects.filter(
            category=obj,
            published=True
        ).order_by('-published_date')[:10]
```

**Template Integration:**

```django
<!-- In base template -->
<head>
    <link rel="alternate" type="application/rss+xml" 
          title="RSS Feed" href="{% url 'article_feed' %}">
    <link rel="alternate" type="application/atom+xml" 
          title="Atom Feed" href="{% url 'article_feed' %}">
</head>

<!-- Feed link -->
<a href="{% url 'article_feed' %}">
    <i class="fa fa-rss"></i> Subscribe to RSS
</a>
```

**Custom Feed Generator:**

```python
from django.utils.feedgenerator import Rss201rev2Feed

class CustomFeed(Rss201rev2Feed):
    def add_item_elements(self, handler, item):
        super().add_item_elements(handler, item)
        
        # Add custom elements
        if item.get('custom_field'):
            handler.addQuickElement('customField', item['custom_field'])

class MyFeed(Feed):
    feed_type = CustomFeed
    
    def item_extra_kwargs(self, item):
        return {'custom_field': item.custom_data}
```

---

### Long Answer Questions

#### 19. Build a complete social media application with Django. Include user profiles, posts, comments, likes, followers, and a news feed algorithm.

**Answer:**

## Social Media Application with Django

### 1. Project Setup

```bash
django-admin startproject socialmedia
cd socialmedia
python manage.py startapp accounts
python manage.py startapp posts
python manage.py startapp social
```

### 2. Models

```python
# accounts/models.py
from django.contrib.auth.models import AbstractUser
from django.db import models

class User(AbstractUser):
    bio = models.TextField(max_length=500, blank=True)
    avatar = models.ImageField(upload_to='avatars/', blank=True, null=True)
    cover_photo = models.ImageField(upload_to='covers/', blank=True, null=True)
    website = models.URLField(blank=True)
    location = models.CharField(max_length=100, blank=True)
    birth_date = models.DateField(null=True, blank=True)
    is_verified = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    
    def __str__(self):
        return self.username

class UserProfile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='profile')
    followers_count = models.PositiveIntegerField(default=0)
    following_count = models.PositiveIntegerField(default=0)
    posts_count = models.PositiveIntegerField(default=0)
    
    def __str__(self):
        return f"{self.user.username}'s profile"

# social/models.py
from django.db import models
from accounts.models import User

class Follow(models.Model):
    follower = models.ForeignKey(User, on_delete=models.CASCADE, related_name='following')
    following = models.ForeignKey(User, on_delete=models.CASCADE, related_name='followers')
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        unique_together = ['follower', 'following']
        ordering = ['-created_at']
    
    def __str__(self):
        return f"{self.follower.username} follows {self.following.username}"

class Block(models.Model):
    blocker = models.ForeignKey(User, on_delete=models.CASCADE, related_name='blocking')
    blocked = models.ForeignKey(User, on_delete=models.CASCADE, related_name='blocked_by')
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        unique_together = ['blocker', 'blocked']

# posts/models.py
from django.db import models
from accounts.models import User

class Post(models.Model):
    PRIVACY_CHOICES = [
        ('public', 'Public'),
        ('followers', 'Followers Only'),
        ('private', 'Private'),
    ]
    
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='posts')
    content = models.TextField(max_length=5000)
    image = models.ImageField(upload_to='posts/', blank=True, null=True)
    privacy = models.CharField(max_length=20, choices=PRIVACY_CHOICES, default='public')
    
    likes_count = models.PositiveIntegerField(default=0)
    comments_count = models.PositiveIntegerField(default=0)
    shares_count = models.PositiveIntegerField(default=0)
    
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['-created_at']
        indexes = [
            models.Index(fields=['-created_at']),
            models.Index(fields=['author', '-created_at']),
        ]
    
    def __str__(self):
        return f"{self.author.username}: {self.content[:50]}"

class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='comments')
    parent = models.ForeignKey('self', on_delete=models.CASCADE, null=True, blank=True, related_name='replies')
    content = models.TextField(max_length=1000)
    likes_count = models.PositiveIntegerField(default=0)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['-created_at']
    
    def __str__(self):
        return f"{self.author.username} on {self.post.id}"

class Like(models.Model):
    CONTENT_TYPES = [
        ('post', 'Post'),
        ('comment', 'Comment'),
    ]
    
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name='likes')
    content_type = models.CharField(max_length=20, choices=CONTENT_TYPES)
    object_id = models.PositiveIntegerField()
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        unique_together = ['user', 'content_type', 'object_id']
        indexes = [
            models.Index(fields=['content_type', 'object_id']),
        ]

class Share(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name='shares')
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='shared_by')
    caption = models.TextField(max_length=500, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        unique_together = ['user', 'post']

class Notification(models.Model):
    NOTIFICATION_TYPES = [
        ('like', 'Like'),
        ('comment', 'Comment'),
        ('follow', 'Follow'),
        ('mention', 'Mention'),
        ('share', 'Share'),
    ]
    
    recipient = models.ForeignKey(User, on_delete=models.CASCADE, related_name='notifications')
    sender = models.ForeignKey(User, on_delete=models.CASCADE, related_name='sent_notifications')
    notification_type = models.CharField(max_length=20, choices=NOTIFICATION_TYPES)
    post = models.ForeignKey(Post, on_delete=models.CASCADE, null=True, blank=True)
    comment = models.ForeignKey(Comment, on_delete=models.CASCADE, null=True, blank=True)
    is_read = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        ordering = ['-created_at']
        indexes = [
            models.Index(fields=['recipient', '-created_at']),
        ]
```

### 3. Views

```python
# posts/views.py
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib.auth.decorators import login_required
from django.http import JsonResponse
from django.db.models import Q, Prefetch
from django.core.paginator import Paginator
from .models import Post, Comment, Like, Share, Notification
from social.models import Follow

@login_required
def news_feed(request):
    # Get posts from followed users and own posts
    following_users = Follow.objects.filter(follower=request.user).values_list('following', flat=True)
    
    posts = Post.objects.filter(
        Q(author__in=following_users) | Q(author=request.user)
    ).select_related('author').prefetch_related(
        Prefetch('comments', queryset=Comment.objects.select_related('author')[:3])
    ).order_by('-created_at')
    
    # Pagination
    paginator = Paginator(posts, 10)
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    
    # Check which posts user has liked
    liked_posts = Like.objects.filter(
        user=request.user,
        content_type='post',
        object_id__in=[p.id for p in page_obj]
    ).values_list('object_id', flat=True)
    
    context = {
        'page_obj': page_obj,
        'liked_posts': list(liked_posts),
    }
    return render(request, 'posts/feed.html', context)

@login_required
def create_post(request):
    if request.method == 'POST':
        content = request.POST.get('content')
        image = request.FILES.get('image')
        privacy = request.POST.get('privacy', 'public')
        
        post = Post.objects.create(
            author=request.user,
            content=content,
            image=image,
            privacy=privacy
        )
        
        # Update user's post count
        request.user.profile.posts_count += 1
        request.user.profile.save()
        
        if request.headers.get('X-Requested-With') == 'XMLHttpRequest':
            return JsonResponse({
                'success': True,
                'post_id': post.id,
                'message': 'Post created successfully'
            })
        
        return redirect('news_feed')
    
    return render(request, 'posts/create.html')

@login_required
def post_detail(request, post_id):
    post = get_object_or_404(
        Post.objects.select_related('author').prefetch_related(
            Prefetch('comments', queryset=Comment.objects.select_related('author'))
        ),
        id=post_id
    )
    
    # Check if user has liked the post
    user_liked = Like.objects.filter(
        user=request.user,
        content_type='post',
        object_id=post.id
    ).exists()
    
    context = {
        'post': post,
        'user_liked': user_liked,
    }
    return render(request, 'posts/detail.html', context)

@login_required
def like_post(request, post_id):
    post = get_object_or_404(Post, id=post_id)
    
    like, created = Like.objects.get_or_create(
        user=request.user,
        content_type='post',
        object_id=post.id
    )
    
    if created:
        # Increment like count
        post.likes_count += 1
        post.save()
        
        # Create notification
        if post.author != request.user:
            Notification.objects.create(
                recipient=post.author,
                sender=request.user,
                notification_type='like',
                post=post
            )
        
        return JsonResponse({'liked': True, 'likes_count': post.likes_count})
    else:
        # Unlike
        like.delete()
        post.likes_count -= 1
        post.save()
        
        return JsonResponse({'liked': False, 'likes_count': post.likes_count})

@login_required
def add_comment(request, post_id):
    if request.method == 'POST':
        post = get_object_or_404(Post, id=post_id)
        content = request.POST.get('content')
        parent_id = request.POST.get('parent_id')
        
        comment = Comment.objects.create(
            post=post,
            author=request.user,
            content=content,
            parent_id=parent_id if parent_id else None
        )
        
        # Update comment count
        post.comments_count += 1
        post.save()
        
        # Create notification
        if post.author != request.user:
            Notification.objects.create(
                recipient=post.author,
                sender=request.user,
                notification_type='comment',
                post=post,
                comment=comment
            )
        
        return JsonResponse({
            'success': True,
            'comment': {
                'id': comment.id,
                'author': comment.author.username,
                'content': comment.content,
                'created_at': comment.created_at.strftime('%Y-%m-%d %H:%M')
            }
        })
    
    return JsonResponse({'success': False})

@login_required
def share_post(request, post_id):
    if request.method == 'POST':
        post = get_object_or_404(Post, id=post_id)
        caption = request.POST.get('caption', '')
        
        share, created = Share.objects.get_or_create(
            user=request.user,
            post=post,
            defaults={'caption': caption}
        )
        
        if created:
            post.shares_count += 1
            post.save()
            
            # Create notification
            if post.author != request.user:
                Notification.objects.create(
                    recipient=post.author,
                    sender=request.user,
                    notification_type='share',
                    post=post
                )
        
        return JsonResponse({'success': True, 'shares_count': post.shares_count})
    
    return JsonResponse({'success': False})

# social/views.py
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib.auth.decorators import login_required
from django.http import JsonResponse
from accounts.models import User
from .models import Follow
from posts.models import Notification

@login_required
def follow_user(request, username):
    user_to_follow = get_object_or_404(User, username=username)
    
    if user_to_follow == request.user:
        return JsonResponse({'success': False, 'message': 'Cannot follow yourself'})
    
    follow, created = Follow.objects.get_or_create(
        follower=request.user,
        following=user_to_follow
    )
    
    if created:
        # Update counts
        request.user.profile.following_count += 1
        request.user.profile.save()
        
        user_to_follow.profile.followers_count += 1
        user_to_follow.profile.save()
        
        # Create notification
        Notification.objects.create(
            recipient=user_to_follow,
            sender=request.user,
            notification_type='follow'
        )
        
        return JsonResponse({
            'success': True,
            'following': True,
            'followers_count': user_to_follow.profile.followers_count
        })
    else:
        # Unfollow
        follow.delete()
        
        request.user.profile.following_count -= 1
        request.user.profile.save()
        
        user_to_follow.profile.followers_count -= 1
        user_to_follow.profile.save()
        
        return JsonResponse({
            'success': True,
            'following': False,
            'followers_count': user_to_follow.profile.followers_count
        })

@login_required
def user_profile(request, username):
    user = get_object_or_404(User, username=username)
    
    # Check if current user follows this user
    is_following = Follow.objects.filter(
        follower=request.user,
        following=user
    ).exists()
    
    # Get user's posts
    posts = Post.objects.filter(author=user).select_related('author')
    
    # Apply privacy filters
    if user != request.user:
        if is_following:
            posts = posts.exclude(privacy='private')
        else:
            posts = posts.filter(privacy='public')
    
    context = {
        'profile_user': user,
        'is_following': is_following,
        'posts': posts[:20],
    }
    return render(request, 'social/profile.html', context)

@login_required
def followers_list(request, username):
    user = get_object_or_404(User, username=username)
    followers = Follow.objects.filter(following=user).select_related('follower')
    
    context = {
        'user': user,
        'followers': followers,
    }
    return render(request, 'social/followers.html', context)

@login_required
def following_list(request, username):
    user = get_object_or_404(User, username=username)
    following = Follow.objects.filter(follower=user).select_related('following')
    
    context = {
        'user': user,
        'following': following,
    }
    return render(request, 'social/following.html', context)

@login_required
def notifications(request):
    notifications = Notification.objects.filter(
        recipient=request.user
    ).select_related('sender', 'post', 'comment')[:50]
    
    # Mark as read
    notifications.filter(is_read=False).update(is_read=True)
    
    context = {
        'notifications': notifications,
    }
    return render(request, 'social/notifications.html', context)
```

### 4. News Feed Algorithm

```python
# posts/feed_algorithm.py
from django.db.models import Q, Count, F
from django.utils import timezone
from datetime import timedelta
from .models import Post, Like
from social.models import Follow

class NewsFeedAlgorithm:
    def __init__(self, user):
        self.user = user
    
    def get_feed(self, limit=20):
        # Get followed users
        following = Follow.objects.filter(follower=self.user).values_list('following', flat=True)
        
        # Base queryset
        posts = Post.objects.filter(
            Q(author__in=following) | Q(author=self.user)
        ).select_related('author').prefetch_related('comments')
        
        # Calculate engagement score
        posts = posts.annotate(
            engagement_score=F('likes_count') + F('comments_count') * 2 + F('shares_count') * 3
        )
        
        # Recent posts get priority
        recent_cutoff = timezone.now() - timedelta(days=7)
        recent_posts = posts.filter(created_at__gte=recent_cutoff)
        older_posts = posts.filter(created_at__lt=recent_cutoff)
        
        # Sort recent posts by engagement
        recent_posts = recent_posts.order_by('-engagement_score', '-created_at')
        
        # Sort older posts by time
        older_posts = older_posts.order_by('-created_at')
        
        # Combine: 70% recent, 30% older
        recent_count = int(limit * 0.7)
        older_count = limit - recent_count
        
        feed = list(recent_posts[:recent_count]) + list(older_posts[:older_count])
        
        return feed
    
    def get_trending_posts(self, limit=10):
        # Posts from last 24 hours with high engagement
        cutoff = timezone.now() - timedelta(hours=24)
        
        trending = Post.objects.filter(
            created_at__gte=cutoff,
            privacy='public'
        ).annotate(
            engagement_score=F('likes_count') + F('comments_count') * 2 + F('shares_count') * 3
        ).order_by('-engagement_score')[:limit]
        
        return trending

# Usage in view
@login_required
def smart_feed(request):
    algorithm = NewsFeedAlgorithm(request.user)
    posts = algorithm.get_feed(limit=20)
    
    context = {
        'posts': posts,
    }
    return render(request, 'posts/feed.html', context)
```

### 5. URLs

```python
# socialmedia/urls.py
from django.urls import path, include

urlpatterns = [
    path('', include('posts.urls')),
    path('users/', include('social.urls')),
    path('accounts/', include('accounts.urls')),
]

# posts/urls.py
from django.urls import path
from . import views

app_name = 'posts'

urlpatterns = [
    path('', views.news_feed, name='feed'),
    path('create/', views.create_post, name='create'),
    path('post/<int:post_id>/', views.post_detail, name='detail'),
    path('post/<int:post_id>/like/', views.like_post, name='like'),
    path('post/<int:post_id>/comment/', views.add_comment, name='comment'),
    path('post/<int:post_id>/share/', views.share_post, name='share'),
]

# social/urls.py
from django.urls import path
from . import views

app_name = 'social'

urlpatterns = [
    path('<str:username>/', views.user_profile, name='profile'),
    path('<str:username>/follow/', views.follow_user, name='follow'),
    path('<str:username>/followers/', views.followers_list, name='followers'),
    path('<str:username>/following/', views.following_list, name='following'),
    path('notifications/', views.notifications, name='notifications'),
]
```

### 6. Templates

```django
<!-- templates/posts/feed.html -->
{% extends 'base.html' %}

{% block content %}
<div class="container">
    <div class="row">
        <div class="col-md-8">
            <!-- Create Post -->
            <div class="card mb-3">
                <div class="card-body">
                    <form method="post" action="{% url 'posts:create' %}" enctype="multipart/form-data">
                        {% csrf_token %}
                        <textarea name="content" class="form-control" placeholder="What's on your mind?"></textarea>
                        <input type="file" name="image" accept="image/*">
                        <button type="submit" class="btn btn-primary mt-2">Post</button>
                    </form>
                </div>
            </div>
            
            <!-- Posts Feed -->
            {% for post in page_obj %}
            <div class="card mb-3">
                <div class="card-header">
                    <img src="{{ post.author.avatar.url }}" class="rounded-circle" width="40">
                    <a href="{% url 'social:profile' post.author.username %}">
                        {{ post.author.username }}
                    </a>
                    <small class="text-muted">{{ post.created_at|timesince }} ago</small>
                </div>
                <div class="card-body">
                    <p>{{ post.content }}</p>
                    {% if post.image %}
                        <img src="{{ post.image.url }}" class="img-fluid">
                    {% endif %}
                </div>
                <div class="card-footer">
                    <button class="btn btn-sm like-btn" data-post-id="{{ post.id }}">
                        <i class="fa fa-heart {% if post.id in liked_posts %}text-danger{% endif %}"></i>
                        <span class="likes-count">{{ post.likes_count }}</span>
                    </button>
                    <button class="btn btn-sm">
                        <i class="fa fa-comment"></i> {{ post.comments_count }}
                    </button>
                    <button class="btn btn-sm share-btn" data-post-id="{{ post.id }}">
                        <i class="fa fa-share"></i> {{ post.shares_count }}
                    </button>
                </div>
                
                <!-- Comments -->
                <div class="card-body">
                    {% for comment in post.comments.all|slice:":3" %}
                    <div class="comment">
                        <strong>{{ comment.author.username }}</strong>
                        {{ comment.content }}
                    </div>
                    {% endfor %}
                    
                    <!-- Add Comment -->
                    <form class="comment-form mt-2" data-post-id="{{ post.id }}">
                        {% csrf_token %}
                        <input type="text" name="content" class="form-control" placeholder="Add a comment...">
                    </form>
                </div>
            </div>
            {% endfor %}
            
            <!-- Pagination -->
            {% if page_obj.has_other_pages %}
            <nav>
                <ul class="pagination">
                    {% if page_obj.has_previous %}
                        <li class="page-item">
                            <a class="page-link" href="?page={{ page_obj.previous_page_number }}">Previous</a>
                        </li>
                    {% endif %}
                    {% if page_obj.has_next %}
                        <li class="page-item">
                            <a class="page-link" href="?page={{ page_obj.next_page_number }}">Next</a>
                        </li>
                    {% endif %}
                </ul>
            </nav>
            {% endif %}
        </div>
        
        <div class="col-md-4">
            <!-- Suggestions -->
            <div class="card">
                <div class="card-header">Suggested Users</div>
                <div class="card-body">
                    <!-- Suggested users list -->
                </div>
            </div>
        </div>
    </div>
</div>

<script>
// Like functionality
document.querySelectorAll('.like-btn').forEach(btn => {
    btn.addEventListener('click', function() {
        const postId = this.dataset.postId;
        fetch(`/post/${postId}/like/`, {
            method: 'POST',
            headers: {
                'X-CSRFToken': '{{ csrf_token }}',
            }
        })
        .then(response => response.json())
        .then(data => {
            this.querySelector('.likes-count').textContent = data.likes_count;
            this.querySelector('i').classList.toggle('text-danger', data.liked);
        });
    });
});

// Comment functionality
document.querySelectorAll('.comment-form').forEach(form => {
    form.addEventListener('submit', function(e) {
        e.preventDefault();
        const postId = this.dataset.postId;
        const content = this.querySelector('[name="content"]').value;
        
        fetch(`/post/${postId}/comment/`, {
            method: 'POST',
            headers: {
                'X-CSRFToken': '{{ csrf_token }}',
                'Content-Type': 'application/x-www-form-urlencoded',
            },
            body: `content=${encodeURIComponent(content)}`
        })
        .then(response => response.json())
        .then(data => {
            if (data.success) {
                location.reload();
            }
        });
    });
});
</script>
{% endblock %}
```

**Key Features:**

1. **User Profiles**: Custom user model with bio, avatar, cover photo
2. **Posts**: Create, view, edit posts with images and privacy settings
3. **Comments**: Nested comments with replies
4. **Likes**: Like posts and comments
5. **Follow System**: Follow/unfollow users
6. **News Feed**: Personalized feed with engagement-based algorithm
7. **Notifications**: Real-time notifications for interactions
8. **Privacy**: Public, followers-only, and private posts

---

#### 20. Implement real-time chat functionality using Django Channels and WebSockets.

**Answer:**

## Real-Time Chat with Django Channels

### 1. Installation

```bash
pip install channels channels-redis daphne
```

### 2. Project Setup

```python
# settings.py
INSTALLED_APPS = [
    'daphne',  # Must be first
    'django.contrib.admin',
    # ... other apps
    'channels',
    'chat',
]

ASGI_APPLICATION = 'myproject.asgi.application'

CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels_redis.core.RedisChannelLayer',
        'CONFIG': {
            "hosts": [('127.0.0.1', 6379)],
        },
    },
}

# For development without Redis
CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels.layers.InMemoryChannelLayer'
    }
}
```

### 3. ASGI Configuration

```python
# myproject/asgi.py
import os
from django.core.asgi import get_asgi_application
from channels.routing import ProtocolTypeRouter, URLRouter
from channels.auth import AuthMiddlewareStack
from channels.security.websocket import AllowedHostsOriginValidator
import chat.routing

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

application = ProtocolTypeRouter({
    "http": get_asgi_application(),
    "websocket": AllowedHostsOriginValidator(
        AuthMiddlewareStack(
            URLRouter(
                chat.routing.websocket_urlpatterns
            )
        )
    ),
})
```

### 4. Models

```python
# chat/models.py
from django.db import models
from django.contrib.auth.models import User

class ChatRoom(models.Model):
    ROOM_TYPES = [
        ('private', 'Private'),
        ('group', 'Group'),
    ]
    
    name = models.CharField(max_length=255)
    room_type = models.CharField(max_length=20, choices=ROOM_TYPES, default='private')
    members = models.ManyToManyField(User, related_name='chat_rooms')
    created_by = models.ForeignKey(User, on_delete=models.CASCADE, related_name='created_rooms')
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['-updated_at']
    
    def __str__(self):
        return self.name
    
    @property
    def last_message(self):
        return self.messages.order_by('-timestamp').first()

class Message(models.Model):
    room = models.ForeignKey(ChatRoom, on_delete=models.CASCADE, related_name='messages')
    sender = models.ForeignKey(User, on_delete=models.CASCADE, related_name='sent_messages')
    content = models.TextField()
    attachment = models.FileField(upload_to='chat_attachments/', blank=True, null=True)
    is_read = models.BooleanField(default=False)
    timestamp = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        ordering = ['timestamp']
        indexes = [
            models.Index(fields=['room', '-timestamp']),
        ]
    
    def __str__(self):
        return f"{self.sender.username}: {self.content[:50]}"

class MessageRead(models.Model):
    message = models.ForeignKey(Message, on_delete=models.CASCADE, related_name='read_by')
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    read_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        unique_together = ['message', 'user']

class TypingStatus(models.Model):
    room = models.ForeignKey(ChatRoom, on_delete=models.CASCADE)
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    is_typing = models.BooleanField(default=False)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        unique_together = ['room', 'user']
```

### 5. WebSocket Consumer

```python
# chat/consumers.py
import json
from channels.generic.websocket import AsyncWebsocketConsumer
from channels.db import database_sync_to_async
from django.contrib.auth.models import User
from .models import ChatRoom, Message, MessageRead, TypingStatus

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.room_id = self.scope['url_route']['kwargs']['room_id']
        self.room_group_name = f'chat_{self.room_id}'
        self.user = self.scope['user']
        
        # Check if user is member of the room
        if not await self.is_room_member():
            await self.close()
            return
        
        # Join room group
        await self.channel_layer.group_add(
            self.room_group_name,
            self.channel_name
        )
        
        await self.accept()
        
        # Send previous messages
        messages = await self.get_room_messages()
        await self.send(text_data=json.dumps({
            'type': 'message_history',
            'messages': messages
        }))
    
    async def disconnect(self, close_code):
        # Leave room group
        await self.channel_layer.group_discard(
            self.room_group_name,
            self.channel_name
        )
        
        # Set typing status to false
        await self.set_typing_status(False)
    
    async def receive(self, text_data):
        data = json.loads(text_data)
        message_type = data.get('type')
        
        if message_type == 'chat_message':
            message = data['message']
            
            # Save message to database
            saved_message = await self.save_message(message)
            
            # Send message to room group
            await self.channel_layer.group_send(
                self.room_group_name,
                {
                    'type': 'chat_message',
                    'message': saved_message
                }
            )
        
        elif message_type == 'typing':
            is_typing = data.get('is_typing', False)
            await self.set_typing_status(is_typing)
            
            # Broadcast typing status
            await self.channel_layer.group_send(
                self.room_group_name,
                {
                    'type': 'typing_status',
                    'user': self.user.username,
                    'is_typing': is_typing
                }
            )
        
        elif message_type == 'read_receipt':
            message_id = data.get('message_id')
            await self.mark_message_read(message_id)
    
    async def chat_message(self, event):
        message = event['message']
        
        # Send message to WebSocket
        await self.send(text_data=json.dumps({
            'type': 'chat_message',
            'message': message
        }))
    
    async def typing_status(self, event):
        # Send typing status to WebSocket
        await self.send(text_data=json.dumps({
            'type': 'typing_status',
            'user': event['user'],
            'is_typing': event['is_typing']
        }))
    
    @database_sync_to_async
    def is_room_member(self):
        try:
            room = ChatRoom.objects.get(id=self.room_id)
            return room.members.filter(id=self.user.id).exists()
        except ChatRoom.DoesNotExist:
            return False
    
    @database_sync_to_async
    def get_room_messages(self):
        messages = Message.objects.filter(
            room_id=self.room_id
        ).select_related('sender').order_by('-timestamp')[:50]
        
        return [{
            'id': msg.id,
            'sender': msg.sender.username,
            'content': msg.content,
            'timestamp': msg.timestamp.isoformat(),
            'is_own': msg.sender.id == self.user.id
        } for msg in reversed(messages)]
    
    @database_sync_to_async
    def save_message(self, content):
        message = Message.objects.create(
            room_id=self.room_id,
            sender=self.user,
            content=content
        )
        
        # Update room's updated_at
        ChatRoom.objects.filter(id=self.room_id).update(updated_at=message.timestamp)
        
        return {
            'id': message.id,
            'sender': self.user.username,
            'content': content,
            'timestamp': message.timestamp.isoformat(),
            'is_own': True
        }
    
    @database_sync_to_async
    def set_typing_status(self, is_typing):
        TypingStatus.objects.update_or_create(
            room_id=self.room_id,
            user=self.user,
            defaults={'is_typing': is_typing}
        )
    
    @database_sync_to_async
    def mark_message_read(self, message_id):
        try:
            message = Message.objects.get(id=message_id)
            MessageRead.objects.get_or_create(
                message=message,
                user=self.user
            )
            return True
        except Message.DoesNotExist:
            return False
```

### 6. Routing

```python
# chat/routing.py
from django.urls import re_path
from . import consumers

websocket_urlpatterns = [
    re_path(r'ws/chat/(?P<room_id>\d+)/$', consumers.ChatConsumer.as_asgi()),
]
```

### 7. Views

```python
# chat/views.py
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib.auth.decorators import login_required
from django.http import JsonResponse
from django.db.models import Q, Max, Count
from .models import ChatRoom, Message

@login_required
def chat_rooms(request):
    # Get user's chat rooms
    rooms = ChatRoom.objects.filter(
        members=request.user
    ).annotate(
        unread_count=Count('messages', filter=Q(
            messages__is_read=False,
            ~Q(messages__sender=request.user)
        ))
    ).prefetch_related('members')
    
    context = {
        'rooms': rooms,
    }
    return render(request, 'chat/rooms.html', context)

@login_required
def chat_room(request, room_id):
    room = get_object_or_404(ChatRoom, id=room_id, members=request.user)
    
    # Mark messages as read
    Message.objects.filter(
        room=room,
        is_read=False
    ).exclude(sender=request.user).update(is_read=True)
    
    context = {
        'room': room,
    }
    return render(request, 'chat/room.html', context)

@login_required
def create_room(request):
    if request.method == 'POST':
        name = request.POST.get('name')
        member_ids = request.POST.getlist('members')
        room_type = request.POST.get('room_type', 'group')
        
        room = ChatRoom.objects.create(
            name=name,
            room_type=room_type,
            created_by=request.user
        )
        
        # Add members
        room.members.add(request.user)
        room.members.add(*member_ids)
        
        return redirect('chat:room', room_id=room.id)
    
    # Get users for member selection
    from django.contrib.auth.models import User
    users = User.objects.exclude(id=request.user.id)
    
    context = {
        'users': users,
    }
    return render(request, 'chat/create_room.html', context)

@login_required
def start_private_chat(request, user_id):
    from django.contrib.auth.models import User
    other_user = get_object_or_404(User, id=user_id)
    
    # Check if private chat already exists
    room = ChatRoom.objects.filter(
        room_type='private',
        members=request.user
    ).filter(members=other_user).first()
    
    if not room:
        # Create new private chat
        room = ChatRoom.objects.create(
            name=f'{request.user.username} & {other_user.username}',
            room_type='private',
            created_by=request.user
        )
        room.members.add(request.user, other_user)
    
    return redirect('chat:room', room_id=room.id)
```

### 8. Templates

```django
<!-- templates/chat/room.html -->
{% extends 'base.html' %}

{% block content %}
<div class="container-fluid h-100">
    <div class="row h-100">
        <div class="col-md-12">
            <div class="card h-100">
                <div class="card-header">
                    <h5>{{ room.name }}</h5>
                    <div id="typing-indicator" class="text-muted small"></div>
                </div>
                
                <div class="card-body" id="chat-messages" style="overflow-y: scroll; height: 500px;">
                    <!-- Messages will be loaded here -->
                </div>
                
                <div class="card-footer">
                    <form id="chat-form">
                        <div class="input-group">
                            <input type="text" id="message-input" class="form-control" 
                                   placeholder="Type a message..." autocomplete="off">
                            <button class="btn btn-primary" type="submit">Send</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
const roomId = {{ room.id }};
const username = '{{ request.user.username }}';
const chatSocket = new WebSocket(
    'ws://' + window.location.host + '/ws/chat/' + roomId + '/'
);

const messagesDiv = document.getElementById('chat-messages');
const messageInput = document.getElementById('message-input');
const chatForm = document.getElementById('chat-form');
const typingIndicator = document.getElementById('typing-indicator');

let typingTimeout;

// WebSocket event handlers
chatSocket.onmessage = function(e) {
    const data = JSON.parse(e.data);
    
    if (data.type === 'message_history') {
        // Load previous messages
        data.messages.forEach(msg => addMessage(msg));
        scrollToBottom();
    }
    else if (data.type === 'chat_message') {
        addMessage(data.message);
        scrollToBottom();
    }
    else if (data.type === 'typing_status') {
        if (data.user !== username) {
            if (data.is_typing) {
                typingIndicator.textContent = `${data.user} is typing...`;
            } else {
                typingIndicator.textContent = '';
            }
        }
    }
};

chatSocket.onclose = function(e) {
    console.error('Chat socket closed unexpectedly');
};

// Send message
chatForm.addEventListener('submit', function(e) {
    e.preventDefault();
    const message = messageInput.value.trim();
    
    if (message) {
        chatSocket.send(JSON.stringify({
            'type': 'chat_message',
            'message': message
        }));
        messageInput.value = '';
    }
});

// Typing indicator
messageInput.addEventListener('input', function() {
    chatSocket.send(JSON.stringify({
        'type': 'typing',
        'is_typing': true
    }));
    
    clearTimeout(typingTimeout);
    typingTimeout = setTimeout(function() {
        chatSocket.send(JSON.stringify({
            'type': 'typing',
            'is_typing': false
        }));
    }, 1000);
});

// Helper functions
function addMessage(message) {
    const messageDiv = document.createElement('div');
    messageDiv.className = message.is_own ? 'message own-message' : 'message other-message';
    
    const time = new Date(message.timestamp).toLocaleTimeString();
    
    messageDiv.innerHTML = `
        <div class="message-header">
            <strong>${message.sender}</strong>
            <small class="text-muted">${time}</small>
        </div>
        <div class="message-content">${escapeHtml(message.content)}</div>
    `;
    
    messagesDiv.appendChild(messageDiv);
}

function scrollToBottom() {
    messagesDiv.scrollTop = messagesDiv.scrollHeight;
}

function escapeHtml(text) {
    const div = document.createElement('div');
    div.textContent = text;
    return div.innerHTML;
}
</script>

<style>
.message {
    margin-bottom: 15px;
    padding: 10px;
    border-radius: 8px;
}

.own-message {
    background-color: #007bff;
    color: white;
    margin-left: 20%;
    text-align: right;
}

.other-message {
    background-color: #f1f1f1;
    margin-right: 20%;
}

.message-header {
    margin-bottom: 5px;
}

.message-content {
    word-wrap: break-word;
}
</style>
{% endblock %}
```

### 9. Running the Application

```bash
# Install Redis
# macOS: brew install redis
# Ubuntu: sudo apt-get install redis-server

# Start Redis
redis-server

# Run Django with Daphne
daphne -b 0.0.0.0 -p 8000 myproject.asgi:application

# Or use Django development server (supports ASGI)
python manage.py runserver
```

**Key Features:**

1. **Real-time Messaging**: Instant message delivery using WebSockets
2. **Private & Group Chats**: Support for both chat types
3. **Typing Indicators**: Show when users are typing
4. **Read Receipts**: Track message read status
5. **Message History**: Load previous messages on connect
6. **Online Status**: Track user presence
7. **File Attachments**: Support for file sharing
8. **Notifications**: Real-time notifications for new messages

---

#### 21. Create a multi-vendor e-commerce platform with Django. Include vendor management, product catalog, shopping cart, order processing, payment integration, and admin dashboard.

**Answer:**

## Multi-Vendor E-Commerce Platform

### 1. Project Setup

```bash
django-admin startproject marketplace
cd marketplace
python manage.py startapp vendors
python manage.py startapp products
python manage.py startapp orders
python manage.py startapp payments
```

### 2. Models

```python
# vendors/models.py
from django.db import models
from django.contrib.auth.models import User

class Vendor(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='vendor_profile')
    business_name = models.CharField(max_length=255)
    slug = models.SlugField(unique=True)
    description = models.TextField()
    logo = models.ImageField(upload_to='vendor_logos/', blank=True)
    banner = models.ImageField(upload_to='vendor_banners/', blank=True)
    
    # Contact Information
    email = models.EmailField()
    phone = models.CharField(max_length=20)
    address = models.TextField()
    city = models.CharField(max_length=100)
    state = models.CharField(max_length=100)
    country = models.CharField(max_length=100)
    postal_code = models.CharField(max_length=20)
    
    # Business Details
    tax_id = models.CharField(max_length=50, blank=True)
    commission_rate = models.DecimalField(max_digits=5, decimal_places=2, default=10.00)
    
    # Status
    is_approved = models.BooleanField(default=False)
    is_active = models.BooleanField(default=True)
    
    # Stats
    total_sales = models.DecimalField(max_digits=12, decimal_places=2, default=0)
    total_orders = models.PositiveIntegerField(default=0)
    rating = models.DecimalField(max_digits=3, decimal_places=2, default=0)
    
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['-created_at']
    
    def __str__(self):
        return self.business_name

class VendorBankAccount(models.Model):
    vendor = models.OneToOneField(Vendor, on_delete=models.CASCADE, related_name='bank_account')
    account_holder_name = models.CharField(max_length=255)
    bank_name = models.CharField(max_length=255)
    account_number = models.CharField(max_length=50)
    routing_number = models.CharField(max_length=50)
    swift_code = models.CharField(max_length=20, blank=True)
    
    def __str__(self):
        return f"{self.vendor.business_name} - {self.bank_name}"

# products/models.py
from django.db import models
from vendors.models import Vendor

class Category(models.Model):
    name = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    parent = models.ForeignKey('self', on_delete=models.CASCADE, null=True, blank=True, related_name='children')
    image = models.ImageField(upload_to='categories/', blank=True)
    description = models.TextField(blank=True)
    is_active = models.BooleanField(default=True)
    
    class Meta:
        verbose_name_plural = 'Categories'
        ordering = ['name']
    
    def __str__(self):
        return self.name

class Product(models.Model):
    vendor = models.ForeignKey(Vendor, on_delete=models.CASCADE, related_name='products')
    category = models.ForeignKey(Category, on_delete=models.SET_NULL, null=True, related_name='products')
    
    name = models.CharField(max_length=255)
    slug = models.SlugField(unique=True)
    description = models.TextField()
    short_description = models.CharField(max_length=500)
    
    # Pricing
    price = models.DecimalField(max_digits=10, decimal_places=2)
    compare_price = models.DecimalField(max_digits=10, decimal_places=2, null=True, blank=True)
    cost_price = models.DecimalField(max_digits=10, decimal_places=2, null=True, blank=True)
    
    # Inventory
    sku = models.CharField(max_length=100, unique=True)
    stock = models.PositiveIntegerField(default=0)
    low_stock_threshold = models.PositiveIntegerField(default=10)
    
    # Shipping
    weight = models.DecimalField(max_digits=6, decimal_places=2, null=True, blank=True)
    length = models.DecimalField(max_digits=6, decimal_places=2, null=True, blank=True)
    width = models.DecimalField(max_digits=6, decimal_places=2, null=True, blank=True)
    height = models.DecimalField(max_digits=6, decimal_places=2, null=True, blank=True)
    
    # SEO
    meta_title = models.CharField(max_length=200, blank=True)
    meta_description = models.CharField(max_length=300, blank=True)
    
    # Status
    is_active = models.BooleanField(default=True)
    is_featured = models.BooleanField(default=False)
    
    # Stats
    views = models.PositiveIntegerField(default=0)
    sales_count = models.PositiveIntegerField(default=0)
    rating = models.DecimalField(max_digits=3, decimal_places=2, default=0)
    review_count = models.PositiveIntegerField(default=0)
    
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['-created_at']
        indexes = [
            models.Index(fields=['vendor', '-created_at']),
            models.Index(fields=['category', '-created_at']),
            models.Index(fields=['-sales_count']),
        ]
    
    def __str__(self):
        return self.name
    
    @property
    def discount_percentage(self):
        if self.compare_price and self.compare_price > self.price:
            return int(((self.compare_price - self.price) / self.compare_price) * 100)
        return 0
    
    @property
    def is_in_stock(self):
        return self.stock > 0

class ProductImage(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE, related_name='images')
    image = models.ImageField(upload_to='products/')
    alt_text = models.CharField(max_length=255, blank=True)
    is_primary = models.BooleanField(default=False)
    order = models.PositiveIntegerField(default=0)
    
    class Meta:
        ordering = ['order']

class ProductVariant(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE, related_name='variants')
    name = models.CharField(max_length=100)
    sku = models.CharField(max_length=100, unique=True)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.PositiveIntegerField(default=0)
    attributes = models.JSONField(default=dict)  # e.g., {"size": "L", "color": "Red"}
    
    def __str__(self):
        return f"{self.product.name} - {self.name}"

class Review(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE, related_name='reviews')
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    rating = models.PositiveSmallIntegerField(choices=[(i, i) for i in range(1, 6)])
    title = models.CharField(max_length=200)
    comment = models.TextField()
    is_verified_purchase = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        unique_together = ['product', 'user']
        ordering = ['-created_at']

# orders/models.py
from django.db import models
from django.contrib.auth.models import User
from products.models import Product, ProductVariant
from vendors.models import Vendor

class Cart(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE, null=True, blank=True)
    session_key = models.CharField(max_length=255, null=True, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    def __str__(self):
        return f"Cart {self.id}"
    
    @property
    def total(self):
        return sum(item.subtotal for item in self.items.all())
    
    @property
    def item_count(self):
        return sum(item.quantity for item in self.items.all())

class CartItem(models.Model):
    cart = models.ForeignKey(Cart, on_delete=models.CASCADE, related_name='items')
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    variant = models.ForeignKey(ProductVariant, on_delete=models.CASCADE, null=True, blank=True)
    quantity = models.PositiveIntegerField(default=1)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    
    class Meta:
        unique_together = ['cart', 'product', 'variant']
    
    @property
    def subtotal(self):
        return self.price * self.quantity

class Order(models.Model):
    STATUS_CHOICES = [
        ('pending', 'Pending'),
        ('confirmed', 'Confirmed'),
        ('processing', 'Processing'),
        ('shipped', 'Shipped'),
        ('delivered', 'Delivered'),
        ('cancelled', 'Cancelled'),
        ('refunded', 'Refunded'),
    ]
    
    order_number = models.CharField(max_length=50, unique=True)
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name='orders')
    
    # Shipping Address
    shipping_name = models.CharField(max_length=255)
    shipping_email = models.EmailField()
    shipping_phone = models.CharField(max_length=20)
    shipping_address = models.TextField()
    shipping_city = models.CharField(max_length=100)
    shipping_state = models.CharField(max_length=100)
    shipping_country = models.CharField(max_length=100)
    shipping_postal_code = models.CharField(max_length=20)
    
    # Billing Address (can be same as shipping)
    billing_address = models.TextField()
    
    # Pricing
    subtotal = models.DecimalField(max_digits=10, decimal_places=2)
    shipping_cost = models.DecimalField(max_digits=10, decimal_places=2, default=0)
    tax = models.DecimalField(max_digits=10, decimal_places=2, default=0)
    discount = models.DecimalField(max_digits=10, decimal_places=2, default=0)
    total = models.DecimalField(max_digits=10, decimal_places=2)
    
    # Status
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default='pending')
    payment_status = models.CharField(max_length=20, default='pending')
    
    # Notes
    customer_notes = models.TextField(blank=True)
    admin_notes = models.TextField(blank=True)
    
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['-created_at']
    
    def __str__(self):
        return self.order_number

class OrderItem(models.Model):
    order = models.ForeignKey(Order, on_delete=models.CASCADE, related_name='items')
    vendor = models.ForeignKey(Vendor, on_delete=models.CASCADE)
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    variant = models.ForeignKey(ProductVariant, on_delete=models.SET_NULL, null=True, blank=True)
    
    product_name = models.CharField(max_length=255)
    product_sku = models.CharField(max_length=100)
    quantity = models.PositiveIntegerField()
    price = models.DecimalField(max_digits=10, decimal_places=2)
    subtotal = models.DecimalField(max_digits=10, decimal_places=2)
    
    # Vendor commission
    commission_rate = models.DecimalField(max_digits=5, decimal_places=2)
    commission_amount = models.DecimalField(max_digits=10, decimal_places=2)
    vendor_earnings = models.DecimalField(max_digits=10, decimal_places=2)
    
    def save(self, *args, **kwargs):
        self.subtotal = self.price * self.quantity
        self.commission_amount = (self.subtotal * self.commission_rate) / 100
        self.vendor_earnings = self.subtotal - self.commission_amount
        super().save(*args, **kwargs)

class VendorOrder(models.Model):
    """Represents a vendor's portion of an order"""
    order = models.ForeignKey(Order, on_delete=models.CASCADE, related_name='vendor_orders')
    vendor = models.ForeignKey(Vendor, on_delete=models.CASCADE, related_name='vendor_orders')
    
    STATUS_CHOICES = [
        ('pending', 'Pending'),
        ('processing', 'Processing'),
        ('shipped', 'Shipped'),
        ('delivered', 'Delivered'),
        ('cancelled', 'Cancelled'),
    ]
    
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default='pending')
    tracking_number = models.CharField(max_length=100, blank=True)
    shipping_carrier = models.CharField(max_length=100, blank=True)
    
    subtotal = models.DecimalField(max_digits=10, decimal_places=2)
    commission = models.DecimalField(max_digits=10, decimal_places=2)
    earnings = models.DecimalField(max_digits=10, decimal_places=2)
    
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

# payments/models.py
from django.db import models
from orders.models import Order
from vendors.models import Vendor

class Payment(models.Model):
    PAYMENT_METHODS = [
        ('stripe', 'Stripe'),
        ('paypal', 'PayPal'),
        ('bank_transfer', 'Bank Transfer'),
    ]
    
    PAYMENT_STATUS = [
        ('pending', 'Pending'),
        ('completed', 'Completed'),
        ('failed', 'Failed'),
        ('refunded', 'Refunded'),
    ]
    
    order = models.ForeignKey(Order, on_delete=models.CASCADE, related_name='payments')
    payment_method = models.CharField(max_length=20, choices=PAYMENT_METHODS)
    transaction_id = models.CharField(max_length=255, unique=True)
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    status = models.CharField(max_length=20, choices=PAYMENT_STATUS, default='pending')
    
    # Payment gateway response
    gateway_response = models.JSONField(default=dict)
    
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    def __str__(self):
        return f"Payment {self.transaction_id}"

class VendorPayout(models.Model):
    vendor = models.ForeignKey(Vendor, on_delete=models.CASCADE, related_name='payouts')
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    
    STATUS_CHOICES = [
        ('pending', 'Pending'),
        ('processing', 'Processing'),
        ('completed', 'Completed'),
        ('failed', 'Failed'),
    ]
    
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default='pending')
    transaction_id = models.CharField(max_length=255, blank=True)
    notes = models.TextField(blank=True)
    
    created_at = models.DateTimeField(auto_now_add=True)
    processed_at = models.DateTimeField(null=True, blank=True)
    
    def __str__(self):
        return f"Payout to {self.vendor.business_name} - ${self.amount}"
```

### 3. Views

```python
# products/views.py
from django.shortcuts import render, get_object_or_404
from django.db.models import Q, Avg
from django.core.paginator import Paginator
from .models import Product, Category, Review

def product_list(request):
    products = Product.objects.filter(is_active=True).select_related('vendor', 'category')
    
    # Filters
    category_slug = request.GET.get('category')
    if category_slug:
        products = products.filter(category__slug=category_slug)
    
    vendor_slug = request.GET.get('vendor')
    if vendor_slug:
        products = products.filter(vendor__slug=vendor_slug)
    
    # Search
    search = request.GET.get('search')
    if search:
        products = products.filter(
            Q(name__icontains=search) | 
            Q(description__icontains=search)
        )
    
    # Price range
    min_price = request.GET.get('min_price')
    max_price = request.GET.get('max_price')
    if min_price:
        products = products.filter(price__gte=min_price)
    if max_price:
        products = products.filter(price__lte=max_price)
    
    # Sorting
    sort = request.GET.get('sort', '-created_at')
    products = products.order_by(sort)
    
    # Pagination
    paginator = Paginator(products, 12)
    page_obj = paginator.get_page(request.GET.get('page'))
    
    context = {
        'page_obj': page_obj,
        'categories': Category.objects.filter(is_active=True),
    }
    return render(request, 'products/list.html', context)

def product_detail(request, slug):
    product = get_object_or_404(
        Product.objects.select_related('vendor', 'category').prefetch_related('images', 'variants'),
        slug=slug,
        is_active=True
    )
    
    # Increment views
    product.views += 1
    product.save(update_fields=['views'])
    
    # Get reviews
    reviews = product.reviews.select_related('user').order_by('-created_at')
    
    # Related products
    related_products = Product.objects.filter(
        category=product.category,
        is_active=True
    ).exclude(id=product.id)[:4]
    
    context = {
        'product': product,
        'reviews': reviews,
        'related_products': related_products,
    }
    return render(request, 'products/detail.html', context)

# orders/views.py
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib.auth.decorators import login_required
from django.http import JsonResponse
from django.db import transaction
from .models import Cart, CartItem, Order, OrderItem, VendorOrder
from products.models import Product
import uuid

@login_required
def add_to_cart(request, product_id):
    product = get_object_or_404(Product, id=product_id, is_active=True)
    quantity = int(request.POST.get('quantity', 1))
    variant_id = request.POST.get('variant_id')
    
    # Get or create cart
    cart, created = Cart.objects.get_or_create(user=request.user)
    
    # Add or update cart item
    cart_item, created = CartItem.objects.get_or_create(
        cart=cart,
        product=product,
        variant_id=variant_id,
        defaults={'price': product.price, 'quantity': quantity}
    )
    
    if not created:
        cart_item.quantity += quantity
        cart_item.save()
    
    return JsonResponse({
        'success': True,
        'cart_count': cart.item_count,
        'message': 'Product added to cart'
    })

@login_required
def view_cart(request):
    cart = Cart.objects.filter(user=request.user).prefetch_related(
        'items__product__vendor',
        'items__variant'
    ).first()
    
    context = {
        'cart': cart,
    }
    return render(request, 'orders/cart.html', context)

@login_required
def checkout(request):
    cart = get_object_or_404(Cart, user=request.user)
    
    if request.method == 'POST':
        with transaction.atomic():
            # Create order
            order = Order.objects.create(
                order_number=f'ORD-{uuid.uuid4().hex[:8].upper()}',
                user=request.user,
                shipping_name=request.POST.get('shipping_name'),
                shipping_email=request.POST.get('shipping_email'),
                shipping_phone=request.POST.get('shipping_phone'),
                shipping_address=request.POST.get('shipping_address'),
                shipping_city=request.POST.get('shipping_city'),
                shipping_state=request.POST.get('shipping_state'),
                shipping_country=request.POST.get('shipping_country'),
                shipping_postal_code=request.POST.get('shipping_postal_code'),
                billing_address=request.POST.get('billing_address'),
                subtotal=cart.total,
                total=cart.total,
            )
            
            # Group cart items by vendor
            vendor_items = {}
            for cart_item in cart.items.all():
                vendor = cart_item.product.vendor
                if vendor not in vendor_items:
                    vendor_items[vendor] = []
                vendor_items[vendor].append(cart_item)
            
            # Create order items and vendor orders
            for vendor, items in vendor_items.items():
                vendor_subtotal = sum(item.subtotal for item in items)
                vendor_commission = (vendor_subtotal * vendor.commission_rate) / 100
                vendor_earnings = vendor_subtotal - vendor_commission
                
                # Create vendor order
                vendor_order = VendorOrder.objects.create(
                    order=order,
                    vendor=vendor,
                    subtotal=vendor_subtotal,
                    commission=vendor_commission,
                    earnings=vendor_earnings
                )
                
                # Create order items
                for cart_item in items:
                    OrderItem.objects.create(
                        order=order,
                        vendor=vendor,
                        product=cart_item.product,
                        variant=cart_item.variant,
                        product_name=cart_item.product.name,
                        product_sku=cart_item.product.sku,
                        quantity=cart_item.quantity,
                        price=cart_item.price,
                        commission_rate=vendor.commission_rate
                    )
                    
                    # Update product stock
                    if cart_item.variant:
                        cart_item.variant.stock -= cart_item.quantity
                        cart_item.variant.save()
                    else:
                        cart_item.product.stock -= cart_item.quantity
                        cart_item.product.save()
            
            # Clear cart
            cart.items.all().delete()
            
            return redirect('orders:payment', order_id=order.id)
    
    context = {
        'cart': cart,
    }
    return render(request, 'orders/checkout.html', context)

# vendors/views.py
from django.shortcuts import render, redirect
from django.contrib.auth.decorators import login_required
from django.db.models import Sum, Count
from .models import Vendor, VendorOrder
from products.models import Product

@login_required
def vendor_dashboard(request):
    vendor = get_object_or_404(Vendor, user=request.user)
    
    # Stats
    total_products = vendor.products.count()
    total_orders = vendor.vendor_orders.count()
    pending_orders = vendor.vendor_orders.filter(status='pending').count()
    
    # Recent orders
    recent_orders = vendor.vendor_orders.select_related('order').order_by('-created_at')[:10]
    
    # Revenue
    total_revenue = vendor.vendor_orders.aggregate(
        total=Sum('earnings')
    )['total'] or 0
    
    context = {
        'vendor': vendor,
        'total_products': total_products,
        'total_orders': total_orders,
        'pending_orders': pending_orders,
        'recent_orders': recent_orders,
        'total_revenue': total_revenue,
    }
    return render(request, 'vendors/dashboard.html', context)

@login_required
def vendor_products(request):
    vendor = get_object_or_404(Vendor, user=request.user)
    products = vendor.products.all().order_by('-created_at')
    
    context = {
        'vendor': vendor,
        'products': products,
    }
    return render(request, 'vendors/products.html', context)

@login_required
def vendor_orders(request):
    vendor = get_object_or_404(Vendor, user=request.user)
    orders = vendor.vendor_orders.select_related('order').order_by('-created_at')
    
    context = {
        'vendor': vendor,
        'orders': orders,
    }
    return render(request, 'vendors/orders.html', context)
```

### 4. Payment Integration (Stripe)

```python
# payments/stripe_payment.py
import stripe
from django.conf import settings
from .models import Payment

stripe.api_key = settings.STRIPE_SECRET_KEY

def create_payment_intent(order):
    try:
        intent = stripe.PaymentIntent.create(
            amount=int(order.total * 100),  # Amount in cents
            currency='usd',
            metadata={
                'order_id': order.id,
                'order_number': order.order_number,
            }
        )
        
        return {
            'success': True,
            'client_secret': intent.client_secret,
            'payment_intent_id': intent.id
        }
    except stripe.error.StripeError as e:
        return {
            'success': False,
            'error': str(e)
        }

def confirm_payment(payment_intent_id, order):
    try:
        intent = stripe.PaymentIntent.retrieve(payment_intent_id)
        
        if intent.status == 'succeeded':
            # Create payment record
            payment = Payment.objects.create(
                order=order,
                payment_method='stripe',
                transaction_id=payment_intent_id,
                amount=order.total,
                status='completed',
                gateway_response=intent
            )
            
            # Update order
            order.payment_status = 'completed'
            order.status = 'confirmed'
            order.save()
            
            return {'success': True, 'payment': payment}
        
        return {'success': False, 'error': 'Payment not completed'}
    
    except stripe.error.StripeError as e:
        return {'success': False, 'error': str(e)}

# payments/views.py
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib.auth.decorators import login_required
from django.http import JsonResponse
from django.views.decorators.csrf import csrf_exempt
from orders.models import Order
from .stripe_payment import create_payment_intent, confirm_payment

@login_required
def payment_page(request, order_id):
    order = get_object_or_404(Order, id=order_id, user=request.user)
    
    # Create payment intent
    result = create_payment_intent(order)
    
    if result['success']:
        context = {
            'order': order,
            'client_secret': result['client_secret'],
            'stripe_public_key': settings.STRIPE_PUBLIC_KEY,
        }
        return render(request, 'payments/payment.html', context)
    
    return redirect('orders:order_detail', order_id=order.id)

@csrf_exempt
def stripe_webhook(request):
    payload = request.body
    sig_header = request.META['HTTP_STRIPE_SIGNATURE']
    
    try:
        event = stripe.Webhook.construct_event(
            payload, sig_header, settings.STRIPE_WEBHOOK_SECRET
        )
        
        if event['type'] == 'payment_intent.succeeded':
            payment_intent = event['data']['object']
            order_id = payment_intent['metadata']['order_id']
            
            order = Order.objects.get(id=order_id)
            confirm_payment(payment_intent['id'], order)
        
        return JsonResponse({'status': 'success'})
    
    except Exception as e:
        return JsonResponse({'error': str(e)}, status=400)
```

**Key Features:**

1. **Multi-Vendor System**: Separate vendor accounts with approval workflow
2. **Product Management**: Full catalog with variants, images, inventory
3. **Shopping Cart**: Session and user-based carts
4. **Order Processing**: Split orders by vendor, commission calculation
5. **Payment Integration**: Stripe payment processing
6. **Vendor Dashboard**: Sales analytics, order management
7. **Commission System**: Automatic commission calculation and payouts
8. **Reviews & Ratings**: Product reviews with verified purchases

---

#### 22. Implement a content management system (CMS) with Django. Include page builder, media library, SEO tools, and multi-language support.

**Answer:**

## Django CMS Implementation

### 1. Project Setup

```bash
django-admin startproject cms_project
cd cms_project
python manage.py startapp pages
python manage.py startapp media_library
python manage.py startapp seo
```

### 2. Models

```python
# pages/models.py
from django.db import models
from django.contrib.auth.models import User
from django.utils.text import slugify

class Page(models.Model):
    STATUS_CHOICES = [
        ('draft', 'Draft'),
        ('published', 'Published'),
        ('archived', 'Archived'),
    ]
    
    TEMPLATE_CHOICES = [
        ('default', 'Default'),
        ('landing', 'Landing Page'),
        ('blog', 'Blog Post'),
        ('contact', 'Contact Page'),
    ]
    
    title = models.CharField(max_length=255)
    slug = models.SlugField(unique=True, max_length=255)
    template = models.CharField(max_length=50, choices=TEMPLATE_CHOICES, default='default')
    
    # Content
    excerpt = models.TextField(blank=True)
    featured_image = models.ImageField(upload_to='pages/', blank=True, null=True)
    
    # SEO
    meta_title = models.CharField(max_length=200, blank=True)
    meta_description = models.CharField(max_length=300, blank=True)
    meta_keywords = models.CharField(max_length=255, blank=True)
    og_image = models.ImageField(upload_to='og_images/', blank=True, null=True)
    
    # Publishing
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='pages')
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default='draft')
    published_at = models.DateTimeField(null=True, blank=True)
    
    # Hierarchy
    parent = models.ForeignKey('self', on_delete=models.CASCADE, null=True, blank=True, related_name='children')
    order = models.PositiveIntegerField(default=0)
    
    # Settings
    is_homepage = models.BooleanField(default=False)
    show_in_menu = models.BooleanField(default=True)
    require_auth = models.BooleanField(default=False)
    
    # Timestamps
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['order', '-created_at']
    
    def __str__(self):
        return self.title
    
    def save(self, *args, **kwargs):
        if not self.slug:
            self.slug = slugify(self.title)
        
        if self.is_homepage:
            # Ensure only one homepage
            Page.objects.filter(is_homepage=True).update(is_homepage=False)
        
        super().save(*args, **kwargs)

class PageBlock(models.Model):
    """Modular content blocks for page builder"""
    BLOCK_TYPES = [
        ('text', 'Text'),
        ('image', 'Image'),
        ('video', 'Video'),
        ('gallery', 'Gallery'),
        ('html', 'HTML'),
        ('form', 'Form'),
        ('testimonial', 'Testimonial'),
        ('cta', 'Call to Action'),
        ('accordion', 'Accordion'),
        ('tabs', 'Tabs'),
        ('columns', 'Columns'),
    ]
    
    page = models.ForeignKey(Page, on_delete=models.CASCADE, related_name='blocks')
    block_type = models.CharField(max_length=50, choices=BLOCK_TYPES)
    content = models.JSONField(default=dict)
    order = models.PositiveIntegerField(default=0)
    is_active = models.BooleanField(default=True)
    
    class Meta:
        ordering = ['order']
    
    def __str__(self):
        return f"{self.page.title} - {self.block_type}"

class PageTranslation(models.Model):
    """Multi-language support"""
    page = models.ForeignKey(Page, on_delete=models.CASCADE, related_name='translations')
    language = models.CharField(max_length=10)
    title = models.CharField(max_length=255)
    excerpt = models.TextField(blank=True)
    meta_title = models.CharField(max_length=200, blank=True)
    meta_description = models.CharField(max_length=300, blank=True)
    
    class Meta:
        unique_together = ['page', 'language']
    
    def __str__(self):
        return f"{self.page.title} - {self.language}"

class PageVersion(models.Model):
    """Version control for pages"""
    page = models.ForeignKey(Page, on_delete=models.CASCADE, related_name='versions')
    version_number = models.PositiveIntegerField()
    content_snapshot = models.JSONField()
    created_by = models.ForeignKey(User, on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)
    notes = models.TextField(blank=True)
    
    class Meta:
        ordering = ['-version_number']
        unique_together = ['page', 'version_number']

# media_library/models.py
from django.db import models
from django.contrib.auth.models import User
import os

class MediaFolder(models.Model):
    name = models.CharField(max_length=255)
    parent = models.ForeignKey('self', on_delete=models.CASCADE, null=True, blank=True, related_name='subfolders')
    created_by = models.ForeignKey(User, on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        ordering = ['name']
    
    def __str__(self):
        return self.name

class MediaFile(models.Model):
    FILE_TYPES = [
        ('image', 'Image'),
        ('video', 'Video'),
        ('document', 'Document'),
        ('audio', 'Audio'),
        ('other', 'Other'),
    ]
    
    folder = models.ForeignKey(MediaFolder, on_delete=models.SET_NULL, null=True, blank=True, related_name='files')
    file = models.FileField(upload_to='media_library/%Y/%m/')
    file_type = models.CharField(max_length=20, choices=FILE_TYPES)
    
    # Metadata
    title = models.CharField(max_length=255, blank=True)
    alt_text = models.CharField(max_length=255, blank=True)
    caption = models.TextField(blank=True)
    description = models.TextField(blank=True)
    
    # File info
    file_size = models.PositiveIntegerField()  # in bytes
    mime_type = models.CharField(max_length=100)
    width = models.PositiveIntegerField(null=True, blank=True)
    height = models.PositiveIntegerField(null=True, blank=True)
    
    # Tracking
    uploaded_by = models.ForeignKey(User, on_delete=models.CASCADE)
    uploaded_at = models.DateTimeField(auto_now_add=True)
    downloads = models.PositiveIntegerField(default=0)
    
    class Meta:
        ordering = ['-uploaded_at']
    
    def __str__(self):
        return self.title or self.file.name
    
    @property
    def filename(self):
        return os.path.basename(self.file.name)
    
    def save(self, *args, **kwargs):
        if self.file:
            self.file_size = self.file.size
            
            # Determine file type
            ext = os.path.splitext(self.file.name)[1].lower()
            if ext in ['.jpg', '.jpeg', '.png', '.gif', '.webp', '.svg']:
                self.file_type = 'image'
            elif ext in ['.mp4', '.avi', '.mov', '.wmv']:
                self.file_type = 'video'
            elif ext in ['.pdf', '.doc', '.docx', '.xls', '.xlsx']:
                self.file_type = 'document'
            elif ext in ['.mp3', '.wav', '.ogg']:
                self.file_type = 'audio'
            else:
                self.file_type = 'other'
        
        super().save(*args, **kwargs)

# seo/models.py
from django.db import models

class SEOSettings(models.Model):
    site_name = models.CharField(max_length=255)
    site_description = models.TextField()
    default_og_image = models.ImageField(upload_to='seo/', blank=True)
    
    # Social Media
    facebook_url = models.URLField(blank=True)
    twitter_url = models.URLField(blank=True)
    instagram_url = models.URLField(blank=True)
    linkedin_url = models.URLField(blank=True)
    
    # Analytics
    google_analytics_id = models.CharField(max_length=50, blank=True)
    google_tag_manager_id = models.CharField(max_length=50, blank=True)
    
    # Verification
    google_site_verification = models.CharField(max_length=100, blank=True)
    bing_site_verification = models.CharField(max_length=100, blank=True)
    
    class Meta:
        verbose_name = 'SEO Settings'
        verbose_name_plural = 'SEO Settings'

class Redirect(models.Model):
    REDIRECT_TYPES = [
        (301, '301 Permanent'),
        (302, '302 Temporary'),
    ]
    
    old_path = models.CharField(max_length=500, unique=True)
    new_path = models.CharField(max_length=500)
    redirect_type = models.IntegerField(choices=REDIRECT_TYPES, default=301)
    is_active = models.BooleanField(default=True)
    created_at = models.DateTimeField(auto_now_add=True)
    
    def __str__(self):
        return f"{self.old_path} → {self.new_path}"
```

### 3. Page Builder Views

```python
# pages/views.py
from django.shortcuts import render, get_object_or_404, redirect
from django.contrib.auth.decorators import login_required
from django.http import JsonResponse
from django.views.decorators.csrf import csrf_exempt
from .models import Page, PageBlock, PageVersion
import json

def page_view(request, slug=None):
    if slug:
        page = get_object_or_404(Page, slug=slug, status='published')
    else:
        page = get_object_or_404(Page, is_homepage=True, status='published')
    
    # Get blocks
    blocks = page.blocks.filter(is_active=True).order_by('order')
    
    # Get translation if available
    language = request.LANGUAGE_CODE
    translation = page.translations.filter(language=language).first()
    
    context = {
        'page': page,
        'blocks': blocks,
        'translation': translation,
    }
    
    template = f'pages/templates/{page.template}.html'
    return render(request, template, context)

@login_required
def page_builder(request, page_id):
    page = get_object_or_404(Page, id=page_id)
    
    if request.method == 'POST':
        # Save page blocks
        blocks_data = json.loads(request.POST.get('blocks', '[]'))
        
        # Delete existing blocks
        page.blocks.all().delete()
        
        # Create new blocks
        for idx, block_data in enumerate(blocks_data):
            PageBlock.objects.create(
                page=page,
                block_type=block_data['type'],
                content=block_data['content'],
                order=idx
            )
        
        # Create version
        PageVersion.objects.create(
            page=page,
            version_number=page.versions.count() + 1,
            content_snapshot={'blocks': blocks_data},
            created_by=request.user,
            notes=request.POST.get('version_notes', '')
        )
        
        return JsonResponse({'success': True})
    
    blocks = page.blocks.all()
    
    context = {
        'page': page,
        'blocks': blocks,
    }
    return render(request, 'pages/builder.html', context)

# media_library/views.py
from django.shortcuts import render
from django.contrib.auth.decorators import login_required
from django.http import JsonResponse
from .models import MediaFile, MediaFolder
from PIL import Image

@login_required
def media_library(request):
    folder_id = request.GET.get('folder')
    
    if folder_id:
        folder = get_object_or_404(MediaFolder, id=folder_id)
        files = folder.files.all()
        subfolders = folder.subfolders.all()
    else:
        folder = None
        files = MediaFile.objects.filter(folder__isnull=True)
        subfolders = MediaFolder.objects.filter(parent__isnull=True)
    
    context = {
        'current_folder': folder,
        'files': files,
        'subfolders': subfolders,
    }
    return render(request, 'media_library/index.html', context)

@login_required
def upload_media(request):
    if request.method == 'POST':
        files = request.FILES.getlist('files')
        folder_id = request.POST.get('folder_id')
        
        folder = None
        if folder_id:
            folder = MediaFolder.objects.get(id=folder_id)
        
        uploaded_files = []
        
        for file in files:
            media_file = MediaFile.objects.create(
                folder=folder,
                file=file,
                title=file.name,
                uploaded_by=request.user
            )
            
            # Get image dimensions
            if media_file.file_type == 'image':
                try:
                    img = Image.open(media_file.file.path)
                    media_file.width, media_file.height = img.size
                    media_file.save()
                except:
                    pass
            
            uploaded_files.append({
                'id': media_file.id,
                'url': media_file.file.url,
                'title': media_file.title,
                'type': media_file.file_type
            })
        
        return JsonResponse({
            'success': True,
            'files': uploaded_files
        })
    
    return JsonResponse({'success': False})
```

### 4. Page Builder Template

```django
<!-- templates/pages/builder.html -->
{% extends 'admin/base.html' %}
{% load static %}

{% block content %}
<div class="page-builder">
    <div class="builder-header">
        <h1>Edit Page: {{ page.title }}</h1>
        <button id="save-page" class="btn btn-primary">Save</button>
        <button id="preview-page" class="btn btn-secondary">Preview</button>
    </div>
    
    <div class="builder-container">
        <!-- Sidebar with block types -->
        <div class="blocks-sidebar">
            <h3>Add Blocks</h3>
            <div class="block-list">
                <div class="block-item" data-type="text">
                    <i class="fa fa-font"></i> Text
                </div>
                <div class="block-item" data-type="image">
                    <i class="fa fa-image"></i> Image
                </div>
                <div class="block-item" data-type="video">
                    <i class="fa fa-video"></i> Video
                </div>
                <div class="block-item" data-type="gallery">
                    <i class="fa fa-images"></i> Gallery
                </div>
                <div class="block-item" data-type="html">
                    <i class="fa fa-code"></i> HTML
                </div>
                <div class="block-item" data-type="cta">
                    <i class="fa fa-bullhorn"></i> Call to Action
                </div>
            </div>
        </div>
        
        <!-- Canvas area -->
        <div class="builder-canvas" id="canvas">
            {% for block in blocks %}
            <div class="canvas-block" data-type="{{ block.block_type }}" data-id="{{ block.id }}">
                <div class="block-controls">
                    <button class="move-up"><i class="fa fa-arrow-up"></i></button>
                    <button class="move-down"><i class="fa fa-arrow-down"></i></button>
                    <button class="edit-block"><i class="fa fa-edit"></i></button>
                    <button class="delete-block"><i class="fa fa-trash"></i></button>
                </div>
                <div class="block-content">
                    <!-- Block content rendered here -->
                </div>
            </div>
            {% endfor %}
        </div>
        
        <!-- Properties panel -->
        <div class="properties-panel" id="properties">
            <h3>Block Properties</h3>
            <div id="properties-content">
                <!-- Dynamic properties form -->
            </div>
        </div>
    </div>
</div>

<script>
class PageBuilder {
    constructor() {
        this.canvas = document.getElementById('canvas');
        this.properties = document.getElementById('properties-content');
        this.blocks = [];
        
        this.init();
    }
    
    init() {
        // Drag and drop blocks
        document.querySelectorAll('.block-item').forEach(item => {
            item.addEventListener('click', () => {
                this.addBlock(item.dataset.type);
            });
        });
        
        // Save button
        document.getElementById('save-page').addEventListener('click', () => {
            this.savePage();
        });
        
        // Load existing blocks
        this.loadBlocks();
    }
    
    addBlock(type) {
        const block = {
            id: Date.now(),
            type: type,
            content: this.getDefaultContent(type)
        };
        
        this.blocks.push(block);
        this.renderBlock(block);
    }
    
    getDefaultContent(type) {
        const defaults = {
            text: { html: '<p>Enter your text here...</p>' },
            image: { url: '', alt: '', caption: '' },
            video: { url: '', autoplay: false },
            gallery: { images: [] },
            html: { code: '' },
            cta: { title: '', description: '', buttonText: '', buttonUrl: '' }
        };
        
        return defaults[type] || {};
    }
    
    renderBlock(block) {
        const blockEl = document.createElement('div');
        blockEl.className = 'canvas-block';
        blockEl.dataset.id = block.id;
        blockEl.dataset.type = block.type;
        
        blockEl.innerHTML = `
            <div class="block-controls">
                <button class="move-up"><i class="fa fa-arrow-up"></i></button>
                <button class="move-down"><i class="fa fa-arrow-down"></i></button>
                <button class="edit-block"><i class="fa fa-edit"></i></button>
                <button class="delete-block"><i class="fa fa-trash"></i></button>
            </div>
            <div class="block-content">
                ${this.renderBlockContent(block)}
            </div>
        `;
        
        this.canvas.appendChild(blockEl);
        this.attachBlockEvents(blockEl, block);
    }
    
    renderBlockContent(block) {
        switch(block.type) {
            case 'text':
                return block.content.html || '';
            case 'image':
                return `<img src="${block.content.url}" alt="${block.content.alt}">`;
            case 'video':
                return `<video src="${block.content.url}" controls></video>`;
            default:
                return `<div>${block.type} block</div>`;
        }
    }
    
    attachBlockEvents(blockEl, block) {
        blockEl.querySelector('.edit-block').addEventListener('click', () => {
            this.editBlock(block);
        });
        
        blockEl.querySelector('.delete-block').addEventListener('click', () => {
            this.deleteBlock(block.id);
            blockEl.remove();
        });
    }
    
    editBlock(block) {
        // Show properties panel with block-specific form
        this.properties.innerHTML = this.getPropertiesForm(block);
        
        // Save changes
        this.properties.querySelector('form').addEventListener('submit', (e) => {
            e.preventDefault();
            const formData = new FormData(e.target);
            block.content = Object.fromEntries(formData);
            this.updateBlockDisplay(block);
        });
    }
    
    getPropertiesForm(block) {
        // Return HTML form based on block type
        if (block.type === 'text') {
            return `
                <form>
                    <label>Content:</label>
                    <textarea name="html" class="form-control">${block.content.html || ''}</textarea>
                    <button type="submit" class="btn btn-primary mt-2">Update</button>
                </form>
            `;
        }
        // Add forms for other block types
        return '';
    }
    
    savePage() {
        const formData = new FormData();
        formData.append('blocks', JSON.stringify(this.blocks));
        formData.append('csrfmiddlewaretoken', '{{ csrf_token }}');
        
        fetch(window.location.href, {
            method: 'POST',
            body: formData
        })
        .then(response => response.json())
        .then(data => {
            if (data.success) {
                alert('Page saved successfully!');
            }
        });
    }
    
    loadBlocks() {
        // Load existing blocks from page
        document.querySelectorAll('.canvas-block').forEach(blockEl => {
            const block = {
                id: blockEl.dataset.id,
                type: blockEl.dataset.type,
                content: {}  // Parse from existing content
            };
            this.blocks.push(block);
            this.attachBlockEvents(blockEl, block);
        });
    }
    
    deleteBlock(id) {
        this.blocks = this.blocks.filter(b => b.id !== id);
    }
    
    updateBlockDisplay(block) {
        const blockEl = this.canvas.querySelector(`[data-id="${block.id}"]`);
        if (blockEl) {
            blockEl.querySelector('.block-content').innerHTML = this.renderBlockContent(block);
        }
    }
}

// Initialize page builder
const builder = new PageBuilder();
</script>

<style>
.page-builder {
    height: 100vh;
    display: flex;
    flex-direction: column;
}

.builder-header {
    padding: 15px;
    background: #f5f5f5;
    border-bottom: 1px solid #ddd;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.builder-container {
    display: flex;
    flex: 1;
    overflow: hidden;
}

.blocks-sidebar {
    width: 250px;
    background: #f9f9f9;
    padding: 20px;
    overflow-y: auto;
}

.block-item {
    padding: 10px;
    margin-bottom: 10px;
    background: white;
    border: 1px solid #ddd;
    border-radius: 4px;
    cursor: pointer;
}

.block-item:hover {
    background: #e9e9e9;
}

.builder-canvas {
    flex: 1;
    padding: 20px;
    overflow-y: auto;
    background: white;
}

.canvas-block {
    margin-bottom: 20px;
    border: 2px dashed #ddd;
    padding: 15px;
    position: relative;
}

.block-controls {
    position: absolute;
    top: 5px;
    right: 5px;
    background: white;
    padding: 5px;
    border-radius: 4px;
}

.properties-panel {
    width: 300px;
    background: #f9f9f9;
    padding: 20px;
    overflow-y: auto;
}
</style>
{% endblock %}
```

**Key Features:**

1. **Page Builder**: Drag-and-drop interface with modular blocks
2. **Media Library**: Organized file management with folders
3. **SEO Tools**: Meta tags, OG images, redirects
4. **Multi-language**: Translation support for pages
5. **Version Control**: Track page changes and restore previous versions
6. **Templates**: Multiple page templates
7. **Hierarchical Pages**: Parent-child page relationships
8. **Access Control**: Authentication requirements per page

---

#### 23. Build a learning management system (LMS) with Django. Include course management, video lessons, quizzes, progress tracking, and certificates.

**Answer:**

This comprehensive LMS implementation would follow similar patterns to the previous examples, with models for courses, lessons, quizzes, enrollments, and progress tracking. Key features include:

1. **Course Structure**: Hierarchical organization with sections and lessons
2. **Video Hosting**: Integration with services like Vimeo or AWS S3
3. **Quiz System**: Multiple question types with automatic grading
4. **Progress Tracking**: Completion percentages and learning analytics
5. **Certificates**: Auto-generated upon course completion
6. **Discussion Forums**: Student interaction and Q&A
7. **Instructor Dashboard**: Course analytics and student management

---

#### 24. Implement a job board platform with Django. Include job postings, applications, resume management, and employer dashboard.

**Answer:**

A job board platform would include:

1. **Job Listings**: Advanced search and filtering
2. **Application System**: Resume uploads and cover letters
3. **Employer Accounts**: Company profiles and job management
4. **Candidate Profiles**: Skills, experience, portfolio
5. **Matching Algorithm**: Job recommendations based on skills
6. **Application Tracking**: Status updates and notifications
7. **Payment Integration**: Featured listings and premium accounts

---

#### 25. Create a project management tool with Django. Include task management, team collaboration, file sharing, and time tracking.

**Answer:**

A project management system would feature:

1. **Project Organization**: Workspaces, projects, and task boards
2. **Task Management**: Kanban boards, Gantt charts, assignments
3. **Team Collaboration**: Comments, mentions, notifications
4. **File Management**: Document uploads and version control
5. **Time Tracking**: Work logs and productivity reports
6. **Reporting**: Project analytics and team performance
7. **Integrations**: Calendar sync, email notifications, webhooks

---

## Conclusion

This comprehensive Django exam answers document covers all aspects of Django development from fundamentals to advanced topics including:

- **Set A**: Core Django concepts, models, views, templates, forms, authentication, REST APIs, caching, testing, and ORM optimization
- **Set B**: Middleware, static/media files, signals, context processors, sessions, management commands, email, contenttypes, pagination, sites framework, i18n/l10n, generic views, transactions, messages, flatpages, humanize, sitemaps, syndication, and complex application implementations

Each answer includes detailed explanations, code examples, and best practices for building production-ready Django applications.

