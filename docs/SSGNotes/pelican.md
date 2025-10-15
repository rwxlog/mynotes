Setting up a **Pelican** site with Netlify provides a powerful static publishing workflow using Python. This guide covers the complete process, including creating custom templates, managing assets, and configuring Netlify for continuous deployment in a Python environment.

-----

## 1\. Local Pelican Setup and Project Initialization

Pelican is a Python-based static site generator, requiring a Python environment and `pip`.

### 1.1. Install Prerequisites and Pelican

1.  **Install Python** (3.7+ recommended).
2.  **Create a virtual environment** (recommended to isolate dependencies):
    ```bash
    python3 -m venv venv
    source venv/bin/activate  # On Windows, use: venv\Scripts\activate
    ```
3.  **Install Pelican and Markdown dependencies:**
    ```bash
    pip install pelican markdown
    ```

### 1.2. Create a New Pelican Site

Use Pelican's quickstart script to generate the basic directory structure.

1.  **Run the quickstart wizard:**

    ```bash
    pelican-quickstart
    ```

2.  **Follow the prompts:**

      * **Where do you want to create your new web site?** `my-pelican-site` (or preferred name)
      * Answer the questions regarding Title, Author, URL prefix (usually fine to leave blank for local development), and default language.
      * **Do you want to specify a URL prefix?** **No**
      * **Do you want to generate a Fabfile/Makefile?** **Yes** (This creates handy build scripts)

3.  **Navigate into the new directory:**

    ```bash
    cd my-pelican-site
    ```

### 1.3. Local Test

Use the provided `Makefile` to generate and serve the site locally.

```bash
make html   # Generates static files into the output directory (default: output)
make serve  # Serves the generated site at http://localhost:8000/
```

Press **Ctrl+C** to stop the server.

-----

## 2\. Custom Layouts, Partials, and Static Assets

Pelican uses the **Jinja2** templating engine, which organizes templates into the **`theme`** directory.

### 2.1. Project File/Folder Structure Tree

Your project structure, after customization, will look like this:

```
my-pelican-site/
├── venv/                    # ⚠️ Python Virtual Environment (ignored by Git)
├── output/                  # ⚠️ Generated static site (ignored by Git)
├── content/                 # Source content files (Markdown, reStructuredText)
│   ├── pages/
│   │   └── about.md         # Content for an 'About' page
│   └── first-post.md        # Blog post content
├── theme/                   # Custom theme files
│   ├── static/              # Static files unique to the theme (CSS, JS)
│   │   └── style.css        # Custom CSS file
│   └── templates/           # Jinja2 templates for structure
│       ├── base.html        # The main HTML skeleton (Layout)
│       ├── includes/        # Reusable snippets (Partials)
│       │   └── header.html
│       ├── article.html     # Template for individual blog posts
│       └── index.html       # Template for the homepage/article listing
├── pelicanconf.py           # Main site settings (theme path, language, pagination)
├── publishconf.py           # Settings specific to production/publishing
├── requirements.txt         # Lists Python dependencies for Netlify
└── Makefile                 # Build scripts
```

### 2.2. Configure the Custom Theme

1.  **Create the theme directory structure:**
    ```bash
    mkdir -p theme/static theme/templates/includes
    ```
2.  **Update `pelicanconf.py`:** Tell Pelican where to find the custom theme. Open **`pelicanconf.py`** and ensure the `THEME` variable points to your custom theme directory:
    ```python
    # pelicanconf.py
    THEME = 'theme'
    ```

### 2.3. Create the Base Layout

The base template defines the common HTML structure and includes the header/footer.

1.  **Create the Base Template:**
      * File: **`theme/templates/base.html`**
    <!-- end list -->
    ```html+jinja
    <!DOCTYPE html>
    <html lang="{{ lang }}">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{% block title %}{{ SITENAME }}{% endblock %}</title>
        <link rel="stylesheet" href="{{ SITEURL }}/theme/css/style.css">
    </head>
    <body>
        {% include 'includes/header.html' %}  <main id="content">
            {% block content %}{% endblock %} </main>

        <footer>
            <p>&copy; {{ CURRENT_YEAR }} {{ AUTHOR }}</p>
        </footer>
    </body>
    </html>
    ```

### 2.4. Create Partials (Includes)

Partials are reusable snippets placed in the `includes` folder.

1.  **Create Header Partial:**
      * File: **`theme/templates/includes/header.html`**
    <!-- end list -->
    ```html+jinja
    <header>
        <nav>
            <a href="{{ SITEURL }}/">Home</a>
            {% for page in pages %}
                <a href="{{ SITEURL }}/{{ page.url }}">{{ page.title }}</a>
            {% endfor %}
        </nav>
    </header>
    ```

### 2.5. Implement the Article Template

This template extends the base layout and adds specific structure for content pages.

1.  **Create the Article Template:**
      * File: **`theme/templates/article.html`**
    <!-- end list -->
    ```html+jinja
    {% extends "base.html" %}
    {% block title %}{{ article.title }}{% endblock %}

    {% block content %}
        <article>
            <h1>{{ article.title }}</h1>
            <p class="meta">Published on: {{ article.date | strftime('%Y-%m-%d') }}</p>
            {{ article.content }}
        </article>
    {% endblock %}
    ```

### 2.6. Manage Static Assets

Pelican serves assets from the theme's `static/` folder using the path `/theme/`.

1.  **Add Custom CSS:**
      * File: **`theme/static/css/style.css`**
    <!-- end list -->
    ```css
    body {
        font-family: sans-serif;
        margin: 0 auto;
        max-width: 960px;
        padding: 20px;
    }
    header {
        border-bottom: 2px solid #333;
        padding-bottom: 15px;
        margin-bottom: 30px;
    }
    ```

-----

## 3\. GitHub Setup

### 3.1. Create Dependencies File

Since Netlify will run the build remotely, you need a list of dependencies.

1.  **Create the dependency file:**
    ```bash
    pip freeze > requirements.txt
    ```
    This file should contain `pelican` and `markdown`.

### 3.2. Verify `.gitignore`

Ensure the build output, environment, and caches are ignored.

```bash
touch .gitignore
echo "/venv/" >> .gitignore
echo "/output/" >> .gitignore
echo "/__pycache__/" >> .gitignore
```

### 3.3. Commit and Push to GitHub

1.  **Initialize Git:**
    ```bash
    git init
    ```
2.  **Add files and commit:**
    ```bash
    git add .
    git commit -m "Initial Pelican site with custom theme and dependencies"
    ```
3.  **Create a new repository on GitHub** (e.g., named `my-pelican-site`).
4.  **Link your local repo and push:** (Replace placeholders)
    ```bash
    git remote add origin https://github.com/<your_username>/<repo_name>.git
    git branch -M main
    git push -u origin main
    ```

-----

## 4\. Netlify Setup for Continuous Deployment

Since Pelican is a Python tool, you must configure the Netlify build process to install the necessary environment.

### 4.1. Sign Up and Import Project

1.  Go to the **Netlify website** and log in.
2.  In the Netlify dashboard, click **"Add new site"** then **"Import an existing project"**.
3.  Select **"GitHub"** and choose your repository (`my-pelican-site`).

### 4.2. Configure Build Settings

Because Pelican requires a custom environment and command, you must specify them manually.

| Setting | Value | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The branch Netlify monitors. |
| **Build command** | **`pelican content -s publishconf.py`** | This command uses the production settings file to generate the final site. |
| **Publish directory** | **`output`** | The directory specified by Pelican's default settings. |

### 4.3. Netlify Build Environment

Netlify will automatically detect the **`requirements.txt`** file and install Python dependencies. You may optionally set the Python version.

1.  In the Netlify deploy settings, click **"Show advanced"**.
2.  Add a new environment variable:
      * **Key:** `PYTHON_VERSION`
      * **Value:** (e.g., `3.10`)

### 4.4. Deploy the Site

Click the **"Deploy site"** button. Netlify will:

1.  Detect and install Python and the packages listed in `requirements.txt`.
2.  Run the build command: `pelican content -s publishconf.py`.
3.  Publish the contents of the **`output`** directory.

Your Pelican site is now live with continuous deployment.

-----

## 5\. Ongoing Workflow

Your Continuous Deployment (CD) pipeline is now fully automated:

1.  Make changes locally (update content in `content/`, edit templates in `theme/templates/`).
2.  Commit your changes:
    ```bash
    git commit -am "Added new article and fixed layout"
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, runs the Pelican build process, and deploys the new version live.