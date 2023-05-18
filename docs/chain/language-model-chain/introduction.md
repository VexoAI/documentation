# Introduction

The `LanguageModelChain` is at the heart of Vexo. In its most basic form it has a single dependency, an instance of `Vexo\Model\Language\LanguageModel`.

## Example

```php
<?php

use Vexo\Model\Language\OpenAIChatModel;
use Vexo\Chain\Context;
use Vexo\Chain\LanguageModelChain\LanguageModelChain;

// Initialize our OpenAI client
$chat = \OpenAI::client(getenv('OPENAI_API_KEY'));

// Initialize our language model
$languageModel = new OpenAIChatModel($openAI->chat());

// Create the LanguageModelChain
$chain = new LanguageModelChain($languageModel);

// Run the chain!
$context = new Context(['prompt' => 'What is the capital of France?']);
$chain->run($context);

// Outputs something like: The capital of France is Paris
echo $context->get('generation');

```

## Prompt rendering

The example above is not terribly useful, as it relies on a static prompt being available in the context. Vexo comes a few different prompt renderers that will allow you to render prompts dynamically based on context values.

In the example below we use the basic `StrReplaceRenderer` to render our prompt.

```php
<?php

use Vexo\Chain\LanguageModelChain\LanguageModelChain;
use Vexo\Chain\LanguageModelChain\Prompt\StrReplaceRenderer;

// Create the LanguageModelChain
$chain = new LanguageModelChain(
    languageModel: $languageModel, // As before
    promptRenderer: new StrReplaceRenderer('What is the capital of {{country}}?')
);

// Run the chain!
$context = new Context(['country' => 'France']);
$chain->run($context);

// Outputs something like: The capital of France is Paris
echo $context->get('generation');
```

## Output parsing

Sometimes the language model produces output which we need to interpret and structure, as opposed to simply using the whole generation as plain text. In that case you can supply an `OutputParser` to parse the output.

In the example below we add a `RegexOutputParser` to interpret the output.

```php
<?php

use Vexo\Chain\LanguageModelChain\LanguageModelChain;
use Vexo\Chain\LanguageModelChain\OutputParser\RegexOutputParser;
use Vexo\Chain\LanguageModelChain\Prompt\StrReplaceRenderer;

// Create the LanguageModelChain
$chain = new LanguageModelChain(
    languageModel: $languageModel, // As before
    promptRenderer: new StrReplaceRenderer('What is the capital of {{country}}?'),
    outputParser: new RegexOutputParser('/^The capital of .* is (?P<capital>.*)!$/i')
);

// Run the chain!
$context = new Context(['country' => 'France']);
$chain->run($context);

// Outputs something like: Paris
echo $context->get('capital');
```

## Stop tokens

Sometimes language models will produce more output than we need, adding additional explanation beyond its provided instructions. In that case we can provide stop tokens. Stop tokens are words which will trigger the model to stop generating and immediately return its response.

These will be directly provided to the underlying model when making the call to generate the response.

```php
<?php

use Vexo\Chain\LanguageModelChain\LanguageModelChain;

// Create the LanguageModelChain
$chain = new LanguageModelChain(
    languageModel: $languageModel, // As before
    stops: ['Observation:']
);
```

## Remapping context values

In the example above we are relying on the default context values. Sometimes these values don't line up or you simply want to rename these. You can control what these values are called by providing the `inputMap` and `outputMap` variables.

```php
<?php

use Vexo\Chain\LanguageModelChain\LanguageModelChain;

// Create the LanguageModelChain
$chain = new LanguageModelChain(
    languageModel: $languageModel, // As before
    inputMap: ['prompt' => 'question'],
    outputMap: ['generation' => 'answer']
);

// Run the chain!
$context = new Context(['question' => 'What is the capital of France?']);
$chain->run($context);

// Outputs something like: The capital of France is Paris
echo $context->get('answer');
```

You can do the same for any context values which are needed by the prompt renderer or are produced by the output parser.


```php
<?php

use Vexo\Chain\LanguageModelChain\LanguageModelChain;
use Vexo\Chain\LanguageModelChain\OutputParser\RegexOutputParser;
use Vexo\Chain\LanguageModelChain\Prompt\StrReplaceRenderer;

// Create the LanguageModelChain
$chain = new LanguageModelChain(
    languageModel: $languageModel, // As before
    promptRenderer: new StrReplaceRenderer('What is the capital of {{country}}?'),
    outputParser: new RegexOutputParser('/^The capital of .* is (?P<capital>.*)!$/i'),
    inputMap: ['country' => 'nation'],
    outputMap: ['capital' => 'city']
);

// Run the chain!
$context = new Context(['nation' => 'France']);
$chain->run($context);

// Outputs something like: Paris
echo $context->get('city');
```
