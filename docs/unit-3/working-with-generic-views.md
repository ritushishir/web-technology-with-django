# Working with Generic Views

- Generic views 
    - built-in classes in Django 
    - provide common web development patterns, reducing the amount of boilerplate code you need to write.

- Class-Based Views (CBVs)
    - foundation of generic views
    - allows you to create views as Python classes rather than functions.

- Examples of common tasks handled by generic views include:
    - displaying a list of items, 
    - showing details of a single item, 
    - handling forms
    - and more - without having to write repetitive code.

* Reducing boilerplate code makes your codebase cleaner, more maintainable, and easier to understand.


## Class-Based Views (CBVs) and Function-Based Views (FBVs) {#cbvs-and-fbvs}

- both ultimately serve the same purpose: to handle `HTTPRequest` and return `HTTPResponse`.

## Function-Based Views (FBVs)

- FBVs are defined as simple Python functions that take a request and return a response.
- simple, straightforward, and easy to understand for small views.

- **Pros**
    - Easy to read and understand for simple views.
    - Direct control over the request and response process.

- **Cons**
    - Can lead to repetitive code for common patterns.
    - Can lead to "spaghetti code" if the logic is complex

```python
from django.shortcuts import render, redirect
from .models import Song

def upload_song(request):
    if request.method == 'POST':
        # Manual form handling logic
        form = SongForm(request.POST, request.FILES)
        if form.is_valid():
            form.save()
            return redirect('song-list')
    else:
        form = SongForm()
    
    return render(request, 'song_form.html', {'form': form})
```

## Class-Based Views (CBVs)

- Python classes that encapsulate view logic.
- Provide a structured way to organize view code using methods and attributes.
- Promote code reuse through inheritance and mixins.
- Built-in generic views handle common tasks, allowing you to focus on application-specific logic.
- Instead of if `request.method == 'POST'`, you define methods like `get()` and `post()

- **Pros**
    - Promote code reuse and organization.
    - Easier to extend and customize through inheritance.
    - Built-in generic views for common tasks.

- **Cons**
    - Slightly steeper learning curve for beginners.
    - "hidden" logic happens behind the scenes in parent classes

```python
from django.views import View

class UploadSong(View):
    def get(self, request):
        form = SongForm()
        return render(request, 'song_form.html', {'form': form})

    def post(self, request):
        form = SongForm(request.POST, request.FILES)
        if form.is_valid():
            form.save()
            return redirect('song-list')
        return render(request, 'song_form.html', {'form': form})
```

## Using Generic Views {#using-generic-views}

- `django.views.generic` module contains various generic views that can be used to handle common tasks.

- **Don't Repeat Yourself (DRY) principle**: providing reusable components for common tasks.

## Generic Views of Objects

The most common views include:

1. ListView
2. DetailView
3. CreateView/UpdateView/DeleteView

### ListView

Used to display a list of objects from a model

```python
class SongListView(ListView):
    model = Song
    template_name = 'songs/song_list.html'
    context_object_name = 'songs'  # Custom context name
```

### DetailView

Used to display details of a single object.

```python
class SongDetailView(DetailView):
    model = Song
    template_name = 'songs/song_detail.html'
    context_object_name = 'song'  # Custom context name
```

### CreateView / UpdateView / DeleteView

Used to handle C.R.U.D. operations.

```python
from django.views.generic import CreateView
from .models import Song
class SongCreateView(CreateView):
    model = Song
    fields = ['title', 'artist', 'genre']
    template_name = 'songs/song_form.html'
    success_url = '/songs/'  # Redirect after successful creation
```

## Extending Generic Views {#extending-generic-views}

To make generic views more powerful, you can override methods and attributes.

- **Overriding `get_context_data`**: To add extra context variables to the template.

```python
class SongListView(ListView):
    model = Song
    template_name = 'songs/song_list.html'
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['extra_info'] = 'This is some extra information'
        return context
```

- **Customizing Querysets**: You can filter or modify the queryset returned by the view.

```python
class SongListView(ListView):
    model = Song
    template_name = 'songs/song_list.html'
    def get_queryset(self):
        return Song.objects.filter(is_published=True)
```

- **Handling Forms with Generic Views**: You can use `CreateView` and `UpdateView` to handle forms for creating and updating objects.

```python
from django.views.generic import CreateView
from .models import Song
class SongCreateView(CreateView):
    model = Song
    fields = ['title', 'artist', 'genre']
    template_name = 'songs/song_form.html'
    success_url = '/songs/'  # Redirect after successful creation
```

- **Customizing Form Behavior**: You can override form methods to add custom validation or processing.

```python
class SongCreateView(CreateView):
    model = Song
    fields = ['title', 'lyrics', 'release_date']
    template_name = 'songs/song_form.html'
    def form_valid(self, form):
        # Custom processing before saving
        # form.instance.created_by = self.request.user
        return super().form_valid(form)
```

## The Example - Song

??? Mini-Challenge - Implement Generic Views
    Build a simple Django app to manage a collection of songs using generic views. Implement the following features:

    - List all songs using `ListView`.

    - View details of a single song using `DetailView`.

    - Add new songs using `CreateView`.

    - Edit existing songs using `UpdateView`.

    - Delete songs using `DeleteView`.
