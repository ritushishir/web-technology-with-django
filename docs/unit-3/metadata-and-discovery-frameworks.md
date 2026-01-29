
# Metadata & Discovery Frameworks

- making your content discoverable through metadata, syndication feeds, and sitemaps.
- Django's built-in frameworks to manage metadata and enhance content discovery.

## Understanding Metadata in Django

- data about data
- in a web context, tells search engines and social media platforms what your content is about before they even load it

**The Model `Meta` Class**
- defining metadata for your models
- The `class Meta` inside a Django model allows you to specify metadata options like ordering, verbose names, and permissions.

```python
from django.db import models
class Song(models.Model):
    title = models.CharField(max_length=100)
    artist = models.CharField(max_length=100)

    class Meta:
        ordering = ['title']
        verbose_name = 'Song'
        verbose_name_plural = 'Songs'
        get_latest_by = 'release_date'
```

**SEO Metadata**

While the Model Meta handles internal metadata, SEO metadata (Meta tags in HTML) is crucial for discovery:

* **Description tags**: Improve Click-Through Rate (CTR) on Google.
* **OpenGraph (OG) tags**: Control how your links look when shared on WhatsApp, X (Twitter), or LinkedIn.

## The Syndication Feed Framework {#syndication-feed-framework}


**Setting up a Feed Class**
You define a feed by creating a subclass of `django.contrib.syndication.views.Feed` and specifying attributes like `title`, `link`, and `description`.


```python
from django.contrib.syndication.views import Feed
from .models import Song
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

**Linking to URLs**
You hook up your feed class to a URL pattern in your `urls.py`.

```python
from django.urls import path
from .feeds import LatestSongsFeed
urlpatterns = [
    path('feeds/latest/', LatestSongsFeed(), name='latest-songs-feed'),
]
```

**Customizing Feed Content**
- Adding additional metadata to feed items (like author, categories).
- Formatting dates and content for better readability in feed readers.
- Implementing custom feed item methods for more control over content.
- Handling edge cases and errors in feed generation.
- Creating feed classes and linking them to views.
- Customizing feed content and structure.
- Handling edge cases and errors in feed generation.


## The Sitemap Framework {#sitemap-framework}

- Sitemap: 
  - An XML file that lists all the important URLs on your site 
  - Help search engines crawl your site more intelligently 
  - Tells which pages are available, how often they are updated, and their relative importance.
- Django's `sitemaps` framework to generate sitemaps automatically.

---

**Configuring Sitemaps**

Example: Basic Sitemap Setup

```python
from django.contrib.sitemaps import Sitemap
from .models import Song
class SongSitemap(Sitemap):
    changefreq = "weekly"
    priority = 0.8

    def items(self):
        return Song.objects.all()

    def lastmod(self, obj):
        return obj.updated_at
```

**Integration**

To serve the sitemap, you add it to your URL patterns. Django will then render a standard `sitemap.xml` at that path.

```python
from django.contrib.sitemaps.views import sitemap
from .sitemaps import SongSitemap
sitemaps = {
    'songs': SongSitemap,
}
urlpatterns = [
    path('sitemap.xml', sitemap, {'sitemaps': sitemaps}, name='sitemap'),
]
```

## Best Practices for Metadata and Discovery {#best-practices-for-metadata-and-discovery}

- Goal is to make your content easily discoverable and well-represented across platforms.
  - Actually `FOUND` not just `ONLINE`

| Strategy | Action |
|----------|--------|
| Canonical URLs | Always use the get_absolute_url() method in models to ensure sitemaps and feeds point to the correct link. |
| Robots.txt | Use a robots.txt file to tell bots which parts of the site to ignore (e.g., /admin/). |
| Rich Snippets | Use Schema.org metadata (JSON-LD) to help Google show star ratings or prices in search results. |
| Validation | Use the W3C Feed Validator to ensure your RSS feeds aren't broken. |