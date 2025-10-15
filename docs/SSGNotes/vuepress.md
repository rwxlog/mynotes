Setting up a **VuePress** site with Netlify provides an easy way to deploy a documentation or content-focused site powered by Vue.js. VuePress converts Markdown files into static HTML and runs entirely client-side, making it ideal for Netlify's static hosting.

Here is a detailed, step-by-step guide covering installation, customization, and continuous deployment.

-----

## 1\. Local VuePress Setup and Project Initialization

VuePress is a Node.js-based framework. You'll need **Node.js** and npm (or yarn/pnpm).

### 1.1. Create the Project Directory

1.  **Create the site directory:**
    ```bash
    mkdir my-vuepress-site
    cd my-vuepress-site
    ```
2.  **Initialize a Node.js project:**
    ```bash
    npm init -y
    # OR: yarn init -y
    ```

### 1.2. Install VuePress

We will install VuePress as a local dependency.

1.  **Install VuePress:**

    ```bash
    npm install -D vuepress@next
    # OR: yarn add -D vuepress@next
    ```

    *Note: We are using VuePress 2 (`@next`), which has a slightly different setup than VuePress 1.*

2.  **Add build scripts:** Open **`package.json`** and add the following scripts:

    ```json
    "scripts": {
      "docs:dev": "vuepress dev src",
      "docs:build": "vuepress build src"
    }
    ```

3.  **Create the Source Directory:** VuePress defaults to using a `src` folder for its core files.

    ```bash
    mkdir src
    ```

4.  **Create the Homepage:** The `README.md` file acts as the homepage.

    ```bash
    echo '# Hello VuePress' > src/README.md
    ```

### 1.3. Local Test

Run the development server to verify the basic site.

```bash
npm run docs:dev
```

Your site will be running at `http://localhost:8080/`. Press **Ctrl+C** to stop the server.

-----

## 2\. Custom Layouts, Partials, and Static Assets

VuePress follows a convention-over-configuration approach. Customization is handled through the **`.vuepress`** directory.

### 2.1. Project File/Folder Structure Tree

Your project structure, after customization, will look like this:

```
my-vuepress-site/
├── node_modules/            # Node.js dependencies
├── src/                     # Source directory for all content and config
│   ├── .vuepress/           # VuePress configuration and customization
│   │   ├── client.js        # Client-side enhancements (e.g., component registration)
│   │   ├── components/      # Reusable Vue components (Partials)
│   │   │   └── CustomFooter.vue
│   │   ├── config.js        # Main configuration (title, theme, plugins, navbar)
│   │   ├── public/          # Static assets copied directly to output root (like favicon)
│   │   │   └── logo.png
│   │   └── styles/          # Global styles (CSS/Sass)
│   │       └── index.css    # Global stylesheet
│   ├── README.md            # The site homepage (/)
│   └── guide/
│       └── intro.md         # A content page
├── package.json
└── package-lock.json
```

-----

### 2.2. Configure VuePress

1.  **Create the Configuration Directory:**

    ```bash
    mkdir -p src/.vuepress
    ```

2.  **Create the Config File:**

      * File: **`src/.vuepress/config.js`**

    <!-- end list -->

    ```javascript
    import { defaultTheme } from 'vuepress'

    export default {
      lang: 'en-US',
      title: 'My VuePress Site',
      description: 'A static site deployed with Netlify',
      base: '/', // Important: Use root path for Netlify hosting
      
      theme: defaultTheme({
        // Theme configuration options
        navbar: [
          { text: 'Home', link: '/' },
          { text: 'Guide', link: '/guide/intro.html' },
        ],
        // Set the public folder for static assets
        publicDir: 'src/.vuepress/public', 
      }),
    }
    ```

### 2.3. Create Partials (Components)

Components are reusable Vue files placed in the `.vuepress/components/` folder and can be used directly in any Markdown file.

1.  **Create the Components directory:**

    ```bash
    mkdir src/.vuepress/components
    ```

2.  **Create a Custom Footer Component:**

      * File: **`src/.vuepress/components/CustomFooter.vue`**

    <!-- end list -->

    ```vue
    <template>
      <footer style="text-align: center; margin-top: 50px; padding-top: 20px; border-top: 1px solid #eee;">
        <p>&copy; {{ new Date().getFullYear() }} {{ $site.title }}</p>
      </footer>
    </template>

    <script setup>
    // Access global site data via $site
    </script>
    ```

3.  **Include the Partial:** Edit **`src/README.md`** and use the component tag directly:

    ```markdown
    # Hello VuePress

    This site is awesome and uses a custom component.

    <CustomFooter/>
    ```

### 2.4. Manage Static Assets

1.  **Global Styles:**

      * File: **`src/.vuepress/styles/index.css`**
      * Any styles in this file are injected globally.

2.  **Static Assets:** Place assets that should be copied directly (e.g., logo, favicon) into the **`src/.vuepress/public/`** folder. These assets can be referenced from the root in Markdown or HTML (e.g., `<img src="/logo.png">`).

### 2.5. Final Local Check

Run the server again:

```bash
npm run docs:dev
```

Verify that the custom component, global styles, and navigation are working.

-----

## 3\. GitHub Setup

### 3.1. Verify `.gitignore`

The starter includes a `.gitignore` that correctly ignores the built site and cache. Ensure it contains:

```
# VuePress build output
/src/.vuepress/.temp/
/src/.vuepress/.cache/
/src/.vuepress/dist/  # The output folder (Default: .vuepress/dist)

# Node dependencies
/node_modules/
```

### 3.2. Commit and Push to GitHub

1.  **Initialize Git:**
    ```bash
    git init
    ```
2.  **Add all files and commit:**
    ```bash
    git add .
    git commit -m "Initial VuePress site with custom component"
    ```
3.  **Create a new repository on GitHub** (e.g., named `my-vuepress-netlify`).
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
3.  Select **"GitHub"** and choose your repository (`my-vuepress-netlify`).

### 4.2. Configure Build Settings

Netlify is optimized for VuePress and Node.js but needs explicit instructions on the build command and output directory.

| Setting | Value | Note |
| :--- | :--- | :--- |
| **Base directory** | (Leave Blank) | The repository root is the base. |
| **Build command** | **`npm run docs:build`** | Runs the script defined in `package.json` to generate the static site. |
| **Publish directory** | **`src/.vuepress/dist`** | The default folder VuePress 2 outputs the static files to. |

### 4.3. Deploy the Site

Click the **"Deploy site"** button. Netlify will:

1.  Fetch your GitHub code and install dependencies.
2.  Run the build command (`npm run docs:build`).
3.  Publish the contents of the **`src/.vuepress/dist`** directory.

Your VuePress site will be live on a temporary Netlify URL.

-----

## 5\. Ongoing Workflow

The Continuous Deployment (CD) pipeline is now established:

1.  Make changes locally (update Markdown content, edit components, or adjust `config.js`).
2.  Commit your changes:
    ```bash
    git commit -am "Added new guide page and updated navigation."
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, runs the VuePress build, and deploys the new version live.