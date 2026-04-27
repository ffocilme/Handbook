# 📘 ICPC Competitive Programming Handbook

A **competitive programming reference handbook** built for ICPC-style contests.  
Written in Markdown, compiled to a print-ready PDF via **pandoc + pdflatex**.

---

## 🎯 Purpose

Quick reference during contests — algorithms, data structures, templates and formulas, all in one place.

- **Concise** → no long explanations, straight to the code  
- **Practical** → ready-to-use C++ snippets  
- **Reliable** → tested during practice and real contests  
- **Printable** → designed to look great on paper

---

## 📂 Project Structure

```
Handbook/
├── main.tex                  # LaTeX entry point (auto-updated by make)
├── style.tex                 # Visual style: themes, fonts, code blocks, tables
├── Makefile                  # Build pipeline
│
├── scripts/
│   ├── handbook.lua          # Pandoc Lua filter (code blocks, headings)
│   ├── inject.py             # Injects \input{} list into main.tex
│   ├── gobble.sty            # LaTeX stub (required by markdown package)
│   ├── theme.tex             # Active theme (auto-written by make)
│   └── settings.json         # VSCode LaTeX Workshop config
│
├── 00-legal/
├── 01-fundamentos/           # I/O, templates, precision, bit manipulation
├── 02-ordenamiento_busqueda/ # Sorting and binary search
├── 03-matematicas/           # Combinatorics, modular arithmetic, number theory
├── 04-data_structures/       # Arrays, monotonic stacks
├── 05-dp/                    # Dynamic programming
├── 06-grafos/                # Graphs, MST, advanced graphs
├── 07-misc/                  # Miscellaneous tricks
├── 09_RangeQuery/            # Sparse table, range queries
├── 10-arboles/               # Trees, tries, LCA, Tarjan
├── 11-miscelanios/           # Formulas and reference tables
├── 12-LegacyHandbook/        # Classic algorithms (KMP, Manacher, Mo, etc.)
└── 13_Legacy_geometry/       # Computational geometry
```

---

## ⚙️ Setup

### 🐧 Linux / WSL (Ubuntu / Debian)

```bash
sudo apt update
sudo apt install -y git make pandoc python3 \
  texlive-latex-base texlive-latex-recommended \
  texlive-latex-extra texlive-fonts-recommended \
  texlive-plain-generic texlive-science texlive-pictures
```

> **Note:** `texlive-full` (~5 GB) also works but is much larger.  
> The packages above cover everything the handbook needs (~500 MB).

Auto-rebuild on save (optional):

```bash
sudo apt install -y entr
```

---

### 🪟 Windows

The recommended approach is **VSCode + WSL** (no LaTeX install on Windows needed):

1. Install [WSL with Debian](https://learn.microsoft.com/en-us/windows/wsl/install)
2. Install the [VSCode WSL extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl)
3. Open the project from inside Debian:
   ```bash
   cd ~/handbook
   code .
   ```
4. Install the LaTeX Workshop extension **in WSL** (VSCode will prompt you)
5. Copy `scripts/settings.json` into `.vscode/settings.json`

Alternatively, install [MiKTeX](https://miktex.org/download) natively on Windows and use Git Bash.

#### Native Windows (no WSL) — Git Bash + Chocolatey

If your machine doesn't support virtualization (so WSL is not an option), you can build natively using Git Bash. You need to install three tools:

**1. Install `make` via Chocolatey**

Open **PowerShell as Administrator** and run:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

Then, still in PowerShell as Administrator:

```powershell
choco install make -y
```

**2. Install Pandoc**

Download and run the `.msi` installer from [pandoc.org/installing.html](https://pandoc.org/installing.html).

**3. Install MiKTeX (LaTeX for Windows)**

Download and run the installer from [miktex.org/download](https://miktex.org/download).  
When asked *"Install missing packages on-the-fly"*, select **Yes** — MiKTeX will automatically download any missing LaTeX packages on the first build.

**4. Build**

Close and reopen Git Bash (so PATH updates take effect), then:

```bash
cd /c/Users/<your-user>/Desktop/Handbook
make all
```

---

### 🍎 macOS

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install git pandoc python3
brew install --cask mactex
```

---

## 📥 Clone

```bash
git clone https://github.com/Felipe-Focil/Handbook.git
cd Handbook
```

---

## 🛠️ Build

### Full build (recommended — two pdflatex passes for correct TOC)

```bash
make
```

### Fast draft (single pass, TOC may be stale)

```bash
make once
```

### Auto-rebuild on file save

```bash
make watch    # requires: sudo apt install entr
```

### Open the PDF

```bash
make open
```

### Output

The final PDF is written to the project root:

```
main.pdf
```

Intermediate build files go into `.build/` and are ignored by Git.

---

## 🎨 Themes

The code block color theme can be changed at build time:

```bash
make THEME=ide      # purple keywords, dark-red strings, green comments (default)
make THEME=print    # bold black — safe for B&W laser printers
make THEME=blue     # navy / steel-blue palette
make THEME=dark     # VS Code dark (light text on dark background)
```

To make a theme permanent, edit `scripts/theme.tex`:

```latex
\def\THEME{print}
```

---

## ✍️ Adding Content

1. Create or edit a `.md` file in the appropriate chapter folder
2. Write in standard Markdown — math and raw LaTeX both work:
   - Inline math: `$O(n \log n)$`
   - Display math: `$$\sum_{i=1}^{n} i = \frac{n(n+1)}{2}$$`
   - Raw LaTeX environments: `\begin{cases}...\end{cases}`
3. Fenced code blocks are automatically styled:
   ````markdown
   ```cpp
   int main() { ... }
   ```
   ````
4. Add a new `\input{_tex/your-folder/your-file.tex}` line in `main.tex` where you want it to appear (the `make` pipeline generates the `_tex/` files automatically)
5. Run `make` or `make once`

---

## 🔧 How the Pipeline Works

```
*.md  ──pandoc──►  _tex/*.tex  ──pdflatex──►  .build/main.pdf  ──cp──►  main.pdf
         │
         └── scripts/handbook.lua
               • Fenced code blocks  → \codeInput{_pandoc_cache/file_NNN.verbatim}
               • Headings            → \section{} / \subsection{} (no \hypertarget)
               • Inline code         → \codeinline{...}
```

1. **`make _pandoc`** — runs pandoc on every `.md` file, outputs into `_tex/`  
2. **`make _generate_main`** — injects `\input{_tex/...}` lines into `main.tex` at the `>>> GENERATED_BY_MAKEFILE <<<` marker  
3. **`pdflatex` × 2** — compiles to PDF (two passes resolve the TOC and cross-references)

---

## 🧹 Clean

```bash
make clean      # removes .build/, _tex/, intermediate files (keeps main.pdf)
make cleanall   # also removes main.pdf
```

---

## 📄 License

See [00-legal/01_legal.md](00-legal/01_legal.md).
