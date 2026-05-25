# Another Awesome CV — Research / Academic Variant

A real-world academic research CV (LaTeX → PDF) and personal GitHub Pages website, forked from [posquit0/Awesome-CV](https://github.com/posquit0/Awesome-CV) and extended for research use.

The [`research-cv/`](research-cv/) folder contains **complete, working LaTeX source** of an active academic CV — use it as a full worked example or adapt it for your own.

**[📄 Live CV PDF](https://dae416.github.io/papers/choi_cv.pdf) · [🌐 Live Website](https://dae416.github.io) · [Website Source](https://github.com/dae416/dae416.github.io)**

---

## Part 1: LaTeX Research CV

### File structure

```
research-cv/
  cv.tex                           # Main file — personal info, macros, section order
  awesome-cv.cls                   # Extended class (adds pubSubsectionNum, etc.)
  cv/
    aboutme.tex                    # Summary, highlights, research interests, skills
    work_experience.tex            # Academic employment
    education.tex                  # Degrees
    publications.tex               # Section wrapper (inputs journals + conferences)
    publication_journals.tex       # Journal articles (numbered list)
    publication_conf.tex           # Conferences & seminars (numbered list)
    research_project_experience.tex
    honors.tex                     # Awards & grants
    teaching.tex                   # Mentoring & teaching
    patents.tex
```

### Compile

```bash
cd research-cv
xelatex -interaction=nonstopmode cv.tex
```

Requires XeLaTeX. Packages used: `fontspec`, `fontawesome5`, `academicons`, `hyperref`, `enumitem`, `booktabs`, `xcolor`, `pifont`, `wrapfig`, `subcaption`.

On macOS with MacTeX:
```bash
brew install --cask mactex
```

### Key customizations over vanilla Awesome-CV

#### 1. Color theme
```latex
\definecolor{awesome}{HTML}{1A2F6B}        % Dark navy
\setbool{acvSectionColorHighlight}{false}
```

#### 2. Journal / venue shorthands
```latex
\newcommand{\jfm}{\textit{J. Fluid Mech.}}
\newcommand{\pof}{\textit{Phys. Fluids}}
\newcommand{\srep}{\textit{Sci. Rep.}}
% ... add your own field's journals
```

#### 3. Under review / in preparation labels
```latex
\renewcommand{\underreview}{\textcolor{BrickRed}{\textit{Under review.}}}
\newcommand{\inprep}[1]{\textcolor{BrickRed}{\textit{In preparation (#1).}}}
```

#### 4. Toggle links on/off (public vs. private version)
Wrap URLs in `\ifshowcontent...\fi` to compile a clean link-free version:
```latex
\newif\ifshowcontent
\showcontenttrue   % change to \showcontentfalse for link-free output
```

```latex
\item Author, ``Title,'' \jfm (2024).
      \ifshowcontent
        \href{https://doi.org/...}{\underline{DOI}}
        \href{https://arxiv.org/...}{\underline{arXiv}}
      \fi
```

#### 5. GitHub stars badge
```latex
\newcommand{\githubstars}[1]{[\,{\faGithub}\,\ding{72}\,\textbf{#1}]}
% Usage: DeepBubbleVelocimetry \githubstars{9}
```

#### 6. Numbered publication list (`pubSubsectionNum`)
Custom environment (added to `awesome-cv.cls`) that produces a consecutively numbered publication list:
```latex
\begin{pubSubsectionNum}
  \item \textbf{Your Name} \& Coauthor, ``Title,'' \jfm, vol, page (year).
\end{pubSubsectionNum}
```

Bold your own name with a shorthand:
```latex
\newcommand{\daehyunchoi}{\textbf{Daehyun Choi}}
```

#### 7. Compact awards table (two columns: year | award)
```latex
\renewenvironment{cvhonors}{%
  \begin{tabular*}{\textwidth}{@{\extracolsep{\fill}} C{1.5cm} L{\textwidth - 1.5cm}}
}{...}
\renewcommand*{\cvhonor}[4]{%
  \honordatestyle{#4} & \honorpositionstyle{#1}, \honortitlestyle{#2} \\
}
```

---

## Part 2: GitHub Pages Academic Website

> Source: **[dae416/dae416.github.io](https://github.com/dae416/dae416.github.io)**
> Live: **[dae416.github.io](https://dae416.github.io)**

### Tech stack
- **Bootstrap 3.4** — grid, navbar, responsive breakpoints
- **Custom CSS** — `assets/css/daehyun2.css` (global), `assets/css/projects.css` (projects page)
- **Font Awesome 6**, Google Fonts (Inter)
- Pure HTML/CSS/JS — no build step, no framework

### Page structure

| File | Purpose |
|---|---|
| `index.html` | Landing page — bio, research interests, awards, news |
| `projects.html` | Research projects — two-panel layout |
| `publications.html` | Publications with thumbnail images/videos |
| `opensource.html` | Open source tools |
| `gallery.html` | Photo/video gallery |

### Two-panel projects page

`projects.html` uses a sticky sidebar (30%) + scrollable main panel (70%):
```css
.dc-proj-page            { display: flex; }
.dc-proj-sidebar-panel   { flex: 0 0 30%; position: sticky; top: 65px; }
.dc-proj-main-panel      { flex: 1; }
```
The sidebar highlights the current section as you scroll via `IntersectionObserver`.

### Video rules

Three video categories with different behavior:

| Type | Element | Autoplay | Progress bar | Cursor |
|---|---|---|---|---|
| Hero video | `.dc-video-wrap` | Manual (click overlay) | Hover seek bar | pointer on overlay |
| Gallery slide | `.dc-slide-wrap video` | Viewport enter | Hidden bar, shows on hover | pointer (via `:has(video)`) |
| Inline standalone | `.dc-full-proj-img-single video` | Viewport enter | Hidden bar, shows on hover | default |

All videos: `muted loop playsinline preload="none"` with `<source data-src="...">` for lazy loading.

#### Carousel static mode (≤3 slides)
Add `dc-gallery-static` **in HTML** (not just JS) to prevent a resize flash on load:
```html
<div class="dc-gallery-slider dc-gallery-static" id="gallery-xyz">
  ...
  <div class="dc-gallery-nav" style="display:none"></div>
</div>
```

#### Zoomed inline video
Wrap in `overflow:hidden` to clip scaled overflow from covering adjacent captions:
```html
<div style="overflow:hidden; border-radius:5px;">
  <video style="width:100%; transform:scale(1.4); transform-origin:center;">
    <source data-src="videos/..." type="video/mp4">
  </video>
</div>
<div class="dc-slide-progress"><div class="dc-slide-bar"></div></div>
<p class="dc-slide-caption">Caption</p>
```

### Deploying without local git (GitHub API method)

If your `.git` is unavailable (e.g., corrupted by Dropbox sync) or you want to push a single file directly:

```bash
# Requires GitHub CLI: https://cli.github.com/
FILE_SHA=$(gh api repos/USERNAME/USERNAME.github.io/contents/PATH --jq '.sha')
CONTENT=$(base64 -i "LOCAL_PATH")
gh api --method PUT repos/USERNAME/USERNAME.github.io/contents/PATH \
  -f message="update: description" \
  -f content="$CONTENT" \
  -f sha="$FILE_SHA" \
  --jq '.commit.sha'
```

---

## Credits

- LaTeX class: [posquit0/Awesome-CV](https://github.com/posquit0/Awesome-CV)
- Research CV adaptation: [LimHyungTae/Awesome-PhD-CV](https://github.com/LimHyungTae/Awesome-PhD-CV)
