Dev-AIS-API
=============

A system that provide Academic Information we name it AIS Academic Information System.

# Clone This Bundle

Go to your working directory with your terminal (command line)

```
$ cd your-working-directory
```

Then run the Git command to clone the bundle, if not yet install Git on your system just run this command (ubuntu):

```

$ sudo apt-get install git
```

for more detail explanation please visit this page:

[Setting up Git](https://help.github.com/articles/set-up-git/)

```
$ git clone https://github.com/theredfoxfire/dev-ais-api.git
```

Wait till the clone process is done.

# Preparing Your Kit

First thing you need is Composer.

Go to your app directory, then run the comand to install Composer

```
$ cd dev-ais-api/

$ curl -sS https://getcomposer.org/installer | php
```
For more information about Composer you may visit the link below

[Getting started with Composer](https://getcomposer.org/doc/00-intro.md)

Ok, after Composer installed on your working directory then update the vendor of the app by run this command

```
$ php composer.phar update
```

See the progress usually it's takes several minutes to be done.

# Setting Up the Web Service

## In case we want to improve our AisUserBundle

```text

dev-ais-api$ git clone https://github.com/theredfoxfire/ais-user-bundle.git src/Ais/UserBundle

```

Once we finished, update our `app/AppKernel.php`

```php
// app/AppKernel.php
use Symfony\Component\HttpKernel\Kernel;
use Symfony\Component\Config\Loader\LoaderInterface;

class AppKernel extends Kernel
{
    public function registerBundles()
    {
        $bundles = array(
            ...
            new Ais\UserBundle\AisUserBundle(),
            ...
        );
        ...
    ...
...

```

In this repo I already set up exactly for ais-user-bundle so in other case if we prefer another AIS bundle please take attention to the following file `app/config/config.yml`, `app/config/routing.yml`, `app/config/security.yml`

Ok, So everything is installed on our kit. To setting up the web service, first thing to do is

update your parameters.yml setting up with your environment

```yaml

# this file is located in app/config/parameters.yml

parameters:
    database_host: 127.0.0.1
    database_port: null
    database_name: your-dbname
    database_user: your-dbusername
    database_password: your-dbpassword
    mailer_transport: smtp
    mailer_host: 127.0.0.1
    mailer_user: null
    mailer_password: null
    secret: 4ec56e086e1a7834c546c1e7f637c3cb2390b21e

```

Update the database structure by run this command:

```
$ php app/console doctrine:database:create

$ php app/console doctrine:schema:update --force
```

Well done! next is we try to inserting dummy user to our database. Just run this command:

```
$ php app/console doctrine:fixtures:load
```

Yes we have one user that ready to taste the API!

by default the user atrribute is:

```
username: user
password: 12345

```

# Start Consuming The API

Before you get continue it's recomended to run app server by type this command:

```
$ php app/console server:run

it will running the app on:

http://127.0.0.1:8000
```

## If everything done, you may find the API doc by access this url:

```
 http://127.0.0.1:8000/service/api/doc
```

## Future read

To feed the dummy data and start sending request please visit the link below.

[AIS-WEB-SERVICE](https://github.com/theredfoxfire/ais-web-service)


You can find out the available url API by type this command:

```
$ php app/console debug:route | grep api
```

## Grab Your Coffee

If you find a typo or miss a thing let me know by vizzlearn@gmail.com
