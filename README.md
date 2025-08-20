# des-git-workflows

This repository contains a collection of reusable GitHub Actions workflows designed to streamline automation processes across multiple projects. By leveraging these workflows, teams can enhance efficiency, maintain consistency, and promote best practices in their development workflows.

## Table of Contents

- [Quick Start](#quick-start)
- [Available Workflows](#available-workflows)
- [Workflows](#workflows)
  - [JS and CSS Linting](#js-and-css-linting)
  - [PHPCS Validation](#phpcs-validation)
  - [WordPress Pattern Validation](#wordpress-pattern-validation)
  - [General Linting](#general-linting)
- [Versioning](#versioning)
- [Contributing](#contributing)
- [License](#license)
- [Troubleshooting](#troubleshooting)

## Quick Start

1. **Choose a workflow** from the available options below
2. **Create a `.github/workflows/` directory** in your project if it doesn't exist
3. **Add a workflow file** (e.g., `quality-check.yml`) with the appropriate configuration
4. **Commit and push** - the workflow will run automatically on your configured triggers

## Available Workflows

| Workflow | Purpose | Required Files | Language/Framework |
|----------|---------|----------------|-------------------|
| [js-css-lint.yml](#js-and-css-linting) | JavaScript and CSS linting | `package.json` with lint scripts | JavaScript/CSS |
| [phpcs-validate.yml](#phpcs-validation) | PHP code quality validation | `composer.json` with PHPCS script | PHP |
| [pattern-validate.yml](#wordpress-pattern-validation) | WordPress pattern validation | `composer.json` + `patterns/` dir | WordPress/PHP |
| [linting.yml](#general-linting) | General purpose linting | `package.json` and/or `composer.json` | Multi-language |

## Workflows

### JS and CSS Linting

A comprehensive workflow for linting JavaScript and CSS files to ensure code quality and adherence to coding standards. This workflow provides:

- 🎨 **CSS Linting** using your project's `lint:css` script
- ⚡ **JavaScript Linting** using your project's `lint:js` script
- 📦 **Automatic dependency installation** including `@wordpress/scripts`
- 🔍 **Multi-directory support** for monorepos and complex project structures
- 🚀 **npm caching** for faster builds
- ✅ **Graceful handling** when linting scripts are not present

#### JS and CSS Linting Requirements

Your repository should have a `package.json` file with the following scripts:

```json
{
  "scripts": {
    "lint:css": "your-css-linting-command",
    "lint:js": "your-js-linting-command"
  }
}
```

#### Usage (PHPCS Validation)

The workflow can be called from another workflow file or triggered manually. It runs on Ubuntu latest and defaults to Node.js version 20, but can be customized.

#### JS and CSS Linting Usage

```yaml
name: Code Quality
on: [push, pull_request, workflow_dispatch]

jobs:
  lint:
    uses: bcgov/des-git-workflows/.github/workflows/js-css-lint.yml@main
```

#### Custom Node.js Version

```yaml
name: Code Quality
on: [push, pull_request, workflow_dispatch]

jobs:
  lint:
    uses: bcgov/des-git-workflows/.github/workflows/js-css-lint.yml@main
    with:
      node_version: '18'
```

#### Available Inputs

- `node_version` (optional): Node.js version to use (defaults to '20')

#### Behavior

- ✅ **Safe execution**: Workflow gracefully skips steps when `package.json` or required scripts are missing
- 📊 **Clear feedback**: Visual indicators and detailed status messages throughout execution
- 🔄 **Automatic dependency management**: Installs all dependencies and `@wordpress/scripts`
- 📁 **Subdirectory support**: Automatically finds and installs dependencies in subdirectories

### PHPCS Validation

A comprehensive workflow for validating PHP code quality using PHP CodeSniffer (PHPCS) and Composer validation. This workflow provides:

- 🧹 **PHP CodeSniffer** validation using your project's `phpcs-allow-todo` Composer script
- 🔍 **Composer configuration validation** to ensure proper project setup
- 📦 **Automatic Composer dependency installation**

#### Pattern Validation Requirements

Your repository must have:

1. A `composer.json` file with proper PHP project configuration
2. A Composer script named `phpcs-allow-todo` for running PHPCS

Example `composer.json` scripts section:

```json
{
  "scripts": {
    "phpcs-allow-todo": "phpcs --standard=your-standard --ignore=vendor/ ."
  }
}
```

#### PHPCS Validation Usage

The workflow can be called from another workflow file. It runs on Ubuntu latest with the default PHP version.

#### PHPCS Validation Basic Usage

```yaml
name: PHP Code Quality
on: [push, pull_request, workflow_dispatch]

jobs:
  phpcs:
    uses: bcgov/des-git-workflows/.github/workflows/phpcs-validate.yml@main
```

### WordPress Pattern Validation

A specialized workflow for validating WordPress block patterns in projects that include custom pattern development. This workflow provides:

- 🔍 **WordPress pattern validation** using your project's `scan-wp-patterns` Composer script
- 📁 **Pattern directory detection** to ensure patterns exist before validation
- 🔍 **Composer configuration validation** to ensure proper project setup
- 📦 **Automatic Composer dependency installation**

#### Requirements

Your repository must have:

1. A `composer.json` file with proper PHP project configuration
2. A `patterns/` directory containing WordPress block patterns
3. A Composer script named `scan-wp-patterns` for pattern validation

Example `composer.json` scripts section:

```json
{
  "scripts": {
    "scan-wp-patterns": "your-pattern-validation-command"
  }
}
```

#### Pattern Validation Usage

The workflow can be called from another workflow file. It runs on Ubuntu latest with the default PHP version and is specifically designed for WordPress projects with block patterns.

#### Pattern Validation Example

```yaml
name: WordPress Pattern Validation
on: [push, pull_request, workflow_dispatch]

jobs:
  pattern-validation:
    uses: bcgov/des-git-workflows/.github/workflows/pattern-validate.yml@main
```

#### Full WordPress Project Validation

```yaml
name: Complete WordPress Code Quality
on: [push, pull_request, workflow_dispatch]

jobs:
  php-validation:
    uses: bcgov/des-git-workflows/.github/workflows/phpcs-validate.yml@main

  pattern-validation:
    uses: bcgov/des-git-workflows/.github/workflows/pattern-validate.yml@main

  js-css-linting:
    uses: bcgov/des-git-workflows/.github/workflows/js-css-lint.yml@main
    with:
      node_version: '22' # Custom Node.js version
```

### General Linting

A comprehensive multi-language workflow that automatically detects and lints both JavaScript/CSS and PHP projects. This workflow provides:

- 🔄 **Multi-language support** - automatically detects JavaScript/CSS and PHP projects
- 📦 **Automatic dependency management** for both npm and Composer projects
- 🎨 **CSS and JavaScript linting** using your project's lint scripts
- 🧹 **PHP CodeSniffer** support with automatic detection
- 📁 **Subdirectory support** for monorepos and complex project structures
- ✅ **Graceful handling** when dependencies or scripts are not present

#### General Linting Requirements

Your repository can have either or both:

1. **For JavaScript/CSS**: `package.json` with lint scripts
2. **For PHP**: `composer.json` with appropriate configuration

#### Usage

The workflow can be called from another workflow file or triggered manually. It runs on Ubuntu latest with Node.js 20 and automatically detects project types.

#### Basic Usage

```yaml
name: General Code Quality
on: [push, pull_request, workflow_dispatch]

jobs:
  lint:
    uses: bcgov/des-git-workflows/.github/workflows/linting.yml@main
```

## Versioning

Use specific version tags or commits instead of `@main` for workflows to ensure stability:

```yaml
# Use a specific release tag (recommended for production)
uses: bcgov/des-git-workflows/.github/workflows/js-css-lint.yml@v1.0.0

# Use a specific commit (most stable)
uses: bcgov/des-git-workflows/.github/workflows/js-css-lint.yml@abc123def

# Use main branch (latest features, but may have breaking changes)
uses: bcgov/des-git-workflows/.github/workflows/js-css-lint.yml@main
```

## Contributing

We welcome contributions to improve these workflows! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

For major changes, please open an issue first to discuss the proposed changes.

## License

This project is licensed under the Apache License 2.0. See the [LICENSE](LICENSE) file for details.

## Troubleshooting

### Common Issues

#### "Script not found" errors

- **Cause**: Your `package.json` or `composer.json` doesn't have the required scripts
- **Solution**: Add the missing scripts or use a different workflow that matches your project setup

#### Workflow fails on dependency installation

- **Cause**: Missing or invalid `package.json` or `composer.json` files
- **Solution**: Ensure your project has valid dependency files with proper syntax

#### Node.js version compatibility issues

- **Cause**: Your project requires a specific Node.js version
- **Solution**: Use the `node_version` input parameter to specify the required version

### Getting Help

- 📖 **Documentation**: Check this README for detailed usage instructions
- 🐛 **Bug Reports**: Open an issue in this repository
- 💡 **Feature Requests**: Open an issue with the "enhancement" label
- 🤝 **Discussions**: Use GitHub Discussions for questions and community support
