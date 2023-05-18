# Contributing

Contributions are very much appreciated! There are several ways you can help with the project.

## Coding Guidelines

* Vexo is meant to be lean and focussed and therefore not all feature requests will be accepted.
* Compatibility breaks or major changes should first be proposed in a [GitHub issue](https://github.com/VexoAI/vexo/issues) outlining why the change is needed, what the benefits are, and what alternatives have been considered.
* Vexo has minimum PHP version requirement of PHP 8.1. Pull requests must not require a higher version.
* All code contributions must be accompanied by unit tests to ensure the change works as expected.
* All code contributions should pass coding style requirements enforced by [PHPStan](https://github.com/VexoAI/vexo/blob/master/phpstan.neon.dist) and [PHP-CS-Fixer](https://github.com/VexoAI/vexo/blob/master/.php-cs-fixer.dist.php).

## Running the tests

1. Clone the repository:
```bash
git clone git@github.com:VexoAI/vexo.git && cd vexo
```

2. Install dependencies using [Composer](https://getcomposer.org/):
```bash
composer install
```

3. Run the tests
```bash
./run-tests.sh
```

## Contribution documentation

Vexo's documentation is maintained in a seperate repository. If you notice any mistakes or would like to expand our documentation with more information, feel free to [create an issue there](https://github.com/VexoAI/documentation/issues) or submit a pull request.

## Where can I go for help?

If you need help, feel free to [create an issue](https://github.com/VexoAI/vexo/issues) with your question and we'll help you as best we can.

Thank you for considering contributing to the project!
