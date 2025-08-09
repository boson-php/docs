# WebView

The `WebView` class represents a webview in the Boson application. It provides 
a way to manage html content, including JavaScript scripts, styles, functions, 
and more.

## Main WebView

The `Application::$webview` property provides convenient access to the WebView
instance of the <tooltip term="main window">main window</tooltip>.

> This is a <tooltip term="facade">facade property</tooltip> that internally 
> accesses the webview of the default window inside the window manager.

```php
$app = new Boson\Application();

// Access the main WebView
$webview = $app->webview;
```

> Behavior is similar to the <tooltip term="main window">main window</tooltip>:
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
> <a href="webview-events.md#navigated-event">corresponding event</a> that can be 
> subscribed to using the <a href="events.md">event system</a>.


## HTML Content

The `WebView::$html` property allows you to load custom
html content without navigation to any address.

```php
$webview->html = '<button>Do Not Click Me!</button>';
```

> Direct HTML loading implemented via `data:` protocol is an 
> <a href="https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts">insecure context</a>
> which does NOT allow the implementation of 
> <a href="https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts/features_restricted_to_secure_contexts">some functionality</a>.
{.warning}

> WebView navigation also fires a 
> <a href="webview-events.md#navigated-event">corresponding event</a> that can be 
> subscribed to using the <a href="events.md">event system</a>.


## State

The `WebView::$state` property provides access to the current state 
of the webview.

```php
// Check if the webview is loading
if ($webview->state === Boson\WebView\State::Loading) {
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


## Configuration

The webview configuration class `Boson\WebView\WebViewCreateInfo` is
<tooltip term="optional class">optional</tooltip> and serves as a convenient way to
define default settings for webview of main window instance.


### Default User Agent

Sets a custom user agent string for the WebView.

```php
$webviewConfig = new Boson\WebView\WebViewCreateInfo( 
    userAgent: 'MyCustomUserAgent/1.0',
);
```


### Storage (Persistent Client Settings)

Configures persistent storage settings for the WebView.

If storage is enabled, persistent settings will be saved between
different app executions, such as saved passwords, history, and other data
shared across browsers.

```php
$webviewConfig = new Boson\WebView\WebViewCreateInfo( 
    storage: '/path/to/storage', // Defaults to false (disabled)
);
```

The storage value can take on different types:
- `false` - The storage will be **disabled**.
- `null` - The storage will be **enabled** and directory will be
  determined automatically based on the current working directory.
- `string` - The storage will be **enabled** and directory will be
  based on the passed argument.


### Extra Flags

Sets additional WebView configuration flags.

```php
$webviewConfig = new Boson\WebView\WebViewCreateInfo( 
    flags: [
        'enable-javascript' => false,
    ],
);
```

These are additional **platform-dependent** launch flags and their behavior may
differ on different platforms.

**Windows/WebView2**

For WebView2 list of all available flags can be
[found on MSDN](https://learn.microsoft.com/en-us/dotnet/api/microsoft.web.webview2.core.corewebview2environmentoptions.additionalbrowserarguments)
and [chromium.org](https://www.chromium.org/developers/how-tos/run-chromium-with-flags)
```php
$webviewConfig = new Boson\WebView\WebViewCreateInfo(flags: [
    '--disable-features' => ['feature1', 'feature2'],
    '--do-something',
]);
```

**Linux/GTK4**

For WebkitGTK list of all available flags can be
[found at webkitgtk.org](https://webkitgtk.org/reference/webkitgtk/stable/class.Settings.html#properties)
```php
$webviewConfig = new Boson\WebView\WebViewCreateInfo(flags: [
    'enable-javascript' => false,
]);
```

**macOS/WebKit**

For WKWebView list of all available flags can be
[found at developer.apple.com](https://developer.apple.com/documentation/webkit/wkwebviewconfiguration)
```php
$webviewConfig = new Boson\WebView\WebViewCreateInfo(flags: [
     'upgradeKnownHostsToHTTPS' => true,
     'defaultWebpagePreferences.allowsContentJavaScript' => false,
     'preferences.minimumFontSize' => 10,
     'applicationNameForUserAgent' => 'Boson',
]);
```


### Context Menu

Controls whether the default context menu (right mouse button) is enabled.

```php
$webviewConfig = new Boson\WebView\WebViewCreateInfo( 
    contextMenu: true, // Default is false
);
```


### Dev Tools

Enables or disables developer tools for the WebView.

```php
$webviewConfig = new Boson\WebView\WebViewCreateInfo( 
    devTools: true, // Default is null
);
```

The developer tools settings can take one of the following values:
- `true` - Enables developer tools window.
- `false` - Disables developer tools window.
- `null` - Depends on the application `debug` settings.
  Developer tools will be enabled if debug is enabled and vice versa.


## WebView Events

The webview will automatically emit the following events (and intentions)
during its lifecycle.

To subscribe to events, you can use direct access to the
<a href="events.md#event-listener">event listener</a>.

```php
$webview = $app->webview;

$webview->addEventListener(Event::class, function (Event $e): void {
    var_dump($e);
});
```

The webview instance also supports a more convenient and simple way of
registering events using the `on()` method.

```php
$webview->on(function (Event $event): void {
    var_dump($event);
});
```

> More information about events can be found in the <a href="events.md">events
> documentation</a>.
{.note}

### Dom Ready Event

An `Boson\WebView\Event\WebViewDomReady` event fired after webview DOM has been
loaded and ready to work.

```php
class WebViewDomReady<WebView>
```

### Favicon Changing Intention

An `Boson\WebView\Event\WebViewFaviconChanging` intention to change the
window's icon from loaded HTML content.

**Linux/GTK4**

> An intention does not change the windows icon.
>
> Icon change intention has no effect.
{.warning}

**macOS/WebKit**

> Provides no way to access favicons.
>
> Icon change intention has no effect.
{.warning}

```php
class WebViewFaviconChanging<WebView>
```

> If intention is cancelled, the window icon has not been changed.

### Favicon Changed Event

An `Boson\WebView\Event\WebViewFaviconChanged` event fired after the window's
icon has been changed and the `Boson\WebView\Event\WebViewFaviconChanging`
intention has not been cancelled.

```php
class WebViewFaviconChanged<WebView>
```

### Message Received Event

An `Boson\WebView\Event\WebViewMessageReceived` intention to
<a href="https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage">receive message</a>
from the webview.

```php
class WebViewMessageReceived<WebView> 
{
    public readonly string $message;

    public function ack(): void;
}
```

- `$message` - Received message string value.

> The `ack()` method marks the message as accepted and processed.
{.note}

> The `stopPropagation()` method works in a similar way to
> `ack()`, but is not recommended due semantic conflicts.

### Navigating Intention

An `Boson\WebView\Event\WebViewNavigating` intention to change the
webview's URL (navigating to passed URL).

```php
class WebViewNavigating<WebView> 
{
    public readonly string $url;
    public readonly bool $isNewWindow;
    public readonly bool $isRedirection;
    public readonly bool $isUserInitiated;
}
```

- `$url` - The URL address by which navigation occurs.
- `$isNewWindow` - Navigation to the specified URL should have been made
  in a new window.
- `$isRedirection` - Navigation to the specified URL occurs using a redirect.
- `$isUserInitiated` - Navigation to the specified URL does not occur
  automatically, but is initialized by the user.

> If intention is cancelled, the URL navigation will be cancelled.

### Navigated Event

An `Boson\WebView\Event\WebViewNavigated` event fired after the webview has been
navigated to the given URL and the `Boson\WebView\Event\WebViewNavigating`
intention has not been cancelled.

```php
class WebViewNavigated<WebView> 
{
    public readonly string $url;
}
```

- `$url` - The URL address by which navigation occurs.

### Title Changing Intention

An `Boson\WebView\Event\WebViewTitleChanging` intention to change the
window title from loaded HTML content.

```php
class WebViewTitleChanging<WebView> 
{
    public readonly string $title;
}
```

- `$title` - Expected title string to be set.

> If intention is cancelled, then the window title has not been changed.

### Title Changed Event

An `Boson\WebView\Event\WebViewTitleChanged` event fired after window title has
been changed and the `Boson\WebView\Event\WebViewTitleChanging`
intention has not been cancelled.

```php
class WebViewTitleChanged<WebView> 
{
    public readonly string $title;
}
```

- `$title` - Title string from the HTML content of the webview.