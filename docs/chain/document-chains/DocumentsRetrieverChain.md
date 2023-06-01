# DocumentsRetrieverChain

The `DocumentsRetrieverChain` takes a query and then uses a document retriever to load documents into the current context.

This will allow you to use any retriever to load documents, and then use the documents in the context in other chains.

## Example

In the example below we use a Vector Store retriever to load documents from a vector store into the current context.

```php
<?php

use Vexo\Chain\Context;
use Vexo\Chain\DocumentsRetrieverChain\DocumentsRetrieverChain;
use Vexo\Document\Retriever\VectorStoreRetriever;

$vectorStore = ...; // An implementation of Vexo\VectorStore\VectorStore

// Create our documents retriever chain
$retrieverChain = new DocumentsRetrieverChain(
    retriever: new VectorStoreRetriever( // Any concrete Vexo\Document\Retriever\Retriever
        vectorStore: $vectorStore
    ),
    maxResults: 3
);

// Call the chain
$context = new Context(['query' => 'Regulation of gene expression']);
$retrieverChain->run($context);

// The documents are now available in the context
$documents = $context->get('documents'); // Instance of Vexo\Document\Documents
```
