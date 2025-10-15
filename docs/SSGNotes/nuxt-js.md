Setting up a **Nuxt.js** site with Netlify, especially utilizing its **Static Site Generation (SSG)** mode, results in a highly performant, serverless application powered by Vue.js.

Here is a detailed, step-by-step guide focusing on the necessary configuration for Netlify deployment.

-----

## 1\. Local Nuxt.js Setup and Project Initialization

Nuxt.js is a Vue.js framework built on Node.js. You'll need Node.js and npm (or yarn/pnpm).

### 1.1. Create a New Nuxt.js Project

The recommended way to start a Nuxt.js project is by using the `npx create-nuxt-app` command (for Nuxt 2) or the `npx nuxi init` command (for Nuxt 3). We'll use **Nuxt 3** for the modern approach.

1.  **Initialize the project:**
    ```bash
    npx nuxi init my-nuxtjs-site
    ```
2.  **Navigate into the directory and install dependencies:**
    ```bash
    cd my-nuxtjs-site
    npm install
    # OR: yarn install / pnpm install
    ```
3.  **Configure for Static Output (SSG):** For simple static sites on Netlify, Nuxt 3 uses a default build that generates highly optimized static files. We will confirm the proper generation step later.

### 1.2. Local Test

Run the development server to verify the default site.

```bash
npm run dev
# OR: yarn dev / pnpm dev
```

Your site will be running at `http://localhost:3000/`. Press **Ctrl+C** to stop the server.

-----

## 2\. Custom Layouts, Partials, and Static Assets

Nuxt uses a clear, convention-based file structure for defining routes, layouts, and components.

### 2.1. Project File/Folder Structure Tree

Your project structure, after customization, will look like this:

```
my-nuxtjs-site/
├── .nuxt/                   # ⚠️ Generated code and cache (ignored by Git)
├── .output/                 # ⚠️ Build output directory (Publish Directory for Netlify)
├── node_modules/            # Node.js dependencies
├── public/                  # Static assets copied directly to the output root
│   ├── favicon.ico
│   └── images/
│       └── logo.svg
├── assets/                  # Non-processed assets (fonts, global CSS/SCSS)
│   └── main.css             # Global CSS file
├── components/              # Reusable Vue components (Partials/UI elements)
│   ├── AppFooter.vue
│   └── AppHeader.vue
├── layouts/                 # Defines custom layouts that wrap pages
│   └── default.vue          # The main HTML skeleton (Layout)
├── pages/                   # Vue components that automatically become site routes
│   ├── index.vue            # The site homepage (/)
│   └── about.vue            # The '/about' page
├── nuxt.config.ts           # Main Nuxt configuration (modules, build settings)
├── package.json             # Node.js dependencies and run scripts
└── tsconfig.json            # TypeScript configuration (if used)
```

### 2.2. Create the Default Layout

The default layout wraps all pages that do not specify a different layout.

1.  **Create the Layouts directory and file:**

    ```bash
    mkdir layouts
    touch layouts/default.vue
    ```

2.  **Define the Layout:**

      * File: **`layouts/default.vue`**

    <!-- end list -->

    ```vue
    <template>
      <div class="app-wrapper">
        <AppHeader />
        <main class="page-content">
          <slot /> </main>
        <AppFooter />
      </div>
    </template>

    <script setup>
    import AppHeader from '@/components/AppHeader.vue';
    import AppFooter from '@/components/AppFooter.vue';
    </script>

    <style>
    /* Import global styles from the assets directory */
    @import '~/assets/main.css'; 
    </style>
    ```

### 2.3. Create Components (Partials)

1.  **Create Header Component:**
      * File: **`components/AppHeader.vue`**
    <!-- end list -->
    ```vue
    <template>
      <header>
        <nav>
          <NuxtLink to="/">Home</NuxtLink>
          <NuxtLink to="/about">About</NuxtLink>
        </nav>
      </header>
    </template>
    ```
2.  **Create Footer Component:**
      * File: **`components/AppFooter.vue`**
    <!-- end list -->
    ```vue
    <template>
      <footer>
        <p>&copy; {{ new Date().getFullYear() }} Nuxt Site</p>
      </footer>
    </template>
    ```

### 2.4. Manage Static Assets

1.  **Assets (`assets/`):** For files you want included in the build process (like global styles, fonts).
      * File: **`assets/main.css`** (Add some basic CSS here).
2.  **Public (`public/`):** For files that are copied directly to the output root (e.g., `favicon.ico`, `robots.txt`).

### 2.5. Final Local Check

1.  **Build the static site:**

    ```bash
    npm run generate
    # OR: yarn generate / pnpm generate
    ```

    *Note: In Nuxt 3, the output for static generation is often handled by the `nuxi generate` command, which builds the files and places them inside the **`.output/public`** directory by default.*

2.  **Run the preview command** (recommended to test the static output):

    ```bash
    npm run preview
    ```

    *Verify the static site structure before deployment.*

-----

## 3\. GitHub Setup

### 3.1. Verify `.gitignore`

The starter includes a `.gitignore` that correctly ignores the built site and cache. Ensure it contains:

```
# Nuxt build and cache
.nuxt/
.output/

# Node dependencies
/node_modules/
```

### 3.2. Commit and Push to GitHub

1.  **Add all files and commit:**
    ```bash
    git add .
    git commit -m "Initial Nuxt 3 static site setup"
    ```
2.  **Create a new repository on GitHub** (e.g., named `my-nuxt-netlify`).
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
3.  Select **"GitHub"** and choose your repository (`my-nuxt-netlify`).

### 4.2. Configure Build Settings

When deploying a static Nuxt 3 site, you need to tell Netlify to use the SSG build command and point to the static output directory.

| Setting | Value | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The branch Netlify monitors. |
| **Build command** | **`npm run generate`** | This command executes the SSG build. |
| **Publish directory** | **`.output/public`** | The default folder where Nuxt 3 places static files after running `generate`. |

### 4.3. Set Environment Variable (Crucial for Nuxt 3)

For Nuxt 3 to build correctly in the Netlify environment, you must explicitly set a Node.js environment variable.

1.  In the Netlify deploy settings, click **"Show advanced"**.
2.  Add a new environment variable:
      * **Key:** `NUXT_PUBLIC_BASE_URL`
      * **Value:** `/` (This ensures all paths are relative to the root for static serving.)

### 4.4. Deploy the Site

Click the **"Deploy site"** button. Netlify will:

1.  Fetch your GitHub code and install dependencies.
2.  Run the build command (`npm run generate`).
3.  Publish the contents of the **`.output/public`** directory.

Your static Nuxt.js site will be live with continuous deployment.

-----

## 5\. Ongoing Workflow

Your Continuous Deployment (CD) pipeline is now established:

1.  Make changes locally (update components or pages).
2.  Commit your changes:
    ```bash
    git commit -am "Updated header navigation and fixed styles"
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, runs the Nuxt SSG build, and deploys the new version live.