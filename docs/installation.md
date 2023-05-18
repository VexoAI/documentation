# Installation

## Requirements

Vexo requires at least PHP 8.1. Some of the components require additional PHP extensions and tooling to function. See the individual component documentation for more info.

## Installation

The recommended way to install Vexo is through [Composer](https://getcomposer.org/).

```bash
composer require vexo/vexo
```

When you want to make use of individual components, you have to install them separately. Please check the individual component documentation for the package name and further installation instructions.

!!! notice

    Currently all of Vexo is contained in a single package. The command above will pull in all the components and their dependencies. In the future this will be split up in multiple component packages so you are able to only pull in what you need.
