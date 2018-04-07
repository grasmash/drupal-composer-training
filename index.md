## Agenda
{:.no_toc}

* TOC
{:toc}

## Check System Requirements

* PHP 5.3.2+

We strongly suggest that you use OSX or Linux OS. Windows users, please consider using [Drupal VM](https://www.drupalvm.com/).

## Install Composer

Follow the instructions below, or follow the [Composer installation instructions](https://getcomposer.org/doc/00-intro.md#system-requirements) and return here when you’re finished.

If you’re a homebrew user on OSX, you can use `brew install composer`. Otherwise, execute the following on your command line:

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
chmod 755 composer.phar
```

This downloaded and installed Composer. Now you need to move it into directory that is part of your `PATH` so you can access it globally. For instance:

```
mv composer.phar /usr/local/bin/composer
```

If that did not work, follow the more detailed [Composer installation instructions](https://getcomposer.org/doc/00-intro.md#system-requirements) and return here when you’re finished.

## Take Composer for a test drive without Drupal

### Create a new project

```
mkdir my-new-project
cd my-new-project
touch index.php
```

### Create a PHP script

### Initialize Composer

```
composer init
```

### Require a new dependency

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

## Try basic Composer commands

```
composer
composer list
composer info monolog/monolog
composer why monolog/monolog
composer browse monolog/monolog
composer validate
```

## Create a new Drupal project with Composer

```
composer create-project drupal-composer/drupal-project:8.x-dev my-project --stability dev --no-interaction
cd my-project
```
## Require and update Drupal dependencies

### Require a module

```
composer require drupal/token
```

### Require a theme

```
composer require drupal/bootstrap
```

### Require a dev dependency

```
composer require squizlabs/php_codesniffer --dev
```

## Execute project binaries

```
./vendor/bin/drush status
./vendor/bin/drupal
./vendor/bin/phpcs
```

## Update core to dev version

```
composer require drupal/core:8.6.x-dev
```

## Patch something!

```
"extra" {
    "patches": {
      "drupal/core": {
        "Clear Twig caches on deploys": "https://www.drupal.org/files/issues/2752961-130.patch"
      }
}
```

## Update Drupal Core

Execute:
```
composer update drupal/core
```
