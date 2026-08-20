# Django Final Examination Question Papers
## MIT - Mastering Django Course

---

# Question Paper Set A

**Total Marks: 100**  
**Duration: 3 Hours**

---

## Section A: Short Answer Questions (40 Marks)

**Instructions:** Answer any **8** questions from the following. Each question carries **5 marks**.

### Unit 1: Introduction to Django

**Q1.** Explain the concept of Django Project vs Django App with a real-world analogy. Why is this separation important for scalability? **(5 marks)**  
*Reference: Unit 1 - Project and App Structure*

**Q2.** What are the key advantages of using Django as a web framework? List and briefly explain any four benefits. **(5 marks)**  
*Reference: Unit 1 - Introduction to Django*

### Unit 2: Models, Admin, and Forms

**Q3.** Write Python code to define a Django model called `Artist` with the following fields: name (CharField, max 100), bio (TextField), debut_year (IntegerField), and is_active (BooleanField with default True). **(5 marks)**  
*Reference: Unit 2 - Models*

**Q4.** Explain the difference between `makemigrations` and `migrate` commands in Django. When should each be used? **(5 marks)**  
*Reference: Unit 2 - Database Migrations*

**Q5.** What is the difference between a Standard Form (`forms.Form`) and a ModelForm (`forms.ModelForm`) in Django? Provide one use case for each. **(5 marks)**  
*Reference: Unit 2 - Forms in Django*

**Q6.** Why is it considered a bad practice to perform database queries directly in Django templates? Explain with an example of the N+1 query problem. **(5 marks)**  
*Reference: Unit 2 - MTV Pattern*

### Unit 3: Advanced Views and Templates

**Q7.** What are Context Processors in Django? Write code to create a custom context processor that adds the current year to all templates. **(5 marks)**  
*Reference: Unit 3 - Deep Dive Django Templates*

**Q8.** Explain the difference between Class-Based Views (CBVs) and Function-Based Views (FBVs). When would you prefer one over the other? **(5 marks)**  
*Reference: Unit 3 - Working with Generic Views*

**Q9.** How can Django generate non-HTML content like CSV files? Write a simple view function that returns CSV data. **(5 marks)**  
*Reference: Unit 3 - Specialized Content and Output*

### Unit 4: Sessions, Authentication, and Caching

**Q10.** Explain Django's session framework. List three different session backends and their use cases. **(5 marks)**  
*Reference: Unit 4 - Session and State Management*

**Q11.** What is the difference between Authentication and Authorization in Django? Provide examples of each. **(5 marks)**  
*Reference: Unit 4 - Authentication and Authorization*

**Q12.** Explain the concept of caching in Django. Describe the difference between per-site cache and per-view cache. **(5 marks)**  
*Reference: Unit 4 - Setting Up Caching*

### Unit 5: Middleware and Admin Customization

**Q13.** Draw a diagram showing the request-response lifecycle in Django with middleware. Explain why middleware order matters. **(5 marks)**  
*Reference: Unit 5 - Middleware Architecture*

**Q14.** Write a simple custom middleware class that logs the time taken to process each request. **(5 marks)**  
*Reference: Unit 5 - Middleware Architecture*

**Q15.** What is the philosophy behind Django's Admin interface? When should you use the Admin vs building a custom dashboard? **(5 marks)**  
*Reference: Unit 5 - Advanced Django Admin*

### Unit 6: Security, Deployment, and Internationalization

**Q16.** Explain the "Defense in Depth" security philosophy. List five security layers that should be implemented in a Django application. **(5 marks)**  
*Reference: Unit 6 - Advanced Web Security*

**Q17.** What is CSRF protection? How does Django implement CSRF protection in forms? **(5 marks)**  
*Reference: Unit 6 - Advanced Web Security*

**Q18.** Explain the "Shared Nothing" architecture for horizontal scaling. Why is it important for production deployments? **(5 marks)**  
*Reference: Unit 6 - Architecture and Deployment*

---

## Section B: Long Answer Questions (60 Marks)

**Instructions:** Answer any **4** questions from the following. Each question carries **15 marks**.

**Q19.** 
a) Create a complete Django model for a music streaming application with the following requirements: **(8 marks)**
   - A `Song` model with fields: title, duration, release_date, lyrics, play_count
   - An `Album` model with fields: title, release_year, cover_image
   - A `Genre` model with fields: name, description
   - Establish appropriate relationships between these models
   
b) Write the necessary code to register these models in the Django admin with custom list displays and filters. **(7 marks)**

*Reference: Unit 2 - Models and Admin Interface*

---

**Q20.**
a) Explain the complete form validation process in Django, including field-level and form-level validation. **(7 marks)**

b) Create a ModelForm for a `Song` model with the following custom validations: **(8 marks)**
   - Title must be at least 3 characters long
   - Duration must be between 30 and 600 seconds
   - Release date cannot be in the future
   - Custom form-level validation to ensure the title doesn't contain profanity (check against a list: ['bad', 'worse'])

*Reference: Unit 2 - Forms in Django*

---

**Q21.**
a) Explain the concept of Generic Views in Django. What problems do they solve? **(5 marks)**

b) Write a complete implementation using Class-Based Generic Views for the following: **(10 marks)**
   - A ListView to display all songs
   - A DetailView to show individual song details
   - A CreateView to add new songs
   - Include URL patterns and template names for each view

*Reference: Unit 3 - Working with Generic Views*

---

**Q22.**
a) Explain Django's authentication system including the User model, login/logout functionality, and permissions. **(7 marks)**

b) Write code to implement the following: **(8 marks)**
   - A custom user registration view with password validation
   - A login-required decorator for a view
   - A permission check to ensure only users with 'can_publish_song' permission can access a specific view
   - Use Django's messages framework to display success/error messages

*Reference: Unit 4 - Authentication and Authorization*

---

**Q23.**
a) Explain the middleware architecture in Django. Describe the execution flow of middleware during request and response processing. **(7 marks)**

b) Create a custom middleware that: **(8 marks)**
   - Logs the IP address and user agent of each request
   - Measures the time taken to process the request
   - Adds a custom header 'X-Processed-Time' to the response
   - Handles exceptions by logging them and returning a custom error page

*Reference: Unit 5 - Middleware Architecture*

---

**Q24.**
a) Explain the following web security vulnerabilities and how Django protects against them: **(9 marks)**
   - SQL Injection
   - Cross-Site Scripting (XSS)
   - Cross-Site Request Forgery (CSRF)

b) Write secure code examples demonstrating: **(6 marks)**
   - Safe database queries using Django ORM
   - Proper form handling with CSRF protection
   - Secure template rendering to prevent XSS

*Reference: Unit 6 - Advanced Web Security*

---

**Q25.**
a) Explain the importance of internationalization (i18n) and localization (l10n) in web applications. **(5 marks)**

b) Demonstrate how to implement i18n in Django by: **(10 marks)**
   - Marking strings for translation in Python code
   - Marking strings for translation in templates
   - Creating and compiling language files
   - Implementing a language switcher
   - Configuring Django settings for multiple languages

*Reference: Unit 6 - Internationalization and Localization*

---

# Question Paper Set B

**Total Marks: 100**  
**Duration: 3 Hours**

---

## Section A: Short Answer Questions (40 Marks)

**Instructions:** Answer any **8** questions from the following. Each question carries **5 marks**.

### Unit 1: Introduction to Django

**Q1.** What is the MVT (Model-View-Template) architecture in Django? How does it differ from the traditional MVC pattern? **(5 marks)**  
*Reference: Unit 1 - Introduction to Django*

**Q2.** Explain the purpose of `manage.py` in a Django project. List any five commands that can be executed using it. **(5 marks)**  
*Reference: Unit 1 - Project Setup*

### Unit 2: Models, Admin, and Forms

**Q3.** Write code to demonstrate CRUD operations (Create, Read, Update, Delete) on a `Song` model using Django ORM. **(5 marks)**  
*Reference: Unit 2 - Models Overview*

**Q4.** What are Django's field types? Explain CharField, TextField, DateField, and BooleanField with their common parameters. **(5 marks)**  
*Reference: Unit 2 - Models*

**Q5.** Explain the concept of form widgets in Django. How can you customize the appearance of form fields? **(5 marks)**  
*Reference: Unit 2 - Forms in Django*

**Q6.** What is the purpose of the Django admin interface? How do you customize the list display and add filters to a model in the admin? **(5 marks)**  
*Reference: Unit 2 - Admin Interface*

### Unit 3: Advanced Views and Templates

**Q7.** Explain template inheritance in Django. Write code showing a base template and a child template that extends it. **(5 marks)**  
*Reference: Unit 3 - Deep Dive Django Templates*

**Q8.** What are URL namespaces in Django? Why are they important in large projects? Provide an example. **(5 marks)**  
*Reference: Unit 3 - Advanced URL Dispatching*

**Q9.** Explain how Django's Sitemap framework helps with SEO. Write code to create a basic sitemap for a blog application. **(5 marks)**  
*Reference: Unit 3 - Metadata and Discovery Frameworks*

### Unit 4: Sessions, Authentication, and Caching

**Q10.** How does Django maintain state in a stateless HTTP protocol? Explain the session framework with an example. **(5 marks)**  
*Reference: Unit 4 - Session and State Management*

**Q11.** What are Django Groups and Permissions? How do they simplify access control in applications? **(5 marks)**  
*Reference: Unit 4 - Authentication and Authorization*

**Q12.** Explain the low-level cache API in Django. Write code to manually set, get, and delete cache keys. **(5 marks)**  
*Reference: Unit 4 - Setting Up Caching*

### Unit 5: Middleware and Admin Customization

**Q13.** List and explain three built-in middleware classes in Django (SecurityMiddleware, CommonMiddleware, GZipMiddleware). **(5 marks)**  
*Reference: Unit 5 - Middleware Architecture*

**Q14.** What is the `inspectdb` command? How is it useful when integrating Django with legacy databases? **(5 marks)**  
*Reference: Unit 5 - Legacy System and Integration*

**Q15.** How can you customize Django admin templates? Explain the process of overriding `admin/base_site.html`. **(5 marks)**  
*Reference: Unit 5 - Advanced Django Admin*

### Unit 6: Security, Deployment, and Internationalization

**Q16.** What are the risks of running Django with `DEBUG = True` in production? How do you create custom 404 and 500 error pages? **(5 marks)**  
*Reference: Unit 6 - Advanced Web Security*

**Q17.** Explain session hijacking and how Django protects against it. What security settings should be configured for session cookies? **(5 marks)**  
*Reference: Unit 6 - Advanced Web Security*

**Q18.** What is the purpose of monitoring and maintenance in production Django applications? List five key areas to monitor. **(5 marks)**  
*Reference: Unit 6 - Monitoring and Maintenance*

---

## Section B: Long Answer Questions (60 Marks)

**Instructions:** Answer any **4** questions from the following. Each question carries **15 marks**.

**Q19.**
a) Explain the complete process of setting up a Django project from scratch, including: **(8 marks)**
   - Creating a virtual environment
   - Installing Django
   - Starting a project and creating an app
   - Configuring database settings
   - Running migrations

b) Write the code to create a simple view that displays "Hello, Django!" and configure the URL pattern for it. **(7 marks)**

*Reference: Unit 1 - Project Setup*

---

**Q20.**
a) Explain the relationship types in Django models (OneToOne, ForeignKey, ManyToMany) with real-world examples. **(7 marks)**

b) Create a complete model structure for a library management system with: **(8 marks)**
   - A `Book` model (title, ISBN, publication_date, available_copies)
   - An `Author` model (name, birth_date, biography)
   - A `Member` model (name, email, membership_date)
   - A `Borrowing` model to track which member borrowed which book
   - Implement appropriate relationships and constraints

*Reference: Unit 2 - Models*

---

**Q21.**
a) Explain the template loading mechanism in Django. How does Django find templates? **(5 marks)**

b) Create a custom template tag and a custom template filter with the following specifications: **(10 marks)**
   - A template tag `{% current_time %}` that displays the current date and time in a specified format
   - A template filter `truncate_words` that truncates text to a specified number of words
   - Include registration code and usage examples in templates

*Reference: Unit 3 - Deep Dive Django Templates*

---

**Q22.**
a) Explain the different caching strategies in Django: **(7 marks)**
   - Per-site cache
   - Per-view cache
   - Template fragment caching
   - Low-level cache API

b) Implement caching for a blog application: **(8 marks)**
   - Cache the homepage for 15 minutes
   - Cache individual blog post views for 1 hour
   - Cache a "popular posts" sidebar fragment for 30 minutes
   - Write code to invalidate cache when a new post is published

*Reference: Unit 4 - Setting Up Caching*

---

**Q23.**
a) Explain the concept of custom authentication backends in Django. When would you need to create one? **(6 marks)**

b) Implement a complete user profile system: **(9 marks)**
   - Extend the User model with a Profile model containing: bio, profile_picture, phone_number, date_of_birth
   - Use signals to automatically create a profile when a user registers
   - Create a view to display and edit user profiles
   - Implement proper authentication checks

*Reference: Unit 4 - Authentication and Authorization*

---

**Q24.**
a) Explain the following security vulnerabilities with examples: **(9 marks)**
   - Email Header Injection
   - Directory Traversal
   - Session Forging

b) Write secure code for: **(6 marks)**
   - A contact form that prevents email header injection
   - A file upload view that prevents directory traversal attacks
   - Session security configuration in settings.py

*Reference: Unit 6 - Advanced Web Security*

---

**Q25.**
a) Explain the "Shared Nothing" architecture and its importance in scaling Django applications. **(7 marks)**

b) Design a deployment strategy for a Django application that includes: **(8 marks)**
   - Separating settings for development, staging, and production environments
   - Configuring static and media file serving
   - Setting up proper security headers
   - Database connection pooling
   - Provide code examples for settings configuration

*Reference: Unit 6 - Architecture and Deployment*

---

## Marking Scheme Guidelines

### Short Answer Questions (5 marks each)
- **5 marks:** Complete answer with code examples (where applicable), clear explanation, and proper terminology
- **3-4 marks:** Good understanding demonstrated but missing some details or examples
- **1-2 marks:** Basic understanding but incomplete or partially incorrect
- **0 marks:** No attempt or completely incorrect

### Long Answer Questions (15 marks each)
- **13-15 marks:** Comprehensive answer with working code, excellent explanation, proper structure
- **10-12 marks:** Good answer with mostly correct code, clear explanation, minor issues
- **7-9 marks:** Satisfactory answer with basic code, adequate explanation, some errors
- **4-6 marks:** Incomplete answer with partial code, limited explanation
- **0-3 marks:** Minimal attempt or largely incorrect

---

## Additional Notes for Examiners

1. **Code Evaluation:** When evaluating code answers, check for:
   - Correct syntax and Django conventions
   - Proper use of Django features (ORM, forms, views, etc.)
   - Security best practices
   - Code readability and structure

2. **Theoretical Questions:** Look for:
   - Understanding of concepts
   - Real-world application
   - Ability to compare and contrast different approaches

3. **Partial Credit:** Award partial marks for:
   - Correct approach even if implementation has minor errors
   - Good explanation even if code is incomplete
   - Demonstrating understanding of core concepts

---

**End of Question Papers**






