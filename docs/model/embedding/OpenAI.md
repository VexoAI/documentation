# OpenAI

The OpenAI embedding model allows you to embed text into a vector space using OpenAI's embedding API.

## Requirements

The `OpenAIModel` requires the `openai-php/client` package to be installed.

```
composer require openai-php/client
```

You will also need an OpenAI API key. You can create an API key from the [API keys](https://platform.openai.com/account/api-keys) page in your OpenAI account.

## Example

```php
<?php

use Vexo\Model\Embedding\OpenAIModel;

// Load our OpenAI client with our API key
$openAI = \OpenAI::client($_ENV['OPENAI_API_KEY']);

// Create our OpenAI model based on the OpenAI embedding API
$model = new OpenAIModel($openAI->embeddings());

// Embed a query
$vector = $model->embedQuery('What is the capital of France?');

// Embed a list of texts
$vectors = $model->embedTexts([
    'The capital of France is Paris',
    'The capital of Germany is Berlin',
    'The capital of Italy is Rome'
]);
```
