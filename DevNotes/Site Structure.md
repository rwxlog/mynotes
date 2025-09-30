# Site Structure

## Static Structure
- index.html
- about.html
- contact.html
- portfolio.html
- posts.html
- pages/
  - page1.html
  - page2.html
  - page3.html
- posts/
  - post1.html
  - post2.html
  - post3.html
- css/
  - style.css
  - responsive.css
- js/
  - main.js
- images/
  - logo.png

---

## Dynamic Structure
- index.html
- about.html
- contact.html
- portfolio.html
- posts.html
- pages/
  - page1.html
  - page2.html
  - page3.html
- posts/
  - post1.html
  - post2.html
  - post3.html
- components/
  - header.html
  - footer.html
- assets/
  - css/
    - style.css
    - responsive.css
  - js/
    - main.js
  - images/
    - logo.png
- data/
  - posts.json

---

## Hugo Structure
- config.toml
- content/
  - pages/
    - about.md
    - contact.md
    - portfolio.md
  - posts/
    - post1.md
    - post2.md
    - post3.md
- layouts/
  - _default/
    - baseof.html
    - list.html
    - page.html
    - single.html
  - posts/
    - list.html
  - partials/
    - header.html
    - footer.html
  - index.html
- static/
  - css/
    - style.css
    - responsive.css
  - js/
    - theme.js
  - images/
    - logo.png

---

## Jekyll Structure
- _config.yml
- index.md
- 404.html
- pages/
  - about.md
  - contact.md
  - portfolio.md
  - posts.md
- _posts/
  - 2025-09-22-post-001.md
  - 2025-09-23-post-002.md
  - 2025-09-23-post-003.md
- _includes/
  - header.html
  - footer.html
  - head.html
- _layouts/
  - default.html
  - home.html
  - post.html
  - list.html
  - page.html
- assets/
  - css/
    - style.css
    - responsive.css
  - js/
    - main.js
  - images/
    - logo.png

---

## Eleventy Project Structure

- .eleventy.js  
- package.json
- index.md
- 404.md
- pages/
  - about.md
  - contact.md
  - portfolio.md
  - posts.md
- posts/
  - hello-world.md
  - example-post.md
  - another-post.md
- _includes/
  - header.html
  - footer.html
  - head.html
  - layouts/
    - base.html
    - home.html
    - post.html
    - list.html
    - page.html
- assets/
  - css/
    - style.css
    - responsive.css
  - js/
    - main.js
  - images/
    - logo.png
- _data/
  - site.json
