# Introduction

Retrievers provide a simple way to retrieve documents based on a query. They are used by chains and agent tools to easily retrieve documents.

```php
<?php

use Vexo\Document\Documents;

interface Retriever
{
    public function retrieve(string $query, int $maxResults = 4): Documents;
}
```
