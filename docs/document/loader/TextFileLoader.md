# TextFileLoader

The `TextFileLoader` is a simple file loader that loads the contents of a single file as plain text.

## Requirements

The `TextFileLoader` depends on the `league/flysystem` library to access the filesystem. This can be installed using composer.

```
composer require league/flysystem
```

## Example

```php
<?php

use League\Flysystem\Filesystem;
use League\Flysystem\Local\LocalFilesystemAdapter;
use Vexo\Document\Loader\TextFileLoader;

// Create our filesystem instance
$filesystem = new Filesystem(
    new LocalFilesystemAdapter('/path/to/root/directory')
);

// Create the loader
$loader = new TextFileLoader(
    filesystem: $filesystem,
    path: 'relative/path'
);

// Load the documents
$documents = $loader->load();
```

Note that in the example above the `load` method will return a `Vexo\Document\Documents` instance containing a single document. This is because the `TextFileLoader` only loads a single file.

If you want to load multiple files, have a look at the `DirectoryLoader`.
