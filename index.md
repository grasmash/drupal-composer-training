## Agenda

This hands-on guide to using Composer with Drupal was originally created for the DrupalCon Nashville session [How to build a Drupal site with Composer AND keep all of your hair](https://events.drupal.org/nashville2018/sessions/how-build-drupal-site-composer-and-keep-all-your-hair), presented by [Matthew Grasmick](http://matthewgrasmick.com) and [Jeff Geerling](https://www.jeffgeerling.com) on Wednesday, April 11, 2018.

{:.no_toc}

* TOC
{:toc}

## Check System Requirements

* PHP 5.3.2+

We strongly suggest that you use OSX or Linux OS. Windows users, please consider using [Drupal VM](https://www.drupalvm.com/).

## Install Composer

Follow the instructions below, or follow the [Composer installation instructions](https://getcomposer.org/doc/00-intro.md#system-requirements) and return here when you’re finished.

If you’re a [Homebrew](https://brew.sh) user on OSX, you can use `brew install composer`. Otherwise, execute the following on your command line:

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

Run the command:

```
composer init
```

And you will be prompted for a few settings for your new project:

  - Enter a description like 'Test project at DrupalCon'
  - Enter `project` for Package Type.
  - Use defaults (hit enter when prompted) for everything else.
  - When it asks about defining dependencies, answer `no`.

Once you confirm the composer project initialization, you'll see a new `composer.json` file in the folder, containing the project information you just entered.

It's also a good idea to tell Composer you prefer to use _stable_ versions of projects, instead of whatever's latest. So let's explicitly tell Composer:

```
composer config prefer-stable true
```

There are [many other Composer config options](https://getcomposer.org/doc/06-config.md) you could set, but for the rest of them, the defaults are fine.

### Require a new dependency

Your project doesn't do anything yet, so let's add a dependency so we can start doing something productive! Use `composer require` to add a new dependency.

```
composer require monolog/monolog
```

[Monolog](https://seldaek.github.io/monolog/) is a logging library for PHP that uses the standard PSR-3 logging interface. Basically, it makes logging things easy!

#### Implement the dependency

Using Monolog is easy; add the following lines of code to the `index.php` file you created earlier:

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

Save the file, and run it on the command line:

```
php -f index.php
```

The script should execute very quickly, and afterwards, there should be a new `example.log` file in your project's directory. Let's look at what's inside:

```
$ cat example.log
[2018-03-25 12:05:00] example-log.ERROR: Goodbye world! [] []
```

Once you're done with this simple project, clean up your project if you don't want it for future reference:

```
cd ..
rm -rf my-new-project
```

That's it! You've already started using Composer to make features in your PHP applications easier to implement!

## Try basic Composer commands

Now that you have your feet wet, let's explore a few more commands that can helpful when learning or exploring Composer.

```
composer
composer list
```

These two commands list out Composer's help, along with a list of all the available Composer commands on your system. Note if you have extra composer `scripts` defined in your project, they will be listed alongside the other available commands.

You can use other commands to explore Composer dependencies:

```
composer info monolog/monolog
```

For any dependency used in your current project, `composer info` lists out all the information, including metadata, requirements, and suggestions.

```
composer why monolog/monolog
```

`composer why` (an alias for `composer depends`) gives a concise description of why a certain dependency is used. For example, in our test project, we can see the project directly requires monolog:

```
$ composer why monolog/monolog
myusername/my-new-project  -  requires  monolog/monolog (^1.23)
```

When you want to start using a library, you might want to look up documentation, or get support. To do that, you could go search on Google for documentation, but it's even faster to go straight to the library's website using `composer browse`:

```
composer browse monolog/monolog
```

This will open up a browser window to the project's home page.

```
composer outdated
```

Finally, this handy command will look at all the packages used in your project, and check if any updates are available. If there are none, it won't output anything.

## Create a new Drupal project with Composer

Drupal requires a bit of extra scaffolding and setup to be able to more easily manage your Drupal project with Composer, so we're going to use the most popular method of building a new Drupal codebase, the [Composer Template for Drupal projects](https://github.com/drupal-composer/drupal-project).

Following the directions from that project's README, create a new Drupal project using Composer:

```
composer create-project drupal-composer/drupal-project:8.x-dev my-project --stability dev --no-interaction
cd my-project
```

This process will take a while; you'll notice it installs a large number of dependencies, most of which are required by Drupal itself, but a few others will help you in managing your Drupal codebase with Composer (like [cweagans/composer-patches](https://github.com/cweagans/composer-patches), a library that lets you easily manage patches to Drupal core and contrib projects!).

Once your codebase is complete, it's a good idea to install some of the Drupal modules, themes, and tools that will complete your site.

> TODO: Notes or quick guide to adding a VM or Docker setup as a Composer dependency/plugin and run the site locally? Maybe create a quick Composer plugin just for this purpose, e.g. a Drupal VM Docker plugin that lets you just `docker-compose up` after adding/running it?

## Require and update Drupal dependencies

### Require a module

Almost every Drupal site requires a few additional modules to provide much-needed functionality. One of the most popular Drupal modules is the [Token](https://www.drupal.org/project/token) module, which provides many tokens you can use around your Drupal site.

You can install the latest version with:

```
composer require drupal/token
```

If you do this, you'll see an entry added to the `require` section of your `composer.json` file like:

```
        "drupal/token": "^1.1",
```

Basically, Composer looked up the Token project on Drupal.org's Packagist, found that the latest stable version is `1.1` (corresponding to `8.x-1.1`), and then automatically set the _version constraint_ for the module to `^1.1`.

#### Version constraints

The version constraint `^1.1` here tells Composer: Install any version of Token 8.x-1.0 or later, up to _but not including_ Token 8.x-2.0. Refer to our earlier discussion about versions and Composer.

> **Note**: You could also specify an exact version, e.g. `drupal/token:1.1`, and Composer will keep the project pinned to that version no matter what. This isn't necessarily the _best practice_ for Composer, but many projects and teams prefer to manage _Drupal_ dependencies this way, especially if they rely on Drupal's own update mechanisms (instead of using `composer outdated`) to warn when there are new releases.

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
