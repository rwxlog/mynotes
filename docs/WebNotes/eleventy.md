An Eleventy (11ty) site deployed with Netlify offers a fast, flexible, and efficient development workflow. Here is a detailed, step-by-step guide to setting it up from scratch, including custom layouts, partials, and asset management.

-----

## 1\. Local Eleventy Setup and Project Initialization

Eleventy is a Node.js-based static site generator, so you'll need Node.js and npm (or yarn/pnpm).

### 1.1. Create and Initialize the Project

1.  **Create a directory and navigate into it:**
    ```bash
    mkdir my-eleventy-site
    cd my-eleventy-site
    ```
2.  **Initialize a Node.js project:**
    ```bash
    npm init -y
    ```
    This creates the default `package.json` file.
3.  **Install Eleventy:**
    ```bash
    npm install @11ty/eleventy
    ```

### 1.2. Configure Scripts

For convenience, add `start` and `build` scripts to your `package.json` file. Open the file and modify the `"scripts"` section:

```json
  "scripts": {
    "start": "npx @11ty/eleventy --serve",
    "build": "npx @11ty/eleventy",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

### 1.3. Define the Input/Output Structure

While Eleventy works with zero configuration, it's best practice to separate your source files. We'll tell Eleventy to look for source files in a **`src`** directory and output the built site to **`public`**.

1.  **Create the configuration file:**
    ```bash
    touch .eleventy.js
    ```
2.  **Add the following configuration to `.eleventy.js`:**
    ```javascript
    module.exports = function(eleventyConfig) {
      // Set directories to pass through to the public folder
      eleventyConfig.addPassthroughCopy("./src/assets/");

      return {
        dir: {
          input: "src", // Source files go here
          includes: "_includes", // Layouts and partials go here
          data: "_data", // Global data files go here
          output: "public" // Built site goes here
        }
      };
    };
    ```
3.  **Create the source directory:**
    ```bash
    mkdir src
    ```

-----

## 2\. Custom Layouts, Partials, and Assets

This section outlines the directory structure and how to implement custom templating.

### 2.1. Project File/Folder Structure Tree

Your project structure, after completing this section, will look like this:

```
my-eleventy-site/
├── .eleventy.js             # Eleventy configuration (input, output, passthrough)
├── .gitignore               # Tells Git to ignore the built site (public/)
├── package.json             # Node.js dependencies and run scripts
├── package-lock.json        # Exact dependency versions
├── src/                     # All source templates, content, layouts, and data
│   ├── _includes/           # Layouts and Partials/Snippets (default setting)
│   │   ├── base.njk         # The main HTML skeleton (base layout)
│   │   ├── header.njk       # Reusable header partial
│   │   └── post.njk         # Layout for individual blog posts
│   ├── _data/               # Global data files (JSON, JS)
│   │   └── site.json        # Global site metadata (e.g., title)
│   ├── assets/              # Static assets copied directly to 'public/assets/'
│   │   ├── css/
│   │   │   └── style.css
│   │   └── images/
│   ├── blog/                # Content folder (creates a 'blog' collection)
│   │   ├── blog.json        # Directory data file (sets default layout for all files in 'blog/')
│   │   └── first-post.md    # Markdown content file
│   └── index.md             # Site homepage content
└── public/                  # ⚠️ Generated output folder (ignored by Git)
```

### 2.2. Create the Layouts Directory and Base Template

Eleventy uses the **`_includes`** directory for both layouts and partials by default. We will use **Nunjucks (`.njk`)** as the template language.

1.  **Create the Includes and Data directories:**
    ```bash
    mkdir -p src/_includes src/_data
    ```
2.  **Create the Global Data file:** This file stores variables accessible everywhere.
      * File: **`src/_data/site.json`**
    <!-- end list -->
    ```json
    {
      "title": "My Eleventy Site",
      "author": "The Dev"
    }
    ```
3.  **Create the Base Layout:** This provides the core HTML structure.
      * File: **`src/_includes/base.njk`**
    <!-- end list -->
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{{ title }} | {{ site.title }}</title>
        <link rel="stylesheet" href="/assets/css/style.css">
    </head>
    <body>
        {% include "header.njk" %} <main>
            {{ content | safe }} </main>

        <footer>
            <p>&copy; {{ "now" | date: "%Y" }} {{ site.author }}</p>
        </footer>
    </body>
    </html>
    ```
    *Note: `{{ content | safe }}` is required to output rendered Markdown/HTML safely.*

### 2.3. Create Partials (Includes)

Partials are reusable components included with the `{% include "filename.njk" %}` tag.

1.  **Create Header Partial:**
      * File: **`src/_includes/header.njk`**
    <!-- end list -->
    ```html
    <header>
        <nav>
            <a href="/">Home</a>
            <a href="/blog/">Blog</a>
            <h1>{{ site.title }}</h1>
        </nav>
    </header>
    ```

### 2.4. Create Content and Layout Chain

We'll set a default layout for all content in the `blog` folder using a Directory Data File.

1.  **Create the Blog Content and Data Directories:**
    ```bash
    mkdir src/blog
    ```
2.  **Create the Post Layout:** This layout will *chain* to the `base.njk` layout.
      * File: **`src/_includes/post.njk`**
    <!-- end list -->
    ```html
    ---
    layout: base.njk
    ---
    <article>
        <h1>{{ title }}</h1>
        <p>Published: {{ date | date: "%B %d, %Y" }}</p>
        {{ content | safe }}
    </article>
    ```
3.  **Set the Default Layout for the Blog Folder:** This avoids repeating `layout: post.njk` in every post's front matter.
      * File: **`src/blog/blog.json`**
    <!-- end list -->
    ```json
    {
      "layout": "post.njk"
    }
    ```
4.  **Create Sample Content:**
      * File: **`src/blog/first-post.md`**
    <!-- end list -->
    ```markdown
    ---
    title: My First Eleventy Post
    date: 2025-10-15
    tags: posts
    ---
    This is the content of my first post. It automatically uses the `post.njk` layout thanks to `blog.json`.
    ```
5.  **Create the Homepage:**
      * File: **`src/index.md`**
    <!-- end list -->
    ```markdown
    ---
    title: Welcome
    layout: base.njk
    ---
    ## Hello World!

    This is the homepage of my new Eleventy site.
    ```

### 2.5. Manage Static Assets (Passthrough Copy)

Assets like CSS and images are copied directly to the output folder using the `addPassthroughCopy` function we added to **`.eleventy.js`** in step 1.3.

1.  **Create Assets Directory:**
    ```bash
    mkdir -p src/assets/css src/assets/images
    ```
2.  **Add a Custom CSS File:**
      * File: **`src/assets/css/style.css`**
    <!-- end list -->
    ```css
    body {
        font-family: sans-serif;
        margin: 0 auto;
        max-width: 960px;
        padding: 20px;
    }
    h1 {
        color: #333;
    }
    ```

### 2.6. Local Test

Run the development server to see your custom setup:

```bash
npm start
```

Your site will be running at `http://localhost:8080/`.

-----

## 3\. GitHub Setup

Prepare the project for version control and Netlify continuous deployment.

### 3.1. Create `.gitignore`

Prevent the large generated output folder and Node modules from being committed.

```bash
touch .gitignore
echo "/node_modules/" >> .gitignore
echo "/public/" >> .gitignore
```

*Note: We ignore `/public/` because Netlify will generate it for us.*

### 3.2. Commit and Push to GitHub

1.  **Initialize Git:**
    ```bash
    git init
    ```
2.  **Add all files and commit:**
    ```bash
    git add .
    git commit -m "Initial Eleventy setup with custom layouts and Netlify config"
    ```
3.  **Create a new repository on GitHub** (e.g., named `my-eleventy-site`).
4.  **Link your local repo and push:** (Replace placeholders)
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
3.  Select **"GitHub"** and authorize Netlify.
4.  Find and select your repository (`my-eleventy-site`).

### 4.2. Configure Build Settings

Netlify's automatic framework detection is excellent, but we can be explicit based on our custom setup.

| Setting | Value | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The main branch of your repository. |
| **Build command** | `npm run build` | Runs the custom build script defined in `package.json`. |
| **Publish directory** | **`public`** | The custom output folder defined in `.eleventy.js`. |

### 4.3. Deploy the Site

Click the **"Deploy site"** button. Netlify will:

1.  Fetch your GitHub code.
2.  Install Node.js dependencies (`npm install`).
3.  Run the build command (`npm run build`), which executes Eleventy.
4.  Publish the contents of the **`public`** directory to the live web.

Once the deployment completes, your live Eleventy site will be available at a temporary Netlify URL.

-----

## 5\. Ongoing Workflow

The Continuous Deployment (CD) pipeline is now established:

1.  Make changes locally (add new content, edit layouts, update CSS).
2.  Commit your changes:
    ```bash
    git commit -am "Added blog post and fixed header alignment"
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, runs the build process, and deploys your changes live\! 🎉