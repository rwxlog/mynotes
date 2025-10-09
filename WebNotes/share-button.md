Adding social share buttons to a Hugo blog post involves either using a built-in feature of your theme, implementing a custom Hugo **partial template**, or using an external **third-party service**.

The most effective, fast, and privacy-friendly method in Hugo is to **create a custom partial** that uses standard social media sharing URLs and Hugo's page variables.

## 1\. Using a Built-in Theme Feature

Many popular Hugo themes, like those based on Hugo Blox (formerly Wowchemy), have built-in share button functionality.

  * **Check Theme Documentation:** Look in your theme's documentation for "sharing," "social," or "page sharer."
  * **Enable in Front Matter:** If available, you often only need to set a variable in the post's front matter:
    ```yaml
    ---
    title: "My Blog Post"
    share: true  # This enables the built-in share buttons
    ---
    ```
  * **Configure Globally:** The sharing networks and their order are usually configured in a global configuration file (e.g., `config/_default/params.yaml` or a file in the `data/` folder like `data/page_sharer.yaml`).

-----

## 2\. Using a Custom Hugo Partial (Recommended Method)

This method gives you full control and avoids external scripts. You'll create a reusable piece of code (a partial) and call it in your single post template.

### Step 1: Create the Partial File

Create a new file called `social-share.html` (or similar) in your **layouts/partials/** directory.

### Step 2: Add the Sharing Link Logic

Inside `social-share.html`, use Hugo's variables (`.Title`, `.Permalink`) and standard social sharing URL structures.

The core concept is to create an $\text{<a>}$ tag with a specially formatted URL that includes the page's title and link.

```html
{{ $title := .Title }}
{{ $url := .Permalink | absLangURL }}

<div class="social-share-buttons">
    <a href="https://twitter.com/intent/tweet?text={{ $title }}&url={{ $url }}" target="_blank" rel="noopener" aria-label="Share on Twitter">
        Share on Twitter
    </a>

    <a href="https://www.facebook.com/sharer/sharer.php?u={{ $url }}" target="_blank" rel="noopener" aria-label="Share on Facebook">
        Share on Facebook
    </a>

    <a href="https://www.linkedin.com/shareArticle?mini=true&url={{ $url }}&title={{ $title }}" target="_blank" rel="noopener" aria-label="Share on LinkedIn">
        Share on LinkedIn
    </a>

    {{ $body := print $title "\n\n" $url }}
    <a href="mailto:?subject={{ $title }}&body={{ $body }}" target="_blank" aria-label="Share via Email">
        Share via Email
    </a>
</div>
```

**Note:** You would typically replace the plain text like "Share on Twitter" with $\text{<svg>}$ icons for a cleaner look.

### Step 3: Embed the Partial

Find the file that controls the layout of your individual blog posts. This is usually one of the following:

  * `layouts/_default/single.html`
  * `layouts/posts/single.html`
  * `themes/<your-theme>/layouts/_default/single.html`

Edit this file and insert the partial where you want the buttons to appear (e.g., below the content, or in the footer of the article):

```html
{{ .Content }}

<hr>

{{ partial "social-share.html" . }}
```

-----

## 3\. Using Third-Party Share Libraries

You can use third-party libraries that provide the HTML and CSS for clean, ready-to-use buttons, such as **sharingbuttons.io** or a dedicated Hugo module like **hugo-share-buttons**.

1.  **Install the Partial:** Download the required $\text{.html}$ partial file from the chosen source and place it into your `layouts/partials/` directory.
2.  **Configure:** Add the necessary configuration variables to your site's main `config.toml` or `config.yaml` to specify which networks to show and the size of the buttons.
3.  **Embed:** Call the partial in your `single.html` template using:
    ```html
    {{ partial "share-buttons.html" . }}
    ```

The video "Social Media Sharing with Twitter, Facebook, Linkedin & Pinterest for HUGO | Static Site" provides a visual guide on setting up social media sharing features on a Hugo site.

-----

Here's how to implement your share button by putting the JavaScript and CSS into your **main.js** and **style.css** files.

## 🧩 1. The Hugo Partial ($\text{layouts/partials/share-button.html}$)

This file contains only the **HTML structure** and uses Hugo variables (`.Permalink`, `.Title`) to pass data to the JavaScript via $\text{data-attributes}$.

```html
<div class="share-btn-wrap" data-url="{{ .Permalink }}" data-title="{{ .Title }}">
  <button class="share-btn" aria-haspopup="true" aria-expanded="false" aria-label="Share this post">
    <svg width="18" height="18" viewBox="0 0 24 24" aria-hidden="true" focusable="false">
      <path d="M18 8a3 3 0 1 0-2.83-4H11a1 1 0 0 0 0 2h4.17A3.01 3.01 0 0 0 18 8zM6 6a3 3 0 1 0 0 6 3 3 0 0 0 0-6zm0 10a3 3 0 1 0 0 6 3 3 0 0 0 0-6zm12 0a3 3 0 1 0 0 6 3 3 0 0 0 0-6z" fill="currentColor"></path>
    </svg>
    <span class="share-label">Share</span>
  </button>

  <div class="share-menu" role="menu" aria-hidden="true">
    <button class="share-action share-native" role="menuitem">Share (Native)</button>
    <button class="share-action share-copy" role="menuitem">Copy link</button>
    <a class="share-action share-twitter" role="menuitem" target="_blank" rel="noopener noreferrer">Twitter</a>
    <a class="share-action share-facebook" role="menuitem" target="_blank" rel="noopener noreferrer">Facebook</a>
    <a class="share-action share-linkedin" role="menuitem" target="_blank" rel="noopener noreferrer">LinkedIn</a>
  </div>
</div>
```

-----

## 🎨 2. The CSS ($\text{static/css/style.css}$)

Add these styles to your main stylesheet to handle the layout, appearance, and the initial hidden state of the menu.

```css
/* === Share Button Styles === */
.share-btn-wrap {
  position: relative;
  display: inline-block;
  font-family: system-ui, -apple-system, Segoe UI, Roboto, 'Helvetica Neue', Arial;
}

.share-btn {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.4rem 0.6rem;
  border: 1px solid rgba(0, 0, 0, 0.08);
  background: #fff;
  border-radius: 8px;
  cursor: pointer;
  font-size: 0.95rem;
  transition: background 0.1s; /* Add transition for hover effect */
}

.share-btn:hover {
  background: rgba(0, 0, 0, 0.02);
}

.share-btn svg {
  display: block;
}

.share-label {
  display: inline-block;
}

.share-menu {
  position: absolute;
  right: 0;
  top: calc(100% + 0.5rem);
  min-width: 160px;
  padding: 0.4rem;
  border-radius: 8px;
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.08);
  background: #fff;
  border: 1px solid rgba(0, 0, 0, 0.06);
  display: none; /* Hidden by default */
  z-index: 60;
}

/* JavaScript toggles this attribute to show the menu */
.share-menu[aria-hidden="false"] {
  display: block;
}

.share-action {
  display: block;
  width: 100%;
  text-align: left;
  padding: 0.5rem 0.6rem;
  border-radius: 6px;
  border: none;
  background: transparent;
  cursor: pointer;
  font-size: 0.95rem;
  text-decoration: none; /* Ensure links look like buttons */
  color: inherit; /* Inherit text color */
}

.share-action:hover {
  background: rgba(0, 0, 0, 0.03);
}

/* Ensure anchor tags and buttons within the menu share the same style */
.share-action[role="menuitem"]:not(a) {
  text-decoration: none;
  color: inherit;
}
```

-----

## ⚙️ 3. The JavaScript ($\text{static/js/main.js}$)

This script handles the menu logic, populates the social media links using the **data-attributes**, and implements the native sharing and copy-to-clipboard functionality.

```javascript
document.addEventListener("DOMContentLoaded", function () {
  // Select all share button wrappers on the page
  const wraps = document.querySelectorAll(".share-btn-wrap");

  wraps.forEach((wrap) => {
    // Get all necessary elements within the current wrapper
    const btn = wrap.querySelector(".share-btn");
    const menu = wrap.querySelector(".share-menu");
    const nativeBtn = wrap.querySelector(".share-native");
    const copyBtn = wrap.querySelector(".share-copy");
    const twitter = wrap.querySelector(".share-twitter");
    const facebook = wrap.querySelector(".share-facebook");
    const linkedin = wrap.querySelector(".share-linkedin");

    // Get page data from HTML data-attributes
    const pageUrl = wrap.getAttribute("data-url") || window.location.href;
    const pageTitle = wrap.getAttribute("data-title") || document.title || "";

    // Encode URL and Title for safe use in query strings
    const encodedUrl = encodeURIComponent(pageUrl);
    const encodedTitle = encodeURIComponent(pageTitle);

    // 1. Populate Social Media Links
    twitter.href = `https://twitter.com/intent/tweet?url=${encodedUrl}&text=${encodedTitle}`;
    facebook.href = `https://www.facebook.com/sharer/sharer.php?u=${encodedUrl}`;
    linkedin.href = `https://www.linkedin.com/sharing/share-offsite/?url=${encodedUrl}`;

    // Helper functions for menu state
    function openMenu() {
      menu.setAttribute("aria-hidden", "false");
      btn.setAttribute("aria-expanded", "true");
      // Add global listener to close menu when clicking outside
      document.addEventListener("click", outsideListener);
    }

    function closeMenu() {
      menu.setAttribute("aria-hidden", "true");
      btn.setAttribute("aria-expanded", "false");
      // Remove global listener
      document.removeEventListener("click", outsideListener);
    }

    function outsideListener(e) {
      if (!wrap.contains(e.target)) closeMenu();
    }

    // 2. Button Click Listener (Toggle Menu)
    btn.addEventListener("click", function () {
      const expanded = btn.getAttribute("aria-expanded") === "true";
      if (expanded) closeMenu();
      else openMenu();
    });

    // 3. Native Share Implementation
    nativeBtn.addEventListener("click", async function () {
      if (navigator.share) {
        try {
          await navigator.share({ title: pageTitle, url: pageUrl });
          closeMenu();
        } catch (err) {
          // console.debug is often preferred over console.log for non-critical errors
          console.debug("Native share canceled or failed", err);
        }
      } else {
        alert("Native sharing not supported on this device/browser.");
      }
    });

    // 4. Copy Link Implementation
    copyBtn.addEventListener("click", async function () {
      try {
        if (navigator.clipboard && navigator.clipboard.writeText) {
          // Modern, preferred way
          await navigator.clipboard.writeText(pageUrl);
        } else {
          // Fallback for older browsers (document.execCommand is deprecated but useful)
          const t = document.createElement("textarea");
          t.value = pageUrl;
          t.style.position = "absolute";
          t.style.left = "-9999px";
          document.body.appendChild(t);
          t.select();
          document.execCommand("copy");
          document.body.removeChild(t);
        }
        
        // Visual feedback
        copyBtn.textContent = "Link copied ✓";
        setTimeout(() => (copyBtn.textContent = "Copy link"), 1500);
        closeMenu();
      } catch (e) {
        alert("Could not copy link. Please try manually.");
      }
    });

    // 5. Accessibility and Keyboard Support (Escape key to close)
    wrap.addEventListener("keydown", function (e) {
      if (e.key === "Escape") closeMenu();
      // Optional: Add logic for ArrowDown to focus on the first menu item
      if (e.key === "ArrowDown") {
        e.preventDefault();
        const first = menu.querySelector('[role="menuitem"]');
        if (first) first.focus();
      }
    });

    // Ensure all menu items are focusable for keyboard navigation
    menu
      .querySelectorAll('[role="menuitem"]')
      .forEach((el) => (el.tabIndex = 0));
  });
});
```

-----

## ✅ 4. Including Files in Your Hugo Layout

Make sure your Hugo templates reference these assets correctly so the HTML structure, styles, and script all work together.

### In Your $\text{<head>}$ (For CSS)

Include the stylesheet link in your base template (`layouts/_default/baseof.html`) or your custom `<head>` partial:

```html
<link rel="stylesheet" href="{{ "css/style.css" | relURL }}">
```

### At the End of Your $\text{<body>}$ (For JavaScript)

Include the script tag, ideally just before the closing $\text{</body>}$ tag to ensure the DOM is fully loaded before the script tries to access the elements (though wrapping it in `DOMContentLoaded` handles this too):

```html
<script src="{{ "js/main.js" | relURL }}"></script>
```
