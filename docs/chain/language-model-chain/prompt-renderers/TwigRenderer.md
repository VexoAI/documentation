# TwigRenderer

The `TwigRenderer` makes use of [Twig](https://twig.symfony.com/) templates to render prompts. This allows for a great deal of flexibility in generating more elaborate prompts.

## Requirements

The renderer requires you to have Twig installed in your project.

```bash
composer require twig/twig
```

## Example

The renderer has a factory method for convenience which will allow you to specify the location of your prompt template files and the name of the template you would like to use.

Assuming you have the following template at `/your/project/templates/prompt.twig`:

```twig
Use the following pieces of context to answer the question at the end. If you don't know
the answer, just say that you don't know. Don't try to make up an answer.

{{ context }}

Question: {{ question }}
Helpful Answer:
```

You can render it as follows.

```php
<?php

use Vexo\Chain\LanguageModelChain\LanguageModelChain;
use Vexo\Chain\LanguageModelChain\Prompt\TwigRenderer;

// Create the LanguageModelChain
$chain = new LanguageModelChain(
    languageModel: $languageModel, // Our already initialized language model
    promptRenderer: TwigRenderer::createWithFilesystemLoader(
        'prompt.twig'
        '/your/project/templates'
    )
);

// Run the chain!
$context = new Context([
    'context' => 'Some long article about Edsger Dijkstra...'
    'question' => 'When was Edsger Dijkstra born?'
]);
$chain->run($context);

// Outputs something like: Edsget Dijkstra was born on May 11th, 1930
echo $context->get('generation');
```

## Overriding the template loader

If you would like to use a different way to load your templates, or want to provide Twig with more advanced configuration options, you can use the `createWithLoader` factory method instead and inject your own loader.

```php
<?php

use Twig\Loader\ArrayLoader;
use Vexo\Chain\LanguageModelChain\LanguageModelChain;
use Vexo\Chain\LanguageModelChain\Prompt\TwigRenderer;

// Create the LanguageModelChain
$chain = new LanguageModelChain(
    languageModel: $languageModel, // Our already initialized language model
    promptRenderer: TwigRenderer::createWithLoader(
        'prompt.twig'
        new ArrayLoader(['prompt.twig' => 'What is the capital of {{ country }}?']),
        ['debug' => true] // Optional extra Twig configuration options
    )
);

```

## Template syntax

Twig's templating language is elaborate and supports many useful features like control structures, filters, expressions, etc. Please have a look at [Twig for Template Designers](https://twig.symfony.com/doc/3.x/templates.html) for more information on the possibilities.

## Missing values

If the template references a value which isn't available in the context, a `FailedToGetContextValue` exception will be thrown.
