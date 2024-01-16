---
Image:
Title: Secure your REST API in 5 minutes with Laravel Sanctum
Description: Quickly secure a REST API using Laravel Sanctum by letting your users generate tokens.
Canonical: 
Audio:
Published at: 2024-01-16
Modified at:
Categories: laravel
---

## Introduction to Laravel Sanctum and how it helps securing REST APIs

[Laravel Sanctum](https://laravel.com/docs/sanctum) is a package for Laravel that provides a simple way to secure your REST API. For instance, in case you want your users to be able to build services top of your application.

That being said, the official documentation is extensive and you probably don't have that kind of time. So I hope my tutorial will serve you well.

## Install Laravel Sanctum via Composer

The package now come installed by default in any new Laravel application.

If for some reasons you don't have Laravel Sanctum in your project, install it using Composer:

```bash
composer require laravel/sanctum
```

Once done, publish Sanctum's configuration and migration files:

```bash
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

Finally, run your database migrations:

```bash
php artisan migrate
```

## Issue API tokens to your users

You need to let your users generate tokens to consume your API.

Add the `Laravel\Sanctum\HasApiTokens` trait in your `User` model:

```php
namespace App\Models;

//
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens;

    //
}
```

You can issue a token using the `createToken` method:

```php
$token = $user->createToken('token-name')->plainTextToken;
```

Make sure to let the user know that the token is only shown once. If they lose it, they'll have to generate a new one.

## Protect your REST API routes with Sanctum's auth guard

To secure your API routes, use the `sanctum` guard. This ensures that all incoming requests are authenticated:

```php
Route::middleware('auth:sanctum')
    ->get('/api/user', function (Request $request) {
        return $request->user();
    });
```

## Manage your users' API tokens

Managing tokens is crucial for security. To revoke them, use:

```php
// Revoke all tokens...
$user->tokens()->delete();

// Revoke a specific token...
$user->tokens()->where('id', $tokenId)->delete();
```

## Conclusion

Securing your REST API with Laravel Sanctum is an effective way to manage authentication and protect your data without overcomplicating everything.

There's a lot more to Laravel Sanctum and I encourage your to go read the [official documentation](https://laravel.com/docs/sanctum).