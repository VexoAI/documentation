# StrReplaceRenderer

The `StrReplaceRenderer` is a basic prompt renderer around PHP's `str_replace` function. In essence it does little more than replace any occurrence of `{{name}}` with the context value of the referenced name.

This is the best option if your prompt templates are fairly basic and only require basic string interpolation.

```php
<?php

use Vexo\Chain\LanguageModelChain\LanguageModelChain;
use Vexo\Chain\LanguageModelChain\Prompt\StrReplaceRenderer;

// Create the LanguageModelChain
$chain = new LanguageModelChain(
    languageModel: $languageModel, // Our already initialized language model
    promptRenderer: new StrReplaceRenderer('What is the capital of {{country}}?')
);

// Run the chain!
$context = new Context(['country' => 'France']);
$chain->run($context);

// Outputs something like: The capital of France is Paris
echo $context->get('generation');
```

## Missing values

If the template references a value which isn't available in the context, a `FailedToGetContextValue` exception will be thrown.
