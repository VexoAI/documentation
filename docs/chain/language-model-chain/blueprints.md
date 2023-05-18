# Blueprints

Blueprints are a way to define a set of parameters that commonly go together when creating new language model chains. Very often the prompt renderer, language model, and output parser are coupled, as the prompt commonly contains instructions on how the language model should format its output. Blueprints allow you to define these as a single unit.

## Common blueprints

The following blueprints are provided out of the box.

| Blueprint | Description |
| --------- | ----------- |
| [AnswerQuestionAboutContext](https://github.com/VexoAI/vexo/blob/master/src/Chain/LanguageModelChain/Blueprint/AnswerQuestionAboutContext.php) | Instructs a model to answer a question about a given context. |
| [ReasonAndAct](https://github.com/VexoAI/vexo/blob/master/src/Chain/LanguageModelChain/Blueprint/ReasonAndAct.php) | Employs the [Reason and Act](https://learnprompting.org/docs/advanced_applications/react) paradigm for more complex reasoning. |


## Using blueprints

You can use the `LanguageModelChainFactory` to create a chain from a blueprint.

```php
<?php

use Vexo\Chain\LanguageModelChain\Blueprint\AnswerQuestionAboutContext;
use Vexo\Chain\LanguageModelChain\LanguageModelChainFactory;

// Create the LanguageModelChain
$factory = new LanguageModelChainFactory($languageModel); // Use our preinitialized model
$chain = $factory->createFromBlueprint(
    blueprint: new AnswerQuestionAboutContext(),

    // The following is optional; if you want to remap the input/output values
    inputMap: ['question' => 'query', 'context' => 'article'],
    outputMap: ['answer' => 'result']
);

// Run the chain!
$context = new Context([
    'query' => 'What is the article about?',
    'article' => 'Some long article...'
]);
$chain->run($context);

// Outputs something like: This article is about...
echo $context->get('result');
```

## Creating your own

You can create your own blueprint by implementing the `Vexo\Chain\LanguageModelChain\Blueprint` interface. It should define a prompt renderer, an output parser, and a list of stop tokens.

```php
<?php

use Vexo\Chain\LanguageModelChain\OutputParser\OutputParser;
use Vexo\Chain\LanguageModelChain\Prompt\Renderer;

interface Blueprint
{
    public function promptRenderer(): Renderer;

    public function outputParser(): OutputParser;

    /**
     * @return array<string>
     */
    public function stops(): array;
}
```

### Example

What follows is a basic blueprint which will ask the model to classify a given piece of text and output its classification as JSON.

```php
<?php

namespace Acme;

use Vexo\Chain\LanguageModelChain\Blueprint;
use Vexo\Chain\LanguageModelChain\OutputParser\JsonOutputParser;
use Vexo\Chain\LanguageModelChain\Prompt\StrReplaceRenderer;

final class ClassifyText implements Blueprint
{
    public function promptRenderer(): Renderer
    {
        $schemaAsJson = json_encode($this->schema());

        return new StrReplaceRenderer(
            <<<PROMPT_TEMPLATE
            Classify the text below by sentiment. It is either positive or negative.
            Output the classification as valid JSON. Your output should adhere to the
            following schema:

            {$schemaAsJson}

            Do not output anything else. The text is:

            {{text}}

            Classification JSON:
            PROMPT_TEMPLATE
        );
    }

    public function outputParser(): OutputParser
    {
        return JsonOutputParser::createWithSchema($this->schema());
    }

    public function stops(): array
    {
        return [];
    }

    private function schema(): object
    {
        return (object) [
            'type' => 'object',
            'properties' => (object) [
                'sentiment' => (object) ['type' => 'string']
            ],
            'required' => ['sentiment']
        ];
    }
}
```

You can then use it as you would any other blueprint.

```php
<?php

use Acme\ClassifyText;
use Vexo\Chain\LanguageModelChain\LanguageModelChainFactory;

// Create the LanguageModelChain
$factory = new LanguageModelChainFactory($languageModel); // Use our preinitialized model
$chain = $factory->createFromBlueprint(new ClassifyText());

// Run the chain!
$context = new Context([
    'text' => 'My life is amazing!'
]);
$chain->run($context);

echo $context->get('sentiment'); // Outputs: positive
```
