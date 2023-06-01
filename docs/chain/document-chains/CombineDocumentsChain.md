# CombineDocumentsChain

The `CombineDocumentsChain` takes multiple documents and combines their contents into a single piece of text.

## Example

```php
<?php

use Vexo\Chain\Context;
use Vexo\Chain\CombineDocumentsChain\CombineDocumentsChain;
use Vexo\Document\Document;
use Vexo\Document\Documents;

// Set up our chain
$combineDocumentsChain = new CombineDocumentsChain();

// Create a context with some documents
$context = new Context([
    'documents' => new Documents([
        new Document('Roses are red'),
        new Document('Violets are blue')
    ])
]);

// Call the chain
$combineDocumentsChain->run($context);

// The combined contents are now available in the context
//
// Outputs:
//
// Roses are red
//
// Violets are blue
//
echo $context->get('combined_contents');
```
