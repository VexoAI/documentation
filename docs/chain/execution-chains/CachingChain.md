# CachingChain

The `CachingChain` wraps another chain to provide it with caching capabilities. It relies on being provided a [PSR-16](https://www.php-fig.org/psr/psr-16/) compatible cache implementation.

It caches specified output context values, and does this by fingerprinting specified input context values. If the input context values are different from previous cache entries, the chain will be executed and the output context values will be cached.

## Requirements

The `CachingChain` requires a PSR-16 compatible cache implementation to be provided.

We recommend using the [Symfony Cache](https://symfony.com/doc/current/components/cache.html) component but you can any any PSR-16 compatible implementation. You can find a list of implementations [here](https://packagist.org/providers/psr/simple-cache-implementation).

```bash
composer require symfony/cache
```

## Example

In the example below we wrap a WebTextChain to cache webpage text extractions using `symfony/cache`.

```php
<?php

use Symfony\Component\Cache\Adapter\ArrayAdapter;
use Symfony\Component\Cache\Psr16Cache;
use Vexo\Chain\CachingChain;
use Vexo\Chain\WebTextChain;

// Create our caching chain
$cachingChain = new CachingChain(
    chain: new WebTextChain(),                 // The chain to wrap
    cache: new Psr16Cache(new ArrayAdapter()), // The cache implementation
    inputValuesToMatch: ['url']                // The input values to fingerprint
    outputValuesToCache: ['text']              // The output values to cache
);

// The first run will execute the chain and cache the output values
$context = new Context(['url' => 'https://example.com/']);
$cachingChain->run($context);
echo $context->get('text') . PHP_EOL; // Outputs the text of the webpage

// The second run will not execute the chain, but return the cached output values
$context = new Context(['url' => 'https://example.com/']);
$cachingChain->run($context);
echo $context->get('text') . PHP_EOL; // Same output, this time returned from cache
```

## Cache lifetime

By default no cache lifetime is specified for the cache entries, which means that whatever default lifetime set on the PSR-16 cache implementation will be used. If you want to specify a lifetime for the cache entries, you can do so by passing the number of seconds the entries should last as the `lifetime` argument to the `CachingChain` constructor.

```php
<?php

// Create our caching chain
$cachingChain = new CachingChain(
    chain: new WebTextChain(),                 // The chain to wrap
    cache: new Psr16Cache(new ArrayAdapter()), // The cache implementation
    inputValuesToMatch: ['url']                // The input values to fingerprint
    outputValuesToCache: ['text']              // The output values to cache,
    lifetime: 3600                             // The cache lifetime in seconds
);
```

## Cache key prefix

By default the cache key prefix is `vexo.chain.cache`. If you want to specify a different cache key prefix, you can do so by passing the prefix as the `cacheKeyPrefix` argument to the `CachingChain` constructor.

```php
<?php

// Create our caching chain
$cachingChain = new CachingChain(
    chain: new WebTextChain(),                 // The chain to wrap
    cache: new Psr16Cache(new ArrayAdapter()), // The cache implementation
    inputValuesToMatch: ['url']                // The input values to fingerprint
    outputValuesToCache: ['text']              // The output values to cache,
    cacheKeyPrefix: 'acme.chain.cache'         // The cache key prefix
);
```
