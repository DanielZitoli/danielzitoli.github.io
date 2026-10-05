# danielzitoli.github.io

My personal website, built with [Quarto](https://quarto.org) and hosted on GitHub Pages.

- **Home** (`index.qmd`): profile picture, links (LinkedIn, GitHub, resume, email) and an about-me section.
- **Projects** (`projects.qmd`): one card per project, each with **Code**, **Live site** and **Write-up** buttons.
- **Write-ups** (`projects/<name>/index.qmd`): Markdown pages with LaTeX math, code and callouts.

```
.
├── _quarto.yml                 # site config: title, navbar, theme, footer
├── styles.scss                 # custom styles (card look, fonts)
├── index.qmd                   # landing page
├── projects.qmd                # projects listing page
├── projects.ejs                # card template used by the listing
├── projects/
│   ├── _metadata.yml           # shared options for every write-up (TOC, etc.)
│   └── <project-name>/
│       ├── index.qmd           # the write-up; its front matter drives the card
│       └── thumbnail.svg|png   # card image
├── images/profile.svg          # replace with your photo
├── files/resume.pdf            # replace with your resume
└── .github/workflows/publish.yml  # renders and deploys on every push to main
```

---

## 1. Run it locally

1. Install Quarto: <https://quarto.org/docs/get-started/> (VS Code users: also install the Quarto extension).
2. Clone the repo and preview it:
   ```bash
   git clone https://github.com/DanielZitoli/danielzitoli.github.io
   cd danielzitoli.github.io
   quarto preview          # opens a live-reloading browser tab
   ```
3. Edit any `.qmd` file and the page reloads automatically.

You don't commit the rendered HTML: `_site/` is git-ignored and GitHub Actions builds the site for you.

## 2. Make it yours

| What | Where |
|---|---|
| Name, navbar links, footer | `_quarto.yml` |
| Profile photo | Put `profile.jpg` in `images/`, then set `image: images/profile.jpg` in `index.qmd` |
| Bio, current work, education | Body of `index.qmd` |
| LinkedIn / email / resume links | `about.links` in `index.qmd` (and the LinkedIn icon in `_quarto.yml`) |
| Resume | Replace `files/resume.pdf` (keep the same name so the link keeps working) |
| Landing-page layout | `about.template` in `index.qmd`: `trestles`, `jolla`, `solana`, `marquee`, `broadside` |
| Colours / theme | `format.html.theme` in `_quarto.yml` (any [Bootswatch theme](https://quarto.org/docs/output-formats/html-themes.html)) and `styles.scss` |

Search for `YOUR` to find every placeholder.

## 3. Add a project

1. Copy an existing folder, e.g. `projects/example-gradient-descent/` → `projects/my-cool-project/`.
2. Edit the front matter at the top of `index.qmd`:
   ```yaml
   ---
   title: "My Cool Project"
   description: "One-sentence summary shown on the card."
   date: 2026-10-01                 # cards are sorted newest first
   categories: [Python, ML]         # tags; also used by the category filter
   image: thumbnail.png             # card image (16:9 looks best)
   github: https://github.com/DanielZitoli/my-cool-project   # omit to hide the Code button
   demo: https://my-cool-project.vercel.app                  # omit to hide the Live site button
   # writeup: false                 # set this if the project has no write-up yet
   ---
   ```
3. Write the explanation below the front matter. It appears on the Projects page automatically.

### Writing math (LaTeX)

Quarto renders TeX with MathJax:

```markdown
Inline: $e^{i\pi} + 1 = 0$

Display, numbered and referenceable:

$$
\mathcal{L}(\theta) = \frac{1}{n}\sum_{i=1}^n \ell(f_\theta(x_i), y_i)
$$ {#eq-loss}

As shown in @eq-loss, ...
```

`align`/`aligned`, `\begin{cases}` and the other standard environments work too.
See `projects/example-gradient-descent/index.qmd` for a complete example.

> **Executable code:** if a write-up runs Python/R code chunks, run `quarto render` locally
> and commit the generated `_freeze/` folder. CI then reuses those results instead of
> needing your Python environment (`freeze: auto` is already set in `_quarto.yml`).

## 4. Deploy to GitHub Pages (one-time setup)

Because the repo is named `danielzitoli.github.io`, it's a *user site* served at
**https://danielzitoli.github.io**.

1. Merge this branch into `main`.
2. On GitHub, go to **Settings → Pages → Build and deployment → Source** and choose **GitHub Actions**.
3. Push to `main` (or open **Actions → Build and deploy site → Run workflow**).
4. When the workflow is green, the site is live. Each later push to `main` redeploys in about a minute.

Pull requests only build the site, so a broken page fails the check before it reaches `main`.

## 5. Custom domain (optional)

GitHub Pages supports custom domains for free, with automatic HTTPS. Buy a domain from any
registrar (Cloudflare, Namecheap, Porkbun, Google/Squarespace, etc.), then:

1. **Verify the domain** (recommended, prevents takeover): GitHub → your profile **Settings → Pages → Add a domain**, and add the TXT record it shows at your registrar.
2. **Add DNS records** at your registrar:

   | Type | Host / Name | Value |
   |---|---|---|
   | `A` | `@` | `185.199.108.153` |
   | `A` | `@` | `185.199.109.153` |
   | `A` | `@` | `185.199.110.153` |
   | `A` | `@` | `185.199.111.153` |
   | `AAAA` | `@` | `2606:50c0:8000::153` (also `8001`, `8002`, `8003`) |
   | `CNAME` | `www` | `danielzitoli.github.io` |

   For a subdomain only (e.g. `me.example.com`), a single `CNAME me → danielzitoli.github.io` is enough.
3. **Repo settings**: **Settings → Pages → Custom domain**, enter `yourdomain.com`, save, and once the DNS check passes, tick **Enforce HTTPS** (the certificate can take up to about an hour).
4. **Update** `site-url` in `_quarto.yml` to `https://yourdomain.com`.

Since the site deploys through GitHub Actions, you set the domain in Settings and **don't** need a `CNAME` file in the repo.
`danielzitoli.github.io` will redirect to your domain automatically.
DNS changes can take anywhere from a few minutes to 24 hours to take effect.
