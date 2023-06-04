# CharacterTextSplitter

The `CharacterTextSplitter` is a text splitter that recursively splits text into smaller chunks based on a maximum chunk size and a list of separator characters to split on.

In the examples below we only show the `split` method, but the `splitDocuments` method works in the same way, except that it operates on and returns a `Documents` instance.

## Example

```php
<?php

use Vexo\Document\TextSplitter\CharacterTextSplitter;

// Create the text splitter
$textSplitter = new CharacterTextSplitter(
    maxChunkSize: 40,
    separatorCharacters: [' ']
);

// Define our "large" piece of text
$text = 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod '
    . 'tempor incididunt ut labore et dolore magna aliqua.';

// Split the text
$chunks = $textSplitter->split($text);

// Chunks will now contain the following:
// [
//     'Lorem ipsum dolor sit amet, consectetur',
//     'adipiscing elit, sed do eiusmod tempor',
//     'incididunt ut labore et dolore magna',
//     'aliqua.'
// ]
```

## Recursive splitting

When you specify multiple split characters, the text splitter will split the text on the first character in the list. If the resulting chunks are still larger than the maximum chunk size, it will recursively split the chunks on the next character in the list, and so on.

In the example below we first try to separate by paragraphs separated by two newlines, but if these are too large we will split on single newlines. If that's still too big, we will split on words separated by spaces, and if that's still too big we will split on characters.

Note that if the split chunks are still too large after exhausting the list of separator characters, the text splitter will always split on characters.

```php
<?php

use Vexo\Document\TextSplitter\CharacterTextSplitter;

// Create the text splitter
$textSplitter = new CharacterTextSplitter(
    maxChunkSize: 40,
    separatorCharacters: ["\n\n", "\n", ' ']
);

// Define our "large" piece of text
$text = <<<TEXT
    This paragraph will fit in a chunk.

    This is a paragraph which is too long
    for a single chunk, but each line will
    fit into a single chunk.

    This paragraph and this line which it contains will not fit, so it will be split up.

    This_is_an_extremely_long_word_that_will_be_split_to_fit_into_a_chunk.
    TEXT;

// Split the text
$chunks = $textSplitter->split($text);

// Chunks will now contain the following:
// [
//     'This paragraph will fit in a chunk.',
//     'This is a paragraph which is too long',
//     'for a single chunk, but each line will',
//     'fit into a single chunk.',
//     'This paragraph and this line which it',
//     'contains will not fit, so it will be',
//     'split up.',
//     'This_is_an_extremely_long_word_that_will',
//     '_be_split_to_fit_into_a_chunk.'
// ]
```

## Chunk overlap

Sometimes you may want to have some overlap between chunks. This can be useful when you want to make sure that for a given split chunk you have some context from the previous chunk. You can specify the overlap size by passing the `minChunkOverlap` parameter to the constructor.

The splitter will try to respect your separator characters when choosing the overlap. The overlap will probably be larger than the `minChunkOverlap` parameter to make sure no undesirable splitting occurs.

In the example below we split by space, and the overlaps chosen will never split a word in half if possible.

```php
<?php

use Vexo\Document\TextSplitter\CharacterTextSplitter;

// Create the text splitter
$textSplitter = new CharacterTextSplitter(
    maxChunkSize: 40,
    minChunkOverlap: 10,
    separatorCharacters: [' ']
);

// Define our "large" piece of text
$text = 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor '
    . 'incididunt ut labore et dolore magna aliqua.';

// Split the text
$chunks = $textSplitter->split($text);

// Chunks will now contain the following:
// [
//     'Lorem ipsum dolor sit amet, consectetur adipiscing',
//     'adipiscing elit, sed do eiusmod tempor incididunt',
//     'incididunt ut labore et dolore magna aliqua.'
// ]
```

## On whitespace

The text splitter will not trim whitespace by default from the resulting chunks, but it will ignore empty chunks. For instance if your text contains multiple consecutive spaces or newlines, and you split by those characters, the resulting chunks will not contain empty strings.

However if you split by a newline character, and the resulting chunks contain whitespace, the whitespace will be preserved.

You can force the text splitter to always trim chunks from whitespace by passing the `trimWhitespace` parameter to the constructor. When this is set, any chunks containing only whitespace will also be ignored, regardless of your separator characters.

```php
<?php

use Vexo\Document\TextSplitter\CharacterTextSplitter;

// Create the text splitter
$textSplitter = new CharacterTextSplitter(
    maxChunkSize: 50,
    minChunkOverlap: 10,
    separatorCharacters: ['_'],
    trimWhitespace: true
);

// Define our "large" piece of text
$text = 'Lorem_ipsum_dolor_sit_amet,_consectetur_ _ ___adipiscing_elit,_sed_do_eiusmod__'
    . ' _ __ _tempor_incididunt_ut_labore_et_dolore_magna_aliqua.';

// Split the text
$chunks = $textSplitter->split($text);

// Chunks will now contain the following:
// [
//     'Lorem_ipsum_dolor_sit_amet,_consectetur_adipiscing',
//     'adipiscing_elit,_sed_do_eiusmod_tempor_incididunt',
//     'incididunt_ut_labore_et_dolore_magna_aliqua.'
// ]
```

## Measuring chunk size

By default the text splitter relies on the `mb_strlen` function to determine text and chunk size. If you want to use a different method to measure the size of a chunk, you can pass a callable as the `sizeFunction` parameter to the constructor. This callable is passed the chunk text, and should return the size of the chunk as an integer.

In the example below we use the `strlen` function to measure the size of a chunk. This will result in the text splitter counting bytes instead of characters.

```php
<?php

use Vexo\Document\TextSplitter\CharacterTextSplitter;

// Create the text splitter
$textSplitter = new CharacterTextSplitter(
    maxChunkSize: 40,
    separatorCharacters: ["\n\n", "\n", ' '],
    sizeFunction: 'strlen'
);
```
