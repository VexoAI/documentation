# Introduction

The Document component provides various ways to load, split, and transform text documents for usage with chains, vector stores, and large language models.

The basis of the component is the `Vexo\Document\Document` class, which represents a single document. It has two methods, `contents` and `metadata`, which return the document's contents and metadata respectively.

Documents can contain any sort of contents and any relevant metadata. It is up to you to decide what metadata is relevant to their use case.

There is also the `Vexo\Document\Documents` class which represents an iterable collection of documents.

## Creating documents

Generally documents are created by using loaders, but you can also instantiate them directly.

```php
<?php

use Vexo\Contract\Metadata;
use Vexo\Document\Document;
use Vexo\Document\Documents;

// Create our document
$document = new Document(
    contents: 'This is the document contents.',
    metadata: new Metadata([
        'id' => 1234,
        'title' => 'Document title',
        'something_else' => 'Some other metadata'
    ])
);

// Put it in a collection
$documents = new Documents([$document]);

// Iterate over our collection
foreach ($documents as $document) {
    echo $document->contents();
    echo $document->metadata()->get('title');
}
```
