# Introduction

The document loader allows you load new documents from a variety of sources for further processing. Generally there are two types of loaders, those that are based around a source, and those that are based around a format. These can be combined to load a wide variety of documents.

All loaders implement the `Vexo\Document\Loader` interface, which has a single method, `load` which will return a `Vexo\Document\Documents` instance containing all the loaded documents.

```php
<?php

use Vexo\Document\Documents;

interface Loader
{
    public function load(): Documents;
}
```
