# WebTextChain

The `WebTextChain` takes a URL and loads a plain text version of the page into the current context. By default the chain will load the first 8k of plain text from the page, but this can be configured by passing the `maxTextLength` argument to the constructor.

The chain will strip all HTML and tags which are not part of the page content, such as `<script>` and `<style>` tags.

## Requirements

This chain relies on `php-http/discovery` to find a [PSR-18](https://www.php-fig.org/psr/psr-18/) compatible HTTP client. You can find a list of possible implementations [here](https://packagist.org/providers/psr/http-client-implementation).


## Example

```php
<?php

use Vexo\Chain\Context;
use Vexo\Chain\WebTextChain\WebTextChain;

// Set up our chain
$webTextChain = new WebTextChain(
    maxTextLength: 4000 // The maximum length of the text to extract; defaults to 8k
);

// Call the chain
$context = new Context('url' => 'https://www.example.com');
$webTextChain->run($context);

// The plain text is now available in the context
echo $context->get('text'); // Outputs the plain text of the page
```

## Overriding the default HTTP client

If you don't want to rely on `php-http/discovery` to select an HTTP client, you can pass a PSR-18 compatible HTTP client and/or a [PSR-17](https://www.php-fig.org/psr/psr-17/) compatible request factory to the `WebTextChain` constructor.

```php
<?php

use Vexo\Chain\Context;
use Vexo\Chain\WebTextChain\WebTextChain;

// Set up our chain
$webTextChain = new WebTextChain(
    httpClient: $httpClient, // Any PSR-18 compatible HTTP client
    requestFactory: $requestFactory // Any PSR-17 compatible request factory
);
```
