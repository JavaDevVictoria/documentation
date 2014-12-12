# Symfony Migration Guide
This guide is intended to help migrate a Symfony app deployed on [dotCloud], to the [Next dotCloud] PaaS.

Please, read first the [PHP & PHP Worker Migration Guide] which explains some basic PHP migration steps.

Migrating your Symfony application is pretty straightforward and involves two steps:

* Set the approot to the `web` directory
* Database configuration

## Set the approot to the `web` directory
Symfony needs to set the approot to the `web`. You can get this done by creating a file `.buildpack/apache/conf/symfony.conf` in the applications root directory with the content:
~~~xml
DocumentRoot "/app/code/web"

<Directory "/app/code/web">
    AllowOverride All
    Options SymlinksIfOwnerMatch
    Order Deny,Allow
    Allow from All
</Directory>
~~~
Symfony provides three `.htaccess` files in `/web`, `/app` and `/src`. To increase the performance you should merge the content of those files into the `.buildpack/apache/conf/symfony.conf` file. Paste the content from `/web/.htaccess` beneath the `<Directory "/app/code/web">` directive. To get webserver compliance you need to remove all comments (whitespaces before the comment "#" character are treated as error)

Additional, create a new `Directory` directives: `<Directory "/app/code/app">` and `<Directory "/app/code/src">` with:
~~~xml
<Directory "/app/code/app">
    Deny from All
</Directory>

<Directory "/app/code/src">
    Deny from All
</Directory>
~~~

## Database configuration
Add the [MySQLs Add-on] to your deployment and migrate your data - check our [mysql migration guide] for any help.

Symfony's database configuration is set in `/app/config/config.yml`. So it is a bit tricky to get the database configuration from the credentials file. Create a file `/app/config/credentials.php` with:
~~~php
<?php
if (isset($_ENV['CRED_FILE'])) {
    $string = file_get_contents($_ENV['CRED_FILE'], false);
    $creds = json_decode($string, true);
    $database_host = $creds["MYSQLS"]["MYSQLS_HOSTNAME"];
    $database_name = $creds["MYSQLS"]["MYSQLS_DATABASE"];
    $database_user = $creds["MYSQLS"]["MYSQLS_USERNAME"];
    $database_password = $creds["MYSQLS"]["MYSQLS_PASSWORD"];
} else {
    $database_host = 'localhost';
    $database_name = '<local_symfony_database_name>';
    $database_user = '<local_symfony_database_user>';
    $database_password = '<local_symfony_database_password>';
}
$container->setParameter('database_driver', 'pdo_mysql');
$container->setParameter('database_port', 3306);
$container->setParameter('database_host', $database_host);
$container->setParameter('database_name', $database_name);
$container->setParameter('database_user', $database_user);
$container->setParameter('database_password', $database_password);
?>
~~~

Then you need to import this file in the `/app/config/config.yml` as resource:
~~~yaml
imports:
    - { resource: parameters.yml }
    - { resource: security.yml }
    - { resource: credentials.php }
...
~~~

[dotCloud]: https://www.dotcloud.com/
[Next dotCloud]: https://next.dotcloud.com/
[PHP & PHP Worker Migration Guide]: https://next.dotcloud.com/dev-center/guides/migration-guides/php-basic-use
[MySQLs Add-on]: https://next.dotcloud.com/add-ons/mysqls
[mysql migration guide]: https://next.dotcloud.com/dev-center/guides/migration-guides/migrating-mysql-services.md