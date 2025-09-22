# Bindings API

You can register custom PHP functions to call them from the client.

> This is a **built-in** extension and does not require separate installation.
>
> To disable the extension, remove `Boson\WebView\Api\Bindings\BindingsExtensionProvider`
> from the list of extensions.
>
> More information about extensions can be found on the [relevant documentation
> page](../05.webview/webview-extensions.md).
{.note}

The API is available in the `WebView::$bindings` property.

```php
$app = new Boson\Application();

$app->webview->bindings; // Access to Bindings API
```


## Binding

You can create a function that can be called directly from WebView.

```php
$app = new Boson\Application();

$app->webview->bindings->bind('foo', function () { 
    var_dump('Executed!');
});
```

> WebView also provides a more convenient way (facade method `bind()`) 
> to bind arbitrary PHP function.

> Just use `WebView::bind()` instead of bind method
> from `WebView::$bindings`.
> 
> ```php
> $api = $webview->bindings;
> 
> $api->bind('foo', foo(...));
> ```
> 
> ```php
> //
> 
> $webview->bind('foo', foo(...));
> ```
> 
> In all examples from here on, the short facade method will
> be used to simplify the examples.
>
{.note}

Also, don't forget that PHP has a simple way to pass functions using 
[first class callable](https://www.php.net/manual/en/functions.first_class_callable_syntax.php) 
syntax. Thus, a simple registration of the `var_dump()` function looks like this:

```php
$app = new Boson\Application();

$app->webview->bind('var_dump', var_dump(...));
```

> During registration, an exception `FunctionAlreadyDefinedException` 
> may occur if you are trying to register a function that has 
> already been registered.
{.warning}


### Custom Context

You may have noticed that functions are registered globally, 
which is not always convenient.

To register functions in a context, you can use the dot-syntax, which allows 
you to register a function in a specific JavaScript context.

```php
$app = new Boson\Application();

$app->webview->bind('example.some', $example->some(...));
$app->webview->bind('example.any', $example->any(...));
```

Access to such functions from the client side is also done through a dot.

```javascript
example.some('hello');
example.any('world');
```