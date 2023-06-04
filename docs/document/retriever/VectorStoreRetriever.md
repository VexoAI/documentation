# VectorStoreRetriever

The `VectorStoreRetriever` is a retriever that uses a `Vexo\VectorStore\VectorStore` to retrieve documents.

## Example

```php
<?php

use Vexo\Document\Retriever\VectorStoreRetriever;

// Create the retriever
$retriever = new VectorStoreRetriever(
    vectorStore: $vectorStore // Your `Vexo\VectorStore\VectorStore` instance
);

// Load the documents
$documents = $retriever->retrieve(
    query: 'How do I make a chocolate cake?',
    maxResults: 10
);
```
