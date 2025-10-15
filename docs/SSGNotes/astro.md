Setting up an Astro site with Netlify provides a high-performance publishing workflow. Astro's default configuration is highly optimized for Netlify, making the deployment process simple.

Here is a detailed, step-by-step guide covering installation, custom components, static assets, and deployment.

-----

## 1\. Local Astro Setup and Project Initialization

Astro is a Node.js-based framework, so you'll need Node.js and npm (or yarn/pnpm).

### 1.1. Create and Initialize the Project

Astro provides a helpful command-line wizard (`create astro`) to set up a new project quickly.

1.  **Create the site using npm:**

    ```bash
    npm create astro@latest
    ```

2.  **Follow the setup wizard:**

      * **Where should we create your new project?** `my-astro-site` (or your preferred name)
      * **How would you like to start your new project?** **"Just the basics (Recommended)"**
      * **Would you like to install npm dependencies?** **Yes**
      * **Would you like to initialize a new Git repository?** **Yes**
      * **How would you like to setup TypeScript?** **"Strict"** (or your preference)

3.  **Navigate into the new directory:**

    ```bash
    cd my-astro-site
    ```

### 1.2. Local Test

Run the development server to verify the default site.

```bash
npm run dev
```

Your site will be running at `http://localhost:4321/`. Press **Ctrl+C** to stop the server.

-----

## 2\. Custom Layouts, Components, and Assets

Astro uses a component-based structure where `.astro` files act as both templates and reusable partials.

### 2.1. Project File/Folder Structure Tree

Your project structure, after customization, will look like this:

```
my-astro-site/
├── node_modules/            # Node.js dependencies
├── public/                  # Static assets copied directly to the final output (dist/)
│   ├── favicon.svg
│   ├── assets/
│   │   └── style.css        # Custom, unbundled CSS
│   └── images/
├── src/                     # All source code, components, layouts, and pages
│   ├── components/          # Reusable UI components (.astro, .jsx, .vue, etc.)
│   │   ├── Footer.astro     # Site footer partial/component
│   │   └── Header.astro     # Site header/navigation partial/component
│   ├── layouts/             # The main page wrappers (Layouts)
│   │   └── BaseLayout.astro # Primary site shell, defines HTML structure
│   ├── pages/               # Top-level pages (generates final HTML files)
│   │   ├── index.astro      # The site homepage (/)
│   │   └── blog/
│   │       └── [slug].astro # Dynamic page for blog posts
│   └── env.d.ts             # TypeScript environment file
├── astro.config.mjs         # Astro framework configuration
├── package.json             # Node.js dependencies and run scripts
├── package-lock.json        # Exact dependency versions
└── tsconfig.json            # TypeScript configuration
```

### 2.2. Create the Custom Layout

Astro layouts are reusable wrappers that define the page's HTML structure. They accept content via a `<slot />`.

1.  **Create the Base Layout:**

      * File: **`src/layouts/BaseLayout.astro`**

    <!-- end list -->

    ```astro
    ---
    // Component Script (Frontmatter)
    const { title = 'My Astro Site', description = 'A description for my awesome site' } = Astro.props;
    import Header from '../components/Header.astro';
    import Footer from '../components/Footer.astro';
    ---

    <!doctype html>
    <html lang="en">
        <head>
            <meta charset="UTF-8" />
            <meta name="description" content={description} />
            <meta name="viewport" content="width=device-width, initial-scale=1.0" />
            <title>{title}</title>
            <link rel="stylesheet" href="/assets/style.css"> 
        </head>
        <body>
            <Header /> 
            <main>
                <slot /> {/* Page content is injected here */}
            </main>
            <Footer />
        </body>
    </html>
    ```

### 2.3. Create Components (Partials)

Components are reusable elements and are typically stored in the `src/components/` folder.

1.  **Create Component Directory:**
    ```bash
    mkdir src/components
    ```
2.  **Create Header Component:**
      * File: **`src/components/Header.astro`**
    <!-- end list -->
    ```astro
    <header>
        <nav>
            <a href="/">Home</a>
            <a href="/blog/">Blog</a>
        </nav>
    </header>
    <style>
        header { padding: 1em 0; border-bottom: 1px solid #ccc; }
        nav a { margin-right: 1em; text-decoration: none; }
    </style>
    ```
3.  **Create Footer Component:**
      * File: **`src/components/Footer.astro`**
    <!-- end list -->
    ```astro
    <footer>
        <p>&copy; {new Date().getFullYear()} {Astro.site?.hostname || 'My Site'}</p>
    </footer>
    <style>
        footer { margin-top: 2em; padding-top: 1em; border-top: 1px solid #ccc; text-align: center; }
    </style>
    ```

### 2.4. Apply the Layout

Update your pages to use the new layout.

1.  **Update the Homepage:**
      * Open **`src/pages/index.astro`** and replace its contents with:
    <!-- end list -->
    ```astro
    ---
    import BaseLayout from '../layouts/BaseLayout.astro';
    ---
    <BaseLayout title="Astro Home Page" description="This is the main page content">
        <section>
            <h1>Welcome to Astro!</h1>
            <p>This page uses the custom BaseLayout.</p>
        </section>
    </BaseLayout>
    ```

### 2.5. Manage Static Assets

Assets that **do not need processing** go into the **`public/`** folder. They are copied directly to the final build output's root.

1.  **Create Assets Directory:**
    ```bash
    mkdir -p public/assets
    ```
2.  **Add a Custom CSS File:**
      * File: **`public/assets/style.css`**
      * Add some basic styling:
    <!-- end list -->
    ```css
    body {
        font-family: sans-serif;
        margin: 0 auto;
        max-width: 1200px;
        padding: 0 1em;
    }
    ```

### 2.6. Final Local Check

Run the server again:

```bash
npm run dev
```

Verify that the `Header` and `Footer` components are included and the custom CSS is applied.

-----

## 3\. GitHub Setup

Since the `create astro` wizard initialized Git for you (Step 1.1), you just need to commit the changes and push to a remote repository.

### 3.1. Verify `.gitignore`

The generated `.gitignore` file should already ignore the build output (`/dist/`) and Node modules (`/node_modules/`).

### 3.2. Commit and Push to GitHub

1.  **Add all files and commit:**
    ```bash
    git add .
    git commit -m "Initial Astro site with custom components and layout"
    ```
2.  **Create a new repository on GitHub** (e.g., named `my-astro-netlify-site`).
3.  **Link your local repo and push:** (Replace placeholders)
    ```bash
    git remote add origin https://github.com/<your_username>/<repo_name>.git
    git branch -M main
    git push -u origin main
    ```

-----

## 4\. Netlify Setup for Continuous Deployment

Netlify will monitor your GitHub repository to automatically build and deploy your Astro site.

### 4.1. Sign Up and Import Project

1.  Go to the **Netlify website** and log in.
2.  In the Netlify dashboard, click **"Add new site"** then **"Import an existing project"**.
3.  Select **"GitHub"** and authorize Netlify.
4.  Find and select your repository (`my-astro-netlify-site`).

### 4.2. Configure Build Settings

Netlify is pre-configured for Astro and will usually auto-detect the following settings correctly.

| Setting | Value (Default for Astro) | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The main branch of your repository. |
| **Build command** | `npm run build` | Runs the script defined in your `package.json`. |
| **Publish directory** | **`dist`** | The default output folder defined by Astro. |

### 4.3. Deploy the Site

Click the **"Deploy site"** button. Netlify will:

1.  Fetch your GitHub code.
2.  Install Node.js dependencies.
3.  Run the build command (`npm run build`).
4.  Publish the contents of the **`dist`** directory to the live web.

Once deployed, your high-performance Astro site will be live on a temporary Netlify URL.

-----

## 5\. Ongoing Workflow

Your Continuous Deployment (CD) pipeline is now fully automated:

1.  Make changes locally (edit `.astro` files, add content).
2.  Commit your changes:
    ```bash
    git commit -am "Updated homepage content and footer date"
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, rebuilds the site, and deploys the new version live.