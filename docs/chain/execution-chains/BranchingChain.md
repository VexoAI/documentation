# BranchingChain

The `BranchingChain` allows you to create multiple execution paths within your workflows by evaluating the context before executing a chain.

You can nest multiple chains within the `BranchingChain` paired with a condition. Conditions are then executed in order, and if they evaluate to `true`, the chain is executed. If multiple conditions evaluate to `true`, multiple chains will be executed.

All the values contained in the context are available for evaluation.

## Requirements

The condition is evaluated using the [Symfony ExpressionLanguage](https://symfony.com/doc/current/components/expression_language.html) component and requires this to be available in your project.

```bash
composer require symfony/expression-language
```

## Example

```php
<?php

use Vexo\Chain\BranchingChain\BranchingChain;
use Vexo\Chain\Chain;
use Vexo\Chain\Context;

$context = new Context([
    'amount' => 42,
    'thing' => ['foo' => 'bar'],
    'action' => 'some-chain',
    'definitely' => true
]);

$branchingChain = new BranchingChain(
    chains: [
        "amount in 40..100"                   => new Chain(...), // true
        "action in ['some-chain', 'another']" => new Chain(...), // true
        "thing['foo'] == 'bar'"               => new Chain(...), // true
        "definitely"                          => new Chain(...), // true
        "doesNotExist"                        => new Chain(...)  // Throws an exception
    ]
);

$branchingChain->run($context);
```

## Condition syntax

The ExpressionLanguage component gives you a lot of different ways to evaluate the given context. For more information on the syntax and possible evaluations, have a look at [The Expression Syntax](https://symfony.com/doc/current/reference/formats/expression_language.html).

## Syntax errors

On syntax error or references to non-existent values, a `FailedToEvaluateCondition` exception is thrown explaining the error.

## Events

The `BranchingChain` will emit the following events.

### `ChainBranchConditionEvaluated`

Emitted when the condition has been evaluated. The following methods are available on the event.

| Method                      | Description                                                           |
| --------------------------- | --------------------------------------------------------------------- |
| `chainIdentifier(): string` | The unique object ID of the chain who's condition is being evaluated. |
| `chainClass(): string`      | The fully-qualified class name of the chain.                          |
| `context(): Context`        | The context object used for the evaluation.                           |
| `condition(): string`       | The condition being evaluated.                                        |
| `evaluationResult(): bool`  | What the condition evaluated to.                                      |

### `ChainStarted`

Emitted when the `BranchingChain` is starting execution of the chain, after the condition evaluates to `true`.

| Method                      | Description                                                |
| --------------------------- | ---------------------------------------------------------- |
| `chainIdentifier(): string` | The unique object ID of the chain which is being executed. |
| `chainClass(): string`      | The fully-qualified class name of the chain.               |
| `context(): Context`        | The context object passed to the chain.                    |

### `ChainFinished`

Emitted when the `BranchingChain` has finished execution of a chain.

| Method                      | Description                                                |
| --------------------------- | ---------------------------------------------------------- |
| `chainIdentifier(): string` | The unique object ID of the chain which is being executed. |
| `chainClass(): string`      | The fully-qualified class name of the chain.               |
| `context(): Context`        | The context object passed to the chain.                    |
