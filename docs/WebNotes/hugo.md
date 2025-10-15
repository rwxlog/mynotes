This is a detailed, step-by-step guide to setting up a Hugo site with custom layouts, partials, and static assets, and deploying it automatically via Netlify using GitHub for continuous deployment.

-----

## 1\. Local Hugo Setup and Project Creation

This section covers installing Hugo and setting up the basic site structure.

### 1.1. Install Hugo

The installation method depends on your operating system. For simplicity, using a package manager is usually best.

  - **macOS (using Homebrew):**
    ```bash
    brew install hugo
    ```
  - **Windows (using Chocolatey):**
    ```bash
    choco install hugo -confirm
    ```
  - **Linux (or manually):** See the official Hugo documentation for your distribution.

### 1.2. Create a New Hugo Site

Navigate to where you want to create your project and run:

```bash
hugo new site my-hugo-site
cd my-hugo-site
```

This creates the default directory structure.

### 1.3. Initialize Git

Initialize a local Git repository in the new site directory:

```bash
git init
```

### 1.4. Add a Basic Content File

Create your first content file (this will use a default template later):

```bash
hugo new content posts/first-post.md
```

Open `content/posts/first-post.md` and change `draft: true` to **`draft: false`** if you want it to build immediately. Add some content below the front matter.

-----

## 2\. Custom Layouts, Partials, and Assets

Hugo uses a specific file structure for templates and assets. We will use the main project directories to override or create new components.

### 2.1. Project File/Folder Structure Tree

After completing the steps in this section, your project structure should look like this:

```
my-hugo-site/
├── .gitignore               # Tells Git to ignore /public and /resources
├── archetypes/              # Default front matter templates for new content
├── assets/                  # For files Hugo needs to process (e.g., SASS/SCSS, images for Hugo Pipes)
├── config.toml              # Primary site configuration file
├── content/                 # All your Markdown content files
│   └── posts/
│       └── first-post.md
├── data/                    # Store structured data files (JSON, YAML, TOML) for use in templates
├── layouts/                 # All HTML templates, overriding any theme files
│   ├── _default/            # Default templates for pages
│   │   ├── baseof.html      # The main HTML skeleton for all pages (required)
│   │   ├── list.html        # Template for list pages (e.g., /posts/)
│   │   └── single.html      # Template for single pages (e.g., a blog post)
│   └── partials/            # Reusable template snippets
│       ├── footer.html      # Site footer
│       ├── head_includes.html # Code for the <head> section (meta, CSS links)
│       └── nav.html         # Site navigation menu
├── public/                  # ⚠️ Generated output folder (ignored by Git)
├── resources/               # ⚠️ Generated cache for processed assets (ignored by Git)
└── static/                  # Static assets copied directly to the final site root
    └── css/
        └── style.css        # Custom CSS file
```

### 2.2. Create Custom Layouts

Since you are creating custom layouts, you will work primarily in the **`layouts/`** directory. We'll set up a base template and simple list/single layouts.

1.  **Create the Base Template:** This file provides the skeleton (`<html>`, `<head>`, `<body>`) for all other layouts.

      * Create a directory: `mkdir -p layouts/_default`
      * Create the base file: `touch layouts/_default/baseof.html`

    In `layouts/_default/baseof.html`, add the basic HTML structure, using the `block` function for sections other layouts can define:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        {{ partial "head_includes.html" . }} 
        <title>{{ block "title" . }} {{ .Site.Title }} {{ end }}</title>
    </head>
    <body>
        <header>
            {{ partial "nav.html" . }} 
        </header>

        <main>
            {{ block "main" . }}
            {{ end }}
        </main>

        <footer>
            {{ partial "footer.html" . }}
        </footer>
    </body>
    </html>
    ```

2.  **Create the Single and List Layouts:** These templates define the main content area using the `main` block from `baseof.html`.

      * **Single Page Layout** (for individual posts):
          * Create file: `touch layouts/_default/single.html`
        <!-- end list -->
        ```html
        {{ define "main" }}
        <article>
            <h1>{{ .Title }}</h1>
            <p>Published: {{ .Date.Format "January 2, 2006" }}</p>
            {{ .Content }}
        </article>
        {{ end }}
        ```
      * **List Page Layout** (for the homepage, sections, or taxonomies):
          * Create file: `touch layouts/_default/list.html`
        <!-- end list -->
        ```html
        {{ define "main" }}
        <h1>{{ .Title }}</h1>
        <ul>
        {{ range .Pages }}
            <li>
                <a href="{{ .RelPermalink }}">{{ .Title }}</a>
                <p>{{ .Summary }}</p>
            </li>
        {{ end }}
        </ul>
        {{ end }}
        ```

### 2.3. Create Partials

Partials are reusable snippets of code. Create them in the **`layouts/partials/`** directory.

1.  **Create Partials Directory:**
    ```bash
    mkdir layouts/partials
    ```
2.  **Create `head_includes.html` (for CSS/Meta):**
    ```bash
    touch layouts/partials/head_includes.html
    ```
    Add a link to your CSS file:
    ```html
    <link rel="stylesheet" href="/css/style.css">
    ```
3.  **Create `nav.html` and `footer.html`:**
    ```bash
    touch layouts/partials/nav.html
    touch layouts/partials/footer.html
    ```
      * **`nav.html`:**
        ```html
        <nav>
            <a href="/">Home</a>
            <a href="/posts/">Posts</a>
        </nav>
        ```
      * **`footer.html`:**
        ```html
        <footer>
            <p>&copy; {{ now.Year }} {{ .Site.Title }}</p>
        </footer>
        ```

### 2.4. Manage Static Assets

The **`static/`** directory is for assets that **do not need any processing** (e.g., plain CSS, JS, images, `robots.txt`, `favicon.ico`). Hugo copies everything in `static/` directly to the root of the `public/` directory during the build.

1.  **Create Assets Directories:**
    ```bash
    mkdir -p static/css static/images
    ```
2.  **Add a Custom CSS File:**
      * Create file: `touch static/css/style.css`
      * Add some basic styling:
        ```css
        body {
            font-family: sans-serif;
            margin: 0 auto;
            max-width: 800px;
            padding: 20px;
        }
        header {
            border-bottom: 1px solid #ccc;
            padding-bottom: 10px;
            margin-bottom: 20px;
        }
        ```
    *(The asset path `/css/style.css` is relative to the `static` folder, which is copied to the root.)*

### 2.5. Local Test

Run the Hugo development server to see your custom setup:

```bash
hugo server -D
```

You can now view your site at `http://localhost:1313/` and see your custom layouts, partials, and styles applied. Press **Ctrl+C** to stop the server.

-----

## 3\. GitHub Setup

Continuous deployment relies on a Git repository (like GitHub) that Netlify can monitor.

### 3.1. Create `.gitignore`

Prevent unnecessary files from being uploaded.

```bash
touch .gitignore
echo "/public/" >> .gitignore
echo "/resources/" >> .gitignore
echo "temp/" >> .gitignore
```

*Note: Hugo's `public/` directory is the final build output and should not be tracked by Git.*

### 3.2. Commit and Push to GitHub

1.  **Add files and commit:**
    ```bash
    git add .
    git commit -m "Initial Hugo site structure with custom templates and assets"
    ```
2.  **Create a new repository on GitHub** (e.g., named `my-hugo-site`).
3.  **Link your local repo and push:** (Replace `<your_username>` and `<repo_name>` as necessary)
    ```bash
    git remote add origin https://github.com/<your_username>/<repo_name>.git
    git branch -M main
    git push -u origin main
    ```

-----

## 4\. Netlify Setup for Continuous Deployment

Netlify will monitor your GitHub repository and automatically build and deploy your site whenever you push changes to the `main` branch.

### 4.1. Sign Up and Import Project

1.  Go to the **Netlify website** and log in or sign up.
2.  In the Netlify dashboard, click **"Add new site"** then **"Import an existing project"**.
3.  Select **"GitHub"** (or your preferred Git provider) and authorize Netlify to access your repositories.
4.  Find and select the repository you just pushed (e.g., `my-hugo-site`).

### 4.2. Configure Build Settings

On the deployment settings screen, Netlify will usually auto-detect Hugo. Verify the settings:

| Setting | Value (Default for Hugo) | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The main branch of your repository. |
| **Build command** | `hugo` | The command Hugo uses to generate the site. |
| **Publish directory** | **`public`** | The folder Hugo outputs the static files to. |

### 4.3. Set Environment Variable (Optional but Recommended)

If you need a specific Hugo version, or if the build fails, you should set the `HUGO_VERSION` environment variable.

1.  In the Netlify deploy settings, click **"Show advanced"**.
2.  Click **"New variable"** under **"Environment variables"**.
3.  Set:
      * **Key:** `HUGO_VERSION`
      * **Value:** (e.g., `0.125.7` - check your local version with `hugo version`)

### 4.4. Deploy the Site

Click the **"Deploy site"** button. Netlify will fetch your GitHub repo, run the `hugo` build command, and publish the contents of the `public` directory.

Once the deployment is complete, Netlify will give you a temporary URL (e.g., `https://random-name-12345.netlify.app/`) where your live site is available. You can change this site name in the **Site settings** within the Netlify dashboard.

-----

## 5\. Ongoing Workflow

Your setup is now complete\! The beauty of this is the **Continuous Deployment (CD)** pipeline:

1.  Make changes locally in your Hugo project (content, layouts, partials, static assets).
2.  Commit your changes:
    ```bash
    git commit -am "My next awesome change"
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push to the `main` branch, rebuilds your Hugo site, and deploys the new version live\! 🎉