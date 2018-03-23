## Agenda
{:.no_toc}

* TOC
{:toc}
  
## Test Drive

### Check System Requirements

### Install Composer

Follow the [Composer installation instructions](https://getcomposer.org/doc/00-intro.md#system-requirements) and return here when you’re finished!

If you’re a homebrew user on OSX, you can use `brew install composer`.

### Take Composer for a test drive without Drupal

#### Create a new project

```
mkdir my-new-project
cd my-new-project
touch index.php
```

#### Create a PHP script

#### Initialize Composer

```
composer init
```

#### Require a new dependency

```
composer require monolog/monolog
```

#### Implement the dependency


```php
<?php

// Require Composer's autoloader.
require __DIR__ . "/vendor/autoload.php";

use Monolog\Logger;
use Monolog\Handler\StreamHandler;

// Create a logger.
$log = new Logger('example-log');
$log->pushHandler(new StreamHandler(__DIR__ . "/example.log", Logger::WARNING));

// Log a message!
$log->error('Goodbye world!');
```

```
php -f index.php
```

```
$ cat example.log
[2018-03-25 12:05:00] example-log.ERROR: Goodbye world! [] []
```

```
cd ..
rm -rf my-new-project 
```

## (slides) Review of Composer anatomy

## (slides) Semantic Versioning and Constraints

## (hands-on) Create a new Drupal project with Composer

```
composer create-project drupal-composer/drupal-project:8.x-dev my-project --stability dev --no-interaction
cd my-project
```

## (slides) Drupal-specific Composer config

## (hands-on) Require and update Drupal dependencies

### Require a module
### Require a theme
### Update core to dev version
### Patch something! 

## (slides) Deploying to hosting env

## (slides) Advanced scenarios

### Front end libraries
### Custom install locations
### Custom modules and merge plugin
