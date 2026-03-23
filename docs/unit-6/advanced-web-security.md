# Advanced Web Security

## Web Security Themes {#web-security-themes}

### The Philosophy of "Defense in Depth"

- Never rely on a single line of defense!

Defense in Depth is a layered security approach that provides multiple levels of protection against threats. In web applications, this means implementing security controls at every layer of the application stack.

**Core Principles:**

1. **Multiple Layers of Protection**: If one security control fails, others remain to protect the application
2. **Diverse Security Controls**: Use different types of security measures (authentication, authorization, input validation, etc.)
3. **Redundancy**: Critical security functions should have backup mechanisms
4. **Fail-Safe Defaults**: Systems should default to secure configurations

**Implementation in Django:**

1. **Allowed Hosts**

        ```python
        # settings.py - Multiple layers of security

        # Layer 1: Environment-based security
        DEBUG = False
        ALLOWED_HOSTS = ['example.com', 'www.example.com']
        ```

2. **Transport Security Policy**

    - protecting your data while it is in motion between the user's browser and your servevr
    - without transport security, data travels over the open internet like a postcard - anyone can read it, or worse, change the message before it reaches its destination

    Three goals of transport security:

    1. **Encryption** - ensures that data is unreadable to anyone who intercepts it
    2. **Authentication** - ensures that the data is coming from a trusted source
    3. **Integrity** - ensures that the data has not been tampered with

            
            # Layer 2: transport security layer
            SECURE_SSL_REDIRECT = True
            SECURE_HSTS_SECONDS = 31536000
            SECURE_HSTS_INCLUDE_SUBDOMAINS = True
            SECURE_HSTS_PRELOAD = True
            

3. **CSRF & Form Integrity**
    - identify verification

            CSRF_COOKIE_SECURE = True // ensures the secret token used to validate forms is only sent over encrypted HTTPS
            CSRF_COOKIE_HTTPONLY = True // prevents JavaScript from accessing the token
            CSRF_TRUSTED_ORIGINS = ['https://example.com'] // specifies which origins are allowed to send CSRF tokens

4. **Session & Cookie Hardening**
    - Key management

            # Layer 4: Session security
            SESSION_COOKIE_SECURE = True
            SESSION_COOKIE_HTTPONLY = True
            SESSION_COOKIE_SAMESITE = 'Strict'
            SESSION_COOKIE_AGE = 3600  # 1 hour


5. **Authentication and Authorization**
    - Vault door

            AUTH_PASSWORD_VALIDATORS = [
                {
                    'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
                    'OPTIONS': {'min_length': 12},
                },
                {
                    'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
                },
                {
                    'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
                },
            ]

6. **Database Security**

            DATABASES = {
                'default': {
                    'ENGINE': 'django.db.backends.postgresql',
                    'NAME': 'myapp',
                    'USER': 'myapp_user',
                    'PASSWORD': os.environ.get('DB_PASSWORD'),  # Never hardcode
                    'HOST': 'localhost',
                    'PORT': '5432',
                    'OPTIONS': {
                        'sslmode': 'require',  # Enforce SSL
                    },
                }
            }



**Security Headers Implementation:**

- front-gate instructions of the application
- instructions Django sends to the user's browser, telling it how to behave while visiting your application
- security headers are part of the response's metadata - `I am a secure site; here are the rules you must follow while you are here`

```python
# middleware.py
class SecurityHeadersMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)
        
        # Content Security Policy
        csp = (
            "default-src 'self'; "
            "script-src 'self' 'unsafe-inline' https://cdn.example.com; "
            "style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; "
            "font-src 'self' https://fonts.gstatic.com; "
            "img-src 'self' data: https:; "
            "connect-src 'self' https://api.example.com; "
            "frame-ancestors 'none'; "
            "base-uri 'self'; "
            "form-action 'self'"
        )
        response['Content-Security-Policy'] = csp
        
        # Additional security headers
        response['X-Content-Type-Options'] = 'nosniff'
        response['X-Frame-Options'] = 'DENY'
        response['X-XSS-Protection'] = '1; mode=block'
        response['Referrer-Policy'] = 'strict-origin-when-cross-origin'
        response['Permissions-Policy'] = (
            'geolocation=(), '  
            'microphone=(), '
            'camera=(), '
            'payment=()'
        )
        
        return response

# Add to MIDDLEWARE in settings.py
MIDDLEWARE = [
    # ... other middleware
    'myapp.middleware.SecurityHeadersMiddleware',
]
```

## Common Vulnerabilities & Mitigations {#common-vulnerabilities-and-mitigations}

### **SQL Injection: How the Django ORM protects against malicious queries**

- malicious SQL code is inserted into input fields to manipulate database queries. 
- Django's ORM provides robust protection against SQL injection.

- **How Django Protects Against SQL Injection:**

    1. **Query Parameterization**: All queries use parameterized statements
    2. **Input Validation**: Automatic escaping of user input
    3. **Type Safety**: Strong typing prevents injection attempts

- **Safe ORM Usage Examples:**


        # ✅ SAFE: Using ORM methods with parameters
        def get_user_songs(artist_id, status):
            return Song.objects.filter(
                artist_id=artist_id,
                status=status
            )

        # ✅ SAFE: Using Q objects for complex queries
        from django.db.models import Q

        def search_songs(query, artist_id):
            return Song.objects.filter(
                Q(title__icontains=query) | Q(content__icontains=query),
                artist_id=artist_id
            )

        # ✅ SAFE: Using F() expressions for field operations
        from django.db.models import F

        def increment_views(song_id):
            Song.objects.filter(id=song_id).update(views=F('views') + 1)

        # ✅ SAFE: Using raw() with parameterized queries
        def get_custom_report(start_date, end_date):
            query = """
                SELECT category, COUNT(*) as count
                FROM songs 
                WHERE created_at BETWEEN %s AND %s
                GROUP BY category
            """
            return Song.objects.raw(query, [start_date, end_date])

- **Dangerous Practices to Avoid:**

        # ❌ DANGEROUS: String formatting in queries
        def get_song_items_unsafe(artist_id):
            # NEVER DO THIS - Vulnerable to SQL injection
            query = f"SELECT * FROM songs WHERE artist_id = {artist_id}"
            return Song.objects.raw(query)

### **XSS (Cross-Site Scripting): Automatic HTML escaping in templates**

- Cross-Site Scripting (XSS) attacks occur when malicious scripts are injected into web pages viewed by other users. Django provides automatic XSS protection in templates.

- **Django's XSS Protection Mechanisms:**

    1. **Auto-escaping**: All template variables are automatically HTML-escaped
    2. **Context-aware escaping**: Different escaping for HTML, CSS, and JavaScript contexts
    3. **Safe string handling**: Explicit marking of safe content
    4. **Content Security Policy (CSP)**: HTTP header that tells the browser which sources of scripts, styles, and images are trusted


### **CSRF (Cross-Site Request Forgery): Token validation for state-changing requests**

- CSRF attacks force authenticated users to submit unwanted actions to web applications they're currently authenticated to. Django provides robust CSRF protection.

- **Django's CSRF Protection Mechanism:**

    - **CSRF Tokens**: Unique tokens for each user session
    - **SameSite Cookies**: Prevent cross-site requests
    - **Origin Validation**: Verify request origins
    - **Double Submit Cookie**: Additional validation layer

- **CSRF Protection in Forms:**


        <!-- ✅ SAFE: Include CSRF token in forms -->
        <form method="post" action="{% url 'create_song' %}">
            ........
            {% csrf_token %}
            ........
        </form>

        <!-- ✅ SAFE: AJAX requests with CSRF token -->
        <script>
        function submitPost(data) {
            const csrfToken = getCookie('csrftoken');
            
            fetch('{% url "create_song" %}', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'X-CSRFToken': csrfToken,
                },
                body: JSON.stringify(data)
            })
            .then(response => response.json())
            .then(data => console.log('Success:', data))
            .catch(error => console.error('Error:', error));
        }
        </script>

- **Advanced CSRF Configuration:**

        # settings.py
        CSRF_USE_SESSIONS = True  # Store CSRF token in session instead of cookie
        CSRF_COOKIE_AGE = 3600     # 1 hour
        CSRF_COOKIE_DOMAIN = None  # Restrict to current domain
        CSRF_COOKIE_PATH = '/'     # Available site-wide
        CSRF_COOKIE_SECURE = True  # Only send over HTTPS
        CSRF_COOKIE_HTTPONLY = True  # Not accessible via JavaScript
        CSRF_TRUSTED_ORIGINS = [
            'https://example.com',
            'https://www.example.com',
        ]
        CSRF_FAILURE_VIEW = 'myapp.views.csrf_failure'


- **API CSRF Protection:**


        # decorators.py
        from django.views.decorators.csrf import csrf_exempt, csrf_protect

        # ✅ SAFE: Protect API endpoints with CSRF
        @api_view(['POST'])
        @csrf_protect  # Explicitly enable CSRF protection
        def create_post_api(request):
            ..........

        # For stateless APIs (use with caution)
        @api_view(['POST'])
        @csrf_exempt  # Disable CSRF for token-based APIs
        def create_post_token_api(request):
            .........

### **Session Forging/Hijacking: Securing session cookies and rotating IDs**

- Session hijacking occurs when an attacker steals or forges session cookies to impersonate a legitimate user. Django provides several mechanisms to prevent session attacks.

- **Session Security Best Practices:**


        # settings.py - Secure session configuration

        # Session cookie security
        SESSION_COOKIE_SECURE = True      # Only send over HTTPS
        SESSION_COOKIE_HTTPONLY = True    # Prevent JavaScript access
        SESSION_COOKIE_SAMESITE = 'Strict'  # Prevent cross-site requests
        SESSION_COOKIE_AGE = 3600         # 1 hour session timeout
        SESSION_COOKIE_DOMAIN = None     # Restrict to current domain
        SESSION_COOKIE_PATH = '/'         # Available site-wide

        # Session backend security
        SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
        SESSION_CACHE_ALIAS = 'sessions'  # Use Redis for session storage

        # Session security settings
        SESSION_SAVE_EVERY_REQUEST = True  # Update session on each request
        SESSION_EXPIRE_AT_BROWSER_CLOSE = True  # Expire when browser closes

        # Session rotation on login
        AUTHENTICATION_BACKENDS = [
            'django.contrib.auth.backends.ModelBackend',
        ]

- **Session Hijacking Detection:**

    - IP/User-agent binding
    - Session rotation on sensitive actions
    - Session timeout and invalidation
    - Session invalidation on logout

- **Enhanced Authentication Views:**
    - Custom login views with additional security checks
    - Password reset with rate limiting
    - Account lockout after failed attempts

### **Email Header Injection: Preventing attackers from using your contact forms for spam**

- Email header injection occurs when attackers inject additional email headers into form data to send spam through your application's email system.

- **Preventing Email Header Injection:**
    - an attacker adds extra headers(like `From:` or `To:`, `Cc:`, `Bcc:`) to the email headers to a contact form to turn your server into a spam bot
    
    - **Prevention:**
        - Validate and sanitize all email headers
        - Use Django's `send_mail()` function which automatically sanitizes headers
        - Reject emails with suspicious headers

- **Rate Limiting for Email Forms:**
    - abuse for Email bombing or Denial of Service (DoS)
    - Implement rate limiting to prevent spam and abuse
    - Use Django's built-in rate limiting or third-party packages like `django-ratelimit`

### **Directory Traversal: Protecting file uploads and media serving**

- Directory traversal attacks allow attackers to access files and directories outside the intended directory structure.

- **Preventing Directory Traversal in File Uploads:**


- **Secure Media Serving:**


- **URL Configuration for Secure Media Serving:**

### **Exposed Error Messages**

- #### **The danger of `DEBUG` mode ON in production**

    Running Django with `DEBUG = True` in production exposes sensitive information that attackers can use to compromise your application.

- **Risks of DEBUG Mode in Production:**

    1. **Stack Trace Exposure**: Full error details including file paths and code snippets
    2. **Configuration Disclosure**: Database credentials, API keys, and secret keys
    3. **Environment Information**: Server details, Python version, installed packages
    4. **SQL Query Exposure**: Database queries and parameters
    5. **Local Variables**: Internal application state and variable values


- #### **Customizing 404 and 500 error pages**

    - Custom error pages provide a better user experience and prevent information leakage while maintaining security.


- #### **Error Monitoring and Alerting:**

    - The first line: internal logging

        - built-in logging framework
        - `settings.py` to handle different levels of severity
            - DEBUG
            - INFO
            - WARNING
            - ERROR
            - CRITICAL

    - Moderrn Error Tracking(Sentry, Rollbar etc)
        - Real-time error tracking
        - Performance monitoring
        - User impact analysis
        - Automated alerts