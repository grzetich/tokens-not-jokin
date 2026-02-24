# blog-og-image

Generate an Open Graph image for a blog post.

## Usage

```
/blog-og-image <slug>
```

Where `<slug>` is the blog post filename without extension (e.g., `the-problem-nobodys-measuring`).

## File structure

Blog posts use a directory structure:

```
blog/<slug>/
  index.html      ← the blog post
  og-image.html   ← OG image HTML template (1200x630)
  og-image.png    ← screenshotted PNG
```

The OG image URL is `https://tokensnotjokin.com/blog/<slug>/og-image.png`.

## Steps

### 1. Read the blog post

Read `blog/<slug>/index.html` to extract:
- The article `<h1>` title
- The `.article-lede` text (shortened to fit the OG card)
- The publication date from the `<time>` element

### 2. Create the OG HTML template

Create `blog/<slug>/og-image.html` — a standalone 1200x630 HTML page designed for screenshotting. Use `blog/the-problem-nobodys-measuring/og-image.html` as the reference template. The structure is:

- Fixed dimensions: `width: 1200px; height: 630px; overflow: hidden`
- Fonts: DM Mono, Instrument Serif, DM Sans (loaded from Google Fonts)
- Colors: background `#f6f1eb`, text `#0a0a0a`, accent `#c44b1a`, muted `#7a7168`
- Layout: top-line (Blog + dot + month year), middle (h1 title + lede), bottom (site name + author/domain)
- The `h1` should be max `72px`, the lede max `22px`
- Adjust font sizes or lede text length so everything fits within 630px height without overflow

### 3. Screenshot with Puppeteer

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

### 4. Update the blog post meta tags

In `blog/<slug>/index.html`, update these three image references to point to the post-specific OG image:

- `og:image` meta tag → `https://tokensnotjokin.com/blog/<slug>/og-image.png`
- `twitter:image` meta tag → `https://tokensnotjokin.com/blog/<slug>/og-image.png`
- LD+JSON `image.url` → `https://tokensnotjokin.com/blog/<slug>/og-image.png`
