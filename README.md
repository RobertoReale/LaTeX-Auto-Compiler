# LaTeX Auto-Compiler

A GitHub Actions workflow that automatically finds, compiles, and commits all LaTeX documents in your repository whenever you push changes.

## Features

- **Smart Root Detection**: Only compiles root documents (files containing `\documentclass`), skipping sub-files and inputs automatically
- **Full TeXLive Environment**: Uses the complete TeXLive Docker container for maximum compatibility
- **`latexmk` Compilation**: Handles bibliographies (BibTeX/Biber), indexes, and multi-pass references automatically
- **Auto-Commit**: Automatically commits compiled PDFs back to the repository on main/master branches
- **Artifact Storage**: Uploads all PDFs as a single artifact with 30-day retention
- **Concurrency Control**: Cancels redundant runs when you push multiple times in quick succession
- **Error Resilience**: Continues processing other files even if one fails to compile

## Quick Setup

### 1. Copy the Workflow File

Create the directory `.github/workflows/` in your repository and copy `clean_latex_workflow.yml` into it, renaming it to `latex.yml` (or any `.yml` name you prefer).

### 2. Enable GitHub Actions Permissions

**This step is crucial for the workflow to function properly.**

1. Go to your repository on GitHub
2. Navigate to **Settings** → **Actions** → **General**
3. Scroll down to **"Workflow permissions"**
4. Select **"Read and write permissions"**
5. Click **"Save"**

Without these permissions, the workflow cannot commit the compiled PDFs back to your repository.

### 3. Add Your LaTeX Files

Simply add your `.tex` files anywhere in your repository. The workflow will automatically find and compile them.

## How It Works

### Trigger Events
The workflow runs automatically on:
- Push to `main` or `master` branch
- Pull requests to `main` or `master` branch

If you push multiple commits in quick succession, the workflow cancels the previous run and starts fresh (concurrency control).

### Compilation Process

1. **Discovery**: Scans the entire repository for `.tex` files that contain `\documentclass` (root documents only)
2. **Compilation**: Runs `latexmk -pdf` on each document — handles bibliographies, indexes, and multiple passes automatically
3. **Artifact Upload**: Bundles all compiled PDFs into a single downloadable artifact
4. **Auto-Commit**: On main/master push events, commits PDFs back to their original locations
5. **Summary**: Generates a table report showing compiled vs. failed files

### Directory Structure Preserved

The workflow maintains your repository's directory structure. If you have:
```
docs/
├── chapter1/
│   └── intro.tex
├── chapter2/
│   └── methodology.tex
└── references.tex
```

The compiled PDFs will be placed in the same directories:
```
docs/
├── chapter1/
│   ├── intro.tex
│   └── intro.pdf
├── chapter2/
│   ├── methodology.tex
│   └── methodology.pdf
├── references.tex
└── references.pdf
```

### Sub-files Are Skipped Automatically

If your project uses `\input{}` or `\include{}` to split the document across multiple `.tex` files, only the root file (the one with `\documentclass`) will be compiled. Sub-files without `\documentclass` are ignored.

## Usage Examples

### Basic Usage
1. Add a `.tex` file to your repository
2. Push to main/master branch
3. The workflow automatically compiles it and commits the PDF

### Projects with Bibliographies
No extra configuration needed — `latexmk` automatically runs Biber/BibTeX and re-runs pdflatex as many times as needed.

### Multiple Documents
The workflow handles any number of LaTeX documents across any directory structure. Each root document is compiled in sequence.

## Workflow Jobs

### 1. `compile-latex`
- Discovers all root `.tex` files (`\documentclass` present)
- Compiles each with `latexmk -pdf`
- Collects all generated PDFs and uploads them as one artifact
- Outputs a summary table to the Actions run page

### 2. `commit-pdfs`
- Runs only on push to main/master (not on pull requests)
- Downloads the artifact and places each PDF next to its source `.tex` file
- Commits and pushes only if PDFs actually changed

## Customization

### Changing Target Branches
Edit the `branches` section in the workflow:
```yaml
on:
  push:
    branches: [ main, master, your-branch ]
```

### Adjusting `latexmk` Options
Modify the `latexmk` command in the compile step:
```bash
latexmk -pdf -interaction=nonstopmode -halt-on-error "$base_name.tex"
```
For example, add `-xelatex` or `-lualatex` if your document requires a different engine.

### Artifact Retention
Change the retention period in the "Upload all PDFs" step:
```yaml
retention-days: 30  # Change to desired number of days
```

## Troubleshooting

### Permission Errors
Ensure you've enabled "Read and write permissions" in the repository settings as described in the setup section.

### Compilation Failures
Check the workflow logs for specific LaTeX errors. The workflow continues processing other files even if one fails, and the summary table shows which files failed.

### Missing PDFs
PDFs are only committed to main/master branches on `push` events. On pull requests, they're available as a downloadable artifact.

### Package Not Found Errors
The workflow uses the full TeXLive distribution. If you encounter package errors, the issue is likely in your LaTeX source rather than missing packages.

## Security Notes

- The workflow only runs on your repository's content
- No external LaTeX code is executed
- All compilation happens in isolated Docker containers
- PDFs are committed using GitHub's built-in token with minimal permissions

## Contributing

Feel free to submit issues and enhancement requests. Contributions are welcome!

## License

This workflow configuration is provided as-is. Use it freely in your projects.
