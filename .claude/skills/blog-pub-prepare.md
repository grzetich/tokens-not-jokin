# blog-pub-prepare

Prepare a blog post for publication: add SEO meta tags, Open Graph tags, Twitter Card, LD+JSON structured data, social sharing links, and generate the OG image.

## Usage

```
/blog-pub-prepare <slug>
```

Where `<slug>` is the blog post directory name (e.g., `the-problem-nobodys-measuring`).

## File structure

```
blog/<slug>/
  index.html      ← the blog post
  og-image.html   ← OG image HTML template (1200x630)
  og-image.png    ← screenshotted PNG
```

Base URL: `https://tokensnotjokin.com`
Post URL: `https://tokensnotjokin.com/blog/<slug>`
OG image URL: `https://tokensnotjokin.com/blog/<slug>/og-image.png`

## Steps

### 1. Read the blog post

Read `blog/<slug>/index.html` to extract:
- The article `<h1>` title
- The `.article-lede` text
- The publication date from the `<time datetime="...">` element
- The article body text (to estimate word count and reading time)
- Relevant keywords from the content

### 2. Add SEO meta tags

Add or update these tags in `<head>`. Use `blog/the-problem-nobodys-measuring/index.html` as the reference for exact format.

```html
<title>{Title}: {Short subtitle} | Tokens Not Jokin'</title>
<meta name="description" content="{Lede or summary, ~155 chars}">
<meta name="author" content="Ed Grzetich">
<meta name="keywords" content="{comma-separated relevant keywords}">
```

### 3. Add Open Graph tags

```html
<!-- Open Graph -->
<meta property="og:type" content="article">
<meta property="og:title" content="{Title}: {Short subtitle}">
<meta property="og:description" content="{Summary, ~200 chars}">
<meta property="og:url" content="https://tokensnotjokin.com/blog/<slug>">
<meta property="og:image" content="https://tokensnotjokin.com/blog/<slug>/og-image.png">
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">
<meta property="og:site_name" content="Tokens Not Jokin'">
<meta property="article:published_time" content="{ISO 8601 date}">
<meta property="article:author" content="https://grzeti.ch">
<meta property="article:section" content="Research">
<meta property="article:tag" content="{Tag 1}">
<meta property="article:tag" content="{Tag 2}">
<!-- ...more tags as appropriate -->
```

### 4. Add Twitter Card tags

```html
<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="{Title}: {Short subtitle}">
<meta name="twitter:description" content="{Summary, ~200 chars}">
<meta name="twitter:image" content="https://tokensnotjokin.com/blog/<slug>/og-image.png">
<meta name="twitter:label1" content="Written by">
<meta name="twitter:data1" content="Ed Grzetich">
<meta name="twitter:label2" content="Reading time">
<meta name="twitter:data2" content="{N} min read">
```

### 5. Add canonical URL and favicon

```html
<link rel="icon" type="image/svg+xml" href="/favicon.svg">
<link rel="canonical" href="https://tokensnotjokin.com/blog/<slug>">
```

### 6. Add LD+JSON structured data

Add a `<script type="application/ld+json">` block with an `@graph` containing three objects. Use `blog/the-problem-nobodys-measuring/index.html` as the exact reference for structure:

1. **Article** — `@type: Article` with headline, description, url, datePublished, dateModified, author ref, publisher, image (pointing to og-image.png), keywords array, articleSection, wordCount, isPartOf (Blog), and about array
2. **Person** — `@type: Person` with `@id: "https://tokensnotjokin.com/#author"`, name "Ed Grzetich", url "https://grzeti.ch", sameAs LinkedIn, jobTitle, description
3. **BreadcrumbList** — Home → Blog → Post title

### 7. Add social share bar

Add a `.share-bar` div after the `.article-cta` box (or at the end of the article content if no CTA). Include the share bar CSS in the `<style>` block.

HTML:
```html
<div class="share-bar">
    <span class="share-label">Share</span>
    <a href="https://twitter.com/intent/tweet?url={encoded post URL}&text={encoded title + short teaser}" target="_blank" rel="noopener">Twitter</a>
    <a href="https://www.linkedin.com/sharing/share-offsite/?url={encoded post URL}" target="_blank" rel="noopener">LinkedIn</a>
    <button onclick="navigator.clipboard.writeText('{post URL}');this.textContent='Copied!';setTimeout(()=>this.textContent='Copy link',2000)">Copy link</button>
</div>
```

CSS (add to `<style>` if not already present):
```css
/* ---- SHARE BAR ---- */
.share-bar {
    margin-top: 2.5rem;
    padding-top: 1.5rem;
    border-top: 1px solid var(--rule);
    display: flex;
    align-items: center;
    gap: 1.5rem;
}

.share-bar .share-label {
    font-family: 'DM Mono', monospace;
    font-size: 0.7rem;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--muted);
}

.share-bar a,
.share-bar button {
    font-family: 'DM Mono', monospace;
    font-size: 0.7rem;
    color: var(--muted);
    text-decoration: none;
    letter-spacing: 0.05em;
    border: none;
    background: none;
    cursor: pointer;
    padding: 0;
    border-bottom: 1px solid transparent;
    transition: all 0.2s;
}

.share-bar a:hover,
.share-bar button:hover {
    color: var(--accent);
    border-bottom-color: var(--accent);
}
```

### 8. Create OG image HTML template

Create `blog/<slug>/og-image.html` — a standalone 1200x630 HTML page. Use `blog/the-problem-nobodys-measuring/og-image.html` as the reference template. The structure is:

- Fixed dimensions: `width: 1200px; height: 630px; overflow: hidden`
- Fonts: DM Mono, Instrument Serif, DM Sans (loaded from Google Fonts)
- Colors: background `#f6f1eb`, text `#0a0a0a`, accent `#c44b1a`, muted `#7a7168`
- Layout: top-line (Blog + dot + month year), middle (h1 title + lede), bottom (site name + author/domain)
- The `h1` should be max `72px`, the lede max `22px`
- Adjust font sizes or lede text length so everything fits within 630px height without overflow

### 9. Screenshot OG image with Puppeteer

Create a temp script `screenshot-og.mjs` and run it to capture the PNG at exactly 1200x630:

```js
import puppeteer from 'puppeteer';
const slug = process.argv[2];
const browser = await puppeteer.launch();
const page = await browser.newPage();
await page.setViewport({ width: 1200, height: 630 });
await page.goto(`file:///${process.cwd().replace(/\\/g, '/')}/blog/${slug}/og-image.html`, { waitUntil: 'networkidle0' });
await page.screenshot({ path: `blog/${slug}/og-image.png`, type: 'png' });
await browser.close();
console.log(`Saved blog/${slug}/og-image.png`);
```

Run:
```
npm install --no-save puppeteer
node screenshot-og.mjs <slug>
```

After the screenshot is saved, clean up: delete `screenshot-og.mjs`, `node_modules/`, `package-lock.json`, and `package.json`.

### 10. Verify

Read the generated `og-image.png` to confirm it rendered correctly. Show the image to the user for approval.
