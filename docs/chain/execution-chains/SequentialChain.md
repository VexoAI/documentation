# SequentialChain

The `SequentialChain` allows you to connect multiple chains in sequence. This is useful when you, for instance, want to create more complex workflows involving one or more language models.

Values added to the context in one chain, are available for usage in the next chains.

## Example

```php
<?php

use Vexo\Chain\Chain;
use Vexo\Chain\Context;
use Vexo\Chain\SequentialChain\SequentialChain;

$context = new Context([
    'amount' => 42
]);

$sequentialChain = new SequentialChain(
    chains: [
        new Chain(...), // Ran first
        new Chain(...), // Ran second
        // etc
    ]
);

$sequentialChain->run($context);
```

## Events

The `SequentialChain` will emit the following events.

### `ChainStarted`

Emitted when the `SequentialChain` is starting execution of a chain.

| Method                      | Description                                                |
| --------------------------- | ---------------------------------------------------------- |
| `chainIdentifier(): string` | The unique object ID of the chain which is being executed. |
| `chainClass(): string`      | The fully-qualified class name of the chain.               |
| `context(): Context`        | The context object passed to the chain.                    |

### `ChainFinished`

Emitted when the `SequentialChain` has finished execution of a chain.

| Method                      | Description                                                |
| --------------------------- | ---------------------------------------------------------- |
| `chainIdentifier(): string` | The unique object ID of the chain which is being executed. |
| `chainClass(): string`      | The fully-qualified class name of the chain.               |
| `context(): Context`        | The context object passed to the chain.                    |
