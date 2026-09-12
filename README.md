# archiviart Documentation

This project contains the documentation site for the archiviart ecosystem. It is built with MkDocs and the Material for MkDocs theme and is intended to serve as the central place for project documentation and developer-facing guides.

## Overview

The documentation site is configured in `mkdocs.yml` and uses the Material theme for a clean, modern documentation experience. The project currently includes the required dependency in `pyproject.toml` and is designed to be run locally for preview and iteration.

## Prerequisites

Before starting, make sure you have:

- Python 3.11+
- pip
- A terminal with access to the project folder

## Quick Start

### 1. Create a virtual environment

```bash
python3 -m venv .venv
```

### 2. Activate the environment

On macOS and Linux:

```bash
source .venv/bin/activate
```

On Windows:

```bash
.venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install .
```

This installs the project and its dependencies declared in `pyproject.toml`, including `mkdocs-material`.

### 4. Run the docs locally

```bash
mkdocs serve
```

Then open the local site in your browser:

```text
http://127.0.0.1:8000
```

The live preview will reload automatically when you update the Markdown files.

## Project Structure

```text
archiviart-doc/
├── .venv/              # Local Python virtual environment
├── .vscode/            # Editor settings
├── mkdocs.yml          # MkDocs configuration and site metadata
├── pyproject.toml      # Python project metadata and dependencies
├── README.md           # Project documentation
└── ...                 # Documentation pages and assets
```

## Configuration

Most of the site configuration lives in `mkdocs.yml`, including:

- site metadata
- theme selection
- color palette
- social links
- footer copyright

If you want to update the branding, navigation, or general site options, start there.

## Common Workflow

1. Add or edit Markdown files for the documentation.
2. Update the MkDocs configuration if you add new pages or navigation sections.
3. Run `mkdocs serve` to preview the changes locally.
4. Commit and push the documentation updates when ready.

## Troubleshooting

If the command `mkdocs` is not found, make sure the virtual environment is activated and the dependencies were installed successfully:

```bash
pip install .
mkdocs serve
```

If you need to rebuild the environment from scratch:

```bash
rm -rf .venv
python3 -m venv .venv
source .venv/bin/activate
pip install .
```

## Notes

This project is intentionally lightweight and focused on documentation delivery. It is a good fit for internal docs, project architecture notes, and onboarding material.
