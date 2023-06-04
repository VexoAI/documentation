# Introduction

Text splitters allow you to split documents into smaller pieces of text. This is especially useful when you are tied to size limits of a vector store or language model in processing documents.

All text splitters implement the `Vexo\Document\TextSplitter` interface, which has two methods, `split` and `splitDocuments`. The `split` method will split a string, into an array of smaller strings, while the `splitDocuments` method will split a collection of documents into smaller documents.

The `splitDocuments` method uses the `split` method under the hood, passing it the contents of each document in the collection, and constructing a new document for each split piece of text. It will maintain metadata from the original document with the new documents.

```php
<?php

use Vexo\Document\Documents;

interface TextSplitter
{
    public function splitDocuments(Documents $document): Documents;

    /**
     * @return array<string>
     */
    public function split(string $text): array;
}
```
