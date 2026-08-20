## 📚 Mastering Django Documentation Repository


MIT

Welcome to the documentation repository for Mastering Django. This repository contains the source Markdown files and configuration (mkdocs.yml) used to build the complete, interactive documentation website.

This documentation serves as the official guide and reference material for learning and mastering the Django Web Framework.

### ⚙️ 1. Technical Stack and ConfigurationThe documentation site is built using the following technologies:

|Element|Tool/Theme|Key Configuration Detail|
|---|---|---|
|Static Site Generator|MkDocs|site_name: Mastering Django|
|Theme|Material for MkDocs|Features: Navigation Tabs, Instant Loading, Code Copy|
|Styling|Custom Palette|Primary: green, Accent: deep purple (Default Scheme)|
|Extensions|pymdownx.emoji, admonition, toc|Enables advanced markdown features like callouts and footnotes.|

### 🛠️ 2. Local Setup Guide (Building the Docs)

To view or contribute to the documentation, you must set up your local development environment.

#### 2.1. Prerequisites

You need Python (version 3.8+) and Git installed on your system.

#### 2.2. Clone and Initialize

Clone the repository:

```bash
git clone https://github.com/ritushishir/mastering-django.git
cd mastering-django
```

#### 2.3. Create and Activate Virtual Environment

Create a virtual environment:

```bash
python -m venv venv
```

Activate the virtual environment:

- On Windows:

```bash
venv\Scripts\activate
```

- On macOS/Linux:

```bash
source venv/bin/activate
```

#### 2.3 Install dependencies: The `requirements.txt` file ensures you have MkDocs and the exact theme and extensions needed (like mkdocs-material)

```bash
pip install -r requirements.txt
```

### ▶️ 3. Development and Usage

#### 3.1. Serve Locally (Development Mode)

Use this command to start a live-reloading server. This is the recommended way to edit content.

```bash
mkdocs serve
```

The documentation will be available at: <http://127.0.0.1:8000/>

#### 3.2. Build Static Site (Production Mode)

To generate the static site files for deployment, run:

```bash
mkdocs build
```

The final HTML/CSS/JS files will be output to the site/ directory.

### ✍️ 4. Contribution Guidelines

I highly value contributions to improve clarity, add examples, or fix errors in the Mastering Django guides. 

Please follow these steps:

#### 4.1. Key Directories

| Directory| Purpose|
|---|---|
|docs/|Contains all Markdown source files (.md).|
|docs/assets/|Location for images (logo.png), favicons (favicon.ico), and other static assets.|
|mkdocs.yml|The main configuration file, controlling navigation and features. If you add a new .md file, you must update the nav section here.|

#### 4.2. Adding a New Page

- Create your new Markdown file (e.g., docs/unit-3/new-topic.md).

- Open mkdocs.yml.

- Locate the appropriate section in the nav: block (e.g., under Models and Databases:).

- Add a link to your new page using the correct hierarchy:

```yaml
  - New Topic: unit-3/new-topic.md
```

#### 4.3. Style and Conventions

- **Code Highlighting:** Use Python, HTML, or Bash as the language tag for code blocks.

- **Admonitions:** Use !!! success, !!! danger, or !!! tip for instructional callouts.

- **Navigation Structure:** Follow the existing Unit-X folder structure (unit-1/, unit-2/, etc.) for organizing course material.

### 5. Further Resources

- [MkDocs Official Documentation](https://www.mkdocs.org/) - For deep dives into MkDocs features and configuration options.
  
- [Material for MkDocs Documentation](https://squidfunk.github.io/mkdocs-material/) - Essential for understanding the theme features enabled in mkdocs.yml.

- [Django Official Website](https://www.djangoproject.com/) - For deep dives into Django concepts referenced in the documentation.
