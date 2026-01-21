# Configuration

The webview configuration class `Boson\WebView\WebViewCreateInfo` is
<span term="optional class">optional</span> and serves as a convenient way to
define default settings for webview of main window instance.


## Default User Agent

Sets a custom user agent string for the WebView.

```php
$webviewConfig = new Boson\WebView\WebViewCreateInfo( 
    userAgent: 'MyCustomUserAgent/1.0',
);
```


## Storage (Persistent Client Settings)

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


## Extra Flags

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


## Context Menu

Controls whether the default context menu (right mouse button) is enabled.

```php
$webviewConfig = new Boson\WebView\WebViewCreateInfo( 
    contextMenu: true, // Default is false
);
```


## Dev Tools

Enables or disables developer tools for the WebView.

```php
$webviewConfig = new Boson\WebView\WebViewCreateInfo( 
    devTools: true, // Default is null
);
```

The developer tools settings can take one of the following values:
- `true` - Enables a developer tools window.
- `false` - Disables a developer tools window.
- `null` - Depends on the application `debug` settings.
  Developer tools will be enabled if debug is enabled and vice versa.


## Hardware Acceleration

Enables or disables hardware-accelerated rendering for webview.

```php
$windowConfig = new Boson\WebView\WebViewCreateInfo( 
    enableHardwareAcceleration: true, // Default is true
);
```

**macOS/WebKit**

> Does not allow controlling hardware-acceleration.
>
> This configuration option has no effect.
{.warning}


## Dark Mode

Force dark mode for the webview if the option is set to `true`.

```php
$windowConfig = new Boson\WebView\WebViewCreateInfo( 
    forceDarkMode: true, // Default is false
);
```

## Extensions

You can explicitly specify a list of webview extensions by passing an
`array` (`iterable`) in the `extensions` field.

```php
$appConfig = new Boson\WebView\WebViewCreateInfo( 
    extension: [
        new ExampleWebViewExtension(),
    ],
);
```

> You can read more about extensions in the [webview extensions](../05.webview/webview-extensions.md) section.
> {.note}
