# OpenAIChat

The OpenAI chat is a completion model that relies on OpenAI's chat API. This is the model needed to use OpenAI's GPT-4 model.

## Requirements

The `OpenAIChatModel` requires the `openai-php/client` package to be installed.

```
composer require openai-php/client
```

You will also need an OpenAI API key. You can create an API key from the [API keys](https://platform.openai.com/account/api-keys) page in your OpenAI account.

## Example

```php
<?php

use Vexo\Model\Completion\OpenAIChatModel;

// Load our OpenAI client with our API key
$openAI = \OpenAI::client($_ENV['OPENAI_API_KEY']);

// Create our OpenAI Chat model based on the OpenAI chat API
$model = new OpenAIChatModel($openAI->chat());

// Generate a result
$result = $model->generate('What is the capital of France?');
```

## Configuration

Most additional parameters that one would normally pass to the OpenAI chat API can be passed to the `OpenAIChatModel` constructor as additional parameters. The only exceptions are the `stop` and `stream` parameters. The stop tokens are set when making the call to `generate` instead. The `stream` parameter is not supported at this time.

You can specify `messages` parameters with an array of messages that you would like prefixed to the prompt. This is useful when you want to specify message history or systems messages that the model should take into account.

See the [OpenAI API reference](https://platform.openai.com/docs/api-reference/chat/create) for more information on these parameters.

```php
<?php

// Create our OpenAI model based on the OpenAI completions API with extra parameters
$model = new OpenAIModel(
    completions: $openAI->completions(),
    parameters: [
        'temperature' => 0.5,
        'max_tokens' => 100,
        'top_p' => 1,
        'n' => 1,
        'logprobs' => null,
        'messages' => [
            ['role' => 'system', 'Provide the briefest possible answers.']
        ]
    ]
);
```
