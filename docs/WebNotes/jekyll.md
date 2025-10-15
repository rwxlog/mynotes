Setting up a Jekyll site with Netlify is straightforward and provides a great workflow for static site publishing. Here is a detailed, step-by-step guide covering the entire process, including using custom layouts, partials, and assets.

-----

## 1\. Local Jekyll Setup and Project Creation

This section covers installing Jekyll and creating your basic site.

### 1.1. Install Prerequisites (Ruby and Bundler)

Jekyll is a Ruby Gem, so you need a working Ruby environment.

1.  **Install Ruby:** Ensure you have Ruby installed (version 2.5 or higher). It's best to use a version manager like **RVM** or **rbenv** on macOS/Linux, or **RubyInstaller** on Windows.
2.  **Install Bundler:** Bundler manages the project's Ruby Gems.
    ```bash
    gem install bundler
    ```

### 1.2. Install Jekyll and Create a New Site

Create a new Jekyll site using the default template.

1.  **Create the site:**
    ```bash
    jekyll new my-jekyll-site
    cd my-jekyll-site
    ```
2.  **Install dependencies:** Use Bundler to install the necessary gems, including Jekyll.
    ```bash
    bundle install
    ```

### 1.3. Local Test

Run the development server to check the default site.

```bash
bundle exec jekyll serve
```

View your site at `http://localhost:4000`. Press **Ctrl+C** to stop the server.

-----

## 2\. Custom Layouts, Partials, and Assets

Jekyll's template system is based on the Liquid templating language and uses specific directories for customization.

### 2.1. Project File/Folder Structure Tree

After completing the customization steps, your project structure will look like this:

```
my-jekyll-site/
├── .gitignore               # Tells Git to ignore the built site (_site)
├── _config.yml              # Primary site configuration (global variables)
├── Gemfile                  # List of Ruby Gems used by the project
├── Gemfile.lock             # Exact versions of installed Gems
├── index.html               # The site's main page
├── _posts/                  # Your Markdown content for blog posts
│   └── 2025-10-15-first-post.md
├── _layouts/                # HTML templates for pages/posts
│   ├── default.html         # The main HTML skeleton (base layout)
│   └── post.html            # Layout specifically for blog posts
├── _includes/               # Reusable HTML snippets (partials)
│   ├── footer.html          # Site footer content
│   └── header.html          # Site navigation/header
└── assets/                  # Static assets that are copied directly
    └── css/
        └── style.css        # Your custom CSS file
```

### 2.2. Create Custom Layouts

Layouts are the HTML wrappers for your content. They live in the **`_layouts/`** directory.

1.  **Modify the Default Layout:** This acts as the base HTML shell.

      * Open **`_layouts/default.html`** (or create it if it doesn't exist).
      * Ensure the structure includes Liquid tags to pull in your header, content, and footer:

    <!-- end list -->

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{{ page.title | default: site.title }}</title>
        <link rel="stylesheet" href="/assets/css/style.css">
    </head>
    <body>
        {% include header.html %} <main>
            {{ content }} </main>

        {% include footer.html %} </body>
    </html>
    ```

2.  **Create a Post Layout:** A layout specifically for blog posts.

      * Create file: **`_layouts/post.html`**
      * Add front matter specifying the base layout:

    <!-- end list -->

    ```html
    ---
    layout: default
    ---
    <article>
        <h1>{{ page.title }}</h1>
        <p class="post-meta">Published on: {{ page.date | date: "%b %d, %Y" }}</p>
        {{ content }}
    </article>
    ```

3.  **Apply the Layout:** Open your sample post in `_posts/` and ensure the front matter uses the new layout:

    ```markdown
    ---
    layout: post
    title: "My First Custom Post"
    date: 2025-10-15 10:00:00 +0600
    ---
    This post is using the custom `post.html` layout.
    ```

### 2.3. Create Partials (Includes)

Partials are reusable snippets placed in the **`_includes/`** directory.

1.  **Create Includes Directory:**
    ```bash
    mkdir _includes
    ```
2.  **Create Header Partial:**
      * Create file: **`_includes/header.html`**
    <!-- end list -->
    ```html
    <header>
        <nav>
            <a href="/">{{ site.title }}</a>
            <a href="/about/">About</a>
        </nav>
    </header>
    ```
3.  **Create Footer Partial:**
      * Create file: **`_includes/footer.html`**
    <!-- end list -->
    ```html
    <footer>
        <p>&copy; {{ site.time | date: "%Y" }} {{ site.title }}. All rights reserved.</p>
    </footer>
    ```

### 2.4. Add Static Assets

Assets that should be served directly (like images, CSS, or client-side JavaScript) go into a root-level folder like **`assets/`**.

1.  **Create Assets Directory:**
    ```bash
    mkdir -p assets/css
    ```
2.  **Add a Custom CSS File:**
      * Create file: **`assets/css/style.css`**
      * Add some basic styling:
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

### 2.5. Final Local Check

Run the server again to confirm all your custom files are working:

```bash
bundle exec jekyll serve
```

-----

## 3\. GitHub Setup

We'll prepare the site for version control and Netlify deployment.

### 3.1. Verify `.gitignore`

The default Jekyll setup usually includes a `.gitignore` that correctly ignores the built site. Ensure it contains:

```
# Ignore Jekyll's build directory
_site/
# Ignore Bundler files
.bundle/
vendor/
```

### 3.2. Commit and Push to GitHub

1.  **Add files and commit:**
    ```bash
    git init
    git add .
    git commit -m "Initial Jekyll site with custom layouts and assets"
    ```
2.  **Create a new repository on GitHub** (e.g., named `my-jekyll-site`).
3.  **Link your local repo and push:** (Replace `<your_username>` and `<repo_name>`)
    ```bash
    git remote add origin https://github.com/<your_username>/<repo_name>.git
    git branch -M main
    git push -u origin main
    ```

-----

## 4\. Netlify Setup for Continuous Deployment

Netlify will monitor your GitHub repository to automatically build and deploy your site.

### 4.1. Sign Up and Import Project

1.  Go to **Netlify** and log in or sign up.
2.  In the Netlify dashboard, click **"Add new site"** then **"Import an existing project"**.
3.  Select **"GitHub"** (or your Git provider) and authorize Netlify.
4.  Find and select your repository (e.g., `my-jekyll-site`).

### 4.2. Configure Build Settings

Netlify is optimized for Jekyll and often auto-detects the required settings.

| Setting | Value (Default for Jekyll) | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The main branch of your repository. |
| **Build command** | `jekyll build` or `bundle exec jekyll build` | Netlify typically handles the environment, so `jekyll build` often suffices, but `bundle exec jekyll build` is the safer command if you use many gems. |
| **Publish directory** | **`_site`** | The folder Jekyll outputs the static files to. |

### 4.3. Deploy the Site

Click the **"Deploy site"** button. Netlify will:

1.  Fetch your code.
2.  Install the required Ruby environment and dependencies (Gems from `Gemfile.lock`).
3.  Run the build command (`jekyll build`).
4.  Publish the contents of the `_site` directory.

Once the deployment finishes, your live site will be available at the temporary Netlify URL (e.g., `https://random-name-12345.netlify.app/`). You can customize the site name in the Netlify dashboard.

-----

## 5\. Ongoing Workflow

Your Continuous Deployment (CD) pipeline is now active:

1.  Make content, layout, or asset changes locally.
2.  Commit your changes:
    ```bash
    git commit -am "New post and style update"
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, initiates a new build, and deploys the updated version live.