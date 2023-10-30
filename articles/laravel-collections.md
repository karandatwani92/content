---
Image: https://life-long-bunny.fra1.digitaloceanspaces.com/media-library/production/18/laravel-collections_vtrxcq.png
Title: Leverage Laravel Collections to refactor your codebase
Description: Laravel Collections make arrays more powerful and convenient to work with. This article provides tons of quick tips to instantly make your codebase better.
Published at: 2022-11-09
Modified at: 2023-08-27
Categories: laravel
---

## Introduction

[Laravel Collections](https://laravel.com/docs/collections) are a powerful tool for manipulating arrays. They wrap native PHP array functions, create new useful ones and provide a fluent interface for working with them.

They're also immutable, meaning they do not modify the original array. Instead, they return a new collection with the changes applied.

By using collections, you'll stop wondering about:
- Which comes first? The needle or the haystack?
- Does this array function returns a new array, or modifies the original?
- What's the difference between `array_map()` and `array_walk()`?

And finally, your code will be more readable and easier to maintain.

## Laravel Collections methods I always use

### Transform an array into a collection

An array can be transformed into a collection using the `collect()` helper. It's that simple!

```php
$collection = collect(); // Empty collection.

$collection = collect([1, 2, 3]); // Collection from an array.
```

If you take a look at `collect()`'s source code, you'll see you can also instantiate the `\Illuminate\Support\Collection` class.

```php
use \Illuminate\Support\Collection;

$collection = new Collection([1, 2, 3]); // Object-oriented style.
```

### Transform a collection back into an array

To transform a collection into an array, use the `toArray()` method.

```php
$collection = collect([1, 2, 3]);

$array = $collection->toArray();
```

This isn't necessary most of the time, but it might come in handy from time to time.

### Collections work in foreach loops

The goal here is not to use collections the same way as arrays. But I find it important to mention that collections support foreach loops anyway.

```php
foreach ($collection as $item) {
    //
}
```

You can even get the key like a normal array:

```php
foreach ($collection as $key => $value) {
    //
}
```

Under the hood, they implement the [`IteratorAggregate`](https://www.php.net/manual/en/class.iteratoraggregate.php) interface, which allows objects to be iterated.

### Use the each() method of collections instead of foreach loops

We saw in the previous section that collections support foreach loops. But the most basic and common use case for collections is to replace `foreach` loops using the `each` method:

```php
use App\Models\Post;

$collection = collect(['Foo', 'Bar', 'Baz']);

$collection->each(function ($value, $key) {
    //
});
```

### Merge Laravel collections together

Merging two collections together is super easy, barely an inconvenience.

Say we have some molecules of oxygen and we want to merge them to one molecule of hydrogen.

Collections provide the `merge()` method for that:

```php
$oxygen = ['O', 'O', 'O'];

$hydrogen = ['H', 'H', 'H'];

// Take one molecule of hydrogen and merge them with
// two molecules of oxygen to create H₂O, or water.
$water = $hydrogen->take(2)->merge(
    $oxygen->take(1)
);
```

### Use the filter() method of collections for cleaning up

It is common practice to use temporary variables to store the result of a loop. Even if this is perfectly fine, collections allow you to do better.

```php
$foo = [];

foreach ($bar as $baz) {
    if ($baz->something()) {
        $foo[] = $baz;
    }
}

return $foo;
```

Instead, use the filter method and return the filtered collection.

```php
return $bar->filter(function ($baz) {
    return $baz->something();
});
```

How cool is that?

### Collections have the sum(), average(), min() and max() methods for handling numbers

Collections provide a set of methods to perform everyday mathematical operations on arrays.

Here's how it looks using native PHP arrays:

```php
$numbers = [1, 2, 3, 4, 5];

$sum = 0;

foreach ($numbers as $number) {
    $sum += $number;
}

// $sum is now 15.
```

```php
collect([1, 2, 3, 4, 5])->sum(); // 15
```

Of course, you can do more than add numbers.

```php
$numbers = collect([1, 2, 3, 4, 5]);

$numbers->average(); // 3
$numbers->min(); // 1
$numbers->max(); // 5
```

### Collections use higher order messages to help you write more concise code

[Higher order messages](https://laravel.com/docs/collections#higher-order-messages) are shortcuts that will make your code even more concise. Let me show you by example.

Can you refactor this code to a collection first?

```php
foreach (User::where('foo', 'bar')->get() as $user) {
    $user->notify(new SomeNotification);
}
```

Here's the result:

```php
User::where('foo', 'bar')
    ->get()
    ->each(function (User $user) {
        $user->notify(new SomeNotification)
    });
```

But we can do better.

Using higher-order messages, we can remove the anonymous function and directly access each User model `notify()` method.

That's what I was saying; it feels like sorcery!

```php
User::where('foo,' 'bar')
    ->get()
    ->each
    ->notify(new SomeNotification);
```

### Collections help you say goodbye to unset() with the only() and except() methods

Let's say you only want to keep a few key-value pairs from an array. 

This is how you'd do it in native PHP.

```php
$original = [
  'foo' => 'foo',
  'bar' => 'bar',
  'baz' => 'baz',
];

$keep = ['foo', 'bar'];

$new = array_intersect_key($original, array_flip($keep));
```

With Collections, it only takes one line of code thanks to the `only()` method.

```php
$new = collect($original)->only('foo', 'bar');
```

Pretty damn cool, right?

Now, imagine we need to do the opposite. We want to exclude some key-value pairs from the array.

This is how you could do it using native PHP array functions.

```php
$original = [
    'foo' => 'foo',
    'bar' => 'bar',
    'baz' => 'baz',
];

unset($original['foo'], $original['bar']);
```

And now, here's how to do it with Collections using the `except()` method:

```php
$new = collect($original)->except('foo', 'bar');
```

### dump() and dd() are built into collections

Instead of encapsulating your collection inside a `dd()` helper, you can call the `dd()` method that's built-in collections.

```php
// Before.
dd($collection->where('foo', 'bar'));

// After.
$collection->where('foo', 'bar')->dd();
```

If you don't want to stop code execution, you want also use `dump()`.

### Collections have a handy random() method

When writing factories, did you ever want to randomly pick a value from a given set? `random()` is the perfect method for this job.

```php
class FooFactory extends Factory
{
    public function definition()
    {
        return [
		    'foo' => collect(['Foo', 'Bar', 'Baz'])
			    ->shuffle() // [tl! --]
		        ->first(), // [tl! --]
			    ->random(), // [tl! ++]
        ];
    }
}
```

### Collections help you get rid of temporary variables with map()

This is something we all did:
1. Get data as an array;
2. Loop over it with `foreach`, transform each value and push them into a temporary array;
3. Return the temporary array.

I don't know for you, but the temporary variable always bothered me. I can illustrate it like so:

```php
namespace App\Twitter;

use App\Twitter\Tweet;
use Illuminate\Support\Facades\Http;

class Client
{
    public function tweets()
	{
		$tweets = Http::get('https://api.twitter.com/2/users/me')
		    ->json('tweets');

		$tmp = [];

		foreach ($tweets as $tweet) {
			$tmp[] = new Tweet(...$tweet);
		}

		return $tmp;
	}
}
```

With Collections, we get rid of the temporary variable and make the code beautifully shorter.

```php
namespace App\Twitter;

use App\Twitter\Tweet;
use Illuminate\Support\Facades\Http;

class Client
{
    public function tweets()
		{
		    return Http::get('https://api.twitter.com/2/users/me')
			      ->collect('tweets')
		        ->map(fn ($value) => new Tweet(…$value));
    }
}
```

### Is your collection empty() or notEmpty(), that is the question

Instead of sticking to our old habit of using `count()` to check whether a Collection is empty or not, Laravel Collections provide a way more readable way of doing it.

```php
if (! $collection->count()) { // [tl! --]
if ($collection->isEmpty()) { // [tl! ++]
    // Do something if the collection is empty.
}
```

```php
if ($collection->count()) { // [tl! --]
if ($collection->isNotEmpty()) { // [tl! ++]
    // Do something if the collection is not empty.
}
```

It reads like English, and code like this makes everyone's life easier.

### Extend collections with your own methods

Among with other classes in Laravel, Collections are "macroable". It means you can extend them with your own methods at runtime.

It all starts in the boot method of a Service Provider.

*app/Providers/AppServiceProvider.php*:

```php
use Illuminate\Support\Str;
use Illuminate\Support\Collection;

class AppServiceProvider extends ServiceProvider
{
	public function boot()
	{
		Collection::macro('pluralize', function () {
			return $this->map(function ($value) {
				 return Str::plural($value);
			});
		});
	}
}
```

Then, your custom methods will be available all over the codebase.

```php
$collection = collect(['apple', 'banana', 'strawberry']);

// ['apples', 'bananas', 'strawberries');
$collection->pluralize();
```