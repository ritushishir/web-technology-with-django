# Django Globalisation, Security, and Scalability

## Internationalization (i18n) and Localization (l10n)

### [Specifying Translation Strings in Python Code](./internationalization-and-localization.md#specifying-translation-strings-in-python-code)

* Using `gettext` and `gettext_lazy`.
* Handling pluralization in Python logic.

### [Specifying Translation Strings in Template Code](./internationalization-and-localization.md#specifying-translation-strings-in-template-code)

* Using the `{% trans %}` and `{% blocktrans %}` tags.
* Translating variable content within templates.

### [Creating Language Files](./internationalization-and-localization.md#creating-language-files)

* Working with `.po` (Portable Object) and `.mo` (Machine Object) files.
* Using `makemessages` and `compilemessages` commands.

### [Language Preference Discovery](./internationalization-and-localization.md#language-preference-discovery)

* How Django determines the user's language via URL, Cookies, or Browser headers.
* The role of `LocaleMiddleware`.

### [The "set_language" Redirect View](./internationalization-and-localization.md#the-set_language-redirect-view)

* Implementing a language switcher for users to manually toggle between languages.

### [Using Translations in Projects](./internationalization-and-localization.md#using-translations-in-projects)

* Structuring the `locale/` directory.
* Best practices for maintaining multi-lingual content.

### [Translations and JavaScript](./internationalization-and-localization.md#translations-and-javascript)

* Using the `javascript_catalog` view to make translation strings available in frontend scripts.

---

## Advanced Web Security

### [Web Security Themes](./advanced-web-security.md#web-security-themes)

* The philosophy of "Defense in Depth."

### [Common Vulnerabilities & Mitigations](./advanced-web-security.md#common-vulnerabilities-and-mitigations)

* **SQL Injection:** How the Django ORM protects against malicious queries.
* **XSS (Cross-Site Scripting):** Automatic HTML escaping in templates.
* **CSRF (Cross-Site Request Forgery):** Token validation for state-changing requests.
* **Session Forging/Hijacking:** Securing session cookies and rotating IDs.
* **Email Header Injection:** Preventing attackers from using your contact forms for spam.
* **Directory Traversal:** Protecting file uploads and media serving.

### [Exposed Error Messages](./advanced-web-security.md#exposed-error-messages)

* The danger of `DEBUG = True` in production.
* Customizing 404 and 500 error pages.

---

## Architecture and Deployment

### [Shared Nothing](./architecture-and-deployment.md#shared-nothing)

* Understanding the "Shared Nothing" architecture for horizontal scaling.
* Managing state (sessions/media) outside the web server.

### [Personal Preferences](./architecture-and-deployment.md#personal-preferences)

* Organizing settings for different environments (Development vs. Production).

### [Server Integration](./architecture-and-deployment.md#server-integration)

* **Apache and mod_python:** Overview of the legacy deployment method.
* **FastCGI:** Using FastCGI for non-standard hosting environments.

### [Scaling and Performance Tuning](./architecture-and-deployment.md#scaling-and-performance-tuning)

* **Horizontal Scaling:** Adding more app servers behind a load balancer.
* **Performance Tuning:** Identifying bottlenecks with tools like Django Debug Toolbar.
* **Database Optimization:** Connection pooling and read-replicas.

---

## Monitoring and Maintenance

### [Importance of Monitoring](./monitoring-and-maintenance.md#importance-of-monitoring)

### [Monitoring Tools and Techniques](./monitoring-and-maintenance.md#monitoring-tools-and-techniques)

### [Routine Maintenance Tasks](./monitoring-and-maintenance.md#routine-maintenance-tasks)

### [Performance Optimization](./monitoring-and-maintenance.md#performance-optimization)

### [Backup and Disaster Recovery](./monitoring-and-maintenance.md#backup-and-disaster-recovery)

### [Documentation and Knowledge Sharing](./monitoring-and-maintenance.md#documentation-and-knowledge-sharing)

### [Security Audits and Compliance](./monitoring-and-maintenance.md#security-audits-and-compliance)

### [User Feedback and Continuous Improvement](./monitoring-and-maintenance.md#user-feedback-and-continuous-improvement)
