# Create your own

You can create your own output parser by implementing the `Vexo\Chain\LanguageModelChain\OutputParser\OutputParser` interface. It has a single `parse` method which takes the output to parse as a string, and returns an array of values which should be inserted into the context.

```php
<?php

interface OutputParser
{
    /**
     * @return array<string, string>
     */
    public function parse(string $text): array;
}
```

## Example

In the example below we create a simple `parse_str` based output parser.

```php
<?php

namespace Acme;

use Vexo\Chain\LanguageModelChain\OutputParser\OutputParser;

final class ParseStrOutputParser implements OutputParser
{
    public function parse(string $text): array
    {
        $values = [];

        parse_str($text, $values);

        return $values;
    }
}

```

Which you can then use as follows:

```php
<?php

use Acme\ParseStrOutputParser;
use Vexo\Chain\LanguageModelChain\LanguageModelChain;

// Create the LanguageModelChain
$chain = new LanguageModelChain(
    languageModel: $languageModel, // Our already initialized language model
    outputParser: new ParseStrOutputParser()
);

// Run the chain!
$context = new Context([
    'prompt' => 'Give me a name and an age as a URL query string. Only output the part
                 after "?". Do not provide further explanation.'
]);
$chain->run($context);

// Given that the model outputs something as follows:
//
// name=JohnDoe&age=42
//
// The following values will be set in the context:
echo $context->get('name') . PHP_EOL;
echo $context->get('age') . PHP_EOL;

// Still outputs the whole LLM response, unparsed:
echo $context->get('generation');
```

## Dealing with parse errors

Language Models are non-deterministic so you cannot rely on their output being consistent 100% of the time. This means you should program your parser defensively, and be prepared for incorrect output.

In general it is best to be as forgiving when you can when parsing the output, however if the language model has made obvious mistakes, it is best to throw an exception with a detailed explanation and have this fed back to the model so it can self-correct.

Throw an instance of `FailedToParseOutput` if that is the case.
