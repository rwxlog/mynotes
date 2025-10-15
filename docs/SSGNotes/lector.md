While there is a Python-based static site generator named **Lektor**, it is not as widely known as Jekyll, Hugo, or Eleventy, and its setup process requires a specific Python environment and configuration for assets.

Here is a detailed, step-by-step guide to setting up a **Lektor** site with Netlify, covering custom layouts, assets, and the necessary Python configuration for deployment.

-----

## 1\. Local Lektor Setup and Project Initialization

Lektor requires Python and its package manager, `pip`.

### 1.1. Install Prerequisites

1.  **Install Python** (3.7+ recommended).
2.  **Install Lektor** globally using `pip`:
    ```bash
    pip install lektor
    ```

### 1.2. Create a New Lektor Site

Lektor provides a CLI wizard to create a basic project structure.

1.  **Create the site:**

    ```bash
    lektor quickstart
    ```

2.  **Follow the setup wizard:**

      * **Project Title:** `My Lektor Site`
      * **Output Path:** `my-lektor-site` (or your preferred directory name)
      * **Package Name:** `my-lektor-site`
      * **Basic or Advanced Project:** Choose **Basic** for the simplest start.

3.  **Navigate into the new directory:**

    ```bash
    cd my-lektor-site
    ```

### 1.3. Local Test

Run the development server to verify the default site.

```bash
lektor server
```

Your site will be running at `http://127.0.0.1:8000/`. Press **Ctrl+C** to stop the server.

-----

## 2\. Custom Layouts, Partials, and Assets

Lektor uses its own templating language, **Jinja2**, and organizes templates, content, and assets in a structured way.

### 2.1. Project File/Folder Structure Tree

After completing this section, your project structure will look like this:

```
my-lektor-site/
├── .gitignore               # Tells Git to ignore the built site (output/)
├── configs/                 # Configuration for Lektor plugins, etc.
├── content/                 # All content files (including Markdown, .ini files, etc.)
│   ├── blog/
│   │   └── first-post/
│   │       └── contents.lr  # Content for a single blog post
│   └── root/
│       └── contents.lr      # Content for the site homepage (index)
├── assets/                  # Non-template files copied directly to the output
│   └── static/
│       └── css/
│           └── style.css    # Custom CSS file
├── models/                  # Defines the structure of content types (e.g., page, blog-post)
│   ├── page.ini
│   └── root.ini
├── templates/               # All Jinja2 templates for pages and reusable blocks
│   ├── layout.html          # The main HTML skeleton (Layout)
│   ├── macros/
│   │   └── nav.html         # Reusable HTML snippets (Partial)
│   └── page.html            # Template for a standard page type
└── .lektorproject           # Main project configuration file
```

### 2.2. Create the Custom Layout

In Lektor, the main wrapper for all pages is typically called **`layout.html`**.

1.  **Create the Layout Template:**
      * File: **`templates/layout.html`**
    <!-- end list -->
    ```html+jinja
    <!doctype html>
    <html lang="en">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{{ this.title }} | {{ site.title }}</title>
        <link rel="stylesheet" href="{{ '/static/css/style.css'|url }}">
    </head>
    <body>
        <header>
            {% import 'macros/nav.html' as nav %}
            {{ nav.main_nav() }} {# Include the navigation partial #}
        </header>

        <main>
            {# The actual content of the page template is rendered here #}
            {% block body %}
            {% endblock %}
        </main>

        <footer>
            <p>&copy; {{ now.year }} {{ site.title }}</p>
        </footer>
    </body>
    </html>
    ```

### 2.3. Create Partials (Macros)

Reusable template snippets are usually placed in a subfolder like **`templates/macros/`** and imported as Jinja2 macros.

1.  **Create the Macros Directory:**
    ```bash
    mkdir -p templates/macros
    ```
2.  **Create the Navigation Partial (Macro):**
      * File: **`templates/macros/nav.html`**
    <!-- end list -->
    ```html+jinja
    {% macro main_nav() %}
        <nav>
            <a href="{{ '/'|url }}">Home</a>
            <a href="{{ '/blog'|url }}">Blog</a>
        </nav>
    {% endmacro %}
    ```

### 2.4. Apply the Layout

Your content templates (like `page.html`) must explicitly extend the `layout.html`.

1.  **Update the Page Template:**
      * File: **`templates/page.html`**
    <!-- end list -->
    ```html+jinja
    {% extends "layout.html" %}
    {% block body %}
        <h1>{{ this.title }}</h1>
        {{ this.body }}
    {% endblock %}
    ```

### 2.5. Manage Static Assets

Assets are handled through the **`assets/`** directory. Lektor preserves the directory structure under `assets/` and copies it to the output folder.

1.  **Create Assets Directory:**
    ```bash
    mkdir -p assets/static/css
    ```
2.  **Add a Custom CSS File:**
      * File: **`assets/static/css/style.css`**
    <!-- end list -->
    ```css
    body {
        font-family: sans-serif;
        margin: 0 auto;
        max-width: 960px;
        padding: 20px;
    }
    header {
        border-bottom: 1px solid #ddd;
        padding-bottom: 15px;
        margin-bottom: 30px;
    }
    ```

### 2.6. Final Local Check

Run the server again:

```bash
lektor server
```

Check `http://127.0.0.1:8000/` to ensure your layouts, partials, and styles are applied.

-----

## 3\. GitHub Setup

### 3.1. Create `.gitignore`

Ensure the build output and cache are ignored by Git.

```bash
touch .gitignore
echo "/.lektor/" >> .gitignore
echo "/.lektor_cache/" >> .gitignore
echo "/output/" >> .gitignore
```

### 3.2. Commit and Push to GitHub

1.  **Initialize Git:**
    ```bash
    git init
    ```
2.  **Add all files and commit:**
    ```bash
    git add .
    git commit -m "Initial Lektor site with custom templates and assets"
    ```
3.  **Create a new repository on GitHub** (e.g., named `my-lektor-site`).
4.  **Link your local repo and push:** (Replace placeholders)
    ```bash
    git remote add origin https://github.com/<your_username>/<repo_name>.git
    git branch -M main
    git push -u origin main
    ```

-----

## 4\. Netlify Setup for Continuous Deployment

Since Lektor is a Python tool, you must explicitly tell Netlify to install the necessary Python environment and dependencies.

### 4.1. Configure Build Environment

Create a **runtime dependency file** so Netlify knows to install Lektor.

1.  **Create the dependency file:**
    ```bash
    touch requirements.txt
    ```
2.  **Add Lektor to the file:**
      * File: **`requirements.txt`**
    <!-- end list -->
    ```
    lektor
    ```

### 4.2. Sign Up and Import Project

1.  Go to the **Netlify website** and log in.
2.  In the Netlify dashboard, click **"Add new site"** then **"Import an existing project"**.
3.  Select **"GitHub"** and choose your repository (`my-lektor-site`).

### 4.3. Configure Build Settings

Because Lektor isn't autodetected, you must manually specify the build command and output directory.

| Setting | Value | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The branch Netlify monitors. |
| **Build command** | **`lektor build --output output`** | Tells Lektor to build the site and specify the output folder. |
| **Publish directory** | **`output`** | The folder Lektor outputs the static files to. |

### 4.4. Set Environment Variable (Optional)

You may need to ensure Netlify uses a Python version compatible with Lektor.

1.  In the Netlify deploy settings, click **"Show advanced"**.
2.  Add a new environment variable:
      * **Key:** `PYTHON_VERSION`
      * **Value:** (e.g., `3.10`)

### 4.5. Deploy the Site

Click the **"Deploy site"** button. Netlify will:

1.  Detect `requirements.txt` and install Python and Lektor.
2.  Run the build command: `lektor build --output output`.
3.  Publish the contents of the **`output`** directory.

Your Lektor site is now live with continuous deployment.

-----

## 5\. Ongoing Workflow

The Continuous Deployment (CD) pipeline is now active:

1.  Make changes locally (update content, layouts, or assets).
2.  Commit your changes:
    ```bash
    git commit -am "Added new blog entry and updated nav macro"
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, runs the Lektor build, and deploys the new version live.