# WebView

The `WebView` class represents a webview in the Boson application. It provides 
a way to manage html content, including JavaScript scripts, styles, functions, 
and more.

## Main WebView

The `Application::$webview` property provides convenient access to the WebView
instance of the <span term="main window">main window</span>.

> This is a <span term="facade">facade property</span> that internally 
> accesses the webview of the default window inside the window manager.

```php
$app = new Boson\Application();

// Access the main WebView
$webview = $app->webview;
```

> Behavior is similar to the <span term="main window">main window</spanspan>:
> 
> If you try to access the `$webview` property after the all windows 
> has been closed, a `NoDefaultWindowException` will be thrown.
{.warning}


## URL Navigation

The `WebView::$url` property allows you to load custom
html content from any address (including real ones from internet).

```php
$webview->url = 'https://bosonphp.com';
```

When setting the URL, webview attempts to load data from the specified source, 
but the address may change based on the behavior of the target page (for example, 
triggering scripts or redirects), so the result displays the real address, and
not the one that was set.

```php
$app = new Boson\Application();

$app->webview->url = 'https://github.com/BosonPHP';

// After set the new URL, the navigation has 
// not yet taken place:
//
// string("URL: about:blank\n")
//
echo 'URL: ' . $app->webview->url . "\n";

$app->on(function (WebViewNavigated $e) use ($app): void {
    // The navigation occurs later, for this you 
    // should subscribe to the required event:
    //
    // string("URL: https://github.com/BosonPHP\n")
    //
    echo 'URL: ' . $app->webview->url . "\n";
});
```

> WebView navigation also fires a 
> [corresponding event](../05.webview/webview-events.md#navigated-event) that 
> can be subscribed to using the [event system](../02.architecture/events.md).


## HTML Content

The `WebView::$html` property allows you to load custom
html content without navigation to any address.

```php
$webview->html = '<button>Do Not Click Me!</button>';
```

> Direct HTML loading implemented via `data:` protocol is an 
> [insecure context](https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts)
> which does NOT allow the implementation of 
> [some functionality](https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts/features_restricted_to_secure_contexts).
{.warning}

> WebView navigation also fires a 
> [corresponding event](../05.webview/webview-events.md#navigated-event) that 
> can be subscribed to using the [event system](../02.architecture/events.md).


## State

The `WebView::$state` property provides access to the current state 
of the webview.

```php
// Check if the webview is loading
if ($webview->state === Boson\WebView\WebViewState::Loading) {
    echo "WebView is currently loading content\n";
}
```

This property will contain one of the possible values of
`Boson\WebView\State` enum.

```php
enum State
{
    /**
     * Navigation to a new URL.
     */
    case Navigating;

    /**
     * Data is being loaded from the specified URL.
     */
    case Loading;

    /**
     * Readiness for work with document.
     */
    case Ready;
}
```
