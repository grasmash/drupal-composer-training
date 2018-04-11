## Agenda

This hands-on guide to using Composer with Drupal was originally created for the DrupalCon Nashville session [How to build a Drupal site with Composer AND keep all of your hair](https://events.drupal.org/nashville2018/sessions/how-build-drupal-site-composer-and-keep-all-your-hair), presented by [Matthew Grasmick](http://matthewgrasmick.com) and [Jeff Geerling](https://www.jeffgeerling.com) on Wednesday, April 11, 2018.

{:.no_toc}

* TOC
{:toc}

## Check System Requirements

To run the example code in this hands-on guide, you need to have **PHP 5.3.2+** installed installed on your computer. Check what version is installed with `php -v`.

We strongly suggest that you use macOS or Linux. Windows users, please [use these instructions to install PHP and Composer](https://www.jeffgeerling.com/blog/2018/installing-php-7-and-composer-on-windows-10), and also install and use [Cmder](http://cmder.net) instead of PowerShell when running commands.

## Install Composer

Follow the instructions below, or follow the [Composer installation instructions](https://getcomposer.org/doc/00-intro.md#system-requirements) and return here when you’re finished.

If you’re a [Homebrew](https://brew.sh) user on macOS, you can use `brew install composer`. If you're on Windows, you can use the [Windows installer](https://getcomposer.org/doc/00-intro.md#installation-windows). Otherwise, execute the following on your command line:

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

> **Want to test this Drupal project locally?**
> 
> If you want to run the Drupal project you just created locally, follow these steps:
> 
>   1. Install [Docker CE](https://store.docker.com/search?type=edition&offering=community).
>   1. Add a copy of Drupal VM to your project: `composer require --dev geerlingguy/drupal-vm-docker`
>   1. Run the local environment: `docker-compose up -d`
>   1. Access http://localhost/, and you should be able to install your new Drupal site (use database name, username, password of `drupal`)!
> 
> **Note**: On Windows, if you get an error like `(Client.Timeout exceeded while awaiting headers)` when running `docker-compose up -d`, go to the Docker Settings, then the Network section, and under 'DNS Server', select 'Fixed' with the `8.8.8.8` server, then click Apply. Once Docker restarts, try running the command again.

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

Basically, Composer looked up the Token project on Drupal.org's Packagist, found that the latest stable version is `1.1` (corresponding to `8.x-1.1`), and then automatically set the _version constraint_ for the module to `^1.1`. Composer also downloaded the Token module, and placed it inside the `modules` directory in your site's document root (the `web/modules` folder) so it can be used by Drupal.

#### Version constraints

The version constraint `^1.1` here tells Composer: Install any version of Token 8.x-1.0 or later, up to _but not including_ Token 8.x-2.0. Refer to our earlier discussion about versions and Composer.

> **Note**: You could also specify an exact version, e.g. `drupal/token:1.1`, and Composer will keep the project pinned to that version no matter what. This isn't necessarily the _best practice_ for Composer, but many projects and teams prefer to manage _Drupal_ dependencies this way, especially if they rely on Drupal's own update mechanisms (instead of using `composer outdated`) to warn when there are new releases.

### Require a theme

Themes work much the same as modules; you just require the theme by name like you would a module:

```
composer require drupal/bootstrap
```

> If you're running the Drupal site locally, you can log in and go to the 'Appearance' section in the Drupal admin, and click on 'Install and set as default' under the Bootstrap theme to start using it.

### Require a dev dependency

There are some things you might want to add which are helpful for _developing_ your website, but aren't necessary (and shouldn't even be installed!) on your production website. For example, many people use [PHP CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer) to help check their PHP code against a set of common coding standards.

To install PHP CodeSniffer—but only in your local development copy of the codebase—require the library and pass the `--dev` flag, like so:

```
composer require squizlabs/php_codesniffer --dev
```

After Composer is finished adding PHP CodeSniffer, look inside `composer.json`; you'll see that it appears under the `require-dev` section, and not under `require` like most of the other dependencies:

```
    "require-dev": {
        "squizlabs/php_codesniffer": "^2.8"
    },
```

> **Warning**: You might have gotten the message `Your requirements could not be resolved to an installable set of packages.` when you run this command, and Composer might have errored and reverted `composer.json` to its original content. If this happened, it's likely due to some other package (usually `drupal/coder`) causing a _dependency conflict_. Congratulations! You can troubleshoot this dependency conflict using the guide later if you'd like—usually you just have to provide a version constraint that fits within the conflict's restrictions, e.g. `composer require squizlabs/php_codesniffer:^2.8.1 --dev`.

> **Note**: If you added Drupal VM to your project earlier, you'll notice it is also in the `require-dev` section, since it was required with the `--dev` flag.

At this point, you can use PHP CodeSniffer to test some code against the Drupal coding standards:

  1. Register the Drupal coder module's Drupal coding standards with `phpcs`:

     ```
     ./vendor/bin/phpcs --config-set installed_paths ../../drupal/coder/coder_sniffer
     ```

  1. Sniff the Token module code, which we added to our codebase earlier:

     ```
     ./vendor/bin/phpcs --standard=Drupal --extensions=php,module,inc,install,test,profile,theme,css,info,txt,md ./web/modules/contrib/token
     ```

> **Note**: On Windows, use the path `vendor\bin\phpcs` (note the backslashes) instead, otherwise you'll get an error message like `'vendor' is not recognized as an internal or external command, operable program or batch file.`

Once PHP CodeSniffer is finished, you should see a report of all the errors found throughout the module. Development tools like PHP CodeSniffer can be very useful in evaluating code quality and fixing problems before you commit them to your codebase!

## Execute project binaries

Many development tools, like PHP CodeSniffer, add 'binaries' to your project. Binaries are small executable programs you can run to do certain things. By default, these binaries are stored inside `vendor/bin` inside your project.

This project already includes a few popular Drupal development binaries, for example:

```
# Run Drush commands with the `drush` binary:
./vendor/bin/drush status

# Run Drupal Console commands with the `drupal` binary:
./vendor/bin/drupal about

# Run PHP CodeSniffer with the `phpcs` binary:
./vendor/bin/phpcs -h
```

> **Note**: On Windows, use the path `vendor\bin\[name of binary]` (note the backslashes) instead, otherwise you'll get an error message like `'vendor' is not recognized as an internal or external command, operable program or batch file.`

> **Note**: To make it easier to run these binaries, you can add the `vendor/bin` directory to your system path. Read more about [Vendor binaries](https://getcomposer.org/doc/articles/vendor-binaries.md) in the Composer documentation.

## Update Drupal core

If you built your project with the Drupal Composer Project, you can update Drupal core to the latest stable version with the following command, taken directly from the Drupal Composer Project documentation on [Updating Drupal Core](https://github.com/drupal-composer/drupal-project#updating-drupal-core):

```
composer update drupal/core webflo/drupal-core-require-dev symfony/* --with-dependencies
```

But in our case, we might want to **update our codebase to the latest Drupal core development release**, so we can test a patch at a code sprint, or work on some code to test it with Drupal's latest version.

To do this with your project, you'll first need to remove the `webflo/drupal-core-require-dev` package, because it locks Drupal core into a stable release version.

So first, remove the package with:

```
composer remove webflo/drupal-core-require-dev --dev
```

(Note that this will also remove any other dependencies that package required which are not required by any other project dependencies.)

Then we can update `drupal/core` to the latest development release:

```
composer require drupal/core:8.6.x-dev
```

After a minute or two, your codebase should be running Drupal 8.6.x-dev. You can verify this in one of two ways:

  1. Run `composer info drupal/core`, and check the `versions` that Composer lists. It should list `* 8.6.x-dev`.
  1. Go to the Status report on the Drupal site in your browser (under Admin > Reports), and verify the 'Drupal Version' under General System Information. It should show `8.6.0-dev`.

## Patch something!

Have you ever heard the _Golden Rule of Drupal Development_, "[Every time you hack core God kills a kitten](https://www.flickr.com/photos/hagengraf/2802915470)"?

Well, with Composer, we can actually bend that rule a little, because we can manage Drupal core and contributed project patches very safely and easily, with the help of the [`cweagans/composer-patches`](https://github.com/cweagans/composer-patches) library. Gone are the days of keeping a folder of random patch files that have to be manually applied with the `patch` command line utility every time you update something—and accidentally forgetting to apply a patch, thus breaking your production website!

You can add patches in the `extra` section of your project's `composer.json` file, like so:

```
    "extra" {
        "patches": {
            "drupal/core": {
              "Clear Twig caches on deploys": "https://www.drupal.org/files/issues/2752961-130.patch"
            }
        }
    }
```

Whenever you install or update `drupal/core`, the patch from [comment #130 in Drupal.org issue #2752961](https://www.drupal.org/project/drupal/issues/2752961#comment-12496589) will automatically be applied. And if you're updating Drupal, and the patch no longer applies, the `composer-patches` library will warn you so you can go back to that Drupal.org issue and look for a newer, compatible version of the patch.

> **Warning**: Even though the `composer-patches` library makes patching easier, you should still use approach patches with caution; a general rule of thumb is to only patch something if it's mission-critical, if you understand everything the patch is doing, and if there's a good chance the patch will be maintained and eventually included in the project so you don't have to use the patch anymore!

## Check into Git

Once you create your Drupal project's codebase, it's a good idea to start tracking the codebase in version control using Git, so you can have a history of changes to your site, and also so you can go back to a safe state if you accidentally break something!

Composer's documentation recommends storing your `composer.lock` file in Git, but _not_ storing the `vendor` directory. Luckily, the Drupal Composer Project creates a `.gitignore` file that uses all the best defaults for your project, so to get started with managing your codebase in Git, create a repository, then commit everything as it is in an initial commit:

```
git init
git add -A
git commit -m "Initial commit of my-project."
```

At this point, you should have all the files that are necessary to replicate your Drupal site codebase in a new Git repository, stored locally on your computer, in the `master` branch.

### Create a 'build' and deploy to cloud hosting

Now, what happens when you want to put your Drupal site on cloud hosting somewhere, like Pantheon, Acquia, or some other provider? If you look at what's actually stored in Git, there's no `vendor` folder. There also aren't any contributed modules or other bits and pieces that Composer installs when you run `composer install`.

Your Drupal project won't run very well without these things!

So, when you want to deploy your project to a production environment, you need to 'build' the codebase specifically for production. One of the best advantages to doing this (as opposed to just committing everything you have locally) is that you can:

  - Exclude `require-dev` dependencies (which can introduce security and performance risks if available in production).
  - Remove pesky metadata that's not needed in production (e.g. `.git` folders for dependencies).
  - Add other build commands to do things like optimize images, compile CSS, and more! (That's a more advanced topic for another day).

Before we can deploy the project, we need to _build_ it for production use, specifically. And to do that, we'll create a separate but parallel `master-build` branch:

```
git checkout -b master-build
```

Now that we're on this branch, we can use Composer and other commands to clean up the 'build artifact' that we want to eventually deploy to our production environment:

```
composer install --no-dev --optimize-autoloader --prefer-dist
```

When you run this command, you might notice Composer _removes_ a bunch of dependencies. This is a good thing—all of those dependencies are only needed when doing local development. Once you switch back to your `master` branch and want to do development work again, you can go ahead and run `composer install` without all these options and it will reinstall your `require-dev` dependencies.

Next up, we should remove all the unneeded Git repositories that may exist in some dependencies which are installed directly from GitHub:

```
find 'vendor' -type d | grep '\.git' | xargs rm -rf
find 'web' -type d | grep '\.git' | xargs rm -rf
```

Now, we will _forcefully_ add the directories and files required to run our Drupal site, even though they are in the `.gitignore` file, since we will need them in the production environment on the `master-build` branch:

```
git add -f 'web/core'
git add -f 'web/modules/contrib'
git add -f 'web/themes/contrib'
git add -f 'web/profiles/contrib'
git add -f 'web/libraries'
git add -f 'vendor'
```

At this point, if you run `git status`, you'll see a large number of new files ready to be added. Go ahead and add them, and then make a commit to the `master-build` branch:

```
git add -A
git commit -m "Production build artifact for 1.0.0 release."
```

At this point, the `master-build` branch can be safely deployed to any cloud hosting environment.

> **Note**: For some web hosts, e.g. Pantheon, you might need to add some extra configuration (e.g. add `web_docroot: true` to your `pantheon.yml` file) to tell the web host that the Drupal codebase files are inside the `web/` subdirectory.

## Advanced usage

There are other advanced ways to use Composer to manage your codebase you can attempt if you have time:

  1. Adding a third party Node.js library using [Asset Packagist](https://asset-packagist.org). See a [helpful guide here](https://github.com/drupal-composer/drupal-project/issues/278#issuecomment-310141272).
  1. Speed up Composer package installation by requiring the [`hirak/prestissimo`](https://github.com/hirak/prestissimo) Composer plugin.
