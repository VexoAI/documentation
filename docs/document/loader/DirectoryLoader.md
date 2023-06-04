# DirectoryLoader

The `DirectoryLoader` allows you to load documents from a directory. It will recursively scan the directory for files and then load them using a provided secondary loader specific to the file format.

## Requirements

The `DirectoryLoader` depends on the `league/flysystem` library to access the filesystem. This can be installed using composer.

```
composer require league/flysystem
```

## Loading documents

The minimum arguments needed when creating a new `DirectoryLoader` are an instance of `League\Flysystem\Filesystem` and the path to the directory to load documents from.


```php
<?php

use League\Flysystem\Filesystem;
use League\Flysystem\Local\LocalFilesystemAdapter;
use Vexo\Document\Loader\DirectoryLoader;

// Create our filesystem instance
$filesystem = new Filesystem(
    new LocalFilesystemAdapter('/path/to/root/directory')
);

// Create the loader. Given the path below, it will end up looking at the
// /path/to/root/directory/relative/path
$loader = new DirectoryLoader(
    filesystem: $filesystem,
    path: 'relative/path'
);

// Load our documents
$documents = $loader->load();
```

## Overriding the file loader

By default the `DirectoryLoader` will use the `Vexo\Document\Loader\TextFileLoader` to load files. If you want to use a different loader, you can pass a callable as the `fileLoader` parameter in the constructor. This callable is called for every file found in the directory. It is passed the `League\Flysystem\FilesystemReader` instance and the path to the file.

This callable must return an instance of `Vexo\Document\Documents`, even if no files are loaded. In that case it should return an empty collection.

```php
<?php

use League\Flysystem\FilesystemReader;

$loader = new DirectoryLoader(
    filesystem: $filesystem,
    path: 'relative/path',
    fileLoader: function (FilesystemReader $filesystem, string $path): Documents {
        return (new MyCustomFileLoader($filesystem, $path))->load();
    }
);
```

## Loading documents recursively

By default the `DirectoryLoader` will only load files in the directory specified. If you want to load files recursively, you can set the `loadRecursive` parameter to `true` in the constructor.

```php
<?php

$loader = new DirectoryLoader(
    filesystem: $filesystem,
    path: 'relative/path',
    loadRecursive: true
);
```

## Filtering files

If you want to filter certain files from being loaded, you can pass a callable as the `filter` parameter in the constructor. This callable is called for every file found in the directory. It is passed an instance of `League\Flysystem\StorageAttributes` containing the file metadata. The callable should return `true` if the file should be loaded, or `false` if it should be skipped.

In the example below we add a filter that will make sure the loader only loads files with the `.txt` extension.

```php
<?php

use League\Flysystem\StorageAttributes;

$loader = new DirectoryLoader(
    filesystem: $filesystem,
    path: 'relative/path',
    filter: function (StorageAttributes $attributes): bool {
        return pathinfo($attributes->path(), PATHINFO_EXTENSION) == 'txt';
    }
);
```

## Loading files from different sources

Because we rely on the `league/flysystem` library, we can load files from a variety of sources. For example, we can load files from an S3 bucket.

```php
<?php

use Aws\S3\S3Client;
use League\Flysystem\AwsS3V3\AwsS3V3Adapter;
use League\Flysystem\Filesystem;
use Vexo\Document\Loader\DirectoryLoader;

// Create our S3 client
$s3 = new Aws\S3\S3Client(/* your options */)

// Create our filesystem instance
$filesystem = new Filesystem(
    new AwsS3V3Adapter($s3, 'bucket-name')
);

// Create the loader
$loader = new DirectoryLoader(
    filesystem: $filesystem,
    path: 'relative/path'
);
```

Flysystem supports AWS S3, Google Cloud, Azure Blob Storage, (S)FTP, and a few others. For more information on which other sources are available, check the [league/flysystem documentation](https://flysystem.thephpleague.com/docs/).
