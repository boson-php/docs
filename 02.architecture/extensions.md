# Extensions

Extensions are a set of functionality that extends the capabilities of the core a
nd can provide a public API for accessing its capabilities.

Each system part, such as an [Application](../03.application/application-extensions.md), 
[Window](../04.window/window-extensions.md), or [WebView](../05.webview/webview-extensions.md), 
contains its own set of extensions.

## Configuration

Extensions are registered in configuration rules, so the
configuration might look like this:

```php
$config = new Boson\ApplicationCreateInfo(
    extensions: [ /* list of Application extensions */ ],
    
    window: new Boson\Window\WindowCreateInfo(
        extensions: [ /* list of Window extensions */ ],

        webview: new Boson\WebView\WebViewCreateInfo(
            extensions: [ /* list of WebView extensions */ ],
        ),
    ),
);
```

By default, if you do not specify `extensions` field explicitly, the list of 
extensions defined in the constants of the corresponding configuration 
DTO will be loaded.

```php
$config = new Boson\ApplicationCreateInfo(
    // by default
    extensions: Boson\ApplicationCreateInfo::DEFAULT_APPLICATION_EXTENSIONS,
    
    window: new Boson\Window\WindowCreateInfo(
        // by default
        extensions: Boson\Window\WindowCreateInfo::DEFAULT_WINDOW_EXTENSIONS, 

        webview: new Boson\WebView\WebViewCreateInfo(
            // by default
            extensions: Boson\WebView\WebViewCreateInfo::DEFAULT_WEBVIEW_EXTENSIONS,
        ),
    ),
);
```

For ease of extension management, each configuration DTO has an `extensions()` 
method available, allowing you to add and filter the list of extensions.

The `extensions()` method contains two arguments `with` and `except`, where:
- `with` – A list of extension instances that should be added to the default 
  set of extensions.
- `except` – A list of extension classes to disable/exclude.

```php
$config = new Boson\ApplicationCreateInfo(
    // by default
    extensions: Boson\ApplicationCreateInfo::extensions(
        with: [ /* list of Application extension instances */ ],
        except: [ /* list of excluded Application extension classes */ ],
    ),
    
    window: new Boson\Window\WindowCreateInfo(
        extensions: /* etc... */
    ),
);
```

