## Laravel MultiAuth

**Laravel**: 5.0, 5.1

This Package extends the default Auth library to allow to login with different accounts.
For example if you want to have a backend and a frontend with a different user.

**Works with the default Laravel 5 AuthController and PasswordController!**

## Installation ##

Firstly you want to include this package in your `composer.json` file,

    "require": {
        "wuifdesign/laravel-multiauth": "0.3.*"
    }

and update or install via composer:

    composer update

Or you can just run:

    composer require wuifdesign/laravel-multiauth

Next you open up `app/config/app.php` and add

    'providers' => [
        WuifDesign\MultiAuth\ServiceProvider::class
    ]

Configuration is pretty easy too, take `app/config/auth.php` with its default values:

    return array(

        'driver' => 'eloquent',
        'model' => 'User',
        'table' => 'users',

        'reminder' => array(
            'email' => 'emails.auth.reminder',
            'table' => 'password_reminders',
            'expire' => 60,
        ),

    );

Now remove the first three options and replace as follows:

    return array(

        'default' => 'user',
        'multi' => array(
            'admin' => array(
                'driver' => 'eloquent',
                'model'  => Admins::class,
            ),
            'user' => array(
                'driver' => 'eloquent',
                'model'  => Users::class,
                'password' => [
                    'email' => 'users.emails.password',
                ]
            )
        ),

        'reminder' => array(
            'email' => 'emails.auth.reminder',
            'table' => 'password_reminders',
            'expire' => 60,
        ),
    );

## Usage ##

Everything is done by using routes. Just add a key "auth" to the route array.

    Route::get('/', array(
        'uses' => function () {
            return 'Hello World';
        },
        'middleware' => [ 'auth' ],
        'auth' => 'admin'
    ));

Now if you call Auth::check() or any other function, it will use the driver and model set in the config for the key "admin".
*If you don't add a "auth" to the route, the "default" type defined in the `app/config/auth.php` will be used.*

If you want to check auth while in a route using a different auth, you can use type($authName) to get the other auth manager.

    Auth::type('admin')->check()

To get the current type used by the route or the default value if you havn't set it in the route use

    Auth::currentType()