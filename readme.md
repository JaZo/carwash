# Carwash

[![Latest Version on Packagist](https://img.shields.io/packagist/v/swisnl/carwash.svg?style=flat-square)](https://packagist.org/packages/swisnl/carwash)
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE.md)
[![Build Status](https://img.shields.io/github/actions/workflow/status/swisnl/carwash/tests.yml?label=tests&branch=master&style=flat-square)](https://github.com/swisnl/carwash/actions/workflows/tests.yml)

**Carwash** is a data scrubbing utility for **Laravel** applications

## Installation

#### Install via composer
```
composer require swisnl/carwash
```

## Usage
#### 1. Publish default config file
```
php artisan vendor:publish
```

#### 2. Edit config file at `config/carwash.php` for your application

```php
<?php

return [
    'users' => [
        'first_name' => 'firstName',
        'last_name' => 'lastName',
        'email' => 'safeEmail'
    ],
    'addresses' => [
        'street' => 'streetAddress',
        'city' => 'city',
        'zip' => 'postcode'
    ],
   'bios' => [
        'content' => 'sentences:2'   
    ]
];
``` 
**Carwash** uses the fabulous [Faker](https://fakerphp.github.io/) package under the hood to generate replacement data. Please refer to the Faker documentation for a complete list of [available formatters](https://fakerphp.github.io/formatters/).

More generally, the format of the **Carwash** config file is:
```php
<?php

return [
    '[TABLE_NAME]' => [
        '[COLUMN_NAME]' => '[Faker Formatter][:argument1,argument2]'
    ]
];
``` 

#### 3. Run Scrub Command

```
php artisan carwash:scrub
```

### Other
Instead of passing a Faker Formatter as the value for each field in your **Carwash** config file, alternatively
you can set the field value to a Callable that returns the new field value. This closure will receive an instance of
**Faker** and the current value of the attribute being scrubbed.

```php
<?php

return [
    'users' => [
        'name' => function ($faker, $currentValue) {
            return "{$faker->firstName} {$faker->lastName}";
        },
        'bio' => BioFormatter::class
    ]
];

class BioFormatter
{
    public function __invoke($faker)
    {
        return $faker->sentences(42);
    }
}
```

Callables can also replace entire table configurations allowing full control over what data is scrubbed and how.

```php
<?php

return [
    'users' => function ($faker, $user) {
        $firstName = $faker->firstName;
        $lastName = $faker->lastName;
    
        return [
            'first_name' => $firstName,
            'last_name' => $lastName,
            'email' => $firstName . "." . $lastName. "@" . $faker->safeEmailDomain,
            'phone' => substr($user['phone'], 0, 3) . "-555-" . $faker->randomNumber(4)
        ];
    },
];
```

## Testing

``` bash
$ composer test
```

## Security

If you discover any security related issues, please email security@swis.nl instead of using the issue tracker.

## Credits

This package is a fork of [dansoppelsa/carwash](https://github.com/dansoppelsa/carwash) with updated Laravel support.

- [All Contributors](../../contributors)

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
