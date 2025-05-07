# WebView

<show-structure for="chapter" depth="2"/>

The `WebView` class represents a webview in the Boson application. It provides 
a way to manage html content, including JavaScript scripts, styles, functions, 
and more.

## Main WebView

The `Application::$webview` property provides convenient access to the WebView
instance of the <tooltip term="main window">main window</tooltip>.

<tip>
This is a <tooltip term="facade">facade property</tooltip> that internally 
accesses the webview of the default window inside the window manager.
</tip>

```php
$app = new Boson\Application();

// Access the main WebView
$webview = $app->webview;
```

<warning>
Behavior is similar to the <tooltip term="main window">main window</tooltip>:

If you try to access the <code>$webview</code> property after the all windows 
has been closed, a <code>NoDefaultWindowException</code> will be thrown.
</warning>


## HTML Content

The <code>WebView::$html</code> property allows you to load custom 
html content without navigation to any address.

```php
$webview->html = '<button>Do Not Click Me!</button>';
```

<warning>
The <code>WebView::$html</code> property is <b>write-only</b> and cannot be read.
</warning>

<warning>
Direct HTML loading implemented via the <code>data:</code> protocol and marks an 
<a href="https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts">insecure context</a>
which does NOT allow the implementation of 
<a href="https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts/features_restricted_to_secure_contexts">some functionality</a>.
</warning>


<tip>
WebView navigation also fires a 
<a href="webview.md#navigated-event">corresponding event</a> that can be 
subscribed to using the <a href="events.md">event system</a>.
</tip>


## URL Navigation

The <code>WebView::$url</code> property allows you to load custom
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

$app->events->addEventListener(WebViewNavigated::class, function () use ($app) {
    // The navigation occurs later, for this you 
    // should subscribe to the required event:
    //
    // string("URL: https://github.com/BosonPHP\n")
    //
    echo 'URL: ' . $app->webview->url . "\n";
});
```

<tip>
WebView navigation also fires a 
<a href="webview.md#navigated-event">corresponding event</a> that can be 
subscribed to using the <a href="events.md">event system</a>.
</tip>


## State

The <code>WebView::$state</code> property provides access to the current state 
of the webview.

```php
// Check if the webview is loading
if ($webview->state === Boson\WebView\State::Loading) {
    echo "WebView is currently loading content\n";
}
```

<warning>
The <code>WebView::$state</code> property is <b>read-only</b> and cannot be 
changed.
</warning>

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


## WebView Events

<primary-label ref="events"/>

The webview will automatically emit the following events (and intentions)
during its lifecycle.

To subscribe to events, you can use direct access to the 
<a href="events.md#event-listener">event listener</a>, using
`WebView::$events` property.

```php
$app->events->addEventListener(Event::class, function(Event $e) {
    var_dump($e);
});
```

The webview instance also supports a more convenient and simple way of
registering events using the `on()` method.

```php
$app->webview->on(function(Event $event): void {
    var_dump($event);
});
```

<note>
More information about events can be found in the <a href="events.md">events 
documentation</a>.
</note>

### Dom Ready Event
<secondary-label ref="event"/>

An `Boson\WebView\Event\WebViewDomReady` event fired after webview DOM has been 
loaded and ready to work.

```php
class WebViewDomReady<WebView>
```

### Favicon Changing Intention
<secondary-label ref="intention"/>

An `Boson\WebView\Event\WebViewFaviconChanging` intention to change the 
window's icon from loaded HTML content.

<warning>
Linux/GTK4: An intention does not change the windows icon.

Icon change intention has no effect.
</warning>

<warning>
MacOS/WebKit: Provides no way to access favicons.

Icon change intention has no effect.
</warning>

```php
class WebViewFaviconChanging<WebView>
```

<tip>
If intention is cancelled, the window icon has not been changed.
</tip>

### Favicon Changed Event
<secondary-label ref="event"/>

An `Boson\WebView\Event\WebViewFaviconChanged` event fired after the window's 
icon has been changed and the `Boson\WebView\Event\WebViewFaviconChanging` 
intention has not been cancelled.

```php
class WebViewFaviconChanged<WebView>
```

### Message Received Event
<secondary-label ref="event"/>

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

<note>
The <code>ack()</code> method marks the message as accepted and processed.
</note>

<tip>
The <code>stopPropagation()</code> method works in a similar way to 
<code>ack()</code>, but is not recommended due semantic conflicts.
</tip>

### Navigating Intention
<secondary-label ref="intention"/>

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

<tip>
If intention is cancelled, the URL navigation will be cancelled.
</tip>

### Navigated Event
<secondary-label ref="event"/>

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

### Request Intention
<secondary-label ref="event"/>

An `Boson\WebView\Event\WebViewRequest` intention processing of user schemes 
registered <a href="configuration.md#intercepted-schemes">in the 
configuration</a>.

```php
class WebViewRequest<WebView> 
{
    public readonly Boson\Http\RequestInterface $request;
    public ?Boson\Http\ResponseInterface $response = null;
}
```

- `$request` - Custom protocol request instance.
- `$response` - An optional response instance containing the body string.

<tip>
If intention is cancelled, then the response will be rejected (aborted).
</tip>

<warning>
An intention is <b>only</b> called for registered (in configuration) schemes.
</warning>

### Title Changing Intention
<secondary-label ref="intention"/>

An `Boson\WebView\Event\WebViewTitleChanging` intention to change the
window title from loaded HTML content.

```php
class WebViewTitleChanging<WebView> 
{
    public readonly string $title;
}
```

- `$title` - Expected title string to be set.

<tip>
If intention is cancelled, then the window title has not been changed.
</tip>

### Title Changed Event
<secondary-label ref="event"/>

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