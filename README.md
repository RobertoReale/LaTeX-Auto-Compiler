# LaTeX Auto-Compiler

A GitHub Actions workflow that automatically finds, compiles, and commits all LaTeX documents in your repository whenever you push changes.

## Features

- **Automatic Discovery**: Finds all `.tex` files in your repository automatically
- **Parallel Compilation**: Compiles multiple LaTeX documents simultaneously using a matrix strategy
- **Full TeXLive Environment**: Uses the complete TeXLive Docker container for maximum compatibility
- **Auto-Commit**: Automatically commits compiled PDFs back to the repository on main/master branches
- **Artifact Storage**: Uploads PDFs as GitHub artifacts with 30-day retention
- **Comprehensive Reporting**: Provides detailed summaries of compilation results
- **Error Handling**: Continues processing other files even if one fails to compile

## Quick Setup

### 1. Copy the Workflow File

Create the directory `.github/workflows/` in your repository and copy the `latex.yml` file into it.

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

### Compilation Process

1. **Discovery**: Scans the entire repository for `.tex` files
2. **Matrix Build**: Creates a parallel compilation job for each LaTeX document found
3. **Compilation**: Runs `pdflatex` twice on each document (for proper reference resolution)
4. **Artifact Upload**: Stores compiled PDFs as downloadable artifacts
5. **Auto-Commit**: On main/master branches, commits PDFs back to their original locations
6. **Summary**: Generates a detailed report of the compilation process

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

## Usage Examples

### Basic Usage
1. Add a `.tex` file to your repository
2. Push to main/master branch
3. The workflow automatically compiles it and commits the PDF

### Multiple Documents
The workflow handles any number of LaTeX documents across any directory structure. Each document is compiled independently.

### LaTeX Packages
The workflow uses the full TeXLive distribution, so most LaTeX packages are available. If you need specific packages, they should work out of the box.

## Workflow Jobs

### 1. Find TeX Files
- Discovers all `.tex` files in the repository
- Creates a matrix for parallel processing

### 2. Compile LaTeX
- Runs in parallel for each discovered file
- Uses TeXLive container for compilation
- Runs pdflatex twice for proper references
- Uploads successful compilations as artifacts

### 3. Commit PDFs
- Downloads all compiled PDFs
- Places them next to their source `.tex` files
- Commits changes to the repository (main/master only)

### 4. Summary
- Provides a detailed report of compilation results
- Shows which files were processed successfully

## Customization

### Changing Target Branches
Edit the `branches` section in the workflow:
```yaml
on:
  push:
    branches: [ main, master, your-branch ]
```

### Adjusting Compilation Options
Modify the pdflatex command in the "Compile LaTeX document" step:
```bash
pdflatex -interaction=nonstopmode -halt-on-error "$tex_file"
```

### Artifact Retention
Change the retention period in the "Upload PDF artifact" step:
```yaml
retention-days: 30  # Change to desired number of days
```

## Troubleshooting

### Permission Errors
Ensure you've enabled "Read and write permissions" in the repository settings as described in the setup section.

### Compilation Failures
Check the workflow logs for specific LaTeX errors. The workflow continues processing other files even if one fails.

### Missing PDFs
PDFs are only committed to main/master branches. On other branches, they're available as downloadable artifacts.

### Package Not Found Errors
The workflow uses the full TeXLive distribution. If you encounter package errors, the issue is likely in your LaTeX code rather than missing packages.

## Security Notes

- The workflow only runs on your repository's content
- No external LaTeX code is executed
- All compilation happens in isolated Docker containers
- PDFs are committed using GitHub's built-in token with minimal permissions

## Contributing

Feel free to submit issues and enhancement requests. Contributions are welcome!

## License

This workflow configuration is provided as-is. Use it freely in your projects.
