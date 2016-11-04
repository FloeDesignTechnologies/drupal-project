# Composer template for Drupal projects hosted on Pantheon

This project template should provide a kickstart for
 - managing your local developement environment with Docker
 - managing your site dependencies with [Composer](https://getcomposer.org/)
 - hosting your site on Pantheon


## Usage

First you need to install [docker](http://www.docker.com/products/docker),
[docker-compose](https://docs.docker.com/compose/install/).

After that you can create the project:

```
docker run --rm -v $(pwd):/var/www/html drupaldocker/php:7-cli composer create-project floe/drupal-project  some-dir --stability dev --no-interaction
```

Then `cd some-dir`, `cp .env.dist .env` and start working on the
project.

The `composer create-project` command passes ownership of all files to
the project that is created. You should create a new git repository, 
update the README.me and composer.json file, then commit all files not
excluded by the .gitignore file.
 
## What does the template do?

When installing the given `composer.json` some tasks are taken care of:

* Docker containers to work on the project are defined in the 
  `docker-compose.yml` file (see below for details).
* _Aliases_ for the most used commands to run inside the Docker
  containers are provided in the `bin` directory. It is suggested to put
  `bin` in your `PATH` while working on the project. The recommended way
  is to use [direnv](http://direnv.net/).
* Drupal will be installed in the `web`-directory.
* Autoloader is implemented to use the generated composer autoloader in `vendor/autoload.php`,
  instead of the one provided by Drupal (`web/vendor/autoload.php`).
* Modules (packages of type `drupal-module`) will be placed in `web/modules/contrib/`
* Theme (packages of type `drupal-theme`) will be placed in `web/themes/contrib/`
* Profiles (packages of type `drupal-profile`) will be placed in `web/profiles/contrib/`
* Creates default writable versions of `settings.php` and `services.yml`.
* Creates `sites/default/files`-directory.
* Latest version of drush is installed locally for use at `vendor/bin/drush`.
* Latest version of DrupalConsole is installed locally for use at `vendor/bin/drupal`.

## Docker containers



## Updating Drupal Core

This project will attempt to keep all of your Drupal Core files up-to-date; the 
project [drupal-composer/drupal-scaffold](https://github.com/drupal-composer/drupal-scaffold) 
is used to ensure that your scaffold files are updated every time drupal/core is 
updated. If you customize any of the "scaffolding" files (commonly .htaccess), 
you may need to merge conflicts if any of your modfied files are updated in a 
new release of Drupal core.

Follow the steps below to update your core files.

1. Run `composer update drupal/core --with-dependencies` to update Drupal Core and its dependencies.
1. Run `git diff` to determine if any of the scaffolding files have changed. 
   Review the files for any changes and restore any customizations to 
  `.htaccess` or `robots.txt`.
1. Commit everything all together in a single commit, so `web` will remain in
   sync with the `core` when checking out branches or running `git bisect`.
1. In the event that there are non-trivial conflicts in step 2, you may wish 
   to perform these steps on a branch, and use `git merge` to combine the 
   updated core files with your customized files. This facilitates the use 
   of a [three-way merge tool such as kdiff3](http://www.gitshah.com/2010/12/how-to-setup-kdiff-as-diff-tool-for-git.html). This setup is not necessary if your changes are simple; 
   keeping all of your modifications at the beginning or end of the file is a 
   good strategy to keep merges easy.


## FAQ

### How can I apply patches to downloaded modules?

If you need to apply patches (depending on the project being modified, a pull 
request is often a better solution), you can do so with the 
[composer-patches](https://github.com/cweagans/composer-patches) plugin.

To add a patch to drupal module foobar insert the patches section in the extra 
section of composer.json:
```json
"extra": {
    "patches": {
        "drupal/foobar": {
            "Patch description": "URL to patch"
        }
    }
}
```
