Setting up a **Hexo** site with Netlify provides a fast, Node.js-based publishing workflow. This guide covers the full process, from local Hexo setup and customization to automated deployment via Git and Netlify.

-----

## 1\. Local Hexo Setup and Project Initialization

Hexo is a Node.js-based framework. You'll need **Node.js** and npm (or yarn/pnpm).

### 1.1. Install Prerequisites and Hexo CLI

1.  **Install Node.js** (LTS version recommended).
2.  **Install the Hexo CLI globally:**
    ```bash
    npm install -g hexo-cli
    # OR: yarn global add hexo-cli
    ```

### 1.2. Create a New Hexo Site

The CLI will scaffold a new project with all necessary files.

1.  **Create the site:**
    ```bash
    hexo init my-hexo-site
    ```
2.  **Navigate into the directory and install dependencies:**
    ```bash
    cd my-hexo-site
    npm install
    # OR: yarn install
    ```

### 1.3. Local Test

Run the development server to verify the default site.

```bash
hexo server
```

Your site will be running at `http://localhost:4000/`. Press **Ctrl+C** to stop the server.

-----

## 2\. Custom Layouts, Partials, and Static Assets

Hexo uses the **EJS** or **Swig** templating engine (depending on the theme) and organizes templates within the **`themes/`** folder. The default theme is usually called `landscape`.

### 2.1. Project File/Folder Structure Tree

Your project structure, after customization, will look like this:

```
my-hexo-site/
├── node_modules/            # Node.js dependencies
├── public/                  # ⚠️ Generated static site (Publish Directory for Netlify)
├── source/                  # Source content files (Markdown, Pages)
│   ├── _posts/
│   │   └── hello-world.md   # Example blog post
│   └── about/
│       └── index.md         # Content for an '/about' page
├── scaffolds/               # Boilerplate files for new posts/pages
├── themes/                  # All installed themes
│   └── landscape/           # The active theme directory (or your custom theme)
│       ├── _config.yml      # Theme-specific configuration
│       ├── layout/          # Templates for page types (Jinja2/EJS/Swig)
│       │   ├── _partial/    # Reusable snippets (Partials)
│       │   │   ├── footer.ejs
│       │   │   └── header.ejs
│       │   ├── layout.ejs   # The main HTML skeleton (Layout)
│       │   └── post.ejs     # Template for individual posts
│       └── source/          # Static theme assets (CSS, JS, images)
│           └── css/
│               └── style.css # Custom CSS file
├── _config.yml              # Main site configuration (URL, theme name, title)
├── package.json             # Node.js dependencies and run scripts
└── package-lock.json
```

### 2.2. Identify the Theme and Layouts

1.  **Check the active theme:** Open **`_config.yml`** (at the root) and note the `theme:` setting (e.g., `theme: landscape`).
2.  **Locate the Layout:** The main wrapper is usually located in **`themes/<theme_name>/layout/layout.ejs`** (or `.swig`, `.pug`, etc.).

### 2.3. Customize Layout and Partials

You should **never edit files directly in `themes/<theme_name>`** as they will be overwritten during updates. Instead, **override** them by copying the file structure into the project root. However, for simplicity here, we'll demonstrate using the default theme structure.

1.  **Find the main template:** Open **`themes/landscape/layout/layout.ejs`**. This file defines the global structure, including `<head>`, and usually includes **Partials** like `header.ejs` and `footer.ejs`.
2.  **Customize the Header Partial:**
      * File: **`themes/landscape/layout/_partial/header.ejs`**
      * Add custom navigation links (e.g., to an "About" page, which is created via `hexo new page about`):
    <!-- end list -->
    ```html+ejs
    <nav>
        <a href="<%- url_for('/') %>">Home</a>
        <a href="<%- url_for('/about') %>">About</a>
    </nav>
    ```

### 2.4. Manage Static Assets

Assets placed directly in the **`themes/<theme_name>/source/`** directory are copied to the final output.

1.  **Locate the CSS directory:** Usually **`themes/landscape/source/css/`**.
2.  **Add Custom Styles:** Add your custom CSS to the theme's CSS files (or create a new one and link it in the `layout.ejs` file).
      * File: **`themes/landscape/source/css/style.css`** (or similar file in your theme).

### 2.5. Final Local Check

1.  **Clean and build the site:**
    ```bash
    hexo clean
    hexo generate
    ```
    *This creates the static HTML files in the **`public/`** directory.*
2.  **Serve the static output:**
    ```bash
    hexo server
    ```

-----

## 3\. GitHub Setup

### 3.1. Verify `.gitignore`

The starter includes a `.gitignore` that correctly ignores the built site. Ensure it contains:

```
# Hexo generated files
.DS_Store
Thumbs.db
db.json
*.log

# Build output
/public/
```

### 3.2. Commit and Push to GitHub

1.  **Add all files and commit:**
    ```bash
    git add .
    git commit -m "Initial Hexo site with custom navigation"
    ```
2.  **Create a new repository on GitHub** (e.g., named `my-hexo-netlify`).
3.  **Link your local repo and push:** (Replace placeholders)
    ```bash
    git remote add origin https://github.com/<your_username>/<repo_name>.git
    git branch -M main
    git push -u origin main
    ```

-----

## 4\. Netlify Setup for Continuous Deployment

### 4.1. Sign Up and Import Project

1.  Go to the **Netlify website** and log in.
2.  In the Netlify dashboard, click **"Add new site"** then **"Import an existing project"**.
3.  Select **"GitHub"** and choose your repository (`my-hexo-netlify`).

### 4.2. Configure Build Settings

Netlify often auto-detects Node.js projects but requires explicit configuration for Hexo's build command and output directory.

| Setting | Value | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The branch Netlify monitors. |
| **Build command** | **`hexo generate`** | The command to generate the static site. |
| **Publish directory** | **`public`** | The folder Hexo outputs the static files to. |

### 4.3. Deploy the Site

Click the **"Deploy site"** button. Netlify will:

1.  Fetch your GitHub code.
2.  Install Node.js dependencies.
3.  Run the build command (`hexo generate`).
4.  Publish the contents of the **`public`** directory.

Your Hexo site will be live on a temporary Netlify URL.

-----

## 5\. Ongoing Workflow

The Continuous Deployment (CD) pipeline is now established:

1.  Make changes locally (e.g., run `hexo new "My New Post"`).
2.  Commit your changes:
    ```bash
    git commit -am "Added new blog post and updated layout."
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, runs the `hexo generate` command, and deploys the new version live.