# Getting started

Chains provide a generic way to compose workflows around large language models.

The concept of a chain is rather simple; it performs an action given a certain context, and then manipulates that context. Some chains allow you to combine multiple chains together to create elaborate workflows.

The following is an example of a simple workflow where we use a `WebTextChain` to load text from a webpage, and then pass that to a `LanguageModelChain` for summarization.

```php
<?php

use Vexo\Chain\Context;
use Vexo\Chain\LanguageModelChain\LanguageModelChain;
use Vexo\Chain\LanguageModelChain\Prompt\StrReplaceRenderer;
use Vexo\Chain\SequentialChain\SequentialChain;
use Vexo\Chain\WebTextChain\WebTextChain;
use Vexo\Model\Language\OpenAIChatModel;


// Load our language model using OpenAI
$languageModel = new OpenAIChatModel(
    \OpenAI::client("sk-AbCdEfG987...")->chat()
);

// Initialize our sequential chain and the two needed chains in order
$sequentialChain = new SequentialChain(
    chains: [
        // Create our WebTextChain which will take a URL from the context, downloads the
        // web page, and saves the text contents in the context
        new WebTextChain(),

        // Create our LanguageModelChain using the language model above and a basic prompt
        // renderer, which will ask the language model to summarize the given text.
        new LanguageModelChain(
            languageModel: $languageModel,
            promptRenderer: new StrReplaceRenderer("Summarize the text below:\n\n{{text}}")
        )
    ]
);

// Create the context the chains will operate on, with our initial URL
$context = new Context(['url' => 'https://www.example.com']);

// Run the chains on the context
$sequentialChain->run($context);

// Show the summarized webpage
echo $context->get('generation'); // Outputs something like: The Example Domain can be...
```

## Remapping context values

In the example above we are relying on the default context values matching up between the different chains. For instance, the `WebTextChain` needs the "url" value to be available in the context, and then puts a new "text" value in the context which the `LanguageModelChain` then uses as part of the prompt.

Sometimes these values don't line up or you simply want to rename these. You can control what these values are called by providing the `inputMap` and `outputMap` variables to a chain.

```php
<?php

$sequentialChain = new SequentialChain(
    chains: [
        new WebTextChain(
            // Context needs a "link" value with the URL
            inputMap: ['url' => 'link'],

            // Contents of the webpage are put in "webpage" instead of "text"
            outputMap: ['text' => 'webpage']
        ),
        new LanguageModelChain(
            languageModel: $languageModel,
            promptRenderer: new StrReplaceRenderer("Summarize the text below:\n\n{{text}}"),

            // Make sure the contents of "webpage" can be used as "text" in the prompt above
            inputMap: ['text' => 'webpage'],

            // The generated value of the LLM is now available as "summary" in the context
            outputMap: ['generation' => 'summary']
        )
    ]
);

echo $context->get('summary'); // This will now work
```

Any chain that operates on the context directly will support these parameters.

## Monitoring events

The `SequentialChain` (and some others) support event dispatching. You can pass a [PSR-14](https://www.php-fig.org/psr/psr-14/) compatible event dispatcher in the `eventDispatcher` parameter which will then be used to dispatch events.

```php
<?php

use Vexo\Contract\Event\Event;

$sequentialChain = new SequentialChain(
    eventDispatcher: $eventDispatcher, // Your PSR-14 compatible dispatcher
    chains: [
        // ...
    ]
);

$eventDispatcher->subscribeTo(
    Event::class,
    function (Event $event): void {
        // Handle the event
    }
);
```

The individual chain documentation will indicate which events are dispatched and when, if any.

## Handling exceptions

If a chain fails for any reason, an instance of `Vexo\Chain\FailedToRunChain` is thrown. All chain exceptions extend this class.

When the chain cannot retrieve required input from the context, an instance of `Vexo\Chain\FailedToGetContextValue` is thrown.

Additionally specific chains may throw other exceptions in different fail cases, but they will all extend from `FailedToRunChain`.
