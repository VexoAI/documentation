# Create your own

You can create your own prompt renderer by implementing the `Vexo\Chain\LanguageModelChain\Prompt\Renderer` interface. It has a single `render` method which takes a context object, and returns the final prompt as a string.

```php
<?php

use Vexo\Chain\Context;

interface Renderer
{
    public function render(Context $context): string;
}
```

## Example

In the example below we create a simple `strtr` based prompt renderer.

```php
<?php

namespace Acme;

use Vexo\Chain\Context;
use Vexo\Chain\LanguageModelChain\Prompt\Renderer;

final class StrtrRenderer implements Renderer
{
    public function __construct(private readonly string $template)
    {
    }

    public function render(Context $context): string
    {
        $replacements = [];
        foreach ($context as $name => $value) {
            $replacements["{{{$name}}}"] = $value;
        }

        return strtr($this->template, $replacements);
    }
}

```

You can then use it as follows:

```php
<?php

use Acme\StrtrRenderer;
use Vexo\Chain\LanguageModelChain\LanguageModelChain;

// Create the LanguageModelChain
$chain = new LanguageModelChain(
    languageModel: $languageModel, // Our already initialized language model
    promptRenderer: new StrtrRenderer('What is the capital of {{country}}?')
);

// Run the chain!
$context = new Context(['country' => 'France']);
$chain->run($context);

// Outputs something like: The capital of France is Paris
echo $context->get('generation');
```

## Dealing with missing values

Note that in the example above we do not check for any missing context values. Any referenced values in the template which are not available in the context will simply be skipped over.

If you use the `Context::get` method to extract values, the appropriate exeception will be automatically thrown if the requested value is not available.

If you use some other method, like in the example above, it is advisable to throw a `FailedToGetContextValue` exception yourself if the value is not available.

## Dealing with rendering issues

If for some reason the prompt cannot be rendered, you should throw a `FailedToRenderPrompt` exception.
