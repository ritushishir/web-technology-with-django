# Course Syllabus — Web Technology

The official Tribhuvan University course document, reproduced verbatim. For the condensed version
we work through in class, see [Course Introduction — Class Slides](../index.md).

## Course Information

| | | | |
|---|---|---|---|
| **Course Title** | Web Technology | **Full Marks** | 45 + 30 |
| **Course No.** | MIT656 | **Pass Marks** | 22.5 + 15 |
| **Nature of the Course** | Theory + Practical | **Credit Hours** | 3 |
| **Semester** | IV | | |

## Course Description

This course is designed to take students from beginner to advanced levels in developing web
applications using the Django framework. Django is a high-level Python web framework that
promotes rapid development and clean, pragmatic design. This course covers all the essential
aspects of Django — including models, views, templates, forms, authentication, and deployment —
as well as advanced topics such as caching. Through hands-on projects and real-world examples,
students will gain practical experience and a deep understanding of how to build robust, scalable
web applications.

## Course Objectives

By the end of this course, participants will be able to:

1. Create and manage Django projects and apps
2. Work with databases and models
3. Handle forms and user input
4. Implement authentication and authorization features
5. Test and deploy Django applications

## Course Contents

| Unit | Title | Hours | Class Slides |
|---|---|---|---|
| 1 | Django Basics | 7 | [Unit 1](../unit-1/slides.md) |
| 2 | Model, Administration Site and Form Processing | 9 | [Unit 2](../unit-2/slides.md) |
| 3 | Views, URLConfs, Template Engine and Non-HTML Content | 7 | [Unit 3](../unit-3/slides.md) |
| 4 | Users, Caching and Subframework | 7 | [Unit 4](../unit-4/slides.md) |
| 5 | Middleware, Legacy Databases and Admin Interface | 7 | [Unit 5](../unit-5/slides.md) |
| 6 | Internationalization, Security and Deployment | 8 | [Unit 6](../unit-6/slides.md) |
| | **Total** | **45** | |

### Unit 1: Django Basics (7 Hrs.)

Web Framework, MVC Design Pattern, Django Features, Setting Up a Database, Starting a
Project, Dynamic Web Pages, Dynamic Content, Mapping URLs to Views, How Django Processes
a Request, URLconfs and Loose Coupling, 404 Errors, Dynamic URLs, Django's Pretty Error,
The Django Template System, Using the Template System, Basic Template Tags and Filters,
Templates in Views, Template Loading, Template Inheritance.

### Unit 2: Model, Administration Site and Form Processing (9 Hrs.)

The Dumb Way to Do Database Queries in Views, The MTV Development Pattern, Configuring
the Database, First App, Defining Models in Python, First Model, Installing the Model, Data
Access, Adding Model String Representations, Inserting and Updating Data, Selecting Objects,
Filtering Data, Deleting Objects, Making Changes to a Database Schema, Activating the Admin
Interface, Using the Admin Interface, Customizing the Admin Interface, Customizing the Admin
Interface's Look and Feel, Customizing the Admin Index Page, Search, The Perfect Form, Creating
a Feedback Form, Processing the Submission, Custom Validation Rules, Custom Look and Feel,
Creating Forms from Models.

### Unit 3: Views, URLConfs, Template Engine and Non-HTML Content (7 Hrs.)

URLconf Tricks, Including Other URLconfs, Using Generic Views, Generic Views of Objects,
Extending Generic Views, Template Language Review, Request Context and Context Processors,
Inside Template Loading, Extending the Template System, Writing Custom Template Loaders,
Using the Built-in Template Reference, Configuring the Template System in Standalone Mode,
The Basics: Views and MIME-types, Producing CSV, Generating PDFs, Other Possibilities, The
Syndication Feed Framework, The Sitemap Framework.

### Unit 4: Users, Caching and Subframework (7 Hrs.)

Cookies, Django's Session Framework, Users and Authentication, The Other Bits: Permissions,
Groups, Messages, and Profiles, Setting Up the Cache, The Per-Site Cache, The Per-View Cache,
The Low-Level Cache API, Upstream Caches, Other Optimizations, Order of Middleware Classes,
The Django Standard Library, Sites, Flatpages, Redirects, CSRF Protection, Humanizing Data,
Markup Filters.

### Unit 5: Middleware, Legacy Databases and Admin Interface (7 Hrs.)

Middleware Overview, Middleware Installation, Middleware Methods, Built-in Middleware,
Integrating with a Legacy Database, Integrating with an Authentication System, Integrating with
Legacy Web Applications, The Zen of Admin, Customizing Admin Templates, Creating Custom
Admin Views, Overriding Built-in Views.

### Unit 6: Internationalization, Security and Deployment (8 Hrs.)

Specifying Translation Strings in Python Code, Specifying Translation Strings in Template Code,
Creating Language Files, Language Preference Discovery, The `set_language` Redirect View, Using
Translations in Projects, Translations and JavaScript, Web Security Theme, SQL Injection Attack,
XSS, Cross-Site Request Forgery, Session Forging/Hijacking, Email Header Injection, Directory
Traversal, Exposed Error Messages, Shared Nothing, Personal Preferences, Using Django with
Apache and mod_python, Using Django with FastCGI, Scaling, Performance Tuning.

## Laboratory Works

Students need to write Python programs to demonstrate every concept taught in the classroom and
should also develop a web app using Django.

## References

1. Adrian Holovaty, Jacob K. Moss, *The Definitive Guide to Django: Web Development Done
   Right*, Apress, Second Edition, 2009.
2. William S. Vincent, *Django for Beginners: Build Websites with Python and Django*,
   WelcomeToCode, First Edition, 2023.

## Source

[Tribhuvan University course portal (PDF)](https://portal.tu.edu.np/downloads/2025_12_03_16_20_07.pdf)
