# Middleware Architecture

## Middleware Overview {#middleware-overview}

### 📌 Request–Response Lifecycle in Django

Django processes every HTTP request through a layered pipeline:

```code
Client → URL Resolver → View → Template → Response
```

Middleware sits at the heart of this pipeline, allowing you to intercept and modify requests and responses at various stages. 

```code
Client → Middleware → URL Resolver → View → Template → Middleware → Response
```


Each middleware component can perform actions before the view is called, after the view returns a response, or even when an exception occurs.

• Incoming requests - modify request objects, perform authentication, or handle sessions.

• View execution - process the view function before it executes, or modify the response after it executes.

• Exceptions - catch and handle exceptions raised during view processing.

• Outgoing responses - modify response objects, add headers, or perform content compression.

* How Middleware acts as a "hook" system for Django’s input and output.

### Middleware as a "Hook" System

A hook is a predefined interception point in execution where custom logic can run.

Middleware provides hooks at critical points in the request/response lifecycle:

1. **Before the view is called**: `process_request(request)` allows you to modify the request or return a response directly.
2. **After the view is called**: `process_response(request, response)` lets you modify the response before it’s sent to the client.
3. **When an exception occurs**: `process_exception(request, exception)` allows you to handle exceptions raised during view processing.
4. **When a template response is returned**: `process_template_response(request, response)` lets you modify the response before rendering the template.
5. **When the middleware is called**: `__call__(self, request)` is the main entry point for processing requests and responses.
6. **When the middleware is initialized**: `__init__(self, get_response)` is called once when the server starts, allowing you to set up any necessary state or configuration.


### 🧪 Execution Order

```code
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


## Middleware Installation {#middleware-installation}


### 📌 Configuration in settings.py

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.gzip.GZipMiddleware',
    'myapp.middleware.CustomMiddleware',
]
```

### ⚠️ Importance of Order

Middleware execution order is top-down for request, bottom-up for response.

Request Flow:

```Security → Common → Custom → View```

Response Flow:

```View → Custom → Common → Security```

## Middleware Methods {#middleware-methods}

### Basic Structure

```python
class CustomMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # Before view
        response = self.get_response(request)
        # After view
        return response
```


### 🔎 Advanced Hook Points

Django supports optional hook methods:

1️⃣ ```process_view(self, request, view_func, view_args, view_kwargs)```

Executed before the view runs.

Use cases:

* Logging
* Access control
* Modifying view arguments

2️⃣ ```process_exception(self, request, exception)``

Executed if the view raises an exception.

Use cases:

* Custom error handling
* Reporting exceptions to monitoring services

3️⃣ ```process_template_response(self, request, response)```

Executed if the view returns a TemplateResponse.

Use cases:

* Modifying the context data before rendering
* Changing the template used for rendering

## 🏗️ Built-in Middleware Deep Dive {#built-in-middleware}

### 🔐 SecurityMiddleware

Purpose: 

Enforces security best practices.

Key features:

* HTTPS redirect (SECURE_SSL_REDIRECT)
* HSTS headers
* Secure cookies
* XSS protection

Critical for production environments.

### 🌐 CommonMiddleware

Handles:

* URL normalization (APPEND_SLASH)
* ETag handling
* Conditional GET support

Improves consistency and HTTP compliance.

### 🚀 GZipMiddleware

Compresses responses > 200 bytes.

Benefits:

* Reduced bandwidth
* Faster client load times

Caution:

* Avoid compressing already compressed data (images, PDFs).