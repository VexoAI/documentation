# CsvFileLoader

The `CsvFileLoader` is a file loader that loads the contents of a single CSV file into multiple documents. Each row in the CSV file will be loaded as a separate document.

It will use one of the columns in the CSV file as the document contents, and the rest of the columns as metadata.

## Requirements

The `CsvFileLoader` depends on the `league/csv` library to read the CSV file. This can be installed using composer.

```
composer require league/csv
```

## Loading documents

The only argument required when creating a new `CsvFileLoader` is the path to the CSV file to load. By default the loader assumes that there's a column called "contents" containing the document contents. You can override this by passing a different column name as the `contentsColumn` parameter in the constructor.

```php
<?php

use Vexo\Document\Loader\CsvFileLoader;

$loader = new CsvFileLoader(
    path: '/path/to/file.csv',
    contentsColumn: 'contents'
);

$documents = $loader->load();
```

In addition to the metadata extracted from the CSV, the document will also get a path metadata entry containing the path to the CSV file, and an offset metadata entry containing the row number in the CSV file. If you have columns in the CSV file with the same name, they will take precedence.

## Overriding CSV properties

The loader will attempt to automatically detect the CSV delimiter, enclosure character, and escape character. If you want to override these, you can pass them as the `delimiter`, `enclosure`, and `escape` parameters in the constructor.

```php
<?php

$loader = new CsvFileLoader(
    path: '/path/to/file.csv',
    contentsColumn: 'contents',
    delimiter: ',',
    enclosure: '"',
    escape: '\\'
);
```
