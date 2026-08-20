# Django Framework - Sample Exam Paper 2

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

**Develop an e-commerce product management system:**

a) Create Django models for:
   - Product with name, SKU, description, price, discount_price, stock, and is_active
   - Category with hierarchical structure (parent-child relationship)
   - ProductImage with multiple images per product
   - ProductReview with rating (1-5), comment, user, and created_at

b) Implement views to:
   - Display products with filtering by category, price range, and availability
   - Show product details with all images and reviews
   - Calculate and display average rating for each product
   - Allow authenticated users to submit reviews (one per product per user)

c) Write a custom manager for Product model to:
   - Get only active products
   - Get products on sale (with discount_price)
   - Get low stock products (stock < 10)

d) Create URL patterns and implement pagination for product listing (12 products per page)

**Evaluation Criteria:**
- Model design with relationships and managers (3 marks)
- View implementation with filtering and aggregation (3 marks)
- Custom manager methods (2 marks)
- URL configuration and pagination (2 marks)

---

### Question 2 (10 marks)

**Build a task management system with team collaboration:**

a) Create models for:
   - Project with name, description, owner, and team members (Many-to-Many)
   - Task with title, description, project, assigned_to, priority (low/medium/high), status (todo/in_progress/done), and due_date
   - TaskComment with task, author, comment, and created_at
   - TaskAttachment with task, file, and uploaded_by

b) Implement class-based views for:
   - ListView to display all tasks with filtering by project, status, and assigned user
   - DetailView for task details with comments and attachments
   - CreateView for creating new tasks
   - UpdateView for updating task status and details
   - DeleteView for removing tasks (only by project owner)

c) Add permissions to ensure:
   - Only project team members can view tasks
   - Only assigned users can update task status
   - Only project owner can delete tasks

d) Create a custom template filter to display task priority with color coding

**Evaluation Criteria:**
- Model design with relationships (3 marks)
- Class-based views implementation (3 marks)
- Permission system (2 marks)
- Custom template filter (2 marks)

---

### Question 3 (10 marks)

**Implement a RESTful API for a library management system:**

a) Create models for:
   - Book with title, ISBN, author, publisher, published_date, pages, and available_copies
   - Member with name, email, phone, membership_date, and is_active
   - Borrowing with book, member, borrowed_date, due_date, returned_date, and status

b) Using Django REST Framework, create API endpoints for:
   - List all books with search on title and author
   - Borrow a book (decrease available_copies, create borrowing record)
   - Return a book (increase available_copies, update borrowing record)
   - Get borrowing history for a member
   - Get currently borrowed books

c) Implement:
   - JWT authentication
   - Custom serializers with nested data (show book details in borrowing)
   - Validation to prevent borrowing if no copies available
   - Validation to prevent borrowing if member has overdue books

d) Add throttling to limit API requests (10 requests per minute for anonymous users, 100 for authenticated)

**Evaluation Criteria:**
- Model design and relationships (3 marks)
- API endpoints and serializers (3 marks)
- Authentication and validation (2 marks)
- Throttling and business logic (2 marks)

---

## Section B: Short Answer Questions (5 marks each)

### Question 4 (5 marks)

**Explain Django's request-response cycle:**

a) Describe the complete flow from when a user enters a URL to when they receive the response

b) Explain the role of:
   - URLconf (urls.py)
   - Middleware
   - View functions/classes
   - Context processors
   - Template rendering

c) Draw a flowchart showing the request-response cycle with all components

---

### Question 5 (5 marks)

**Write a Django ModelForm for a Book model:**

Given the model:
```python
class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    isbn = models.CharField(max_length=13, unique=True)
    published_date = models.DateField()
    price = models.DecimalField(max_digits=6, decimal_places=2)
    pages = models.PositiveIntegerField()
    description = models.TextField()
```

a) Create a ModelForm with:
   - All fields except 'description' required
   - Custom widget for description (Textarea with 5 rows)
   - Custom error messages for each field
   - Help text for ISBN field

b) Add validation to ensure:
   - ISBN is exactly 13 digits
   - Published date is not in the future
   - Price is between 1 and 10000
   - Pages is at least 10

c) Explain the difference between Form and ModelForm

---

### Question 6 (5 marks)

**Explain Django's database transactions:**

a) What are database transactions and why are they important?

b) Explain the difference between:
   - ATOMIC_REQUESTS setting
   - @transaction.atomic decorator
   - transaction.atomic() context manager

c) Write code examples showing:
   - How to use atomic transactions for a bank transfer operation
   - How to handle transaction rollback on errors
   - How to create savepoints within a transaction

d) What is the N+1 query problem and how do you solve it using select_related() and prefetch_related()?

---

### Question 7 (5 marks)

**Discuss Django middleware:**

a) What is middleware and how does it work in Django?

b) Explain the order of middleware execution for:
   - Request processing
   - Response processing
   - Exception handling

c) Write a custom middleware to:
   - Log the time taken to process each request
   - Add a custom header to all responses
   - Block requests from specific IP addresses

d) List 3 built-in Django middleware and explain their purpose

---

### Question 8 (5 marks)

**Explain Django's template system:**

a) What is template inheritance and how does it work?

b) Write a base template (base.html) with:
   - Header block
   - Content block
   - Footer block
   - Static file loading

c) Create a child template that extends the base template

d) Explain the following template tags and filters:
   - {% include %}
   - {% load static %}
   - {{ value|date:"Y-m-d" }}
   - {{ text|truncatewords:50 }}
   - {% csrf_token %}

---

### Question 9 (5 marks)

**Discuss Django deployment and production best practices:**

a) What settings should be changed when deploying Django to production?
   - DEBUG setting
   - ALLOWED_HOSTS
   - SECRET_KEY management
   - Database configuration

b) Explain how to:
   - Serve static files in production
   - Configure HTTPS and SSL
   - Set up environment variables
   - Use gunicorn or uWSGI as WSGI server

c) What is the purpose of:
   - collectstatic command
   - STATIC_ROOT vs STATIC_URL
   - MEDIA_ROOT vs MEDIA_URL

d) List 3 security best practices for Django production deployment

---

## Marking Scheme Summary

### Section A (30 marks total)
- **Question 1:** 10 marks (Models/Managers: 3, Views/Filtering: 3, Custom Manager: 2, URLs/Pagination: 2)
- **Question 2:** 10 marks (Models: 3, Class-based Views: 3, Permissions: 2, Template Filter: 2)
- **Question 3:** 10 marks (Models: 3, API/Serializers: 3, Auth/Validation: 2, Throttling: 2)

### Section B (30 marks total)
- **Questions 4-9:** 5 marks each
- Award partial marks for:
  - Correct understanding of concepts (2-3 marks)
  - Working code with minor issues (3-4 marks)
  - Complete, correct implementation (5 marks)
- Deduct marks for:
  - Syntax errors
  - Missing required components
  - Incorrect Django conventions

---

## Coverage by Units

- **Unit 1 (Introduction):** Question 4 (Request-Response Cycle)
- **Unit 2 (Models & ORM):** Question 1, Question 6
- **Unit 3 (Views & Templates):** Question 2, Question 5, Question 8
- **Unit 4 (Forms & Authentication):** Question 5
- **Unit 5 (REST API):** Question 3
- **Unit 6 (Advanced Topics):** Question 7, Question 9

---

## Time Management Suggestions

- **Section A (90 minutes):**
  - Question 1: 30 minutes
  - Question 2: 30 minutes
  - Question 3: 30 minutes

- **Section B (30 minutes):**
  - Each question: 5 minutes
  - Review: 10 minutes (if time permits)

---

**End of Sample Paper 2**
