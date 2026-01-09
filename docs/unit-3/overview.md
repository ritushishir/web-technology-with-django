# Advanced Concepts

* URL Dispatching
* View
* Template System

## Learning Objectives

By the end of this chapter, you will be able to:

- Architect Sophisticated URL Schemes

  - Implement modular URL configurations using inclusion and namespaces to prevent naming collisions in large-scale projects.

- Implement Class-Based Views (CBVs)

  - Transition from functional programming to object-oriented views, utilizing Django’s generic views to handle common patterns like list displays and detail pages with minimal code.

- Extend the Template Engine
  
  - Use Context Processors to make data globally available across templates

  - Create custom template loaders for specialized file-handling needs.

- Generate Dynamic Non-HTML Content

  - Configure Django to serve more than just web pages, specifically mastering the programmatic generation of CSV files and PDF documents.

- Optimize for SEO and Syndication

  - Deploy the Sitemap and Syndication (RSS/Atom) frameworks to ensure your content is easily indexed by search engines and consumable by feed readers.

## Topics Covered

## 1. Advanced URL Dispatching

Tricks and techniques for managing URL patterns in Django.

### [Including Other URLconfs](./advanced-url-dispatching.md#urlconf-tricks)

### [Naming Patterns for Easier Reverse Lookups](./advanced-url-dispatching.md#naming-patterns-for-easier-reverse-lookups)

### [Passing Extra Options to View Functions](./advanced-url-dispatching.md#passing-extra-options-to-view-functions)

---

## 2. Working with Generic Views

### [Class-Based Views vs. Function-Based Views](./working-with-generic-views.md#cbvs-and-fbvs)

### [Using Generic Views](./working-with-generic-views.md#using-generic-views)

### [Generic Views of Objects](./working-with-generic-views.md#generic-views-of-objects)

### [Extending Generic Views](./working-with-generic-views.md#extending-generic-views)

---

## 3. Deep Dive: Django Template System

### [Template Language Review](./deep-dive-django-templates.md#template-language-review)

### [Request Context and Context Processors](./deep-dive-django-templates.md#request-context-and-context-processors)

### [Inside Template Loading](./deep-dive-django-templates.md#inside-template-loading)

### [Extending the Template System](./deep-dive-django-templates.md#extending-the-template-system)

### [Writing Custom Template Loaders](./deep-dive-django-templates.md#writing-custom-template-loaders)

### [Using the Built-in Template Reference](./deep-dive-django-templates.md#using-the-built-in-template-reference)

### [Configuring the Template System in Standalone Mode](./deep-dive-django-templates.md#configuring-the-template-system-in-standalone-mode)

---

## 4. Specialized Content & Outputs

### [The Basics: Views and MIME-types](./specialized-content-and-output.md#the-basics-views-and-mime-types)

### [Producing CSV Output](./specialized-content-and-output.md#producing-csv)

### [Generating PDFs](./specialized-content-and-output.md#generating-pdfs)

### [Other Possibilities](./specialized-content-and-output.md#other-possibilities)

---

## 5. Metadata & Discovery Frameworks

### [The Syndication Feed Framework](./metadata-and-discovery-frameworks.md#syndication-feed-framework)

### [The Sitemap Framework](./metadata-and-discovery-frameworks.md#sitemap-framework)

### [Best Practices for Metadata and Discovery](./metadata-and-discovery-frameworks.md#best-practices-for-metadata-and-discovery)
