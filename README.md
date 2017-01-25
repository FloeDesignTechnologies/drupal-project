# Composer template for Drupal 8 projects hosted on Pantheon and using Docker for local development environment

__⚠️ Experimental project__

This project template should provide a kickstart for

* managing your local development environment with Docker
* managing your site dependencies with [Composer](https://getcomposer.org/)
* hosting your site on Pantheon


## Usage

First you need to install [docker](http://www.docker.com/products/docker),
and [docker-compose](https://docs.docker.com/compose/install/). If using
OS X, [install GNU coreutils](http://macappstore.org/coreutils/) or any
other implementation of the `realpath` command.

After that you can create the project:

```
docker run --rm -v $(pwd):/var/www/html -v $HOME/.composer/cache:/composer/cache:rw drupaldocker/php:7-cli composer create-project --stability dev --ansi floe/drupal-project some-dir 8.x 
```

Then ```sudo chown -R `id -u`:`id -g` some-dir && cd some-dir && sed "s/^USER_UID=.*/USER_UID=`id -u`/" .env.dist  > .env```
and start working on your project.

Use `docker-compose` to run applications inside the configured Docker
containers:

* `docker-compose run php-cli bash`: Enter a shell to execute cli
  applications (Composer, Drush, Drupal Console, etc.)
* `docker-compose up web`: Start a web server inside its own Docker
  container, accessible as http://localhost.
  
_Aliases_ for the most used commands to run inside the Docker containers
are provided in the `bin` directory. It is suggested to put `bin` in
your `PATH` while working on the project. The recommended way is to use
[direnv](http://direnv.net/)
  
See the `docker-compose` file for details of the configured containers. 

The `create-project` command above passes ownership of all files to
the project that is created. You should create a new git repository, 
update the README.me and composer.json file, then commit all files not
excluded by the .gitignore file.
 
## What does the template do?

* Docker containers to work on the project are defined in the 
  `docker-compose.yml` file.
* _Aliases_ for the most used commands to run inside the Docker
  containers are provided in the `bin` directory.
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
