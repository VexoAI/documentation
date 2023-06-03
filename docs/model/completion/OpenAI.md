# OpenAI

The OpenAI model is a completion model that relies on OpenAI's completions API.

## Requirements

The `OpenAIModel` requires the `openai-php/client` package to be installed.

```
composer require openai-php/client
```

You will also need an OpenAI API key. You can create an API key from the [API keys](https://platform.openai.com/account/api-keys) page in your OpenAI account.

## Example

```php
<?php

use Vexo\Model\Completion\OpenAIModel;

// Load our OpenAI client with our API key
$openAI = \OpenAI::client($_ENV['OPENAI_API_KEY']);

// Create our OpenAI model based on the OpenAI completions API
$model = new OpenAIModel($openAI->completions());

// Generate a result
$result = $model->generate('What is the capital of France?');
```

## Configuration

Most additional parameters that one would normally pass to the OpenAI completions API can be passed to the `OpenAIModel` constructor as additional parameters. The only exceptions are the `prompt`, `stop`, and `stream` parameters. The prompt and stop tokens are set when making the call to `generate` instead. The `stream` parameter is not supported at this time.

See the [OpenAI API reference](https://platform.openai.com/docs/api-reference/completions/create) for more information on these parameters.

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
        'echo' => false
    ]
);
```
