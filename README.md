
# Settings
Super simple key/value settings for Laravel 5.4+ that natively supports [encryption](#encryption) and [multi-tenancy](#multi-tenancy).

* [Installation](#installation)
* [Upgrading](#upgrading)
* [Usage](#usage)
  * [Set new setting](#set-new-setting)
  * [Get all settings](#get-all-settings)
  * [Get single setting](#get-single-setting)
  * [Get certain setting (via array)](#get-certain-settings)
  * [Check if a setting is set](#check-if-a-setting-is-set)
* [Encryption](#encryption)
* [Multi-tenancy](#multi-tenancy)
* [Disable cache](#disable-cache)
  
  
## Installation
This package can be used in Laravel 5.4 or higher.

You can install the package via composer:

``` bash
composer require hackeresq/laravel-settings
```

In Laravel 5.5+ the service provider will automatically get registered and you can skip this step. In older versions of the framework just add the service provider in `config/app.php` file:

```php
'providers' => [
    // ...
    hackerESQ\Settings\SettingsServiceProvider::class,
];
```
The same is true for the alias. If you're running Laravel 5.5+, you can also skip this step. In older versions of the framework just add the alias in `config/app.php` file:

```php
'aliases' => [
    // ...
    'Settings' => hackerESQ\Settings\Facades\Settings::class,
];
```
#### Migrating
You can publish [the migration](https://github.com/hackerESQ/settings/blob/master/database/migrations/create_settings_table.php) and [config](https://github.com/hackerESQ/settings/blob/master/config/settings.php) files, then migrate the new settings table all in one go using:

```bash
php artisan vendor:publish --provider="hackerESQ\Settings\SettingsServiceProvider" --tag=migrations && php artisan vendor:publish --provider="hackerESQ\Settings\SettingsServiceProvider" --tag=config && php artisan migrate
```

<b>Success!</b> laravel-settings is now installed!

## Upgrading
We may have updated config files or migrations, so see the [Migrating](#migrating) section.

Also note that there have been breaking changes recently (e.g. from v2.1.0 to v3.0.0), so you may need to manually adjust the migrations.

## Usage

Settings can be accessed using the easy-to-remember Facade, "Settings."

### Set new setting
You can set new settings using the "set" method, which accepts an associative array of one or more key/value pairs.

```php
Settings::set(['firm_name'=>'new']);
```

<b><mark>For security reasons,</mark> this will first check to see if such a setting key exists in your "settings" table or in the cache. If such a key exists, it will update the key to the new value. If the key does not exist, <i>it will disregard the change.</i> </b> If you wish to force set a new setting, you should pass an array for the second parameter like so:

```php
Settings::set(['firm_name'=>'new'],['force'=>true]);
```

Alternatively, you can globally override this security functionality within the [config/settings.php](https://github.com/hackerESQ/settings/blob/master/config/settings.php) file for this package:

```php
'force' => true
```

If you will be setting variables in the local or development environment and always want to force set settings in that environment, you can do something like this:

```php
'force' => env('APP_ENV') == 'local' ? true : false;
```

### Get all settings
If no parameters are passed to the "get" method, it will return an array of all settings:

```php
Settings::get();
```

### Get single setting
You can return a single setting by passing a single setting key:

```php
Settings::get('firm_name');
```

### Get certain settings
You can also return a list of specified settings by passing an array of setting keys:

```php
Settings::get(['firm_name','contact_types']);
```

### Check if a setting is set
Sometimes you can't know if a setting has been set or not (i.e. boolean settings that will return false if the setting does not exist, but also if the setting has been set to false).

```php
Settings::has(['dark_theme']);
```

## Encryption

You can define keys that should be encrypted automatically within the [config/settings.php](https://github.com/hackerESQ/settings/blob/master/config/settings.php) file. To do so, add the keys as such:

```php
'encrypt' => [
        'twitter_client_id',
        'twitter_client_secret',
    ],
```

## Multi-tenancy
This package can be used in a multi-tenant environment. The [set](#set-new-setting), [get](#get-all-settings), and [has](#check-if-a-setting-is-set) methods all accept an array as the second parameter. This array can contain a 'tenant' attribute, like this:

```php
Settings::get('firm_name',['tenant'=>'example-tenant']);
```

The 'tenant' attribute passed in an array to the second parameter above can be alphanumeric. Although 'tenant' is not strictly typed, and will be passed to the database query as a string. 

If you are using the [get](#get-all-settings) method without a first parameter (which will returns all settings), you must pass 'null' as the first parameter:

```php
Settings::get(null,['tenant'=>'example-tenant']);
```

## Disable cache
Depending on your use case, you may like to disable the cache (enabled by default). Disable the cache by modifying the [config/settings.php](https://github.com/hackerESQ/settings/blob/master/config/settings.php) file as such:

```php
'cache' => false
```

## Finally

### Contributing
Feel free to create a fork and submit a pull request if you would like to contribute.

### Bug reports
Raise an issue on GitHub if you notice something broken.

