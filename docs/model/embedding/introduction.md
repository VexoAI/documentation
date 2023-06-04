# Introduction

Embedding models allow you to embed text into a vector space. We currently support OpenAI's embedding API.

All embedding models implement the `Vexo\Model\Embedding\Model` interface. It has two methods, `embedQuery` and `embedTexts` to embed a query and a list of texts respectively. They return either an instance of `Vexo\Contract\Vector` or `Vexo\Contract\Vectors`.

There are two separate methods for embedding queries and texts, as some implementations have different approaches to each.

```php
<?php

use Vexo\Contract\Vector;
use Vexo\Contract\Vectors;

interface Model
{
    public function embedQuery(string $query): Vector;

    /**
     * @param array<string> $texts
     */
    public function embedTexts(array $texts): Vectors;
}
```
