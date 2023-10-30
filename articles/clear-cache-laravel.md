---
Image: https://life-long-bunny.fra1.digitaloceanspaces.com/media-library/production/4/crazy-monitors-guy_ru8pgz.jpg
Title: How to clear the Laravel's cache in a nutshell
Description: When in doubt, clear the cache. In this article, you'll learn about how to clear every cache Laravel uses.
Published at: 2022-09-10
Modified at: 2023-08-03
Categories: laravel
---

## Introduction

**To clear the cache in Laravel, run `php artisan optimize:clear`. This works no matter which cache driver you are using.**

Now that we got this out of the way, let me remind you that Laravel has many kinds of caches. The framework offers a command for every variety of cache that you can use to enjoy a more granular level of control.

## Clear all caches in Laravel

As we saw, the one-stop solution to clear the cache in Laravel is the command `php artisan optimize:clear`, which clears the following caches:

- **The config cache.**
- **The bootstrap cache.**
- **The auto-discovered events cache.**
- **The application cache.**
- **The routes cache.**
- **The views cache.**

## Clear Laravel's application cache

To clear Laravel's application cache, run `php artisan cache:clear`. Whether you are using files, Redis, or memcached, it will be wiped clean.

You can also remove one particular value from the cache using `php artisan cache:forget <key> [store]`. That's handy when you're trying to fix something without disrupting everything else.

## Clear Laravel's config cache

To clear Laravel's config cache, run `php artisan config:clear`. The *bootstrap/cache/config.php* file will be deleted, and your fresh config settings will take over.

## Clear Laravel's auto-discovered events cache

To clear Laravel's auto-discovered events cache, run `php artisan event:clear` will delete the *bootstrap/cache/events.php* file. Now Laravel can discover all your shiny new listeners.

Learn more about [Laravel's automatic event discovery](https://laravel.com/docs/9.x/events#event-discovery).

## Clear Laravel's routes cache

To clear Laravel's routes caches, run `php artisan route:clear`. Laravel will remove *bootstrap/cache/routes-v7.php*. Your new routes are now live and ready to be explored.

## Clear Laravel's scheduled tasks cache

To clear Laravel's scheduled tasks cache, run `php artisan schedule:clear-cache` to wipe the slate clean.

Here's a word of caution: Unless you have good reasons, it's not advised to run this command in a production environment. Want to know why? Check out Laravel's guide on [preventing task overlaps](https://laravel.com/docs/9.x/scheduling#preventing-task-overlaps).

## Clear Laravel's views cache

To clear Laravel's views cache, run `php artisan view:clear`. The framework will empty the content of *storage/views*.

## Bonus: turn off Laravel's application cache

To turn off Laravel's application cache, change the `CACHE_DRIVER` environment variable to `null`.

```
CACHE_DRIVER=null
```

This action doesn't clear the cache but prevents anything from being cached or being retrieved from it.