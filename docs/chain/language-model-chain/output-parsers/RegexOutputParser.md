# RegexOutputParser

The `RegexOutputParser` allows you to use regular expressions to parse language model output. It will automatically make any named capture groups available as values in the context after parsing.

```php
<?php

use Vexo\Chain\LanguageModelChain\LanguageModelChain;
use Vexo\Chain\LanguageModelChain\OutputParser\RegexOutputParser;

// Create the LanguageModelChain
$chain = new LanguageModelChain(
    languageModel: $languageModel, // Our already initialized language model
    outputParser: new RegexOutputParser('/Action: (?P<action>.*?)\nAction input: (?P<input>.*)/')
);

// Run the chain!
$context = new Context(['prompt' => 'Please decide on an action and an input']);
$chain->run($context);

// Given that the model outputs something as follows:
//
// Action: my action
// Action input: input to the action
//
// The following values will be set in the context:
echo $context->get('action') . PHP_EOL;
echo $context->get('input') . PHP_EOL;

// Still outputs the whole LLM response, unparsed:
echo $context->get('generation');
```

## Parse errors

If the provided regular expression is invalid, or if the parser failed to find any matches, an instance of the `FailedToParseOutput` exception will be thrown.
