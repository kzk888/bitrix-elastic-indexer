# Bitrix Elasticsearch Indexer

[![pipeline status](https://gitlab.com/sheerockoff/bitrix-elastic-indexer/badges/master/pipeline.svg)](https://gitlab.com/sheerockoff/bitrix-elastic-indexer/pipelines)
[![coverage report](https://gitlab.com/sheerockoff/bitrix-elastic-indexer/badges/master/coverage.svg)](https://gitlab.com/sheerockoff/bitrix-elastic-indexer/-/jobs)
[![PHP from Packagist](https://img.shields.io/packagist/php-v/sheerockoff/bitrix-elastic-indexer.svg)](https://packagist.org/packages/sheerockoff/bitrix-elastic-indexer)

Хелпер для индексации данных инфоблока Bitrix в Elasticsearch.

## Установка

```bash
composer require sheerockoff/bitrix-elastic-indexer:dev-master
```

## Быстрый старт

Подключаем зависимости, создаём клиент Elasticsearch, создаём экземпляр `Indexer`.

```php
<?php

use Elasticsearch\ClientBuilder;
use Sheerockoff\BitrixElastic\Indexer;

require 'vendor/autoload.php';

$elastic = ClientBuilder::create()->setHosts(['http://elasticsearch:9200'])->build();
$indexer = new Indexer($elastic);
```

Получаем карту индекса для инфоблока.

```php
$infoBlockMapping = $indexer->getInfoBlockMapping($iBlockId);
```

Обновляем карту индекса в Elasticsearch. Метод обновит карту только тех свойств,
которые отсутствуют в текущем индексе. Карты существующих свойств в индексе
изменяться не будут, чтобы избежать ошибок.

```php
$indexer->putIndexMapping('goods', $infoBlockMapping);
```

Получаем текущую карту индекса из Elasticsearch.

```php
$elasticMapping = $indexer->getIndexMapping('goods');
```

Получаем сырые данные индекса для элемента.

```php
/** @var _CIBElement $element */
$rawData = $indexer->getElementIndexData($element);
```

Нормализуем сырые данные индекса в соответствии с картой индекса Elasticsearch.

```php
$data = $indexer->normalizeData($elasticMapping, $rawData);
```