# Introduction

Completion models allow you to generate text based on a prompt. We currently support OpenAI's completion and chat APIs.

All completion models implement the `Vexo\Model\Completion\Model` interface. It has a single method `generate` which accepts a text prompt and a an array of stop tokens.

```php
<?php

interface Model
{
    /**
     * @param array<string> $stops
     */
    public function generate(string $prompt, array $stops = []): Result;
}
```

## Stop tokens

Stop tokens are used to tell the model when to stop generating text. How many stop tokens you can provide depends on the specific model.

## Interpreting the result

The generate method returns an instance of `Vexo\Model\Completion\Result` which contains the generated text(s) and some additional metadata provided by the model.

Depending on how the model is configured, a result object may contain multiple generated texts. For example, the OpenAI model can generate multiple completions for a single prompt.

The metadata provided by the model by include statistics like the amount of tokens used and the specific model used. This metadata is model-specific and cannot be relied upon to be present across different models

```php
<?php

$result = $model->generate('What is the capital of France?');

// Show the first generated text
echo $result->generation();

// Get all generated texts
$generations = $result->generations();

// Get model metadata. May output something like this:
//
// Array
// (
//     [model] => text-davinci-003
//     [usage] => Array
//         (
//             [prompt_tokens] => 5
//             [completion_tokens] => 7
//             [total_tokens] => 13
//         )
// )
print_r($result->metadata()->toArray());

```

## Monitoring events

All completion models support event dispatching. You can pass a [PSR-14](https://www.php-fig.org/psr/psr-14/) compatible event dispatcher in the `eventDispatcher` constructor argument which will then be used to dispatch events.

The following is an example when injecting an event dispatcher into the `OpenAIModel`.

```php
<?php

use Vexo\Contract\Event\Event;
use Vexo\Model\Completion\OpenAIModel;

// Load our OpenAI client with our API key
$openAI = \OpenAI::client($_ENV['OPENAI_API_KEY']);

// Create our OpenAI model based on the OpenAI completions API
$model = new OpenAIModel(
    completions: $openAI->completions(),
    eventDispatcher: $eventDispatcher // Your PSR-14 compatible dispatcher
);

$eventDispatcher->subscribeTo(
    Event::class,
    function (Event $event): void {
        // Handle the event
    }
);
```

## Events

At this time the only event models will emit is the `ModelGeneratedResult` event when they generate a result. This event is emitted after the result has been generated and is ready to be returned. The event object has the following methods.

| Method                   | Description                                               |
| ------------------------ | --------------------------------------------------------- |
| `prompt(): string`       | The prompt being used to generate the result.             |
| `stops(): array<string>` | The stop tokens provided.                                 |
| `result(): Result`       | The actual result object containing the generated result. |
