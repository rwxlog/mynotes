Setting up a static **React** site (using Vite for modern build tooling) with Netlify is a quick, high-performance solution for single-page applications (SPAs) and static websites.

Here is a detailed, step-by-step guide covering the entire process.

-----

## 1\. Local React (Vite) Setup and Project Initialization

We'll use **Vite** as the build tool, which is faster and easier to configure for a simple React SPA than the traditional Create React App (CRA). You'll need **Node.js** and npm (or yarn/pnpm).

### 1.1. Create a New React Project

1.  **Initialize the project using Vite:**
    ```bash
    npm create vite@latest my-react-site -- --template react
    ```
2.  **Navigate into the directory and install dependencies:**
    ```bash
    cd my-react-site
    npm install
    ```

### 1.2. Local Test

Run the development server to verify the default site.

```bash
npm run dev
```

Your site will be running at `http://localhost:5173/`. Press **Ctrl+C** to stop the server.

-----

## 2\. Custom Layouts, Partials, and Static Assets

In a typical static React application, **Layouts** are implemented as parent components, and **Partials** are reusable child components.

### 2.1. Project File/Folder Structure Tree

Your project structure, after customization, will look like this:

```
my-react-site/
├── node_modules/            # Node.js dependencies
├── dist/                    # ⚠️ Generated static site (Publish Directory for Netlify)
├── public/                  # Static assets copied directly to the output root
│   ├── favicon.svg
│   └── images/
│       └── logo.png
├── src/                     # All source code
│   ├── components/          # Reusable React components (Partials/UI elements)
│   │   ├── Footer.jsx
│   │   └── Header.jsx
│   ├── layouts/             # Layout components (Wrappers for pages/routes)
│   │   └── MainLayout.jsx
│   ├── App.jsx              # Main application component, usually uses the Layout
│   ├── main.jsx             # Entry file (renders App.jsx)
│   └── index.css            # Global application styles
├── index.html               # Main HTML entry file
├── package.json             # Node.js dependencies and run scripts
└── vite.config.js           # Vite build configuration
```

-----

### 2.2. Create the Custom Layout

The **Layout** component wraps the main content and provides consistent structure (header, footer, shared styling).

1.  **Create the Layouts directory:**
    ```bash
    mkdir src/layouts
    ```
2.  **Create the Main Layout Component:**
      * File: **`src/layouts/MainLayout.jsx`**
    <!-- end list -->
    ```jsx
    import React from 'react';
    import Header from '../components/Header';
    import Footer from '../components/Footer';

    const MainLayout = ({ children }) => {
      return (
        <div className="app-container">
          <Header />
          <main className="content">
            {children} {/* Page content is injected via 'children' prop */}
          </main>
          <Footer />
        </div>
      );
    };

    export default MainLayout;
    ```

### 2.3. Create Components (Partials)

1.  **Create the Components directory:**
    ```bash
    mkdir src/components
    ```
2.  **Create Header Component:**
      * File: **`src/components/Header.jsx`**
    <!-- end list -->
    ```jsx
    import React from 'react';

    const Header = () => (
      <header style={{ borderBottom: '1px solid #ddd', padding: '15px 0' }}>
        <nav>
          <a href="/" style={{ marginRight: '15px' }}>Home</a>
          <a href="/about">About</a>
        </nav>
      </header>
    );

    export default Header;
    ```
3.  **Create Footer Component:**
      * File: **`src/components/Footer.jsx`**
    <!-- end list -->
    ```jsx
    import React from 'react';

    const Footer = () => (
      <footer style={{ marginTop: '50px', paddingTop: '20px', borderTop: '1px solid #ddd', textAlign: 'center' }}>
        <p>&copy; {new Date().getFullYear()} My React Site</p>
      </footer>
    );

    export default Footer;
    ```

### 2.4. Apply the Layout

Update the main `App.jsx` component to use the new layout.

1.  **Update `src/App.jsx`:**

    ```jsx
    import MainLayout from './layouts/MainLayout';

    function App() {
      // In a real app, this would be a router (e.g., React Router) 
      // rendering different components based on the URL.
      return (
        <MainLayout>
          <h1>Welcome to my React SPA!</h1>
          <p>This content is wrapped by the custom MainLayout.</p>
        </MainLayout>
      );
    }

    export default App;
    ```

### 2.5. Manage Static Assets

Assets in the **`public/`** folder are copied directly to the root of the **`dist/`** folder during the build.

1.  **Add a Custom Asset:** Place a logo in `public/images/logo.png`.
2.  **Reference the asset:** You can reference it directly from the root path in your HTML or JSX:
    ```jsx
    // In any component:
    <img src="/images/logo.png" alt="Site Logo" />
    ```

### 2.6. Final Local Check

1.  **Build the static site:**
    ```bash
    npm run build
    ```
    *This creates the static HTML, CSS, and JS files in the **`dist/`** directory.*
2.  **Run the preview server:**
    ```bash
    npm run preview
    ```
    *Verify the fully static site is working before deployment.*

-----

## 3\. GitHub Setup

### 3.1. Verify `.gitignore`

The generated `.gitignore` file should correctly exclude the build output and Node modules. Ensure it contains:

```
# Build output
/dist

# Node dependencies
/node_modules
```

### 3.2. Commit and Push to GitHub

1.  **Initialize Git** (if not already done by Vite):
    ```bash
    git init
    ```
2.  **Add all files and commit:**
    ```bash
    git add .
    git commit -m "Initial React site with custom layout and components"
    ```
3.  **Create a new repository on GitHub** (e.g., named `my-react-netlify-site`).
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
3.  Select **"GitHub"** and choose your repository (`my-react-netlify-site`).

### 4.2. Configure Build Settings

Netlify often auto-detects React/Vite projects, but you must confirm the settings.

| Setting | Value | Note |
| :--- | :--- | :--- |
| **Branch to deploy** | `main` | The main branch of your repository. |
| **Build command** | **`npm run build`** | Runs the script defined in your `package.json` to create the static bundle. |
| **Publish directory** | **`dist`** | The default folder where Vite outputs the static files. |

### 4.3. Configure Netlify Redirects (Crucial for SPAs)

Since React is an SPA (Single Page Application), Netlify needs to know that any path should fall back to the main `index.html` file.

1.  **Create the Netlify configuration file:**

    ```bash
    touch public/_redirects
    ```

2.  **Add the redirect rule:**

      * File: **`public/_redirects`** (Since this is in the `public` folder, it will be copied to `dist/_redirects`.)

    <!-- end list -->

    ```
    /* /index.html   200
    ```

    *This rule ensures that any request (`/*`) that doesn't match a static file is served by `index.html` with a 200 OK status, allowing the React Router to handle the URL.*

3.  **Commit and push the redirects file:**

    ```bash
    git add public/_redirects
    git commit -m "Added Netlify SPA redirect file"
    git push origin main
    ```

### 4.4. Deploy the Site

Netlify will detect the new commit and automatically start the build and deploy process.

Your static React site will be live and functional on a temporary Netlify URL.

-----

## 5\. Ongoing Workflow

Your Continuous Deployment (CD) pipeline is now fully automated:

1.  Make changes locally (update components, add new routes).
2.  Commit your changes:
    ```bash
    git commit -am "Updated header navigation and added new content"
    ```
3.  Push to GitHub:
    ```bash
    git push origin main
    ```
4.  Netlify automatically detects the push, runs the Vite build (`npm run build`), and deploys the new static version live.