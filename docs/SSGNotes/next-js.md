Setting up a Next.js site with Netlify is a popular and powerful combination, especially when leveraging Next.js's Static Site Generation (SSG) capabilities. This guide focuses on building a static Next.js site and deploying it seamlessly.

-----

## 1\. Local Next.js Setup and Project Initialization

Next.js is a React framework that requires Node.js and npm (or yarn/pnpm).

### 1.1. Create a New Next.js Project

The recommended way to start a Next.js project is by using `create-next-app`.

1.  **Create the site:**

    ```bash
    npx create-next-app@latest my-nextjs-site
    ```

2.  **Follow the setup wizard:**

      * **Would you like to use TypeScript?** **No** (or Yes, if preferred)
      * **Would you like to use ESLint?** **Yes**
      * **Would you like to use Tailwind CSS?** **No** (or Yes, if preferred)
      * **Would you like to use `src/` directory?** **Yes** (Recommended for structure)
      * **Would you like to use App Router?** **No** (We will use the **Pages Router** for simpler static setup)
      * **Would you like to customize the default import alias?** **No**

3.  **Navigate into the new directory:**

    ```bash
    cd my-nextjs-site
    ```

### 1.2. Local Test

Run the development server to verify the default site.

```bash
npm run dev
```

Your site will be running at `http://localhost:3000/`. Press **Ctrl+C** to stop the server.

-----

## 2\. Custom Layouts, Partials, and Static Assets

Next.js uses React components for layouts and partials. We'll use the Pages Router structure and define a root layout.

### 2.1. Project File/Folder Structure Tree

Your project structure, after customization, will look like this:

```
my-nextjs-site/
├── node_modules/            # Node.js dependencies
├── .next/                   # ⚠️ Generated output/cache (ignored by Git)
├── out/                     # ⚠️ Generated static site (Publish Directory for Netlify)
├── public/                  # Static assets copied directly to the output root
│   ├── favicon.ico
│   └── images/
│       └── logo.svg
├── src/
│   ├── components/          # Reusable React components (Partials/UI elements)
│   │   ├── Footer.js
│   │   └── Header.js
│   ├── styles/              # Global styles
│   │   └── globals.css
│   └── pages/               # Top-level pages (creates site routes)
│       ├── _app.js          # Next.js Custom App: Defines the global layout/wrapper
│       ├── _document.js     # Custom Document: Modifies <html> and <body> tags
│       └── index.js         # The site homepage (/)
├── next.config.mjs          # Next.js framework configuration
├── package.json             # Node.js dependencies and run scripts
└── tsconfig.json            # TypeScript configuration (if used)
```

### 2.2. Create the Custom Layout (`_app.js`)

In the Pages Router, the `_app.js` file is the ideal place to define a global layout that wraps every page.

1.  **Create Components directory:**

    ```bash
    mkdir src/components
    ```

2.  **Open or create the Custom App file:**

      * File: **`src/pages/_app.js`**

    <!-- end list -->

    ```javascript
    import '@/styles/globals.css';
    import Header from '@/components/Header';
    import Footer from '@/components/Footer';

    // The Component prop is the active page (e.g., pages/index.js)
    // The pageProps are the props the page receives (e.g., from getStaticProps)
    export default function App({ Component, pageProps }) {
      return (
        <>
          <Header />
          <main>
            <Component {...pageProps} /> 
          </main>
          <Footer />
        </>
      );
    }
    ```

### 2.3. Create Components (Partials)

1.  **Create Header Component:**
      * File: **`src/components/Header.js`**
    <!-- end list -->
    ```javascript
    import Link from 'next/link';

    export default function Header() {
      return (
        <header style={{ borderBottom: '1px solid #ccc', padding: '15px 0' }}>
          <nav>
            <Link href="/" style={{ marginRight: '15px' }}>Home</Link>
            <Link href="/about">About</Link>
          </nav>
        </header>
      );
    }
    ```
2.  **Create Footer Component:**
      * File: **`src/components/Footer.js`**
    <!-- end list -->
    ```javascript
    export default function Footer() {
      return (
        <footer style={{ marginTop: '50px', paddingTop: '20px', borderTop: '1px solid #ccc', textAlign: 'center' }}>
          <p>&copy; {new Date().getFullYear()} Next.js Site</p>
        </footer>
      );
    }
    ```

### 2.4. Configure Next.js for Static Export

To deploy to Netlify as a purely static site (HTML, CSS, JS), you must configure Next.js to export the project to a directory (default is `out/`).

1.  **Open the configuration file:**

      * File: **`next.config.mjs`**

2.  **Add the `output: 'export'` property:**

    ```javascript
    /** @type {import('next').NextConfig} */
    const nextConfig = {
      output: 'export', // Tells Next.js to export to the 'out' directory

      // Optional: Disable server-side features that won't work with static export
      // trailingSlash: true, 
      // images: { unoptimized: true },
    };

    export default nextConfig;
    ```

### 2.5. Final Local Check

1.  **Build the static site:**
    ```bash
    npm run build
    ```
    *This creates the static HTML files in the **`out/`** directory.*
2.  **Install a simple static file server** (optional, but helpful):
    ```bash
    npm install -g serve
    ```
3.  **Serve the output directory:**
    ```bash
    serve out
    ```
    *Verify the fully static site is working before deployment.*

-----

## 3\. GitHub Setup

### 3.1. Verify `.gitignore`

The starter includes a `.gitignore` that correctly ignores the build output. Ensure it contains:

```
# Next.js build and cache
.next/
/out/
```

### 3.2. Commit and Push to GitHub

1.  **Add all files and commit:**
    ```bash
    git add .
    git commit -m "Initial Next.js static site setup"
    ```
2.  **Create a new repository on GitHub** (e.g., named `my-nextjs-netlify`).
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
3.  Select **"GitHub"** and choose your repository (`my-nextjs-netlify`).

### 4.2. Configure Build Settings

Since we configured Next.js to export statically, the settings are straightforward.

| Setting | Value | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The branch Netlify monitors. |
| **Build command** | **`npm run build`** | Runs the script, which now executes the static export. |
| **Publish directory** | **`out`** | The folder defined by `output: 'export'` in `next.config.mjs`. |

### 4.3. Deploy the Site

Click the **"Deploy site"** button. Netlify will:

1.  Fetch your GitHub code.
2.  Install Node.js dependencies.
3.  Run the build command (`npm run build`), which generates the static site into the `out/` folder.
4.  Publish the contents of the **`out`** directory.

Your static Next.js site will be live on a temporary Netlify URL.

-----

## 5\. Ongoing Workflow

The Continuous Deployment (CD) pipeline is now established:

1.  Make changes locally (update components in `src/components`, edit pages in `src/pages`).
2.  Commit your changes:
    ```bash
    git commit -am "Updated homepage content and added about link"
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, runs the `npm run build` command (which executes the static export), and deploys the new version live.