# Building your own

To build your own chain, simply implement the `Vexo\Chain\Chain` interface. It has a single method `run` which accepts an instance of `Vexo\Chain\Context`. The chain can then use the context to get and set values.

```php
<?php

interface Chain
{
    public function run(Context $context): void;
}
```

## Example

In the following example we build a basic chain which takes documents from the context, and splits them into smaller chunks using a `CharacterTextSplitter`.

```php
<?php

use Vexo\Chain\Chain;
use Vexo\Chain\Context;

final class SplitDocumentsChain implements Chain
{
    public function __construct(
        private readonly CharacterTextSplitter $splitter
    ) {
    }

    public function run(Context $context): void
    {
        // Get the documents from the context
        $documents = $context->get('documents');

        // Use the text splitter to split them
        $splitDocuments = $this->splitter->split($documents);

        // Put the split documents back into the context
        $context->put('split_documents', $splitDocuments);
    }
}
```

## Asserting context values

Usually we want to make sure that the context contains the appropriate values before we run a chain.

Vexo comes with a `Vexo\Chain\ContextAssert` class which can be used to assert that the context contains the appropriate values. It is based on `webmozart/assert` and has the same interface. The only difference is that it will raise the appropriate exceptions if the assertions fail.

If in the example above we want to make sure that the context contains a documents value and that it is an instance of the `Documents` class, we can simply assert its type as shown in the highlighted lines below.

```php hl_lines="5 18"
<?php

use Vexo\Chain\Chain;
use Vexo\Chain\Context;
use Vexo\Chain\ContextAssert;

final class SplitDocumentsChain implements Chain
{
    public function __construct(
        private readonly CharacterTextSplitter $splitter
    ) {
    }

    public function run(Context $context): void
    {
        // Get the documents from the context
        $documents = $context->get('documents');
        ContextAssert::isInstanceOf($documents, Documents::class); // Assert our documents

        // Use the text splitter to split them
        $splitDocuments = $this->splitter->split($documents);

        // Put the split documents back into the context
        $context->put('split_documents', $splitDocuments);
    }
}
```

## Customizable input and output keys

Usually we want to offer the flexibility to the user to specify which context values should be used as input and output. A simple way to do this is by accepting input and output maps in the constructor where the user can define which values should be used for which keys.

```php hl_lines="9 10 14 15 23 32"
<?php

use Vexo\Chain\Chain;
use Vexo\Chain\Context;
use Vexo\Chain\ContextAssert;

final class SplitDocumentsChain implements Chain
{
    private const INPUT_DOCUMENTS = 'documents';
    private const OUTPUT_SPLIT_DOCUMENTS = 'split_documents';

    public function __construct(
        private readonly CharacterTextSplitter $splitter,
        private readonly array $inputMap = [],
        private readonly array $outputMap = []
    ) {
    }

    public function run(Context $context): void
    {
        // Get the documents from the context
        $documents = $context->get(
            $this->inputMap[self::INPUT_DOCUMENTS] ?? self::INPUT_DOCUMENTS
        );
        ContextAssert::isInstanceOf($documents, Documents::class); // Assert our documents

        // Use the text splitter to split them
        $splitDocuments = $this->splitter->splitDocuments($documents);

        // Put the split documents back into the context
        $context->put(
            $this->outputMap[self::OUTPUT_SPLIT_DOCUMENTS] ?? self::OUTPUT_SPLIT_DOCUMENTS,
            $splitDocuments
        );
    }
}
```

## Throwing exceptions

If you need to throw an exception from your chain, you can extend the `Vexo\Chain\FailedToRunChain` exception class with your own exception and throw that. Any exceptions thrown should extend that class.
