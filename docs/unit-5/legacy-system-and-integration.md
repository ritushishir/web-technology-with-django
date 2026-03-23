# Legacy Systems and Integration

Enterprise environments frequently require integrating Django to coexist with pre-existing systems. 

This section covers strategies for connecting Django to legacy databases, authentication systems, and web applications.

## Integrating with a Legacy Database {#integrating-with-a-legacy-database}


### 🔍 Using inspectdb

Automatically generate models from an existing schema:

```bash
python manage.py inspectdb > models.py
```

#### 🗄️ What is inspectdb in Django?

`inspectdb` is a Django management command that automatically generates Django model definitions by inspecting an existing database schema.


### ⚠️ Handling Non-Standard Schemas

Legacy DB issues:

* Non-integer primary keys
* Composite keys
* Missing constraints
* Unconventional naming

Example:

```python
class LegacyUser(models.Model):
    user_code = models.CharField(primary_key=True, max_length=20)

    class Meta:
        managed = False
        db_table = 'legacy_songs'
```

🧠 managed = False

This means preventing Django migrations from modifying the table.

* Django will NOT create this table
* Django will NOT modify this table via migrations
* Django will NOT delete this table



Used when:

* DB is controlled externally
* Schema must not change


## 🔐 Integrating with an Authentication System {#integrating-with-an-authentication-system}

Django supports pluggable authentication backends.

Examples:

* LDAP
* OAuth2
* Remote Header Authentication (SSO)

-----

**Writing a Custom Authentication Backend**

```python
from django.contrib.auth.backends import BaseBackend

class CustomAuthBackend(BaseBackend):
    def authenticate(self, request, username=None, password=None):
        # External verification logic
        return user_object
```

Add to settings.py:

```python
AUTHENTICATION_BACKENDS = [
    'myapp.backends.CustomAuthBackend',
]
```

## Integrating with Legacy Web Applications {#integrating-with-legacy-web-applications}

1️⃣ Reverse Proxy Setup

* Use Nginx or Apache to route requests to both Django and legacy apps.
* Example: `/app/` → Django, `/legacy/` → Old app.

2️⃣ Shared Session Management

* Use a common session store (e.g., Redis) to share authentication state.