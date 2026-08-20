# Django Framework - Sample Exam Paper 1

**Course:** Mastering Django  
**Duration:** 2 Hours  
**Total Marks:** 60 (Section A: 30 marks, Section B: 30 marks)

---

## Instructions

1. Answer **ALL** questions from both sections
2. Section A contains 3 questions of 10 marks each
3. Section B contains 6 questions of 5 marks each
4. Write clear, well-structured code with proper explanations
5. Follow Django best practices and conventions

---

## Section A: Long Answer Questions (10 marks each)

### Question 1 (10 marks)

**Build a complete blog application with the following features:**

a) Create Django models for:
   - Blog posts with title, slug, content, author, published date, and status (draft/published)
   - Categories with name and description
   - Comments with author name, email, content, and approval status
   - Tags for posts (Many-to-Many relationship)

b) Implement views to:
   - Display a list of published posts with pagination (10 posts per page)
   - Show individual post details with approved comments
   - Filter posts by category and tag
   - Allow users to submit comments (pending approval)

c) Create URL patterns for all the above views

d) Write a custom template tag to display the 5 most recent posts in a sidebar

**Evaluation Criteria:**
- Correct model design with relationships (3 marks)
- Proper view implementation with querysets (3 marks)
- URL configuration (2 marks)
- Custom template tag (2 marks)

---

### Question 2 (10 marks)

**Implement a user authentication and authorization system:**

a) Create a custom user model that extends AbstractUser with additional fields:
   - Phone number
   - Date of birth
   - Profile picture
   - Bio
   - User role (choices: admin, editor, viewer)

b) Implement the following views:
   - User registration with email verification
   - Login with "Remember Me" functionality
   - Password reset via email
   - User profile update

c) Create a custom decorator to restrict access to views based on user roles

d) Implement a middleware to log all user activities (login, logout, page views) to a database table

**Evaluation Criteria:**
- Custom user model implementation (3 marks)
- Authentication views (3 marks)
- Custom decorator for authorization (2 marks)
- Activity logging middleware (2 marks)

---

### Question 3 (10 marks)

**Build a RESTful API using Django REST Framework:**

a) Create a Product model with fields: name, description, price, stock, category, image, and created_at

b) Implement API endpoints for:
   - List all products with filtering by category and price range
   - Retrieve a single product by ID
   - Create a new product (admin only)
   - Update product details (admin only)
   - Delete a product (admin only)

c) Implement the following features:
   - Token-based authentication
   - Pagination (20 items per page)
   - Search functionality on product name and description
   - Custom permission class to allow read-only access for unauthenticated users

d) Write serializers with validation to ensure:
   - Price is always positive
   - Stock cannot be negative
   - Name is unique (case-insensitive)

**Evaluation Criteria:**
- Model and serializer design (3 marks)
- ViewSets and URL routing (3 marks)
- Authentication and permissions (2 marks)
- Filtering, search, and validation (2 marks)

---

## Section B: Short Answer Questions (5 marks each)

### Question 4 (5 marks)

**Explain Django's MTV (Model-Template-View) architecture.**

a) Draw a diagram showing how a user request flows through the Django application, from URL routing to the final HTTP response

b) Compare Django's MTV pattern with the traditional MVC (Model-View-Controller) pattern

c) Explain the role of each component (Model, Template, View) with examples

---

### Question 5 (5 marks)

**Write a Django form for user registration with the following requirements:**

a) Create a form class with fields:
   - Username (required, alphanumeric only, 3-20 characters)
   - Email (required, valid email format)
   - Password (required, minimum 8 characters, must contain letters and numbers)
   - Confirm Password (must match password)
   - Age (required, must be 18 or above)
   - Terms and Conditions (checkbox, required)

b) Include custom validation methods for:
   - Checking if username already exists
   - Validating password strength
   - Ensuring age requirement is met

c) Explain how Django processes form validation (clean methods, validators)

---

### Question 6 (5 marks)

**Explain Django's ORM and write queries for the following scenarios:**

Given models:
```python
class Author(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()
    birth_date = models.DateField()

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    published_date = models.DateField()
    price = models.DecimalField(max_digits=6, decimal_places=2)
    is_available = models.BooleanField(default=True)
```

Write ORM queries to:
a) Get all books published in 2024
b) Find authors who have written more than 5 books
c) Get the average price of all available books
d) Retrieve books with their authors in a single query (optimize for N+1 problem)
e) Get books sorted by price (highest to lowest)

---

### Question 7 (5 marks)

**Explain Django signals and provide a practical example:**

a) What are Django signals and when should you use them?

b) List and explain 3 built-in signals in Django:
   - pre_save and post_save
   - pre_delete and post_delete
   - m2m_changed

c) Write code to automatically create a UserProfile when a new User is created using the `post_save` signal

d) What are the potential drawbacks of using signals? When should you avoid them?

---

### Question 8 (5 marks)

**Discuss Django's security features:**

a) Explain how Django protects against:
   - SQL Injection attacks
   - Cross-Site Scripting (XSS)
   - Cross-Site Request Forgery (CSRF)
   - Clickjacking

b) Write code examples showing:
   - How to use CSRF tokens in forms
   - How to sanitize user input in templates
   - How Django implements secure password storage (hashing)

c) What is the purpose of Django's SECRET_KEY?

---

### Question 9 (5 marks)

**Explain Django's caching framework:**

a) What are the different caching strategies available in Django?
   - Per-site cache
   - Per-view cache
   - Template fragment cache
   - Low-level cache API

b) Configure Django to use Redis as a cache backend (show settings.py configuration)

c) Write code to:
   - Cache a view for 15 minutes
   - Cache a specific queryset
   - Invalidate cache when data is updated

d) When should you use caching and what are the trade-offs?

---

## Marking Scheme Summary

### Section A (30 marks total)
- **Question 1:** 10 marks (Models: 3, Views: 3, URLs: 2, Template tag: 2)
- **Question 2:** 10 marks (User model: 3, Views: 3, Decorator: 2, Middleware: 2)
- **Question 3:** 10 marks (Model/Serializer: 3, ViewSets: 3, Auth: 2, Features: 2)

### Section B (30 marks total)
- **Questions 4-9:** 5 marks each
- Award partial marks for:
  - Correct concepts even if implementation has minor errors
  - Good explanation even if code is incomplete
  - Demonstrating understanding of core concepts
- Full marks require complete, working code with proper explanations

---

## Coverage by Units

- **Unit 1 (Introduction):** Question 4 (MTV Architecture)
- **Unit 2 (Models & ORM):** Question 1, Question 6
- **Unit 3 (Views & Templates):** Question 1, Question 5
- **Unit 4 (Authentication):** Question 2
- **Unit 5 (REST API):** Question 3
- **Unit 6 (Advanced Topics):** Question 7, Question 8, Question 9

---

**End of Sample Paper 1**
