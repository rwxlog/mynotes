Setting up a **Gridsome** site with Netlify provides a powerful, GraphQL-powered publishing workflow based on Vue.js. This guide covers the complete process from local setup to continuous deployment.

-----

## 1\. Local Gridsome Setup and Project Initialization

Gridsome is a Node.js-based framework and requires Node.js, npm (or yarn/pnpm), and the Gridsome CLI.

### 1.1. Install Prerequisites and Gridsome CLI

1.  **Install Node.js** (LTS version recommended).
2.  **Install the Gridsome CLI globally:**
    ```bash
    npm install --global @gridsome/cli
    # OR: yarn global add @gridsome/cli
    ```

### 1.2. Create a New Gridsome Site

We'll start with the default starter, which provides a clean structure.

1.  **Create the site:**
    ```bash
    gridsome create my-gridsome-site
    cd my-gridsome-site
    ```
    *This command clones the default starter, installs dependencies, and initializes a basic Gridsome project.*

### 1.3. Local Test

Run the development server to check the basic setup.

```bash
gridsome develop
```

Your site will be running at `http://localhost:8080/`. Press **Ctrl+C** to stop the server.

-----

## 2\. Custom Layouts, Partials, and Static Assets

Gridsome uses a Vue.js component structure where **Layouts** wrap pages, and **Components** act as reusable partials.

### 2.1. Project File/Folder Structure Tree

Your project structure, after customization, will look like this:

```
my-gridsome-site/
├── node_modules/            # Node.js dependencies
├── dist/                    # ⚠️ Generated static site (ignored by Git)
├── src/                     # All source code, components, templates, and assets
│   ├── assets/              # Webpacked assets (JS, SCSS, images imported in components)
│   │   └── style.css        # Custom CSS imported globally
│   ├── components/          # Reusable Vue components (partials/UI elements)
│   │   ├── Footer.vue
│   │   └── Header.vue
│   ├── layouts/             # The main page wrappers (Layouts)
│   │   └── Default.vue      # Primary site shell, defines HTML structure
│   ├── pages/               # Vue components that become site routes
│   │   ├── Index.vue        # The site homepage (/)
│   │   └── About.vue        # The '/about' page
│   └── templates/           # Templates for programmatically generated content (e.g., blog posts)
├── static/                  # Static assets copied directly to the final output (dist/)
│   └── favicon.png
├── gridsome.config.js       # Main configuration (plugins, sources, global data)
├── gridsome.server.js       # Node.js API to extend GraphQL data, etc.
├── package.json             # Node.js dependencies and run scripts
└── .env                     # Environment variables (ignored by Git)
```

### 2.2. Create the Custom Layout

Layouts define the core HTML structure and wrap page content using the `<slot />` element.

1.  **Open the Default Layout:**

      * File: **`src/layouts/Default.vue`**

2.  **Structure the Layout:** Ensure it includes the `<Header>` and `<Footer>` components.

    ```vue
    <template>
      <div id="app">
        <Header />
        <main class="layout">
          <slot/> </main>
        <Footer />
      </div>
    </template>

    <script>
    import Header from '@/components/Header.vue'
    import Footer from '@/components/Footer.vue'

    export default {
      components: {
        Header,
        Footer
      }
    }
    </script>
    ```

### 2.3. Create Components (Partials)

Components are reusable elements stored in the `src/components/` folder.

1.  **Create Header Component:**
      * File: **`src/components/Header.vue`**
    <!-- end list -->
    ```vue
    <template>
      <header>
        <nav>
          <g-link to="/">Home</g-link>
          <g-link to="/about">About</g-link>
        </nav>
      </header>
    </template>
    ```
2.  **Create Footer Component:**
      * File: **`src/components/Footer.vue`**
    <!-- end list -->
    ```vue
    <template>
      <footer>
        <p>&copy; {{ new Date().getFullYear() }} Gridsome Site</p>
      </footer>
    </template>
    ```

### 2.4. Apply the Layout to Pages

The starter already has an `Index.vue` page, which should automatically use the `Default.vue` layout.

1.  **Check the Homepage:**
      * File: **`src/pages/Index.vue`**
      * It should contain: `<Layout>...</Layout>`.

### 2.5. Manage Static Assets

Gridsome handles assets in two ways:

1.  **Assets (`src/assets/`):** For files you want **webpacked**, minified, and processed (like global CSS or images used in components).
      * File: **`src/assets/style.css`** (Add some CSS here).
2.  **Static (`static/`):** For files you want **copied directly** to the final output root (`dist/`). Best for `favicon.ico`, `robots.txt`, or large images.
      * Create the `static` folder at the project root.

### 2.6. Global Asset Import (Optional)

To apply global CSS, import the asset into the layout component.

1.  **Update the Default Layout Script:**
      * File: **`src/layouts/Default.vue`**
    <!-- end list -->
    ```javascript
    <script>
    import Header from '@/components/Header.vue'
    import Footer from '@/components/Footer.vue'
    import '@/assets/style.css' // Global CSS import

    export default {
    // ...
    }
    </script>
    ```

-----

## 3\. GitHub Setup

### 3.1. Verify `.gitignore`

The starter includes a `.gitignore` that correctly ignores the built site. Ensure it contains:

```
# Gridsome files
.cache/
/dist/

# Node dependencies
/node_modules/
.env
```

### 3.2. Commit and Push to GitHub

1.  **Initialize Git** (if not done by the starter):
    ```bash
    git init
    ```
2.  **Add all files and commit:**
    ```bash
    git add .
    git commit -m "Initial Gridsome setup with custom layouts and components"
    ```
3.  **Create a new repository on GitHub** (e.g., named `my-gridsome-site`).
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
3.  Select **"GitHub"** and choose your repository (`my-gridsome-site`).

### 4.2. Configure Build Settings

Netlify is optimized for Gridsome and will usually auto-detect the following settings.

| Setting | Value (Default for Gridsome) | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The branch Netlify monitors. |
| **Build command** | **`gridsome build`** | The command to generate the static site. |
| **Publish directory** | **`dist`** | The folder Gridsome outputs the static files to. |

### 4.3. Deploy the Site

Click the **"Deploy site"** button. Netlify will:

1.  Fetch your GitHub code.
2.  Install Node.js dependencies.
3.  Run the build command (`gridsome build`).
4.  Publish the contents of the **`dist`** directory.

Your Gridsome site will be live with full continuous deployment.

-----

## 5\. Ongoing Workflow

The Continuous Deployment (CD) pipeline is now established:

1.  Make changes locally (update components, add new pages).
2.  Commit your changes:
    ```bash
    git commit -am "Added 'About' page and styled header"
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, runs the Gridsome build, and deploys the new version live.