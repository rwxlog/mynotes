Setting up a Gatsby site with Netlify is a standard and powerful combination for creating high-performance Jamstack websites. This guide covers the complete process, from initial installation to continuous deployment.

-----

## 1\. Local Gatsby Setup and Project Initialization

Gatsby is a React-based framework that requires Node.js and npm (or yarn/pnpm).

### 1.1. Install the Gatsby CLI

The Gatsby Command Line Interface (CLI) is the easiest way to create and manage Gatsby projects.

1.  **Install Node.js** (version 18+ recommended).
2.  **Install the Gatsby CLI globally:**
    ```bash
    npm install -g gatsby-cli
    ```

### 1.2. Create a New Gatsby Site

We will use the default Gatsby starter template for a clean start.

1.  **Create the site:**
    ```bash
    gatsby new my-gatsby-site https://github.com/gatsbyjs/gatsby-starter-minimal
    cd my-gatsby-site
    ```
    *Note: The minimal starter provides a blank slate, ideal for custom development.*

### 1.3. Local Test

Run the development server to check the basic setup.

```bash
npm run develop
```

Your site will be running at `http://localhost:8000/`. Press **Ctrl+C** to stop the server.

-----

## 2\. Custom Layouts, Components, and Assets

Gatsby uses React components for templating. Pages are built from **Layout Components** that wrap **UI Components (Partials)**.

### 2.1. Project File/Folder Structure Tree

Your project structure, after customization, will look like this:

```
my-gatsby-site/
├── node_modules/            # Node.js dependencies
├── public/                  # ⚠️ Generated output folder (ignored by Git)
├── src/                     # All source code, components, layouts, and pages
│   ├── components/          # Reusable UI components (partials)
│   │   ├── Footer.js        # Site footer component
│   │   └── Header.js        # Site header/navigation component
│   ├── pages/               # Top-level pages (creates site routes)
│   │   ├── 404.js           # Not Found page
│   │   └── index.js         # Site homepage (/)
│   └── templates/           # Reusable page wrappers (Layouts)
│       └── Layout.js        # Primary site shell, wraps all content
├── static/                  # Static assets copied directly to the final output (public/)
│   └── css/
│       └── style.css        # Custom CSS file
├── gatsby-config.js         # Main site configuration (plugins, metadata)
├── gatsby-node.js           # API for programmatically creating pages (e.g., blog posts)
├── package.json             # Node.js dependencies and run scripts
└── package-lock.json
```

### 2.2. Create the Layout Component

The Layout component serves as the wrapper for most or all pages, defining the common structural elements (header, footer, shared styling).

1.  **Create the Components and Templates directories:**
    ```bash
    mkdir -p src/components src/templates
    ```
2.  **Create the Main Layout:**
      * File: **`src/templates/Layout.js`**
    <!-- end list -->
    ```jsx
    import React from "react"
    import Header from "../components/Header"
    import Footer from "../components/Footer"

    const Layout = ({ children }) => {
      return (
        <>
          <Header />
          <main>{children}</main> {/* Page content is injected via 'children' prop */}
          <Footer />
        </>
      )
    }

    export default Layout
    ```

### 2.3. Create Components (Partials)

Components are reusable UI elements imported into the Layout.

1.  **Create Header Component:**
      * File: **`src/components/Header.js`**
    <!-- end list -->
    ```jsx
    import React from "react"
    import { Link } from "gatsby"

    const Header = () => (
      <header style={{ borderBottom: '1px solid #ccc', padding: '10px 0' }}>
        <nav>
          <Link to="/" style={{ marginRight: '15px' }}>Home</Link>
          <Link to="/about">About</Link>
        </nav>
      </header>
    )

    export default Header
    ```
2.  **Create Footer Component:**
      * File: **`src/components/Footer.js`**
    <!-- end list -->
    ```jsx
    import React from "react"

    const Footer = () => (
      <footer style={{ marginTop: '50px', paddingTop: '20px', borderTop: '1px solid #ccc', textAlign: 'center' }}>
        <p>&copy; {new Date().getFullYear()} My Gatsby Site</p>
      </footer>
    )

    export default Footer
    ```

### 2.4. Apply the Layout

Update your page components to use the new layout.

1.  **Update the Homepage:**
      * Open **`src/pages/index.js`** and replace its contents with:
    <!-- end list -->
    ```jsx
    import React from "react"
    import Layout from "../templates/Layout" // Import the custom layout

    const IndexPage = () => (
      <Layout>
        <title>Home Page</title>
        <h1>Welcome to Gatsby!</h1>
        <p>This is the content wrapped by the custom Layout component.</p>
      </Layout>
    )

    export default IndexPage
    ```

### 2.5. Manage Static Assets

Assets in the **`static/`** folder are copied directly to the root of the output directory during the build. This is ideal for global CSS, fonts, and favicon files.

1.  **Create the Static directory:**

    ```bash
    mkdir -p static/css
    ```

2.  **Add a Custom CSS File:**

      * File: **`static/css/style.css`**
      * Add some basic styling:

    <!-- end list -->

    ```css
    body {
        font-family: sans-serif;
        margin: 0 auto;
        max-width: 1200px;
        padding: 20px;
    }
    /* Simple global reset */
    * {
        box-sizing: border-box;
    }
    ```

3.  **Link the CSS:** Add a `<link>` tag to the page header. Since Gatsby is a React app, the best way to inject global CSS is often in a file like **`src/templates/Layout.js`** by using the Gatsby `Helmet` or simply a `<link>` tag directly within the head of the layout component (or a dedicated component imported into the layout).

    For simplicity, add this line at the very top of **`src/templates/Layout.js`**:

    ```jsx
    // src/templates/Layout.js
    import React from "react"
    import Header from "../components/Header"
    import Footer from "../components/Footer"

    // Import the global CSS file which Gatsby handles correctly
    import "../../static/css/style.css" 

    const Layout = ({ children }) => {
    // ... rest of the component
    ```

### 2.6. Final Local Check

Run the server again:

```bash
npm run develop
```

Verify that the components, layout, and global CSS are loading correctly.

-----

## 3\. GitHub Setup

Prepare the project for version control and Netlify deployment.

### 3.1. Verify `.gitignore`

The default starter includes a `.gitignore` that correctly ignores the built site. Ensure it contains:

```
# Gatsby files
.cache/
public/

# Node dependencies
node_modules/
```

### 3.2. Commit and Push to GitHub

1.  **Initialize Git** (if not done in the initial step):
    ```bash
    git init
    ```
2.  **Add all files and commit:**
    ```bash
    git add .
    git commit -m "Initial Gatsby site with custom layout and components"
    ```
3.  **Create a new repository on GitHub** (e.g., named `my-gatsby-site`).
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
4.  Find and select your repository (`my-gatsby-site`).

### 4.2. Configure Build Settings

Netlify is optimized for Gatsby and will usually auto-detect the following settings.

| Setting | Value (Default for Gatsby) | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The branch Netlify monitors for changes. |
| **Build command** | `npm run build` or `gatsby build` | Runs the script defined in your `package.json`. |
| **Publish directory** | **`public`** | The folder Gatsby outputs the static files to. |

### 4.3. Deploy the Site

Click the **"Deploy site"** button. Netlify will:

1.  Fetch your GitHub code.
2.  Install Node.js dependencies.
3.  Run the build command (`npm run build`).
4.  Publish the contents of the **`public`** directory.

Your high-performance Gatsby site will be live on a temporary Netlify URL.

-----

## 5\. Ongoing Workflow

Your Continuous Deployment (CD) pipeline is now fully automated:

1.  Make changes locally (update components, add new pages).
2.  Commit your changes:
    ```bash
    git commit -am "Added 'About' page and improved styling"
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, runs the Gatsby build, and deploys the new version live.