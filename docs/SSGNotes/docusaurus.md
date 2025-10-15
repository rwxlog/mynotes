Setting up a **Docusaurus** site with Netlify is a perfect combination for deploying professional, documentation-focused websites. Docusaurus is a React-based static site generator that is highly optimized for documentation and easy to deploy.

Here is a detailed, step-by-step guide covering installation, customization, and continuous deployment.

-----

## 1\. Local Docusaurus Setup and Project Initialization

Docusaurus is a Node.js-based framework. You'll need **Node.js** and npm (or yarn/pnpm).

### 1.1. Create a New Docusaurus Project

Use the official setup command to initialize the project, which will include the necessary files and dependencies.

1.  **Create the site:**

    ```bash
    npx create-docusaurus@latest my-docusaurus-site classic
    ```

    *The `classic` template is recommended as it includes documentation, blog, and custom pages.*

2.  **Navigate into the new directory:**

    ```bash
    cd my-docusaurus-site
    ```

3.  **Install dependencies** (if the command above didn't already):

    ```bash
    npm install
    # OR: yarn install
    ```

### 1.2. Local Test

Run the development server to verify the default site.

```bash
npm run start
# OR: yarn start
```

Your site will be running at `http://localhost:3000/`. Press **Ctrl+C** to stop the server.

-----

## 2\. Custom Layouts, Partials, and Static Assets

Docusaurus uses React for its components and a clear folder structure for configuration and content.

### 2.1. Project File/Folder Structure Tree

Your project structure, after customization, will look like this:

```
my-docusaurus-site/
├── node_modules/            # Node.js dependencies
├── build/                   # ⚠️ Generated static site (Publish Directory for Netlify)
├── src/                     # Source files, including custom pages and components
│   ├── components/          # Reusable React components (Partials)
│   │   └── CustomFooter.js
│   └── css/                 # Global styles
│       └── custom.css
├── blog/                    # Markdown files for the blog feature
├── docs/                    # Markdown files for the documentation feature
├── static/                  # Static assets copied directly to the output root
│   ├── img/                 # Images, logos, favicon
│   └── robots.txt
├── docusaurus.config.js     # Main site configuration (theme, title, navbar, footer)
├── sidebars.js              # Configuration for the documentation sidebar
├── package.json             # Node.js dependencies and run scripts
└── package-lock.json
```

-----

### 2.2. Custom Layouts (Swizzling)

Docusaurus layouts are highly theme-driven. To customize a core component (like the Header or Footer, which are Partials), you use a technique called **Swizzling** to eject the component into your local project structure.

1.  **Swizzle the Footer Component:** (We'll use the classic theme's `Footer` as an example partial)

    ```bash
    npm run swizzle @docusaurus/theme-classic Footer
    ```

2.  **Locate the new component:** This creates the file: **`src/theme/Footer/index.js`**.

3.  **Customize the Footer:** Edit this file to add your custom HTML or React components.

    *Example change in `src/theme/Footer/index.js`:*

    ```jsx
    // ... inside the Footer component's return statement ...
    <footer>
      {/* Original Footer Links/Content */}
      {/* ... */}
      <div style={{ textAlign: 'center', marginTop: '10px' }}>
        <p>Custom Docusaurus Partial Added!</p>
      </div>
    </footer>
    ```

-----

### 2.3. Manage Static Assets and Styles

1.  **Global Styles:**

      * File: **`src/css/custom.css`** (Docusaurus uses this file by default).
      * Add your site-wide CSS here (e.g., font definitions, color overrides).

2.  **Static Assets:**

      * Place assets like your logo, favicon, or any files that should be served directly (not processed by Webpack) into the **`static/`** folder.
      * Example: Place your logo at **`static/img/logo.svg`**.

3.  **Use Static Assets in Configuration:** Update **`docusaurus.config.js`** to point to your new logo.

    ```javascript
    // docusaurus.config.js
    navbar: {
        title: 'My Docusaurus Site',
        logo: {
            alt: 'My Site Logo',
            src: 'img/logo.svg', // References the file in the static/ folder
        },
        // ...
    },
    ```

-----

### 2.4. Final Local Check

Run the server again:

```bash
npm run start
```

Verify that the custom footer component, new logo, and global CSS are correctly applied.

-----

## 3\. GitHub Setup

### 3.1. Verify `.gitignore`

The starter includes a `.gitignore` that correctly ignores the built site and cache. Ensure it contains:

```
# Docusaurus build and cache
.docusaurus/
/build/

# Node dependencies
/node_modules/
```

### 3.2. Commit and Push to GitHub

1.  **Add all files and commit:**
    ```bash
    git add .
    git commit -m "Initial Docusaurus site with custom footer and config"
    ```
2.  **Create a new repository on GitHub** (e.g., named `my-docusaurus-netlify`).
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
3.  Select **"GitHub"** and choose your repository (`my-docusaurus-netlify`).

### 4.2. Configure Build Settings

Netlify is optimized for Docusaurus and will usually auto-detect the correct settings.

| Setting | Value (Default for Docusaurus) | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The branch Netlify monitors. |
| **Build command** | **`npm run build`** | Runs the script, which executes `docusaurus build`. |
| **Publish directory** | **`build`** | The folder Docusaurus outputs the static files to. |

### 4.3. Deploy the Site

Click the **"Deploy site"** button. Netlify will:

1.  Fetch your GitHub code and install dependencies.
2.  Run the build command (`npm run build`).
3.  Publish the contents of the **`build`** directory.

Your Docusaurus documentation site will be live with continuous deployment.

-----

## 5\. Ongoing Workflow

The Continuous Deployment (CD) pipeline is now established:

1.  Make changes locally (update Markdown files in `docs/` or `blog/`, edit components in `src/theme`).
2.  Commit your changes:
    ```bash
    git commit -am "Updated README and documentation sidebar."
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, runs the Docusaurus build, and deploys the updated documentation live.