# des-git-workflows
This repository contains a collection of reusable Git workflows designed to streamline automation processes across multiple projects. By leveraging these workflows, teams can enhance efficiency, maintain consistency, and promote best practices in their development practices.

## Workflows

### JS and CSS Linting
A comprehensive workflow for linting JavaScript and CSS files to ensure code quality and adherence to coding standards. This workflow provides:

- 🎨 **CSS Linting** using your project's `lint:css` script
- ⚡ **JavaScript Linting** using your project's `lint:js` script
- 📦 **Automatic dependency installation** including `@wordpress/scripts`
- 🔍 **Multi-directory support** for monorepos and complex project structures
- 🚀 **npm caching** for faster builds
- ✅ **Graceful handling** when linting scripts are not present

#### Requirements
Your repository should have a `package.json` file with the following scripts:
```json
{
  "scripts": {
    "lint:css": "your-css-linting-command",
    "lint:js": "your-js-linting-command"
  }
}
```

#### Usage
The workflow can be called from another workflow file or triggered manually. It runs on Ubuntu latest and defaults to Node.js version 20, but can be customized.

#### Basic Usage
```yaml
name: Code Quality
on: [push, pull_request]

jobs:
  lint:
    uses: bcgov/des-git-workflows/.github/workflows/js-css-lint.yml@main
```

#### Custom Node.js Version
```yaml
name: Code Quality
on: [push, pull_request]

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

#### Requirements
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

#### Usage
The workflow can be called from another workflow file. It runs on Ubuntu latest with the default PHP version.

#### Basic Usage
```yaml
name: PHP Code Quality
on: [push, pull_request]

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

#### Usage
The workflow can be called from another workflow file. It runs on Ubuntu latest with the default PHP version and is specifically designed for WordPress projects with block patterns.

#### Basic Usage
```yaml
name: WordPress Pattern Validation
on: [push, pull_request]

jobs:
  pattern-validation:
    uses: bcgov/des-git-workflows/.github/workflows/pattern-validate.yml@main
```

#### Full WordPress Project Validation
```yaml
name: Complete WordPress Code Quality
on: [push, pull_request]

jobs:
  php-validation:
    uses: bcgov/des-git-workflows/.github/workflows/phpcs-validate.yml@main

  pattern-validation:
    uses: bcgov/des-git-workflows/.github/workflows/pattern-validate.yml@main

  js-css-linting:
    uses: bcgov/des-git-workflows/.github/workflows/js-css-lint.yml@main
    with:
      node_version: '22' # Custom Node.js version