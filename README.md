# DES GIT WORKFLOWS

This repository contains a collection of reusable GitHub Actions workflows designed to streamline automation processes across multiple projects. By leveraging these workflows, teams can enhance efficiency, maintain consistency, and promote best practices in their development workflows.

## Table of Contents

- [Quick Start](#quick-start)
- [Available Workflows](#available-workflows)
- [Workflows](#workflows)
  - [JS and CSS Linting](#js-and-css-linting)
  - [PHPCS Validation](#phpcs-validation)
  - [WordPress Pattern Validation](#wordpress-pattern-validation)
  - [General Linting](#general-linting)
  - [WordPress PHP Testing](#wordpress-php-testing)
  - [JavaScript Testing](#javascript-testing)
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
| [wordpress-php-tests.yml](#wordpress-php-testing) | WordPress PHP unit testing | `composer.json` with test setup | WordPress/PHP |
| [js-tests.yml](#javascript-testing) | JavaScript unit testing | `package.json` with test script | JavaScript |

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
    uses: bcgov/des-git-workflows/.github/workflows/js-css-lint.yml@1.0.0
```

#### Custom Node.js Version

```yaml
name: Code Quality
on: [push, pull_request, workflow_dispatch]

jobs:
  lint:
    uses: bcgov/des-git-workflows/.github/workflows/js-css-lint.yml@1.0.0
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
    uses: bcgov/des-git-workflows/.github/workflows/phpcs-validate.yml@1.0.0
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
    uses: bcgov/des-git-workflows/.github/workflows/pattern-validate.yml@1.0.0
```

#### Full WordPress Project Validation (Including tests)

```yaml
name: Complete WordPress Code Quality
on: [push, pull_request, workflow_dispatch]

jobs:
  php-validation:
    uses: bcgov/des-git-workflows/.github/workflows/phpcs-validate.yml@1.0.0

  pattern-validation:
    uses: bcgov/des-git-workflows/.github/workflows/pattern-validate.yml@1.0.0

  js-css-linting:
    uses: bcgov/des-git-workflows/.github/workflows/js-css-lint.yml@1.0.0
    with:
      node_version: '22' # Custom Node.js version

  php-tests:
    uses: bcgov/des-git-workflows/.github/workflows/wordpress-php-tests.yml@1.0.0

  js-tests:
    uses: bcgov/des-git-workflows/.github/workflows/js-tests.yml@1.0.0
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
    uses: bcgov/des-git-workflows/.github/workflows/linting.yml@1.0.0
```

### WordPress PHP Testing

A comprehensive workflow for running PHP unit tests in WordPress environments using PHPUnit. This workflow provides:

- 🧪 **PHPUnit Testing** with WordPress test suite integration
- 🗄️ **MySQL Database** service for database-dependent tests
- 🔧 **WordPress Test Environment** setup with SVN-based WordPress core and test libraries
- 📦 **Automatic Composer dependency installation**
- ⚙️ **PHP 7.4** runtime environment optimized for WordPress compatibility
- 📊 **TestDox output format** for readable test results

#### WordPress PHP Testing Requirements

Your repository must have:

1. A `composer.json` file with proper PHP project configuration
2. A Composer script named `test-setup` for WordPress test environment setup
3. PHPUnit configuration compatible with WordPress testing

Example `composer.json` scripts section:

```json
{
  "scripts": {
    "test-setup": "install-wp-tests.sh wordpress_test root '' localhost latest"
  }
}
```

#### WordPress PHP Testing Usage

The workflow can be called from another workflow file. It runs on Ubuntu latest with PHP 7.4 and includes a MySQL 8 service.

#### WordPress PHP Testing Basic Usage

```yaml
name: WordPress PHP Testing
on: [push, pull_request, workflow_dispatch]

jobs:
  test-php:
    uses: bcgov/des-git-workflows/.github/workflows/wordpress-php-tests.yml@1.0.0
```

#### WordPress PHP Testing Behavior

- 🗄️ **MySQL Service**: Provides MySQL 8 database with health checks
- 📁 **WordPress Environment**: Sets up WordPress core in `/tmp/WordPress` with test libraries
- 🧪 **PHPUnit Configuration**: Uses `vendor/bcgov/wordpress-utils/phpunit.xml.dist` configuration
- 📊 **Test Output**: Provides TestDox format for human-readable test results

### JavaScript Testing

A streamlined workflow for running JavaScript unit tests using npm. This workflow provides:

- 🧪 **JavaScript Testing** using your project's `test` npm script
- 📦 **Automatic npm dependency installation** with caching
- ⚡ **Node.js 20** runtime environment
- 🚀 **npm caching** for faster builds

#### JavaScript Testing Requirements

Your repository must have:

1. A `package.json` file with proper Node.js project configuration
2. An npm script named `test` for running tests

Example `package.json` scripts section:

```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  }
}
```

#### JavaScript Testing Usage

The workflow can be called from another workflow file. It runs on Ubuntu latest with Node.js 20.

#### JavaScript Testing Basic Usage

```yaml
name: JavaScript Testing
on: [push, pull_request, workflow_dispatch]

jobs:
  test-js:
    uses: bcgov/des-git-workflows/.github/workflows/js-tests.yml@1.0.0
```

#### Combined Testing Example

```yaml
name: Complete Test Suite
on: [push, pull_request, workflow_dispatch]

jobs:
  php-tests:
    uses: bcgov/des-git-workflows/.github/workflows/wordpress-php-tests.yml@1.0.0

  js-tests:
    uses: bcgov/des-git-workflows/.github/workflows/js-tests.yml@1.0.0

  code-quality:
    uses: bcgov/des-git-workflows/.github/workflows/linting.yml@1.0.0
```

## Versioning

Use specific version tags or commits (like `@1.0.0`) instead of `@main` for workflows to ensure stability:

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
