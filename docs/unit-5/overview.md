# Django Middleware and Advanced Admin Customization

## 1. Middleware Architecture

### [Middleware Overview](./middleware-architecture.md#middleware-overview)

* Understanding the request/response lifecycle.
* How Middleware acts as a "hook" system for Django’s input and output.

### [Middleware Installation](./middleware-architecture.md#middleware-installation)

* Configuring the `MIDDLEWARE` setting in `settings.py`.
* Understanding the importance of plugin order.

### [Middleware Methods](./middleware-architecture.md#middleware-methods)

* Defining custom logic using `__call__`.
* Hooking into specific stages: `process_view`, `process_exception`, and `process_template_response`.

### [Built-in Middleware](./middleware-architecture.md#built-in-middleware)

* Deep dive into essential defaults: `CommonMiddleware`, `SecurityMiddleware`, and `GZipMiddleware`.

---

## 2. Legacy Systems and Integration

### [Integrating with a Legacy Database](./legacy-system-and-integration.md#integrating-with-a-legacy-database)

* Using `inspectdb` to automatically generate Django models from an existing schema.
* Handling non-standard primary keys and unmanaged tables (`managed = False`).

### [Integrating with an Authentication System](./legacy-system-and-integration.md#integrating-with-an-authentication-system)

* Connecting Django to external Auth providers (LDAP, OAuth, or Remote Headers).
* Writing custom Authentication Backends.

### [Integrating with Legacy Web Applications](./legacy-system-and-integration.md#integrating-with-legacy-web-applications)

* Co-existing with non-Django apps on the same server.
* Shared session management and reverse proxy strategies.

---

## 3. Advanced Django Admin

### [The Zen of Admin](./advanced-django-admin.md#the-zen-of-admin)

* Understanding the philosophy: The Admin is for internal staff, not end-users.
* When to use the Admin vs. when to build a custom dashboard.

### [Customizing Admin Templates](./advanced-django-admin.md#customizing-admin-templates)

* Overriding default Admin HTML for branding or functional tweaks.
* Utilizing the `admin/base_site.html` override.

### [Creating Custom Admin Views](./advanced-django-admin.md#creating-custom-admin-views)

* Adding completely new pages to the Admin interface.
* Mapping custom URLs within the `AdminSite` class.

### [Overriding Built-in Views](./advanced-django-admin.md#overriding-built-in-views)

* Customizing the behavior of `add_view`, `change_view`, or `changelist_view`.
* Adding custom validation or redirect logic during the admin save process.
