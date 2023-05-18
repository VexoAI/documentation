# JsonOutputParser

The `JsonOutputParser` allows you to parse output according to a specified [JSON Schema](https://json-schema.org/). The first level properties of the resulting object will be made available as context values.

## Requirements

The `JsonOutputParser` makes use of the `justinrainbow/json-schema` library to validate the JSON structure against a JSON schema. It must be available in your project.

```
composer require justinrainbow/json-schema
```

## Example

```php
<?php

use Vexo\Chain\LanguageModelChain\LanguageModelChain;
use Vexo\Chain\LanguageModelChain\OutputParser\JsonOutputParser;
use Vexo\Chain\LanguageModelChain\Prompt\StrReplaceRenderer;

// Our JSON schema
$schema = (object) [
    'type' => 'object',
    'properties' => (object) [
        'name' => (object) [
            'type' => 'string'
        ]
    ]
];

// Our prompt template
$promptTemplate = <<<PROMPT_TEMPLATE
Provide a random name and age as valid JSON. Your output must adhere to the following
schema:

{{schema}}

Only output the code, prefixed with "```json" and postfixed with "```". Nothing else.
PROMPT_TEMPLATE;

// Create the LanguageModelChain
$chain = new LanguageModelChain(
    languageModel: $languageModel, // Our already initialized language model
    promptRenderer: new StrReplaceRenderer($promptTemplate),
    outputParser: JsonOutputParser::createWithSchema($schema)
);

// Run the chain!
$context = new Context(['schema' => json_encode($schema));
$chain->run($context);

// Given that the model outputs something as follows:
//
// ```json
// {"name": "John Doe", "age": 42}
// ```
//
// The following value will be set in the context:
echo $context->get('name') . PHP_EOL;
echo $context->get('age') . PHP_EOL;

// Still outputs the whole LLM response, unparsed:
echo $context->get('generation');
```

## Parse errors

If the parser cannot extract the JSON from the output or if the returned JSON does not adhere to the provided schema, a `FailedToParseOutput` exception is thrown.
